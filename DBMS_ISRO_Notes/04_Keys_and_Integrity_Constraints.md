# 04. Keys and Integrity Constraints

> **Keys are how a database tells rows apart and links tables together.** In exams, you'll be asked to (1) identify the type of a key, (2) find **all candidate keys** from FDs, (3) count super keys, and (4) trace foreign-key cascades. This chapter teaches all four.

---

## 1. The family of keys

Think of identifying a student in a college:

- `{roll_no}` identifies a student. ✓
- `{roll_no, name}` also identifies a student (roll_no alone already does; name is extra baggage). ✓ but not minimal.
- `{email}` identifies a student. ✓
- `{name}` doesn't (two students can share a name). ✗

| Key type | Definition | In our example |
|---|---|---|
| **Super key** | Any set of attributes that **uniquely identifies** every tuple (its closure = all attributes) | {roll_no}, {email}, {roll_no, name}, {email, dept}, ... |
| **Candidate key** | A **minimal** super key: remove any attribute and it stops being a super key | {roll_no}, {email} |
| **Primary key (PK)** | **One** candidate key **chosen** by the designer. Cannot be NULL. One per table | {roll_no} |
| **Alternate key** | Candidate keys **not chosen** as PK | {email} |
| **Composite key** | A key with **more than one** attribute | {course_id, roll_no} in an enrolment table |
| **Foreign key (FK)** | Attribute(s) in one table referring to a candidate key (usually PK) of another (or the same) table | dept_id in Student referring to Department |
| **Secondary key** | An attribute used for searching/indexing; **need not be unique** | dept, city |
| **Surrogate key** | An artificial key with no business meaning (e.g. auto-increment ID) | student_internal_id |

Relationships:

```
Candidate keys ⊆ Super keys
Primary key ∈ Candidate keys
Alternate keys = Candidate keys − {Primary key}
```

> **Trap.** Every candidate key is a super key, but **not** every super key is a candidate key. The primary key is always a candidate key.

### Prime and non-prime attributes

- **Prime attribute:** belongs to **at least one** candidate key (not just the primary key!).
- **Non-prime attribute:** belongs to **no** candidate key.

These terms drive 2NF and 3NF (Chapter 05).

---

## 2. Finding ALL candidate keys from FDs (a fail-safe method)

This is the single most useful skill for normalization questions.

### Step 0. Classify every attribute by where it appears in F

| Appears in | Category | Consequence |
|---|---|---|
| **Only on the left** (or nowhere at all) | "Must" attributes | **In every** candidate key (nothing can derive them) |
| **Only on the right** | "Never" attributes | **In no** candidate key (something else always derives them) |
| **Both sides** | "Maybe" attributes | Try them |

### Step 1. Compute the closure of the "must" set

If it covers everything, it's the **only** candidate key. Done.

### Step 2. Otherwise, add "maybe" attributes one at a time, then two at a time...

Each combination whose closure covers everything **and** doesn't contain a smaller key already found is a candidate key.

### Worked example 1

R(A, B, C, D, E), F = {A → B, BC → E, ED → A}.

| Attribute | Left? | Right? | Category |
|---|---|---|---|
| A | yes | yes | maybe |
| B | yes | yes | maybe |
| C | yes | no | **must** |
| D | yes | no | **must** |
| E | yes | yes | maybe |

- (CD)⁺ = CD. Not all.
- Add one "maybe":
  - (ACD)⁺ = A → B: ABCD; BC → E: ABCDE ✓ **key**
  - (BCD)⁺ = BC → E: BCDE; ED → A: ABCDE ✓ **key**
  - (CDE)⁺ = ED → A: ACDE; A → B: ABCDE ✓ **key**
- Any two-"maybe" combination contains one of these, so it's not minimal.

**Candidate keys: ACD, BCD, CDE.** Prime attributes: A, B, C, D, E (all). Non-prime: none.

### Worked example 2

R(W, X, Y, Z), F = {X → W, WZ → XY, Y → WXZ}.

| Attr | Left | Right | Category |
|---|---|---|---|
| W | yes | yes | maybe |
| X | yes | yes | maybe |
| Y | yes | yes | maybe |
| Z | yes | yes | maybe |

No "must" attributes. Try singles:
- Y⁺ = WXYZ ✓ **key**
- X⁺ = XW. W⁺ = W. Z⁺ = Z.

Try pairs not containing Y:
- (WZ)⁺ = WZ → XY: WXYZ ✓ **key**
- (XZ)⁺ = X → W: WXZ; WZ → XY: WXYZ ✓ **key**
- (WX)⁺ = WX ✗

**Candidate keys: Y, WZ, XZ.**

### Worked example 3

R(A, B, C, D, E, F, G, H), F = {CH → G, A → BC, B → CFH, E → A, F → EG}.

