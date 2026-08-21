# Non-functional / Quality Requirements

> **Mục tiêu:** biến các từ như nhanh, an toàn, ổn định và dễ dùng thành quality scenarios có context, measure và evidence.

## Learning outcomes

Sau bài này, bạn có thể:

- phân biệt quality requirement, functional requirement và constraint;
- viết measurable quality scenario;
- phân tích performance, security, reliability, availability, scalability, maintainability, usability, portability và compatibility;
- nhận diện trade-offs và conflict giữa quality attributes;
- giải thích khi một “non-functional” concern sinh ra functional behavior;
- chọn verification method phù hợp.

## 1. Thuật ngữ và mental model

**Non-functional requirement (NFR)** là thuật ngữ phổ biến cho requirements mô tả quality, constraint hoặc điều kiện xuyên suốt. Tuy nhiên, tên “non-functional” dễ khiến người học hiểu sai rằng chúng không liên quan chức năng hoặc ít quan trọng.

Trong knowledge base này:

- dùng **quality requirement (QR)** khi nói về mức chất lượng cần đạt;
- dùng **constraint** cho hạn chế bắt buộc về solution/process/environment;
- dùng **NFR** khi đối chiếu thuật ngữ phổ biến hoặc nhóm rộng.

### Quality requirement trả lời

> Hành vi hoặc hệ thống phải tốt đến mức nào, dưới điều kiện nào, đo tại đâu và evidence gì chứng minh?

Ví dụ:

> `QR-PERF-001`: Trong giờ cao điểm với 5.000 yêu cầu báo giá đồng thời, 95% response hợp lệ phải hoàn tất trong 2 giây và 99% trong 5 giây, đo tại API gateway trong khoảng 15 phút.

## 2. FR vs QR vs constraint

| Loại | Câu hỏi | Ví dụ |
|---|---|---|
| FR | Hệ thống làm gì? | Hệ thống trả báo giá theo thành phần |
| QR | Tốt đến mức nào/trong điều kiện nào? | 95% báo giá trong 2 giây |
| Constraint | Lựa chọn nào bị giới hạn? | Dữ liệu phải lưu trong khu vực pháp lý được phê duyệt |
| Business rule | Policy domain nào quyết định behavior? | Phí giao phụ thuộc vùng và khoảng cách |

Một concern có thể tạo cả FR và QR. Security cần authentication behavior (FR) và resistance/evidence properties (QR).

## 3. Quality scenario template

Một quality scenario đầy đủ gồm:

| Thành phần | Ý nghĩa | Performance example |
|---|---|---|
| Source | Nguồn kích thích | Customer app |
| Stimulus | Event/condition | Request quote |
| Environment | Hoàn cảnh vận hành | Peak hour, normal dependency health |
| Artifact | Phần hệ thống bị tác động | Quote API |
| Response | Phản ứng mong muốn | Return valid itemized quote |
| Measure | Ngưỡng kiểm chứng | p95 ≤ 2 s, p99 ≤ 5 s |

Template ngăn việc viết “system should be fast”, nhưng số đo phải xuất phát từ goal/user tolerance/risk và feasibility, không được analyst tự bịa.

## 4. Performance

### Concepts

- **Latency/response time:** thời gian hoàn thành một request.
- **Throughput:** lượng công việc mỗi đơn vị thời gian.
- **Concurrency:** số workload đồng thời.
- **Resource utilization:** CPU, memory, network, connection.
- **Deadline:** hoàn tất trước mốc tuyệt đối.
- **Percentile:** phân phối trải nghiệm, thường hữu ích hơn average.

### Câu hỏi elicitation

- Operation nào nhạy cảm?
- Workload bình thường/đỉnh/burst là bao nhiêu?
- Dataset/cache/dependency condition nào?
- Đo từ client, gateway hay component?
- Threshold và thời gian quan sát?
- Khi overload cần degrade thế nào?

### Bad

> Hệ thống phải phản hồi tức thì cho nhiều người dùng.

### Better

> Trong thời gian mở đăng ký môn, với 2.000 sinh viên đồng thời và catalog 10.000 lớp học phần, 95% truy vấn tìm lớp phải trả kết quả đầu tiên trong 3 giây, đo từ web client trên mạng campus chuẩn.

## 5. Security

Security không phải một checkbox “secure”. Phân tích assets, threats, actors và obligations.

### Quality/behavior areas

