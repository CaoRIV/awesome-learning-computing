# OOP Quick Review — Table of Contents

## Overview

Curriculum gồm 8 module, tổng thời lượng 6–8 giờ. Nội dung tập trung vào core OOP và các vấn đề C++ trực tiếp ảnh hưởng đến object design.

## Module 1 — OOP Mental Model

Lesson: [01-oop-mental-model.md](../modules/01-oop-mental-model.md)

### Core concepts

- Programming paradigm là gì.
- Procedural Programming so với Object-Oriented Programming.
- C++ là multi-paradigm language.
- Object: state, behavior, identity và responsibility.
- Encapsulation, Abstraction, Inheritance và Polymorphism ở mức bản đồ tổng quan.
- Vì sao OOP tồn tại và loại complexity nó giúp tổ chức.
- Khi procedural/free-function design đơn giản hơn.

### Review focus

- OOP không đồng nghĩa với “mọi thứ là class”.
- Four pillars không phải bốn feature độc lập.
- Inheritance không phải trung tâm của OOP.

## Module 2 — Class, Object và Object Lifecycle

Lesson: [02-class-object-lifecycle.md](../modules/02-class-object-lifecycle.md)

### Core concepts

- Class và object.
- State, behavior và identity.
- Member variable và member function.
- Constructor và member initializer list.
- Destructor.
- Object invariant.
- Value object so với entity.
- Construction/destruction order cơ bản.
- `const` member function.

### Review focus

- Class có responsibility, không chỉ là data container.
- Constructor tạo valid state.
- Copy một entity có thể không có ý nghĩa domain.

## Module 3 — Encapsulation và Abstraction

Lesson: [03-encapsulation-abstraction.md](../modules/03-encapsulation-abstraction.md)

### Core concepts

- Tính đóng gói (**Encapsulation**).
- Data hiding và access modifiers.
- Public API và invariant.
- Representation exposure.
- Getter/setter và anemic object.
- Tính trừu tượng (**Abstraction**).
- Interface và abstract class ở mức thực dụng.
- Implementation hiding.
- Abstraction so với Encapsulation.

### Review focus

- `private` không tự động tạo encapsulation tốt.
- Không tạo setter nếu operation có tên domain rõ hơn.
- Interface nên mô tả capability client cần.

## Module 4 — Object Relationships và Composition

Lesson: [04-object-relationships-composition.md](../modules/04-object-relationships-composition.md)

### Core concepts

- Association.
- Dependency.
- Composition.
- Aggregation ở mức nhận biết.
- Delegation.
- Cardinality, optionality, navigability.
- Ownership và lifetime.
- HAS-A và IS-A.
- Composition over Inheritance.

### Comparison

| Relationship | Câu hỏi chính |
|---|---|
| Dependency | Object này có dùng object kia tạm thời không? |
| Association | Hai object có quan hệ lâu hơn một lời gọi không? |
| Composition | Whole có sở hữu part và kiểm soát lifetime không? |
| Delegation | Object có chuyển responsibility thực thi cho collaborator không? |
| Inheritance | Derived có thực sự là behavioral subtype của base không? |

### Review focus

- Relationship name không thay thế ownership decision.
- Composition giúp reuse behavior mà không tạo subtype.
- Bidirectional association làm tăng coupling và synchronization cost.

## Module 5 — Inheritance, Polymorphism và Dynamic Dispatch

Lesson: [05-inheritance-polymorphism.md](../modules/05-inheritance-polymorphism.md)

### Core concepts

- Tính kế thừa (**Inheritance**).
- Base class và derived class.
- Public inheritance và IS-A.
- Method overriding.
- Function overloading so với overriding.
- Tính đa hình (**Polymorphism**).
- Virtual function và pure virtual function.
- Abstract class/interface convention trong C++.
- Static type và dynamic type.
- Dynamic dispatch.
- Virtual destructor.
- Object slicing.
- Behavioral substitutability và LSP connection.

### Review focus

