# Requirement Verification và Validation

> **Mục tiêu:** tìm defect trước khi chúng lan truyền và xác nhận requirement set vừa được viết đúng, vừa mô tả đúng hệ thống cần xây.

## Learning outcomes

Sau bài này, bạn có thể:

- phân biệt verification và validation;
- tổ chức review, walkthrough và inspection;
- dùng prototype, model, test design và acceptance criteria để validation;
- đánh giá correctness, clarity, completeness, consistency, feasibility và testability;
- ghi defect, decision, evidence và unresolved issue;
- chọn validation effort theo risk.

## 1. Verification vs Validation

### Verification

> **Are we specifying the product right?**

Kiểm tra artifact có tuân thủ quality rules, format, consistency, traceability và đủ làm input downstream không.

Ví dụ: `FR-QUOTE-01` có trigger/response rõ, không mâu thuẫn rule, có ID/source và testable.

### Validation

> **Are we specifying the right product?**

Xác nhận requirement set phản ánh đúng stakeholder needs, goals, context và acceptable outcome.

Ví dụ: dù quote requirement viết hoàn hảo, khách có thực sự cần breakdown này và Finance có xác nhận cách tính không?

### So sánh

| Axis | Verification | Validation |
|---|---|---|
| Focus | Artifact quality/conformance | Need/fitness/intended use |
| Reference | Rules, templates, models, source consistency | Stakeholders, goals, real context, evidence |
| Participants | Analyst, reviewer, tester, engineer | Representative stakeholders + team |
| Methods | Inspection, checklist, consistency/testability analysis | Review, prototype, scenario walkthrough, acceptance example |
| Failure example | Requirement mơ hồ | Requirement rõ nhưng giải quyết sai problem |

Trong thực tế hai hoạt động đan xen; distinction giúp đặt đúng câu hỏi.

## 2. Tại sao validation quan trọng?

Stakeholder “đồng ý” một document không bảo đảm họ hiểu cùng meaning. Validation cần active evidence:

- stakeholder thực hiện scenario/prototype;
- nhóm giải thích requirement bằng examples;
- tester tạo oracle và edge cases;
- engineer đánh giá feasibility;
- operations diễn tập failure/recovery;
- models làm lộ state/rule gap.

Mục tiêu không phải chữ ký, mà là shared understanding đủ cho risk hiện tại.

## 3. Validation planning

| Field | Câu hỏi |
|---|---|
| Scope | Requirement set/feature/version nào? |
| Risk | Failure gây consequence gì? |
| Questions | Correct need, clarity, completeness, feasibility hay testability? |
| Participants | Ai có knowledge/authority/impact? |
| Method | Review, inspection, prototype, test design? |
| Inputs | Baseline, glossary, models, rules, prototype |
| Evidence | Defect log, approval, test/prototype result |
| Exit criteria | Issue nào phải đóng; risk nào có thể accept? |

High-risk requirement cần nhiều independent perspectives và evidence hơn low-risk content text.

## 4. Requirement review

Review là thuật ngữ rộng cho việc stakeholder/team đánh giá artifact.

### Chuẩn bị

1. Chọn scope nhỏ, version cố định.
2. Gửi objective, artifacts và checklist trước.
3. Assign reviewer perspectives: business, user, test, design, operations, security.
4. Reviewer chuẩn bị findings độc lập.
5. Meeting tập trung classify/resolve, không đọc tài liệu từ đầu.

### Review perspectives

| Role | Câu hỏi mạnh |
|---|---|
| Business/Product | Có tạo value và đúng priority? |
| Domain user | Workflow/rule/exception có đúng thực tế? |
| Tester | Oracle, boundary, negative case ở đâu? |
| Developer/Architect | Feasible, dependency, ambiguity kỹ thuật? |
| Operations/Support | Monitor, recover, diagnose, support ra sao? |
| Security/Privacy | Asset, access, misuse, retention, evidence? |

## 5. Walkthrough

Author/analyst dẫn người tham gia qua artifact hoặc scenario để thu feedback và shared understanding.

### Phù hợp

- onboarding reviewers;
- story/use-case scenario;
- early draft;
- cross-functional vocabulary.

### Risk

Author explanation có thể che câu chữ mơ hồ. Sau walkthrough, reviewer nên đọc requirement độc lập: artifact phải tự đủ cho intended audience.

## 6. Inspection

Inspection là review có cấu trúc và discipline cao hơn, thường gồm roles, preparation, defect logging và exit criteria.

### Roles điển hình

