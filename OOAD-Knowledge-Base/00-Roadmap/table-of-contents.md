# Complete Table of Contents

Mục lục này là curriculum contract. Checkbox thể hiện trạng thái lesson, không phải mức độ quan trọng. Giai đoạn hiện tại chỉ hoàn thành nhóm `00-Roadmap`.

## 00 — Roadmap & Navigation

- [x] [Learning Roadmap](roadmap.md)
- [x] [Knowledge Architecture](knowledge-architecture.md)
- [x] [Dependency Map](dependency-map.md)
- [x] [Complete Table of Contents](table-of-contents.md)
- [x] [Study Order](study-order.md)
- [x] [Glossary: Vietnamese explanation + canonical English terms](glossary.md)

## 01 — Software Requirements Engineering ✅

**Trạng thái:** hoàn thành 9/9 lessons. Mỗi lesson có worked example, common mistakes, artifact checklist, bốn cấp bài tập và exam preparation.

### 01.1 Requirements Engineering Fundamentals

- [x] Requirement là gì?
- [x] Software requirement và các abstraction level
- [x] Requirements Engineering lifecycle
- [x] Vì sao requirement defect có chi phí lan truyền
- [x] Ambiguity, conflict và volatility
- [x] Baseline, version và change
- [x] Example: “giao đồ ăn nhanh” từ vague goal thành analyzable problem
- [x] Exercises và exam preparation

**Lesson:** [requirements-engineering.md](../01-Requirements/requirements-engineering.md)

### 01.2 Stakeholders

- [x] Stakeholder, user, customer và actor
- [x] Business owner, administrator, developer, tester, regulator
- [x] External system như một stakeholder/interaction party tùy ngữ cảnh
- [x] Identify → Classify → Analyze → Prioritize
- [x] Influence/interest, authority, impact và communication need
- [x] Stakeholder conflict và missing stakeholder
- [x] Worked example + stakeholder map
- [x] Exercises và exam preparation

**Lesson:** [stakeholders.md](../01-Requirements/stakeholders.md)

### 01.3 Functional Requirements

- [x] Business functionality
- [x] User interaction và observable system behavior
- [x] Input, output và response
- [x] Business rule so với functional requirement
- [x] Trigger–response và conditional behavior
- [x] Functional decomposition without UI bias
- [x] Bad/good requirement rewriting
- [x] Exercises và exam preparation

**Lesson:** [functional-requirements.md](../01-Requirements/functional-requirements.md)

### 01.4 Non-functional / Quality Requirements

- [x] Quality requirement, constraint và global concern
- [x] Performance, security, reliability, availability
- [x] Scalability, maintainability, usability
- [x] Portability và compatibility
- [x] Measurable quality scenario
- [x] Trade-offs giữa quality attributes
- [x] Khi “NFR” thực sự tạo functional behavior
- [x] Exercises và exam preparation

**Lesson:** [non-functional-requirements.md](../01-Requirements/non-functional-requirements.md)

### 01.5 Requirement Elicitation

Với mỗi kỹ thuật: What, How, Advantages, Disadvantages, When to use, Example.

- [x] Interview
- [x] Questionnaire
- [x] Observation/contextual inquiry
- [x] Workshop
- [x] Brainstorming
- [x] Document analysis
- [x] Focus group
- [x] Prototyping
- [x] Chọn phối hợp kỹ thuật theo risk/uncertainty
- [x] Elicitation plan, question design và note validation
- [x] Exercises và exam preparation

**Lesson:** [elicitation.md](../01-Requirements/elicitation.md)

### 01.6 Requirement Analysis

- [x] Classification và organization
- [x] Dependency và impact
- [x] Conflict và negotiation
- [x] Feasibility
- [x] Consistency và completeness
- [x] Verifiability/testability
- [x] Detect ambiguity, omission, contradiction và over-specification
- [x] Modeling hỗ trợ analysis
- [x] Exercises và exam preparation

**Lesson:** [analysis.md](../01-Requirements/analysis.md)

### 01.7 Requirement Prioritization

