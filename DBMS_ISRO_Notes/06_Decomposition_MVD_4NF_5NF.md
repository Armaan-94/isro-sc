# 06. Decomposition, Multivalued Dependencies, 4NF and 5NF

> **The worry behind this chapter.** Normalization splits tables. But splitting is dangerous: if you split carelessly, joining the pieces back can produce **fake rows** that were never in the original (lossy), or you may lose the ability to check a rule without an expensive join (dependency loss). This chapter teaches you how to split **safely**, and then covers the two highest normal forms.

---

## 1. Two properties we want from a decomposition

When we replace R by R1, R2, ..., Rn:

1. **Lossless join** (also called non-additive join): R1 ⋈ R2 ⋈ ... ⋈ Rn = R **exactly**. This is **mandatory**. A lossy decomposition corrupts data.
2. **Dependency preservation**: every FD of R can be checked **within a single** Ri, without joining. **Desirable**, but sometimes sacrificed.

---

## 2. What "lossy" looks like

R(Student, Course, Room):

| Student | Course | Room |
|---|---|---|
| Riya | DBMS | 101 |
| Aman | OS | 101 |

Decompose into R1(Student, Room) and R2(Course, Room):

R1: (Riya, 101), (Aman, 101). R2: (DBMS, 101), (OS, 101).

Natural join on Room:

| Student | Course | Room |
|---|---|---|
| Riya | DBMS | 101 |
| Riya | OS | 101 | ← fake |
| Aman | DBMS | 101 | ← fake |
| Aman | OS | 101 |

We got **more** rows, not fewer. That's why it's called "lossy": we **lost information** about which combinations were real. **A lossy join always produces a superset** of the original (spurious tuples); it never loses actual rows.

---

## 3. The lossless-join test for two relations

R decomposed into R1 and R2 is lossless if and only if:

1. **R1 ∪ R2 = R** (all attributes covered),
2. **R1 ∩ R2 ≠ ∅** (they share something), and
3. **(R1 ∩ R2) → R1** or **(R1 ∩ R2) → R2** holds in F⁺.

In words: **the common attributes must be a super key of at least one of the two pieces.**

Why? If the common attribute determines all of R1, then each value of the common attribute matches exactly one R1 row, so the join can't create mismatched combinations.

In the Student/Course/Room example, the common attribute Room doesn't determine Student or Course, so it's lossy.

### Worked example 1

R(A, B, C), F = {A → B, B → C, C → A}. Decompose into R1(A, B), R2(B, C).
- Common = {B}. B⁺ = B → C → A = ABC, so B → AB (all of R1). ✓ **Lossless.**

### Worked example 2

R(A, B, C, D), F = {A → B, C → D}. Decompose into R1(A, B), R2(C, D).
- Common = ∅. ✗ **Lossy** (the join is a Cartesian product).

### Worked example 3

R(A, B, C, D), F = {A → B, B → C, C → D}. Decompose into R1(A, B, C), R2(C, D).
- Common = {C}. C → D, so C determines all of R2 ✓. **Lossless.**

### Worked example 4

R(A, B, C, D, E), F = {A → B, B → C, C → D, D → E}. Decompose into R1(A, B), R2(B, C), R3(C, D), R4(D, E)... for **more than 2 pieces**, apply the binary test step by step:
- R3 ⋈ R4 on D: D → E, so D is a key of R4 ✓ → gives (C, D, E).
- R2 ⋈ (C, D, E) on C: C → DE ✓ → gives (B, C, D, E).
- R1 ⋈ (B, C, D, E) on B: B → CDE ✓ → gives R.
- **Lossless.**

