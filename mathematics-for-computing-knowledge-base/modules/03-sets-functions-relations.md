# 3. Sets, Functions and Relations — Mô hình hóa object, mapping và connection

> **Thời lượng:** 65–80 phút  
> **Prerequisites:** [Module 2](02-logic-for-computing.md)  
> **Status:** Reviewed

## Overview

Set trả lời “những object nào nằm trong phạm vi”. Function trả lời “mỗi input được ánh xạ tới output nào”. Relation trả lời “những cặp object nào có liên hệ”. Ba khái niệm này xuất hiện trong types, database, APIs, state machines, graph và probability.

Mục tiêu không phải thao tác ký hiệu nhanh. Mục tiêu là chọn model làm rules và edge cases rõ hơn code hiện tại.

## 1. Set là collection theo membership

Set không quan tâm thứ tự và không chứa duplicate theo meaning toán học. `{A, B, A}` là cùng set với `{A, B}`. List thì khác: `[A, B, A]` có ba positions và thứ tự có ý nghĩa. Chọn nhầm abstraction dẫn tới bug hoặc constraint thiếu.

Ký hiệu `x ∈ A` nghĩa x là phần tử của A; `x ∉ A` nghĩa không thuộc. Set có thể được liệt kê `{1, 2, 3}` hoặc mô tả bằng predicate:

```text
ActiveLearners = { l ∈ Learners | Active(l) }
```

Đọc là tập learners `l` sao cho `Active(l)` đúng. Database query `WHERE active = true` tạo một result set gần với ý tưởng này, dù SQL result còn có row order/duplicates tùy query.

## 2. Empty set, finite set và cardinality

Empty set `∅` không có phần tử. Nó khác set chứa empty set: `∅` có size 0, còn `{∅}` có size 1. Trong code, empty collection khác collection chứa một empty collection.

Cardinality `|A|` là số phần tử với finite set. Nếu có 120 learners khác nhau, `|Learners| = 120`. Khi dữ liệu có duplicate records, cardinality của identities có thể khác row count; definition object identity cần rõ.

Infinite sets như integers vẫn là sets nhưng không có finite cardinality. Core examples chủ yếu finite vì computing storage hữu hạn, nhưng algorithms thường được phân tích cho mọi input size `n` không bị giới hạn trước.

## 3. Subset và equality

`A ⊆ B` nghĩa mọi phần tử A cũng thuộc B. A có thể bằng B. `A ⊂ B` đôi khi được dùng cho proper subset nhưng sách khác nhau về ký hiệu; hãy nêu convention. Hai sets bằng nhau khi chứa đúng cùng phần tử, thường chứng minh bằng hai chiều `A ⊆ B` và `B ⊆ A`.

Membership và subset khác loại. Nếu `Course` là set courses, `c ∈ Course`; nếu `AdvancedCourses` là một set courses, `AdvancedCourses ⊆ Course`. Viết `AdvancedCourses ∈ Course` sẽ coi cả collection như một course.

Type hierarchy có nét giống subset nhưng không đồng nhất hoàn toàn vì object behavior/subtyping có rules thêm. Đừng dùng set notation để bỏ qua runtime semantics.

## 4. Union, intersection, difference và complement

`A ∪ B` chứa phần tử ở A hoặc B. `A ∩ B` chứa phần tử ở cả hai. `A \ B` chứa phần tử ở A nhưng không ở B. Complement `Aᶜ` chỉ có nghĩa khi universe U rõ: `U \ A`.

Với `CompletedCourses(l)` và `RequiredCourses(program)`, phần còn thiếu là:

```text
RequiredCourses(program) \ CompletedCourses(l)
```

Learner đủ requirement khi difference này empty, hoặc tương đương required set là subset completed set. Hai formulations giúp implementation và test khác nhau.

De Morgan cũng áp dụng sets: complement của union bằng intersection của complements. Nó nối trực tiếp với logic vì membership trong set operation được định nghĩa bằng `and`, `or`, `not`.

## 5. Cartesian product và records

`A × B` là set mọi ordered pairs `(a, b)` với `a ∈ A`, `b ∈ B`. Nếu A có 3 phần tử và B có 4, product có 12 pairs. Ordered pair phân biệt `(a, b)` với `(b, a)` trừ khi values tình cờ bằng.

Enrollment có thể là subset của `Learners × Courses`: chỉ một số learner-course pairs thực sự enrolled. Nếu enrollment còn status/time, model là records/triples hoặc relation có attributes. Database table enrollment chính là representation của relation có data thêm.

Product lớn rất nhanh. Join hai tables không filter có thể tạo Cartesian product; hiểu cardinality giúp nhận ra vì sao missing join condition làm query nổ.

## 6. Power set và state explosion

Power set `P(A)` là set của mọi subsets của A. Nếu `|A| = n`, power set có `2^n` phần tử. Với ba feature flags, có tám combinations; với 20 flags, hơn một triệu.

Điều này giải thích state explosion trong permissions, configuration và testing. Không phải mọi combination đều hợp lệ, nên domain model hoặc constraints cần loại states vô nghĩa. Thay nhiều flags bằng một state machine có thể giảm state space.

Power set không phải thứ phải enumerate. Nó là cách thấy complexity của “chọn bất kỳ subset features” trước khi thiết kế exhaustive tests.

