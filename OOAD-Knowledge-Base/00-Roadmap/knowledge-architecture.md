# Knowledge Architecture

## 1. Mục tiêu kiến trúc

Knowledge base được tổ chức để hỗ trợ ba cách sử dụng khác nhau:

1. **Curriculum:** học tuần tự theo prerequisite.
2. **Reference:** tra cứu nhanh một concept hoặc artifact.
3. **Project playbook:** đi từ problem statement tới implementation/test.

Vì vậy, số thứ tự thư mục biểu diễn learning progression, còn dependency map mới là nguồn quyết định prerequisite thực sự. Ví dụ, State Machine nằm trong module UML nhưng chỉ cần học khi domain object có lifecycle đủ phức tạp.

## 2. Các lớp tri thức

| Layer | Nội dung | Câu hỏi chính |
|---|---|---|
| L0 — Vocabulary | Thuật ngữ và distinction | “Ta đang nói về cùng một thứ không?” |
| L1 — Problem & Scope | Need, stakeholder, context, boundary | “Vấn đề nào nằm trong hệ thống?” |
| L2 — Requirements | FR, quality, rule, constraint | “Hệ thống phải đáp ứng điều gì?” |
| L3 — Specification | SRS, quality, validation, traceability | “Phát biểu đã đủ rõ để đồng thuận và test chưa?” |
| L4 — Scenarios | Actor goals, use cases, acceptance flows | “Hành vi quan sát được diễn ra thế nào?” |
| L5 — Analysis Model | Domain concepts, state, rules, responsibilities | “Problem domain được cấu trúc ra sao?” |
| L6 — Design Model | Software classes, collaboration, dependencies | “Giải pháp phần mềm tổ chức thế nào?” |
| L7 — Realization | Component, deployment, code, tests | “Thiết kế được triển khai và chứng minh ra sao?” |

Không được nhảy trực tiếp từ L2 sang L6 chỉ vì đã nhìn thấy các danh từ có vẻ giống class. Mỗi lần chuyển layer phải ghi lại quyết định và assumption.

## 3. Cấu trúc thư mục mục tiêu

```text
OOAD-Knowledge-Base/
├── README.md
├── 00-Roadmap/
│   ├── roadmap.md
│   ├── knowledge-architecture.md
│   ├── dependency-map.md
│   ├── table-of-contents.md
│   ├── study-order.md
│   └── glossary.md
├── 01-Requirements/
│   ├── requirements-engineering.md
│   ├── stakeholders.md
│   ├── functional-requirements.md
│   ├── non-functional-requirements.md
│   ├── elicitation.md
│   ├── analysis.md
│   ├── prioritization.md
│   ├── user-stories.md
│   └── validation.md
├── 02-SRS/
│   ├── srs.md
│   ├── requirement-quality.md
│   └── traceability.md
├── 03-Use-Cases/
│   ├── use-case.md
│   ├── use-case-identification.md
│   ├── use-case-diagram.md
│   └── use-case-specification.md
├── 04-OO-Analysis/
│   ├── oo-analysis.md
│   ├── domain-model.md
│   ├── identifying-classes.md
│   └── crc-cards.md
├── 05-UML/
│   ├── uml-overview.md
│   ├── class-diagram.md
│   ├── sequence-diagram.md
│   ├── activity-diagram.md
│   ├── state-machine.md
│   ├── package-diagram.md
│   ├── component-diagram.md
│   └── deployment-diagram.md
├── 06-OO-Design/
│   ├── analysis-vs-design.md
│   ├── responsibility-assignment.md
│   ├── grasp.md
│   ├── solid.md
│   └── design-patterns.md
├── 07-Case-Studies/
│   ├── case-study-method.md
│   ├── library.md
│   ├── hotel.md
│   ├── ecommerce.md
│   ├── banking.md
│   ├── university.md
│   └── food-delivery.md
├── 08-Exercises/
│   ├── basic.md
│   ├── intermediate.md
│   ├── advanced.md
│   └── case-study-prompts.md
└── 09-Exam-Preparation/
    ├── key-concepts.md
    ├── common-questions.md
    ├── comparison-questions.md
    └── scenario-questions.md
```

