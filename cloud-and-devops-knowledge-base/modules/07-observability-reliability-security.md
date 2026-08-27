# 7. Observability, Reliability and Security — Biết người dùng đang nhận gì và phục hồi được

> **Thời lượng:** 70–90 phút  
> **Prerequisites:** [Module 6](06-ci-cd-and-safe-delivery.md)  
> **Status:** Reviewed

## Overview

Một service chưa production-ready chỉ vì deploy thành công. Đội cần biết behavior bên ngoài có đạt mục tiêu, tìm nguyên nhân khi không đạt, bảo vệ dữ liệu và khôi phục sau lỗi. Observability, reliability và security không phải ba lớp trang trí cuối; chúng dựa trên runtime, architecture và delivery đã thiết kế từ đầu.

Module này chọn một bộ signal nhỏ nhưng có quan hệ rõ với user impact. Mục tiêu là trả lời được “đang hỏng gì, ảnh hưởng ai, thay đổi nào liên quan, làm sao giảm tác động”, không phải thu thập mọi dữ liệu có thể.

## 1. Monitoring và observability

Monitoring kiểm tra câu hỏi đã biết bằng dashboard/alert, chẳng hạn error rate có vượt ngưỡng. Observability là khả năng suy ra internal state từ outputs để điều tra câu hỏi chưa dự đoán. Logs, metrics và traces là dữ liệu; khả năng điều tra còn phụ thuộc context, correlation và thiết kế application.

Telemetry phải bắt đầu từ user journey. Với Learning Journal, create/read note là behavior chính. CPU là signal tài nguyên, nhưng user không mua CPU thấp; họ cần request thành công và nhanh. Dashboard đầu tiên vì vậy ưu tiên traffic, errors và latency theo route/version, sau đó mới resource saturation.

Không thu thập vô hạn. Telemetry có cost, privacy và retention. Chọn field/label cardinality có kiểm soát; user ID làm metric label có thể tạo hàng triệu series. ID chi tiết phù hợp log/trace có access policy.

## 2. Metrics cho xu hướng và cảnh báo

Counter đếm event tăng dần như requests/errors. Gauge biểu diễn giá trị hiện tại như queue depth hoặc active connections. Histogram phân phối latency theo buckets và cho percentile gần đúng. Average latency có thể che một nhóm request rất chậm; p95/p99 giúp thấy tail nhưng cần đủ traffic để ổn định.

Bốn golden signals hữu ích cho request service: latency, traffic, errors và saturation. Không cần dashboard một trăm biểu đồ. Một trang có request rate, success/error rate, p50/p95 latency, instance CPU/memory, database pool wait và deployment marker đã đủ để bắt đầu.

Metric cần dimensions thấp và hữu ích: service, environment, route template, status class, version. Không dùng raw URL chứa note ID làm route label. Deployment marker cho biết khi nào version đổi để correlation.

## 3. Logs kể event có context

Structured log ghi timestamp, severity, service, environment, version, event, request/trace ID và fields liên quan. Request log có method, route template, status, duration; error log có error type và stack ở nơi kiểm soát. Không log access token, password, full authorization header hoặc note content mặc định.

Correlation ID nối request qua components. Trace ID tốt hơn khi có distributed tracing. Nếu user báo request ID, operator tìm được log tương ứng. Clock synchronization và timestamp UTC giúp timeline đúng.

Retention theo giá trị và nghĩa vụ, không giữ mãi. Debug logs có thể ngắn; audit/security logs có policy riêng và quyền chặt hơn. Sampling giảm high-volume success logs nhưng error và rare event cần giữ đủ.

## 4. Traces cho đường đi của một request

Trace gồm spans biểu diễn application handler, database query và outbound call. Nó trả lời thời gian nằm ở đâu và dependency nào lỗi. Trong monolith nhỏ, trace vẫn hữu ích khi request đi qua database/object storage, nhưng chưa cần self-host một tracing cluster nếu managed APM/log timing đã đủ.

