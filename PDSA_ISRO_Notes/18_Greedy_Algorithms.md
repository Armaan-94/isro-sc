# 18. Greedy Algorithms (Huffman, Optimal Merge, Knapsack, Job Sequencing, Activity Selection)

> **The greedy idea:** at each step, grab whatever looks best **right now**, and never look back. It's fast and simple, and for some problems it's provably optimal. For others it fails badly. The skill is knowing which problems are which, and executing the greedy rule precisely in numericals.

---

## 1. The greedy paradigm

At each step:
1. Make the **locally optimal** choice (by some rule: smallest, largest, best ratio, earliest finish...).
2. **Commit** to it; never undo it.
3. Solve the remaining smaller problem the same way.

Greedy gives the **global optimum** only if the problem has:
- **Greedy-choice property:** some optimal solution starts with the greedy choice.
- **Optimal substructure:** an optimal solution contains optimal solutions to subproblems.

| | Greedy | Dynamic programming |
|---|---|---|
| Decisions | One choice per step, never revisited | Considers all choices via subproblem tables |
| Speed | Usually faster | Usually slower, more memory |
| Correct for | Problems with the greedy-choice property | Problems with optimal substructure and overlapping subproblems |
| Example | Fractional knapsack, Huffman, MST, Dijkstra | 0/1 knapsack, LCS, matrix chain |

### A counterexample to keep in mind

**Coin change** with coins {1, 3, 4}, amount 6. Greedy (largest first): 4 + 1 + 1 = **3 coins**. Optimal: 3 + 3 = **2 coins**. Greedy fails. (For standard currency systems like {1, 2, 5, 10, ...}, greedy happens to work.)

---

## 2. Huffman coding

### 2.1 The goal

Assign **variable-length binary codes** to characters so that frequent characters get **short** codes, minimising the total number of bits. Codes must be **prefix-free** (no code is a prefix of another), so decoding is unambiguous.

### 2.2 The algorithm

1. Make a leaf for each character, weighted by frequency. Put them in a **min-priority queue**.
2. Repeat n − 1 times: **remove the two smallest**, create a parent whose weight is their **sum**, insert it back.
3. The last node is the root. Label left edges 0 and right edges 1 (or vice versa). A character's code = the path from root to its leaf.

Time: **O(n log n)** with a min-heap.

### 2.3 Worked example 1

Frequencies: M1 = 12, M2 = 4, M3 = 45, M4 = 17, M5 = 23 (total 101).

| Step | Merge | New node |
|---|---|---|
| 1 | M2 (4) + M1 (12) | 16 |
| 2 | 16 + M4 (17) | 33 |
| 3 | M5 (23) + 33 | 56 |
| 4 | M3 (45) + 56 | 101 (root) |

```
            101
           /   \
       M3:45    56
               /  \
           M5:23   33
                  /  \
                16    M4:17
               /  \
           M2:4   M1:12
```

Code lengths: M3 → 1, M5 → 2, M4 → 3, M1 → 4, M2 → 4.

Total bits = 45(1) + 23(2) + 17(3) + 12(4) + 4(4) = 45 + 46 + 51 + 48 + 16 = **206**.

Fixed-length code: ⌈log₂ 5⌉ = 3 bits each → 3 × 101 = 303. Saved **97 bits**.

**Average code length** = 206/101 ≈ **2.04 bits** per symbol.

### 2.4 Worked example 2 (classic textbook)

a = 5, b = 9, c = 12, d = 13, e = 16, f = 45 (total 100).

| Step | Merge | New |
|---|---|---|
| 1 | a (5) + b (9) | 14 |
| 2 | c (12) + d (13) | 25 |
| 3 | 14 + e (16) | 30 |
| 4 | 25 + 30 | 55 |
| 5 | f (45) + 55 | 100 |

One valid code: **f = 0, c = 100, d = 101, a = 1100, b = 1101, e = 111**.

Total = 45(1) + 12(3) + 13(3) + 16(3) + 5(4) + 9(4) = 45 + 36 + 39 + 48 + 20 + 36 = **224** bits. Fixed length: 3 × 100 = 300. Saved 76.

Encode "dead": d e a d = 101 111 1100 101 = **1011111100101** (13 bits).

### 2.5 Non-uniqueness

Swapping 0/1 at any node, or breaking ties between equal weights differently, gives **different codes with the same total length**. Both are optimal. The **code lengths** (in terms of total cost) are what's fixed, not the exact bit strings.

### 2.6 Facts

- Huffman is optimal among **prefix codes** for given symbol frequencies.
- The two least frequent symbols are siblings at the deepest level.
- Shannon-Fano coding (top-down splitting) is not always optimal; Huffman (bottom-up) is.

