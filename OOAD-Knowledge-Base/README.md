# Software Requirements & OOAD Knowledge Base

Knowledge base Markdown bằng tiếng Việt để học có hệ thống:

> Software Requirements Engineering → Specification → Use Cases → Object-Oriented Analysis → UML → Object-Oriented Design → Implementation & Testing

Mục tiêu cuối cùng không phải là thuộc nhiều định nghĩa hay vẽ thật nhiều diagram. Người học phải có thể giải thích và bảo vệ toàn bộ chuỗi quyết định:

```text
Business problem
  → Stakeholders & system boundary
  → Software requirements
  → Acceptance criteria
  → Use cases & scenarios
  → Analysis model
  → Design model
  → Implementation
  → Test evidence & traceability
```

## Trạng thái curriculum

| Module | Nội dung | Trạng thái |
|---|---|---|
| `00-Roadmap` | Roadmap, architecture, dependencies, study order, glossary | ✅ Hoàn thành |
| `01-Requirements` | Software Requirements Engineering | ✅ Hoàn thành |
| `02-SRS` | SRS, requirement quality, traceability | ⏳ Tiếp theo |
| `03-Use-Cases` | Actors, goals, diagrams và specifications | Chưa triển khai |
| `04-OO-Analysis` | Domain model, class identification, CRC | Chưa triển khai |
| `05-UML` | Structural, behavioral và deployment views | Chưa triển khai |
| `06-OO-Design` | Responsibility assignment, GRASP, SOLID, patterns | Chưa triển khai |
| `07-Case-Studies` | Sáu hệ thống end-to-end | Chưa triển khai |
| `08-Exercises` | Bài tập tổng hợp theo cấp độ | Chưa triển khai |
| `09-Exam-Preparation` | Key concepts, comparisons và scenarios | Chưa triển khai |

`Hoàn thành` nghĩa module đã có learning outcomes, mental models, workflows, examples, common mistakes, artifact checklists, exercises và exam preparation. Nó không có nghĩa chủ đề không còn có thể được cải tiến khi các module sau làm lộ yêu cầu consistency mới.

## Bắt đầu từ đâu?

### Nếu học tuần tự

1. Đọc [Learning Roadmap](00-Roadmap/roadmap.md).
2. Kiểm tra prerequisite trong [Dependency Map](00-Roadmap/dependency-map.md).
3. Học `01-Requirements` theo thứ tự lesson bên dưới.
4. Tự làm bài tập trước khi chuyển sang lesson tiếp theo.
5. Chỉ chuyển sang `02-SRS` sau khi đạt exit criteria của Module 01.

### Nếu cần tra cứu

1. Tìm thuật ngữ trong [Glossary](00-Roadmap/glossary.md).
2. Mở lesson nguồn để đọc boundary, trade-offs và examples.
3. Kiểm tra phần `Common mistakes` và `Artifact checklist` trước khi áp dụng.

### Nếu đang làm đồ án

Đi theo artifact flow:

```text
Problem statement
→ Stakeholder register
→ Scope & boundary
→ Elicitation plan
→ Candidate FR / QR / rules / constraints
→ Analysis issue log
→ Prioritized requirement set
→ User stories & acceptance criteria
→ Validation evidence
```

Không bắt đầu bằng class diagram hoặc database schema khi requirement và boundary chưa rõ.

## Tài liệu điều hướng

| Artifact | Mục đích |
|---|---|
| [Learning Roadmap](00-Roadmap/roadmap.md) | Kết quả học tập, các chặng, checkpoint và exit criteria |
| [Knowledge Architecture](00-Roadmap/knowledge-architecture.md) | Cách tổ chức nội dung, artifact contract và quality gate |
| [Dependency Map](00-Roadmap/dependency-map.md) | Prerequisite giữa concept và luồng chuyển đổi artifact |
| [Complete Table of Contents](00-Roadmap/table-of-contents.md) | Curriculum contract và trạng thái từng topic |
| [Study Order](00-Roadmap/study-order.md) | Lộ trình exam-first, project-first và deep-study |
| [Glossary](00-Roadmap/glossary.md) | Thuật ngữ chuẩn và các distinction xuyên curriculum |

