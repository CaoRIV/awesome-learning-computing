# Requirement Elicitation

> **Mục tiêu:** chọn và phối hợp kỹ thuật để khám phá goals, rules, workflow, exceptions và tacit knowledge; không xem elicitation là “hỏi khách hàng muốn gì rồi ghi lại”.

## Learning outcomes

Sau bài này, bạn có thể:

- giải thích elicitation là discovery có chuẩn bị, không chỉ data collection;
- thiết kế elicitation plan theo uncertainty, stakeholder và risk;
- sử dụng interview, questionnaire, observation, workshop, brainstorming, document analysis, focus group và prototyping;
- so sánh ưu, nhược điểm và trường hợp sử dụng của từng kỹ thuật;
- đặt câu hỏi ít bias và xác nhận notes;
- triangulate nhiều nguồn khi evidence mâu thuẫn.

## 1. Elicitation là gì?

**Requirement elicitation** là hoạt động khám phá, làm rõ và kiểm chứng ban đầu về stakeholder needs, goals, current work, business rules, constraints, quality expectations, assumptions và risks.

“Thu thập” dễ gợi ý requirements đã nằm sẵn trong đầu stakeholder. Thực tế:

- stakeholder có tacit knowledge khó diễn đạt;
- nhiều người chỉ biết một phần quy trình;
- current process khác procedure document;
- nhu cầu và solution proposal bị trộn;
- conflict chỉ xuất hiện khi nhiều nhóm gặp nhau;
- prototype làm stakeholder nhận ra điều trước đó họ chưa nghĩ tới.

## 2. Elicitation cần khám phá gì?

| Dimension | Câu hỏi |
|---|---|
| Problem/goal | Điều gì đang không tốt và success là gì? |
| Stakeholder | Ai dùng, chịu tác động, quyết định và cung cấp knowledge? |
| Context/boundary | System chịu trách nhiệm gì; environment làm gì? |
| Workflow | Công việc hiện tại và desired workflow? |
| Information | Input, output, source, owner, meaning, sensitivity? |
| Rules | Policy, calculation, eligibility, lifecycle? |
| Exceptions | Điều gì sai, hiếm, khẩn cấp hoặc cần override? |
| Quality | Tốc độ, an toàn, reliability, usability cần mức nào? |
| Constraints | Luật, contract, platform, schedule, budget? |
| Change | Điều gì thường thay đổi và vì sao? |

## 3. Chọn kỹ thuật theo loại uncertainty

| Uncertainty | Kỹ thuật ưu tiên | Kỹ thuật bổ sung |
|---|---|---|
| Không hiểu goal/rationale | Interview | Document analysis |
| Không hiểu work thực tế | Observation | Interview sau quan sát |
| Nhiều nhóm conflict | Workshop | Interviews trước workshop |
| Cần breadth từ population lớn | Questionnaire | Focus group/interviews |
| Cần ý tưởng mới | Brainstorming | Prototype |
| Cần phản ứng với interaction | Prototype | Usability observation |
| Cần policy/legacy constraints | Document analysis | Expert interview |
| Cần perception/group language | Focus group | Survey định lượng |

Không kỹ thuật nào đủ cho mọi concern. **Triangulation** là đối chiếu nhiều phương pháp/nguồn để tăng độ tin cậy và làm lộ khác biệt.

## 4. Elicitation plan

### Template

| Field | Nội dung |
|---|---|
| Objective | Quyết định/uncertainty cần giải quyết |
| Scope | Feature/process/boundary đang xét |
| Participants | Roles và lý do chọn |
| Technique | Phương pháp và sequence |
| Questions/tasks | Topics, prompts, scenarios |
| Inputs | Documents, data, prototype |
| Recording | Notes/audio/artifacts và consent |
| Analysis | Cách code/synthesize evidence |
| Validation | Ai xác nhận summary và khi nào |
| Risks | Bias, access, confidentiality, representation |
| Output | Candidate requirements, rules, issues, decisions |

### Example objective

Không viết “phỏng vấn nhà hàng để lấy requirements”. Viết:

> Xác định events khiến nhà hàng không thể nhận đơn, thời điểm họ biết điều đó, current workaround và response platform cần cung cấp; output là exception catalog và candidate rules cho order acceptance.

## 5. Interview

### What

Trao đổi có mục tiêu với một hoặc vài stakeholder. Có thể structured, semi-structured hoặc open-ended.

### How

1. Xác định objective và participant phù hợp.
2. Nghiên cứu context trước.
3. Chuẩn bị funnel: broad → specific → example → exception → validation.
4. Xin consent cho recording/notes.
5. Hỏi về sự kiện thực, artifact và decision, không chỉ opinion.
6. Tóm tắt lại để xác nhận.
7. Gửi summary, unresolved questions và corrections.

