# 11. Queues and Linked Lists

> **Queue = a line at a ticket counter.** First come, first served. **Linked list = a treasure hunt**: each clue (node) tells you where the next one is. Queues are about *order of service*; linked lists are about *flexible memory*. Exams test circular-queue conditions, linked-list pointer manipulation, and traces of recursive list functions.

---

## Part A: Queues

### 1. The queue ADT

**FIFO: First In, First Out.**
- **enqueue(x)** at the **rear**.
- **dequeue()** from the **front**.
- Both **O(1)**.

Applications: CPU/printer scheduling, buffering between producer and consumer, BFS, handling requests in servers, keyboard buffers.

### 2. Linear array queue and its problem

```c
int q[N], front = -1, rear = -1;
enqueue: if (rear == N - 1) overflow; else { if (front == -1) front = 0; q[++rear] = x; }
dequeue: if (front == -1 || front > rear) underflow; else x = q[front++];
```

After many enqueues and dequeues, `front` moves right and the slots before it are **wasted**: the queue can report "full" (rear == N − 1) while most of the array is empty.

### 3. Circular queue

Treat the array as a ring using **modulo N**:

```
enqueue: rear  = (rear + 1) % N
dequeue: front = (front + 1) % N
```

#### Version 1: sentinel −1 for empty (front = rear = −1 initially)

- **Empty:** front == −1.
- **Full:** (front == 0 && rear == N − 1) **or** front == rear + 1. Equivalently **(rear + 1) % N == front**.
- After dequeuing the last element, reset front = rear = −1.
- Capacity: **N** elements.

#### Version 2: sacrifice one slot (front and rear start at 0)

- **Empty:** front == rear.
- **Full:** (rear + 1) % N == front.
- Capacity: **N − 1** elements (one slot always kept empty to tell full from empty).
- Number of elements = (rear − front + N) % N.

#### Version 3: keep a count variable

Empty when count == 0, full when count == N. Simplest to reason about.

> **Trap.** With only front and rear (and no sentinel/count), "front == rear" can't distinguish **empty** from **full**. That's why one of the three fixes above is needed.

#### Trace (Version 1, N = 5)

| Operation | front | rear | Contents (by index 0..4) |
|---|---|---|---|
| start | −1 | −1 | _ _ _ _ _ |
| enq 10 | 0 | 0 | 10 _ _ _ _ |
| enq 20 | 0 | 1 | 10 20 _ _ _ |
| enq 30 | 0 | 2 | 10 20 30 _ _ |
| deq (10) | 1 | 2 | _ 20 30 _ _ |
| deq (20) | 2 | 2 | _ _ 30 _ _ |
| enq 40 | 2 | 3 | _ _ 30 40 _ |
| enq 50 | 2 | 4 | _ _ 30 40 50 |
| enq 60 | 2 | **0** (wrap) | 60 _ 30 40 50 |
| enq 70 | 2 | 1 | 60 70 30 40 50 → now front == rear + 1: **full** |

### 4. Deque (double-ended queue)

Insert and delete at **both** ends.
- **Input-restricted deque:** insert at one end only, delete at both.
- **Output-restricted deque:** delete at one end only, insert at both.

A deque can act as both a stack and a queue.

### 5. Priority queue

Each element has a **priority**; dequeue removes the **highest-priority** element (ties often FIFO).

| Implementation | Insert | Delete-max/min |
|---|---|---|
| Unsorted array/list | O(1) | O(n) |
| Sorted array/list | O(n) | O(1) |
| **Binary heap** | **O(log n)** | **O(log n)** |

Heaps are the standard choice ([Chapter 12](12_Trees_BST_AVL_Heaps.md)).

### 6. Queue/stack conversions

- **Queue using two stacks:** enqueue pushes onto S1. Dequeue: if S2 is empty, pop everything from S1 and push onto S2 (reversing order), then pop S2. Amortised **O(1)** per operation.
- **Stack using two queues:** make either push or pop O(n) by moving elements between queues.
- **Reverse a queue with a stack:** dequeue all into a stack, then pop all back into the queue → order reversed.

---

## Part B: Linked lists

### 7. The idea

A **node** holds **data** and a **pointer to the next node**. A **head** pointer points to the first node; the last node's `next` is **NULL**.

