# Awesome Learning Computing [![Awesome](https://awesome.re/badge-flat2.svg)](https://awesome.re)

> A structured, long-term knowledge base for learning Computer Science, Software Engineering, Information Technology, Artificial Intelligence, data, and computer systems—from fundamentals to real-world practice.

Tài liệu được viết chủ yếu bằng **tiếng Việt**, đồng thời giữ nguyên các thuật ngữ kỹ thuật quan trọng bằng **English** để thuận tiện khi đọc documentation, sách, source code và research papers.

## Contents

- [About](#about)
- [Learning Tracks](#learning-tracks)
- [Available Now](#available-now)
- [How to Use This Repository](#how-to-use-this-repository)
- [Learning Model](#learning-model)
- [Knowledge Quality Standard](#knowledge-quality-standard)
- [Repository Structure](#repository-structure)
- [Roadmap](#roadmap)
- [Who This Is For](#who-this-is-for)
- [Scope and Boundaries](#scope-and-boundaries)

## About

**Awesome Learning Computing** là một personal knowledge base được xây dựng để học computing theo hệ thống, thay vì tích lũy các ghi chú và đường dẫn rời rạc.

Repository hướng tới ba kết quả:

1. Xây dựng mental model chính xác từ nền tảng.
2. Kết nối theory với code, design decisions và engineering trade-offs.
3. Tạo một curriculum có thể học tuần tự, tra cứu độc lập và duy trì lâu dài.

Đây không chỉ là một danh sách tài nguyên. Mỗi learning module phải giải thích động cơ của concept, cơ chế hoạt động, failure modes, cách áp dụng, giới hạn và mối liên hệ với các chủ đề khác.

## Learning Tracks

| Track | Focus | Status |
|---|---|---|
| [Object-Oriented Programming](oop-knowledge-base/README.md) | OOP, SOLID, Design Patterns, OOAD và hướng chuyển sang Software Architecture. | Foundation available |
| Programming Foundations | Programming models, problem solving, debugging và language fundamentals. | Planned |
| Computer Science Foundations | Discrete Mathematics, algorithms, data structures, computation và complexity. | Planned |
| Software Engineering | Requirements, testing, code quality, delivery, maintenance và engineering practices. | Planned |
| Computer Systems | Computer architecture, operating systems, concurrency và performance. | Planned |
| Networks and Distributed Systems | Networking, distributed computation, consistency, reliability và scalability. | Planned |
| Data and Databases | Data modeling, SQL, storage engines, transactions và data systems. | Planned |
| Artificial Intelligence | AI foundations, Machine Learning, Deep Learning, NLP và intelligent systems. | Planned |
| Cybersecurity | Security foundations, secure software, systems security và threat modeling. | Planned |
| Cloud and DevOps | Cloud platforms, containers, infrastructure, observability và operations. | Planned |
| Mathematics for Computing | Logic, discrete structures, probability, statistics và linear algebra. | Planned |

`Foundation available` nghĩa là track đã có roadmap, curriculum structure, topic dependencies và content-quality contract. Nó không có nghĩa toàn bộ lesson content đã hoàn thành.

## Available Now

### Object-Oriented Programming Knowledge Base

Track đầu tiên xây dựng nền tảng theo learning path:

> OOP → SOLID → Design Patterns → OOAD → Software Architecture → System Design

Các entry point:

- [OOP Learning Roadmap](oop-knowledge-base/00-meta/01-learning-roadmap.md) — Thứ tự học, outcomes, checkpoints và exit criteria.
- [Knowledge Base Structure](oop-knowledge-base/00-meta/02-knowledge-base-structure.md) — Kiến trúc nội dung, code standards và Definition of Done.
- [Complete Table of Contents](oop-knowledge-base/00-meta/03-table-of-contents.md) — Curriculum đầy đủ từ fundamentals đến architecture bridge.
- [Topic Dependencies](oop-knowledge-base/00-meta/04-topic-dependencies.md) — Prerequisite graph và critical learning paths.
- [Topic Template](oop-knowledge-base/00-meta/topic-template.md) — Content contract dùng cho từng learning unit.

OOP track sử dụng **C++20** làm ngôn ngữ chính để object lifetime, ownership, RAII, value semantics, copy/move semantics và runtime polymorphism được trình bày đầy đủ. Java hoặc C# chỉ được dùng khi phép so sánh giúp làm rõ concept.

## How to Use This Repository

### Học tuần tự

1. Mở roadmap của track và kiểm tra prerequisite.
2. Học topic theo dependency graph, không chỉ theo số thứ tự file.
3. Tự giải thích concept trước khi đọc lại summary.
4. Compile, chạy và thay đổi example code.
5. Hoàn thành Mini Exercise và review bằng acceptance criteria.
6. Chỉ chuyển module sau khi đạt checkpoint hoặc exit criteria.

### Tra cứu concept

1. Tìm concept trong Table of Contents của track.
2. Đọc Overview để xác định đúng scope.
3. Kiểm tra Bad Example, Trade-offs và When NOT to Use.
4. Theo các liên kết prerequisite hoặc related concepts khi mental model còn thiếu.

### Review code hoặc design

Đặt các câu hỏi sau trước khi gắn tên principle hoặc pattern:

- Requirement và behavior cần bảo toàn là gì?
- Invariant nằm ở đâu và object nào bảo vệ nó?
- Responsibility có cohesive không?
- Dependency nào ổn định, dependency nào thường thay đổi?
- Ownership và lifetime có rõ không?
- Abstraction có giải quyết variation thực tế hay chỉ dự đoán tương lai?
- Error path, partial failure và testability đã được xem xét chưa?

## Learning Model

Mỗi topic được phát triển theo vòng lặp:

```text
Concept → Understanding → Example → Design → Practice → Review
```

Nội dung không dừng ở câu hỏi **What**. Một learning unit hoàn chỉnh phải trả lời:

| Dimension | Question |
|---|---|
| What | Concept là gì và boundary của nó ở đâu? |
| Why | Vì sao concept tồn tại? |
| Problem | Thiếu concept này dẫn đến failure mode nào? |
| How | Nó hoạt động ở design, compile-time hoặc runtime như thế nào? |
| Example | Behavior hoặc design decision nào được minh họa? |
| Bad Example | Root cause của thiết kế có vấn đề là gì? |
| Good Example | Refactoring thay đổi responsibility hoặc dependency ra sao? |
| Trade-offs | Lợi ích, chi phí và alternative là gì? |
| Use / Avoid | Khi nào nên dùng và khi nào không nên dùng? |
| Connection | Concept phụ thuộc và mở khóa kiến thức nào? |

### Learning principles

- **Deep Understanding > Memorization**.
- **Design Reasoning > Syntax**.
- **Practical Application > Definitions**.
- **Technical Accuracy > Brevity**.
- **Contextual Decisions > Universal Best Practices**.

## Knowledge Quality Standard

### Technical accuracy

- Phân biệt language rule, common implementation detail, design heuristic và project convention.
- Không khẳng định một design là tốt nhất nếu kết luận phụ thuộc context.
- Với nhiều approaches: trình bày cách phổ biến, alternatives, comparison, trade-offs và recommendation có điều kiện.
- Memory, ownership, object lifetime và error handling phải được giải thích khi có liên quan.

### Code quality

- Code phải phục vụ concept hoặc design reasoning, không chỉ trình diễn syntax.
- Example C++ phải chỉ rõ language standard và có thể compile trong context được mô tả.
- Naming, responsibility, coupling, cohesion, encapsulation và maintainability phải được review.
- Raw owning pointers không được dùng như design mặc định.
- Bad examples phải ghi rõ failure scenario và không được trình bày như recommendation.

### Learning quality

Mỗi topic đầy đủ gồm:

- motivation và conceptual model;
- focused example;
- bad design analysis và refactored design;
- trade-offs, alternatives và decision signals;
- Common Mistakes và Misconceptions;
- Interview Questions và Thinking Questions;
- Mini Exercise có acceptance criteria;
- Challenge có ambiguity và change request;
- summary và concept connections.

### Curation policy

Repository ưu tiên nội dung có thể giải thích và kiểm chứng hơn số lượng. Một resource hoặc concept chỉ nên được thêm khi:

- nằm đúng phạm vi của learning track;
- tạo learning value rõ ràng;
- không trùng lặp mà không có góc nhìn mới;
- có nguồn đáng tin cậy hoặc reasoning kỹ thuật có thể kiểm chứng;
- được đặt đúng vị trí trong prerequisite graph;
- có mô tả cho biết người học nhận được gì từ nó.

## Repository Structure

Cấu trúc hiện tại:

```text
awesome-learning-computing/
├── README.md
└── oop-knowledge-base/
    ├── README.md
    └── 00-meta/
        ├── 01-learning-roadmap.md
        ├── 02-knowledge-base-structure.md
        ├── 03-table-of-contents.md
        ├── 04-topic-dependencies.md
        └── topic-template.md
```

Mỗi track mới chỉ được tạo khi đã xác định:

1. scope và learning outcomes;
2. prerequisite và dependency graph;
3. curriculum structure;
4. content template và quality gate;
5. ít nhất một implementation sequence khả thi.

Quy tắc này giữ repository có cấu trúc và tránh tạo nhiều directory rỗng chỉ để thể hiện kế hoạch.

## Roadmap

### Phase 1 — Object-Oriented Design Foundation

- [x] Learning roadmap.
- [x] Knowledge base architecture.
- [x] Complete curriculum and topic dependencies.
- [x] Standardized topic template.
- [ ] OOP Fundamentals modules.
- [ ] Object Relationships and advanced C++ OOP modules.
- [ ] SOLID and Design Patterns modules.
- [ ] OOAD and real-world case studies.
- [ ] Architecture and System Design bridge.

### Phase 2 — Core Computer Science

- Programming foundations.
- Discrete Mathematics.
- Data Structures and Algorithms.
- Computer Architecture and Operating Systems.
- Networking and Database Systems.

### Phase 3 — Software Engineering

- Requirements and software design.
- Testing and quality engineering.
- Version control and collaborative development.
- Delivery, maintenance and observability.
- Software Architecture and distributed systems.

### Phase 4 — Data and Artificial Intelligence

- Mathematics for Machine Learning.
- Data analysis and data engineering foundations.
- Classical Machine Learning.
- Deep Learning and representation learning.
- Natural Language Processing and modern AI systems.
- AI engineering, evaluation, safety and operations.

Roadmap ưu tiên hoàn thành vertical learning slices có exercises và review, thay vì mở đồng thời mọi track ở trạng thái dở dang.

## Who This Is For

Repository phù hợp với:

- sinh viên IT/Computer Science đã có kiến thức lập trình cơ bản;
- self-taught developers cần hệ thống hóa kiến thức;
- software engineers muốn củng cố foundations và design reasoning;
- người học AI muốn hiểu computer systems và software engineering thay vì chỉ sử dụng frameworks;
- người cần một knowledge base để học, tra cứu, review code và chuẩn bị technical interviews.

Repository không giả định người đọc là beginner tuyệt đối, nhưng không bỏ qua nền tảng quan trọng. Mỗi track sẽ ghi rõ baseline riêng.

## Scope and Boundaries

### Included

- Foundational theory gắn với implementation và engineering consequences.
- Programming, software design, systems, data và AI.
- Code examples, diagrams, exercises, case studies và review checklists.
- Connections giữa concepts ở nhiều cấp độ abstraction.

### Not the primary goal

- Link dump không có curation hoặc description.
- Cheat sheets thay thế cho conceptual understanding.
- Framework tutorials thiếu nền tảng kỹ thuật.
- Pattern catalogs tách rời problem và trade-offs.
- Nội dung tuyên bố “best practice” mà không xác định context.
- Chạy theo số lượng topic hoặc công nghệ đang thịnh hành.

## Contributing

Contributions nên giữ đúng learning philosophy và quality standards của repository. Một contribution tốt phải:

- có scope rõ và nằm đúng track;
- giải thích problem trước solution;
- dùng terminology nhất quán;
- có code/diagram có thể kiểm chứng khi phù hợp;
- phân tích alternatives và trade-offs;
- cập nhật navigation cùng dependency links bị ảnh hưởng;
- không tạo abstraction hoặc Design Pattern chỉ để làm nội dung có vẻ nâng cao.

Với thay đổi lớn, proposal nên mô tả learning gap, prerequisite, expected outcome và ảnh hưởng tới curriculum trước khi viết module.

## Footnotes

The [Awesome badge](https://awesome.re) identifies the repository's curated-learning direction. It does not imply that this project has been accepted into the official Awesome index.

