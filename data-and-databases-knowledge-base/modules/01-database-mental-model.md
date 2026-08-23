# 1. Database Mental Model — Nơi giữ facts dùng chung

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** Biết biến, collection và object/record cơ bản  
> **Status:** Reviewed

## Overview

Một biến trong chương trình biến mất khi process dừng. Một file có thể tồn tại lâu hơn, nhưng nhiều requests cùng sửa file sẽ nhanh chóng tạo vấn đề về đồng bộ, tìm kiếm và phục hồi. Database Management System (DBMS) là software quản lý dữ liệu bền vững, cung cấp query, concurrency control, constraints, authorization và recovery để nhiều clients làm việc trên cùng facts.

Database không “thông minh” thay business design. Nó chỉ bảo đảm những rules đã được biểu diễn và những transaction đã được đặt đúng boundary. Nếu application lưu giá tiền bằng text, cho phép order item không có order hoặc cập nhật stock ở ngoài transaction, DBMS không thể đoán ý định còn thiếu.

## 1. Từ object đang chạy tới fact cần được giữ

Trong case cửa hàng, một `Product` object trong memory có thể chứa `id`, `name`, `price` và `stock_quantity`. Khi request kết thúc, object không còn, nhưng product vẫn phải xuất hiện ở request sau và cho nhiều users. Database lưu representation bền vững của facts đó. Application tạo object hoặc JSON từ rows khi cần, nhưng row không đồng nhất với object: object có methods và lifecycle trong process; row thuộc schema và được nhiều programs cùng đọc.

Một relational database tổ chức dữ liệu thành relations, thường nhìn như tables. Table có columns mô tả attributes và rows mô tả facts cùng loại. `products` chứa một row cho mỗi product. `orders` chứa một row cho mỗi order. Table boundary tốt giúp một fact có chỗ ở rõ và constraints có thể bảo vệ.

## 2. Key tạo identity ổn định

Primary key phân biệt mỗi row. Tên product không phải key tốt vì có thể trùng hoặc đổi. Với một database đơn, `bigint generated always as identity` là lựa chọn thực dụng: gọn, tăng tuần tự và được PostgreSQL quản lý. UUID phù hợp khi cần tạo ID phân tán hoặc không muốn lộ số tuần tự, nhưng lớn hơn và strategy tạo ID ảnh hưởng index locality. Core case dùng identity vì chưa có requirement phân tán.

Foreign key nối một fact tới fact khác. `orders.customer_id` tham chiếu `customers.id`, nhờ đó order không thể trỏ tới customer không tồn tại. Foreign key không thay thế business permission; nó chỉ bảo vệ referential integrity.

```sql
create table customers (
  id bigint generated always as identity primary key,
  name text not null,
  email text not null unique
);

create table orders (
  id bigint generated always as identity primary key,
  customer_id bigint not null references customers (id),
  status text not null,
  created_at timestamptz not null default now()
);
```

Schema này chưa hoàn chỉnh, nhưng đã nói được ba facts: mỗi customer/order có identity, order bắt buộc có customer hợp lệ và thời điểm có timezone. `text` được dùng thay `varchar(255)` vì giới hạn 255 không đến từ business rule; nếu tên thực sự có giới hạn nghiệp vụ, dùng `check` có ý nghĩa rõ hơn.

## 3. SQL là declarative

Trong imperative code, ta thường mô tả từng bước loop và filter. SQL chủ yếu mô tả result cần có. Query planner của DBMS chọn scan, index và join strategy dựa trên schema, indexes và statistics. Điều này làm SQL mạnh nhưng cũng yêu cầu tư duy theo sets thay vì xử lý từng row bằng vòng lặp ứng dụng.

```sql
select id, name, price
from products
where is_active = true
order by name;
```

Query nói “cần ba columns của products đang active theo thứ tự name”. Nó không ép engine phải đọc file từ đầu hay dùng index cụ thể. Module 6 sẽ dùng execution plan để xem engine đã chọn gì; trước đó, hãy tập trung diễn đạt đúng business question.

## 4. Schema, data và metadata

Schema là cấu trúc và rules: tables, columns, types, constraints, indexes và views. Data là rows. Metadata là thông tin mô tả database, chẳng hạn tên column hoặc statistics planner dùng. Migration thay đổi schema theo phiên bản; query và writes làm việc với data.