(For general multi-way decompositions there's the **chase / tableau** test; the step-by-step method works when you can find a good joining order.)

---

## 4. Dependency preservation

Let Fᵢ = the FDs from F⁺ that involve **only** attributes of Rᵢ (the **projection** of F onto Rᵢ).

The decomposition is **dependency preserving** if

```
(F1 ∪ F2 ∪ ... ∪ Fn)⁺ = F⁺
```

### Practical check

For each FD X → Y in F:
- If X ∪ Y fits entirely inside some Rᵢ, it's obviously preserved.
- Otherwise, check whether it can be **derived** from the projected FDs. A quick test: compute X⁺ using only the projected FDs (closure restricted piece by piece). If Y ⊆ that, it's preserved.

### Worked example 1 (preserved, with a subtle derivation)

R(A, B, C), F = {A → B, B → C, C → A}, decomposed into R1(A, B), R2(B, C).
- F1 (on A, B): A → B, and also **B → A** (since B → C → A in F⁺ and both B, A are in R1).
- F2 (on B, C): B → C, and **C → B** (since C → A → B).
- Is C → A preserved? From F1 ∪ F2: C → B, B → A, so C → A. ✓
- **Lossless and dependency preserving.**

Lesson: projected FDs include **implied** FDs, not just the original ones written in F.

### Worked example 2 (not preserved)

R(A, B, C), F = {AB → C, C → A} (the classic 3NF-not-BCNF relation). BCNF decomposition: R1(C, A), R2(B, C).
- Lossless? Common C, and C → A, so C is a key of R1 ✓.
- AB → C: A and B are never together in one piece. Projected FDs: F1 = {C → A}, F2 = {} (only trivial). Can't derive AB → C. ✗
- **Lossless but NOT dependency preserving.**

Consequence: to check "AB → C" whenever data changes, the DBMS must **join** R1 and R2. Expensive.

---

## 5. BCNF vs 3NF decomposition guarantees

| | Lossless | Dependency preserving |
|---|---|---|
| **BCNF decomposition** | **Always** | **Not always** |
| **3NF synthesis** | **Always** | **Always** |

> **Very frequently tested.** "BCNF decomposition is always lossless and dependency preserving" is **FALSE**.

### BCNF decomposition algorithm

```
result = {R}
while some Ri in result is not in BCNF:
    find a non-trivial FD X → Y on Ri that violates BCNF (X not a super key of Ri)
    replace Ri with  (X ∪ Y)  and  (Ri − (Y − X))
```

Each split is lossless (X is a key of X ∪ Y).

### 3NF synthesis algorithm

```
1. Find a minimal cover Fc of F.
2. For each FD X → A in Fc, create a relation (X, A)
   (group FDs with the same left side into one relation (X, A1, A2, ...)).
3. If no relation contains a candidate key of R, add one relation that is just a candidate key.
4. Remove any relation that is a subset of another.
```

**Example:** R(A, B, C, D), F = {A → B, B → C}. Key: AD.
- Fc = {A → B, B → C}.
- Relations: (A, B), (B, C).
- No relation contains AD → add (A, D).
- Result: (A, B), (B, C), (A, D). Lossless and dependency preserving.

---

## 6. Multivalued dependencies (MVD)

### 6.1 The problem FDs can't see

A student can join several **clubs** and has several **phone numbers**, completely independently.

| Student | Club | Phone |
|---|---|---|
| Riya | Robotics | 111 |
| Riya | Robotics | 222 |
| Riya | Music | 111 |
| Riya | Music | 222 |

There is **no non-trivial FD** here (Student doesn't determine a single club or a single phone). So the table is **vacuously in BCNF**.

Yet look at the redundancy: every club is paired with every phone. Add a third phone, and you must add **two** rows (one per club). Forget one, and the data claims Riya's new phone exists only for Music. That's an anomaly BCNF doesn't catch.

### 6.2 Definition

**X →→ Y** (X **multidetermines** Y) holds in R if, for each X value, the **set** of Y values is **independent** of the values of the remaining attributes Z = R − X − Y.

Here: **Student →→ Club** and **Student →→ Phone**.

Formally: if two tuples t1, t2 agree on X, then there exist tuples t3, t4 that agree with them on X and "swap" their Y values (t3 has t1's Y and t2's Z, t4 has t2's Y and t1's Z).

### 6.3 Facts about MVDs

- **Trivial MVD:** X →→ Y is trivial if **Y ⊆ X** or **X ∪ Y = R**.
- **Every FD is an MVD:** if X → Y, then X →→ Y. (The reverse is false.)
- **Complementation:** if X →→ Y, then X →→ (R − X − Y). MVDs come in pairs. Student →→ Club implies Student →→ Phone.

### 6.4 Fourth Normal Form (4NF)

R is in **4NF** if for every **non-trivial MVD X →→ Y**, **X is a super key**.

4NF ⊂ BCNF (every 4NF relation is in BCNF).

Fix for the example: decompose into **(Student, Club)** and **(Student, Phone)**. Now each MVD is trivial (X ∪ Y = the whole relation).

> **Trap.** **BCNF does not imply 4NF.** A relation with zero FDs is in BCNF but can still violate 4NF.

---

## 7. Join dependencies and 5NF

### 7.1 Join dependency (JD)

A **join dependency** ⋈{R1, R2, ..., Rn} holds on R if R is always equal to the join of its projections onto R1, ..., Rn. (An MVD is a JD with exactly two components.)

### 7.2 The classic case: Supplier-Part-Project

SPJ(Supplier, Part, Project). Business rule: **if** supplier S supplies part P, **and** S supplies project J, **and** project J uses part P, **then** S supplies P to J.

This constraint involves all three pairs at once. You **cannot** decompose losslessly into any **two** projections, but you **can** into **three**:

- SP(Supplier, Part)
- SJ(Supplier, Project)
- PJ(Part, Project)

SP ⋈ SJ ⋈ PJ = SPJ exactly (under that rule).

### 7.3 Fifth Normal Form (5NF), also called Project-Join Normal Form (PJNF)

R is in **5NF** if every non-trivial join dependency is **implied by the candidate keys**. Practically: you can't decompose it further without loss except along keys.

5NF situations are rare in practice and in exams usually appear as recall ("Which normal form deals with join dependencies?" → 5NF).

---

## 8. Summary of all normal forms

| NF | Removes | Condition |
|---|---|---|
| 1NF | Non-atomic values | Atomic cells |
| 2NF | Partial dependencies | No non-prime attribute depends on part of a key |
| 3NF | Transitive dependencies | X → Y: X super key or Y prime |
| BCNF | All FD anomalies | X → Y: X super key |
| 4NF | Multivalued dependency anomalies | X →→ Y non-trivial: X super key |
| 5NF | Join dependency anomalies | Every JD implied by candidate keys |

---

## 9. Exam traps

1. Lossy joins produce **extra** (spurious) tuples, never fewer.
2. Lossless test: common attributes must be a **super key of one side**.
3. Projected FDs include **implied** FDs.
4. BCNF: lossless always, dependency preservation **not guaranteed**. 3NF: **both** guaranteed.
5. Every FD is an MVD; not every MVD is an FD.
6. MVDs come in complementary pairs.
7. BCNF does **not** imply 4NF.
8. 5NF = PJNF = join dependencies.

---

## 10. Practice questions

**Q1.** R(A, B, C, D), F = {A → B, B → C}. Decomposition R1(A, B), R2(B, C, D). Lossless?
(a) Yes (b) No

**Answer: (b).** Common B. B⁺ = BC. Does B → all of R1 (A, B)? No. Does B → all of R2 (B, C, D)? No (D missing). Lossy.

---

**Q2.** R(A, B, C, D), F = {A → B, A → C, C → D}. Decomposition R1(A, B, C), R2(C, D). Lossless and dependency preserving?
(a) Both (b) Lossless only (c) DP only (d) Neither

**Answer: (a).** Common C, C → D (key of R2): lossless. A → B, A → C in R1; C → D in R2: all preserved.

---

**Q3.** R(A, B, C, D), F = {AB → C, C → D, D → A}. Decomposition R1(A, B, C), R2(C, D). Is AB → C preserved? Is D → A preserved?
(a) Both preserved (b) Only AB → C (c) Only D → A (d) Neither

**Answer: (b) Only AB → C.**
- AB → C fits entirely in R1 ✓.
- D → A: D and A are never together in one relation. Can it be derived from the projections? F1 on (A, B, C) contains AB → C and the implied **C → A** (since C → D → A). F2 on (C, D) contains C → D, but not D → C (D⁺ = DA, no C). Using only F1 ∪ F2, D⁺ = {D}. So D → A is **lost**.

(The decomposition is still lossless: common attribute C determines D, so C is a key of R2. Lossless and dependency-preserving are independent properties.)

---

**Q4.** "A BCNF decomposition is always lossless and dependency preserving." This is:
(a) True (b) False

**Answer: (b).**

---

**Q5.** Which normal form deals with multivalued dependencies?
(a) 3NF (b) BCNF (c) 4NF (d) 5NF

**Answer: (c).**

---

**Q6.** In R(A, B, C), if A →→ B holds, which must also hold?
(a) A → B (b) A →→ C (c) B →→ C (d) C → A

**Answer: (b).** Complementation rule.

---

**Q7.** Which statement is FALSE?
(a) Every FD is an MVD (b) Every MVD is an FD (c) A trivial MVD has Y ⊆ X or X ∪ Y = R (d) 4NF implies BCNF

**Answer: (b).**

---

**Q8.** A lossy decomposition, when joined back, produces:
(a) fewer tuples than the original (b) exactly the original (c) more tuples (spurious) than the original (d) an empty relation

**Answer: (c).**

---

**Q9.** R(A, B, C, D, E), F = {A → B, C → D, AC → E}. Key? Decomposition into (A, B), (C, D), (A, C, E). Lossless?
(a) Key AC; lossless (b) Key ACE; lossy (c) Key AC; lossy (d) Key A; lossless

**Answer: (a).** (AC)⁺ = ABCDE. Join (A, C, E) with (A, B) on A: A → B, A is key of (A, B) ✓ → (A, B, C, E). Join with (C, D) on C: C → D ✓ → R. Lossless.

---

**Q10.** Which normal form is also called Project-Join Normal Form?
(a) 3NF (b) BCNF (c) 4NF (d) 5NF

**Answer: (d).**

---

**Q11.** Which decomposition approach guarantees both lossless join and dependency preservation?
(a) BCNF decomposition (b) 3NF synthesis (c) 4NF decomposition (d) Any arbitrary split

**Answer: (b).**

---

**Q12.** R(Course, Teacher, Book): a course has several teachers and several recommended books, independent of each other. The relation is:
(a) not in BCNF (b) in BCNF but not 4NF (c) in 4NF (d) not in 1NF

**Answer: (b).** No non-trivial FDs (so BCNF), but Course →→ Teacher and Course →→ Book are non-trivial MVDs with Course not a super key.

---

**Practice questions:** [6.06 Decomposition MVD 4NF 5NF](../ISRO_CS_Question_Bank/06_DBMS/6.06_Decomposition_MVD_4NF_5NF.md)