## Module 01 — Software Requirements Engineering

### Năng lực đầu ra

Sau Module 01, người học phải có thể:

- phân biệt business goal, stakeholder need, requirement, business rule, constraint và design decision;
- xác định stakeholder, impact, authority và engagement strategy;
- viết functional requirements theo behavior quan sát được;
- viết quality scenarios có workload, environment, measure và evidence;
- chọn kỹ thuật elicitation theo loại uncertainty;
- phát hiện ambiguity, omission, inconsistency, conflict và over-specification;
- đánh giá dependency, feasibility, completeness và verifiability;
- ưu tiên requirements bằng MoSCoW có release context và rationale;
- viết user stories, acceptance criteria và áp dụng INVEST đúng vai trò;
- phân biệt verification với validation và tổ chức requirement review.

### Thứ tự lesson

| # | Lesson | Câu hỏi trung tâm | Artifact thực hành |
|---:|---|---|---|
| 1 | [Requirements Engineering Fundamentals](01-Requirements/requirements-engineering.md) | Requirement là gì và vì sao defect lan truyền? | Problem statement, requirement record |
| 2 | [Stakeholders](01-Requirements/stakeholders.md) | Ai có knowledge, interest, impact hoặc authority? | Stakeholder register và engagement map |
| 3 | [Functional Requirements](01-Requirements/functional-requirements.md) | Hệ thống phải thực hiện behavior nào? | Functional requirement set |
| 4 | [Non-functional / Quality Requirements](01-Requirements/non-functional-requirements.md) | Behavior phải tốt đến mức nào và đo ra sao? | Quality scenarios |
| 5 | [Requirement Elicitation](01-Requirements/elicitation.md) | Cần khám phá điều gì, từ ai và bằng kỹ thuật nào? | Elicitation plan và evidence notes |
| 6 | [Requirement Analysis](01-Requirements/analysis.md) | Candidate requirements có coherent và khả thi không? | Issue log, dependency và decision tables |
| 7 | [Requirement Prioritization](01-Requirements/prioritization.md) | Release phải giữ, hoãn hoặc loại scope nào? | Prioritized requirement set |
| 8 | [User Stories và Acceptance Criteria](01-Requirements/user-stories.md) | Làm sao tổ chức conversation và delivery slices? | Stories và acceptance criteria |
| 9 | [Requirement Verification và Validation](01-Requirements/validation.md) | Specification đã được viết đúng và mô tả đúng product chưa? | Review log và validation evidence |

### Case study xuyên module

Module sử dụng **Food Delivery — Order lifecycle** làm anchor example:

- khách yêu cầu báo giá;
- platform kiểm tra menu, địa chỉ và pricing rules;
- payment provider authorize thanh toán;
- nhà hàng chấp nhận hoặc từ chối;
- order được theo dõi, hủy hoặc hoàn tiền theo state;
- operations, security và finance xử lý exceptions.

Các domain Library, Hotel, Banking và University được dùng làm comparative examples và bài tập chuyển giao kiến thức.

### Vòng lặp học cho mỗi lesson

```text
Read mental model
→ Explain in your own words
→ Diagnose bad example
→ Produce an artifact
→ Review with checklist
→ Apply to another domain
```

Một concept chỉ được xem là đã học khi bạn có thể:

1. định nghĩa bằng lời của mình;
2. giải thích vấn đề nó giải quyết;
3. phân biệt với concept gần nhất;
4. nhận diện cách dùng sai;
5. áp dụng vào case mới;
6. nói khi nào không nên dùng;
7. nối nó với artifact upstream và downstream.

### Exit criteria của Module 01

Trước khi chuyển sang `02-SRS`, hãy tự kiểm tra:

- [ ] Problem, goal, requirement và solution proposal được phân biệt rõ.
- [ ] System boundary và external responsibilities đã được ghi.
- [ ] Stakeholder map bao phủ users, operations, support, risk và external parties.
- [ ] Functional requirements có trigger, condition và observable response.
- [ ] Quality requirements có environment, workload, measure và verification method.
- [ ] Elicitation plan phối hợp ít nhất hai nguồn hoặc kỹ thuật phù hợp.
- [ ] Requirement set đã được kiểm tra conflict, omission, dependency và feasibility.
- [ ] MoSCoW labels có release objective và rationale.
- [ ] User stories không bị dùng thay thế mọi dạng specification.
- [ ] Acceptance criteria có normal, boundary và failure examples phù hợp.
- [ ] Verification và validation evidence được phân biệt.
- [ ] Open issues, assumptions và decisions có owner/status.

