# 12. Trees: Binary Trees, BST, AVL, Heaps, Threaded and K-ary Trees

> **Trees are the single biggest data-structures topic in ISRO papers.** Expect at least two questions: traversals, reconstructing a tree, counting nodes/leaves, BST insertion/deletion, AVL rotations, or heap operations. Every one of them is solved by drawing the tree and following a rule. Let's learn the rules one by one.

---

## 1. Vocabulary

```
              A          <- root (level 0, depth 0)
            /   \
           B     C       <- level 1
          / \     \
         D   E     F     <- level 2; D, E, F are leaves
```

- **Root:** the top node (no parent).
- **Parent / child / siblings** (B and C are siblings).
- **Leaf (external node):** no children (D, E, F). **Internal node:** at least one child (A, B, C).
- **Degree of a node:** number of children (B has 2, C has 1). Degree of a tree: max node degree.
- **Depth of a node:** number of edges from the root. **Height of a node:** edges on the longest path down to a leaf. **Height of the tree** = height of the root (here 2).
- **Level:** root at level 0 (some books use 1; always check).
- A tree with **N nodes** has exactly **N − 1 edges**.

---

## 2. Binary trees and their formulas

A **binary tree**: each node has at most **2** children (left and right).

| Fact (root at level 0, height in edges) | Formula |
|---|---|
| Max nodes at level L | **2^L** |
| Max nodes in a tree of height h | **2^(h+1) − 1** |
| Min nodes in a tree of height h | **h + 1** (a chain) |
| Min height with n nodes | **⌈log₂(n + 1)⌉ − 1** (= ⌊log₂ n⌋) |
| Max height with n nodes | **n − 1** |
| NULL child pointers in a tree with n nodes | **n + 1** |
| Leaves vs nodes with 2 children | **n₀ = n₂ + 1** |

The last one is the most useful: in **any** binary tree, **number of leaves = number of nodes with two children + 1**.

Example: a binary tree has 20 nodes with two children. Leaves = **21**.

### Types of binary trees

- **Full (strict / proper):** every node has **0 or 2** children. Then n = 2L − 1 (L = leaves), internal = L − 1.
- **Complete:** every level full except possibly the last, which is filled **left to right**. (Can be stored in an array with no gaps.)
- **Perfect:** all internal nodes have 2 children and all leaves are at the same level: n = 2^(h+1) − 1.
- **Skewed:** every node has only one child (a linked list in disguise).
- **Balanced:** heights of left and right subtrees differ by at most a small amount everywhere (e.g. AVL).

### Counting trees