- [x] Value, risk, urgency, dependency và cost
- [x] MoSCoW: Must, Should, Could, Won't this time
- [x] Điều kiện để Must thực sự có nghĩa
- [x] Priority vs sequence vs release scope
- [x] Negotiation khi mọi thứ đều là Must
- [x] Full example cho một release
- [x] Exercises và exam preparation

**Lesson:** [prioritization.md](../01-Requirements/prioritization.md)

### 01.8 User Stories

- [x] User Story và conversational intent
- [x] Format: As a / I want / So that
- [x] Acceptance Criteria
- [x] INVEST
- [x] Story splitting và common smells
- [x] Requirement vs User Story vs Use Case
- [x] Khi user story không phù hợp
- [x] Exercises và exam preparation

**Lesson:** [user-stories.md](../01-Requirements/user-stories.md)

### 01.9 Requirement Validation Overview

- [x] Verification vs validation
- [x] Review, walkthrough và inspection
- [x] Prototype/model/test-based validation
- [x] Requirement checklist
- [x] Validation evidence và unresolved issue
- [x] Exercises và exam preparation

**Lesson:** [validation.md](../01-Requirements/validation.md)

## 02 — Software Requirements Specification

### 02.1 SRS

- [ ] SRS: purpose, audience và lifecycle
- [ ] SRS vs Requirement Document vs Product Backlog
- [ ] Cấu trúc document-centric phổ biến
- [ ] Cấu trúc use-case/feature-centric và agile repository
- [ ] Introduction và overall description
- [ ] Functional and quality requirements
- [ ] External interface requirements
- [ ] Constraints, business rules, acceptance criteria, appendices
- [ ] Tailoring theo project context
- [ ] Complete mini-SRS example
- [ ] Exercises và exam preparation

**File dự kiến:** `02-SRS/srs.md`

### 02.2 Quality of Good Requirements

- [ ] Correct
- [ ] Unambiguous
- [ ] Complete
- [ ] Consistent
- [ ] Verifiable
- [ ] Feasible
- [ ] Traceable
- [ ] Prioritized
- [ ] Modifiable
- [ ] Atomicity và necessity như quality checks bổ sung
- [ ] “System should be fast” — diagnosis và rewrite
- [ ] Quality checklist + review practice
- [ ] Exercises và exam preparation

**File dự kiến:** `02-SRS/requirement-quality.md`

### 02.3 Requirement Traceability

- [ ] Why traceability; cost và granularity
- [ ] Forward, backward, bidirectional traceability
- [ ] Requirement → Use Case → Design → Code → Test
- [ ] RTM columns và example
- [ ] Coverage, orphan artifact và gold-plating detection
- [ ] Change impact analysis
- [ ] Traceability trong document, issue tracker và repository
- [ ] Exercises và exam preparation

**File dự kiến:** `02-SRS/traceability.md`

## 03 — Use Cases

### 03.1 Use Case Fundamentals

- [ ] Use case, actor, goal và system boundary
- [ ] Primary/secondary/supporting actor
- [ ] Trigger
- [ ] Preconditions và minimal/success guarantees
- [ ] Main Success Scenario
- [ ] Alternative và Exception Flow
- [ ] Use case vs business process vs UI flow
- [ ] Goal level và use-case granularity
- [ ] Exercises và exam preparation

**File dự kiến:** `03-Use-Cases/use-case.md`

### 03.2 Use Case Identification

- [ ] Identify stakeholders
- [ ] Identify actors
- [ ] Identify actor goals
- [ ] Convert goals into candidate use cases
- [ ] Define/recheck boundary
- [ ] Validate name, value, granularity và coverage
- [ ] CRUD trap, screen-as-use-case và internal-step trap
- [ ] Worked example
- [ ] Exercises và exam preparation

**File dự kiến:** `03-Use-Cases/use-case-identification.md`

### 03.3 Use Case Diagram

