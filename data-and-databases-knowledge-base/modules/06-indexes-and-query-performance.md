# 6. Indexes and Query Performance — Tối ưu sau khi nhìn execution plan

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 5](05-writes-transactions-concurrency.md)  
> **Status:** Reviewed

## Overview

Index là cấu trúc dữ liệu phụ giúp database tìm rows mà không đọc toàn table theo một số access patterns. Nó giống mục lục có trật tự hơn một nút “làm nhanh”. Mỗi index chiếm storage, phải cập nhật khi write và tạo maintenance cost. Vì vậy index tốt bắt đầu từ query quan trọng và execution plan, không bắt đầu từ danh sách columns.

PostgreSQL dùng cost-based planner. Planner ước lượng số rows và cost của scan/join/sort dựa trên statistics, rồi chọn plan. Estimate sai hoặc statistics cũ có thể dẫn tới plan kém. `EXPLAIN` cho plan dự kiến; `EXPLAIN ANALYZE` thực thi query và cho actual timing/rows.

## 1. Sequential scan không luôn là lỗi

Sequential scan đọc table theo thứ tự vật lý. Với table nhỏ hoặc query lấy phần lớn rows, nó có thể nhanh hơn nhảy qua index rồi đọc nhiều heap pages. Dấu hiệu cần điều tra là sequential scan trên table lớn khi predicate rất selective và query quan trọng, không phải thấy chữ `Seq Scan` ở bất kỳ đâu.

```sql
explain (analyze, buffers)
select id, customer_id, status, created_at
from orders
where customer_id = 42
order by created_at desc, id desc
limit 20;
```

`analyze` thật sự chạy query. Với `select` thường an toàn trên môi trường test; với write, dùng transaction có rollback và cực kỳ cẩn thận. `buffers` cho biết pages đọc từ cache/disk. So sánh estimated rows với actual rows để phát hiện statistics/selectivity issue; nhìn total execution time nhưng nhớ cache và tải hệ thống làm kết quả dao động.

## 2. Index từ access pattern

Query lịch sử order filter equality trên `customer_id`, sort theo `created_at desc, id desc` và limit. Composite B-tree index theo cùng prefix hỗ trợ tốt:

```sql
create index orders_customer_created_id_idx
on orders (customer_id, created_at desc, id desc);
```

Column order quan trọng. Equality column thường đứng trước range/sort columns. Index trên `(customer_id, created_at)` hỗ trợ query chỉ có `customer_id` và query có cả hai, nhưng không tối ưu cho query chỉ lọc `created_at` theo leftmost-prefix behavior thông thường. Một index riêng trên từng column không luôn thay được composite index vì planner phải combine rồi vẫn sort.

Đừng tạo index trùng. Primary key và unique constraint đã có index. Trước khi thêm, xem indexes hiện tại và các query khác. Tên index nên nói table/columns hoặc purpose để migration và incident dễ đọc.

## 3. Foreign key phía referencing cần index theo usage

PostgreSQL không tự index foreign key columns. `orders.customer_id` cần index cho customer history và để parent delete/update checks không scan toàn orders. `order_items.product_id` cần cho product-to-items joins. Composite primary key `(order_id, product_id)` đã hỗ trợ lookups theo `order_id`, nhưng không theo `product_id` một mình.

Không phải mọi foreign key ở table rất nhỏ cần index ngay, nhưng với core transactional tables đây là default review tốt. Evidence vẫn gồm query/relationship và expected growth.

## 4. Partial index cho subset được query nhiều

Nếu phần lớn orders đã fulfilled nhưng dashboard thường lấy pending, partial index nhỏ hơn full index.

```sql
create index orders_pending_created_idx
on orders (created_at, id)
where status = 'pending';

select id, customer_id, created_at
from orders
where status = 'pending'
order by created_at, id
limit 100;
```

Query predicate phải tương thích với index predicate. Partial index không nên tạo cho mọi status; chỉ khi subset ổn định, selective và access thường xuyên. Status distribution thay đổi có thể làm benefit biến mất.

## 5. Function trên indexed column có thể đổi access

