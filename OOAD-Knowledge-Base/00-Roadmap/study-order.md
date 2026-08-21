# Study Order

## 1. Nguyên tắc sắp xếp

Thứ tự học không đơn thuần đi theo tên folder. Nó theo chuỗi năng lực:

```text
Frame the problem
→ Specify observable needs
→ Validate shared understanding
→ Model scenarios and domain
→ Assign software responsibilities
→ Realize and test the design
```

Mỗi vòng học gồm sáu bước:

1. **Read:** hiểu mental model và distinction.
2. **Explain:** tự giải thích không nhìn tài liệu.
3. **Diagnose:** tìm lỗi trong bad example.
4. **Produce:** tạo một artifact nhỏ.
5. **Review:** kiểm tra bằng quality criteria và traceability.
6. **Transfer:** áp dụng vào domain khác.

Không chuyển module chỉ vì đã đọc hết file.

## 2. Lộ trình chuẩn

## Phase A — Vocabulary, Context & Boundary

### Học

1. Roadmap và dependency map.
2. Requirement, specification, stakeholder, actor, system boundary.
3. Analysis vs design ở mức overview.

### Làm

- Chọn một capstone domain.
- Viết problem statement 150–250 từ.
- Viết in-scope/out-of-scope.
- Vẽ system context thô: system, users, external systems.
- Tạo assumption log và glossary ban đầu.

### Gate A

Bạn phải trả lời được:

- User khác actor thế nào?
- Stakeholder không dùng hệ thống có thể ảnh hưởng requirement ra sao?
- Khi boundary đổi, actor và requirement thay đổi thế nào?
- Một phát biểu là problem, goal hay solution proposal?

## Phase B — Requirements Discovery

### Học theo thứ tự

1. Requirements Engineering Fundamentals.
2. Stakeholders.
3. Elicitation.
4. Functional Requirements.
5. Non-functional/Quality Requirements.
6. Requirement Analysis.
7. Prioritization.
8. User Stories.
9. Validation overview.

### Làm

- Stakeholder map + communication needs.
- Elicitation plan dùng ít nhất hai kỹ thuật bổ sung nhau.
- 10–20 candidate requirements.
- Phân loại FR, QR, business rule, constraint, assumption.
- Rewrite ít nhất năm vague requirements.
- MoSCoW với rationale/value/risk/dependency.
- User stories cho backlog slice và acceptance criteria.

### Gate B

Requirement set phải:

- có nguồn và rationale;
- có boundary/condition rõ;
- không dùng từ subjective nếu thiếu measure;
- không mâu thuẫn hiển nhiên;
- testable ở mức phù hợp;
- có priority đủ để ra quyết định release.

## Phase C — SRS, Quality & Traceability

### Học theo thứ tự

1. SRS purpose/audience/structure.
2. Requirement Quality.
3. Validation methods.
4. Acceptance criteria refinement.
5. Traceability.

### Làm

- Tạo mini-SRS cho capstone.
- Review bằng checklist Correct/Unambiguous/Complete/Consistent/Verifiable/Feasible/Traceable/Prioritized/Modifiable.
- Ghi defect log và resolution.
- Tạo RTM bản đầu: BR/FR/QR → AC.

### Gate C

- Phân biệt được “document đúng format” với “nội dung đúng need”.
- Giải thích verification và validation bằng cùng một example.
- Thực hiện impact analysis khi một requirement thay đổi.

## Phase D — Use Cases & Scenarios

### Học theo thứ tự

1. Use Case Fundamentals.
2. Use Case Identification.
3. Use Case Specification.
4. Use Case Diagram.

Học specification trước khi quá tập trung diagram giúp giữ goal/behavior là trung tâm. Diagram là overview của inventory và relationship, không phải nguồn duy nhất mô tả flow.

### Làm

- Actor-goal list.
- Candidate use cases theo goal level nhất quán.
- Hai specifications đầy đủ: một happy-path-heavy, một exception-heavy.
- Diagram có boundary và quan hệ có rationale.
- Cập nhật RTM: requirement → use case/scenario → acceptance criterion.

### Gate D

- Tên use case dùng động từ + mục tiêu có giá trị.
- Preconditions không mô tả các bước đáng lẽ thuộc flow.
- Main flow không chứa chi tiết UI/code không cần thiết.
- Alternative/exception flow có condition, resume/termination point và guarantee.
- Giải thích được vì sao một quan hệ là include, extend hoặc không cần quan hệ nào.

## Phase E — OO Analysis & Domain Model

### Học theo thứ tự

1. OOAD Foundations.
2. Domain Modeling.
3. Identifying Classes.
4. CRC Cards.
5. BCE như optional technique.

### Làm

