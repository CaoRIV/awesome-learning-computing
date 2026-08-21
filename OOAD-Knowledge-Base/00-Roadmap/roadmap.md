# Learning Roadmap — Software Requirements & OOAD

## 1. Đích đến của curriculum

Sau khi hoàn thành, người học phải có thể nhận một business problem chưa rõ ràng và tạo ra một chuỗi artifact nhất quán đủ để một nhóm phát triển triển khai và kiểm thử:

```text
Problem statement
  → Stakeholder map
  → Scope & system boundary
  → Requirements + business rules
  → Acceptance criteria
  → Use cases / scenarios
  → Domain model
  → Interaction model
  → Design classes & architecture
  → Code responsibilities
  → Tests & traceability evidence
```

Một diagram đẹp nhưng không truy vết được về requirement **không** được xem là kết quả đạt yêu cầu. Tương tự, một danh sách requirement dài nhưng không testable hoặc không xác định boundary cũng chưa đủ để thiết kế.

## 2. Năng lực đầu ra

### 2.1 Requirements Engineering

Người học có thể:

- phân biệt business need, stakeholder need, system requirement và solution constraint;
- nhận diện stakeholder trực tiếp, gián tiếp và external system;
- thu thập yêu cầu bằng kỹ thuật phù hợp với loại thông tin cần khám phá;
- phát hiện ambiguity, inconsistency, omission, infeasibility và conflict;
- phân loại functional requirement, quality requirement, constraint và business rule;
- ưu tiên và thương lượng requirement với lý do có thể kiểm chứng;
- viết requirement rõ, khả thi, có nguồn, có priority và testable.

### 2.2 Specification & Validation

Người học có thể:

- chọn cấu trúc SRS phù hợp với quy mô và quy trình dự án;
- viết acceptance criteria cho normal path, alternative path và failure path;
- phân biệt verification với validation;
- tổ chức review/inspection và dùng checklist chất lượng;
- xây dựng Requirement Traceability Matrix hai chiều.

### 2.3 Use-case Modeling

Người học có thể:

- xác định actor theo role và external interaction, không đồng nhất actor với tên người;
- đặt system boundary trước khi xác định use case;
- chuyển actor goal thành use case có giá trị quan sát được;
- viết main success scenario, alternative flow và exception flow;
- sử dụng `<<include>>`, `<<extend>>` và generalization có chủ đích;
- phân biệt use case, user story, business process và UI flow.

### 2.4 Object-Oriented Analysis

Người học có thể:

- phân biệt analysis model với design model;
- trích xuất domain concept từ requirement nhưng loại bỏ noun không có identity, state hoặc responsibility hữu ích;
- xác định entity, value object, attribute, relationship, multiplicity và invariant;
- dùng noun analysis, verb analysis, CRC và responsibility analysis;
- dùng Boundary–Control–Entity khi nó giúp tách interaction, coordination và domain state.

### 2.5 UML & Behavioral Modeling

Người học có thể chọn diagram theo câu hỏi cần trả lời:

- **Use Case Diagram:** ai dùng hệ thống để đạt mục tiêu gì?
- **Class/Domain Diagram:** các khái niệm và cấu trúc tĩnh liên hệ ra sao?
- **Sequence Diagram:** các participant cộng tác theo thời gian như thế nào?
- **Activity Diagram:** workflow, decision, parallelism và responsibility flow ra sao?
- **State Machine:** lifecycle của một đối tượng stateful thay đổi bởi event nào?
- **Package Diagram:** model/code được nhóm và phụ thuộc ra sao?
- **Component Diagram:** các deployable/replaceable component cung cấp interface nào?
- **Deployment Diagram:** artifact chạy trên node/execution environment nào?

### 2.6 Object-Oriented Design

Người học có thể:

- chuyển domain concept thành software class mà không sao chép analysis model máy móc;
- phân công responsibility bằng GRASP;
- kiểm soát coupling, cohesion, dependency direction và variation point;
- dùng SOLID để chẩn đoán và refactor một design problem cụ thể;
- chọn design pattern từ force/problem, không từ tên pattern;
- nối design class và interaction tới implementation và test.

## 3. Prerequisite

### Bắt buộc

- Biết biến, hàm, điều kiện, vòng lặp và cấu trúc dữ liệu cơ bản.
- Hiểu class, object, method và interface ở mức nhập môn.
- Có thể đọc pseudocode hoặc code của một ngôn ngữ hướng đối tượng.

### Hữu ích nhưng không bắt buộc

- Kiến thức database và HTTP ở mức cơ bản.
- Kinh nghiệm làm một ứng dụng CRUD nhỏ.
- Biết dùng Git và Markdown.

