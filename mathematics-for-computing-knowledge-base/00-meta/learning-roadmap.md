# Learning Roadmap — Mathematics for Computing Practical Foundations

> **Thời lượng gợi ý:** 12–16 giờ core, cộng 12–20 giờ thực hành  
> **Prerequisites:** Lập trình cơ bản; không yêu cầu toán nâng cao  
> **Case study:** Learning Platform

## Đích đến

Sau lộ trình, người học có thể dùng toán như công cụ reasoning thay vì tập hợp công thức để nhớ. Bạn sẽ biết cách mô tả một rule chính xác, chứng minh behavior cho mọi input trong phạm vi, định lượng uncertainty từ dữ liệu và chọn representation phù hợp cho quan hệ hoặc vector.

Đích đến không phải xử lý mọi bài toán bằng ký hiệu. Một lời giải tốt thường bắt đầu bằng câu chữ rõ, sau đó dùng ký hiệu để rút gọn mà không mất meaning. Nếu một biểu thức dài hơn lời giải thích và không giúp kiểm tra claim, nó chưa tạo giá trị.

## Bản đồ học tập

```text
01. Claims + assumptions + proof
              ↓
02. Logic + predicates + quantifiers
              ↓
03. Sets + functions + relations
              ↓
04. Induction + recursion + invariants
              ↓
05. Counting + probability
              ↓
06. Statistics + experiments
              ↓
07. Graphs + trees + paths
              ↓
08. Vectors + matrices + transformations
```

Module 1–3 xây ngôn ngữ mô tả. Module 4 nối proof với chương trình. Module 5–6 xử lý số trường hợp và dữ liệu không chắc chắn. Module 7–8 giới thiệu hai representation xuất hiện rộng khắp computing: graph cho connections và vector cho quantities/features.

## Nhịp học gợi ý

Trong sáu buổi, buổi đầu học Module 1–2 và dịch conditions trong code sang logic. Buổi hai học Module 3 và mô hình hóa Learning Platform. Buổi ba học Module 4, trace recursion và viết loop invariant. Buổi bốn học Module 5 bằng cách tự vẽ sample space. Buổi năm học Module 6 và phân tích một experiment nhỏ. Buổi cuối học Module 7–8 rồi làm bài tích hợp.

Nếu chỉ có tám giờ để review, ưu tiên Module 1, 2, 4, 5, 7 và phần vector/similarity của Module 8. Người học algorithms cần Module 3, 4, 5 và 7 sâu hơn. Người học data/AI cần Module 5, 6 và 8 nhưng không nên bỏ logic và functions vì chúng giữ definition và assumptions rõ.

## Checkpoint 1 — Claim có nghĩa và có cách kiểm tra

Sau Module 1, bạn cần tách được definition, claim, assumption và conclusion. Bạn biết thử example để hiểu claim, tìm counterexample để bác bỏ, và chọn proof trực tiếp, contradiction hoặc case analysis khi claim đúng.

Artifact là một proof note ngắn: chọn claim computing như “tổng của hai số chẵn là số chẵn”, “binary search không bỏ qua target nếu invariant giữ”, hoặc một claim tương đương. Viết domain, assumption, từng bước và lý do; không dùng vài test cases thay proof.

## Checkpoint 2 — Rule không còn mơ hồ

Sau Module 3, bạn cần dịch một business rule thành predicate, dùng `and`, `or`, `not`, implication và quantifier đúng. Bạn phải phân biệt `∀x ∃y` với `∃y ∀x`, subset với membership, function với relation và total function với partial lookup.

Artifact là model cho learners, courses và prerequisites: định nghĩa sets, eligibility predicate, mapping course → instructor và prerequisite relation. Ghi rõ trường hợp không có value thay vì giả vờ mọi lookup là total.

## Checkpoint 3 — Chứng minh process lặp

