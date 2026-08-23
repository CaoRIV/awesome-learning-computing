# 2. From Problem to Data — Model học từ signal nào?

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 1](01-ai-mental-model.md)  
> **Status:** Reviewed

## Overview

Sau khi xác định behavior, câu hỏi quan trọng nhất không phải algorithm nào mạnh nhất mà là model sẽ học từ examples nào. Dữ liệu không phải ảnh chụp trung lập của thế giới; nó là dấu vết của workflow, policy, giao diện và quyết định cũ. Nếu ticket trước đây được route theo thói quen không nhất quán của từng agent, label lịch sử chứa cả business knowledge lẫn noise.

Module này chuyển một product problem thành learning problem có input, target, unit of observation và thời điểm prediction rõ. Ta cũng đặt baseline và chia dữ liệu trước khi thử model. Đây là những bước trông đơn giản nhưng quyết định experiment có đáng tin hơn phần lớn lựa chọn algorithm.

## 1. Từ decision tới target

Giả sử team muốn “ưu tiên ticket quan trọng”. Câu này chưa đủ để làm dữ liệu. “Quan trọng” có thể là customer tier, nguy cơ mất tiền, vi phạm SLA hoặc cảm nhận của agent. Mỗi cách hiểu tạo target khác nhau. Nếu target là “ticket có cần phản hồi trong 30 phút hay không”, team cần định nghĩa label bằng rule nghiệp vụ và xác định thời điểm hệ thống phải dự đoán.

Một supervised example thường có dạng `(features, target)`. Features là thông tin có sẵn tại prediction time; target là điều model cần dự đoán và chỉ có thể biết sau hoặc do con người gắn nhãn. Với routing, text và channel của ticket có thể là features, còn queue cuối cùng là target. ID của agent đã giải quyết ticket không nên là feature nếu ticket mới chưa có agent.

**Unit of observation** trả lời một row đại diện cho gì. Một row có thể là ticket, message, customer hoặc phiên support. Chọn sai unit dẫn tới duplicate và leakage. Nếu cùng một ticket có mười message được chia ngẫu nhiên vào train và test, model có thể thấy gần như cùng nội dung ở cả hai phía và metric cao giả tạo.

## 2. Ba learning problem phổ biến

**Classification** chọn một category hữu hạn: Billing, Account hay Technical. **Regression** dự đoán giá trị liên tục: số phút để giải quyết hoặc nhu cầu tài nguyên. **Ranking** sắp xếp candidates: tài liệu nào phù hợp nhất với câu hỏi. Generative tasks tạo chuỗi mới, nhưng trong ứng dụng thực tế thường chứa classification và ranking ở bên trong, chẳng hạn chọn tool hay retrieve document trước khi viết câu trả lời.

Việc gọi đúng loại problem giúp chọn output và metric. Nếu mục tiêu thực là đưa ticket khẩn cấp lên đầu danh sách, ranking có thể phù hợp hơn cố dự đoán chính xác “17 phút”. Nếu agent chỉ cần biết có nên escalation, binary classification dễ sử dụng hơn regression chi tiết.

## 3. Label là một contract

Label quality không chỉ là “đã điền đủ”. Hai agent có thể hiểu category khác nhau; queue cuối cùng đôi khi phản ánh ai đang rảnh chứ không phản ánh nội dung. Trước khi train, hãy lấy một sample nhỏ và để hai người gắn nhãn độc lập. Những nơi họ bất đồng cho thấy guideline mơ hồ hoặc category chồng lấn.

Không phải mọi ambiguity đều cần model giải quyết. Nếu “Account” và “Security” thường bị nhầm do taxonomy kém, sửa taxonomy và UI có thể tạo nhiều giá trị hơn tuning. Model học theo target được cung cấp; nó không tự tìm ra business truth mà tổ chức chưa thống nhất.

## 4. Features và leakage

Feature hữu ích phải có mặt lúc prediction và có quan hệ đủ ổn định với target. Text ban đầu, product area và customer language thường hợp lệ cho routing. `resolved_by_team`, resolution note hoặc final status xuất hiện sau khi route nên là leakage. Model dùng chúng có thể đạt 99% trong notebook nhưng không có giá trị khi ticket vừa đến.

Một dạng leakage tinh tế là thời gian. Nếu train ngẫu nhiên trên hai năm dữ liệu, cùng incident hoặc template có thể xuất hiện trong cả train và test. Production lại yêu cầu dự đoán tương lai. Time-based split — train trên quá khứ, validate trên giai đoạn sau — mô phỏng tình huống tốt hơn. Với data theo customer, group split tránh cùng customer xuất hiện ở cả hai phía khi mục tiêu là generalize sang customer mới.

Hãy dùng câu hỏi counterfactual đơn giản: “Tại đúng thời điểm model được gọi, hệ thống đã biết field này chưa?” Nếu câu trả lời là chưa hoặc chỉ biết nhờ outcome, field không được làm feature.

## 5. Một dataset nhỏ có chủ ý

