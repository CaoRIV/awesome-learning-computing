# Cloud and DevOps — Practical Foundations

Knowledge base tiếng Việt dành cho người đã biết lập trình cơ bản và muốn đưa một ứng dụng từ máy cá nhân lên môi trường có người dùng thật. Track tập trung vào những câu hỏi nền tảng: application đang chạy trong process nào, request đi qua đâu, cấu hình và secret được đưa vào thế nào, một phiên bản mới được phát hành ra sao, và đội ngũ biết gì khi hệ thống gặp lỗi.

> Source code → Build artifact → Runtime → Cloud service → Release → Observe → Improve

Cloud và DevOps thường bị trình bày như hai catalog công cụ. Cách đó khiến người học biết tên nhiều dịch vụ nhưng không biết vì sao hệ thống cần chúng. Track này đi theo một vertical path: triển khai một dịch vụ web nhỏ có database, sau đó chỉ thêm automation hoặc infrastructure khi một rủi ro thật xuất hiện.

## DevOps không phải tên một vị trí hay một bộ công cụ

Development tạo thay đổi; Operations giữ dịch vụ hoạt động. DevOps là cách tổ chức công việc để hai trách nhiệm này không bị ném qua một bức tường. Người viết thay đổi cũng cần hiểu cách build, release, quan sát và phục hồi nó. Automation hỗ trợ vòng phản hồi đó, nhưng mua một CI tool hoặc dùng Kubernetes chưa tự tạo ra DevOps.

Cloud cung cấp tài nguyên qua API và mô hình thuê theo nhu cầu. Nó giúp một đội nhỏ dùng compute, network, database và storage mà không tự mua phần cứng. Cloud không loại bỏ operation: đội vẫn phải chọn boundary, quyền truy cập, backup, alert và cách xử lý failure. Managed service chuyển một phần trách nhiệm cho provider; phần còn lại phải được nói rõ.

## Track dành cho ai

Baseline phù hợp là người đã viết một ứng dụng nhỏ, biết Git ở mức commit và có thể dùng terminal cơ bản. Không cần từng quản trị server, viết pipeline hay dùng một cloud provider. Ví dụ giữ trung lập giữa AWS, Azure và Google Cloud; tên sản phẩm cụ thể chỉ xuất hiện khi giúp ánh xạ một khái niệm.

Track phù hợp với developer muốn tự triển khai service nhỏ, sinh viên cần mental model trước khi học chứng chỉ cloud, và người mới làm platform/operations. Đây không phải tài liệu luyện thi vendor certification hay hướng dẫn xây platform cho tổ chức hàng trăm đội.

## Bắt đầu học

Đọc [Learning Roadmap](00-meta/learning-roadmap.md), sau đó học tuần tự tám module. Mỗi module cần khoảng 50–75 phút đọc; bài thực hành có thể mất thêm một đến hai giờ.

| # | Module | Kết quả chính |
|---:|---|---|
| 1 | [Cloud and DevOps Mental Model](modules/01-cloud-devops-mental-model.md) | Hiểu value flow, responsibility và lý do cần cloud/DevOps |
| 2 | [How an Application Runs](modules/02-how-an-application-runs.md) | Theo được process, port, DNS, HTTP, config, secret và shutdown |
| 3 | [Containers without the Magic](modules/03-containers-without-the-magic.md) | Đóng gói ứng dụng bằng image nhỏ, cố định và chạy được như production |
| 4 | [Cloud Building Blocks](modules/04-cloud-building-blocks.md) | Chọn compute, network, database và storage theo workload |
| 5 | [Infrastructure and Environments](modules/05-infrastructure-and-environments.md) | Quản lý môi trường bằng cấu hình có phiên bản và quyền tối thiểu |
| 6 | [CI/CD and Safe Delivery](modules/06-ci-cd-and-safe-delivery.md) | Xây pipeline có một artifact, kiểm tra tự động và rollback rõ ràng |
| 7 | [Observability, Reliability and Security](modules/07-observability-reliability-security.md) | Đo behavior người dùng, đặt mục tiêu và phản ứng khi có sự cố |
| 8 | [A Production-Ready Small Service](modules/08-production-ready-small-service.md) | Kết hợp toàn bộ track thành thiết kế release có thể vận hành |

## Case study xuyên suốt

`Learning Journal` là một HTTP API nhỏ cho phép người dùng lưu và xem ghi chú học tập. Application là một process stateless; PostgreSQL giữ dữ liệu; file đính kèm, nếu có, nằm trong object storage. Đội có ba môi trường: local, staging và production. Mục tiêu ban đầu chỉ là vài trăm người dùng, nên một managed container service và managed database thường đã đủ.

Case này cố ý không bắt đầu bằng microservices, message broker, service mesh hay Kubernetes. Khi tải nhỏ và đội ít người, các công nghệ đó chủ yếu tăng số failure modes. Track vẫn giải thích signal để biết lúc nào cần tách workload, thêm queue hoặc dùng orchestrator mạnh hơn.

## Cách học

Đừng học bằng cách ghi nhớ màn hình console của một provider. Sau mỗi module, hãy tự kể lại request từ DNS tới process, dữ liệu nào tồn tại sau khi instance bị thay, phiên bản nào đang chạy và operator sẽ nhìn vào đâu khi người dùng báo chậm.

Mỗi thay đổi hạ tầng cần trả lời ba câu: vấn đề nào đang được giải quyết, failure mới nào được tạo ra, và ai chịu trách nhiệm khi nó hỏng. Một sơ đồ nhiều box không phải dấu hiệu trưởng thành; một hệ thống nhỏ, có boundary và đường phục hồi rõ thường đáng tin hơn.

## Phạm vi và điểm dừng

Core path bao gồm Linux/process/networking vừa đủ, container, các building block cloud, Infrastructure as Code, identity và secret, CI/CD, deployment strategy, observability, reliability, security và incident response. Nội dung ưu tiên managed services và một region cho service nhỏ.

Kubernetes, multi-region active-active, service mesh, GitOps platform, advanced autoscaling, FinOps chuyên sâu và self-hosted observability là extension paths. Chúng chỉ nên được học khi workload hoặc vai trò yêu cầu. Track kết thúc ở khả năng triển khai và vận hành an toàn một dịch vụ nhỏ, không cố biến người học thành cloud architect cho mọi quy mô.

## Exit criteria

Bạn hoàn thành track khi có thể giải thích một request đi tới application thế nào, tạo container image có version bất biến, chọn một compute service phù hợp, tách config khỏi artifact, phát hành cùng một artifact qua staging tới production và kiểm tra health sau deploy. Bạn cũng cần viết được mục tiêu availability đơn giản, tìm một lỗi qua metric/log/trace, mô tả backup và restore test, giới hạn quyền runtime, và đưa ra rollback hoặc roll-forward khi release hỏng.

Quan trọng hơn, bạn phải biết nói “chưa cần”. Nếu một managed container, một managed PostgreSQL và object storage đáp ứng workload, việc không thêm cluster hoặc microservices là một quyết định kỹ thuật có cơ sở.
