# 6. Statistics and Data Reasoning — Kết luận vừa đủ từ dữ liệu hữu hạn

> **Thời lượng:** 75–90 phút  
> **Prerequisites:** [Module 5](05-counting-and-probability.md)  
> **Status:** Reviewed

## Overview

Probability bắt đầu từ model rồi hỏi dữ liệu có thể trông như thế nào. Statistics thường đi chiều ngược: quan sát một sample rồi suy luận về population hoặc process đã tạo nó. Khoảng cách giữa sample và population tạo uncertainty.

Statistics tốt không làm uncertainty biến mất. Nó đo uncertainty, tìm bias và giữ conclusion không vượt quá thiết kế thu thập dữ liệu. Một dashboard có nhiều decimal places vẫn có thể trả lời sai câu hỏi nếu sample lệch.

## 1. Câu hỏi trước dữ liệu

Trước khi tính, viết decision và quantity cần biết. Learning Platform muốn biết giao diện mới có làm tăng tỷ lệ hoàn thành lesson đầu trong bảy ngày hay không. Metric, unit, time window và comparison phải định nghĩa trước khi nhìn result.

Data không “tự nói”. Cùng bảng events có thể trả lời active users, completion rate hoặc latency, mỗi câu cần denominator và filter khác. Chọn metric sau khi xem dữ liệu dễ dẫn tới cherry-picking.

Data-generating process gồm ai/cái gì được quan sát, khi nào, measurement bằng cách nào và record nào bị thiếu. Instrumentation bug hoặc bot traffic là phần của statistical problem, không chỉ data cleaning phụ.

## 2. Population, sample và unit of analysis

Population là toàn bộ nhóm muốn kết luận; sample là observations thực sự có. Nếu mục tiêu là learners mới tháng tới nhưng sample chỉ learners tích cực tháng trước, generalization cần assumption mạnh.

Sampling unit có thể là learner, session hoặc enrollment. Treat 1,000 sessions từ 20 learners như 1,000 independent learners làm uncertainty bị đánh giá thấp. Unit of analysis phải khớp randomization và câu hỏi.

Census quan sát toàn population hiện tại vẫn không loại uncertainty về tương lai hoặc counterfactual. Toàn bộ users hôm qua không phải toàn bộ users ngày mai.

## 3. Variable và measurement scale

Categorical variables biểu diễn nhóm như device type hoặc course category. Numerical variables biểu diễn quantity như minutes hoặc score. Discrete counts nhận values đếm được; continuous measurement có thể nhận values trong khoảng.

Ordinal category có thứ tự nhưng khoảng cách không chắc bằng nhau, như rating 1–5. Tính mean rating có thể hữu ích thực dụng nhưng không nên giả sử chênh 1→2 giống 4→5 mà không xem context.

Một ID nhìn là số nhưng là label, không phải quantity. Average user ID vô nghĩa. Kiểu lưu trữ không quyết định statistical meaning.

## 4. Center: mean, median và mode

Mean là tổng chia count. Nó dùng mọi value và phù hợp khi muốn balance tổng deviation, nhưng nhạy với outlier. Median là value giữa sau khi sort; nó bền hơn với extreme values. Mode là value/category xuất hiện nhiều nhất.

Thời gian hoàn thành `[8, 9, 10, 11, 62]` có mean 20 phút nhưng median 10. Mean đúng phép tính, nhưng nếu nói “typical learner mất 20 phút” dễ gây hiểu sai vì một outlier kéo lên. Báo center cùng distribution/spread.

Weighted mean cần weights có meaning. Average completion rate giữa một course 10 learners và một course 1,000 learners không nên lấy trung bình hai percentages nếu muốn overall learner rate; cần weight theo denominator.

## 5. Spread và distribution shape

Range là max trừ min, rất nhạy outlier. Interquartile range (IQR) là khoảng từ percentile 25 tới 75, mô tả nửa giữa. Variance lấy trung bình squared deviations khỏi mean; standard deviation là căn variance và cùng unit với data.

Standard deviation hữu ích nhưng không tự nói “đa số nằm trong một standard deviation” nếu distribution không gần normal. Histogram hoặc quantiles cho hình dạng trực tiếp hơn.

Distribution có thể symmetric, skewed, nhiều peaks hoặc heavy tail. Latency thường right-skewed: phần lớn nhanh, một ít rất chậm. Mean một mình che tail; p50, p95, p99 nói trải nghiệm theo percentiles.

## 6. Outlier là signal cần ngữ cảnh

Outlier có thể là lỗi đo, bot, case hợp lệ hiếm hoặc behavior quan trọng. Xóa outlier chỉ vì làm chart xấu tạo bias. Trước hết truy nguồn event và định nghĩa rule loại dựa trên data-generating process.

Nếu session kéo dài 24 giờ vì tab để mở, metric “time on lesson” cần definition timeout, không phải thao tác xóa tùy ý. Nếu learner accessibility cần thời gian dài, đó là user group cần hiểu, không phải noise.

Robust statistics như median/IQR giảm ảnh hưởng extreme values nhưng không thay investigation.

## 7. Sampling bias và missing data

Random sample cho mỗi unit cơ hội selection đã biết và giúp inference. Convenience sample như chỉ users trả survey thường lệch vì người trả lời khác người im lặng. Survivorship bias chỉ nhìn learners còn hoạt động và bỏ người đã rời.

Missing data không luôn ngẫu nhiên. Learner yếu có thể bỏ assessment nên missing scores liên quan chính outcome. Điền mean làm distribution hẹp giả và che mechanism.

Hỏi observation nào không thể xuất hiện trong dataset. Logging chỉ success events làm error rate trông bằng zero. Tracking bị ad blocker ảnh hưởng một nhóm. Statistical technique không cứu measurement system bỏ sai data.

