# OOP Quick Review

## Mục tiêu

Đây là lộ trình ôn tập Lập trình hướng đối tượng (**Object-Oriented Programming — OOP**) dành cho:

- sinh viên đã biết lập trình và cần hệ thống hóa kiến thức;
- người đi làm muốn ôn lại OOP để đọc, viết và review code tốt hơn;
- người chuẩn bị phỏng vấn về OOP ở mức nền tảng đến thực dụng.

Lộ trình dùng **C++20** làm ngôn ngữ chính, hoàn thành trong khoảng **6–8 giờ**. Trọng tâm là mental model, design reasoning và lỗi thường gặp; không đi sâu vào mọi chi tiết của C++ object model, toàn bộ Design Patterns hoặc Software Architecture.

## Bắt đầu

1. [Learning Roadmap](00-meta/01-learning-roadmap.md) — Lộ trình 6–8 giờ và checkpoint.
2. [Knowledge Base Structure](00-meta/02-knowledge-base-structure.md) — Phạm vi và chuẩn nội dung ngắn gọn.
3. [Table of Contents](00-meta/03-table-of-contents.md) — Nội dung của 8 module.
4. [Topic Dependencies](00-meta/04-topic-dependencies.md) — Thứ tự và prerequisite tối thiểu.
5. [Module Template](00-meta/topic-template.md) — Khuôn viết cho từng module.

## Lộ trình 8 module

| Module | Chủ đề | Thời lượng |
|---|---|---:|
| 1 | OOP Mental Model | 30–45 phút |
| 2 | Class, Object và Object Lifecycle | 45–60 phút |
| 3 | Encapsulation và Abstraction | 45–60 phút |
| 4 | Object Relationships và Composition | 45–60 phút |
| 5 | Inheritance, Polymorphism và Dynamic Dispatch | 60–75 phút |
| 6 | Ownership, Lifetime và RAII trong C++ | 45–60 phút |
| 7 | SOLID Essentials | 60–75 phút |
| 8 | Applied Review: Thiết kế một hệ thống nhỏ | 60–90 phút |

## Sau khi hoàn thành

Người học có thể:

- phân biệt class, object, state, behavior và identity;
- thiết kế class bảo vệ invariant thay vì chỉ chứa getter/setter;
- phân biệt Encapsulation và Abstraction;
- nhận diện Association, Dependency, Composition và Delegation;
- quyết định Composition hoặc Inheritance theo substitutability;
- giải thích overriding, virtual function và runtime polymorphism;
- tránh object slicing và lỗi virtual destructor;
- mô tả ownership, lifetime và vai trò của RAII;
- dùng SOLID như checklist chẩn đoán, không áp dụng máy móc;
- đọc và review một thiết kế OOP nhỏ bằng reasoning cụ thể.

## Những gì không nằm trong core path

- Multiple inheritance và virtual inheritance chi tiết.
- vtable/vptr ở mức ABI hoặc compiler implementation.
- Rule of 3/5 và exception guarantees chuyên sâu.
- Toàn bộ GoF Design Patterns.
- UML và quy trình OOAD đầy đủ.
- Software Architecture và System Design.

Các nội dung này nên được học ở track nâng cao riêng sau khi core OOP đã chắc.

## Nguyên tắc học

- **Hiểu problem trước solution.**
- **Code phải thể hiện design decision, không chỉ syntax.**
- **Composition là lựa chọn mặc định để xem xét; inheritance cần chứng minh IS-A và substitutability.**
- **SOLID là heuristic theo context, không phải luật chấm điểm.**
- **Trong C++, ownership và lifetime là một phần của object design.**

## Quick paths

### Ôn cực nhanh trong 3 giờ

Học Module 2 → 3 → 4 → 5, sau đó dùng checklist ở Module 8 để tự review một class design.

### Ôn phỏng vấn trong một ngày

Học đủ 8 module, tự trả lời Interview Questions không nhìn tài liệu và hoàn thành bài tập tổng hợp.

### Ôn cho công việc

Tập trung Module 3 → 4 → 5 → 6 → 7 → 8. Với mỗi module, lấy một class trong codebase thật và áp dụng review checklist.

