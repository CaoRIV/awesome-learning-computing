# [Module Number]. [Tên module] ([English Term])

> **Thời lượng:** 30–75 phút  
> **Prerequisites:** Module cần học trước  
> **Status:** Draft | Reviewed | Stable

## Overview

Trong 1–2 đoạn, nêu module giải quyết vấn đề gì và vì sao người học cần biết khi đọc, viết hoặc review code OOP.

## Learning Outcomes

Sau module này, người học có thể:

- mô tả một năng lực có thể kiểm chứng;
- phân tích một đoạn code hoặc design;
- đưa ra một quyết định kèm trade-off.

## 1. Mental Model

Đưa ra cách hiểu ngắn gọn nhưng chính xác. Phân biệt các concept dễ nhầm ngay tại đây.

## 2. Core Concepts

Chỉ giữ concepts nằm trong scope của module. Mỗi concept trả lời:

- What: nó là gì?
- Why: nó giải quyết problem gì?
- How: mechanism hoặc design reasoning chính là gì?

## 3. Focused C++ Example

Đưa một example đủ nhỏ để đọc nhanh nhưng đủ thực tế để thể hiện design decision. Code phải compile được trong context đã trình bày.

Sau code, giải thích:

- responsibility;
- invariant;
- relationship;
- ownership/lifetime khi liên quan;
- expected behavior.

## 4. Bad Design

Đưa một bad example realistic và phân tích:

- symptom;
- root cause;
- change scenario làm lỗi lộ ra;
- maintenance hoặc correctness cost.

## 5. Better Design

Refactor vừa đủ để giải quyết root cause. Không thêm interface, factory hoặc hierarchy nếu direct design đã đáp ứng requirement.

## 6. Trade-offs

| Choice | Benefit | Cost | Use when |
|---|---|---|---|
| A | Lợi ích cụ thể | Chi phí cụ thể | Điều kiện cụ thể |
| B | Lợi ích cụ thể | Chi phí cụ thể | Điều kiện cụ thể |

## 7. Common Mistakes

Liệt kê 3–5 lỗi phổ biến, mỗi lỗi có hậu quả hoặc dấu hiệu nhận biết.

## 8. Misconceptions

Sửa 2–4 cách hiểu sai thường gặp. Phân biệt design guideline với language rule.

## 9. Quick Review Questions

Đưa 3–5 câu hỏi bao phủ:

- definition bằng lời của người học;
- code behavior;
- design decision;
- trade-off hoặc when-not-to-use.

Mỗi câu có answer key ngắn ở cuối module.

## 10. Mini Exercise

### Problem

Bài tập hoàn thành trong 15–30 phút và tập trung đúng năng lực của module.

### Acceptance Criteria

- Behavior quan sát được.
- Design constraint rõ.
- Yêu cầu giải thích ít nhất một decision.

## 11. One-Minute Summary

Tóm tắt:

- mental model;
- lỗi cần tránh;
- decision rule;
- module tiếp theo.

## Module Checklist

- [ ] Đọc trong thời lượng mục tiêu.
- [ ] Không vượt depth limit của core OOP.
- [ ] Có focused example và bad/better design.
- [ ] Có trade-off và when-not-to-use.
- [ ] Có ownership/lifetime analysis khi liên quan.
- [ ] Có 3–5 review questions cùng answer key.
- [ ] Mini exercise có acceptance criteria.
- [ ] Code compile được.
- [ ] Links hợp lệ.

