# 14. Database Recovery

> **The promise a DBMS makes.** "Once I say COMMIT succeeded, your data will survive a power cut. And if a transaction dies halfway, it will leave no trace." Keeping that promise (Atomicity and Durability) is the job of the **recovery manager**, and its main tool is the **log**.

---

## 1. Kinds of failure

| Failure | Example | What's lost |
|---|---|---|
| **Transaction failure** | Logical error (divide by zero, constraint violation), or the system aborts it (deadlock victim) | Only that transaction's work |
| **System crash** | Power failure, OS crash, DBMS bug | **Contents of main memory** (buffers); disk is fine |
| **Disk failure** | Head crash, disk dies | Data on that disk |

Recovery from transaction failures and system crashes uses the **log**. Disk failures need **backups/archives** plus the log (or RAID/replication).

---

## 2. Storage types

- **Volatile storage:** RAM, cache. Lost in a crash.
- **Non-volatile storage:** disk, SSD, tape. Survives a crash, but can fail.
- **Stable storage:** an **ideal** that never loses data. Approximated by keeping **multiple copies on separate non-volatile devices** (even at different sites). The **log** is kept on stable storage.

---

## 3. Buffer policies: steal/no-steal, force/no-force

The DBMS modifies pages in a **memory buffer**. When are they written to disk?

- **Steal:** a page modified by an **uncommitted** transaction **may** be written to disk (the buffer manager can "steal" the frame).
- **No-steal:** uncommitted changes never reach disk.
- **Force:** at commit, **all** pages the transaction modified are written to disk immediately.
- **No-force:** they may be written later.

| Policy | Need UNDO? | Need REDO? |
|---|---|---|
| No-steal + Force | No | No (but terrible performance) |
| No-steal + No-force | No | **Yes** |
| Steal + Force | **Yes** | No |
| **Steal + No-force** | **Yes** | **Yes** |

**Steal + No-force** is what real systems use (best performance), which is why real recovery needs **both undo and redo**:
- Steal → uncommitted data may be on disk → **undo**.
- No-force → committed data may not be on disk → **redo**.

---

## 4. Log-based recovery

### 4.1 The log

A sequential file on stable storage recording every change. Record types:

```
<Ti start>
<Ti, X, V_old, V_new>      update: item X changed from V_old to V_new
<Ti commit>
<Ti abort>
<checkpoint L>             L = list of active transactions
```

Each record gets a **Log Sequence Number (LSN)**, increasing.

- **undo(Ti):** restore every X written by Ti to **V_old**, going **backwards**.
- **redo(Ti):** set every X written by Ti to **V_new**, going **forwards**.
- Both must be **idempotent**: doing them twice gives the same result as doing them once (because a crash can happen during recovery itself).

### 4.2 Write-Ahead Logging (WAL): the golden rule

1. **Before** a modified data page is written to disk, the **log records** describing those changes must be on stable storage. (**Log first, data second.**) This guarantees we can always **undo**.
2. **Before** a transaction is declared **committed**, **all** its log records (including `<Ti commit>`) must be on stable storage. This guarantees we can always **redo**.

> **Trap.** WAL = **log before data**. If data reached disk first and we crashed before logging it, we'd have a change with no record of the old value, and no way to undo it.

A transaction is considered **committed** at the moment its `<Ti commit>` record reaches stable storage, even if its data pages haven't.

---

## 5. Deferred vs immediate update

### 5.1 Deferred update (NO-UNDO / REDO)

- The database on disk is **not touched** until the transaction **commits**. Until then, changes live only in the log (and private workspace).
- The log only needs **new values**: `<Ti, X, V_new>`.
- After a crash:
  - Ti has `<start>` and `<commit>` in the log → **redo** Ti.
  - Ti has `<start>` but no `<commit>` → **ignore** it (nothing reached the database).
- **Never needs undo.**

### 5.2 Immediate update (UNDO / REDO)

- The database **may** be updated **before** commit (following WAL).
- The log needs **both** old and new values.
- After a crash:
  - `<start>` **and** `<commit>` present → **redo**.
  - `<start>` but **no** `<commit>` (and no `<abort>`) → **undo**.
