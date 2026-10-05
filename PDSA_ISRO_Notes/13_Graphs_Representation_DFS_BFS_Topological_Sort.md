# 13. Graphs: Terminology, Representation, DFS, BFS, Topological Sort and SCC

> **Graphs model anything with connections**: roads between cities, links between web pages, dependencies between tasks, satellites relaying to ground stations. Two traversals (DFS and BFS) are the building blocks of almost every graph algorithm. Learn exactly how each one moves, and the rest follows.

---

## 1. Terminology

A **graph** G = (V, E): a set of **vertices** V and a set of **edges** E.

- **Undirected:** edge {u, v} goes both ways. **Directed (digraph):** edge (u, v) goes from u to v.
- **Weighted:** each edge has a cost.
- **Simple graph:** no self-loops, no parallel edges.
- **Degree** (undirected): number of edges at a vertex. Directed: **in-degree** and **out-degree**.
- **Path:** sequence of vertices joined by edges. **Simple path:** no repeated vertex. **Cycle:** a path that starts and ends at the same vertex.
- **Connected** (undirected): a path exists between every pair. **Strongly connected** (directed): a directed path in both directions between every pair. **Weakly connected:** connected if directions are ignored.
- **Tree:** a connected acyclic undirected graph. **Forest:** acyclic (possibly disconnected).
- **DAG:** directed acyclic graph.
- **Complete graph Kₙ:** every pair connected.
- **Bipartite graph:** vertices split into two sets with every edge going across. A graph is bipartite **iff it has no odd-length cycle**.
- **Sparse** (E ≈ V) vs **dense** (E ≈ V²).

### Counting facts (memorise)