- [ ] Actor, use case, association và boundary
- [ ] `<<include>>`
- [ ] `<<extend>>`
- [ ] Actor/use-case generalization
- [ ] Include vs extend: decision guide
- [ ] Khi không nên dùng include/extend
- [ ] Diagram readability và level consistency
- [ ] Mermaid/PlantUML example
- [ ] Common mistakes
- [ ] Exercises và exam preparation

**File dự kiến:** `03-Use-Cases/use-case-diagram.md`

### 03.4 Use Case Specification

- [ ] Template và field semantics
- [ ] Viết actor intent/system responsibility
- [ ] Numbering alternative flows
- [ ] Business rules và special requirements
- [ ] Example 1: Borrow Book
- [ ] Example 2: Place Food Order
- [ ] Review checklist
- [ ] Derive acceptance criteria và analysis input
- [ ] Exercises và exam preparation

**File dự kiến:** `03-Use-Cases/use-case-specification.md`

## 04 — Object-Oriented Analysis

### 04.1 OOAD Foundations

- [ ] Object-Oriented Analysis
- [ ] Object-Oriented Design
- [ ] OOAD vs OOP
- [ ] Analysis vs design
- [ ] Pipeline từ requirements tới implementation
- [ ] Iteration và feedback
- [ ] Common premature-design mistakes
- [ ] Exercises và exam preparation

**File dự kiến:** `04-OO-Analysis/oo-analysis.md`

### 04.2 Domain Modeling

- [ ] Domain, subdomain và bounded vocabulary
- [ ] Domain concept
- [ ] Entity và identity
- [ ] Value Object và value equality
- [ ] Attribute, relationship và responsibility
- [ ] Association, multiplicity và invariant
- [ ] Requirement → Nouns → Candidates → Filter → Domain Model
- [ ] Vì sao không phải noun nào cũng là class
- [ ] Worked domain model
- [ ] Exercises và exam preparation

**File dự kiến:** `04-OO-Analysis/domain-model.md`

### 04.3 Identifying Classes

- [ ] Noun analysis
- [ ] Verb analysis
- [ ] Responsibility analysis
- [ ] Candidate classification và elimination
- [ ] Entity–Boundary–Control
- [ ] Khi BCE hữu ích và khi gây ceremony
- [ ] From analysis concept to software class: non-1:1 mapping
- [ ] Exercises và exam preparation

**File dự kiến:** `04-OO-Analysis/identifying-classes.md`

### 04.4 CRC Cards

- [ ] Class, Responsibilities, Collaborators
- [ ] Responsibility wording
- [ ] Role-play một scenario bằng CRC
- [ ] Detect low cohesion/high coupling
- [ ] CRC vs class diagram vs sequence diagram
- [ ] Complete worked example
- [ ] Exercises và exam preparation

**File dự kiến:** `04-OO-Analysis/crc-cards.md`

## 05 — UML as a Modeling Language

### 05.0 UML Overview

- [ ] UML là language, không phải development process
- [ ] Structural, behavioral và interaction diagrams
- [ ] Model, view, diagram và abstraction level
- [ ] Chọn diagram theo question/audience
- [ ] Sketch vs specification vs documentation
- [ ] Consistency across views

**File dự kiến:** `05-UML/uml-overview.md`

Mỗi diagram dưới đây gồm: Purpose, When to use, Main elements, Relationships, Example, Common mistakes, Derivation from requirements.

### 05.1 Class Diagram

- [ ] Class, attribute, operation và visibility
- [ ] Association, aggregation, composition
- [ ] Generalization và dependency
- [ ] Multiplicity và navigability
- [ ] Association vs aggregation vs composition
- [ ] Domain class diagram vs design class diagram

**File dự kiến:** `05-UML/class-diagram.md`

### 05.2 Sequence Diagram

- [ ] Lifeline, actor/object/participant
- [ ] Synchronous/asynchronous message, return, activation
- [ ] `alt`, `opt`, `loop`, `par`
- [ ] Use Case → Scenario → Sequence Diagram
- [ ] System Sequence vs Design Sequence
- [ ] Complete example + consistency checks

**File dự kiến:** `05-UML/sequence-diagram.md`

### 05.3 Activity Diagram

