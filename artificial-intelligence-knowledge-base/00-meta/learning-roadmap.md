# Learning Roadmap — Artificial Intelligence Practical Foundations

> **Thời lượng gợi ý:** 10–14 giờ học core, cộng 6–10 giờ thực hành  
> **Prerequisites:** Lập trình Python cơ bản, collection, function và dữ liệu dạng bảng  
> **Ngôn ngữ ví dụ:** Python 3; `scikit-learn` và PyTorch chỉ xuất hiện khi chúng làm ví dụ rõ hơn

## Đích đến của lộ trình

Sau track này, người học chưa trở thành AI researcher và cũng chưa cần tự train một foundation model. Đích đến thực tế hơn là có thể tham gia xây một AI feature nhỏ mà không coi model như hộp đen: hiểu bài toán, chuẩn bị dữ liệu, tạo baseline, đánh giá theo cost thật, nối model vào sản phẩm và theo dõi failure sau khi phát hành.

Lộ trình sử dụng nguyên tắc “just enough theory”. Một công thức chỉ xuất hiện khi nó làm rõ behavior hoặc trade-off; một framework chỉ xuất hiện khi nó giảm công việc không liên quan tới concept. Nếu một rule hoặc search thông thường giải quyết tốt requirement, đó vẫn là một kết quả đúng — không dùng model cũng là một quyết định AI engineering hợp lệ.

## Bản đồ học tập

```text
01. AI mental model
        ↓
02. Problem framing + data
        ↓
03. Small ML workflow
        ↓
04. Evaluation + error analysis
        ↓
05. Neural networks
        ↓
06. Language models
        ↓
07. Grounded assistant (RAG)
        ↓
08. Production + responsible AI
```

Module 1–4 tạo nền tảng bắt buộc. Đây là nơi người học hình thành thói quen đo baseline, tách train/test và nhìn lỗi thay vì chỉ nhìn demo. Module 5 giải thích neural network ở độ sâu đủ để đọc tài liệu hiện đại. Module 6–8 là vertical slice cho generative AI, nhưng vẫn kế thừa toàn bộ tư duy evaluation của phần trước.

## Nhịp học gợi ý

Nếu học trong bốn buổi, buổi đầu nên hoàn thành Module 1 và 2 rồi tự viết problem statement cho case support-ticket. Buổi hai chạy workflow ở Module 3 và làm error analysis của Module 4. Buổi ba học neural network và language model, ưu tiên hiểu cơ chế hơn API. Buổi cuối thiết kế grounded assistant, threat model ngắn và production checklist.

Nếu chỉ có sáu giờ để review, hãy đọc Module 1, 2, 4, 6, 7 và 8; xem code Module 3 để hiểu pipeline nhưng không cần tinh chỉnh. Nếu mục tiêu là classical Machine Learning, học kỹ Module 1–5 và coi Module 6–8 là overview. Việc rẽ nhánh như vậy được phép vì roadmap phục vụ mục tiêu học, không phải một syllabus cứng.

## Checkpoint 1 — Từ ý tưởng tới experiment

Sau Module 2, bạn cần viết được một problem statement ngắn có user, decision, input, expected output và cost của lỗi. Bạn cũng phải xác định unit of observation, label đến từ đâu, thời điểm prediction và một baseline không học máy. Nếu chưa trả lời được các câu hỏi đó, thêm model sẽ chỉ che sự mơ hồ.

Artifact nhỏ nên có dạng: “Khi ticket mới được tạo, hệ thống gợi ý một trong ba queue cho support agent; gợi ý sai queue làm chậm phản hồi, nên hệ thống hiển thị confidence và không tự động route dưới threshold.” Câu này hữu ích hơn “xây AI phân loại ticket” vì nó đã nói rõ thời điểm, người dùng và failure behavior.

## Checkpoint 2 — Model có evidence

Sau Module 4, bạn cần có baseline, validation result, test result cuối và một bảng error analysis nhỏ. Không cần metric cao; cần phân biệt được model thực sự học signal hay đang tận dụng leakage. Bạn phải giải thích tại sao chọn precision, recall, F1 hoặc một metric khác dựa trên cost của false positive và false negative.

Một experiment tốt có thể kết luận “chưa nên dùng model”. Chẳng hạn dữ liệu chỉ có 120 ticket, label thiếu nhất quán và keyword rule đạt kết quả gần tương đương classifier. Kết luận này tiết kiệm chi phí và chỉ ra việc tiếp theo là cải thiện labeling, không phải thêm neural network.

## Checkpoint 3 — AI feature có boundary

Sau Module 7, bạn cần mô tả một luồng hỏi đáp có retrieval, generation, citation và fallback. Model không được tự nhận là đã tìm thấy policy khi retrieval không cung cấp evidence. Hệ thống phải tách nội dung tài liệu khỏi instruction đáng tin cậy và giới hạn tool theo quyền của user.

Bạn không bắt buộc gọi một paid API. Có thể hoàn thành checkpoint bằng interface giả lập, một tập tài liệu nhỏ và test cases mô tả expected evidence. Trọng tâm là architecture và evaluation contract, không phải nhà cung cấp model.

## Checkpoint 4 — Sẵn sàng phát hành nhỏ

Sau Module 8, bạn cần một release plan gồm offline evaluation, pilot có human review, metrics quan sát, cách rollback và owner xử lý incident. Với dữ liệu nhạy cảm, data flow phải cho biết thông tin nào đi tới model provider, được lưu bao lâu và ai có quyền truy cập.

“Production-ready” trong track không có nghĩa xây platform MLOps nhiều service. Với một feature nhỏ, versioned prompt/model/config, structured logs đã loại PII, một evaluation set, dashboard vài metric và kill switch có thể là đủ. Complexity chỉ tăng khi traffic, risk hoặc số team tạo ra nhu cầu thật.

## Extension paths sau core

Học thêm Mathematics for Machine Learning khi bạn cần hiểu optimization, uncertainty hoặc paper sâu hơn. Học Computer Vision khi input chính là image/video; học NLP chuyên sâu khi cần tokenization, sequence labeling, fine-tuning hoặc multilingual evaluation. Recommendation, time series và reinforcement learning có assumptions riêng nên nên được coi là các track kế tiếp, không phải vài chương phụ của core.

Nếu đi theo hướng AI application engineer, extension có giá trị nhất thường là backend engineering, data modeling, security, experiment design và observability. Nếu đi theo hướng ML engineer, hãy bổ sung feature pipelines, distributed data processing, model serving và monitoring. Nếu đi theo research, calculus, linear algebra, probability và paper reproduction trở thành core tiếp theo.

## Definition of Done cho từng module

Một module được hoàn thành khi bạn có thể giải thích mental model mà không nhìn tài liệu, thay đổi ví dụ và dự đoán ảnh hưởng, nhận ra ít nhất một failure mode, rồi làm bài thực hành bằng một dataset hoặc domain khác. Đọc hết trang chỉ là activity; khả năng đưa ra quyết định có lý do mới là outcome.