### Điều chỉnh so với cây khởi đầu

- Thêm `prioritization.md` và `user-stories.md` để các chủ đề lớn không bị chôn trong `analysis.md`.
- Thêm `use-case-identification.md` vì actor-goal discovery khác với notation của diagram.
- Thêm `uml-overview.md` để dạy cách chọn view trước khi học ký pháp.
- Thêm `package-diagram.md`, vốn nằm trong scope nhưng thiếu ở cây mẫu.
- Thêm `food-delivery.md`, case study bắt buộc thứ sáu.
- Tách `09-Exam-Preparation/` để exam material có thể tái tạo từ module thay vì lẫn vào exercises.
- Thêm `case-study-method.md` để sáu case study dùng chung một pipeline và quality contract.

## 4. Đơn vị nội dung chuẩn

Mỗi lesson file là một **learning unit**, không phải một glossary entry dài. Cấu trúc mặc định:

```text
# Topic
## Learning outcomes
## Context and motivating problem
## Core mental model
## What / Why / Problem / How
## Boundaries and related concepts
## Method or workflow
## Worked example
## Bad example and diagnosis
## Alternatives and trade-offs
## When not to use
## Common mistakes
## Artifact checklist
## Exercises: Basic / Intermediate / Advanced / Case Study
## Exam preparation
## Summary and next dependencies
```

Không bắt buộc giữ heading máy móc nếu làm hỏng mạch giải thích, nhưng mọi câu hỏi trong contract phải được trả lời.

## 5. Artifact contract

Mỗi artifact thực hành phải khai báo metadata tối thiểu:

| Field | Ý nghĩa |
|---|---|
| Artifact ID | Định danh ổn định, ví dụ `UC-ORDER-01` |
| Purpose | Câu hỏi hoặc quyết định artifact hỗ trợ |
| Scope / Boundary | Phần hệ thống được model |
| Source | Requirement, stakeholder hoặc assumption nguồn |
| Audience | Ai review và ai sử dụng |
| Assumptions | Điều đang giả định nhưng chưa xác nhận |
| Related artifacts | Liên kết upstream/downstream |
| Status / Version | Draft, reviewed, accepted, superseded |

### Quy ước ID

| Artifact | Prefix mẫu |
|---|---|
| Business requirement | `BR-###` |
| Functional requirement | `FR-###` |
| Quality/non-functional requirement | `QR-###` |
| Business rule | `BRULE-###` |
| Constraint | `CON-###` |
| Use case | `UC-###` |
| Acceptance criterion | `AC-###` hoặc `FR-###-AC-#` |
| Design decision | `DD-###` |
| Test case | `TC-###` |

Prefix chỉ hỗ trợ navigation; bản thân ID không thay thế semantic name.

## 6. Example strategy

### Anchor example

Food Delivery Order lifecycle được dùng xuyên module để người học nhìn thấy artifact thay đổi qua từng layer.

### Comparative examples

- Banking: security, audit, transaction consistency.
- Hotel: date range, inventory và overbooking.
- E-commerce: catalog, order, promotion, payment, fulfillment.
- Library: dễ tiếp cận để học concept đầu tiên.
- University: prerequisite, enrollment rule và capacity.

Một topic không nên dùng sáu domain cùng lúc. Chọn một anchor và tối đa một counterexample để giữ clarity.

## 7. Diagram policy

### Diagram chỉ được tạo khi có question

Trước mỗi diagram phải có một câu như:

> Diagram này làm rõ lifecycle của `Order` và các transition hợp lệ khi hủy/hoàn tiền.

Không có modeling question thì dùng prose/table thường rõ hơn.

### Mermaid và PlantUML

