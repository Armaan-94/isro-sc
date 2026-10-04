# 07. File Organization, Indexing, B-Trees and B+ Trees

> **The one fact that explains this whole chapter.** Reading from disk happens in **blocks**, and one block access costs about as much as **millions** of CPU operations. So database performance is measured in **number of block accesses**. Indexes and B+ trees exist to make that number tiny.

---

## 1. Records and blocks

- Data is stored as **records** (rows) inside **blocks** (pages) on disk.
- The DBMS reads and writes **whole blocks**, never single records.

### Blocking factor

```
bfr = floor(Block size / Record size)        records per block (unspanned)
b   = ceil(Number of records / bfr)          blocks needed for the file
```

**Unspanned** = a record never crosses a block boundary (leftover space at the end of each block is wasted). **Spanned** = records may continue into the next block (no waste, more complex).

**Example:** 30,000 records of 100 bytes, block size 1024 bytes.
- bfr = floor(1024/100) = 10.
- b = ceil(30000/10) = **3000 blocks**.
- Wasted per block: 1024 − 1000 = 24 bytes.

---

## 2. File organizations

### Heap (unordered) file

New records are appended at the end.
- **Insert:** cheap (1 block write).
- **Search:** **linear**: on average b/2 blocks, worst case b.
- Delete: find, then mark/remove (may leave holes).

### Sorted (ordered / sequential) file

Records kept sorted on an **ordering field**.
- **Search on the ordering field:** binary search, **ceil(log₂ b)** block accesses.
- **Search on any other field:** still linear.
- **Insert/delete:** expensive (must keep order: shifting or overflow blocks plus periodic reorganisation).

> **Trap.** Binary search helps **only** when searching on the field the file is sorted by.

### Hashed file

A hash function on a field maps each record to a bucket. Equality search on that field takes about **1 block access**. Useless for range queries.

---

## 3. Indexes

An **index** is a separate, small, sorted file of (search key, pointer) pairs, like the index at the back of a textbook.

- The index file is much **smaller** than the data file (each entry is just key + pointer), so more entries fit per block.
- The index is always **sorted on its key**, so we can binary-search it.
- You can have **many indexes** on one table, on key or non-key attributes.

### Dense vs sparse

- **Dense index:** an entry for **every search-key value** (for a secondary index on a key: every record).
- **Sparse index:** entries for **only some** values, typically one per **block** (pointing to the first record, the **block anchor**). Only possible when the data file is **sorted on that key**.

### Types of single-level index

| Index | Data file sorted on | Key or non-key field? | Dense/sparse | Number of index entries |
|---|---|---|---|---|
| **Primary** | The index field | **Key** (unique) | **Sparse** | = **number of data blocks** |
| **Clustering** | The index field | **Non-key** (repeating values) | Sparse | = **number of distinct values** |
| **Secondary** | Anything (not this field) | Key or non-key | **Dense** | = **number of records** (for a key field) |

Rules:
- A file can have **at most one** primary **or** clustering index (it can be physically sorted only one way).
- A file can have **many** secondary indexes.

> **Trap.** Primary index = **sparse**, on the **ordering key**. Secondary index = **dense**, on **any** field.

---

## 4. Counting block accesses (the classic numerical)

**Given:** r = 30,000 records, block = 1024 B, record = 100 B, key field V = 9 B, block pointer P = 6 B.

From section 1: bfr = 10, b = 3000 data blocks.

### Without an index

- Heap file, linear search: average b/2 = **1500**, worst **3000**.
- File sorted on the key, binary search: ceil(log₂ 3000) = **12**.

### Primary index (sparse)

- Index entry = V + P = 15 B. Index blocking factor **bfrᵢ = floor(1024/15) = 68** (this is also the **fan-out**).
- Entries = number of data blocks = 3000.
- Index blocks = ceil(3000/68) = **45**.
- Search: binary search on the index + 1 data block = ceil(log₂ 45) + 1 = 6 + 1 = **7**.

### Secondary index (dense) on a key field

- Entries = 30,000 records.
- Index blocks = ceil(30000/68) = **442**.
- Search: ceil(log₂ 442) + 1 = 9 + 1 = **10**. (Way better than 1500 for a linear scan of an unsorted field.)

### Multilevel index

Why binary-search the index? Build an **index on the index**:

- Level 1: 442 blocks.
- Level 2: one entry per level-1 block: ceil(442/68) = **7** blocks.
- Level 3: ceil(7/68) = **1** block (the top).
- Search: one block per level + 1 data block = 3 + 1 = **4**.

For the primary index: 45 → ceil(45/68) = 1. Two levels → 2 + 1 = **3**.

General: levels **t = ceil(log_fo (first-level blocks)) + 1** where fo = fan-out. Multilevel search cost = **t + 1** block accesses.

A multilevel index that supports insertions and deletions gracefully is exactly what a **B+ tree** is.

---

## 5. Why trees, and why not a binary search tree?

A BST or AVL tree has **fan-out 2**, so 1 million keys need height ≈ 20, i.e. **20 block accesses**. Disk-based trees make each node **one disk block** holding **hundreds of keys**, so fan-out is in the hundreds and the height for a million keys is 3 or 4.

