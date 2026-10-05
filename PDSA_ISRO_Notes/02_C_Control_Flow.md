# 02. C Control Flow: if, switch, Loops, break, continue, goto

> **The single most important idea.** In C, **any expression can be a condition**: zero means false, everything else means true. Combine that with stray semicolons, missing braces, and switch fall-through, and you have 90% of the control-flow traps in exams.

---

## 1. if, if-else, else-if ladder

```c
if (condition)
    statement;            // only ONE statement belongs to the if without braces

if (condition) {
    s1; s2;               // braces for several statements
} else {
    s3;
}

if (marks >= 90)      grade = 'A';
else if (marks >= 75) grade = 'B';
else                  grade = 'C';
```

### Any expression is a condition

```c
if (-5)      printf("yes");     // non-zero → true → prints yes
if (0)       printf("no");      // never prints
if (a = 10)  printf("set");     // assignment! a becomes 10, value 10 → true
if (a == 10) printf("equal");   // comparison
```

> **Trap.** `if (a = 0)` assigns 0 to a and is **always false**. Writing `=` instead of `==` is a classic bug (and exam question).

### The stray semicolon

```c
if (x == 5);          // the ; is an EMPTY statement: it IS the if-body
    printf("hi");     // runs ALWAYS, regardless of x
```

The compiler won't complain. Same for loops (section 3).

### Dangling else

An `else` attaches to the **nearest unmatched `if`**, no matter how the code is indented.

```c
if (a > 0)
    if (b > 0)
        printf("both positive");
else                                  // looks like it belongs to the outer if...
    printf("a not positive");         // ...but it belongs to if (b > 0)!
```

With a = 5, b = −1: prints "a not positive" (misleading text, correct C behaviour). With a = −5: prints **nothing**. Use braces to bind it to the outer `if`.

---

## 2. The conditional operator

```c
max = (a > b) ? a : b;
```

It's an **expression** (it has a value), so it can be used inside other expressions, unlike `if`. Right-associative when chained.

---

## 3. Loops

### 3.1 The three loops

```c
// while: test first
i = 0;
while (i < 3) { printf("%d ", i); i++; }       // 0 1 2

// do-while: body first, test after
i = 0;
do { printf("%d ", i); i++; } while (i < 3);   // 0 1 2   (note the ; after while)

// for: init; test; update
for (i = 0; i < 3; i++) printf("%d ", i);      // 0 1 2
```

| Loop | Condition checked | Minimum body executions |
|---|---|---|
| `while` | Before | **0** |
| `for` | Before | **0** |
| `do-while` | **After** | **1** |

> **Most tested fact:** `do-while` always runs its body **at least once**.

```c
int i = 10;
do { printf("%d", i); } while (i < 5);   // prints 10 once
while (i < 5) { printf("%d", i); }       // prints nothing
```

### 3.2 for-loop details

- All three parts are optional: `for (;;)` is an infinite loop.
- The **comma operator** allows several init/update expressions: `for (i = 0, j = 10; i < j; i++, j--)`.
- Execution order: **init (once) → test → body → update → test → body → update → ...**

### 3.3 Semicolon after a loop header

```c
for (i = 5; i <= 10; i++);     // empty body; loop just runs i up to 11
    printf("%d", i);            // runs once, prints 11
```

```c
while (i < 10);                 // if i < 10, this is an INFINITE loop (empty body, i never changes)
```

### 3.4 Counting iterations

- `for (i = a; i < b; i++)` → **b − a** iterations (if b > a).
- `for (i = a; i <= b; i++)` → **b − a + 1**.
- `for (i = 0; i < n; i += k)` → **⌈n/k⌉**.
- `for (i = 1; i < n; i *= 2)` → **⌈log₂ n⌉**.
- Nested `for i in 0..n−1, for j in 0..n−1` → **n²**.
- Nested `for i in 0..n−1, for j in i..n−1` → **n(n+1)/2**.
- `while (n--)` starting from n → runs **n** times; afterwards n = −1.
- `while (--n)` starting from n → runs **n − 1** times; afterwards n = 0.

### 3.5 Loop counter overflow

On a system with 16-bit `int` (max 32767):
```c
int i = 1;
while (i <= 32767) i++;      // after 32767, i+1 wraps to −32768, still ≤ 32767 → infinite loop
```
Same idea with `char c; for (c = 0; c < 200; c++)` when char is signed (max 127): **infinite loop**.

