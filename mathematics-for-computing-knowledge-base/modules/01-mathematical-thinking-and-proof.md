# 1. Mathematical Thinking and Proof — Nói chính xác điều gì đúng và vì sao

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** Lập trình cơ bản  
> **Status:** Reviewed

## Overview

Trong lập trình, một function chạy đúng với vài input chưa chứng minh nó đúng với mọi input hợp lệ. Mathematical reasoning giúp tách điều ta quan sát khỏi điều ta có thể khẳng định. Nó không yêu cầu viết dài hoặc dùng ký hiệu khó; nó yêu cầu domain, assumption và từng bước suy luận phải nhìn thấy.

Một proof giống một code review cho claim. Người đọc cần kiểm tra mỗi bước dựa trên definition hoặc fact nào, không phải tin vào trực giác của tác giả.

## 1. Object, definition và claim

Một bài toán có **objects** đang nói tới, **definitions** quy định meaning và **claims** nói điều được cho là đúng. Ví dụ, với integers, số `n` là even nếu tồn tại integer `k` sao cho `n = 2k`. Claim “tổng hai số chẵn là số chẵn” chỉ có nghĩa nhờ definition này.

Trong software, “user active” cũng cần definition: đã đăng nhập trong 30 ngày, còn subscription hay không bị khóa? Nếu definition đổi, query và metric đổi. Toán không sửa domain definition kém; nó làm sự mơ hồ lộ ra.

Mỗi claim nên có dạng gần như:

```text
Với mọi object trong domain D,
nếu assumptions A đúng,
thì conclusion C đúng.
```

Domain bị bỏ quên là nguồn lỗi phổ biến. `x / x = 1` đúng với mọi real `x ≠ 0`, không đúng tại `x = 0`. “Mảng có maximum” cần mảng hữu hạn và không rỗng.

## 2. Example giúp hiểu, không thay proof

Thử `2 + 4 = 6` và `-2 + 8 = 6` giúp ta đoán claim về số chẵn. Nhưng dù thử một triệu cặp, vẫn còn vô hạn cặp chưa thử. Example cung cấp intuition và phát hiện lỗi; proof bao phủ toàn domain bằng structure chung.

Ngược lại, một **counterexample** đủ bác bỏ universal claim. Claim “mọi số nguyên tố đều lẻ” sai vì `2` là số nguyên tố và chẵn. Khi review algorithm, hãy tích cực tìm empty input, duplicate, negative, boundary và overflow như counterexamples tiềm năng.

Property-based testing nằm giữa example và proof: nó sinh nhiều input theo property và tìm counterexample thực tế. Nó rất hữu ích nhưng không chứng minh property nếu input space chưa được bao phủ hoàn toàn.

## 3. Direct proof

Direct proof bắt đầu từ assumptions rồi dùng definitions/facts để tới conclusion. Với claim tổng hai số chẵn là chẵn:

Giả sử `a` và `b` chẵn. Theo definition, tồn tại integers `m`, `n` sao cho `a = 2m`, `b = 2n`. Khi đó:

```text
a + b = 2m + 2n = 2(m + n)
```

Vì `m + n` vẫn là integer, `a + b` có dạng hai lần một integer, nên chẵn. Proof ngắn nhưng mỗi bước có lý do.

Trong computing, direct proof thường trace data transformation. Nếu input thỏa invariant, operation bảo toàn invariant, output tiếp tục thỏa. Type system cũng mang tinh thần này: từ type assumptions và typing rules, compiler kết luận expression hợp lệ.

## 4. Proof by contrapositive

Implication “nếu P thì Q” tương đương “nếu không Q thì không P”. Đôi khi chiều sau dễ chứng minh hơn. Claim: nếu `n²` chẵn thì `n` chẵn. Contrapositive là nếu `n` lẻ thì `n²` lẻ.

Giả sử `n = 2k + 1`. Khi đó:

```text
n² = (2k + 1)²
   = 4k² + 4k + 1
   = 2(2k² + 2k) + 1
```

Nó có dạng `2t + 1`, nên lẻ. Vì contrapositive đúng, claim gốc đúng.

Đừng nhầm contrapositive với converse. Từ “nếu request authenticated thì có user identity” không tự suy ra “nếu có user identity field thì request authenticated”; field có thể bị client giả mạo.

## 5. Proof by contradiction

Contradiction giả sử claim cần chứng minh là sai, rồi cho thấy giả sử đó dẫn tới điều không thể cùng đúng. Kỹ thuật này hữu ích cho non-existence hoặc uniqueness, nhưng đôi khi bị lạm dụng làm proof khó đọc hơn direct route.

Ví dụ graph prerequisite hữu hạn không có cycle phải có ít nhất một course không phụ thuộc course nào trong graph. Giả sử mọi course đều có prerequisite. Bắt đầu từ một course và liên tục đi theo prerequisite. Vì số course hữu hạn, cuối cùng phải gặp lại course đã đi qua, tạo cycle, trái assumption. Vậy phải có một course nguồn.

