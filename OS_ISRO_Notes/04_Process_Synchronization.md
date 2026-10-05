# 04. Process Synchronization: Critical Sections and Semaphores

> **The heart of the problem.** When two processes touch the same data at the same time, the result can depend on *who ran when*. That makes bugs that appear once in a million runs. This chapter is about the tools that make shared data safe.

---

## 1. A bug you can't see by reading the code

Two processes share a variable `count = 5`. Process A does `count++`. Process B does `count--`. You'd expect the final value to be 5.

But `count++` is **not one instruction**. In machine code it's three:

```
A1: reg1 = count        (load)
A2: reg1 = reg1 + 1     (add)
A3: count = reg1        (store)
```

and `count--` is:

```
B1: reg2 = count
B2: reg2 = reg2 - 1
B3: count = reg2
```

The CPU can switch between A and B **after any instruction**. Consider this interleaving:

| Step | Executes | reg1 | reg2 | count |
|---|---|---|---|---|
| 1 | A1 | 5 | | 5 |
| 2 | A2 | 6 | | 5 |
| 3 | B1 | 6 | 5 | 5 |
| 4 | B2 | 6 | 4 | 5 |
| 5 | A3 | 6 | 4 | **6** |
| 6 | B3 | 6 | 4 | **4** |

Final value: **4**. If A3 and B3 were swapped: **6**. Only if one finished before the other started do we get the correct 5.

This is a **race condition**: the outcome depends on the precise order in which concurrent operations happen.

### Definition

A **race condition** occurs when several processes access and manipulate shared data concurrently, and the final result depends on the particular order of execution.

---

## 2. The critical-section problem

The part of a program that accesses shared data is called its **critical section (CS)**. If we ensure **no two processes are in their critical sections at the same time**, race conditions disappear.

Every process is structured like this:

```
do {
    [ entry section ]      <- ask permission to enter
        critical section   <- touch shared data
    [ exit section ]       <- announce you've left
        remainder section  <- everything else
} while (true);
```

The whole problem is: **design the entry and exit sections.**

### The three requirements of a correct solution

1. **Mutual Exclusion (ME).** At most **one** process is in its CS at any time.
2. **Progress.** If no process is in its CS and some want to enter, then **only those that want to enter** take part in deciding who goes next, and the decision **can't be postponed forever**. In plain words: *a process that isn't interested must not block one that is.*
3. **Bounded Waiting.** After a process requests entry, there is a **limit** on how many times others can enter before it gets its turn. In plain words: **no starvation**.

Plus an implicit assumption: we make **no assumptions about relative speeds** or the number of CPUs.

> **Exam trap.** ME and Progress are **mandatory**. Bounded waiting is often treated as the stronger/extra criterion: a solution may satisfy ME + Progress and still allow starvation.

Also note: Progress violation often looks like "deadlock" (nobody can enter) or "strict alternation" (an uninterested process blocks a willing one).

---

## 3. Software solutions for two processes

We have processes P0 and P1. In code, `i` is "me" and `j` is "the other one".

### Attempt 1: a `turn` variable (strict alternation)

```c
int turn = 0;               // shared

// Process Pi
while (turn != i) ;         // wait for my turn (busy wait)
    /* critical section */
turn = j;                   // give turn to the other
    /* remainder section */
```

- **ME: yes.** `turn` is either 0 or 1, so only one can pass.
- **Progress: NO.** Suppose P0 wants to enter twice in a row, but P1 is busy in its remainder section and doesn't care. After P0's first visit, `turn = 1`. P0 is stuck until P1 enters and leaves. An uninterested process is blocking an interested one.

Analogy: two people sharing a bicycle with a rule "we must alternate". If one person never wants to ride, the other can never ride twice.

### Attempt 2: a `flag[]` array (declare intent)

```c
bool flag[2] = {false, false};

// Process Pi
flag[i] = true;             // I want to enter
while (flag[j]) ;           // wait while the other wants to enter
    /* critical section */
flag[i] = false;
```

- **ME: yes.**
- **Progress: NO.** If both set their flags at the same time, both see the other's flag as true and **both wait forever**. That's a deadlock.

Analogy: two very polite people at a door, each saying "you first", forever.

### Peterson's solution (turn + flag)

```c
bool flag[2] = {false, false};
int turn;

// Process Pi
flag[i] = true;             // I want in
turn = j;                   // but I'll be polite: you go first if you want
while (flag[j] && turn == j) ;   // wait only if you want in AND it's your turn
    /* critical section */
flag[i] = false;
```