| Fact | Value |
|---|---|
| **Handshaking lemma** | Σ degrees = **2E** (so the number of odd-degree vertices is even) |
| Directed graph | Σ in-degrees = Σ out-degrees = E |
| Max edges, simple undirected graph | **n(n − 1)/2** |
| Max edges, simple directed graph | **n(n − 1)** |
| Edges in a tree with n vertices | **n − 1** |
| Edges in a forest with n vertices and k trees | **n − k** |
| Minimum edges for a connected graph | **n − 1** |
| Spanning trees of Kₙ (Cayley's formula) | **n^(n − 2)** |
| Number of simple undirected graphs on n labelled vertices | 2^(n(n − 1)/2) |

Examples: K₅ has 10 edges and 5³ = 125 spanning trees. A graph with 10 vertices each of degree 3 has 15 edges.

---

## 2. Representations

### Adjacency matrix

V × V matrix; A[i][j] = 1 (or the weight) if there's an edge i → j.

```
Graph: 0-1, 0-2, 1-2, 2-3
     0 1 2 3
  0 [0 1 1 0]
  1 [1 0 1 0]
  2 [1 1 0 1]
  3 [0 0 1 0]
```

- Undirected graph → **symmetric** matrix.
- Degree of vertex i = sum of row i.

### Adjacency list

An array of lists; list[i] holds i's neighbours.

```
0: 1 → 2
1: 0 → 2
2: 0 → 1 → 3
3: 2
```

| | Adjacency matrix | Adjacency list |
|---|---|---|
| Space | **O(V²)** always | **O(V + E)** |
| Is (u, v) an edge? | **O(1)** | O(deg(u)) |
| List all neighbours of u | O(V) | **O(deg(u))** |
| BFS/DFS time | O(V²) | **O(V + E)** |
| Best for | Dense graphs, frequent edge checks | **Sparse graphs** (most real graphs) |

(Also: **incidence matrix**, V × E, and **edge list**, a list of (u, v, w) triples, handy for Kruskal.)

---

## 3. Depth-First Search (DFS)

**Go as deep as possible**, then backtrack. Uses a **stack** (explicitly or via recursion).

```c
void DFS(int v) {
    visited[v] = 1;
    printf("%d ", v);
    for each neighbour x of v (in order):
        if (!visited[x]) DFS(x);
}
```

A `visited` array is essential: graphs can have cycles.

### Example

Undirected edges: 1-2, 1-3, 2-4, 2-5, 3-6, 3-7. Start at 1; visit neighbours in increasing order.

```
        1
       / \
      2   3
     / \ / \
    4  5 6  7
```

DFS: 1 → 2 → 4 (dead end, back to 2) → 5 (back to 2, back to 1) → 3 → 6 → 7.
**DFS order: 1 2 4 5 3 6 7.**

### Discovery and finish times

Give each vertex a **discovery time** d[v] (when first visited) and **finish time** f[v] (when all its descendants are done). For the example (time starts at 1):

| Vertex | d | f |
|---|---|---|
| 1 | 1 | 14 |
| 2 | 2 | 7 |
| 4 | 3 | 4 |
| 5 | 5 | 6 |
| 3 | 8 | 13 |
| 6 | 9 | 10 |
| 7 | 11 | 12 |

**Parenthesis theorem:** intervals [d, f] of any two vertices are either nested or disjoint.

### Edge classification (directed DFS)

| Edge u → v | Meaning | Times |
|---|---|---|
| **Tree edge** | v discovered via this edge | |
| **Back edge** | v is an **ancestor** of u (still in progress) | d[v] < d[u] < f[u] < f[v] |
| **Forward edge** | v is a descendant (already finished), not a tree edge | d[u] < d[v] < f[v] < f[u] |
| **Cross edge** | v is in another branch, already finished | d[v] < f[v] < d[u] < f[u] |

> **A directed graph has a cycle iff DFS finds a back edge.**

In an **undirected** DFS there are only **tree** and **back** edges.

### Complexity

**O(V + E)** with adjacency lists; **O(V²)** with a matrix.

### Applications

Cycle detection, **topological sort**, connected components, **strongly connected components**, articulation points and bridges, maze solving, path existence, backtracking.

---

## 4. Breadth-First Search (BFS)

Explore **level by level**: all vertices at distance 1, then distance 2, ... Uses a **queue**.

```c
void BFS(int s) {
    visited[s] = 1; enqueue(s);
    while (queue not empty) {
        int u = dequeue();
        printf("%d ", u);
        for each neighbour x of u (in order):
            if (!visited[x]) { visited[x] = 1; enqueue(x); }
    }
}
```

Mark vertices visited **when enqueued** (not when dequeued), or they may be enqueued several times.

### Example (same graph)

Level 0: {1}; level 1: {2, 3}; level 2: {4, 5, 6, 7}.
**BFS order: 1 2 3 4 5 6 7.**

### Key property

**BFS finds shortest paths (fewest edges) in an unweighted graph.** The BFS tree's depth of each vertex is its distance from the source. DFS gives no such guarantee.

For **weighted** graphs, use Dijkstra or Bellman-Ford ([Chapter 20](20_Graph_Algorithms_MST_Shortest_Paths.md)).

### Complexity

**O(V + E)** with lists, O(V²) with a matrix. Extra space O(V) for the queue.

### Applications

Shortest paths in unweighted graphs, **bipartite testing** (2-colouring level by level; an edge within a level means an odd cycle), connected components, web crawlers, network broadcasting, finding all nodes within k hops, Ford-Fulkerson (Edmonds-Karp).

| | DFS | BFS |
|---|---|---|
| Data structure | **Stack** / recursion | **Queue** |
| Strategy | Deep first | Level by level |
| Shortest path (unweighted) | No | **Yes** |
| Memory | O(depth) | O(width) |
| Typical uses | Cycles, topological sort, SCC | Shortest paths, bipartite check |

---

## 5. Topological sort

A **topological order** of a DAG is a linear ordering of vertices such that for every edge **u → v**, **u comes before v**. Think: courses with prerequisites, build dependencies, task scheduling.

**Only DAGs** have topological orders. A cycle makes it impossible.

### Method 1: DFS finishing times

Run DFS; when a vertex **finishes**, push it on a stack (or prepend to a list). The final order is **decreasing finish time**.

### Method 2: Kahn's algorithm (BFS-based)

1. Compute in-degrees.
2. Put all vertices with **in-degree 0** in a queue.
3. Repeatedly remove a vertex, output it, and decrement its neighbours' in-degrees; enqueue any that drop to 0.
4. If fewer than V vertices are output, the graph **has a cycle**.

### Example

Edges: A → B, A → C, B → D, C → D, D → E.
- In-degrees: A 0, B 1, C 1, D 2, E 1.
- Remove A → B and C become 0. Remove B → D becomes 1. Remove C → D becomes 0. Remove D → E becomes 0. Remove E.
- One order: **A B C D E**. Another valid one: **A C B D E**. So **2** topological orders.

### Counting topological orders (small cases)

- Two independent chains a → b and c → d: interleave two pairs keeping each chain's order: C(4, 2) = **6**.
- 1 → 2, 1 → 3, 2 → 4, 3 → 4: **2** orders (1 2 3 4, 1 3 2 4).
- n vertices, no edges: **n!**.
- A single path: exactly **1**.

Complexity: **O(V + E)**.

---

## 6. Connected components and SCCs

### Undirected: connected components

Run BFS or DFS from each unvisited vertex; each run discovers one component. O(V + E).

### Directed: strongly connected components (SCC)

An SCC is a **maximal** set of vertices in which every vertex can reach every other. Collapsing each SCC to a single node always gives a **DAG** (the condensation graph).

**Kosaraju's algorithm:**
1. Run DFS on G, recording vertices in order of **finish time**.
2. Build the **transpose** Gᵀ (reverse every edge).
3. Run DFS on Gᵀ in **decreasing finish time** order; each tree found is one SCC.

**Tarjan's algorithm:** one DFS using discovery times and **low-link** values.

Both run in **O(V + E)**.

**Example:** edges 1 → 2, 2 → 3, 3 → 1, 3 → 4, 4 → 5, 5 → 4. SCCs: **{1, 2, 3}** and **{4, 5}**.

---

## 7. Other DFS applications (recall)

- **Articulation point (cut vertex):** removing it disconnects the graph.
- **Bridge (cut edge):** removing it disconnects the graph.
- Both found in O(V + E) with DFS low-link values.
- **Euler path/circuit** (every edge once): connected graph with 0 odd-degree vertices → Euler circuit; exactly 2 odd-degree vertices → Euler path.
- **Hamiltonian path/cycle** (every vertex once): NP-complete in general.

---

## 8. Exam traps

1. Σ degrees = 2E.
2. Max edges n(n − 1)/2 (undirected), n(n − 1) (directed).
3. Tree: n − 1 edges; forest with k trees: n − k.
4. Matrix O(V²) space; list O(V + E).
5. DFS uses a stack, BFS a queue; both O(V + E) with lists.
6. BFS gives shortest paths in **unweighted** graphs only.
7. Back edge ⟺ cycle (directed).
8. Topological sort only for DAGs; Kahn removes in-degree-0 vertices.
9. Topological order = decreasing DFS finish times.
10. Bipartite ⟺ no odd cycle.
11. Cayley: Kₙ has n^(n − 2) spanning trees.

---

## 9. Practice questions

**Q1.** A simple undirected graph has 8 vertices with degrees 3, 3, 3, 3, 2, 2, 2, 2. Number of edges?
(a) 10 (b) 20 (c) 8 (d) 12

**Answer: (a).** Sum = 20 = 2E.

---

**Q2.** Maximum number of edges in a simple undirected graph with 10 vertices?
(a) 45 (b) 90 (c) 100 (d) 50

**Answer: (a).**

---

**Q3.** Number of spanning trees of K₄?
(a) 4 (b) 8 (c) 16 (d) 64

**Answer: (c).** 4² = 16.

---

**Q4.** Undirected edges: A-B, A-C, B-D, C-D, D-E. DFS from A (alphabetical neighbour order) visits:
(a) A B C D E (b) A B D C E (c) A C D B E (d) A B D E C

**Answer: (b).** A → B → D → C (D's first unvisited neighbour alphabetically is C) → back to D → E.

---

**Q5.** Same graph, BFS from A (alphabetical)?
(a) A B C D E (b) A B D C E (c) A C B D E (d) A D B C E

**Answer: (a).** Level 1: B, C. Level 2: D. Level 3: E.

---

**Q6.** Which traversal finds the shortest path (fewest edges) in an unweighted graph?
(a) DFS (b) BFS (c) Inorder (d) Topological sort

**Answer: (b).**

---

**Q7.** How many topological orders does the DAG with edges 1 → 2, 1 → 3, 2 → 4, 3 → 4 have?
(a) 1 (b) 2 (c) 3 (d) 4

**Answer: (b).**

---

**Q8.** A DAG has 4 vertices and no edges. Number of topological orders?
(a) 1 (b) 4 (c) 16 (d) 24

**Answer: (d).** 4!.

---

**Q9.** Time complexity of BFS using an adjacency matrix?
(a) O(V + E) (b) O(V²) (c) O(E log V) (d) O(V log V)

**Answer: (b).**

---

**Q10.** In Kahn's algorithm, if the queue empties before all vertices are output, the graph:
(a) is disconnected (b) has a cycle (c) is a tree (d) is bipartite

**Answer: (b).**

---

**Q11.** A graph is bipartite if and only if it has no:
(a) cycle (b) odd-length cycle (c) even-length cycle (d) vertex of odd degree

**Answer: (b).**

---

**Q12.** A forest has 20 vertices and 4 trees. Number of edges?
(a) 16 (b) 19 (c) 24 (d) 20

**Answer: (a).** n − k.

---

**Q13.** During DFS on a directed graph, an edge u → v where v is an ancestor of u (still on the recursion stack) is a:
(a) tree edge (b) forward edge (c) back edge (d) cross edge

**Answer: (c).**

---

**Q14.** SCCs of the digraph with edges 1→2, 2→3, 3→1, 3→4, 4→5, 5→4, 5→6?
(a) {1,2,3}, {4,5}, {6} (b) {1,2,3,4,5,6} (c) {1,2,3}, {4,5,6} (d) six singletons

**Answer: (a).** 6 can't reach back to anything.

---

**Practice questions:** [2.13 Graphs DFS BFS Topological Sort](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.13_Graphs_DFS_BFS_Topological_Sort.md)
