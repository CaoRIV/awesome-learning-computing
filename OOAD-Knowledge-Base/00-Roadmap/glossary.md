# Glossary — Software Requirements & OOAD

Glossary dùng English term làm khóa tra cứu và giải thích bằng tiếng Việt. Definition ở đây ngắn để navigation; lesson tương ứng là nguồn giải thích đầy đủ. Khi một tổ chức dùng convention khác, project glossary phải ghi rõ.

## A

### Acceptance Criteria (AC)

Các điều kiện hoặc examples cụ thể mà requirement/story phải thỏa để được chấp nhận. AC không đồng nhất với Definition of Done.

### Actor

Role, người hoặc external entity tương tác qua system boundary để đạt goal. Actor không đồng nhất stakeholder hoặc tên người cụ thể.

### Ambiguity

Tình trạng một phát biểu có nhiều cách hiểu hợp lý do từ ngữ, cú pháp, semantic hoặc context ngầm.

### Assumption

Điều đang được coi là đúng để tiếp tục reasoning nhưng chưa được xác nhận đầy đủ. Assumption phải có owner và validation/monitoring plan nếu ảnh hưởng quan trọng.

### Availability

Mức độ service sẵn sàng cung cấp behavior đã định nghĩa trong observation window và measurement conditions xác định.

## B

### Backward Traceability

Khả năng đi từ artifact downstream như design, code hoặc test về requirement/goal nguồn để giải thích vì sao nó tồn tại.

### Baseline

Tập artifacts đã được review và chấp thuận làm mốc kiểm soát thay đổi. Baseline vẫn có thể đổi qua change process.

### Business Goal

Kết quả kinh doanh mong muốn, thường có direction và success measure. Goal biện minh requirements nhưng không thay software behavior specification.

### Business Requirement

Requirement ở mức business outcome/capability mà tổ chức cần đạt, nằm cao hơn stakeholder/system requirements.

### Business Rule

Policy, calculation, eligibility, obligation hoặc invariant của domain. Functional requirement mô tả software enforce/expose rule ra sao.

## C

### Capability

Khả năng mà tổ chức, stakeholder hoặc system cần có để đạt outcome, chưa nhất thiết mô tả implementation.

### Change Request

Đề xuất có định danh để thêm, sửa hoặc loại bỏ một baseline artifact, kèm reason và impact analysis.

### Completeness

Mức requirement/artifact bao phủ đủ thông tin cần cho scope và intended use, gồm normal, alternative, failure, data, quality và operational concerns phù hợp.

### Conflict

Tình trạng hai obligations, interests hoặc constraints không thể đồng thời thỏa mãn trong cùng conditions/resources.

### Constraint

Hạn chế bắt buộc lên solution, process hoặc environment, ví dụ platform, regulation, location hoặc compatibility.

### Correctness

Mức requirement phản ánh đúng nguồn có thẩm quyền, rule, fact và intended need.

### Customer

Cá nhân/tổ chức mua, tài trợ, ký hợp đồng hoặc nhận value. Customer có thể khác end user.

## D

### Decision Authority

Quyền được giao để phê duyệt hoặc resolve một loại quyết định trong scope xác định.

### Definition of Done (DoD)

Quality bar chung mà increment/work item phải đáp ứng, như review, tests, documentation và deployability; khác AC riêng cho behavior.

### Dependency

Quan hệ trong đó requirement/artifact phụ thuộc logic, dữ liệu, thời gian, interface, rule hoặc quality vào phần khác.

### Design Decision

Lựa chọn cách cấu trúc/hiện thực solution để đáp ứng requirements và constraints, kèm rationale/trade-offs.

## E

### Elicitation

Hoạt động khám phá goals, needs, workflow, rules, exceptions, quality, constraints và assumptions bằng nhiều nguồn/kỹ thuật.

### External System

Hệ thống nằm ngoài boundary đang xét nhưng trao đổi data/event/service với system. Nó thường là actor trong use-case view; tổ chức/owner liên quan là stakeholder.

## F

### Feasibility

Khả năng requirement được đáp ứng trong constraints kỹ thuật, kinh tế, lịch, vận hành, pháp lý, tổ chức và dữ liệu.

### Focus Group

Thảo luận có điều phối với một nhóm target participants để khám phá perception, language và reactions; không phải cơ chế phê duyệt hay thống kê population.

### Forward Traceability

Khả năng đi từ goal/requirement tới use case, design, code và tests để xác định realization và coverage.

### Functional Requirement (FR)

Requirement mô tả capability, interaction, transformation hoặc phản ứng quan sát được mà system phải thực hiện trong conditions xác định.

## I

### Impact Analysis

Phân tích artifacts, stakeholders, value, risk, cost và schedule bị ảnh hưởng bởi một thay đổi.

### Inspection

Review có cấu trúc với preparation, roles, defect logging, rework và closure; phù hợp artifacts/risk quan trọng.

### INVEST

Heuristic cho user story: Independent, Negotiable, Valuable, Estimable, Small, Testable.

## M

### Maintainability

Mức dễ dàng và an toàn khi hiểu, sửa, test, deploy và vận hành thay đổi.

### MoSCoW

Kỹ thuật phân loại scope trong timebox thành Must, Should, Could và Won't Have This Time.

## N

### Need

Vấn đề, thiếu hụt hoặc outcome stakeholder cần giải quyết/đạt được. Need có thể tồn tại trước khi có solution.

### Non-functional Requirement (NFR)

Thuật ngữ rộng thường dùng cho quality requirements và constraints. Knowledge base ưu tiên gọi đúng quality requirement hoặc constraint khi có thể.

## O

### Observation

Kỹ thuật elicitation quan sát work thật trong context để khám phá tacit knowledge, artifacts, interruptions và workarounds.

