# Learning Roadmap

## Overview

Roadmap này tổ chức việc học theo dependency của năng lực thiết kế, không theo độ nổi tiếng của thuật ngữ. Mục tiêu không phải đi qua danh sách khái niệm, mà phát triển dần khả năng:

1. mô hình hóa state, behavior, identity và lifetime;
2. phân bổ responsibility cho object;
3. kiểm soát coupling, invariants và dependency;
4. đánh giá substitutability và chọn composition hoặc inheritance;
5. tạo variation point bằng abstraction và polymorphism;
6. áp dụng principles và patterns theo pressure thực tế;
7. chuyển requirement thành thiết kế và code có thể thay đổi an toàn.

## 1. Cách sử dụng roadmap

Mỗi topic đi qua vòng lặp:

> Concept → Understanding → Example → Design → Practice → Review

Không chuyển topic chỉ vì đã đọc xong. Một topic được xem là nắm vững khi người học có thể:

- giải thích **What, Why, Problem, How** bằng lời của mình;
- đọc code và chỉ ra cơ chế runtime hoặc compile-time liên quan;
- nhận diện bad design cùng failure mode cụ thể;
- refactor mà không làm thay đổi behavior ngoài ý muốn;
- nêu ít nhất một trade-off và một trường hợp không nên áp dụng;
- hoàn thành mini exercise không sao chép ví dụ;
- kết nối topic với prerequisite và downstream concept.

## 2. Baseline trước khi bắt đầu

### Kiến thức giả định

- C++ cơ bản: function, reference, pointer, `const`, scope, header/source file.
- Cấu trúc dữ liệu cơ bản và Standard Library: `std::string`, `std::vector`, `std::map`.
- Compile, link và đọc compiler diagnostics ở mức cơ bản.
- Biết viết test hoặc ít nhất là assertion để kiểm chứng behavior.

### C++ bridge cần bổ sung nếu baseline chưa chắc

Các chủ đề này không phải OOP, nhưng là prerequisite trực tiếp:

- value semantics và reference semantics;
- stack, free store và automatic storage duration;
- lvalue, rvalue ở mức phục vụ copy/move;
- `const` correctness;
- raw pointer so với `std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`;
- exception basics và deterministic destruction;
- header dependency, forward declaration và One Definition Rule ở mức sử dụng.

### Diagnostic đầu vào

Thiết kế một `BankAccount` nhỏ với deposit, withdraw và transaction history. Sau đó tự trả lời:

1. Invariant của account là gì?
2. Thành phần nào nên `private`, và vì sao?
3. Nếu transaction history giữ pointer đến account thì ai sở hữu ai?
4. Copy account có ý nghĩa domain không?
5. Làm sao thay đổi policy tính phí mà không sửa mọi account?

Diagnostic không dùng để chấm điểm; nó xác định những lỗ hổng cần quay lại khi học Level 1–3.

## 3. Roadmap tổng thể

| Phase | Trọng tâm | Năng lực đầu ra | Checkpoint chính |
|---|---|---|---|
| 0 | C++ object-model bridge | Lý giải value, reference, lifetime và ownership | Trace được construction/destruction/copy/move |
| 1 | OOP Fundamentals | Thiết kế class giữ invariant và hiểu bốn trụ cột | Refactor procedural module thành object model hợp lý |
| 2 | Object Relationships | Mô hình hóa quan hệ và chọn composition/inheritance | Vẽ object graph và giải thích ownership |
| 3 | Advanced OOP in C++ | Viết polymorphic code an toàn về memory/lifetime | Rule of 0, RAII và virtual destruction đúng |
| 4 | Design Principles & SOLID | Phân tích change pressure và dependency direction | Refactor một thiết kế cứng mà không over-engineer |
| 5 | Design Patterns | Chọn pattern từ problem/context/consequences | So sánh pattern với naive solution và alternative |
| 6 | OOAD | Chuyển requirement thành interaction và class design | Trace Requirement → Use Case → Code |
| 7 | Real-world Design | Thiết kế hệ thống nhỏ theo nhiều iteration | Defend design bằng constraints và trade-offs |
| 8 | Architecture Bridge | Kết nối object design với boundary cấp hệ thống | Phân biệt object-level và architecture-level decision |

## 4. Phase 0 — C++ Object-Model Bridge

### Mục tiêu

Xây nền để tránh học OOP C++ như Java có thêm pointer. C++ cho phép object có value semantics, deterministic lifetime và nhiều mô hình ownership; những đặc điểm này ảnh hưởng trực tiếp đến API và thiết kế class.

### Topics

