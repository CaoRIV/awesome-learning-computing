# OOP Quick Review — Knowledge Base Structure

## Overview

Knowledge base được tổ chức thành 8 module ngắn, mỗi module học trong khoảng 30–75 phút. Mục tiêu là ôn nhanh nhưng vẫn chính xác, không biến tài liệu thành danh sách định nghĩa hoặc một khóa C++ nâng cao.

## 1. Planned structure

```text
oop-knowledge-base/
├── README.md
├── 00-meta/
│   ├── 01-learning-roadmap.md
│   ├── 02-knowledge-base-structure.md
│   ├── 03-table-of-contents.md
│   ├── 04-topic-dependencies.md
│   └── topic-template.md
└── modules/
    ├── 01-oop-mental-model.md
    ├── 02-class-object-lifecycle.md
    ├── 03-encapsulation-abstraction.md
    ├── 04-object-relationships-composition.md
    ├── 05-inheritance-polymorphism.md
    ├── 06-cpp-ownership-lifetime-raii.md
    ├── 07-solid-essentials.md
    └── 08-applied-review.md
```

Thư mục `modules/` chỉ được tạo khi bắt đầu viết nội dung, tránh file rỗng hoặc placeholder.

## 2. Scope rules

Một concept được giữ trong core path khi thỏa ít nhất một điều kiện:

- cần để hiểu object và class design;
- thường xuất hiện khi đọc hoặc review code OOP;
- giúp tránh bug hoặc design mistake phổ biến;
- là câu hỏi OOP nền tảng thường gặp trong phỏng vấn;
- là prerequisite trực tiếp của module tiếp theo.

Concept được đưa ra khỏi core path khi:

- chủ yếu thuộc Software Architecture hoặc System Design;
- là chi tiết compiler/ABI không ảnh hưởng quyết định OOP thông thường;
- là C++ resource-management technique chuyên sâu;
- là catalog pattern cần học như một track riêng;
- làm tăng thời lượng nhưng không tăng đáng kể khả năng áp dụng OOP cơ bản.

## 3. Module format

Mỗi module dùng cùng cấu trúc:

1. Overview và learning outcomes.
2. Mental model.
3. Core concepts.
4. Focused C++ example.
5. Bad design và problem analysis.
6. Better design.
7. Trade-offs và when not to use.
8. Common mistakes và misconceptions.
9. Quick review questions.
10. Mini exercise và summary.

Các phần được viết ngắn và tập trung. Một module không cần lặp lại mọi định nghĩa đã có ở module prerequisite.

## 4. Code standard

- C++20 là baseline.
- Code phải minh họa behavior hoặc design decision.
- Snippet phải compile được trong context được trình bày.
- Không dùng raw owning pointer.
- Ownership và lifetime phải được nói rõ khi liên quan.
- Polymorphic base phải có destruction policy đúng.
- Ưu tiên Rule of 0, value members và Standard Library types.
- Bad code phải gắn nhãn và có phân tích root cause.
- Không thêm abstraction chỉ để làm ví dụ trông “đúng SOLID”.

## 5. Depth limit

### Giải thích đủ dùng

- vtable/vptr: chỉ nói đây là implementation model phổ biến; trọng tâm là dispatch semantics.
- Copy/move: giải thích ý nghĩa design và Rule of 0; không triển khai resource-owning class phức tạp trong core.
- Aggregation: nhận biết thuật ngữ và ambiguity; tập trung ownership/lifetime thay vì học thuộc diamond.
- SOLID: mỗi principle có một bad design, một refactoring và một overuse warning.

### Để dành cho track nâng cao

- ABI, object layout và devirtualization.
- Multiple/virtual inheritance chi tiết.
- Rule of 3/5 implementation nâng cao.
- Exception-safety guarantees chuyên sâu.
- Type erasure và static polymorphism nâng cao.
- 18 GoF Design Patterns.
- OOAD/UML đầy đủ.
- Architecture patterns.

## 6. Definition of Done cho một module

Một module hoàn thành khi:

- thời gian đọc và thực hành nằm trong target;
- learning outcomes có thể kiểm chứng;
- có mental model và focused example;
- có bad design cùng root-cause analysis;
- có better design nhưng không over-engineer;
- có ít nhất một trade-off và một when-not-to-use;
- có common mistakes và misconceptions;
- có 3–5 review questions;
- có mini exercise với acceptance criteria;
- code compile được và links hợp lệ.

## 7. Content status

| Status | Meaning |
|---|---|
| Planned | Module đã có scope nhưng chưa viết |
| Draft | Nội dung đầy đủ, đang technical review |
| Reviewed | Code, terminology và reasoning đã kiểm tra |
| Stable | Có thể dùng cho quick review |

## 8. Maintenance

- Thay đổi module order phải cập nhật roadmap, TOC và dependency graph.
- Nội dung nâng cao mới phải chứng minh giá trị trước khi vào core path.
- Nếu một module thường vượt 75 phút, tách phần nâng cao ra appendix hoặc track khác.
- Không tăng số module chỉ để mỗi concept có một file riêng.
- Ưu tiên một example xuyên suốt hơn nhiều example nhỏ không liên kết.

## Summary

Cấu trúc này tối ưu cho tốc độ ôn tập: đủ sâu để tránh hiểu sai OOP, nhưng đủ gọn để hoàn thành trong một ngày. Mọi chi tiết không trực tiếp cải thiện khả năng thiết kế, đọc hoặc review code OOP đều được hoãn sang track nâng cao.