### Question patterns

- “Hãy kể lần gần nhất đơn bị từ chối.”
- “Điều gì xảy ra ngay trước và sau bước này?”
- “Ai quyết định exception?”
- “Cho tôi xem artifact/data bạn dùng.”
- “Khi nào rule này không áp dụng?”
- “Nếu dependency không phản hồi thì hiện nay làm gì?”

Tránh leading question: “Anh có đồng ý app nên tự động hủy sau 5 phút không?”

### Advantages

- đào sâu rationale và tacit knowledge;
- hỏi tiếp theo câu trả lời;
- phù hợp topic nhạy cảm;
- hiểu ngôn ngữ và mental model cá nhân.

### Disadvantages

- tốn thời gian và khó mở rộng;
- recall/social-desirability/interviewer bias;
- một người không đại diện toàn nhóm;
- self-reported work có thể khác thực tế.

### When to use

Khi cần depth, goal, exception, decision rationale hoặc sensitive concern.

### Example

Phỏng vấn restaurant staff về lần cuối menu hết món giữa ca, cách họ phát hiện, thông báo cho ai, order nào bị ảnh hưởng và workaround.

## 6. Questionnaire

### What

Bộ câu hỏi phân phối cho population lớn để thu dữ liệu có cấu trúc hoặc feedback rộng.

### How

1. Xác định population và sampling.
2. Chọn closed/open questions theo analysis plan.
3. Dùng wording trung lập, một ý mỗi câu.
4. Pilot với nhóm nhỏ.
5. Giải thích purpose, privacy và thời lượng.
6. Phân tích response rate, missing data và segment differences.
7. Follow-up định tính cho điểm bất thường.

### Advantages

- tiếp cận nhiều người với chi phí thấp;
- chuẩn hóa response;
- hỗ trợ segmentation và frequency estimate;
- anonymous format có thể giảm ngại chia sẻ.

### Disadvantages

- khó hỏi tiếp;
- wording bias và misunderstanding;
- self-selection/nonresponse bias;
- câu trả lời rộng nhưng nông;
- không phù hợp khám phá domain chưa hiểu.

### When to use

Khi đã hiểu đủ để hỏi đúng câu và cần breadth, ranking, frequency hoặc segment comparison.

### Example

Sau interviews, khảo sát 500 couriers về tần suất pickup delay theo khung giờ, loại restaurant và cách họ nhận notification.

## 7. Observation / Contextual Inquiry

### What

Quan sát stakeholder thực hiện công việc trong context thật; contextual inquiry kết hợp quan sát với câu hỏi ngắn tại chỗ.

### How

1. Chọn representative situations, gồm peak và exception nếu an toàn.
2. Thỏa thuận consent, privacy và non-disruption.
3. Ghi action, artifact, handoff, interruption, workaround và environment.
4. Hỏi “vì sao” tại thời điểm phù hợp, không làm hỏng task.
5. Phân biệt observed fact với interpretation.
6. Debrief để stakeholder giải thích.

### Advantages

- thấy real work và tacit knowledge;
- phát hiện workaround, interruption, physical/social constraints;
- giảm recall bias;
- tốt cho usability và operational workflow.

### Disadvantages

- tốn thời gian và access khó;
- observer effect;
- privacy/safety concern;
- hiếm khi quan sát được rare failure;
- analyst dễ diễn giải sai nếu thiếu debrief.

### When to use

Khi work phụ thuộc context, collaboration, artifact hoặc người dùng khó diễn đạt quy trình.

### Example

Quan sát restaurant staff trong giờ trưa: tablet đặt xa khu chế biến, âm thanh bị che, staff đánh dấu món hết trên giấy trước khi cập nhật app.

## 8. Workshop

### What

Phiên làm việc có facilitation, nhiều stakeholder cùng tạo, review hoặc quyết định artifact.

### How

1. Đặt outcome, agenda, pre-read và decision rules.
2. Mời đủ knowledge/authority/impact representatives.
3. Chuẩn bị shared model: process, event storm, rule table hoặc prototype.
4. Facilitate để tách fact, assumption, option và decision.
5. Quản lý power imbalance và airtime.
6. Ghi parking lot, conflict, owner và due date.
7. Phát decision record sau phiên.

### Advantages

- tăng tốc shared understanding;
- làm lộ conflict và vocabulary mismatch;
- đồng sáng tạo model;
- có thể ra quyết định ngay nếu authority hiện diện.

### Disadvantages

- scheduling và facilitation khó;
- người quyền lực lấn át;
- groupthink;
- scope dễ trôi;
- không phù hợp topic cá nhân/nhạy cảm nếu không chuẩn bị.

