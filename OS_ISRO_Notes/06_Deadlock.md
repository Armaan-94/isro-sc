# 06. Deadlock: Prevention, Avoidance, Detection and Recovery

> **Picture this.** A narrow one-lane bridge. A car enters from each side. They meet in the middle. Neither can move forward, neither will reverse. They will wait forever. That is a deadlock.

---

## 1. What is a deadlock?

A set of processes is **deadlocked** when **every** process in the set is waiting for an event (usually a resource release) that **only another process in the same set** can cause.

Since everyone is waiting for someone else in the group, nobody ever moves.

Simple example with two resources:

```
P1 holds the Printer, wants the Scanner.
P2 holds the Scanner, wants the Printer.
```

Neither will release what it holds until it gets what it wants. Stuck.

### The normal life of a resource

Every process uses a resource in three steps:

1. **Request** (if not available, wait)
2. **Use**
3. **Release**

Deadlock happens when processes get stuck at step 1 forever.

### Deadlock vs starvation

| | Deadlock | Starvation |
|---|---|---|
| Waiting | **Forever** (without outside help) | **Indefinitely long**, but could eventually be served |
| Cause | Circular waiting among processes | Unfair scheduling (others always preferred) |
| Who is stuck | A set of processes waiting on each other | One process being skipped repeatedly |
| Example | Two cars on a one-lane bridge | A low-priority job that never gets the CPU |

Deadlock implies the involved processes starve; starvation does **not** imply deadlock.

---

## 2. The four necessary conditions (Coffman conditions)

Deadlock can happen **only if all four** hold **at the same time**:

1. **Mutual Exclusion.** At least one resource is **non-sharable**: only one process can use it at a time (e.g. a printer).
2. **Hold and Wait.** A process is **holding** at least one resource **while waiting** for another.
3. **No Preemption.** Resources can't be **forcibly taken** away; a process releases a resource only voluntarily.
4. **Circular Wait.** There is a cycle P0 → P1 → P2 → ... → Pn → P0 where each waits for a resource held by the next.

Memory hook: **"M-H-N-C"**, or "**My Hungry Neighbour Cooks**".

> **Key insight.** Because **all four** are needed, **breaking any one** of them makes deadlock impossible. Every prevention technique is just "pick one condition and break it".

Note: circular wait actually implies hold-and-wait, so the four aren't fully independent, but exams treat them as four.

---

## 3. Resource Allocation Graph (RAG)

A picture of who holds what and who wants what.

- **Circle** = a process.
- **Rectangle** = a resource type; **dots** inside = instances.
- **Request edge** P → R: process P is waiting for an instance of R.
- **Assignment edge** R → P: an instance of R is allocated to P.

```
   (P1) ----request----> [R1 •]
     ^                      |
     |assigned              |assigned
     |                      v
   [R2 •] <---request---- (P2)
```

Here P1 holds R2 and wants R1; P2 holds R1 and wants R2. There's a cycle P1 → R1 → P2 → R2 → P1, and each resource has one instance: **deadlock**.

### The two rules you must know

| Situation | Cycle in the RAG means |
|---|---|
| **Every resource type has a single instance** | Cycle is **necessary and sufficient**: cycle ⟺ deadlock |
| **Some resource type has multiple instances** | Cycle is **necessary but not sufficient**: deadlock is **possible**, not certain |
| **No cycle at all** | **No deadlock**, in any case |

Why can a multi-instance cycle be harmless? Suppose R1 has 2 instances; one is held by P2 (in the cycle) and one by P3 (not in the cycle). When P3 finishes and releases its R1, P1 can get it and the cycle breaks.

### Wait-for graph

If all resources are single-instance, you can simplify the RAG by removing resource nodes: draw Pi → Pj if Pi is waiting for a resource Pj holds. A **cycle in the wait-for graph = deadlock**. This is what detection algorithms use.

---

## 4. Four ways to handle deadlock