- Inheritance để model subtype, không chỉ để reuse code.
- Base interface cần contract rõ.
- Xóa derived object qua base pointer cần destruction policy đúng.
- Pass-by-value qua base type gây slicing.

## Module 6 — Ownership, Lifetime và RAII trong C++

Lesson: [06-cpp-ownership-lifetime-raii.md](../modules/06-cpp-ownership-lifetime-raii.md)

### Core concepts

- Object lifetime và storage duration ở mức nền tảng.
- Owner, borrower và observer.
- Value member và dynamic allocation.
- RAII.
- `std::unique_ptr` cho exclusive ownership.
- `std::shared_ptr` cho shared lifetime thực sự.
- `std::weak_ptr` để observe shared object và phá cycle.
- Rule of 0.
- Copy/move semantics ở mức design awareness.
- Dangling pointer, leak, double delete và ownership cycle.

### Review focus

- Chọn ownership model trước smart-pointer type.
- Value semantics thường đơn giản nhất.
- `shared_ptr` không phải default dependency wrapper.
- RAII áp dụng cho mọi resource, không chỉ memory.

## Module 7 — SOLID Essentials

Lesson: [07-solid-essentials.md](../modules/07-solid-essentials.md)

### Foundation

- High Cohesion.
- Low Coupling.
- Program to an Interface theo nhu cầu thực tế.
- Encapsulate What Varies có bằng chứng.

### Principles

- **S — Single Responsibility Principle:** một reason to change theo actor/context.
- **O — Open/Closed Principle:** mở rộng tại variation point đã biết.
- **L — Liskov Substitution Principle:** subtype giữ behavioral contract.
- **I — Interface Segregation Principle:** client không phụ thuộc operation không dùng.
- **D — Dependency Inversion Principle:** policy không bị detail điều khiển.

### Review focus

- SRP không có nghĩa một class chỉ có một method.
- OCP không có nghĩa code không bao giờ được sửa.
- LSP không được kiểm tra chỉ bằng hình dạng class hierarchy.
- ISP không yêu cầu tách mỗi method thành một interface.
- DIP không đồng nghĩa với Dependency Injection container.
- SOLID có thể bị overused và tạo indirection không cần thiết.

## Module 8 — Applied Review

Lesson: [08-applied-review.md](../modules/08-applied-review.md)

### Case study

Thiết kế một notification system hỗ trợ Email và SMS, sau đó thêm Push Notification như một change request.

### Activities

1. Xác định state, behavior và invariants.
2. Phân bổ responsibilities.
3. Xác định relationships và ownership.
4. Chọn abstraction boundary.
5. So sánh `if/switch`, inheritance và composition.
6. Implement core C++ classes.
7. Thêm change request.
8. Review bằng cohesion, coupling và SOLID.
9. Ghi lại trade-offs và abstraction đã từ chối.

### Final checklist

- Object có responsibility rõ không?
- Invalid state có thể được tạo dễ dàng không?
- API có expose representation không?
- Relationship và ownership có rõ không?
- Inheritance có thỏa substitutability không?
- Runtime polymorphism có thực sự cần không?
- Destructor và lifetime có an toàn không?
- SOLID có được áp dụng theo change scenario không?
- Có cách đơn giản hơn với ít class hơn không?

## Optional Next Steps

Không thuộc core OOP refresher:

- Strategy, Factory, Observer và Decorator.
- Rule of 3/5 và exception safety.
- Multiple inheritance.
- OOAD và UML.
- Software Architecture.

## Coverage audit

| OOP skill | Module |
|---|---|
| Hiểu bản chất OOP | 1 |
| Thiết kế class/object | 2–3 |
| Bảo vệ state và invariant | 2–3 |
| Hiểu object relationships | 4 |
| Chọn Composition/Inheritance | 4–5 |
| Hiểu runtime polymorphism | 5 |
| Quản lý lifetime trong C++ | 6 |
| Review bằng SOLID | 7 |
| Áp dụng tổng hợp | 8 |
