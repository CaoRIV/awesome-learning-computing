# 8. Reliable Data Systems — Phục hồi được và chọn đúng công cụ

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 7](07-application-integration-and-change.md)  
> **Status:** Reviewed

## Overview

Database feature chưa hoàn thành khi query chạy trên laptop. Dữ liệu phải được backup, restore, quan sát và thay đổi mà không vượt risk chấp nhận. Đồng thời, relational database không phải công cụ duy nhất; cache, search, document store và warehouse có strengths riêng. Module cuối cung cấp decision model vừa đủ mà không biến kiến trúc thành catalog sản phẩm.

Reliability bắt đầu bằng mục tiêu business. Có thể mất tối đa bao nhiêu data và chấp nhận downtime bao lâu? Những câu hỏi này tạo Recovery Point Objective (RPO) và Recovery Time Objective (RTO). “Backup hàng ngày” chỉ có nghĩa khi biết restore mất bao lâu và mất một ngày data có chấp nhận không.

## 1. Backup khác replication

Backup giữ bản sao có thể dùng để phục hồi state ở thời điểm trước. Replication duy trì copies để availability/read scale và có thể sao chép nhanh operator mistake như `delete` nhầm. Read replica không tự là backup. Cần backup độc lập, retention và restore test.

PostgreSQL có logical dump phù hợp database nhỏ/portable restore và physical/base backup cùng Write-Ahead Log (WAL) cho phục hồi quy mô lớn hoặc point-in-time recovery. Core track không cấu hình commands vendor-specific vì deployment khác nhau; điều bắt buộc là policy nêu phạm vi, lịch, encryption, retention, location tách biệt và người có quyền restore.

Backup chưa từng restore chỉ là hy vọng. Restore drill định kỳ tạo database mới, kiểm tra schema/row counts/invariants và đo thời gian. Với dữ liệu nhạy cảm, môi trường restore phải có access control và masking tương ứng.

## 2. WAL, MVCC và vacuum ở mức vừa đủ

PostgreSQL ghi thay đổi vào WAL để hỗ trợ durability/recovery trước khi data pages cuối cùng được flush. MVCC giữ nhiều row versions để transactions đọc snapshot; update thường tạo version mới. Dead tuples cần vacuum để space có thể reuse và tránh transaction ID wraparound. Auto-vacuum vì vậy là cơ chế correctness/maintenance, không phải optional cleanup có thể tắt tùy ý.

Planner cần statistics từ analyze. Sau bulk load lớn hoặc distribution đổi mạnh, analyze giúp estimates đúng hơn. Theo dõi `last_autovacuum`, `last_autoanalyze`, dead tuples và table/index growth trên busy tables; chỉ tuning thresholds khi measurement cho thấy defaults không theo kịp.

Không cần hiểu file pages sâu để dùng database tốt, nhưng mental model giải thích vì sao long transaction gây bloat, vì sao update-heavy table cần maintenance và vì sao disk usage không giảm ngay sau delete.

## 3. Monitoring từ symptom tới action

Các signals tối thiểu gồm availability/error rate, connection usage/wait, query latency, slow queries, lock waits/deadlocks, disk growth, replication lag nếu có và backup/restore status. Mỗi alert phải có owner và action; alert CPU cao đơn độc ít ý nghĩa nếu query latency vẫn ổn và không có capacity risk.

Khi database chậm, đừng restart hoặc thêm index ngẫu nhiên. Xác định scope: mọi queries hay một endpoint, pool hay server, lock wait hay I/O, change nào vừa deploy. Dùng application traces, active sessions, slow-query statistics và `EXPLAIN` ở môi trường an toàn. Containment có thể tắt report nặng hoặc rollback release trước khi root-cause fix.

Capacity planning theo trends thay vì đợi disk đầy. Data, indexes, WAL/replication và temporary files đều dùng storage. Growth forecast không cần mô hình phức tạp; weekly size và traffic trend cùng headroom đã tốt hơn không quan sát.

## 4. Availability và consistency trade-off

Primary-replica setup có thể cho reads scale/failover, nhưng replicas có lag. Đọc order vừa tạo từ asynchronous replica có thể chưa thấy; read-your-writes flow nên đọc primary hoặc có consistency strategy. Failover cũng cần test: application reconnect, DNS/service discovery, transaction đang chạy và sequence/state behavior.

High availability làm operation phức tạp hơn. Một ứng dụng nhỏ có managed PostgreSQL với backup và single-region failover provider-managed thường phù hợp hơn tự dựng cluster. Requirement, budget và team capacity quyết định, không phải sơ đồ architecture đẹp.

## 5. Scale theo bottleneck thật

Scale đầu tiên thường là sửa query, index, connection pool và data lifecycle. Sau đó tăng CPU/RAM/storage (vertical scale) thường đơn giản. Read replicas giúp read-heavy workloads chấp nhận lag. Partitioning giúp quản lý table rất lớn theo time/key và pruning/maintenance, nhưng không làm mọi query nhanh; query phải có partition key phù hợp.

