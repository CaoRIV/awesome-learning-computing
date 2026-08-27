# 6. CI/CD and Safe Delivery — Một artifact, nhiều bằng chứng, đường lui rõ

> **Thời lượng:** 65–75 phút  
> **Prerequisites:** [Module 5](05-infrastructure-and-environments.md)  
> **Status:** Reviewed

## Overview

Continuous Integration (CI) là thói quen hợp nhất thay đổi nhỏ thường xuyên và kiểm tra tự động để main branch luôn gần trạng thái có thể phát hành. Continuous Delivery giữ mọi phiên bản hợp lệ sẵn sàng cho production; bước production có thể cần approval. Continuous Deployment tự đưa mọi thay đổi vượt gate tới production.

Không cần chọn mức tự động cao nhất. Một service nhỏ có thể continuous delivery với approval production trong giờ làm việc. Điều quan trọng là build lặp lại, artifact bất biến, gate có lý do và release có thể quan sát/phục hồi.

## 1. Pipeline là đường tạo bằng chứng

Pipeline không chỉ chạy commands. Nó trả lời: source nào, ai thay đổi, test gì đã qua, artifact digest nào, dependency nào, môi trường nào nhận, và health sau deploy ra sao. Khi production lỗi, record này rút ngắn việc tìm thay đổi liên quan.

Một flow vừa đủ:

```text
Pull request: format → unit test → build check → review
Main branch: integration test → build image → scan → publish digest
Staging: deploy digest → migrate safely → smoke test
Production: approve → promote same digest → verify → record release
```

Mỗi stage thất bại phải dừng và trả log có thể hành động. Pipeline có 40 steps nhưng failure chỉ nói `exit 1` là automation kém.

## 2. Fast checks trước, expensive checks sau

Formatter, lint và unit tests chạy sớm vì nhanh. Integration tests cần database hoặc service dependency chạy sau. End-to-end tests ít, tập trung critical journey vì chậm và dễ flaky. Security/dependency scan đặt nơi có artifact/lockfile đúng.

Feedback time ảnh hưởng behavior: nếu CI mất 50 phút, developer batch changes hoặc bỏ đợi. Chạy jobs độc lập song song, cache dependency có key đúng, và chỉ giữ test mang signal. Không che test flaky bằng retry vô hạn; quarantine có owner và deadline, rồi sửa root cause.

Branch protection yêu cầu review và checks thiết yếu. Tuy nhiên gate quá nhiều approval không gắn risk tạo queue. Production access/review có thể chặt hơn source change bình thường, nhưng process vẫn cần đường emergency có audit.

## 3. Build once, identify precisely

Main branch build image một lần. Image được tag bằng commit SHA để con người tìm và lưu digest để máy xác định. Pipeline tạo Software Bill of Materials (SBOM) nếu tooling sẵn, giúp biết package nào trong artifact khi vulnerability xuất hiện.

Không build production từ source checkout trên server. Không chạy `npm install` lúc startup. Không sửa image trong registry. Artifact promotion nghĩa staging và production tham chiếu cùng digest; environment config nằm ngoài image.

Version endpoint hoặc startup log có commit và digest. Nếu binary được build với timestamp, hãy biết timestamp ảnh hưởng reproducibility; mục tiêu core là traceable artifact, không cần theo đuổi bit-for-bit reproducible trước khi pipeline cơ bản đúng.

## 4. Credential và supply chain trong CI

Pipeline chạy code từ repository, nên credential của nó có giá trị cao. Pull request không tin cậy không được nhận production secret. CI dùng short-lived identity qua federation nếu platform hỗ trợ; scope theo repository, branch, environment và action.

Build job push registry không cần deploy. Deploy staging không cần đọc production database. Production environment có approval/protection và deploy role riêng. Secrets không echo vào log; masking không đủ nếu command upload nguyên environment.

Third-party action/plugin nên pin version hoặc commit, review quyền và cập nhật có chủ đích. Dependency install dùng lockfile. Build provenance nối source với artifact. Đây là supply-chain security ở mức thực dụng: bảo vệ đường tạo thứ đang chạy.

## 5. Deployment strategy theo risk

**Recreate** dừng bản cũ rồi chạy bản mới; đơn giản nhưng có downtime. Phù hợp internal/non-critical workload. **Rolling update** thay instances từng phần, giữ capacity và là default tốt cho stateless service. Trong lúc rollout, hai versions cùng chạy nên API và schema phải tương thích.

**Blue-green** tạo environment mới song song rồi chuyển traffic; rollback traffic nhanh nhưng tốn tài nguyên và data migration vẫn khó. **Canary** gửi một phần traffic tới version mới, so signal rồi tăng dần; nó chỉ có giá trị khi traffic đủ, cohort/routing đúng và telemetry phân biệt version.

