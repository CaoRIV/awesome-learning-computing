# OOP Quick Review — Learning Roadmap

## Overview

Roadmap này tối ưu cho việc ôn OOP nhanh và có hệ thống. Nó không cố biến OOP thành một chương trình học kiến trúc phần mềm. Mỗi module chỉ giữ những kiến thức có tác động trực tiếp đến khả năng đọc code, thiết kế class, hiểu object collaboration và nhận diện design problem.

## 1. Baseline

Người học nên biết:

- biến, function, condition, loop và container cơ bản;
- pointer, reference và `const` ở mức sử dụng;
- cách compile và chạy một chương trình C++ nhỏ.

Không cần biết trước SOLID, Design Patterns hoặc UML.

## 2. Learning loop

Mỗi module đi qua vòng lặp ngắn:

> Mental Model → Focused Example → Bad Design → Better Design → Checkpoint

Một module được xem là hoàn thành khi người học:

- giải thích concept bằng lời của mình;
- dự đoán behavior của example code;
- chỉ ra vấn đề trong bad design;
- nêu một trade-off;
- trả lời checkpoint không nhìn tài liệu.

## 3. Module 1 — OOP Mental Model

**Thời lượng:** 30–45 phút.

### Học gì

- Procedural Programming và OOP tổ chức code khác nhau thế nào.
- Object là state + behavior + identity trong một boundary có responsibility.
- Bốn khái niệm thường gọi là “four pillars”: Encapsulation, Abstraction, Inheritance và Polymorphism.
- Vì sao inheritance không phải trung tâm của mọi thiết kế OOP.

### Cần nhớ

OOP hữu ích khi chương trình có stateful concepts và collaborations cần được tổ chức quanh responsibilities. OOP không tự động tốt hơn free functions, procedural code hoặc functional style.

### Checkpoint

Giải thích vì sao một function thuần tính tổng số tiền trong giỏ hàng không nhất thiết phải trở thành method của một object.

## 4. Module 2 — Class, Object và Object Lifecycle

**Thời lượng:** 45–60 phút.

### Học gì

- Class, object, state, behavior và identity.
- Member variable và member function.
- Constructor, destructor và invariant.
- Value object so với entity ở mức thực dụng.
- Construction/destruction order cơ bản.

### Cần nhớ

Class tốt không chỉ gom data và functions. Nó đại diện cho một concept có responsibility rõ và giữ object hợp lệ trong suốt lifetime.

### Checkpoint

Thiết kế `BankAccount` sao cho không thể withdraw số âm và không thể đưa balance vào trạng thái bị cấm bởi business rule.

## 5. Module 3 — Encapsulation và Abstraction

**Thời lượng:** 45–60 phút.

### Học gì

- Encapsulation, data hiding và access modifiers.
- Invariant và representation exposure.
- Getter/setter khi nào hợp lý, khi nào tạo anemic object.
- Abstraction, interface và implementation hiding.
- Abstraction khác Encapsulation thế nào.

### Cần nhớ

`private` chỉ là mechanism hỗ trợ. Encapsulation thực sự đạt được khi API bảo vệ invariant và che giấu quyết định có khả năng thay đổi. Abstraction xác định điều client cần biết; Encapsulation kiểm soát detail và state bên trong boundary.

### Checkpoint

Refactor một `Rectangle` có public `width` và `height` thành API không cho phép kích thước âm.

## 6. Module 4 — Object Relationships và Composition

**Thời lượng:** 45–60 phút.

### Học gì

- Association và Dependency.
- Composition và ownership/lifetime.
- Aggregation ở mức nhận biết, cùng sự mơ hồ thường gặp.
- Delegation.
- HAS-A so với IS-A.
- Composition over Inheritance.

### Cần nhớ

Tên relationship không đủ để thiết kế code. Phải hỏi thêm: ai sở hữu object, lifetime ra sao, relation có bắt buộc không và behavior được delegated thế nào.

### Checkpoint

Mô tả relationship giữa `Order`, `OrderLine`, `Product` và `PaymentMethod`, bao gồm ownership và lifetime.

## 7. Module 5 — Inheritance, Polymorphism và Dynamic Dispatch

**Thời lượng:** 60–75 phút.

### Học gì

- Base class, derived class và public inheritance.
- IS-A và behavioral substitutability.
- Overloading so với overriding.
- Virtual function, pure virtual function và abstract class.
- Static type, dynamic type và runtime dispatch.
- Virtual destructor và object slicing.
- Khi inheritance tạo hierarchy cứng.

### Cần nhớ

