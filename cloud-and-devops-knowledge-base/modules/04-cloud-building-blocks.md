# 4. Cloud Building Blocks — Chọn compute, network và data theo workload

> **Thời lượng:** 65–75 phút  
> **Prerequisites:** [Module 3](03-containers-without-the-magic.md)  
> **Status:** Reviewed

## Overview

Cloud console có hàng trăm sản phẩm, nhưng một web service nhỏ chỉ cần vài capability: nơi chạy process, endpoint để nhận traffic, nơi giữ data, identity để gọi dịch vụ và telemetry. Module này xây decision model trung lập giữa provider, tránh học bằng tên sản phẩm.

Mỗi lựa chọn là đổi control lấy operational work. Virtual machine cho quyền cao nhưng đội patch và vận hành nhiều hơn. Managed service giảm phần đó nhưng đặt constraint về runtime, network hoặc pricing. Lựa chọn tốt phù hợp workload và năng lực đội, không phải lựa chọn có nhiều feature nhất.

## 1. Region và availability zone

Region là vùng địa lý cloud có nhiều datacenter; availability zone (AZ) là failure domain tách tương đối trong region. Đặt hai instances ở hai zones giúp chịu một số lỗi datacenter. Nó không bảo vệ khỏi mọi lỗi region, lỗi account/config hoặc database bị xóa.

Chọn region theo latency tới users, data residency, dịch vụ có sẵn, cost và recovery requirement. Một service mới thường bắt đầu một region. Multi-region tạo data replication, consistency, routing và incident complexity; chỉ đáng khi downtime region vượt risk chấp nhận và đội đã test failover.

High availability trong một region có thể dùng compute nhiều zones và managed database multi-zone. Disaster recovery có backup/copy ở failure domain khác cùng kế hoạch khôi phục. Hai mục tiêu liên quan nhưng không thay thế nhau.

## 2. Compute: VM, managed container hay function

Virtual machine phù hợp khi cần OS control, software đặc biệt, long-running process không vừa managed platform hoặc workload legacy. Đổi lại đội chịu patching, base image, process manager, capacity và nhiều security surface.

Managed container service phù hợp HTTP service/background worker đóng gói bằng image. Provider quản lý hosts và scheduling, đội khai báo image, CPU/memory, health, scaling và identity. Đây là default hợp lý cho Learning Journal vì runtime lâu dài, stateless và không cần cluster API.

Function as a Service phù hợp event handler ngắn, traffic gián đoạn và execution model do provider quản lý. Nó có cold start, duration/concurrency limit và coupling với event/runtime. Chia một service bình thường thành nhiều functions không tự làm architecture đơn giản hơn.

Kubernetes cung cấp scheduling, service discovery, rollout và extensibility nhất quán cho nhiều workloads. Giá của nó là cluster lifecycle, networking, policy, observability và nhiều abstraction. Một service không cần một orchestrator general-purpose chỉ để chạy container.

Hỏi theo thứ tự: workload là request-driven hay batch, chạy bao lâu, state ở đâu, cần OS control gì, scale signal nào, latency/cold-start budget bao nhiêu, và đội có thể trực gì. Tên công nghệ đến sau.

## 3. Network là đường đi và quyền đi

Virtual network/VPC tạo address space và routing boundary. Public endpoint nhận internet traffic; private resources như database không cần public IP. Security group/firewall cho phép flow cụ thể, chẳng hạn compute identity/subnet tới database port, không mở database cho toàn internet.

Một kiến trúc nhỏ có thể là:

```text
Internet
   ↓ HTTPS
Managed ingress / service endpoint
   ↓ internal application traffic
Container instances
   ↓ TLS database connection
Managed PostgreSQL (private)
```

NAT hoặc egress gateway cho private workload gọi internet mà không nhận inbound trực tiếp, nhưng thêm cost và failure/capacity consideration. Nếu platform managed đã trừu tượng network an toàn, không cần tự dựng public/private subnet phức tạp chỉ để giống reference architecture.

Network policy nên theo least connectivity: chỉ path cần thiết được mở. Tuy nhiên private network không thay application authentication và TLS. “Ở trong VPC” không chứng minh caller đáng tin.

## 4. DNS, TLS và traffic entry

DNS record nối domain tới provider endpoint. TLS certificate chứng minh domain và mã hóa kết nối. Managed certificate/renewal giảm operational risk so với copy certificate thủ công. Redirect HTTP sang HTTPS nếu endpoint public.

Ingress hoặc load balancer có thể terminate TLS, route theo host/path, health-check và phân phối traffic. Nếu managed container service đã cung cấp endpoint và TLS phù hợp, load balancer riêng có thể chưa cần. Thêm nó khi cần custom domain, routing, web application firewall hoặc traffic control thực sự.

Client IP, protocol và request ID có thể đi qua proxy headers. Application chỉ nên tin headers từ proxy được kiểm soát; tin trực tiếp `X-Forwarded-For` từ internet làm audit/rate-limit sai.

## 5. Data: database, object storage và cache

Managed relational database phù hợp notes, users và relationships. Provider quản lý host, patch và một số backup/replication, trong khi đội quản lý schema, query, connection, access, retention và restore verification. Database nên private, mã hóa in transit/at rest và runtime role không phải admin.

