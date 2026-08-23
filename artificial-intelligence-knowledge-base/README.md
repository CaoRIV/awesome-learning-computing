# Artificial Intelligence — Practical Foundations

Knowledge base tiếng Việt dành cho người đã biết lập trình cơ bản và muốn hiểu Artificial Intelligence theo hướng thực dụng. Track không bắt đầu bằng một danh sách công thức, cũng không bắt đầu bằng cách gọi API để tạo chatbot. Người học đi từ câu hỏi “hệ thống đang cần dự đoán hay tạo ra điều gì?” tới dữ liệu, baseline, đánh giá, model hiện đại và cách vận hành một tính năng AI có giới hạn rõ ràng.

> Problem → Data → Baseline → Model → Evaluation → AI Feature → Production Feedback

## Vì sao track này được tổ chức theo vertical path

AI là một vùng kiến thức rất rộng. Nếu tách ngay từ đầu thành Linear Algebra, Statistics, Machine Learning, Deep Learning, NLP, Computer Vision và MLOps, người mới dễ học nhiều mảnh rời nhưng chưa biết chúng kết nối để giải quyết một bài toán ra sao. Track này chọn một đường đi ngắn hơn: mỗi module chỉ đưa vào lượng lý thuyết đủ để giải thích quyết định đang làm, sau đó dùng ví dụ support-ticket và trợ lý hỏi đáp để nối các khái niệm với nhau.

Cách tổ chức này không phủ nhận toán học. Nó chỉ trì hoãn phần chứng minh và độ sâu chuyên ngành cho đến khi người học biết mình cần chúng để trả lời câu hỏi nào. Sau core path, bạn có thể học riêng Mathematics for Machine Learning, Computer Vision, NLP chuyên sâu hoặc Reinforcement Learning mà không phải học lại mental model nền tảng.

## Track dành cho ai

Baseline phù hợp là người đã biết biến, function, collection, đọc một đoạn Python ngắn và hiểu dữ liệu dạng bảng. Không cần biết calculus, probability nâng cao hay framework AI. Nếu chưa từng viết Python, bạn vẫn đọc được phần lý thuyết nhưng nên bổ sung Programming Foundations trước khi làm bài tập.

Track hướng tới beginner và intermediate. Beginner nhận được một lộ trình có điểm dừng rõ; người đã từng gọi model API hoặc train notebook có thể dùng các module về evaluation, grounding và production để lấp khoảng trống engineering.

## Bắt đầu học

Đọc [Learning Roadmap](00-meta/learning-roadmap.md) trước, sau đó học tuần tự tám module. Mỗi module mất khoảng 45–75 phút để đọc và lâu hơn nếu tự làm bài thực hành.

| # | Module | Kết quả chính |
|---:|---|---|
| 1 | [AI Mental Model](modules/01-ai-mental-model.md) | Phân biệt rule, ML và generative AI; xác định đúng boundary |
| 2 | [From Problem to Data](modules/02-data-and-learning-problems.md) | Chuyển nhu cầu thành target, feature, split và baseline |
| 3 | [A Small Machine Learning Workflow](modules/03-machine-learning-workflow.md) | Train một classifier nhỏ mà không biến notebook thành phép màu |
| 4 | [Evaluation and Improvement](modules/04-evaluation-and-improvement.md) | Chọn metric, đọc lỗi và cải thiện theo evidence |
| 5 | [Neural Networks without the Fog](modules/05-neural-networks.md) | Hiểu representation, training loop và khi nào Deep Learning đáng dùng |
| 6 | [Language Models and Generative AI](modules/06-language-models-and-generative-ai.md) | Hiểu token, context, prompting, hallucination và tool use |
| 7 | [Build a Grounded AI Assistant](modules/07-build-a-grounded-ai-assistant.md) | Thiết kế RAG nhỏ, có citation và đường lui khi thiếu bằng chứng |
| 8 | [Responsible AI in Production](modules/08-responsible-ai-in-production.md) | Vận hành, quan sát, bảo vệ dữ liệu và phát hành có kiểm soát |

## Case study xuyên suốt

Một đội support nhận ticket với tiêu đề và nội dung tự do. Họ muốn tự động gợi ý nhóm xử lý, ưu tiên ticket khẩn cấp và giúp nhân viên tìm câu trả lời từ handbook nội bộ. Đây là một case đủ nhỏ để đọc trong vài phút nhưng đủ thật để thấy ba loại hệ thống khác nhau: rule cho tình huống rõ ràng, classifier học từ ticket cũ, và language model tạo câu trả lời dựa trên tài liệu.

Case study không được giả vờ là production system hoàn chỉnh. Dữ liệu minh họa nhỏ, provider call được giữ ở mức pseudocode hoặc interface trung lập, và các chủ đề như distributed training hay GPU optimization nằm ngoài core path. Scope này là chủ ý để người học tập trung vào reasoning thay vì infrastructure.

## Cách học để không bị ngợp

Mỗi module được viết theo các đoạn giải thích có mạch, xen kẽ một ví dụ đủ nhỏ để quan sát. Đừng cố ghi nhớ mọi thuật ngữ ở lần đọc đầu. Sau mỗi module, hãy tự kể lại bằng ngôn ngữ của mình: input là gì, output là gì, model học từ signal nào, ta biết nó tốt bằng cách nào và điều gì xảy ra khi nó không chắc chắn.

Code là phương tiện kiểm tra mental model. Hãy chạy ví dụ, thay đổi một vài record và quan sát metric thay vì sao chép nguyên notebook. Một bài thực hành được coi là hoàn thành khi bạn giải thích được một quyết định và một giới hạn, không phải khi output tình cờ đẹp.

## Scope và điểm dừng

Core path bao gồm problem framing, supervised learning, evaluation, neural-network intuition, LLM application, retrieval-augmented generation và production fundamentals. Track chỉ giới thiệu vừa đủ regression, classification, embeddings và optimization để người học hiểu luồng chính.

Các chuyên đề Mathematics for ML, Computer Vision, time series, recommendation systems, reinforcement learning, fine-tuning quy mô lớn, distributed training và nghiên cứu model architecture là extension paths. Chúng chỉ nên được mở khi project hoặc mục tiêu nghề nghiệp thực sự cần; không phải mọi người học AI đều cần học tất cả.

## Exit criteria

Bạn hoàn thành track khi có thể nhận một yêu cầu AI mơ hồ, chuyển nó thành một behavior có thể đánh giá, tạo baseline đơn giản, tránh leakage, chọn metric phù hợp, đọc một số lỗi đại diện và mô tả cách phát hành an toàn. Với ứng dụng LLM, bạn cần giải thích được vì sao model có thể trả lời trôi chảy nhưng sai, khi nào nên retrieval hoặc tool call, và khi nào hệ thống phải từ chối hoặc chuyển cho con người.