Instrument bằng chuẩn như OpenTelemetry giúp giảm coupling với backend quan sát, nhưng instrumentation phải giữ semantic tốt: operation name ổn định, status đúng, attributes không chứa dữ liệu nhạy cảm. Sampling có thể giữ mọi error và một phần success.

Trace không thay query plan hoặc profiler. Nó chỉ định span database chậm; điều tra tiếp cần query/lock metric và plan.

## 5. SLI, SLO và error budget

Service Level Indicator (SLI) là phép đo behavior, chẳng hạn tỷ lệ request hợp lệ trả status thành công dưới 500 ms. Service Level Objective (SLO) là mục tiêu cho SLI trong cửa sổ, chẳng hạn 99.9% trong 30 ngày. Service Level Agreement (SLA) là cam kết kinh doanh/pháp lý có consequence; không dùng lẫn.

Ví dụ availability SLI:

```text
good requests / valid requests
```

Định nghĩa valid phải rõ: loại health checks và client request sai, nhưng không loại server error chỉ để số đẹp. Latency có thể là SLI riêng: tỷ lệ read-note hoàn tất dưới 500 ms.

SLO không nên luôn 100%. Hệ thống hoàn hảo có chi phí vô hạn và ngăn thay đổi. Error budget là phần lỗi được phép theo mục tiêu. Burn-rate alert nhìn tốc độ tiêu budget qua cửa sổ nhanh và chậm, tốt hơn alert mỗi error đơn lẻ.

## 6. Alert phải dẫn tới hành động

Page chỉ khi cần người hành động sớm để bảo vệ user/SLO. Ticket phù hợp disk trend hoặc certificate còn nhiều ngày. Dashboard không đồng nghĩa alert. Alert CPU 80% không có context thường gây noise; alert sustained error-budget burn gắn trực tiếp impact hơn.

Mỗi alert có owner, severity, description, dashboard link và runbook. Runbook bắt đầu bằng cách xác nhận impact, thay đổi gần nhất, containment an toàn và escalation. Nó không cần mô tả mọi nguyên nhân; cần giúp người đang căng thẳng đi bước đúng đầu tiên.

Test alert route định kỳ. Một rule đúng nhưng gửi vào channel không ai trực bằng không có alert. Review false positive và missed incident để tuning.

## 7. Reliability patterns theo failure thật

Timeout giới hạn chờ. Retry xử lý transient failure nhưng cần idempotency/backoff. Circuit breaker có thể ngừng call dependency đang hỏng khi repeated failures; chỉ thêm khi client library/platform không đã xử lý và behavior fallback rõ. Bulkhead giới hạn resource cho một dependency/tenant để lỗi không chiếm toàn service.

Load shedding từ chối có kiểm soát khi quá tải để giữ core capacity; queue limit ngăn memory tăng vô hạn. Graceful degradation có thể tắt attachment upload nhưng vẫn cho đọc notes nếu object storage lỗi. Những behavior này phải được product chấp nhận, không tự đoán.

Redundancy giải quyết component failure nhưng không giải quyết bad deploy. Rolling/canary và rollback giải quyết change failure. Backup/restore giải quyết data loss/corruption. Reliability là nhiều lớp cho các failure khác nhau.

## 8. Backup, RPO và RTO

Recovery Point Objective (RPO) là mức dữ liệu có thể mất theo thời gian. Recovery Time Objective (RTO) là thời gian phục hồi chấp nhận. RPO 15 phút và RTO 2 giờ dẫn tới backup/replication/runbook khác RPO 24 giờ, RTO một ngày.

Managed database backup cần retention, encryption và point-in-time capability phù hợp. Object versioning/lifecycle có thể bảo vệ xóa nhầm file. Backup phải ở failure boundary hợp lý và quyền xóa backup tách khỏi runtime. Replica không phải backup vì xóa nhầm có thể replicate.

Restore drill tạo resource mới, phục hồi, kiểm tra schema/count/invariant, chuyển application test tới bản restore và đo thời gian. Backup chưa restore thử chỉ là assumption. Với project nhỏ, drill theo quý hoặc trước thay đổi lớn đã tạo giá trị lớn.

