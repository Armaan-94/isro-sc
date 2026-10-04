# 17. Searching Algorithms and the Min-Max Problem

> **The core idea.** Searching unsorted data means looking at everything (O(n)). Sorted data lets you throw away **half** the candidates with each comparison (O(log n)). And sometimes clever pairing of comparisons saves a constant factor, as in the min-max problem, which is a favourite numerical.

---

## 1. Linear (sequential) search

Check each element in turn until you find the key or run out.

```c
int linearSearch(int a[], int n, int key) {
    for (int i = 0; i < n; i++)
        if (a[i] == key) return i;
    return -1;
}
```

| Case | Comparisons |
|---|---|
| Best (key is first) | 1 → **O(1)** |
| Worst (key last or absent) | n → **O(n)** |
| Average (key present, equally likely anywhere) | (n + 1)/2 → **O(n)** |

- Works on **unsorted** data, arrays **and** linked lists.
- **Sentinel trick:** put the key at the end so the loop doesn't need an `i < n` check each time (halves the comparisons per step).

---

## 2. Binary search

**Precondition: the array must be sorted** (and allow O(1) random access).

**Idea:** compare the key with the **middle** element. If equal, done. If smaller, search the left half; if larger, the right half.

```c
int binarySearch(int a[], int n, int key) {
    int lo = 0, hi = n - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;      // avoids overflow of (lo + hi)
        if (a[mid] == key) return mid;
        else if (a[mid] < key) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;                             // lo > hi: not found
}
```

### Trace

Search **47** in [3, 9, 15, 22, 31, 40, 47, 58, 66, 72] (indices 0 to 9):

| lo | hi | mid | a[mid] | Action |
|---|---|---|---|---|
| 0 | 9 | 4 | 31 | 31 < 47 → lo = 5 |
| 5 | 9 | 7 | 58 | 58 > 47 → hi = 6 |
| 5 | 6 | 5 | 40 | 40 < 47 → lo = 6 |
| 6 | 6 | 6 | 47 | **found** at index 6 |

4 key comparisons (counting each probe of a[mid]).

### Complexity

