# 7. Graphs and Trees — Mô hình hóa kết nối, dependency và đường đi

> **Thời lượng:** 70–85 phút  
> **Prerequisites:** [Module 6](06-statistics-and-data-reasoning.md)  
> **Status:** Reviewed

## Overview

Nhiều bài toán computing không xoay quanh một dãy values mà quanh connections: course phụ thuộc course khác, users theo dõi nhau, services gọi nhau, roads nối locations. Graph bỏ phần chi tiết không liên quan và giữ vertices cùng edges để ta hỏi path, reachability, cycle và order.

Graph mạnh vì representation nhỏ có thể mô tả nhiều domain. Cũng vì vậy, phải nói edge có hướng không, có weight không và meaning gì; vẽ vài nodes chưa thành model.

## 1. Vertex, edge và loại graph

Graph `G = (V, E)` gồm set vertices V và set edges E. Trong prerequisite graph, vertices là courses; directed edge `A → B` có thể nghĩa A là prerequisite trực tiếp của B. Direction phải được ghi rõ vì một số đội chọn chiều ngược.

Undirected edge phù hợp relation symmetric như connection hai chiều. Directed edge phù hợp follow, dependency hoặc call. Weighted edge có số như distance, latency hoặc cost. Weight meaning quyết định algorithm; negative cost khác physical distance.

Simple graph không có self-loop hoặc duplicate edge. Multigraph cho nhiều edges giữa cùng pair, hữu ích khi connections khác loại. Core examples dùng simple directed/undirected graph trừ khi nêu khác.

## 2. Degree và local structure

Trong undirected graph, degree của vertex là số incident edges. Tổng degrees bằng `2|E|` vì mỗi edge chạm hai endpoints. Đây là handshake lemma.

Trong directed graph, in-degree đếm edges đi vào; out-degree đếm edges đi ra. Với edge prerequisite → dependent course, in-degree của course là số direct prerequisites; out-degree là số courses trực tiếp phụ thuộc nó.

Degree cao không tự nghĩa “quan trọng”. Một prerequisite nền có out-degree cao; một spam account có degree cao; centrality cần definition theo câu hỏi.

## 3. Path, reachability và cycle

Path là sequence vertices nối bởi edges. Path length trong unweighted graph thường là số edges. Vertex B reachable từ A nếu tồn tại directed path A tới B.

Nếu A prerequisite B và B prerequisite C, A là prerequisite gián tiếp C. Reachability chính là transitive closure của direct prerequisite relation.

Cycle là path quay về điểm đầu (theo direction với directed graph). Prerequisite cycle `A → B → C → A` làm không course nào bắt đầu được. Dependency validation cần phát hiện cycle trước khi publish.

Self-loop A → A là cycle độ dài 1 và thường invalid trong prerequisite graph.

## 4. Connected components

Undirected graph connected nếu mọi pair có path. Connected components là maximal connected subgraphs. Trong social/network data, components tách có thể báo isolated communities hoặc data import thiếu.

Directed graph có weak connectivity khi bỏ hướng thì connected, và strong connectivity khi mọi vertex tới được mọi vertex khác trong component. Strongly connected component size > 1 trong dependency graph cho thấy nhóm cycle.

Không dùng từ “connected” mà bỏ loại graph/direction; câu hỏi reachability có thể đổi hoàn toàn.

## 5. Adjacency list và adjacency matrix

Adjacency list lưu mỗi vertex cùng neighbors. Space gần `O(|V| + |E|)`, phù hợp sparse graph như prerequisite. Kiểm tra mọi neighbors và traversal hiệu quả.

Adjacency matrix là ma trận `|V| × |V|`, entry cho biết edge có tồn tại/weight. Kiểm tra edge pair nhanh và matrix operations hữu ích, nhưng space `O(|V|²)` dù edges ít.

Edge list lưu pairs và đơn giản cho file/processing. Chọn representation theo operations và density, không theo thói quen. Database table `(from_id, to_id)` là edge list có indexes.

## 6. Breadth-first search

BFS khám phá theo số edges từ source: dùng queue, đánh dấu visited khi enqueue để tránh duplicate. Trong unweighted graph, lần đầu tới vertex cho shortest path length theo số edges.

```python
from collections import deque

def distances(graph: dict[str, list[str]], start: str) -> dict[str, int]:
    result = {start: 0}
    queue = deque([start])

    while queue:
        current = queue.popleft()
        for neighbor in graph.get(current, []):
            if neighbor not in result:
                result[neighbor] = result[current] + 1
                queue.append(neighbor)

    return result
```

`result` vừa là distances vừa là visited. Nếu cần reconstruct path, lưu predecessor. BFS time `O(V+E)` với adjacency list cho phần reachable; không phải `O(V²)` mặc định.

BFS không tìm shortest weighted path khi weights khác nhau. Edge 1 hop có cost 100 có thể tệ hơn 3 hops cost 3.

## 7. Depth-first search

DFS đi sâu một branch rồi quay lại, dùng recursion hoặc explicit stack. Nó phù hợp cycle detection, topological sort, connected components và exhaustive search.

Trong directed cycle detection, thường giữ ba states: unvisited, visiting (đang trong recursion stack), visited. Edge tới visiting vertex là back edge tạo cycle. Chỉ một Boolean visited không phân biệt edge tới node đã hoàn tất với node đang trên path.