- D appears **nowhere** in F, so it's in every key ("must").
- G appears only on right sides (CH → G, F → EG), so it's in no key ("never").
- A, B, C, E, F, H appear on both sides ("maybe").
- (D)⁺ = D. Add one "maybe" attribute:
  - (AD)⁺: A → BC, B → CFH, F → EG → ABCDEFGH ✓
  - (BD)⁺: B → CFH, F → EG, E → A, A → BC → all ✓
  - (ED)⁺: E → A → ... → all ✓
  - (FD)⁺: F → EG, E → A, A → BC, B → CFH → all ✓
  - (CD)⁺ = CD. (HD)⁺ = HD. (CHD)⁺ = CDGH, not all.

**Candidate keys: AD, BD, DE, DF.**

---

## 3. Counting super keys (formula questions)

A relation with **n** attributes has 2ⁿ − 1 non-empty attribute subsets. Which of them are super keys?

**Rule:** a set is a super key iff it **contains at least one candidate key**.

### One candidate key K with k attributes

Every super key = K plus any subset of the other n − k attributes:

```
#super keys = 2^(n − k)
```

Example: R(A, B, C, D), candidate key A. Super keys = 2³ = **8**.

### Two candidate keys: use inclusion-exclusion

```
|S(K1) ∪ S(K2)| = 2^(n − |K1|) + 2^(n − |K2|) − 2^(n − |K1 ∪ K2|)
```

**Example:** R(A, B, C, D), candidate keys A and B.
= 2³ + 2³ − 2² = 8 + 8 − 4 = **12**.

**Example:** R(A, B, C, D), candidate keys AB and CD.
= 2² + 2² − 2⁰ = 4 + 4 − 1 = **7**.

**Example:** R(A, B, C, D), candidate keys AB and BC.
K1 ∪ K2 = ABC (3 attributes). = 2² + 2² − 2¹ = 4 + 4 − 2 = **6**.

### Three candidate keys

```
|S1 ∪ S2 ∪ S3| = |S1| + |S2| + |S3| − |S1∩S2| − |S1∩S3| − |S2∩S3| + |S1∩S2∩S3|
```
where |Si ∩ Sj| = 2^(n − |Ki ∪ Kj|).

**Example:** R(A, B, C, D), candidate keys A, B, C.
= 8 + 8 + 8 − 4 − 4 − 4 + 2 = **14**.

### Maximum possible

If **every** single attribute is a candidate key, all 2ⁿ − 1 non-empty subsets are super keys. Maximum number of **candidate keys** for n attributes is C(n, floor(n/2)) (all subsets of size n/2), e.g. n = 4 gives 6.

---

## 4. Integrity constraints

Rules the DBMS enforces so the data stays valid.

| Constraint | Rule |
|---|---|
| **Domain constraint** | Each value must come from the attribute's domain (type, range, `CHECK`) |
| **Key constraint** | Key values must be **unique** |
| **Entity integrity** | **Primary key** attributes can **never be NULL** |
| **Referential integrity** | Every non-NULL **foreign key** value must **match** an existing key value in the referenced table |
| **NOT NULL, UNIQUE, CHECK, DEFAULT** | SQL column-level constraints |

> **Trap.** **Entity** integrity is about the **PK not being NULL**. **Referential** integrity is about **FKs pointing to existing rows**. Different rules.

Note: a foreign key **may be NULL** (unless declared NOT NULL). NULL means "no reference".

### What can violate referential integrity?

Suppose `Employee.dept_id` references `Department.dept_id`.