Điểm quan trọng là chỉ rõ contradiction với assumption nào; không kết thúc bằng “vô lý” mà không nói điều gì xung đột.

## 6. Proof by cases

Khi domain tự nhiên chia thành các trường hợp bao phủ hết, chứng minh từng case. Giá trị absolute `|x|` có hai definition tùy `x ≥ 0` hoặc `x < 0`. Một authorization rule có thể chia owner, administrator và user khác.

Các cases phải **exhaustive** và không bỏ boundary. Chia `x > 0` và `x < 0` bỏ `x = 0`. Cases có thể overlap mà vẫn proof được, nhưng disjoint cases thường dễ kiểm tra hơn.

Trong code, exhaustive pattern matching trên enum là phiên bản thực dụng của case analysis. Default branch quá rộng có thể che case mới và làm reasoning mất chính xác.

## 7. Necessary và sufficient conditions

P là **sufficient** cho Q nếu P đúng thì Q đúng. P là **necessary** cho Q nếu Q không thể đúng khi thiếu P, tức Q → P. “Biết password đúng” có thể cần thiết cho một login flow cũ nhưng chưa đủ nếu account bị khóa. “Đã hoàn thành mọi prerequisite” cần thiết để eligible, nhưng có thể chưa đủ nếu course đầy.

Từ “only if” thường gây nhầm. “Learner được cấp certificate only if đã pass assessment” nghĩa certificate → pass. Pass là necessary, chưa chắc sufficient vì có thể còn payment hoặc identity check.

Viết hai chiều riêng trước khi nói “if and only if”. Nhiều bug specification đến từ việc team nghĩ hai conditions tương đương trong khi mới có một implication.

## 8. Existence và uniqueness

Claim “có một output” có thể chỉ existence; “có đúng một output” thêm uniqueness. Function specification thường cần cả hai. Với lookup theo unique email, schema constraint giúp uniqueness. Không có constraint, code có thể nhận hai rows và assumption function-like bị phá.

Để chứng minh tồn tại, có thể xây một object cụ thể. Để chứng minh unique, giả sử có hai objects cùng thỏa rồi chỉ ra chúng phải bằng nhau. Trong optimization hoặc leader election, existence và uniqueness là hai câu hỏi khác; đừng gộp.

## 9. Proof và program correctness

Correctness luôn relative với specification. Một sorting function đúng nếu output là permutation của input và theo order đã định. Chỉ kiểm tra output sorted chưa đủ: function trả empty list luôn sorted nhưng làm mất data.

Ta thường tách **partial correctness** và termination. Partial correctness nói nếu program kết thúc thì output đúng. Termination nói nó thực sự kết thúc. Cả hai tạo total correctness. Module 4 sẽ dùng invariant và decreasing measure để xử lý hai phần.

Proof không loại bỏ testing. Proof dựa trên model và assumptions; test kiểm tra implementation, integration và assumption có đúng trong hệ thống thật hay không. Hai công cụ bảo vệ các failure khác nhau.

## 10. Những lỗi reasoning phổ biến

Circular reasoning dùng conclusion như assumption. “Algorithm đúng vì nó luôn trả đáp án đúng” không cung cấp lý do. Generalization từ vài examples làm claim quá mạnh. Ambiguous term khiến hai người chứng minh hai điều khác nhau. Hidden assumption như input sorted làm binary search proof vô nghĩa nếu API không enforce/precondition nó.

Authority hoặc popularity cũng không phải proof. Một pattern phổ biến có thể không phù hợp context. Trong engineering decision, evidence thường không phải proof tuyệt đối, nhưng structure vẫn giống: context, assumptions, alternatives, observations và conclusion vừa với evidence.

## 11. Mini practice

Chọn hai claim sau hoặc tự tạo claim tương đương:

- Tổng của một số lẻ và một số chẵn là số lẻ.
- Nếu một integer chia hết cho 6 thì nó chia hết cho 2 và 3.
- Một list hữu hạn không rỗng có maximum nếu các phần tử có total order.

Với mỗi claim, viết domain, definitions, assumptions và direct proof. Sau đó viết một claim converse và kiểm tra nó bằng proof hoặc counterexample. Cuối cùng chọn một function quen thuộc, viết precondition/postcondition và nêu một input phá hidden assumption.

Bài đạt khi example không được trình bày như proof, từng biến có domain và conclusion không mạnh hơn assumptions.

## Checkpoint

Bạn sẵn sàng sang Module 2 khi phân biệt được definition, claim, example, counterexample và proof; biết necessary khác sufficient; và có thể trình bày direct proof từng bước. Mental model cần giữ: proof là chuỗi lý do có thể kiểm tra, không phải lời khẳng định nghe thuyết phục.
