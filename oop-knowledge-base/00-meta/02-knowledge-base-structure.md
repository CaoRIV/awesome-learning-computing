# Knowledge Base Structure

## Overview

Tài liệu này định nghĩa information architecture, quy ước nội dung, chuẩn code và quality gate cho toàn bộ OOP Knowledge Base. Mục tiêu là để mỗi file vừa đọc độc lập được, vừa tạo thành một learning sequence có liên kết và không lặp ý không cần thiết.

## 1. Design goals

Knowledge base phải tối ưu cho bốn kiểu sử dụng:

1. **Sequential learning**: học theo roadmap và prerequisite.
2. **Concept lookup**: tra cứu nhanh một thuật ngữ hoặc cơ chế.
3. **Code/design review**: dùng checklist để phân tích code thực tế.
4. **Long-term maintenance**: thêm module mà không phá navigation hoặc terminology.

## 2. Planned directory structure

```text
oop-knowledge-base/
├── README.md
├── 00-meta/
│   ├── 01-learning-roadmap.md
│   ├── 02-knowledge-base-structure.md
│   ├── 03-table-of-contents.md
│   ├── 04-topic-dependencies.md
│   └── topic-template.md
├── 01-oop-fundamentals/
│   ├── 01-programming-paradigms.md
│   ├── 02-class-object-state-behavior-identity.md
│   ├── 03-construction-destruction-members.md
│   ├── 04-encapsulation-invariants.md
│   ├── 05-abstraction-interfaces.md
│   ├── 06-inheritance-subtyping.md
│   └── 07-polymorphism-dynamic-dispatch.md
├── 02-object-relationships/
│   ├── 01-relationship-modeling.md
│   ├── 02-association-dependency.md
│   ├── 03-aggregation-composition.md
│   ├── 04-delegation.md
│   └── 05-composition-over-inheritance.md
├── 03-advanced-oop-cpp/
│   ├── 01-virtual-functions-abstract-classes.md
│   ├── 02-runtime-polymorphism-safety.md
│   ├── 03-coupling-cohesion.md
│   ├── 04-dependency-injection.md
│   ├── 05-immutability-value-semantics.md
│   ├── 06-object-lifetime-ownership.md
│   ├── 07-raii-smart-pointers.md
│   ├── 08-copy-semantics-rule-of-three.md
│   ├── 09-move-semantics-rule-of-five.md
│   └── 10-rule-of-zero-exception-safety.md
├── 04-design-principles-solid/
│   ├── 01-high-cohesion-low-coupling.md
│   ├── 02-program-to-interface.md
│   ├── 03-encapsulate-what-varies.md
│   ├── 04-composition-over-inheritance.md
│   ├── 05-solid-overview.md
│   ├── 06-srp.md
│   ├── 07-ocp.md
│   ├── 08-lsp.md
│   ├── 09-isp.md
│   └── 10-dip.md
├── 05-design-patterns/
│   ├── 00-pattern-language-and-selection.md
│   ├── creational/
│   │   ├── 01-factory-method.md
│   │   ├── 02-abstract-factory.md
│   │   ├── 03-builder.md
│   │   ├── 04-prototype.md
│   │   └── 05-singleton.md
│   ├── structural/
│   │   ├── 01-adapter.md
│   │   ├── 02-decorator.md
│   │   ├── 03-facade.md
│   │   ├── 04-composite.md
│   │   ├── 05-proxy.md
│   │   └── 06-bridge.md
│   └── behavioral/
│       ├── 01-strategy.md
│       ├── 02-observer.md
│       ├── 03-command.md
│       ├── 04-state.md
│       ├── 05-template-method.md
│       ├── 06-chain-of-responsibility.md
│       └── 07-iterator.md
├── 06-ooad/
│   ├── 01-ooa-vs-ood.md
│   ├── 02-requirements-and-use-cases.md
│   ├── 03-identifying-objects.md
│   ├── 04-responsibility-assignment.md
│   ├── 05-relationships-and-domain-model.md
│   ├── 06-class-diagrams.md
│   ├── 07-sequence-diagrams.md
│   ├── 08-activity-diagrams.md
│   └── 09-requirement-to-code-workflow.md
├── 07-case-studies/
│   ├── 01-library-management/
│   ├── 02-banking/
│   ├── 03-hotel-management/
│   ├── 04-ecommerce/
│   ├── 05-parking-lot/
│   └── 06-food-delivery/
├── 08-architecture-bridge/
│   ├── 01-object-to-module-boundaries.md
│   ├── 02-layered-and-hexagonal.md
│   ├── 03-domain-persistence-transport-models.md
│   └── 04-oop-limits-and-system-design.md
├── glossary/
│   ├── glossary-en-vi.md
│   └── concept-comparison-index.md
└── assets/
    └── diagrams/
```

