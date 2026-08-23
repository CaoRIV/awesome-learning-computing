# 3. A Small Machine Learning Workflow — Từ text tới prediction

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 2](02-data-and-learning-problems.md)  
> **Status:** Reviewed

## Overview

Module này train một text classifier nhỏ bằng `scikit-learn`. Mục tiêu không phải đạt metric ấn tượng trên sáu dòng dữ liệu, mà là nhìn thấy toàn bộ đường đi: raw text được biến thành numbers, model học association, pipeline được fit chỉ trên train data và output được kiểm tra trên examples chưa thấy.

Classical Machine Learning vẫn là lựa chọn rất tốt cho nhiều bài toán business có dữ liệu dạng bảng hoặc text classification vừa phải. Nó rẻ, nhanh, dễ debug và thường cần ít data hơn neural network. Việc model mới hơn tồn tại không làm những properties này biến mất.

## 1. Representation: model không đọc chữ như con người

Algorithm số học cần vector. Với text, một representation đơn giản là bag-of-words: mỗi chiều biểu diễn một token và giá trị nói token quan trọng đến đâu. TF–IDF giảm trọng số của từ xuất hiện khắp nơi và tăng trọng số của từ phân biệt document. Representation này bỏ qua phần lớn word order và nuance, nhưng thường là baseline mạnh cho routing, spam hoặc intent classification.

Ví dụ, “trừ tiền hai lần” và “hóa đơn bị trừ sai” chia sẻ signal `trừ`, `tiền`; vector của chúng gần hơn những ticket về mật khẩu. Model tuyến tính sau đó học weight cho từng token theo class. Ta không cần tưởng tượng model “hiểu thanh toán” như người; nó học rằng một số patterns tương quan với label Billing trong data.

## 2. Pipeline giữ preprocessing và model đi cùng nhau

Một lỗi phổ biến là fit vectorizer trên toàn bộ dataset trước khi split. Dù label không được dùng, vocabulary và document frequency từ test đã ảnh hưởng representation, tạo leakage nhỏ nhưng thật. `Pipeline` giúp mỗi lần fit, transformation học được chỉ nhìn train fold; lúc predict, đúng transformation đó được tái sử dụng.

Ví dụ dưới đây cần Python 3 và `scikit-learn`. Dataset được viết trực tiếp để ví dụ tự chứa; một project thật sẽ load versioned data và có nhiều examples hơn.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline

train_texts = [
    "thẻ bị trừ tiền hai lần",
    "hóa đơn tháng này không đúng",
    "tôi muốn kiểm tra khoản thanh toán",
    "quên mật khẩu đăng nhập",
    "không nhận được mã otp",
    "tài khoản của tôi bị khóa",
    "ứng dụng bị đứng khi mở",
    "màn hình trắng sau khi cập nhật",
    "app tự thoát liên tục",
]

train_labels = [
    "billing", "billing", "billing",
    "account", "account", "account",
    "technical", "technical", "technical",
]

model = Pipeline([
    ("text", TfidfVectorizer(ngram_range=(1, 2))),
    ("classifier", LogisticRegression(max_iter=1_000)),
])

model.fit(train_texts, train_labels)

new_tickets = [
    "tôi bị tính phí hai lần",
    "không thể vào tài khoản",
    "ứng dụng crash sau cập nhật",
]

for text, label, scores in zip(
    new_tickets,
    model.predict(new_tickets),
    model.predict_proba(new_tickets),
):
    confidence = max(scores)
    print(f"{label:10} {confidence:.2f} | {text}")
