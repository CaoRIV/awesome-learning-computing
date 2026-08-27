# 8. A Production-Ready Small Service — Ghép các quyết định thành một hệ thống vận hành được

> **Thời lượng:** 75–90 phút  
> **Prerequisites:** [Module 7](07-observability-reliability-security.md)  
> **Status:** Reviewed

## Overview

Module cuối không thêm một công nghệ mới. Nó kiểm tra các contracts đã khớp với nhau chưa. Runtime health phải được deployment dùng; artifact identity phải xuất hiện trong telemetry; SLO phải dẫn tới alert; backup phải đáp ứng RPO/RTO; runtime identity phải đúng với data path.

Ta thiết kế release đầu cho Learning Journal: API lưu notes và attachment, vài trăm users, đội hai người, một region. Mục tiêu là đủ tin cậy để có user thật nhưng đủ nhỏ để đội hiểu toàn bộ.

## 1. Requirements trước architecture

Core journey là đăng nhập, tạo note, xem danh sách và đọc note. Attachment là optional; nếu object storage tạm lỗi, note text vẫn nên hoạt động. Dữ liệu note là private. Traffic thấp nhưng có thể tăng vào buổi tối. Đội chấp nhận bảo trì ngắn ngoài giờ, nhưng không chấp nhận mất cả ngày notes.

Mục tiêu ban đầu: 99.9% valid API requests thành công trong 30 ngày; 95% read requests dưới 500 ms. RPO database 15 phút và RTO 2 giờ. Một region đủ; managed multi-zone database được cân nhắc theo budget. Các con số là assumptions cần product xác nhận, nhưng chúng đã tốt hơn “highly available”.

Requirements này loại bỏ nhiều kiến trúc. Không cần global active-active, không cần cache trước khi đo, không cần event bus. Cần database bền vững, private object storage, TLS, backup/restore, release an toàn và telemetry.

## 2. Runtime contract hoàn chỉnh

API là một stateless container process chạy non-root, listen `0.0.0.0:8080`. Startup validate `DATABASE_URL`, `OBJECT_BUCKET`, `SIGNING_KEY_REF` và timeout values. Secret được resolve bằng runtime identity. Process ghi JSON logs ra stdout với request ID, route, status, duration và version; note content và token không được log.

`/live` chỉ kiểm tra event loop/process còn đáp ứng. `/ready` kiểm tra initialization và một database ping có timeout ngắn vì database cần cho core journey; object storage không làm service unready vì attachment có thể degrade. Termination chuyển readiness false, drain tối đa 25 giây rồi đóng pool và exit; platform grace period là 35 giây.

CPU/memory khởi đầu từ test nhỏ và platform minimum, sau đó điều chỉnh qua metrics. Database pool mỗi instance bị giới hạn để tổng connection ở max scale còn dưới database budget. Local filesystem chỉ dùng temp upload có size/time cleanup.

## 3. Deployment architecture

```text
Users
  ↓ DNS + HTTPS
Managed container endpoint / ingress
  ↓ healthy traffic
Learning Journal containers ──→ private object storage
  ↓ TLS + least-privilege role
Managed PostgreSQL (private, automated backup)

CI registry ── image digest ──→ deployment
Secret manager ── runtime identity ──→ containers
Telemetry backend ← metrics, logs, traces
```

Compute được chọn là managed container service vì workload là HTTP process lâu dài và đội không cần OS/cluster control. Min instance là một nếu cold start ảnh hưởng mục tiêu; autoscaling theo concurrency/CPU với max bảo vệ database. Database không public. Object access dùng SDK với workload identity hoặc short-lived credential, không hard-code key.

Managed ingress giữ certificate. Chỉ ingress nhận internet; admin endpoint, nếu có, cần authentication/authorization riêng chứ không dựa vào URL bí mật. Một cloud account/project riêng cho production giảm blast radius.

## 4. Data ownership và failure behavior

PostgreSQL là source of truth cho user, note và attachment metadata. Object storage là source of truth cho file bytes. Upload flow tạo object bằng unique key, sau đó ghi metadata; nếu database write lỗi, cleanup job xóa orphan theo age. Download kiểm tra quyền từ database rồi tạo signed URL ngắn hạn.