- authentication;
- authorization và least privilege;
- confidentiality;
- integrity;
- accountability/auditability;
- non-repudiation khi cần;
- data minimization và privacy;
- detection, response và recovery;
- resistance to abuse/fraud.

### Example set

- `FR-SEC-01`: Trước khi xem lịch sử đơn, hệ thống phải xác thực customer identity.
- `FR-SEC-02`: Hệ thống chỉ trả đơn thuộc customer đã xác thực, trừ role support được cấp quyền và có reason code.
- `QR-SEC-01`: Mọi lần truy cập lịch sử đơn bởi support phải tạo audit record gồm actor, order, time, reason và result.
- `CON-SEC-01`: Card data không được lưu trong platform; payment provider được phê duyệt xử lý dữ liệu đó.

Không ghi chi tiết bảo mật nhạy cảm vào tài liệu công khai không được kiểm soát; specification vẫn phải đủ cho implementation và verification bởi audience phù hợp.

## 6. Reliability

**Reliability** là khả năng thực hiện đúng chức năng trong điều kiện xác định trong một khoảng thời gian.

Measures có thể gồm:

- failure rate;
- successful operation ratio;
- mean time between failures với context phù hợp;
- data correctness/invariant violation rate;
- recovery success.

Example:

> Với payment callbacks hợp lệ, hệ thống phải xử lý sao cho không tạo hơn một order cho cùng một idempotency key; kiểm chứng qua fault-injection test với duplicate và reordered callbacks.

Đây là reliability/consistency concern tạo functional behavior về duplicate handling.

## 7. Availability

**Availability** là tỷ lệ thời gian hệ thống/service sẵn sàng cung cấp service theo định nghĩa.

Một target availability thiếu meaning nếu không rõ:

- service nào;
- observation window;
- planned maintenance có tính không;
- partial degradation tính thế nào;
- measurement point;
- dependency exclusions;
- recovery objectives.

Example:

> Order Status API phải đạt monthly availability 99,9%, đo từ hai synthetic probes ngoài production network; response lỗi 5xx hoặc timeout trên 5 giây được tính là unavailable, không loại trừ planned maintenance.

Trade-off: availability cao hơn thường tăng cost, operational complexity và consistency trade-offs.

## 8. Scalability

**Scalability** là khả năng duy trì target quality khi workload/data/geography tăng bằng cách bổ sung hoặc điều chỉnh resources với chi phí chấp nhận được.

“Hỗ trợ 1 triệu users” chưa đủ; cần active users, request pattern, data volume và growth.

Example:

> Khi số restaurant tăng từ 10.000 lên 100.000 và peak search throughput tăng từ 500 lên 5.000 request/s, hệ thống phải duy trì search p95 dưới 3 giây bằng cách tăng tối đa 10 lần compute resources mà không partition thủ công theo restaurant.

Không phải dự án nào cũng cần target scalability dài hạn. Overdesign cho growth không có evidence là một cost.

## 9. Maintainability

**Maintainability** là mức dễ dàng và an toàn khi hiểu, sửa, test, deploy và vận hành thay đổi.

Các proxy có thể đo:

- thời gian thực hiện change scenario cụ thể;
- phạm vi modules bị ảnh hưởng;
- automated regression coverage cho critical rules;
- deploy/recovery time;
- dependency update compatibility;
- documentation/build reproducibility.

Example:

> Một payment provider mới tuân theo payment contract đã xác định phải được tích hợp mà không sửa order-domain rules; team mục tiêu hoàn tất implementation và automated contract tests trong tối đa 10 engineer-days.

Target này cần baseline/team capability; không biến maintainability thành một số đẹp nhưng vô căn cứ.

## 10. Usability và accessibility

**Usability** xét effectiveness, efficiency và satisfaction của user cụ thể trong context cụ thể. Accessibility bảo đảm người có khả năng đa dạng có thể sử dụng.

Measures:

- task completion rate;
- time on task;
- error/recovery rate;
- learnability;
- satisfaction score có protocol;
- conformance với accessibility criteria được chọn;
- assistive-technology task completion.

Example:

> Trong usability test với 12 người dùng mới đáp ứng profile nghiên cứu, ít nhất 10 người phải hoàn tất đặt một đơn hợp lệ không cần trợ giúp trong 4 phút; không ai xác nhận nhầm địa chỉ sau màn hình review.

Không dùng “số click” làm proxy duy nhất; giảm click có thể làm giảm clarity hoặc tăng error.