```

Không nên diễn giải confidence từ dataset nhỏ như xác suất đáng tin. Ví dụ chỉ cho thấy API contract: model nhận text và tạo class cùng score. Trong workflow thật, threshold phải được chọn trên validation set, probability calibration cần được kiểm tra và input score thấp đi vào manual review.

## 3. Logistic Regression không chỉ dành cho regression

Tên dễ gây nhầm: Logistic Regression là classifier. Với binary case, model tính weighted sum của features rồi dùng logistic function để đưa score vào khoảng 0–1. Multiclass implementation mở rộng để tạo score cho các classes. Training điều chỉnh weights sao cho class đúng nhận probability cao hơn, đồng thời regularization hạn chế weights quá cực đoan.

Ta không cần tự đạo hàm loss để dùng model đúng ở level này, nhưng cần hiểu ba ý. Weight là điều model học; objective/loss nói prediction sai bao nhiêu trên train examples; optimizer tìm weights làm loss giảm. Metric như F1 hoặc accuracy dùng để đánh giá behavior, không nhất thiết là hàm model tối ưu trực tiếp.

Linear classifier tạo decision boundary tuyến tính trong feature space. Với TF–IDF có hàng nghìn token dimensions, boundary đơn giản vẫn mô tả được nhiều pattern hữu ích. Khi relationship quá phi tuyến hoặc representation không chứa signal cần thiết, đổi model hoặc representation mới có thể giúp.

## 4. Training loop ở mức conceptual

Mọi supervised workflow có thể hình dung bằng vòng lặp: model tạo prediction từ current parameters, loss so prediction với target, optimizer cập nhật parameters và quá trình lặp lại. Classical algorithms thường đóng gói vòng lặp trong `.fit()`. Neural network ở Module 5 làm các bước lộ rõ hơn, nhưng logic nền không đổi.

Fit tốt trên training data chưa đủ. Model có thể memorise noise hoặc token đặc trưng của examples. **Underfitting** là khi model quá đơn giản hoặc features thiếu signal, nên cả train và validation đều kém. **Overfitting** là khi train tốt nhưng validation kém. Cách xử lý bắt đầu bằng learning curves, error analysis và data quality, không phải tự động tăng complexity.

## 5. Inspect model để phá cảm giác “hộp đen”

Với linear text classifier, ta có thể xem token nào có weight lớn cho từng class. Đây không phải giải thích nhân quả, nhưng giúp phát hiện shortcut. Nếu token tên agent có weight cao cho Billing, dataset có thể encode assignment history thay vì nội dung.

```python
vectorizer = model.named_steps["text"]
classifier = model.named_steps["classifier"]
feature_names = vectorizer.get_feature_names_out()

for class_name, weights in zip(classifier.classes_, classifier.coef_):
    top_indexes = weights.argsort()[-5:][::-1]
    top_terms = [feature_names[index] for index in top_indexes]
    print(class_name, "->", ", ".join(top_terms))
```

Interpretability tool không chứng minh model công bằng hoặc đúng. Nó là debugging lens. Bạn vẫn cần examples chưa thấy, slice evaluation và kiểm tra behavior khi input thiếu signal.

## 6. Reproducibility vừa đủ

Một experiment nhỏ nên lưu version của data split, code/config, library và kết quả. Không cần dựng experiment platform trước khi có hai experiments. Một directory có `requirements.txt`, script train, JSON metrics và model artifact đã đủ để một người khác chạy lại.

Random seed giúp lặp lại split hoặc initialization nhưng không bảo đảm mọi phần cứng cho kết quả bit-for-bit. Mục tiêu thực tế là tái hiện decision: cùng data và config tạo metric gần tương đương, và ta biết artifact nào đang phục vụ production. Seed không sửa data leakage hay test contamination.

## 7. Khi nào không cần nâng cấp model

Nếu classifier đạt requirement, latency thấp và lỗi có fallback, dừng lại là hợp lý. Gradient boosting, transformer hoặc fine-tuning chỉ nên được thử khi error analysis cho thấy representation/model hiện tại là bottleneck. Đổi model trong khi labels mâu thuẫn thường chỉ làm experiment đắt hơn.

Một heuristic hữu ích là cải thiện theo thứ tự: kiểm tra target và label, kiểm tra split/leakage, cải thiện coverage, chỉnh threshold, rồi mới tăng model complexity. Đây không phải luật tuyệt đối, nhưng ngăn team dùng compute để bù cho problem definition yếu.

## 8. Mini practice

Mở rộng dataset lên tối thiểu 30 câu với ba classes, giữ lại ít nhất chín câu test do bạn viết trước khi train. Chạy keyword baseline và TF–IDF classifier, ghi lại accuracy cùng từng prediction sai. Thêm một câu mơ hồ có hai intents và mô tả output mong muốn ở product level: chọn một class, multi-label hay manual review.

Bài đạt khi preprocessing nằm trong pipeline, test examples không được dùng để sửa trực tiếp train data từng câu một, và báo cáo có ít nhất một giới hạn. Metric cao trên dataset tự viết không phải bằng chứng production; mục tiêu là thực hành workflow đúng.

## Checkpoint

Bạn cần giải thích được raw text trở thành vector thế nào, `.fit()` học gì, vì sao transformation phải nằm trong pipeline và vì sao confidence chưa chắc là probability đã calibrated. Module tiếp theo sẽ biến output thành evidence bằng metrics và error analysis.

