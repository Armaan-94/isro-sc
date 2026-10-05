# 10. Stacks: Operations, Valid Pop Sequences, Expression Conversion and Evaluation

> **Picture a stack of plates.** You can only add or remove the **top** plate. That one rule (Last In, First Out) powers expression evaluation, function calls, undo, browser back buttons, and depth-first search. Exam questions here are mechanical once you simulate the stack carefully.

---

## 1. The stack ADT

**LIFO: Last In, First Out.**

| Operation | Does | Time |
|---|---|---|
| `push(x)` | Put x on top | O(1) |
| `pop()` | Remove and return the top | O(1) |
| `peek()` / `top()` | Read the top without removing | O(1) |
| `isEmpty()` | | O(1) |
| `isFull()` (array version) | | O(1) |

### Array implementation

```c
#define N 100
int s[N], top = -1;

void push(int x) {
    if (top == N - 1) { printf("Overflow"); return; }
    s[++top] = x;
}
int pop() {
    if (top == -1) { printf("Underflow"); return -1; }
    return s[top--];
}
```

- **Overflow:** push when full (top == N − 1).
- **Underflow:** pop when empty (top == −1).

### Linked-list implementation

Push and pop at the **head** (O(1)). No fixed capacity (until memory runs out); costs an extra pointer per element.

### Two stacks in one array

Stack 1 grows from index 0 upward; stack 2 grows from index N − 1 downward. **Overflow only when top1 + 1 == top2**, so no space is wasted.

### Applications

Function calls (call stack), recursion, expression conversion/evaluation, parenthesis matching, undo/redo, browser back, DFS, backtracking, reversing a string.

---

## 2. Valid pop sequences

Elements are pushed in a **fixed order** (say 1, 2, 3, 4, 5), and you can pop at any time. Which output orders are possible?

### Method: simulate

Go through the target sequence. For each number:
- If it's on **top**, pop it.
- If it hasn't been pushed yet, push everything up to it, then pop it.
- If it's in the stack but **not on top** → **impossible**.

**Example:** push order 1, 2, 3, 4, 5. Is **5, 4, 3, 1, 2** possible?
- 5: push 1, 2, 3, 4, 5; pop 5. Stack [1, 2, 3, 4].
- 4: pop. 3: pop. Stack [1, 2] (2 on top).
- 1: it's in the stack but 2 is on top → **impossible**.

**Is 3, 5, 4, 2, 1 possible?** Push 1, 2, 3, pop 3. Push 4, 5, pop 5. Pop 4. Pop 2. Pop 1. ✓

**Is 2, 4, 3, 5, 1 possible?** Push 1, 2, pop 2. Push 3, 4, pop 4. Pop 3. Push 5, pop 5. Pop 1. ✓

### Quick rule

For push order 1, 2, ..., n, an output sequence is **impossible** exactly when some number k is followed (later, not necessarily adjacent) by two **smaller** numbers that appear in **increasing** order: k ... x ... y with x < y < k. (Once k is popped, all smaller unpopped numbers are stuck in the stack in increasing order from bottom to top, so they must come out in **decreasing** order.) In 5, 4, 3, 1, 2 we have 3 ... 1 ... 2 with 1 < 2 < 3: invalid.

### How many valid sequences?

For n distinct elements, the number of valid pop sequences is the **Catalan number**:

```
C(n) = (2n)! / ((n + 1)! n!)
```

n = 1: 1, n = 2: 2, **n = 3: 5**, **n = 4: 14**, n = 5: 42.

(Same number counts binary trees with n nodes and valid parenthesisations.)

---

## 3. Expression notations

| Notation | Operator position | (A + B) * C |
|---|---|---|
| **Infix** | Between operands | (A + B) * C |
| **Prefix** (Polish) | Before | * + A B C |
| **Postfix** (Reverse Polish) | After | A B + C * |

Prefix and postfix need **no parentheses** and **no precedence rules** when evaluating: perfect for machines and compilers.

### Precedence and associativity (for conversion)

| Operator | Precedence | Associativity |
|---|---|---|
| `^` (power) | Highest | **Right to left** |
| `* / %` | Middle | Left to right |
| `+ -` | Lowest | Left to right |

---

## 4. Infix to postfix (the operator-stack algorithm)

Scan the infix expression left to right:

1. **Operand** → write it to the output.
2. **`(`** → push.
3. **`)`** → pop and output until `(`; discard both parentheses.
4. **Operator op** → while the stack top is an operator with **higher precedence**, or **equal precedence and op is left-associative**, pop it to the output. Then push op.
5. At the end, pop everything to the output.

### Worked example 1: A + B * C − D

