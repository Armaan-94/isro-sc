# 19. Dynamic Programming (LCS, Matrix Chain, Knapsack, Floyd-Warshall, Subset Sum)

> **DP in one sentence:** if a recursive solution keeps solving the **same subproblems again and again**, solve each one **once**, store the answer in a table, and reuse it. That single trick turns many exponential algorithms into polynomial ones. The exam skill is (1) recognising DP problems and (2) filling small DP tables correctly by hand.

---

## 1. The idea, through Fibonacci

```c
int fib(int n) { if (n <= 1) return n; return fib(n - 1) + fib(n - 2); }
```

fib(5) calls fib(3) twice, fib(2) three times, ... The call tree is exponential: **O(φⁿ)** ≈ O(1.618ⁿ).

But there are only **n + 1 distinct subproblems** (fib(0) to fib(n)). Store them:

**Top-down (memoisation):**
```c
int memo[100];   // initialised to -1
int fib(int n) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    return memo[n] = fib(n - 1) + fib(n - 2);
}
```

**Bottom-up (tabulation):**
```c
f[0] = 0; f[1] = 1;
for (i = 2; i <= n; i++) f[i] = f[i - 1] + f[i - 2];
```

Both are **O(n)**. (Space can even be O(1) by keeping only the last two values.)

---

## 2. When does DP apply?

Two properties:
1. **Optimal substructure:** an optimal solution is built from optimal solutions of subproblems.
2. **Overlapping subproblems:** the same subproblems recur many times.

| | Divide and conquer | Dynamic programming | Greedy |
|---|---|---|---|
| Subproblems | **Independent** (no overlap) | **Overlapping** | One choice per step |
| Reuses answers? | No need | **Yes** (table) | n/a |
| Examples | Merge sort, quick sort, binary search | LCS, matrix chain, 0/1 knapsack, Floyd-Warshall, Bellman-Ford | Huffman, Prim, Kruskal, Dijkstra |

> **Trap:** merge sort has optimal substructure but **no overlapping subproblems**, so memoisation gains nothing. That's why it's divide and conquer, not DP.

### Memoisation vs tabulation

| | Top-down (memoisation) | Bottom-up (tabulation) |
|---|---|---|
| Style | Recursive + cache | Iterative table filling |
| Computes | Only subproblems actually needed | All subproblems |
| Overhead | Function calls, recursion depth | None |
| Asymptotic time | Same | Same |

### Recipe

1. Define the **state** (what does table entry dp[i][j] mean?).
2. Write the **recurrence** (how does it depend on smaller states?).
3. Set **base cases**.
4. Fill the table in an order where dependencies are already computed.
5. (Optional) **trace back** to reconstruct the actual solution.

---

## 3. Longest Common Subsequence (LCS)

A **subsequence** keeps the order but may skip characters ("ACE" is a subsequence of "ABCDE"; a **substring** must be contiguous).

**LCS(X, Y):** the longest sequence that is a subsequence of both.

### Recurrence

Let c[i][j] = LCS length of X[1..i] and Y[1..j].

```
c[i][j] = 0                                 if i = 0 or j = 0
c[i][j] = c[i−1][j−1] + 1                   if X[i] = Y[j]
c[i][j] = max(c[i−1][j], c[i][j−1])         if X[i] ≠ Y[j]
```

Intuition: if the last characters match, they can end the LCS; otherwise drop the last character of one string or the other and take the better result.

### Worked table: X = ABCD, Y = ACBD

|   | ∅ | A | C | B | D |
|---|---|---|---|---|---|
| ∅ | 0 | 0 | 0 | 0 | 0 |
| A | 0 | **1** | 1 | 1 | 1 |
| B | 0 | 1 | 1 | **2** | 2 |
| C | 0 | 1 | **2** | 2 | 2 |
| D | 0 | 1 | 2 | 2 | **3** |

LCS length = **3** ("ABD" or "ACD").