---

## 6. B-tree

### 6.1 Definition (order m = maximum number of children)

A **B-tree of order m** is an m-way search tree where:

| Node | Max children | Min children | Max keys | Min keys |
|---|---|---|---|---|
| Root | m | 2 (if not a leaf) | m − 1 | 1 |
| Internal | m | **⌈m/2⌉** | m − 1 | **⌈m/2⌉ − 1** |
| Leaf | 0 | 0 | m − 1 | ⌈m/2⌉ − 1 |

- **All leaves are at the same level** (perfectly balanced).
- A node with k keys has k + 1 children.
- Keys in a node are sorted; subtrees between keys hold values in between.
- **Data (record) pointers are stored with every key**, in internal nodes too.

Example: order 5 → max 4 keys, 5 children; internal nodes have at least 3 children and 2 keys.

> Some books define "order" as the **minimum degree t** (each node has t to 2t children, t − 1 to 2t − 1 keys). Check the question's convention.

### 6.2 Insertion

1. Find the correct leaf and insert the key in sorted position.
2. If the node now has **m keys** (overflow), **split**: the **median** key **moves up** to the parent; the left and right halves become two nodes.
3. If the parent overflows, split it too. If the **root** splits, a **new root** is created: **that's the only way the tree's height grows** (it grows upward from the root).

**Example (order 3: max 2 keys).** Insert 10, 20, 30, 40, 50.

- 10, 20 → root [10 | 20].
- 30 → [10 | 20 | 30] overflows. Median 20 goes up.
  ```
       [20]
      /    \
   [10]    [30]
  ```
- 40 → [30 | 40].
- 50 → [30 | 40 | 50] overflows. Median 40 goes up to the root.
  ```
        [20 | 40]
       /    |    \
    [10]  [30]   [50]
  ```

### 6.3 Deletion (conceptual)

