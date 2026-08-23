# 7. Application Integration and Change — Kết nối mà không làm mất guarantees

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 6](06-indexes-and-query-performance.md)  
> **Status:** Reviewed

## Overview

Database có constraints và transactions, nhưng application có thể làm yếu chúng bằng cách nối SQL từ input, giữ transaction qua HTTP call, mở quá nhiều connections hoặc deploy code không tương thích schema. Module này tập trung vào boundary giữa code và PostgreSQL: parameters, pool, transaction ownership, migrations và privileges.

Không cần dựng generic repository framework. Một data-access function rõ query và transaction contract thường tốt hơn nhiều layers chỉ chuyển tên method. Abstraction được thêm khi có repetition hoặc boundary thật, không để giấu hoàn toàn SQL.

## 1. Parameterized SQL là boundary bắt buộc

Nối input vào SQL string tạo SQL injection và lỗi quoting. Validate type không đủ nếu vẫn interpolate text. Driver parameters gửi values tách khỏi statement structure.

```python
def find_orders_by_status(connection, status: str, limit: int):
    query = """
        select id, customer_id, status, created_at
        from orders
        where status = %s
        order by created_at desc, id desc
        limit %s
    """

    with connection.cursor() as cursor:
        cursor.execute(query, (status, limit))
        return cursor.fetchall()
```

Placeholder syntax khác giữa drivers (`%s`, `$1`, `?`), nhưng principle giống nhau. Parameters chỉ đại diện values, không đại diện table name hay arbitrary `order by`. Dynamic identifiers phải chọn từ allowlist do code kiểm soát.

Output cũng cần contract. Trả tuple vị trí có thể khó maintain; map về typed record/DTO ở boundary. Không trả database exception/raw rows trực tiếp ra public API.

## 2. ORM là query generator, không phải database replacement

ORM hữu ích cho mapping, migrations và common CRUD. Nó không loại bỏ joins, N+1, indexes, transaction hoặc locking. Hãy xem SQL generated cho query quan trọng và chạy plan khi chậm. Lazy loading trong loop là nguồn N+1 phổ biến; eager/batch loading phải theo access pattern.

Không cần ép mọi query qua domain entities. Report/read model có thể dùng explicit SQL trả projection nhỏ. Ngược lại, không cần bỏ ORM chỉ vì một query phức tạp; dùng cả ORM và SQL có chủ ý là bình thường.

Data-access boundary nên nói rõ function tự mở transaction hay yêu cầu caller truyền transaction. Nếu mỗi repository method tự commit, một business operation qua ba repositories không còn atomic.

## 3. Connection là tài nguyên hữu hạn

PostgreSQL process/session có cost; application không nên mở connection mới cho từng statement mà không quản lý. Connection pool tái sử dụng một số connections giới hạn. Pool size phải dựa trên database capacity và số application instances, không đặt 100 cho mỗi instance theo thói quen.

Nếu database cho phép 100 connections và có 10 pods, pool 20 mỗi pod đã hứa 200 connections. Dành capacity cho migrations, monitoring và admin. Khi pool đầy, requests nên chờ có timeout hoặc fail có kiểm soát, không mở vô hạn.

Connection lấy từ pool phải được trả ở `finally`/context manager; transaction lỗi cần rollback trước khi reuse. Set connect/query/statement timeout phù hợp để runaway query không giữ tài nguyên mãi. Pool metric gồm active, idle, wait time và timeout giúp phân biệt database chậm với pool exhausted.

Pooler bên ngoài như transaction pooling có semantics với prepared statements/session state cần kiểm tra theo driver/deployment. Core lesson là hiểu connection scope trước khi thêm layer.

## 4. Transaction ownership trong application

Một service operation nên own transaction của business unit. Data functions dùng connection/transaction đó và không commit riêng.

```python
def create_order(connection, customer_id: int, items: list[dict]):
    try:
        with connection.cursor() as cursor:
            # Lock/validate/update stock and insert order/items here.
            # Every statement uses parameters.
            ...
        connection.commit()
    except Exception:
        connection.rollback()
        raise
```

Ellipsis ở đây đánh dấu phần workflow đã học ở Module 5, không phải production implementation. Điểm cần thấy là one commit/rollback owner. Trong code thật, context manager của driver có thể quản lý transaction; đọc semantics thay vì giả định.

