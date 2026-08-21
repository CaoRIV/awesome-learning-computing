# Requirement Prioritization

> **Mục tiêu:** quyết định requirement nào cần được thực hiện trong một scope/release cụ thể dựa trên value, risk, dependency và constraint; không biến MoSCoW thành nhãn cảm tính.

## Learning outcomes

Sau bài này, bạn có thể:

- phân biệt priority, sequence, urgency và release scope;
- áp dụng MoSCoW với criteria rõ;
- kết hợp business value, risk, cost, dependency và learning value;
- xử lý tình huống “mọi thứ đều là Must”;
- tổ chức negotiation và ghi rationale;
- re-prioritize khi evidence hoặc constraint thay đổi.

## 1. Prioritization là gì?

**Requirement prioritization** là quá trình so sánh relative importance và timing của requirements để phân bổ nguồn lực hữu hạn và đạt outcome/risk profile mong muốn.

Priority không phải thuộc tính vĩnh viễn của requirement. Nó phụ thuộc:

- business goal hiện tại;
- release horizon;
- stakeholder/market deadline;
- risk và uncertainty;
- cost/capacity;
- dependencies;
- regulatory obligation;
- available workaround.

## 2. Các concept thường bị trộn

| Concept | Câu hỏi | Ví dụ |
|---|---|---|
| Importance/value | Requirement đóng góp bao nhiêu? | Fee transparency ảnh hưởng conversion |
| Urgency | Khi nào value/risk thay đổi? | Regulation có hiệu lực tháng tới |
| Priority | So với item khác, nên chú ý/thực hiện mức nào? | Must trong Release 1 |
| Sequence | Thứ tự implementation/learning | Quote trước Place Order |
| Dependency | Item nào cần item khác? | Order confirmation cần payment result |
| Release scope | Cam kết cho mốc cụ thể | MVP khu vực thí điểm |

Một item có value cao nhưng chưa cần làm trước nếu dependency hoặc uncertainty cần giải quyết bằng spike. Một technical enabler có user value gián tiếp nhưng sequence sớm.

## 3. Inputs cho prioritization

### Value

- business outcome;
- user outcome;
- revenue/cost reduction;
- risk reduction;
- compliance;
- strategic learning.

### Cost và capacity

Estimate không hoàn hảo nhưng vẫn cần relative view. Include development, testing, operations, migration, support và opportunity cost.

### Risk

- nếu không làm;
- nếu làm sai;
- technical/market uncertainty;
- security/safety/legal exposure;
- dependency/vendor risk.

### Dependency

- logical prerequisite;
- shared enabler;
- external deadline;
- bundled value;
- incompatible choices.

### Evidence confidence

High-value claim với evidence yếu có thể ưu tiên discovery/prototype, chưa chắc ưu tiên full build.

## 4. MoSCoW

MoSCoW phân loại items trong một **timebox/scope cụ thể**:

### Must Have

Item bắt buộc để release đạt mục tiêu tối thiểu, hợp pháp/an toàn, hoặc hoạt động được. Nếu thiếu, release không được chấp nhận hoặc cần thay đổi mục tiêu/timebox.

Must test:

- Release có còn đạt stated objective nếu bỏ item?
- Có workaround chấp nhận được trong timebox không?
- Item có obligation bắt buộc áp dụng không?
- Dependency có thật sự cần cho Must outcome không?

### Should Have

Quan trọng và tạo value/risk reduction đáng kể, nhưng release vẫn usable/viable nếu thiếu trong thời gian giới hạn và consequence được chấp nhận.

### Could Have

Có value nhưng impact thấp hơn; thường là contingency scope khi capacity cho phép. Could không có nghĩa “vô giá trị”.

### Won't Have This Time

Chủ động không nằm trong timebox/scope hiện tại. Phải ghi rõ **this time** hoặc disposition khác: rejected, future candidate, needs discovery. “Won't” bảo vệ focus và expectation.

## 5. MoSCoW cần context contract

Trước workshop, ghi:

- mục tiêu release;
- timebox/capacity range;
- non-negotiable obligations;
- quality floor;
- target users/market;
- assumptions;
- decision owner.

Không có context, stakeholder sẽ gắn Must cho mọi item họ quan tâm.

