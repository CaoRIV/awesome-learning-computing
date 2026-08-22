# Exercises — Module 01: Software Requirements Engineering

Bài tập được tập trung tại đây để các lesson ưu tiên phần lý thuyết. Hãy làm sau khi đọc lesson tương ứng; không có đáp án ngay bên dưới câu hỏi.

## Cách làm

- **Basic:** tự giải thích và phân loại mà không nhìn lại lesson.
- **Intermediate:** tạo hoặc sửa một artifact có phạm vi nhỏ.
- **Advanced:** phân tích trade-off, conflict và bảo vệ quyết định.
- **Case Study:** chuyển kiến thức sang một domain khác.

Mọi assumption phải được ghi rõ. Không tự thêm database, class hoặc architecture nếu đề chỉ yêu cầu requirements.

<a id="requirements-engineering-fundamentals"></a>

## 01 — Requirements Engineering Fundamentals

### Basic

1. Giải thích requirement bằng lời của bạn và cho một phản ví dụ.
2. Phân loại tám phát biểu tự tạo thành goal, FR, QR, rule, constraint, design decision hoặc test.
3. Nêu ba nguồn làm requirement thay đổi.

### Intermediate

Phân tích phát biểu “Hệ thống thư viện phải cho phép mượn sách dễ dàng và bảo mật”. Liệt kê ambiguity, câu hỏi làm rõ và viết lại thành ít nhất ba candidate requirements khác loại.

### Advanced

Một tổ chức đã ký baseline nhưng regulation mới có hiệu lực trước release. Thiết kế change workflow, impact dimensions, người cần tham gia và evidence cần revalidate.

### Case Study

Với Hotel Management System, tạo problem statement, system boundary, năm business goals và 12 candidate requirements. Không thiết kế class hoặc database ở bước này.

<a id="stakeholders"></a>

## 02 — Stakeholders

### Basic

1. Cho ví dụ một stakeholder không phải user và một user không phải customer.
2. Giải thích external system là actor hay stakeholder trong hai context khác nhau.
3. Liệt kê stakeholder cho hệ thống ATM theo onion model.

### Intermediate

Tạo stakeholder register cho University Enrollment System. Phải có ít nhất một nhóm high-impact/low-power và giải thích cách đại diện nhóm đó.

### Advanced

Thiết kế workshop giải quyết conflict giữa fraud prevention, conversion và privacy cho guest checkout. Chỉ rõ data, roles, authority và decision record.

### Case Study

Với Hotel Management System, xác định stakeholder cho booking, check-in, cancellation, housekeeping và payment. Tạo engagement plan dùng ít nhất bốn kỹ thuật.

<a id="functional-requirements"></a>

## 03 — Functional Requirements

### Basic

1. Phân loại mười phát biểu của một ATM thành FR, QR, rule hoặc design decision.
2. Viết ba FR theo trigger–condition–response.
3. Giải thích vì sao CRUD thường không phản ánh business goals.

### Intermediate

Rewrite “Hệ thống phải quản lý đặt phòng và tự động xử lý mọi vấn đề” thành một FR set có success và failure behaviors.

### Advanced

Tạo functional decomposition cho bank transfer, gồm authorization, balance rule, idempotency-related behavior, external failure và notification. Không chọn architecture.

### Case Study

Viết 15 FR cho University Enrollment System, liên kết mỗi FR với stakeholder goal và business rule source. Bao phủ enrollment, waitlist, withdrawal và prerequisite exception.

<a id="quality-requirements"></a>

## 04 — Non-functional / Quality Requirements

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

<a id="requirement-elicitation"></a>

## 05 — Requirement Elicitation

### Basic

1. Chọn kỹ thuật phù hợp cho năm uncertainty khác nhau và giải thích.
2. Rewrite năm leading questions thành neutral prompts.
3. Phân biệt focus group và workshop theo purpose/output.

### Intermediate

Thiết kế interview guide 30 phút để khám phá hotel cancellation exceptions. Gồm objective, participant, funnel questions, evidence và validation.

### Advanced

Thiết kế mixed-method elicitation plan cho banking fraud alert. Xử lý privacy, rare-event evidence, customer perception, compliance và operations workflow.

### Case Study

Lập elicitation plan hai vòng cho University Enrollment System. Vòng một khám phá domain; vòng hai giải quyết conflict về capacity, priority và override. Chỉ rõ artifacts sau mỗi activity.

<a id="requirement-analysis"></a>

## 06 — Requirement Analysis

### Basic

1. Phân biệt inconsistency, incompleteness và ambiguity bằng ba ví dụ.
2. Liệt kê năm dependency types.
3. Chọn model phù hợp để phân tích rule, lifecycle và interface.

### Intermediate

Review một tập requirement đặt phòng gồm search, reserve, pay, cancel. Tạo issue log có ít nhất tám findings thuộc bốn loại.

### Advanced

Phân tích feasibility và conflict cho yêu cầu “bank transfer tức thì, miễn phí, luôn khả dụng và không bao giờ sai”. Viết options/trade-offs và decision inputs.

### Case Study

Với University Enrollment System, tạo decision table cho prerequisite/capacity/waitlist/override, event-response table và dependency map. Không đưa ra design classes.

<a id="requirement-prioritization"></a>

## 07 — Requirement Prioritization

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

<a id="user-stories"></a>

## 08 — User Stories và Acceptance Criteria

### Basic

1. Giải thích Three Cs và INVEST.
2. Phân biệt AC và Definition of Done.
3. Rewrite ba UI-centric stories theo outcome.

### Intermediate

Split story “As a customer, I want to checkout” thành vertical slices. Viết AC cho một slice gồm normal, boundary và failure examples.

### Advanced

Thiết kế representation strategy cho bank transfer: phần nào dùng requirements, use case, stories, decision table và quality scenarios. Giải thích trade-offs.

### Case Study

Tạo story map rút gọn cho Hotel Booking từ search tới cancellation. Chọn Release 1 slices, viết AC và trace về FR/QR/rules.

<a id="requirement-validation"></a>

## 09 — Requirement Verification và Validation

### Basic

1. Cho hai ví dụ requirement verify được nhưng validate sai.
2. Phân biệt review, walkthrough và inspection.
3. Chọn validation method cho usability, security rule và lifecycle.

### Intermediate

Review mười requirements của Hotel Booking. Tạo defect log, rewrite ba defects lớn và nêu validation participants.

### Advanced

Thiết kế risk-based validation strategy cho bank transfer gồm requirement inspection, state/decision models, security review, test design và approval evidence.

### Case Study

Tạo validation package cho University Enrollment Release 1: plan, roles, checklist, scenario walkthroughs, acceptance examples, exit criteria và issue log format.

## Self-review chung

Sau mỗi bài tạo artifact, kiểm tra:

- [ ] Scope và system boundary được nói rõ.
- [ ] Thuật ngữ nhất quán với glossary.
- [ ] Assumptions, source và rationale được ghi.
- [ ] Normal, alternative và failure concerns phù hợp được xét.
- [ ] Không trộn requirement với design decision vô căn cứ.
- [ ] Artifact có thể được review hoặc kiểm chứng.