- Order: undo the losers (backwards) and redo the winners (forwards). The classic textbook order is **undo first, then redo**; ARIES does **redo first, then undo** (section 8). Both work when done correctly.

### 5.3 Worked example

Initial values A = 1000, B = 2000, C = 700. Log at crash time:

```
<T0 start>
<T0, A, 1000, 950>
<T0, B, 2000, 2050>
<T0 commit>
<T1 start>
<T1, C, 700, 600>
--- crash ---
```

**Immediate update:** T0 committed → redo → A = 950, B = 2050. T1 did not commit → undo → C = 700.
Final: **A = 950, B = 2050, C = 700.**

**Deferred update:** T0 redo (A = 950, B = 2050). T1 ignored (C was never changed on disk; still 700). Same final values, but no undo work was needed.

If the crash happened right after `<T0, B, 2000, 2050>` (before T0's commit): immediate → undo T0 (A = 1000, B = 2000); deferred → nothing to do.

---

## 6. Checkpoints

Without checkpoints, recovery would have to scan the **entire log** since the beginning of time, and redo tons of already-safe work.

### Taking a checkpoint

1. Write all log records in memory to stable storage.
2. Write all modified buffer pages to disk.
3. Write `<checkpoint L>` to the log, where L = transactions active right now.

(During a simple checkpoint, updates are paused. Real systems use **fuzzy checkpoints** that don't stop activity.)

### Using it during recovery

Everything committed **before** the checkpoint is already on disk → **no redo needed** for it.

**Classic timeline example:** checkpoint at time tc, crash at tf.

```
        tc (checkpoint)                     tf (crash)
T1  |----commit|        
T2      |--------------|-----commit|
T3                         |---commit|
T4                            |-------------- (no commit)
T5     |--------------------------------------- (no commit)
```

| Transaction | Situation | Action |
|---|---|---|
| T1 | Committed **before** the checkpoint | **Nothing** |
| T2 | Started before, committed after checkpoint | **Redo** |
| T3 | Started and committed after checkpoint | **Redo** |
| T4 | Started after checkpoint, not committed | **Undo** |
| T5 | Started before checkpoint, not committed | **Undo** |

---

## 7. Shadow paging

An alternative to logging.

- Keep two page tables: the **shadow page table** (on disk, points to the last committed state) and the **current page table** (used by the running transaction).
- When the transaction first writes a page, copy it to a **new** page (copy-on-write) and point the current table to the copy. The shadow table still points to the old page.
- **Commit:** write all modified pages to disk, write the current page table to disk, then **atomically switch one pointer** so the current table becomes the shadow table.
- **Crash before commit:** the shadow table still describes a consistent state. **No undo, no redo.**

| Advantages | Disadvantages |
|---|---|
| No log needed (for single transactions) | **Data fragmentation** (related pages scattered) |
| Very fast, simple recovery | **Garbage collection** of old pages |
| | Copying the page table is costly |
| | **Hard to support concurrent transactions** |

So real systems use **log-based (WAL/ARIES)** recovery. Shadow paging ideas live on in copy-on-write file systems.

---

## 8. ARIES (the industrial-strength algorithm)

**ARIES** = Algorithms for Recovery and Isolation Exploiting Semantics (IBM). Used, in spirit, by DB2, SQL Server and others. Works with **steal + no-force** and **WAL**.

### Key data structures

- **LSN:** every log record's unique, increasing number.
- **PageLSN:** stored in each data page: the LSN of the latest update applied to that page. If a log record's LSN ≤ PageLSN, that update is already on the page; **don't redo it**.
- **Transaction table:** active transactions and their latest LSN.
- **Dirty page table:** pages modified in memory but not yet on disk, with **RecLSN** (the first LSN that dirtied the page).
- **CLR (Compensation Log Record):** written when an update is undone. CLRs are **redo-only**: they are never undone, so undo work is never repeated after a second crash.

### The three passes (always in this order)

1. **Analysis** (forward from the last checkpoint): rebuild the transaction table and dirty page table; find **RedoLSN** (the smallest RecLSN), where redo must start; identify **loser** transactions (active at the crash).
2. **Redo** (forward from RedoLSN): **repeat history**: re-apply **every** logged update (even those of losers), skipping ones already on the page (PageLSN check). After this, the database is exactly as it was at the moment of the crash.
3. **Undo** (backward from the end of the log): roll back all **losers**, writing CLRs.

> **Trap.** Order: **Analysis → Redo → Undo**. And ARIES redoes even the losers' updates ("repeating history"), then undoes them. That sounds wasteful but makes the algorithm simple and correct.

---

## 9. Exam traps

1. Steal → undo needed. No-force → redo needed. Real systems: steal + no-force → both.
2. WAL: **log before data**; all log records before commit.
3. Deferred update: no undo ever. Immediate update: undo + redo.
4. Redo: transactions with `<commit>`. Undo: transactions with `<start>` but no `<commit>`.
5. Committed before the checkpoint → ignore.
6. Shadow paging: no undo/redo, but fragmentation, garbage collection, poor concurrency.
7. ARIES: Analysis → Redo → Undo; PageLSN prevents double redo; CLRs are redo-only.
8. Undo/redo must be idempotent.

---

## 10. Practice questions

**Q1.** Which buffer policy pair requires both undo and redo during recovery?
(a) No-steal, force (b) No-steal, no-force (c) Steal, force (d) Steal, no-force

**Answer: (d).**

---

**Q2.** Under deferred update, a transaction with `<start>` but no `<commit>` at crash time requires:
(a) undo (b) redo (c) nothing (d) undo then redo

**Answer: (c).**

---

**Q3.** Under immediate update, the same transaction requires:
(a) undo (b) redo (c) nothing (d) redo only if checkpointed

**Answer: (a).**

---

**Q4.** Log:
```
<T1 start> <T1, X, 10, 20> <T2 start> <T2, Y, 5, 15> <T1 commit> <T2, X, 20, 30> --crash--
```
Immediate update. Final values of X and Y after recovery?
(a) X = 20, Y = 5 (b) X = 30, Y = 15 (c) X = 10, Y = 5 (d) X = 20, Y = 15

**Answer: (a).** Redo T1 (X = 20). Undo T2 backwards: X back to 20, Y back to 5.

---

**Q5.** WAL requires that:
(a) data pages be written before log records (b) log records reach stable storage before the corresponding data pages (c) logs be deleted after commit (d) checkpoints be taken after every transaction

**Answer: (b).**

---

**Q6.** A transaction committed before the most recent checkpoint. During recovery it needs:
(a) redo (b) undo (c) nothing (d) both

**Answer: (c).**

---

**Q7.** In ARIES, the redo pass starts from:
(a) the beginning of the log (b) the last checkpoint always (c) RedoLSN (smallest RecLSN in the dirty page table) (d) the end of the log

**Answer: (c).**

---

**Q8.** The purpose of a Compensation Log Record is to:
(a) record a redo action (b) record an undo action so it's never undone again (c) mark a checkpoint (d) record a commit

**Answer: (b).**

---

**Q9.** Which is NOT a disadvantage of shadow paging?
(a) Data fragmentation (b) Garbage collection (c) Difficulty with concurrent transactions (d) Need for an undo pass

**Answer: (d).** Shadow paging needs no undo.

---

**Q10.** In ARIES, if a log record's LSN is less than or equal to the PageLSN of its page, the redo pass:
(a) applies it (b) skips it (c) undoes it (d) aborts recovery

**Answer: (b).** The update is already reflected on the page.

---

**Q11.** Stable storage is implemented by:
(a) RAM with battery (b) a single SSD (c) multiple copies on independent non-volatile devices (d) cache memory

**Answer: (c).**

---

**Q12.** The correct ARIES pass order is:
(a) Redo, Analysis, Undo (b) Analysis, Undo, Redo (c) Analysis, Redo, Undo (d) Undo, Redo, Analysis

**Answer: (c).**

---

**Practice questions:** [6.14 Database Recovery](../ISRO_CS_Question_Bank/06_DBMS/6.14_Database_Recovery.md)