## 6. Full example — Food Delivery Release 1

### Release objective

Cho phép khách trong một khu vực thí điểm đặt và theo dõi đơn từ nhóm nhà hàng đã onboard; card payment qua một provider; operations có thể xử lý failure cơ bản.

### Prioritized set

| ID | Requirement | MoSCoW | Rationale/consequence |
|---|---|---|---|
| `FR-QUOTE-01` | Itemized quote trước xác nhận | Must | Không có giá cuối thì không thể consent/pay đúng |
| `FR-PAY-01` | Authorize card payment | Must | Payment method duy nhất trong release |
| `FR-ORDER-01` | Gửi order tới restaurant sau authorization | Must | Core outcome |
| `FR-ORDER-02` | Release authorization khi restaurant reject/timeout | Must | Tránh giữ tiền sai; financial risk |
| `FR-STATUS-01` | Hiển thị lifecycle status | Must | Core promise và supportability |
| `QR-SEC-01` | Authorization/audit cho support access | Must | Privacy/security acceptance floor |
| `FR-CANCEL-01` | Customer self-cancel trước Preparing | Should | Value cao; support workaround tạm có thể dùng |
| `FR-NOTIFY-01` | Push notification khi status đổi | Should | Giảm polling/support; in-app status vẫn có |
| `FR-PROMO-01` | Coupon code | Could | Growth value nhưng không cần cho core flow |
| `FR-SOCIAL-01` | Chia sẻ đơn lên social | Won't this release | Không hỗ trợ objective thí điểm |

### Dependency correction

Nếu `FR-CANCEL-01` bị xếp Should nhưng regulation yêu cầu self-service cancellation trước fulfillment, nó có thể trở thành Must. Classification thay đổi theo obligation/evidence, không theo sở thích.

## 7. Khi mọi thứ đều là Must

### Nguyên nhân

- release objective quá rộng;
- không có consequence test;
- stakeholder bảo vệ scope bằng label;
- obligation và preference bị trộn;
- item quá lớn, chứa cả must và optional parts;
- chưa có cost/capacity visibility;
- không có decision authority.

### Cách xử lý

1. Re-state release outcome và fixed constraints.
2. Split requirements theo independent outcomes/rules.
3. Dùng “nếu bỏ item, điều gì xảy ra?” với evidence.
4. Tách compliance minimum khỏi enhancement.
5. Xem workaround và duration.
6. Map dependencies, loại false prerequisites.
7. Tạo scenario budget: capacity cho Must phải để lại contingency.
8. Escalate conflict tới đúng owner, ghi dissent.

Nếu Must vượt capacity, không “ép team làm nhanh hơn” như một kỹ thuật prioritization. Phải đổi scope, time, resources, quality floor hợp lệ hoặc objective.

## 8. Các phương pháp bổ sung

### Ranking / pairwise comparison

So sánh từng cặp hoặc tạo thứ tự. Hữu ích với danh sách nhỏ; khó khi items nhiều và dependencies phức tạp.

### Weighted scoring

```text
Score = Value weight + Risk reduction + Strategic fit − Cost − Uncertainty penalty
```

Ưu điểm: minh bạch criteria. Nhược điểm: số tạo cảm giác chính xác giả; weights và scales dễ bị game. Dùng như conversation aid, không phải máy ra quyết định.

### Value vs effort matrix

Nhanh để triage. Không thể hiện obligation, dependency, risk hoặc confidence nếu không thêm dimensions.

### Kano-style thinking

Phân biệt basic expectations, performance attributes và delighters. Hữu ích cho product discovery, nhưng không thay compliance/risk analysis.

### Risk-first / learning-first

Ưu tiên spike/prototype cho item uncertainty cao để giảm cost of ignorance. “Làm trước” ở đây là học/validate, chưa chắc build production.

## 9. Business value vs technical enablers

Technical enabler không nên được che dưới user story giả. Ghi rõ:

- downstream requirement được enable;
- risk/quality được giảm;
- evidence/exit condition;
- cost nếu trì hoãn;
- có alternative nào.

Ví dụ contract-test harness cho payment provider không trực tiếp tạo UI value nhưng giảm integration/reliability risk cho Must payment flow.

