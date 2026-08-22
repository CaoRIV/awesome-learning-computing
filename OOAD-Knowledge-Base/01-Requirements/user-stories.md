# User Stories và Acceptance Criteria

> **Mục tiêu:** dùng user story như lời mời đối thoại về giá trị và một delivery slice nhỏ; không xem format một dòng là toàn bộ specification.

## Learning outcomes

Sau bài này, bạn có thể:

- giải thích user story, Three Cs và format phổ biến;
- viết story có value và boundary phù hợp;
- áp dụng INVEST như heuristic;
- viết acceptance criteria bao phủ rule và exception;
- split story theo business value;
- phân biệt requirement, user story và use case;
- nhận diện khi user story không phải representation phù hợp.

## 1. User story là gì?

**User story** là mô tả ngắn về capability/outcome từ góc nhìn một role, được dùng để khởi tạo conversation và tổ chức delivery/validation.

Format phổ biến:

```text
As a <role>
I want <goal/capability>
So that <value/outcome>
```

Ví dụ:

> As a customer, I want to see an itemized quote before confirming, so that I can understand the full cost and decide whether to order.

Format giúp giữ role, goal và value nhưng không tự bảo đảm story tốt.

## 2. Three Cs

### Card

Tên và mô tả ngắn đủ để nhận diện work item.

### Conversation

Trao đổi về examples, rules, alternatives, quality, assumptions và design options. Đây là phần tạo shared understanding.

### Confirmation

Acceptance criteria/examples cho biết outcome nào được chấp nhận. Confirmation không nhất thiết chỉ là automated tests, nhưng phải tạo được evidence.

Story chỉ có Card là reminder bị hiểu nhầm thành complete requirement.

## 3. Story anatomy

| Thành phần | Câu hỏi review |
|---|---|
| Role | Role có goal khác biệt hay chỉ là “user” chung chung? |
| Goal | Mô tả outcome/capability hay UI task? |
| Value | Vì sao cần; có trace tới stakeholder/business goal? |
| Scope | Có thể hoàn tất/validate trong iteration? |
| Rules | Condition/calculation/eligibility nào? |
| Examples | Normal, boundary, invalid, failure? |
| Quality | Performance/security/usability target liên quan? |
| Dependencies | Data/service/story nào cần trước? |

## 4. Acceptance Criteria

**Acceptance criteria (AC)** là các điều kiện cụ thể mà story/requirement phải đáp ứng để stakeholder có cơ sở chấp nhận.

AC tốt:

- nói về observable outcome;
- cụ thể nhưng không khóa implementation không cần thiết;
- bao phủ rule và boundaries quan trọng;
- nhất quán với system boundary;
- testable hoặc reviewable;
- liên kết QR khi quality quan trọng.

### Checklist form

- Quote hiển thị subtotal, delivery fee, service fee, discount, total và currency.
- Quote có expiry time.
- Item không còn bán được chỉ rõ trước confirmation.
- Coupon invalid không làm mất cart.

### Given–When–Then form

```gherkin
Given a serviceable address and a cart containing available items
And a valid coupon
When the customer requests a quote
Then the system returns the price components and total
And the discount follows the active promotion rule
And the quote includes an expiry time
```

Gherkin hữu ích cho examples theo scenario. Không cần ép mọi quality/rule phức tạp vào Gherkin; decision table hoặc prose có thể rõ hơn.

## 5. Acceptance criteria vs Definition of Done

| Acceptance Criteria | Definition of Done |
|---|---|
| Specific cho story/requirement | Quality bar chung cho increment/team |
| Mô tả business/observable conditions | Bao gồm review, tests, documentation, deployment readiness |
| Thay đổi theo behavior | Tương đối ổn định qua stories |

Một story có thể đạt AC nhưng chưa Done nếu thiếu security review hoặc deployment evidence theo team policy.

## 6. INVEST

INVEST là heuristic, không phải luật tuyệt đối.

### Independent

Có thể lập kế hoạch/implement tương đối độc lập. Domain dependencies vẫn tồn tại; mục tiêu là giảm coupling không cần thiết và làm dependency rõ.

### Negotiable

Story không khóa solution trước conversation. Contract/regulatory requirement có thể ít negotiable về outcome nhưng implementation vẫn có options.

### Valuable

Cung cấp value cho stakeholder hoặc enable value/risk reduction có trace rõ. Technical story không cần giả làm end-user story.

### Estimable

Team hiểu đủ để ước lượng tương đối. Không estimable là signal cần split, research hoặc clarify.

### Small

Đủ nhỏ cho planning/feedback cadence. “Small” phụ thuộc team và iteration, không có kích thước tuyệt đối.

### Testable

Có observable outcome và acceptance evidence. Nếu không, làm rõ intent/measure.

## 7. Story splitting

Split theo **vertical business value**, không theo technical layers như “database story”, “backend story”, “UI story” nếu từng phần không independently demonstrable.

### Kỹ thuật split

| Axis | Example Place Order |
|---|---|
| Workflow steps | Quote first, then confirmation/cancel |
| Business rules | Basic pricing before promotion combinations |
| Data variation | One restaurant before multi-restaurant cart |
| Operations | Card payment before additional methods |
| Happy vs exceptions | Core success plus essential safety; uncommon recovery later nếu acceptable |
| Interface/channel | Mobile web before native app if value independently usable |
| Performance scale | Functional slice then planned scale target, nếu quality floor vẫn đáp ứng |