- Number of **structurally different** binary trees with n nodes = **Catalan number** C(n) = (2n)! / ((n + 1)! n!): 1, 1, 2, **5**, **14**, 42, ...
- Number of **labelled** binary trees with n distinct labels = C(n) × n!.
- Number of different **BSTs** with n distinct keys = C(n) (the keys' order fixes the labelling).

---

## 3. Traversals

| Traversal | Order | Memory trick |
|---|---|---|
| **Preorder** | **Root**, Left, Right | Root first |
| **Inorder** | Left, **Root**, Right | Root in the middle |
| **Postorder** | Left, Right, **Root** | Root last |
| **Level order** | Level by level, left to right | Uses a **queue** (BFS) |

In all three depth-first traversals, **left is always before right**; only the root's position moves.

### Example

```
        4
      /   \
     2     6
    / \   / \
   1   3 5   7
```

- Preorder: **4 2 1 3 6 5 7**
- Inorder: **1 2 3 4 5 6 7** (sorted: it's a BST)
- Postorder: **1 3 2 5 7 6 4**
- Level order: **4 2 6 1 3 5 7**

### Uses

- Preorder: copy/serialise a tree; prefix expression from an expression tree.
- Inorder: **sorted order of a BST**; infix expression.
- Postorder: delete/free a tree (children first); postfix expression; evaluating expression trees.

### Expression trees

`(a + b) * (c − d)`:
```
        *
      /   \
     +     −
    / \   / \
   a   b c   d
```
Preorder → prefix `* + a b − c d`; inorder → infix; postorder → postfix `a b + c d − *`.

---

## 4. Reconstructing a tree from traversals

| Given | Unique binary tree? |
|---|---|
| Inorder + preorder | **Yes** |
| Inorder + postorder | **Yes** |
| Inorder + level order | **Yes** |
| Preorder + postorder | **No** (only if the tree is known to be **full**) |
| Only one traversal | No (except preorder or postorder of a **BST**, since the inorder is just the sorted keys) |

### Method (inorder + preorder)

1. The **first** element of preorder is the **root**.
2. Find it in inorder: everything **left** of it is the left subtree, everything **right** is the right subtree.
3. Recurse with the corresponding pieces of preorder.

For **postorder**, the root is the **last** element.

### Worked example (inorder + postorder)

Inorder: **D B E A F C**. Postorder: **D E B F C A**.
- Root = **A** (last of postorder). Inorder splits: left {D, B, E}, right {F, C}.
- Left part of postorder: D E B → root **B**; inorder D | B | E → left D, right E.
- Right part of postorder: F C → root **C**; inorder F C → F is C's **left** child.

```
        A
      /   \
     B     C
    / \   /
   D   E F
```
Preorder: **A B D E C F**.

### Worked example (BST from preorder only)

Preorder of a BST: **30, 20, 10, 15, 25, 23, 39, 35, 42**. Find the postorder.
- Root 30. Keys < 30: 20, 10, 15, 25, 23 (left); keys > 30: 39, 35, 42 (right).
- Left: root 20; < 20: 10, 15 (10 with right child 15); > 20: 25, 23 (25 with left child 23).
- Right: root 39; left 35, right 42.
- Postorder: **15 10 23 25 20 35 42 39 30**.

---

## 5. Binary Search Tree (BST)

**Property:** for every node, all keys in the **left** subtree are **smaller**, all keys in the **right** subtree are **larger**.

Consequence: **inorder traversal gives sorted order.**

### Search, insert

Start at the root; go left if the key is smaller, right if larger. Insert at the NULL spot where the search ends.

**Insert 50, 30, 70, 20, 40, 60, 80:**
```
          50
        /    \
      30      70
     /  \    /  \
    20  40  60  80
```
Preorder 50 30 20 40 70 60 80; postorder 20 40 30 60 80 70 50.

**Insert 10, 20, 30, 40, 50 (sorted):** a right-skewed chain of height 4. **This is the worst case.**

### Delete (three cases)

1. **Leaf:** just remove it.
2. **One child:** connect that child to the parent.
3. **Two children:** replace the key with its **inorder successor** (smallest in the right subtree) or **inorder predecessor** (largest in the left subtree), then delete that node (which has at most one child).

Delete **50** from the tree above: successor = **60** (leftmost of the right subtree). Root becomes 60; 70's left becomes NULL.

> The inorder successor of a node with two children has **no left child** (it's the leftmost node of the right subtree).

### Complexity

| Operation | Average | Worst (skewed) |
|---|---|---|
| Search / insert / delete | O(log n) | **O(n)** |

### Average comparisons for a successful search

Sum of (level of each node, root = 1) ÷ number of nodes.

```
          10          level 1
         /  \
        5    20       level 2
       /    /  \
      4    15   30    level 3
          /
         11           level 4
```
(1 + 2 + 2 + 3 + 3 + 3 + 4)/7 = 18/7 ≈ **2.57**.

---

## 6. AVL trees (height-balanced BSTs)

### Definition

A BST in which, for **every** node, **balance factor = height(left) − height(right) ∈ {−1, 0, +1}**.

Guarantees height **O(log n)**, so search/insert/delete are **O(log n) in the worst case**.

### Rotations after insertion

Insert as in a BST, then walk up; at the **first** unbalanced node (BF = +2 or −2), identify the case by the path from that node toward the inserted key:

| Case | Inserted into | Fix |
|---|---|---|
| **LL** | left subtree of the left child | **Single right rotation** |
| **RR** | right subtree of the right child | **Single left rotation** |
| **LR** | right subtree of the left child | **Left rotate the child, then right rotate the node** |
| **RL** | left subtree of the right child | **Right rotate the child, then left rotate the node** |

**Examples:**
- Insert **30, 20, 10** → LL at 30 → right rotate → **20** with children 10, 30.
- Insert **10, 20, 30** → RR at 10 → left rotate → **20** with children 10, 30.
- Insert **30, 10, 20** → LR at 30 → **20** with children 10, 30.
- Insert **10, 30, 20** → RL at 10 → **20** with children 10, 30.

After an **insertion**, **at most one** (single or double) rotation fixes the whole tree. After a **deletion**, rotations may be needed at several ancestors (up to O(log n)).

### Minimum nodes in an AVL tree of height h

```
N(h) = N(h − 1) + N(h − 2) + 1,   N(0) = 1, N(1) = 2
```
N(2) = 4, N(3) = 7, N(4) = 12, **N(5) = 20**, N(6) = 33.

So the **maximum height** of an AVL tree with n nodes is about **1.44 log₂ n**: e.g. with 12 nodes the height can be at most 4; with 20 nodes, at most 5.

---

## 7. Heaps

### Definition

A **binary heap** is a **complete binary tree** with the **heap property**:
- **Max-heap:** every parent ≥ its children (maximum at the root).
- **Min-heap:** every parent ≤ its children.

> A heap is **not** a BST. There's no ordering between siblings or across subtrees, only parent vs child.

### Array representation (0-indexed)

- parent(i) = **⌊(i − 1)/2⌋**
- left(i) = **2i + 1**, right(i) = **2i + 2**
- Leaves occupy indices ⌊n/2⌋ to n − 1.

(1-indexed: parent ⌊i/2⌋, children 2i and 2i + 1.)

### Is this array a max-heap?

[23, 17, 14, 7, 13, 10, 1, 5, 6, 12]: check each child against its parent: 17, 14 ≤ 23; 7, 13 ≤ 17; 10, 1 ≤ 14; 5, 6 ≤ 7; 12 ≤ 13. ✓ **Valid.**

### Insert (sift up / bubble up), O(log n)

Place the new key at the end, then swap with the parent while it's larger (max-heap).

Max-heap [50, 30, 40, 10, 20], insert **45**: placed at index 5 (parent index 2 = 40). 45 > 40 → swap → index 2 (parent 50). 45 < 50 → stop.
Result **[50, 30, 45, 10, 20, 40]**.

### Delete max (sift down / heapify), O(log n)

Remove the root, move the **last** element to the root, then swap it down with its **larger** child until the heap property holds.

Start [25, 14, 16, 13, 10, 8, 12]:
- Delete 25. Move 12 to the root: [12, 14, 16, 13, 10, 8]. Larger child is 16 → swap: [16, 14, 12, 13, 10, 8]. 12's child 8: fine. → **[16, 14, 12, 13, 10, 8]**.
- Delete 16. Move 8 to root: [8, 14, 12, 13, 10]. Larger child 14 → swap: [14, 8, 12, 13, 10]. 8's children 13, 10: larger 13 → swap: **[14, 13, 12, 8, 10]**.

### Build a heap from an array: O(n)

Heapify (sift down) each internal node from the **last internal node** ⌊n/2⌋ − 1 back to the root.

[4, 10, 3, 5, 1] (n = 5): last internal index = 1.
- i = 1 (10): children 5, 1: OK.
- i = 0 (4): children 10, 3 → swap with 10: [10, 4, 3, 5, 1]; continue at index 1 (4): children 5, 1 → swap with 5: **[10, 5, 3, 4, 1]**.

Building by inserting one at a time is O(n log n); bottom-up heapify is **O(n)**.

### Other heap facts

- Find max (max-heap): O(1). Find **min** in a max-heap: O(n) (it's among the leaves, about n/2 of them).
- **Heap sort:** build a max-heap, then repeatedly swap the root with the last element and heapify: **O(n log n)**, in place, not stable.
- k-th largest: O(k log n) with repeated delete-max.

---

## 8. Threaded binary trees

In a binary tree with n nodes there are **n + 1 NULL pointers**: wasted. A **threaded** tree replaces them:
- a NULL **left** pointer → points to the **inorder predecessor**,
- a NULL **right** pointer → points to the **inorder successor**.

A flag bit per pointer says whether it's a real child or a thread. Benefit: **inorder traversal without recursion or a stack**.
- **Single-threaded:** only one kind (usually right threads).
- **Double-threaded:** both.

---

## 9. K-ary trees

A **full (strict) k-ary tree**: every node has **0 or exactly k** children. With I internal nodes and L leaves:

```
Total nodes  N = k·I + 1
Leaves       L = (k − 1)·I + 1
```

Why? Every internal node contributes k child edges; total edges N − 1 = kI.

| Given | Find |
|---|---|
| 3-ary, L = 41 | I = (41 − 1)/2 = 20; N = **61** |
| 4-ary, N = 85 | I = (85 − 1)/4 = **21**; L = 64 |
| 5-ary, I = 26 | L = 4 × 26 + 1 = **105** |
| N = 121, I = 30 | 121 = 30k + 1 → **k = 4** |
| L = 67, I = 33 | 67 = 33(k − 1) + 1 → **k = 3** |

(Binary case k = 2: L = I + 1, matching n₀ = n₂ + 1 for full binary trees.)

---

## 10. B-trees (pointer)

Multi-way balanced search trees used for disk indexes are covered in [DBMS Chapter 07](../DBMS_ISRO_Notes/07_File_Organization_Indexing_BTrees.md).

---

## 11. Exam traps

1. Leaves = (nodes with 2 children) + 1 in any binary tree.
2. Max nodes of height h: 2^(h+1) − 1 (root at height/level 0).
3. Catalan numbers: 5 trees/BSTs for 3 keys, 14 for 4.
4. Preorder + postorder doesn't give a unique tree.
5. BST inorder = sorted; worst case O(n) for sorted insertions.
6. Two-child deletion: inorder successor/predecessor.
7. AVL BF ∈ {−1, 0, 1}; LR/RL need double rotations; minimum nodes recurrence.
8. Heap: complete tree, parent-child order only; parent (i − 1)/2 (0-indexed).
9. Build-heap O(n); heap sort O(n log n).
10. Full k-ary: N = kI + 1, L = (k − 1)I + 1.

---

## 12. Practice questions

**Q1.** A binary tree has 10 leaves. How many nodes have exactly two children?
(a) 9 (b) 10 (c) 11 (d) cannot be determined

**Answer: (a).**

---

**Q2.** Maximum number of nodes in a binary tree of height 5 (root at height 0)?
(a) 31 (b) 32 (c) 63 (d) 64

**Answer: (c).** 2⁶ − 1.

---

**Q3.** Number of structurally different binary trees with 3 nodes?
(a) 3 (b) 5 (c) 6 (d) 14

**Answer: (b).**

---

**Q4.** Inorder: B D A G E C H F I; preorder: A B D C E G F H I. Postorder?
(a) D B G E H I F C A (b) D B E G H F I C A (c) B D G E H I F C A (d) D B G E I H F C A

**Answer: (a).**
- Root A. Inorder left {B, D}, right {G, E, C, H, F, I}.
- Left: preorder B D → root B, D is its right child (D after B in inorder).
- Right: preorder C E G F H I → root C. Inorder left of C: {G, E}; right: {H, F, I}.
  - {G, E}: preorder E G → root E, G is left child.
  - {H, F, I}: preorder F H I → root F, H left, I right.
- Postorder: D B | G E H I F C | A → **D B G E H I F C A**.

---

**Q5.** Preorder of a BST: 30, 20, 10, 15, 25, 23, 39, 35, 42. Postorder?
(a) 10, 20, 15, 23, 25, 35, 42, 39, 30 (b) 15, 10, 25, 23, 20, 42, 35, 39, 30 (c) 15, 20, 10, 23, 25, 42, 35, 39, 30 (d) 15, 10, 23, 25, 20, 35, 42, 39, 30

**Answer: (d).**

---

**Q6.** Inserting keys 1, 2, 3, ..., n in order into an empty BST produces a tree of height:
(a) log n (b) n − 1 (c) n/2 (d) 1

**Answer: (b).**

---

**Q7.** Minimum number of nodes in an AVL tree of height 4 (single node = height 0)?
(a) 7 (b) 12 (c) 15 (d) 20

**Answer: (b).**

---

**Q8.** Inserting 10, 30, 20 into an empty AVL tree requires:
(a) LL rotation (b) RR rotation (c) LR rotation (d) RL rotation

**Answer: (d).** 20 goes into the left subtree of 10's right child (30).

---

**Q9.** Max-heap [25, 14, 16, 13, 10, 8, 12]. After two delete-max operations?
(a) [14, 13, 12, 10, 8] (b) [14, 12, 13, 8, 10] (c) [14, 13, 8, 12, 10] (d) [14, 13, 12, 8, 10]

**Answer: (d).**

---

**Q10.** In a 0-indexed heap array, the parent of index 9 is:
(a) 4 (b) 5 (c) 3 (d) 8

**Answer: (a).** (9 − 1)/2 = 4.

---

**Q11.** Time to build a heap of n elements using bottom-up heapify:
(a) O(log n) (b) O(n) (c) O(n log n) (d) O(n²)

**Answer: (b).**

---

**Q12.** A full 3-ary tree has 41 leaves. Total nodes?
(a) 20 (b) 41 (c) 60 (d) 61

**Answer: (d).**

---

**Q13.** Which pair of traversals is NOT enough to reconstruct a general binary tree uniquely?
(a) Pre + In (b) In + Post (c) Pre + Post (d) Level + In

**Answer: (c).**

---

**Q14.** In a binary tree with 50 nodes, the number of NULL child pointers is:
(a) 49 (b) 50 (c) 51 (d) 100

**Answer: (c).**

---

**Q15.** Which is TRUE of a max-heap?
(a) Inorder gives sorted order (b) The minimum is always a leaf (c) Left child < right child always (d) It must be a perfect binary tree

**Answer: (b).** With distinct keys, every internal node is larger than its children, so the minimum can't be an internal node: it must be a leaf.

---

**Practice questions:** [2.12 Trees BST AVL Heaps](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.12_Trees_BST_AVL_Heaps.md)
