# 13. Recoverability and Concurrency Control Protocols

> **Two separate worries.** Serializability (Chapter 12) is about getting the **right answer** when everything commits. **Recoverability** is about what happens when something **aborts**. Then **concurrency control protocols** (locking, timestamps) are the practical machinery that produce good schedules automatically.

---

## Part A: Recoverability

### 1. Why serializability isn't enough

| T1 | T2 |
|---|---|
| W1(A) | |
| | R2(A) |
| | **commit** |
| **abort** | |

T2 read A from T1 and **committed**. Then T1 aborted, so the value T2 read never existed. But T2 is already committed and **durable**; we can't undo it. The database is now inconsistent and **unrecoverable**.

### 2. The classes of schedules

#### Recoverable

If Tj reads a value written by Ti, then **Ti must commit before Tj commits**.

(Readers commit only after the writers they read from.)

#### Cascadeless (avoids cascading rollback)

If Tj reads a value written by Ti, then **Ti must commit before Tj reads it**. In other words: **no dirty reads at all**.

Why care? In a merely recoverable schedule, one abort can force a **chain** of rollbacks:

| T1 | T2 | T3 |
|---|---|---|
| W1(A) | | |
| | R2(A), W2(B) | |
| | | R3(B) |
| abort | | |

T1 aborts → T2 read dirty data → T2 must roll back → T3 read T2's dirty data → T3 must roll back. This is a **cascading rollback**: wasteful.

#### Strict

If Tj **reads or writes** an item written by Ti, then **Ti must commit (or abort) first**.

Strict schedules make recovery trivial: to undo a write, just restore the **before-image**, since nobody else touched that item in between.

#### Containment

```
Strict  ⊂  Cascadeless  ⊂  Recoverable  ⊂  All schedules
```

(And serial ⊂ strict.) Note: serializability and recoverability are **independent** dimensions. A schedule can be serializable but not recoverable, and vice versa.

### 3. A fast classification procedure

1. Look for **dirty reads** (a read of an item whose last writer hasn't committed yet).
2. **No dirty reads** → **cascadeless** (hence recoverable).
3. **Dirty reads present** → **not cascadeless**. Then for each dirty read: does the writer commit before the reader commits?
   - All yes → **recoverable**.
   - Any no → **not recoverable**.
4. For **strict**: also check **dirty writes** (a write to an item whose last writer hasn't committed). No dirty reads **and** no dirty writes → strict.

### 4. Worked examples

**A)** W1(X) R2(X) C1 C2
- Dirty read: R2(X) reads from uncommitted T1 → not cascadeless.
- T1 commits before T2 → **recoverable, not cascadeless**.

**B)** W1(X) R2(X) C2 C1
- Dirty read, and the reader T2 commits **before** the writer T1 → **not recoverable**.

**C)** W1(X) W2(X) C1 C2
- No reads → no dirty reads → **cascadeless** (and recoverable).
- W2(X) overwrites uncommitted X → dirty write → **not strict**.

**D)** W1(X) C1 W2(X) R2(X) C2
- T2 touches X only after T1 committed → **strict** (hence cascadeless and recoverable).

> **Trap.** Cascadeless only forbids **reading** uncommitted data. Writing over it is allowed in cascadeless schedules; strictness forbids that too.

---

## Part B: Concurrency control protocols

The goal: let transactions run concurrently but **guarantee** that only serializable (ideally also recoverable/cascadeless) schedules occur.