## 11. Portability

**Portability** là khả năng chuyển software/artifact giữa environments với effort và impact xác định.

Example:

> Deployment package phải chạy trên hai Linux distributions được tổ chức phê duyệt bằng cùng build artifact; khác biệt environment chỉ được cung cấp qua external configuration đã liệt kê.

Portability có cost. Nếu product chỉ chạy trong một managed environment ổn định, target đa nền tảng có thể không cần thiết.

## 12. Compatibility và interoperability

- **Compatibility:** cùng tồn tại/hoạt động đúng trong shared environment hoặc với phiên bản liên quan.
- **Interoperability:** trao đổi và sử dụng thông tin qua interface/protocol.

Example:

> Public Order API phải chấp nhận client version N và N−1 trong 90 ngày sau khi version N phát hành; breaking changes phải dùng versioned endpoint và migration notice.

Cần nói rõ direction và semantic compatibility, không chỉ “tích hợp với hệ thống X”.

## 13. Trade-offs giữa quality attributes

| Quyết định | Có thể cải thiện | Có thể làm xấu/đắt hơn |
|---|---|---|
| Strong authentication mỗi action | Security | Usability, latency |
| Replication đa vùng | Availability, latency vùng xa | Cost, consistency complexity |
| Aggressive caching | Performance, scalability | Freshness, invalidation complexity |
| Synchronous confirmation | Immediate consistency/clarity | Availability, latency |
| Abstraction nhiều provider | Maintainability với known variation | Initial complexity, debugging |
| Detailed audit | Accountability | Storage, privacy, performance |

Trade-off không có nghĩa phải hy sinh tùy tiện. Cần rank scenarios, xem alternatives và ghi decision/risk.

## 14. Khi NFR trở thành functional behavior

Quality concern thường đòi system behaviors:

| Concern | Functional behavior phát sinh |
|---|---|
| Security | login, authorize, revoke, audit, alert |
| Reliability | retry, deduplicate, recover, reconcile |
| Availability | health check, failover, graceful degradation |
| Usability | validation feedback, undo, progress indication |
| Privacy | consent, export, delete, retention enforcement |
| Maintainability | diagnostic endpoint, configuration validation |

Vì vậy “functional vs non-functional” là classification hữu ích nhưng không phải bức tường tuyệt đối.

## 15. Scope của QR

### Global QR trap

> Hệ thống phải phản hồi trong 2 giây.

Không phải operation nào cũng cần cùng target. Export report lớn và validate password có context khác nhau.

QR nên gắn với:

- operation/feature cụ thể;
- critical user journey;
- component/interface nếu requirement thực sự ở mức đó;
- toàn hệ thống chỉ khi property có nghĩa global.

## 16. Verification methods

| Quality | Evidence phù hợp |
|---|---|
| Performance/scalability | Load, stress, soak test với workload model |
| Security | Review, automated scan, penetration test, audit evidence |
| Reliability | Fault injection, recovery/duplicate tests, production measures |
| Availability | Monitoring/SLO report, synthetic probes, failover test |
| Maintainability | Change scenario, architecture/code review, pipeline evidence |
| Usability | Representative-user usability study |
| Accessibility | Automated checks + manual/assistive technology tests |
| Compatibility | Contract, matrix và backward-compatibility tests |

Không chọn verification method sau cùng; cách chứng minh ảnh hưởng requirement wording và feasibility.

## 17. Worked example — Food Delivery quality set

| ID | Scenario rút gọn | Link |
|---|---|---|
| `QR-PERF-001` | Peak quote p95 ≤ 2 s, p99 ≤ 5 s tại gateway | `FR-QUOTE-01` |
| `QR-AVAIL-001` | Order status monthly availability 99,9% theo probe definition | `FR-STATUS-01` |
| `QR-REL-001` | Duplicate payment callbacks không tạo duplicate order | `FR-PAY-01` |
| `QR-SEC-001` | Support access tạo immutable audit event với required fields | `FR-SEC-02` |
| `QR-USE-001` | Representative new users hoàn tất order task theo protocol | Place-order journey |
| `QR-COMP-001` | Mobile API hỗ trợ client N/N−1 theo migration window | API lifecycle |

### Conflict review

Live tracking refresh mỗi giây có thể tăng perceived responsiveness nhưng tăng battery/network/backend load và privacy exposure. Requirement cần dựa trên user need, delivery accuracy và cost, không mặc định “real-time nhất có thể”.

