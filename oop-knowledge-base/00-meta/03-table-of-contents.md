# Table of Contents

## Overview

Mục lục này là curriculum contract cho toàn bộ knowledge base. Reading order mặc định đi theo Level 0–8, nhưng dependency thực tế được mô tả trong [Topic Dependencies](04-topic-dependencies.md).

## Meta — Learning System

1. [Learning Roadmap](01-learning-roadmap.md)
2. [Knowledge Base Structure](02-knowledge-base-structure.md)
3. [Table of Contents](03-table-of-contents.md)
4. [Topic Dependencies](04-topic-dependencies.md)
5. [Topic Template](topic-template.md)

## Level 0 — C++ Object-Model Bridge

Level 0 được tích hợp như prerequisite sections trong Level 1 và Level 3, tránh tạo một khóa C++ tổng quát ngoài phạm vi OOP.

### Object model essentials

- Object, storage và lifetime.
- Value semantics, identity và reference semantics.
- `const` correctness và state observation.
- Stack, free store và storage duration.
- Raw pointer, reference và ownership vocabulary.
- Smart-pointer overview.
- Exception, stack unwinding và destructor.
- Header boundary và physical coupling.

## Level 1 — OOP Fundamentals

### 1.1 Programming Paradigms

Planned file: `01-oop-fundamentals/01-programming-paradigms.md`

- Programming paradigm là gì?
- Procedural Programming.
- Object-Oriented Programming.
- Functional Programming.
- So sánh procedural và OOP.
- Hybrid/multi-paradigm design trong C++.
- Vì sao OOP xuất hiện?
- OOP giải quyết loại complexity nào?
- Khi OOP không phải lựa chọn phù hợp.

### 1.2 Class, Object, State, Behavior, Identity

Planned file: `01-oop-fundamentals/02-class-object-state-behavior-identity.md`

- Class như type, blueprint và abstraction boundary.
- Object và object representation.
- State.
- Behavior.
- Identity.
- Value object so với entity ở mức nhập môn.
- Responsibility và invariant.
- Class khác struct trong C++ ở đâu và không khác ở đâu.

### 1.3 Construction, Destruction và Members

Planned file: `01-oop-fundamentals/03-construction-destruction-members.md`

- Member variable.
- Member function.
- Constructor.
- Member initializer list.
- Default construction.
- Destructor.
- Construction order và destruction order.
- `this` pointer.
- Static data/member function.
- Object validity trước, trong và sau lifecycle operations.

### 1.4 Encapsulation và Invariants

Planned file: `01-oop-fundamentals/04-encapsulation-invariants.md`

- Tính đóng gói (**Encapsulation**).
- Data hiding.
- Access control: `public`, `private`, `protected`.
- Invariant và valid state.
- Command/query API.
- Getter/setter: legitimate use và anemic model risk.
- Tell, Don’t Ask ở mức heuristic.
- Representation exposure.
- Encapsulation boundary và change surface.

### 1.5 Abstraction và Interfaces

Planned file: `01-oop-fundamentals/05-abstraction-interfaces.md`

- Tính trừu tượng (**Abstraction**).
- Essential property so với implementation detail.
- Abstract concept và domain vocabulary.
- Interface như contract.
- Abstract class.
- Concrete abstraction.
- Implementation hiding.
- Abstraction leak.
- Abstraction so với Encapsulation.
- Interface so với Abstract Class.

### 1.6 Inheritance và Subtyping

Planned file: `01-oop-fundamentals/06-inheritance-subtyping.md`

- Tính kế thừa (**Inheritance**).
- Base class và derived class.
- Code inheritance so với interface inheritance.
- Public, protected và private inheritance trong C++.
- IS-A và substitutability.
- Method overriding.
- Name hiding.
- Constructor/destructor order trong hierarchy.
- Multiple inheritance.
- Diamond problem và virtual inheritance.
- Object slicing.
- Fragile Base Class Problem.
- Hierarchy depth và evolution cost.

### 1.7 Polymorphism và Dynamic Dispatch

Planned file: `01-oop-fundamentals/07-polymorphism-dynamic-dispatch.md`

