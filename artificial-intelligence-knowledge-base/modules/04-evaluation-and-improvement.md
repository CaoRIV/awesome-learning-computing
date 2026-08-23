# 4. Evaluation and Improvement — Đo điều có ý nghĩa

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 3](03-machine-learning-workflow.md)  
> **Status:** Reviewed

## Overview

Evaluation trả lời hai câu hỏi khác nhau. Câu hỏi model là “prediction có đúng trên data đại diện không?”. Câu hỏi sản phẩm là “prediction có cải thiện workflow với cost chấp nhận được không?”. Một classifier có 92% accuracy vẫn có thể vô dụng nếu 8% sai tập trung ở ticket Security, hoặc nếu agent mất nhiều thời gian sửa gợi ý hơn tự chọn.

Module này tập trung vào classification vì case xuyên suốt, nhưng mental model áp dụng rộng hơn: metric chỉ là phép nén nhiều behaviors thành một con số. Muốn cải thiện có chủ đích, ta phải mở số đó ra, nhìn confusion matrix, slices và từng lỗi đại diện.

## 1. Accuracy không sai, nhưng thường chưa đủ

Accuracy là tỷ lệ predictions đúng. Nếu 95% ticket là `normal` và 5% là `urgent`, model luôn đoán `normal` đạt 95% nhưng bỏ sót toàn bộ urgent. Vấn đề không nằm ở công thức accuracy; vấn đề là nó không phản ánh cost bất đối xứng và class imbalance.

Với một class quan tâm, **precision** hỏi: trong các items model dự đoán là class đó, bao nhiêu thật sự đúng? **Recall** hỏi: trong các items thật sự thuộc class đó, model tìm được bao nhiêu? Nếu tự động khóa giao dịch, false positive gây phiền lớn nên precision quan trọng. Nếu phát hiện tình huống an toàn khẩn cấp, bỏ sót nguy hiểm nên recall được ưu tiên, dù vẫn cần kiểm soát false alarm.

F1 là harmonic mean của precision và recall, hữu ích khi cần một số cân bằng nhưng không biết business cost. Nó không tự động là metric tốt nhất. Product requirement nên quyết định operating point, chẳng hạn “recall urgent ít nhất 95% trong khi precision ít nhất 70%”, thay vì tối đa hóa một số chung chung.

## 2. Confusion matrix kể câu chuyện class nào bị nhầm

```python
from sklearn.metrics import classification_report, confusion_matrix

actual = [
    "billing", "billing", "account", "account",
    "technical", "technical", "security", "security",
]
predicted = [
    "billing", "account", "account", "account",
    "technical", "billing", "account", "security",
]

labels = ["billing", "account", "technical", "security"]
print(confusion_matrix(actual, predicted, labels=labels))
print(classification_report(actual, predicted, labels=labels, zero_division=0))
```

Matrix cho thấy hướng nhầm, không chỉ số lượng sai. Security bị nhầm sang Account có thể nghiêm trọng hơn Technical bị nhầm sang Billing. Với multiclass routing, team nên review các cell có volume hoặc cost cao, sau đó đọc raw examples trong cell đó.

Report trên tám examples chỉ mang tính minh họa. Dataset thật cần support count và confidence interval hoặc ít nhất cảnh báo sample size. “100% recall trên 2 cases” không nên trình bày ngang hàng với “95% trên 20.000 cases”.

## 3. Threshold tạo trade-off

Classifier thường tạo score trước khi chọn label. Với binary urgent detection, giảm threshold làm model đánh dấu nhiều ticket hơn: recall thường tăng, precision có thể giảm. Không có threshold đúng ngoài context. Team cần chọn điểm dựa trên cost, capacity review và SLA.

Ví dụ, nếu agent chỉ có thể review 100 alerts mỗi ngày, threshold phải giữ volume trong capacity. Nếu incident nghiêm trọng vừa xảy ra, team có thể tạm hạ threshold để ưu tiên recall. Điều này cho thấy model behavior còn phụ thuộc operational policy; lưu model version mà không lưu threshold/config chưa đủ để tái hiện hệ thống.

Calibration kiểm tra score có phù hợp với frequency quan sát không. Trong một nhóm prediction khoảng 0.8, lý tưởng khoảng 80% đúng nếu score được diễn giải là probability. Calibration quan trọng khi score đi vào risk calculation hoặc UI. Ranking tốt không đảm bảo calibration tốt.

## 4. Error analysis là engine cải thiện

Lấy một sample prediction sai, không chỉ những lỗi dễ sửa, rồi gắn error tags. Ticket có thể sai vì label mơ hồ, thiếu context, typo, intent kép, product mới hoặc model shortcut. Sau 30–50 lỗi, đếm themes. Nếu phần lớn lỗi là label inconsistency, retrain model mạnh hơn không giải quyết root cause. Nếu lỗi tập trung ở tiếng Việt không dấu, data augmentation hoặc coverage phù hợp hơn.