Traceback: start at the bottom-right; on a match, go diagonally (that character is in the LCS); otherwise move toward the larger neighbour (up or left).

### Classic: X = ABCBDAB, Y = BDCABA

LCS length = **4** (e.g. "BCBA", "BDAB", "BCAB").

### Complexity

**O(mn)** time and space (space can be reduced to O(min(m, n)) if only the length is needed).

Number of subsequences of a string of length n: **2ⁿ** (so brute force is exponential).

---

## 4. Matrix Chain Multiplication (MCM)

### The problem

Multiplying a p × q matrix by a q × r matrix costs **p · q · r** scalar multiplications. Matrix multiplication is associative, so we can choose the **order** (parenthesisation). Different orders can cost wildly different amounts.

**Example:** A (10 × 20), B (20 × 30), C (30 × 40).
- (AB)C: 10·20·30 + 10·30·40 = 6000 + 12000 = **18,000**.
- A(BC): 20·30·40 + 10·20·40 = 24000 + 8000 = **32,000**.

### Recurrence

Matrix Aᵢ has dimensions **p[i−1] × p[i]**. Let m[i][j] = minimum cost to multiply Aᵢ...Aⱼ.

```
m[i][i] = 0
m[i][j] = min over i ≤ k < j of { m[i][k] + m[k+1][j] + p[i−1] · p[k] · p[j] }
```

k is where the **last** multiplication splits the chain.

### Worked example: p = [10, 100, 20, 5, 80] (M1 10×100, M2 100×20, M3 20×5, M4 5×80)

**Chain length 2:**
- m[1][2] = 10·100·20 = **20,000**
- m[2][3] = 100·20·5 = **10,000**
- m[3][4] = 20·5·80 = **8,000**

**Chain length 3:**
- m[1][3] = min(k=1: 0 + 10,000 + 10·100·5 = 15,000; k=2: 20,000 + 0 + 10·20·5 = 21,000) = **15,000**
- m[2][4] = min(k=2: 0 + 8,000 + 100·20·80 = 168,000; k=3: 10,000 + 0 + 100·5·80 = 50,000) = **50,000**

**Chain length 4:**
- k = 1: 0 + 50,000 + 10·100·80 = 130,000
- k = 2: 20,000 + 8,000 + 10·20·80 = 44,000
- k = 3: 15,000 + 0 + 10·5·80 = **19,000** ← minimum

**Answer: 19,000**, grouping **M1((M2 M3) M4)**. (The "obvious" groupings (M1M2)(M3M4) = 44,000 and ((M1M2)M3)M4 = 25,000 are much worse.)

### Classic CLRS chain

A1 30×35, A2 35×15, A3 15×5, A4 5×10, A5 10×20, A6 20×25: minimum **15,125**, parenthesisation ((A1(A2A3))((A4A5)A6)). (For just A1..A5 the minimum is 11,875.)

### Facts

- Time **O(n³)**, space **O(n²)**.
- Number of possible parenthesisations of n matrices = Catalan(n − 1): n = 3 → 2, **n = 4 → 5**, n = 5 → 14.

---

## 5. 0/1 knapsack

n items with weights wᵢ and values vᵢ; capacity W; each item taken **whole or not at all**.

### Recurrence

K[i][w] = best value using the first i items with capacity w.

```
K[0][w] = 0, K[i][0] = 0
K[i][w] = K[i−1][w]                                   if wᵢ > w
K[i][w] = max( K[i−1][w],  vᵢ + K[i−1][w − wᵢ] )      otherwise
```

(Either skip item i, or take it and use the remaining capacity for earlier items.)

### Worked example

W = 5. Items: 1 (w 2, v 3), 2 (w 3, v 4), 3 (w 4, v 5), 4 (w 5, v 6).