- Tính đa hình (**Polymorphism**).
- Ad hoc, parametric và subtype polymorphism.
- Compile-time polymorphism.
- Runtime polymorphism.
- Function overloading.
- Function overriding.
- Virtual function.
- Pure virtual function.
- Dynamic dispatch.
- Static type so với dynamic type.
- Interface polymorphism.
- Cost model cơ bản.
- Overloading so với overriding.

## Level 2 — Object Relationships

### 2.1 Relationship Modeling

Planned file: `02-object-relationships/01-relationship-modeling.md`

- Object graph so với class diagram.
- Role, multiplicity, navigability, optionality.
- Ownership và lifetime như dimension riêng.
- Quan hệ domain so với representation trong code.
- IS-A và HAS-A như shorthand, cùng giới hạn của chúng.

### 2.2 Association và Dependency

Planned file: `02-object-relationships/02-association-dependency.md`

- Association.
- Unidirectional/bidirectional association.
- Dependency.
- Parameter/local dependency.
- Physical/header dependency.
- Association so với Dependency.
- Hidden dependency và global access.

### 2.3 Aggregation và Composition

Planned file: `02-object-relationships/03-aggregation-composition.md`

- Aggregation.
- Composition.
- Whole–part semantics.
- Shared so với exclusive ownership.
- Lifetime dependency.
- UML notation và ambiguity của aggregation.
- Representation bằng value, smart pointer và reference.
- Aggregation so với Composition.

### 2.4 Delegation

Planned file: `02-object-relationships/04-delegation.md`

- Delegation là gì?
- Delegation so với forwarding.
- Responsibility distribution.
- Policy object.
- Delegation và runtime polymorphism.
- Cost: indirection, navigation và debugging.

### 2.5 Composition over Inheritance

Planned file: `02-object-relationships/05-composition-over-inheritance.md`

- Reuse so với subtype.
- Capability assembly.
- Variation by composed policy.
- Strategy-based composition.
- Inheritance hợp lệ khi contract/substitutability bền vững.
- Decision matrix: composition, inheritance, templates, free functions.
- Migration từ hierarchy cứng sang composed design.

## Level 3 — Advanced OOP in C++

### 3.1 Virtual Functions và Abstract Classes

Planned file: `03-advanced-oop-cpp/01-virtual-functions-abstract-classes.md`

- Virtual dispatch semantics.
- vtable/vptr như implementation model phổ biến.
- Pure virtual function.
- Abstract base class.
- Interface convention trong C++.
- Virtual call trong constructor/destructor.
- `override`, `final` và name hiding.

### 3.2 Runtime Polymorphism Safety

Planned file: `03-advanced-oop-cpp/02-runtime-polymorphism-safety.md`

- Virtual destructor.
- Destruction through base pointer.
- Object slicing.
- Clone idiom.
- Covariant return.
- Downcasting và design smell.
- RTTI, `dynamic_cast` và alternatives.
- Runtime polymorphism so với templates, `std::variant`, type erasure.

### 3.3 Coupling và Cohesion

Planned file: `03-advanced-oop-cpp/03-coupling-cohesion.md`

- Coupling.
- Cohesion.
- Afferent/efferent dependency ở mức module bridge.
- Temporal, logical và functional cohesion.
- Data/control/stamp coupling.
- Coupling so với Cohesion.
- Connascence ở mức mở rộng.

### 3.4 Dependency Injection

Planned file: `03-advanced-oop-cpp/04-dependency-injection.md`

- Dependency Injection (**DI**).
- Constructor injection.
- Method injection.
- Factory/provider injection.
- Dependency ownership so với dependency access.
- DI container so với manual injection.
- Service Locator anti-pattern.
- Test seams và fakes.

### 3.5 Immutability và Value Semantics

Planned file: `03-advanced-oop-cpp/05-immutability-value-semantics.md`

- Immutable object.
- Logical so với bitwise constness.
- Value semantics.
- Entity identity.
- Copy-on-write caveats.
- Thread-safety benefit và update cost.

### 3.6 Object Lifetime và Ownership

Planned file: `03-advanced-oop-cpp/06-object-lifetime-ownership.md`

