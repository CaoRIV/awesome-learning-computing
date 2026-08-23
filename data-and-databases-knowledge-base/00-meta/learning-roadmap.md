# Learning Roadmap — Data and Databases Practical Foundations

> **Thời lượng gợi ý:** 10–14 giờ core, cộng 8–12 giờ thực hành  
> **Prerequisites:** Lập trình cơ bản; không yêu cầu SQL  
> **Hệ quản trị minh họa:** PostgreSQL

## Đích đến

Sau lộ trình, người học có thể thiết kế và sử dụng database cho một ứng dụng nhỏ mà không coi ORM hay SQL là phép màu. Bạn sẽ biết data invariant nên được giữ ở đâu, query nào cần index, transaction bảo vệ điều gì và hệ thống phải chuẩn bị thế nào cho thay đổi hoặc sự cố. Đích đến không phải thuộc mọi câu lệnh PostgreSQL hay tự quản lý cluster production lớn.

Track dùng nguyên tắc “schema và query theo behavior thật”. Ta bắt đầu bằng facts và access patterns, không bắt đầu bằng danh sách normal forms. Ta thêm index sau khi có query và execution plan, không thêm cho mọi column. Ta dùng một relational database trước, chỉ chọn nhiều data stores khi một workload cụ thể chứng minh giới hạn.

## Bản đồ học tập

```text
01. Database mental model
        ↓
02. Read data with SQL
        ↓
03. Schema + constraints
        ↓
04. Relationships + useful queries
        ↓
05. Writes + transactions + concurrency
        ↓
06. Indexes + query plans
        ↓
07. Application integration + migrations + security
        ↓
08. Reliability + choosing data systems
```

Module 1–4 tạo nền tảng để đọc và mô hình hóa. Module 5–6 giải thích behavior thường chỉ lộ khi có nhiều requests hoặc data tăng. Module 7 nối database với code ứng dụng. Module 8 đặt relational database vào bức tranh lớn hơn nhưng không mở một catalog công nghệ.

## Nhịp học gợi ý

Trong bốn buổi, buổi đầu học Module 1–2 và tự query một dataset nhỏ. Buổi hai thiết kế schema của cửa hàng ở Module 3–4. Buổi ba thực hành transaction và query plan ở Module 5–6. Buổi cuối kết nối application, viết migration/release plan và làm reliability review.

Nếu chỉ review trong sáu giờ, ưu tiên Module 1, 3, 4, 5, 6 và 8. Nếu đang làm data analysis, học kỹ Module 2 và 4 rồi đi tiếp sang analytics extension. Nếu đang làm backend, toàn bộ tám module là core vì correctness, performance và operation đều ảnh hưởng trực tiếp tới code.

## Checkpoint 1 — Đọc data có chủ đích

Sau Module 2, bạn cần viết được query chọn columns cần thiết, filter đúng với `NULL`, sort deterministic và giới hạn result. Bạn phải giải thích SQL là declarative: query nói kết quả cần gì, optimizer chọn cách thực thi. Chưa cần tối ưu bằng index.

Artifact nên là năm query cho case cửa hàng: tìm product còn bán, order của một customer, order gần nhất, dữ liệu thiếu email và một thống kê đơn giản. Mỗi query kèm một câu mô tả business question, tránh viết SQL không có ngữ cảnh.

## Checkpoint 2 — Schema bảo vệ facts

Sau Module 4, schema phải có primary keys, foreign keys, `not null`, `unique` và `check` đúng nơi. Bạn cần giải thích vì sao order item lưu `unit_price` thay vì luôn join current product price, và vì sao một many-to-many relationship thường cần junction table có meaning riêng.

Normalization được coi là công cụ giảm duplicate facts. Không cần đọc thuộc 1NF–5NF. Nếu cùng customer email bị copy vào 500 orders và phải sửa 500 nơi, model có duplication issue. Nếu report query khó, tạo view hoặc read model có chủ ý tốt hơn phá schema write model ngay lập tức.

## Checkpoint 3 — Correct under concurrency

Sau Module 5, bạn cần đặt create-order và stock update trong transaction, hiểu rollback, biết lock được giữ tới khi commit và không thực hiện HTTP call dài bên trong transaction. Bạn phải mô tả ít nhất một race condition và cách database condition/lock ngăn nó.

Một transaction tốt có boundary theo business unit of work, ngắn và có failure behavior. “Dùng transaction cho an toàn” chưa đủ; cần nói các statements nào phải cùng thành công hoặc cùng thất bại.

## Checkpoint 4 — Performance có evidence

Sau Module 6, bạn cần chạy `EXPLAIN (ANALYZE, BUFFERS)` trên query test an toàn, nhận ra sequential scan không phải lúc nào cũng xấu và tạo index theo filter/join/order thực tế. Bạn phải biết index cải thiện read nhưng làm write/storage đắt hơn, và composite index phụ thuộc column order.

Đừng xem thời gian trên table 20 rows là benchmark. Hãy tạo đủ test data hoặc ít nhất reasoning bằng cardinality. Một index được coi là justified khi có query quan trọng, plan trước/sau và trade-off được ghi lại.

## Checkpoint 5 — Sẵn sàng cho một release nhỏ

Sau Module 8, bạn cần migration forward/backward strategy, application role không phải superuser, connection pool có giới hạn, backup policy cùng restore test và vài monitoring signals có owner. Với project local, có thể mô tả các artifacts thay vì dựng infrastructure thật.

Release-ready không có nghĩa zero downtime ở mọi thay đổi. Nó nghĩa team biết migration có lock/rewrite risk nào, có cách rollback hoặc forward-fix, và không nhầm read replica với backup.

## Extension paths

Backend engineers nên học tiếp PostgreSQL query tuning, isolation levels, locks, partitioning và high availability. Data analysts nên học statistics, visualization và analytical SQL/window functions sâu hơn. Data engineers nên học modeling cho analytics, batch/stream pipelines, orchestration, data quality và warehouse/lakehouse. DBA/SRE cần thực hành backup/restore, replication, capacity planning và incident response trên môi trường gần production.

NoSQL không phải level “sau SQL”. Document, key-value, wide-column, graph và search systems tối ưu cho workloads khác. Học chúng khi bạn có access pattern hoặc scale constraint cụ thể; relational model vẫn là lựa chọn mặc định tốt cho nhiều ứng dụng có transactions và relationships.

## Definition of Done

Một module được hoàn thành khi bạn giải thích mental model bằng lời mình, chạy hoặc trace ví dụ, thay một assumption và dự đoán behavior, rồi làm mini practice trong domain khác. Viết query trả đúng ba rows trên sample chưa đủ; bạn cần biết `NULL`, duplicates, concurrency hoặc data growth có thể thay kết quả thế nào.

