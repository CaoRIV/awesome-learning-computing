# Mathematics for Computing — Practical Foundations

Knowledge base tiếng Việt dành cho người đã biết lập trình cơ bản và muốn hiểu phần toán thực sự đứng sau software, algorithms, data và Artificial Intelligence. Track không bắt đầu bằng việc học thuộc ký hiệu. Nó bắt đầu từ câu hỏi gần với lập trình: một điều kiện nói chính xác điều gì, vì sao một vòng lặp luôn đúng, có bao nhiêu trường hợp cần xét, dữ liệu có đủ để kết luận hay không, và cấu trúc nào đang ẩn sau bài toán.

> Statement → Model → Reason → Prove → Measure → Apply

Toán trong computing không chỉ dùng để tính số. Nó cung cấp ngôn ngữ để mô tả object và quan hệ, công cụ để chứng minh chương trình đúng, cách định lượng sự không chắc chắn và representation để máy tính xử lý dữ liệu. Một developer không cần trở thành nhà toán học, nhưng cần nhận ra lúc nào trực giác chưa đủ và biết dùng công cụ nào để kiểm tra.

## Cách track tránh biến toán thành catalog công thức

Mỗi module bắt đầu bằng một vấn đề computing, sau đó mới đưa vào định nghĩa và ký hiệu. Logic xuất hiện khi cần viết rule không mơ hồ. Set và relation xuất hiện khi cần mô hình hóa user, course và prerequisite. Induction xuất hiện khi cần chứng minh recursion. Probability và statistics xuất hiện khi dữ liệu không cho câu trả lời chắc chắn. Graph và vector xuất hiện khi cấu trúc bài toán yêu cầu.

Track giữ công thức ở mức đủ để reasoning. Mỗi công thức đều đi cùng ý nghĩa, assumption, ví dụ và cách hiểu sai thường gặp. Việc thay số vào công thức mà không biết đại lượng đại diện điều gì không được coi là hiểu.

## Track dành cho ai

Baseline phù hợp là người biết biến, condition, loop, function, collection và đọc một đoạn Python ngắn. Không yêu cầu calculus, đại số tuyến tính hay xác suất trước đó. Người từng sợ toán vẫn có thể bắt đầu vì nội dung ưu tiên câu chữ và ví dụ nhỏ trước ký hiệu.

Track phù hợp với sinh viên Computer Science, self-taught developer, software engineer muốn củng cố reasoning, và người chuẩn bị học algorithms, databases, AI hoặc distributed systems. Đây không phải khóa luyện thi toán, không đi sâu vào chứng minh hàn lâm tách khỏi ứng dụng.

## Bắt đầu học

Đọc [Learning Roadmap](00-meta/learning-roadmap.md), sau đó học tuần tự tám module. Mỗi module cần khoảng 60–80 phút đọc và thêm thời gian tự giải bài.

| # | Module | Kết quả chính |
|---:|---|---|
| 1 | [Mathematical Thinking and Proof](modules/01-mathematical-thinking-and-proof.md) | Biến phát biểu mơ hồ thành claim có assumption và evidence |
| 2 | [Logic for Computing](modules/02-logic-for-computing.md) | Viết và kiểm tra điều kiện, implication, quantifier và Boolean rule |
| 3 | [Sets, Functions and Relations](modules/03-sets-functions-relations.md) | Mô hình hóa collections, mappings, equivalence và order |
| 4 | [Induction, Recursion and Invariants](modules/04-induction-recursion-invariants.md) | Chứng minh behavior lặp, recursive và loop correctness |
| 5 | [Counting and Probability](modules/05-counting-and-probability.md) | Đếm đúng không gian trường hợp và reasoning dưới uncertainty |
| 6 | [Statistics and Data Reasoning](modules/06-statistics-and-data-reasoning.md) | Tóm tắt dữ liệu, hiểu sampling, uncertainty và experiment |
| 7 | [Graphs and Trees](modules/07-graphs-and-trees.md) | Mô hình hóa kết nối, dependency, path và hierarchy |
| 8 | [Vectors, Matrices and Linear Algebra](modules/08-vectors-matrices-linear-algebra.md) | Hiểu vector representation, matrix transformation và similarity |

## Case study xuyên suốt

`Learning Platform` quản lý learners, courses, prerequisite và kết quả học. Nó cần quyết định ai đủ điều kiện đăng ký, kiểm tra prerequisite có cycle hay không, thống kê thời gian hoàn thành, chạy experiment cho một giao diện mới và biểu diễn course bằng vector để tìm nội dung gần nhau.

Case study đủ rộng để nối các khái niệm nhưng vẫn giữ một domain quen thuộc. Các con số minh họa nhỏ để có thể tự tính bằng tay; code chỉ dùng để kiểm tra reasoning, không che phần toán sau library call.

## Cách học hiệu quả

Khi gặp ký hiệu, hãy đọc nó thành câu tiếng Việt trước. `∀x ∈ A` nghĩa “với mọi x thuộc A”; đừng cố ghi nhớ hình dạng mà bỏ meaning. Trước khi tính, hãy viết object là gì, domain nào, assumption nào và kết quả cần nói điều gì.

Tự tìm counterexample là thói quen quan trọng. Một claim đúng với ba ví dụ chưa thành chứng minh. Nếu claim sai, một counterexample hợp lệ là đủ bác bỏ. Nếu claim đúng, proof phải bao phủ toàn bộ trường hợp được nêu.

Mỗi bài thực hành nên có ba lớp: giải một ví dụ nhỏ bằng tay, giải thích reasoning bằng lời, rồi dùng code kiểm tra nhiều input. Code là dụng cụ kiểm tra, không thay lời giải.

## Phạm vi và điểm dừng

Core path bao gồm proof fundamentals, propositional/predicate logic, sets, functions, relations, induction, recursion, invariants, combinatorics, probability, descriptive/inferential statistics, experiments, graphs, trees, vectors, matrices và linear transformations ở mức nền tảng.

Calculus, differential equations, numerical analysis, formal logic nâng cao, abstract algebra, cryptography, optimization, spectral graph theory và Mathematics for Machine Learning chuyên sâu là extension paths. Chúng nên được mở khi algorithms, systems, graphics, security hoặc AI tạo nhu cầu cụ thể.

## Exit criteria

Bạn hoàn thành track khi có thể viết một claim cùng assumption, phân biệt ví dụ với proof, dùng logic để đọc điều kiện, mô hình hóa domain bằng set/function/relation và chứng minh một recursive hoặc iterative behavior bằng induction/invariant. Bạn cũng cần đếm sample space không double-count, tính và giải thích conditional probability, đọc trung bình cùng độ phân tán, không nhầm correlation với causation, mô hình hóa dependency bằng graph và giải thích vector/matrix đang biểu diễn điều gì.

Quan trọng nhất, bạn phải biết dừng trước một kết luận quá mạnh. Toán tốt trong computing không làm lời giải trông phức tạp; nó làm assumption và giới hạn trở nên nhìn thấy.