1. Object, storage duration và lifetime.
2. Value semantics, identity và reference semantics.
3. `const` correctness và observable state.
4. Resource ownership và smart pointers.
5. Exceptions, stack unwinding và destructor.
6. Header boundary và dependency vật lý.

### Deliverable

Một chương trình trace lifecycle của object qua return-by-value, pass-by-reference, container insertion và move.

### Exit criteria

- Phân biệt được storage với object lifetime.
- Không dùng `shared_ptr` như lựa chọn mặc định.
- Giải thích được khi nào type nên copyable, movable hoặc non-copyable.
- Biết ownership là quyết định thiết kế, không chỉ là quyết định cấp phát memory.

## 5. Phase 1 — OOP Fundamentals

### Module 1.1 — Programming Paradigms

Học procedural, object-oriented và functional programming như các cách tổ chức computation và change, không phải các phe loại trừ nhau.

**Outcome:** chọn style phù hợp cho từng phần của chương trình; không biến mọi function thành method hoặc mọi data thành object có getter/setter.

### Module 1.2 — Class and Object

Đi từ state, behavior, identity đến responsibility, invariant, constructor và destructor.

**Outcome:** class biểu diễn một abstraction có behavior meaningful, không chỉ là record dữ liệu.

### Module 1.3 — Encapsulation

Phân biệt encapsulation với data hiding; dùng access control để bảo vệ invariant và thu nhỏ change surface.

**Outcome:** thiết kế API khiến invalid state khó hoặc không thể biểu diễn.

### Module 1.4 — Abstraction

Học abstraction như hành động bỏ qua detail không liên quan để tạo một model/useful contract. So sánh interface, abstract class và concrete abstraction.

**Outcome:** tạo contract dựa trên capability và client need, không dựa trên danh sách method tùy tiện.

### Module 1.5 — Inheritance

Học inheritance sau invariant và abstraction để đánh giá quan hệ **IS-A** bằng substitutability. Bao gồm overriding, access, constructor/destructor, multiple inheritance và common failure modes.

**Outcome:** nhận ra fragile base class, inappropriate hierarchy và lý do ưu tiên composition khi reuse không kéo theo subtype relationship.

### Module 1.6 — Polymorphism

Phân biệt overloading, templates và runtime polymorphism; hiểu virtual dispatch, object slicing và interface polymorphism.

**Outcome:** dự đoán method nào được gọi, hiểu cost model cơ bản và thiết kế polymorphic base an toàn.

### Checkpoint Level 1

Refactor một procedural notification module sang hai phương án:

- value-oriented solution với functions và data types;
- runtime-polymorphic solution với interface.

So sánh change scenario, testability, allocation, ownership và complexity. Mục tiêu là chứng minh OOP không tự động tốt hơn procedural design.

## 6. Phase 2 — Object Relationships

### Thứ tự học

1. Association và navigability.
2. Dependency và dependency direction.
3. HAS-A như cách nói informal.
4. Aggregation và giới hạn diễn giải của nó.
5. Composition với exclusive ownership/lifetime implication.
6. Delegation như cách phân phối behavior.
7. IS-A và substitutability.
8. Composition over Inheritance như decision framework.

### Outcome

- Vẽ object graph và ghi rõ cardinality, navigability, ownership, optionality, lifetime.
- Không suy luận ownership chỉ từ raw pointer hoặc UML diamond.
- Phân biệt “biết về”, “dùng tạm”, “sở hữu” và “ủy quyền cho” object khác.
- Chọn inheritance vì polymorphic contract; chọn composition vì capability assembly hoặc reuse.

### Checkpoint Level 2

Thiết kế `Order`, `OrderLine`, `Product`, `Customer`, `PaymentMethod`, `PricingPolicy`. Với mỗi edge, ghi:

- loại relationship;
- object nào giữ reference;
- ownership và lifetime;
- relation có bắt buộc không;
- behavior được gọi trực tiếp hay delegated;
- điều gì xảy ra khi object ở đầu kia bị hủy.

## 7. Phase 3 — Advanced OOP in C++

### Cluster 3.1 — Runtime Polymorphism

- vtable/vptr như mô hình triển khai phổ biến, không phải guarantee của standard;
- pure virtual function, abstract class và interface convention;
- virtual destructor;
- object slicing;
- clone idiom và covariant return ở mức cần thiết;
- alternatives: templates, variants, type erasure.

### Cluster 3.2 — Lifetime and Ownership

- RAII;
- ownership vocabulary;
- smart pointers;
- cyclic ownership;
- exception safety;
- dependency injection không làm mơ hồ ownership.

### Cluster 3.3 — Special Member Functions

- destructor, copy constructor, copy assignment;
- move constructor, move assignment;
- Rule of 3, Rule of 5, Rule of 0;
- self-assignment, strong/basic/no-throw guarantees;
- default/delete special members.