Main families:
1. **Lock-based** (two-phase locking and its variants).
2. **Timestamp-based** (timestamp ordering, Thomas's write rule).
3. **Validation-based (optimistic)**.

---

### 5. Locks

| Lock | Allows | Others can also hold |
|---|---|---|
| **Shared (S)** | Read | Other S locks |
| **Exclusive (X)** | Read and write | **Nothing** |

**Compatibility matrix:**

| Held \ Requested | S | X |
|---|---|---|
| **S** | ✓ | ✗ |
| **X** | ✗ | ✗ |

A transaction that requests an incompatible lock **waits**.

**Locking alone doesn't guarantee serializability.** If T1 locks A, reads it, **unlocks** it, then locks B, another transaction can sneak in between and produce a non-serializable interleaving. We need a **discipline**.

---

### 6. Two-Phase Locking (2PL)

Each transaction has two phases:

1. **Growing phase:** may **acquire** locks, may **not** release any.
2. **Shrinking phase:** may **release** locks, may **not** acquire any.

```
#locks held
   ^
   |        lock point
   |          /\
   |         /  \
   |        /    \
   |       /      \
   +---------------------> time
     growing   shrinking
```

The moment of the **last lock acquisition** is the **lock point**. The equivalent serial order is the order of lock points.

**Example: 2PL or not?**

```
T1: X(A) R(A) W(A) X(B) U(A) R(B) W(B) U(B)     -> 2PL ✓ (all locks before the first unlock)
T2: X(A) R(A) W(A) U(A) X(B) R(B) W(B) U(B)     -> NOT 2PL ✗ (locks B after unlocking A)
```

**What 2PL guarantees:**
- ✓ **Conflict serializability** (and so view serializability).

**What basic 2PL does NOT guarantee:**
- ✗ Freedom from **deadlock**.
- ✗ **Recoverability** / cascadelessness (a transaction may release an X lock before committing, letting others read dirty data).

Also: 2PL ⟹ conflict serializable, but a conflict serializable schedule need **not** be producible under 2PL.

### 7. 2PL variants

| Variant | Rule | Effect |
|---|---|---|
| **Basic 2PL** | Growing then shrinking | Conflict serializable; deadlocks and cascading rollbacks possible |
| **Conservative (static) 2PL** | Acquire **all** locks **before** starting; if any isn't available, take none and wait | **Deadlock-free** (no hold-and-wait). Needs the read/write set in advance. Cascading rollback still possible |
| **Strict 2PL** | Hold all **X** locks until **commit/abort** | **Strict** schedules (so recoverable and cascadeless); deadlocks possible. **Most widely used** |
| **Rigorous 2PL** | Hold **all** locks (S and X) until commit/abort | Strict; serial order = commit order; deadlocks possible |

### Summary table (memorise)

| Protocol | Conflict serializable | View serializable | Recoverable | Cascadeless | Deadlock-free |
|---|---|---|---|---|---|
| Basic 2PL | ✓ | ✓ | ✗ | ✗ | ✗ |
| Conservative 2PL | ✓ | ✓ | ✗ | ✗ | **✓** |
| Strict 2PL | ✓ | ✓ | ✓ | ✓ | ✗ |
| Rigorous 2PL | ✓ | ✓ | ✓ | ✓ | ✗ |
| Timestamp ordering | ✓ | ✓ | ✗ | ✗ | **✓** |
| Thomas's write rule | **✗** | ✓ | ✗ | ✗ | ✓ |

---

### 8. Deadlocks under locking: wait-die and wound-wait

Locking can deadlock (T1 holds A, wants B; T2 holds B, wants A). Two timestamp-based **prevention** schemes (smaller timestamp = **older**):

**Wait-Die (non-preemptive):**
- **Older** requests a lock held by younger → older **waits**.
- **Younger** requests a lock held by older → younger **dies** (rolls back, restarts later **with its original timestamp**).

**Wound-Wait (preemptive):**
- **Older** requests a lock held by younger → older **wounds** the younger (forces it to roll back).
- **Younger** requests a lock held by older → younger **waits**.

Memory trick: the first word says what the **older** transaction does. Wait-die: older waits. Wound-wait: older wounds.

Both avoid starvation because a restarted transaction keeps its original timestamp, so it eventually becomes the oldest.

Other approaches: **timeouts**; **wait-for graph** detection and choosing a victim.

---

### 9. Multiple-granularity locking (brief)

Lock at different levels: database → table → page → row. Use **intention locks** on ancestors:
- **IS** (intention shared): will S-lock something below.
- **IX** (intention exclusive): will X-lock something below.
- **SIX**: S on this node + IX below.

Compatibility: IS is compatible with everything except X; IX is compatible with IS and IX; S with IS and S; SIX only with IS; X with nothing.

---

### 10. Timestamp Ordering (TO) protocol

Each transaction Ti gets a unique timestamp **TS(Ti)** when it starts (older = smaller). The protocol forces the schedule to be equivalent to the **serial order of timestamps**.

Each data item Q keeps:
- **W-TS(Q):** largest timestamp of any transaction that successfully wrote Q.
- **R-TS(Q):** largest timestamp of any transaction that successfully read Q.

**Read(Q) by Ti:**
- If **TS(Ti) < W-TS(Q)**: Ti wants a value that has already been overwritten by a younger transaction → **reject, roll back Ti**.
- Else: allow; R-TS(Q) = max(R-TS(Q), TS(Ti)).

**Write(Q) by Ti:**
- If **TS(Ti) < R-TS(Q)**: a younger transaction already read the old value; Ti's write comes too late → **reject, roll back**.
- If **TS(Ti) < W-TS(Q)**: a younger transaction already wrote Q; Ti's write is obsolete → **reject, roll back**.
- Else: allow; W-TS(Q) = TS(Ti).

Rolled-back transactions restart with a **new** (larger) timestamp.

Properties: **conflict serializable**, **deadlock-free** (nobody waits), but **not** necessarily recoverable or cascadeless, and possible **starvation** (a long transaction may keep getting restarted).

### 11. Thomas's Write Rule

Modify **only** the second write check: if TS(Ti) < W-TS(Q) (obsolete write), **ignore the write** instead of rolling back. Nobody will ever read that obsolete value, so skipping it is harmless.

- The read rule and the first write rule (TS(Ti) < R-TS(Q)) are **unchanged**.
- Allows some schedules that are **view serializable but not conflict serializable** (blind writes).

### 12. Worked TO example

TS(T1) = 10, TS(T2) = 20. Initially all R-TS and W-TS = 0.

| Step | Operation | Check | Result |
|---|---|---|---|
| 1 | R1(A) | 10 ≥ W-TS(A)=0 | OK, R-TS(A) = 10 |
| 2 | W2(A) | 20 ≥ R-TS(A)=10 and ≥ W-TS(A)=0 | OK, W-TS(A) = 20 |
| 3 | W1(A) | 10 ≥ R-TS(A)=10 ✓, but 10 < W-TS(A)=20 | **Basic TO: roll back T1. Thomas: ignore the write.** |
| 4 | R1(B)... | | (under basic TO T1 has restarted) |

Another: if step 3 were **R1(A)**: 10 < W-TS(A) = 20 → **rolled back under both** basic TO and Thomas (the read rule is the same).

---

### 13. Validation-based (optimistic) protocol (brief)

Assumes conflicts are **rare**. Each transaction has three phases:
1. **Read phase:** read values and compute on private copies.
2. **Validation phase:** check whether its reads/writes conflict with transactions that committed meanwhile.
3. **Write phase:** if validation passes, apply writes; otherwise roll back.

Good for read-mostly workloads; no locks, no deadlocks.

### 14. Multiversion schemes (brief)

Keep **several versions** of each item, each with a timestamp. Readers get the appropriate old version and **never wait or fail**. Basis of **snapshot isolation** in PostgreSQL, Oracle, MySQL InnoDB.

---

## 15. Exam traps

1. Strict ⊂ Cascadeless ⊂ Recoverable.
2. Cascadeless = no dirty **reads**; strict also forbids dirty **writes**.
3. Recoverable: writer commits before reader **commits**.
4. 2PL ⟹ conflict serializable, but not deadlock-free or recoverable.
5. **Conservative 2PL** is the deadlock-free 2PL variant.
6. **Strict 2PL**: hold X locks until commit; most common in practice.
7. TO: deadlock-free, conflict serializable, not recoverable.
8. Thomas's rule changes **only** the obsolete-write case; it gives view-serializable but not conflict-serializable schedules.
9. Wait-die: older waits, younger dies. Wound-wait: older wounds, younger waits.

---

## 16. Practice questions

**Q1.** W1(A) R2(A) C2 C1. This schedule is:
(a) strict (b) cascadeless (c) recoverable but not cascadeless (d) not recoverable

**Answer: (d).** T2 read from T1 and committed first.

---

**Q2.** W1(A) R2(A) C1 C2 is:
(a) strict (b) cascadeless (c) recoverable but not cascadeless (d) not recoverable

**Answer: (c).**

---

**Q3.** R1(A) W2(A) C2 W1(A) C1. Is it strict?
(a) Yes (b) No

**Answer: (a).** W1(A) happens after T2 committed. No dirty reads or writes.

---

**Q4.** Which 2PL variant guarantees freedom from deadlock?
(a) Basic (b) Strict (c) Rigorous (d) Conservative

**Answer: (d).**

---

**Q5.** Which protocol is used by most commercial DBMSs to obtain strict, cascadeless schedules?
(a) Basic 2PL (b) Strict 2PL (c) Timestamp ordering (d) Thomas's write rule

**Answer: (b).**

---

**Q6.** Under wait-die, T5 (TS = 5) requests an item held by T9 (TS = 9). What happens?
(a) T5 waits (b) T5 dies (c) T9 is wounded (d) T9 waits

**Answer: (a).** T5 is older; older waits.

---

**Q7.** Under wound-wait, T5 (TS = 5) requests an item held by T9 (TS = 9). What happens?
(a) T5 waits (b) T5 is rolled back (c) T9 is rolled back (wounded) (d) both wait

**Answer: (c).**

---

**Q8.** Under wound-wait, T9 requests an item held by T5. What happens?
(a) T9 waits (b) T9 is rolled back (c) T5 is rolled back (d) deadlock

**Answer: (a).** Younger waits.

---

**Q9.** Under basic timestamp ordering, TS(T1) = 5, W-TS(Q) = 8, R-TS(Q) = 3. T1 issues Read(Q). Result?
(a) allowed (b) T1 rolled back (c) ignored (d) T1 waits

**Answer: (b).** 5 < W-TS(Q) = 8.

---

**Q10.** TS(T1) = 5, W-TS(Q) = 8, R-TS(Q) = 3. T1 issues Write(Q). Under Thomas's write rule?
(a) allowed, W-TS = 5 (b) rolled back (c) ignored (d) waits

**Answer: (c).** 5 ≥ R-TS (3) so the first check passes; 5 < W-TS (8), so the write is obsolete and ignored.

---

**Q11.** TS(T1) = 5, W-TS(Q) = 2, R-TS(Q) = 7. T1 issues Write(Q). Under Thomas's write rule?
(a) allowed (b) rolled back (c) ignored (d) waits

**Answer: (b).** 5 < R-TS (7): a younger transaction has already read Q, so T1's write is too late. Thomas's rule doesn't relax this check.

---

**Q12.** Which statement about basic 2PL is FALSE?
(a) It ensures conflict serializability (b) It can lead to deadlock (c) It ensures cascadeless schedules (d) A transaction can't acquire a lock after releasing one

**Answer: (c).**

---

**Q13.** Which protocol can produce a schedule that is view serializable but not conflict serializable?
(a) Strict 2PL (b) Basic TO (c) Thomas's write rule (d) Conservative 2PL

**Answer: (c).**

---

**Q14.** T: lock-S(A), read(A), lock-X(B), unlock(A), write(B), unlock(B). Does T follow 2PL?
(a) Yes (b) No

**Answer: (a).** Both locks are acquired before the first unlock.

---

**Q15.** Which statement is TRUE about timestamp ordering?
(a) It can deadlock (b) It ensures recoverability (c) It is deadlock-free but may cause starvation (d) It doesn't ensure serializability

**Answer: (c).**
