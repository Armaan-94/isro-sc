# 05. C Storage Classes, Scoping, Structures, Unions and Enums

> **Two big ideas.** (1) Every variable has a **storage class** that decides where it lives, how long it lives, who can see it, and what its default value is. The star is `static`. (2) **Structures** bundle different types together, and their size is often bigger than you'd expect because of **padding**. Unions share memory instead.

---

## 1. The four properties of a variable

For every variable ask:
1. **Storage:** where does it live (stack, data segment, CPU register)?
2. **Default value:** what's in it if not initialised?
3. **Scope:** where in the code can it be **seen** (named)?
4. **Lifetime:** how long does it **exist** in memory?

---

## 2. Storage classes

| Class | Storage | Default value | Scope | Lifetime |
|---|---|---|---|---|
| **auto** (default for locals) | Stack | **Garbage** | Block where declared | Until the block exits |
| **register** | CPU register (if available; a hint only) | Garbage | Block | Until the block exits |
| **static** (local) | Data segment | **0** | Block | **Whole program run** |
| **static** (global / file level) | Data segment | 0 | **This file only** | Whole program |
| **extern** (global) | Data segment | 0 | **All files** (that declare it) | Whole program |

### 2.1 auto

```c
void f() { int i = 1; printf("%d ", i); i++; }
f(); f(); f();      // 1 1 1   (i is created fresh each call)
```

### 2.2 static local: remembers between calls

```c
void g() { static int i = 1; printf("%d ", i); i++; }
g(); g(); g();      // 1 2 3
```

- Initialised **only once** (before the program starts), not on each call.
- Keeps its value across calls.
- Still only **visible** inside g (scope is local; lifetime is global).
- Static initialisers must be **constants** (`static int x = y;` with a variable y is an error in C).

### 2.3 static global: file privacy

A `static` variable or function at file level is visible **only within that source file**. It hides it from other files (internal linkage). Great for encapsulation.

### 2.4 register

A hint to keep the variable in a CPU register for speed (compilers usually ignore it today).
- You **cannot take its address**: `&r` is an error (registers have no memory address).
- If no register is free, it behaves like `auto`.

### 2.5 extern: declaration vs definition

- **Definition** creates the variable (allocates memory): `int count = 0;` (in one file).
- **Declaration** only announces it exists elsewhere: `extern int count;` (no memory allocated).
- A variable can be **declared many times** but **defined only once**.

---

## 3. Scope rules

### 3.1 Nested blocks and shadowing

An inner declaration **hides** (shadows) an outer one with the same name.

```c
int main() {
    int i = 1;
    {
        int i = 2;
        {
            int i = 3;
            printf("%d ", i);   // 3
        }
        printf("%d ", i);       // 2
    }
    printf("%d", i);            // 1
}
// Output: 3 2 1
```

A **local** variable always takes precedence over a **global** one with the same name inside its block.

```c
int x = 10;
int main() { int x = 20; printf("%d", x); }   // 20
```

### 3.2 Static (lexical) vs dynamic scoping

- **Static (lexical) scoping:** a name refers to the declaration in the **textually enclosing** scope where the code is **written**. Decided at **compile time**. Used by **C, C++, Java, Python**.
- **Dynamic scoping:** a name refers to the **most recent active binding on the call stack** at **run time**. Used by some old Lisps, Perl's `local`, shell scripts.

**The test case that proves C is statically scoped:**

```c
int x = 10;
int f() { return x; }
int g() { int x = 20; return f(); }
int main() { printf("%d", g()); }
```

- **Static scoping (real C):** f's x is the **global** x → prints **10**.
- **Dynamic scoping:** when f runs, the most recent x on the call stack is g's x = 20 → would print **20**.

**Another example (both answers):**

```c
int x = 1;
void p() { printf("%d ", x); }
void q() { int x = 2; p(); }
int main() { q(); p(); }
```

