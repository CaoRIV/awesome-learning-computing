# 5. Infrastructure and Environments — Tái tạo được, có quyền rõ và thay đổi an toàn

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 4](04-cloud-building-blocks.md)  
> **Status:** Reviewed

## Overview

Click console giúp khám phá, nhưng một môi trường chỉ tồn tại qua thao tác tay sẽ khó review, tái tạo và biết vì sao khác production. Infrastructure as Code (IaC) mô tả desired infrastructure trong file có version và dùng provider API để đưa state thật về gần state mong muốn.

IaC không biến mọi thay đổi thành an toàn. Nó làm thay đổi nhìn thấy và lặp lại. Người review vẫn phải hiểu replacement, data loss, permission và cost. Module này tập trung vào workflow, không dạy cú pháp của một tool cụ thể.

## 1. Desired state, actual state và state record

Configuration nói muốn một container service, database và bucket với thuộc tính cụ thể. Provider giữ actual resources. IaC engine so sánh hai phía rồi tạo plan: create, update, replace hoặc delete. Một số tool giữ state record để ánh xạ logical resource trong code với resource ID thật.

State có thể chứa endpoint và dữ liệu nhạy cảm. Nó cần remote backend có encryption, access control, versioning và locking. Không commit state vào Git hoặc để nhiều người apply từ laptop mà không lock.

Drift xảy ra khi resource bị đổi ngoài IaC. Plan phát hiện drift nhưng cách xử lý phụ thuộc intent: đưa config về actual nếu thay đổi đúng, hoặc apply để hoàn nguyên nếu thay đổi tay là sai. Cấm mọi console action tuyệt đối có thể cản incident response; thay vào đó, break-glass change phải được audit và reconcile sau sự cố.

## 2. Module hóa theo responsibility, không theo tham vọng

Một project nhỏ có thể tách module `service`, `database` và `storage`, hoặc thậm chí giữ trong một stack rõ ràng. Module có giá trị khi gom policy lặp lại và tạo interface ổn định. Tạo abstraction cho mọi resource ngay lần đầu khiến input/output phức tạp hơn chính provider resource.

Staging và production dùng cùng cấu trúc với parameter khác như size, domain, min instances và backup retention. Tránh copy nguyên folder rồi sửa tay; hai bản sẽ drift. Cũng không ép mọi giá trị giống nhau: production có redundancy/retention cao hơn vì risk khác.

Boundary state nên theo blast radius và lifecycle. Network nền dùng chung có thể ở state riêng; application stack thay thường xuyên ở state khác; database cần lifecycle bảo vệ hơn compute. Không cần chia hàng chục states khi chỉ có ba resources.

## 3. Workflow plan, review, apply

Thay đổi bắt đầu bằng branch và pull request. CI format/validate IaC rồi tạo plan cho môi trường mục tiêu. Reviewer đọc cả code và plan: resource nào bị replace, port/quyền nào mở, cost/reliability đổi ra sao. Apply dùng identity của pipeline sau approval phù hợp.

Production apply không nên dùng credential cá nhân lâu dài. Workload identity federation cho CI nhận short-lived credential dựa trên repository/branch là lựa chọn tốt hơn lưu cloud access key trong secret. Deploy log ghi commit, actor, plan và result.

Plan có thể cũ nếu actual state đổi trước apply. Pipeline nên refresh hoặc apply đúng saved plan trong cửa sổ ngắn. Thay đổi lớn được chia nhỏ: tạo resource mới, migrate traffic/data, xác nhận, rồi xóa cũ ở release sau.

## 4. Lifecycle và thay đổi phá hủy

Tên resource đổi trong code có thể khiến engine hiểu là delete cũ/create mới. Với stateless compute điều đó thường chấp nhận nếu rollout đúng. Với database hoặc bucket, replacement có thể mất data. Lifecycle protection/prevent-destroy là guardrail, không thay backup.

Database schema không nên được quản lý như table resources trong infrastructure tool. IaC tạo database service, network và role cơ sở; migration tool của application quản lý schema theo release. Hai lifecycle khác nhau và cần rollback/compatibility khác nhau.

Trước thay đổi stateful, trả lời: backup gần nhất có dùng được không, restore mất bao lâu, migration có lock/rewrite không, app cũ/mới tương thích không, và abort condition là gì. Nếu không trả lời được, plan “update in place” vẫn chưa đủ bằng chứng.

## 5. Environments và promotion

Local tối ưu feedback, có thể dùng container dependencies. Staging kiểm tra artifact và integration trong môi trường gần production. Production phục vụ user thật. Mục tiêu không phải staging giống production về kích thước; nó giống về topology, config contract và deployment mechanism ở phần cần kiểm chứng.

Cùng image digest được promote. Environment-specific config được inject lúc deploy. Staging dùng account/project/subscription riêng khi có thể để giảm blast radius; production access nghiêm hơn. Dữ liệu production không copy thẳng sang staging; dùng synthetic hoặc masked data.