Nếu database timeout, API fail nhanh với error chuẩn và không retry write mù quáng. Client có idempotency key cho create note để retry không tạo trùng. Nếu object storage lỗi, create/read text vẫn hoạt động; attachment endpoint trả trạng thái tạm không khả dụng. Behavior này được metric riêng.

Backup database có point-in-time recovery đáp ứng RPO giả định; object versioning/lifecycle bảo vệ xóa nhầm theo policy. Restore drill phục hồi database mới và sample objects, chạy invariant checks và smoke test rồi đo RTO.

## 5. Infrastructure và quyền

IaC stack mô tả service, database, bucket, secret references, identities, telemetry sink và DNS. Staging/production dùng cùng modules, khác parameters về size, domain, retention và protection. Remote state mã hóa, lock và chỉ pipeline apply; production resource stateful có deletion protection.

CI build identity đọc source và push registry. Deploy identity update service và chạy migration job, không đọc note data. Runtime identity đọc đúng secret version/reference, connect database bằng runtime role và access object prefix. Human dùng SSO/MFA; break-glass admin được audit.

Security group/policy chỉ cho application path tới database. Bucket public access bị chặn. Encryption bật nhưng key-management complexity theo requirement; provider-managed key đủ cho baseline nếu compliance không yêu cầu customer-managed key.

## 6. Delivery sequence

Pull request chạy format, unit tests và integration tests với PostgreSQL disposable. Main branch build một image, scan dependency/image, publish commit tag và digest, rồi tạo release metadata. Staging deploy digest, chạy migration job và smoke test create/read/delete synthetic note.

Production approval hiển thị commit diff, digest, checks, migration summary và plan. Rolling update giữ old/new instances cùng lúc. Readiness kiểm soát traffic. Pipeline theo dõi error rate, p95 và restart sau rollout. Nếu abort condition xảy ra, rollback previous digest khi schema compatible.

Migration theo expand-and-contract. DDL role chỉ tồn tại trong migration job. Backfill lớn chạy batch ngoài request path và có progress metric. Xóa schema cũ là release riêng sau khi telemetry chứng minh không còn reader/writer cũ.

## 7. Observability nối với mục tiêu

Dashboard đầu có request rate, valid success ratio, p50/p95 latency theo core route, 5xx, instance restart, CPU/memory, database pool wait/query latency và object storage error. Deployment markers hiển thị digest/version. Log có trace/request ID; trace sample các request và giữ error đầy đủ hơn.

Availability SLI đếm valid core API requests không phải 5xx/timeout. Authentication failure do credential sai không tính server failure; authorization 5xx do bug thì có. Latency SLI đo server-side journey đã thống nhất. Synthetic probe từ ngoài gọi login/read test note để phát hiện DNS/TLS/ingress chứ không chỉ process.

Page alert dựa trên nhanh/chậm error-budget burn và synthetic critical failure. Pool saturation trend tạo ticket trước khi outage. Mỗi alert có runbook và owner. Log retention cân bằng investigation, privacy và cost.

## 8. Security và abuse cases

Threat chính gồm user đọc note người khác qua broken object authorization, token bị lộ, malicious upload, dependency/image bị chèn và credential CI bị lạm dụng. Application kiểm tra owner/permission theo authenticated subject trên mọi note/object operation; không chỉ ẩn ID trên UI.

Upload giới hạn size/type, dùng random object key, không serve active content từ application origin nếu rủi ro, và có malware scanning khi requirement cần. Signed URL ngắn và scoped một object. Rate limit login/create theo identity và nguồn phù hợp; audit admin action.

CI không đưa secret cho untrusted pull request, action được pin, artifact có digest/provenance. Base/dependencies được cập nhật định kỳ và urgent path cho exploitable critical vulnerability. Security finding có owner và risk decision, không chất thành report không ai xử lý.

## 9. Capacity và cost

Đội load test critical read/write với data volume gần sáu tháng dự kiến. Kết quả tạo initial instance size, database tier, pool và max scale. Headroom đủ cho burst và một instance thay trong rolling update. Database storage/connection và object growth có trend alert.

