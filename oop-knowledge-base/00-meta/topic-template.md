# [Tên chủ đề tiếng Việt] ([English Term])

> **Status:** Draft  
> **Prerequisites:** Liệt kê các topic prerequisite bằng link  
> **Learning outcomes:** 3–6 năng lực có thể kiểm chứng  
> **Last reviewed:** YYYY-MM-DD

## Overview

Nêu problem context, phạm vi và giá trị học tập trong 1–3 đoạn. Xác định rõ topic không bao gồm điều gì nếu boundary dễ nhầm.

## 1. Concept — What

Định nghĩa chính xác, boundary, vocabulary và mental model. Phân biệt language rule, implementation detail, design heuristic hoặc project convention khi cần.

## 2. Why It Exists

Trình bày motivation và forces. Giải thích vì sao cách tổ chức đơn giản hơn bắt đầu gặp giới hạn.

## 3. Problem Without It

Đưa ra failure mode cụ thể: invalid state, ripple change, hidden dependency, lifetime bug, duplicated policy hoặc contract violation.

## 4. How It Works

Giải thích mechanism ở compile time, runtime và design level tùy topic. Với C++, làm rõ memory, ownership và object lifetime nếu có liên quan.

## 5. Focused Example

Đưa code C++ tối thiểu nhưng đủ để chứng minh behavior hoặc design decision. Ghi expected behavior và lý do từng thành phần tồn tại.

## 6. Bad Example

Đưa một implementation realistic có design flaw. Phân tích:

- symptom;
- root cause;
- change scenario;
- maintenance/bug cost;
- behavior cần bảo toàn khi refactor.

## 7. Refactored Good Example

Đưa code hoàn chỉnh cho phạm vi example. Giải thích responsibility, dependency direction, invariant, ownership, lifetime, error handling và testability đã thay đổi thế nào.

## 8. Trade-offs and Alternatives

| Option | Advantages | Costs/Risks | Suitable Context |
|---|---|---|---|
| Option A | Lợi ích cụ thể | Chi phí cụ thể | Điều kiện áp dụng |
| Option B | Lợi ích cụ thể | Chi phí cụ thể | Điều kiện áp dụng |

Đưa recommendation theo context; không gọi một lựa chọn là universal best.

## 9. When to Use

Liệt kê các decision signals có thể quan sát.

## 10. When NOT to Use

Liệt kê contraindications, simpler alternatives và dấu hiệu over-engineering.

## 11. Connections With Other Concepts

### Prerequisite connections

Giải thích concept này xây trên topic nào.

### Downstream connections

Giải thích concept này mở khóa principle, pattern hoặc OOAD skill nào.

### Easily confused concepts

Dùng bảng nhiều dimension khi có cặp dễ nhầm.

## 12. Common Mistakes

Liệt kê lỗi kèm hậu quả và cách nhận biết, không chỉ tên lỗi.

## 13. Misconceptions

Đối chiếu phát biểu thường nghe với correction có context.

## 14. Interview Questions

Đưa 5–10 câu từ concept đến reasoning. Câu hỏi phải có answer key ngắn hoặc link chính xác đến section chứa câu trả lời.

## 15. Thinking Questions

Đưa 3–5 câu không có một đáp án duy nhất, yêu cầu nêu assumptions và trade-offs.

## 16. Mini Exercise

### Problem

Một bài tập tập trung vào topic, hoàn thành trong 20–40 phút.

### Acceptance criteria

- Behavior kiểm chứng được.
- Constraint kỹ thuật cụ thể.
- Yêu cầu giải thích ít nhất một design decision.

### Review prompts

- Invariant nằm ở đâu?
- Responsibility có cohesive không?
- Ownership/lifetime có rõ không?
- Alternative đơn giản hơn là gì?

## 17. Challenge

Một bài toán kết hợp nhiều concept, có ambiguity cần ghi assumption và một change request để kiểm tra design.

## 18. Summary

Tóm tắt mental model, decision rule có context, failure mode quan trọng và connection tiếp theo. Không chỉ lặp lại định nghĩa đầu bài.

## Review Checklist

- [ ] What, Why, Problem và How đầy đủ.
- [ ] Example chứng minh concept/design, không chỉ syntax.
- [ ] Bad example có failure scenario.
- [ ] Refactored code giải quyết root cause.
- [ ] Trade-offs và alternatives cụ thể.
- [ ] When to use/not use có decision signals.
- [ ] Memory, ownership và lifetime được giải thích khi liên quan.
- [ ] Common Mistakes và Misconceptions không trùng ý.
- [ ] Có 5–10 Interview Questions và answer key/link.
- [ ] Có 3–5 Thinking Questions.
- [ ] Mini Exercise có acceptance criteria.
- [ ] Challenge có ambiguity và change request.
- [ ] Internal links hợp lệ.
- [ ] Code compile/test thành công.
- [ ] Terminology khớp glossary.

