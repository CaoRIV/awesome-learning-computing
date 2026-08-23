# 4. Relationships and Useful Queries — Kết nối facts mà không làm mất meaning

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 3](03-schema-design-and-constraints.md)  
> **Status:** Reviewed

## Overview

Schema normalized đặt facts ở nhiều tables để tránh mâu thuẫn. Query join chúng lại theo câu hỏi. Join không phải “ghép bảng” tùy ý; điều kiện join diễn đạt relationship. Chọn sai join type hoặc aggregate sau one-to-many có thể tạo kết quả đúng hình thức nhưng sai business.

Module dùng schema cửa hàng của Module 3. Trước mỗi query, hãy nói grain của result: một row cho mỗi order, mỗi item hay mỗi customer. Grain là công cụ đơn giản nhất để tránh duplicate ngoài ý muốn.

## 1. Inner join giữ những rows có match

Danh sách order items với product name có grain “một row cho mỗi order item”.

```sql
select
  oi.order_id,
  p.sku,
  p.name as product_name,
  oi.quantity,
  oi.unit_price,
  oi.quantity * oi.unit_price as line_total
from order_items oi
join products p on p.id = oi.product_id
where oi.order_id = $1
order by p.name, p.id;
```

Foreign key bảo đảm product match trong schema này, nên inner join không làm mất valid items. Aliases ngắn nhưng có meaning giúp query nhiều tables dễ đọc. Condition phải dùng keys đúng; join bằng product name có thể match trùng và vỡ khi rename.

## 2. Left join giữ rows phía trái kể cả không có match

Muốn liệt kê mọi customer cùng số orders, kể cả customer chưa mua, dùng left join. `count(o.id)` đếm non-null matches; `count(*)` sẽ đếm cả row customer được giữ lại và cho kết quả 1 sai với customer chưa có order.

```sql
select
  c.id,
  c.name,
  count(o.id) as order_count
from customers c
left join orders o on o.customer_id = c.id
group by c.id, c.name
order by order_count desc, c.id;
```

Filter table bên phải đặt sai trong `where` có thể biến left join thành inner join. Nếu muốn chỉ đếm paid orders nhưng vẫn giữ customer không có paid order, đặt condition trong `on` hoặc dùng filtered aggregate.

```sql
select
  c.id,
  c.name,
  count(o.id) filter (where o.status = 'paid') as paid_order_count
from customers c
left join orders o on o.customer_id = c.id
group by c.id, c.name;
```

## 3. Aggregate ở đúng grain

Order total được tính từ items, không từ current product price.

```sql
select
  o.id as order_id,
  o.status,
  o.created_at,
  sum(oi.quantity * oi.unit_price) as order_total
from orders o
join order_items oi on oi.order_id = o.id
where o.customer_id = $1
group by o.id, o.status, o.created_at
order by o.created_at desc, o.id desc;
```

PostgreSQL biết primary key functional dependency trong một số grouping cases, nhưng track liệt kê selected non-aggregate columns rõ để query dễ chuyển hệ và dễ đọc. Với order chưa có item, inner join loại nó; nếu draft order rỗng là valid và phải hiển thị, dùng left join cùng `coalesce(sum(...), 0)`.

Nối đồng thời hai one-to-many relationships rồi aggregate có thể nhân rows. Ví dụ join orders với items và payments làm mỗi item lặp theo mỗi payment. Cách sửa là aggregate mỗi child về grain order trước rồi join results.

## 4. CTE đặt tên cho bước reasoning

Common Table Expression (`with`) giúp tách query dài thành relations có tên. Nó không mặc định là optimization; dùng để làm grain và intent rõ.

```sql
with order_totals as (
  select
    order_id,
    sum(quantity * unit_price) as total
  from order_items
  group by order_id
)
select
  c.id,
  c.name,
  count(o.id) as paid_orders,
  coalesce(sum(ot.total), 0) as lifetime_value
from customers c
left join orders o
  on o.customer_id = c.id
  and o.status in ('paid', 'fulfilled')
left join order_totals ot on ot.order_id = o.id
group by c.id, c.name
order by lifetime_value desc, c.id;
```

