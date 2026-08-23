# 1. AI Mental Model — Chọn đúng loại “thông minh”

> **Thời lượng:** 45–60 phút  
> **Prerequisites:** Biết lập trình cơ bản  
> **Status:** Reviewed

## Overview

Artificial Intelligence là tên cho một vùng bài toán, không phải một kỹ thuật duy nhất. Một bộ lọc spam dùng luật, một classifier học từ email cũ và một language model viết câu trả lời đều có thể được gọi là AI, nhưng chúng học, sai và được kiểm soát theo các cách rất khác nhau. Mental model quan trọng nhất của track là: đừng bắt đầu bằng “dùng model nào”; hãy bắt đầu bằng decision hoặc behavior mà hệ thống cần tạo ra.

Trong case support-ticket, đội vận hành có thể muốn phát hiện chữ “không đăng nhập được”, gợi ý queue phù hợp hoặc viết nháp câu trả lời từ handbook. Nhu cầu đầu có thể giải quyết bằng rule. Nhu cầu thứ hai phù hợp với supervised learning nếu đã có ticket được gắn nhãn. Nhu cầu thứ ba có thể dùng language model kết hợp retrieval. Gọi cả ba là “AI cho support” không sai, nhưng quá mơ hồ để thiết kế hoặc đánh giá.

## 1. Hệ thống AI thực sự làm gì?

Ở mức sản phẩm, một hệ thống AI nhận input, tạo output và tham gia vào một decision. Output có thể là một con số, một label, một ranking, một đoạn text hoặc một action. Model chỉ là component biến đổi input thành output; toàn bộ hệ thống còn có data collection, business rules, user interface, threshold, fallback và feedback loop. Một demo model tốt chưa chứng minh hệ thống tốt vì người dùng có thể không hiểu output, dữ liệu production có thể khác dữ liệu test, hoặc một lỗi hiếm có thể gây hậu quả lớn.

Hãy mô tả feature bằng một câu có cấu trúc tự nhiên: “Khi có input X, hệ thống tạo output Y để user Z đưa ra decision D; nếu không đủ chắc chắn thì làm F.” Với support-ticket: “Khi ticket mới xuất hiện, hệ thống gợi ý queue cho agent; nếu confidence thấp thì để agent chọn.” Câu này ngay lập tức tạo ra boundary, fallback và đối tượng nhận giá trị.

### Rule-based, Machine Learning và Generative AI

Rule-based system sử dụng logic do con người viết trực tiếp. Nó phù hợp khi điều kiện rõ, thay đổi ít và cần giải thích tuyệt đối. Nếu ticket chứa mã lỗi `PAYMENT-DECLINED`, route sang Billing là một rule tốt. Không có lý do phải train model để học lại một mapping đã biết chính xác.

Machine Learning không viết từng rule; nó tìm pattern từ examples. Trong supervised learning, mỗi example có input và target mong muốn. Classifier ticket có thể học rằng “thẻ bị trừ hai lần” thường thuộc Billing dù không có mã lỗi chuẩn. Đổi lại, behavior là xác suất chứ không tuyệt đối: model có thể sai, có thể học shortcut từ dữ liệu và có thể giảm chất lượng khi ngôn ngữ người dùng thay đổi.

Generative AI tạo ra nội dung mới theo context, chẳng hạn text, image hoặc code. Language model thường dự đoán token tiếp theo rất tốt, nhờ đó có thể tóm tắt hoặc viết câu trả lời tự nhiên. Nhưng mục tiêu “text có vẻ tiếp nối hợp lý” không đồng nhất với “fact đúng”. Khi dùng generative model cho câu hỏi nội bộ, ta cần cung cấp evidence, kiểm tra output và thiết kế quyền hành động thay vì chỉ viết prompt dài hơn.

| Cách tiếp cận | Strength | Failure thường gặp | Dùng khi |
|---|---|---|---|
| Rule | Dễ hiểu, deterministic | Nhiều exception, khó bao phủ input tự do | Logic rõ và ổn định |
| Classical ML | Học pattern từ labeled data | Sai phân phối, leakage, label kém | Prediction có examples lịch sử |
| Generative model | Linh hoạt với ngôn ngữ và nội dung | Hallucination, prompt injection, output biến động | Tóm tắt, soạn thảo, hỏi đáp có guardrail |

## 2. Learning khác với database lookup

Một database trả lại record đã lưu theo query. Một learned model tạo output bằng parameters đã được điều chỉnh từ nhiều examples. Khi model dự đoán ticket “không nhận được mã OTP” thuộc Account, nó thường không tìm một record giống hệt; nó sử dụng pattern đã nén vào parameters. Vì vậy model có thể generalize cho câu chưa từng thấy, nhưng cũng có thể suy luận sai mà không có exception rõ ràng.

Search và model thường bổ sung nhau. Search tìm evidence; model có thể hiểu câu hỏi, xếp hạng hoặc diễn đạt câu trả lời. Retrieval-Augmented Generation trong Module 7 chính là sự kết hợp này. Việc phân biệt giúp ta không kỳ vọng model “nhớ database” một cách chính xác và không dùng generation cho câu hỏi chỉ cần truy vấn có cấu trúc.

