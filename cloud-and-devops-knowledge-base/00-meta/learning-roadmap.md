# Learning Roadmap — Cloud and DevOps Practical Foundations

> **Thời lượng gợi ý:** 10–14 giờ core, cộng 10–16 giờ thực hành  
> **Prerequisites:** Lập trình, Git và terminal cơ bản  
> **Case study:** Learning Journal API với PostgreSQL

## Đích đến

Sau lộ trình, người học có thể đưa một web service nhỏ lên cloud và giữ nó hoạt động mà không coi deployment là thao tác chép file bí ẩn. Bạn sẽ hiểu artifact nào được build, runtime cần gì, provider chịu phần nào, đội chịu phần nào, release được kiểm soát thế nào và bằng chứng nào cho thấy người dùng đang nhận dịch vụ tốt.

Đích đến không phải thuộc hàng trăm sản phẩm cloud. Một service nhỏ thường chỉ cần compute, network endpoint, managed database, object storage, identity, secret store và monitoring. Ta học sâu contract giữa các phần này trước khi học thêm sản phẩm.

## Bản đồ học tập

```text
01. Value flow + shared responsibility
                 ↓
02. Process + network + configuration
                 ↓
03. Container image + runtime contract
                 ↓
04. Compute + network + data on cloud
                 ↓
05. Environments + IaC + identity
                 ↓
06. CI/CD + safe release + rollback
                 ↓
07. Signals + reliability + security + incidents
                 ↓
08. Complete production-readiness review
```

Module 1–3 tạo mental model từ source code tới process có thể chạy lặp lại. Module 4–5 đặt process đó vào cloud với boundary quyền và môi trường rõ. Module 6 xây vòng delivery. Module 7–8 kiểm tra hệ thống bằng góc nhìn người dùng và operator.

## Nhịp học gợi ý

Trong năm buổi, buổi đầu học Module 1–2 và chạy một service local bằng biến môi trường. Buổi hai học Module 3, build image và kiểm tra shutdown/health. Buổi ba học Module 4–5 rồi vẽ deployment nhỏ bằng dịch vụ managed. Buổi bốn xây pipeline của Module 6. Buổi cuối học Module 7–8, thực hiện một failure drill và viết production-readiness review.

Nếu chỉ có sáu giờ để review, ưu tiên Module 1, 2, 3, 6, 7 và phần decision record của Module 8. Nếu đang theo vai trò operations/platform, học kỹ Module 4–7. Nếu là application developer, đừng bỏ Module 2 và 7: rất nhiều lỗi “hạ tầng” thực ra bắt nguồn từ process, timeout, connection pool, migration hoặc thiếu telemetry trong code.

## Checkpoint 1 — Giải thích được runtime

Sau Module 2, bạn cần theo được đường đi `domain → DNS → HTTPS endpoint → application port → handler → database`. Bạn phải biết process nhận cấu hình khi khởi động, secret không nằm trong Git, log đi ra standard output, và shutdown cần ngừng nhận traffic trước khi kết thúc request đang chạy.

Artifact nên là runtime contract một trang cho Learning Journal: command khởi động, port, health endpoint, biến cấu hình, secret references, dependency, timeout và shutdown behavior. Không cần cloud account ở checkpoint này.

## Checkpoint 2 — Build một lần, chạy lặp lại

Sau Module 3, bạn cần build một image có tag bất biến, chạy nó local mà không mount source code, và giải thích khác biệt giữa image với container. Image không chứa secret, process không chạy bằng root nếu không cần, base image được cố định có chủ đích, và `.dockerignore` tránh gửi file thừa vào build context.

Container đạt khi cùng digest có thể đi qua các môi trường; không phải khi `docker run` tình cờ hoạt động trên máy người viết. Health check, memory limit, writable data và signal shutdown đều phải được xem xét.

## Checkpoint 3 — Chọn cloud service vừa đủ

Sau Module 4, bạn cần chọn giữa virtual machine, managed container và function dựa trên runtime, traffic, execution time và mức control. Với case mặc định, managed container service là lựa chọn khởi đầu vì service HTTP chạy lâu, stateless và đội không cần quản lý cluster.