- author;
- moderator/facilitator;
- reviewers;
- recorder;
- owner/decision participants khi cần.

### Flow

```text
Planning
→ individual preparation
→ inspection meeting / defect consolidation
→ rework
→ follow-up and closure
```

Meeting ưu tiên phát hiện/classify defects; tranh luận solution dài được tách thành action/decision session.

### Khi dùng

Requirements critical, regulated, safety/security/financial risk cao hoặc làm contract/baseline quan trọng.

### Trade-off

Inspection tốn effort; áp dụng risk-based thay vì mọi note đều cùng ceremony.

## 7. Prototype-based validation

Prototype trả lời câu hỏi về interaction, workflow, desirability hoặc feasibility.

Validation tốt:

- có representative tasks/users;
- có learning questions;
- quan sát behavior, không chỉ opinion;
- nói rõ fidelity/limitations;
- ghi findings và requirement impacts;
- không suy diễn prototype performance thành production performance.

Example: khách có nhận ra quote expiry và phí trước confirm không; restaurant staff có xử lý incoming order trong peak context không.

## 8. Model-based validation

### Context model

Xác nhận boundary, external systems và responsibility.

### State model

Walkthrough events để tìm missing/illegal transitions. Hữu ích cho Order, Booking, Transfer, Enrollment.

### Decision table

Kiểm tra rule combinations có overlap/gap.

### Process/activity model

Xác nhận handoff, branch, parallel work và exception.

### Domain/data model

Xác nhận vocabulary, relationship, multiplicity và information ownership.

Model không được coi là đúng chỉ vì stakeholder thấy diagram đẹp. Dùng concrete examples để chạy qua model.

## 9. Testability và test-design validation

Thử thiết kế test sớm là cách mạnh để phát hiện:

- missing condition;
- unclear expected result;
- undefined boundary values;
- nondeterministic outcome;
- inaccessible observation point;
- environment/data không thể tạo;
- quality target không đo được.

### Example

Requirement: “Hệ thống tự động phát hiện địa chỉ đáng ngờ.”

Test-design questions:

- “đáng ngờ” theo rule/model/version nào?
- false positive/negative tolerance?
- expected action: block, warn hay review?
- explanation/audit requirement?
- test dataset và privacy permission?

Nếu không trả lời được, requirement chưa verifiable; không phải tester thiếu sáng tạo.

## 10. Acceptance criteria trong validation

AC là examples/conditions cụ thể giúp xác nhận shared meaning. Bộ AC nên cover:

- normal success;
- boundary values;
- alternative valid path;
- invalid input;
- dependency failure/timeouts;
- permission/security;
- quality threshold;
- state/postcondition.

AC không thay toàn bộ requirement. Nếu examples mâu thuẫn statement/rule, phải xác định source of truth và sửa artifact upstream.

## 11. Requirement quality checklist

### Correct và necessary

- [ ] Có source/goal/rationale hợp lệ?
- [ ] Stakeholder có thẩm quyền xác nhận intent?
- [ ] Requirement cần cho scope hiện tại?

### Unambiguous và understandable

- [ ] Thuật ngữ trong glossary?
- [ ] Actor/event/object/reference rõ?
- [ ] Không dùng adjective/quantifier chủ quan?
- [ ] Hai reviewer độc lập hiểu giống nhau?

### Complete và consistent

- [ ] Trigger, conditions, response/postcondition đủ?
- [ ] Alternative, exception, recovery phù hợp?
- [ ] Không mâu thuẫn rule/state/interface/QR?
- [ ] External assumptions và dependencies rõ?

### Feasible và verifiable

- [ ] Technical/operational/legal/data feasibility có evidence?
- [ ] Observable result/measure/oracle rõ?
- [ ] Verification environment/method khả thi?

### Traceable, prioritized và modifiable

- [ ] ID/source/rationale/version/status?
- [ ] Upstream/downstream links?
- [ ] Priority có context/rationale?
- [ ] Một concern có thể đổi mà không sửa hàng loạt bản sao?

## 12. Defect taxonomy

| Type | Example |
|---|---|
| Ambiguity | “nhanh”, “sau đó”, “hợp lệ” |
| Omission | Không có timeout path |
| Conflict | Cancel anytime vs guaranteed restaurant payment |
| Incorrect | Rule tính phí sai policy |
| Infeasible | Target thấp hơn unavoidable provider latency |
| Unverifiable | “trải nghiệm tốt nhất” |
| Over-specification | Bắt buộc pattern/framework vô căn cứ |
| Trace defect | Test/design không có requirement source |
| Scope defect | Behavior thuộc external organization nhưng gán cho system |