## 10. Prioritizing quality requirements

Không xếp quality như feature tùy chọn nếu nó là acceptance floor. Thay vào đó:

- xác định minimum acceptable threshold;
- phân biệt target vs stretch;
- ưu tiên critical journeys/data;
- ghi trade-off và risk acceptance;
- tránh giảm quality âm thầm để giữ nhiều features.

Ví dụ security authorization không phải Could; push animation có thể là Could. Availability 99,9% vs 99,95% cần cost/risk analysis thay vì nhãn đơn giản.

## 11. Negotiation workshop

### Inputs

- release objective và constraints;
- candidate requirements với source/rationale;
- dependency map;
- value/risk/cost evidence;
- open conflicts;
- decision roles.

### Flow

1. Confirm objective và quality floor.
2. Identify non-negotiable obligations.
3. Review Must candidates bằng Must test.
4. Split oversized items.
5. Map dependencies/enablers.
6. Rank Should/Could theo value/risk/confidence.
7. Check capacity và contingency.
8. Record decision, assumptions và review triggers.

### Output

Không chỉ là label; phải có rationale, owner, timebox, dependency và consequence.

## 12. Re-prioritization

Trigger:

- business strategy hoặc market thay đổi;
- regulation/contract;
- prototype/user evidence;
- estimate/dependency mới;
- incident/security discovery;
- completed item làm mở khóa option;
- capacity/timebox đổi.

Không re-prioritize liên tục theo stakeholder mới nhất. Dùng cadence và emergency rule; communicate impact lên committed scope.

## 13. Common mistakes

1. Gắn priority trước analysis.
2. Mọi item là Must.
3. Ưu tiên theo người nói lớn nhất.
4. Không ghi timebox cho Won't.
5. Dùng score như sự thật khách quan.
6. Bỏ qua dependency và technical enabler.
7. Đổi priority nhưng không đổi expectation/scope.
8. Hy sinh quality floor ngầm để thêm feature.
9. Không phân biệt build priority với learning priority.

## 14. Artifact checklist

- [ ] Release objective/timebox/target users rõ?
- [ ] Value, risk, cost, dependency và confidence có evidence?
- [ ] Must vượt qua consequence test?
- [ ] Obligation và preference tách rõ?
- [ ] Oversized requirement đã split?
- [ ] Quality floor được bảo vệ?
- [ ] Enablers trace tới downstream value/risk?
- [ ] Won't có horizon/disposition?
- [ ] Decision authority và dissent được ghi?
- [ ] Review triggers xác định?

## 15. Exercises

### Basic

1. Phân biệt Should và Could bằng consequence, không bằng cảm giác.
2. Cho ví dụ priority khác sequence.
3. Giải thích vì sao MoSCoW cần timebox.

### Intermediate

Ưu tiên 12 requirements cho Library Release 1. Viết release objective, Must test, dependencies và rationale.

### Advanced

Một release banking có compliance deadline, legacy migration, security debt và feature commitments. Thiết kế decision model, negotiation và risk acceptance process.

### Case Study

Tạo MoSCoW backlog cho University Enrollment launch. Capacity chỉ đủ khoảng 60% candidate scope. Bao gồm quality floor và contingency.

## 16. Exam preparation

### Key concepts

- Priority là relative và context-dependent.
- MoSCoW chỉ có nghĩa trong timebox/scope cụ thể.
- Must được kiểm tra bằng consequence và obligation.
- Learning work có thể cần sequence sớm dù feature chưa build.

### Common exam questions

1. Trình bày MoSCoW và common misuse.
2. Làm gì khi mọi requirement là Must?
3. Dependency ảnh hưởng priority và sequence ra sao?

### Comparison questions

- Priority vs urgency.
- Priority vs sequence.
- Must vs Should.
- Value-first vs risk-first prioritization.

### Scenario question

Food Delivery chỉ đủ capacity cho checkout, tracking hoặc promotion. Hãy tạo release objective, phân tích dependency/value/risk và quyết định có rationale.

## 17. Summary và dependency tiếp theo

Prioritization chọn scope có chủ đích. Học tiếp [User Stories](user-stories.md) để tổ chức conversation và delivery slices mà vẫn trace về requirements.

