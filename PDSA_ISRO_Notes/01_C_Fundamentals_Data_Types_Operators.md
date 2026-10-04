# 01. C Fundamentals: Data Types and Operators

> **How exam C questions really work.** They show you 4 to 6 lines of code and ask for the output. The traps are almost always the same handful: **integer division, short-circuit evaluation, pre vs post increment, precedence, overflow, and `sizeof`**. Master those and most C output questions become mechanical.

---

## 1. The shape of a C program

```c
#include <stdio.h>          // preprocessor directive: bring in printf's declaration

int main(void) {            // execution always starts at main
    int age = 21;           // declaration + initialisation
    printf("%d\n", age);    // statement, ends with ;
    return 0;               // 0 tells the OS "success"
}
```

- Every statement ends with `;`. C is **free-form** (spacing and line breaks don't matter).
- **Comments:** `/* ... */` (cannot be nested) and `// ...` (C99 onwards).
- **main** is the single entry point. Forms: `int main(void)`, `int main(int argc, char *argv[])` (argc = number of command-line arguments, argv = the argument strings; argv[0] is the program name).

### Identifiers (names)

- Letters, digits, underscore. **Must not start with a digit.** Can't be a keyword (`int`, `while`, ...). Case-sensitive (`Sum` ≠ `sum`).
- Valid: `_count`, `total2`, `MAX_SIZE`. Invalid: `2total`, `my-var`, `float`.

### Variables vs constants

- **Variable:** a named memory location whose value can change.
- **Constant:** a value that doesn't change: literals (`42`, `3.14`, `'A'`, `"hi"`), `const int N = 10;`, `#define N 10`.

### Garbage values

An **uninitialised local variable** holds whatever bits were in that memory: a **garbage** value. (Globals and `static` variables are automatically 0.)

```c
int main() {
    int x;            // garbage
    static int y;     // 0
}
```

---

## 2. Data types and sizes

Typical sizes on a 32/64-bit system (what exams assume unless told otherwise):

| Type | Size | Range / precision |
|---|---|---|
| `char` | **1 byte** | signed: −128 to 127; unsigned: 0 to 255 |
| `short` | 2 bytes | −32,768 to 32,767 |
| `int` | **4 bytes** | about −2.1 × 10⁹ to 2.1 × 10⁹ (−2³¹ to 2³¹ − 1) |
| `long` | 4 or 8 bytes | |
| `long long` | 8 bytes | |
| `float` | **4 bytes** | ~6 to 7 significant digits |
| `double` | **8 bytes** | ~15 to 16 significant digits |

Only guaranteed ordering: `sizeof(char) = 1 ≤ short ≤ int ≤ long ≤ long long`.

**Range of an n-bit type:** unsigned 0 to 2ⁿ − 1; signed (two's complement) −2ⁿ⁻¹ to 2ⁿ⁻¹ − 1.

### Characters are small integers

`'A'` is stored as its ASCII code **65**, `'a'` = **97**, `'0'` = **48**. Arithmetic works on these numbers:

```c
char c = 'A' + 2;          // 'C'
int d = 'a' - 'A';         // 32
int digit = '7' - '0';     // 7
```

> **Trap.** In C, a character **constant** like `'A'` has type **int**, so `sizeof('A')` is **4** in C (but 1 in C++). A `char` **variable** has size 1.

### Floating constants

Written as `426.0`, `.5`, or exponent form `3.2e-5` (= 3.2 × 10⁻⁵). A bare `0.1` is a **double**; `0.1f` is a float.

---

## 3. Overflow and wraparound

**Unsigned** arithmetic wraps **modulo 2ⁿ** (well-defined):

```c
unsigned char u = 255;
u = u + 1;                 // 0
unsigned int v = 0;
v = v - 1;                 // 4294967295 (2^32 − 1)
```

**Signed** overflow is **undefined behaviour** in standard C, but exam questions conventionally assume **two's-complement wraparound**:

```c
signed char s = 127;
s = s + 1;                 // assumed −128
```

Think of the values on a circle: after the maximum comes the minimum.

---

## 4. Type conversion

### Implicit (automatic) conversion in expressions

- int op int → **int**
- int op double → the int is **promoted** to double → **double**
- char and short are promoted to int first ("integer promotion").

**Integer division truncates toward zero:**

```c
5 / 2       // 2
-7 / 2      // -3 (toward zero, not -4)
5.0 / 2     // 2.5
5 / 2.0     // 2.5
(float)5/2  // 2.5 (cast happens first, then division)
(float)(5/2)// 2.0 (division first: 2, then cast)
```

### Assignment conversion

The right side is computed **first** (in its own type), **then** converted to the left side's type.

```c
float f = 9 / 2;   // 9/2 = 4 (int division), then 4 → 4.0. f = 4.000000
int k = 3.99;      // fraction discarded: 3
```

> **Trap.** `float f = 9/2;` gives **4.0**, not 4.5. The division already happened in integers.

### Modulus `%`

- Only for **integers** (`5.5 % 2` is a compile error; use `fmod`).
- The sign of the result follows the **dividend** (left operand): `-5 % 2 = -1`, `5 % -2 = 1`, `-5 % -2 = -1`.
- Identity: `(a / b) * b + a % b == a`.

### No power operator

`2 ^ 3` is **bitwise XOR** (= 1), not 8. `2 ** 3` doesn't compile. Use `pow(2, 3)` from `<math.h>` (returns a double).

---

## 5. Operators

### 5.1 Arithmetic

`+ - * / %` and unary `+ -`.

### 5.2 Relational and equality

`< <= > >= == !=`. They produce **1 (true)** or **0 (false)** as an `int`.

```c
printf("%d", 10 > 5 > 2);   // (10>5)=1, then 1>2 = 0  → prints 0
printf("%d", 3 == 3 == 1);  // (3==3)=1, then 1==1 = 1 → prints 1
```

### 5.3 Logical: `&&`, `||`, `!`

- Any **non-zero** value is true; **0** is false.
- `!` of any non-zero is 0; `!0` is 1.
- **Short-circuit evaluation:**
  - `A && B`: if A is false (0), **B is not evaluated**.
  - `A || B`: if A is true (non-zero), **B is not evaluated**.

This matters when B has **side effects** (like `++b` or a function call).

```c
int a = -1, b = 2, c;
c = ++a && ++b;          // ++a → 0 (false). ++b is SKIPPED.
printf("%d %d %d", a, b, c);   // 0 2 0
```

```c
int a = 0, b = 2, c;
c = ++a || ++b;          // ++a → 1 (true). ++b is SKIPPED.
printf("%d %d %d", a, b, c);   // 1 2 1
```

### 5.4 Increment and decrement

| | `++x` (pre) | `x++` (post) |
|---|---|---|
| When x changes | Before its value is used | After its value is used |
| Value of the expression | **New** value | **Old** value |

```c
int a = 10;
int b = a++ + 5;     // uses 10, then a = 11.   b = 15
int c = ++a + 5;     // a = 12 first, uses 12.  c = 17
```

> **Trap.** Expressions that modify the same variable twice without a sequence point, like `i = i++ + ++i` or `printf("%d %d", i++, i++)`, are **undefined behaviour**. Real compilers disagree. If an exam insists, it usually expects left-to-right evaluation, but know that it's technically undefined.

### 5.5 Bitwise operators

| Op | Name | Example (12 = 1100, 10 = 1010) |
|---|---|---|
| `&` | AND | 12 & 10 = 1000 = **8** |
| `\|` | OR | 12 \| 10 = 1110 = **14** |
| `^` | XOR | 12 ^ 10 = 0110 = **6** |
| `~` | NOT (one's complement) | ~5 = **−6** (in two's complement, ~x = −x − 1) |
| `<<` | Left shift | 5 << 2 = **20** (× 2²) |
| `>>` | Right shift | 20 >> 1 = **10** (÷ 2) |

Right-shifting a negative signed number is implementation-defined (usually arithmetic: the sign bit is copied).

Useful tricks:
- `x & 1`: 1 if x is odd.
- `x & (x − 1)`: clears the lowest set bit; equals 0 iff x is a power of 2 (x > 0).
- `x ^ x = 0`, `x ^ 0 = x`: swap without a temporary via XOR.

### 5.6 Assignment and compound assignment

`=`, `+=`, `-=`, `*=`, `/=`, `%=`, `<<=`, `>>=`, `&=`, `^=`, `|=`. An assignment is itself an expression whose value is the assigned value: `a = b = 5` sets both (right-to-left).

### 5.7 Conditional (ternary) operator

`cond ? x : y`. **Right-associative**: `a ? b : c ? d : e` means `a ? b : (c ? d : e)`.

### 5.8 Comma operator

Evaluates left to right and gives the value of the **last** expression:

```c
int x = (2, 5, 9);     // x = 9
int y = (x = 3, x + 4);// x = 3, then y = 7
```

### 5.9 `sizeof`

A **compile-time** operator. The expression inside is **not evaluated**:

```c
int a = 5;
int s = sizeof(a = 10);   // s = 4, and a is STILL 5
int t = sizeof(a++);      // t = 4, a still 5
```

(Exception: variable-length arrays in C99 are evaluated at runtime.)

---

## 6. Precedence and associativity (high to low)

| Level | Operators | Associativity |
|---|---|---|
| 1 | `()` `[]` `->` `.` postfix `++ --` | Left to right |
| 2 | Unary: prefix `++ --`, `+ -`, `! ~`, `(type)`, `*` (deref), `&` (address), `sizeof` | **Right to left** |
| 3 | `* / %` | L to R |
| 4 | `+ -` | L to R |
| 5 | `<< >>` | L to R |
| 6 | `< <= > >=` | L to R |
| 7 | `== !=` | L to R |
| 8 | `&` | L to R |
| 9 | `^` | L to R |
| 10 | `\|` | L to R |
| 11 | `&&` | L to R |
| 12 | `\|\|` | L to R |
| 13 | `?:` | **Right to left** |
| 14 | `= += -=` etc. | **Right to left** |
| 15 | `,` | L to R |

Memory aid: "**U**nary, **A**rithmetic, **S**hift, **R**elational, **E**quality, **B**itwise (& ^ |), **L**ogical (&& ||), **T**ernary, **A**ssignment, **C**omma" (UASREBLTAC).

Common precedence traps:
- `a & b == c` means `a & (b == c)` (equality binds tighter than bitwise AND).
- `x << 1 + 2` means `x << 3`.
- `*p++` means `*(p++)`: postfix binds tighter than `*`.
- `3 + 4 * 5 % 3` = `3 + ((4 * 5) % 3)` = 3 + 2 = **5**.

---

## 7. Exam traps (checklist)

1. `int / int` truncates; assignment to float happens **after**.
2. `%` only for integers; sign follows the dividend.
3. `^` is XOR, not power.
4. `&&` / `||` short-circuit: skipped operands' side effects never happen.
5. Pre vs post increment value.
6. `sizeof` never evaluates its operand.
7. `sizeof('A')` is 4 in C.
8. Relational chains: `10 > 5 > 2` is 0.
9. `~x = −x − 1`.
10. Ternary and assignment are right-associative.
11. Unsigned wraps modulo 2ⁿ; signed overflow is UB (exam: assume wrap).
12. Uninitialised locals hold garbage; globals/statics are 0.

---

## 8. Practice questions

**Q1.** Output?
```c
int a = 2, b;
b = a && !a;
printf("%d", b);
```
(a) 0 (b) 1 (c) 2 (d) error

**Answer: (a).** !2 = 0, 2 && 0 = 0.

---

**Q2.** Output?
```c
int a = 1, b = 1;
int c = a || --b;
int d = a-- && --b;
printf("%d %d %d %d", a, b, c, d);
```
(a) 0 1 1 0 (b) 0 0 1 0 (c) 1 1 1 1 (d) 0 0 0 0

**Answer: (b).** `a || --b`: a = 1 is true, `--b` skipped, c = 1, b = 1. `a-- && --b`: a-- yields 1 (true) then a = 0; `--b` runs, b = 0 (false); d = 0.

---

**Q3.** Output?
```c
float f = 7 / 2;
printf("%.1f", f);
```
(a) 3.5 (b) 3.0 (c) 4.0 (d) error

**Answer: (b).**

---

**Q4.** Output?
```c
printf("%d %d", -7 / 2, -7 % 2);
```
(a) −4 1 (b) −3 −1 (c) −3 1 (d) −4 −1

**Answer: (b).** Division truncates toward zero; the remainder takes the dividend's sign. Check: (−3)(2) + (−1) = −7 ✓.

---

**Q5.** Output?
```c
int x = 5;
printf("%d %d", sizeof(x++), x);
```
(a) 4 6 (b) 4 5 (c) 5 5 (d) 2 6

**Answer: (b).**

---

**Q6.** Value of `12 ^ 10`?
(a) 1000000000000 (b) 6 (c) 14 (d) 8

**Answer: (b).**

---

**Q7.** Output?
```c
int a = 10;
int b = a++ + ++a;
```
(a) b = 22 (b) b = 21 (c) b = 20 (d) undefined behaviour

**Answer: (d).** `a` is modified twice without a sequence point. (Many compilers would print 22, which is why some exams expect 22; know both.)

---

**Q8.** Output?
```c
unsigned char c = 250;
c = c + 10;
printf("%d", c);
```
(a) 260 (b) 4 (c) −6 (d) 255

**Answer: (b).** 260 mod 256 = 4.

---

**Q9.** Output?
```c
int x = 10, y = 5, z = 2;
int r = x > y ? y > z ? 1 : 2 : 3;
printf("%d", r);
```
(a) 1 (b) 2 (c) 3 (d) error

**Answer: (a).** Groups as `x > y ? (y > z ? 1 : 2) : 3`. Both conditions true → 1.

---

**Q10.** Output?
```c
int x = (5, 10, 15);
printf("%d", x);
```
(a) 5 (b) 10 (c) 15 (d) error

**Answer: (c).**

---

**Q11.** Output?
```c
printf("%d", 5 & 3 == 3);
```
(a) 1 (b) 3 (c) 0 (d) 5

**Answer: (a).** `3 == 3` is 1 first; then `5 & 1` = 1.

---

**Q12.** Output?
```c
int i = 0, j = 0;
if (i++ || j++) printf("A");
else printf("B");
printf("%d%d", i, j);
```
(a) A10 (b) B11 (c) B10 (d) A11

**Answer: (b).** `i++` yields 0 (false) then i = 1. So `j++` is evaluated: yields 0 then j = 1. Condition false → "B". Prints **B11**.

---

**Q13.** Value of `~0` for a 32-bit signed int?
(a) 0 (b) 1 (c) −1 (d) 2³² − 1

**Answer: (c).** −0 − 1 = −1 (all bits 1).

---

**Q14.** Which is a valid identifier?
(a) 3rdValue (b) _value3 (c) value-3 (d) int

**Answer: (b).**

---

**Q15.** Output?
```c
int a = 7;
a <<= 2;
a >>= 1;
printf("%d", a);
```
(a) 14 (b) 28 (c) 7 (d) 3

**Answer: (a).** 7 × 4 = 28, then ÷ 2 = 14.
