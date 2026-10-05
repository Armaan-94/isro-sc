# 04. C Arrays and Pointers

> **The one sentence that unlocks this chapter:** `a[i]` is just a shorthand for `*(a + i)`. Arrays and pointers are two views of the same idea: a starting address plus an offset. Once that clicks, address arithmetic questions become simple multiplication.

---

## 1. Arrays

An array is a collection of elements of the **same type** stored in **contiguous** memory. Indices run from **0** to **size − 1**.

```c
int a[5];                    // 5 ints, garbage if local
int b[5] = {1, 2};           // {1, 2, 0, 0, 0}  (remaining elements become 0)
int c[] = {10, 20, 30};      // size inferred: 3
int d[5] = {0};              // all zeros
```

- **Partial initialisation** zeros the rest. An **uninitialised local** array has garbage. Global/static arrays are zero.
- Size must be a constant (except C99 variable-length arrays).
- **No bounds checking:** `a[10]` on a 5-element array compiles and reads/writes some other memory (undefined behaviour).

### sizeof an array

```c
int a[10];
sizeof(a)            // 40 (10 × 4): the whole array
sizeof(a) / sizeof(a[0])   // 10: number of elements
```

But inside a function receiving the array as a parameter, `sizeof` gives the **pointer** size (the array has decayed).

### Address of an element (1-D)

```
Address of a[i] = Base + i × (size of element)
```

Example: `int a[5]` at base **2000**, int = 4 bytes. `&a[3]` = 2000 + 3 × 4 = **2012**.

If the array's lower bound is L (e.g. Pascal-style a[L..U]): address = Base + (i − L) × size.

---

## 2. Pointers

A **pointer** is a variable that stores a **memory address**.

```c
int x = 10;
int *p = &x;    // p holds the address of x

printf("%d", *p);   // 10   (* = "value at address")
*p = 20;            // x is now 20
```

- `&x`: **address of** x.
- `*p`: **value at** the address in p (**dereference**).
- `int *p` reads as "p is a pointer to int" or "*p is an int".
- All pointers (on a given machine) are the same size (8 bytes on 64-bit, 4 on 32-bit), whatever they point to.

### Pointer to pointer

```c
int x = 5;
int *p = &x;
int **q = &p;
**q = 50;        // x = 50
```

`q` → `p` → `x`. `*q` is p (an address); `**q` is x.

### NULL and wild pointers

- `int *p = NULL;` points nowhere. Dereferencing NULL → crash (undefined behaviour).
- An **uninitialised** pointer is a **wild** pointer (random address).
- A pointer to memory that has been freed (or to a local variable that has gone out of scope) is a **dangling** pointer.

### void pointer

`void *v` can hold any address but **can't be dereferenced** or used in arithmetic until cast to a real type. `malloc` returns `void *`.

---

## 3. Pointer arithmetic (scaled by type size)

If `p` points to type T, then `p + k` points `k × sizeof(T)` **bytes** further.

```c
int a[5] = {10, 20, 30, 40, 50};   // base 1000, int 4 bytes
int *p = a;                         // p = 1000

p + 1     // 1004  → &a[1]
p + 3     // 1012  → &a[3]
*(p + 2)  // 30
*p + 2    // 12  (dereference first, then add)
```

Allowed: pointer + int, pointer − int, pointer − pointer (same array: gives **number of elements** between them), comparisons. Not allowed: pointer + pointer, multiplying pointers.

```c
int *q = &a[4];
q - p    // 4  (elements, not bytes)
```

For `char *`, +1 moves 1 byte; for `double *`, 8 bytes.

---

## 4. Arrays and pointers together

### The golden equivalence

```
a[i]  ≡  *(a + i)  ≡  *(i + a)  ≡  i[a]
```

(Yes, `2[a]` is legal C and equals `a[2]`. A favourite trick question.)

The array name `a` **decays** to a pointer to its first element: `a ≡ &a[0]`.

### But an array name is not a pointer variable

```c
int a[5], *p = a;
p++;        // OK: p now points to a[1]
a++;        // ERROR: a is not a modifiable lvalue (it's like a constant pointer)
```

### Increment/dereference combos (very common)

With `int a[] = {10, 20, 30}; int *p = a;`

| Expression | Value produced | Side effect |
|---|---|---|
| `*p++` | **10** | p moves to a[1] (postfix binds tighter: `*(p++)`) |
| `*++p` | **20** (if starting from a[0]) | p moves to a[1] first |
| `++*p` | **11** | a[0] becomes 11 |
| `(*p)++` | **10** | a[0] becomes 11 afterward |

### `a` vs `&a`