### Safety rule

Không tách security, data integrity hoặc mandatory failure behavior khỏi slice khiến phần đầu nguy hiểm/không releasable. Split phải giữ quality floor.

## 8. Worked example

### Epic-like statement

> As a customer, I want to manage my order so that I can get food.

Quá rộng và “manage” mơ hồ.

### Story slices

#### `US-QUOTE-01 — View itemized quote`

As a customer, I want to see the complete price before confirmation, so that I can make an informed purchase decision.

Acceptance criteria:

1. Available items, quantities và selected options xuất hiện trong quote.
2. Subtotal, fees, discounts, total, currency và expiry được trả.
3. Unsupported address trả lý do có thể hành động, không tạo quote.
4. Menu change làm hệ thống chỉ ra affected items.
5. `QR-PERF-001` áp dụng cho peak quote response.

#### `US-CONFIRM-01 — Confirm quoted order`

As a customer, I want to confirm a valid quote with payment, so that the restaurant can receive my order.

AC bao phủ quote expiry, payment decline, duplicate confirmation và restaurant timeout theo linked rules.

### Trace

```text
BR-001 → FR-QUOTE-01 → US-QUOTE-01 → AC set
                         └→ QR-PERF-001
```

User story không thay FR hoặc traceability; nó là delivery/conversation representation.

## 9. Common story smells

### Generic role

> As a user, I want reports.

Role và value không giúp ra quyết định.

### UI-centric

> As a user, I want a green button.

Có thể là design constraint từ tested UX, nhưng thường bỏ mất outcome.

### Technical task giả dạng story

> As a developer, I want a database table so that data is stored.

Nếu là enabler, ghi technical work item với risk/value trace rõ.

### No value

“So that I can use the feature” lặp goal, không nêu outcome.

### Hidden compound story

Nhiều “and”, nhiều role, nhiều independent rules. Split hoặc dùng use case/feature specification.

### AC lặp implementation

AC nên xác nhận behavior. UI selectors, class names và database rows thuộc test/design level khác trừ khi externally mandated.

## 10. Requirement vs User Story vs Use Case

| Axis | Requirement | User Story | Use Case |
|---|---|---|---|
| Mục đích | Nêu condition/capability phải thỏa | Conversation + delivery slice | Mô tả actor–system interaction để đạt goal |
| Scope | Từ business tới system/detail | Thường nhỏ cho iteration | End-to-end goal, có nhiều flows |
| Hình thức | Statement/model/spec | Card + conversation + confirmation | Diagram overview + textual scenarios |
| Detail | Tùy mức và risk | Detail nằm trong AC/conversation | Main, alternative, exception, guarantees |
| Lifecycle | Baseline/change/trace | Backlog refinement/delivery | Analysis/specification/validation |

### Không phải mapping 1:1

- Một use case có thể được triển khai qua nhiều stories.
- Một story có thể thỏa nhiều requirements nhỏ.
- Một QR có thể áp dụng nhiều stories/use cases.
- Một requirement có thể không có user role trực tiếp, như retention/audit constraint.

## 11. Khi không nên dùng user story làm representation chính

- Algorithm/regulatory specification cần precision cao.
- System-to-system protocol có schema/state/error contract phức tạp.
- Safety-critical behavior cần formal/structured specification.
- End-to-end interaction có nhiều alternative/exception flows: use case rõ hơn.
- Quality scenario xuyên nhiều features.
- Data migration/operational procedure không có meaningful “user” story.

Vẫn có thể dùng story để track work, nhưng source specification nằm ở artifact phù hợp.

## 12. Story refinement workflow

1. Trace story tới stakeholder/business goal.
2. Confirm role và outcome.
3. Gather concrete examples/counterexamples.
4. Extract business rules và quality links.
5. Identify dependencies/assumptions.
6. Split theo value nếu quá lớn.
7. Viết AC và verification approach.
8. Estimate/confidence check.
9. Validate với stakeholder và team.
10. Cập nhật trace links khi scope đổi.

## 13. Common mistakes

1. Coi một dòng story là specification đủ.
2. Ép mọi work vào As-a format.
3. Dùng INVEST như pass/fail cứng.
4. Split theo frontend/backend/database.
5. Bỏ failure/security khỏi “MVP”.
6. AC chỉ có happy path.
7. AC khóa implementation.
8. Story không trace về goal/requirement.
9. Product owner viết story một mình rồi “handoff”.

## 14. Artifact checklist

- [ ] Role có goal khác biệt và nằm đúng boundary?
- [ ] Goal là outcome/capability, không chỉ UI action?
- [ ] Value/rationale có thật và traceable?
- [ ] Conversation đã cover rules, examples, exceptions, quality?
- [ ] Story small/estimable đủ cho context?
- [ ] Dependencies/assumptions rõ?
- [ ] AC observable, unambiguous, testable?
- [ ] Quality floor không bị split khỏi behavior?
- [ ] Representation khác được reference khi cần precision?
- [ ] Definition of Done không bị trộn AC?

## 15. Practice và exam review

- [Bài tập Basic → Case Study](../08-Exercises/01-requirements.md#user-stories)
- [Key concepts, comparison và scenario questions](../09-Exam-Preparation/01-requirements.md#user-stories)

## 16. Summary và dependency tiếp theo

User stories tổ chức conversation và delivery slices. Mọi representation vẫn cần được kiểm tra đúng nhu cầu và đủ quality; học tiếp [Requirement Validation](validation.md).
