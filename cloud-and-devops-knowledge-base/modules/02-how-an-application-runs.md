# 2. How an Application Runs — Process, network, config và lifecycle

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 1](01-cloud-devops-mental-model.md)  
> **Status:** Reviewed

## Overview

Deployment cuối cùng phải tạo ra một process nhận request. Nếu không hiểu process đọc config, mở port, gọi dependency và dừng như thế nào, cloud console chỉ che lỗi bằng nhiều lớp giao diện. Module này xây runtime contract đủ dùng cho VM, container lẫn managed platform.

Ta không học Linux như một khóa quản trị riêng. Ta học đúng phần ảnh hưởng tới application: process, file, permission, signal, DNS, TCP, HTTP, environment, timeout và resource limit.

## 1. Từ command tới process

Khi chạy `./learning-journal` hoặc `node server.js`, operating system tạo process với executable, arguments, environment, working directory, user identity và resource limits. Process có PID để hệ điều hành theo dõi. Nếu process chính kết thúc, platform thường coi workload đã dừng và có thể restart nó.

Application nên chạy foreground trong container và ghi log ra standard output/error. Không tự daemonize hoặc giấu log vào file local nếu platform đã thu stdout. Một process model đơn giản làm signal, exit code và health dễ quan sát.

Exit code `0` thường nghĩa kết thúc bình thường; non-zero báo lỗi. Crash loop xảy ra khi platform liên tục restart process không thể khởi động, thường do config thiếu, dependency không reachable hoặc startup command sai. Restart có thể phục hồi lỗi tạm thời nhưng không sửa lỗi cấu hình cố định.

## 2. Filesystem không phải nơi mặc định cho state lâu dài

Process nhìn thấy filesystem, nhưng trên cloud instance hoặc container, local disk có thể biến mất khi workload được thay. Source code, temporary files và cache có thể ở local disk. User upload hoặc business data cần database/object storage nếu phải tồn tại qua replacement.

Phân biệt read-only application files, temporary data và durable data. Image chứa application files. Temporary directory có thể giữ file đang xử lý và phải có size limit/cleanup. Durable attachment được upload object storage rồi database giữ object key. Việc này cho phép instance bị thay mà dữ liệu vẫn còn.

Không ghi secret vào image hoặc file config commit trong repository. Filesystem permission vẫn quan trọng: process chỉ cần đọc application và ghi đúng temporary path. Chạy bằng root làm một lỗ hổng application có phạm vi tác động lớn hơn.

## 3. Port, interface và đường đi của request

Server process bind một địa chỉ và port, chẳng hạn `0.0.0.0:8080`. `127.0.0.1` chỉ nhận traffic trong cùng network namespace; vì vậy application bind localhost trong container thường không được platform gọi tới. Port là endpoint logic của transport, không phải cổng vật lý.

Đường request có thể được trace như sau:

```text
api.example.com
  → DNS trả endpoint
  → client mở TCP connection và TLS
  → managed ingress/load balancer nhận HTTPS :443
  → ingress chuyển HTTP tới instance :8080
  → router của application chọn handler
  → handler query database qua connection riêng
```

DNS ánh xạ name tới endpoint và có cache theo TTL. TLS xác thực server và mã hóa traffic. Load balancer phân phối request giữa healthy instances; nó không tự biết application thực sự sẵn sàng nếu health check thiết kế kém.

Khi debug “không kết nối được”, đi từng lớp: name có resolve không, route/firewall có cho phép không, connection có mở không, TLS có hợp lệ không, server có listen đúng interface/port không, rồi mới nhìn HTTP status và application log. Đổi config ngẫu nhiên làm mất bằng chứng.

## 4. Timeout là boundary của sự chờ đợi

Network có thể chậm hoặc mất response. Mọi outbound call nên có timeout. Không timeout nghĩa request có thể treo, giữ thread, memory và database connection cho tới khi service cạn tài nguyên. Timeout phải theo budget từ ngoài vào trong: nếu client chờ 3 giây, handler không thể cho database query chờ 10 giây.

Retry chỉ phù hợp lỗi tạm thời và operation an toàn để lặp. GET thường dễ retry hơn POST tạo dữ liệu. Với write, idempotency key giúp cùng logical request không tạo hai records. Retry cần giới hạn, backoff và jitter; retry tức thời từ mọi instance có thể làm dependency đang yếu bị quá tải nặng hơn.

Connection pool cũng là resource boundary. Pool quá lớn không làm database nhanh vô hạn; nhiều instances nhân số connection có thể vượt database limit. Chọn pool theo concurrency và capacity, theo dõi wait time, và đóng connection đúng lifecycle.

## 5. Configuration là input của deployment

Artifact nên giống nhau giữa staging và production. Khác biệt nằm ở configuration: port, database endpoint, log level, feature flag và external URL. Environment variables là cơ chế phổ biến cho giá trị nhỏ; file mounted hoặc configuration service phù hợp cấu trúc lớn hơn.

Application phải validate config khi startup và fail với thông báo rõ. Nếu `DATABASE_URL` thiếu, dừng trước khi nhận traffic tốt hơn chờ request đầu tiên mới lỗi. Giá trị mặc định chỉ dùng khi thực sự an toàn; production không nên vô tình dùng database local vì biến bị thiếu.

