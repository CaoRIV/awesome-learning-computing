# 8. Responsible AI in Production — Phát hành nhỏ, quan sát kỹ

> **Thời lượng:** 60–75 phút  
> **Prerequisites:** [Module 7](07-build-a-grounded-ai-assistant.md)  
> **Status:** Reviewed

## Overview

Production không chỉ là đặt endpoint quanh model. Khi user, data và workflow thay đổi, behavior có thể thay đổi dù code không đổi. Model provider có thể cập nhật version, handbook có document mới, query distribution chuyển sang product mới, hoặc agent quá tin vào câu trả lời có vẻ tự tin. Responsible AI ở mức thực dụng là thiết kế để nhìn thấy, giới hạn và phục hồi từ những thay đổi đó.

Module này không dựng một MLOps platform. Với team nhỏ, một release gate rõ, versioning, logs an toàn, vài metrics và kill switch thường đủ. Hạ tầng chỉ mở rộng khi traffic, regulation hoặc số models tạo nhu cầu thật.

## 1. System boundary và risk trước deployment

Vẽ data flow: input đến từ đâu, fields nào chứa personal data, dữ liệu nào gửi tới provider, artifacts nào được lưu, output đi tới ai và action nào có side effect. Sau đó chọn vài failure có impact lớn: leak policy, route sai Security ticket, bịa refund rule hoặc tool thực hiện action ngoài quyền.

Risk có thể xem bằng impact × likelihood × detectability ở mức thô. Không cần con số giả chính xác; mục đích là ưu tiên control. Một lỗi dễ phục hồi như draft viết chưa hay có thể chấp nhận human review. Một refund tự động sai cần authorization, amount limit và audit dù likelihood thấp.

System card một trang nên ghi intended use, out-of-scope use, user group, data/eval summary, known limitations, owner và fallback. Tài liệu này hữu ích cho engineering, product và support cùng hiểu feature; nó không phải tuyên bố model an toàn tuyệt đối.

## 2. Release theo từng mức autonomy

Bắt đầu shadow mode nếu có thể: chạy model nhưng chưa hiển thị hoặc chưa ảnh hưởng decision, so output với workflow hiện tại. Tiếp theo pilot với nhóm agents nhỏ và output dạng suggestion. Thu thập acceptance, corrections, latency và qualitative feedback. Auto-action chỉ đến sau khi evidence đủ và action có giới hạn.

Feature flag hoặc kill switch cho phép tắt nhanh mà không redeploy toàn hệ thống. Rollback phải bao gồm model/prompt/retrieval config, không chỉ code. Với RAG, index version cũng là một phần release vì đổi corpus có thể thay answer.

Một release gate gọn có thể yêu cầu offline eval đạt threshold, không regression trên safety set, privacy review hoàn tất, load/latency trong budget, dashboard và on-call owner sẵn sàng. Checklist chỉ có giá trị nếu mỗi item có evidence hoặc người chịu trách nhiệm.

## 3. Version mọi thứ làm thay đổi behavior

Model name, provider version, prompt template, tool schema, threshold, preprocessing, dataset và index đều có thể đổi output. Log một `configuration_version` trỏ tới bundle các thành phần này. Không nhất thiết log toàn prompt chứa personal data; có thể log template version, hashes và structured metadata.

Reproducibility với hosted model có giới hạn vì backend có thể nondeterministic hoặc được cập nhật. Mục tiêu vẫn là biết request đã đi qua config nào, retrieve sources nào và tool nào được gọi. Trace đó đủ để điều tra nhiều incidents mà không lưu dữ liệu vượt nhu cầu.

## 4. Observability theo ba lớp

**System metrics** gồm latency, error rate, timeout, token/compute cost và throughput. **Model/application metrics** gồm class distribution, confidence, retrieval hit, citation validity, refusal rate và tool-call failure. **Outcome metrics** gồm agent acceptance, handling time, escalation, customer resolution và incident count.

Chỉ theo dõi metric có action. Nếu citation validity giảm, owner kiểm tra parser/index. Nếu latency tăng, trace chỉ ra retrieval hay generation. Nếu acceptance giảm, sample output và xem distribution shift. Dashboard 50 charts không có response playbook kém hữu ích hơn sáu signals có threshold và owner.

Production ground truth thường đến chậm hoặc không đầy đủ. Agent correction là useful signal nhưng có bias: họ có thể chấp nhận vì automation bias hoặc sửa theo style cá nhân. Kết hợp telemetry với periodic audited sample và user feedback thay vì coi click acceptance là sự thật.

## 5. Drift và feedback loop

Data drift nghĩa input distribution thay đổi; concept drift nghĩa relationship giữa input và desired output thay đổi. Product launch mới tạo vocabulary mới là data drift. Policy đổi khiến cùng ticket cần queue khác là concept drift. Model performance chỉ biết chắc khi có labels/outcome, vì vậy proxy distribution metrics không thay thế evaluation định kỳ.