- [ ] Action, control flow, object flow
- [ ] Initial/final, decision/merge, fork/join
- [ ] Guard và swimlane
- [ ] Workflow vs use-case flow
- [ ] Parallelism semantics

**File dự kiến:** `05-UML/activity-diagram.md`

### 05.4 State Machine Diagram

- [ ] State, event, transition, guard, action
- [ ] Entry/exit/do behavior
- [ ] Initial/final/composite state
- [ ] Derive lifecycle từ rule và scenario
- [ ] State machine vs activity diagram
- [ ] Khi object không cần state machine

**File dự kiến:** `05-UML/state-machine.md`

### 05.5 Package Diagram

- [ ] Package, namespace và grouping
- [ ] Dependency, import, access, merge ở mức phù hợp
- [ ] Layer/package dependency direction
- [ ] Cyclic dependency detection
- [ ] Package diagram vs component diagram

**File dự kiến:** `05-UML/package-diagram.md`

### 05.6 Component Diagram

- [ ] Component, provided/required interface, port
- [ ] Connector và dependency
- [ ] Logical component vs deployable unit
- [ ] Derive component boundaries từ responsibility/quality
- [ ] Component diagram vs class/package diagram

**File dự kiến:** `05-UML/component-diagram.md`

### 05.7 Deployment Diagram

- [ ] Node, device, execution environment, artifact
- [ ] Communication path và deployment relation
- [ ] Map runtime topology
- [ ] Relate availability, security, scalability và latency
- [ ] Logical vs physical deployment view

**File dự kiến:** `05-UML/deployment-diagram.md`

## 06 — Object-Oriented Design

### 06.1 Analysis Model vs Design Model

- [ ] What problem vs how to implement
- [ ] Domain concept vs software class
- [ ] Technology-independent vs technology-aware
- [ ] Mapping is not 1:1
- [ ] Add controllers, repositories, gateways và DTOs có rationale
- [ ] Example transformation
- [ ] Exercises và exam preparation

**File dự kiến:** `06-OO-Design/analysis-vs-design.md`

### 06.2 Responsibility Assignment Foundations

- [ ] Responsibility types: knowing vs doing
- [ ] Behavior, information và invariant ownership
- [ ] Coupling, cohesion và change propagation
- [ ] From scenario messages to operations
- [ ] Responsibility-driven design workflow

**File dự kiến:** `06-OO-Design/responsibility-assignment.md`

### 06.3 GRASP

- [ ] Information Expert
- [ ] Creator
- [ ] Controller
- [ ] Low Coupling
- [ ] High Cohesion
- [ ] Polymorphism
- [ ] Indirection
- [ ] Pure Fabrication
- [ ] Protected Variations
- [ ] Trade-offs giữa các pattern
- [ ] Complete responsibility-assignment example
- [ ] Exercises và exam preparation

**File dự kiến:** `06-OO-Design/grasp.md`

### 06.4 SOLID in OOAD

- [ ] SRP: reason to change và cohesion
- [ ] OCP: stable abstraction around actual variation
- [ ] LSP: behavioral substitutability
- [ ] ISP: client-specific contracts
- [ ] DIP: dependency policy and direction
- [ ] Requirement → Design problem → Principle → Refactoring
- [ ] Principle interaction và trade-offs
- [ ] Over-abstraction/common misuse
- [ ] Exercises và exam preparation

**File dự kiến:** `06-OO-Design/solid.md`

### 06.5 Design Patterns

- [ ] OO Problem → Principle → Pattern
- [ ] Pattern intent, context, forces, structure, consequence
- [ ] Factory
- [ ] Strategy
- [ ] Observer
- [ ] Adapter
- [ ] Decorator
- [ ] Facade
- [ ] Command
- [ ] State
- [ ] Pattern combinations và alternatives
- [ ] When not to use a pattern
- [ ] Refactoring examples
- [ ] Exercises và exam preparation

**File dự kiến:** `06-OO-Design/design-patterns.md`

## 07 — Integrated Case Studies

### 07.0 Common Method

