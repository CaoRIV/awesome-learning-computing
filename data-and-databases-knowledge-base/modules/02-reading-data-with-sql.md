# 2. Reading Data with SQL — Hỏi database một câu rõ ràng

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 1](01-database-mental-model.md)  
> **Status:** Reviewed

## Overview

`SELECT` là cách biến một business question thành result set. Query tốt không chỉ trả kết quả đúng trên ba rows mẫu; nó chọn columns cần thiết, xử lý `NULL`, có thứ tự ổn định khi cần và không vô tình nhân đôi rows. Module này bắt đầu với một table để mental model rõ, rồi Module 4 mới kết nối nhiều tables.

Hãy tưởng tượng table `products` có `id`, `name`, `category`, `price`, `stock_quantity`, `is_active` và `created_at`. Mỗi query dưới đây nên được đọc như một câu về tập rows, không phải một loop ẩn.

## 1. `SELECT` và projection

`select id, name, price from products` chọn ba attributes của mọi product. Việc chọn columns gọi là projection. `select *` tiện khi khám phá local data, nhưng trong application contract nó kéo columns không cần, làm response phụ thuộc schema và có thể vô tình lộ field nhạy cảm. Chọn tên columns làm intent và output shape rõ hơn.

Alias giúp result dễ đọc hoặc làm bridge sang API:

```sql
select
  id,
  name as product_name,
  price as current_price
from products;
```

SQL clauses có thứ tự viết, còn logical processing có thể hình dung `from → where → group by → having → select → order by → limit`. Optimizer có quyền biến đổi execution miễn giữ semantics. Vì alias được tạo ở `select`, nhiều trường hợp không thể dùng nó trong `where` cùng level; hiểu logical order giúp tránh học thuộc lỗi riêng lẻ.

## 2. `WHERE` và boolean expressions

`where` giữ rows thỏa predicate. Dùng parentheses khi trộn `and` và `or` để business rule không phụ thuộc việc nhớ precedence.

```sql
select id, name, price
from products
where is_active = true
  and stock_quantity > 0
  and (category = 'book' or category = 'course');
```

String dùng single quotes; identifiers thường không cần quotes nếu đặt lowercase. Parameter từ user không được nối trực tiếp vào SQL string. Module 7 trình bày parameterized query; hiện tại hãy coi `$1` hoặc named parameter là placeholder an toàn.

```sql
select id, name, price
from products
where category = $1
  and price <= $2;
```

## 3. `NULL` là unknown/absent, không phải empty

`NULL` biểu diễn value không có hoặc chưa biết tùy data model. Nó không bằng empty string, zero hoặc false. So sánh `email = null` không trả true; dùng `is null`. SQL sử dụng three-valued logic: expression có thể true, false hoặc unknown. `where` chỉ giữ true, nên rows tạo unknown bị loại.

```sql
select id, name
from customers
where phone_number is null;
```

`coalesce(phone_number, 'chưa cung cấp')` chọn value không-null đầu tiên để hiển thị, nhưng đừng dùng nó để che schema mơ hồ. Nếu field bắt buộc theo business rule, `not null` ở schema tốt hơn xử lý missing ở mọi query.

`not in` có trap khi subquery chứa `NULL`, vì comparisons có thể thành unknown. `not exists` thường diễn đạt anti-join an toàn hơn:

```sql
select c.id, c.name
from customers c
where not exists (
  select 1
  from orders o
  where o.customer_id = c.id
);
```

## 4. Sort phải deterministic nếu pagination hoặc “mới nhất” quan trọng

Rows không có thứ tự mặc định. `order by created_at desc` đưa thời điểm mới trước, nhưng nhiều rows có cùng timestamp có thể đổi vị trí. Thêm unique tie-breaker tạo thứ tự ổn định.

```sql
select id, customer_id, status, created_at
from orders
order by created_at desc, id desc
limit 20;
```

