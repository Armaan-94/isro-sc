# 15. Algorithm Analysis: Asymptotic Notation, Loop Analysis and Recurrences

> **Why this matters so much.** Almost every algorithms question boils down to "how does the running time grow with n?" You need three skills: (1) reading loops and counting iterations, (2) writing a recurrence for recursive code, and (3) solving it (Master theorem, recursion tree, substitution). This chapter drills all three.

---

## 1. Ways to analyse an algorithm

| | Experimental (a posteriori) | Asymptotic (a priori) |
|---|---|---|
| When | After implementing and running | Before, on paper |
| Measures | Actual seconds/bytes | Growth rate as n → ∞ |
| Depends on hardware/compiler? | Yes | **No** |

### Best, average, worst case

- **Worst case:** the maximum time over all inputs of size n. A **guarantee**. Most commonly reported.
- **Average case:** expected time over a probability distribution of inputs (needs a model of "typical" input).
- **Best case:** the minimum time. Usually not very informative.

Example, linear search: best O(1) (first element), worst O(n), average about n/2 → O(n).

---

## 2. Asymptotic notations

We care about **growth for large n**, ignoring constant factors and lower-order terms.

| Notation | Meaning | Analogy | Definition |
|---|---|---|---|
| **O(g)** | Upper bound | f ≤ g | 0 ≤ f(n) ≤ c·g(n) for all n ≥ n₀ |
| **Ω(g)** | Lower bound | f ≥ g | 0 ≤ c·g(n) ≤ f(n) for all n ≥ n₀ |
| **Θ(g)** | Tight bound | f = g | c₁·g(n) ≤ f(n) ≤ c₂·g(n) for all n ≥ n₀ |
| **o(g)** | Strictly smaller | f < g | for **every** c > 0, f(n) < c·g(n) eventually; equivalently f/g → 0 |
| **ω(g)** | Strictly larger | f > g | f/g → ∞ |

**f = Θ(g) ⟺ f = O(g) and f = Ω(g).**

Examples:
- 3n² + 5n + 7 = **Θ(n²)**. Also O(n³) (a loose upper bound) and Ω(n).
- n = o(n²), but n² ≠ o(n²).
- log n = o(n^ε) for any ε > 0 (logs grow slower than any power).

### Properties

- **Reflexive:** f = O(f), Ω(f), Θ(f). (Not o or ω.)
- **Transitive:** all five.
- **Symmetric:** only Θ: f = Θ(g) ⟺ g = Θ(f).
- **Transpose symmetry:** f = O(g) ⟺ g = Ω(f); f = o(g) ⟺ g = ω(f).
- **Sum:** O(f) + O(g) = O(max(f, g)). **Product:** O(f) · O(g) = O(f · g).

### The growth ladder (slowest to fastest)

```
1 < log log n < log n < (log n)^k < √n < n < n log n < n² < n³ < 2ⁿ < 3ⁿ < n! < nⁿ
```

### Comparing tricky functions: take logs

