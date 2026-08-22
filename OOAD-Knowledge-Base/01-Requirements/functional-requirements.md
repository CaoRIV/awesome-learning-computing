# Functional Requirements

> **Mục tiêu:** mô tả hệ thống phải làm gì bằng behavior quan sát được, có trigger, condition, response và boundary rõ ràng.

## Learning outcomes

Sau bài này, bạn có thể:

- nhận diện functional requirement (FR);
- phân biệt FR với business goal, business rule, quality requirement và design decision;
- viết behavior theo trigger–condition–response;
- mô tả input, validation, output và failure behavior mà không khóa UI/implementation;
- phân rã chức năng theo goal/capability thay vì màn hình hoặc database CRUD;
- review và rewrite bad functional requirements.

## 1. Functional requirement là gì?

**Functional requirement** mô tả capability, service, calculation, transformation, interaction hoặc phản ứng mà hệ thống phải thực hiện trong điều kiện xác định.

FR trả lời:

> Khi actor hoặc event X xảy ra, trong condition Y, hệ thống phải tạo response/result Z nào?

Ví dụ:

> `FR-012`: Khi khách hàng yêu cầu báo giá cho giỏ hàng hợp lệ và địa chỉ thuộc vùng phục vụ, hệ thống phải trả về tiền món, phí dịch vụ, phí giao, giảm giá áp dụng, tổng tiền và ETA.

## 2. Vì sao FR cần thiết?

FR nối stakeholder goal với:

- use-case scenarios;
- acceptance criteria;
- domain rules;
- interface behavior;
- design responsibilities;
- functional tests.

Nếu FR chỉ là tên chức năng như “Quản lý đơn hàng”, downstream team phải tự đoán create/update/cancel/authorize/recover nghĩa là gì.

## 3. Thành phần của một FR rõ

| Thành phần | Câu hỏi | Ví dụ |
|---|---|---|
| ID/name | Làm sao trace? | `FR-ORDER-012 — Provide quote` |
| Actor/event | Ai/cái gì kích hoạt? | Customer requests quote |
| Preconditions/context | Khi nào behavior áp dụng? | Cart valid, address serviceable |
| System response | Hệ thống làm gì? | Calculate and return itemized quote |
| Data/input | Thông tin nào được dùng? | Items, quantities, address, coupon |
| Output/state effect | Kết quả quan sát được? | Quote with components and expiry |
| Rule reference | Policy nào chi phối? | `BRULE-PRICE-03` |
| Exception | Input/dependency failure? | Menu changed, address unsupported |
| Acceptance link | Chứng minh bằng gì? | `FR-012-AC-1` |

Không phải mọi câu cần nhét tất cả field. Metadata và linked acceptance criteria có thể giữ detail, miễn tổng artifact set không mơ hồ.

## 4. Các loại functional behavior

### Business functionality

Khả năng tạo giá trị nghiệp vụ: đặt đơn, chuyển tiền, đặt phòng, đăng ký môn.

### User interaction

Hệ thống nhận intent/input và phản hồi: tìm món, xác nhận đơn, báo lỗi. FR tập trung observable behavior, không mặc định button/layout.

### System behavior theo event

Event có thể không do user tạo:

- hết thời hạn giữ phòng;
- payment provider gửi callback;
- lịch định kỳ đóng đăng ký;
- sensor báo sự kiện.

### Input/output và transformation

- validate input;
- calculate price;
- generate statement;
- import/export data;
- map external response thành internal result.

### State-changing behavior

- reserve inventory;
- confirm order;
- cancel booking;
- refund payment.

State effect phải khớp business rule và lifecycle.

## 5. FR vs các concept gần nhau

| Phát biểu | Phân loại | Reasoning |
|---|---|---|
| “Tăng tỷ lệ checkout hoàn tất” | Goal | Outcome kinh doanh |
| “Khách có thể hủy trước khi nhà hàng bắt đầu chuẩn bị” | Need/rule candidate | Chưa nói system response; chứa policy |
| “Khi nhận yêu cầu hủy trước trạng thái Preparing, hệ thống phải hủy đơn và giải phóng payment authorization” | FR | Trigger, condition, response |
| “Đơn ở trạng thái Preparing phải chịu phí hủy” | Business rule | Policy quyết định outcome |
| “Response hủy trả trong 2 giây” | Quality requirement | Mức performance của behavior |
| “Dùng event bus để phát OrderCancelled” | Design decision | Cách triển khai |

### Business rule và FR