Secret là config nhạy cảm như password, API key hoặc signing key. Nó được giữ trong secret manager, cấp cho workload qua runtime identity và không xuất hiện trong log. Secret rotation yêu cầu application reload hoặc restart có kiểm soát; chỉ lưu secret an toàn nhưng không thể rotate vẫn là thiết kế thiếu.

## 6. Health, readiness và liveness

Health endpoint không nên chỉ trả `200` vì process còn sống. **Liveness** trả lời process có bị kẹt tới mức cần restart không. **Readiness** trả lời instance có nên nhận traffic không. Startup có thể cần load config hoặc warm cache; trong thời gian đó process sống nhưng chưa ready.

Readiness không nên phụ thuộc vào mọi hệ thống xa một cách mù quáng. Nếu một analytics API phụ bị lỗi nhưng core feature vẫn hoạt động, đánh instance unready có thể loại toàn bộ capacity. Health phải phản ánh dependency thật sự cần cho request mà endpoint phục vụ.

Database check trong readiness cần timeout ngắn. Nếu database lỗi, hàng trăm instances health-check dồn dập có thể tăng tải. Platform cũng cần grace period để app khởi động, interval và failure threshold hợp lý.

## 7. Graceful shutdown

Khi deploy phiên bản mới, platform gửi signal yêu cầu instance dừng. Application nên chuyển sang not-ready, ngừng nhận request mới, hoàn thành request đang chạy trong giới hạn, đóng connection và exit. Nếu bỏ qua signal, request có thể bị cắt giữa chừng.

Shutdown deadline phải nhỏ hơn termination grace period của platform. Background job cần checkpoint hoặc idempotent để chạy lại. Không dựa vào shutdown hook để lưu business data quan trọng; crash hoặc power loss có thể không cho hook chạy.

Startup và shutdown là một phần API giữa application với platform. Document chúng cùng port và health endpoint, không để chúng là behavior tình cờ của framework.

## 8. Logs có cấu trúc và context

Log tốt ghi event có ý nghĩa: request hoàn thành, dependency timeout, authentication failed hoặc migration started. Mỗi entry nên có timestamp, level, message/event name và context như request ID, route, status, duration; không ghi password, token hay nội dung nhạy cảm.

Structured JSON giúp log platform parse field. Tuy nhiên JSON không cứu được message vô nghĩa. `error happened` không đủ; `database_query_timeout` kèm operation, duration và request ID cho phép điều tra.

Log level production cần cân bằng. Debug log quá nhiều vừa tốn chi phí vừa rò dữ liệu. Error log chỉ dành cho điều cần chú ý; client gửi input sai thường là warning/info cùng metric, không phải stack trace làm nhiễu alert.

## 9. Resource limits và saturation

CPU limit quyết định thời gian xử lý có thể nhận; memory limit tạo boundary rõ và process có thể bị kill khi vượt. Không đặt limit làm một workload lỗi ảnh hưởng host; đặt quá thấp tạo restart và latency. Bắt đầu từ load test/measurement, để headroom rồi quan sát.

Memory leak thường thể hiện qua memory tăng theo thời gian rồi OOM kill. CPU saturation làm queue và latency tăng. Thread pool, file descriptor, connection pool và disk đều có thể bão hòa dù CPU thấp. Monitoring cần nhìn resource gắn với symptom người dùng.

Horizontal scaling chỉ giúp nếu instance thay thế được và bottleneck không nằm ở shared database. Session nên ở signed cookie hoặc shared store khi cần, không giữ trong memory của một instance rồi hy vọng load balancer luôn gửi đúng chỗ.

## 10. Runtime contract cho Learning Journal

Application chạy một foreground process, listen `0.0.0.0` trên `PORT`, cung cấp `/live` và `/ready`. Nó đọc database URL và signing secret lúc startup, validate trước khi ready, ghi structured log ra stdout và dùng request ID. Note nằm trong PostgreSQL; attachment nằm object storage; local disk chỉ giữ temporary upload có giới hạn.

Mỗi database call có timeout. Process nhận termination signal, đánh readiness false và drain request trước khi thoát. Runtime identity được quyền đọc đúng secret và object prefix cần thiết, không có quyền quản trị hạ tầng.

Contract này chưa phụ thuộc provider. Nó có thể chạy trong Docker local, managed container hoặc VM. Sự độc lập đó có giá trị hơn một deployment script chỉ hoạt động nhờ trạng thái ẩn trên laptop.

## 11. Mini practice

Viết runtime contract một trang cho service của bạn. Mô tả startup command, user chạy process, interface/port, config bắt buộc, secret, durable data, temporary data, health endpoints, outbound dependencies, timeout, log fields, resource limit ban đầu và shutdown behavior.

Sau đó trace hai failure: DNS không resolve database và process vượt memory limit. Với mỗi failure, nêu user symptom, signal quan sát được và hành động đầu tiên. Bài đạt khi bạn phân biệt được network failure với application error và không dùng local filesystem cho state phải tồn tại.

## Checkpoint

Bạn sẵn sàng sang Module 3 khi có thể giải thích application như một process có contract, không phải một folder source code. Giữ mental model: platform chỉ vận hành tốt những lifecycle, dependency và resource boundary mà application biểu diễn rõ.