## 7. Function là mapping có một output cho mỗi input

Function `f: A → B` ánh xạ mỗi input trong domain A tới đúng một output trong codomain B. “Đúng một” không cấm hai inputs cùng output; nó cấm một input có hai outputs trong cùng function/context.

`InstructorOf: Course → Instructor` chỉ là total function nếu mỗi course có đúng một instructor. Nếu course có nhiều instructors, đây là relation hoặc function tới set `Course → P(Instructor)`. Nếu course draft chưa có instructor, function có thể partial hoặc output type là `Optional[Instructor]`.

Codomain là set output được khai báo; range/image là values thực sự đạt tới. Function `square: Integer → Integer` có codomain integers nhưng range chỉ non-negative integers.

## 8. Injective, surjective và bijective

Function injective (one-to-one) nếu inputs khác nhau không map cùng output. Email owner mapping có thể injective khi unique constraint giữ. Surjective (onto) nếu mọi value trong codomain được ít nhất một input map tới. Bijective nếu vừa injective vừa surjective, tạo pairing một-một và có inverse.

Database ID mapping row → ID là bijection trong table nếu mỗi row có unique ID và mỗi stored ID thuộc một row. Hash function nói chung không injective vì input space lớn hơn output space; collision là không thể tránh theo pigeonhole principle.

Không cần gán các nhãn này cho mọi function. Dùng chúng khi uniqueness, coverage hoặc invertibility là requirement.

## 9. Composition và inverse

Nếu `f: A → B` và `g: B → C`, composition `g ∘ f: A → C` nghĩa áp dụng f rồi g. Order đọc từ phải sang trái. Data pipeline, function composition và transformations đều theo cấu trúc này.

Composition chỉ hợp lệ khi output type của f phù hợp input của g. Type system bắt được nhiều mismatch mà toán gọi là domain/codomain không khớp.

Inverse function `f⁻¹` chỉ tồn tại trên toàn codomain khi f bijective. Parsing rồi serializing không luôn inverse nếu format bỏ whitespace/order. Hashing không có inverse chỉ vì ta muốn tìm input. “Undo” trong software thường cần lưu history vì operation không invertible.

## 10. Relation là set các pairs

Binary relation R từ A tới B là subset của `A × B`. `Prerequisite(p, c)` nghĩa pair `(p, c)` thuộc relation. Relation có thể many-to-many và không cần output duy nhất.

Trên cùng set A, relation có properties hữu ích. **Reflexive** nếu mọi x liên hệ chính nó. **Symmetric** nếu xRy kéo theo yRx. **Antisymmetric** nếu xRy và yRx kéo theo x = y. **Transitive** nếu xRy và yRz kéo theo xRz.

“Cùng nhóm học” có thể equivalence-like nếu được model đúng. “Là prerequisite trực tiếp” không transitive, nhưng “là prerequisite trực tiếp hoặc gián tiếp” là transitive closure của relation trực tiếp.

## 11. Equivalence relation và partition

Equivalence relation reflexive, symmetric và transitive. Nó nhóm objects thành equivalence classes. Quan hệ “có cùng normalized email” có thể nhóm records bị duplicate; relation “có cùng remainder modulo 3” chia integers thành ba classes.

Các equivalence classes tạo partition: không overlap và union bao phủ set. Trong software, deduplication, canonicalization và union-find dùng ý tưởng này.

Similarity không tự là equivalence. “Khoảng cách dưới 1” có thể symmetric nhưng không transitive: A gần B, B gần C, A có thể không gần C. Dùng clustering như partition cần hiểu algorithm thêm assumptions.

## 12. Order relation

Partial order reflexive, antisymmetric và transitive. Một số pairs không so sánh được. Subset `⊆` là partial order: `{a}` và `{b}` không cái nào subset cái kia. Quan hệ prerequisite trực tiếp tạo một directed graph; khi graph không có cycle, quan hệ “là prerequisite trực tiếp hoặc gián tiếp” mới tạo một strict partial order.

Total order yêu cầu mọi pair so sánh được, như integers với `≤`. Sorting API cần comparator tạo order nhất quán; comparator vi phạm transitivity có thể làm kết quả không xác định hoặc algorithm sai.

Topological order của prerequisite graph không nhất thiết unique. Hai courses độc lập có thể đổi vị trí. Đừng yêu cầu một thứ tự duy nhất khi relation chỉ định partial constraints.

## 13. Mini practice

Mô hình hóa Learning Platform bằng sets `L`, `C`, `I`; function hoặc relation cho instructor, enrollment và prerequisite. Với mỗi mapping, nói total/partial, one-to-one/many-to-many và edge case empty.

Viết set expression cho courses learner còn thiếu, courses vừa thuộc track backend vừa available, và learners enrolled nhưng chưa completed. Sau đó kiểm tra prerequisite trực tiếp có reflexive, symmetric, antisymmetric hay transitive không; phân biệt nó với transitive closure.

Bài đạt khi function chỉ được dùng ở nơi mỗi input có đúng một output, universe của complement rõ và duplicate/order không bị vô tình bỏ qua.

## Checkpoint

Bạn sẵn sàng sang Module 4 khi phân biệt membership/subset, list/set, function/relation, codomain/range và partial/total. Mental model cần giữ: representation toán học là một contract; chọn sai type của object làm reasoning phía sau sai ngay từ nền.
