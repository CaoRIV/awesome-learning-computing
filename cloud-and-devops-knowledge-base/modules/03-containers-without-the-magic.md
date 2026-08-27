# 3. Containers without the Magic — Đóng gói runtime có thể lặp lại

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 2](02-how-an-application-runs.md)  
> **Status:** Reviewed

## Overview

“Chạy được trên máy tôi” thường có nghĩa máy đó đang giữ nhiều trạng thái ẩn: đúng runtime version, package đã cài, file config local và command mà chỉ người viết nhớ. Container image đóng gói application cùng runtime dependencies thành artifact có version. Nó giảm khác biệt môi trường, nhưng không tự sửa code, network hay security.

Container không phải máy ảo nhỏ. Các process trong container vẫn dùng kernel của host, nhưng được cô lập view về process, network, filesystem và resource bằng cơ chế của operating system. Hiểu boundary này giúp dùng container đúng mức và không gán cho nó khả năng bảo mật tuyệt đối.

## 1. Image, container và registry

**Image** là artifact read-only gồm filesystem layers và metadata như startup command, working directory, user. **Container** là một lần chạy image với writable layer, network và config cụ thể. Từ cùng image có thể tạo nhiều containers. **Registry** lưu và phân phối images.

Tag như `v1.4.2` là tên dễ đọc nhưng có thể bị ghi đè tùy registry policy. Digest có dạng `sha256:<giá-trị-băm>` và xác định nội dung bất biến. Production release nên ghi digest để biết chính xác bytes nào đang chạy. `latest` không nói phiên bản mới nhất theo một quy tắc đáng tin; nó chỉ là một tag tên “latest”.

Build một lần rồi promote cùng digest từ staging tới production. Nếu mỗi môi trường build lại, dependency có thể đổi và production không còn chạy artifact đã được test.

## 2. Dockerfile là build recipe

Ví dụ một Node.js service nhỏ:

```dockerfile
FROM node:22.11.0-bookworm-slim AS dependencies
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev

FROM node:22.11.0-bookworm-slim AS runtime
ENV NODE_ENV=production
WORKDIR /app
COPY --from=dependencies /app/node_modules ./node_modules
COPY package.json ./
COPY src ./src
RUN useradd --system --uid 10001 appuser && chown -R appuser:appuser /app
USER 10001
EXPOSE 8080
CMD ["node", "src/server.js"]
```

Base image được pin version để build ít bất ngờ hơn. `npm ci` dùng lockfile và thất bại nếu dependency declaration không khớp. Multi-stage build tách tool/dependency stage khỏi runtime, dù ví dụ này vẫn cố ý đơn giản. `USER` tránh chạy root. Exec form của `CMD` giúp process nhận signal trực tiếp.

Pin image tuyệt đối theo digest tăng reproducibility nhưng cũng cần quy trình cập nhật khi có security patch. Pin không có nghĩa đóng băng vĩnh viễn. Dependabot/Renovate hoặc lịch cập nhật có review giúp tạo image mới có chủ đích.

## 3. Build context và layers

Lệnh build gửi một context cho engine. Nếu context gồm `.git`, test output, secret file hoặc `node_modules`, build chậm, cache kém và có nguy cơ đưa dữ liệu vào layer. `.dockerignore` tối thiểu có thể là:

```text
.git
.env
node_modules
coverage
dist
*.log
```

Mỗi instruction thường tạo layer. Copy lockfile trước source cho phép dependency layer được cache khi code đổi nhưng dependency không đổi. Cache là optimization; output đúng không nên phụ thuộc cache cũ.

Xóa secret ở instruction sau không đảm bảo secret biến mất vì nó có thể còn trong layer trước. Build cần private package nên dùng build-secret mechanism, không `COPY .env` rồi xóa.

## 4. Reproducible không có nghĩa bit-for-bit tự động

Image giảm biến thiên nhưng build vẫn có input ngoài: base image tag, package registry, timestamp, generated file và architecture. Lockfile, pinned base, controlled build environment và lưu provenance làm kết quả đáng tin hơn.

Một build cần tạo metadata gồm commit SHA, image digest và thời điểm. Version có thể được expose qua `/version` hoặc log startup, nhưng không chứa secret. Khi incident xảy ra, operator phải nối được instance → digest → pipeline run → commit.

Không cài package lúc container startup. Startup phụ thuộc internet làm release chậm, không lặp lại và có thể lấy version khác. Mọi dependency tĩnh nên có trong image; runtime chỉ nhận config và kết nối dịch vụ.

## 5. Chạy local giống production ở phần quan trọng

Lệnh minh họa:

```bash
docker build -t learning-journal:local .
docker run --rm \
  --name learning-journal \
  --env-file .env.local \
  -p 8080:8080 \
  --memory 512m \
  --cpus 1 \
  learning-journal:local
```

Port bên trái là host, bên phải là container. `.env.local` không commit và chỉ chứa local credential. Image không mount source code; như vậy test đúng artifact. Memory/CPU limit giúp phát hiện assumption trước cloud.

Nếu cần local PostgreSQL, Docker Compose có thể mô tả hai services. Compose là công cụ development/integration tiện lợi, không mặc định là production orchestrator. Local database volume có thể durable trên máy, nhưng phải phân biệt với managed backup production.

## 6. Container phải disposable

Platform có thể thay container khi deploy, scale hoặc host lỗi. Disposable không nghĩa process có thể chết tùy ý; nghĩa mọi state quan trọng nằm ngoài instance và startup/shutdown được định nghĩa. Container mới phải tự trở nên ready chỉ từ image, config và dependency.

Không SSH vào container rồi sửa file như cách vận hành chính. Sửa đó biến mất khi replace và tạo configuration drift. Debug bằng log, metric, trace hoặc một debug session có audit; fix được đưa vào source/image hoặc infrastructure definition.

Writable filesystem nên nhỏ và có mục đích. Upload lớn cần stream tới object storage hoặc có temp limit. Nếu application lấp disk local, instance có thể chết dù business data đã ở database.

## 7. Health check và signal trong container

Dockerfile có thể có `HEALTHCHECK`, nhưng nhiều platform định nghĩa check ở deployment configuration. Tránh hai health definitions khác nhau. Quan trọng là endpoint và semantics nhất quán.

Process chính phải nhận `SIGTERM` và graceful shutdown như Module 2. Shell-form `CMD node src/server.js` có thể tạo shell làm PID 1 và signal handling khó hơn; exec-form rõ hơn. Nếu app spawn child processes, cần reap chúng hoặc dùng init phù hợp, nhưng một web service đơn process thường không cần supervisor phức tạp.

Health check không nên dùng command phụ như `curl` nếu runtime image cố ý không có nó, trừ khi package được thêm có chủ đích. Platform HTTP check trực tiếp thường đơn giản hơn.

## 8. Container security vừa đủ

Image nhỏ giảm package và attack surface, nhưng “nhỏ nhất” không phải mục tiêu tuyệt đối. Distroless image tăng độ gọn nhưng làm debug và certificate/runtime issue khó hơn; slim official image là khởi đầu thực dụng. Alpine dùng musl thay glibc và có thể tạo compatibility issue; đừng chọn chỉ vì dung lượng.

Các kiểm soát core gồm non-root user, read-only root filesystem khi app hỗ trợ, drop Linux capabilities không cần, không mount host socket, image scan và dependency update. Scan report cần triage theo package thực sự dùng và exploitability; fail pipeline với mọi CVE không phân biệt thường khiến đội bỏ qua signal.

Image ký và provenance hữu ích khi supply chain quan trọng. Với project nhỏ, bước đầu là registry private, immutable tag/digest, quyền push giới hạn và pipeline là đường phát hành duy nhất.

## 9. Khi nào container không cần thiết

Static site có thể deploy thẳng lên object storage/CDN. Một function nhỏ có provider packaging riêng. Một ứng dụng desktop không được lợi từ server container. Container có giá trị khi cần đóng gói process và dependencies nhất quán, không phải vì mọi software hiện đại đều phải có Dockerfile.

Container cũng không buộc phải dẫn tới Kubernetes. Managed container service có thể nhận image, chạy instances, quản lý TLS, health và rolling update. Kubernetes đáng học khi cần orchestration/control nhất quán cho nhiều workloads và đội đủ khả năng vận hành abstraction đó.

## 10. Mini practice

Containerize một service nhỏ. Build image, chạy không mount source, gọi health endpoint, gửi termination signal và xác nhận request đang chạy được drain trong deadline. Inspect image để chắc không có `.env`, source control metadata hoặc package development không cần.

Ghi commit SHA và digest. Chạy hai containers từ cùng image trên hai host ports để kiểm tra stateless assumption. Nếu behavior khác nhau, tìm state ẩn. Bài đạt khi image không chứa secret, process non-root, startup rõ và dữ liệu bền vững không nằm ở writable layer.

## Checkpoint

Bạn sẵn sàng sang Module 4 khi phân biệt image, container và registry; có thể giải thích layer/cache, và tin vào digest thay vì tên tag. Mental model cần giữ: container là runtime contract được đóng gói, không phải một server cần chăm sóc bằng thao tác thủ công.
