# 06. Dynamic Memory Allocation, Macros and File Handling

> **Three separate tools, three classic trap families.** Dynamic memory: **leaks** and **dangling pointers**. Macros: **blind text substitution** that ignores precedence. Files: **opening modes** that silently destroy data. Learn the mental model for each and the traps become obvious.

---

## Part A: Dynamic memory allocation

### 1. Stack vs heap

| | Stack (static/automatic allocation) | Heap (dynamic allocation) |
|---|---|---|
| Who manages | Compiler, automatically | **You** (malloc/free) |
| When size is decided | Compile time | **Run time** |
| Lifetime | Until the block/function returns | Until you call **free** (or the program ends) |
| Typical use | Local variables, fixed arrays | Data whose size is known only at run time; data that must outlive a function |
| Speed | Very fast | Slower (allocator bookkeeping) |
| Size | Small (MBs) | Large |

### 2. The four functions (`<stdlib.h>`)

| Function | Arguments | Initialises? | Returns |
|---|---|---|---|
| `malloc(bytes)` | Total bytes | **No** (garbage) | `void *` to the block, or **NULL** on failure |
| `calloc(n, size)` | Number of elements, size of each | **Yes, all zero** | `void *` or NULL |
| `realloc(ptr, newsize)` | Old pointer, new total size | Old contents kept; extra part uninitialised | Possibly a **new** address (block may move); NULL on failure |
| `free(ptr)` | Pointer from malloc/calloc/realloc | | Nothing |

```c
int n = 10;
int *a = (int *) malloc(n * sizeof(int));     // 10 ints, garbage
int *b = (int *) calloc(n, sizeof(int));      // 10 ints, all 0
if (a == NULL) { /* allocation failed */ }

a = (int *) realloc(a, 20 * sizeof(int));     // grow to 20; ALWAYS use the returned pointer
free(a);  a = NULL;
free(b);  b = NULL;
```