Tách ba khái niệm giúp tránh thao tác nguy hiểm. `delete from orders` xóa data nhưng giữ table. `drop table orders` xóa schema object và data. `truncate` xóa toàn bộ rows theo cơ chế khác và thường không phải lệnh dùng tùy tiện. Trong core exercises, mọi thao tác phá dữ liệu nên chạy trên database local có thể tạo lại.

## 5. ACID như một behavioral contract

ACID thường được học thuộc nhưng nên hiểu qua create-order. **Atomicity** nghĩa tạo order, items và trừ stock cùng thành công hoặc cùng rollback. **Consistency** nghĩa transaction chuyển database từ state thỏa constraints sang state khác cũng thỏa constraints; application vẫn phải viết đúng business logic. **Isolation** quy định concurrent transactions có thể quan sát lẫn nhau ở mức nào. **Durability** nghĩa sau commit thành công, DBMS có cơ chế giữ thay đổi qua crash theo guarantee cấu hình.

ACID không có nghĩa “không thể mất dữ liệu trong mọi thảm họa” hay “mọi business rule tự đúng”. Disk failure, operator mistake và region outage vẫn cần backup/replication/recovery. Một transaction có thể atomically ghi sai giá nếu code gửi sai value.

## 6. OLTP và OLAP giải quyết câu hỏi khác nhau

Online Transaction Processing (OLTP) phục vụ nhiều thao tác nhỏ như tạo order, cập nhật status và xem account. Schema thường normalized, queries chạm ít rows và latency quan trọng. Online Analytical Processing (OLAP) phục vụ scan/aggregate lớn theo tháng, sản phẩm hoặc cohort; data warehouse và columnar storage thường phù hợp hơn khi analytics tăng.

Ứng dụng nhỏ có thể chạy report trên PostgreSQL chính. Không cần dựng warehouse từ ngày đầu. Khi report nặng làm chậm transactions, lịch sử cần hợp nhất nhiều nguồn hoặc modeling phân tích khác hẳn, tách analytical workload mới có giá trị. Module 8 quay lại decision này.

## 7. Database, cache và search không thay thế lẫn nhau

Database thường là source of truth cho orders. Cache giữ bản sao dễ mất để giảm latency; invalidation và staleness là trade-off. Search engine tạo index tối ưu full-text/fuzzy search nhưng thường không giữ transactional truth. Object storage phù hợp files/blobs; database lưu metadata và location thay vì nhét mọi file lớn vào row theo mặc định.

Một architecture nhỏ nên bắt đầu với database duy nhất. Thêm cache khi đo được query/latency bottleneck, search engine khi search requirement vượt khả năng hiện tại, và queue khi asynchronous boundary thực sự cần. Mỗi store mới tạo consistency và operational cost.

## 8. Những cách hiểu sai thường gặp

Database không chỉ là Excel có API: transactions, constraints và concurrent access tạo khác biệt lớn. ORM không loại bỏ nhu cầu biết SQL vì query shape, joins, transaction và index vẫn tồn tại. “NoSQL nhanh hơn SQL” không có ý nghĩa nếu chưa nói workload, consistency và data model. “Schema-less” thường nghĩa schema enforcement chuyển sang application, không phải dữ liệu không có structure.

Một lỗi tư duy khác là model database giống hệt UI hiện tại. UI thay đổi thường xuyên, trong khi facts và invariants ổn định hơn. Schema nên phản ánh domain facts và access patterns quan trọng, không phản ánh từng component trên màn hình.

## 9. Mini practice

Chọn ứng dụng library, expense tracker hoặc learning platform. Viết ba tables có thể cần, mỗi table đại diện fact gì, primary key là gì và một relationship. Sau đó nêu một operation phải dùng transaction và một report có thể trở thành analytical workload khi data lớn.

Bài đạt khi table không chỉ là bản sao của một màn hình và transaction được mô tả bằng “các thay đổi nào phải cùng thành công”. Không cần viết SQL hoàn chỉnh.

## Checkpoint

Bạn sẵn sàng sang Module 2 khi phân biệt được row với object, key với label, database với cache và OLTP với OLAP. Hãy giữ mental model: database là nơi quản lý facts dùng chung dưới các contracts, không chỉ là nơi serialize state của chương trình.

