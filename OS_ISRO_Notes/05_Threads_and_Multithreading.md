# 05. Threads and Multithreading Models (plus `fork()`)

> **One-line idea.** A process is a house. Threads are the people living in it. They share the kitchen, the fridge and the address, but each person has their own to-do list and their own place in that list.

---

## 1. Why do we need threads at all?

Think about a web browser. While a page is loading images from the network, you still want to scroll, type in the address bar, and hear a video playing. If the browser were a single sequence of instructions, it would freeze every time it waited for the network.

Option 1: make every activity a separate **process**. That works, but processes are **heavy**:
- each needs its own address space (page tables, memory),
- creating one is slow,
- switching between processes is slow (the memory map changes, caches and TLB get flushed),
- sharing data between them needs special IPC.

Option 2: **threads**. Several flows of execution **inside one process**, sharing its memory.

---

## 2. What exactly is a thread?

A **thread** is the basic unit of CPU utilization. It's sometimes called a **lightweight process**.

| Each thread has its OWN | Threads of one process SHARE |
|---|---|
| Thread ID | Code (text) section |
| **Program counter** | **Data section** (global variables) |
| **Register set** | **Heap** |
| **Stack** | Open files, signals, other OS resources |

Why must each thread have its own stack? Because each thread is calling its own functions. If two threads shared a stack, their function calls and local variables would trample each other.

Why must each have its own PC and registers? Because each is at a different point in the code.

```
         One process
 +-------------------------------+
 |  code   |  data   |  files    |   <- shared by all threads
 +-------------------------------+
 | regs    | regs    | regs      |
 | stack   | stack   | stack     |   <- private per thread
 | PC      | PC      | PC        |
 +---------+---------+-----------+
   thread1   thread2   thread3
```

> **Trap.** Because threads share the heap and globals, **race conditions** between threads are just as real as between processes ([Chapter 04](04_Process_Synchronization.md)). Separate processes are isolated by separate address spaces; threads are not.

---

## 3. Benefits of multithreading

Remember **R-R-E-S**:

1. **Responsiveness.** One thread can handle the user while another blocks on I/O.
2. **Resource sharing.** Threads share memory automatically. No IPC setup needed.
3. **Economy.** Creating a thread and switching between threads is **much cheaper** than for processes (often 10 to 100 times). No new address space.
4. **Scalability.** On a multicore machine, threads of one process can run **truly in parallel** on different cores.

### Concurrency vs parallelism

- **Concurrency:** multiple tasks make progress over the same period (can happen on one core by interleaving).
- **Parallelism:** multiple tasks **literally execute at the same instant** (needs multiple cores).

All parallelism is concurrency, but not all concurrency is parallelism.

---

## 4. User-level threads vs kernel-level threads

### User-level threads (ULT)

Managed by a **thread library in user space** (e.g. old "green threads"). The kernel doesn't even know they exist; it sees just one process.

- Fast to create and switch: no system calls.
- Can run on an OS that doesn't support threads.
- **If one thread makes a blocking system call, the kernel blocks the whole process**, so all its threads stop.
- No true parallelism: the kernel schedules the process on only one CPU.

### Kernel-level threads (KLT)

Managed **directly by the OS**. The kernel knows each thread and schedules them individually.

- One thread blocking doesn't block the others.
- Threads can run on different CPUs in parallel.
- Slower to create and switch (each operation is a system call).

| | User-level | Kernel-level |
|---|---|---|
| Managed by | Library in user space | OS kernel |
| Kernel aware? | No | Yes |
| Creation/switch cost | Low | Higher |
| Blocking call blocks... | **Whole process** | Only that thread |
| Multi-CPU parallelism | No | Yes |

---

## 5. Multithreading models: mapping user threads to kernel threads

### 5.1 Many-to-One

Many user threads → **one** kernel thread.

```
U U U U
 \ | | /
   K
```

- Thread management is fast (all in user space).
- **A single blocking system call blocks the entire process.**
- **No parallelism**: only one kernel thread, so only one CPU used.
- Example: Solaris Green Threads, early GNU Portable Threads.

### 5.2 One-to-One

Each user thread → **its own** kernel thread.

```
U  U  U  U
|  |  |  |
K  K  K  K
```

- True parallelism; a blocking call blocks only that thread.
- Creating a user thread means creating a kernel thread: **overhead**. Many systems cap the number of threads.
- **Used by Linux and Windows.**

