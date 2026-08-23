# 5. Neural Networks without the Fog — Học representation qua nhiều lớp

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 4](04-evaluation-and-improvement.md)  
> **Status:** Reviewed

## Overview

Neural network thường được trình bày bằng nhiều công thức hoặc ví dụ thần bí về “bộ não”. Ở mức engineering, có thể hiểu đơn giản hơn: đây là một function có nhiều parameters, được ghép từ các layers; training dùng examples để điều chỉnh parameters sao cho loss giảm. Điểm mạnh quan trọng là network có thể học representation phù hợp với task, thay vì phụ thuộc hoàn toàn vào features do con người thiết kế.

Deep Learning là neural network có nhiều layers đủ để học representation theo cấp. Với image, layers sớm có thể phản ứng với edges, layers sau với shapes; với language, model học patterns của token và context. Cách mô tả này là intuition, không phải cam kết rằng mỗi neuron có một ý nghĩa con người đọc được.

## 1. Một neuron chỉ là phép biến đổi

Một unit nhận vector `x`, tính weighted sum `w·x + b`, rồi đi qua activation function. Nếu không có activation phi tuyến, nhiều linear layers ghép lại vẫn chỉ tương đương một linear transformation. Activation cho phép network mô tả relationships phức tạp hơn.

Layer nhận nhiều inputs và tạo nhiều outputs song song. Stack layers tạo network. Parameters là weights và biases được học; hyperparameters như learning rate, số layers và batch size do người thiết kế chọn. Output layer phụ thuộc task: một value cho regression, logits cho classification hoặc distribution trên vocabulary cho language model.

Ta có thể giữ mental model:

```text
input → representation layers → task output → loss
                    ↑                       |
                    └── parameter updates ──┘
```

## 2. Forward pass, loss và gradient descent

Forward pass đưa batch inputs qua network để tạo predictions. Loss biến khoảng cách giữa predictions và targets thành một số mà optimizer có thể giảm. Backpropagation dùng chain rule để tính mỗi parameter ảnh hưởng loss ra sao; optimizer như SGD hoặc Adam dùng gradients để cập nhật parameters.

Learning rate quyết định bước cập nhật. Quá lớn có thể làm loss dao động hoặc diverge; quá nhỏ làm training chậm. Epoch là một lượt qua training data, batch là nhóm examples được xử lý trước một update. Những khái niệm này đủ để đọc training log và phát hiện vấn đề cơ bản; chi tiết calculus có thể học trong extension Mathematics for ML.

Validation vẫn đứng ngoài update loop. Nếu validation loss bắt đầu tăng trong khi train loss tiếp tục giảm, network có thể overfit. Early stopping, regularization, dropout hoặc thêm data là các lựa chọn, nhưng error analysis vẫn cần để biết root cause.

## 3. Ví dụ PyTorch nhỏ nhưng đầy đủ

Ví dụ dùng bốn numerical features đã được chuẩn hóa để dự đoán ticket có urgent hay không. Nó cố ý không xử lý raw text; mục đích là lộ training loop. Cần Python 3 và PyTorch.

```python
import torch
from torch import nn

torch.manual_seed(7)

# [contains_security_term, customer_tier, normalized_failed_attempts, sentiment_score]
features = torch.tensor([
    [0.0, 0.2, 0.2, -0.2],
    [1.0, 0.9, 1.0, -0.9],
    [0.0, 0.8, 0.2,  0.1],
    [1.0, 0.4, 0.8, -0.7],
    [0.0, 0.3, 0.0,  0.4],
    [1.0, 0.7, 0.6, -0.8],
], dtype=torch.float32)
targets = torch.tensor([[0], [1], [0], [1], [0], [1]], dtype=torch.float32)

model = nn.Sequential(
    nn.Linear(4, 8),
    nn.ReLU(),
    nn.Linear(8, 1),
)

loss_function = nn.BCEWithLogitsLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.01)

for epoch in range(200):
    logits = model(features)             # forward pass
    loss = loss_function(logits, targets)

    optimizer.zero_grad()
    loss.backward()                      # compute gradients
    optimizer.step()                     # update parameters

with torch.no_grad():
    probabilities = torch.sigmoid(model(features))
    predictions = (probabilities >= 0.5).int()
    print(torch.cat([probabilities, predictions], dim=1))
```