- `a` (decayed) has type `int *`: `a + 1` moves **one element** (4 bytes).
- `&a` has type `int (*)[5]` (pointer to the whole array): `&a + 1` moves **the whole array** (20 bytes).

Classic:
```c
int a[5] = {1, 2, 3, 4, 5};
int *p = (int *)(&a + 1);    // just past the end of the array
printf("%d", *(p - 1));      // 5
```

---

## 5. Two-dimensional arrays

```c
int m[3][4];    // 3 rows, 4 columns
```

C stores 2-D arrays in **row-major order**: row 0's elements, then row 1's, etc.

### Address formulas

For `A[R][C]` with element size w and base B (0-indexed):

```
Row-major:     &A[i][j] = B + (i × C + j) × w
Column-major:  &A[i][j] = B + (j × R + i) × w      (Fortran, MATLAB)
```

**Example:** `int m[3][4]`, base 1000, int = 4 bytes. `&m[2][1]` = 1000 + (2 × 4 + 1) × 4 = 1000 + 36 = **1036**. In column-major it would be 1000 + (1 × 3 + 2) × 4 = **1020**.

**Example with non-zero lower bounds:** A[1..10][1..15], element 2 bytes, base 100, row-major. Address of A[5][7] = 100 + ((5 − 1) × 15 + (7 − 1)) × 2 = 100 + (60 + 6) × 2 = **232**.

### Pointer view of 2-D arrays

- `m` → pointer to row 0 (type `int (*)[4]`); `m + 1` jumps a whole row (16 bytes).
- `m[i]` or `*(m + i)` → pointer to the first element of row i.
- `m[i][j]` ≡ `*(*(m + i) + j)`.

### Passing 2-D arrays to functions

Must specify all dimensions except the first: `void f(int m[][4], int rows)`.

---

## 6. Strings and character pointers

A string is a **char array ending with `'\0'`** (the null character).

```c
char s[] = "ISRO";      // 5 bytes: 'I' 'S' 'R' 'O' '\0'. sizeof(s) = 5, strlen(s) = 4
char t[10] = "ISRO";    // sizeof(t) = 10, strlen(t) = 4
char *p = "ISRO";       // p points to a string literal; sizeof(p) = pointer size (4 or 8)
```

| | `char s[] = "Hello";` | `char *p = "Hello";` |
|---|---|---|
| What it is | An array holding a **copy** | A pointer to a **string literal** |
| Modify characters? | **Yes** (`s[0] = 'h'` OK) | **No**: literal is usually read-only; writing is **undefined** (often crashes) |
| Reassign the name? | **No** (`s = "Bye"` is an error) | **Yes** (`p = "Bye"` OK) |
| sizeof | 6 | Pointer size |

Pointer arithmetic on strings:
```c
char *s = "hello";
printf("%s", s + 2);     // "llo"
printf("%c", *(s + 1));  // 'e'
printf("%c", s[4]);      // 'o'
```

Common `<string.h>` functions: `strlen` (length without '\0'), `strcpy(dest, src)`, `strcat`, `strcmp` (0 if equal, negative/positive otherwise), `strncpy`, `strchr`, `strstr`.

Classic string-length function:
```c
int len(char *s) { int n = 0; while (*s++) n++; return n; }
```

---

## 7. Call by address

Passing addresses lets a function modify the caller's variables and "return" several values.

```c
void areaperi(int r, float *a, float *p) {
    *a = 3.14f * r * r;
    *p = 2 * 3.14f * r;
}
// call: areaperi(5, &area, &perimeter);
```

### Mixed example (classic trap)

```c
void junk(int *i, int j) {
    *i = *i * *i;     // modifies the caller's i
    j = j * j;        // modifies only the local copy
}
int main() {
    int i = 4, j = 2;
    junk(&i, j);
    printf("%d %d", i, j);    // 16 2
}
```

### Arrays are always passed as pointers

`void f(int a[])` is exactly `void f(int *a)`. The function **can modify** the caller's array elements (it receives the address), but `sizeof(a)` inside f gives the pointer size.

---

## 8. Complex declarations (read them right-to-left with precedence)