Preview environment hữu ích cho UI/integration nhưng mỗi branch environment tạo cost, data lifecycle và quota. Chỉ dùng khi feedback value lớn hơn operational cost và có automatic expiry.

## 6. Identity là principal, permission là action

Human, CI pipeline và runtime workload cần identities khác nhau. Human đăng nhập qua SSO/MFA và role. CI build có quyền đọc source/push image nhưng không nhất thiết đọc production data. Deploy identity update service/IaC. Runtime identity chỉ đọc secret cụ thể, kết nối database và truy cập object prefix cần thiết.

Least privilege không có nghĩa tạo policy cực nhỏ không thể vận hành ngay lập tức. Bắt đầu từ actions thực tế, scope theo resource, quan sát denied calls rồi điều chỉnh có review. Tránh wildcard admin và credential dùng chung.

Role assumption/short-lived token tốt hơn access key dài hạn. Key không còn owner rõ sẽ khó rotate. Break-glass admin account cần bảo vệ, audit và chỉ dùng incident đặc biệt.

## 7. Secret lifecycle

Secret manager lưu value mã hóa, kiểm soát access và audit. IaC nên tạo secret container/permission nhưng không đưa plaintext secret vào config hoặc state nếu tránh được. Value có thể được nạp qua quy trình riêng hoặc generated service.

Application nhận secret qua reference/runtime API hoặc mounted file, không log. Rotation là hai-phase khi dependency cho phép: tạo credential mới, cho app dùng, xác nhận, rồi revoke cũ. Nếu rotate database password ngay trước khi instance mới rollout xong, instance cũ có thể mất connection.

Không dùng secret cho giá trị không nhạy cảm; việc đó làm config khó nhìn và rotation system nhiễu. Ngược lại, không coi base64 là encryption.

## 8. Configuration, feature flags và migration

Deployment config thay behavior môi trường như endpoint và capacity. Feature flag thay behavior product mà không nhất thiết build mới. Flag cần owner, expiry và default an toàn; flag cũ tích tụ thành branch logic khó test.

Config validation xảy ra trước readiness. Thay config production nên đi qua review/audit giống code vì có thể gây outage. Dynamic config chỉ cần khi latency thay đổi thật sự quan trọng; restart controlled đơn giản và dễ reasoning hơn cho nhiều service nhỏ.

Database migration dùng expand-and-contract. Release đầu thêm column/table mới mà code cũ vẫn chạy. Release sau ghi/đọc dạng mới và backfill có kiểm soát. Chỉ khi không còn code cũ mới xóa field cũ. Cách này tránh deploy app và schema phải xảy ra đúng cùng một mili-giây.

## 9. Policy, guardrail và security baseline

Guardrail tốt ngăn lỗi tác động lớn: bucket public bị chặn mặc định, production resource bắt buộc encryption/tag, database không có public route, stateful resource có deletion protection. Policy as code đáng dùng khi nhiều teams/resources cần enforcement; project nhỏ có thể bắt đầu từ module defaults và CI checks.

Security baseline gồm MFA cho human, audit log, short-lived credentials, encryption, backup, patch/update và network tối thiểu. Compliance framework có thể yêu cầu bằng chứng thêm, nhưng checkbox không thay threat model.

Threat model cho Learning Journal hỏi assets nào quan trọng, actor nào, entry points và impact. Notes riêng, signing key và database là assets. Public API, upload và CI supply chain là entry points. Từ đó mới chọn rate limit, validation, private storage, identity boundary và audit.

## 10. Cost và ownership trong IaC

Mỗi resource nên có environment, service và owner tags/labels nếu provider hỗ trợ. Review size/retention/egress trước apply. Budget alert theo account và anomaly detection giúp thấy database test bị bỏ quên hoặc log tăng đột ngột.

Không tự động xóa production khi vượt budget. Cost control phải giữ reliability và data safety. Non-production có thể schedule scale-down hoặc expiry. IaC làm teardown lặp lại, nhưng stateful deletion vẫn cần protection và backup policy.

## 11. Mini practice

Viết pseudo-IaC hoặc dùng tool bạn chọn để tạo container service, private database, bucket và runtime identity. Tách input theo environment nhưng dùng chung cấu trúc. Chạy plan và chú thích mọi create/update/replace. Không cần apply cloud thật nếu không có account; plan review vẫn là artifact học tập.

Thiết kế permission matrix cho human developer, CI build, deploy pipeline và runtime. Mỗi principal ghi actions và resources. Sau đó mô tả rotation của database credential mà không làm instance cũ mất kết nối giữa rollout.

Bài đạt khi production không phụ thuộc thao tác tay ẩn, state được bảo vệ, cùng artifact đi qua môi trường, và runtime không có quyền deploy hoặc quản trị database.

## Checkpoint

Bạn sẵn sàng sang Module 6 khi giải thích được desired state, plan, state và drift; có thể review replacement risk và phân biệt identity build/deploy/runtime. Mental model cần giữ: IaC làm infrastructure change thành software change có bằng chứng, nhưng judgment và recovery vẫn là trách nhiệm con người.