### When to use

Khi cần cross-functional agreement, resolve conflict, map process hoặc prioritize.

### Example

Workshop cancellation policy với Product, Restaurant Ops, Courier Ops, Finance, Support và Legal; dùng order-state timeline để xác định cost/risk ở từng điểm.

## 9. Brainstorming

### What

Kỹ thuật tạo nhiều ý tưởng/options trước khi đánh giá.

### How

1. Viết prompt và constraint rõ.
2. Tách divergent generation khỏi convergent evaluation.
3. Cho silent idea generation trước để giảm anchoring.
4. Nhóm themes và làm rõ intent.
5. Đánh giá theo value, feasibility, risk và goal.
6. Chuyển ý tưởng thành hypothesis/candidate requirement, không tự động accept.

### Advantages

- tạo breadth và alternatives;
- huy động knowledge đa dạng;
- hữu ích khi solution space chưa rõ;
- giúp thoát khỏi current-process bias.

### Disadvantages

- dominant voice/anchoring;
- ý tưởng nhiều nhưng evidence ít;
- dễ biến wish list thành requirements;
- cần bước evaluation nghiêm túc.

### When to use

Khi cần explore options, risks, failure modes hoặc opportunities.

### Example

Brainstorm cách giảm failed delivery: address confirmation, location pin, contact fallback, delivery note, risk flag. Sau đó validation từng hypothesis với data và users.

## 10. Document analysis

### What

Nghiên cứu tài liệu và artifacts hiện có: policy, contract, regulation, form, report, logs, SOP, API specification, support tickets.

### How

1. Xác định source owner, version, authority và freshness.
2. Trích thuật ngữ, fields, rules, exceptions, obligations và conflicts.
3. So sánh nhiều documents và actual practice.
4. Đánh dấu statement là fact, rule, assumption hay question.
5. Xác nhận interpretation với domain/legal owner.

### Advantages

- nhanh để học domain và chuẩn bị interview;
- phát hiện constraints/rules lịch sử;
- có evidence và vocabulary;
- truy cập được rare cases qua logs/tickets.

### Disadvantages

- outdated/inconsistent;
- mô tả official process, không phải actual work;
- context/rationale có thể thiếu;
- legal text cần chuyên gia diễn giải;
- dễ copy legacy solution thành requirement mới.

### When to use

Gần như luôn hữu ích ở đầu discovery; đặc biệt với regulated, legacy và integration-heavy systems.

### Example

Phân tích payment contract, refund policy, chargeback tickets và settlement report để tìm states, time limits và reconciliation rules.

## 11. Focus group

### What

Thảo luận được điều phối với một nhóm người có profile mục tiêu để khám phá perception, language, expectation và reaction.

### How

1. Xác định segment và recruitment criteria.
2. Chuẩn bị prompts/scenarios/prototype.
3. Facilitate để mọi người tham gia.
4. Quan sát agreement, disagreement và reasoning.
5. Không coi frequency trong nhóm nhỏ là thống kê population.
6. Kết hợp survey hoặc interviews khi cần evidence khác.

### Advantages

- participant kích hoạt ý tưởng/ký ức cho nhau;
- khám phá language, expectation và social norm;
- so sánh phản ứng với concept/prototype;
- hiệu quả hơn nhiều interview riêng cho breadth định tính.

### Disadvantages

- conformity và dominant participant;
- privacy hạn chế;
- không đại diện định lượng;
- topic nhạy cảm có thể bị che;
- logistics/recruitment.

### When to use

Khi khám phá perception, concept reaction hoặc khác biệt giữa segments; không dùng để phê duyệt requirement thay decision owner.

### Example

Focus group khách mới và khách thường xuyên về cách họ hiểu ETA range, fee breakdown và cancellation warning.

## 12. Prototyping

### What

Tạo representation có chủ đích để học về requirement: paper sketch, clickable flow, service simulation, technical spike hoặc data prototype.

### Types

- **Throwaway:** học nhanh rồi bỏ.
- **Evolutionary:** dần trở thành product, cần quality discipline.
- **Horizontal:** breadth của interface/workflow.
- **Vertical:** sâu một slice để kiểm tra feasibility/integration.

### How

1. Viết learning question trước.
2. Chọn fidelity thấp nhất đủ trả lời.
3. Nói rõ phần giả, assumption và limitation.
4. Cho representative users thực hiện task, không chỉ hỏi thích/không.
5. Quan sát error, hesitation, workaround và language.
6. Ghi finding → requirement/decision/question.

### Advantages

- biến abstraction thành trải nghiệm;
- phát hiện missing flow và usability issue;
- kiểm tra feasibility/risk;
- tạo shared object để thảo luận.

