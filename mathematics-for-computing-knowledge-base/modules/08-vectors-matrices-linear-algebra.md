# 8. Vectors, Matrices and Linear Algebra — Biểu diễn quantities và transformations

> **Thời lượng:** 75–90 phút  
> **Prerequisites:** [Module 7](07-graphs-and-trees.md)  
> **Status:** Reviewed

## Overview

Linear algebra cung cấp ngôn ngữ cho nhiều quantities cùng lúc. Vector có thể là hướng trong game, màu pixel, features của course hoặc counts của user. Matrix có thể chứa data, nhưng ý nghĩa mạnh hơn là transformation biến vector đầu vào thành vector đầu ra.

Core module không bắt đầu bằng thao tác nhân ma trận lớn. Ta luôn hỏi mỗi coordinate có cùng basis/unit không, operation đang giữ information gì và result dùng để quyết định điều gì.

## 1. Scalar và vector

Scalar là một số. Vector là ordered tuple numbers trong cùng coordinate system, viết `x = [x₁, x₂, ⋯, xₙ]`. Order có meaning: `[difficulty, duration, project_ratio]` khác `[duration, difficulty, project_ratio]` dù values giống.

Geometric vector biểu diễn displacement có direction/magnitude. Feature vector biểu diễn object trong n-dimensional space. Course có thể là `[0.8, 0.6, 0.9]` sau khi normalize ba features. Representation là design decision, không phải fact tự nhiên.

Không cộng coordinates khác meaning tùy tiện. `[3 hours, 4 dollars]` có thể là record numeric nhưng norm Euclidean không có meaning nếu units chưa scale và objective chưa rõ.

## 2. Vector addition và scalar multiplication

Hai vectors cùng dimension cộng component-wise. Scalar multiplication scale mọi component:

```text
[1, 2] + [3, 4] = [4, 6]
2[1, 2] = [2, 4]
```

Trong geometry, addition ghép displacements. Trong data, weighted combination có thể trộn profiles. Operations chỉ hợp lệ khi coordinate systems khớp.

Linear combination `a₁v₁ + ⋯ + aₖvₖ` là nền của span, interpolation và models. Weighted average thêm condition weights sum 1; nếu weights non-negative, result nằm trong convex combination của points.

## 3. Norm và distance

Euclidean norm:

```text
||x||₂ = sqrt(x₁² + x₂² + ⋯ + xₙ²)
```

Distance Euclidean giữa x, y là `||x-y||₂`. Manhattan norm/distance dùng tổng absolute differences. Metric phù hợp domain: grid movement không diagonal có Manhattan meaning; physical straight-line có Euclidean.

Scale ảnh hưởng distance. Nếu duration nằm 0–1000 còn difficulty 0–1, duration thống trị. Standardization/normalization không chỉ preprocessing ritual; nó định nghĩa relative importance theo scale.

Norm zero chỉ cho zero vector; triangle inequality giữ distance hợp lý. Không phải mọi “similarity score” đều là metric; cosine distance variants cần kiểm tra properties nếu algorithm yêu cầu metric.

## 4. Dot product và angle

Dot product:

```text
x · y = x₁y₁ + x₂y₂ + ⋯ + xₙyₙ
```

Geometrically `x·y = ||x|| ||y|| cos θ`. Dot dương nghĩa hướng tương đối giống, zero orthogonal, âm đối hướng trong geometry. Projection amount của x lên unit direction u là `x·u`.

Trong scoring, dot product giữa user preference weights và course features tạo weighted score. Nếu weight/feature meaning không rõ, score chỉ là arithmetic.

Dot product tăng theo magnitude. Hai vectors cùng direction nhưng vector dài hơn có score lớn hơn. Khi chỉ quan tâm orientation/profile, cosine similarity chia norms:

```text
cosine(x,y) = (x·y) / (||x|| ||y||)
```

Undefined cho zero vector; implementation phải handle.

## 5. Orthogonality và projection