Inheritance phù hợp khi derived object có thể thay thế base object mà không phá contract. Nếu mục tiêu chỉ là reuse code hoặc thay đổi behavior, composition thường đơn giản và linh hoạt hơn.

### Checkpoint

Giải thích vì sao `Square : public Rectangle` có thể vi phạm substitutability nếu API cho phép thay đổi width và height độc lập.

## 8. Module 6 — Ownership, Lifetime và RAII trong C++

**Thời lượng:** 45–60 phút.

### Học gì

- Object lifetime và storage duration ở mức cần thiết.
- Owning so với non-owning reference/pointer.
- RAII và deterministic destruction.
- `std::unique_ptr`, `std::shared_ptr` và `std::weak_ptr` theo use case.
- Rule of 0 là lựa chọn mặc định.
- Copy/move semantics ở mức nhận diện design implication.

### Cần nhớ

Smart pointer không tự giải quyết ownership design. Hãy xác định owner trước, sau đó chọn representation. Ưu tiên value members và Rule of 0; dùng dynamic allocation khi lifetime hoặc polymorphism thực sự yêu cầu.

### Checkpoint

Giải thích vì sao dùng `std::shared_ptr` cho mọi dependency làm ownership khó hiểu và có thể tạo cycle.

## 9. Module 7 — SOLID Essentials

**Thời lượng:** 60–75 phút.

### Học gì

- High Cohesion và Low Coupling.
- SRP: reason to change.
- OCP: variation point có bằng chứng.
- LSP: behavioral substitutability.
- ISP: interface theo nhu cầu client.
- DIP: dependency của policy không bị detail điều khiển.
- DI là mechanism, không đồng nghĩa với DIP.
- Dấu hiệu áp dụng SOLID quá mức.

### Cần nhớ

SOLID giúp đặt câu hỏi về change và dependency. Nó không yêu cầu mỗi class có một method, mọi dependency đều có interface hoặc code không bao giờ được sửa.

### Checkpoint

Cho một `CheckoutService` vừa tính giá, lưu order, charge payment và gửi email: xác định responsibility nào thay đổi vì các lý do khác nhau, nhưng không tạo interface nếu chưa có variation/test seam cần thiết.

## 10. Module 8 — Applied Review

**Thời lượng:** 60–90 phút.

### Bài toán

Thiết kế core của một notification system hỗ trợ email và SMS. Client có thể gửi message mà không phụ thuộc trực tiếp vào implementation cụ thể.

### Yêu cầu

- Model state và behavior cần thiết.
- Dùng encapsulation để giữ object hợp lệ.
- Chỉ rõ relationship và ownership.
- Dùng runtime polymorphism hoặc composition có lý do.
- Thêm một notification channel mới mà không sửa client flow chính.
- Không dùng global service locator hoặc `shared_ptr` mặc định.

### Review checklist

1. Responsibility của mỗi class là gì?
2. Invariant nào được bảo vệ?
3. Dependency direction có hợp lý không?
4. Ownership và lifetime có đọc được từ API không?
5. Inheritance có thỏa substitutability không?
6. Có abstraction nào chưa mang lại giá trị không?
7. Change request nào khiến thiết kế hiện tại khó sửa?

## 11. Ba learning paths

### Quick Refresh — 3 giờ

Module 2 → 3 → 4 → 5 → Review checklist của Module 8.

### Complete Refresh — 6–8 giờ

Module 1 → 8 theo thứ tự.

### Interview Refresh — một ngày

Học đủ 8 module, sau mỗi module tự trả lời:

- concept giải quyết problem gì;
- bad design thường gặp;
- một ví dụ code;
- một trade-off;
- khi nào không nên dùng.

## 12. Exit criteria

Hoàn thành track khi có thể:

- phân biệt bốn OOP concepts mà không dùng định nghĩa vòng tròn;
- thiết kế class giữ invariant;
- giải thích relationship kèm ownership/lifetime;
- dự đoán virtual dispatch;
- phát hiện slicing và destruction bug;
- bảo vệ lựa chọn composition hoặc inheritance;
- review code bằng cohesion, coupling và SOLID;
- hoàn thành Module 8 và giải thích trade-offs.

## 13. Học tiếp sau core OOP

Chỉ sau khi cần cho công việc hoặc mục tiêu riêng:

- Design Patterns thực dụng: Strategy, Factory, Observer, Decorator.
- C++ resource management nâng cao: Rule of 3/5, exception safety.
- OOAD và UML.
- Software Architecture và System Design.

Các chủ đề này không phải điều kiện để hoàn thành OOP Quick Review.