Why it works:

- **ME:** For both to be inside, both `while` conditions must be false. P0 inside means `flag[1] == false` or `turn == 0`. P1 inside means `flag[0] == false` or `turn == 1`. Both flags are true (both wanted in), so we'd need `turn == 0` **and** `turn == 1` simultaneously. Impossible. Whoever wrote `turn` **last** waits.
- **Progress:** If P1 isn't interested, `flag[1] == false`, so P0 walks straight in.
- **Bounded waiting:** After P1 exits, if it immediately tries again, it sets `turn = 0`, which lets P0 in. So P0 waits for at most **one** entry by P1.

**Peterson's satisfies all three requirements, but:**
- It works only for **exactly 2 processes**.
- It uses **busy waiting** (wastes CPU).
- On modern CPUs that reorder memory operations, it can fail without memory barriers.

> **Exam trap.** Peterson's does **not** generalise directly to N processes. (Lamport's Bakery algorithm does, but semaphores are the usual answer.)

### Summary

| Approach | ME | Progress | Bounded waiting | Problem |
|---|---|---|---|---|
| `turn` only | Yes | **No** | Yes | Strict alternation |
| `flag[]` only | Yes | **No** | n/a | Can deadlock |
| Peterson's | Yes | Yes | Yes | Only 2 processes, busy waiting |

---

## 4. Hardware support

Software-only solutions are clumsy. Hardware can help.

### 4.1 Disabling interrupts

On a **single CPU**, a context switch only happens because of an interrupt. So:

```
disable interrupts
    critical section
enable interrupts
```

No interrupt, no switch, so no one else runs. Simple!

But:
- On a **multiprocessor**, disabling interrupts on one CPU doesn't stop a process on **another** CPU from entering the CS.
- Disabling interrupts on all CPUs requires messaging them all: slow.
- Giving user programs the power to disable interrupts is dangerous (they may never re-enable).

So it's used only inside the kernel, for very short sections, on uniprocessors.

### 4.2 Test-and-Set (TAS)

An **atomic** hardware instruction. "Atomic" means it happens as **one indivisible step**; no other CPU can sneak in between the read and the write.

```c
bool test_and_set(bool *target) {   // executed atomically by hardware
    bool old = *target;
    *target = true;
    return old;
}
```

Using it as a lock:

```c
bool lock = false;

while (test_and_set(&lock)) ;   // spin until we get the old value false
    /* critical section */
lock = false;
```

The first process to call it gets back `false` (lock was free) and the lock becomes `true`. Everyone else gets back `true` and keeps spinning.

- ME: yes, even on multiprocessors (hardware guarantees atomicity on the memory word).
- Progress: yes.
- **Bounded waiting: NO** in this simple form. A fast process can keep grabbing the lock; an unlucky one might spin forever. (A more elaborate version with a `waiting[]` array fixes this.)

### 4.3 Compare-and-Swap (CAS)

```c
int compare_and_swap(int *value, int expected, int new_value) {  // atomic
    int temp = *value;
    if (*value == expected) *value = new_value;
    return temp;
}
```

Lock: `while (compare_and_swap(&lock, 0, 1) != 0) ;` Same idea as TAS; used widely in modern lock-free programming.

### 4.4 Spinlocks

A lock that **busy-waits** is called a **spinlock**. Busy waiting wastes CPU, but on a **multiprocessor** for a **very short** critical section, spinning can be cheaper than putting the thread to sleep and waking it (which needs two context switches).

---

## 5. Semaphores

A **semaphore** (invented by Dijkstra) is an integer variable `S` that can be touched only through two **atomic** operations:

```c
wait(S) {           // also called P(S), from Dutch "proberen" (to test)
    while (S <= 0) ;   // busy wait
    S--;
}

signal(S) {         // also called V(S), from "verhogen" (to increment)
    S++;
}
```

Think of `S` as the **number of available permits**.
- `wait` = "take a permit; if none, wait".
- `signal` = "return a permit".

### 5.1 Two kinds

| Type | Values | Use |
|---|---|---|
| **Binary semaphore** | 0 or 1 | Mutual exclusion (like a **mutex lock**) |
| **Counting semaphore** | Any non-negative integer (or negative in block/wakeup form) | A pool of **N identical resources** (e.g. 5 printers: init S = 5) |