| i \ w | 0 | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 (2, 3) | 0 | 0 | 3 | 3 | 3 | 3 |
| 2 (3, 4) | 0 | 0 | 3 | 4 | 4 | **7** |
| 3 (4, 5) | 0 | 0 | 3 | 4 | 5 | 7 |
| 4 (5, 6) | 0 | 0 | 3 | 4 | 5 | **7** |

Answer **7** (items 1 and 2, weight 5).

Check a cell: K[2][5] = max(K[1][5] = 3, 4 + K[1][2] = 4 + 3) = 7.

### Complexity

**O(nW)** time and space. This is **pseudo-polynomial** (polynomial in the **value** of W, exponential in its number of bits). 0/1 knapsack is **NP-complete** in general.

---

## 6. Floyd-Warshall (all-pairs shortest paths)

dist[i][j] = shortest distance from i to j. Allow vertices 1..k as intermediates, increasing k:

```
for k = 1..n
  for i = 1..n
    for j = 1..n
      dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

- **O(n³)** time, O(n²) space.
- Handles **negative edges**, but **not negative cycles** (a negative value appearing on the diagonal dist[i][i] < 0 signals one).

### Worked example

Edges: 1 → 2 (5), 2 → 3 (2), 3 → 1 (1).

| Initial | 1 | 2 | 3 |
|---|---|---|---|
| 1 | 0 | 5 | ∞ |
| 2 | ∞ | 0 | 2 |
| 3 | 1 | ∞ | 0 |

**k = 1** (via 1): dist[3][2] = min(∞, 1 + 5) = **6**.
**k = 2** (via 2): dist[1][3] = min(∞, 5 + 2) = **7**.
**k = 3** (via 3): dist[2][1] = min(∞, 2 + 1) = **3**.

| Final | 1 | 2 | 3 |
|---|---|---|---|
| 1 | 0 | 5 | 7 |
| 2 | 3 | 0 | 2 |
| 3 | 1 | 6 | 0 |

---

## 7. Subset sum

Is there a subset of S summing to exactly T?

```
dp[i][s] = dp[i−1][s]  OR  dp[i−1][s − aᵢ]     (if aᵢ ≤ s)
dp[0][0] = true, dp[0][s > 0] = false
```

**O(n·T)** (pseudo-polynomial; NP-complete in general).

**Example:** S = {3, 34, 4, 12, 5, 2}, T = 9 → **yes** (4 + 5, or 3 + 4 + 2).

**Example:** S = {2, 3, 7, 8, 10}, T = 14 → **no**. Reachable sums near 14: 13 (3 + 10, or 2 + 3 + 8) and 15 (7 + 8, or 2 + 3 + 10), but nothing equals 14.

---

## 8. More classic DP problems (with recurrences)

| Problem | Recurrence / idea | Time |
|---|---|---|
| **Coin change (number of ways)** | ways[a] += ways[a − c] for each coin c (outer loop over coins) | O(n·A) |
| **Coin change (min coins)** | dp[a] = min(dp[a − c] + 1) | O(n·A) |
| **Edit distance** | d[i][j] = d[i−1][j−1] if equal, else 1 + min(insert, delete, replace) | O(mn) |
| **Longest increasing subsequence** | L[i] = 1 + max L[j] for j < i with a[j] < a[i] | O(n²), or O(n log n) |
| **Rod cutting** | r[n] = max(pᵢ + r[n − i]) | O(n²) |
| **Optimal BST** | like MCM over key ranges | O(n³) |
| **Bellman-Ford** | relax all edges V − 1 times (DP over number of edges) | O(VE) |
| **Travelling salesman (Held-Karp)** | over subsets | O(n² 2ⁿ) |

Examples:
- Coins {1, 2, 5}, amount 5: **4 ways** ({5}, {2, 2, 1}, {2, 1, 1, 1}, {1, 1, 1, 1, 1}).
- Edit distance "kitten" → "sitting": **3** (k→s, e→i, insert g).
- LIS of [10, 9, 2, 5, 3, 7, 101, 18]: **4** (e.g. 2, 3, 7, 18).

---

## 9. Exam traps

1. DP needs **overlapping subproblems**; divide and conquer has independent ones.
2. Memoisation and tabulation have the same asymptotic time.
3. LCS: match → diagonal + 1; mismatch → max(up, left). O(mn).
4. MCM cost of (p × q)(q × r) = pqr. Recurrence over split point k. O(n³). Check **all** splits.
5. 0/1 knapsack is DP O(nW); fractional is greedy.
6. Floyd-Warshall: O(n³); negative edges OK, negative cycles not.
7. Subset sum/knapsack are pseudo-polynomial and NP-complete.
8. Prim's is greedy, not DP; Bellman-Ford and Floyd-Warshall are DP.

---

## 10. Practice questions

**Q1.** Which property distinguishes DP from divide and conquer?
(a) Recursion (b) Overlapping subproblems (c) Sorting (d) Graphs

**Answer: (b).**

---

**Q2.** LCS length of "ABCBDAB" and "BDCABA"?
(a) 3 (b) 4 (c) 5 (d) 6

**Answer: (b).**

---

**Q3.** LCS length of "AGGTAB" and "GXTXAYB"?
(a) 3 (b) 4 (c) 5 (d) 2

**Answer: (b).** "GTAB".

---

**Q4.** Minimum scalar multiplications for M1 (10×100), M2 (100×20), M3 (20×5), M4 (5×80)?
(a) 44,000 (b) 25,000 (c) 19,000 (d) 15,000

**Answer: (c).**

---

**Q5.** A (5×10), B (10×3), C (3×12). Minimum cost to compute ABC?
(a) 330 (b) 510 (c) 180 (d) 360

**Answer: (a).** (AB)C: 5·10·3 + 5·3·12 = 150 + 180 = 330. A(BC): 10·3·12 + 5·10·12 = 360 + 600 = 960.

---

**Q6.** Number of ways to parenthesise a product of 5 matrices?
(a) 5 (b) 14 (c) 42 (d) 10

**Answer: (b).** Catalan(4).

---

**Q7.** Time complexity of the MCM DP for n matrices?
(a) O(n²) (b) O(n³) (c) O(2ⁿ) (d) O(n log n)

**Answer: (b).**

---

**Q8.** 0/1 knapsack, W = 5, items (w, v): (2, 3), (3, 4), (4, 5), (5, 6). Maximum value?
(a) 6 (b) 7 (c) 8 (d) 9

**Answer: (b).**

---

**Q9.** Floyd-Warshall fails to give meaningful answers when the graph has:
(a) negative edges (b) a negative cycle (c) self-loops of weight 0 (d) disconnected vertices

**Answer: (b).**

---

**Q10.** Does {2, 3, 7, 8, 10} have a subset summing to 14?
(a) Yes, 7 + 7 (b) Yes, 2 + 3 + 9 (c) No (d) Cannot be determined

**Answer: (c).**

---

**Q11.** Number of ways to make 4 using coins {1, 2, 3} (order doesn't matter)?
(a) 3 (b) 4 (c) 5 (d) 7

**Answer: (b).** {1,1,1,1}, {1,1,2}, {2,2}, {1,3}.

---

**Q12.** Which is NOT a DP algorithm?
(a) Bellman-Ford (b) Floyd-Warshall (c) Prim's MST (d) LCS

**Answer: (c).**

---

**Q13.** The 0/1 knapsack DP running time O(nW) is called:
(a) polynomial (b) pseudo-polynomial (c) exponential in n (d) logarithmic

**Answer: (b).**

---

**Q14.** Naive recursive Fibonacci runs in O(φⁿ). With memoisation it becomes:
(a) O(log n) (b) O(n) (c) O(n²) (d) O(2ⁿ)

**Answer: (b).**

---

**Practice questions:** [2.19 Dynamic Programming](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.19_Dynamic_Programming.md)
