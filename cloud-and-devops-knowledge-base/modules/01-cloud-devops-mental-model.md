# 1. Cloud and DevOps Mental Model — Từ thay đổi code tới giá trị cho người dùng

> **Thời lượng:** 50–65 phút  
> **Prerequisites:** Biết source code, Git và web application ở mức cơ bản  
> **Status:** Reviewed

## Overview

Người dùng không nhận giá trị từ source code nằm trong repository. Họ nhận giá trị khi một phiên bản đang chạy trả đúng response, đủ nhanh, bảo vệ dữ liệu và có thể phục hồi. Khoảng cách từ commit tới behavior đang chạy là nơi Cloud và DevOps gặp nhau.

Cloud cung cấp tài nguyên bằng API. DevOps tổ chức dòng thay đổi và phản hồi quanh tài nguyên đó. Hai khái niệm không đồng nghĩa: có thể dùng cloud nhưng release thủ công, và có thể áp dụng DevOps trên máy chủ riêng. Điểm chung là trách nhiệm phải đi xuyên từ ý tưởng tới operation.

## 1. Value flow thay vì tool chain

Hãy bắt đầu bằng một thay đổi nhỏ: thêm chức năng tìm ghi chú theo ngày cho Learning Journal. Developer sửa code và test. Build tạo artifact. Pipeline kiểm tra artifact. Deployment đặt nó vào runtime. Monitoring cho biết request thật có thành công. Feedback từ production quay lại backlog.

```text
Need → Change → Review → Build → Release → Operate → Learn
```

Nếu mỗi bước do một nhóm giữ thông tin riêng, handoff chậm và lỗi khó truy ngược. DevOps cố rút ngắn vòng phản hồi bằng ownership chung, automation và telemetry. Mục tiêu không phải deploy nhiều lần mỗi ngày bằng mọi giá; mục tiêu là thay đổi nhỏ, dễ hiểu, có thể kiểm tra và phục hồi.

Một pipeline rất dài nhưng phải đợi ba ngày để biết production hỏng không phải flow tốt. Một pipeline ngắn, kiểm tra rủi ro chính trong mười phút và theo dõi release bằng signal người dùng có thể tốt hơn.

## 2. Cloud là tài nguyên có API

Trước cloud, tổ chức mua server, chờ lắp đặt, cấu hình network và dự đoán capacity từ sớm. Cloud cho phép yêu cầu compute, database hoặc storage qua API, dùng trong phút và trả theo mô hình tiêu thụ. Điều này làm infrastructure có thể được version, review và tự động hóa giống software.

Elasticity là khả năng tăng hoặc giảm tài nguyên theo nhu cầu. Nó khác scalability: hệ thống scalable có thể xử lý tải tăng khi thêm tài nguyên; elasticity là cơ chế điều chỉnh tài nguyên theo tải. Một ứng dụng giữ session trên memory local có thể không scale ngang đúng dù platform tự thêm instance.

On-demand không có nghĩa miễn phí hoặc vô hạn. Một loop tạo resource, log retention quá dài hay database quá lớn đều tạo chi phí. Cost là một system signal giống latency và error, nhưng tối ưu cost trước khi hiểu workload thường dẫn tới lựa chọn sai.

## 3. Service models là ranh giới trách nhiệm

Với virtual machine, provider quản lý datacenter, phần cứng và hypervisor; đội của bạn quản lý operating system, patch, runtime và application. Với managed container service, provider còn quản lý host và scheduler; đội tập trung vào image, config, scaling rule và behavior. Với Software as a Service, đội chủ yếu quản lý account, data, access và cách tích hợp.

Điều quan trọng không phải thuộc IaaS, PaaS, SaaS. Hãy hỏi: ai patch OS, ai backup database, ai rotate certificate, ai xử lý region outage, và guarantee nằm ở đâu? “Managed” luôn có boundary. Managed database có thể tự backup nhưng đội vẫn phải chọn retention, kiểm tra restore và tránh application xóa nhầm dữ liệu.

Shared responsibility cũng áp dụng cho security. Provider bảo vệ phần họ vận hành; bạn chịu trách nhiệm identity, quyền, config, data và application. Cloud không tự làm một bucket public thành private nếu policy của bạn mở nó.

## 4. Reliability là behavior có mục tiêu

“Hệ thống luôn hoạt động” không thể kiểm tra. Một mục tiêu tốt nói behavior, phạm vi và khoảng thời gian, chẳng hạn 99.9% request đọc ghi chú thành công trong 30 ngày, loại trừ request sai từ client. Mục tiêu giúp đội cân bằng delivery với reliability.

Availability 99.9% tương đương khoảng 43 phút không đáp ứng trong 30 ngày nếu đo liên tục. Con số không phải lời hứa tuyệt đối; nó là budget để quyết định. Nếu hệ thống đang tốt hơn mục tiêu, đội có chỗ thử thay đổi. Nếu đang tiêu hết error budget, ưu tiên reliability thay vì tiếp tục release rủi ro.