### 5.2 Solving the critical-section problem with a semaphore

```c
semaphore mutex = 1;

// every process
wait(mutex);
    /* critical section */
signal(mutex);
```

This works for **any number of processes** (unlike Peterson's). It gives ME and progress. Basic semaphores do **not** guarantee bounded waiting by themselves (it depends on how the waiting list is managed; a FIFO queue fixes it).

### 5.3 Using semaphores for ordering

Semaphores can also enforce **"S1 must happen before S2"**:

```c
semaphore synch = 0;

P1:  S1;  signal(synch);
P2:  wait(synch);  S2;
```

P2 can't pass `wait` until P1 has done `signal`. So S2 always runs after S1. This "init to 0" pattern appears in many exam questions about precedence graphs.

### 5.4 Busy waiting vs block/wakeup implementation

The `while (S <= 0);` loop wastes CPU. The better implementation keeps a **waiting list** inside each semaphore:

```c
typedef struct {
    int value;
    struct process *list;
} semaphore;

wait(semaphore *S) {
    S->value--;
    if (S->value < 0) {
        add this process to S->list;
        block();               // Running -> Waiting; CPU given to someone else
    }
}

signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) {
        remove a process P from S->list;
        wakeup(P);             // Waiting -> Ready
    }
}
```

**Important consequence:** in this version, **the value can go negative**, and

```
if value < 0, |value| = number of processes waiting on the semaphore
```

Example: `value = −3` means 3 processes are blocked on it.

### 5.5 Semaphore arithmetic questions

These are very common. Rule: **each wait subtracts 1, each signal adds 1** (counting semaphore, block/wakeup).

Example: S = 10. Then 6 `wait` and 4 `signal` operations are performed. Final value = 10 − 6 + 4 = **8**.

Example: S = 7. Then 20 `wait` and 15 `signal`. Final = 7 − 20 + 15 = **2**.

Example: S = 2. Five processes call `wait`. Final value = 2 − 5 = −3, so **3** processes are blocked and 2 got through.

### 5.6 Deadlock with semaphores

```
P0:  wait(S);  wait(Q);  ...  signal(S);  signal(Q);
P1:  wait(Q);  wait(S);  ...  signal(Q);  signal(S);
```

If P0 gets S and P1 gets Q, each waits forever for the other. **Deadlock.**

Rule: **always acquire multiple semaphores in the same global order** in every process.

### 5.7 Mutex vs binary semaphore (a frequent interview and exam point)

| | Mutex | Binary semaphore |
|---|---|---|
| Ownership | **Has an owner**: only the thread that locked it should unlock it | **No owner**: any process can signal |
| Purpose | Mutual exclusion | Mutual exclusion **and** signalling/ordering |

---

## 6. The classical synchronization problems

These are the "standard test cases" for any synchronization tool.

### 6.1 Producer-Consumer (bounded buffer)

A producer puts items into a buffer of size n. A consumer takes them out.

Constraints:
- Producer must not add to a **full** buffer.
- Consumer must not remove from an **empty** buffer.
- Buffer access itself must be **mutually exclusive**.

Semaphores:
- `mutex = 1` (protects the buffer)
- `empty = n` (number of empty slots)
- `full = 0` (number of full slots)

```c
Producer:                         Consumer:
while (true) {                    while (true) {
    produce item;                     wait(full);      // any item?
    wait(empty);   // any space?      wait(mutex);
    wait(mutex);                      remove item;
    add item;                         signal(mutex);
    signal(mutex);                    signal(empty);   // one more space
    signal(full);  // one more item   consume item;
}                                 }
```

> **Critical trap: order of waits.** If the producer does `wait(mutex)` **before** `wait(empty)`, and the buffer is full, the producer holds the mutex while waiting for space. The consumer can never get the mutex to remove an item. **Deadlock.** Always do the **counting wait first**, then the mutex wait. The order of `signal`s doesn't matter for deadlock.

### 6.2 Readers-Writers

A shared database. **Many readers** can read at once (reading doesn't change anything). A **writer** needs exclusive access (no other writers and no readers).

First readers-writers problem (readers have priority):

```c
semaphore wrt = 1;      // writers' lock (also held by the group of readers)
semaphore mutex = 1;    // protects readcount
int readcount = 0;

Writer:                     Reader:
wait(wrt);                  wait(mutex);
    write;                  readcount++;
signal(wrt);                if (readcount == 1) wait(wrt);   // FIRST reader locks out writers
                            signal(mutex);
                                read;
                            wait(mutex);
                            readcount--;
                            if (readcount == 0) signal(wrt); // LAST reader lets writers in
                            signal(mutex);
```

Key idea: the readers act as a **group**. The first reader in grabs `wrt` for the whole group; the last reader out releases it.

Problem: if readers keep arriving, a writer may **starve**. (The second readers-writers problem gives writers priority; then readers may starve.)

### 6.3 Dining Philosophers

Five philosophers sit at a round table. Between each pair is one chopstick (5 total). To eat, a philosopher needs **both** adjacent chopsticks.

Naive solution:

```c
semaphore chopstick[5] = {1,1,1,1,1};

Philosopher i:
wait(chopstick[i]);            // left
wait(chopstick[(i+1) % 5]);    // right
    eat;
signal(chopstick[i]);
signal(chopstick[(i+1) % 5]);
```

**Deadlock:** if all five pick up their left chopstick simultaneously, all wait forever for the right one.

Fixes:
1. Allow at most **4 (N−1)** philosophers at the table at once. Then at least one can get both chopsticks.
2. Pick up **both chopsticks atomically** (inside a critical section), or only if both are free.
3. **Asymmetric** solution: odd-numbered philosophers pick left first; even-numbered pick right first. This breaks the circular wait.

Even with deadlock fixed, a philosopher could still **starve**; a full solution must also address that.

### 6.4 Sleeping Barber (brief)

A barber shop with one barber, one barber chair and n waiting chairs. If no customers, the barber sleeps. A customer wakes the barber, or waits in a chair, or leaves if all chairs are full. Solved with semaphores `customers`, `barber`, and a `mutex` protecting the count of waiting customers.

---

## 7. Monitors (high-level construct)

Semaphores are powerful but error-prone: swap a `wait` and a `signal` and you get either a deadlock or a broken ME, and it's hard to spot.

A **monitor** is a programming-language construct that bundles shared data with the procedures that operate on it, and **guarantees that only one process is active inside the monitor at a time**. ME comes for free.

For waiting inside a monitor, it provides **condition variables**:
- `x.wait()`: suspend the caller until someone signals x.
- `x.signal()`: resume exactly one waiting process (if none is waiting, **nothing happens**; unlike a semaphore, the signal is not remembered).

Java's `synchronized` methods with `wait()/notify()` are monitor-like.

---

## 8. Exam traps

1. `count++` is not atomic; it's load-add-store.
2. ME and Progress are mandatory; bounded waiting prevents starvation.
3. `turn`-only fails **Progress** (strict alternation). `flag`-only can **deadlock** (also a Progress failure).
4. Peterson's: correct for **2** processes only.
5. Disabling interrupts **doesn't work on multiprocessors**.
6. TAS spinlock: ME yes, bounded waiting **no** (simple version).
7. Block/wakeup semaphore: negative value = number of waiting processes.
8. Producer-consumer: `wait(empty/full)` **before** `wait(mutex)`, otherwise deadlock.
9. Readers-writers: first reader locks `wrt`, last reader unlocks it.
10. Naive dining philosophers can **deadlock**.
11. Monitor `signal()` with no waiter is lost; semaphore `signal()` is remembered (it increments).

---

## 9. Practice questions

**Q1.** Two processes each execute `x = x + 1` (implemented as load, add, store) once. Initially x = 0. Possible final values of x are:
(a) only 2 (b) only 1 (c) 1 or 2 (d) 0, 1 or 2

**Answer: (c).** If both load 0 before either stores, final is 1. If they don't overlap, 2. It can never be 0 because each process stores at least the value 1.

---

**Q2.** A counting semaphore S is initialised to 10. Then 6 `P` operations and 4 `V` operations are performed. Final value of S?
(a) 0 (b) 8 (c) 10 (d) 12

**Answer: (b).** 10 − 6 + 4 = 8.

---

**Q3.** A semaphore is initialised to 2. Five processes execute `wait()` one after another (no `signal`). Using block/wakeup, how many processes are blocked and what's the value?
(a) 3 blocked, value −3 (b) 5 blocked, value −5 (c) 3 blocked, value 0 (d) 2 blocked, value −2

**Answer: (a).** 2 − 5 = −3. Two got permits, three are blocked.

---

**Q4.** In the strict-alternation (`turn` variable) solution, which requirement is violated?
(a) Mutual exclusion (b) Progress (c) Both (d) None

**Answer: (b).**

---

**Q5.** Which of the following satisfies mutual exclusion, progress and bounded waiting for two processes using only shared variables?
(a) Strict alternation (b) Flag array alone (c) Peterson's algorithm (d) Disabling interrupts

**Answer: (c).**

---

**Q6.** In the producer-consumer solution, if the producer executes `wait(mutex)` before `wait(empty)`, the problem that can occur when the buffer is full is:
(a) Starvation of producer (b) Deadlock (c) Buffer overflow (d) Nothing

**Answer: (b).** Producer holds mutex and waits for space; consumer can't acquire mutex to make space.

---

**Q7.** In the first readers-writers solution, which statement is TRUE?
(a) Writers may starve (b) Readers may starve (c) Neither starves (d) Only one reader can read at a time

**Answer: (a).** A steady stream of readers keeps `wrt` held forever.

---

**Q8.** In the dining philosophers problem with 5 philosophers, the maximum number who can eat simultaneously is:
(a) 1 (b) 2 (c) 3 (d) 5

**Answer: (b).** Each eater needs 2 of the 5 chopsticks, and neighbours can't eat at the same time. floor(5/2) = 2.

---

**Q9.** Which hardware approach to mutual exclusion fails on a multiprocessor?
(a) Test-and-Set (b) Compare-and-Swap (c) Disabling interrupts (d) Swap instruction

**Answer: (c).**

---

**Q10.** Consider:
```
P1: S1; signal(a);
P2: wait(a); S2; signal(b);
P3: wait(b); S3;
```
with semaphores a = 0, b = 0. The execution order is always:
(a) S1, S2, S3 (b) S3, S2, S1 (c) arbitrary (d) S2, S1, S3

**Answer: (a).** Each semaphore initialised to 0 forces a "happens-before" link.

---

**Q11.** A binary semaphore differs from a mutex mainly because:
(a) a binary semaphore can count above 1 (b) a mutex has an owner that must release it; a semaphore can be signalled by any process (c) a mutex never blocks (d) they are identical in every way

**Answer: (b).**

---

**Q12.** A busy-waiting lock is called a:
(a) monitor (b) spinlock (c) barrier (d) condition variable

**Answer: (b).**

---

**Q13.** In a monitor, if a process calls `x.signal()` and no process is waiting on condition x:
(a) the signal is saved for the next wait (b) nothing happens (c) deadlock occurs (d) the monitor is released

**Answer: (b).** This is the key difference from a semaphore's `signal`, which increments the value and is therefore "remembered".

---

**Q14.** Two processes use semaphores S and Q (both initialised to 1):
```
P0: wait(S); wait(Q); ... signal(Q); signal(S);
P1: wait(Q); wait(S); ... signal(S); signal(Q);
```
This can lead to:
(a) Starvation only (b) Deadlock (c) Violation of ME (d) Nothing wrong

**Answer: (b).** Inconsistent lock order creates a possible circular wait.

---

**Q15.** A counting semaphore initialised to 4 protects a pool of printers. At some moment its value is −2. How many printers are in use and how many processes are waiting?
(a) 4 in use, 2 waiting (b) 2 in use, 2 waiting (c) 4 in use, 0 waiting (d) 6 in use

**Answer: (a).** All 4 permits are taken; |−2| = 2 waiting.

---

**Q16.** The simple Test-and-Set spinlock does NOT guarantee:
(a) Mutual exclusion (b) Progress (c) Bounded waiting (d) Atomicity

**Answer: (c).**

---

**Q17.** Which is the correct structure of the reader's entry section in the first readers-writers problem?
(a) `wait(wrt)` for every reader (b) `wait(mutex); readcount++; if (readcount == 1) wait(wrt); signal(mutex);` (c) `wait(mutex); wait(wrt);` (d) No synchronization needed for readers

**Answer: (b).**

---

**Q18.** Which fix does NOT prevent deadlock in dining philosophers?
(a) Allow only 4 philosophers to sit at once (b) Odd philosophers pick left first, even pick right first (c) Pick up both chopsticks only if both are available (d) Every philosopher picks left first, then right

**Answer: (d).** That's the naive deadlock-prone version.

---

**Practice questions:** [5.04 Process Synchronization Semaphores](../ISRO_CS_Question_Bank/05_Operating_Systems/5.04_Process_Synchronization_Semaphores.md)