`limit` không có `order by` chỉ lấy một số rows theo plan hiện tại, không bảo đảm “20 rows đầu tiên” có meaning. Offset pagination dễ hiểu cho UI nhỏ nhưng chậm và thiếu ổn định ở page sâu; Module 4 dùng keyset pagination.

## 5. Expressions và `CASE`

SQL có thể tạo derived value mà không lưu duplicate column. `case` biểu diễn classification trong query khi rule cục bộ và rõ.

```sql
select
  id,
  name,
  stock_quantity,
  case
    when stock_quantity = 0 then 'out-of-stock'
    when stock_quantity < 5 then 'low-stock'
    else 'available'
  end as stock_state
from products;
```

Nếu stock state được nhiều systems dùng và rule thay đổi có governance, có thể chuyển vào view hoặc domain logic rõ ràng. Không cần lưu `stock_state` nếu nó luôn suy ra được từ quantity, vì hai values có thể mâu thuẫn.

## 6. Aggregate trả lời câu hỏi về nhóm

`count`, `sum`, `avg`, `min` và `max` nén nhiều rows. `group by` tạo nhóm theo attributes. Mọi selected expression không aggregate phải phù hợp với grouping rules.

```sql
select
  status,
  count(*) as order_count
from orders
group by status
order by order_count desc;
```

`where` lọc rows trước grouping; `having` lọc groups sau aggregate.

```sql
select
  customer_id,
  count(*) as order_count
from orders
where created_at >= date_trunc('month', now())
group by customer_id
having count(*) >= 3
order by order_count desc, customer_id;
```

`count(*)` đếm rows; `count(phone_number)` chỉ đếm non-null values của column đó. Sự khác biệt quan trọng khi đo coverage.

## 7. Dates và time zones

Track dùng `timestamptz` cho thời điểm. PostgreSQL lưu instant và hiển thị theo session timezone. Application nên chuyển sang timezone của user ở boundary hiển thị thay vì lưu local time mơ hồ. Một “ngày kinh doanh” vẫn cần business timezone rõ vì midnight khác theo vùng.

Range query nên dùng half-open interval để tránh lỗi phần nhỏ của giây:

```sql
select id, customer_id, created_at
from orders
where created_at >= timestamptz '2026-08-01 00:00:00+07'
  and created_at <  timestamptz '2026-09-01 00:00:00+07';
```

Đừng cast column trong filter thường xuyên nếu muốn index trên column gốc dễ được dùng. Tính start/end ở application hoặc dùng expression/index có chủ ý khi requirement cần.

## 8. Query đúng trước, performance sau

Một query ngắn không nhất thiết nhanh; một query nhiều dòng không nhất thiết chậm. Cost phụ thuộc rows, selectivity, indexes, joins và plan. Ở giai đoạn này, ưu tiên semantics: columns đúng, filter đúng, `NULL` đúng, ordering đúng. Module 6 đo plan thay vì tối ưu bằng cảm giác.

Một thói quen tốt là viết business question trên query và kiểm thử edge cases: table rỗng, duplicate timestamp, missing value, Unicode, boundary time và số lượng lớn. Những tests này tìm correctness bugs sớm hơn tuning.

## 9. Mini practice

Với `products`, `customers` và `orders`, viết sáu query: product active còn hàng; product trong khoảng giá; customer chưa có phone; 10 order mới nhất với tie-breaker; số order theo status; customers có ít nhất ba order trong tháng. Với mỗi query, viết một câu về `NULL` hoặc ordering có thể làm kết quả khác dự kiến.

Bài đạt khi không dùng `select *` trong output ứng dụng, input được biểu diễn bằng parameter placeholder và query “mới nhất” có `order by` deterministic.

## Checkpoint

Bạn cần giải thích projection, filtering, three-valued logic, grouping và deterministic order. Module 3 chuyển từ đọc data sang thiết kế nơi data được phép tồn tại.

