# 05. Normalization: 1NF, 2NF, 3NF and BCNF

> **What normalization really is.** It's a disciplined way of asking: "Is this table secretly about more than one thing?" If yes, split it. Functional dependencies tell us exactly where the hidden "second thing" is. Each normal form catches one more kind of hidden mixing.

---

## 1. The ladder

```
1NF  ⊃  2NF  ⊃  3NF  ⊃  BCNF  ⊃  4NF  ⊃  5NF
(weakest)                            (strictest)
```

Every relation in a higher normal form is automatically in all the lower ones. A **BCNF** relation is in 3NF, 2NF and 1NF.

FDs take us up to **BCNF**. 4NF and 5NF need multivalued and join dependencies ([Chapter 06](06_Decomposition_MVD_4NF_5NF.md)).

### Vocabulary refresher

- **Prime attribute:** part of **some** candidate key.
- **Non-prime attribute:** part of **no** candidate key.
- **Partial dependency:** a **non-prime** attribute depends on a **proper subset** of a candidate key. (Only possible when a candidate key is **composite**.)
- **Full (total) dependency:** depends on the whole key, not on any part.
- **Transitive dependency:** key → X → Y, where X is not a super key and Y is non-prime (Y depends on the key **through** some other non-key attribute).

---

## 2. First Normal Form (1NF)

**Rule:** every attribute value is **atomic** (single, indivisible). No repeating groups, no multivalued cells, no nested tables.

Not in 1NF:

| roll | name | phones |
|---|---|---|
| 1 | Riya | 98xxx, 97xxx |

In 1NF (one value per cell):

| roll | name | phone |
|---|---|---|
| 1 | Riya | 98xxx |
| 1 | Riya | 97xxx |

(Or better, move phones to a separate table.)

By the strict relational definition, **every relation is already in 1NF**. Most exam questions assume 1NF and ask about higher forms.

---

## 3. Second Normal Form (2NF)

**Rule:** 1NF **and no partial dependency**: no non-prime attribute depends on a **part** of a candidate key.

### Example

`Enrolment(roll, course_id, student_name, grade)`, key = (roll, course_id).

FDs: roll, course_id → grade; **roll → student_name**.

`student_name` (non-prime) depends on **roll alone**, which is part of the key. **Partial dependency → not 2NF.**

Symptom: the student's name repeats in every course row (update anomaly).

Fix: split into `Student(roll, student_name)` and `Enrolment(roll, course_id, grade)`.

### Shortcut

