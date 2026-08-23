# 5. Writes, Transactions and Concurrency — Đúng cả khi hai request chạy cùng lúc

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 4](04-relationships-and-useful-queries.md)  
> **Status:** Reviewed

## Overview

`insert`, `update` và `delete` thay facts nên correctness quan trọng hơn cú pháp. Một request có thể crash giữa hai statements; hai requests có thể cùng đọc stock 1 rồi đều bán; retry có thể tạo order hai lần. Transaction và concurrency control giải quyết các failure này khi boundary được đặt theo business unit of work.

Database không chạy requests lần lượt. PostgreSQL dùng Multi-Version Concurrency Control (MVCC) để readers và writers ít chặn nhau hơn: một query thấy snapshot phù hợp thay vì luôn đọc row đang thay dở. Khi nhiều writers chạm cùng rows, locks và isolation rules quyết định behavior.

## 1. Basic writes và `RETURNING`

Insert nên cung cấp columns rõ, để schema đổi không làm lệch values. `returning` lấy ID/defaults trong cùng statement.

```sql
insert into customers (name, email)
values ($1, $2)
returning id, name, email, created_at;
```

Update phải có predicate có chủ ý. Chạy `update products set is_active = false` không `where` ảnh hưởng mọi rows. Trong tool quản trị, nên xem `select` cùng predicate trước hoặc dùng transaction để kiểm tra count.

```sql
update products
set price = $1
where id = $2
returning id, sku, price;
```

Nếu `returning` không có row, application biết ID không tồn tại hoặc condition không thỏa. Đây thường rõ hơn update rồi query lại.

Delete là business decision, không chỉ CRUD verb. Orders thường cần retention/audit, nên cancel status hoặc soft deletion có thể phù hợp hơn delete. Soft delete cũng có cost: mọi query phải filter, unique constraints và foreign keys cần policy rõ. Không thêm `deleted_at` cho mọi table theo thói quen.

## 2. Transaction boundary theo “cùng thành công hoặc cùng thất bại”

Tạo order gồm header, items và stock update. Nếu item insert lỗi nhưng order header đã commit, database có partial fact. Gói các statements trong transaction tạo atomicity.

```sql
begin;

insert into orders (customer_id, status)
values ($1, 'pending')
returning id;

-- Application dùng order id vừa trả về.
insert into order_items (order_id, product_id, quantity, unit_price)
select $2, id, $3, price
from products
where id = $4;

update products
set stock_quantity = stock_quantity - $3
where id = $4;

commit;
```

Đoạn trên atomic nhưng chưa ngăn stock âm dưới concurrency. Transaction là container, không tự sửa condition logic. Constraints có thể làm update âm fail, nhưng failure nên được phát hiện rõ và không dựa vào việc vi phạm ở cuối.

## 3. Conditional update biến check và write thành một statement

Pattern “select stock, kiểm tra trong application, rồi update” có race: hai transactions cùng thấy đủ stock. Conditional update thực hiện check trên current row khi write.

```sql
update products
set stock_quantity = stock_quantity - $1
where id = $2
  and stock_quantity >= $1
returning id, price, stock_quantity;
```

Nếu trả zero rows, product không tồn tại hoặc stock không đủ; application có thể phân biệt bằng query khác nếu UX cần. Statement này atomic cho một product. Trong create-order transaction, chạy stock update trước, dùng returned price làm `unit_price`, sau đó insert header/items. Bất kỳ item nào không đủ làm rollback toàn transaction.

Với nhiều products, transactions có thể update cùng set theo thứ tự khác nhau và deadlock. Lock rows theo ID ổn định trước khi cập nhật hoặc sắp xếp updates theo product ID. PostgreSQL phát hiện deadlock và hủy một transaction, nên application vẫn cần retry có giới hạn cho transient errors.

```sql
select id, price, stock_quantity
from products
where id = any($1::bigint[])
order by id
for update;
```

Sau khi locks được lấy theo cùng order, validate quantities rồi update/insert. Đừng khóa nhiều rows hơn cần thiết.

## 4. Giữ transaction ngắn

Locks thường được giữ tới commit. Nếu transaction gọi payment HTTP API trong năm giây, rows bị khóa suốt thời gian network và throughput giảm. External call có failure semantics khác database; không thể rollback charge bằng SQL.