## Bốn cấp độ luyện tập

Mỗi lesson có:

| Level | Năng lực |
|---|---|
| Basic | Recall, explain và classify |
| Intermediate | Diagnose, rewrite và complete partial artifacts |
| Advanced | Phân tích conflict, trade-off và ra quyết định |
| Case Study | Tạo coherent artifact set cho một domain mới |

Đáp án không được đặt ngay dưới bài tập để người học có không gian tự reasoning.

## Exam preparation trong từng lesson

Mỗi lesson kết thúc bằng bốn nhóm:

- **Key Concepts:** mental models bắt buộc nhớ;
- **Common Exam Questions:** câu hỏi explain và justify;
- **Comparison Questions:** phân biệt theo axis và example;
- **Scenario Question:** áp dụng vào một đề bài chưa được giải sẵn.

## Nguyên tắc học và thiết kế

1. **Understanding > Memorization** — giải thích được lý do trước khi nhớ thuật ngữ.
2. **Reasoning > UML Syntax** — diagram phải trả lời một câu hỏi phân tích hoặc thiết kế.
3. **Requirements > Diagrams** — không tạo model khi chưa biết requirement nào cần làm rõ.
4. **Design Decisions > Drawing Diagrams** — mỗi relationship và abstraction phải có rationale.
5. **Practical Application > Definitions** — concept phải được dùng trong scenario, artifact hoặc code.

## Quality standard của nội dung

Mỗi module được review theo bốn tiêu chí:

| Tiêu chí | Câu hỏi kiểm tra |
|---|---|
| Technical Accuracy | Semantic, terminology và notation có đúng trong scope? |
| Conceptual Clarity | Boundary và distinction có đủ rõ? |
| Practical Applicability | Người học có thể tạo hoặc review artifact thực tế? |
| Cross-module Consistency | Thuật ngữ, ID, rules, assumptions và links có nhất quán? |

Mỗi concept quan trọng phải trả lời: What, Why, Problem, How, When Not to Use, Related Concepts và Common Mistakes.

## Quy ước nội dung

- Giải thích bằng **tiếng Việt** và giữ English technical terms cần thiết.
- Requirement IDs dùng các prefix như `BR`, `FR`, `QR`, `BRULE`, `CON`, `AC` và `TC`.
- Ví dụ phải nói rõ boundary và assumptions.
- Một convention phổ biến không được trình bày như chân lý duy nhất nếu có alternatives hợp lệ.
- Pattern, UML diagram hoặc technology chỉ xuất hiện khi giải quyết một modeling/design question cụ thể.
- Requirements, models, design, code và tests phải có traceability hai chiều ở mức phù hợp.

## Cấu trúc repository mục tiêu

```text
OOAD-Knowledge-Base/
├── 00-Roadmap/
├── 01-Requirements/
├── 02-SRS/
├── 03-Use-Cases/
├── 04-OO-Analysis/
├── 05-UML/
├── 06-OO-Design/
├── 07-Case-Studies/
├── 08-Exercises/
└── 09-Exam-Preparation/
```

Chi tiết từng lesson và coverage của yêu cầu ban đầu nằm trong [Complete Table of Contents](00-Roadmap/table-of-contents.md).

## Bước triển khai kế tiếp

Module tiếp theo là `02-SRS`, theo thứ tự:

1. `srs.md` — mục đích, audience, cấu trúc và tailoring;
2. `requirement-quality.md` — đặc điểm requirement tốt và review practice;
3. `traceability.md` — forward/backward/bidirectional traceability và RTM.

Output của `01-Requirements` là input trực tiếp cho Module 02; vì vậy mọi thay đổi terminology hoặc requirement ID trong Module 02 phải được kiểm tra ngược với Module 01.