- Storage duration so với lifetime.
- Ownership, borrowing và observation.
- Dangling reference/pointer.
- Double deletion và leak.
- Aliasing.
- Lifetime trong object graph.
- Non-owning API conventions.

### 3.7 RAII và Smart Pointers

Planned file: `03-advanced-oop-cpp/07-raii-smart-pointers.md`

- Resource Acquisition Is Initialization (**RAII**).
- Deterministic destruction.
- `std::unique_ptr`.
- `std::shared_ptr`.
- `std::weak_ptr`.
- Custom deleter.
- Cyclic ownership.
- Exception safety.
- Vì sao smart pointer không tự quyết định design ownership.

### 3.8 Copy Semantics và Rule of Three

Planned file: `03-advanced-oop-cpp/08-copy-semantics-rule-of-three.md`

- Copy constructor.
- Copy assignment.
- Destructor.
- Shallow/deep copy.
- Self-assignment.
- Rule of 3.
- Copy-and-swap.
- Copyability như domain decision.

### 3.9 Move Semantics và Rule of Five

Planned file: `03-advanced-oop-cpp/09-move-semantics-rule-of-five.md`

- lvalue/rvalue bridge.
- Move constructor.
- Move assignment.
- Valid but unspecified state.
- `noexcept` và containers.
- Rule of 5.
- Defaulted/deleted special members.

### 3.10 Rule of Zero và Exception Safety

Planned file: `03-advanced-oop-cpp/10-rule-of-zero-exception-safety.md`

- Rule of 0.
- Resource-owning member types.
- Basic, strong và no-throw guarantees.
- Transactional update.
- Pimpl như dependency/ABI technique và trade-offs.
- Khi vẫn cần custom special member functions.

## Level 4 — OOP Design Principles và SOLID

### 4.1 High Cohesion, Low Coupling

Planned file: `04-design-principles-solid/01-high-cohesion-low-coupling.md`

- Responsibility cluster.
- Change locality.
- Dependency surface.
- Tension giữa cohesion và coupling.
- Measurement limits.

### 4.2 Program to an Interface

Planned file: `04-design-principles-solid/02-program-to-interface.md`

- Depend on capability/contract.
- Concrete type vẫn có thể là stable interface.
- Abstraction cost.
- Testability và substitution.
- Interface ownership bởi client.

### 4.3 Encapsulate What Varies

Planned file: `04-design-principles-solid/03-encapsulate-what-varies.md`

- Variation point.
- Stable core và volatile detail.
- Policy extraction.
- Premature abstraction.
- Change-frequency evidence.

### 4.4 Composition over Inheritance — Design Principle

Planned file: `04-design-principles-solid/04-composition-over-inheritance.md`

- Principle recap từ Level 2.
- Replaceable behavior.
- Delegation boundary.
- Runtime so với compile-time composition.
- Cases inheritance rõ ràng hơn composition.

### 4.5 SOLID Overview

Planned file: `04-design-principles-solid/05-solid-overview.md`

- Nguồn gốc và mục tiêu của SOLID.
- SOLID là heuristic về change và dependency.
- Quan hệ giữa năm principles.
- Dấu hiệu over-engineering.
- Cách review SOLID bằng scenarios.

### 4.6 Single Responsibility Principle

Planned file: `04-design-principles-solid/06-srp.md`

- Definition.
- Actor và reason to change.
- Cohesion connection.
- Bad code và ripple effects.
- Refactoring by responsibility.
- “Một class chỉ có một method” misconception.
- Over-fragmentation.

### 4.7 Open/Closed Principle

Planned file: `04-design-principles-solid/07-ocp.md`

- Definition.
- Closed relative to a chosen variation.
- Stable abstraction.
- Plugin/policy examples.
- Speculative extensibility.
- Modification vẫn cần thiết trong maintenance.

### 4.8 Liskov Substitution Principle

Planned file: `04-design-principles-solid/08-lsp.md`

- Behavioral subtyping.
- Preconditions, postconditions và invariants.
- History constraint.
- Rectangle/Square analysis và giới hạn của ví dụ.
- Exception behavior và mutability.
- LSP ngoài inheritance syntax.