Flow thực tế có thể tạo pending order trong transaction ngắn, gọi payment ngoài transaction với idempotency key, rồi update payment/order state trong transaction ngắn khác. Điều này chấp nhận intermediate state và cần recovery job/webhook. Với core case không tích hợp payment thật, rule đơn giản là chuẩn bị input trước `begin`, chỉ làm database work cần atomicity và commit sớm.

Transaction không nên mở trong lúc chờ user bấm confirm. Business conversation có thể kéo dài phút; database transaction nên tính bằng milliseconds/seconds ngắn.

## 5. Isolation levels bằng anomalies thay vì khẩu hiệu

PostgreSQL mặc định `read committed`: mỗi statement thấy rows committed trước khi statement bắt đầu. Hai selects trong cùng transaction có thể thấy kết quả khác nếu transaction khác commit ở giữa. `repeatable read` giữ snapshot ổn định cho transaction nhưng concurrent writes có thể tạo serialization failure. `serializable` cố cho outcome tương đương chạy tuần tự và có thể abort để application retry.

Isolation cao hơn không miễn phí và không thay data model. Chọn level theo anomaly cần ngăn. Nhiều workflows xử lý tốt bằng atomic conditional update, unique constraint và row locks ở default isolation. Chỉ nâng level khi invariant nhiều rows khó bảo vệ và đã thiết kế retry.

Không dùng `read uncommitted` từ hệ khác làm mental model cho PostgreSQL; PostgreSQL xử lý nó như `read committed`. Chi tiết engine-specific cần đọc documentation khi thay DBMS.

## 6. Lost update và optimistic concurrency

Nếu hai users mở cùng profile version, sửa khác fields và ghi toàn row, người sau có thể đè thay đổi người trước. Optimistic concurrency thêm version vào predicate.

```sql
alter table products
add column version integer not null default 1;

update products
set
  name = $1,
  price = $2,
  version = version + 1
where id = $3
  and version = $4
returning id, name, price, version;
```

Zero rows nghĩa record đã đổi hoặc không tồn tại. Application reload và cho user quyết định. Pattern phù hợp khi conflict hiếm và không muốn giữ lock trong thời gian edit. Với counter như stock, atomic arithmetic update thường phù hợp hơn versioned read-modify-write.

## 7. Idempotency cho retries

Network timeout có thể xảy ra sau database commit nhưng trước khi client nhận response. Client retry `create order` và tạo duplicate. Idempotency key do client sinh, unique theo operation/customer, giúp retry trả lại outcome cũ.

```sql
alter table orders
add column idempotency_key text;

create unique index orders_customer_idempotency_key_uq
on orders (customer_id, idempotency_key)
where idempotency_key is not null;
```

Application insert key trong cùng transaction. Khi unique violation, nó query order đã tồn tại và bảo đảm payload tương thích. Không cần idempotency cho mọi update; dùng cho operations có thể retry và duplicate gây hậu quả.

## 8. Error handling và retry có giới hạn

Constraint violation thường là permanent cho input hiện tại; sửa input thay vì retry. Deadlock, serialization failure hoặc connection reset có thể transient; retry transaction từ đầu với backoff và giới hạn. Retry một statement ở giữa transaction đã fail không khôi phục unit of work.

Application log error category và correlation/idempotency key nhưng không log secret hoặc raw payment data. Error message từ database nên được map sang domain response có kiểm soát, không trả nguyên internals cho client.

## 9. Mini practice

Thiết kế transaction chuyển tiền giữa hai accounts. Balance không được âm, hai account rows phải lock theo ID ổn định và tổng tiền không đổi. Mô tả behavior khi hai transfers ngược chiều chạy cùng lúc, khi request timeout sau commit và khi account không đủ tiền.

Bài đạt khi external notification không nằm trong transaction, retry bắt đầu lại toàn unit of work và invariant được bảo vệ ở database statement/constraint chứ không chỉ bằng một `if` trước update.

## Checkpoint

Bạn cần phân biệt atomicity với isolation, biết conditional update ngăn race thế nào, giữ transaction ngắn và chọn retry theo error. Module 6 sẽ xem database tìm rows ra sao và index thay đổi cost như thế nào.
