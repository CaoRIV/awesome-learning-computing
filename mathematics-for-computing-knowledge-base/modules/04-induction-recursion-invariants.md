# 4. Induction, Recursion and Invariants — Chứng minh điều lặp lại luôn đúng

> **Thời lượng:** 70–85 phút  
> **Prerequisites:** [Module 3](03-sets-functions-relations.md)  
> **Status:** Reviewed

## Overview

Programs dùng loops và recursion để xử lý input có kích thước bất kỳ. Ta không thể test từng `n`. Mathematical induction chứng minh statement cho mọi size bằng cách nối trường hợp nhỏ với trường hợp tiếp theo. Loop invariant dùng cùng tinh thần để chứng minh state luôn giữ một property trong quá trình lặp.

Induction không phải câu thần chú “đúng với n, nên đúng với n+1”. Bước nối đó chính là phần phải chứng minh.

## 1. Sequence, index và recursive definition

Sequence là function từ index tới value. Với zero-based array, index domain có thể `{0, 1, ⋯, n-1}`. Viết domain rõ giúp tránh off-by-one.

Một sequence có thể định nghĩa trực tiếp, như `a_n = 2n`, hoặc recursively bằng base case và rule:

```text
F₀ = 0
F₁ = 1
Fₙ = Fₙ₋₁ + Fₙ₋₂, với n ≥ 2
```

Recursive definition phải có base cases đủ và tiến về chúng. Thiếu `F₁` làm rule với hai previous terms không khởi động được. Program recursion cũng vậy.

## 2. Summation notation có meaning

`Σ` rút gọn một tổng có pattern:

```text
Σ từ i=1 tới n của i = 1 + 2 + ⋯ + n
```

Index variable là local/bound; đổi `i` thành `k` không đổi sum. Lower/upper bounds là phần của definition. Tổng trên empty range thường là 0, identity của addition, giúp formulas và loops xử lý empty input nhất quán.

Công thức `1 + 2 + ⋯ + n = n(n+1)/2` hữu ích không chỉ để tính nhanh; nó là claim có thể chứng minh bằng induction hoặc pairing. Trong complexity, sums mô tả total work của nested/gradually shrinking loops.

## 3. Principle of mathematical induction

Để chứng minh `P(n)` đúng cho mọi integer `n ≥ n₀`, ta cần:

1. Base case: chứng minh `P(n₀)`.
2. Inductive step: với arbitrary `k ≥ n₀`, giả sử `P(k)` đúng và từ đó chứng minh `P(k+1)`.

Inductive hypothesis chỉ được dùng cho `k`, không được giả sử luôn `P(k+1)`. `k` arbitrary nghĩa proof không dựa vào một số cụ thể.

Hình dung domino: base làm viên đầu ngã; step chứng minh mỗi viên đang ngã làm viên kế ngã. Chỉ base không đủ; chỉ step không có điểm bắt đầu cũng không đủ.

## 4. Ví dụ induction với tổng

Claim: với mọi `n ≥ 1`:

```text
1 + 2 + ⋯ + n = n(n + 1) / 2
```

Base `n = 1`: trái là 1, phải là `1×2/2 = 1`.

Inductive step: giả sử claim đúng tại `k`:

```text
1 + 2 + ⋯ + k = k(k + 1)/2
```

Tại `k+1`:

```text
1 + 2 + ⋯ + k + (k + 1)
= k(k + 1)/2 + (k + 1)
= (k + 1)(k + 2)/2
```

Đó chính là formula với `n = k+1`. Proof không “thay thử”; nó chứng minh chain cho arbitrary k.

## 5. Strong induction

Strong induction cho phép bước tại `k+1` giả sử tất cả cases từ base tới k đúng. Nó không mạnh hơn về những theorem có thể chứng minh, nhưng phù hợp recursive problem phụ thuộc nhiều size nhỏ.

Claim: mọi integer `n ≥ 2` có thể viết thành tích các số nguyên tố. Base 2 là prime. Với n > 2, nếu n prime thì xong. Nếu composite, `n = ab` với `2 ≤ a,b < n`. Theo inductive hypotheses, a và b đều phân tích thành primes, nên n cũng vậy.

Algorithms chia input thành subproblems kích thước khác nhau thường được chứng minh bằng strong induction. Mỗi recursive call phải thật sự nhỏ hơn theo measure đã chọn.

## 6. Structural induction

Data recursive như list/tree không luôn được index tự nhiên nhất bằng một số. Structural induction chứng minh property theo constructors của structure.

Với binary tree: base là empty tree hoặc leaf. Step giả sử property đúng cho left/right subtrees rồi chứng minh cho node ghép từ chúng. Ví dụ, recursive function count nodes trả đúng: empty trả 0; node trả `1 + count(left) + count(right)`. Nếu calls đúng trên subtrees, result đúng cho whole tree.

Đây là proof tự nhiên cho parsers, AST transformations, serializers và tree algorithms.

## 7. Proving recursive function correctness

