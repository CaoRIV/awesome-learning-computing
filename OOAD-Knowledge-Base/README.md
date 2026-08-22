# Software Requirements & OOAD Knowledge Base

Knowledge base tiếng Việt để học theo chuỗi:

> Business Problem → Requirements → SRS → Use Cases → OO Analysis → UML → OO Design → Code & Tests

Trọng tâm là **lý thuyết đủ sâu để reasoning**: mỗi concept phải có mental model, boundary, distinction, cách áp dụng, trade-offs và common mistakes. Diagram và artifact chỉ được tạo khi giúp trả lời một câu hỏi phân tích hoặc thiết kế.

## Trạng thái

| Phần | Trạng thái |
|---|---|
| Foundation, roadmap và glossary | ✅ Hoàn thành |
| `01-Requirements` | ✅ Hoàn thành |
| Bài tập và luyện thi Module 01 | ✅ Hoàn thành |
| `02-SRS` | ⏳ Tiếp theo |
| `03-Use-Cases` → `07-Case-Studies` | Chưa triển khai |
| Bài tập/luyện thi cho các module sau | Chưa triển khai |

## Bắt đầu học

Chỉ cần ba tài liệu chính:

1. [Learning Roadmap](00-Roadmap/roadmap.md) — đích đến và checkpoint.
2. [Table of Contents](00-Roadmap/table-of-contents.md) — thứ tự và trạng thái lessons.
3. [Glossary](00-Roadmap/glossary.md) — thuật ngữ và distinctions.

Các tài liệu sau dùng khi cần hiểu sâu cấu trúc curriculum, không phải prerequisite bắt buộc:

- [Dependency Map](00-Roadmap/dependency-map.md)
- [Study Order](00-Roadmap/study-order.md)
- [Knowledge Architecture](00-Roadmap/knowledge-architecture.md)

## Module 01 — Software Requirements Engineering

Học theo thứ tự:

| # | Lesson | Trọng tâm lý thuyết |
|---:|---|---|
| 1 | [Requirements Engineering Fundamentals](01-Requirements/requirements-engineering.md) | Requirement, abstraction levels, RE lifecycle, ambiguity, conflict, volatility |
| 2 | [Stakeholders](01-Requirements/stakeholders.md) | Stakeholder, user, customer, actor, impact và authority |
| 3 | [Functional Requirements](01-Requirements/functional-requirements.md) | Observable behavior, trigger–condition–response, rules và decomposition |
| 4 | [Quality Requirements](01-Requirements/non-functional-requirements.md) | Quality scenarios, measures và attribute trade-offs |
| 5 | [Requirement Elicitation](01-Requirements/elicitation.md) | Interview, observation, workshop, documents, prototype và triangulation |
| 6 | [Requirement Analysis](01-Requirements/analysis.md) | Classification, dependency, conflict, completeness, feasibility và verifiability |
| 7 | [Requirement Prioritization](01-Requirements/prioritization.md) | MoSCoW, value, risk, dependency và release context |
| 8 | [User Stories](01-Requirements/user-stories.md) | Three Cs, INVEST, acceptance criteria và story splitting |
| 9 | [Verification & Validation](01-Requirements/validation.md) | Review, walkthrough, inspection, testability và validation evidence |

Sau khi học lý thuyết:

- [Bài tập Module 01](08-Exercises/01-requirements.md) — Basic, Intermediate, Advanced và Case Study.
- [Luyện thi Module 01](09-Exam-Preparation/01-requirements.md) — Key Concepts, Common Questions, Comparisons và Scenarios.

## Cách học một lesson

```text
Mental model
→ Distinctions and boundaries
→ Reasoning workflow
→ Worked example
→ Trade-offs and common mistakes
→ Artifact checklist
→ Practice in a different domain
```

Không đánh dấu đã học chỉ vì đã đọc xong. Bạn cần có thể:

1. giải thích concept bằng lời của mình;
2. phân biệt với concept gần nghĩa;
3. chỉ ra cách dùng sai;
4. áp dụng vào một case mới;
5. giải thích khi nào không nên dùng.

## Case study xuyên suốt

**Food Delivery — Order lifecycle** là anchor example để nối requirements, scenarios, domain model, design và tests. Library, Hotel, Banking và University được dùng để luyện khả năng chuyển giao kiến thức, không phải để lặp lại cùng một solution.

## Exit criteria của Module 01

Trước khi chuyển sang `02-SRS`, người học phải có thể:

- phân biệt goal, requirement, rule, constraint và design decision;
- xác định stakeholder và system boundary;
- viết FR theo observable behavior;
- viết QR có environment và measure;
- chọn elicitation technique theo uncertainty;
- phát hiện ambiguity, conflict, omission và infeasibility;
- ưu tiên requirement có release context;
- viết acceptance criteria;
- phân biệt verification với validation.

## Curriculum

```text
00-Roadmap
01-Requirements          ✅
02-SRS                   next
03-Use-Cases
04-OO-Analysis
05-UML
06-OO-Design
07-Case-Studies
08-Exercises             Module 01 available
09-Exam-Preparation      Module 01 available
```

Module tiếp theo là `02-SRS`: SRS structure, requirement quality và bidirectional traceability.
