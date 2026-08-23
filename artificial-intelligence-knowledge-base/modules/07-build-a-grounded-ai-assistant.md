# 7. Build a Grounded AI Assistant — Trả lời từ evidence

> **Thời lượng:** 75–90 phút  
> **Prerequisites:** [Module 6](06-language-models-and-generative-ai.md)  
> **Status:** Reviewed

## Overview

Support agent muốn hỏi “khách được hoàn tiền trong bao lâu?” và nhận câu trả lời dựa trên handbook mới nhất. Đưa toàn bộ handbook vào prompt vừa tốn context vừa khó kiểm soát permission. Chỉ hỏi model bằng kiến thức pretrained thì không có đảm bảo policy đúng phiên bản. Retrieval-Augmented Generation (RAG) giải quyết bằng cách tìm một số đoạn liên quan tại request time, rồi yêu cầu model trả lời từ những đoạn đó.

RAG không tự động làm câu trả lời đúng. Retrieval có thể bỏ sót document, chunk có thể mất ngữ cảnh, model có thể diễn giải quá evidence và citation có thể gắn sai. Một grounded assistant vì thế là pipeline được đánh giá theo từng phần, không chỉ là prompt với vector database.

## 1. Pipeline tối thiểu

Luồng cơ bản gồm ingest documents, chia chunks, tạo search representation và index. Khi user hỏi, system kiểm tra identity/permission, retrieve top candidates, có thể rerank, dựng context, generate answer rồi trả citation. Nếu evidence dưới ngưỡng, system nói không tìm thấy hoặc chuyển người phụ trách.

```text
Documents → parse → chunks → index
                              ↑
User question → permission → retrieve → context → generate → answer + citations
                                    ↘ insufficient evidence → fallback
```

Mỗi mũi tên là một contract có thể test. Parse phải giữ title/version. Chunk phải có source metadata. Retrieval phải tôn trọng access scope. Generation chỉ dùng evidence và citation phải trỏ đúng chunk. Nhìn pipeline theo contracts giúp debug: câu sai do không retrieve đúng khác với retrieve đúng nhưng model diễn giải sai.

## 2. Chunking là quyết định về meaning

Chunk quá nhỏ mất context; “trong vòng 7 ngày” không còn biết nói về refund nào. Chunk quá lớn chứa nhiều chủ đề, làm retrieval kém chính xác và context tốn token. Bắt đầu bằng document structure: heading + một vài paragraphs, giữ metadata như document ID, section, version và effective date. Chỉ tuning kích thước sau khi xem retrieval errors.

Overlap có thể giữ câu chuyển tiếp nhưng tạo duplicates và citation rối. Table, code hoặc FAQ cần parser phù hợp hơn cắt theo số ký tự. Không có chunk size thần kỳ dùng cho mọi corpus. Evaluation queries đại diện mới quyết định.

## 3. Retrieval: lexical, semantic và hybrid

Lexical search như BM25 mạnh với keyword, mã lỗi và tên sản phẩm chính xác. Semantic retrieval dùng embeddings để tìm ý nghĩa gần nhau, hữu ích khi query và document dùng từ khác. Hybrid kết hợp cả hai thường là baseline thực dụng cho handbook có cả ngôn ngữ tự nhiên lẫn identifiers.

Vector similarity không chứng minh relevance. Embedding có thể đưa hai đoạn cùng chủ đề lại gần dù một đoạn đã hết hiệu lực hoặc dành cho quốc gia khác. Metadata filters phải loại document không hợp lệ trước hoặc trong retrieval. Reranker có thể cải thiện thứ tự top candidates nhưng tăng latency/cost; chỉ thêm khi error analysis cho thấy ranking là bottleneck.

## 4. Một retriever nhỏ không cần vector database

Ví dụ sau dùng TF–IDF và cosine similarity để làm local retrieval. Nó cố ý dùng cùng công cụ classical ML đã học, cho thấy RAG concept không phụ thuộc một dịch vụ vector cụ thể.

```python
from dataclasses import dataclass
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity


@dataclass(frozen=True)
class Chunk:
    source: str
    section: str
    text: str


chunks = [
    Chunk("refund-policy-v3", "Thời hạn",
          "Yêu cầu hoàn tiền hợp lệ được xử lý trong vòng 7 ngày làm việc."),
    Chunk("account-guide-v2", "Khôi phục",
          "Người dùng có thể yêu cầu mã khôi phục sau khi xác minh email."),
    Chunk("billing-guide-v4", "Thanh toán trùng",
          "Giao dịch bị tính hai lần phải được chuyển tới nhóm Billing để kiểm tra."),
]

vectorizer = TfidfVectorizer()
document_vectors = vectorizer.fit_transform([chunk.text for chunk in chunks])


def retrieve(question: str, limit: int = 2) -> list[tuple[Chunk, float]]:
    query_vector = vectorizer.transform([question])
    scores = cosine_similarity(query_vector, document_vectors)[0]
    indexes = scores.argsort()[::-1][:limit]
    return [(chunks[index], float(scores[index])) for index in indexes]


for chunk, score in retrieve("Bao lâu thì yêu cầu hoàn tiền được xử lý?"):
    print(f"{score:.3f} [{chunk.source} — {chunk.section}] {chunk.text}")
```