**If every candidate key is a single attribute, the relation is automatically in 2NF** (there's no "part" of a single-attribute key).

---

## 4. Third Normal Form (3NF)

**Rule (intuitive):** 2NF **and no transitive dependency** of a non-prime attribute on a key.

**Rule (the exam-friendly test):** for **every non-trivial FD X → Y**:

```
X is a super key     OR     Y is a prime attribute     (each attribute of Y prime)
```

### Example

`Employee(emp_id, dept_id, dept_name)`, key = emp_id.

FDs: emp_id → dept_id, **dept_id → dept_name**.

dept_id → dept_name: dept_id is not a super key, dept_name is not prime. **Violates 3NF.** (emp_id → dept_id → dept_name is transitive.)

Fix: `Employee(emp_id, dept_id)` and `Department(dept_id, dept_name)`.

---

## 5. Boyce-Codd Normal Form (BCNF)

**Rule:** for **every non-trivial FD X → Y**, **X must be a super key.** No exceptions.

BCNF removes 3NF's escape clause "or Y is prime".

### The classic "3NF but not BCNF" example

R(Student, Course, Teacher). Rules: each teacher teaches only one course (Teacher → Course); for each course, a student has exactly one teacher (Student, Course → Teacher).

In abstract letters this is the textbook relation **R(A, B, C), F = {AB → C, C → A}** (A = Course, B = Student, C = Teacher).

**Candidate keys:**
- (AB)⁺ = ABC ✓
- (BC)⁺ = C → A gives ABC ✓
- A⁺ = A, B⁺ = B, C⁺ = AC. None alone.

Keys: **AB** and **BC**. Prime attributes: **A, B, C** (all).

- AB → C: AB is a super key ✓.
- C → A: C is **not** a super key. Is A prime? **Yes.** → **3NF satisfied**, **BCNF violated**.

Result: **3NF but not BCNF.**

Why does this still have redundancy? In the student-course-teacher version, the fact "Teacher T teaches Course C" is repeated for every student taking T's class.

---

## 6. The checking procedure (use this every time)

1. **Find all candidate keys** ([Chapter 04](04_Keys_and_Integrity_Constraints.md) method).
2. Mark **prime** and **non-prime** attributes.
3. For each FD X → Y (split Y into single attributes, ignore trivial ones):
   - **BCNF check:** is X a super key? If every FD passes, it's **BCNF**. Stop.
   - **3NF check:** for failing FDs, is Y prime? If every failing FD has prime Y, it's **3NF**.
   - **2NF check:** is there an FD where X is a **proper subset of a candidate key** and Y is **non-prime**? If yes, **not 2NF** (only 1NF).
4. Report the **highest** form that holds.

> Careful in step 3: check FDs in a **minimal/complete enough** set. Sometimes a violation is hidden in an implied FD. For MCQs, the given FDs are usually enough.

---

## 7. Shortcuts for fast elimination

| Situation | Conclusion |
|---|---|
| Relation has only **2 attributes** | Always **BCNF** |
| **No non-trivial FDs** | **BCNF** |
| **All candidate keys are single attributes** | At least **2NF** |
| **All attributes are prime** | At least **3NF** |
| In **3NF** and all candidate keys are single attributes | **BCNF** |
| Every FD's left side is a super key | **BCNF** |

---

## 8. Worked examples (do these slowly)

### Example 1: 1NF only (GATE pattern)

R(A, B, C, D, E, F, G, H), F = {CH → G, A → BC, B → CFH, E → A, F → EG}.

From [Chapter 04](04_Keys_and_Integrity_Constraints.md): candidate keys **AD, BD, DE, DF**.
Prime: A, B, D, E, F. Non-prime: C, G, H.

- A → BC: A is a proper subset of key AD; C is non-prime. **Partial dependency.**

Result: **1NF only** (not 2NF).

### Example 2: 2NF but not 3NF

R(A, B, C, D), F = {A → BC, C → D}.
- A⁺ = ABCD. Key: **A** (single attribute) → 2NF automatically.
- C → D: C not a super key (C⁺ = CD), D non-prime. **Violates 3NF.**

Result: **2NF**.

### Example 3: 3NF but not BCNF

R(A, B, C), F = {AB → C, C → A}. (Section 5.)

Result: **3NF**.

### Example 4: BCNF

R(A, B, C), F = {A → B, B → C, C → A}.
- A⁺ = B⁺ = C⁺ = ABC. Keys: A, B, C. Every left side is a key.

Result: **BCNF**.

### Example 5

R(A, B, C, D, E), F = {A → B, BC → E, ED → A}.
- Keys: ACD, BCD, CDE ([Chapter 04](04_Keys_and_Integrity_Constraints.md)). All attributes prime.
- A → B: A not a super key, B prime ✓ for 3NF.
- BC → E: not a super key, E prime ✓.
- ED → A: not a super key, A prime ✓.

Result: **3NF, not BCNF** (all FDs fail BCNF).

### Example 6

R(A, B, C, D), F = {AB → C, B → D}.
- A, B appear only on the left. (AB)⁺ = ABCD. Key: **AB**.
- B → D: B ⊂ AB, D non-prime. **Partial.**

Result: **1NF**.

### Example 7

R(A, B, C, D), F = {AB → CD, D → A}.
- B appears only on the left (must). B⁺ = B.
- (AB)⁺ = ABCD ✓. (BD)⁺: D → A, AB → CD: ABCD ✓. (BC)⁺ = BC ✗.
- Keys: **AB, BD**. Prime: A, B, D. Non-prime: C.
- AB → CD: super key ✓.
- D → A: D not a super key; A prime ✓ (3NF).

Result: **3NF, not BCNF**.

---

## 9. Decomposing into BCNF / 3NF (preview)

When a relation violates a normal form, we **decompose** it:

**BCNF decomposition step:** pick a violating FD X → Y. Split R into **R1 = X ∪ Y** and **R2 = R − (Y − X)**. Repeat on each piece.

Example: R(A, B, C), F = {AB → C, C → A}. Violator: C → A.
- R1 = (C, A), R2 = (B, C).
- Lossless? Common attribute C, and C → A, so C is a key of R1 ✓.
- But **AB → C is lost** (A and B are in different tables). Not dependency preserving.

This is why BCNF decomposition is **always lossless but not always dependency preserving**, while 3NF synthesis guarantees **both**. [Chapter 06](06_Decomposition_MVD_4NF_5NF.md) covers this fully.

---

## 10. Why not always go to BCNF?

- BCNF may break dependency preservation, so some constraints can only be checked with a **join** (expensive).
- 3NF always allows a lossless **and** dependency-preserving decomposition.
- In practice, designers aim for **3NF or BCNF**, and sometimes **denormalise** deliberately for read performance (data warehouses).

---

## 11. Exam traps

1. 2NF violations need a **composite** candidate key.
2. 3NF test: **X super key OR Y prime**. BCNF: **X super key**, no exception.
3. Prime means part of **any** candidate key, so find **all** keys first.
4. All attributes prime ⟹ at least 3NF.
5. Two-attribute relation ⟹ BCNF.
6. 3NF + only simple candidate keys ⟹ BCNF.
7. BCNF decomposition: lossless, maybe not dependency preserving.

---

## 12. Practice questions

**Q1.** R(A, B, C, D), F = {A → B, B → C, C → D}. Highest normal form?
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (b).** Key A (single) → 2NF. B → C: B not a key, C non-prime → not 3NF.

---

**Q2.** R(A, B, C, D), F = {AB → C, C → D}. Highest normal form?
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (b).** Key AB. No non-prime attribute depends on A or B alone (C depends on AB fully; D depends on C, which isn't part of the key). So 2NF holds. C → D is transitive → not 3NF.

---

**Q3.** R(A, B, C, D), F = {AB → C, B → D}. Highest normal form?
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (a).** B → D is partial.

---

**Q4.** R(A, B, C), F = {A → B, B → A, A → C}. Highest normal form?
(a) 2NF (b) 3NF (c) BCNF (d) 1NF

**Answer: (c).** A⁺ = ABC, B⁺ = ABC. Keys A and B. Every left side (A or B) is a key.

---

**Q5.** A relation in which every attribute is prime is guaranteed to be in at least:
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (c).**

---

**Q6.** R(A, B, C, D, E), F = {AB → CDE, D → A}. Highest normal form?
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (c).**
- B on left only: must. Keys: AB ((AB)⁺ = ABCDE) and BD ((BD)⁺: D → A, then AB → CDE: all).
- Prime: A, B, D. Non-prime: C, E.
- AB → CDE: super key ✓.
- D → A: D not a super key, A prime → 3NF ✓, BCNF ✗.

---

**Q7.** Which statement is TRUE?
(a) Every 3NF relation is in BCNF (b) Every BCNF relation is in 3NF (c) 2NF implies 3NF (d) 1NF implies BCNF

**Answer: (b).**

---

**Q8.** R(A, B, C, D), F = {A → C, B → D}. Highest normal form?
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**Answer: (a).** Key AB. A → C and B → D are both partial dependencies.

---

**Q9.** R(A, B, C, D), F = {ABC → D, D → A}. Highest normal form?
(a) 2NF (b) 3NF (c) BCNF (d) 1NF

**Answer: (b).** Keys: ABC and BCD (D → A, then ABC → D). Prime: A, B, C, D (all). D → A: D not a super key, A prime → 3NF, not BCNF.

---

**Q10.** A table R(X, Y) with FD X → Y is in:
(a) 1NF only (b) 2NF only (c) 3NF only (d) BCNF

**Answer: (d).** Two attributes: always BCNF.

---

**Q11.** In 3NF, an FD X → Y with X not a super key is allowed only if:
(a) Y is non-prime (b) Y is prime (c) X is prime (d) never

**Answer: (b).**

---

**Q12.** R(A, B, C, D, E), F = {A → B, B → C, C → D, D → E}. Decomposing into BCNF requires how many relations (using the natural chain decomposition)?
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (c).** (A, B), (B, C), (C, D), (D, E). Each has its left side as the key.

---

**Practice questions:** [6.05 Normalization 1NF to BCNF](../ISRO_CS_Question_Bank/06_DBMS/6.05_Normalization_1NF_to_BCNF.md)