Index thường lưu original expression. Query `where lower(email) = lower($1)` không dùng plain index trên email theo cách trực tiếp. Có thể normalize email khi write hoặc tạo expression index nếu case-insensitive lookup là contract.

```sql
create unique index customers_email_lower_uq
on customers (lower(email));

select id, name, email
from customers
where lower(email) = lower($1);
```

Expression index tăng write cost và semantics của case folding cần phù hợp domain/language. Đây là design decision, không phải mẹo performance chung.

Tương tự, `where created_at::date = $1` có thể khó dùng index `created_at`; range `[start, end)` thường rõ và index-friendly hơn.

## 6. Query shape trước index

N+1 query xảy ra khi application lấy 100 orders rồi chạy thêm một query cho items của từng order. Dù từng query có index, 101 round trips tạo latency. Batch query/join/preload theo ORM phù hợp thường tốt hơn.

```sql
select
  oi.order_id,
  oi.product_id,
  oi.quantity,
  oi.unit_price
from order_items oi
where oi.order_id = any($1::bigint[])
order by oi.order_id, oi.product_id;
```

Pagination sâu bằng offset cũng là query-shape issue; keyset ở Module 4 giữ work gần như ổn định theo page. Lấy `select *` tăng row width và I/O. Trước khi thêm index, giảm data không cần và round trips thường cho thiết kế rõ hơn.

## 7. Join performance và cardinality

Planner chọn nested loop, hash join hoặc merge join dựa trên estimated rows và cost. Không cần ép join algorithm trong core. Hãy kiểm tra join keys được index đúng phía, predicates đủ selective và estimates có hợp lý. `Nested Loop` với inner operation chạy hàng triệu loops là dấu hiệu đáng xem, nhưng nested loop cho 20 outer rows có thể hoàn hảo.

Cardinality estimate phụ thuộc statistics. Sau bulk load hoặc thay đổi lớn, `analyze orders` cập nhật statistics; autovacuum/autoanalyze thường xử lý định kỳ. Đừng tắt autovacuum để “tăng performance” mà không hiểu MVCC dead tuples và wraparound risk.

## 8. Index làm writes đắt hơn

Mỗi insert/update/delete có thể sửa nhiều indexes. Một table với 15 indexes sẽ write chậm hơn, tốn storage và vacuum work hơn. Index ít dùng nên được review bằng production statistics trước khi remove; removal cũng là migration có risk vì có thể có query hiếm nhưng quan trọng.

Index-only scan có thể đọc từ index khi query columns được cover và visibility cho phép, nhưng thêm nhiều included columns không miễn phí. Core path không tối ưu bằng covering index cho tới khi plan chứng minh heap fetch là bottleneck.

## 9. Workflow điều tra query chậm

Bắt đầu bằng query và parameters đại diện, data volume gần production và latency target. Chạy `EXPLAIN (ANALYZE, BUFFERS)` ở môi trường an toàn. Tìm node tốn thời gian, actual/estimated rows lệch, loops lớn, sort spill hoặc rows removed. Sau đó thay đúng một thứ — query shape, index hoặc statistics — rồi đo lại và kiểm tra write/storage trade-off.

Không benchmark chỉ một lần. Warm cache khác cold cache; concurrent load khác local laptop. Mục tiêu core là so plan và order-of-magnitude, không tuyên bố milliseconds phổ quát.

## 10. Mini practice

Tạo đủ orders để customer history có ý nghĩa. Chạy plan trước/sau index `(customer_id, created_at desc, id desc)`, ghi scan type, actual rows, buffers và execution time. Sau đó thêm một index không phục vụ query, giải thích vì sao planner bỏ qua và vì sao không nên giữ chỉ vì đã tạo.

Bài đạt khi conclusion dựa trên plan, không mặc định sequential scan xấu và có nêu write/storage cost của index.

## Checkpoint

Bạn cần giải thích index từ access pattern, composite order, partial index và N+1. Quan trọng nhất: đo query trên data đại diện trước và sau thay đổi. Module 7 nối các guarantees này với connection, migration và quyền của ứng dụng.