### 4.9 Interface Segregation Principle

Planned file: `04-design-principles-solid/09-isp.md`

- Client-specific interfaces.
- Interface cohesion.
- Role interface.
- Fat interface failure modes.
- Interface explosion.
- Versioning and capability discovery trade-offs.

### 4.10 Dependency Inversion Principle

Planned file: `04-design-principles-solid/10-dip.md`

- High-level policy và low-level detail.
- Source-code dependency direction.
- Ownership of abstraction.
- DIP so với DI.
- Plugin boundary.
- When direct dependency is simpler and safer.

## Level 5 — OOP và Design Patterns

### 5.0 Pattern Language và Selection

Planned file: `05-design-patterns/00-pattern-language-and-selection.md`

- Pattern là gì và không phải là gì.
- Context, problem, forces, solution, consequences.
- Pattern so với principle, library và algorithm.
- Pattern selection workflow.
- Pattern compound và pattern interaction.
- Refactoring to/from patterns.
- Pattern overuse.

### Creational Patterns

#### 5.1 Factory Method

Planned file: `05-design-patterns/creational/01-factory-method.md`

- Creation variation trong inheritance hierarchy.
- Creator, Product, ConcreteCreator, ConcreteProduct.
- Factory Method so với simple factory.
- Virtual constructor idiom.

#### 5.2 Abstract Factory

Planned file: `05-design-patterns/creational/02-abstract-factory.md`

- Family of related products.
- Product compatibility.
- Abstract Factory so với Factory Method và Builder.
- Product-family expansion trade-off.

#### 5.3 Builder

Planned file: `05-design-patterns/creational/03-builder.md`

- Complex construction process.
- Stepwise construction và invariant timing.
- Fluent builder.
- Builder so với named parameters/aggregate initialization/factory.

#### 5.4 Prototype

Planned file: `05-design-patterns/creational/04-prototype.md`

- Creation by cloning.
- Deep copy và polymorphic clone.
- Prototype registry.
- Copy semantics risk.

#### 5.5 Singleton

Planned file: `05-design-patterns/creational/05-singleton.md`

- Single-instance constraint so với global access.
- Initialization order và thread safety.
- Hidden dependency/test isolation.
- Process boundary và distributed reality.
- Alternatives: ownership root, DI, module-local instance.

### Structural Patterns

#### 5.6 Adapter

Planned file: `05-design-patterns/structural/01-adapter.md`

- Incompatible interfaces.
- Object adapter so với class adapter.
- Semantic adaptation so với signature conversion.

#### 5.7 Decorator

Planned file: `05-design-patterns/structural/02-decorator.md`

- Add behavior without subclass explosion.
- Transparent wrapping.
- Ordering and identity issues.
- Decorator so với Proxy và middleware chain.

#### 5.8 Facade

Planned file: `05-design-patterns/structural/03-facade.md`

- Simplified subsystem boundary.
- Facade as dependency firewall.
- God facade risk.
- Facade so với Adapter.

#### 5.9 Composite

Planned file: `05-design-patterns/structural/04-composite.md`

- Uniform treatment of leaf and composite.
- Tree ownership.
- Transparent so với safe interface.
- Recursive operations.

#### 5.10 Proxy

Planned file: `05-design-patterns/structural/05-proxy.md`

- Access control, lazy loading, remote và caching proxy.
- Lifecycle/latency/error semantics.
- Proxy so với Decorator.

#### 5.11 Bridge

Planned file: `05-design-patterns/structural/06-bridge.md`

- Two independently varying dimensions.
- Abstraction/Implementor split.
- Bridge so với Strategy và Adapter.

### Behavioral Patterns

#### 5.12 Strategy

Planned file: `05-design-patterns/behavioral/01-strategy.md`

- Interchangeable algorithms/policies.
- Runtime, compile-time và function-object strategies.
- Context ownership.
- Strategy so với State.

#### 5.13 Observer

Planned file: `05-design-patterns/behavioral/02-observer.md`

- One-to-many notification.
- Subscription lifetime.
- Reentrancy, ordering, failure isolation.
- Push/pull model.
- Observer so với event bus.

#### 5.14 Command