Đừng bắt đầu transaction ở controller rồi truyền qua hàng chục layers nếu use case đơn giản. Service-level boundary đủ. Nếu operation qua nhiều databases/services, local SQL transaction không thể atomically cover tất cả; cần workflow/idempotency/compensation phù hợp, không giả distributed transaction từ code.

## 5. Migration là code thay đổi contract bền vững

Schema phải được version bằng migration reviewable, chạy cùng quy trình release và không sửa migration đã áp dụng ở shared environment. Migration nên nhỏ, có tên purpose và hiểu lock/rewrite behavior. Backup không thay thế migration plan, nhưng là safety net cho changes rủi ro.

Thay đổi breaking nên dùng expand–migrate–contract. Ví dụ đổi `name` thành `display_name`: đầu tiên thêm column mới nullable, deploy code ghi/đọc tương thích, backfill theo batches, thêm constraints/index cần thiết, chuyển reads, rồi xóa column cũ ở release sau. Không rename/drop cùng lúc code cũ còn chạy.

```sql
alter table customers
add column display_name text;

-- Backfill theo batch trong operation script, không giữ một transaction khổng lồ.
update customers
set display_name = name
where display_name is null
  and id > $1
  and id <= $2;
```

Sau khi verify không còn null, thêm constraint. PostgreSQL không hỗ trợ `add constraint if not exists`; migration framework thường bảo đảm chạy một lần. Nếu script thật sự cần idempotent, kiểm tra catalog rõ thay vì viết syntax không tồn tại.

## 6. Index migration trên table lớn

`create index` thông thường có thể chặn writes trong lúc build. PostgreSQL hỗ trợ `create index concurrently` để giảm blocking, nhưng không được chạy trong transaction block và có failure/cleanup considerations. Migration tool cần cấu hình riêng. Với table nhỏ/local core case, normal create index đủ; production table lớn cần plan, monitoring và rollback.

Thêm `not null`, foreign key hoặc check trên table lớn cũng có validation/lock cost. Đọc version-specific PostgreSQL documentation và test trên volume gần thật. “DDL luôn instant” là assumption nguy hiểm.

## 7. Least privilege và secret handling

Application không nên kết nối bằng superuser hoặc migration owner. Runtime role chỉ có privileges cần cho tables/sequences/functions của feature. Read-only jobs không cần write; service không xóa orders thì không cấp delete.

```sql
create role shop_runtime nologin;

grant usage on schema public to shop_runtime;
grant select, insert, update on customers, products, orders, order_items
to shop_runtime;
grant usage, select on sequence
  customers_id_seq,
  products_id_seq,
  orders_id_seq
to shop_runtime;

create role shop_app login password 'replace-through-secret-management';
grant shop_runtime to shop_app;
```

Ví dụ password là placeholder; credential thật được tạo/rotate qua secret management và không commit. Migration role tách riêng có quyền DDL. Default privileges cần được cấu hình nếu muốn tables tương lai tự nhận grants; review cụ thể thay vì `grant all` rộng.

Row-Level Security (RLS) hữu ích khi database phải enforce tenant/user row access, đặc biệt với direct data APIs. Nó không thay authentication và policy có performance/complexity. Với service backend duy nhất đã filter tenant, RLS có thể là defense-in-depth nhưng không bắt buộc cho core case. Chỉ thêm khi threat model/architecture cần và test policy bằng roles thật.

## 8. Data validation, logging và observability

Application validate format/range sớm, database constraints giữ invariant cuối. Map unique/check/foreign-key errors sang domain response có meaning. Không dùng error text parsing nếu driver cung cấp SQLSTATE/constraint name.

Structured query logs nên có operation name, duration, rows và correlation ID; không log parameter nhạy cảm theo mặc định. Slow-query log và `pg_stat_statements` giúp tìm query tốn tổng thời gian, trong khi distributed trace nối database span với request. Observability phải bảo vệ data giống production database.

## 9. Mini practice

Thiết kế data-access boundary cho endpoint tạo customer và list orders. Chỉ ra parameters, output types, transaction owner, pool timeout và runtime privileges. Sau đó viết expand–migrate–contract plan để tách `customers.name` thành `first_name`/`last_name` mà code cũ và mới có thể chạy chồng một thời gian.

Bài đạt khi không interpolate input, không dùng superuser, migration có compatibility window và backfill không giữ transaction khổng lồ.

## Checkpoint

Bạn cần giải thích parameterization, pool budget, transaction ownership, compatible migration và least privilege. Module cuối đặt những practices này trong lifecycle vận hành và quyết định chọn data store.