### 5.3 Many-to-Many

Many user threads → a **smaller or equal number** of kernel threads.

```
U U U U U
 \ \|/ /
  K   K
```

- Best of both: create as many user threads as you like, they run in parallel on the available kernel threads, and a blocking call doesn't freeze everything.
- **Most complex** to implement.

A variant, the **two-level model**, is many-to-many but also allows binding a specific user thread to a specific kernel thread.

> **Most tested fact.** In **Many-to-One**, one blocking system call blocks **all** threads of the process.

---

## 6. The `fork()` system call

`fork()` creates a new process (the **child**) that is a near-exact copy of the caller (the **parent**). Both continue executing from the instruction **right after** the `fork()`.

How do they know who's who? The **return value**:

| Where | `fork()` returns |
|---|---|
| In the **child** | **0** |
| In the **parent** | The **child's PID** (a positive number) |
| On **failure** | A **negative** value (−1), no child is created |

```c
pid_t pid = fork();
if (pid < 0) {
    printf("fork failed\n");
} else if (pid == 0) {
    printf("I am the child\n");
} else {
    printf("I am the parent, my child is %d\n", pid);
}
```

Memory: after fork, parent and child have **separate copies** of variables. A change in one is **not** visible in the other. (Modern systems use **copy-on-write**: pages are shared until one side writes, then copied.)

### 6.1 Counting processes: the core rule

Each `fork()` call is executed by **every process that reaches it**, and each such execution **doubles** that process.

**n sequential `fork()` calls (no conditions) → 2ⁿ processes in total, 2ⁿ − 1 of them new (children).**

Example:
```c
fork();
fork();
fork();
printf("hi\n");
```
- After 1st fork: 2 processes.
- After 2nd: 4.
- After 3rd: 8.
- "hi" is printed **8** times. New processes created: **7**.

### 6.2 Forks in a loop

```c
for (int i = 0; i < n; i++)
    fork();
```
Same thing: **2ⁿ** total processes.

### 6.3 Printing inside the loop

```c
for (int i = 0; i < 3; i++) {
    fork();
    printf("x\n");
}
```

Count the prints per iteration:
- i=0: after fork, 2 processes, each prints → 2
- i=1: 4 processes print → 4
- i=2: 8 processes print → 8

Total = 2 + 4 + 8 = **14**. General formula: 2 + 4 + ... + 2ⁿ = **2ⁿ⁺¹ − 2**.

### 6.4 Forks with conditions (draw the tree!)

```c
if (fork() && fork())
    fork();
```

Use short-circuit rules: `A && B` evaluates B **only if A is true** (non-zero). In the parent, fork returns non-zero (true); in the child, it returns 0 (false).

- First `fork()`: parent (true) and child C1 (false).
  - C1: `&&` short-circuits. C1 skips the body.
  - Parent evaluates second `fork()`: parent (true) and child C2 (false).
    - C2: condition false. Skips the body.
    - Parent: condition true, executes the third `fork()`: parent + C3.

Total processes: parent, C1, C2, C3 = **4**.

```c
if (fork() || fork())
    fork();
```

`A || B` evaluates B **only if A is false**.

- First fork: parent (true), C1 (false).
  - Parent: `||` short-circuits to true, executes body fork: parent + C2.
  - C1: evaluates second fork: C1 (true) and C3 (false).
    - C1: true, executes body fork: C1 + C4.
    - C3: false, skips body.

Total: parent, C1, C2, C3, C4 = **5**.

**Method:** always draw a tree, marking each branch with the return value (non-zero for parent, 0 for child).

### 6.5 `exec()` and `wait()`

- `exec()` family (`execl`, `execvp`, ...) **replaces** the calling process's memory with a new program. If exec succeeds, **it never returns** (the old code is gone).
- `wait()` makes the parent pause until a child terminates, and collects its exit status (preventing a zombie).

Typical shell behaviour: `fork()`, then the child calls `exec("ls")`, and the parent calls `wait()`.

---

## 7. Threading issues worth knowing

- **fork() in a multithreaded program:** does the child duplicate all threads or just the calling one? UNIX systems typically provide both versions; POSIX `fork()` duplicates only the calling thread.
- **Thread cancellation:** asynchronous (kill immediately) vs deferred (thread checks periodically whether it should stop).
- **Thread pools:** create a fixed set of threads at startup and reuse them for tasks. Avoids creation cost and limits resource use.
- **Thread-local storage:** data that each thread gets its own copy of.