Các thư mục module được tạo khi bắt đầu viết module tương ứng để tránh file rỗng hoặc placeholder. Directory tree trên là contract về organization, không khẳng định mọi topic phải giữ nguyên ranh giới nếu review cho thấy cần tách hoặc gộp.

## 3. Rationale của cấu trúc

### `00-meta`

Chứa learning system, không chứa kiến thức OOP trực tiếp. Việc tách meta giúp roadmap và quality rules không làm nhiễu concept navigation.

### `01` đến `04`

Đi từ object model đến dependency management. Đây là core bắt buộc trước patterns và OOAD.

### `05-design-patterns`

Tách theo GoF categories để tra cứu, nhưng learning order có thể khác directory order. Category là taxonomy, không phải prerequisite graph.

### `06-ooad`

Đặt sau principles/patterns để người học có design vocabulary, nhưng analysis không được biến thành việc tìm chỗ nhét pattern.

### `07-case-studies`

Mỗi hệ thống là một thư mục vì có requirements, diagrams, code và review riêng. Case study dùng lại concept bằng link thay vì định nghĩa lại toàn bộ.

### `08-architecture-bridge`

Tạo điểm chuyển rõ giữa object-level design và system-level concerns, ngăn việc suy diễn rằng class diagram đủ để thiết kế distributed system.

## 4. File granularity

Một file nên đại diện cho một **learning unit** có thể hoàn thành trong một đến hai phiên học sâu. Tách file khi:

- có prerequisite khác biệt;
- cơ chế kỹ thuật cần code/diagram riêng;
- topic có đủ problem, example, trade-offs và exercises độc lập;
- file vượt quá khả năng review như một đơn vị coherent.

Gộp topic khi việc tách làm mất causal connection. Ví dụ `State`, `Behavior`, `Identity` ở cùng một file vì chúng cùng tạo mental model của object; copy và move tách file vì khác prerequisite và failure mode.

## 5. Naming conventions

### File và directory

- Dùng lowercase kebab-case ASCII.
- Prefix số hai chữ số thể hiện reading order cục bộ.
- Không đưa `final`, `new`, `v2` vào tên file; lịch sử thuộc version control.
- Tên phản ánh concept, không phản ánh tuần học.

### Heading

- Một H1 duy nhất, trùng title của topic.
- H2 cho các section ổn định.
- H3 cho sub-concept hoặc scenario.
- Không nhảy từ H2 xuống H4.

### Thuật ngữ

Lần đầu xuất hiện trong một topic:

> Tiếng Việt (**English term**)

Sau đó có thể dùng English term khi chính xác hơn. Các thuật ngữ có nhiều bản dịch phải dùng canonical translation trong glossary.

## 6. Topic content contract

Mỗi topic phải trả lời đủ các câu hỏi sau, dù heading có thể điều chỉnh:

1. **What** — concept là gì, boundary của nó ở đâu?
2. **Why** — force lịch sử hoặc thiết kế nào khiến nó tồn tại?
3. **Problem** — nếu thiếu concept, failure mode cụ thể là gì?
4. **How** — language/runtime/design mechanism hoạt động thế nào?
5. **Example** — scenario thực tế chứng minh behavior gì?
6. **Bad Example** — code sai ở đâu, hậu quả xuất hiện dưới change nào?
7. **Good Example** — refactoring thay đổi dependency/responsibility ra sao?
8. **Trade-offs** — benefit, cost, alternative và context.
9. **When to use / not use** — decision signals và contraindications.
10. **Connection** — prerequisite, related concept và downstream use.

Phần cuối bắt buộc có:

- Common Mistakes;
- Misconceptions;
- 5–10 Interview Questions;
- 3–5 Thinking Questions;
- Mini Exercise;
- Challenge;
- Summary.

## 7. Chuẩn cho code C++

### Language level

C++20 là baseline để dùng move semantics, smart pointers, concepts ở mức so sánh và Standard Library hiện đại. Nếu feature mới hơn cần thiết, file phải ghi rõ standard version.

### Code requirements

- Snippet phải compile được trong context đã trình bày hoặc được đánh dấu rõ là pseudocode.
- Include đầy đủ những header trực tiếp sử dụng.
- Không dùng `using namespace std;` trong tài liệu.
- Dùng `const` và reference có chủ ý.
- Ownership phải đọc được từ API hoặc được mô tả cạnh code.
- Polymorphic base phải nêu destruction policy.
- Không dùng raw owning pointer.
- Exception/error strategy phải nhất quán trong cùng example.
- Naming phản ánh domain responsibility.
- Comment giải thích **why**, không diễn giải lại syntax.

### Example layering

Một topic nên dùng ba tầng nếu phù hợp:

1. **Micro example** cô lập cơ chế.
2. **Design example** cho thấy responsibility/dependency.
3. **Change scenario** kiểm tra extensibility và trade-off.

### Bad code policy

Bad example phải realistic và có mục đích chẩn đoán. Ngay sau code phải chỉ ra:

- symptom;
- root cause;
- change scenario làm vấn đề lộ ra;
- bug risk hoặc maintenance cost;
- điều refactoring cần bảo toàn.

## 8. Technical accuracy policy

Mỗi khẳng định quan trọng cần được phân loại ngầm hoặc trực tiếp:

- **C++ language rule**: điều standard quy định.
- **Common implementation detail**: ví dụ vtable; phổ biến nhưng không bắt buộc bởi standard.
- **Design principle**: heuristic có context.
- **Project convention**: lựa chọn nhất quán trong knowledge base.

Khi có nhiều cách tiếp cận, trình bày theo thứ tự:

1. cách phổ biến;
2. alternative;
3. comparison;
4. trade-off;
5. recommendation theo context.

Không dùng “best practice” nếu không nêu scope và force khiến recommendation đúng.

## 9. Diagram conventions

### Mermaid

Dùng Mermaid cho:

- class relationship;
- sequence/interaction;
- activity/workflow;
- dependency graph;
- state transition.

Mỗi diagram phải có đoạn giải thích về message flow hoặc relationship semantics. Diagram không thay thế prose.

### UML precision

- Association ghi cardinality khi có ý nghĩa.
- Composition diamond chỉ dùng khi part có lifetime phụ thuộc whole trong model đang xét.
- Aggregation cần giải thích semantics cụ thể vì diễn giải UML thường mơ hồ.
- Dependency arrow không được dùng thay cho ownership.
- Class diagram không biểu diễn đầy đủ runtime object graph.

## 10. Cross-linking rules

Mỗi topic có ba loại link:

- **Prerequisites** ở đầu file.
- **Related concepts** tại section Connection.
- **Next topics** ở cuối file, dựa trên dependency graph.

Không sao chép lời giải thích dài giữa các file. File mới tóm tắt đủ context trong 1–3 câu rồi link về source concept.

## 11. Comparison tables