High availability không đồng nghĩa disaster recovery. Hai instances cùng region có thể chịu một host failure nhưng không giải quyết mất region hoặc xóa dữ liệu. Backup không đồng nghĩa failover. Mỗi cơ chế bảo vệ một failure khác nhau.

## 5. Automation là executable knowledge

Một runbook thủ công có giá trị vì ghi lại cách làm. Khi thao tác lặp lại, ổn định và có thể kiểm tra, automation biến kiến thức đó thành execution nhất quán. Build, test, provision và deploy là ứng viên tốt. Incident diagnosis trong tình huống mới vẫn cần judgment của con người.

Automation kém có thể làm lỗi lan nhanh hơn. Script xóa nhầm chạy tự động nguy hiểm hơn thao tác chậm. Vì vậy automation cần input validation, preview/plan, quyền tối thiểu, log và stop condition. Tự động hóa sau khi hiểu process; không đóng gói sự mơ hồ thành pipeline.

Maturity không được đo bằng số YAML. Nếu deploy thủ công mỗi tháng cho một trang nội bộ ít rủi ro, một checklist tốt có thể đủ. Khi deploy thường xuyên, nhiều người thao tác hoặc failure cost tăng, pipeline có giá trị rõ hơn.

## 6. Feedback loops quyết định tốc độ thật

Fast feedback bắt đầu local: formatter và unit test trả lời trong giây. Integration test kiểm tra contract trong phút. Staging kiểm tra artifact gần production. Production telemetry trả lời bằng workload thật. Đẩy mọi test vào cuối làm developer chờ lâu; bỏ hết test giữa làm lỗi tới người dùng.

Mỗi lớp nên bắt loại lỗi phù hợp. Unit test không phát hiện security group chặn connection. Smoke test không chứng minh mọi business rule. Monitoring không thay test; nó phát hiện điều test và giả định đã bỏ sót.

Một thay đổi nhỏ giúp feedback chính xác hơn vì ít nguyên nhân có thể. Batch release lớn làm rollback và root-cause analysis khó. DevOps vì vậy liên quan trực tiếp tới cách chia work, không chỉ infrastructure.

## 7. Case study và lựa chọn ban đầu

Learning Journal gồm một API, PostgreSQL và tùy chọn file attachment. Tải ban đầu nhỏ, đội hai người. Lựa chọn hợp lý là một repository, một deployable service, managed container compute, managed database và object storage. TLS endpoint có thể do platform quản lý.

Chưa có lý do tách microservices: domain nhỏ, đội nhỏ và không có phần nào cần scale hay release độc lập. Chưa có lý do dùng Kubernetes: một managed container service đã scheduling, restart và rolling deployment. Chưa có lý do multi-region: reliability target và dữ liệu chưa yêu cầu chi phí, replication cùng complexity đó.

Thiết kế này không “thiếu khả năng mở rộng”. Nó giữ các boundary có thể tiến hóa: application stateless, file ở object storage, schema migration có version và artifact bất biến. Khi requirement đổi, ta có nền tảng để thay đổi có chủ đích.

## 8. Các hiểu lầm cần bỏ

DevOps không phải người nhận ticket deploy từ developer. Nếu một vị trí mang tên DevOps nhưng chỉ giữ script và production access, bức tường handoff vẫn còn. Platform team có thể cung cấp paved road, nhưng application team vẫn chịu trách nhiệm behavior của service.

Cloud-native không có nghĩa dùng mọi dịch vụ cloud. Ý nghĩa hữu ích hơn là thiết kế tận dụng automation, replaceable runtime, managed capability và observable behavior. Một monolith được container hóa tốt có thể cloud-friendly hơn mười microservices phụ thuộc thủ công.

“Serverless” không phải không có server; provider quản lý server và billing/scheduling abstraction. “Zero downtime” không phải checkbox; nó phụ thuộc traffic switching, readiness, connection draining, migration compatibility và dependency behavior.

## 9. Mini practice

Chọn một ứng dụng bạn từng làm. Viết một đoạn mô tả value flow từ commit tới người dùng và một đoạn về đường feedback khi release lỗi. Sau đó lập responsibility boundary cho compute, operating system, runtime, application, database backup, access control và incident response nếu dùng managed container cùng managed database.

Bài đạt khi mỗi responsibility có owner rõ và architecture ban đầu không thêm component chỉ vì phổ biến. Chọn một automation đáng làm ngay và một thao tác nên giữ manual có kiểm soát cho đến khi hiểu rõ hơn.

## Checkpoint

Bạn sẵn sàng sang Module 2 khi giải thích được Cloud là delivery model cho tài nguyên, DevOps là cách tối ưu value flow và feedback, còn managed service là chuyển một phần trách nhiệm chứ không xóa trách nhiệm. Mental model cần giữ: công cụ chỉ có giá trị khi làm thay đổi an toàn hơn, nhanh hơn hoặc dễ vận hành hơn.
