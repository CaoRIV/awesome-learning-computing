# 2. Logic for Computing — Viết điều kiện đúng với điều ta muốn nói

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 1](01-mathematical-thinking-and-proof.md)  
> **Status:** Reviewed

## Overview

Logic là nền của conditions, queries, access rules, tests và specifications. Bug thường không đến từ việc máy tính tính sai `and`; nó đến từ việc người viết chưa tách rõ câu tự nhiên thành các mệnh đề và phạm vi.

Module này dùng logic như ngôn ngữ đọc code. Truth table chỉ là công cụ kiểm tra; mục tiêu là hiểu implication, negation và quantifier đủ sâu để tránh rule đúng cú pháp nhưng sai meaning.

## 1. Proposition và truth value

Một **proposition** là câu có thể xác định true hoặc false trong context. “Learner đã pass assessment” là proposition khi learner và assessment cụ thể. “Hãy pass assessment” là command, không có truth value. “Course này tốt” chưa là proposition kiểm tra được nếu “tốt” chưa định nghĩa.

Ký hiệu `P`, `Q` giúp tập trung structure. Trong code, `is_active`, `has_paid` nên đại diện propositions có meaning rõ. Tên `status_check` không nói truth condition nào và làm expression khó review.

Logic cổ điển dùng hai truth values. SQL có thêm `UNKNOWN` vì `NULL`, và nhiều hệ thống có state như pending. Đừng áp truth table hai giá trị mù quáng khi domain runtime có nhiều state; hãy model state rõ trước.

## 2. NOT, AND và OR

`¬P` (NOT P) đảo truth value. `P ∧ Q` (P AND Q) chỉ true khi cả hai true. `P ∨ Q` trong logic thường là inclusive OR: true khi ít nhất một đúng, kể cả cả hai. Exclusive OR (XOR) true khi đúng đúng một phía.

Rule đăng ký course có thể là:

```text
eligible = account_active
           AND prerequisites_completed
           AND (seats_available OR has_reserved_seat)
```

Parentheses thể hiện grouping. Trong code, dù precedence đã định, parentheses có thể giữ intent dễ đọc. Không rút gọn expression chỉ để ít ký tự hơn nếu rule trở nên khó audit.

Short-circuit evaluation là behavior lập trình, không phải truth meaning. Trong `P && Q`, Q có thể không chạy nếu P false. Nếu Q có side effect, expression logic và behavior execution bị trộn; đó thường là design khó reasoning.

## 3. Implication không phải quan hệ nguyên nhân

`P → Q` đọc “nếu P thì Q”. Nó false duy nhất khi P true nhưng Q false. Khi P false, implication vẫn true vì claim không nói gì về case đó. Điều này gọi là vacuous truth và thường gây ngạc nhiên.

Rule “nếu learner có certificate thì learner đã pass” không bị vi phạm bởi learner không có certificate, dù họ pass hay fail. Nó chỉ bị vi phạm khi certificate có mà pass không có.

Implication không tự nói P gây ra Q. “Nếu số chia hết cho 4 thì số chẵn” là quan hệ logic, không phải causal process. Trong data, `country = VN → timezone gần UTC+7` có thể sai vì domain/exception; business rules cần được xác minh chứ không chỉ viết thành implication.

## 4. Converse, inverse và contrapositive

Từ `P → Q`:

```text
Converse:       Q → P
Inverse:       ¬P → ¬Q
Contrapositive: ¬Q → ¬P
```

Chỉ contrapositive tương đương claim gốc. Nếu “admin thì có quyền xem report”, không suy ra “có quyền xem report thì là admin”; analyst cũng có thể có quyền. Đây là lỗi converse phổ biến trong authorization và classification.

Hai propositions tương đương khi cả `P → Q` và `Q → P` đúng, viết `P ↔ Q`. Equivalence mạnh hơn one-way rule; schema/query optimizer hoặc refactoring chỉ được thay expression khi meaning hai chiều giữ.

## 5. Truth table như unit test cho expression

Truth table liệt kê mọi assignment cho variables. Hai variables có bốn rows; `n` variables có `2^n` rows. Với ít variables, đây là cách đáng tin để kiểm tra refactoring Boolean.

Ví dụ `¬(P ∧ Q)` và `¬P ∨ ¬Q`:

| P | Q | P ∧ Q | ¬(P ∧ Q) | ¬P ∨ ¬Q |
|---|---|---|---|---|
| F | F | F | T | T |
| F | T | F | T | T |
| T | F | F | T | T |
| T | T | T | F | F |

Hai cột cuối giống nhau, nên expressions tương đương. Đây là De Morgan's law. Nó đặc biệt hữu ích khi phủ định rule phức tạp.

Truth table tăng theo hàm mũ, nên không dùng thủ công cho hàng chục flags. Khi rule lớn đến mức đó, vấn đề thường cần decision table, state machine hoặc domain model thay Boolean expression dài.

## 6. De Morgan và phủ định đúng phạm vi

Phủ định `P AND Q` thành `NOT P OR NOT Q`. Phủ định `P OR Q` thành `NOT P AND NOT Q`. Lỗi đổi dấu mà không đổi connector tạo authorization bug.

