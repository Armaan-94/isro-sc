# 03. C Functions and Recursion

> **Recursion is the most-asked programming concept in ISRO papers.** A short recursive function, a small input, "what is printed / returned?". The only reliable method is to **trace it on paper** with a call tree or a stack. This chapter teaches you how, with plenty of worked traces.

---

## 1. Functions: the basics

A **function** is a named block of code that does one job. Every C program has `main`, where execution starts.

```c
int add(int x, int y);        // prototype (declaration)

int main() {
    int s = add(3, 4);        // call: 3 and 4 are ACTUAL arguments
    printf("%d", s);
}

int add(int x, int y) {       // definition: x, y are FORMAL parameters
    return x + y;
}
```

- **Calling function** pauses; the **called function** runs; control returns to the point right after the call.
- **Prototype** tells the compiler the return type and parameter types before the call, so it can check calls. **Definition** contains the body.
- **Actual** and **formal** arguments must match in **number, order, and type**. Their **names** can be the same or different: they're separate variables.
- C does **not** allow defining a function **inside** another function.
- **Library functions** (printf, strlen) vs **user-defined** functions.

### Return values

- `return expr;` sends back **one** value and ends the function immediately.
- A function may have several `return` statements (only one executes per call).
- **`void`** return type: returns nothing. `void` in the parameter list: takes no arguments.
- Old C rule: if no return type is written, it defaults to **int**. So a function returning a float **must** be declared with `float` (and prototyped), or its value gets misread as an int.
- **Only one value can be returned.** `return (a, b);` returns just **b** (comma operator). To "return" several values, pass **pointers** ([Chapter 04](04_C_Arrays_and_Pointers.md)).

---

## 2. Parameter passing: C is always call by value

When you call a function, **copies** of the argument values are made. Changing the copy doesn't change the original.

```c
void change(int b) { b = 60; }

int main() {
    int a = 30;
    change(a);
    printf("%d", a);   // 30
}
```

```c
void swap(int x, int y) { int t = x; x = y; y = t; }   // swaps the COPIES only

int main() {
    int a = 1, b = 2;
    swap(a, b);
    printf("%d %d", a, b);   // 1 2  (unchanged!)
}
```

To let a function modify the caller's variable, pass its **address** (call by address):

```c
void swap(int *x, int *y) { int t = *x; *x = *y; *y = t; }
swap(&a, &b);                // now a = 2, b = 1
```

Strictly speaking this is still call by value: the **address** is copied. C has **no** true call-by-reference (C++ does, with `int &x`).

---

## 3. Scope and lifetime (quick view)

- A variable declared inside a function is **local**: visible only there, created on each call, destroyed on return.
- Global variables are visible to all functions after their declaration.
- A **static** local variable keeps its value **between calls** and is initialised only once ([Chapter 05](05_C_Storage_Classes_Structures_Unions_Enums.md)). This matters a lot in recursion questions.

---

## 4. Recursion

### 4.1 What and why

A function is **recursive** if it calls itself. Every correct recursive function has:

1. a **base case** that returns without recursing, and
2. a **recursive case** that moves toward the base case.

Without a reachable base case → infinite recursion → **stack overflow**.

### 4.2 How it runs: the call stack

Each call gets its own **stack frame** (own parameters, own local variables, own return address). Frames are pushed on each call and popped on each return.

```c
int fact(int n) {
    if (n == 1) return 1;
    return n * fact(n - 1);
}
```

Trace `fact(4)`:

```
fact(4) = 4 * fact(3)               (waiting)
          fact(3) = 3 * fact(2)     (waiting)
                    fact(2) = 2 * fact(1)
                              fact(1) = 1        <- base case
                    fact(2) = 2 * 1 = 2
          fact(3) = 3 * 2 = 6
fact(4) = 4 * 6 = 24
```

Maximum stack depth = 4 frames.

### 4.3 Types of recursion

