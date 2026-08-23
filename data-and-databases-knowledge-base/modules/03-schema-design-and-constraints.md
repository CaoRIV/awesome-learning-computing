# 3. Schema Design and Constraints — Chỉ cho phép state có nghĩa

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 2](02-reading-data-with-sql.md)  
> **Status:** Reviewed

## Overview

Schema design là việc quyết định facts nào tồn tại, identity của chúng, relationships và rules luôn phải đúng. Một schema tốt không cố dự đoán mọi requirement tương lai; nó bảo vệ invariants hiện có và để các thay đổi có thể diễn ra rõ ràng. Constraint đặt rule gần dữ liệu, nên mọi application, script hoặc import đều phải giữ rule đó.

Case cửa hàng cần customers, products, orders và order items. Ta bắt đầu từ sentences trong domain rồi chuyển sang tables. “Customer đặt nhiều orders”, “order chứa nhiều products với số lượng và giá tại lúc mua”, “stock không âm” là những câu đủ để thiết kế core schema.

## 1. Một table nên giữ một loại fact

Nếu `orders` chứa `product_1`, `product_2`, `product_3`, schema đã đặt giới hạn tùy ý và query khó. Nếu một cell chứa JSON array items, ta mất foreign key và aggregate SQL cơ bản. `order_items` là relationship có attributes riêng: quantity và unit price. Nó không chỉ là table nối kỹ thuật.

```text
customers 1 ─── n orders 1 ─── n order_items n ─── 1 products
```

Mỗi order item nói: “order X mua product Y, quantity Q, với unit price P tại thời điểm mua.” `unit_price` được snapshot dù products đã có `price`, vì giá hiện tại có thể đổi nhưng lịch sử order không được viết lại. Đây là duplication có chủ ý của một fact theo thời điểm, không phải duplication vô tình.

## 2. Chọn data type theo meaning

IDs trong core case dùng `bigint identity`. Text dùng `text` trừ khi business có giới hạn có ý nghĩa. Tiền dùng `numeric(12, 2)` để arithmetic decimal chính xác; `float` có rounding behavior không phù hợp cho số tiền cần khớp. Cờ active dùng `boolean`, quantity dùng integer và thời điểm dùng `timestamptz`.

Data type vừa bảo vệ value vừa ảnh hưởng storage/comparison. Đừng lưu ngày, boolean hoặc số dưới dạng text chỉ vì input từ form là string. Parse và validate ở application boundary, sau đó gửi typed parameter vào database.

## 3. Core schema hoàn chỉnh vừa đủ

```sql
create table customers (
  id bigint generated always as identity primary key,
  name text not null,
  email text not null,
  created_at timestamptz not null default now(),
  constraint customers_name_not_blank check (length(trim(name)) > 0),
  constraint customers_email_unique unique (email)
);

create table products (
  id bigint generated always as identity primary key,
  sku text not null unique,
  name text not null,
  category text not null,
  price numeric(12, 2) not null,
  stock_quantity integer not null default 0,
  is_active boolean not null default true,
  created_at timestamptz not null default now(),
  constraint products_name_not_blank check (length(trim(name)) > 0),
  constraint products_price_non_negative check (price >= 0),
  constraint products_stock_non_negative check (stock_quantity >= 0)
);

create table orders (
  id bigint generated always as identity primary key,
  customer_id bigint not null references customers (id),
  status text not null default 'pending',
  created_at timestamptz not null default now(),
  constraint orders_status_valid
    check (status in ('pending', 'paid', 'cancelled', 'fulfilled'))
);

create table order_items (
  order_id bigint not null references orders (id) on delete cascade,
  product_id bigint not null references products (id),
  quantity integer not null,
  unit_price numeric(12, 2) not null,
  constraint order_items_pk primary key (order_id, product_id),
  constraint order_items_quantity_positive check (quantity > 0),
  constraint order_items_price_non_negative check (unit_price >= 0)
);

create index orders_customer_id_idx on orders (customer_id);
create index order_items_product_id_idx on order_items (product_id);
```

Primary key của `order_items` ngăn cùng product xuất hiện hai dòng trong một order; application cộng quantity nếu user thêm lại. Nếu domain cần cùng product với configuration/price khác nhau, constraint này không còn đúng và một identity key cùng unique rule khác sẽ phù hợp hơn.

PostgreSQL tự tạo index cho primary key và unique constraint, nhưng không tự tạo index ở phía foreign key. `orders.customer_id` và `order_items.product_id` được index vì queries/join/delete checks thường đi từ parent sang children. `order_items.order_id` đã là cột đầu của composite primary-key index, nên không cần index trùng lặp chỉ trên `order_id` cho access pattern này.