Vectors orthogonal khi dot product bằng zero. Trong geometry là vuông góc; trong feature space có thể hiểu directions độc lập theo representation, không tự có nghĩa statistical independence.

Projection của x lên non-zero v:

```text
proj_v(x) = ((x·v)/(v·v))v
```

Nó tìm component của x theo direction v; residual là phần còn lại. Least squares/regression mở rộng ý tưởng projection lên subspace.

Orthogonal basis giúp coordinates không trộn direction và tính ổn định hơn. Core không cần Gram–Schmidt thủ công, nhưng cần thấy basis choice làm coordinates đổi dù underlying vector không đổi.

## 6. Matrix là array và linear map

Matrix m×n có m rows, n columns. Nhìn như data table là một use: rows observations, columns features. Nhìn như linear map, matrix A biến vector n chiều thành vector m chiều: `y = Ax`.

Nếu:

```text
A = [[2, 0],
     [0, 3]]
```

thì A scale coordinate đầu 2 lần, coordinate sau 3 lần. Columns của A là output của basis vectors; vì vậy matrix hoàn toàn được xác định bởi tác động lên basis.

Shape là type. `A(m×n)` nhân `x(n×1)` cho `y(m×1)`. Dimension mismatch không chỉ syntax error; model đang nối representations không tương thích.

## 7. Matrix multiplication là composition

Entry của `AB` là dot product giữa row của A và column của B. Nhưng mental model tốt hơn: B transform trước, A transform sau; `ABx = A(Bx)`. Order quan trọng và matrix multiplication nói chung không commutative: `AB ≠ BA`.

Associative `A(BC)=(AB)C` cho phép nhóm computation, nhưng numerical performance/stability có thể khác. Identity matrix I giữ vector: `Ix=x`.

Trong graphics, scale rồi rotate khác rotate rồi scale nếu scale không đều. Trong neural network, mỗi layer là transformation cộng nonlinearity; nếu chỉ compose linear layers không activation, toàn bộ vẫn rút thành một linear transformation.

## 8. Transpose và symmetric structure

Transpose `Aᵀ` đổi rows thành columns. Với data matrix X (observations × features), `XᵀX` chứa pairwise feature dot products. Inner product có thể viết `xᵀy` khi vectors là columns.

Symmetric matrix thỏa `A=Aᵀ`. Adjacency matrix của undirected graph symmetric; directed graph thường không. Covariance matrix symmetric vì covariance(i,j)=covariance(j,i).

Transpose không đảo transformation. Inverse là khái niệm khác; chỉ một số matrices có inverse.

## 9. Systems of linear equations

Nhiều equations có thể viết `Ax=b`. Solution x là input được A map tới b. Có thể không có solution, đúng một solution hoặc vô số solutions tùy equations/geometry.

Hai equations cùng một line không cung cấp hai pieces of independent information. Rank đo số directions độc lập mà matrix giữ. Nếu columns dependent, một feature là linear combination của features khác và parameters có thể không unique.

Gaussian elimination giải systems bằng row operations, nhưng application nên dùng numerical library thay tự viết cho production. Core goal là đọc solution existence/uniqueness, không luyện determinant lớn bằng tay.

## 10. Span, basis và linear independence

Span của vectors là set mọi linear combinations của chúng. Basis là tập vectors linearly independent và span toàn space/subspace. Coordinates nói vector được ghép từ basis thế nào.

Vectors linearly dependent nếu một vector có thể tạo từ others. Redundant features không luôn gây lỗi nhưng có thể làm model khó xác định hoặc computation lãng phí.

Dimension là số basis vectors cần, không chỉ số columns đang lưu. Data 100 features có thể gần một subspace thấp chiều nếu features correlated; dimensionality reduction khai thác điều này nhưng cũng mất information.

## 11. Linear transformation và giới hạn

Transformation T linear nếu giữ addition và scalar multiplication:

```text
T(x+y)=T(x)+T(y)
T(cx)=cT(x)
```