Dataset quá nhỏ và evaluation trên chính training data nên ví dụ không chứng minh khả năng generalize. Đó là giới hạn được nói rõ, không phải chi tiết có thể bỏ qua. Khi áp dụng thật, cần train/validation/test, preprocessing fit trên train, metric theo cost và artifact versioning như Module 2–4.

`BCEWithLogitsLoss` kết hợp sigmoid conceptually với binary cross-entropy theo cách numerical stable. Model trả logits chưa bị ép vào 0–1 khi training; sigmoid chỉ được dùng lúc diễn giải probability-like score. `zero_grad()` cần thiết vì PyTorch mặc định cộng dồn gradients.

## 4. Representation learning và embeddings

Embedding là vector học được để biểu diễn một item như token, sentence, user hoặc product. Items được dùng trong contexts tương tự thường có vectors gần nhau theo metric phù hợp. Khác với one-hot vector rất thưa, embedding dày và có dimensions do training khám phá.

“Gần” không có nghĩa giống nhau theo mọi khía cạnh hay có quan hệ nhân quả. Một text embedding được train cho semantic retrieval có thể tốt để tìm handbook liên quan nhưng không phù hợp đo sentiment. Chọn embedding dựa trên task và đánh giá retrieval trên dữ liệu domain.

Embeddings là cầu nối sang language models và RAG. Trong Module 7, document chunks được embed để truy xuất evidence có nội dung gần câu hỏi. Retrieval vẫn có thể sai nếu chunks kém, vocabulary domain lạ hoặc permission filter thiếu.

## 5. Tại sao network cần nhiều data và compute?

Network linh hoạt có nhiều parameters nên có thể học pattern phức tạp, đồng thời có nhiều cách fit noise. Large datasets, pretraining, regularization và architecture bias giúp kiểm soát. Transfer learning thay vì train từ đầu là lựa chọn mặc định cho phần lớn application teams: dùng pretrained model, sau đó prompt, retrieve, fine-tune nhẹ hoặc train task head tùy nhu cầu.

Compute cost không chỉ là training. Serving model lớn tạo latency, memory và energy cost. Một classifier tuyến tính chạy CPU có thể tốt hơn transformer nếu requirement là route 500 ticket mỗi ngày với vocabulary ổn định. Model size là trade-off, không phải maturity score.

## 6. Khi Deep Learning đáng dùng

Deep Learning thường có lợi khi input unstructured như image, audio hoặc language, data/transfer signal đủ và task cần representation phức tạp. Nó cũng hữu ích khi một pretrained model đã mang khả năng generalization mà classical feature engineering khó đạt.

Không nên chọn chỉ vì data “nhiều”. Tabular data vừa phải thường được tree-based models xử lý rất cạnh tranh. Dataset nhỏ, label noise cao hoặc need explainability nghiêm ngặt có thể phù hợp model đơn giản. Hãy so với baseline bằng cùng split và product metric; đừng so tên architecture.

## 7. Common misunderstandings

Neural network không tự động khám phá “sự thật”; nó tối ưu objective trên distribution được thấy. Thêm layers không bảo đảm tốt hơn; optimization khó hơn và overfit có thể tăng. Training loss thấp không chứng minh production quality. Pretrained model cũng không “biết mọi thứ”: training cutoff, data coverage và objective giới hạn behavior.

Một hiểu nhầm khác là fine-tuning luôn cần cho domain knowledge. Nếu knowledge thay đổi thường xuyên và cần citation, retrieval thường phù hợp hơn vì data nằm ngoài parameters và cập nhật độc lập. Fine-tuning hữu ích hơn khi cần behavior, style hoặc task pattern ổn định, sau khi prompting và examples chưa đủ.

## 8. Mini practice

Chạy ví dụ, thay learning rate lần lượt thành `1.0` và `0.00001`, quan sát loss mỗi 20 epochs. Sau đó thêm hai records có label mâu thuẫn với pattern và mô tả điều gì xảy ra. Cuối cùng so sánh neural network này với Logistic Regression cho dataset nhỏ: model nào dễ kiểm tra hơn và evidence nào cần có để chọn network?

Bài đạt khi bạn không dùng training accuracy làm kết luận cuối. Hãy nêu ít nhất một lý do về data, latency hoặc interpretability trong decision.

## Checkpoint

Bạn cần kể lại training loop bằng forward pass, loss, gradients và update; giải thích embedding là representation theo task; và nêu khi nào pretrained Deep Learning tạo giá trị so với model nhỏ. Module 6 áp dụng những ý này cho language model.