| Symbol | Action | Stack | Output |
|---|---|---|---|
| A | output | | A |
| + | push | + | A |
| B | output | + | A B |
| * | * > +, push | + * | A B |
| C | output | + * | A B C |
| − | pop * (higher), pop + (equal, left-assoc), push − | − | A B C * + |
| D | output | − | A B C * + D |
| end | pop − | | **A B C * + D −** |

### Worked example 2: (A + B) * (C − D) / E

| Symbol | Stack | Output |
|---|---|---|
| ( | ( | |
| A | ( | A |
| + | ( + | A |
| B | ( + | A B |
| ) | | A B + |
| * | * | A B + |
| ( | * ( | A B + |
| C | * ( | A B + C |
| − | * ( − | A B + C |
| D | * ( − | A B + C D |
| ) | * | A B + C D − |
| / | pop * (equal, left-assoc), push / | A B + C D − * |
| E | / | A B + C D − * E |
| end | | **A B + C D − * E /** |

### Worked example 3: A ^ B ^ C (right-associative)

`^` is right-associative, so an incoming `^` does **not** pop an equal `^`:
- A → out. ^ → push. B → out. ^ → equal and right-assoc → **don't pop**, push. C → out. End: pop ^, pop ^.
- **A B C ^ ^** (meaning A ^ (B ^ C)).

### Worked example 4: A * (B + C) − D / E

→ **A B C + * D E / −**.

### Manual shortcut (fully parenthesise)

Put brackets around every operation according to precedence, then move each operator to just after its closing bracket (postfix) or just before its opening bracket (prefix), and drop the brackets.

A + B * C → (A + (B * C)) → postfix A B C * +, prefix + A * B C.

---

## 5. Infix to prefix

Method: **reverse** the infix (swapping `(` and `)`), convert to postfix (treating equal-precedence operators slightly differently: pop only on strictly higher precedence for left-associative ops), then **reverse** the result. Or use the bracket shortcut, which is usually faster by hand.

| Infix | Prefix |
|---|---|
| A + B * C | + A * B C |
| (A + B) * C | * + A B C |
| A − B + C | + − A B C |
| (A + B) * (C − D) | * + A B − C D |
| A * B + C / D | + * A B / C D |

---

## 6. Evaluating postfix

Scan left to right:
- **Operand** → push.
- **Operator** → pop **two**: the first popped is the **right** operand, the second the **left**. Compute **left op right**, push the result.
- At the end, the single stack value is the answer.

### Example: 6 2 3 * + 5 −

| Token | Stack after |
|---|---|
| 6 | 6 |
| 2 | 6 2 |
| 3 | 6 2 3 |
| * | 6 6 (2 × 3) |
| + | 12 |
| 5 | 12 5 |
| − | 7 (12 − 5) |

Result **7**.

### Example: 5 3 + 8 2 − *

5 + 3 = 8; 8 − 2 = 6; 8 × 6 = **48**.

### Example with order sensitivity: 8 2 / 3 −

8 / 2 = 4 (8 is left, 2 is right); 4 − 3 = **1**. (Popping order matters for − and /.)

### Maximum stack size

Count the height while evaluating. For `6 2 3 * + 5 −` the max is **3**.

## 7. Evaluating prefix

Scan **right to left**: operand → push; operator → pop two: the **first popped is the left** operand, the second is the right; push the result.

**Example: + * 2 3 4**
- 4 → [4]. 3 → [4, 3]. 2 → [4, 3, 2].
- * → pop 2 (left), pop 3 (right): 2 × 3 = 6 → [4, 6].
- + → pop 6 (left), pop 4 (right): 6 + 4 = **10**.

**Example: − / 8 2 3** → 8/2 = 4, 4 − 3 = **1**.

---

## 8. Parenthesis matching

Scan: push every opening bracket; on a closing bracket, the top must be the **matching** opening bracket (pop it); at the end the stack must be **empty**.

`{[()()]}` ✓. `([)]` ✗ (on `)`, top is `[`). `((()` ✗ (stack not empty).

---

## 9. Recursion and the call stack

Each function call pushes an **activation record** (parameters, locals, return address); returning pops it. Recursion depth = maximum number of frames at once. (Details in [Chapter 03](03_C_Functions_and_Recursion.md).)

**Print order rule:** printing **before** the recursive call gives the call order; printing **after** gives the **reverse**.

```c
void f(int x) { if (x > 0) { f(x - 1); printf("%d", x); } }   // f(4) prints 1234
void g(int x) { if (x > 0) { printf("%d", x); g(x - 1); } }   // g(4) prints 4321
```

---

## 10. Tower of Hanoi

Move n disks from peg A to C using B, one disk at a time, never placing a larger disk on a smaller one.

```c
void hanoi(int n, char from, char to, char via) {
    if (n == 0) return;
    hanoi(n - 1, from, via, to);
    printf("Move disk %d %c->%c\n", n, from, to);
    hanoi(n - 1, via, to, from);
}
```

- Moves: T(n) = 2T(n − 1) + 1, T(0) = 0 → **T(n) = 2ⁿ − 1**. (n = 3: 7; n = 5: 31; n = 10: 1023.)
- Total function calls (including the n = 0 calls) = **2^(n+1) − 1**.
- The largest disk moves exactly once; disk k (1 = smallest) moves 2^(n−k) times.

---

## 11. Fibonacci and Ackermann (recall)

- Naive recursive Fibonacci is **exponential**; with memoisation or iteration it's O(n).
- **Ackermann's function** A(m, n) is **computable (total recursive) but not primitive recursive**: it grows faster than any primitive recursive function. A(0, n) = n + 1; A(1, n) = n + 2; A(2, n) = 2n + 3; A(3, n) = 2^(n+3) − 3.

---

## 12. Exam traps

1. Simulate pop sequences; "in the stack but not on top" = impossible.
2. Valid pop sequences for n items = Catalan number (n = 3: 5, n = 4: 14).
3. `^` is right-associative: don't pop an equal `^`.
4. Postfix evaluation: first pop = right operand.
5. Prefix evaluation: scan right to left; first pop = left operand.
6. Hanoi: 2ⁿ − 1 moves.
7. Print after the recursive call → reversed order.

---

## 13. Practice questions

**Q1.** Push order 1, 2, 3, 4, 5. Which pop sequence is impossible?
(a) 3, 5, 4, 2, 1 (b) 2, 4, 3, 5, 1 (c) 4, 3, 5, 2, 1 (d) 5, 4, 3, 1, 2

**Answer: (d).**

---

**Q2.** Push order a, b, c. How many distinct pop sequences are possible?
(a) 3 (b) 5 (c) 6 (d) 4

**Answer: (b).** All except c, a, b.

---

**Q3.** Postfix of `A + B * C − D`?
(a) A B C * + D − (b) A B + C * D − (c) A B C + * D − (d) A B * C + D −

**Answer: (a).**

---

**Q4.** Postfix of `(A + B) * C − (D − E) * (F + G)`?
(a) A B + C * D E − F G + * − (b) A B C + * D E F G − + * − (c) A B + C D E − * F G + * − (d) A B + C * D E F G + − * −

**Answer: (a).**

---

**Q5.** Evaluate postfix `6 2 3 * + 5 −`.
(a) 5 (b) 7 (c) 13 (d) 19

**Answer: (b).**

---

**Q6.** Evaluate postfix `2 3 1 * + 9 −`.
(a) −4 (b) 4 (c) 14 (d) −14

**Answer: (a).** 3 × 1 = 3; 2 + 3 = 5; 5 − 9 = −4.

---

**Q7.** Evaluate prefix `− + 7 * 4 5 + 2 0`.
(a) 25 (b) 27 (c) 29 (d) 23

**Answer: (a).** * 4 5 = 20; + 7 20 = 27; + 2 0 = 2; − 27 2 = 25.

---

**Q8.** Prefix form of `(A + B) * (C − D)`?
(a) * + A B − C D (b) + * A B − C D (c) A B + C D − * (d) * A + B − C D

**Answer: (a).**

---

**Q9.** Postfix of `A ^ B ^ C`?
(a) A B ^ C ^ (b) A B C ^ ^ (c) ^ ^ A B C (d) A ^ B C ^

**Answer: (b).**

---

**Q10.** Minimum moves for Tower of Hanoi with 6 disks?
(a) 32 (b) 63 (c) 64 (d) 127

**Answer: (b).**

---

**Q11.** Maximum stack depth while evaluating postfix `2 3 4 * 5 + −`?
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (b).** 2 → [2], 3 → [2, 3], 4 → [2, 3, 4] (depth 3), * → [2, 12], 5 → [2, 12, 5] (depth 3), + → [2, 17], − → [−15]. Max 3.

---

**Q12.** Which application does NOT naturally use a stack?
(a) Parenthesis matching (b) Function calls (c) Breadth-first search (d) Undo

**Answer: (c).** BFS uses a queue.

---

**Q13.** Value returned by `fun(4, 3)`?
```c
int fun(int x, int y) { if (x == 0) return y; return fun(x - 1, x + y); }
```
(a) 7 (b) 10 (c) 13 (d) 12

**Answer: (c).** (4, 3) → (3, 7) → (2, 10) → (1, 12) → (0, 13) → 13.

---

**Q14.** A stack is implemented in an array of size N with top starting at −1. The condition for overflow is:
(a) top == 0 (b) top == N (c) top == N − 1 (d) top == −1

**Answer: (c).**

---

**Practice questions:** [2.10 Stacks Expressions Recursion](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.10_Stacks_Expressions_Recursion.md)
