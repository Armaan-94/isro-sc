# 12. Transactions, ACID and Serializability

> **The question this chapter answers.** Many users hit the database at the same time, and the machine can crash at any moment. How do we make sure the data still ends up correct? The answer is the **transaction**, its **ACID** guarantees, and the theory of **serializability**, which is the most numerically tested part of DBMS.

---

## 1. What is a transaction?

A **transaction** is a sequence of database operations that forms **one logical unit of work**: it must happen **completely or not at all**.

Example: transfer ₹500 from A to B.

```
T1:  read(A)
     A = A − 500
     write(A)
     read(B)
     B = B + 500
     write(B)
     commit
```

If the system crashes after `write(A)` but before `write(B)`, ₹500 vanishes. A transaction prevents that.

The two basic operations:
- **read(X):** bring X from the database (disk/buffer) into a local variable.
- **write(X):** write the local variable back to the database (buffer, later disk).

---

## 2. ACID properties

| Property | Meaning | Who ensures it |
|---|---|---|
| **Atomicity** | All or nothing. A failed transaction leaves **no** partial effects | **Recovery manager** (undo using the log) |
| **Consistency** | A transaction takes the database from one consistent state to another (e.g. A + B unchanged in a transfer) | **Programmer** + integrity constraints |
| **Isolation** | Concurrent transactions don't see each other's intermediate states; the result is as if they ran one at a time | **Concurrency control manager** |
| **Durability** | Once committed, changes survive any later failure | **Recovery manager** (redo using the log) |

> **Trap.** Isolation ↔ concurrency control. Atomicity and Durability ↔ recovery. Consistency ↔ the programmer.

---

## 3. Transaction states

```
          +---------> Partially committed ------> Committed
          |                    |
       Active                  | (failure before
          |                    |  changes are durable)
          v                    v
        Failed  -----------> Aborted
```

