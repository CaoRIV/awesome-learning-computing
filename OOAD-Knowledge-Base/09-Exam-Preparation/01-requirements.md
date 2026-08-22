# Exam Preparation — Module 01: Software Requirements Engineering

Tài liệu này tập trung retrieval, comparison và scenario reasoning. Hãy học lý thuyết trong `01-Requirements` trước; không dùng phần này thay cho lesson.

<a id="requirements-engineering-fundamentals"></a>

## 01 — Requirements Engineering Fundamentals

### Key concepts

- Requirement là condition/capability cần thiết; specification là representation có cấu trúc của requirements.
- RE gồm discovery, analysis, specification, validation và management.
- Ambiguity, conflict và volatility là ba vấn đề khác nhau.
- Baseline tạo mốc kiểm soát thay đổi, không đóng băng vĩnh viễn.

### Common exam questions

1. Vì sao requirements quan trọng đối với chi phí phần mềm?
2. Mô tả lifecycle của Requirements Engineering và feedback loops.
3. Requirement volatility nên được loại bỏ hay quản lý? Giải thích.

### Comparison questions

- Business goal vs software requirement.
- Requirement vs design decision.
- Ambiguity vs incompleteness vs conflict.
- Baseline vs version.

### Scenario question

Một trường đại học yêu cầu “đăng ký môn học nhanh, tự động và công bằng”. Xác định điều đã biết, điều chưa biết, stakeholder cần hỏi và candidate requirement types. Ghi rõ assumptions.

<a id="stakeholders"></a>

## 02 — Stakeholders

### Key concepts

- Stakeholder rộng hơn user và actor.
- Identification phải đi qua toàn lifecycle và ecosystem.
- Prioritization chủ yếu là ưu tiên engagement/decision involvement.
- Power không thay thế impact hoặc legitimacy.

### Common exam questions

1. Trình bày quy trình Identify → Classify → Analyze → Prioritize.
2. Vì sao developer và tester là stakeholders?
3. Làm sao phát hiện missing stakeholder?

### Comparison questions

- Stakeholder vs user.
- Customer vs business owner.
- Stakeholder vs actor.
- Influence vs impact.

### Scenario question

Một bệnh viện mua hệ thống quản lý lịch khám. Hãy xác định stakeholder, phân loại knowledge/authority và nêu ba conflict có thể xảy ra. Không coi “bệnh viện” là một stakeholder đồng nhất.

<a id="functional-requirements"></a>

## 03 — Functional Requirements

### Key concepts

- FR mô tả observable behavior/capability.
- Trigger–condition–response giúp viết event-oriented behavior.
- Rule quyết định policy; FR mô tả hệ thống enforce/expose policy.
- Functional decomposition không đồng nghĩa class/service decomposition.

### Common exam questions

1. Functional requirement là gì? Cho good/bad examples.
2. Vì sao không nên mô tả FR bằng UI details?
3. Business rules liên hệ FR thế nào?

### Comparison questions

- Functional requirement vs business goal.
- Functional requirement vs business rule.
- Functional requirement vs use case.
- Business operation vs CRUD operation.

### Scenario question

Một thư viện muốn “tự động gia hạn sách nếu có thể”. Viết câu hỏi làm rõ, business rules candidate, FR success/failure và acceptance examples.

<a id="quality-requirements"></a>

## 04 — Non-functional / Quality Requirements

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

<a id="requirement-elicitation"></a>

## 05 — Requirement Elicitation

### Key concepts

- Elicitation khám phá knowledge/uncertainty; không chỉ hỏi preference.
- Technique selection phụ thuộc question và evidence.
- Triangulation làm lộ khác biệt giữa lời nói, tài liệu và thực tế.
- Candidate requirement chưa phải accepted requirement.

### Common exam questions

1. So sánh ưu/nhược của interview, observation và questionnaire.
2. Vì sao prototyping là kỹ thuật elicitation?
3. Mô tả một elicitation plan tốt.

### Comparison questions

- Interview vs questionnaire.
- Observation vs document analysis.
- Workshop vs focus group.
- Throwaway vs evolutionary prototype.

### Scenario question

Nhân viên nhà hàng nói họ luôn cập nhật món hết ngay, nhưng support tickets cho thấy nhiều đơn bị từ chối. Chọn kỹ thuật tiếp theo, giải thích bias và output cần tạo.

<a id="requirement-analysis"></a>

## 06 — Requirement Analysis

### Key concepts

- Analysis biến evidence thành coherent requirement model.
- Completeness relative với scope/use; kiểm tra qua nhiều lenses.
- Feasibility gồm technical, economic, schedule, operational, legal, organizational và data.
- Model được dùng để tìm defect, không chỉ documentation.

### Common exam questions

1. Requirement analysis gồm những hoạt động nào?
2. Làm sao phát hiện requirement không complete/testable?
3. Trình bày quy trình negotiation conflict.

### Comparison questions

- Ambiguity vs inconsistency.
- Completeness vs correctness.
- Feasibility vs priority.
- Business rule vs design constraint.

### Scenario question

Customer yêu cầu xóa dữ liệu ngay, Legal yêu cầu giữ transaction record bảy năm, Support cần lịch sử khi xử lý tranh chấp. Phân tích conflict theo data categories, purpose, authority và candidate resolutions.

<a id="requirement-prioritization"></a>

## 07 — Requirement Prioritization

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

<a id="user-stories"></a>

## 08 — User Stories và Acceptance Criteria

### Key concepts

- Story là Card + Conversation + Confirmation.
- INVEST là heuristic quality/refinement.
- AC xác nhận outcome riêng cho item; DoD là quality bar chung.
- Story không thay mọi dạng specification.

### Common exam questions

1. User story là gì và có đủ thay use case không?
2. Trình bày INVEST với caveats.
3. Làm sao split story mà vẫn giữ business value?

### Comparison questions

- Requirement vs User Story.
- User Story vs Use Case.
- Acceptance Criteria vs Definition of Done.
- Epic vs story.

### Scenario question

Story “As a student, I want to manage courses so that enrollment is easy” có vấn đề gì? Hãy split, viết AC và chỉ ra rules cần tách.

<a id="requirement-validation"></a>

## 09 — Requirement Verification và Validation

### Key concepts

- Verification: specification được tạo đúng; Validation: đúng product/need.
- Review methods khác nhau về structure và purpose.
- Test design và models là công cụ phát hiện requirement defect.
- Sign-off phải gắn artifact version/scope và không đóng băng change.

### Common exam questions

1. So sánh verification và validation bằng ví dụ.
2. Requirement inspection diễn ra thế nào?
3. Vì sao acceptance criteria hỗ trợ validation?

### Comparison questions

- Verification vs validation.
- Walkthrough vs inspection.
- Requirement review vs acceptance testing.
- Defect vs change request.

### Scenario question

Stakeholder ký requirement “refund ngay” nhưng tester không tạo được expected result vì provider settlement mất nhiều ngày. Hãy phân loại findings, chọn participants và mô tả resolution/evidence.

## Cách tự chấm câu trả lời lý thuyết

Một câu trả lời tốt nên có:

1. definition và boundary;
2. lý do concept tồn tại;
3. example trong một system boundary rõ;
4. distinction với concept gần nghĩa;
5. common misuse hoặc counterexample;
6. trade-off hay condition khi kết luận phụ thuộc context.