| Declaration | Meaning |
|---|---|
| `int *p[5]` | Array of 5 **pointers to int** (`[]` binds tighter than `*`) |
| `int (*p)[5]` | **Pointer to an array** of 5 ints |
| `int *f()` | Function returning a pointer to int |
| `int (*f)()` | **Pointer to a function** returning int |
| `int (*f[3])()` | Array of 3 pointers to functions returning int |
| `char **argv` | Pointer to pointer to char |
| `const int *p` | Pointer to a **constant int** (can't change *p, can change p) |
| `int * const p` | **Constant pointer** to int (can change *p, can't change p) |

**Function pointers:**
```c
int add(int a, int b) { return a + b; }
int (*fp)(int, int) = add;
printf("%d", fp(2, 3));    // 5
```

---

## 9. Exam traps

1. `a[i] ≡ *(a + i) ≡ i[a]`.
2. Pointer arithmetic is scaled by type size; pointer difference counts **elements**.
3. Array name can't be incremented or assigned.
4. `*p++` = `*(p++)`: value at p, then p advances.
5. `&a + 1` skips the whole array.
6. Row-major address: B + (i × C + j) × w.
7. `char *p = "..."` literal is read-only; `char s[] = "..."` is a modifiable copy.
8. `sizeof` of an array parameter is the pointer size.
9. `int *p[5]` vs `int (*p)[5]`.
10. Partial initialisation zeros the rest.

---

## 10. Practice questions

**Q1.** `int a[5]` at base 2000 (int = 4 bytes). Address of a[3]?
(a) 2003 (b) 2012 (c) 2008 (d) 2016

**Answer: (b).**

---

**Q2.** Output?
```c
int a[] = {10, 20, 30, 40};
int *p = a;
printf("%d %d", *(p + 2), *p + 2);
```
(a) 30 12 (b) 30 30 (c) 12 30 (d) 20 12

**Answer: (a).**

---

**Q3.** Output?
```c
int a[] = {5, 10, 15};
int *p = a;
printf("%d ", *p++);
printf("%d", *p);
```
(a) 5 10 (b) 6 6 (c) 10 10 (d) 5 5

**Answer: (a).**

---

**Q4.** Output?
```c
int a[] = {5, 10, 15};
int *p = a;
printf("%d ", ++*p);
printf("%d", a[0]);
```
(a) 10 5 (b) 6 6 (c) 6 5 (d) 10 10

**Answer: (b).**

---

**Q5.** `int m[4][5]`, base 1000, int = 4 bytes, row-major. Address of m[2][3]?
(a) 1052 (b) 1048 (c) 1060 (d) 1044

**Answer: (a).** (2 × 5 + 3) × 4 = 52.

---

**Q6.** Same array in column-major order. Address of m[2][3]?
(a) 1056 (b) 1052 (c) 1060 (d) 1044

**Answer: (a).** (3 × 4 + 2) × 4 = 56.

---

**Q7.** Output?
```c
char s[] = "GATE2026";
printf("%d %d", sizeof(s), strlen(s));
```
(a) 8 8 (b) 9 8 (c) 8 9 (d) 9 9

**Answer: (b).**

---

**Q8.** Output?
```c
int a[5] = {1, 2, 3, 4, 5};
int *p = (int *)(&a + 1);
printf("%d", *(p - 1));
```
(a) 1 (b) 2 (c) 5 (d) garbage

**Answer: (c).**

---

**Q9.** Which is a compile-time error?
(a) `int *p = a; p++;` (b) `int a[5]; a++;` (c) `int *p = &a[2];` (d) `int *p = a + 1;`

**Answer: (b).**

---

**Q10.** `int *p` holds 5000, sizeof(int) = 4. What address is `p − 2`?
(a) 4998 (b) 4992 (c) 5008 (d) 4996

**Answer: (b).**

---

**Q11.** `int (*p)[10];` declares:
(a) an array of 10 int pointers (b) a pointer to an array of 10 ints (c) a function pointer (d) a 2-D array

**Answer: (b).**

---

**Q12.** Output?
```c
int a[] = {1, 2, 3, 4};
printf("%d", 2[a]);
```
(a) 2 (b) 3 (c) compile error (d) garbage

**Answer: (b).** `2[a]` = `*(2 + a)` = a[2] = 3.

---

**Q13.** Output?
```c
int x = 7, *p = &x, **q = &p;
**q = **q + 3;
printf("%d", x);
```
(a) 7 (b) 10 (c) address (d) error

**Answer: (b).**

---

**Q14.** Output?
```c
char *s = "program";
printf("%s", s + 3);
```
(a) pro (b) gram (c) ogram (d) r

**Answer: (b).**

---

**Q15.** `int a[] = {1,2,3,4,5}; int *p = &a[1], *q = &a[4];` What is `q − p`?
(a) 12 (b) 3 (c) 4 (d) 16

**Answer: (b).** Difference in elements.

---

**Q16.** A[1..8][1..10] with 4-byte elements, base 500, row-major. Address of A[3][4]?
(a) 592 (b) 596 (c) 588 (d) 600

**Answer: (a).** ((3 − 1) × 10 + (4 − 1)) × 4 = 23 × 4 = 92 → 592.

---

**Practice questions:** [2.04 C Arrays and Pointers](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.04_C_Arrays_and_Pointers.md)