## 13. Defect và decision log

| Field | Nội dung |
|---|---|
| Finding ID | `REV-01-023` |
| Artifact/version/location | Requirement ID và version |
| Type/severity | Ambiguity, conflict; major/minor |
| Description | Một vấn đề, có evidence |
| Consequence | Downstream/risk nếu không sửa |
| Owner | Người rework/resolve |
| Resolution | Rewrite, rule decision, defer, reject |
| Verification | Ai kiểm tra closure và bằng gì |
| Status | Open, resolved, accepted risk |

Không dùng meeting minutes mơ hồ làm defect control. Accepted risk phải có authority và review trigger.

## 14. Worked validation — Food Delivery quote

### Draft

> Hệ thống phải hiển thị giá chính xác nhanh chóng trước khi đặt.

### Verification findings

- “chính xác” thiếu rule/version/rounding.
- “nhanh chóng” thiếu workload/measure.
- “trước khi đặt” không rõ event boundary.
- không nói breakdown, expiry, menu change.

### Validation findings

- Customer cần biết tổng phí và cancellation implication.
- Finance cần rounding/tax/discount order.
- Restaurant cần price snapshot không đổi sau acceptance.
- Operations cần trace pricing rule version khi dispute.

### Revised artifact set

- `FR-QUOTE-01`: trả itemized quote và expiry trước confirmation.
- `BRULE-PRICE-01`: order of calculations và rounding.
- `QR-PERF-001`: peak response target.
- `FR-QUOTE-02`: re-quote khi price/menu thay đổi.
- AC examples cho coupon, unsupported address, expired quote và rounding boundary.

Điểm quan trọng: một câu vague không nhất thiết rewrite thành một câu dài; thường phải tách đúng concerns.

## 15. Validation exit criteria

Một requirement set có thể tiến tới baseline khi:

- critical defects resolved;
- stakeholder representatives xác nhận intent/outcome;
- feasibility của high-risk items có evidence hoặc accepted risk;
- test/verification approach tồn tại;
- unresolved issues có owner, due và impact được chấp nhận;
- terms/boundary/state nhất quán;
- traceability đủ cho impact/coverage;
- version và approval scope rõ.

Sign-off không loại bỏ trách nhiệm quản lý change.

## 16. Khi không nên dùng một validation method duy nhất

- Review prose không chứng minh usability.
- Prototype không chứng minh regulatory correctness.
- Automated test không chứng minh xây đúng product.
- Manager approval không thay representative user evidence.
- Checklist không phát hiện mọi domain omission.
- Formal inspection có thể quá nặng cho low-risk discovery notes.

Kết hợp methods theo risk và uncertainty.

## 17. Common mistakes

1. Dùng verification và validation như cùng một từ.
2. Chờ specification “hoàn tất” mới review.
3. Meeting đọc tài liệu thay individual preparation.
4. Reviewer chỉ sửa grammar.
5. Chỉ có author giải thích, artifact tự nó vẫn mơ hồ.
6. Prototype demo thay user task observation.
7. AC chỉ có happy path.
8. Findings không có owner/status.
9. Sign-off nhưng không ghi version/scope.
10. Áp cùng ceremony cho mọi risk.

## 18. Artifact checklist

- [ ] Validation objective và artifact version rõ?
- [ ] Reviewers bao phủ business, user, test, technical, operations và risk phù hợp?
- [ ] Preparation/checklist hoàn tất trước meeting?
- [ ] Concrete scenarios/examples được dùng?
- [ ] Verification và validation findings phân biệt?
- [ ] Defect có type, consequence, owner và status?
- [ ] Rework được follow-up độc lập?
- [ ] Unresolved risk có authority chấp nhận?
- [ ] Acceptance/verification evidence traceable?
- [ ] Baseline scope/version và change process rõ?

## 19. Practice và exam review

- [Bài tập Basic → Case Study](../08-Exercises/01-requirements.md#requirement-validation)
- [Key concepts, comparison và scenario questions](../09-Exam-Preparation/01-requirements.md#requirement-validation)

## 20. Summary và dependency tiếp theo

Module 01 kết thúc bằng khả năng khám phá, phân tích, ưu tiên và kiểm tra requirements. Module kế tiếp dùng các outputs này để xây dựng Software Requirements Specification theo [Table of Contents](../00-Roadmap/table-of-contents.md#02--software-requirements-specification).