Ví dụ sau biểu diễn ticket tối giản. Dataset thật cần nhiều record hơn rất nhiều, nhưng cấu trúc nhỏ giúp nhìn thấy assumptions.

```python
tickets = [
    {"text": "Thẻ của tôi bị trừ tiền hai lần", "channel": "app", "label": "billing"},
    {"text": "Tôi quên mật khẩu đăng nhập", "channel": "web", "label": "account"},
    {"text": "Màn hình thanh toán bị đứng", "channel": "app", "label": "technical"},
    {"text": "Hóa đơn tháng này bị sai", "channel": "email", "label": "billing"},
    {"text": "Không nhận được OTP", "channel": "app", "label": "account"},
    {"text": "Ứng dụng tự thoát khi mở", "channel": "app", "label": "technical"},
]
```

Mỗi row là một ticket tại lúc được tạo. `label` là queue đã được reviewer xác nhận, không đơn thuần là queue lịch sử. Nếu mục tiêu chỉ route từ text, `channel` không cần dùng dù đã lưu. Thêm feature vì “có sẵn” dễ tạo shortcut và làm model phụ thuộc workflow không liên quan.

Dataset card ngắn nên ghi nguồn, thời gian, unit, label process, fields bị loại, nhóm có thể thiếu và mục đích được phép. Không cần hệ thống governance đồ sộ; một file Markdown một trang đã tốt hơn dataset không có context.

## 6. Split trước khi nhìn kết quả

Train set dùng để fit parameters. Validation set dùng để chọn feature, model hoặc threshold. Test set được giữ lại cho đánh giá cuối cùng. Nếu nhìn test rồi tiếp tục sửa model, test đã trở thành validation và con số cuối bị optimistic. Với dataset nhỏ, cross-validation giúp giảm phụ thuộc vào một split, nhưng không sửa được bias hoặc leakage.

Tỉ lệ 70/15/15 hay 80/20 không phải luật phổ quát. Điều quan trọng là đủ examples cho từng class và split mô phỏng production. Với 200 ticket, test 20% chỉ có khoảng 40 records; một vài lỗi làm metric dao động mạnh. Khi data quá ít, hãy báo uncertainty, ưu tiên manual review và thu thập thêm thay vì trình bày phần trăm với độ chính xác giả.

## 7. Baseline trước model

Baseline trả lời “model cần thắng cái gì?”. Với imbalanced classification, dự đoán class phổ biến nhất là baseline tối thiểu. Keyword rules là baseline thực dụng hơn. Với regression, mean hoặc median là baseline. Với generative task, template hoặc search result có thể là baseline.

```python
from collections import Counter

train_labels = ["billing", "account", "billing", "technical", "billing"]
majority_label = Counter(train_labels).most_common(1)[0][0]

validation_labels = ["account", "billing", "technical"]
correct = sum(majority_label == actual for actual in validation_labels)
accuracy = correct / len(validation_labels)

print({"baseline": majority_label, "accuracy": accuracy})
```

Nếu một model phức tạp chỉ hơn baseline một chút, chi phí latency, monitoring và retraining có thể không đáng. Ngược lại, baseline yếu nhưng transparent vẫn hữu ích để phát hiện pipeline bug: model thấp hơn majority baseline thường báo hiệu feature, label hoặc evaluation có vấn đề.

## 8. Bias và coverage nhìn từ dữ liệu

Dataset thường đại diện tốt cho nhóm tạo ra nhiều log và kém cho nhóm ít xuất hiện. Ticket tiếng Việt không dấu, typo, code-mixing hoặc input từ accessibility tool có thể hiếm nhưng quan trọng. Chỉ báo một metric tổng sẽ che các slice này. Trước khi train, hãy liệt kê những nhóm input có cách viết hoặc cost khác và bảo đảm evaluation set có examples đại diện.

Bias không chỉ là demographic attribute. Trong support, ticket từ product mới, customer mới hoặc ca đêm có thể bị đối xử kém vì thiếu dữ liệu. Mục tiêu không phải tuyên bố dataset “không bias” mà là biết coverage, đo những slice quan trọng và thiết kế fallback cho vùng chưa biết.

## 9. Mini practice

Viết data specification cho bài toán “dự đoán học viên có cần hỗ trợ trong tuần tới”. Xác định unit of observation, prediction time, target, tối đa năm features hợp lệ, hai fields có nguy cơ leakage, split strategy và baseline. Sau đó viết hai đoạn giải thích label được tạo như thế nào và nhóm nào có thể bị thiếu trong data.

Bài làm đạt khi một người khác có thể thu thập dataset mà không phải tự đoán “cần hỗ trợ” nghĩa là gì. Nếu target phụ thuộc vào thông tin xảy ra sau prediction, điều đó không sao; điều quan trọng là features không được nhìn trước tương lai.

## Checkpoint

Trước khi sang Module 3, bạn cần trả lời được năm câu: một row là gì, model dự đoán gì, features nào có tại prediction time, split mô phỏng production ra sao và baseline là gì. Algorithm chỉ bắt đầu có ý nghĩa sau năm câu này.