Nếu thiếu OOP foundation, người học nên ôn: encapsulation, abstraction, inheritance, polymorphism, composition, identity, equality và object lifecycle trước giai đoạn OO Design.

## 4. Các chặng học

## Chặng 0 — Orientation & Shared Vocabulary

**Câu hỏi trung tâm:** Ta đang mô tả vấn đề, hành vi hay giải pháp?

Nội dung:

- business problem, need, goal, requirement, specification;
- stakeholder, user, actor, customer;
- system, environment, context và boundary;
- analysis artifact, design artifact và implementation artifact;
- traceability spine của toàn curriculum.

**Sản phẩm:** một context statement một trang và bảng thuật ngữ ban đầu cho case study.

**Checkpoint:** phân loại đúng 15 phát biểu hỗn hợp thành goal, requirement, business rule, constraint, design decision hoặc test.

## Chặng 1 — Requirements Engineering

**Câu hỏi trung tâm:** Hệ thống cần giải quyết vấn đề gì, cho ai, trong điều kiện nào?

Nội dung:

- stakeholder identification, classification, analysis, prioritization;
- functional vs non-functional/quality requirements;
- elicitation techniques;
- classification, prioritization, dependency, negotiation và feasibility;
- MoSCoW và giới hạn của việc ưu tiên không có tiêu chí;
- user story, INVEST và acceptance criteria.

**Sản phẩm:** stakeholder map, scope, elicitation plan và prioritized requirement backlog.

**Checkpoint:** phát hiện tối thiểu ambiguity, missing condition, conflict, unverifiable wording và hidden constraint trong một requirement set.

## Chặng 2 — SRS, Quality, Validation & Traceability

**Câu hỏi trung tâm:** Làm sao biến hiểu biết phân tán thành specification có thể review, thay đổi và kiểm thử?

Nội dung:

- vai trò, audience và cấu trúc SRS;
- đặc điểm requirement tốt;
- requirement review, inspection, verification và validation;
- acceptance criteria và testability;
- forward, backward và bidirectional traceability;
- Requirement Traceability Matrix.

**Sản phẩm:** SRS rút gọn, review log và RTM.

**Checkpoint:** mỗi requirement quan trọng có ID, source, rationale, priority, acceptance evidence và liên kết downstream.

## Chặng 3 — Use Cases & Goal-oriented Scenarios

**Câu hỏi trung tâm:** Actor và hệ thống tương tác thế nào để đạt một goal có giá trị?

Nội dung:

- actor, goal, boundary và trigger;
- use-case identification;
- use-case diagram;
- specification: preconditions, guarantees, main/alternative/exception flows;
- include, extend, generalization và các lạm dụng thường gặp;
- đối chiếu requirement, user story và use case.

**Sản phẩm:** actor-goal list, use-case diagram và ít nhất hai use-case specifications hoàn chỉnh.

**Checkpoint:** mọi bước trong scenario thể hiện intent/response quan sát được, không biến use case thành mô tả click UI hoặc code algorithm.

## Chặng 4 — Object-Oriented Analysis & Domain Modeling

**Câu hỏi trung tâm:** Domain có các concept, rule, state và relationship nào?

Nội dung:

- OO Analysis vs OO Design vs OOP;
- noun/verb analysis và candidate filtering;
- entity, value object, attribute, association và invariant;
- multiplicity và lifecycle semantics;
- responsibility, CRC và Boundary–Control–Entity.

**Sản phẩm:** domain glossary, candidate-class log, domain model và CRC cards.

**Checkpoint:** giải thích được vì sao mỗi concept tồn tại, concept nào bị loại và rule nào được model bảo vệ.

## Chặng 5 — UML as a Reasoning Toolkit

**Câu hỏi trung tâm:** Loại view nào làm rõ uncertainty hiện tại?

Nội dung:

- structural, behavioral và interaction views;
- class, sequence, activity, state machine, package, component và deployment diagrams;
- derivation từ requirement/use case;
- consistency giữa nhiều views;
- mức chi tiết, audience và notation alternatives.

**Sản phẩm:** một coherent model set cho cùng một feature, không phải các diagram rời rạc.

**Checkpoint:** state, message, operation và relationship quan trọng không mâu thuẫn giữa các diagrams.

## Chặng 6 — OO Design with GRASP & SOLID

**Câu hỏi trung tâm:** Software objects nên cộng tác và phụ thuộc theo hướng nào?

Nội dung:

- analysis model vs design class model;
- Information Expert, Creator, Controller;
- Low Coupling, High Cohesion;
- Polymorphism, Indirection, Pure Fabrication, Protected Variations;
- SRP, OCP, LSP, ISP, DIP trong ngữ cảnh thay đổi thực tế;
- refactoring từ problem/force thay vì áp principle máy móc.

