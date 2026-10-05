# 08. Relational Algebra

> **What relational algebra is.** A small set of operators that take tables in and give a table out. SQL is translated internally into something very like relational algebra, which the optimizer then rearranges. Exams test (1) what each operator does, (2) sizes of results, and (3) reading/writing RA expressions, especially **division**.

---

## 1. Query languages: where RA fits

| | Relational Algebra (RA) | Relational Calculus (TRC/DRC) |
|---|---|---|
| Style | **Procedural**: a sequence of operations (what **and** how) | **Declarative**: describe the result (only what) |
| Basis | Set theory | Predicate logic |
| Role | Theoretical foundation; close to how queries are executed | Theoretical foundation; close to how SQL is written |

Both have the same expressive power (for "safe" expressions); SQL is **relationally complete** (can express anything RA can) and more (aggregates, sorting).

### Closure property

Every RA operator takes one or two relations as input and returns **one relation**. So operators can be **nested** freely: `Π_name(σ_dept='CS'(Student))`.

### Sets, not bags

Pure RA works on **sets**: **no duplicate tuples** ever. SQL works on **bags** (multisets) and keeps duplicates unless told otherwise. This matters for counting questions.

---

## 2. The fundamental operators

There are **six basic** operators: **σ, Π, ∪, −, ×, ρ**. Everything else (∩, joins, ÷) can be built from them.

### 2.1 Selection σ (filter rows)

`σ_condition(R)` keeps the tuples satisfying the condition.

```
σ_salary > 50000 ∧ dept = 'CS' (Employee)
```

- **Horizontal** slicing (rows).
- Result degree = degree of R. 0 ≤ |result| ≤ |R|.
- **Commutative:** σ_p(σ_q(R)) = σ_q(σ_p(R)) = σ_{p ∧ q}(R).
- Never creates duplicates (it only removes rows from a set).

### 2.2 Projection Π (choose columns)

`Π_A1, A2(R)` keeps only the listed attributes.

```
Π_name, dept (Employee)
```

- **Vertical** slicing (columns).
- **Removes duplicate rows** in the result (because the result is a set).
- 1 ≤ |result| ≤ |R| (if R is non-empty). If the projected attributes include a key, |result| = |R|.
- Π_L1(Π_L2(R)) = Π_L1(R) if L1 ⊆ L2.

> **Trap.** σ never removes duplicates (it has none to remove). **Π can shrink the row count** because it removes duplicates.

### 2.3 Union ∪

`R ∪ S`: tuples in R or S (or both), duplicates removed.

### 2.4 Set difference −

`R − S`: tuples in R but not in S. **Not commutative.**

### 2.5 Cartesian product ×

`R × S`: every tuple of R paired with every tuple of S.

- Degree = deg(R) + deg(S).
- Cardinality = |R| × |S|.
- No compatibility requirement.

### 2.6 Rename ρ

`ρ_X(E)` names the result X. `ρ_X(A1, ..., An)(E)` also renames attributes. Needed for **self-joins** (joining a table with itself) and for naming intermediate results. It doesn't change the stored table.

### Union compatibility

∪, −, and ∩ require R and S to be **union-compatible**:
1. **Same degree** (number of attributes), and
2. **Corresponding attributes have the same domains** (in order).

Attribute **names** don't have to match.

---

## 3. Derived operators

### 3.1 Intersection ∩

`R ∩ S = R − (R − S)`. Derived from difference. Requires union compatibility.

### 3.2 Size bounds (memorise)

Let |R| = m, |S| = n.

| Expression | Min | Max |
|---|---|---|
| R ∪ S | max(m, n) | m + n |
| R ∩ S | 0 | min(m, n) |
| R − S | 0 | m |
| R × S | m × n | m × n |
| σ(R) | 0 | m |
| Π(R) | 1 (if m ≥ 1) | m |

---

## 4. Joins

### 4.1 Theta join

`R ⋈_θ S = σ_θ(R × S)`. Any comparison (=, <, ≤, >, ≥, ≠). The result keeps **all** columns of both.

### 4.2 Equi-join

A theta join where θ uses **only equality**. Both copies of the compared columns remain.

### 4.3 Natural join ⋈

`R ⋈ S`: equi-join on **all attributes with the same name**, then **keep only one copy** of each common attribute.

Example: `Employee(eid, name, did)` ⋈ `Dept(did, dname)` gives `(eid, name, did, dname)`.

Special cases:
- **No common attributes:** natural join = **Cartesian product**.
- **All attributes common (same schema):** natural join = **intersection**.

