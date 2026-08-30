# 5. Counting and Probability — Biết có bao nhiêu khả năng trước khi nói về cơ hội

> **Thời lượng:** 75–90 phút  
> **Prerequisites:** [Module 4](04-induction-recursion-invariants.md)  
> **Status:** Reviewed

## Overview

Probability bắt đầu bằng sample space: những outcomes nào có thể xảy ra. Counting sai không gian này làm mọi công thức phía sau sai. Combinatorics cung cấp cách đếm mà không enumerate từng trường hợp; probability gắn trọng số cho events trong không gian đó.

Trong computing, chúng xuất hiện ở test combinations, collision, randomized algorithms, reliability, security token và experiment. Mục tiêu là model uncertainty có assumption rõ, không tạo cảm giác chắc chắn giả.

## 1. Sum rule và product rule

Nếu một lựa chọn thuộc một trong các nhóm rời nhau, tổng số cách là tổng cardinalities. Learning Platform có 3 beginner courses và 4 intermediate courses; chọn đúng một course từ hai nhóm có `3 + 4 = 7` cách.

Nếu một process có nhiều stages và mỗi choice ở stage trước ghép được với mỗi choice stage sau, dùng product. Chọn 3 course và 2 time slots có `3 × 2 = 6` schedules.

Sum rule yêu cầu cases không overlap hoặc phải điều chỉnh overlap. Product rule yêu cầu số choices ở mỗi branch như nhau; nếu khác, tách cases hoặc dùng tree. Vẽ vài outcomes nhỏ thường ngăn áp công thức máy móc.

## 2. Permutation: thứ tự có ý nghĩa

Sắp xếp n objects khác nhau có `n!` cách, với `n! = n(n-1)⋯1` và `0! = 1`. Chọn và sắp `k` objects từ n có:

```text
P(n, k) = n! / (n-k)!
```

Nếu chọn 3 courses khác nhau từ 8 để đặt vào ba tuần có thứ tự, có `8 × 7 × 6 = 336` plans.

Khi objects có duplicates, chia cho permutations không làm arrangement thay đổi. Chuỗi `AAB` có `3!/2! = 3` arrangements. Definition “objects khác nhau theo identity nào” phải rõ.

## 3. Combination: chọn group, không quan tâm thứ tự

Chọn k objects từ n mà order không quan trọng:

```text
C(n, k) = n! / (k!(n-k)!)
```

Vì mỗi group k phần tử bị đếm `k!` lần nếu dùng permutation, ta chia `k!`. Chọn 3 electives từ 8 có `C(8,3)=56` sets, không phải 336 schedules.

Câu hỏi đơn giản để chọn công thức: đổi thứ tự selected items có tạo outcome mới không? Nếu committee `{A,B}` giống `{B,A}`, combination. Nếu first/second place khác nhau, permutation.

## 4. Inclusion–exclusion và double-count

Khi sets overlap:

```text
|A ∪ B| = |A| + |B| - |A ∩ B|
```

Nếu 40 learners học Python, 30 học SQL và 12 học cả hai, số học ít nhất một course là `40 + 30 - 12 = 58`. Cộng thẳng 70 đếm nhóm giao hai lần.

Với nhiều sets, formula mở rộng phức tạp nhanh. Trong thực tế, có thể dùng partition hoặc code query distinct. Điều quan trọng là nhận ra overlap, không cố nhớ formula dài.

Counting by complement đôi khi dễ hơn. Số passwords có ít nhất một digit bằng tổng passwords trừ passwords không có digit.

## 5. Pigeonhole principle

Nếu đặt nhiều hơn n objects vào n boxes, ít nhất một box chứa từ hai objects. Nguyên lý đơn giản nhưng cho kết luận mạnh. Hash function map input space lớn vào output space hữu hạn nên collision chắc chắn tồn tại; mục tiêu là làm collision hiếm/khó khai thác, không loại bỏ toán học bất khả.

Với 13 người và 12 tháng, ít nhất hai người cùng tháng sinh. Principle không nói cặp nào hoặc xác suất; nó là guarantee existence.

Trong distributed IDs, số possible IDs, số generated IDs và selection process quyết định collision risk. Chỉ nói “UUID rất lớn” là intuition; counting cho order of magnitude.

## 6. Sample space, outcome và event

Experiment ngẫu nhiên có sample space `Ω`, set mọi outcomes. Event là subset của Ω. Tung đồng xu hai lần có:

```text
Ω = {HH, HT, TH, TT}
```

Event “đúng một mặt ngửa” là `{HT, TH}`. Nếu coin fair và trials independent, mỗi outcome probability `1/4`, event probability `2/4 = 1/2`.

Outcomes không phải lúc nào equally likely. Chọn ngẫu nhiên một learner rồi nhìn course họ học khác chọn ngẫu nhiên một enrollment row: learner học nhiều courses được trọng số cao hơn trong cách thứ hai. Sampling process là một phần sample space.

## 7. Probability axioms

Probability function gán mỗi event số từ 0 tới 1. `P(Ω)=1`. Với disjoint events A, B, `P(A ∪ B)=P(A)+P(B)`. Từ đó có complement:

```text
P(not A) = 1 - P(A)
```

Với finite equally likely outcomes:

```text
P(A) = |A| / |Ω|
```

Formula này chỉ dùng khi outcomes thật sự equally likely. Dữ liệu lịch sử 80% completion không tự là probability cho learner mới nếu population/process đã đổi.

Probability 0 không luôn nghĩa impossible trong continuous models; probability 1 không luôn logic certainty. Core finite examples tránh nuance này, nhưng đừng đồng nhất probability với proof.

## 8. Conditional probability

`P(A | B)` là probability A khi biết B đã xảy ra:

```text
P(A | B) = P(A ∩ B) / P(B), với P(B) > 0
```

Meaning quan trọng hơn formula: universe được thu hẹp về B rồi hỏi phần nào cũng thuộc A.

Ví dụ 100 learners: 60 hoàn thành prerequisite, 48 trong số đó hoàn thành course; 40 không hoàn thành prerequisite, 12 trong số đó vẫn hoàn thành course. Khi biết prerequisite completed:

```text
P(complete course | prerequisite complete) = 48/60 = 0.8
```

Đây là association trong data, chưa chứng minh prerequisite gây completion. Learners khác nhau về kinh nghiệm/motivation.

## 9. Independence

A và B independent nếu biết B không đổi probability A:

```text
P(A ∩ B) = P(A)P(B)
```

Mutually exclusive khác independence. Hai events không thể cùng xảy ra có intersection 0; nếu cả hai có probability dương, chúng không independent vì biết A xảy ra làm B impossible.

Independence thường là assumption mạnh. Hai server cùng region không independent trước region outage. Hai retries tới cùng dependency đang quá tải không independent. Nhân probabilities chỉ hợp lệ khi model independence có lý do.

Conditional independence cho phép independence sau khi biết một variable khác; đây là nền của nhiều probabilistic models nhưng core chỉ cần nhận ra context có thể tạo dependency.

## 10. Bayes' theorem và base rate

Bayes đảo conditional direction:

```text
P(A | B) = P(B | A)P(A) / P(B)
```

Giả sử 1% submissions gian lận. Detector bắt đúng 90% gian lận nhưng false-positive 5% submissions bình thường. Trong 10,000 submissions: khoảng 100 gian lận, detector flag 90; 9,900 bình thường, detector flag 495. Trong 585 flags, chỉ 90 thật sự gian lận, khoảng 15.4%.

Accuracy “90% bắt được fraud” không trả lời `P(fraud | flagged)`. Base rate nhỏ làm false positives áp đảo. Security alert, medical test và spam filter đều cần đọc đúng direction.

Bayes không biến estimate kém thành sự thật. Prior và likelihood phải có nguồn/assumption.

## 11. Random variable và expectation

Random variable là function từ outcomes tới số. `X` có thể là số courses learner hoàn thành trong tháng. Distribution nói mỗi value có probability nào.

Expected value là weighted average dài hạn:

```text
E[X] = Σ x · P(X=x)
```

Expected value không nhất thiết là outcome có thể xảy ra. Expected số lỗi 0.3 mỗi deploy không nghĩa deploy có 0.3 lỗi; qua nhiều deploy, average tiến gần 0.3 theo model.

Linearity of expectation `E[X+Y]=E[X]+E[Y]` giữ cả khi X, Y không independent. Điều này hữu ích để đếm expected events bằng indicator variables.

## 12. Variance và risk intuition

Hai distributions có cùng mean nhưng mức biến động khác. Variance đo expected squared distance khỏi mean; standard deviation là căn variance và cùng unit với X. Module 6 dùng chúng cho data.

Expected cost thấp không đủ nếu tail loss rất lớn. Một operation 99.9% nhanh nhưng 0.1% treo vô hạn cần timeout. Reliability và latency cần percentile/distribution, không chỉ average.

Probability model nên nói horizon và repeated process. “Có 1% chance lỗi mỗi request” dưới independence tạo xác suất ít nhất một lỗi tăng khi requests nhiều.

## 13. Mini practice

Giải ba bài: chọn 4 electives từ 10 khi order không quan trọng; xếp 4 course vào bốn tuần khi order quan trọng; đếm learners học Python hoặc SQL khi hai nhóm overlap. Viết lý do chọn combination, permutation hoặc inclusion–exclusion trước khi tính.

Tạo bảng 2×2 cho `prerequisite completed` và `course completed`. Tính hai conditional directions, kiểm tra independence và giải thích vì sao association chưa là causation. Sau đó tạo detector example với base rate khác để thấy positive predictive value đổi.

Bài đạt khi sample space rõ, outcomes equally likely chỉ được giả sử có lý do và mọi phép nhân probability nêu independence.

## Checkpoint

Bạn sẵn sàng sang Module 6 khi đếm được selections theo order/overlap, phân biệt mutually exclusive với independent, đọc conditional probability như thu hẹp sample space và dùng Bayes không đảo nhầm điều kiện. Mental model cần giữ: probability là kết luận bên trong một model; model chỉ đáng tin bằng assumptions và data của nó.