| Operation | On which table | Can it violate RI? |
|---|---|---|
| INSERT | Employee (child) | **Yes** (dept_id that doesn't exist) |
| INSERT | Department (parent) | No |
| DELETE | Employee (child) | No |
| DELETE | Department (parent) | **Yes** (employees left pointing to nothing) |
| UPDATE dept_id | Employee (child) | **Yes** |
| UPDATE dept_id | Department (parent) | **Yes** |

### Referential actions (what happens when a parent row is deleted/updated)

| Action | Effect on child rows |
|---|---|
| **CASCADE** | Delete/update the child rows too |
| **SET NULL** | Set the child's FK to NULL |
| **SET DEFAULT** | Set the child's FK to its default value |
| **RESTRICT / NO ACTION** | **Reject** the parent delete/update while children exist |

```sql
CREATE TABLE Employee (
    emp_id   INT PRIMARY KEY,
    dept_id  INT,
    FOREIGN KEY (dept_id) REFERENCES Department(dept_id)
        ON DELETE CASCADE
        ON UPDATE SET NULL
);
```

---

## 5. Cascade tracing (a classic numerical)

Table T(A, B). A is the primary key. B is a foreign key referencing A **of the same table**, with **ON DELETE CASCADE**.

Rows: (1, 2), (2, 3), (3, 5), (5, 9), (6, 2).

(Think of B as "my manager is A = B".)

Delete the row with **A = 3**:

1. Delete (3, 5).
2. Any row whose **B = 3** references the deleted row → delete (2, 3).
3. Now A = 2 is gone. Rows with **B = 2**: (1, 2) and (6, 2) → delete both.
4. Now A = 1 and A = 6 are gone. Rows with B = 1 or B = 6: none. Stop.

Deleted: (3, 5), (2, 3), (1, 2), (6, 2) = **4 rows**. Remaining: **(5, 9)**.

> **Method:** keep a queue of deleted A-values; for each, find rows whose B equals it; delete those and enqueue their A-values. Continue until the queue is empty.

Note that (5, 9) survives even though 3 pointed to 5: cascade flows from parent to **children** (rows referencing the deleted key), not to the row the deleted row referenced.

---

## 6. Exam traps

1. Candidate key = **minimal** super key.
2. Prime attribute = part of **any** candidate key.
3. Attributes appearing **nowhere** or **only on the left** are in **every** candidate key.
4. Attributes only on the **right** are in **no** candidate key.
5. #super keys with one candidate key of size k = 2^(n − k).
6. Entity integrity: PK not NULL. Referential integrity: FK must match.
7. FKs can be NULL.
8. Deleting a **parent** or inserting a **child** can violate referential integrity.
9. Cascades propagate recursively through self-references.

---

## 7. Practice questions

**Q1.** R(A, B, C, D, E), F = {AB → C, C → D, D → E}. Candidate key(s)?
(a) A (b) AB (c) ABC (d) C

**Answer: (b).** A, B appear only on the left: must be in every key. (AB)⁺ = ABCDE.

---

**Q2.** R(A, B, C, D), F = {A → B, B → C, C → D, D → A}. Number of candidate keys?
(a) 1 (b) 2 (c) 4 (d) 8

**Answer: (c).** Each single attribute determines all others (it's a cycle). Candidate keys: A, B, C, D.

---

**Q3.** For R(A, B, C, D) with candidate keys A, B, C, D (as in Q2), number of super keys?
(a) 4 (b) 8 (c) 15 (d) 16

**Answer: (c).** Every non-empty subset: 2⁴ − 1 = 15.

---

**Q4.** R(A, B, C, D, E) with only one candidate key AB. Number of super keys?
(a) 4 (b) 8 (c) 16 (d) 32

**Answer: (b).** 2^(5 − 2) = 8.

---

**Q5.** R(A, B, C, D) with candidate keys AB and CD. Number of super keys?
(a) 6 (b) 7 (c) 8 (d) 9

**Answer: (b).** 4 + 4 − 1 = 7.

---

**Q6.** R(A, B, C, D, E, F), F = {A → B, C → D, E → F}. Candidate key?
(a) ACE (b) ABCDEF (c) AC (d) BDF

**Answer: (a).** A, C, E appear only on the left. (ACE)⁺ = ABCDEF.

---

**Q7.** Which constraint ensures the primary key is never NULL?
(a) Referential integrity (b) Entity integrity (c) Domain constraint (d) Key constraint

**Answer: (b).**

---

**Q8.** `Orders.cust_id` references `Customers.cust_id` with ON DELETE SET NULL. Deleting a customer:
(a) is rejected if orders exist (b) deletes their orders (c) keeps their orders with cust_id = NULL (d) has no effect on orders, leaving dangling references

**Answer: (c).**

---

**Q9.** Which operation can never violate referential integrity (for the FK Employee.dept_id → Department.dept_id)?
(a) Insert into Employee (b) Delete from Department (c) Delete from Employee (d) Update Department.dept_id

**Answer: (c).** Removing a child row can't leave anything dangling.

---

**Q10.** Table T(A, B), A primary key, B references A with ON DELETE CASCADE. Rows: (1, NULL), (2, 1), (3, 1), (4, 2), (5, 4), (6, 3). Delete A = 2. How many rows remain?
(a) 3 (b) 4 (c) 2 (d) 5

**Answer: (a).** Delete (2, 1). Rows with B = 2: (4, 2) deleted. Rows with B = 4: (5, 4) deleted. Rows with B = 5: none. Remaining: (1, NULL), (3, 1), (6, 3) = 3 rows.

---

**Q11.** R(A, B, C, D, E), F = {A → BC, CD → E, B → D, E → A}. Which is NOT a candidate key?
(a) A (b) E (c) CD (d) B

**Answer: (d).**
- A⁺: A → BC, B → D, CD → E: ABCDE ✓
- E⁺: E → A → ... all ✓
- (CD)⁺: CD → E, E → A, A → BC: all ✓
- (BC)⁺: B → D, CD → E, E → A: all ✓ (so BC is also a key)
- B⁺: B → D: BD only ✗

So B alone is not a candidate key.

---

**Q12.** An attribute that belongs to an alternate key but not the primary key is:
(a) non-prime (b) prime (c) a foreign key (d) a secondary key

**Answer: (b).** Prime means "in **some** candidate key".