Sharding chia data qua database nodes và tạo routing, cross-shard query, transaction, rebalancing cùng operational complexity. Chỉ dùng khi một database được tối ưu và scale hợp lý vẫn không đáp ứng, hoặc data ownership/region tạo boundary bắt buộc. Phần lớn project học tập và nhiều production apps chưa cần sharding.

## 6. Chọn data store theo access pattern

Relational database là default mạnh khi facts có relationships, transactions, constraints và queries thay đổi. Key-value store phù hợp lookup theo key và cache/session. Document store phù hợp aggregate documents có shape biến đổi và ít cross-document transaction/join. Search engine phù hợp full-text, fuzzy relevance và facets. Time-series system tối ưu ingest/retention/query theo time; graph database hữu ích khi traversal nhiều hops là workload trung tâm.

Không chọn bằng câu “data của tôi là JSON” vì PostgreSQL cũng lưu JSONB. Hỏi operations chính, consistency, transaction boundary, query flexibility, scale và team có thể vận hành gì. Một store chuyên dụng chỉ tạo value khi strength của nó giải quyết bottleneck quan trọng hơn cost đồng bộ với source of truth.

Polyglot persistence nghĩa dùng nhiều stores có chủ ý, không dùng một công nghệ cho mỗi entity. Khi order ở PostgreSQL và search index là derived copy, phải có pipeline cập nhật, xử lý retry, rebuild và staleness. Source of truth cần rõ.

## 7. Operational data và analytical data

OLTP schema tối ưu correctness của writes. Analytics thường cần scan lịch sử, join nhiều domains và metrics nhất quán. Ban đầu report nhỏ có thể query primary hoặc replica với timeout. Khi workload ảnh hưởng transactions hoặc cần historical snapshots, ETL/ELT sang warehouse mới hợp lý.

Analytical model có thể denormalize thành facts/dimensions để query dễ và nhanh. Nó không thay schema transactional; hai models phục vụ questions khác. Data pipeline cần freshness expectation, quality checks và lineage vừa đủ để metric không thành con số không nguồn.

Không stream mọi thay đổi chỉ để “realtime”. Nếu dashboard cập nhật mỗi sáng đáp ứng decision, batch đơn giản hơn và dễ phục hồi. Freshness là requirement, không phải prestige.

## 8. Data lifecycle và privacy

Dữ liệu không nên được giữ mãi theo mặc định. Retention policy nói loại data nào giữ bao lâu, archive/delete ra sao và legal/business reason. Xóa personal data có thể phải lan tới backups, logs, search indexes và analytics theo policy thực tế. Tokenization/anonymization có thể giữ utility mà giảm risk, nhưng phải kiểm tra khả năng re-identification.

Production data không nên copy tự do vào local/test. Dùng synthetic data hoặc masked subset. Quyền backup thường nhạy hơn runtime vì backup chứa toàn database; encrypt và audit access.

## 9. Final case review — cửa hàng nhỏ

Core design dùng PostgreSQL làm source of truth cho customers, products, orders/items. Create order là transaction ngắn với stock condition và idempotency. Customer history dùng composite index theo access pattern. Application dùng parameterized queries, bounded pool và runtime role giới hạn. Migrations tương thích code cũ/mới; backup có restore drill.

Chưa cần Redis nếu latency đạt mục tiêu, chưa cần search engine nếu product search đơn giản, chưa cần warehouse nếu report nhỏ và chưa ảnh hưởng OLTP. Khi requirement xuất hiện, derived stores được thêm với source-of-truth và rebuild path rõ. Đây là “không over-engineer” ở mức architecture: không từ chối scale, chỉ yêu cầu evidence trước complexity.

## 10. Final practice — data system review

Viết review hai trang cho một booking system. Trang đầu gồm schema core, một transaction, query quan trọng và index. Trang hai gồm RPO/RTO, backup/restore test, runtime privileges, năm monitoring signals và decision về cache/search/warehouse. Với mỗi store được thêm, nêu access pattern, source of truth và behavior khi đồng bộ trễ.

Bài đạt khi backup khác replica, transaction không kéo qua network call, index có query evidence và architecture có thể bắt đầu bằng một relational database.

## One-minute summary

Data system tốt giữ facts có meaning, bảo vệ invariants, xử lý concurrency, trả query trong budget và phục hồi được khi con người hoặc máy móc sai. SQL, constraint, transaction, index và backup là các phần của cùng một contract. Hãy bắt đầu với schema rõ và một database; chỉ thêm technology khi workload thật buộc bạn trả thêm complexity.

Hoàn thành module này là kết thúc core track. Extension tiếp theo nên theo vai trò: analytical SQL/data engineering, PostgreSQL administration, database internals hoặc distributed data systems.