Một recursive function cần ba câu trả lời: base cases đúng không, recursive result được kết hợp đúng không, và calls có tiến gần base để terminate không.

Ví dụ factorial cho `n ≥ 0`:

```python
def factorial(n: int) -> int:
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

Correctness theo induction: base trả `0! = 1`; giả sử call `factorial(k)` đúng, `factorial(k+1)` trả `(k+1) × k! = (k+1)!`. Termination vì non-negative n giảm 1 và cuối cùng tới 0.

Nếu API cho negative n, measure không tiến tới base; precondition phải reject. Proof thường tìm ra validation thiếu.

## 8. Loop invariant

Loop invariant là property đúng trước mỗi iteration. Proof có ba phần:

- Initialization: đúng trước iteration đầu.
- Maintenance: nếu đúng trước iteration, body giữ nó đúng cho iteration sau.
- Termination: khi loop dừng, invariant cùng exit condition suy ra postcondition.

Xét tìm maximum trong non-empty array:

```python
maximum = values[0]
for i in range(1, len(values)):
    if values[i] > maximum:
        maximum = values[i]
```

Invariant: trước iteration với index i, `maximum` là maximum của prefix `values[0:i]`. Initialization với i=1: prefix chỉ có `values[0]`. Maintenance: so phần tử i và cập nhật tạo maximum của prefix dài hơn. Khi loop kết thúc i=n, prefix là toàn array, nên result đúng.

## 9. Invariant không phải log statement

Invariant phải đủ mạnh để suy ra goal nhưng đủ đơn giản để giữ. “Program chưa crash” thường quá yếu. “Array đã sorted hoàn toàn” có thể chưa đúng giữa sorting loop. Invariant đúng thường mô tả phần đã xử lý và phần còn lại.

Binary search invariant có thể là: nếu target tồn tại, nó nằm trong search interval `[low, high]`. Mỗi comparison loại một phần nhưng không loại target. Exit interval empty suy ra target không tồn tại; match trả index hợp lệ.

Off-by-one thường đến từ interval convention không thống nhất. Chọn closed `[low, high]` hoặc half-open `[low, high)` rồi giữ invariant, update và exit condition cùng convention.

## 10. Termination và decreasing measure

Correct output không đủ nếu loop không dừng. Chọn variant/decreasing measure là non-negative quantity giảm nghiêm ngặt mỗi iteration. Binary search dùng interval size. Graph traversal dùng số unvisited reachable nodes nếu visited set đúng. Retry loop cần attempt budget/deadline.

Measure phải well-founded: không thể giảm mãi. Integers không âm phù hợp. Float có thể tiến gần zero mà không chạm do update, nên termination reasoning cần cẩn thận.

Distributed systems có loop chờ external state; mathematical termination có thể không guarantee nếu dependency không bao giờ đáp ứng. Timeout chuyển open-ended wait thành bounded behavior.

## 11. Recurrence nói cost tự gọi lại

Recurrence mô tả quantity qua input nhỏ hơn. Binary search work có dạng gần:

```text
T(n) = T(n/2) + constant
```

Sau mỗi step, size nửa đi, nên số steps khoảng số lần chia 2 tới 1, tức logarithmic. Merge sort có hai subproblems và merge work:

```text
T(n) = 2T(n/2) + linear work
```

Core module chỉ cần đọc recurrence từ algorithm. Formal Master Theorem và asymptotic analysis sâu thuộc Algorithms track. Đừng dùng recurrence nếu iterative counting đơn giản hơn.

## 12. Common proof failures

Base case sai domain, chỉ chứng minh `n=1` khi recurrence cần cả `n=0` và `n=1`, hoặc inductive step dùng claim hiện tại đều làm proof hỏng. Với loop, invariant được nêu sau body nhưng proof lại dùng trước body gây mismatch.

Một lỗi tinh tế là chứng minh partial correctness mà quên termination. Recursive graph traversal không visited set có thể đúng trên DAG nhưng lặp vô hạn trên cycle. Domain assumption “graph acyclic” phải explicit hoặc algorithm phải handle cycles.

## 13. Mini practice

Chứng minh bằng induction `1 + 3 + 5 + ⋯ + (2n-1) = n²` với `n ≥ 1`. Viết base, hypothesis và algebra từng bước. Sau đó chọn recursive sum/list-length function, chứng minh correctness và termination.

Viết loop invariant cho linear search hoặc tính prefix sum. Nêu initialization, maintenance và conclusion. Thử empty input để xem precondition hoặc identity value đã đúng chưa.

Bài đạt khi inductive step dùng arbitrary k, recursive calls nhỏ hơn và invariant đủ suy ra postcondition.

## Checkpoint

Bạn sẵn sàng sang Module 5 khi có thể viết induction hoàn chỉnh, dùng structural induction cho tree/list, và chứng minh loop bằng invariant cùng termination measure. Mental model cần giữ: induction và invariant cho phép một proof hữu hạn bao phủ behavior lặp trên input tùy ý.
