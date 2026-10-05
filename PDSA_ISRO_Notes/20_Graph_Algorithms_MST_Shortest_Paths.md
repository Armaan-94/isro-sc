# 20. Graph Algorithms: Minimum Spanning Trees and Shortest Paths

> **Two different questions about weighted graphs.** (1) "What's the cheapest way to **connect everything**?" → Minimum Spanning Tree (Kruskal, Prim). (2) "What's the cheapest way to **get from here to there**?" → Shortest paths (Dijkstra, Bellman-Ford, Floyd-Warshall). Confusing the two is a common mistake: an MST does **not** contain shortest paths in general.

---

## Part A: Minimum Spanning Trees

### 1. Definitions

- A **spanning tree** of a connected undirected graph includes **all V vertices**, is **connected**, and has **no cycles**. It always has exactly **V − 1 edges**.
- A **minimum spanning tree (MST)** has the smallest total edge weight among all spanning trees.
- A disconnected graph has a **minimum spanning forest** instead.
- If all edge weights are **distinct**, the MST is **unique**. With ties, several MSTs may exist, but their **total weight is always the same**.

### 2. Two key properties

- **Cut property:** for any cut (split of vertices into two groups), the **lightest edge crossing the cut** belongs to some MST (to every MST if it's the unique lightest).
- **Cycle property:** the **heaviest edge on any cycle** (if unique) is **not** in any MST.

Consequences:
- The **globally lightest edge** is always in the MST (if unique).
- The heaviest edge of the whole graph is in the MST **only** if it's a bridge (the only connection to some part).

### 3. Kruskal's algorithm (edge by edge)

1. Sort all edges by weight, ascending.
2. For each edge (u, v): if u and v are in **different components**, add the edge (and merge the components). Otherwise skip it (it would form a cycle).
3. Stop after V − 1 edges.

Cycle checks use **Union-Find (disjoint sets)** with path compression and union by rank.

Time: **O(E log E)** = O(E log V), dominated by sorting. Good for **sparse** graphs.

Kruskal grows a **forest** that merges into one tree at the end.

### 4. Prim's algorithm (vertex by vertex)

1. Start from any vertex.
2. Repeatedly add the **cheapest edge** connecting the tree to a vertex **outside** it.
3. Stop when all vertices are in.

Time: **O(E log V)** with a binary heap; **O(V²)** with a simple array (better for **dense** graphs); O(E + V log V) with a Fibonacci heap.

Prim always keeps **one connected tree**.

### 5. Worked example

Vertices A, B, C, D, E. Edges: A-B 2, A-C 3, B-C 1, B-D 4, C-D 5, C-E 6, D-E 2.

**Kruskal:** sorted: BC 1, AB 2, DE 2, AC 3, BD 4, CD 5, CE 6.

| Edge | Action | Components |
|---|---|---|
| BC (1) | add | {B, C} {A} {D} {E} |
| AB (2) | add | {A, B, C} {D} {E} |
| DE (2) | add | {A, B, C} {D, E} |
| AC (3) | **skip** (cycle) | |
| BD (4) | add | {A, B, C, D, E} → done (4 edges) |

Total = 1 + 2 + 2 + 4 = **9**.

**Prim from A:**

| Step | Tree | Cheapest crossing edge | Add |
|---|---|---|---|
| 1 | {A} | A-B 2, A-C 3 | B (2) |
| 2 | {A, B} | B-C 1, A-C 3, B-D 4 | C (1) |
| 3 | {A, B, C} | B-D 4, C-D 5, C-E 6 | D (4) |
| 4 | {A, B, C, D} | D-E 2, C-E 6 | E (2) |

Total = 2 + 1 + 4 + 2 = **9** ✓ (same MST here).

The **order** in which edges are added differs: Kruskal: BC, AB, DE, BD. Prim (from A): AB, BC, BD, DE. Exam questions often ask "which sequence could Prim's/Kruskal's produce?". For Prim's, every new edge must touch the current tree; for Kruskal's, edges must appear in non-decreasing weight order.

| | Kruskal | Prim |
|---|---|---|
| Grows | A forest (edges anywhere) | One tree from a start vertex |
| Picks | Globally lightest safe edge | Lightest edge leaving the tree |
| Needs | Sorting + Union-Find | Priority queue |
| Time | O(E log E) | O(E log V) heap / O(V²) array |
| Best for | Sparse graphs | Dense graphs (array version) |
| Negative weights? | Fine | Fine |

(Borůvka's algorithm is a third MST algorithm, good for parallel computation.)

---

## Part B: Shortest paths

### 6. Single-source shortest paths: relaxation

Both Dijkstra and Bellman-Ford use **relaxation**:

```
relax(u, v, w):
    if dist[v] > dist[u] + w:
        dist[v] = dist[u] + w
        parent[v] = u
```

"Can I reach v more cheaply by going through u?"

### 7. Dijkstra's algorithm

1. dist[source] = 0; all others ∞.
2. Repeatedly pick the **unvisited vertex with the smallest dist**, mark it **final**, and relax all its outgoing edges.

Time: **O((V + E) log V)** with a binary heap; O(V²) with an array.

**Requirement: no negative edge weights.** It's a **greedy** algorithm: once a vertex is finalised, it assumes no later path can be cheaper. A negative edge can break that.

### Worked example (same graph, undirected, from A)

| Step | Visit (final) | Updates |
|---|---|---|
| 1 | A (0) | B = 2, C = 3 |
| 2 | B (2) | C via B = 3 (no improvement), D = 2 + 4 = 6 |
| 3 | C (3) | D via C = 8 (no), E = 3 + 6 = 9 |
| 4 | D (6) | E = min(9, 6 + 2) = **8** |
| 5 | E (8) | |

Final: A 0, B 2, C 3, D 6, **E 8** (path A → B → D → E).

Note: the shortest path to E (cost 8) uses edges A-B, B-D, D-E, while the MST happened to contain those too. In general **MST ≠ shortest-path tree**.

### Why Dijkstra fails with negative edges

Edges: S → A (2), S → B (4), B → A (−3).
- Dijkstra finalises A with dist 2 (smallest), then B with 4, then relaxes B → A: 4 − 3 = 1 < 2, but A was already final. Dijkstra reports **2**; the truth is **1**.

### 8. Bellman-Ford

1. dist[source] = 0; others ∞.
2. Repeat **V − 1 times**: relax **every** edge.
3. One more pass: if any edge can still be relaxed, there's a **negative-weight cycle** reachable from the source.

Why V − 1 passes? A shortest simple path has at most V − 1 edges; after pass i, all shortest paths with at most i edges are correct.

Time: **O(V·E)**. Works with **negative edges**; **detects negative cycles**. It's DP-flavoured (not greedy).

Example (S → A 2, S → B 4, B → A −3):
- Pass 1 (edge order S→A, S→B, B→A): A = 2, B = 4, then A = min(2, 4 − 3) = **1**.
- Pass 2: no change. Correct answer A = 1, B = 4.

### 9. Shortest paths in a DAG

Relax edges in **topological order**: **O(V + E)**, works with negative edges (no cycles possible). Also gives **longest** paths in a DAG (negate weights), used in critical-path (PERT) analysis.

### 10. All-pairs shortest paths

- **Floyd-Warshall:** O(V³), DP ([Chapter 19](19_Dynamic_Programming.md)). Negative edges OK, no negative cycles.
- Run Dijkstra from every vertex: O(V (V + E) log V) for non-negative weights; better for sparse graphs.
- **Johnson's algorithm:** reweights edges using Bellman-Ford, then runs Dijkstra from each vertex: O(VE log V); handles negative edges.

### 11. Unweighted graphs

Use **BFS**: O(V + E) ([Chapter 13](13_Graphs_Representation_DFS_BFS_Topological_Sort.md)).

---

## 12. Comparison table

| Algorithm | Problem | Negative edges? | Negative cycle detection? | Paradigm | Time |
|---|---|---|---|---|---|
| Kruskal | MST | Yes | n/a | Greedy | O(E log E) |
| Prim | MST | Yes | n/a | Greedy | O(E log V) / O(V²) |
| BFS | SSSP, unweighted | n/a | n/a | | O(V + E) |
| **Dijkstra** | SSSP | **No** | No | **Greedy** | O(E log V) |
| **Bellman-Ford** | SSSP | **Yes** | **Yes** | DP | **O(VE)** |
| DAG relaxation | SSSP in DAG | Yes | (no cycles) | | O(V + E) |
| **Floyd-Warshall** | All pairs | Yes | Yes (diagonal < 0) | DP | **O(V³)** |

---

## 13. Classic true/false traps

1. "With all distinct edge weights, the MST is unique." **True.**
2. "With all distinct edge weights, the shortest path between two vertices is unique." **False in general**: two different paths can still have equal sums (e.g. 1 + 4 = 2 + 3).
3. "Doubling every edge weight keeps the same shortest paths (only costs double)." **True.**
4. "Adding the same constant to every edge keeps the same shortest paths." **False.** Paths with more edges are penalised more. Example: direct edge A→B = 10 vs a 3-edge path of weight 3 + 3 + 3 = 9. Add 1 to each edge: direct = 11, 3-edge path = 12. The shortest path flips.
5. "Adding the same constant to every edge keeps the same MST." **True** (every spanning tree has exactly V − 1 edges, so all totals shift equally).
6. "The MST contains the shortest path between every pair of vertices." **False.**
7. "Dijkstra and Bellman-Ford with non-negative weights may output different paths but the same distances." **True** (ties).
8. "Which is not greedy: Dijkstra, Prim, Kruskal, Huffman, Bellman-Ford?" **Bellman-Ford.**

---

## 14. Exam traps

1. MST has V − 1 edges; distinct weights → unique MST.
2. Kruskal: sort + union-find; O(E log E); sparse graphs.
3. Prim: grows one tree; O(V²) array for dense, O(E log V) heap.
4. Dijkstra fails with negative edges; Bellman-Ford handles them and detects negative cycles.
5. Bellman-Ford: V − 1 passes, O(VE).
6. Adding a constant to all edges preserves the MST but not necessarily shortest paths.
7. Floyd-Warshall O(V³) all pairs.

---

## 15. Practice questions

**Q1.** A connected graph has 12 vertices. Edges in its MST?
(a) 12 (b) 11 (c) 13 (d) depends

**Answer: (b).**

---

**Q2.** MST weight of the example graph (A-B 2, A-C 3, B-C 1, B-D 4, C-D 5, C-E 6, D-E 2)?
(a) 7 (b) 9 (c) 11 (d) 13

**Answer: (b).**

---

**Q3.** Shortest distance from A to E in the same graph?
(a) 6 (b) 8 (c) 9 (d) 11

**Answer: (b).**

---

**Q4.** Graph: edges P-Q 1, Q-R 2, P-R 3, R-S 4, Q-S 5. MST weight?
(a) 7 (b) 8 (c) 10 (d) 6

**Answer: (a).** Take P-Q 1, Q-R 2, skip P-R 3 (cycle), take R-S 4. Total 7.

---

**Q5.** Which algorithm detects negative-weight cycles?
(a) Dijkstra (b) Prim (c) Kruskal (d) Bellman-Ford

**Answer: (d).**

---

**Q6.** Time complexity of Bellman-Ford?
(a) O(V + E) (b) O(E log V) (c) O(VE) (d) O(V³)

**Answer: (c).**

---

**Q7.** For a dense graph (E ≈ V²), which MST implementation is asymptotically best?
(a) Kruskal O(E log E) (b) Prim with an array O(V²) (c) Prim with a binary heap O(E log V) (d) All equal

**Answer: (b).** O(V²) vs O(V² log V) for the others.

---

**Q8.** Every edge weight is increased by 5. Which is guaranteed to stay the same?
(a) Shortest paths (b) The MST (c) Both (d) Neither

**Answer: (b).**

---

**Q9.** Dijkstra on S → A (2), S → B (4), B → A (−3) reports dist(A) as:
(a) 1 (b) 2 (c) −3 (d) 4

**Answer: (b).** Wrong answer (true value 1), illustrating Dijkstra's failure with negative edges.

---

**Q10.** Which is NOT a greedy algorithm?
(a) Prim (b) Kruskal (c) Dijkstra (d) Bellman-Ford

**Answer: (d).**

---

**Q11.** In Kruskal's algorithm, cycle detection is done efficiently using:
(a) a stack (b) a heap (c) disjoint-set (union-find) (d) BFS

**Answer: (c).**

---

**Q12.** Which statement is TRUE?
(a) The heaviest edge of a graph is never in the MST (b) The lightest edge (unique) is always in the MST (c) An MST always contains the shortest path between any two vertices (d) Prim's and Kruskal's may give MSTs of different total weight

**Answer: (b).** (a) is false if the heaviest edge is a bridge.

---

**Q13.** Number of passes over all edges that Bellman-Ford makes (excluding the detection pass) on a graph with 10 vertices?
(a) 10 (b) 9 (c) 11 (d) depends on E

**Answer: (b).**

---

**Q14.** Shortest paths in a weighted DAG can be found in:
(a) O(V + E) (b) O(VE) (c) O(V³) (d) O(E log V)

**Answer: (a).** Relax in topological order.

---

**Practice questions:** [2.20 MST and Shortest Paths](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.20_MST_and_Shortest_Paths.md)