1. Delete the key (if it's in an internal node, replace it with its in-order predecessor or successor from a leaf, then delete that from the leaf).
2. If a node **underflows** (fewer than ⌈m/2⌉ − 1 keys):
   - **Borrow** from an adjacent sibling through the parent (rotation), if the sibling has spare keys.
   - Otherwise **merge** with a sibling, pulling the separating key down from the parent. The parent may underflow in turn; if the root becomes empty, the height **shrinks**.

### 6.4 Capacity formulas

For a B-tree of order m with height h (root at level 0, so h + 1 levels):

- **Maximum keys:** every node full: (m − 1)(1 + m + m² + ... + m^h) = **m^(h+1) − 1**.
  Example: m = 3, h = 2: 3³ − 1 = **26** keys.
- **Minimum keys:** root has 1 key and 2 children; every other node has ⌈m/2⌉ − 1 keys and ⌈m/2⌉ children. With d = ⌈m/2⌉: min keys = **2d^h − 1**.
  Example: m = 5 (d = 3), h = 2: 2 × 9 − 1 = **17**.

---

## 7. B+ tree (what real databases use)

### 7.1 Differences from a B-tree

| Feature | B-tree | B+ tree |
|---|---|---|
| Data pointers | In **every** node | **Only in leaves** |
| Internal nodes | Keys + data pointers | **Only keys + child pointers** (pure routing) |
| Leaves | Not linked | **Linked** left-to-right (sequence set) |
| Key duplication | Each key appears once | Keys in internal nodes **also appear** in leaves |
| Search cost | Can stop early at an internal node | Always goes to a leaf (uniform cost) |
| Range queries | Awkward (tree traversal) | **Excellent** (find start, walk the leaf chain) |
| Fan-out | Lower | **Higher** (no data pointers in internal nodes) → shorter tree |

That's why **almost all real DBMS indexes and many file systems use B+ trees**.

### 7.2 Insertion difference

When a **leaf** splits, the median key is **copied up** to the parent (it **stays** in the leaf, since leaves must hold all keys). When an **internal** node splits, the median **moves up** (as in a B-tree).

**Example (B+ tree, order 3, max 2 keys per node).** Insert 10, 20, 30.
- [10 | 20], then 30 → leaf [10 | 20 | 30] overflows. Split into [10] and [20 | 30] (one common convention); **copy** 20 up:
  ```
       [20]
      /    \
   [10] -> [20 | 30]
  ```
  Note 20 is in both the internal node and a leaf, and leaves are linked.

### 7.3 Computing the order of a B+ tree node (very common numerical)

**Internal node:** holds p child pointers and p − 1 keys:

```
p × P + (p − 1) × V ≤ B
```

**Leaf node:** holds p_leaf (key, record-pointer) pairs plus one next-leaf block pointer:

```
p_leaf × (V + Pr) + P ≤ B
```

**Example:** B = 1024, V = 9, block pointer P = 6, record pointer Pr = 7.
- Internal: 6p + 9(p − 1) ≤ 1024 → 15p ≤ 1033 → **p = 68**.
- Leaf: 16 p_leaf + 6 ≤ 1024 → p_leaf ≤ 63.6 → **p_leaf = 63**.

**B-tree node** (every key has a record pointer too): p × P + (p − 1)(V + Pr) ≤ B → 6p + 16(p − 1) ≤ 1024 → 22p ≤ 1040 → **p = 47**. Smaller fan-out than the B+ tree's 68. That's the fan-out advantage in numbers.

### 7.4 How many records can a B+ tree index?

With internal order p and leaf capacity p_leaf, and nodes filled to some fraction (often assumed **69% full** in practice, or 100% for maximum):

- Root: p children. Level 1: p² children... Leaves at level h: p^h leaf nodes × p_leaf keys.

**Example (100% full, 3 levels: root, one internal level, leaves):** p = 68, p_leaf = 63.
- Root: 68 pointers → 68 nodes at level 1 → 68 × 68 = 4624 leaves → 4624 × 63 = **291,312** keys.

---

## 8. Exam traps

1. Performance = number of **block accesses**.
2. bfr = floor(B/R); blocks = ceil(r/bfr).
3. Primary index: sparse, entries = data blocks. Secondary: dense, entries = records.
4. Single-level index search = ceil(log₂ index blocks) + 1. Multilevel = levels + 1.
5. B-tree order m: max keys m − 1, min keys (non-root) ⌈m/2⌉ − 1.
6. B-tree height grows **only** at the root.
7. B+ tree: data only in leaves; leaves linked; better range queries; higher fan-out.
8. B+ leaf split: median **copied** up. Internal split: median **moved** up.
9. Use the inequalities in 7.3 to compute node order.

---

## 9. Practice questions

**Q1.** Block size 2048 B, record size 120 B, 50,000 records (unspanned). Number of blocks?
(a) 2930 (b) 2942 (c) 3000 (d) 2500

**Answer: (b).** bfr = floor(2048/120) = 17. Blocks = ceil(50000/17) = 2942 (17 × 2941 = 49,997, so one more block is needed).

---

**Q2.** A data file has 4096 blocks and is sorted on the key. A binary search on the key costs:
(a) 12 (b) 13 (c) 4096 (d) 2048

**Answer: (a).** log₂ 4096 = 12.

---

**Q3.** A single-level primary index occupies 64 blocks. Block accesses to retrieve a record?
(a) 6 (b) 7 (c) 64 (d) 65

**Answer: (b).** log₂ 64 + 1 = 7.

---

**Q4.** Which index is always dense?
(a) Primary (b) Clustering (c) Secondary (d) None

**Answer: (c).**

---

**Q5.** Minimum number of keys in a non-root node of a B-tree of order 7?
(a) 2 (b) 3 (c) 4 (d) 6

**Answer: (b).** ⌈7/2⌉ − 1 = 4 − 1 = 3.

---

**Q6.** Maximum number of keys in a B-tree of order 4 with height 2 (3 levels)?
(a) 63 (b) 64 (c) 48 (d) 21

**Answer: (a).** 4³ − 1 = 63. (Nodes: 1 + 4 + 16 = 21, each with 3 keys.)

---

**Q7.** B+ tree internal node: block 512 B, key 10 B, block pointer 6 B. Maximum order p?
(a) 31 (b) 32 (c) 33 (d) 51

**Answer: (b).** 6p + 10(p − 1) ≤ 512 → 16p ≤ 522 → p = 32.

---

**Q8.** In a B+ tree, where are record pointers stored?
(a) every node (b) only the root (c) only leaf nodes (d) only internal nodes

**Answer: (c).**

---

**Q9.** Which structure is best for the query `SELECT * FROM T WHERE salary BETWEEN 50000 AND 60000`?
(a) Hash index on salary (b) B+ tree index on salary (c) Heap file (d) Bitmap on name

**Answer: (b).** Range query: find 50000 in the B+ tree, then walk the linked leaves.

---

**Q10.** Insert 5, 15, 25, 35, 45, 55, 65 into an empty B-tree of order 3 (max 2 keys per node), splitting by moving the median up. Final root contents?
(a) [35] (b) [25 | 45] (c) [15 | 35 | 55] (d) [45]

**Answer: (a).**
- 5, 15 → [5 | 15]. 25 → split, root [15], leaves [5], [25].
- 35 → [25 | 35]. 45 → [25 | 35 | 45] split, 35 up: root [15 | 35], leaves [5], [25], [45].
- 55 → [45 | 55]. 65 → [45 | 55 | 65] split, 55 up: root [15 | 35 | 55] overflows → split, 35 up as new root.
- Final: root [35], children [15] and [55], leaves [5], [25], [45], [65].

---

**Q11.** A file with 10,000 blocks has a secondary index of 1000 blocks with fan-out 100. With a multilevel index, block accesses to fetch a record?
(a) 3 (b) 4 (c) 11 (d) 14

**Answer: (b).** Level 1: 1000 blocks. Level 2: 10. Level 3: 1. Three levels + 1 data block = 4.

---

**Q12.** Why does a B+ tree usually have smaller height than a B-tree with the same block size?
(a) B+ trees are unbalanced (b) Internal nodes don't store record pointers, so they hold more keys (higher fan-out) (c) B+ trees store fewer keys (d) B+ trees use hashing

**Answer: (b).**