Scale, rotation, reflection và projection qua origin là linear. Translation `x → x+b` không linear vì zero không map zero; nó là affine. Dùng homogeneous coordinates có thể biểu diễn affine transformations bằng matrix lớn hơn trong graphics.

Nhiều systems không linear. User satisfaction không tăng gấp đôi chỉ vì gấp đôi videos. Linear model có giá trị vì dễ hiểu/tính và local approximation, không vì thế giới luôn linear.

## 12. Eigenvector intuition và điểm dừng

Eigenvector v của A giữ direction khi transform: `Av = λv`; eigenvalue λ nói scale. Ý tưởng xuất hiện trong stability, PageRank, PCA và dynamics.

Core chỉ cần intuition. Tính eigen decomposition, complex eigenvalues và spectral theorem thuộc extension. Không dùng eigen terminology để làm content có vẻ nâng cao khi bài chỉ cần matrix multiplication hoặc graph traversal.

Khi library trả eigenvectors, sign/order có thể không unique và numerical errors tồn tại. Meaning phải nối lại domain.

## 13. Course similarity example

Giả sử features đã scale cùng khoảng và có definition:

```text
course_a = [theory=0.8, practice=0.6, difficulty=0.7]
course_b = [theory=0.7, practice=0.7, difficulty=0.6]
course_c = [theory=0.1, practice=0.9, difficulty=0.3]
```

Cosine có thể cho A gần B hơn C theo profile direction. Nhưng ba features do người thiết kế chọn; missing topic/language/duration không được score biết. Similarity không tự là recommendation: learner eligibility, goals và diversity vẫn cần rules.

Nếu vectors là learned embeddings, coordinates không dễ giải thích nhưng geometry vẫn phụ thuộc training objective/data. “Gần trong embedding space” phải luôn kèm tên model/version và use case.

## 14. Numerical computing realities

Floating-point không biểu diễn chính xác mọi real. `0.1 + 0.2` có thể không equal đúng `0.3`; so với tolerance phù hợp. Matrix computations lớn tích lũy error; condition number cho biết problem nhạy với input error.

Không tính inverse trực tiếp để giải `Ax=b` nếu library có solver; inverse vừa tốn vừa kém ổn định. Không tự implement linear algebra kernels cho production nếu không có lý do; dùng NumPy/BLAS/library đã kiểm chứng và test shapes/units.

Sparse matrix lưu chỉ non-zero entries, hữu ích adjacency hoặc high-dimensional data ít values. Dense allocation `n²` có thể không khả thi.

## 15. Final practice — integrated Learning Platform model

Tạo feature vectors cho 5 courses với 3–5 features có definition và scale. Tính norm, dot product và cosine cho hai pairs bằng tay hoặc code nhỏ. Giải thích result và ít nhất hai pieces of information representation bỏ qua.

Kết hợp với Module 7: prerequisite graph quyết định eligibility/order; vector similarity chỉ xếp hạng trong tập courses hợp lệ. Dùng completion data từ Module 6 để báo outcome cùng uncertainty, không biến historical correlation thành causal claim.

Viết một trang nêu từng model: logic bảo vệ constraints, graph giữ dependencies, statistics mô tả evidence, vector giữ numerical profile. Bài đạt khi các model không thay thế sai vai trò lẫn nhau và zero vector/scale/dimension được xử lý.

## One-minute summary

Vector là representation có ordered coordinates; norm/distance/dot product chỉ có meaning khi coordinates và scale có meaning. Matrix là linear transformation, multiplication là composition, basis nói hệ tọa độ và rank nói số directions độc lập được giữ. Linear algebra làm nhiều quantities có cấu trúc trở nên tính được, nhưng representation luôn bỏ bớt thế giới thật.

Hoàn thành module này là kết thúc core track. Hướng học tiếp nên theo nhu cầu: calculus/optimization cho AI, numerical methods cho scientific computing, geometry cho graphics, number theory cho security hoặc advanced discrete mathematics cho algorithms.