| Strategy | Idea | Cost |
|---|---|---|
| **Prevention** | Design the system so one of the 4 conditions can **never** hold | Low resource utilization, reduced concurrency |
| **Avoidance** | Allow the conditions, but **check every request** and refuse any that might lead to an unsafe state (Banker's algorithm) | Needs advance knowledge of max needs; runtime overhead |
| **Detection and recovery** | Let deadlocks happen, **detect** them periodically, then **recover** | Detection overhead, lost work |
| **Ignorance (Ostrich algorithm)** | Pretend deadlocks never happen | Rare manual reboot |

> **Exam fact.** **Linux and Windows** use the **ostrich approach** for general resources. Deadlocks are rare, and the cost of prevention/avoidance is too high for normal systems.

---

## 5. Deadlock prevention (break a condition)

### Break Mutual Exclusion

Make resources sharable. Works for read-only files. But **a printer is inherently non-sharable**; you can't print two documents on the same sheet at once.

So **mutual exclusion generally cannot be denied**. (Spooling partially helps: processes "print" to a spool file, and only the printer daemon touches the real printer.)

### Break Hold and Wait

Ensure a process never holds something while waiting for something else:

- **Option A:** request **all** resources **at once, before starting**. If you can't get all, get none.
- **Option B:** a process may request resources only when it holds **none**. Release everything before asking for more.

Drawbacks: **low resource utilization** (you hold things you won't use for hours) and **possible starvation** (a process needing many popular resources may wait forever).

### Break No Preemption

Allow resources to be taken away:

- If a process holding some resources requests one that isn't available, it **releases everything** it holds and restarts later.
- Or: if the requested resource is held by another **waiting** process, preempt it from that process.

Works well for resources whose state can be **saved and restored** easily (CPU registers, memory). **Doesn't work** for printers or tape drives (you can't "un-print" half a page).

### Break Circular Wait (the most practical)

Give every resource type a **unique number**, and require that each process requests resources **only in increasing order** of number.

Example: Tape = 1, Disk = 5, Printer = 12. A process that needs Disk and Printer must request Disk (5) first, then Printer (12).

**Why does this kill cycles?** In a cycle P0 → P1 → ... → P0, each process holds a resource and waits for a higher-numbered one held by the next. Following the cycle, the numbers would have to keep increasing and yet return to the start: R0 < R1 < ... < R0. Impossible.

---

## 6. Deadlock avoidance and the Banker's algorithm

Prevention is too strict. **Avoidance** is smarter: allow normal behaviour, but before granting each request, **look ahead** and make sure the system stays **safe**.

Requirement: each process must declare in advance the **maximum** number of each resource it might ever need.

### 6.1 Safe and unsafe states

A state is **safe** if there exists **at least one order** (a **safe sequence**) in which every process can get its maximum need and finish, one after another.

```
         +-----------------------------+
         |          UNSAFE             |
         |     +----------------+      |
         |     |   DEADLOCK     |      |
         |     +----------------+      |
         +-----------------------------+
   SAFE (outside all of this)
```

- **Safe ⟹ no deadlock.**
- **Unsafe ⟹ deadlock is POSSIBLE**, not certain. (Processes might not actually request their maximum.)
- **Deadlock ⊂ Unsafe.**

> **Trap.** "Unsafe state means deadlock" is **false**. Unsafe means "we can no longer *guarantee* avoiding deadlock".

Avoidance = **never enter an unsafe state.**

### 6.2 Why the name "Banker's"?

A bank has limited cash. Customers each have a credit limit (max need). The banker grants a loan only if, even after granting it, the bank can still satisfy **every** customer's remaining needs in some order. Never let the bank get into a position where it might not be able to pay out.

### 6.3 Data structures

n processes, m resource types:

```
Available[m]       free instances of each type right now
Max[n][m]          maximum demand of each process
Allocation[n][m]   currently allocated to each process
Need[n][m]         = Max − Allocation   (what each process may still ask for)
```

Also, Total resources = Available + sum of all Allocations (useful when only totals are given).

### 6.4 Safety algorithm

```
1. Work = Available; Finish[i] = false for all i.
2. Find an i with Finish[i] == false AND Need[i] <= Work (component-wise).
   If none, go to step 4.
3. Work = Work + Allocation[i]   (Pi finishes and returns everything)
   Finish[i] = true; go to step 2.
4. If all Finish[i] == true, the state is SAFE; the order you picked is a safe sequence.
```

Intuition for step 3: if Pi can get everything it still needs, it will eventually finish and **release everything it holds** (its Allocation). So the pool grows.

Complexity: O(m × n²).

### 6.5 Resource-request algorithm

When process Pi requests a vector Request[i]:

```
1. If Request[i] > Need[i]       -> ERROR (asking more than declared max)
2. If Request[i] > Available     -> Pi must WAIT (not enough right now)
3. Pretend to grant:
     Available    -= Request[i]
     Allocation[i]+= Request[i]
     Need[i]      -= Request[i]
   Run the safety algorithm on this new state.
     SAFE   -> grant for real
     UNSAFE -> undo the pretend changes; Pi waits
```

### 6.6 Full worked example

Resources A, B, C. Total = (10, 5, 7).

| | Allocation | Max | Need = Max − Alloc |
|---|---|---|---|
| P0 | 0 1 0 | 7 5 3 | 7 4 3 |
| P1 | 2 0 0 | 3 2 2 | 1 2 2 |
| P2 | 3 0 2 | 9 0 2 | 6 0 0 |
| P3 | 2 1 1 | 2 2 2 | 0 1 1 |
| P4 | 0 0 2 | 4 3 3 | 4 3 1 |

Sum of Allocation = (7, 2, 5), so Available = (10, 5, 7) − (7, 2, 5) = **(3, 3, 2)**.

**Is it safe?** Work = (3, 3, 2).

| Step | Pick | Need ≤ Work? | New Work |
|---|---|---|---|
| 1 | P1 | (1,2,2) ≤ (3,3,2) yes | (3,3,2) + (2,0,0) = (5,3,2) |
| 2 | P3 | (0,1,1) ≤ (5,3,2) yes | + (2,1,1) = (7,4,3) |
| 3 | P4 | (4,3,1) ≤ (7,4,3) yes | + (0,0,2) = (7,4,5) |
| 4 | P2 | (6,0,0) ≤ (7,4,5) yes | + (3,0,2) = (10,4,7) |
| 5 | P0 | (7,4,3) ≤ (10,4,7) yes | + (0,1,0) = (10,5,7) |

All finish. **Safe sequence: P1, P3, P4, P2, P0.** (Other safe sequences may exist; you only need one.)

Notice the final Work equals the Total. That's always true and is a great self-check.

**Request 1:** P1 requests (1, 0, 2).
- (1,0,2) ≤ Need P1 (1,2,2)? Yes.
- (1,0,2) ≤ Available (3,3,2)? Yes.
- Pretend: Available = (2,3,0), Alloc P1 = (3,0,2), Need P1 = (0,2,0).
- Safety: Work (2,3,0). P1 (0,2,0) ok → (5,3,2). P3 (0,1,1) ok → (7,4,3). P4 (4,3,1) ok → (7,4,5). P0 (7,4,3) ok → (7,5,5). P2 (6,0,0) ok → (10,5,7). **Safe, so grant.**

**Request 2 (after granting request 1):** P4 requests (3, 3, 0). Available is (2,3,0). 3 > 2 for A. **P4 must wait.**

**Request 3 (after request 1):** P0 requests (0, 2, 0).
- ≤ Need P0 (7,4,3) yes; ≤ Available (2,3,0) yes.
- Pretend: Available = (2,1,0).
- Safety: Work (2,1,0). P0 needs (7,2,3): no. P1 (0,2,0): B 2 > 1, no. P2 (6,0,0): no. P3 (0,1,1): C 1 > 0, no. P4 (4,3,1): no. **Nobody can proceed: UNSAFE. Deny**; P0 waits.

### 6.7 Single-resource-type shortcut questions

Very popular in ISRO/GATE.

**Pattern 1:** n processes, each needs at most k instances of a resource. What is the **minimum** number of instances R that **guarantees** no deadlock?

Worst case: every process holds k − 1 and waits for one more. That uses n(k − 1) instances with everyone stuck. One extra instance lets someone finish, then releases cascade.

```
R_min = n(k − 1) + 1
```

Example: 3 processes, each needs 2 printers. R_min = 3(1) + 1 = **4**.

Example: 5 processes, each needs 3 tape drives. R_min = 5(2) + 1 = **11**.

**Pattern 2:** R instances and n processes, process i needs at most maxᵢ. Deadlock-free if

```
Σ maxᵢ < R + n         (equivalently Σ (maxᵢ − 1) < R, i.e. Σ (maxᵢ − 1) + 1 ≤ R)
```

Example: R = 6, n = 3, max needs 3, 3, 2. Σ(max − 1) = 2 + 2 + 1 = 5 < 6, so deadlock-free.

**Pattern 3:** R instances, each process needs k. **Maximum** number of processes n that is guaranteed deadlock-free: largest n with n(k − 1) + 1 ≤ R, i.e. **n ≤ (R − 1)/(k − 1)**.

Example: R = 7, k = 3: n ≤ 6/2 = 3. So **3** processes.

---

## 7. Deadlock detection

If we don't prevent or avoid, we can **detect** deadlocks after they happen.

### Single-instance resources

Maintain a **wait-for graph** and periodically search for a **cycle**. Cycle detection costs O(n²) (n = number of processes).

### Multiple-instance resources

Use an algorithm very similar to the safety algorithm, but with **Request** (what each process is currently asking for) instead of Need:

```
1. Work = Available. For each i: Finish[i] = (Allocation[i] == 0) ? true : false
2. Find i with Finish[i] == false and Request[i] <= Work. If none, go to 4.
3. Work += Allocation[i]; Finish[i] = true; go to 2.
4. Any i with Finish[i] == false is DEADLOCKED.
```

Difference from avoidance: we don't need Max, and we're optimistic (assume a process that gets its current request will finish and release).

### When to run detection?

- After **every** request that can't be granted immediately (expensive but catches deadlock instantly and identifies the culprit).
- **Periodically** (e.g. once an hour).
- When **CPU utilization drops** below some threshold (deadlocked processes don't use the CPU).

---

## 8. Recovery from deadlock

### Option 1: Process termination

- **Abort all** deadlocked processes. Simple, guaranteed, but wastes all their work.
- **Abort one at a time** until the cycle disappears. Re-run detection after each abort. Less waste, more overhead.

Whom to kill first? Consider: priority, how long it has run and how much is left, resources held, resources still needed, how many processes would need to be killed, interactive or batch.

### Option 2: Resource preemption

Take resources away from some processes and give them to others. Three issues:

1. **Selecting a victim** (minimise cost).
2. **Rollback**: the victim must be rolled back to a safe state (often a total restart, or a checkpoint).
3. **Starvation**: the same process might be chosen as victim again and again. Fix: include the **number of rollbacks** in the cost factor.

---

## 9. Exam traps

1. **All four** conditions needed; breaking one prevents deadlock.
2. **Mutual exclusion usually can't be broken.**
3. Resource ordering breaks **circular wait**.
4. Single-instance: **cycle ⟺ deadlock**. Multi-instance: cycle = **possible** deadlock.
5. **Unsafe ≠ deadlock.** Deadlock ⊂ unsafe.
6. Banker's needs **maximum needs in advance**.
7. Banker's safety check: final Work should equal Total.
8. **R_min = n(k − 1) + 1.**
9. Linux/Windows: **ostrich**.
10. Track rollback count to prevent victim starvation.

---

## 10. Practice questions

**Q1.** A system has 3 processes, each needing at most 3 units of a resource. Minimum number of units that guarantees no deadlock?
(a) 6 (b) 7 (c) 8 (d) 9

**Answer: (b).** 3(3 − 1) + 1 = 7.

---

**Q2.** A system has 6 tape drives, and n processes each need at most 2 drives. Maximum n for which the system is guaranteed deadlock-free?
(a) 4 (b) 5 (c) 6 (d) 3

**Answer: (b).** n(2 − 1) + 1 ≤ 6 gives n ≤ 5.

---

**Q3.** Which condition is broken by requiring processes to request resources in increasing order of resource number?
(a) Mutual exclusion (b) Hold and wait (c) No preemption (d) Circular wait

**Answer: (d).**

---

**Q4.** Requiring a process to request all its resources before starting execution breaks:
(a) Mutual exclusion (b) Hold and wait (c) No preemption (d) Circular wait

**Answer: (b).**

---

**Q5.** In a RAG where each resource has exactly one instance, a cycle indicates:
(a) possible deadlock (b) definite deadlock (c) no deadlock (d) starvation

**Answer: (b).**

---

**Q6.** Which statement is TRUE?
(a) Every unsafe state is a deadlock (b) Every deadlock state is unsafe (c) Every safe state leads to deadlock (d) Safe and unsafe states are the same

**Answer: (b).**

---

**Q7.** Banker's algorithm. Single resource type, total = 12.

| | Max | Allocation |
|---|---|---|
| P0 | 10 | 5 |
| P1 | 4 | 2 |
| P2 | 9 | 2 |

Is the state safe? If yes, give a safe sequence.
(a) Unsafe (b) Safe: P1, P0, P2 (c) Safe: P0, P1, P2 (d) Safe: P2, P1, P0

**Answer: (b).** Available = 12 − 9 = 3. Needs: P0 = 5, P1 = 2, P2 = 7. Work 3: P1 (2) ok → Work = 3 + 2 = 5. P0 (5) ok → 10. P2 (7) ok → 12. Safe sequence P1, P0, P2.

---

**Q8.** Same system as Q7. Now P2 requests 1 more unit. Should it be granted?
(a) Yes, state remains safe (b) No, state becomes unsafe (c) Error, exceeds max (d) Must wait, not available

**Answer: (b).** Pretend: Available = 2, P2 Alloc = 3, P2 Need = 6. Work 2: P1 needs 2, ok → 4. P0 needs 5 > 4. P2 needs 6 > 4. Stuck. Unsafe, so deny. (This is the textbook example of how one innocent-looking request can push the system into an unsafe state.)

---

**Q9.** Which deadlock-handling method requires each process to declare its maximum resource needs in advance?
(a) Prevention (b) Avoidance (c) Detection (d) Ostrich

**Answer: (b).**

---

**Q10.** System with 4 identical resources and 3 processes. Max needs: P1 = 2, P2 = 2, P3 = 2. Can deadlock occur?
(a) Yes (b) No (c) Only if preemption allowed (d) Cannot determine

**Answer: (b).** Σ(max − 1) = 3 < 4. Deadlock-free.

---

**Q11.** System with 5 units of a resource. Three processes with maximum needs 3, 2, 2. Can deadlock occur?
(a) Yes (b) No

**Answer: (b) No.** Σ(max − 1) = 2 + 1 + 1 = 4 < 5, so the system is deadlock-free. Verify with the worst case: P1 holds 2, P2 holds 1, P3 holds 1. That's 4 units, so 1 unit is still free. Whoever gets it can finish and release everything. No deadlock.

(Many students rush and say "yes" because three processes compete. Always run the formula.)

---

**Q12.** Which deadlock recovery issue is addressed by counting the number of times a process has been rolled back?
(a) Victim selection cost (b) Starvation (c) Rollback point (d) Detection frequency

**Answer: (b).**

---

**Q13.** Which of the following is not a necessary condition for deadlock?
(a) Mutual exclusion (b) Hold and wait (c) Preemption (d) Circular wait

**Answer: (c).** The condition is **no** preemption. Allowing preemption actually helps prevent deadlock.

---

**Q14.** A system is in a safe state. Which is guaranteed?
(a) No process is waiting (b) The system can allocate resources to every process up to its max in some order without deadlock (c) All resources are free (d) Deadlock will never occur no matter how requests are granted

**Answer: (b).** Note (d) is wrong: if the OS later grants requests carelessly, it can still move into an unsafe state.

---

**Q15.** Which of the following is the most practical prevention technique used in real systems (for example in kernel lock design)?
(a) Breaking mutual exclusion (b) Lock ordering to prevent circular wait (c) Requesting all resources at start (d) Preempting printers

**Answer: (b).**

---

**Practice questions:** [5.06 Deadlock](../ISRO_CS_Question_Bank/05_Operating_Systems/5.06_Deadlock.md)