Error analysis nên dẫn tới hypothesis có thể test. “Model chưa đủ thông minh” không test được. “Bigram representation không nhận ra biến thể không dấu; bổ sung examples không dấu sẽ tăng recall Account trên slice đó” là hypothesis. Chạy experiment mới trên validation, giữ test cuối không đụng tới.

Một bảng nhỏ là đủ:

| Input | Actual / Predicted | Error tag | Hành động tiếp theo |
|---|---|---|---|
| `ko nhan dc otp` | Account / Technical | Không dấu, viết tắt | Thêm coverage và normalization có kiểm soát |
| `đăng nhập được nhưng hóa đơn sai` | Billing / Account | Hai intents | Làm rõ taxonomy hoặc multi-label |
| `SEC-204` | Security / Account | Context nằm ở mã lỗi | Join metadata hợp lệ tại prediction time |

## 5. Slice evaluation tránh metric trung bình che lỗi

Slice là subset có ý nghĩa: language, platform, product version, customer tier, message length hoặc time period. Chọn slices từ domain risk và data coverage, không tạo hàng trăm dashboard vô mục đích. Nếu mobile release mới thay đổi từ vựng ticket, performance theo app version đáng đo. Nếu feature phục vụ nhiều ngôn ngữ, metric riêng từng language là bắt buộc.

Không nên chỉ săn slice có metric thấp rồi tuyên bố bias. Sample nhỏ có variance lớn. Hãy báo count, so sánh qua thời gian và điều tra pipeline. Slice evaluation là phương tiện phát hiện risk và tạo test, không phải nghi thức compliance.

## 6. Offline, online và human evaluation

Offline test nhanh, lặp lại được và phù hợp so sánh experiments. Nó phụ thuộc dataset đại diện và không đo được cách con người phản ứng. Online evaluation đo workflow thật: thời gian xử lý, tỷ lệ agent chấp nhận gợi ý, escalation hay customer outcome. A/B test mạnh nhưng chỉ phù hợp khi risk cho phép và sample đủ.

Generative output cần rubric và human evaluation nhiều hơn vì có nhiều câu trả lời chấp nhận được. Rubric có thể chấm groundedness, completeness, clarity và safety. Reviewer cần examples chuẩn và calibration session để giảm bất đồng. LLM-as-judge có thể hỗ trợ scale nhưng bản thân judge cũng là model có bias; phải kiểm tra tương quan với human review trên sample.

## 7. Test set là tài sản sản phẩm

Một evaluation set tốt nên chứa normal cases, edge cases, known incidents và important slices. Khi production có lỗi mới, thêm một regression case sau khi đã điều tra và loại thông tin nhạy cảm. Theo thời gian, set trở thành memory về những gì feature phải bảo toàn.

Tuy nhiên thêm mọi production failure vào test rồi tune liên tục cũng làm overfit test. Nên tách development eval dùng thường xuyên, holdout eval dùng ở release gate và live monitoring. Với team nhỏ, ba file versioned cùng policy sử dụng rõ đã đủ; không cần platform phức tạp ngay.

## 8. Khi nào dừng cải thiện

Optimization có diminishing returns. Dừng khi đạt product threshold, lỗi còn lại có fallback và lợi ích experiment tiếp theo thấp hơn cost. Có thể model B tăng F1 từ 0.89 lên 0.895 nhưng latency gấp năm, khó giải thích và cần GPU; model A có thể là lựa chọn tốt hơn.

Decision record nên ghi model nào được chọn, evidence, trade-off và risk chấp nhận. Điều này ngăn team sau đó nhìn leaderboard mà không biết constraint ban đầu.

## 9. Mini practice

Dùng predictions của Module 3 hoặc tự tạo 20 examples. Viết confusion matrix, chọn một class quan trọng và tính precision/recall. Đọc ít nhất năm lỗi, gắn error tags rồi đề xuất đúng một cải thiện có hypothesis. Cuối cùng viết release criterion gồm một model metric và một workflow metric.

Bài đạt khi proposal cải thiện xuất phát từ lỗi quan sát, không chỉ từ mong muốn đổi algorithm. Hãy ghi count cho mọi slice và nêu điều gì metric hiện tại chưa đo.

## Checkpoint

Bạn sẵn sàng sang Module 5 khi có thể giải thích vì sao một model có accuracy cao vẫn có thể thất bại, threshold thay đổi precision/recall ra sao và error analysis dẫn tới experiment thế nào. Từ đây, neural network chỉ là một họ model mới; tiêu chuẩn evidence không thay đổi.