---

## 3. Optimal merge pattern

**Problem:** merge n sorted files into one, two at a time. Merging files of sizes x and y costs **x + y** record moves. Minimise the total cost.

**Greedy rule:** always merge the **two smallest** files (exactly Huffman's rule).

**Example:** sizes 18, 3, 15, 12, 10, 11, 7, 9. Sorted: 3, 7, 9, 10, 11, 12, 15, 18.

| Merge | Cost | Remaining list |
|---|---|---|
| 3 + 7 | 10 | 9, 10, 10, 11, 12, 15, 18 |
| 9 + 10 | 19 | 10, 11, 12, 15, 18, 19 |
| 10 + 11 | 21 | 12, 15, 18, 19, 21 |
| 12 + 15 | 27 | 18, 19, 21, 27 |
| 18 + 19 | 37 | 21, 27, 37 |
| 21 + 27 | 48 | 37, 48 |
| 37 + 48 | 85 | 85 |

Total = 10 + 19 + 21 + 27 + 37 + 48 + 85 = **247**.

Quick check formula: total cost = Σ (file size × depth of that file in the merge tree).

**Example 2:** sizes 20, 30, 10, 5, 30. Sorted 5, 10, 20, 30, 30: 5 + 10 = 15; 15 + 20 = 35; 30 + 30 = 60; 35 + 60 = 95. Total = 15 + 35 + 60 + 95 = **205**.

---

## 4. Knapsack

Capacity W; items with weight wᵢ and value (profit) pᵢ; maximise total value.

### 4.1 Fractional knapsack: greedy works

You may take **fractions** of items.

**Rule:** sort by **value/weight ratio** (descending); take as much as possible of each in that order.

**Example 1:** W = 20. O1 (p 25, w 18), O2 (p 24, w 15), O3 (p 15, w 10).
Ratios: O1 = 1.39, O2 = **1.60**, O3 = 1.50 → order O2, O3, O1.
- Take all of O2: weight 15, profit 24. Remaining 5.
- Take 5/10 of O3: profit 7.5.
- **Total 31.5** (optimal).

Other greedy rules fail:
- By profit (O1 first): O1 (25) + 2/15 of O2 (3.2) = **28.2**.
- By lightest weight (O3 first): O3 (15) + 10/15 of O2 (16) = **31**.

**Example 2:** W = 50. Items (p, w): (60, 10), (100, 20), (120, 30). Ratios 6, 5, 4.
Take items 1 and 2 fully (weight 30, profit 160), then 20/30 of item 3 (80). **Total 240.**

Time: O(n log n) (sorting). At most **one** item is taken fractionally.

### 4.2 0/1 knapsack: greedy fails

Items can't be split. Greedy by ratio can be wrong.

Same Example 2 but 0/1: greedy by ratio takes items 1 and 2 (weight 30, profit 160) and can't fit item 3. But items 2 and 3 (weight 50) give **220**. Greedy is **not optimal**.

Use **dynamic programming** (Chapter 19): O(nW).

---

## 5. Job sequencing with deadlines

n jobs, each takes **1 unit** of time, has a **deadline** dᵢ and a **profit** pᵢ (earned only if finished by its deadline). One machine. Maximise profit.

**Algorithm:**
1. Sort jobs by **profit, descending**.
2. For each job, put it in the **latest free slot ≤ its deadline**.
3. If no such slot, **skip** it.

**Example 1:** T1 (100, d 2), T2 (10, d 1), T3 (27, d 2), T4 (15, d 1).

| Job (by profit) | Deadline | Slot | Running profit |
|---|---|---|---|
| T1 (100) | 2 | slot 2 | 100 |
| T3 (27) | 2 | slot 2 taken → slot 1 | 127 |
| T4 (15) | 1 | slot 1 taken → rejected | 127 |
| T2 (10) | 1 | rejected | 127 |

Max profit **127** (sequence T3, T1).

**Example 2:** J1 (20, d 2), J2 (15, d 2), J3 (10, d 1), J4 (5, d 3), J5 (1, d 3).
- J1 → slot 2. J2 → slot 1. J3 (d 1) → slot 1 taken → reject. J4 → slot 3. J5 → slots 3, 2, 1 taken → reject.
- Max profit = 20 + 15 + 5 = **40**.

Time: O(n²) simply; O(n log n) with a disjoint-set structure.

---

## 6. Activity selection (interval scheduling)

Choose the **maximum number** of non-overlapping activities (each with start sᵢ and finish fᵢ).

**Rule:** sort by **finish time**; repeatedly pick the activity that finishes earliest among those starting after the last chosen one finishes.

**Example:** (start, finish): (1, 4), (3, 5), (0, 6), (5, 7), (3, 9), (5, 9), (6, 10), (8, 11), (8, 12), (2, 14), (12, 16).
- Pick (1, 4). Next starting ≥ 4 with earliest finish: (5, 7). Next starting ≥ 7: (8, 11). Next starting ≥ 11: (12, 16).
- **4 activities.**

(Greedy by earliest **start** or **shortest duration** can fail; earliest **finish** is provably optimal.)

---

## 7. Other greedy algorithms (covered elsewhere)

- **Kruskal's and Prim's MST** (Chapter 20).
- **Dijkstra's shortest paths** (Chapter 20) (fails with negative edges).
- Huffman, as above.
- Scheduling to minimise average completion time: **shortest job first** (that's SJF from OS).

---

## 8. Exam traps

1. Greedy is optimal only with the greedy-choice property.
2. **Fractional knapsack: greedy by ratio is optimal. 0/1 knapsack: greedy fails, use DP.**
3. Greedy by profit or by weight alone fails even for fractional knapsack.
4. Huffman: merge the two smallest; O(n log n); codes not unique, total cost is.
5. Optimal merge = Huffman on file sizes; total = sum of all intermediate merge costs.
6. Job sequencing: highest profit first, latest free slot before the deadline.
7. Activity selection: earliest finish time first.
8. Coin change {1, 3, 4} for 6 shows greedy failure.

---

## 9. Practice questions

**Q1.** For which problem does greedy generally fail?
(a) Fractional knapsack (b) Huffman coding (c) 0/1 knapsack (d) Optimal merge pattern

**Answer: (c).**

---

**Q2.** Huffman total bits for frequencies M1 = 12, M2 = 4, M3 = 45, M4 = 17, M5 = 23?
(a) 303 (b) 206 (c) 224 (d) 101

**Answer: (b).**

---

**Q3.** Characters with frequencies a = 1, b = 1, c = 2, d = 3, e = 5. Huffman total bits?
(a) 25 (b) 27 (c) 24 (d) 30

**Answer: (a).**
- a + b = 2. Then {2, c 2, d 3, e 5}: 2 + 2 = 4. Then {d 3, 4, e 5}: 3 + 4 = 7. Then 5 + 7 = 12.
- Depths: e 1, d 2, c 3, a 4, b 4.
- Total = 5(1) + 3(2) + 2(3) + 1(4) + 1(4) = 5 + 6 + 6 + 4 + 4 = **25**.

---

**Q4.** Files of sizes 5, 10, 20, 30, 30. Minimum total merge cost?
(a) 190 (b) 205 (c) 215 (d) 180

**Answer: (b).**

---

**Q5.** Fractional knapsack, W = 20: O1 (25, 18), O2 (24, 15), O3 (15, 10) as (profit, weight). Maximum profit?
(a) 28.2 (b) 31 (c) 31.5 (d) 34

**Answer: (c).**

---

**Q6.** Jobs T1 (100, d 2), T2 (10, d 1), T3 (27, d 2), T4 (15, d 1). Maximum profit?
(a) 100 (b) 115 (c) 127 (d) 152

**Answer: (c).**

---

**Q7.** Time complexity of Huffman coding with a min-heap for n symbols?
(a) O(n) (b) O(n log n) (c) O(n²) (d) O(log n)

**Answer: (b).**

---

**Q8.** Activity selection is solved optimally by repeatedly choosing the compatible activity with the:
(a) earliest start (b) shortest duration (c) earliest finish (d) highest number of overlaps

**Answer: (c).** (By symmetry, "latest start, scanning backwards" also works, but the standard rule is earliest finish.)

---

**Q9.** With coins {1, 5, 6, 9}, greedy (largest first) makes 11 as:
(a) 9 + 1 + 1, 3 coins, which is optimal (b) 9 + 1 + 1, 3 coins, but 5 + 6 uses only 2 (c) 6 + 5 (d) 5 + 5 + 1

**Answer: (b).**

---

**Q10.** Fractional knapsack W = 50, items (p, w): (60, 10), (100, 20), (120, 30). Maximum profit?
(a) 220 (b) 240 (c) 280 (d) 160

**Answer: (b).**

---

**Q11.** In Huffman coding, which is always TRUE?
(a) The code for each symbol is unique across all valid Huffman trees (b) The most frequent symbol has the longest code (c) No code is a prefix of another (d) All codes have equal length

**Answer: (c).**

---

**Q12.** Average code length (bits/symbol) in Q3?
(a) 25/12 ≈ 2.08 (b) 3 (c) 2.5 (d) 1.5

**Answer: (a).** 25 bits over 12 symbols.