Object storage phù hợp file attachment: durable, scale theo object và truy cập qua API. Bucket mặc định private; application tạo short-lived signed URL nếu user được phép tải. Database lưu metadata, owner và object key. Không lưu public URL vĩnh viễn cho dữ liệu riêng.

Cache giúp giảm latency hoặc load cho access pattern đã đo. Nó tạo stale data, invalidation và failure mode mới. Learning Journal chưa cần cache nếu database query đạt mục tiêu. CDN hữu ích cho static asset hoặc public content gần users; nó cũng cần cache policy rõ.

## 6. Queue và asynchronous work

Request nên trả nhanh. Công việc như resize file, gửi email hoặc tạo report có thể đưa vào queue để worker xử lý sau. Queue tách thời điểm nhận yêu cầu khỏi thời điểm thực hiện, hấp thụ burst và cho retry.

Queue không đảm bảo job chỉ chạy đúng một lần trong mọi tình huống. Consumer cần idempotent: cùng message chạy lại không tạo hậu quả trùng. Message có ID; database ghi processed state hoặc dùng unique constraint. Retry có giới hạn; message lỗi lâu dài đi dead-letter queue để điều tra.

Đừng thêm queue chỉ để “decouple”. Nó tạo eventual consistency, monitoring và operational ownership. Chỉ thêm khi request latency, burst hoặc failure isolation cần asynchronous boundary.

## 7. Scaling theo bottleneck

Vertical scaling tăng CPU/memory cho instance. Horizontal scaling tăng số instances. Stateless HTTP service thường scale ngang được, nhưng database, connection limit hoặc external API có thể là bottleneck. Autoscaling theo CPU đôi khi không phản ánh queue/latency; chọn signal gần workload như concurrency hoặc queue depth khi platform hỗ trợ.

Scale-to-zero giảm cost cho traffic ít nhưng tạo cold start. Minimum instance giữ latency ổn định nhưng tốn tiền khi idle. Decision dựa trên latency target và traffic, không phải khẩu hiệu serverless.

Load test cần workload đại diện và safety limit. Đo request latency/error, instance saturation và dependency. Nếu query chậm, thêm mười app instances có thể làm database tệ hơn.

## 8. Managed service và lock-in

Managed service tạo value bằng cách chuyển patching, failover hoặc scaling cho provider. Đổi lại API, limits và pricing có thể đặc thù. Lock-in không chỉ là không chuyển provider được; self-hosted stack cũng khóa đội vào operational knowledge và maintenance.

Đánh giá exit cost theo xác suất và tác động. Business data nên export/backup bằng format có thể phục hồi. Application core giữ boundary với provider SDK ở nơi hợp lý. Không xây abstraction chung cho ba clouds khi chưa có requirement; abstraction dự đoán thường che capability hữu ích và tăng code.

Một decision record ngắn nêu context, choice, alternatives và consequence giúp thay đổi sau này. “Cloud-agnostic” không phải mục tiêu miễn phí.

## 9. Cost như một constraint thiết kế

Compute tính theo thời gian/tài nguyên; database thường có baseline cost; storage tính dung lượng và request; network egress có thể đáng kể; logs tính ingest/retention. Estimate ban đầu cần traffic, data size, retention và growth, dù còn thô.

Gắn resource theo project/environment/owner để cost có trách nhiệm. Budget alert phát hiện bất thường nhưng không tự chặn production. Xóa environment tạm khi không dùng và đặt log retention. Đừng giảm redundancy hoặc backup mà không liên hệ reliability target.

Unit cost như chi phí trên một nghìn requests hoặc một active user hữu ích hơn hóa đơn tổng khi product tăng trưởng. Với project học tập, một cost ceiling tháng và danh sách resource là đủ.

## 10. Kiến trúc Learning Journal

Một region chứa managed container service chạy ít nhất một instance theo latency target, managed PostgreSQL private và object storage private. Managed ingress giữ TLS/domain. Runtime identity đọc secret database, truy cập đúng bucket prefix và gửi telemetry. CI identity chỉ push image; deploy identity chỉ update service. Backup database có retention và restore drill.

Chưa có queue cho đến khi email/processing làm request chậm. Chưa có cache cho đến khi query metrics chứng minh bottleneck. Chưa có Kubernetes, multi-region hoặc service mesh. Kiến trúc vẫn scale được theo bước: tăng instance, tối ưu database, thêm worker/queue khi có async workload.

## 11. Mini practice

Vẽ sơ đồ deployment cho service của bạn với tối đa bảy building blocks. Ghi data nào durable, endpoint nào public, connection nào private/mã hóa và identity nào thực hiện call. Viết decision record chọn compute giữa VM, managed container và function.

Thêm một failure: database unavailable hoặc object storage timeout. Mô tả user behavior, timeout, retry và signal. Bài đạt khi database không public, state không nằm ở compute, và mọi component ngoài core có requirement cụ thể.

## Checkpoint

Bạn sẵn sàng sang Module 5 khi có thể chọn cloud capability theo workload và responsibility, không theo logo. Mental model cần giữ: architecture là tập contracts và failure boundaries; càng thêm building block càng phải trả operational cost.