- Static: **1 1**.
- Dynamic: **2 1** (inside q's call, x = 2 is the latest binding; the second p() is called from main, where only the global exists).

| | Static scoping | Dynamic scoping |
|---|---|---|
| Resolved using | Program text | Runtime call stack |
| When | Compile time | Run time |
| Speed | Faster | Slower |
| Readability | Predictable | Depends on call path |

---

## 4. Structures

### 4.1 Defining and using

```c
struct Book {
    char title[30];
    int pages;
    float price;
};                                  // the ; is required

struct Book b1 = {"OS Concepts", 900, 750.5};
struct Book *p = &b1;

b1.pages = 950;                     // dot operator on a variable
p->price = 800.0;                   // arrow on a pointer; same as (*p).price
```

- A struct declaration defines a **type**; it allocates **no memory** until you declare a variable.
- Members can have **different types** (unlike arrays).
- **Whole-struct assignment** works: `b2 = b1;` copies all members (including arrays inside).
- Structs **cannot be compared** with `==`; compare member by member.
- `typedef struct Book Book;` lets you write `Book b;`.
- Arrays of structs: `struct Book lib[100];`.
- Structs can contain other structs (nesting) and **pointers to their own type** (self-referential structs, used for linked lists: `struct Node { int data; struct Node *next; };`).

### 4.2 Padding and alignment (the size question)

The CPU reads data fastest when each item starts at an address that's a multiple of its size (its **alignment**). So the compiler inserts **padding bytes**.

Rules (typical: char 1, short 2, int 4, double 8; alignment = size):
1. Each member starts at an offset that's a **multiple of its own alignment**.
2. The **total size** is rounded up to a **multiple of the largest member alignment** (so arrays of the struct stay aligned).

**Example 1:**
```c
struct A { char c; int i; };
```
c at 0; pad 1 to 3; i at 4 to 7. **sizeof = 8** (not 5).

**Example 2:**
```c
struct B { char a; double b; char c; };
```
a at 0; pad 1 to 7; b at 8 to 15; c at 16; then round 17 up to a multiple of 8 → **24**.

**Example 3 (same members, reordered):**
```c
struct C { double b; char a; char c; };
```
b 0 to 7; a 8; c 9; round 10 up to 16 → **16**. Ordering members from largest to smallest reduces padding.

**Example 4:**
```c
struct D { char c; short s; int i; };
```
c 0; pad 1; s 2 to 3; i 4 to 7 → **8**.

**Example 5:**
```c
struct E { int i; char c; };
```
i 0 to 3; c 4; round 5 up to 8 → **8** (trailing padding).

> **Trap.** sizeof(struct) ≥ sum of member sizes. Never assume they're equal. (Compilers can be told to pack structs with `#pragma pack(1)`, giving the plain sum.)

### 4.3 Bit fields

```c
struct Flags { unsigned int ready : 1; unsigned int mode : 3; };
```
Members occupy a specified number of **bits**. You can't take the address of a bit field.

---

## 5. Unions

Same syntax as a struct, but **all members share the same memory**. At any moment only one member holds a meaningful value.

```c
union Data { int i; float f; char str[20]; };
union Data d;
d.i = 10;          // now d.f and d.str contain reinterpreted bits
d.f = 2.5;         // overwrites i
```

- **sizeof(union) = size of the largest member**, rounded up to the strictest alignment.
  - `union { int i; float f; char s[20]; }` → **20** (20 is already a multiple of 4).
  - `union { int i; char c[6]; }` → largest is 6, rounded up to a multiple of 4 → **8**.
  - `union { double d; char c[10]; }` → 10 rounded to a multiple of 8 → **16**.
- Writing one member then reading another gives the **same bits reinterpreted** (type punning).

**Endianness check with a union:**
```c
union { int i; char c[4]; } u;
u.i = 1;
printf("%d", u.c[0]);   // 1 on little-endian (x86), 0 on big-endian
```

| | Structure | Union |
|---|---|---|
| Memory | Separate for each member | **Shared** |
| Size | ≥ sum (with padding) | Largest member (aligned) |
| Members valid at once | All | **One** |
| Use | A record with many fields | One value viewed as different types; saving memory |

---

## 6. Enumerations

```c
enum Color { RED, GREEN, BLUE };            // 0, 1, 2
enum Num { ONE = 5, TWO, THREE, FOUR = 20, FIVE };   // 5, 6, 7, 20, 21
enum E { A = 3, B = 1, C, D = A + C };      // 3, 1, 2, 5
```

- Default: first constant = **0**, each next = previous + 1.
- After an explicitly assigned value, counting continues **from that value**.
- Values can repeat (`enum { X = 1, Y = 1 }` is legal).
- Enum constants are **integers**: `printf("%d", GREEN)` prints 1. `sizeof(enum ...)` is usually sizeof(int).
- Fixed at compile time.

---

## 7. typedef

Creates an **alias** for an existing type (it doesn't create a new type).

```c
typedef unsigned long ulong;
typedef struct Node { int data; struct Node *next; } Node;
typedef int (*Op)(int, int);   // Op is "pointer to function (int, int) returning int"
```

`typedef` vs `#define`: `typedef char *STR; STR a, b;` makes **both** a and b `char *`. `#define STR char *` then `STR a, b;` expands to `char *a, b;` so **b is only a char**. Classic trap.

---

## 8. Exam traps

1. auto/register default: **garbage**; static/extern/global default: **0**.
2. static local: initialised once, keeps its value, scope still local.
3. static global: visible only in its file.
4. Can't take the address of a register variable.
5. extern declares; definition allocates. One definition only.
6. C uses **static (lexical) scoping**.
7. Inner declarations shadow outer ones.
8. Struct size includes padding; union size = largest member (aligned).
9. Enum values continue from the last explicit value.
10. `->` for pointers, `.` for variables.
11. `typedef char *P; P a, b;` → both pointers; `#define` version → only a.

---

## 9. Practice questions

**Q1.** Output?
```c
void f() { static int i = 1; printf("%d ", i); i++; }
int main() { f(); f(); f(); }
```
(a) 1 1 1 (b) 1 2 3 (c) 0 1 2 (d) 3 3 3

**Answer: (b).**

---

**Q2.** Output?
```c
int x = 10;
int f() { return x; }
int g() { int x = 20; return f(); }
int main() { printf("%d", g()); }
```
(a) 10 (b) 20 (c) error (d) undefined

**Answer: (a).**

---

**Q3.** If the language in Q2 used dynamic scoping, the output would be:
(a) 10 (b) 20 (c) 30 (d) error

**Answer: (b).**

---

**Q4.** sizeof(struct S { char c; int i; char d; }) on a typical 32/64-bit system?
(a) 6 (b) 8 (c) 12 (d) 16

**Answer: (c).** c 0; pad 1 to 3; i 4 to 7; d 8; round 9 up to 12.

---

**Q5.** sizeof(struct T { int i; char c; char d; })?
(a) 6 (b) 8 (c) 12 (d) 4

**Answer: (b).** i 0 to 3; c 4; d 5; round 6 up to 8.

---

**Q6.** sizeof(union U { int i; double d; char s[13]; })?
(a) 13 (b) 16 (c) 25 (d) 8

**Answer: (b).** Largest = 13; strictest alignment = 8; round up to 16.

---

**Q7.** Values of A, B, C in `enum { A = 2, B, C = 10 };`?
(a) 2, 3, 10 (b) 0, 1, 10 (c) 2, 3, 4 (d) 2, 0, 10

**Answer: (a).**

---

**Q8.** Which storage class's variables can't have their address taken?
(a) auto (b) static (c) extern (d) register

**Answer: (d).**

---

**Q9.** Output?
```c
int main() {
    int a = 5;
    { int a = 10; a++; }
    printf("%d", a);
}
```
(a) 5 (b) 10 (c) 11 (d) 6

**Answer: (a).** The inner a is a different variable.

---

**Q10.** Output?
```c
int count() { static int c; return ++c; }
int main() { count(); count(); printf("%d", count()); }
```
(a) 1 (b) 2 (c) 3 (d) garbage

**Answer: (c).** static c starts at 0.

---

**Q11.** After `typedef char *CP; CP x, y;` and `#define DP char *` then `DP p, q;`, which variables are pointers?
(a) x, y, p, q (b) x, y, p (c) x, p (d) only p

**Answer: (b).** q expands to plain `char`.

---

**Q12.** A global variable declared `static` in file1.c is:
(a) visible in all files (b) visible only in file1.c (c) local to main (d) stored on the stack

**Answer: (b).**

---

**Q13.** Accessing the member `age` through a pointer `p` to a struct is written:
(a) p.age (b) p->age (c) *p.age (d) &p.age

**Answer: (b).** Note (c) means `*(p.age)` because `.` binds tighter than `*`; the correct long form is `(*p).age`.

---

**Q14.** Which statement about unions is TRUE?
(a) All members can hold valid values simultaneously (b) Size equals the sum of members (c) Members share the same memory (d) Unions can't contain arrays

**Answer: (c).**

---

**Practice questions:** [2.05 C Storage Classes Structures Unions](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.05_C_Storage_Classes_Structures_Unions.md)