- **Active:** executing.
- **Partially committed:** the last statement has executed, but the results may still be only in memory. A failure now can still force an abort.
- **Committed:** completed successfully; changes are durable.
- **Failed:** normal execution can't continue (error, crash, deadlock victim).
- **Aborted:** rolled back; the database is restored to its state before the transaction. The system may then **restart** the transaction (if the failure wasn't a logical error) or **kill** it.

A transaction is **terminated** once it's committed or aborted.

---

## 4. Why allow concurrency at all?

Running transactions one by one (serially) is always correct but slow. Concurrency gives:
- better **throughput** (one transaction uses the CPU while another waits for disk),
- better **resource utilization**,
- shorter **average response time** (short transactions don't wait behind long ones).

But naive interleaving causes anomalies.

---

## 5. Concurrency anomalies

### 5.1 Lost update (write-write)

A = 100. T1 and T2 both want to add to A.

| T1 | T2 |
|---|---|
| read(A) → 100 | |
| | read(A) → 100 |
| A = A + 50 | |
| | A = A + 20 |
| write(A) → 150 | |
| | write(A) → 120 |

Final A = 120. T1's update is **lost**. Correct answer: 170.

### 5.2 Dirty read (write-read)

| T1 | T2 |
|---|---|
| write(A) (A = 50, uncommitted) | |
| | read(A) → 50 |
| **abort** (A back to 100) | |
| | uses 50, which **never really existed** |

### 5.3 Unrepeatable read (read-write)

| T1 | T2 |
|---|---|
| read(A) → 100 | |
| | write(A) = 80; commit |
| read(A) → 80 | |

T1 reads the same item twice and gets different values.

### 5.4 Phantom read

T1 runs `SELECT COUNT(*) FROM Emp WHERE dept='CS'` → 3. T2 inserts a new CS employee and commits. T1 runs it again → 4. **New rows** ("phantoms") appeared.

> **Trap.** Dirty read = reading **uncommitted** data. Unrepeatable read = the **same item** changes between two reads (even if everything is committed). Phantom = the **set of rows** matching a condition changes.

### 5.5 SQL isolation levels

| Level | Dirty read | Unrepeatable read | Phantom |
|---|---|---|---|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | **Prevented** | Possible | Possible |
| REPEATABLE READ | Prevented | **Prevented** | Possible |
| SERIALIZABLE | Prevented | Prevented | **Prevented** |

---

## 6. Schedules

A **schedule** is an ordering of the operations of several transactions, **preserving the order within each transaction**.

- **Serial schedule:** transactions run one after another, no interleaving. With n transactions there are **n!** serial schedules. All are correct (assuming each transaction is consistent on its own).
- **Non-serial (concurrent) schedule:** operations interleave.

### 6.1 Counting schedules

If transactions T1, ..., Tk have n1, ..., nk operations, the number of **all possible schedules** is the number of ways to interleave them keeping each one's internal order:

```
Total = (n1 + n2 + ... + nk)! / (n1! × n2! × ... × nk!)
Serial = k!
Non-serial = Total − k!
```

**Example:** T1 has 2 operations, T2 has 3.
Total = 5!/(2! × 3!) = 120/12 = **10**. Serial = 2. Non-serial = **8**.

**Example:** three transactions with 2 operations each.
Total = 6!/(2! 2! 2!) = 720/8 = **90**. Serial = 6. Non-serial = **84**.

The goal: find which non-serial schedules are **as good as** some serial schedule. That property is **serializability**.

---

## 7. Conflict serializability

### 7.1 Conflicting operations

Two operations **conflict** if **all three** hold:
1. They belong to **different** transactions.
2. They access the **same** data item.
3. **At least one** is a **write**.

| Pair (different txns, same item) | Conflict? |
|---|---|
| R – R | **No** |
| R – W | Yes |
| W – R | Yes |
| W – W | Yes |

Non-conflicting adjacent operations can be **swapped** without changing the result. Conflicting ones can't (swapping changes what's read or what's finally written).

### 7.2 Definition

A schedule is **conflict serializable** if it can be turned into a serial schedule by swapping **adjacent non-conflicting** operations. Equivalently, if it's **conflict equivalent** to some serial schedule.

### 7.3 The precedence graph test (the method you'll use every time)

1. Draw one **node** per transaction.
2. For each pair of **conflicting** operations where Ti's operation comes **before** Tj's, draw an edge **Ti → Tj**.
3. **No cycle ⟺ conflict serializable.**
4. If acyclic, any **topological order** of the graph is an equivalent serial schedule.

**Tip:** for each data item, scan the schedule and look at every pair (earlier op, later op) from different transactions where at least one is a write. Don't just check neighbours.

### 7.4 Worked example 1 (serializable)

S: R1(A) R2(B) W1(A) R3(A) W2(B) W3(A)

- Item A: R1(A), W1(A), R3(A), W3(A).
  - R1(A) vs W3(A): T1 → T3.
  - W1(A) vs R3(A): T1 → T3.
  - W1(A) vs W3(A): T1 → T3.
  - R1(A) vs R3(A): read-read, no conflict.
- Item B: only T2 touches B. No edges.

Edges: T1 → T3. **Acyclic → conflict serializable.** Equivalent serial orders: any order with T1 before T3: (T1, T2, T3), (T2, T1, T3), (T1, T3, T2).

### 7.5 Worked example 2 (not serializable)

S: R1(x) R1(y) R2(x) R2(y) W2(y) W1(x)

- x: R1(x), R2(x), W1(x). R2(x) before W1(x): **T2 → T1**.
- y: R1(y), R2(y), W2(y). R1(y) before W2(y): **T1 → T2**.

Cycle T1 → T2 → T1. **Not conflict serializable.**

### 7.6 Worked example 3

S: R2(x) W2(x) R2(y) W2(y) R1(y) W1(x)

- x: W2(x) before W1(x): T2 → T1. (R2(x) before W1(x): T2 → T1.)
- y: W2(y) before R1(y): T2 → T1.

All edges T2 → T1. **Serializable**, equivalent to T2, T1.

---

## 8. View serializability

### 8.1 View equivalence

Schedules S and S' (same transactions) are **view equivalent** if:

1. **Initial reads:** if Ti reads the **initial** value of X in S, it does so in S' too.
2. **Reads-from:** if Ti reads a value of X written by Tj in S, it reads the value written by Tj in S' too.
3. **Final writes:** the transaction that performs the **last write** of each X is the same in S and S'.

A schedule is **view serializable** if it's view equivalent to some serial schedule.

### 8.2 Relationship with conflict serializability

```
Serial  ⊂  Conflict serializable  ⊂  View serializable  ⊂  All schedules
```

- **Every conflict serializable schedule is view serializable.**
- **Not** every view serializable schedule is conflict serializable.
- The difference appears only with **blind writes** (a write by a transaction that never read that item).
- Testing view serializability is **NP-complete**; conflict serializability is polynomial (cycle detection).

### 8.3 Worked example (view serializable but not conflict serializable)

S: R1(A) W2(A) W1(A) W3(A)

**Conflict test:**
- R1(A) before W2(A): T1 → T2.
- W2(A) before W1(A): T2 → T1.
- Cycle. **Not conflict serializable.**

**View test** against serial T1, T2, T3:
- Initial read: T1 reads the initial A in S; in T1, T2, T3 it also reads the initial A ✓.
- Reads-from: no other reads.
- Final write of A: T3 in S; T3 in the serial schedule ✓.

**View serializable** (equivalent to T1, T2, T3). T2 and T3 perform **blind writes**: they write A without reading it, so the "conflict" between W2 and W1 is never observed by anyone.

### 8.4 Quick rule

If a schedule is **not conflict serializable** and has **no blind writes**, it is **not view serializable** either.

---

## 9. Exam traps

1. ACID mapping: A, D → recovery; I → concurrency control; C → programmer.
2. Partially committed ≠ committed.
3. Total schedules = (Σnᵢ)!/Π(nᵢ!); serial = k!.
4. Conflict needs different transactions, same item, at least one write.
5. Check **all** conflicting pairs, not just adjacent ones.
6. Acyclic precedence graph ⟺ conflict serializable.
7. Conflict serializable ⟹ view serializable (not the reverse).
8. View-only serializability requires blind writes.
9. Dirty read vs unrepeatable read vs phantom.
10. Isolation levels table.

---

## 10. Practice questions

**Q1.** T1 has 3 operations and T2 has 2. Number of non-serial schedules?
(a) 10 (b) 8 (c) 120 (d) 6

**Answer: (b).** 5!/(3! 2!) = 10; minus 2 serial = 8.

---

**Q2.** T1: R1(A) W1(A). T2: R2(A) W2(A). How many of the possible schedules are conflict serializable?
(a) 1 (b) 2 (c) 4 (d) 6

**Answer: (b).** Total = 4!/(2! 2!) = 6. Any interleaving puts one transaction's read before the other's write and vice versa (cycle), except the two serial schedules.

---

**Q3.** Is S: R1(A) R2(A) W1(A) W2(A) conflict serializable?
(a) Yes, as T1, T2 (b) Yes, as T2, T1 (c) No (d) Can't tell

**Answer: (c).** R1(A) before W2(A): T1 → T2. R2(A) before W1(A): T2 → T1. Cycle. (This is the lost-update pattern.)

---

**Q4.** S: R1(A) W1(A) R2(A) W2(A) R1(B) W1(B) R2(B) W2(B). Conflict serializable?
(a) Yes, T1 then T2 (b) Yes, T2 then T1 (c) No (d) Only view serializable

**Answer: (a).** On A: all T1 ops before T2's: T1 → T2. On B: same: T1 → T2. No cycle.

---

**Q5.** Which pair of operations does NOT conflict?
(a) R1(X), W2(X) (b) W1(X), W2(X) (c) R1(X), R2(X) (d) W1(X), R2(X)

**Answer: (c).**

---

**Q6.** Which ACID property is the responsibility of the concurrency control manager?
(a) Atomicity (b) Consistency (c) Isolation (d) Durability

**Answer: (c).**

---

**Q7.** A transaction reads the same row twice and sees different committed values. This anomaly is:
(a) dirty read (b) lost update (c) unrepeatable read (d) phantom read

**Answer: (c).**

---

**Q8.** Which isolation level prevents dirty reads but still allows unrepeatable reads?
(a) Read uncommitted (b) Read committed (c) Repeatable read (d) Serializable

**Answer: (b).**

---

**Q9.** S: R1(X) W2(X) W1(X) W3(X). Which is TRUE?
(a) Conflict serializable (b) View serializable but not conflict serializable (c) Neither (d) Serial

**Answer: (b).** See section 8.3 (same structure with A renamed X).

---

**Q10.** S: R1(A) R2(B) W2(A) W1(B). Precedence graph edges?
(a) T1 → T2 only (b) T2 → T1 only (c) both (cycle) (d) none

**Answer: (c).** A: R1(A) before W2(A): T1 → T2. B: R2(B) before W1(B): T2 → T1.

---

**Q11.** Three transactions with 1, 2 and 3 operations respectively. Total number of schedules?
(a) 60 (b) 720 (c) 6 (d) 120

**Answer: (a).** 6!/(1! 2! 3!) = 720/12 = 60.

---

**Q12.** If a schedule has a cycle in its precedence graph and **no blind writes**, it is:
(a) view serializable (b) not view serializable (c) recoverable (d) strict

**Answer: (b).**

---

**Q13.** S: R1(A) R3(A) W1(B) R2(B) W3(A) R2(A). Equivalent serial order?
(a) T1, T2, T3 (b) T1, T3, T2 (c) T3, T1, T2 (d) not serializable

**Answer: (b).**
- A: R1(A) before W3(A): T1 → T3. W3(A) before R2(A): T3 → T2. R3(A)/R1(A): no.
- B: W1(B) before R2(B): T1 → T2.
- Edges: T1 → T3, T3 → T2, T1 → T2. Topological order: **T1, T3, T2**.

---

**Q14.** A transaction has executed its final statement but its changes are still in the buffer. Its state is:
(a) active (b) partially committed (c) committed (d) failed

**Answer: (b).**

---

**Practice questions:** [6.12 Transactions ACID Serializability](../ISRO_CS_Question_Bank/06_DBMS/6.12_Transactions_ACID_Serializability.md)