Artifact là sơ đồ một region gồm DNS/TLS endpoint, compute, managed PostgreSQL, object storage và secret store. Mỗi arrow ghi protocol hoặc trust boundary. Nếu thêm queue, cache hoặc load balancer riêng, cần chỉ ra requirement thật khiến nó xuất hiện.

## Checkpoint 4 — Môi trường có thể tái tạo

Sau Module 5, infrastructure definition phải có version trong Git, plan được review trước apply, state được bảo vệ và quyền deploy khác quyền runtime. Staging và production dùng cùng module/cấu trúc nhưng khác parameter; không copy-paste hai bộ hạ tầng rồi để chúng trôi xa nhau.

Bạn cần giải thích state hiện tại, desired state và drift. Infrastructure as Code không bảo đảm thay đổi an toàn: rename có thể thành destroy/create, database replacement có thể mất dữ liệu, và provider plan vẫn cần người hiểu tác động.

## Checkpoint 5 — Release có bằng chứng và đường lui

Sau Module 6, một commit hợp lệ tạo một artifact bất biến, qua test và security check vừa đủ, deploy staging, smoke test rồi mới được promote production. Production không rebuild. Pipeline ghi lại commit, artifact digest, thời điểm, actor và kết quả.

Release plan phải nói rõ strategy, health signal và hành động khi lỗi. Rolling update đủ cho phần lớn service nhỏ; canary chỉ đáng dùng khi có traffic và telemetry đủ để so sánh. Database migration phải tương thích phiên bản cũ/mới trong lúc rollout.

## Checkpoint 6 — Vận hành theo mục tiêu người dùng

Sau Module 7, bạn cần định nghĩa một Service Level Indicator cho successful request và một Service Level Objective vừa sức. Dashboard ưu tiên request rate, error, latency và saturation. Alert chỉ phát khi cần hành động, có owner và runbook ngắn.

Failure drill có thể là database unavailable hoặc release tăng lỗi. Bạn cần phát hiện bằng signal, giảm tác động, xác định thay đổi gần nhất, rollback/roll-forward, rồi ghi action item sửa system thay vì tìm người để trách.

## Bài tốt nghiệp

Module 8 yêu cầu một release dossier cô đọng cho Learning Journal hoặc service của bạn. Nó gồm runtime contract, sơ đồ, quyết định compute/data, infrastructure change flow, pipeline, migration strategy, SLO, dashboard, backup/restore và một incident drill. Các phần phải kết nối: health check trong runtime được deployment dùng; SLO quyết định alert; backup đáp ứng RPO/RTO; runtime identity chỉ có đúng quyền application cần.

Không chấm theo số công nghệ. Bài dùng năm managed services với reasoning rõ tốt hơn bài dùng mười lăm component nhưng không biết source of truth hoặc failure behavior.

## Extension paths

Khi phải vận hành nhiều container có scheduling, service discovery, policy và rollout phức tạp, học Kubernetes có lab riêng. Khi hệ thống có asynchronous workload, học queue semantics, idempotency và retry trước khi chọn broker. Khi cần nhiều region, học data consistency, failover và disaster recovery; đừng chỉ nhân đôi compute.

Platform engineers có thể học tiếp GitOps, policy as code, internal developer platform và cluster operations. SRE đi sâu SLO, error budget, capacity, distributed tracing và incident command. Security/DevSecOps đi sâu threat modeling, supply-chain security, cloud identity và detection. FinOps đi sâu allocation, forecast và unit economics.

## Definition of Done

Một module hoàn thành khi bạn giải thích mental model bằng lời mình, chạy hoặc trace ví dụ, thay một assumption rồi dự đoán failure, và tạo artifact được nêu ở checkpoint. Chụp màn hình một deployment thành công chưa đủ. Bạn cần biết phiên bản nào đang chạy, vì sao request tới được nó, dữ liệu tồn tại ở đâu và làm gì khi dependency hoặc release thất bại.