## 3. Prediction luôn đi cùng uncertainty

Nhiều model trả về score hoặc probability-like value, ví dụ `Billing: 0.72`. Con số này không tự động có nghĩa 72% các dự đoán như vậy là đúng; muốn hiểu như xác suất cần kiểm tra calibration. Tuy nhiên score vẫn hữu ích để ranking hoặc đặt threshold. Thay vì tự động route mọi ticket, team có thể auto-route khi score trên 0.9, gợi ý cho agent ở khoảng giữa và bỏ qua khi thấp.

Threshold là product decision, không chỉ là model setting. Nếu route nhầm Security ticket có hậu quả lớn, threshold của Security nên cao hơn queue thông thường. Cùng một model có thể tạo trải nghiệm khác hẳn tùy cách hệ thống dùng uncertainty. Đây là lý do evaluation phải gắn với cost và workflow thật.

## 4. Ví dụ minh họa: bắt đầu bằng rule

Đoạn Python sau tạo baseline route một số ticket có tín hiệu rõ. Nó không thông minh theo nghĩa học từ dữ liệu, nhưng transparent và có thể là solution đúng cho phiên bản đầu.

```python
def route_ticket(text: str) -> str:
    normalized = text.lower()

    billing_terms = ("thanh toán", "trừ tiền", "hóa đơn")
    account_terms = ("đăng nhập", "mật khẩu", "otp")

    if any(term in normalized for term in billing_terms):
        return "billing"
    if any(term in normalized for term in account_terms):
        return "account"
    return "manual-review"


examples = [
    "Tôi bị trừ tiền hai lần",
    "Không nhận được mã OTP",
    "Ứng dụng chạy rất chậm",
]

for ticket in examples:
    print(ticket, "->", route_ticket(ticket))
```

Baseline này lộ giới hạn nhanh: “tiền đã đi đâu?” có thể là Billing nhưng không match term; “tôi đăng nhập được nhưng muốn đổi hóa đơn” có hai tín hiệu và thứ tự rule quyết định output. Những lỗi này không phải lý do tự động nhảy sang Deep Learning. Trước hết, team có thể đo tần suất, thêm mã lỗi vào form hoặc cải thiện category. Model chỉ đáng có khi variation của input và giá trị cải thiện đủ lớn so với chi phí dữ liệu, đánh giá và vận hành.

## 5. Automation không đồng nghĩa autonomy

AI có thể gợi ý, soạn nháp, tự quyết định hoặc tự hành động. Bốn mức này có risk rất khác nhau. Một model đề xuất queue để agent bấm xác nhận dễ phục hồi hơn model tự khóa tài khoản hoặc hoàn tiền. Khi tăng autonomy, hệ thống cần permission boundary, audit log, idempotency, approval và rollback tương ứng.

Một cách khởi đầu an toàn là “copilot before autopilot”: model tạo proposal, con người xem và phản hồi. Feedback này còn cung cấp data để biết model sai ở đâu. Chỉ tự động hóa khi error distribution đã được hiểu và action có giới hạn rõ, không phải chỉ vì demo có vẻ thuyết phục.

## 6. Common traps

**Solution-first thinking** xuất hiện khi team đã chọn chatbot rồi mới tìm vấn đề. Kết quả thường là giao diện chat cho một workflow vốn cần form hoặc search. **Demo-driven evaluation** xảy ra khi vài prompt đẹp thay thế test set. **Anthropomorphism** khiến ta nói model “biết”, “hiểu” hoặc “muốn” rồi vô thức trao cho nó độ tin cậy chưa được đo. **AI everywhere** làm rule ổn định bị thay bằng component đắt, chậm và khó dự đoán.

Cách sửa chung là quay lại behavior quan sát được. Input nào được chấp nhận, output nào được phép, metric nào cho biết có ích, ai chịu tác động và fallback là gì? Những câu hỏi này giữ cuộc thảo luận ở mức engineering thay vì tranh luận tên model.

## 7. Mini practice

Chọn một ý tưởng quen thuộc như phát hiện giao dịch đáng ngờ, gợi ý khóa học hoặc tóm tắt cuộc họp. Viết problem statement trong tối đa năm câu, chỉ rõ user, input, output, thời điểm sử dụng và fallback. Sau đó đề xuất solution đơn giản nhất trong ba nhóm rule, predictive ML hoặc generative AI và giải thích vì sao hai nhóm còn lại chưa cần.

Bài làm đạt yêu cầu khi có ít nhất một failure có thể quan sát, một cách đo và một boundary cho automation. Không cần code. Nếu câu trả lời chỉ nói “dùng AI để tăng hiệu quả” thì vẫn còn quá rộng.

## Checkpoint

Bạn đã sẵn sàng sang Module 2 khi có thể nhìn một AI idea và tách được problem khỏi model. Hãy nhớ: model là một component dự đoán dưới uncertainty; product quyết định cách dùng dự đoán, và đôi khi direct rule là lựa chọn tốt hơn.