Planned file: `05-design-patterns/behavioral/03-command.md`

- Request as object.
- Queueing, logging, retry và undo.
- Receiver lifetime.
- Command so với Strategy.

#### 5.15 State

Planned file: `05-design-patterns/behavioral/04-state.md`

- Behavior by internal state.
- Transition ownership.
- State object lifecycle.
- State pattern so với finite state machine table.

#### 5.16 Template Method

Planned file: `05-design-patterns/behavioral/05-template-method.md`

- Algorithm skeleton with overridable steps.
- Hook methods.
- Hollywood Principle.
- Template Method so với Strategy.

#### 5.17 Chain of Responsibility

Planned file: `05-design-patterns/behavioral/06-chain-of-responsibility.md`

- Dynamic handler chain.
- First-handler so với all-handler semantics.
- Ordering, observability và error handling.

#### 5.18 Iterator

Planned file: `05-design-patterns/behavioral/07-iterator.md`

- Traversal without representation exposure.
- External/internal iterator.
- Iterator invalidation và ranges connection.

## Level 6 — OOP và OOAD

### 6.1 OOA so với OOD

Planned file: `06-ooad/01-ooa-vs-ood.md`

- Object-Oriented Analysis.
- Object-Oriented Design.
- Problem space so với solution space.
- Analysis model không đồng nhất với code class.

### 6.2 Requirements và Use Cases

Planned file: `06-ooad/02-requirements-and-use-cases.md`

- Functional/non-functional requirement.
- Actor và system boundary.
- Use Case.
- Preconditions, trigger, success guarantee.
- Main, alternate và exception flows.
- Acceptance criteria.

### 6.3 Identifying Objects

Planned file: `06-ooad/03-identifying-objects.md`

- Noun extraction và giới hạn.
- Domain concept, entity, value object, service, policy.
- Identity và lifecycle.
- Avoiding database-table-first modeling.

### 6.4 Identifying và Assigning Responsibilities

Planned file: `06-ooad/04-responsibility-assignment.md`

- Knowing/doing responsibilities.
- Information Expert.
- Creator, Controller và Low Coupling heuristics.
- CRC cards.
- God Object và anemic model trade-offs.

### 6.5 Relationships và Domain Model

Planned file: `06-ooad/05-relationships-and-domain-model.md`

- Conceptual relationship.
- Multiplicity và constraint.
- Domain model so với design class model.
- Ownership decisions deferred to design khi cần.

### 6.6 Class Diagram

Planned file: `06-ooad/06-class-diagrams.md`

- Conceptual, specification và implementation perspective.
- Attribute, operation và visibility.
- Association, generalization, dependency, composition.
- Diagram scope và readability.

### 6.7 Sequence Diagram

Planned file: `06-ooad/07-sequence-diagrams.md`

- Lifeline, activation và message.
- Sync/async message.
- Alternative, loop và error fragment.
- Responsibility discovery từ interaction.

### 6.8 Activity Diagram

Planned file: `06-ooad/08-activity-diagrams.md`

- Action, control flow và decision.
- Fork/join.
- Swimlane.
- Activity diagram so với sequence/state diagram.

### 6.9 Requirement-to-Code Workflow

Planned file: `06-ooad/09-requirement-to-code-workflow.md`

- Requirement → Use Case → Objects → Classes → Relationships → Design → Code.
- Traceability matrix.
- Feedback loop từ code/test về analysis.
- Vertical slice.
- Review checklist.

## Level 7 — Real-world OOP Design

Mỗi case study dùng cùng mười trục phân tích: requirements, entities, responsibilities, relationships, class design, OOP principles, SOLID, suitable patterns, core implementation và trade-offs.

### 7.1 Library Management System

Planned directory: `07-case-studies/01-library-management/`

- Book title so với physical copy.
- Member, loan, reservation và fine.
- Circulation policies.
- Availability và reservation queue.
- Notification boundary.

### 7.2 Banking System

Planned directory: `07-case-studies/02-banking/`

- Money value object.
- Account invariant.
- Transaction, transfer và ledger boundary.
- Overdraft/fee policy.
- Idempotency, audit và consistency discussion.