### Over-specification

Đưa solution/implementation detail vào requirement vượt quá điều cần để giới hạn hoặc xác nhận outcome, làm giảm solution space không có rationale.

## P

### Performance

Quality liên quan latency, throughput, concurrency, deadline và resource behavior dưới workload/environment xác định.

### Portability

Khả năng chuyển software/artifact giữa environments với effort và impact được xác định.

### Prioritization

So sánh relative importance/timing của requirements dựa trên value, risk, cost, dependency, urgency và constraints cho một scope cụ thể.

### Prototype

Representation được tạo để học/validate requirement hoặc feasibility. Prototype có thể throwaway/evolutionary và horizontal/vertical.

## Q

### Quality Attribute

Dimension chất lượng như performance, security, reliability, usability hoặc maintainability.

### Quality Requirement (QR)

Requirement đặt mức quality có thể đánh giá cho behavior/system trong source, stimulus, environment và measure xác định.

### Quality Scenario

Cấu trúc gồm source, stimulus, environment, artifact, response và response measure để đặc tả quality.

## R

### Rationale

Lý do một requirement hoặc decision tồn tại; hỗ trợ validation, prioritization và change impact.

### Reliability

Khả năng thực hiện đúng behavior trong conditions và khoảng thời gian xác định, gồm failure/recovery/consistency concerns phù hợp.

### Requirement

Need, capability, characteristic hoặc condition mà stakeholder cần hoặc system phải thỏa để đạt goal/obligation.

### Requirement Analysis

Reasoning để classify, organize, clarify, model, evaluate feasibility, resolve conflict và kiểm tra quality của candidate requirements.

### Requirements Engineering (RE)

Quá trình có hệ thống để khám phá, phân tích, đặc tả, verify, validate và quản lý requirements.

### Requirement Traceability Matrix (RTM)

Bảng ghi liên kết giữa requirements và upstream/downstream artifacts nhằm kiểm tra coverage và impact.

### Review

Hoạt động đánh giá artifact bởi một hay nhiều người để tìm defect, xác nhận understanding hoặc ra quyết định.

## S

### Scalability

Khả năng duy trì target quality khi workload/data/geography tăng bằng resource/cấu trúc với cost chấp nhận được.

### Scope

Phạm vi problem, capability và responsibility được chọn; thường diễn đạt bằng in-scope, out-of-scope và assumptions.

### Software Requirements Specification (SRS)

Specification có cấu trúc mô tả requirements, context, interfaces, constraints và supporting information của software/system trong scope.

### Specification

Representation có cấu trúc và đủ precision của requirements/rules/interfaces để communication, agreement, design và verification.

### Stakeholder

Cá nhân, nhóm, tổ chức hoặc bên liên quan có interest, impact, knowledge hoặc authority đối với system.

### Stakeholder Need

Capability/outcome một stakeholder hoặc class of stakeholders cần, thường là cầu nối giữa business goal và system requirement.

### System Boundary

Ranh giới xác định phần nào thuộc responsibility của system đang xét và phần nào thuộc environment/external actors.

## T

### Testability

Mức có thể thiết kế evidence khách quan để xác định requirement thỏa hay không; cần observable result, oracle và conditions.

### Three Cs

User story model gồm Card, Conversation và Confirmation.

### Traceability

Khả năng thiết lập và theo dõi quan hệ giữa goals, requirements, models, design, code và tests theo một hoặc hai chiều.

### Triangulation

Đối chiếu nhiều nguồn hoặc kỹ thuật elicitation/validation để tăng confidence và phát hiện khác biệt.

## U

### Unambiguity

Đặc tính một requirement chỉ có một cách hiểu hợp lý trong glossary và context đã định nghĩa.

### Usability

Mức representative users đạt task effectiveness, efficiency và satisfaction trong context sử dụng cụ thể.

### User

Người trực tiếp tương tác với product/system. User có thể là stakeholder và actor nhưng ba concept không đồng nhất.

### User Story

Mô tả ngắn về capability/outcome từ góc nhìn role, được dùng làm lời mời conversation và delivery slice, kèm acceptance criteria.

## V

### Validation

Xác nhận requirements/specification phản ánh đúng stakeholder needs, goals và intended use: “đang đặc tả đúng sản phẩm cần xây?”

### Verification

Kiểm tra artifact được viết/cấu trúc đúng quality rules, consistency và intended standard: “đang đặc tả sản phẩm đúng cách?”

### Version

Định danh trạng thái sửa đổi của artifact để biết nội dung nào được review, baseline hoặc thay thế.

### Volatility

Xu hướng requirement thay đổi do learning, market, policy, regulation, dependency hoặc strategy.

## W

### Walkthrough

Review trong đó author/analyst dẫn người tham gia qua artifact hoặc scenario để thu feedback và xây shared understanding.

### Workshop

Phiên elicitation/analysis có facilitation với nhiều stakeholder để đồng tạo model, làm rõ conflict hoặc ra quyết định.

## Các distinction bắt buộc nhớ

| Cặp | Distinction ngắn |
|---|---|
| Stakeholder / User / Actor | Interest-impact / trực tiếp dùng / role tương tác boundary |
| Goal / Requirement | Outcome mong muốn / condition-capability phải thỏa |
| FR / QR / Constraint | Behavior / mức quality / giới hạn solution-environment |
| Rule / FR | Policy domain / system behavior enforce policy |
| Verification / Validation | Artifact đúng cách / đúng product cần xây |
| Priority / Sequence | Relative importance / thứ tự thực hiện |
| AC / DoD | Điều kiện riêng của behavior / quality bar chung |
| User Story / Use Case | Delivery conversation slice / end-to-end actor-goal scenarios |