Rule tồn tại độc lập với automation; FR mô tả hệ thống enforce/expose rule thế nào.

```text
BRULE-CANCEL-01: Đơn chưa vào Preparing được hủy không mất phí.
FR-CANCEL-01: Khi khách hủy đơn đủ điều kiện, hệ thống phải chuyển đơn sang Cancelled và thông báo số tiền được hoàn.
```

Không nên sao chép rule vào nhiều FR; reference một rule có version giúp consistency.

## 6. Trigger–Condition–Response pattern

### Event-driven form

```text
When <trigger>, while/if <condition>, the system shall <observable response>.
```

Ví dụ:

> Khi payment provider thông báo authorization bị từ chối, hệ thống phải giữ đơn ở trạng thái `AwaitingPayment`, thông báo lý do có thể hiển thị cho khách và cho phép chọn phương thức khác trong thời hạn báo giá.

### Unwanted-event form

```text
If <undesired event>, the system shall <detect/recover/report behavior>.
```

> Nếu menu item bị vô hiệu hóa sau khi khách tạo giỏ nhưng trước khi xác nhận, hệ thống phải từ chối xác nhận, chỉ ra item không còn khả dụng và trả báo giá cập nhật cho các item còn lại.

### State-driven form

```text
While <state>, the system shall <behavior/constraint>.
```

> Trong khi đơn ở trạng thái `OutForDelivery`, hệ thống phải cung cấp trạng thái giao hàng mới nhất cho khách.

Pattern hỗ trợ completeness nhưng không thay thế use-case narrative khi flow dài.

## 7. Input và validation

Một FR về input nên làm rõ:

- semantic của input, không chỉ data type;
- required/optional và default;
- valid range/format/domain rule;
- duplicate/idempotency expectation nếu relevant;
- response khi invalid;
- data source và trust boundary khi external.

Bad:

> Hệ thống phải kiểm tra địa chỉ hợp lệ.

Vấn đề: “hợp lệ” có thể là đúng format, tồn tại trên bản đồ, thuộc vùng phục vụ hoặc đủ chi tiết để giao.

Rewrite thành các concerns:

- `FR-ADDR-01`: Khi khách gửi địa chỉ, hệ thống phải xác định được tọa độ hoặc yêu cầu bổ sung thông tin.
- `FR-ADDR-02`: Trước khi báo giá, hệ thống phải xác định địa chỉ có thuộc vùng phục vụ của nhà hàng hay không.
- `BRULE-ADDR-01`: Địa chỉ giao phải nằm trong vùng phục vụ tại thời điểm báo giá.

## 8. Output và observable result

Output có thể là:

- thông tin hiển thị/trả qua API;
- state transition;
- notification;
- generated artifact;
- request tới external system;
- audit event nếu có requirement.

Tránh “xử lý đơn hàng thành công” nếu không nói observable postcondition. Hãy xác định order state, payment effect, reservation effect và response cho actor.

## 9. Functional decomposition

### Theo actor goal/capability

```text
Place Order
├── Obtain Quote
├── Validate Availability
├── Authorize Payment
└── Confirm Order
```

Đây là decomposition để phân tích behavior. Không mặc định mỗi node là use case hoặc service/class.

### Không phân rã theo UI

“Trang giỏ hàng”, “nút thanh toán”, “popup lỗi” là solution/UI elements. Chúng có thể xuất hiện trong UI specification sau này nhưng không phải capability structure ổn định.

### Không phân rã máy móc theo CRUD

`CreateOrder`, `ReadOrder`, `UpdateOrder`, `DeleteOrder` che mất business semantics. Domain thường cần `PlaceOrder`, `AcceptOrder`, `CancelOrder`, `MarkDelivered`; mỗi operation có rule khác nhau.

## 10. Worked example — Place Food Order

### Upstream goal

Khách muốn biết chính xác giá và xác nhận đơn có thể thực hiện được.

### Candidate FR set

| ID | Statement | Rule/dependency |
|---|---|---|
| `FR-CART-01` | Khi khách thêm item đang bán, hệ thống phải lưu item, option, quantity và unit price reference vào giỏ. | Menu availability |
| `FR-QUOTE-01` | Khi khách yêu cầu báo giá cho giỏ hợp lệ, hệ thống phải tính và trả breakdown cùng expiry time. | Pricing rules |
| `FR-QUOTE-02` | Nếu giá/menu thay đổi trước xác nhận, hệ thống phải yêu cầu khách chấp nhận báo giá mới. | Quote consistency |
| `FR-PAY-01` | Khi khách xác nhận order, hệ thống phải yêu cầu payment provider authorize tổng tiền của báo giá còn hiệu lực. | Provider/API |
| `FR-ORDER-01` | Chỉ sau khi payment authorization thành công, hệ thống mới gửi order request tới nhà hàng. | State rule |
| `FR-ORDER-02` | Khi nhà hàng từ chối hoặc timeout, hệ thống phải hủy authorization và thông báo kết quả cho khách. | Timeout/refund rule |