Trong production, index được build khi documents thay đổi và loaded như artifact, không fit lại trong mỗi request. Với corpus lớn, specialized index cần thiết. Tuy vậy, đừng thêm distributed vector store cho 200 policy chunks nếu local index đáp ứng latency và deployment; scale thật mới biện minh infrastructure.

## 5. Generation contract có citation và abstention

Context gửi model nên đánh số sources và giữ metadata. Instruction yêu cầu chỉ dùng sources, citation theo ID và nói rõ không đủ evidence khi sources không trả lời. Application kiểm tra citations có nằm trong retrieved IDs; với high-risk facts có thể yêu cầu câu trả lời chứa trích dẫn cho từng claim.

```text
Bạn hỗ trợ nhân viên support. Chỉ trả lời bằng thông tin trong SOURCES.
Nếu SOURCES không đủ, trả lời: "Không tìm thấy đủ thông tin trong handbook."
Mọi fact về policy phải có citation dạng [S1].

SOURCES
[S1] refund-policy-v3 / Thời hạn
Yêu cầu hoàn tiền hợp lệ được xử lý trong vòng 7 ngày làm việc.

QUESTION
Bao lâu thì yêu cầu hoàn tiền được xử lý?
```

Expected answer có thể là: “Yêu cầu hoàn tiền hợp lệ được xử lý trong vòng 7 ngày làm việc [S1].” Citation cho phép agent mở source để kiểm tra. Nó không chỉ là decoration; UI phải làm citation actionable và source phải hiển thị effective version.

## 6. Permission phải đi trước retrieval

Nếu user không được xem HR policy, document đó không được lọt vào candidate context. Lọc sau khi model đã thấy content là quá muộn. Index có thể lưu access groups và query filter bằng identity đã được application xác thực. Cache cũng phải key theo scope thích hợp để không trả kết quả của người có quyền cao cho người khác.

Document content là untrusted ngay cả khi nội bộ, vì có thể chứa instruction hoặc text do user upload. Generator không được nhận tools nhạy cảm chỉ vì đang làm RAG. Retrieval và action authorization là hai boundary độc lập.

## 7. Đánh giá retrieval và answer riêng

Retrieval eval có query, relevant source IDs và có thể có hard negatives. Đo `Recall@k`: source đúng có nằm trong top k không? Nếu không, generator không có cơ hội trả lời đúng. Answer eval kiểm tra groundedness, correctness, citation và abstention. Tách hai lớp giúp tránh sửa prompt khi lỗi thật nằm ở chunking/search.

Một evaluation set ban đầu có thể chỉ 30–50 câu nhưng phải lấy từ query thật hoặc domain expert, gồm câu không có answer. Câu không có answer kiểm tra assistant có biết dừng hay vẫn bịa. Với policy versioning, thêm test bảo đảm phiên bản hết hiệu lực không được retrieve.

## 8. Failure modes và cách cải thiện có thứ tự

Nếu source đúng không vào top k, kiểm tra parsing, metadata filter, query wording, lexical/semantic mix và chunk boundary. Nếu source đúng đã có nhưng answer sai, kiểm tra context construction, instruction, model hoặc ambiguity của source. Nếu answer đúng nhưng citation sai, sửa mapping/validation. Nếu latency cao, đo từng stage trước khi cache hoặc đổi infrastructure.

RAG agentic nhiều vòng, knowledge graph và query rewriting có thể hữu ích cho câu hỏi phức tạp, nhưng làm trace và eval khó hơn. Core assistant nên bắt đầu bằng một retrieval call và một generation call. Chỉ thêm bước khi evaluation set chứng minh loại câu hỏi cụ thể chưa được giải quyết.

## 9. Mini practice

Tạo 8–12 chunks từ một handbook hoặc tài liệu học nhỏ. Viết 10 queries: sáu có answer, hai dùng từ khác document, hai không có answer. Chạy TF–IDF retriever, ghi Recall@2 và xem từng miss. Sau đó thiết kế prompt grounded cùng validation rule cho citations; không bắt buộc gọi model.

Bài đạt khi mỗi chunk giữ source metadata, query không có answer có expected fallback và report phân biệt retrieval miss với generation risk. Nếu bạn đổi chunking, hãy ghi hypothesis chứ không chỉ thử ngẫu nhiên.

## Checkpoint

Bạn cần mô tả RAG như pipeline có contracts, giải thích chunking trade-off, đặt permission trước retrieval và biết đo Recall@k riêng với answer quality. Module cuối đưa pipeline này vào một release có monitoring, privacy và incident path vừa đủ.