Recursive DFS có thể vượt call stack trên graph sâu; explicit stack an toàn hơn khi input không giới hạn. Proof termination dựa visited set: mỗi vertex xử lý hữu hạn lần.

BFS và DFS đều traversal `O(V+E)` với adjacency list, nhưng order và properties khác; không có cái nào “nhanh hơn” cho mọi bài.

## 8. DAG và topological order

Directed Acyclic Graph (DAG) không có directed cycle. Prerequisite graph hợp lệ là DAG. Topological order sắp vertices sao cho mọi edge `u → v` đặt u trước v.

Kahn's algorithm bắt đầu từ vertices in-degree 0, lấy ra, giảm in-degree neighbors và tiếp tục. Nếu đã xử lý ít hơn `|V|` nhưng không còn in-degree 0, graph có cycle.

Topological order có thể không unique. Courses độc lập có thể đổi chỗ. Scheduler không nên gán meaning cho tie order nếu requirement không có.

DAG cũng mô hình build dependencies, workflow và version history. Nó không mô hình mọi distributed workflow nếu retry/loops là behavior thực sự cần.

## 9. Shortest path và weight assumptions

BFS dùng cho unweighted hoặc equal-weight edges. Dijkstra dùng non-negative weights. Negative edge làm greedy choice của Dijkstra không còn an toàn; Bellman–Ford xử lý negative weights và phát hiện negative cycle với cost cao hơn.

Trước khi chọn algorithm, hỏi weight là gì. “Ít prerequisite nhất” khác “tổng thời gian học ngắn nhất”. Nếu mỗi course có duration, path tối ưu theo duration cần weighted model. Nếu prerequisites là AND requirements, path đơn có thể không đủ vì phải hoàn thành toàn bộ incoming dependencies; đây là planning trên DAG, không standard shortest path.

Heuristic A* hữu ích khi có estimate không vượt remaining cost (admissible) cho route finding. Nó thuộc Algorithms extension; core cần hiểu assumptions quan trọng hơn tên algorithm.

## 10. Tree là graph có constraint mạnh

Undirected tree connected và acyclic; với n vertices có n−1 edges. Giữa mọi pair có đúng một simple path. Rooted tree chọn root, tạo parent/child, depth và subtree.

Hierarchy file system hoặc DOM thường là tree conceptually, nhưng symbolic links/shared references có thể biến thành graph. Organization chart có dotted-line relationships cũng không còn tree thuần.

Binary tree giới hạn tối đa hai children; binary search tree thêm order invariant. Heap thêm heap-order và shape constraint. “Tree” một mình chưa nói search complexity; degenerate BST có thể thành chain.

## 11. Tree traversal

Preorder xử lý node trước children, phù hợp serialize/copy structure. Postorder xử lý children trước node, phù hợp tính size hoặc delete dependencies. Inorder có meaning đặc biệt với binary tree, cho sorted order nếu là BST hợp lệ. Level-order là BFS theo depth.

Traversal correctness dùng structural induction: base empty; giả sử calls đúng trên subtrees; combine đúng tại root. Complexity thường `O(n)` nếu thăm mỗi node một lần, nhưng work tại node có thể làm tổng lớn hơn.

Caching subtree result có ích khi structure là DAG/shared nodes; trên true tree mỗi node chỉ có một parent nên traversal vốn không repeat.

## 12. Spanning tree và network intuition

Spanning tree nối mọi vertices của connected undirected graph bằng n−1 edges, bỏ cycles. Minimum spanning tree giảm tổng edge weight, phù hợp thiết kế network cable cost khi chỉ cần connectivity.

Nó không phải shortest-path tree cho mọi pair và không áp dụng trực tiếp directed prerequisite. Đây là ví dụ quan trọng: hai problems cùng từ “tree tối ưu” nhưng objective khác.

Core không triển khai Kruskal/Prim; hãy giữ distinction giữa connectivity cost tổng và route cost từ source.

## 13. Modeling pitfalls

Node identity khác label. Hai courses cùng title vẫn là hai vertices nếu IDs khác. Edge direction phải được document. Missing edge có thể nghĩa “không relation” hoặc “data chưa biết”; hai meanings khác.

Graph database không tự cần chỉ vì domain có graph. Relational edge table và traversal application/recursive query đủ cho graph nhỏ. Chọn specialized store khi traversal workload/scale chứng minh lợi ích.

Visualization lớn dễ thành hairball. Query subgraph theo câu hỏi thay vì vẽ toàn bộ.

## 14. Mini practice

Tạo prerequisite graph 8–12 courses với ít nhất hai paths và một course độc lập. Viết adjacency list, trace BFS và DFS từ course nền. Thêm một cycle, dùng three-state DFS hoặc Kahn reasoning để phát hiện rồi sửa.

Tạo topological order và tìm một order khác hợp lệ. Nếu muốn tối thiểu tổng thời gian tới target, giải thích vì sao prerequisites AND có thể làm shortest path thông thường chưa đủ.

Bài đạt khi direction/weight meaning rõ, visited ngăn lặp và algorithm được chọn đúng assumptions.

## Checkpoint

Bạn sẵn sàng sang Module 8 khi phân biệt directed/undirected, path/cycle/component, chọn adjacency list/matrix, trace BFS/DFS và giải thích DAG/topological order. Mental model cần giữ: graph giữ connections; algorithm đúng chỉ khi edge meaning và weight assumptions đúng.
