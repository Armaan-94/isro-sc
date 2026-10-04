# 16. Sorting Algorithms

> **Sorting questions come in three flavours:** (1) "what's the complexity / is it stable / in place?" (memorise one table), (2) "what does the array look like after one pass?" (trace carefully), and (3) "which sort is best for this situation?" (understand each algorithm's personality). This chapter covers all three.

---

## 1. Vocabulary

- **In-place:** uses only O(1) (or O(log n) for recursion) extra memory. Bubble, selection, insertion, heap, quick.
- **Not in-place:** needs a large extra buffer. Merge sort (O(n)), counting sort (O(k)).
- **Stable:** equal keys keep their original relative order. Matters when sorting records by one field after another (e.g. sort by name, then stably by department).
- **Adaptive:** runs faster on nearly sorted input (insertion, bubble with a flag).
- **Online:** can sort data as it arrives (insertion sort).
- **Internal vs external:** internal sorts fit in RAM; external sorting (huge files on disk) uses merge-based methods.
- **Inversion:** a pair (i, j) with i < j but A[i] > A[j]. A sorted array has 0 inversions; a reversed one has n(n − 1)/2.

---

## 2. Bubble sort

**Idea:** repeatedly walk through the array swapping **adjacent** out-of-order pairs. After pass k, the k largest elements are in their final places at the end.

```c
for (i = 0; i < n - 1; i++) {
    swapped = 0;
    for (j = 0; j < n - 1 - i; j++)
        if (a[j] > a[j + 1]) { swap(a[j], a[j + 1]); swapped = 1; }
    if (!swapped) break;        // early exit
}
```

**Trace** on [5, 1, 4, 2, 8]:
- Pass 1: (5,1) swap → 1 5 4 2 8; (5,4) swap → 1 4 5 2 8; (5,2) swap → 1 4 2 5 8; (5,8) ok. → **[1, 4, 2, 5, 8]**.
- Pass 2: (1,4) ok; (4,2) swap → 1 2 4 5 8; rest ok. → **[1, 2, 4, 5, 8]**.
- Pass 3: no swaps → stop.

- Worst/average: **O(n²)** comparisons (n(n − 1)/2) and swaps.
- Best: **O(n)** with the early-exit flag (sorted input); O(n²) without it.
- **Stable**, in place.
- Number of swaps = number of inversions.

---

## 3. Selection sort

**Idea:** find the **minimum** of the unsorted part and swap it into the next position.

```c
for (i = 0; i < n - 1; i++) {
    min = i;
    for (j = i + 1; j < n; j++) if (a[j] < a[min]) min = j;
    swap(a[i], a[min]);
}
```

**Trace** on [64, 25, 12, 22, 11]:
- i = 0: min 11 → swap with 64 → [11, 25, 12, 22, 64].
- i = 1: min 12 → swap with 25 → [11, 12, 25, 22, 64].
- i = 2: min 22 → swap with 25 → [11, 12, 22, 25, 64].
- i = 3: min 25 already in place.

- **Always O(n²)** comparisons (n(n − 1)/2), even on sorted input. No best-case speed-up.
- **At most n − 1 swaps**: the fewest of the simple sorts. Good when **writes are expensive** (e.g. flash memory).
- **Not stable** (the long-distance swap can jump an element over an equal one). In place.

---

## 4. Insertion sort

**Idea:** like sorting playing cards in your hand. Take the next element and **shift** larger elements of the sorted prefix right until its spot is found.

```c
for (i = 1; i < n; i++) {
    key = a[i]; j = i - 1;
    while (j >= 0 && a[j] > key) { a[j + 1] = a[j]; j--; }
    a[j + 1] = key;
}
```

**Trace** on [12, 11, 13, 5, 6]:
- i = 1: insert 11 → [11, 12, 13, 5, 6].
- i = 2: 13 stays.
- i = 3: insert 5 → [5, 11, 12, 13, 6].
- i = 4: insert 6 → [5, 6, 11, 12, 13].

- Best **O(n)** (already sorted: inner loop never runs). Worst/average **O(n²)**.
- Running time is **O(n + number of inversions)**: excellent for **nearly sorted** data.
- **Stable**, in place, **online**, great for small n (used inside hybrid sorts like Timsort and introsort for small subarrays).

> **Trap: binary insertion sort.** Using binary search to find the position reduces **comparisons** to O(n log n), but **shifting** is still O(n) per insertion → overall **still O(n²)**.

---

## 5. Merge sort

**Idea (divide and conquer):** split in half, sort each half recursively, **merge** the two sorted halves.

```c
void mergeSort(int a[], int l, int r) {
    if (l >= r) return;
    int m = (l + r) / 2;
    mergeSort(a, l, m);
    mergeSort(a, m + 1, r);
    merge(a, l, m, r);      // O(r − l + 1) with a temporary array
}
```

**Merge:** repeatedly take the smaller front element of the two halves. Taking from the **left** half on ties keeps it **stable**.

**Trace** on [38, 27, 43, 3, 9, 82, 10]:
- Split: [38, 27, 43, 3] and [9, 82, 10] → ... → singletons.
- Merge up: [27, 38], [3, 43] → [3, 27, 38, 43]; [9, 82], [10] → [9, 10, 82].
- Final merge: **[3, 9, 10, 27, 38, 43, 82]**.

- T(n) = 2T(n/2) + n → **Θ(n log n) in all cases**.
- Merging lists of sizes m and n takes at most **m + n − 1** comparisons (at least min(m, n)).
- **O(n) extra space**. **Stable**. Not in place (array version).
- Excellent for **linked lists** (no extra array needed, no random access needed) and **external sorting** (sequential access).

---

## 6. Quick sort

**Idea (divide and conquer):** pick a **pivot**, **partition** so smaller elements go left and larger go right (the pivot lands in its **final position**), then recursively sort both sides.

### Lomuto partition (pivot = last element)

```c
int partition(int a[], int lo, int hi) {
    int pivot = a[hi], i = lo - 1;
    for (int j = lo; j < hi; j++)
        if (a[j] < pivot) { i++; swap(a[i], a[j]); }
    swap(a[i + 1], a[hi]);
    return i + 1;
}
```

**Trace** on [10, 80, 30, 90, 40, 50, 70], pivot 70:
- j = 0 (10 < 70): i = 0, swap a[0] with itself.
- j = 1 (80): skip.
- j = 2 (30): i = 1, swap a[1] (80) and a[2] (30) → [10, 30, 80, 90, 40, 50, 70].
- j = 3 (90): skip.
- j = 4 (40): i = 2, swap a[2] (80) and a[4] (40) → [10, 30, 40, 90, 80, 50, 70].
- j = 5 (50): i = 3, swap a[3] (90) and a[5] (50) → [10, 30, 40, 50, 80, 90, 70].
- Finally swap a[4] (80) with the pivot → **[10, 30, 40, 50, 70, 90, 80]**. Pivot 70 at index 4, final position.

(Hoare's partition, with two pointers moving inward, does fewer swaps.)

### Complexity

| Case | When | Recurrence | Time |
|---|---|---|---|
| Best | Pivot always splits in half | T(n) = 2T(n/2) + n | **Θ(n log n)** |
| Average | Random input | | **Θ(n log n)** (about 1.39 n log₂ n comparisons) |
| Any **constant-ratio** split (e.g. 1:9) | | T(n) = T(n/10) + T(9n/10) + n | **Θ(n log n)** |
| **Worst** | Pivot is always the smallest/largest (e.g. **sorted or reverse-sorted** input with first/last pivot; or **all equal** keys with Lomuto) | T(n) = T(n − 1) + n | **Θ(n²)** |

Fixes: **random pivot**, **median-of-three** pivot, three-way partitioning for duplicates.

- Space: **O(log n)** average stack, O(n) worst (recurse into the smaller side first to keep O(log n)).
- **Not stable**. In place.
- Usually the **fastest in practice** (good cache behaviour, small constants).

### Identifying the pivot after one partition (GATE classic)

After the first partition: [2, 5, 1, 7, 9, 12, 11, 10]. Which could have been the pivot? An element is a valid pivot position if **everything left is smaller and everything right is larger**.
- 7: left {2, 5, 1} < 7, right {9, 12, 11, 10} > 7 ✓.
- 9: left {2, 5, 1, 7} < 9, right {12, 11, 10} > 9 ✓.
- So **either 7 or 9**.

### Quickselect

Find the k-th smallest element by partitioning and recursing into **one** side only: average **O(n)**, worst O(n²). (Median of medians gives worst-case O(n).)

---

## 7. Heap sort

**Idea:** build a **max-heap** (O(n)), then repeatedly swap the root (maximum) with the last element of the heap, shrink the heap by one, and sift down (O(log n)).

- **Θ(n log n) in all cases.**
- **O(1) extra space** (in place).
- **Not stable.**
- Guaranteed O(n log n) with O(1) space: preferred when worst-case guarantees and memory both matter (introsort switches to heap sort if quicksort recursion gets too deep).

---

## 8. Shell sort (recall)

Insertion sort on elements a **gap** apart, with gaps shrinking to 1 (e.g. n/2, n/4, ..., 1). Complexity depends on the gap sequence (about O(n^1.5) or O(n log² n) for good sequences). In place, not stable.

---

## 9. Non-comparison sorts

### Lower bound for comparison sorts

Any sort that only compares elements needs **Ω(n log n)** comparisons in the worst case.

**Why:** there are **n!** possible orderings. Each comparison has two outcomes, so a decision tree of height h distinguishes at most 2^h orderings. Need 2^h ≥ n! → h ≥ log₂(n!) = Θ(n log n).

Exact minimum worst-case comparisons = ⌈log₂(n!)⌉: n = 3 → ⌈log₂ 6⌉ = **3**; n = 4 → ⌈log₂ 24⌉ = **5**; n = 5 → ⌈log₂ 120⌉ = **7**.

Non-comparison sorts escape this bound by using **key values directly**.

### Counting sort

For integers in a small range 0..k:
1. Count occurrences of each value.
2. Prefix-sum the counts (each count becomes "number of elements ≤ this value").
3. Place elements into the output from **right to left** (keeps it **stable**).

- **O(n + k)** time, **O(n + k)** space, **stable**. Useless if k is huge (e.g. k = n²).

### Radix sort

Sort by **digits**, from **least significant** to most significant, using a **stable** sort (counting sort) at each digit.

- d digits, base b: **O(d (n + b))**.
- Example: [170, 45, 75, 90, 802, 24, 2, 66].
  - By 1s digit: 170, 90, 802, 2, 24, 45, 75, 66.
  - By 10s digit: 802, 2, 24, 45, 66, 170, 75, 90.
  - By 100s digit: 2, 24, 45, 66, 75, 90, 170, 802. ✓

### Bucket sort

Distribute uniformly distributed values (e.g. in [0, 1)) into n buckets, sort each bucket (insertion sort), concatenate. **Average O(n)**, worst O(n²).

---

## 10. The master comparison table

| Algorithm | Best | Average | Worst | Extra space | Stable | In place |
|---|---|---|---|---|---|---|
| Bubble (with flag) | **n** | n² | n² | 1 | **Yes** | Yes |
| Selection | n² | n² | n² | 1 | **No** | Yes |
| Insertion | **n** | n² | n² | 1 | **Yes** | Yes |
| Merge | n log n | n log n | n log n | **n** | **Yes** | No |
| Quick | n log n | n log n | **n²** | log n | **No** | Yes |
| Heap | n log n | n log n | n log n | **1** | **No** | Yes |
| Shell | n log n | ~n^1.3 | n² (depends) | 1 | No | Yes |
| Counting | n + k | n + k | n + k | n + k | Yes | No |
| Radix | d(n + b) | d(n + b) | d(n + b) | n + b | Yes | No |
| Bucket | n + k | n + k | n² | n + k | Yes | No |

Memory trick for **unstable** sorts: "**Q**uick **S**elect a **H**eap of **Sh**ells" (Quick, Selection, Heap, Shell).

---

## 11. Which sort when?

| Situation | Best choice | Why |
|---|---|---|
| Nearly sorted / very small n | **Insertion** | O(n + inversions), tiny constants |
| Guaranteed O(n log n), stable needed | **Merge** | Stable, predictable |
| Guaranteed O(n log n), O(1) memory | **Heap** | In place, no worst-case blow-up |
| Fastest on average, general data | **Quick** (randomised) | Cache-friendly |
| Swaps/writes are very costly | **Selection** | ≤ n − 1 swaps |
| Linked list | **Merge** | Sequential access |
| Small integer key range | **Counting / radix** | Linear time |
| Data on disk (huge) | **External merge sort** | Sequential I/O |
| All elements equal | **Insertion** (O(n)) | Quick sort with Lomuto would be O(n²) |

---

## 12. Exam traps

1. Selection sort is O(n²) even on sorted input, but makes at most n − 1 swaps.
2. Bubble sort's O(n) best case needs the early-exit flag.
3. Binary insertion sort is still O(n²) (shifting).
4. Merge sort: Θ(n log n) always, O(n) space, stable.
5. Quick sort: O(n²) on sorted input with a first/last pivot; any constant-ratio split stays n log n.
6. Heap sort: Θ(n log n), O(1) space, unstable.
7. Comparison lower bound: ⌈log₂ n!⌉ = Ω(n log n). Counting/radix beat it by not comparing.
8. Unstable: quick, selection, heap, shell.
9. After one quicksort partition, the pivot is in its final position.

---

## 13. Practice questions

**Q1.** Best-case complexity of bubble sort with the early-exit flag?
(a) O(n log n) (b) O(n) (c) O(n²) (d) O(log n)

**Answer: (b).**

---

**Q2.** Worst case of insertion sort using binary search to find positions?
(a) O(n) (b) O(n log n) (c) O(n²) (d) O(n log² n)

**Answer: (c).**

---

**Q3.** After the first quicksort partition the array is [2, 5, 1, 7, 9, 12, 11, 10]. The pivot could be:
(a) 7 or 9 (b) only 7 (c) only 9 (d) neither

**Answer: (a).**

---

**Q4.** Array after the first pass of bubble sort (ascending) on [4, 3, 2, 1]?
(a) [3, 2, 1, 4] (b) [1, 2, 3, 4] (c) [3, 4, 2, 1] (d) [2, 3, 1, 4]

**Answer: (a).**

---

**Q5.** Array after two passes of selection sort on [29, 10, 14, 37, 13]?
(a) [10, 13, 14, 37, 29] (b) [10, 14, 29, 37, 13] (c) [10, 13, 29, 37, 14] (d) [10, 13, 14, 29, 37]

**Answer: (a).** Pass 1: min 10 ↔ 29 → [10, 29, 14, 37, 13]. Pass 2: min of [29, 14, 37, 13] is 13 ↔ 29 → [10, 13, 14, 37, 29].

---

**Q6.** Array after the 3rd iteration (i = 3) of insertion sort on [5, 2, 4, 6, 1, 3]?
(a) [2, 4, 5, 6, 1, 3] (b) [2, 4, 5, 1, 6, 3] (c) [1, 2, 4, 5, 6, 3] (d) [2, 5, 4, 6, 1, 3]

**Answer: (a).** i = 1: [2, 5, 4, 6, 1, 3]; i = 2: [2, 4, 5, 6, 1, 3]; i = 3: 6 stays.

---

**Q7.** Which sort is NOT stable?
(a) Insertion (b) Merge (c) Heap (d) Bubble

**Answer: (c).**

---

**Q8.** Minimum number of comparisons needed in the worst case to sort 5 elements (comparison model)?
(a) 5 (b) 7 (c) 10 (d) 8

**Answer: (b).** ⌈log₂ 120⌉ = 7.

---

**Q9.** Quick sort with the last element as pivot on an already sorted array of n elements takes:
(a) Θ(n) (b) Θ(n log n) (c) Θ(n²) (d) Θ(log n)

**Answer: (c).**

---

**Q10.** Merging two sorted lists of sizes 5 and 7 needs at most how many comparisons?
(a) 5 (b) 7 (c) 11 (d) 12

**Answer: (c).** m + n − 1.

---

**Q11.** Number of swaps bubble sort performs on [3, 1, 2]?
(a) 1 (b) 2 (c) 3 (d) 0

**Answer: (b).** Inversions: (3,1), (3,2).

---

**Q12.** Which sort runs in O(n) on an array where all elements are equal?
(a) Selection (b) Heap (c) Insertion (d) Merge

**Answer: (c).**

---

**Q13.** Quick sort where every partition splits 1 : 99. Time complexity?
(a) Θ(n²) (b) Θ(n log n) (c) Θ(n) (d) Θ(n log log n)

**Answer: (b).** Any constant-ratio split gives depth O(log n).

---

**Q14.** Radix sort of n numbers with d digits in base 10 runs in:
(a) O(n log n) (b) O(d (n + 10)) (c) O(n²) (d) O(d log n)

**Answer: (b).**

---

**Q15.** Which sort makes the fewest swaps?
(a) Bubble (b) Insertion (c) Selection (d) Quick

**Answer: (c).**
