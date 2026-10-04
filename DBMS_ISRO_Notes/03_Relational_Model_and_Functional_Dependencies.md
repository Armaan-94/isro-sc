# 03. The Relational Model and Functional Dependencies

> **Why this chapter is the foundation of normalization.** Normalization (Chapters 05 and 06) is entirely driven by **functional dependencies**. If you can compute an **attribute closure** quickly and correctly, you can solve almost every key-finding and normal-form question. So we'll practise that skill until it's automatic.

---

## 1. Relational model vocabulary

Proposed by **E. F. Codd (1970)**. Data is stored as **relations** (tables).

| Formal term | Informal term | Meaning |
|---|---|---|
| **Relation** | Table | A set of tuples |
| **Tuple** | Row, record | One entry |
| **Attribute** | Column, field | A named property |
| **Domain** | Data type / allowed values | Set of **atomic** values an attribute can take |
| **Degree (arity)** | Number of columns | How many attributes |
| **Cardinality** | Number of rows | How many tuples |
| **Relation schema** | Table definition | `Student(roll, name, dept)` |
| **Relation instance** | Table contents | The rows right now |

Memory hook: **D**egree goes **D**own the header (across columns); **C**ardinality **C**ounts rows.

### Properties of a relation

1. Each cell holds an **atomic** (indivisible) value.
2. All values in a column come from the **same domain**.
3. **No two tuples are identical** (it's a **set**).
4. **Order of rows doesn't matter.**
5. **Order of columns doesn't matter** (attributes are referred to by name).
6. Each attribute name in a relation is unique; each relation name in a schema is unique.

> **Trap.** Pure relational theory says no duplicate rows. **SQL tables, however, allow duplicates** (they're multisets/bags) unless you use a key or `DISTINCT`.

---

## 2. Why we need normalization: update anomalies

Look at this table:

| emp_id | emp_name | dept_id | dept_name | dept_head |
|---|---|---|---|---|
| 1 | Riya | D1 | Avionics | Dr. Rao |
| 2 | Aman | D1 | Avionics | Dr. Rao |
| 3 | Neha | D2 | Propulsion | Dr. Iyer |

It mixes two "ideas": employees and departments. Three problems follow:

| Anomaly | Problem | Here |
|---|---|---|
| **Insertion** | Can't record one fact without another | Can't add a new department D3 "Payload" until it has an employee (emp_id is the key, can't be NULL) |
| **Deletion** | Deleting one fact destroys another | Deleting Neha (the only D2 employee) erases all knowledge of the Propulsion department and its head |
| **Update (modification)** | Same fact stored many times; update one copy, others go stale | If Avionics gets a new head, we must update every Avionics row; miss one and the data is inconsistent |

The cure: split the table so each table is about **one thing**. To do that **systematically**, we need functional dependencies.

---

## 3. Functional dependency (FD)

### 3.1 Definition

**X → Y** (read "X functionally determines Y") means: **whenever two tuples agree on X, they must also agree on Y.**

Formally, for all tuples t₁, t₂: if t₁[X] = t₂[X] then t₁[Y] = t₂[Y].

- X is the **determinant**, Y the **dependent**.
- In plain words: **if you know X, you know Y.**

Examples in the table above:
- `emp_id → emp_name, dept_id` (an employee ID tells you the name and department)
- `dept_id → dept_name, dept_head`
- `dept_name → dept_id`? Only if department names are unique. That's a **business rule**, not something we read off the data.

### 3.2 FDs come from meaning, not from data

An FD is a rule about **all legal instances** of the schema. Looking at one instance:

- You can **disprove** an FD: find two rows with the same X but different Y.
- You can **never prove** an FD from one instance. The data might satisfy it by coincidence.

**Instance-checking exercise:**

| A | B | C |
|---|---|---|
| 1 | 4 | 2 |
| 3 | 5 | 6 |
| 3 | 4 | 6 |
| 7 | 3 | 8 |
| 1 | 4 | 5 |

- A → B? A=1 rows: B = 4, 4 ✓. A=3 rows: B = 5, 4 ✗. **Violated.**
- B → C? B=4 rows: C = 2, 6, 5 ✗. **Violated.**
- C → A? C values 2, 6, 6, 8, 5. C=6 rows: A = 3, 3 ✓. Others unique. **Not violated** (may hold).
- A → C? A=1: C = 2, 5 ✗. **Violated.**
- AB → C? (1,4) appears twice with C = 2 and 5 ✗. **Violated.**

### 3.3 Trivial and non-trivial FDs

- **Trivial:** Y ⊆ X. Always true. `AB → A`, `A → A`.
- **Non-trivial:** Y ⊄ X. `A → B`.
- **Completely non-trivial:** X ∩ Y = ∅.

### 3.4 Keys in terms of FDs

- **Super key:** X such that X → (all attributes of R).
- **Candidate key:** a **minimal** super key (no proper subset is a super key).

---

## 4. Armstrong's axioms

How do we figure out **all** FDs implied by a given set F? Use inference rules.

### The three basic axioms (memorise: **R-A-T**)

1. **Reflexivity:** if Y ⊆ X, then X → Y. (Trivial FDs.)
2. **Augmentation:** if X → Y, then XZ → YZ. (Add the same thing to both sides.)
3. **Transitivity:** if X → Y and Y → Z, then X → Z.

These are **sound** (they never produce a false FD) and **complete** (they produce **every** implied FD).

### Derived rules (provable from R-A-T)

4. **Union:** X → Y and X → Z ⟹ X → YZ.
5. **Decomposition:** X → YZ ⟹ X → Y and X → Z.
6. **Pseudo-transitivity:** X → Y and WY → Z ⟹ WX → Z.
7. **Composition:** X → Y and Z → W ⟹ XZ → YW.

> **Trap.** **Left sides cannot be split.** XY → Z does **not** imply X → Z. Only right sides decompose.

Example proof of union: X → Y; augment with X: X → XY. X → Z; augment with Y: XY → YZ. Transitivity: X → YZ. ✓

### Closure of an FD set, F⁺

F⁺ = the set of **all** FDs implied by F. It's huge (exponential), so in practice we never list it. Instead we use **attribute closure**.

---

## 5. Attribute closure X⁺ (the most important skill)

**X⁺** = the set of **all attributes** that X determines (directly or through chains).

### Algorithm

```
result = X
repeat
    for each FD  L → R  in F:
        if L ⊆ result:
            result = result ∪ R
until result stops changing
return result
```

### Worked example 1

R(A, B, C, D), F = {A → B, B → C, AB → D}. Find A⁺.

- Start: {A}
- A → B: {A, B}
- B → C: {A, B, C}
- AB → D (AB ⊆ result): {A, B, C, D}

**A⁺ = ABCD** = all attributes, so A is a super key (and a candidate key, since a single attribute can't be smaller).

### Worked example 2 (classic textbook)

R(A, B, C, D, E, F), F = {A → BC, E → CF, B → E, CD → EF}. Find (AB)⁺.

- Start: {A, B}
- A → BC: {A, B, C}
- B → E: {A, B, C, E}
- E → CF: {A, B, C, E, F}
- CD → EF: need D. Not present. Skip.
- Nothing more.

**(AB)⁺ = ABCEF.** D is missing, so AB is **not** a super key.

### Uses of attribute closure

1. **Is X a super key?** Check X⁺ = all attributes.
2. **Is X a candidate key?** X⁺ = all, and for every attribute a ∈ X, (X − a)⁺ ≠ all.
3. **Does F imply X → Y?** Compute X⁺ (using F). If Y ⊆ X⁺, yes.
4. **Are two FD sets equivalent?** (section 6)
5. **Is an attribute extraneous?** (section 7)

### Practice right now

R(A, B, C, D, E), F = {A → B, BC → E, ED → A}.
- (CD)⁺ = CD (no FD has its left side inside CD). Not a key.
- (ACD)⁺: A → B gives ABCD, BC → E gives ABCDE. **Key.**
- (BCD)⁺: BC → E gives BCDE, ED → A gives ABCDE. **Key.**
- (CDE)⁺: ED → A gives ACDE, A → B gives ABCDE. **Key.**

(We'll learn to find all candidate keys systematically in Chapter 04.)

---

## 6. Equivalence of FD sets

Two FD sets F and G are **equivalent** if **F⁺ = G⁺**, i.e. each **covers** the other:
- every FD in G can be derived from F, **and**
- every FD in F can be derived from G.

**How to check "F covers G":** for each X → Y in G, compute X⁺ **using F**; check Y ⊆ X⁺.

**Example:** F = {A → B, B → C}, G = {A → B, B → C, A → C}.
- F covers G? A → C: A⁺ under F = ABC ⊇ C ✓ (others are in F already).
- G covers F? Trivially, F ⊆ G.
- **Equivalent.**

**Example:** F = {A → B, B → C}, H = {A → B, A → C}.
- H covers F? B → C: B⁺ under H = {B}. C not in it ✗.
- **Not equivalent.**

---

## 7. Minimal (canonical) cover

A **minimal cover** Fc of F is an equivalent FD set with **no redundancy**:

1. Every right side is a **single attribute**.
2. No left side has an **extraneous** attribute.
3. No FD is **redundant** (derivable from the others).

### Procedure

**Step 1. Split right sides.** A → BC becomes A → B and A → C.

**Step 2. Remove extraneous attributes from left sides.** For an FD XA → B, check whether B ∈ X⁺ (computed with the **current full** set). If yes, A is extraneous: replace with X → B.

**Step 3. Remove redundant FDs.** For each FD X → A, remove it temporarily and compute X⁺ with the **remaining** FDs. If A is still in X⁺, the FD was redundant: delete it permanently.

(Do step 2 before step 3. Doing it the other way can leave redundancy.)

### Worked example 1

F = {A → BC, B → C, A → B, AB → C} on R(A, B, C).

**Step 1:** A → B, A → C, B → C, A → B (duplicate, drop), AB → C.
Set: {A → B, A → C, B → C, AB → C}.

**Step 2:** AB → C. Is B extraneous? A⁺ = {A, B, C} contains C, yes. So AB → C becomes A → C (already present, drop duplicate).
Set: {A → B, A → C, B → C}.

**Step 3:**
- Test A → C: without it, A⁺ = A → B → C = ABC. Contains C. **Redundant.** Remove.
- Test A → B: without it, A⁺ = {A}. Not redundant.
- Test B → C: without it, B⁺ = {B}. Not redundant.

**Fc = {A → B, B → C}.**

### Worked example 2

F = {AC → BD, A → C, B → C, D → C} on R(A, B, C, D).

**Step 1:** AC → B, AC → D, A → C, B → C, D → C.

**Step 2:** In AC → B, is C extraneous? A⁺ = A → C gives AC, then AC → B and AC → D give ABCD. B ∈ A⁺, so yes: AC → B becomes **A → B**. Same for AC → D, becomes **A → D**.
Set: {A → B, A → D, A → C, B → C, D → C}.

**Step 3:**
- A → C: without it, A⁺ = A → B, A → D, B → C = ABCD. Contains C. **Redundant.**
- A → B: without it, A⁺ = A → D → C = ACD. No B. Keep.
- A → D: without it, A⁺ = ABC. No D. Keep.
- B → C: without it, B⁺ = {B}. Keep.
- D → C: without it, D⁺ = {D}. Keep.

**Fc = {A → B, A → D, B → C, D → C}.**

> **Note.** A minimal cover is **not always unique**. Different elimination orders can give different (equally minimal) answers. In MCQs, check each option for equivalence and minimality.

---

## 8. Exam traps

1. Degree = columns; cardinality = rows.
2. Relations are sets: no duplicate rows, order doesn't matter.
3. An instance can only **disprove** an FD.
4. Basic axioms: **Reflexivity, Augmentation, Transitivity**.
5. Left sides of FDs can't be decomposed.
6. Attribute closure answers super key, candidate key, implication and equivalence questions.
7. Minimal cover: split RHS, remove extraneous LHS attributes, then remove redundant FDs.
8. Minimal cover isn't necessarily unique.

---

## 9. Practice questions

**Q1.** A relation has 6 columns and 40 rows. Its degree and cardinality are:
(a) 40, 6 (b) 6, 40 (c) 6, 6 (d) 40, 40

**Answer: (b).**

---

**Q2.** R(A, B, C, D, E) with F = {A → B, B → C, C → D, D → E}. Find (B)⁺.
(a) {B, C} (b) {B, C, D} (c) {B, C, D, E} (d) {A, B, C, D, E}

**Answer: (c).**

---

**Q3.** R(A, B, C, D, E, F) with F = {A → BC, E → CF, B → E, CD → EF}. Is AD a super key?
(a) Yes (b) No

**Answer: (a).** (AD)⁺: A → BC gives ABCD; B → E gives ABCDE; E → CF gives ABCDEF. All six.

---

**Q4.** Which is NOT one of Armstrong's basic axioms?
(a) Reflexivity (b) Augmentation (c) Union (d) Transitivity

**Answer: (c).** Union is derived.

---

**Q5.** Given AB → C, which of the following can be inferred?
(a) A → C (b) B → C (c) ABD → C (d) C → AB

**Answer: (c).** ABD → AB by reflexivity, and AB → C, so ABD → C by transitivity. Options (a) and (b) try to split the left side, which is not allowed.

---

**Q6.** For the instance below, which FD is definitely violated?

| P | Q | R |
|---|---|---|
| a | 1 | x |
| b | 1 | y |
| a | 2 | x |

(a) P → R (b) Q → R (c) R → P (d) PQ → R

**Answer: (b).** Q = 1 appears with R = x and R = y.

---

**Q7.** F = {A → B, AB → C}. Which is the minimal cover?
(a) {A → B, AB → C} (b) {A → B, A → C} (c) {A → C} (d) {AB → C}

**Answer: (b).** In AB → C, B is extraneous because A⁺ = ABC includes C.

---

**Q8.** Are F = {A → B, B → C, C → A} and G = {A → BC, B → A, C → A} equivalent?
(a) Yes (b) No

**Answer: (a).** Under F, every single attribute's closure is ABC, so all FDs of G follow. Under G: A⁺ = ABC, B⁺ = B → A → BC = ABC, C⁺ = C → A → ABC. So every FD of F follows too.

---

**Q9.** Pseudo-transitivity states that X → Y and WY → Z imply:
(a) WX → Z (b) XZ → W (c) Y → Z (d) X → Z

**Answer: (a).**

---

**Q10.** A deletion anomaly means:
(a) you can't delete any row (b) deleting a row unintentionally removes other information (c) deletion requires multiple updates (d) duplicates are deleted

**Answer: (b).**

---

**Q11.** R(A, B, C, D), F = {AB → C, C → D, D → A}. Which of the following is a candidate key?
(a) A (b) AB (c) BD (d) Both (b) and (c)

**Answer: (d).** (AB)⁺ = ABCD. (BD)⁺: D → A gives ABD, AB → C gives ABCD. Both minimal (A⁺ = A, B⁺ = B, D⁺ = AD).

---

**Q12.** Which FD is trivial?
(a) A → B (b) AB → B (c) A → AB (d) B → A

**Answer: (b).** The right side is a subset of the left. (Note (c) is **not** trivial: B is not in {A}.)