CTE `order_totals` có một row mỗi order. Query ngoài có một row mỗi customer. Nói rõ hai grains làm join dễ review hơn.

## 5. Window functions giữ rows trong khi tính theo nhóm

`group by` nén nhiều rows thành một row mỗi group. Window function tính theo group nhưng giữ từng row. Ví dụ xếp hạng products theo doanh thu trong category:

```sql
with product_revenue as (
  select
    p.id,
    p.name,
    p.category,
    sum(oi.quantity * oi.unit_price) as revenue
  from products p
  join order_items oi on oi.product_id = p.id
  join orders o on o.id = oi.order_id
  where o.status in ('paid', 'fulfilled')
  group by p.id, p.name, p.category
)
select
  id,
  name,
  category,
  revenue,
  dense_rank() over (
    partition by category
    order by revenue desc
  ) as category_rank
from product_revenue
order by category, category_rank, id;
```

Window syntax có vẻ học thuật nếu học riêng, nhưng câu hỏi “giữ từng product và thêm rank trong category” làm purpose rõ. Không cần học toàn bộ catalog functions trong core path.

## 6. `EXISTS` diễn đạt existence mà không tạo duplicate

Muốn customers có ít nhất một pending order, `exists` nói đúng question và không nhân customer rows.

```sql
select c.id, c.name
from customers c
where exists (
  select 1
  from orders o
  where o.customer_id = c.id
    and o.status = 'pending'
);
```

`select distinct` đôi khi được dùng để che duplicate do join sai. `distinct` hợp lệ khi business thật sự cần unique projection, nhưng nếu không giải thích được duplicate đến từ đâu, hãy sửa grain/join trước.

## 7. Keyset pagination cho danh sách tăng dần

`offset` phải đi qua rows bị bỏ và result có thể trượt khi inserts xảy ra. Với feed/order history sâu, giữ cursor gồm mọi sort columns.

```sql
-- Trang đầu
select id, customer_id, status, created_at
from orders
order by created_at desc, id desc
limit 20;

-- Trang tiếp theo: cursor là created_at và id cuối trang trước
select id, customer_id, status, created_at
from orders
where (created_at, id) < ($1, $2)
order by created_at desc, id desc
limit 20;
```

Keyset không tiện nhảy trực tiếp page 127 và cursor phải encode ordering. Với admin table nhỏ cần page number, offset có thể đủ. Chọn theo UX và data size, không biến cursor thành dogma.

## 8. View cho read contract ổn định

View đặt tên cho query dùng lại và có thể giới hạn columns. Nó không tự lưu data; query view thường được planner mở rộng. Materialized view lưu kết quả và cần refresh, phù hợp report nặng chấp nhận stale. Đừng tạo view cho mọi query một lần; dùng khi read contract có nhiều consumers hoặc logic cần ownership rõ.

```sql
create view paid_order_totals as
select
  o.id as order_id,
  o.customer_id,
  o.created_at,
  sum(oi.quantity * oi.unit_price) as total
from orders o
join order_items oi on oi.order_id = o.id
where o.status in ('paid', 'fulfilled')
group by o.id, o.customer_id, o.created_at;
```

## 9. Mini practice

Viết query cho ba yêu cầu: customers chưa từng order; năm products có doanh thu cao nhất trong tháng; lịch sử order có total và phân trang keyset. Trước mỗi query ghi grain. Sau đó cố ý join thêm một one-to-many table và giải thích cách duplicate ảnh hưởng sum.

Bài đạt khi left join không làm mất zero case, aggregate dùng historical unit price và pagination có tie-breaker unique.

## Checkpoint

Bạn cần phân biệt inner/left join, group aggregate/window, existence và keyset pagination. Module 5 chuyển sang writes, nơi đúng query chưa đủ nếu hai requests chạy cùng lúc.