Monthly budget gồm compute baseline, database, storage, telemetry và egress. Tags gắn service/environment/owner. Non-production scale down khi không dùng nếu startup data không mất. Log sampling/retention được điều chỉnh theo giá trị, không tắt error telemetry chỉ để giảm hóa đơn.

Cost review theo unit như cost trên một nghìn active users khi sản phẩm tăng. Không tối ưu sớm bằng reserved commitment dài hạn trước khi workload ổn định.

## 10. Hai failure drill trước launch

Drill thứ nhất deploy một version làm list query chậm ở staging. Expected path: latency/pool metric tăng, trace chỉ query, deployment marker nối release, pipeline abort hoặc operator rollback, service phục hồi trong mục tiêu. Sau drill, kiểm tra alert có quá chậm/noise và runbook có đủ quyền/link.

Drill thứ hai restore database từ backup vào environment cô lập. Đội đo từ lúc quyết định tới khi application smoke test qua, kiểm tra record count, recent note theo RPO và attachment references. Nếu quá RTO, cải thiện automation/runbook hoặc điều chỉnh objective có thỏa thuận; không chỉ ghi “backup enabled”.

Có thể thêm credential rotation drill: tạo credential mới, rollout, xác nhận old instances đã drain rồi revoke cũ. Drill chứng minh secret lifecycle thay vì chỉ secret storage.

## 11. Production-readiness review

Review bắt đầu từ user journey và objective, sau đó trace xuống runtime, dependency, release và recovery. Mỗi câu trả lời cần link tới artifact: runtime contract, IaC plan, pipeline run, dashboard, backup policy hoặc runbook. Câu “provider tự lo” phải được thay bằng tên capability và phần đội vẫn chịu.

Một review tốt sẽ tìm được rủi ro còn lại. Ví dụ, một region nghĩa region outage vượt availability target trong thời gian sự cố; đội chấp nhận vì xác suất/chi phí hiện tại và có backup ở boundary phù hợp. Đây là risk decision minh bạch, không phải giả vờ hệ thống không thể hỏng.

Launch không cần hoàn hảo. Nó cần owner, signal và response cho rủi ro lớn nhất. Những improvement chưa cần ngay được ghi decision/backlog với trigger, chẳng hạn chuyển multi-zone khi số user/doanh thu vượt mức hoặc RTO giảm.

## 12. Final practice — release dossier

Tạo release dossier cho service của bạn, khoảng sáu đến mười trang cô đọng. Nội dung gồm requirements/SLO, runtime contract, deployment/data diagram, responsibility boundary, IaC change workflow, identity matrix, pipeline/deployment/migration, dashboard/alerts, backup restore và hai failure drills.

Mỗi component phải trả lời lý do tồn tại và failure behavior. Mỗi goal reliability phải nối tới phép đo. Mỗi durable data store phải có backup/restore hoặc lời giải thích. Mỗi production permission phải có principal. Mỗi release phải nối commit với digest đang chạy.

Bài đạt khi một người khác có thể dùng dossier để deploy, xác nhận health, điều tra một alert và phục hồi mà không cần trạng thái ẩn trên laptop của bạn. Nếu dossier cần thêm Kubernetes, queue hoặc cache, phải có workload evidence và owner vận hành.

## One-minute summary

Cloud và DevOps tốt biến thay đổi thành một flow có thể hiểu: source tạo artifact bất biến; runtime có contract; cloud service được chọn theo workload; infrastructure và identity có version/boundary; pipeline tạo bằng chứng; telemetry đo behavior người dùng; backup, rollback và incident practice tạo đường phục hồi.

Không overengineering không có nghĩa làm sơ sài. Nó nghĩa dùng giải pháp đơn giản nhất vẫn đáp ứng requirement và giữ đường tiến hóa. Một service, managed container, managed database, object storage và pipeline rõ có thể là production architecture đúng.

Hoàn thành module này là kết thúc core track. Hướng học tiếp nên xuất phát từ bottleneck hoặc vai trò thật: Kubernetes/platform engineering, SRE, cloud security, distributed systems, networking sâu hoặc FinOps.