- Highlight nouns, verbs, rules và events trong requirement/use-case text.
- Tạo candidate list có cột Keep/Reject/Reason.
- Domain model: concept, attribute cần thiết, relationship, multiplicity.
- Ghi ít nhất ba invariant.
- CRC role-play một use-case scenario.

### Gate E

- Không biến mọi noun thành class.
- Không đưa database/controller/API vào domain model nếu đang ở problem-space view.
- Multiplicity có business meaning, không đoán theo thói quen.
- Attribute/class distinction có rationale.
- Model nói được rule quan trọng mà prose đã nêu.

## Phase F — UML Behavioral & Structural Views

### Học core path

1. UML Overview.
2. Class Diagram.
3. Sequence Diagram.
4. Activity Diagram.

### Học conditionally

- State Machine khi có entity lifecycle phức tạp.
- Package Diagram khi dependency/grouping cần làm rõ.
- Component Diagram khi integration/runtime module boundaries xuất hiện.
- Deployment Diagram khi quality requirement phụ thuộc topology.

### Làm

Chọn một feature và tạo coherent model set:

- class/domain view cho structure;
- sequence view cho một main và một alternative scenario;
- activity view nếu workflow có branching/parallelism;
- state view cho Order/Reservation/Transfer nếu lifecycle là trọng tâm.

### Gate F

- Mỗi diagram có modeling question và audience.
- Không trộn analysis/design abstraction level mà không ghi rõ.
- Tên và semantics nhất quán giữa views.
- Có thể xóa một diagram không cần thiết thay vì giữ cho “đủ bộ”.

## Phase G — OO Design, GRASP & SOLID

### Học theo thứ tự

1. Analysis Model vs Design Model.
2. Responsibility Assignment.
3. GRASP: Expert → Creator → Controller → Coupling/Cohesion.
4. GRASP: Polymorphism → Indirection → Pure Fabrication → Protected Variations.
5. SOLID theo design problem và refactoring.

### Làm

- Chọn system events từ use-case/sequence scenario.
- Phân công responsibilities và ghi rationale.
- Tạo design classes/interfaces và design sequence diagram.
- Thêm technical services (repository/gateway/controller) chỉ khi có responsibility.
- Thực hiện một refactoring theo SOLID và so sánh trước/sau.

### Gate G

- Mỗi method quan trọng trace được tới scenario/responsibility.
- Class có cohesion giải thích được.
- Dependency hướng về policy/stable abstraction khi cần.
- Không tạo interface chỉ để “đúng DIP”.
- LSP được đánh giá bằng behavioral contract, không chỉ type hierarchy.

## Phase H — Patterns, Architecture Bridge & Code

### Học theo problem order

1. **Strategy** cho pricing/assignment policy variation.
2. **Adapter** cho payment/map provider interface mismatch.
3. **Factory** khi construction concrete phụ thuộc context/configuration.
4. **Observer** cho notification/subscriber reactions.
5. **State** cho behavior theo lifecycle.
6. **Facade** cho subsystem entry point.
7. **Command** cho action cần queue/log/retry/undo.
8. **Decorator** cho behavior tổ hợp động.

Đây là thứ tự thực hành được đề xuất, không phải độ quan trọng tuyệt đối.

### Làm

- Viết problem/forces trước khi chọn pattern.
- So sánh chosen pattern với ít nhất một simpler alternative.
- Tạo package/component view khi boundaries đủ rõ.
- Implement một vertical slice.
- Unit test domain rule; integration test gateway; acceptance test scenario.
- Hoàn tất trace: requirement → design → code → test result.

### Gate H

- Pattern name không phải rationale.
- Code giữ invariant và error/alternative paths.
- Test chứng minh acceptance criteria, không chỉ line coverage.
- Quality requirement có design/test evidence phù hợp.

## Phase I — Transfer, Case Studies & Exam

### Thứ tự case study khuyến nghị

1. **Library:** học pipeline với domain dễ hiểu.
2. **Hotel:** luyện multiplicity, time range và availability rule.
3. **University:** luyện complex business rules và prerequisite.
4. **E-commerce:** luyện subsystem và external integration.
5. **Banking:** luyện security, audit, transaction/invariant.
6. **Food Delivery:** capstone concurrency, lifecycle và quality trade-offs.

### Hoạt động

- Làm lại pipeline trong domain mới mà không nhìn worked example.
- Review một artifact set cố ý có lỗi.
- Trả lời comparison question theo axis + example + decision rule.
- Làm scenario exam theo timebox rồi self-review.

### Exit Gate

Với đề bài mới, bạn có thể:

1. đặt câu hỏi làm rõ và ghi assumption;
2. xác định stakeholder/boundary;
3. viết và review requirements;
4. mô hình hóa goal/scenario/domain;
5. phân công responsibility và chọn design;
6. xác định diagram cần/không cần;
7. trace tới implementation/test;
8. giải thích trade-off và tác động khi requirement đổi.

