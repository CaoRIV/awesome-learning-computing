# Data and Databases — Practical Foundations

Knowledge base tiếng Việt dành cho người đã biết lập trình cơ bản và muốn hiểu cách ứng dụng lưu, đọc và bảo vệ dữ liệu. Track dùng PostgreSQL làm hệ quản trị minh họa vì SQL và relational model của nó đủ gần với nhiều hệ thống thực tế, nhưng trọng tâm là mental model có thể chuyển sang MySQL, SQL Server hoặc một relational database khác.

> Business facts → Tables and constraints → Queries → Transactions → Performance → Reliable operation

## Cách track này tiếp cận database

Database không chỉ là nơi “cất object” và SQL không chỉ là cú pháp để lấy records. Database giữ facts được nhiều requests, processes và users cùng đọc hoặc thay đổi. Schema định nghĩa điều gì được phép tồn tại; query diễn đạt dữ liệu nào cần lấy; transaction bảo vệ một thay đổi gồm nhiều bước; index đổi cost truy cập; backup và quyền truy cập quyết định hệ thống có phục hồi và giới hạn sự cố được hay không.

Track đi theo một vertical path thay vì tách sớm thành Relational Algebra, Storage Engine, Distributed Database, Data Warehouse và NoSQL. Lý thuyết xuất hiện đúng lúc nó giúp giải thích một behavior. Người học vẫn gặp key, normalization, ACID, MVCC và query planner, nhưng thông qua các tình huống như tạo order, trừ tồn kho, phân trang danh sách và điều tra query chậm.

## Track dành cho ai

Baseline phù hợp là người đọc được code ứng dụng và từng thấy JSON hoặc bảng tính. Không cần biết SQL. Nếu đã dùng ORM nhưng thường không chắc query nào được tạo, index nào cần có hoặc transaction boundary nên nằm ở đâu, track này cũng phù hợp để củng cố nền tảng.

Đây là core track cho beginner và intermediate, không phải hướng dẫn trở thành Database Administrator. High availability nhiều vùng, distributed consensus, internals sâu của B-tree, sharding và data lakehouse nằm ngoài đường học chính.

## Bắt đầu học

Đọc [Learning Roadmap](00-meta/learning-roadmap.md), sau đó học tám module theo thứ tự. Mỗi module mất khoảng 45–75 phút để đọc; phần thực hành cần một PostgreSQL local/container hoặc SQL playground tương thích.

| # | Module | Kết quả chính |
|---:|---|---|
| 1 | [Database Mental Model](modules/01-database-mental-model.md) | Hiểu table, row, key, OLTP và boundary của database |
| 2 | [Reading Data with SQL](modules/02-reading-data-with-sql.md) | Viết `SELECT`, filter, sort và xử lý `NULL` đúng |
| 3 | [Schema Design and Constraints](modules/03-schema-design-and-constraints.md) | Chuyển business rules thành tables, types và constraints vừa đủ |
| 4 | [Relationships and Useful Queries](modules/04-relationships-and-useful-queries.md) | Dùng join, aggregate, CTE và pagination theo access pattern |
| 5 | [Writes, Transactions and Concurrency](modules/05-writes-transactions-concurrency.md) | Giữ multi-step changes atomic và tránh lost update/deadlock cơ bản |
| 6 | [Indexes and Query Performance](modules/06-indexes-and-query-performance.md) | Đọc execution plan, tạo index từ evidence và hiểu write cost |
| 7 | [Application Integration and Change](modules/07-application-integration-and-change.md) | Dùng parameterized SQL, connection pool, migration và least privilege |
| 8 | [Reliable Data Systems](modules/08-reliable-data-systems.md) | Thiết kế backup/restore, monitoring và chọn database theo workload |

## Case study xuyên suốt

Một cửa hàng nhỏ quản lý customers, products, orders và order items. Khi khách đặt hàng, hệ thống phải lưu đúng giá tại thời điểm mua, không bán vượt tồn kho, hiển thị lịch sử order và tạo báo cáo doanh thu. Case này đủ quen để tập trung vào database reasoning, đồng thời tạo ra relationships, transaction và query performance thực tế.

Ví dụ không cố mô phỏng toàn bộ thương mại điện tử. Không có microservices, event bus, CQRS hoặc sharding trong core path. Những kiến trúc đó chỉ có ý nghĩa khi scale, ownership hoặc failure boundary tạo requirement thật; thêm chúng vào một ứng dụng nhỏ sẽ che mất kiến thức database nền.

## Cách học

Mỗi module được trình bày theo các đoạn giải thích dài có mạch, sau đó là một ví dụ SQL tập trung. Hãy chạy query, thay dữ liệu và dự đoán kết quả trước khi nhìn output. Đừng học thuộc thứ tự clauses như một bài thơ; hãy hỏi mỗi clause đang biến đổi relation tạm thời ra sao và database phải đọc bao nhiêu data để trả kết quả.

SQL snippets dùng lowercase để giữ style nhất quán. Tên tables ở dạng số nhiều, identifiers không quoted và thời gian dùng `timestamptz`. Đây là convention của track, không phải quy tắc duy nhất của mọi dự án.

## Scope và extension paths

Core path bao gồm relational modeling, CRUD/querying, joins/aggregation, constraints, transactions, concurrency, indexes, query plans, application integration, migrations, privileges, backup và lựa chọn data store. PostgreSQL-specific detail chỉ được dùng khi giúp ví dụ chính xác.

Data analytics chuyên sâu, ETL/ELT, dimensional modeling, stream processing, search engines, database internals, replication topology, sharding và cloud vendor operations là extension paths. Sau core, người làm backend nên học query tuning và reliability sâu hơn; người làm data nên chuyển sang analytics/data engineering; người vận hành nên học PostgreSQL administration và disaster recovery hands-on.

## Exit criteria

Bạn hoàn thành track khi có thể nhận một workflow nhỏ, thiết kế schema có keys/constraints, viết query không phụ thuộc `SELECT *`, giữ thay đổi nhiều bước trong transaction, dùng `EXPLAIN ANALYZE` trước khi thêm index và mô tả cách ứng dụng kết nối với database an toàn. Bạn cũng cần phân biệt backup với replication, OLTP với analytics và giải thích khi relational database vẫn là lựa chọn đơn giản nhất.