### 7.3 Hotel Management System

Planned directory: `07-case-studies/03-hotel-management/`

- Room type so với room inventory.
- Rate plan và availability.
- Reservation lifecycle.
- Check-in/check-out.
- Cancellation policy.

### 7.4 E-commerce System

Planned directory: `07-case-studies/04-ecommerce/`

- Product/catalog/inventory boundary.
- Cart và price snapshot.
- Order lifecycle.
- Payment và fulfillment.
- Promotion/tax/shipping policies.

### 7.5 Parking Lot System

Planned directory: `07-case-studies/05-parking-lot/`

- Vehicle, spot và ticket.
- Spot allocation strategy.
- Pricing strategy.
- Entry/exit workflow.
- Capacity, concurrency và hardware boundary.

### 7.6 Food Delivery System

Planned directory: `07-case-studies/06-food-delivery/`

- Restaurant/menu/order boundary.
- Courier assignment.
- Order and delivery state machine.
- Location/payment/notification integrations.
- Events, retries và eventual consistency discussion.

## Level 8 — Architecture Bridge

### 8.1 Object to Module Boundaries

Planned file: `08-architecture-bridge/01-object-to-module-boundaries.md`

- Class coupling so với module coupling.
- Public API và information hiding cấp module.
- Package by layer so với feature.
- Stable Dependencies direction.

### 8.2 Layered và Hexagonal Architecture

Planned file: `08-architecture-bridge/02-layered-and-hexagonal.md`

- Layer responsibilities.
- Ports and adapters.
- Domain/application/infrastructure boundaries.
- Dependency rule.
- When architecture ceremony exceeds system needs.

### 8.3 Domain, Persistence và Transport Models

Planned file: `08-architecture-bridge/03-domain-persistence-transport-models.md`

- Domain model.
- ORM/persistence model.
- DTO/transport model.
- Mapping cost so với coupling cost.
- Transaction boundary.

### 8.4 Limits of OOP và System Design Bridge

Planned file: `08-architecture-bridge/04-oop-limits-and-system-design.md`

- Object boundary so với process/network boundary.
- Latency, partial failure và serialization.
- Concurrency và shared mutable state.
- Data-oriented/functional alternatives.
- Scalability, reliability và observability.
- Chuyển từ method call reasoning sang distributed interaction reasoning.

## Glossary và Comparison Index

### English–Vietnamese Glossary

Planned file: `glossary/glossary-en-vi.md`

Canonical translation, short definition, first source topic và disambiguation notes.

### Concept Comparison Index

Planned file: `glossary/concept-comparison-index.md`

- Abstraction / Encapsulation.
- Inheritance / Composition.
- Overloading / Overriding.
- Aggregation / Composition.
- Interface / Abstract Class.
- Coupling / Cohesion.
- Association / Dependency.
- Compile-time / Runtime Polymorphism.
- Value / Reference Semantics.
- Copy / Move.
- OOA / OOD.
- Design Principle / Design Pattern.

## Coverage audit

| Requested area | Covered in |
|---|---|
| Paradigms và OOP motivation | Level 1.1 |
| Class/Object/State/Behavior/Identity/Lifecycle | Level 1.2–1.3, Level 3.6 |
| Encapsulation/Abstraction/Inheritance/Polymorphism | Level 1.4–1.7 |
| Object relationships và Composition over Inheritance | Level 2 |
| Advanced C++ memory/lifetime/copy/move | Level 3 |
| Design principles và SOLID | Level 4 |
| 18 requested Design Patterns | Level 5 |
| OOA/OOD/Use Case/UML/workflow | Level 6 |
| 6 real-world systems | Level 7 |
| OOP → Architecture → System Design | Level 8 |
| Easy-to-confuse concepts | Glossary/Comparison Index và source topics |

## Summary

Mục lục bao phủ toàn bộ learning goal, nhưng giữ ranh giới rõ: fundamentals tạo mental model; advanced C++ bảo đảm semantic/lifetime safety; principles và patterns quản lý change; OOAD nối requirement với code; case studies kiểm chứng; architecture bridge chỉ ra giới hạn của object-level reasoning.