Notes:
- In C, the `(int *)` cast is optional (`void *` converts implicitly); in C++ it's required.
- `realloc(NULL, size)` behaves like `malloc(size)`. `realloc(p, 0)` may free p.
- `free(NULL)` is safe (does nothing). **Freeing the same pointer twice** (double free) is undefined behaviour.
- Better pattern for realloc: `int *tmp = realloc(a, newsize); if (tmp) a = tmp;` (so you don't lose the old block if it fails).

### 3. Memory leak

Allocated memory that's **never freed** and whose address has been **lost**, so it can never be freed.

```c
void f() {
    int *p = malloc(100 * sizeof(int));
    // ... no free(p)
}   // p (the only pointer) disappears: 400 bytes leaked on every call
```

```c
int *p = malloc(10);
p = malloc(20);      // the first block is now unreachable: leak
```

### 4. Dangling pointer

A pointer that still holds the address of memory that's **no longer valid**.

```c
int *p = malloc(sizeof(int));
free(p);
*p = 5;              // dangling: undefined behaviour

int *g() { int x = 5; return &x; }   // returns the address of a dead local: dangling
```

Good habit: set pointers to **NULL** right after `free`.

| | Memory leak | Dangling pointer |
|---|---|---|
| Problem | Memory never freed | Memory freed (or expired) but still used |
| Effect | Program slowly uses more memory | Crashes, corrupted data, security holes |

### 5. Dynamic 2-D arrays

```c
int **m = malloc(rows * sizeof(int *));
for (int i = 0; i < rows; i++)
    m[i] = malloc(cols * sizeof(int));
// use m[i][j]
for (int i = 0; i < rows; i++) free(m[i]);
free(m);
```

---

## Part B: Macros and the preprocessor

### 6. What the preprocessor does

Before compilation, the **preprocessor** handles lines starting with `#`:
- `#include` pastes in a file.
- `#define` defines macros.
- `#if, #ifdef, #ifndef, #else, #endif` for conditional compilation (e.g. include guards).
- `#undef`, `#pragma`, `#error`.

It works on **text (tokens)**. It knows nothing about types, scopes or precedence.

### 7. Object-like and function-like macros

```c
#define PI 3.14159
#define MAX 100
#define SQUARE(x) ((x) * (x))
#define MAX2(a, b) ((a) > (b) ? (a) : (b))
```

A function-like macro is **substituted literally**, then compiled.

| | Macro | Function |
|---|---|---|
| When | Preprocessing (text replacement) | Compiled once, called at run time |
| Type checking | **None** | Yes |
| Call overhead | None | Small |
| Code size | Grows (copied at each use) | One copy |
| Side effects in args | **Evaluated multiple times** | Evaluated once |
| Debugging | Harder | Easier |

(`inline` functions give macro-like speed with function-like safety.)

### 8. The precedence trap (most tested)

```c
#define SQ(x) x * x

SQ(3 + 2)     →  3 + 2 * 3 + 2     = 3 + 6 + 2 = 11      (not 25!)
64 / SQ(4)    →  64 / 4 * 4        = (64 / 4) * 4 = 64   (not 4!)
```

Fix: parenthesise **each parameter** and the **whole body**: `#define SQ(x) ((x) * (x))`. Then SQ(3 + 2) = 25 and 64 / SQ(4) = 4.

### 9. The side-effect trap

```c
#define SQ(x) ((x) * (x))
int i = 3;
int r = SQ(i++);     // ((i++) * (i++)): i incremented twice: undefined behaviour
```

A real function would evaluate `i++` once.

### 10. Other macro facts

**No substitution inside string literals:**
```c
#define mno pqr
printf("mno");       // prints mno
```

**Stringizing `#`:** turns an argument into a string.
```c
#define STR(x) #x
printf(STR(hello));  // "hello"
```

**Token pasting `##`:** glues tokens into one.
```c
#define fun(i, j) i##j
int f1 = 10, f12 = 20;
printf("%d", fun(f1, 2));    // f12 → 20
```

**Redefining keywords works (blind text replacement):**
```c
#define int char
int a = 10, *p = &a;         // becomes: char a = 10, *p = &a;
printf("%d %d", sizeof(a), sizeof(p));   // 1 and pointer size (4 on 32-bit, 8 on 64-bit)
```

**Include guard:**
```c
#ifndef MYHEADER_H
#define MYHEADER_H
/* declarations */
#endif
```

**Predefined macros:** `__FILE__`, `__LINE__`, `__DATE__`, `__TIME__`.

---

## Part C: File handling

### 11. Why and how

Variables vanish when the program ends; files persist.

- **Text files:** human-readable characters, lines end with newline characters (may be translated on some OSes).
- **Binary files:** raw bytes; compact and exact (e.g. writing a struct directly).

Basic workflow:

```c
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) { perror("open failed"); return 1; }
/* read or write */
fclose(fp);      // flushes buffers, releases the file
```

### 12. Opening modes

| Mode | Purpose | If file exists | If file doesn't exist |
|---|---|---|---|
| `"r"` | Read | Opens at start | **Fails (NULL)** |
| `"w"` | Write | **Erases contents** | Creates it |
| `"a"` | Append | Writes go to the **end** | Creates it |
| `"r+"` | Read and write | Opens at start, keeps contents | Fails |
| `"w+"` | Read and write | **Erases contents** | Creates it |
| `"a+"` | Read and append | Reads anywhere, writes at end | Creates it |

Add **`b`** for binary: `"rb"`, `"wb"`, `"ab+"`, etc.

> **Trap.** `"w"` and `"w+"` **truncate the file to zero length the moment it's opened**. Use `"a"` or `"r+"` to keep existing data.

### 13. Reading and writing functions

| Level | Write | Read |
|---|---|---|
| Character | `fputc(c, fp)` / `putc` | `fgetc(fp)` / `getc` (returns **EOF** at end) |
| Line/string | `fputs(s, fp)` | `fgets(buf, n, fp)` (reads up to n − 1 chars, stops at newline, keeps the newline) |
| Formatted | `fprintf(fp, ...)` | `fscanf(fp, ...)` |
| Binary blocks | `fwrite(ptr, size, count, fp)` | `fread(ptr, size, count, fp)` (both return the number of items) |

### 14. Moving around in a file

- `fseek(fp, offset, origin)`: origin is **SEEK_SET** (start), **SEEK_CUR** (current), **SEEK_END** (end). Offset can be negative.
- `ftell(fp)`: current position (bytes from start).
- `rewind(fp)`: back to the start (= `fseek(fp, 0, SEEK_SET)`, and clears error flags).
- `feof(fp)`: true **after** a read has tried to go past the end.

**File size trick:**
```c
fseek(fp, 0, SEEK_END);
long size = ftell(fp);
```

**Copy a file character by character (classic):**
```c
int ch;
while ((ch = fgetc(in)) != EOF)
    fputc(ch, out);
```
(`ch` must be `int`, not `char`, so EOF (−1) can be distinguished from a real byte value 255.)

Standard streams always open: **stdin**, **stdout**, **stderr**.

---

## 15. Exam traps

1. malloc: garbage; calloc: zeros, two arguments.
2. realloc may move the block; use its return value.
3. Leak = never freed and unreachable. Dangling = freed but still used.
4. `free` doesn't set the pointer to NULL.
5. Macros are text substitution: `SQ(3+2)` with `x*x` → 11.
6. Macros don't substitute inside string literals.
7. `##` pastes tokens; `#` stringizes.
8. Macro arguments with side effects may be evaluated more than once.
9. `"w"` erases existing content; `"r"` fails if the file doesn't exist.
10. Use `int` for fgetc results to detect EOF.

---

## 16. Practice questions

**Q1.** Output?
```c
#define abc(x) x * x
int main() { int a = 64 / abc(4); printf("%d", a); }
```
(a) 4 (b) 16 (c) 64 (d) 1

**Answer: (c).**

---

**Q2.** Output?
```c
#define SQ(x) x * x
int main() { printf("%d", SQ(2 + 3)); }
```
(a) 25 (b) 11 (c) 13 (d) 10

**Answer: (b).** 2 + 3 * 2 + 3 = 11.

---

**Q3.** Output?
```c
#define SQ(x) ((x) * (x))
int main() { printf("%d", 100 / SQ(5)); }
```
(a) 4 (b) 100 (c) 20 (d) 500

**Answer: (a).** 100 / 25.

---

**Q4.** Which function allocates memory AND initialises it to zero?
(a) malloc (b) calloc (c) realloc (d) free

**Answer: (b).**

---

**Q5.** Using a pointer after `free()` is:
(a) a memory leak (b) a dangling pointer (c) a wild pointer always (d) safe

**Answer: (b).**

---

**Q6.** Which fopen mode destroys existing contents when opened?
(a) "r" (b) "a" (c) "w" (d) "r+"

**Answer: (c).**

---

**Q7.** Output?
```c
#define fun(i, j) i##j
int main() { int f1 = 10, f12 = 20; printf("%d", fun(f1, 2)); }
```
(a) 10 (b) 20 (c) 12 (d) error

**Answer: (b).**

---

**Q8.** Output on a 64-bit system?
```c
#define int char
int main() { int a; int *p; printf("%d %d", (int)sizeof(a), (int)sizeof(p)); }
```
Careful: the casts `(int)` also become `(char)`.
(a) 4 8 (b) 1 8 (c) 1 1 (d) 4 4

**Answer: (b).** a is a char (1); p is a char pointer (8 on 64-bit). Casting those small numbers to char doesn't change them.

---

**Q9.** Which code leaks memory?
(a) `int *p = malloc(4); free(p);` (b) `int *p = malloc(4); p = malloc(8); free(p);` (c) `int *p = NULL; free(p);` (d) `int *p = calloc(1, 4); free(p); p = NULL;`

**Answer: (b).** The first 4-byte block is lost.

---

**Q10.** What does `rewind(fp)` do?
(a) Closes the file (b) Moves to the end (c) Moves to the beginning (d) Deletes contents

**Answer: (c).**

---

**Q11.** Output?
```c
#define MAX(a, b) ((a) > (b) ? (a) : (b))
int main() { int x = 5, y = 3; int m = MAX(x++, y); printf("%d %d", m, x); }
```
(a) 5 6 (b) 6 7 (c) 5 7 (d) 6 6

**Answer: (b).** Expands to `((x++) > (y) ? (x++) : (y))`. First x++ yields 5 (5 > 3 true), x = 6. Then the chosen branch evaluates x++ again: yields 6, x = 7. m = 6, x = 7. (The ternary's `?` is a sequence point, so this is well defined.)

---

**Q12.** `malloc` returns what when it fails?
(a) 0xFFFF (b) NULL (c) −1 (d) a garbage pointer

**Answer: (b).**

---

**Q13.** To read a file that must already exist, without modifying it, use mode:
(a) "w" (b) "a" (c) "r" (d) "w+"

**Answer: (c).**