**Size bounds** for R(A, B) ⋈ S(B, C) with |R| = m, |S| = n:
- 0 to m × n in general (all B values equal → m × n).
- If B is the **primary key of S** and a **non-null foreign key in R** (referential integrity holds): exactly **m**.
- If B is a key of S (but no FK constraint): at most m.

> **Trap.** Equi-join keeping both `did` columns is **not** a natural join.

### 4.4 Outer joins (keep the unmatched)

| Join | Keeps |
|---|---|
| **Left outer** ⟕ | All of R; S-side NULL where unmatched |
| **Right outer** ⟖ | All of S; R-side NULL where unmatched |
| **Full outer** ⟗ | All of both |

Example: Employee rows (1, Riya, D1), (2, Aman, D9); Dept rows (D1, Avionics), (D2, Propulsion).

- Natural join: (1, Riya, D1, Avionics).
- Left outer: + (2, Aman, D9, NULL).
- Right outer: (1, Riya, D1, Avionics) + (NULL, NULL, D2, Propulsion).
- Full outer: all three rows.

Size: |R ⟕ S| ≥ |R|. |R ⟗ S| ≥ max(|R|, |S|).

### 4.5 Semi-join and anti-join (bonus)

- **Semi-join** R ⋉ S: tuples of R that have **at least one** match in S (only R's columns).
- **Anti-join** R ▷ S: tuples of R with **no** match in S.

---

## 5. Division ÷ (the "for all" operator)

### 5.1 Meaning

R(Y, X) ÷ S(X) returns the Y-values in R that are paired with **every** X-value in S.

**Recognition cue:** "find students who took **all** courses", "suppliers who supply **every** part", "employees who work on **all** projects of department 5".

### 5.2 Example

Enrolled(Student, Course):

| Student | Course |
|---|---|
| s1 | c1 |
| s1 | c2 |
| s2 | c1 |
| s3 | c1 |
| s3 | c2 |
| s3 | c3 |

Required(Course): c1, c2.

Enrolled ÷ Required = students enrolled in **both** c1 and c2 = **{s1, s3}**.

s2 has only c1, so it's out. s3 has an extra c3, that's fine (division asks "at least all of S").

### 5.3 Division in terms of basic operators

```
R ÷ S = Π_Y(R) − Π_Y( (Π_Y(R) × S) − R )
```

Read it in steps:
1. `Π_Y(R)`: all candidate students {s1, s2, s3}.
2. `Π_Y(R) × S`: every student paired with every required course (the "ideal" if everyone took everything).
3. `− R`: the pairs that are **missing**: (s2, c2).
4. `Π_Y(...)`: students with something missing: {s2}.
5. Subtract from all candidates: {s1, s3}.

### 5.4 Schema of the result

If R has attributes Z and S has X ⊆ Z, the result has attributes **Y = Z − X**.

---

## 6. Writing RA queries (practice patterns)

Schema: `Student(sid, sname, dept)`, `Course(cid, cname, credits)`, `Enrolled(sid, cid, grade)`.

1. Names of CS students:
   `Π_sname(σ_dept='CS'(Student))`
2. Names of students enrolled in course 'C101':
   `Π_sname(Student ⋈ σ_cid='C101'(Enrolled))`
3. Students not enrolled in any course:
   `Π_sid(Student) − Π_sid(Enrolled)`
4. Students enrolled in C101 or C102:
   `Π_sid(σ_cid='C101' ∨ cid='C102'(Enrolled))`
5. Students enrolled in **both** C101 and C102:
   `Π_sid(σ_cid='C101'(Enrolled)) ∩ Π_sid(σ_cid='C102'(Enrolled))`
   (Not `σ_cid='C101' ∧ cid='C102'`, which is always empty: one tuple can't have two cids.)
6. Students enrolled in **all** courses:
   `Π_sid,cid(Enrolled) ÷ Π_cid(Course)`
7. Highest credits (no aggregate operator in basic RA!):
   `Π_credits(Course) − Π_C1.credits(σ_C1.credits < C2.credits(ρ_C1(Course) × ρ_C2(Course)))`
   Idea: remove every value that is smaller than some other value; what's left is the maximum.

---

## 7. Extended operators (brief)

- **Aggregation / grouping** 𝒢: `dept 𝒢 avg(salary)(Employee)`.
- **Generalised projection**: arithmetic in the projection list `Π_name, salary*1.1(Employee)`.
- **Assignment** ←: temp ← expression.

---

## 8. Equivalence rules used by the optimizer (useful facts)

- **Push selections down:** σ_p(R ⋈ S) = σ_p(R) ⋈ S if p uses only R's attributes. Filtering early shrinks intermediate results.
- **Push projections down** (keep needed attributes only).
- Joins are **commutative** and **associative** (natural join), so the optimizer can reorder them.
- Cascade of selections: σ_{p∧q}(R) = σ_p(σ_q(R)).

---

## 9. Exam traps

1. Six basic operators: **σ, Π, ∪, −, ×, ρ**. ∩, ⋈, ÷ are derived.
2. Π removes duplicates (set semantics). σ doesn't need to.
3. ∪, ∩, − need union compatibility; × and ⋈ don't.
4. R × S: degree adds, cardinality multiplies.
5. Natural join with no common attribute = Cartesian product.
6. Equi-join keeps both copies; natural join keeps one.
7. "All" → division.
8. "Both A and B" → intersection, not a conjunctive selection on one tuple.
9. FK-to-PK natural join (no NULLs) returns exactly |R| tuples.

---

## 10. Practice questions

**Q1.** R has 4 attributes and 10 tuples; S has 3 attributes and 6 tuples. R × S has:
(a) 7 attributes, 60 tuples (b) 12 attributes, 16 tuples (c) 7 attributes, 16 tuples (d) 12 attributes, 60 tuples

**Answer: (a).**

---

**Q2.** R and S are union compatible with |R| = 8 and |S| = 5. Maximum and minimum of |R ∪ S|?
(a) 13 and 8 (b) 13 and 5 (c) 8 and 5 (d) 40 and 8

**Answer: (a).**

---

**Q3.** Minimum and maximum of |R ∩ S| with |R| = 8, |S| = 5?
(a) 0 and 5 (b) 5 and 8 (c) 0 and 8 (d) 3 and 5

**Answer: (a).**

---

**Q4.** Which operator can return fewer tuples than its input even without any condition?
(a) σ (b) Π (c) × (d) ρ

**Answer: (b).** Duplicate elimination.

---

**Q5.** R(A, B) and S(C, D) have no common attributes. R ⋈ S equals:
(a) empty (b) R ∩ S (c) R × S (d) R ∪ S

**Answer: (c).**

---

**Q6.** Employee(eid, did) has 100 tuples; did is a non-null foreign key referencing Dept(did), the primary key of Dept (20 tuples). |Employee ⋈ Dept| = ?
(a) 20 (b) 100 (c) 2000 (d) 120

**Answer: (b).** Each employee matches exactly one department.

---

**Q7.** Using the Enrolled table in section 5.2 and Required = {c1, c2, c3}, Enrolled ÷ Required = ?
(a) {s1, s3} (b) {s3} (c) {s1} (d) {}

**Answer: (b).** Only s3 has all three.

---

**Q8.** R(A, B, C, D) ÷ S(C, D) has which attributes?
(a) A, B (b) C, D (c) A, B, C, D (d) A only

**Answer: (a).**

---

**Q9.** Which expression finds students enrolled in both 'C1' and 'C2'?
(a) `Π_sid(σ_cid='C1' ∧ cid='C2'(Enrolled))` (b) `Π_sid(σ_cid='C1'(Enrolled)) ∩ Π_sid(σ_cid='C2'(Enrolled))` (c) `Π_sid(σ_cid='C1' ∨ cid='C2'(Enrolled))` (d) `Π_sid(Enrolled) ÷ {C1}`

**Answer: (b).** (a) is always empty; (c) is "either".

---

**Q10.** Which join keeps all tuples of the left relation?
(a) natural join (b) left outer join (c) right outer join (d) semi-join

**Answer: (b).**

---

**Q11.** Which is NOT a fundamental relational algebra operator?
(a) Selection (b) Rename (c) Intersection (d) Set difference

**Answer: (c).**

---

**Q12.** R(A, B) = {(1, 2), (2, 3), (3, 4)}. Result of Π_A(σ_B > 2(R))?
(a) {1, 2, 3} (b) {2, 3} (c) {3, 4} (d) {1}

**Answer: (b).** Rows with B > 2: (2, 3), (3, 4). Project A: {2, 3}.

---

**Q13.** R(A) = {1, 2, 3, 4}, S(A) = {3, 4, 5}. R − S and S − R are:
(a) {1, 2} and {5} (b) {5} and {1, 2} (c) {3, 4} both (d) {1, 2, 5} both

**Answer: (a).**

---

**Q14.** R has 5 tuples and S has 3 tuples, no matching join values at all. |R ⟗ S| (full outer join) = ?
(a) 0 (b) 8 (c) 15 (d) 5

**Answer: (b).** Every tuple appears unmatched, padded with NULLs: 5 + 3 = 8.

---

**Practice questions:** [6.08 Relational Algebra](../ISRO_CS_Question_Bank/06_DBMS/6.08_Relational_Algebra.md)