---

## 8. Exam traps

1. Threads share **code, data, heap, files**; they have their own **PC, registers, stack**.
2. `fork()` returns **0 in the child**, **child's PID in the parent**, **negative on failure**.
3. n forks → **2ⁿ total**, **2ⁿ − 1 new**.
4. Prints inside a loop with fork: **2ⁿ⁺¹ − 2**.
5. Many-to-One: blocking call blocks **all** threads; **no parallelism**.
6. One-to-One: Linux and Windows.
7. Parent and child after fork have **separate** copies of variables.
8. `exec` doesn't return on success.

---

## 9. Practice questions

**Q1.** Which of the following is NOT shared among threads of the same process?
(a) Global variables (b) Heap (c) Open files (d) Stack

**Answer: (d).**

---

**Q2.** What does `fork()` return in the parent process on success?
(a) 0 (b) −1 (c) The PID of the child (d) The PID of the parent

**Answer: (c).**

---

**Q3.** How many times is "Hello" printed?
```c
int main() {
    fork(); fork(); fork(); fork();
    printf("Hello\n");
}
```
(a) 4 (b) 8 (c) 15 (d) 16

**Answer: (d).** 2⁴ = 16 processes, each prints once.

---

**Q4.** In the program of Q3, how many child processes are created?
(a) 4 (b) 15 (c) 16 (d) 8

**Answer: (b).** 2⁴ − 1 = 15.

---

**Q5.** How many times is "A" printed?
```c
for (i = 0; i < 2; i++) {
    fork();
    printf("A");
}
```
(a) 4 (b) 6 (c) 8 (d) 3

**Answer: (b).** Iteration 0: 2 prints. Iteration 1: 4 prints. Total 6 = 2³ − 2.

Note: if output isn't flushed (no `\n` and stdout is buffered), buffered text gets duplicated by later forks and you may actually see more characters. Exam questions normally assume each `printf` produces output immediately.

---

**Q6.** How many processes exist after this code?
```c
if (fork() && fork()) fork();
```
(a) 3 (b) 4 (c) 5 (d) 8

**Answer: (b).** See section 6.4.

---

**Q7.** How many processes exist after this code?
```c
if (fork() || fork()) fork();
```
(a) 4 (b) 5 (c) 6 (d) 8

**Answer: (b).**

---

**Q8.**
```c
int x = 5;
if (fork() == 0) { x = x + 10; }
else { wait(NULL); x = x - 2; printf("%d", x); }
```
What does the parent print?
(a) 13 (b) 3 (c) 15 (d) 5

**Answer: (b).** The child's change to x happens in its own copy. Parent's x is 5 − 2 = 3.

---

**Q9.** Which model allows true parallelism but limits the number of threads because each user thread needs a kernel thread?
(a) Many-to-One (b) One-to-One (c) Many-to-Many (d) Two-level

**Answer: (b).**

---

**Q10.** User-level threads are generally faster to switch than kernel-level threads because:
(a) they use more registers (b) switching needs no kernel involvement (no mode switch) (c) they run on separate CPUs (d) they have no stack

**Answer: (b).**

---

**Q11.** A process using the Many-to-One model on a 4-core machine has 8 user threads. How many can execute simultaneously?
(a) 8 (b) 4 (c) 2 (d) 1

**Answer: (d).** Only one kernel thread exists, so only one CPU can run it.

---

**Q12.** If `exec()` succeeds, the statement immediately after it in the calling program:
(a) runs normally (b) is never executed (c) runs in the child only (d) runs twice

**Answer: (b).** The process image is replaced.

---

**Q13.** Which of the following is FALSE about threads?
(a) Thread creation is cheaper than process creation (b) Threads of a process share the address space (c) Each thread has its own program counter (d) Threads of a process cannot cause race conditions with each other

**Answer: (d).** They absolutely can, because they share data.

---

**Q14.** How many times is "x" printed?
```c
for (i = 0; i < 3; i++) fork();
printf("x");
```
(a) 3 (b) 6 (c) 7 (d) 8

**Answer: (d).** All 2³ = 8 processes print once after the loop.

---

**Practice questions:** [5.05 Threads and Multithreading](../ISRO_CS_Question_Bank/05_Operating_Systems/5.05_Threads_and_Multithreading.md)