## 18. Bad requirements và rewrite

### “System should be fast”

Không tốt vì:

- “system” và operation không rõ;
- không có workload/environment;
- “fast” không có measure;
- không có measurement point;
- không xác định percentile/failure handling.

Rewrite chỉ hợp lệ sau elicitation:

> Với 1.000 customer sessions đồng thời trong normal operation, 95% yêu cầu tải order status phải trả dữ liệu không quá 10 giây tuổi trong 2 giây, đo tại mobile client trên network profile đã định nghĩa.

### “System must be 100% available”

Absolute target thường không khả thi hoặc không được định nghĩa. Cần service, window, exclusions, measurement, cost và recovery requirements.

### “System must be user-friendly”

Thay bằng task/user/context và measure; vẫn có thể giữ qualitative research như evidence bổ sung.

## 19. Khi không nên định lượng giả tạo

- Không có baseline hoặc stakeholder value threshold: chạy research/prototype để thu evidence.
- Chất lượng có dimension định tính: kết hợp qualitative evaluation với measures.
- Measure dễ bị game nhưng không đại diện outcome: tránh tối ưu proxy.
- Early discovery: ghi target range/assumption và kế hoạch validation thay vì số chính xác giả.

## 20. Common mistakes

1. Gom quality, constraint và architecture vào một mục “NFR”.
2. Dùng adjective không measure.
3. Có measure nhưng thiếu workload/environment/measurement point.
4. Chỉ viết normal condition, không overload/failure/degraded mode.
5. Đặt target mọi operation giống nhau.
6. Không ghi verification method.
7. Bỏ qua trade-off/cost.
8. Xem quality requirements sau khi chức năng đã thiết kế xong.
9. Chọn technology thay vì mô tả outcome cần đạt.

## 21. Artifact checklist

- [ ] QR liên kết behavior/artifact cụ thể?
- [ ] Source, stimulus, environment, response và measure rõ?
- [ ] Measurement point/window/workload định nghĩa?
- [ ] Threshold có rationale và feasibility evidence?
- [ ] Normal, peak, failure hoặc degraded conditions phù hợp?
- [ ] Constraint được tách khỏi quality target?
- [ ] Trade-offs và conflicting stakeholders đã review?
- [ ] Verification method và environment khả thi?
- [ ] Có downstream design/test trace dự kiến?
- [ ] Thuật ngữ metric không bị nhầm average/percentile/rate?

## 22. Exercises

### Basic

1. Phân biệt performance, scalability và availability bằng cùng một API example.
2. Viết một quality scenario đủ sáu thành phần.
3. Cho hai ví dụ security concern sinh functional requirements.

### Intermediate

Rewrite năm NFR: “nhanh”, “an toàn”, “dễ bảo trì”, “luôn sẵn sàng”, “hỗ trợ nhiều người dùng”. Ghi rõ câu hỏi cần hỏi trước khi chọn số.

### Advanced

Phân tích trade-offs cho banking transfer giữa consistency, availability, latency, security và auditability. Đề xuất scenario priorities và verification evidence, không chọn vendor.

### Case Study

Tạo quality-attribute set cho Hotel Management System gồm performance, availability, reliability, security, usability, maintainability và compatibility. Liên kết mỗi QR với operation hoặc journey.

## 23. Exam preparation

### Key concepts

- Quality requirement cần context và measure.
- Constraint giới hạn solution; QR mô tả mức chất lượng.
- Quality attributes tương tác và tạo trade-offs.
- NFR concern thường sinh functional behavior.

### Common exam questions

1. Vì sao “system should be fast” là requirement kém?
2. Trình bày cấu trúc quality scenario.
3. Giải thích quan hệ giữa availability, reliability và scalability.

### Comparison questions

- Functional vs quality requirement.
- Reliability vs availability.
- Performance vs scalability.
- Portability vs compatibility/interoperability.
- Security requirement vs security design mechanism.

### Scenario question

Một hệ thống đăng ký môn bị quá tải trong 15 phút đầu. Viết performance/scalability/availability scenarios, nêu trade-offs và verification plan.

## 24. Summary và dependency tiếp theo

Requirements chỉ tốt khi được khám phá từ nguồn phù hợp. Học tiếp [Requirement Elicitation](elicitation.md) để chọn kỹ thuật thu thập knowledge và uncertainty.