Feedback data không nên tự động chảy thẳng vào retraining. Một model ảnh hưởng behavior của agent, rồi behavior đó trở thành label có thể khuếch đại lỗi cũ. Cần review, sampling, deduplication và versioned training set. Retraining là release mới có eval, không phải cron job mặc định.

## 6. Privacy, security và retention

Data minimization bắt đầu trước prompt: bỏ fields không cần, redact PII khi phù hợp và không gửi secrets. Xác định provider retention/training policy theo contract đang dùng; không suy đoán từ marketing. Credentials nằm trong secret manager và tool execution service, không nằm trong system prompt.

Logs có thể trở thành data leak vì chứa raw ticket, retrieved policy và output. Chỉ lưu phần cần debug, áp access control, retention limit và audit. Với incident reproduction cần raw sample, dùng restricted store hoặc consent/process phù hợp thay vì log mặc định mọi request.

Security test nên có prompt injection, data exfiltration attempt, unauthorized document, malformed tool arguments và replay action. Model response chỉ là một lớp; enforcement nằm ở retrieval filters, schema validation, authorization và network/tool boundary.

## 7. Human factors và transparency

UI nên nói output là gợi ý AI, cho thấy nguồn khi có và làm correction dễ. Confidence số lẻ có thể tạo cảm giác chính xác giả; đôi khi các trạng thái “tự động”, “cần kiểm tra”, “không đủ evidence” dễ hành động hơn. Human-in-the-loop chỉ hữu ích khi reviewer có thời gian, context và authority để phản đối model.

Automation bias tăng khi output trôi chảy hoặc hệ thống hiếm khi sai. Hãy đưa uncertainty và citation vào đúng nơi decision xảy ra, không giấu trong trang chi tiết. Với action quan trọng, confirmation phải hiển thị arguments cụ thể thay vì nút “Approve AI”.

Người dùng cần con đường báo lỗi và biết owner xử lý. Incident process nên giữ examples, config version, impact window, mitigation và regression test sau sửa. Không đổ lỗi chung cho “AI”; tìm control hoặc assumption đã thất bại.

## 8. Cost và latency là quality attributes

Model lớn hơn có thể cải thiện answer nhưng làm agent chờ, tăng timeout và cost. Đặt budget theo request và theo tháng, đo cache hit, context size và tool calls. Routing model đơn giản có thể dùng model nhỏ; chỉ câu phức tạp mới escalation model mạnh hơn. Tuy vậy routing nhiều tầng chỉ nên thêm khi traffic đủ để bù complexity.

Cache cần chú ý permission, freshness và personal data. Một answer policy có thể cache theo document version; một answer chứa ticket riêng không được chia sẻ giữa users. Cost optimization không được phá security boundary.

## 9. Production checklist vừa đủ

Trước pilot, team cần trả lời được: ai là user và owner; failure nào nghiêm trọng; eval set đại diện ở đâu; config nào đang chạy; data nào rời hệ thống; fallback và kill switch hoạt động ra sao; logs/metrics nào phát hiện lỗi; ai review feedback và quyết định release tiếp theo. Nếu một câu chưa rõ, xử lý nó thường có giá trị hơn tuning thêm prompt.

Sau pilot, review evidence theo kỳ đã định. Feature có giảm handling time không, agent sửa gì, citation nào hay sai, group nào bị phục vụ kém, cost/latency có trong budget không? Quyết định tiếp theo có thể mở rộng, giữ pilot, thu hẹp scope hoặc tắt. Dừng một feature không tạo đủ value là engineering thành công, không phải thất bại.

## 10. Final practice — thiết kế release plan

Viết release plan hai trang cho grounded assistant của Module 7. Trang đầu có data flow, ba risks quan trọng, control và owner. Trang hai có offline gate, pilot scope, năm metrics, rollback/kill switch và lịch review. Thêm một incident scenario: model viện dẫn policy hết hạn và agent đã gửi cho khách; mô tả detection, containment, correction và regression test.

Bài đạt khi control nằm ở system boundary phù hợp, không chỉ là một prompt hứa “hãy an toàn”. Kế hoạch phải có cách tắt hoặc quay lại behavior cũ, và không log raw sensitive data mặc định.

## One-minute summary

Một AI feature đáng tin không phải feature không bao giờ sai. Nó có intended use rõ, evidence trước release, quyền hạn giới hạn, quan sát được, bảo vệ dữ liệu và phục hồi được khi assumption sai. Bắt đầu với suggestion, scope nhỏ và feedback có kiểm soát; chỉ tăng autonomy hoặc infrastructure khi outcome chứng minh nhu cầu.

Hoàn thành module này là kết thúc core track. Bước tiếp theo nên được chọn theo bài toán thật: Mathematics/Deep Learning sâu hơn cho model work, NLP hoặc Computer Vision cho modality, và backend/data/security cho AI application engineering.