```c
struct Node { int data; struct Node *next; };
```

```
head → [10|•] → [20|•] → [30|NULL]
```

### 8. Linked list vs array

| | Array | Linked list |
|---|---|---|
| Size | Fixed (static) | **Grows/shrinks dynamically** |
| Memory | Contiguous | Scattered; **extra pointer per node** |
| Access i-th element | **O(1)** | O(n) (must walk) |
| Insert/delete at front | O(n) (shift) | **O(1)** |
| Insert/delete in the middle (given position pointer) | O(n) (shift) | **O(1)** link change (but O(n) to find the spot) |
| Cache performance | **Good** | Poor |
| Binary search | Yes | **Not efficiently** |

### 9. Core operations (singly linked)

**Traverse:**
```c
for (Node *p = head; p != NULL; p = p->next) printf("%d ", p->data);
```

**Insert at head, O(1):**
```c
Node *n = malloc(sizeof(Node)); n->data = x;
n->next = head;
head = n;
```

**Insert after a given node p, O(1):**
```c
n->next = p->next;
p->next = n;        // order matters: do it the other way and you lose the rest of the list
```

**Insert at tail:** O(n) without a tail pointer (walk to the end), O(1) with one.

**Delete the head, O(1):**
```c
Node *t = head; head = head->next; free(t);
```

**Delete the node after p:**
```c
Node *t = p->next; p->next = t->next; free(t);
```

To delete a node you usually need its **predecessor** (singly linked). Deleting the last node is O(n) even with a tail pointer (you need the second-last).

**Search:** O(n).

**Reverse (iterative), O(n), O(1) extra space:**
```c
Node *prev = NULL, *curr = head, *next;
while (curr != NULL) {
    next = curr->next;     // save
    curr->next = prev;     // reverse the link
    prev = curr;           // advance
    curr = next;
}
head = prev;               // prev is the old last node = new head
```

**Reverse (recursive):**
```c
Node* rev(Node *cur, Node *prev) {
    if (cur == NULL) return prev;
    Node *nxt = cur->next;
    cur->next = prev;
    return rev(nxt, cur);
}
head = rev(head, NULL);
```

**Middle node:** slow pointer moves 1 step, fast pointer moves 2 steps; when fast reaches the end, slow is at the middle.