- **Direct:** f calls f.
- **Indirect (mutual):** f calls g, g calls f.
- **Tail recursion:** the recursive call is the **very last thing** done; nothing remains to compute after it returns. Can be optimised into a loop (C compilers *may* do this, but it's not guaranteed).
  - `return n * fact(n − 1);` is **not** tail recursive (the multiplication happens after the call).
  - Tail version: `int fact(int n, int acc) { if (n <= 1) return acc; return fact(n − 1, n * acc); }`.
- **Head recursion:** the recursive call comes first, work after it.
- **Tree recursion:** more than one recursive call per invocation (e.g. Fibonacci).

---

## 5. The tracing toolkit (with worked examples)

### 5.1 Print before vs after the recursive call

```c
void A(int n) { if (n == 0) return; printf("%d ", n); A(n - 1); }
void B(int n) { if (n == 0) return; B(n - 1); printf("%d ", n); }
```

- `A(3)` prints **3 2 1** (print on the way **down**).
- `B(3)` prints **1 2 3** (print on the way **back up**).

### 5.2 Two recursive calls around a print

```c
void f(int n) {
    if (n > 0) {
        f(n - 1);
        printf("%d ", n);
        f(n - 1);
    }
}
```

`f(1)` → 1. `f(2)` → f(1), 2, f(1) → **1 2 1**. `f(3)` → **1 2 1 3 1 2 1**.

Number of values printed for f(n) = 2ⁿ − 1 (here 7). Same structure as **Tower of Hanoi**.

### 5.3 Digit sum

```c
int ds(int n) {
    if (n == 0) return 0;
    return n % 10 + ds(n / 10);
}
```
ds(1234) = 4 + ds(123) = 4 + 3 + ds(12) = 4 + 3 + 2 + ds(1) = 4 + 3 + 2 + 1 + 0 = **10**.

### 5.4 Reverse printing of digits

```c
void r(int n) { if (n == 0) return; printf("%d", n % 10); r(n / 10); }
```
r(1234) prints **4321**.

### 5.5 GCD (Euclid)

```c
int gcd(int a, int b) { if (b == 0) return a; return gcd(b, a % b); }
```
gcd(48, 18) → gcd(18, 12) → gcd(12, 6) → gcd(6, 0) → **6**.

### 5.6 Power in O(log n)

```c
int power(int x, int n) {
    if (n == 0) return 1;
    int half = power(x, n / 2);
    if (n % 2 == 0) return half * half;
    return x * half * half;
}
```
power(2, 10): power(2, 5) → power(2, 2) → power(2, 1) → power(2, 0) = 1. Then 2·1·1 = 2, then 2·2 = 4, then 2·4·4 = 32, then 32·32 = **1024**. Only about log₂ n calls.

### 5.7 Doubling recurrence (exponential calls)

```c
int x(int n) {
    if (n < 3) return 1;
    return x(n - 1) + x(n - 1) + 1;
}
```
x(2) = 1, x(3) = 3, x(4) = 7, x(5) = 15, x(6) = **31**.
Each call makes **two** identical calls without reusing results, so the number of calls doubles per level: **O(2ⁿ)**.

### 5.8 Fibonacci and counting calls

```c
int fib(int n) { if (n <= 1) return n; return fib(n - 1) + fib(n - 2); }
```

fib(5) = **5**. Number of calls C(n) = C(n−1) + C(n−2) + 1 with C(0) = C(1) = 1:
C(2) = 3, C(3) = 5, C(4) = 9, **C(5) = 15**. Exponential growth: O(φⁿ) ≈ O(1.618ⁿ).

### 5.9 Static variables inside recursion (GATE 2007 classic)

```c
int f(int n) {
    static int r = 0;
    if (n <= 0) return 1;
    if (n > 3) { r = n; return f(n - 2) + 2; }
    return f(n - 1) + r;
}
```

`f(5)`:
- n = 5 > 3: **r = 5** (one shared r for all calls!), return f(3) + 2.
- f(3) = f(2) + r. f(2) = f(1) + r. f(1) = f(0) + r. f(0) = 1.
- With r = 5: f(1) = 6, f(2) = 11, f(3) = 16.
- f(5) = 16 + 2 = **18**.

Key insight: a `static` local is **shared by all calls**. Its value at the time each `+ r` executes is what matters (here it was set to 5 before any of them ran).

### 5.10 Recursion with a global/pass-by-pointer counter

```c
int count = 0;
void g(int n) { count++; if (n > 1) { g(n / 2); g(n / 2); } }
```
g(8): calls for 8 (1), two calls of 4, each makes two of 2, each makes two of 1. Total = 1 + 2 + 4 + 8 = **15**.

### 5.11 Recursion returning through multiple paths

```c
int m(int a, int b) {
    if (b == 0) return 0;
    if (b % 2 == 0) return m(a + a, b / 2);
    return m(a + a, b / 2) + a;
}
```
m(3, 5): b odd → m(6, 2) + 3. m(6, 2): even → m(12, 1). m(12, 1): odd → m(24, 0) + 12 = 12. So m(6, 2) = 12, m(3, 5) = 15. It computes **a × b** (Russian peasant multiplication).

---

## 6. Recursion vs iteration

| | Recursion | Iteration |
|---|---|---|
| Expressiveness | Natural for trees, divide and conquer, backtracking | Natural for simple repetition |
| Memory | O(depth) stack frames | O(1) usually |
| Speed | Call overhead | Faster |
| Risk | Stack overflow | Infinite loop |

Any recursion can be converted to iteration using an explicit **stack**.

---

## 7. Exam traps

1. C is **call by value**; modifying parameters doesn't affect the caller.
2. Only one return value; `return (a, b)` returns b.
3. Missing return type defaults to int (old C).
4. A `static` local in a recursive function is **shared** across all calls.
5. Print-before vs print-after changes the order.
6. Two recursive calls per level → exponential calls.
7. `return n * f(n − 1)` is **not** tail recursion.
8. `main` can be called recursively (legal in C).

---

## 8. Practice questions

**Q1.** Output?
```c
void junk(int i, int j) { i = i * i; j = j * j; }
int main() { int i = 5, j = 2; junk(i, j); printf("%d %d", i, j); }
```
(a) 25 4 (b) 5 2 (c) 25 2 (d) 5 4

**Answer: (b).**

---

**Q2.** Output of `f(4)`?
```c
void f(int n) { if (n == 0) return; f(n - 1); printf("%d", n); }
```
(a) 4321 (b) 1234 (c) 4 (d) 0

**Answer: (b).**

---

**Q3.** What does `fun(5)` return?
```c
int fun(int n) { if (n <= 1) return 1; return n + fun(n - 1); }
```
(a) 120 (b) 15 (c) 14 (d) 5

**Answer: (b).** 5 + 4 + 3 + 2 + 1.

---

**Q4.** How many times is `printf` executed by `f(4)`?
```c
void f(int n) { if (n > 0) { f(n - 1); printf("*"); f(n - 1); } }
```
(a) 4 (b) 8 (c) 15 (d) 16

**Answer: (c).** 2⁴ − 1.

---

**Q5.** Value of `g(5)`?
```c
int g(int n) {
    static int s = 0;
    s += n;
    if (n <= 1) return s;
    return g(n - 1);
}
```
(a) 5 (b) 15 (c) 1 (d) 14

**Answer: (b).** s accumulates 5 + 4 + 3 + 2 + 1 = 15 and is returned at n = 1.

---

**Q6.** Value of `foo(345, 10)`?
```c
int foo(int n, int r) {
    if (n > 0) return (n % r) + foo(n / r, r);
    return 0;
}
```
(a) 345 (b) 12 (c) 5 (d) 3

**Answer: (b).** Digit sum in base 10: 5 + 4 + 3.

---

**Q7.** Same function, `foo(513, 2)`?
(a) 9 (b) 8 (c) 5 (d) 2

**Answer: (d).** 513 = 1000000001₂ → two 1s.

---

**Q8.** Value of `sq(3)`?
```c
int sq(int n) { if (n == 0) return 0; return sq(n - 1) + n * n; }
```
(a) 9 (b) 14 (c) 6 (d) 27

**Answer: (b).** 1 + 4 + 9.

**Q9.** Value of `h(4)`?
```c
int h(int n) { if (n <= 1) return 1; return h(n - 1) + h(n - 2); }
```
(a) 3 (b) 5 (c) 8 (d) 4

**Answer: (b).** h(0) = h(1) = 1, h(2) = 2, h(3) = 3, h(4) = 5.

---

**Q10.** Which is tail recursive?
(a) `return n * f(n - 1);` (b) `return f(n - 1) + 1;` (c) `return f(n - 1, acc * n);` (d) `return 1 + f(n / 2);`

**Answer: (c).** Nothing remains after the call returns.

---

**Q11.** What does `mystery(3, 4)` return?
```c
int mystery(int a, int b) { if (b == 0) return 1; return a * mystery(a, b - 1); }
```
(a) 12 (b) 64 (c) 81 (d) 7

**Answer: (c).** 3⁴.

---

**Q12.** A function has the prototype missing and returns `3.7` from a function declared without a return type. The caller receives:
(a) 3.7 (b) 3 (garbage-like int interpretation is possible) (c) 4 (d) error in all compilers

**Answer: (b).** In old C the default return type is int, so 3.7 is converted to 3 on return.

---

**Q13.** Output?
```c
void p(int n) { if (n <= 0) return; printf("%d ", n); p(n - 2); printf("%d ", n); }
int main() { p(5); }
```
(a) 5 3 1 1 3 5 (b) 5 3 1 (c) 1 3 5 (d) 5 3 1 3 5

**Answer: (a).** Down: 5, 3, 1; then p(−1) returns; back up: 1, 3, 5.

---

**Q14.** How many calls are made in total by `fib(4)` (naive, base cases n ≤ 1)?
(a) 5 (b) 7 (c) 9 (d) 15

**Answer: (c).** C(4) = 9.

---

**Q15.** Mutual recursion means:
(a) a function calls itself (b) two or more functions call each other in a cycle (c) recursion without a base case (d) recursion using static variables

**Answer: (b).**

---

**Practice questions:** [2.03 C Functions and Recursion](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.03_C_Functions_and_Recursion.md)