Các cặp dễ nhầm bắt buộc có bảng theo nhiều dimension, không chỉ một câu “A khác B”:

- abstraction / encapsulation;
- inheritance / composition;
- overloading / overriding;
- aggregation / composition;
- interface / abstract class;
- coupling / cohesion;
- association / dependency;
- compile-time / runtime polymorphism;
- value semantics / reference semantics;
- copy / move;
- OOA / OOD;
- pattern / principle.

Dimension gợi ý: intent, mechanism, ownership, lifetime, binding time, substitutability, change impact và typical misuse.

## 12. Exercise architecture

### Mini Exercise

Kiểm tra một concept, hoàn thành trong 20–40 phút và có acceptance criteria quan sát được.

### Challenge

Kết hợp nhiều concept, có ambiguity có chủ ý và yêu cầu nêu assumptions/trade-offs. Challenge không chỉ tăng số dòng code.

### Cumulative checkpoint

Đặt ở cuối level; bắt buộc có change request thứ hai để kiểm tra khả năng thích nghi của design.

## 13. Case-study structure

Mỗi case-study directory dự kiến có:

```text
case-study-name/
├── README.md
├── 01-requirements.md
├── 02-analysis.md
├── 03-design.md
├── 04-implementation.md
├── 05-change-request.md
└── 06-review-and-trade-offs.md
```

Case study phải ghi rõ scope. Ví dụ Banking System trong curriculum không giả định tự xây core banking production-grade; nó tập trung vào object design, invariant và consistency trong một bounded exercise.

## 14. Definition of Done cho một topic

Một topic chỉ đạt **Stable** khi tất cả điều kiện đúng:

### Content

- Có What, Why, Problem, How.
- Có example, bad example, good/refactored example.
- Có trade-offs, when to use và when not to use.
- Có connections và comparison khi dễ nhầm.
- Có Common Mistakes, Misconceptions, Interview Questions, Thinking Questions, Mini Exercise, Challenge và Summary.

### Code

- Compile/test thành công với standard đã ghi.
- Không có undefined behavior trong intended path.
- Ownership, lifetime và error path được giải thích.
- Bad code được gắn nhãn và không bị hiểu là recommendation.
- Refactored code giải quyết root cause đã phân tích.

### Learning quality

- Learning outcomes có thể kiểm chứng.
- Exercise có acceptance criteria.
- Interview questions có answer key hoặc link đến section chứa câu trả lời.
- Không cần đọc topic downstream để hiểu prerequisite concept.

### Maintenance

- Internal links hợp lệ.
- Terminology nhất quán với glossary.
- Có status và last-reviewed metadata.
- Diagram render được.
- Không lặp nội dung dài đã có source topic.

## 15. Review workflow

1. **Scope review**: topic có đúng ranh giới và prerequisite không?
2. **Technical review**: language rules, memory model, dispatch và UML có chính xác không?
3. **Design review**: recommendation có context/trade-off không?
4. **Code review**: compile, test, ownership, error handling.
5. **Learning review**: outcomes, questions và exercises có đo đúng năng lực không?
6. **Link review**: navigation và dependency links.

## 16. Maintenance rules

- Thay đổi terminology canonical phải cập nhật glossary và search toàn knowledge base.
- Tách/gộp file phải cập nhật TOC, dependency graph và incoming links.
- Thay đổi recommendation kỹ thuật phải ghi context, không âm thầm biến heuristic thành rule.
- Module mới phải chỉ ra prerequisite và capability mà nó mở khóa.
- Code example dùng lại nên được trích từ một source canonical, tránh nhiều bản drift.

## Summary

Cấu trúc knowledge base phản ánh chuỗi năng lực từ object mechanics đến system reasoning. Topic template tạo tính nhất quán; dependency graph quyết định thứ tự; Definition of Done bảo đảm một file không chỉ “có nội dung” mà thực sự dùng được để học, review và bảo trì lâu dài.