### Cluster 3.4 — Design Forces

- coupling: content, common, control, stamp, data/message ở mức conceptual;
- cohesion: responsibility và reason to change;
- immutability;
- Dependency Injection bằng constructor, method hoặc factory.

### Checkpoint Level 3

Xây một document processing pipeline có pluggable parser và exporter. Yêu cầu:

- ownership rõ tại mọi boundary;
- không leak, double-delete hoặc dangling reference;
- polymorphic base có destruction policy đúng;
- resource-owning type tuân Rule of 0 nếu Standard Library type đã quản lý resource;
- test fake được inject mà không đưa service locator toàn cục.

## 8. Phase 4 — Design Principles và SOLID

### Foundation principles

1. High Cohesion.
2. Low Coupling.
3. Program to an Interface.
4. Encapsulate What Varies.
5. Composition over Inheritance.
6. Dependency Inversion.

### SOLID order theo dependency học tập

1. **SRP** — responsibility, actor và reason to change.
2. **ISP** — client-specific contract và interface cohesion.
3. **DIP** — policy, detail và dependency direction.
4. **OCP** — stable abstraction và variation point.
5. **LSP** — behavioral subtype, precondition, postcondition và invariant.

Thứ tự này phục vụ học tập; acronym vẫn là S–O–L–I–D. LSP được đặt sau cùng trong learning sequence vì cần hiểu contract, inheritance và polymorphism sâu hơn.

### Phương pháp học mỗi principle

- Xác định concrete change pressure.
- Đọc bad code và dự đoán ripple effect.
- Viết characterization tests.
- Refactor từng bước.
- Đánh giá complexity mới tạo ra.
- Chỉ ra misunderstanding và overuse condition.

### Checkpoint Level 4

Refactor một checkout service đang vừa tính giá, lưu order, charge payment và gửi email. Phải đưa ra ít nhất hai phương án, trong đó có một phương án đơn giản không cần interface cho mọi class. Đánh giá theo change frequency, team boundary, test seam và operational complexity.

## 9. Phase 5 — OOP và Design Patterns

### Entry rule

Không học pattern bằng sơ đồ thuộc lòng. Mỗi pattern bắt đầu từ:

> recurring problem + context + forces + failed naive design + consequences

### Learning order

#### Behavioral trước

1. Strategy.
2. Template Method.
3. State.
4. Command.
5. Observer.
6. Chain of Responsibility.
7. Iterator.

Behavioral patterns làm rõ delegation, variation và object collaboration.

#### Creational tiếp theo

1. Factory Method.
2. Abstract Factory.
3. Builder.
4. Prototype.
5. Singleton.

Singleton được học cuối nhóm để phân tích global state, hidden dependency và test isolation trước khi nói đến use case hợp lệ.

#### Structural sau cùng

1. Adapter.
2. Facade.
3. Decorator.
4. Composite.
5. Proxy.
6. Bridge.

### Checkpoint Level 5

Cho một rules engine nhỏ, chọn tối đa hai patterns. Với mỗi pattern phải chứng minh:

- áp lực thay đổi nào tồn tại;
- naive design thất bại ra sao;
- complexity mà pattern thêm vào;
- alternative không dùng pattern;
- tín hiệu cho thấy nên gỡ pattern.

## 10. Phase 6 — Object-Oriented Analysis and Design

### Flow chính

> Requirement → Use Case → Domain Concepts → Objects → Responsibilities → Collaborations → Classes → Relationships → Design → Code → Feedback

Flow có vòng lặp; code và feedback có thể làm lộ assumption sai trong analysis.

### Topics

- Object-Oriented Analysis (**OOA**) và problem space.
- Object-Oriented Design (**OOD**) và solution space.
- Use case, actor, precondition, success path, alternate/exception path.
- Identifying objects bằng domain language và behavior.
- Responsibility assignment.
- CRC cards.
- Class, sequence và activity diagrams.
- Boundary–Control–Entity như một heuristic, không phải luật.
- Traceability từ requirement đến test và code.

### Checkpoint Level 6

Từ requirement “khách có thể đặt trước một bản sách đang được mượn”, tạo:

1. use case đầy đủ;
2. domain model;
3. responsibility map;
4. sequence diagram cho success và failure path;
5. class design;
6. core C++ implementation;
7. tests liên kết ngược với acceptance criteria.

## 11. Phase 7 — Real-world OOP Design

### Case-study sequence