### 3.6 Floating-point counters and comparisons

```c
float f = 0.1;
if (f == 0.1) printf("equal"); else printf("not equal");    // prints "not equal"
```
`0.1` is a **double** constant; `f` stores a less precise float version. They differ. Never compare floats with `==`; use a tolerance.

---

## 4. break and continue

- **break:** leave the **nearest enclosing** loop **or switch** immediately.
- **continue:** skip the rest of **this iteration**, go to the next one.
  - In a `for` loop, the **update expression still runs** before the test.
  - In a `while`/`do-while`, control goes straight to the condition test (so if the increment was after the `continue`, it's skipped: possible infinite loop!).

```c
for (i = 1; i <= 5; i++) {
    if (i == 3) continue;    // skip 3
    if (i == 5) break;       // stop at 5
    printf("%d ", i);
}
// prints: 1 2 4
```

```c
int i = 0;
while (i < 5) {
    if (i == 2) continue;    // i stays 2 forever → INFINITE LOOP
    printf("%d", i);
    i++;
}
```

> **Trap.** `break` inside a `switch` that's inside a loop exits only the **switch**, not the loop.

---

## 5. switch

```c
switch (expression) {           // must be an integer type (int, char, enum)
    case 1:  printf("one");  break;
    case 2:  printf("two");  break;
    case 'A': printf("A");   break;
    default: printf("other");
}
```

### Rules

1. The expression must be **integral** (int, char, short, long, enum). **Not float, not double, not a string.**
2. Case labels must be **constant expressions** known at compile time: `case 3 + 4:` OK; `case x:` (variable) ✗ error.
3. **Duplicate case values** → compile error.
4. `default` is optional and can appear **anywhere**; it runs when no case matches.
5. Statements **before the first case** are never executed.

### Fall-through (the big one)

Without `break`, execution **continues into the next case's statements**, without checking their labels.

```c
int x = 2;
switch (x) {
    case 1: printf("1 ");
    case 2: printf("2 ");      // match: start here
    case 3: printf("3 ");      // falls through
    default: printf("D ");     // falls through
}
// prints: 2 3 D
```

### Grouping cases

```c
case 'a': case 'e': case 'i': case 'o': case 'u':
    printf("vowel"); break;
```

### default in the middle

```c
switch (5) {
    default: printf("D ");
    case 1:  printf("1 ");
    case 2:  printf("2 "); break;
}
// 5 matches nothing → jump to default → falls through: "D 1 2 "
```

### switch vs if-else

- `switch` can be compiled into a **jump table**: near-constant-time dispatch when there are many dense cases.
- `if-else` can test **ranges, floats, complex conditions**; `switch` tests only exact integral matches.

---

## 6. goto

```c
for (i = 0; i < n; i++)
    for (j = 0; j < n; j++)
        if (a[i][j] == key) goto found;
printf("not found");
found:
    printf("found");
```

Jumps to a label **in the same function**. Usually discouraged (spaghetti code), but breaking out of deeply nested loops is the accepted use.

---

## 7. Exam traps

1. Zero = false, non-zero = true; `if (a = 5)` is always true.
2. A `;` right after `if (...)`, `for (...)` or `while (...)` makes an empty body.
3. Without braces, only one statement belongs to if/loop.
4. `else` binds to the nearest unmatched `if`.
5. `do-while` runs at least once.
6. `continue` in `for` still runs the update; in `while` it may cause an infinite loop.
7. `break` in a switch inside a loop exits only the switch.
8. switch: integral only, constant labels, unique labels, **fall-through** without break.
9. Comparing a float to a double literal with `==` usually fails.
10. Loop counters can overflow and wrap.

---

## 8. Practice questions

**Q1.** Output?
```c
int i;
for (i = 0; i < 10; i += 3) printf("%d ", i);
```
(a) 0 3 6 9 (b) 0 3 6 (c) 3 6 9 (d) 0 3 6 9 12

**Answer: (a).**

---

**Q2.** Output?
```c
int x = 0;
if (x = 0) printf("zero");
else printf("nonzero");
```
(a) zero (b) nonzero (c) error (d) nothing

**Answer: (b).** The assignment's value is 0 → false.

---

**Q3.** How many times is "Hi" printed?
```c
int n = 5;
while (n--) printf("Hi");
```
(a) 4 (b) 5 (c) 6 (d) infinite

**Answer: (b).** Tests 5, 4, 3, 2, 1 (true), then 0 (false).

---

**Q4.** How many times is "Hi" printed?
```c
int n = 5;
while (--n) printf("Hi");
```
(a) 4 (b) 5 (c) 6 (d) infinite

**Answer: (a).** Tests 4, 3, 2, 1 (true), then 0.

---

**Q5.** Output?
```c
int i;
for (i = 1; i <= 5; i++) {
    if (i % 2) continue;
    printf("%d ", i);
}
```
(a) 1 3 5 (b) 2 4 (c) 1 2 3 4 5 (d) nothing

**Answer: (b).** Odd i → `i % 2` = 1 → continue.

---

**Q6.** Output (GATE 2015 style)?
```c
int i, j, k = 0;
j = 2 * 3 / 4 + 2.0 / 5 + 8 / 5;
k -= --j;
for (i = 0; i < 5; i++) {
    switch (i + k) {
        case 1:
        case 2: printf("%d", i + k);
        case 3: printf("%d", i + k);
        default: printf("%d", i + k);
    }
}
```
How many printf calls execute?
(a) 8 (b) 9 (c) 10 (d) 11

**Answer: (c).** j = 1 + 0.4 + 1 = 2.4 → 2 (int). `--j` → 1, k = −1. i + k = −1, 0, 1, 2, 3.
- −1 and 0: default only → 1 + 1.
- 1: case 1 → case 2 print, case 3 print, default print → 3.
- 2: case 2, 3, default → 3.
- 3: case 3, default → 2.
- Total 1 + 1 + 3 + 3 + 2 = **10**.

---

**Q7.** Output?
```c
char ch = 'B';
switch (ch) {
    case 'A': printf("A");
    case 'B': printf("B");
    case 'C': printf("C"); break;
    case 'D': printf("D");
}
```
(a) B (b) BC (c) BCD (d) ABCD

**Answer: (b).**

---

**Q8.** Which switch statement is illegal?
(a) `switch (x) { case 1+2: ... }` (b) `switch (c) { case 'z': ... }` (c) `switch (f) { case 1.5: ... }` with float f (d) `switch (x) { default: ... }`

**Answer: (c).** Floats aren't allowed.

---

**Q9.** Output?
```c
int a = 5, b = -1;
if (a > 0)
    if (b > 0) printf("X");
else printf("Y");
```
(a) X (b) Y (c) nothing (d) XY

**Answer: (b).** else binds to `if (b > 0)`.

---

**Q10.** Output for a = −5, b = 3 with the same code as Q9?
(a) X (b) Y (c) nothing (d) error

**Answer: (c).** The outer `if` fails, and the else belongs to the inner if, which never runs.

---

**Q11.** Output?
```c
int i = 0;
do {
    printf("%d", i);
} while (i++ < 2);
```
(a) 012 (b) 01 (c) 0123 (d) 0

**Answer: (a).** Print 0, test 0 < 2 (i = 1); print 1, test 1 < 2 (i = 2); print 2, test 2 < 2 false (i = 3).

---

**Q12.** How many times does the inner statement execute?
```c
for (i = 0; i < n; i++)
    for (j = i; j < n; j++)
        count++;
```
(a) n² (b) n(n − 1)/2 (c) n(n + 1)/2 (d) n

**Answer: (c).** n + (n − 1) + ... + 1.

---

**Q13.** Output?
```c
int i;
for (i = 0; i < 3; i++);
printf("%d", i);
```
(a) 012 (b) 3 (c) 2 (d) 0123

**Answer: (b).**

---

**Q14.** Output?
```c
float f = 0.5;
if (f == 0.5) printf("Equal");
else printf("Not");
```
(a) Equal (b) Not (c) error (d) depends on compiler

**Answer: (a).** 0.5 = 1/2 is exactly representable in binary, so float and double values match. (Contrast with 0.1, which gives "Not".)

---

**Q15.** What does `break` do inside a `switch` nested in a `while` loop?
(a) exits the while loop (b) exits only the switch (c) exits both (d) error

**Answer: (b).**

---

**Practice questions:** [2.02 C Control Flow](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.02_C_Control_Flow.md)