**Sản phẩm:** design class diagram, design sequence diagrams và decision log.

**Checkpoint:** mỗi responsibility có owner; dependency direction, boundary với infrastructure và variation points có rationale.

## Chặng 7 — Design Patterns, Architecture Bridge & Implementation

**Câu hỏi trung tâm:** Khi nào một cấu trúc thiết kế lặp lại đủ để dùng pattern, và nó đi vào code thế nào?

Nội dung:

- Factory, Strategy, Observer, Adapter, Decorator, Facade, Command, State;
- pattern intent, forces, participants, trade-offs và alternatives;
- mapping sang package/component/deployment;
- error paths, persistence, external services và transactions;
- implementation skeleton và test strategy.

**Sản phẩm:** implement một vertical slice có unit/integration/acceptance tests và traceability hoàn chỉnh.

**Checkpoint:** bỏ tên pattern đi vẫn giải thích được problem, force, chosen design và consequence.

## Chặng 8 — Integrated Case Studies & Exam Preparation

**Câu hỏi trung tâm:** Có thể tái sử dụng phương pháp trong nhiều domain mà không sao chép solution không?

Áp dụng pipeline cho:

1. Library Management System.
2. Hotel Management System.
3. E-commerce System.
4. Banking System.
5. University Management System.
6. Food Delivery System.

Mỗi case study không nhất thiết có độ sâu giống nhau. Library là worked example; E-commerce hoặc Food Delivery phù hợp làm capstone; Banking nhấn mạnh security, consistency và auditability; Hotel nhấn mạnh inventory/time overlap; University nhấn mạnh rule và prerequisite.

**Sản phẩm:** portfolio artifact set và bộ câu hỏi tự luận/scenario.

**Exit criteria:** xử lý được một đề bài mới từ scope tới test mà không cần dựa vào template như checklist máy móc.

## 5. Hai luồng học song song

Curriculum duy trì hai luồng, gặp nhau ở mỗi checkpoint:

| Concept stream | Artifact stream |
|---|---|
| Học định nghĩa, boundary, trade-off | Tạo artifact cho một feature cụ thể |
| So sánh concept gần nhau | Review sự nhất quán giữa artifacts |
| Chẩn đoán bad example | Rewrite/refactor và ghi rationale |
| Trả lời câu hỏi thi | Bảo vệ quyết định như design review |

Chỉ đọc concept tạo ra “recognition” nhưng chưa tạo “production skill”. Chỉ điền template artifact lại dễ tạo cargo-cult modeling. Hai luồng phải tiến cùng nhau.

## 6. Capstone xuyên suốt

Khuyến nghị dùng **Food Delivery System — Order lifecycle** làm project xuyên suốt vì nó có:

- nhiều stakeholder và external system;
- payment, pricing, restaurant acceptance và courier assignment;
- concurrent/alternative flows;
- trạng thái đơn hàng rõ;
- yêu cầu performance, availability, security và consistency;
- đủ variation point để dùng Strategy, Adapter, Observer và State khi có lý do.

Scope ban đầu chỉ gồm: tạo đơn, báo giá, thanh toán, nhà hàng xác nhận, phân công tài xế, giao hàng và hủy/hoàn tiền. Không mở rộng sang recommendation, loyalty hoặc analytics trước khi core flow hoàn chỉnh.

## 7. Quality gate cho từng module

Mỗi module chỉ được xem là hoàn thành khi đạt bốn tiêu chí:

| Tiêu chí | Câu hỏi kiểm tra |
|---|---|
| Technical Accuracy | Thuật ngữ, semantics và notation có đúng trong scope đã tuyên bố không? |
| Conceptual Clarity | Có phân biệt concept với các concept gần nghĩa và chỉ ra boundary không? |
| Practical Applicability | Người học có thể tạo/review một artifact hoặc ra quyết định không? |
| Cross-module Consistency | ID, thuật ngữ, assumption, state và relationship có khớp module trước không? |

Ngoài ra, mỗi topic phải trả lời: What, Why, Problem, How, Avoid, Related Concepts và Common Mistakes.

## 8. Definition of curriculum completion

Knowledge base hoàn chỉnh khi:

- tất cả topic trong Table of Contents có lesson, example, mistake analysis và exercises;
- sáu case study có traceability spine rõ;
- diagram render được và nhất quán với narrative;
- mỗi module có Basic, Intermediate, Advanced và Case Study exercises;
- mỗi module có Key Concepts, Common Exam Questions, Comparison Questions và Scenario Questions;
- một capstone được đưa từ requirement tới code/test evidence;
- thuật ngữ và cross-link không mâu thuẫn giữa các module.