- **Mermaid:** ưu tiên cho flowchart, dependency graph, sequence/activity đơn giản và nội dung cần render trực tiếp trong Markdown.
- **PlantUML:** dùng khi cần UML notation đầy đủ hơn, multiplicity/visibility chi tiết hoặc diagram phức tạp.
- Khi syntax/tool rendering khác nhau, lesson phải giải thích semantic trước và coi code block là representation, không phải nguồn chân lý duy nhất.

### Consistency rule

- Tên actor/use case trong diagram phải trùng specification.
- Message trên sequence diagram phải có responsibility owner trong design model.
- Operation công khai trên design class phải được scenario hoặc collaboration biện minh.
- State transition phải khớp guard/business rule.
- Component interface phải khớp dependency direction trong package/design view.

## 8. Distinction policy

Các cặp dễ nhầm phải luôn được giải thích bằng cùng một pattern:

1. Định nghĩa ngắn.
2. Axis of comparison.
3. Ví dụ chung boundary.
4. Counterexample.
5. Decision rule.
6. Trường hợp convention khác nhau.

Áp dụng cho:

- functional vs quality requirement;
- verification vs validation;
- stakeholder vs actor vs user;
- requirement vs user story vs use case;
- include vs extend;
- analysis class vs design class;
- association vs aggregation vs composition;
- aggregation vs lifecycle ownership trong code;
- GRASP vs SOLID vs design pattern.

## 9. Exercise architecture

Mỗi module sinh bốn loại bài tập:

| Level | Năng lực cần chứng minh | Không cung cấp ngay |
|---|---|---|
| Basic | Recall + explain distinction | Đáp án trực tiếp bên dưới câu hỏi |
| Intermediate | Diagnose/rewrite/model một phần | Full worked solution |
| Advanced | Ra quyết định và bảo vệ trade-off | Một “đáp án duy nhất” giả tạo |
| Case Study | Tạo coherent artifact set | Solution trước khi người học tự làm |

Đáp án/hint về sau nên đặt ở section hoặc file tách biệt để tránh lộ khi luyện tập.

## 10. Exam-preparation architecture

Exam preparation không thay thế learning content. Nó được tạo từ bốn view:

- **Key Concepts:** mental models bắt buộc nhớ.
- **Common Questions:** explain/define có điều kiện.
- **Comparison Questions:** phân biệt theo axis, không chỉ lập hai danh sách.
- **Scenario Questions:** tạo hoặc review artifact từ đề bài.

Mỗi scenario question cần chỉ rõ assumption nào người học được phép đặt, vì đề thiếu dữ liệu là điều bình thường trong Requirements Engineering.

## 11. Traceability spine

Mọi case study dùng cấu trúc hai chiều:

```text
BR-001
  └─ FR-012
      ├─ QR-004
      ├─ UC-ORDER-01
      │   ├─ Scenario 3a
      │   └─ Domain concepts: Order, Quote, Payment
      ├─ DD-007
      │   ├─ Design classes
      │   └─ Component/API
      └─ TC-031, TC-032
```

Forward traceability trả lời “requirement này được hiện thực và kiểm thử ở đâu?”. Backward traceability trả lời “class/API/test này tồn tại vì requirement nào?”.

## 12. Content-quality workflow

Mỗi module đi qua bốn vòng review:

1. **Accuracy review:** semantic, notation, standard terminology.
2. **Clarity review:** prerequisite, distinction, counterexample.
3. **Application review:** artifact, worked example, exercise.
4. **Consistency review:** terminology, ID, assumptions và links với module trước.

Không đánh dấu module hoàn thành nếu diagram không render, example không traceable hoặc exercise đòi kiến thức chưa được giới thiệu.

## 13. Scope boundaries

Knowledge base này:

- tập trung vào requirements, analysis và OO design;
- dùng architecture như cầu nối, không thay thế curriculum Software Architecture;
- dùng implementation để chứng minh design, không dạy toàn bộ ngôn ngữ lập trình;
- đề cập testing từ acceptance/traceability perspective, không thay thế curriculum Software Testing;
- không coi UML là quy trình phát triển phần mềm;
- không ép mọi dự án phải tạo mọi loại diagram.