**Cycle detection (Floyd's tortoise and hare):** slow 1 step, fast 2 steps; if they ever meet, there's a cycle. O(n) time, O(1) space.

**Merge two sorted lists:** O(m + n).

### 10. Variants

| Variant | Structure | Pros | Cons |
|---|---|---|---|
| **Singly linked** | one `next` | Simple, least memory | Forward only; deleting needs the predecessor |
| **Doubly linked** | `prev` and `next` | **Backward traversal**; delete a node given only a pointer to it in O(1) | Extra pointer per node; more links to update |
| **Circular singly** | last → first (no NULL) | Any node can be a start; good for round-robin | Must stop explicitly (track the start) |
| **Circular doubly** | both | Most flexible (O(1) access to both ends from head) | Most memory |
| **Header (dummy) node** | an always-present node before the first real node | No special cases for an empty list or insert/delete at the front; can store the length | One extra node |

**Doubly linked insert of n after p** (4 pointer updates):
```c
n->next = p->next;
n->prev = p;
if (p->next) p->next->prev = n;
p->next = n;
```

**Doubly linked delete of node p** (2 pointer updates):
```c
p->prev->next = p->next;
p->next->prev = p->prev;
free(p);
```

**Circular list tip:** keep a pointer to the **last** node; then last->next is the first, so insertion at both front and back is O(1).

### 11. Time complexity summary

| Operation | Singly (head only) | Singly (head + tail) | Doubly (head + tail) |
|---|---|---|---|
| Insert at front | O(1) | O(1) | O(1) |
| Insert at end | O(n) | O(1) | O(1) |
| Delete at front | O(1) | O(1) | O(1) |
| Delete at end | O(n) | O(n) | O(1) |
| Search | O(n) | O(n) | O(n) |
| Delete a given node (pointer known) | O(n) (need predecessor) | O(n) | **O(1)** |

---

## 12. Exam traps

1. Circular queue full: (rear + 1) % N == front (or front == 0 && rear == N − 1 in the −1 sentinel version).
2. "Sacrifice one slot" version stores at most N − 1 elements.
3. Priority queue → heap → O(log n).
4. Insert after p: set `n->next` **before** changing `p->next`.
5. Iterative reversal ends with `head = prev`.
6. Linked lists: no random access, no efficient binary search.
7. Deleting the last node of a singly linked list is O(n) even with a tail pointer.
8. Circular lists need an explicit stop condition.
9. Reassigning a function's local copy of `head` doesn't change the caller's head.

---

## 13. Practice questions

**Q1.** A circular queue (array of size 6, one slot sacrificed) has front = 4 and rear = 2. How many elements does it hold?
(a) 2 (b) 4 (c) 6 (d) 3

**Answer: (b).** (rear − front + N) % N = (2 − 4 + 6) % 6 = 4.

---

**Q2.** In the "sacrifice one slot" circular queue of size N, the full condition is:
(a) front == rear (b) (rear + 1) % N == front (c) rear == N − 1 (d) front == 0

**Answer: (b).**

---

**Q3.** Which structure gives O(log n) insert and O(log n) delete-max?
(a) Sorted array (b) Unsorted linked list (c) Binary heap (d) Stack

**Answer: (c).**

---

**Q4.** Output when called with the head of 1 → 2 → 3 → 4 → 5 → 6?
```c
void fun(Node *s) {
    if (s == NULL) return;
    printf("%d ", s->data);
    if (s->next != NULL) fun(s->next->next);
    printf("%d ", s->data);
}
```
(a) 1 4 6 6 4 1 (b) 1 3 5 1 3 5 (c) 1 2 3 5 (d) 1 3 5 5 3 1

**Answer: (d).** fun(1) prints 1 → fun(3) prints 3 → fun(5) prints 5 → fun(NULL) (6's next is NULL) → back: 5, 3, 1.

---

**Q5.** Output for the list 1 → 2 → 3?
```c
void f(Node *h) { if (h == NULL) return; f(h->next); printf("%d", h->data); }
```
(a) 123 (b) 321 (c) 1 (d) 3

**Answer: (b).**

---

**Q6.** What completes the iterative reverse function after the loop?
(a) head = curr; (b) head = next; (c) head = prev; (d) head = NULL;

**Answer: (c).**

---

**Q7.** To insert node n after node p in a singly linked list, which order is correct?
(a) p->next = n; n->next = p->next; (b) n->next = p->next; p->next = n; (c) n->next = p; p->next = n; (d) p->next = n->next; n->next = p;

**Answer: (b).** Option (a) makes n point to itself and loses the rest.

---

**Q8.** Deleting a node from a doubly linked list, given a pointer to that node, takes:
(a) O(1) (b) O(log n) (c) O(n) (d) O(n log n)

**Answer: (a).**

---

**Q9.** Which operation is O(n) for a singly linked list even when both head and tail pointers are kept?
(a) Insert at front (b) Insert at end (c) Delete at front (d) Delete at end

**Answer: (d).** You need the second-last node.

---

**Q10.** Floyd's cycle detection uses:
(a) a hash table (b) two pointers moving at different speeds (c) recursion (d) sorting

**Answer: (b).**

---

**Q11.** Enqueue 1, 2, 3 into a queue, then dequeue everything into a stack, then pop everything back into the queue. The queue (front to rear) is now:
(a) 1 2 3 (b) 3 2 1 (c) 2 3 1 (d) 1 3 2

**Answer: (b).**

---

**Q12.** A queue is implemented with two stacks (S1 for enqueue, S2 for dequeue, transferring only when S2 is empty). The amortised cost per operation is:
(a) O(1) (b) O(log n) (c) O(n) (d) O(n²)

**Answer: (a).** Each element moves from S1 to S2 at most once.

---

**Q13.** A header linked list's main advantage is:
(a) O(1) random access (b) no special cases for an empty list or front insertion/deletion (c) less memory (d) no next pointers

**Answer: (b).**

---

**Q14.** In a singly linked list with n nodes, the number of NULL pointers is:
(a) 0 (b) 1 (c) n (d) n − 1

**Answer: (b).** Only the last node's next.

---

**Practice questions:** [2.11 Queues and Linked Lists](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.11_Queues_and_Linked_Lists.md)