Sau Module 4, bạn cần viết base case và inductive step cho một recursive structure hoặc statement theo `n`. Với loop, bạn phải nêu invariant đúng trước vòng lặp, được giữ sau mỗi iteration và đủ để suy ra postcondition khi loop kết thúc.

Artifact có thể là proof correctness cho hàm tính tổng, tìm max hoặc duyệt prerequisite tree. Bài chưa đạt nếu chỉ nói “dễ thấy” ở bước chính hoặc induction giả định luôn chính điều cần chứng minh cho trường hợp hiện tại.

## Checkpoint 4 — Đếm đúng trước khi tính xác suất

Sau Module 5, bạn cần xác định outcome, sample space và event. Bạn biết khi nào dùng product rule, sum rule, combination hay permutation; biết kiểm tra overlap để không double-count. Conditional probability phải được đọc là sample space đã thu hẹp, không chỉ là công thức.

Artifact là một risk calculation nhỏ cho Learning Platform: probability một learner hoàn thành course dựa trên một nhóm điều kiện minh họa, hoặc collision probability trong không gian ID nhỏ. Ghi assumption về independence; nếu không có cơ sở, không nhân probabilities tùy tiện.

## Checkpoint 5 — Kết luận vừa với dữ liệu

Sau Module 6, bạn cần mô tả distribution bằng center và spread, nhận ra outlier/skew, phân biệt population với sample và confidence interval với xác suất parameter nằm trong interval sau khi đã quan sát. Bạn phải phân biệt association, prediction và causal effect.

Artifact là experiment report một trang: metric chính, randomization unit, sample size reasoning cơ bản, result cùng uncertainty, practical effect và limitation. Không chỉ báo “tăng 5%” nếu không nói 5 percentage points hay 5 percent tương đối.

## Checkpoint 6 — Chọn representation đúng

Sau Module 8, bạn cần nhận ra prerequisite network là directed graph, hierarchy là tree có constraint, và course features có thể là vectors trong cùng coordinate system. Bạn có thể trace BFS/DFS, phát hiện cycle, tính dot product/cosine similarity và giải thích matrix multiplication như composition của transformations.

Artifact cuối là một model tích hợp: graph prerequisite có validation, một thống kê về completion và một vector representation đơn giản để so course. Mỗi representation phải nêu information giữ lại và information bỏ đi.

## Bài tốt nghiệp

Thiết kế một tính năng “gợi ý lộ trình học” ở mức toán học, chưa cần production code. Eligibility được viết bằng logic; learners/courses/prerequisites bằng sets và relations; prerequisite order bằng graph; kế hoạch không có cycle; dữ liệu completion được tóm tắt cùng uncertainty; course similarity dùng vector có features giải thích được.

Bài tốt không cần model AI. Một thuật toán graph cùng rules rõ có thể đáp ứng nhu cầu tốt hơn recommendation system phức tạp khi dữ liệu ít. Nếu thêm probability hoặc similarity score, phải nói chúng đo gì và không đo gì.

## Extension paths

Algorithms mở rộng sang recurrence, asymptotic analysis, randomized algorithms và advanced graph algorithms. Systems mở rộng sang queuing, reliability probability và coding theory. Security mở rộng number theory, modular arithmetic, finite fields và cryptography. Graphics/game mở rộng geometry, transformations và calculus. Data/AI mở rộng calculus, optimization, linear algebra sâu và statistical learning.

Extension không phải level bắt buộc sau core. Chọn nó theo vấn đề cần giải. Học abstract algebra trước khi biết mình cần cryptography hoặc coding theory thường tạo nhiều ký hiệu nhưng ít điểm neo.

## Definition of Done

Một module hoàn thành khi bạn đọc được ký hiệu thành câu, tự giải ví dụ nhỏ, giải thích vì sao từng bước hợp lệ và dùng code để kiểm tra chứ không thay proof. Khi assumption thay, bạn phải dự đoán claim còn đúng không và tìm counterexample nếu không.