### Disadvantages

- stakeholder tưởng prototype là gần hoàn thành;
- UI anchoring quá sớm;
- throwaway code bị đưa vào production;
- feedback thiên về hình thức dễ thấy;
- prototype success không chứng minh production quality.

### When to use

Khi uncertainty về interaction, workflow, desirability hoặc technical feasibility cao.

### Example

Clickable checkout prototype kiểm tra người dùng có hiểu fee breakdown, quote expiry và address confirmation trước payment hay không.

## 13. Question design và bias control

### Từ yếu tới mạnh

| Yếu/bias | Tốt hơn |
|---|---|
| “Bạn muốn notification chứ?” | “Khi trạng thái thay đổi, bạn hiện biết bằng cách nào?” |
| “App chậm gây khó chịu đúng không?” | “Hãy kể lần gần nhất bạn bỏ task vì phải chờ.” |
| “Có exception nào không?” | “Lần gần nhất quy trình không đi đúng chuẩn đã xảy ra gì?” |
| “Feature nào quan trọng?” | “Nếu chỉ giữ ba outcome cho release, failure nào không chấp nhận được?” |

### Phân biệt dữ liệu

Trong notes, đánh dấu:

- `OBS`: observed fact;
- `QUOTE`: stakeholder statement;
- `INT`: analyst interpretation;
- `ASM`: assumption;
- `REQ`: candidate requirement;
- `Q`: unresolved question;
- `DEC`: approved decision.

Không biến quote thành accepted requirement mà bỏ qua analysis/authority.

## 14. Synthesis và validation

Sau mỗi activity:

1. Chuẩn hóa vocabulary.
2. Nhóm goals, events, rules, data, quality và issues.
3. Tách fact, opinion, assumption và conflict.
4. Tạo candidate requirements có source.
5. Đối chiếu nguồn khác.
6. Gửi summary cho participant xác nhận interpretation.
7. Đưa conflict/decision tới đúng authority.

### Evidence conflict

Nếu policy nói một điều nhưng observation thấy staff làm khác, không chọn ngay một bên. Có thể:

- policy cũ;
- staff vi phạm;
- exception không được document;
- system constraint tạo workaround;
- analyst quan sát sample không đại diện.

Ghi conflict và điều tra owner/rationale/risk.

## 15. Ethics, consent và privacy

- Giải thích purpose và cách dùng dữ liệu nghiên cứu.
- Thu consent phù hợp cho recording/observation.
- Chỉ thu dữ liệu cần thiết; bảo vệ thông tin nhạy cảm.
- Không để manager hiện diện làm nhân viên không thể nói thật nếu topic nhạy cảm.
- Không hứa anonymity nếu quy trình không bảo đảm được.
- Đại diện nhóm bị tác động, tránh extractive research.

## 16. Khi không nên dùng từng kỹ thuật đơn độc

- Interview đơn độc không chứng minh frequency.
- Questionnaire không khám phá tốt domain chưa hiểu.
- Observation không giải thích đầy đủ rationale.
- Workshop không bảo đảm người ít quyền lực nói thật.
- Brainstorming không tạo accepted requirements.
- Document analysis không chứng minh current practice.
- Focus group không đại diện population.
- Prototype không chứng minh backend feasibility hay production quality nếu không được thiết kế cho câu hỏi đó.

## 17. Common mistakes

1. Hỏi solution preference trước problem.
2. Chỉ nói với người dễ tiếp cận.
3. Không có objective cho session.
4. Ghi notes nhưng mất source/context.
5. Chỉ hỏi normal process.
6. Dùng leading questions.
7. Không validate summary.
8. Coi prototype feedback là requirement approval.
9. Không quản lý consent/privacy.
10. Dùng một kỹ thuật cho mọi uncertainty.

## 18. Artifact checklist

- [ ] Objective gắn decision/uncertainty cụ thể?
- [ ] Participants bao phủ knowledge, authority và impact?
- [ ] Technique phù hợp loại evidence?
- [ ] Questions gồm goal, real example, exception và quality?
- [ ] Bias/power/privacy risks được xử lý?
- [ ] Fact, quote, interpretation, assumption, requirement và decision tách rõ?
- [ ] Notes có source/time/context?
- [ ] Summary được participant xác nhận?
- [ ] Conflict và open question có owner?
- [ ] Kết quả trace được tới candidate requirements?

## 19. Exercises

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

## 20. Exam preparation

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

## 21. Summary và dependency tiếp theo

Elicitation tạo evidence và candidate requirements. Học tiếp [Requirement Analysis](analysis.md) để biến chúng thành tập requirements coherent, feasible và verifiable.