## 8. Estimator, sampling variability và standard error

Sample mean là estimator của population mean. Nếu lấy sample khác, estimate đổi. Sampling distribution mô tả estimate sẽ biến thiên qua repeated samples. Standard error đo độ biến thiên này, khác standard deviation của individual observations.

Với independent observations, standard error của mean thường giảm gần theo `1/√n`. Muốn giảm một nửa standard error cần khoảng bốn lần sample, không phải gấp đôi. Data nhiều giúp giảm random noise nhưng không sửa systematic bias.

Đừng báo estimate như exact truth. `completion = 42.173%` từ sample nhỏ tạo precision giả. Số chữ số nên phản ánh measurement và uncertainty.

## 9. Confidence interval

Confidence interval là procedure tạo intervals có coverage dài hạn. Một 95% procedure sẽ chứa true parameter trong khoảng 95% repeated samples dưới assumptions. Sau khi có một interval cụ thể theo frequentist interpretation, parameter cố định; không nói có 95% probability parameter nằm trong interval đó.

Trong giao tiếp thực dụng có thể nói “estimate 4 percentage points, 95% confidence interval từ 1 tới 7 points”, rồi giải thích interval cho thấy values tương thích với data/model. Width phụ thuộc sample size, variance và design.

Interval không sửa bias, non-independence hoặc metric sai. Khoảng hẹp quanh measurement sai vẫn tự tin sai.

## 10. Hypothesis test và p-value

Null hypothesis thường mô tả không có effect/difference theo parameter. P-value là xác suất quan sát result ít nhất cực đoan như hiện tại nếu null và assumptions đúng. Nó không phải xác suất null đúng, không phải xác suất result do ngẫu nhiên, và không đo effect lớn.

Ngưỡng 0.05 là convention, không ranh giới chân lý. Với sample rất lớn, effect nhỏ vô nghĩa vẫn “statistically significant”. Với sample nhỏ, effect có ích có thể chưa đủ evidence. Luôn báo effect size và interval, không chỉ pass/fail.

Nếu thử 20 metrics và chỉ báo một p-value nhỏ, false discovery tăng. Chọn primary metric trước; exploratory findings được gắn nhãn và cần confirm.

## 11. Correlation, prediction và causation

Correlation đo association, thường linear với Pearson correlation. Correlation 0 không có nghĩa không có relation; relation cong có thể có correlation thấp. Outlier có thể tạo hoặc phá correlation.

A và B cùng tăng có thể do confounder C. Learners xem nhiều video hoàn thành nhiều hơn có thể vì motivation tác động cả hai. Tăng video bằng ép autoplay chưa chắc tăng completion.

Prediction chỉ cần pattern ổn định trong target population; causal decision hỏi điều gì xảy ra nếu can thiệp. Experiment randomized giúp cân bằng confounders trung bình và hỗ trợ causal inference trong phạm vi design. Observational causal analysis cần assumptions/phương pháp sâu hơn.

## 12. A/B experiment vừa đủ

Randomly assign eligible learners vào control và treatment. Chọn unit là learner để một learner không thấy hai giao diện. Định nghĩa primary metric trước, guardrail như error rate và exposure event để biết ai thực sự nhận variant.

Kiểm tra Sample Ratio Mismatch: phân bổ 50/50 kỳ vọng nhưng observed lệch bất thường có thể báo bug assignment/logging. Không nhìn result mỗi giờ rồi stop ngay khi p-value nhỏ; repeated peeking tăng false positive nếu procedure không điều chỉnh.

Kết quả cần statistical và practical interpretation. Treatment tăng completion từ 40% lên 42% là +2 percentage points, tương đương +5% relative. Có đáng rollout hay không còn phụ thuộc interval, cost, guardrails và segment consistency.

Randomization không bảo đảm external validity. Experiment trên desktop learners ở một quốc gia chưa chắc generalize mobile toàn cầu.

## 13. Dashboard và Simpson's paradox

Aggregate trend có thể đảo khi chia groups. Nếu treatment được dùng nhiều ở course khó, overall completion có thể thấp hơn dù trong từng difficulty group nó tốt hơn. Đây là Simpson's paradox và nhắc rằng mix population ảnh hưởng aggregate.

Segment theo dimension có lý do trước, không đào vô số slices để tìm story. Báo denominators. Rate từ 1/2 cao hơn 400/1,000 nhưng uncertainty khác xa.

Dashboard nên giữ metric definition có version/owner. Khi denominator đổi, trend break cần annotation; không nối thành một series giả đồng nhất.

## 14. Mini practice

Với times `[8, 9, 10, 11, 62]`, tính mean, median, range và mô tả shape. Giải thích statistic nào đại diện typical experience và statistic nào cảnh báo tail. Sau đó tạo hai groups có completion counts, tính percentage-point và relative change với denominator rõ.

Viết experiment plan cho giao diện lesson: population, randomization unit, control/treatment, primary metric, guardrail, exposure, duration logic và limitation. Không cần tính sample size chính xác; phải nói effect nhỏ nhất có ý nghĩa và vì sao stopping rule được đặt trước.

Bài đạt khi conclusion có uncertainty, không suy causation từ observational association và không bỏ missing/outlier không có lý do.

## Checkpoint

Bạn sẵn sàng sang Module 7 khi mô tả distribution bằng center/spread, phân biệt sample/population và SD/SE, giải thích confidence interval/p-value không quá mạnh, và thiết kế A/B test có unit cùng metric rõ. Mental model cần giữ: statistical result là kết luận có điều kiện trên data-generating process và assumptions.