| Thứ tự | Hệ thống | Design pressure chính |
|---|---|---|
| 1 | Library Management | circulation rules, identity, reservation, fines |
| 2 | Parking Lot | allocation strategy, pricing, concurrency boundary |
| 3 | Banking | invariants, money, transaction consistency, audit |
| 4 | Hotel Management | availability, reservation lifecycle, policy variation |
| 5 | E-commerce | catalog/order boundary, pricing, payment, fulfillment |
| 6 | Food Delivery | state transitions, assignment, external services, events |

### Iteration cho mỗi case study

1. Làm rõ scope và non-goals.
2. Viết functional/non-functional requirements.
3. Xây ubiquitous vocabulary nhỏ.
4. Viết use cases và acceptance criteria.
5. Nhận diện entities, value objects, services và policies.
6. Phân bổ responsibilities.
7. Mô hình hóa relationships, ownership và lifetime.
8. Vẽ sequence cho critical scenario.
9. Thiết kế classes và interfaces tối thiểu.
10. Implement core slice.
11. Áp một change request để kiểm tra extensibility.
12. Review SOLID/patterns và loại abstraction không tạo giá trị.

### Capstone

Chọn một trong sáu hệ thống và thực hiện hai iteration. Iteration 2 phải chứa change request không được biết trước ở iteration 1, nhằm kiểm tra thiết kế thay vì trình diễn một diagram tĩnh.

## 12. Phase 8 — Bridge to Software Architecture and System Design

### Mục tiêu

Không kéo object model vượt khỏi phạm vi hiệu quả của nó. Object design xử lý responsibilities và collaboration trong một boundary; architecture xử lý module/service boundary, deployment, data ownership và quality attributes ở quy mô lớn hơn.

### Topics

- package/module boundaries;
- dependency rule và layered/hexagonal architecture;
- domain model so với persistence model và transport model;
- transaction boundary;
- synchronous call so với event/message;
- process/machine boundary;
- scalability, reliability và observability như forces mới;
- khi OOP không phải abstraction tốt nhất cho data pipeline hoặc highly parallel computation.

### Exit criteria cuối lộ trình

Người học có thể:

- giải thích thiết kế từ requirement đến code;
- chỉ ra invariant, ownership, lifetime và dependency direction;
- bảo vệ hoặc bác bỏ inheritance bằng LSP;
- chọn pattern dựa trên context và consequences;
- phân biệt flexibility cần thiết với speculative abstraction;
- review code OOP của người khác bằng evidence;
- thiết kế và implement core của một hệ thống nhỏ;
- nhận biết lúc quyết định đã chuyển từ object design sang architecture/system design.

## 13. Nhịp học đề xuất

Roadmap không khóa theo số tuần, nhưng có thể dùng nhịp 16–24 tuần:

| Tỷ lệ thời gian | Hoạt động |
|---|---|
| 30% | Đọc concept và tự diễn giải |
| 25% | Trace, compile và thay đổi example code |
| 25% | Exercises, refactoring và tests |
| 15% | Case study hoặc cumulative project |
| 5% | Retrieval practice và review notes |

Sau mỗi 3–4 topics, làm một cumulative exercise. Sau mỗi level, làm checkpoint trước khi đi tiếp.

## 14. Review strategy

### Retrieval review

Sau 1, 3, 7 và 21 ngày, trả lời không nhìn tài liệu:

- concept giải quyết problem gì;
- invariant/contract nào quan trọng;
- failure mode thường gặp;
- trade-off chính;
- relation với topic trước và sau.

### Code review lens

Khi đọc code, hỏi theo thứ tự:

1. Behavior và requirement thực sự là gì?
2. Invariant nằm ở đâu và ai bảo vệ?
3. Responsibility có cohesive không?
4. Dependency nào là stable, dependency nào hay đổi?
5. Ownership và lifetime có rõ không?
6. Runtime polymorphism có cần thiết không?
7. Error path và partial failure được xử lý thế nào?
8. Test có kiểm tra contract hay khóa chặt implementation?

## 15. Những anti-goals của roadmap

- Không tối đa hóa số lượng class.
- Không coi getter/setter là encapsulation mặc định.
- Không coi hierarchy sâu là bằng chứng của reuse tốt.
- Không tạo interface chỉ để đạt “đúng SOLID”.
- Không gắn pattern name sau khi code đã phức tạp mà không phân tích problem.
- Không dùng UML như output trang trí tách rời requirement và interaction.
- Không bỏ qua memory, ownership và object lifetime trong C++.

## Summary

Learning path trọng yếu là:

> invariant và responsibility → relationships và ownership → abstraction và polymorphism → dependency management → principles → patterns → OOAD → architecture

Thứ tự này giúp mỗi abstraction xuất hiện sau khi người học đã gặp problem mà nó giải quyết, từ đó tạo design reasoning thay vì ghi nhớ định nghĩa.

