# 09. Data Structures Basics and Arrays (Addressing and Special Matrices)

> **What this chapter is really about.** Two skills: (1) classifying data structures correctly, and (2) computing the **address** of any element of a 1-D, 2-D, 3-D or special (triangular/banded) matrix. Address questions are pure formula plus care with lower bounds. Do them slowly once and they'll never trouble you again.

---

## 1. What is a data structure?

A **data structure** is a way of **organising data in memory** so that certain operations (search, insert, delete, traverse) are efficient. Choosing the right one changes an algorithm from slow to fast.

An **Abstract Data Type (ADT)** describes **what** operations exist (e.g. Stack: push, pop, top) without saying **how** they're implemented (array or linked list).

### Classification

```
Data structures
├── Primitive (directly supported by hardware): int, float, char, pointer
└── Non-primitive
    ├── Linear: Array, Linked list, Stack, Queue
    └── Non-linear: Tree, Graph
```

- **Linear:** elements form a sequence; each has at most one predecessor and one successor; can be traversed in a single pass.
- **Non-linear:** hierarchical or networked; an element can connect to many others.
- **Static** (fixed size, e.g. array) vs **dynamic** (grows/shrinks, e.g. linked list).
- **Homogeneous** (same type: array) vs **heterogeneous** (mixed: struct).

> **Trap.** An array is **non-primitive** (derived from primitives), and it's **linear**.

---

## 2. Arrays

- Elements of the **same type**, stored **contiguously**.
- **O(1) random access** by index: the address is computed directly.
- Insertion/deletion in the middle costs **O(n)** (shifting).
- Fixed size (static arrays); cache-friendly.

Number of elements in a[L..U] = **U − L + 1**.

---

## 3. 1-D array addressing

```
Address(a[k]) = B + W × (k − L)
```

B = base address (address of the first element a[L]), W = size of one element, L = lower bound.

**Example:** a[−6..6], W = 4, B = 3500.
- a[0]: 3500 + 4 × (0 − (−6)) = 3500 + 24 = **3524**.
- a[3]: 3500 + 4 × 9 = **3536**.
- Number of elements = 6 − (−6) + 1 = **13**.

**Example:** a[5..50], W = 2, B = 1000. a[20]: 1000 + 2 × 15 = **1030**.

**Finding the base from a known address:** if a[10] is at 1040 with W = 4, L = 0, then B = 1040 − 40 = 1000.

---

## 4. 2-D array addressing

Array a[L1..U1][L2..U2].
- Number of **rows** R = U1 − L1 + 1.
- Number of **columns** C = U2 − L2 + 1.

### Row-major (C, C++, Java, Python/NumPy default)

Store row by row. To reach a[i][j], skip (i − L1) **full rows** of C elements each, then (j − L2) elements:

```
Address = B + W × [ (i − L1) × C + (j − L2) ]
```

### Column-major (Fortran, MATLAB)

Store column by column. Skip (j − L2) full columns of R elements each, then (i − L1):

```
Address = B + W × [ (j − L2) × R + (i − L1) ]
```

> **Trap.** Row-major multiplies the row offset by the number of **columns**. Column-major multiplies the column offset by the number of **rows**.

### Worked example

VAL[1..15][1..10], W = 4, B = 1500. Find VAL[12][9].
- R = 15, C = 10.
- **Row-major:** 1500 + 4 × [(12 − 1) × 10 + (9 − 1)] = 1500 + 4 × 118 = **1972**.
- **Column-major:** 1500 + 4 × [(9 − 1) × 15 + (12 − 1)] = 1500 + 4 × 131 = **2024**.

### Another (0-indexed)

int m[3][4], B = 1000, W = 4.
- Row-major m[2][1]: 1000 + 4 × (2 × 4 + 1) = **1036**.
- Column-major m[2][1]: 1000 + 4 × (1 × 3 + 2) = **1020**.

### Working backwards

"A 2-D array A[0..9][0..19] row-major with W = 2. A[2][3] is at 1086. Address of A[5][6]?"
- Difference in elements: (5 − 2) × 20 + (6 − 3) = 63. Address = 1086 + 2 × 63 = **1212**.

---

## 5. 3-D and n-D arrays

Row-major generalises: each index's offset is multiplied by the **product of the sizes of all later dimensions**.

For A[L1..U1][L2..U2][L3..U3] with sizes D1, D2, D3:

```
Address = B + W × [ (i − L1) × D2 × D3 + (j − L2) × D3 + (k − L3) ]
```

**Example:** A[1..3][1..4][1..5], W = 2, B = 1000. A[2][3][4]:
- (2 − 1) × 4 × 5 = 20
- (3 − 1) × 5 = 10
- (4 − 1) = 3
- Total 33 → 1000 + 2 × 33 = **1066**.

(Column-major for 3-D: the **first** index varies fastest: offset = (k − L3) × D1 × D2 + (j − L2) × D1 + (i − L1).)

---

## 6. Special matrices and compact storage

Many matrices are mostly zeros or have repeated values. Store only what's needed in a 1-D array.

### 6.1 Lower triangular (non-zero only where i ≥ j)

n × n, elements to store: **n(n + 1)/2**.

Row-major, 0-indexed: row i has (i + 1) elements, so the rows before row i hold 1 + 2 + ... + i = i(i + 1)/2 elements.

```
Index of A[i][j] (j ≤ i) = i(i + 1)/2 + j
Address = B + W × [ i(i + 1)/2 + j ]
```

**Example:** n = 6, B = 1000, W = 4. A[4][2]: 4 × 5/2 + 2 = 12 → **1048**.
**Example:** n = 8, B = 1000, W = 4. A[5][3]: 15 + 3 = 18 → **1072**.