## 4. Constraint và validation không loại trừ nhau

Application validation cho message thân thiện và tránh round trip. Database constraint là hàng rào cuối cho mọi writers và concurrency. Cả hai nên tồn tại khi rule quan trọng. Application có thể báo “Số lượng phải lớn hơn 0”; check constraint vẫn ngăn một script import ghi `-3`.

Không phải mọi rule đều dễ đặt trong check constraint. “Coupon chỉ hợp lệ cho customer tier tại thời điểm order” phụ thuộc nhiều facts và thời gian; application/domain service cùng transaction có thể phù hợp hơn. Constraint tốt thường local, deterministic và luôn đúng cho row/relation.

## 5. `NOT NULL`, `UNIQUE` và business identity

`not null` nên dùng khi absent state không có meaning. Cho mọi field nullable “để linh hoạt” chuyển complexity sang mọi query và application. Ngược lại, ép phone number bắt buộc khi business cho phép user chưa cung cấp tạo fake data; nullable lúc đó đúng hơn.

`unique(email)` bảo đảm exact stored values không trùng theo collation/operator behavior. Email case normalization là product decision: application có thể canonicalize trước insert hoặc database dùng expression/index/extension phù hợp. Đừng mặc định lower-case mọi identifier nếu domain phân biệt case.

Surrogate key như `id` tạo identity ổn định, còn `sku` và email là candidate/business keys cần unique. Có surrogate key không nghĩa bỏ uniqueness của business facts; nếu không, duplicate products có thể tồn tại với IDs khác.

## 6. Referential actions phải theo lifecycle thật

`on delete cascade` ở `order_items.order_id` hợp lý nếu xóa order trong scope hiện tại nghĩa xóa toàn bộ items thuộc nó. `products` không cascade sang lịch sử order items vì xóa product không được xóa purchase history; thực tế thường deactivate product thay vì delete. `customers` và orders cũng cần retention/anonymization policy chứ không tùy tiện cascade.

Default `no action`/restrictive behavior buộc team quyết định thay vì mất dữ liệu im lặng. Chỉ thêm cascade khi child không có meaning ngoài parent và deletion requirement rõ.

## 7. Normalization bằng câu hỏi “fact này nằm ở mấy nơi?”

Normalization giảm update anomalies bằng cách giữ một fact một nơi. Customer name hiện tại nằm trong `customers`, không copy vào mọi order nếu business chỉ cần current name. Product current price nằm trong `products`, còn historical sold price là fact khác nên nằm ở `order_items.unit_price`.

Không cần thuộc tên mọi normal form để dùng mental model này. Hỏi: nếu fact thay đổi, phải sửa bao nhiêu rows? Có thể tạo hai values mâu thuẫn không? Column có phụ thuộc vào identity của row này hay fact khác? Những câu hỏi đó tìm được phần lớn lỗi schema ứng dụng.

Denormalization là duplication có chủ ý để phục vụ read/performance và phải có owner cập nhật. View, materialized view hoặc analytical model thường rõ hơn thêm columns duplicate vào write tables sớm. Chỉ denormalize sau khi query measurement chứng minh cần.

## 8. JSONB là escape hatch có giá

`jsonb` hữu ích cho metadata có shape biến đổi, raw provider payload hoặc thuộc tính ít được query. Nó không nên thay toàn bộ schema. Nếu `status`, `price` và `customer_id` nằm trong JSON, constraints, foreign keys và indexes trở nên phức tạp, còn application tự gánh schema.

Một rule thực dụng: fields tham gia identity, relationship, filter thường xuyên hoặc invariant nên là typed columns. Metadata tùy chọn ít dùng có thể là JSONB. Khi một JSON field trở thành core access pattern, promote nó thành column qua migration là bình thường.

## 9. Mini practice

Thiết kế schema cho library có books, physical copies, members và loans. Phân biệt “book title” với từng copy có barcode; một loan tham chiếu copy nào; ngày trả có được nullable không; constraint nào ngăn ngày due trước ngày borrow. Nêu một deletion mà cascade hợp lý và một deletion phải restrict.

Bài đạt khi facts theo thời điểm không bị ghi đè bởi current value, foreign keys có lifecycle reasoning và mỗi nullable field có meaning rõ.

## Checkpoint

Bạn cần giải thích primary/business key, foreign key, snapshot value, normalization và constraint boundary. Module 4 sẽ dùng schema này để viết joins, aggregates và access patterns thật.