- **n^(log n) vs 2ⁿ:** log of each: (log n)² vs n. Since n grows faster, **2ⁿ is bigger**.
- **n! vs nⁿ:** n! = n·(n−1)·...·1 ≤ nⁿ, so n! = O(nⁿ). And **log(n!) = Θ(n log n)** (Stirling).
- **2^(n+1) vs 2ⁿ:** 2^(n+1) = 2·2ⁿ → **Θ(2ⁿ)**.
- **2^(2n) vs 2ⁿ:** 2^(2n) = 4ⁿ; 4ⁿ/2ⁿ = 2ⁿ → ∞ → **not O(2ⁿ)**.
- **log₂ n vs log₁₀ n:** differ by a constant factor → same Θ. (Base of a log doesn't matter asymptotically.)
- **(log n)^100 vs n^0.01:** any power of n eventually beats any power of log n → n^0.01 is bigger.
- **n^(1/log n):** = 2^(log n / log n) = 2 → **Θ(1)**.

---

## 3. Analysing loops: recognition patterns

| Loop | Iterations | Complexity |
|---|---|---|
| `for (i = 1; i <= n; i++)` | n | **O(n)** |
| `for (i = 1; i <= n; i += c)` | n/c | **O(n)** |
| `for (i = 1; i <= n; i *= 2)` | log₂ n | **O(log n)** |
| `for (i = n; i >= 1; i /= 2)` | log₂ n | **O(log n)** |
| `for (i = 2; i <= n; i = i * i)` | log log n | **O(log log n)** |
| `for (i = n; i > 2; i = sqrt(i))` | log log n | **O(log log n)** |
| `for (i = 1; i * i <= n; i++)` | √n | **O(√n)** |
| `for (i = 1; i < n; i += n/2)` | about 2 | **O(1)** |
| `for (i = 1; s <= n; i++) s += i;` | about √(2n) | **O(√n)** |

Why log log n for squaring: i goes 2, 4, 16, 256, 2^16, ... i = 2^(2^k). It reaches n when 2^k = log n, i.e. k = log log n.

Why √n for `s += i`: after k steps s = k(k + 1)/2 ≈ k²/2 ≤ n → k ≈ √(2n).

### Nested loops

**Independent nested loops → multiply.**
```c
for (i = 1; i <= n; i++)
    for (j = 1; j <= n; j *= 2)   // log n
        ...                        // total O(n log n)
```

**Dependent nested loops → sum.**

```c
for (i = 1; i <= n; i++)
    for (j = 1; j <= i; j++) ...
```
1 + 2 + ... + n = n(n + 1)/2 → **O(n²)**.

```c
for (i = n; i > 0; i /= 2)
    for (j = 0; j < i; j++) ...
```
n + n/2 + n/4 + ... ≤ 2n → **O(n)** (not n log n!).

```c
for (i = 1; i <= n; i *= 2)
    for (j = 1; j <= i; j++) ...
```
1 + 2 + 4 + ... + n ≤ 2n → **O(n)**.

```c
for (i = 1; i <= n; i++)
    for (j = 1; j <= n; j += i) ...
```
n/1 + n/2 + n/3 + ... + n/n = n·Hₙ → **O(n log n)** (harmonic series).

```c
for (i = 1; i <= n; i++)
    for (j = 1; j <= i * i; j++) ...
```
Σ i² = n(n + 1)(2n + 1)/6 → **O(n³)**.

---

## 4. Writing recurrences for recursive code

Count: **how many recursive calls**, on **what size**, and **how much non-recursive work** per call.

| Code | Recurrence | Solution | Stack space |
|---|---|---|---|
| `return f(n−1) + n;` | T(n) = T(n−1) + 1 | **O(n)** | O(n) |
| `return 2 * f(n−1) + n;` | **one** call: T(n) = T(n−1) + 1 | **O(n)** | O(n) |
| `return f(n−1) + f(n−1) + n;` | T(n) = 2T(n−1) + 1 | **O(2ⁿ)** | O(n) |
| `loop 1..n; f(n−1);` | T(n) = T(n−1) + n | **O(n²)** | O(n) |
| `return 2 * f(n/2) + n;` | T(n) = T(n/2) + 1 | **O(log n)** | O(log n) |
| `f(n/2); loop 1..n;` | T(n) = T(n/2) + n | **O(n)** | O(log n) |
| `f(n/2); f(n/2); loop 1..n;` | T(n) = 2T(n/2) + n | **O(n log n)** | O(log n) |
| `f(n/3) + f(n/3) + f(n/3) + n` | T(n) = 3T(n/3) + 1 | **O(n)** | O(log n) |

> **Trap 1:** `2 * f(n − 1)` is **one** call (its result doubled), not two.
> **Trap 2:** recursion **space** = maximum **depth** of the call stack, not the total number of calls. Even O(2ⁿ) time can need only O(n) space.

---

## 5. Solving recurrences

### 5.1 Substitution / back-substitution (unrolling)

**T(n) = T(n − 1) + n**, T(1) = 1:
T(n) = n + (n − 1) + ... + 2 + 1 = n(n + 1)/2 → **Θ(n²)**.

**T(n) = 2T(n − 1) + 1**, T(1) = 1:
T(n) = 2^(n−1) T(1) + (2^(n−1) − 1) = 2ⁿ − 1 → **Θ(2ⁿ)**.

**T(n) = T(n − 1) + log n:** = log 1 + log 2 + ... + log n = log(n!) → **Θ(n log n)**.

**T(n) = T(n/2) + 1:** halve until 1: log₂ n steps → **Θ(log n)**.

**T(n) = 2T(n/2) + 1:** 1 + 2 + 4 + ... + n ≈ 2n → **Θ(n)**.

### 5.2 Recursion tree

Draw the tree of calls, compute the work at each level, add up.

**T(n) = 2T(n/2) + n:** level i has 2ⁱ nodes, each doing n/2ⁱ → **n per level**; log n levels → **Θ(n log n)**.

**T(n) = T(n/3) + T(2n/3) + n:** each level does at most n; the longest path has log_{3/2} n levels → **Θ(n log n)**.

**T(n) = T(n/4) + T(n/2) + n²:** level work n², (1/16 + 1/4) n² = (5/16) n², ... geometric decreasing → **Θ(n²)**.

### 5.3 Master theorem

For **T(n) = a·T(n/b) + f(n)** with a ≥ 1, b > 1, compare f(n) with **n^p**, where **p = log_b a**.

| Case | Condition | Result |
|---|---|---|
| **1** | f(n) = O(n^(p − ε)) (f grows **slower** than n^p by a polynomial factor) | **Θ(n^p)** |
| **2** | f(n) = Θ(n^p) | **Θ(n^p log n)** |
| **2 (extended)** | f(n) = Θ(n^p logᵏ n), k ≥ 0 | **Θ(n^p log^(k+1) n)** |
| **3** | f(n) = Ω(n^(p + ε)) and a·f(n/b) ≤ c·f(n) for some c < 1 | **Θ(f(n))** |

Intuition: n^p is the total work of the leaves. Whichever of "leaves" (n^p) or "top-level work" (f(n)) is bigger wins; if they tie, every level contributes equally and you get an extra log n.

### Worked table

| Recurrence | a, b | p | Case | Answer |
|---|---|---|---|---|
| T(n) = 4T(n/2) + n | 4, 2 | 2 | 1 | **Θ(n²)** |
| T(n) = 9T(n/3) + n | 9, 3 | 2 | 1 | **Θ(n²)** |
| T(n) = 8T(n/2) + n² | 8, 2 | 3 | 1 | **Θ(n³)** |
| T(n) = 7T(n/2) + n² (Strassen) | 7, 2 | 2.807 | 1 | **Θ(n^2.807)** |
| T(n) = 2T(n/2) + n (merge sort) | 2, 2 | 1 | 2 | **Θ(n log n)** |
| T(n) = T(n/2) + 1 (binary search) | 1, 2 | 0 | 2 | **Θ(log n)** |
| T(n) = T(2n/3) + 1 | 1, 3/2 | 0 | 2 | **Θ(log n)** |
| T(n) = 4T(n/2) + n² | 4, 2 | 2 | 2 | **Θ(n² log n)** |
| T(n) = 2T(n/2) + n log n | 2, 2 | 1 | 2 (ext.) | **Θ(n log² n)** |
| T(n) = T(n/3) + n | 1, 3 | 0 | 3 | **Θ(n)** |
| T(n) = 2T(n/2) + n² | 2, 2 | 1 | 3 | **Θ(n²)** |
| T(n) = 3T(n/4) + n log n | 3, 4 | 0.79 | 3 | **Θ(n log n)** |
| T(n) = 3T(n/2) + n | 3, 2 | 1.585 | 1 | **Θ(n^1.585)** (Karatsuba) |

**When the Master theorem does NOT apply:**
- a < 1, or b ≤ 1, or the subproblems aren't of the form n/b (e.g. T(n − 1)).
- f(n) is between cases without a polynomial gap, e.g. **T(n) = 2T(n/2) + n/log n** (f is smaller than n but only by a log factor: not case 1). Answer: Θ(n log log n) by other methods.
- Non-polynomial f like 2ⁿ (case 3 with regularity works: Θ(2ⁿ)).

### 5.4 Change of variables (for √n recurrences)

**T(n) = T(√n) + 1.** Let n = 2^m, S(m) = T(2^m). Then T(√n) = T(2^(m/2)) = S(m/2).
S(m) = S(m/2) + 1 → Θ(log m) → **T(n) = Θ(log log n)**.

**T(n) = 2T(√n) + log n.** S(m) = 2S(m/2) + m → Θ(m log m) → **T(n) = Θ(log n · log log n)**.

**T(n) = 2T(√n) + 1.** S(m) = 2S(m/2) + 1 → Θ(m) → **T(n) = Θ(log n)**.

---

## 6. Space complexity

Space = memory used beyond the input: variables, data structures, and the **recursion stack**.
- Iterative sum: O(1).
- Recursive factorial: O(n) (stack depth).
- Merge sort: O(n) auxiliary + O(log n) stack.
- Quick sort: O(log n) average stack, O(n) worst.

---

## 7. Exam traps

1. Θ = both O and Ω.
2. Base of a logarithm doesn't matter asymptotically; base of an exponent does (2ⁿ vs 4ⁿ).
3. `i *= 2` → log n; `i = i * i` → log log n; `i * i <= n` → √n; step n/2 → O(1).
4. Dependent inner loop shrinking geometrically (n + n/2 + ...) → O(n).
5. Harmonic inner loop (j += i) → n log n.
6. `2 * f(n − 1)` is one call.
7. Recursion space = maximum depth.
8. Master theorem: compare f(n) with n^(log_b a).
9. √n recurrences: substitute n = 2^m.
10. log(n!) = Θ(n log n).

---

## 8. Practice questions

**Q1.** Complexity of `for (i = 1; i <= n; i *= 3) count++;`?
(a) O(n) (b) O(log n) (c) O(√n) (d) O(n log n)

**Answer: (b).**

---

**Q2.** Complexity of `for (i = 2; i <= n; i = i * i) count++;`?
(a) O(log n) (b) O(√n) (c) O(log log n) (d) O(n)

**Answer: (c).**

---

**Q3.** Complexity?
```c
for (i = n; i >= 1; i /= 2)
    for (j = 1; j <= i; j++) count++;
```
(a) O(n log n) (b) O(n) (c) O(log n) (d) O(n²)

**Answer: (b).**

---

**Q4.** Complexity?
```c
for (i = 1; i <= n; i++)
    for (j = 1; j <= n; j += i) count++;
```
(a) O(n) (b) O(n log n) (c) O(n²) (d) O(n √n)

**Answer: (b).**

---

**Q5.** Solve T(n) = 8T(n/2) + n².
(a) Θ(n²) (b) Θ(n² log n) (c) Θ(n³) (d) Θ(n log n)

**Answer: (c).**

---

**Q6.** Solve T(n) = 2T(n/2) + n².
(a) Θ(n²) (b) Θ(n² log n) (c) Θ(n log n) (d) Θ(n³)

**Answer: (a).** Case 3.

---

**Q7.** Solve T(n) = T(√n) + 1.
(a) Θ(log n) (b) Θ(log log n) (c) Θ(√n) (d) Θ(1)

**Answer: (b).**

---

**Q8.** Time and space of:
```c
int rec(int n) { if (n == 1) return 1; return rec(n - 1) + rec(n - 1) + n; }
```
(a) O(n), O(n) (b) O(2ⁿ), O(n) (c) O(2ⁿ), O(2ⁿ) (d) O(n²), O(n)

**Answer: (b).**

---

**Q9.** Which is FALSE?
(a) 2^(n+1) = O(2ⁿ) (b) 2^(2n) = O(2ⁿ) (c) n² = O(n³) (d) log₂ n = Θ(log₁₀ n)

**Answer: (b).**

---

**Q10.** Arrange in increasing order: f₁ = 2ⁿ, f₂ = n^(3/2), f₃ = n log n, f₄ = n^(log n).
(a) f₃, f₂, f₄, f₁ (b) f₃, f₂, f₁, f₄ (c) f₂, f₃, f₁, f₄ (d) f₂, f₃, f₄, f₁

**Answer: (a).** n log n < n^1.5 < n^(log n) < 2ⁿ (compare logs: (log n)² < n).

---

**Q11.** Solve T(n) = 2T(n/2) + n log n.
(a) Θ(n log n) (b) Θ(n log² n) (c) Θ(n²) (d) Θ(n)

**Answer: (b).**

---

**Q12.** Solve T(n) = T(n − 1) + n, T(1) = 1.
(a) Θ(n) (b) Θ(n log n) (c) Θ(n²) (d) Θ(2ⁿ)

**Answer: (c).**

---

**Q13.** Complexity of `for (i = 1; i < n; i = i + n/3) count++;` (n ≥ 3)?
(a) O(1) (b) O(log n) (c) O(n) (d) O(√n)

**Answer: (a).** About 3 iterations.

---

**Q14.** Which statement is TRUE?
(a) f = O(g) implies g = O(f) (b) f = Θ(g) implies g = Θ(f) (c) f = o(f) (d) f = O(g) implies f = Θ(g)

**Answer: (b).**

---

**Q15.** Solve T(n) = 3T(n/3) + n/2.
(a) Θ(n) (b) Θ(n log n) (c) Θ(n²) (d) Θ(log n)

**Answer: (b).** p = 1, f = Θ(n) → case 2.

---

**Practice questions:** [2.15 Asymptotic Analysis and Recurrences](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.15_Asymptotic_Analysis_and_Recurrences.md)