### Acceptance example

```gherkin
Given giỏ có hai item đang bán và địa chỉ thuộc vùng phục vụ
And coupon hợp lệ
When khách yêu cầu báo giá
Then response chứa subtotal, delivery fee, service fee, discount, total, currency và expiry time
And total bằng tổng các component theo pricing rules hiện hành
```

Given nên dùng như notation cho example/criterion, không biến mọi FR thành Gherkin nếu prose/table rõ hơn.

## 11. Bad requirements và rewrite

### Bad 1

> Hệ thống cho phép quản lý đơn hàng.

**Diagnosis:** actor, goals, operations, rules và outcomes đều thiếu.

**Rewrite direction:** tách place, accept, cancel, assign, deliver và refund theo roles/conditions.

### Bad 2

> Người dùng nhấn nút xanh để đặt đơn.

**Diagnosis:** khóa UI và bỏ behavior sau interaction.

**Rewrite:** Khi khách xác nhận báo giá còn hiệu lực và payment authorization thành công, hệ thống phải tạo order với một order identifier và gửi yêu cầu tới nhà hàng.

### Bad 3

> Hệ thống phải tự động xử lý lỗi thanh toán.

**Diagnosis:** “lỗi”, “xử lý” và expected outcome không rõ.

**Rewrite direction:** liệt kê decline, timeout, duplicate callback, unknown status và recovery/notification tương ứng.

## 12. Functional requirement quality checks

- **Necessary:** có stakeholder goal/rule/risk biện minh?
- **Atomic enough:** có thể test và thay đổi mà không trộn behavior không liên quan?
- **Unambiguous:** một cách hiểu hợp lý?
- **Complete in context:** trigger, condition và response đủ?
- **Consistent:** không xung đột state/rule/term?
- **Feasible:** dependency và technology có khả năng hỗ trợ?
- **Verifiable:** có observable result và oracle?
- **Traceable:** có source, downstream links?
- **Implementation-independent enough:** không khóa solution khi không cần?

## 13. Khi không nên dùng một câu FR độc lập

- Flow nhiều bước và alternative: dùng use-case specification, rồi trace tới FR.
- Rule table phức tạp: tách decision table/business rules.
- Data structure chi tiết: dùng data/interface specification.
- UI interaction pixel-level: dùng UX/UI specification.
- Algorithm bắt buộc vì compliance: ghi constraint/rule và rationale riêng.

## 14. Common mistakes

1. Viết tên feature thay vì behavior.
2. Dùng passive voice làm mất actor/system responsibility.
3. Trộn nhiều triggers và outcomes trong một câu.
4. Chỉ mô tả success path.
5. Dùng “real-time”, “automatic”, “appropriate” không có semantic.
6. Biến business rule thành hard-coded behavior ở nhiều nơi.
7. Gắn FR vào screen nên mất ổn định khi UI đổi.
8. Tạo FR từ giải pháp hiện tại mà không xác minh need.

## 15. Artifact checklist

- [ ] FR có ID, name, source và rationale?
- [ ] Boundary và actor/event rõ?
- [ ] Trigger, condition và response quan sát được?
- [ ] Input/output/state effects đủ semantic?
- [ ] Failure/alternative path đã xét?
- [ ] Business rule/reference tách riêng khi cần?
- [ ] Quality target nằm trong QR tương ứng?
- [ ] Không khóa UI/architecture không cần thiết?
- [ ] Có acceptance criteria hoặc verification method?
- [ ] Trace được tới business goal?

## 16. Practice và exam review

- [Bài tập Basic → Case Study](../08-Exercises/01-requirements.md#functional-requirements)
- [Key concepts, comparison và scenario questions](../09-Exam-Preparation/01-requirements.md#functional-requirements)

## 17. Summary và dependency tiếp theo

FR xác định behavior, nhưng behavior chỉ hữu ích khi biết mức chất lượng và constraints. Học tiếp [Non-functional / Quality Requirements](non-functional-requirements.md).