- Recurrence T(n) = T(n/2) + O(1) → **Θ(log n)**.
- **Worst-case number of probes = ⌊log₂ n⌋ + 1.** For n = 1024 → **11**; n = 1000 → 10; n = 100 → 7.
  (Many MCQ options simply say "log₂ n", e.g. "about 10 for 1024". If 11 is an option, it's the precise worst case.)
- Best case: **O(1)** (middle element).
- Space: **O(1)** iterative; **O(log n)** recursive (stack).
- Average successful search ≈ log₂ n − 1 probes.

### When binary search doesn't help

- **Unsorted data:** you'd have to sort first, O(n log n), which is worse than a single O(n) linear scan. Sorting pays off only if you'll search **many** times.
- **Linked lists:** reaching the middle takes O(n) steps, so binary search loses its advantage.

### Useful variants

- First/last occurrence of a duplicate key (keep searching left/right after a match).
- Lower bound / upper bound (insertion point).
- Searching in a rotated sorted array.
- Binary search on the **answer** (e.g. smallest x with some monotone property).

---

## 3. Other searches (recall level)

| Search | Idea | Time | Requirement |
|---|---|---|---|
| **Jump search** | Jump ahead √n steps, then linear search back | **O(√n)** | Sorted |
| **Interpolation search** | Guess the position by value: pos = lo + (key − a[lo]) × (hi − lo)/(a[hi] − a[lo]) | **O(log log n)** average for uniformly distributed data; O(n) worst | Sorted, roughly uniform |
| **Exponential search** | Find range by doubling (1, 2, 4, 8, ...), then binary search | O(log n) | Sorted; good for unbounded lists |
| **Ternary search** | Split into three parts | O(log₃ n), but **more** comparisons than binary search | Sorted / unimodal functions |
| **Hashing** | Compute the slot | O(1) average | Hash table (Chapter 14) |
| **BST search** | Go left/right | O(log n) balanced, O(n) skewed | BST (Chapter 12) |

Jump size √n minimises the worst case: (n/m) jumps + (m − 1) linear steps is minimised at m = √n.

---

## 4. The Min-Max problem

**Task:** find **both** the minimum and the maximum of n elements using as few comparisons as possible.

### 4.1 Straightforward method

```c
max = min = a[0];
for (i = 1; i < n; i++) {
    if (a[i] > max) max = a[i];
    else if (a[i] < min) min = a[i];
}
```

| Case | Comparisons |
|---|---|
| Best (increasing array: every element beats max, so the else is skipped) | **n − 1** |
| Worst (decreasing array: each element fails the max test, then tests min) | **2(n − 1)** |
| Average | about 3n/2 |

(Without the `else`, it's always 2(n − 1).)

### 4.2 Divide and conquer / pairing method

Split into halves, find min and max of each half recursively, then combine with **2 comparisons** (min vs min, max vs max). Base cases: 1 element (0 comparisons), 2 elements (1 comparison).

```
T(n) = 2T(n/2) + 2,   T(2) = 1,   T(1) = 0
```

For n a power of 2: **T(n) = 3n/2 − 2**.

Equivalent iterative idea: process elements **in pairs**: compare the two elements of a pair with each other (1 comparison), then the smaller against min and the larger against max (2 comparisons): **3 comparisons per 2 elements**.

- n **even**: 1 + 3(n − 2)/2 = **3n/2 − 2**.
- n **odd**: 3(n − 1)/2 = **3⌊n/2⌋** (initialise min = max = a[0], then pairs).
- In general: **⌈3n/2⌉ − 2**.

This is **provably optimal**: no comparison algorithm can do better than ⌈3n/2⌉ − 2 in the worst case.

### Worked numbers

| n | Straight (worst) 2(n − 1) | Optimal ⌈3n/2⌉ − 2 |
|---|---|---|
| 8 | 14 | 10 |
| 20 | 38 | 28 |
| 64 | 126 | **94** |
| 100 | 198 | 148 |
| 250 | 498 | **373** |
| 15 | 28 | 21 |

Both are Θ(n). The improvement is in the **constant** (1.5n vs 2n).

### Related: second largest

Find the second largest with **n + ⌈log₂ n⌉ − 2** comparisons using a **tournament**: the second largest must have lost directly to the champion, and the champion played ⌈log₂ n⌉ matches. For n = 8: 8 + 3 − 2 = **9**.

### Related: finding just the max (or just the min)

Exactly **n − 1** comparisons are necessary and sufficient.

---

## 5. Exam traps

1. Binary search needs **sorted** data and **random access** (not linked lists).
2. Worst-case probes: ⌊log₂ n⌋ + 1.
3. Recursive binary search uses O(log n) stack space.
4. Sorting just to search once is slower than a linear scan.
5. Straight min-max: best n − 1 (increasing input), worst 2(n − 1) (decreasing input).
6. Optimal min-max: ⌈3n/2⌉ − 2.
7. Interpolation search: O(log log n) average on uniform data.
8. Second largest: n + ⌈log n⌉ − 2.

---

## 6. Practice questions

**Q1.** Worst-case number of comparisons for binary search on 1000 sorted elements?
(a) 9 (b) 10 (c) 11 (d) 500

**Answer: (b).** ⌊log₂ 1000⌋ + 1 = 9 + 1 = 10.

---

**Q2.** Which statement about binary search is FALSE?
(a) It requires sorted data (b) Worst case is O(log n) (c) It is efficient on singly linked lists (d) Recursive version uses O(log n) stack space

**Answer: (c).**

---

**Q3.** Straight min-max on a strictly decreasing array of 20 elements (using if / else-if). Comparisons?
(a) 19 (b) 20 (c) 38 (d) 40

**Answer: (c).**

---

**Q4.** Same method on a strictly increasing array of 20 elements?
(a) 19 (b) 38 (c) 28 (d) 20

**Answer: (a).**

---

**Q5.** Optimal (divide and conquer) min-max comparisons for n = 64?
(a) 63 (b) 94 (c) 126 (d) 96

**Answer: (b).**

---

**Q6.** Comparisons needed to find the second largest of 16 elements (tournament method)?
(a) 15 (b) 18 (c) 19 (d) 30

**Answer: (b).** 16 + 4 − 2.

---

**Q7.** Average-case complexity of interpolation search on uniformly distributed sorted data?
(a) O(1) (b) O(log log n) (c) O(log n) (d) O(√n)

**Answer: (b).**

---

**Q8.** Optimal block size for jump search on n elements?
(a) log n (b) √n (c) n/2 (d) n

**Answer: (b).**

---

**Q9.** In the binary search trace for 47 above, which index is probed first?
(a) 0 (b) 4 (c) 5 (d) 9

**Answer: (b).** mid = (0 + 9)/2 = 4.

---

**Q10.** You will search an unsorted array of n elements exactly once. Best approach?
(a) Sort, then binary search (b) Linear search (c) Build a BST, then search (d) Build a heap, then search

**Answer: (b).** O(n) beats O(n log n).

---

**Q11.** Recurrence for divide-and-conquer min-max?
(a) T(n) = 2T(n/2) + 1 (b) T(n) = 2T(n/2) + 2 (c) T(n) = T(n/2) + 2 (d) T(n) = T(n − 1) + 2

**Answer: (b).**

---

**Q12.** Optimal min-max comparisons for n = 15?
(a) 21 (b) 22 (c) 28 (d) 20

**Answer: (a).** ⌈22.5⌉ − 2 = 23 − 2 = 21 (or 3 × 7 = 21 by pairs).