- [ ] Problem framing và scope
- [ ] Artifact pipeline và ID convention
- [ ] Assumption/decision log
- [ ] Traceability and consistency review
- [ ] Implementation vertical slice
- [ ] Testing/acceptance evidence

**File dự kiến:** `07-Case-Studies/case-study-method.md`

Mỗi case study đi qua:

```text
Business Requirements → Stakeholders → FR/QR → Actors → Use Cases
→ Use Case Specification → Domain Model → Class/Sequence/Activity Model
→ Design Principles → Patterns when justified → Implementation → Tests/AC
```

### 07.1 Library Management System

- [ ] Borrow/return/reservation/fine scope
- [ ] Worked example ưu tiên clarity

### 07.2 Hotel Management System

- [ ] Search, reservation, date inventory, check-in/out, cancellation
- [ ] Overlap và availability rules

### 07.3 E-commerce System

- [ ] Catalog, cart, checkout, payment, fulfillment, return
- [ ] Promotions và external integrations

### 07.4 Banking System

- [ ] Account transfer lifecycle
- [ ] Authentication, authorization, consistency, auditability

### 07.5 University Management System

- [ ] Course offering, enrollment, prerequisite, capacity, grading
- [ ] Rule-heavy domain modeling

### 07.6 Food Delivery System

- [ ] Quote, order, payment, restaurant acceptance, courier, delivery, cancel/refund
- [ ] Capstone end-to-end

**Files dự kiến:** `07-Case-Studies/library.md`, `hotel.md`, `ecommerce.md`, `banking.md`, `university.md`, `food-delivery.md`

## 08 — Exercises

### Basic

- [ ] Concept checks và explain-in-your-own-words
- [ ] Classification và short comparison

### Intermediate

- [ ] Diagnose bad requirements
- [ ] Identify actors/goals/use cases
- [ ] Complete or correct partial models

### Advanced

- [ ] Negotiate requirement conflict
- [ ] Create coherent UML/design set
- [ ] Review trade-offs and change impact

### Case Study Prompts

- [ ] New domains without immediate answers
- [ ] Requirements → Use Cases → UML → Design deliverables

**Files dự kiến:** `08-Exercises/basic.md`, `intermediate.md`, `advanced.md`, `case-study-prompts.md`

## 09 — Exam Preparation

### Key Concepts

- [ ] Mental models và minimum recall set theo module

### Common Exam Questions

- [ ] Explain, justify và critique questions

### Comparison Questions

- [ ] Functional vs non-functional/quality
- [ ] Verification vs validation
- [ ] Include vs extend
- [ ] Analysis vs design
- [ ] Association vs aggregation vs composition
- [ ] Actor vs user
- [ ] Use Case vs User Story
- [ ] Requirement vs specification
- [ ] State machine vs activity diagram
- [ ] GRASP vs SOLID vs Design Pattern

### Scenario Questions

- [ ] Identify stakeholders, actors và boundary
- [ ] Extract/rewrite requirements
- [ ] Identify use cases và relationships
- [ ] Discover classes/relationships/responsibilities
- [ ] Select and build appropriate UML views
- [ ] Justify design and tests

**Files dự kiến:** `09-Exam-Preparation/key-concepts.md`, `common-questions.md`, `comparison-questions.md`, `scenario-questions.md`

## Coverage matrix với yêu cầu ban đầu

| Requested area | Curriculum location |
|---|---|
| Requirements Engineering, stakeholders, types, elicitation, analysis | 01.1–01.9 |
| SRS, quality, validation, traceability | 02.1–02.3 |
| Use Case fundamentals, identification, diagram, specification | 03.1–03.4 |
| OOAD, domain modeling, classes, BCE, CRC | 04.1–04.4 |
| UML diagrams | 05.0–05.7 |
| Class relationships, multiplicity, navigability | 05.1 |
| Sequence fragments and derivation | 05.2 |
| GRASP, analysis/design, SOLID, patterns | 06.1–06.5 |
| Six systems end-to-end | 07.1–07.6 |
| Exercises at four levels | 08 |
| Exam preparation at four views | 09 |