Strategy phức tạp không bù cho test hoặc observability thiếu. Learning Journal dùng rolling update với readiness và max unavailable thấp. Canary có thể thêm khi user base/release risk đủ lớn.

## 6. Health verification sau deploy

Deployment “completed” chỉ nghĩa platform đạt desired state. Pipeline cần smoke test từ đường gần user: resolve endpoint, gọi health, login/test account và thực hiện một read/write không phá dữ liệu. Sau đó quan sát error rate, latency và instance restart trong cửa sổ phù hợp.

Readiness ngăn traffic tới instance chưa sẵn sàng. Deployment timeout ngăn pipeline chờ vô hạn. Abort condition được định trước: error rate vượt ngưỡng, latency tăng rõ hoặc critical journey thất bại.

Smoke test không dùng production secret quyền cao. Dùng synthetic account/tenant được giới hạn, dữ liệu có prefix và cleanup. Nếu không thể test write an toàn, ít nhất kiểm tra dependency/read path và dùng metric release.

## 7. Rollback và roll-forward

Rollback application đổi service về digest trước. Nó nhanh khi config và schema còn tương thích. Nếu release đã migration phá compatibility, rollback code có thể làm tình hình tệ hơn. Vì vậy database change dùng expand-and-contract qua nhiều release.

Roll-forward tạo bản sửa mới, phù hợp khi data đã chuyển hoặc lỗi nhỏ dễ sửa. Quyết định dựa trên thời gian giảm impact, không dựa lòng tự trọng. Runbook ghi previous known-good digest, command/pipeline rollback, quyền cần và cách verify.

Config rollback cũng phải version/audit. Feature flag có thể tắt behavior rủi ro nhanh, nhưng flag không thay rollback nếu process leak memory hoặc migration sai.

## 8. Database migration trong pipeline

Migration là release component có risk riêng. Chạy migration bằng application startup ở mọi instance dễ race và kéo dài readiness. Một migration job duy nhất trước/giữa rollout thường rõ hơn. Nó dùng role có DDL permission; runtime app role không cần quyền thay schema.

Expand-and-contract ví dụ đổi `note_text` thành `content`: release A thêm `content` nullable và code có thể đọc field mới hoặc cũ; backfill theo batch có checkpoint; release B ghi `content` và tiếp tục tương thích; sau khi không còn code/record cũ, release C mới xóa `note_text`.

Migration lớn cần thử trên data volume đại diện, xem lock/table rewrite, đặt statement timeout và có abort. “Migration tool báo success local” không chứng minh production an toàn.

## 9. Pipeline failure và idempotency

Job có thể bị retry sau network failure khi chưa biết provider đã nhận thao tác chưa. Deploy/IaC command nên idempotent: chạy lại đưa hệ thống về cùng desired state. Script gửi notification hoặc tạo release record cần unique release ID để tránh trùng.

Không tự động retry test deterministic đang fail. Retry phù hợp network pull tạm thời với limit. Nếu deploy response timeout, query actual state trước khi tạo deployment khác.

Pipeline concurrency cần kiểm soát để hai commit không deploy production ngược thứ tự. Environment lock hoặc cancel superseded deployment giúp giữ thứ tự. Migration càng cần serialization.

## 10. Approval và change record vừa đủ

Approval có ý nghĩa khi reviewer thấy artifact, diff/plan, test, migration impact và release window. Nút approve không có context chỉ tạo nghi thức. Với low-risk change, auto-deploy có thể an toàn hơn handoff manual nếu rollback và telemetry tốt.

Release record gồm service, environment, commit, digest, config/IaC version, migration, actor, start/end và outcome. Nó có thể được pipeline tạo tự động. Incident timeline sẽ dùng record này để đối chiếu “điều gì vừa thay đổi”.

Không cần hội đồng change cho project nhỏ. Cần một mechanism nhất quán và evidence đủ theo risk.

## 11. Mini practice

Thiết kế hoặc cài pipeline cho service của bạn. Pull request chạy checks nhanh. Main build image một lần, publish bằng commit tag và lưu digest. Staging deploy digest, smoke test; production promote đúng digest sau approval. Tạo một release lỗi có chủ đích ở staging và chứng minh pipeline dừng.

Viết rollback runbook tối đa một trang: trigger, previous digest, thao tác, database compatibility và verification. Mô tả một migration expand-and-contract. Bài đạt khi production không rebuild, credential tách theo job và pipeline record nối được release về commit.

## Checkpoint

Bạn sẵn sàng sang Module 7 khi phân biệt CI, continuous delivery và continuous deployment; có thể chọn deployment strategy theo risk, và giải thích vì sao rollback phụ thuộc schema compatibility. Mental model cần giữ: pipeline là hệ thống tạo bằng chứng và giới hạn blast radius, không phải máy chạy YAML.