Nếu eligible nghĩa `active AND paid`, not eligible là `not active OR not paid`. Không phải `not active AND not paid`; learner thiếu một điều kiện đã không đủ eligibility.

Phủ định câu có từ “mọi” và “tồn tại” cũng đổi quantifier. “Không phải mọi course đều có seat” nghĩa tồn tại ít nhất một course không có seat, không nghĩa mọi course đều hết seat.

## 7. Predicate và domain

Predicate là proposition phụ thuộc biến, như `Completed(learner, course)`. Nó chưa true/false cho tới khi biến nhận value. Domain nói biến được lấy từ đâu.

Eligibility có thể viết:

```text
Eligible(l, c) = Active(l)
                 ∧ ∀p (Prerequisite(p, c) → Completed(l, p))
                 ∧ SeatsAvailable(c)
```

Đọc thành câu: learner `l` eligible cho course `c` khi account active, với mọi course `p`, nếu `p` là prerequisite của `c` thì learner đã hoàn thành `p`, và course còn chỗ.

Formula chỉ rõ hơn khi predicates có definition. `Completed` nghĩa pass hay chỉ xem hết video? Logic không thay domain decision.

## 8. Universal và existential quantifiers

`∀x ∈ D, P(x)` nghĩa P đúng với mọi x trong D. Để bác bỏ, chỉ cần một counterexample. `∃x ∈ D, P(x)` nghĩa có ít nhất một x; để chứng minh, có thể đưa witness cụ thể.

Thứ tự quantifier thay meaning:

```text
∀ learner, ∃ course: Likes(learner, course)
```

Mỗi learner thích ít nhất một course; có thể là course khác nhau.

```text
∃ course, ∀ learner: Likes(learner, course)
```

Có một course được mọi learner thích. Claim thứ hai mạnh hơn rất nhiều.

Trong API/query, scope lỗi tương tự xuất hiện khi filter được áp dụng trước hoặc sau grouping, hoặc permission “user có access tới một project” bị nhầm thành “có access tới project đang yêu cầu”.

## 9. Vacuous truth và empty collection

Universal statement trên empty set được coi là true vì không có counterexample. `all([])` trong Python trả `True`; `any([])` trả `False`. Điều này nhất quán với identity của AND/OR nhưng có thể không khớp business intent.

Nếu course không có prerequisites, “learner đã hoàn thành mọi prerequisite” true là hợp lý. Nếu checkout cart rỗng, “mọi item đều còn hàng” true không có nghĩa checkout nên được phép; cần thêm condition cart non-empty.

Đừng sửa logic toán; hãy thêm missing domain rule. Empty behavior cần được viết rõ trong specification và tests.

## 10. Tautology, contradiction và satisfiability

Tautology true dưới mọi assignment, như `P ∨ ¬P`. Contradiction luôn false, như `P ∧ ¬P`. Một formula satisfiable nếu có ít nhất một assignment làm nó true.

Configuration rules có thể mâu thuẫn: service yêu cầu region vừa phải EU vừa không thuộc EU. SAT solvers giải phiên bản lớn của câu hỏi có assignment thỏa constraints không. Type checking, dependency resolution và scheduling cũng liên quan constraint satisfaction.

Core track không đi vào SAT algorithms. Điều cần giữ là trước khi tìm “best configuration”, phải chắc constraints không contradiction và ít nhất có solution.

## 11. Logic trong code và tests

Guard clauses làm implication nhìn rõ: nếu precondition không thỏa, return/reject sớm; phần sau chạy dưới assumptions đã thu hẹp. Type narrowing cũng là reasoning: sau `if x is None: return`, nhánh sau biết `x` không None.

Decision table phù hợp rule có vài conditions và outcomes. Mỗi row là một case; coverage giúp thấy combination bị bỏ. Property-based test có thể sinh combinations, nhưng oracle/property vẫn phải được định nghĩa bằng logic đúng.

Quá nhiều Boolean flags thường tạo states vô nghĩa, ví dụ `is_pending=true` và `is_completed=true`. Một enum/state machine biểu diễn exactly-one state tốt hơn hai flags độc lập.

## 12. Mini practice

Viết eligibility rule cho Learning Platform gồm account active, prerequisites, seat và staff override. Nói rõ override bỏ qua condition nào, không chỉ thêm `OR is_staff` ở cuối mà chưa xét security meaning. Lập truth table cho ba conditions cốt lõi hoặc decision table nếu có nhiều outcomes.

Phủ định chính xác các câu: “mọi prerequisite đã hoàn thành”, “có ít nhất một course đang mở”, và “nếu payment thành công thì enrollment được tạo”. Với implication cuối, viết converse và một counterexample cho converse nếu nó không đúng.

Bài đạt khi quantifier có domain, parentheses rõ, empty prerequisite có behavior có chủ đích và rule tự nhiên khớp expression.

## Checkpoint

Bạn sẵn sàng sang Module 3 khi đọc được proposition, predicate, implication và quantifier thành câu; không nhầm converse với contrapositive; và phủ định đúng `and`/`or`, `for all`/`exists`. Mental model cần giữ: logic làm rule kiểm tra được, nhưng definition của predicates vẫn là trách nhiệm domain.