1-indexed version: index = i(i − 1)/2 + (j − 1).

### 6.2 Upper triangular (non-zero only where i ≤ j)

Row-major, 0-indexed: row r holds n − r elements. Elements before row i = n + (n − 1) + ... + (n − i + 1) = **i × n − i(i − 1)/2**.

```
Index of A[i][j] (j ≥ i) = i × n − i(i − 1)/2 + (j − i)
```

**Example:** n = 6, B = 2000, W = 2. A[2][4]: 2 × 6 − 1 + (4 − 2) = 11 + 2 = 13 → 2000 + 26 = **2026**.

(Column-major storage of an upper triangular matrix uses the same formula as row-major lower triangular with i and j swapped: index = j(j + 1)/2 + i.)

### 6.3 Space needed (n × n)

| Matrix | Definition | Elements stored |
|---|---|---|
| Diagonal | A[i][j] = 0 if i ≠ j | **n** |
| Lower / upper triangular | zeros above / below the diagonal | **n(n + 1)/2** |
| Strictly lower triangular | zeros on and above diagonal | n(n − 1)/2 |
| **Symmetric** | A[i][j] = A[j][i] | **n(n + 1)/2** (store one triangle including the diagonal) |
| **Tridiagonal** | A[i][j] = 0 if \|i − j\| > 1 | **3n − 2** |
| Band matrix (bandwidth k on each side) | | roughly (2k + 1)n − k(k + 1) |
| **Toeplitz** | A[i][j] = A[i − 1][j − 1] (constant diagonals) | **2n − 1** |
| Sparse | mostly zeros | 3 × (non-zeros), as (row, col, value) triplets |

**Tridiagonal index (row-major, 0-indexed):** A[i][j] with |i − j| ≤ 1 is at index **2i + j** (row 0 holds 2 elements, every later row 3, minus offsets). Check: A[0][0] → 0, A[0][1] → 1, A[1][0] → 2, A[1][1] → 3, A[1][2] → 4 ✓.

### 6.4 Sparse matrices: triplet form

Store only non-zero entries as (row, column, value), plus a header (rows, columns, count). Worth it when the number of non-zeros t satisfies 3t < m × n (roughly).

---

## 7. Exam traps

1. Array: non-primitive, linear.
2. Count elements as U − L + 1.
3. Always subtract the lower bound before multiplying.
4. Row-major: × number of columns. Column-major: × number of rows.
5. 3-D row-major: first index × (D2 × D3).
6. Triangular and symmetric: n(n + 1)/2. Tridiagonal: 3n − 2. Toeplitz: 2n − 1.
7. Check whether the question is 0-indexed or 1-indexed.

---

## 8. Practice questions

**Q1.** a[−5..10], W = 2, B = 400. Address of a[5]?
(a) 410 (b) 420 (c) 430 (d) 440

**Answer: (b).** 400 + 2 × (5 + 5) = 420.

---

**Q2.** Number of elements in a[−3..12]?
(a) 15 (b) 16 (c) 9 (d) 12

**Answer: (b).**

---

**Q3.** A[1..10][1..20], W = 4, B = 2000, row-major. Address of A[5][15]?
(a) 2376 (b) 2380 (c) 2336 (d) 2316

**Answer: (a).** (4 × 20 + 14) × 4 = 94 × 4 = 376.

---

**Q4.** Same array, column-major. Address of A[5][15]?
(a) 2576 (b) 2580 (c) 2376 (d) 2556

**Answer: (a).** (14 × 10 + 4) × 4 = 144 × 4 = 576.

---

**Q5.** A[0..4][0..5][0..6], W = 1, B = 0, row-major. Address of A[2][3][4]?
(a) 109 (b) 108 (c) 96 (d) 110

**Answer: (a).** 2 × 6 × 7 + 3 × 7 + 4 = 84 + 21 + 4 = 109.

---

**Q6.** Elements needed to store a 10 × 10 symmetric matrix?
(a) 100 (b) 55 (c) 50 (d) 45

**Answer: (b).** 10 × 11 / 2.

---

**Q7.** Elements needed to store a 10 × 10 tridiagonal matrix?
(a) 30 (b) 28 (c) 19 (d) 20

**Answer: (b).** 3 × 10 − 2.

---

**Q8.** Elements needed for a 10 × 10 Toeplitz matrix?
(a) 10 (b) 19 (c) 20 (d) 55

**Answer: (b).** 2 × 10 − 1.

---

**Q9.** Lower triangular, n = 10, 0-indexed, row-major, B = 500, W = 2. Address of A[6][4]?
(a) 550 (b) 548 (c) 552 (d) 546

**Answer: (a).** 6 × 7/2 + 4 = 25 → 500 + 50 = 550.

---

**Q10.** Upper triangular, n = 5, 0-indexed, row-major, B = 0, W = 1. Index of A[1][3]?
(a) 6 (b) 7 (c) 8 (d) 5

**Answer: (b).** Elements before row 1 = 5. Offset 3 − 1 = 2. Index 7.

---

**Q11.** Which is a non-linear data structure?
(a) Queue (b) Stack (c) Graph (d) Linked list

**Answer: (c).**

---

**Q12.** A row-major array X[0..19][0..29], W = 4. X[3][5] is at 3000. Address of X[4][2]?
(a) 3108 (b) 3104 (c) 3112 (d) 3100

**Answer: (a).** Element difference = (4 − 3) × 30 + (2 − 5) = 30 − 3 = 27. 3000 + 27 × 4 = 3108.