## 9. Security theo lớp và identity

Application xác thực user và kiểm tra authorization trên từng resource. Network giới hạn path nhưng không thay auth. Runtime identity dùng short-lived credential và least privilege. Data mã hóa in transit/at rest. Secret rotate được. CI supply chain tạo artifact có trace.

Input validation, parameterized query, output encoding và dependency update vẫn là application responsibility. Web application firewall có thể giảm một số traffic xấu nhưng không sửa authorization bug. Rate limit theo user/key/IP với hiểu biết proxy giúp giảm abuse, không phải cơ chế access control.

Vulnerability management cần inventory, severity, exploitability, exposure và owner. Critical package có đường khai thác trên public service ưu tiên cao hơn package dev không vào image. Patch base image nghĩa rebuild, test và redeploy; instance đang chạy không tự nhận patch chỉ vì Dockerfile đã đổi.

Audit log ghi hành động quản trị và truy cập nhạy cảm với identity, time, action, target và result. Nó được bảo vệ khỏi sửa và có retention. Không biến mọi application log thành audit log.

## 10. Incident response

Incident bắt đầu khi service behavior vượt mức chấp nhận, không phải khi đã biết root cause. Vai trò tối thiểu có incident lead điều phối, người điều tra/khắc phục và communication owner khi đủ người; đội nhỏ có thể gộp nhưng vẫn tách mạch quyết định khỏi mạch chat hỗn loạn.

Quy trình thực dụng là detect, assess impact, contain, recover, communicate và learn. Containment ưu tiên giảm user impact: rollback release, disable feature, scale bottleneck hoặc chuyển read-only. Root cause có thể tìm sau khi service ổn.

Timeline ghi signal, decision, action và outcome theo UTC. Status update nói impact, điều đang làm và lần cập nhật tiếp theo, tránh đoán nguyên nhân khi chưa có bằng chứng.

Post-incident review không đổ lỗi cá nhân. Hỏi vì sao system cho phép lỗi đi qua, signal nào thiếu, recovery nào chậm. Action item cụ thể, có owner và ưu tiên; “cẩn thận hơn” không thay guardrail.

## 11. Một cuộc điều tra mẫu

Sau release `abc123`, p95 latency tăng từ 180 ms lên 1.8 s và error-budget burn alert chạy. Dashboard cho thấy CPU app bình thường nhưng database pool wait tăng. Trace chỉ ra query list-notes chậm; release marker nối với code mới thêm sort không có index.

Incident lead dừng rollout và rollback digest trước vì schema vẫn tương thích. Metrics trở lại bình thường. Sau đó team dùng query plan, thêm index qua migration an toàn, test data volume đại diện và redeploy. Action items gồm performance test cho query quan trọng và dashboard pool wait; không kết luận “developer viết query kém”.

Ví dụ này cho thấy metric phát hiện impact, trace thu hẹp vùng, release record nối change và rollback giảm tác động. Không công cụ nào tự cung cấp toàn bộ câu trả lời.

## 12. Mini practice

Tạo dashboard design cho service của bạn với tối đa mười panels và giải thích câu hỏi mỗi panel trả lời. Định nghĩa một availability SLI/SLO và một latency SLI/SLO. Viết một alert gắn error-budget burn cùng runbook ngắn.

Thực hiện game day ở staging: làm database unavailable hoặc deploy version trả lỗi cho một route. Quan sát detection, rollback và recovery time. Sau đó viết review gồm timeline, contributing conditions và ba system actions. Bài đạt khi telemetry nối được user impact tới version/dependency, backup có restore test và secret không xuất hiện trong logs.

## Checkpoint

Bạn sẵn sàng sang Module 8 khi có thể phân biệt metrics/logs/traces, định nghĩa SLI/SLO, thiết kế alert có hành động và mô tả recovery bằng RPO/RTO. Mental model cần giữ: production readiness là khả năng phát hiện, giới hạn và học từ failure, không phải lời hứa không bao giờ hỏng.