## 3. Ba lộ trình theo mục tiêu

### 3.1 Exam-first path

Phù hợp khi cần chuẩn bị môn học nhưng vẫn muốn hiểu:

```text
Vocabulary → Requirements types/quality → Stakeholders
→ Use Case fundamentals/spec/diagram
→ OOAD + Domain Model
→ Class/Sequence/Activity/State
→ Analysis vs Design → GRASP → SOLID
→ Comparison + Scenario Questions
```

Không bỏ bài tập scenario. Memorize-only path dễ thất bại khi đề thay domain.

### 3.2 Project-first path

```text
Problem/Boundary → Stakeholders/Elicitation → Requirements/AC
→ Two Use Cases → Domain Model → Sequence
→ Responsibilities/Design Classes → Code/Test → RTM
→ Iterate quality and architecture views
```

Chỉ học notation cần cho feature hiện tại, sau đó quay lại curriculum để lấp gap.

### 3.3 Deep-study path

Đi toàn bộ Phase A–I, hoàn thành exercises bốn level và ít nhất ba case studies. Đây là path phù hợp để tạo nền tảng dài hạn.

## 4. Nhịp học mẫu theo session

Không gắn curriculum với số tuần cố định vì tốc độ và prerequisite khác nhau. Một session 60–90 phút có thể chia:

| Phần | Tỷ lệ | Hoạt động |
|---|---:|---|
| Retrieval | 10% | Tự trả lời câu hỏi cũ không nhìn notes |
| Concept | 25% | Đọc một mental model/distinction |
| Diagnosis | 15% | Review bad example |
| Production | 35% | Viết requirement/model/artifact/code |
| Review | 15% | Checklist, trace link, reflection |

Sau mỗi 3–4 topics, dành một session chỉ để integration và sửa artifact cũ.

## 5. Spaced review

Review theo năng lực, không chỉ flashcard:

- **Lần 1:** giải thích concept và contrast ngay sau khi học.
- **Lần 2:** 1–3 ngày sau, sửa một bad example.
- **Lần 3:** khoảng một tuần sau, áp dụng vào domain khác.
- **Lần 4:** khi downstream module dùng concept, review traceability và consistency.

Ví dụ: khi học State pattern, quay lại state machine của `Order`; khi học deployment, quay lại availability/latency requirements.

## 6. Artifact portfolio tối thiểu

Đến cuối curriculum, portfolio nên có:

- problem/context statement;
- stakeholder map;
- elicitation plan và notes summary;
- prioritized FR/QR/rules/constraints;
- mini-SRS và validation log;
- RTM;
- actor-goal list;
- use-case diagram + hai specifications;
- domain model + candidate-class log;
- CRC cards;
- class, sequence và activity diagrams;
- state/package/component/deployment diagrams khi có modeling question;
- GRASP/SOLID/design-pattern decision records;
- vertical-slice implementation;
- test cases và acceptance evidence.

## 7. Self-assessment rubric

Chấm mỗi artifact từ 0–3:

| Điểm | Mô tả |
|---:|---|
| 0 | Không có hoặc không trả lời đúng câu hỏi |
| 1 | Có hình thức nhưng thiếu semantic/rationale |
| 2 | Đúng phần lớn, traceable, còn gap nhỏ |
| 3 | Chính xác, rõ, usable, nhất quán và bảo vệ được trade-off |

Không cần mọi artifact đạt 3 ngay lần đầu. Mục tiêu là dùng feedback loop để nâng cả chuỗi, không đánh bóng một diagram riêng lẻ.

## 8. Dấu hiệu cần quay lại prerequisite

- Không xác định được actor vì boundary mơ hồ → quay lại scope/context.
- Alternative flow bùng nổ → xem lại goal level, business rules và decomposition.
- Domain model giống database schema → quay lại domain concepts/invariants.
- Sequence diagram chỉ có một “God object” → quay lại CRC/GRASP.
- Nhiều interface/pattern nhưng không nêu được variation → quay lại requirement/change drivers.
- Test không biết assert gì → quay lại acceptance criteria và observable outcome.
- Diagram mâu thuẫn nhau → chọn source of truth, sửa upstream artifact và trace links.

## 9. Definition of “đã học”

Một concept chỉ được đánh dấu đã học khi bạn có thể:

1. định nghĩa bằng lời của mình;
2. giải thích vấn đề nó giải quyết;
3. phân biệt với concept gần nhất;
4. nhận diện một cách dùng sai;
5. áp dụng vào case mới;
6. nói khi nào không nên dùng;
7. nối nó với artifact upstream và downstream.

