# 11. Tuple and Domain Relational Calculus

> **The idea in one line.** Relational algebra is a **recipe** (do this, then that). Relational calculus is a **description** ("give me all tuples such that..."). It's based on predicate logic, and it's the reason SQL looks the way it does.

---

## 1. Procedural vs declarative, once more

| Language | Style | You specify |
|---|---|---|
| Relational Algebra (RA) | **Procedural** | **How**: a sequence of operations |
| Tuple Relational Calculus (TRC) | **Declarative** | **What**: a condition on whole tuples |
| Domain Relational Calculus (DRC) | **Declarative** | **What**: a condition on individual attribute values |

**Codd's theorem:** RA, **safe** TRC and **safe** DRC have exactly the **same expressive power**. A language that can express everything these can is called **relationally complete**. SQL is relationally complete.

---

## 2. A crash course in the logic you need

- **Predicate:** a statement that is true or false, like `t.salary > 50000`.
- **Connectives:** ∧ (and), ∨ (or), ¬ (not), ⇒ (implies).
- **Quantifiers:**
  - **∃ t ∈ r (P(t))**: "there **exists** a tuple t in r such that P(t)".
  - **∀ t ∈ r (P(t))**: "**for every** tuple t in r, P(t)".

Useful equivalences:

```
P ⇒ Q                ≡  ¬P ∨ Q
¬(P ∧ Q)             ≡  ¬P ∨ ¬Q          (De Morgan)
¬(P ∨ Q)             ≡  ¬P ∧ ¬Q
∀ t (P(t))           ≡  ¬∃ t (¬P(t))      ("for all" = "no counter-example exists")
∃ t (P(t))           ≡  ¬∀ t (¬P(t))
```

The ∀ ↔ ¬∃¬ equivalence is exactly why SQL writes "for all" queries with **NOT EXISTS ... NOT EXISTS**.

### Free and bound variables

A variable is **bound** if it's introduced by ∃ or ∀; otherwise it's **free**. In `{t | P(t)}`, **t must be the only free variable**: it's the one that forms the answer.

---

## 3. Tuple Relational Calculus (TRC)

### 3.1 Form

```
{ t | P(t) }
```

"The set of all tuples t such that P(t) is true." Here **t is a tuple variable**: it stands for a whole row.

- `t ∈ r` (or `r(t)`): t is a tuple of relation r.
- `t.A` or `t[A]`: the value of attribute A in t.

### 3.2 Examples on a simple schema

`Student(roll, name, branch, cgpa)`, `Enrolled(roll, cid)`, `Course(cid, title)`.

**All CSE students:**
```
{ t | t ∈ Student ∧ t.branch = 'CSE' }
```

**Only their names** (projection). We build an output tuple t that has just a name:
```
{ t | ∃ s ∈ Student ( s.branch = 'CSE' ∧ t.name = s.name ) }
```
Some books write the shorthand `{ t.name | t ∈ Student ∧ t.branch = 'CSE' }`.

**Names of students enrolled in course 'C1'** (a join):
```
{ t | ∃ s ∈ Student ∃ e ∈ Enrolled ( s.roll = e.roll ∧ e.cid = 'C1' ∧ t.name = s.name ) }
```

### 3.3 Banking examples (classic textbook schema)

`loan(loan_no, branch, amount)`, `borrower(cname, loan_no)`, `depositor(cname, acc_no)`.

Loans over 1200:
```
{ t | t ∈ loan ∧ t.amount > 1200 }
```

Customers with a loan **or** an account:
```
{ t | ∃ s ∈ borrower (t.cname = s.cname) ∨ ∃ u ∈ depositor (t.cname = u.cname) }
```

Customers with **both** a loan and an account:
```
{ t | ∃ s ∈ borrower (t.cname = s.cname) ∧ ∃ u ∈ depositor (t.cname = u.cname) }
```

Customers with a loan but **no** account:
```
{ t | ∃ s ∈ borrower (t.cname = s.cname) ∧ ¬∃ u ∈ depositor (t.cname = u.cname) }
```

> **Trap.** "Or" → ∨ between two ∃. "Both" → ∧. "But not" → ∧ ¬∃. Mixing these up is the most common TRC mistake.

### 3.4 "For all" (division) in TRC

Students enrolled in **every** course:
```
{ t | ∃ s ∈ Student ( t.roll = s.roll ∧
        ∀ c ∈ Course ( ∃ e ∈ Enrolled ( e.roll = s.roll ∧ e.cid = c.cid ) ) ) }
```

Read the inner part: "for every course c, there is an enrolment row linking this student to c". That's RA's **÷**.

Equivalent with ¬∃: "there is **no** course c for which there is **no** enrolment".

---

## 4. Domain Relational Calculus (DRC)

### 4.1 Form

```
{ < x1, x2, ..., xn > | P(x1, x2, ..., xn) }
```

The variables range over **individual values** (domains), **one variable per attribute**, not over whole tuples.

- `< x1, ..., xn > ∈ r`: these values form a tuple of r.
- To output n columns you list **n domain variables**.

### 4.2 Examples

All CSE students (all four attributes):
```
{ < r, n, b, g > | < r, n, b, g > ∈ Student ∧ b = 'CSE' }
```

Only names of CSE students:
```
{ < n > | ∃ r, b, g ( < r, n, b, g > ∈ Student ∧ b = 'CSE' ) }
```

Loans over 1200:
```
{ < l, b, a > | < l, b, a > ∈ loan ∧ a > 1200 }
```

Names of students in course C1 (join through a shared variable r):
```
{ < n > | ∃ r, b, g ( < r, n, b, g > ∈ Student ∧ < r, 'C1' > ∈ Enrolled ) }
```

Notice how the join condition is expressed simply by **reusing the same variable r** in both memberships.

### 4.3 DRC and QBE

**QBE (Query By Example)**, a grid-based visual query language from IBM, is built on DRC: you fill example values (variables) in table skeletons, much like domain variables.

---

## 5. Safety of expressions

A calculus expression must produce a **finite** answer.

**Unsafe:**
```
{ t | ¬ (t ∈ Student) }
```
"Every tuple that is not a student", which includes every possible tuple in the universe: **infinite**.

An expression is **safe** if every value in the result comes from the **domain of the expression**: values appearing in the relations mentioned or constants in the formula. In practice: every free variable must be **bounded** by a positive membership like `t ∈ r`.

> **Trap.** Negation (¬) and universal quantification (∀) are the usual sources of unsafe expressions. Always anchor variables with `∈ relation`.

---

## 6. Comparison table

| Feature | RA | TRC | DRC |
|---|---|---|---|
| Style | Procedural | Declarative | Declarative |
| Variables | None (operators) | **Tuple** variables | **Domain** variables (one per attribute) |
| "For all" | Division ÷ | ∀ | ∀ |
| Practical language based on it | (query plans) | **SQL** | **QBE** |
| Expressive power | Same as safe TRC/DRC | Same | Same |

### Translation cheat sheet

| RA | TRC flavour |
|---|---|
| σ_p(R) | {t | t ∈ R ∧ p(t)} |
| Π_A(R) | {t | ∃ s ∈ R (t.A = s.A)} |
| R ∪ S | {t | t ∈ R ∨ t ∈ S} |
| R − S | {t | t ∈ R ∧ ¬(t ∈ S)} |
| R ∩ S | {t | t ∈ R ∧ t ∈ S} |
| R × S | {t | ∃ r ∈ R ∃ s ∈ S (t = r concatenated with s)} |
| R ÷ S | ∀ inside ∃ |

---

## 7. Exam traps

1. RA = procedural; TRC, DRC = declarative.
2. TRC variables are tuples; DRC variables are attribute values.
3. A degree-n DRC result needs **n** domain variables.
4. ∀ x P(x) ≡ ¬∃ x ¬P(x).
5. "Loan but no account" = ∃ ... ∧ ¬∃ ....
6. {t | ¬(t ∈ r)} is unsafe.
7. ∀ in calculus corresponds to ÷ in RA.
8. SQL ← TRC; QBE ← DRC.
9. Safe RA = safe TRC = safe DRC in power (Codd's theorem).

---

## 8. Practice questions

**Q1.** Which language specifies the sequence of operations to obtain a result?
(a) TRC (b) DRC (c) Relational algebra (d) QBE

**Answer: (c).**

---

**Q2.** In `{ t | ∃ s ∈ R (t.A = s.A ∧ s.B > 5) }`, which variable is free?
(a) s (b) t (c) both (d) neither

**Answer: (b).** s is bound by ∃.

---

**Q3.** `{ t | t ∈ R ∧ ¬(t ∈ S) }` corresponds to:
(a) R ∪ S (b) R ∩ S (c) R − S (d) R × S

**Answer: (c).**

---

**Q4.** Which expression is unsafe?
(a) `{ t | t ∈ R ∧ t.A > 10 }` (b) `{ t | ¬(t ∈ R) }` (c) `{ t | t ∈ R ∨ t ∈ S }` (d) `{ t | t ∈ R ∧ t ∈ S }`

**Answer: (b).**

---

**Q5.** A DRC query returns (name, city). How many free domain variables does the result tuple have?
(a) 1 (b) 2 (c) equal to the number of relations used (d) 0

**Answer: (b).**

---

**Q6.** `∀ x ∈ R (P(x))` is equivalent to:
(a) `∃ x ∈ R (¬P(x))` (b) `¬∃ x ∈ R (¬P(x))` (c) `¬∀ x ∈ R (¬P(x))` (d) `∃ x ∈ R (P(x))`

**Answer: (b).**

---

**Q7.** Customers who have an account but **no** loan:
(a) `{t | ∃ u ∈ depositor (t.cname = u.cname) ∨ ∃ s ∈ borrower (t.cname = s.cname)}`
(b) `{t | ∃ u ∈ depositor (t.cname = u.cname) ∧ ¬∃ s ∈ borrower (t.cname = s.cname)}`
(c) `{t | ¬∃ u ∈ depositor (t.cname = u.cname) ∧ ∃ s ∈ borrower (t.cname = s.cname)}`
(d) `{t | ∃ u ∈ depositor ∃ s ∈ borrower (u.cname = s.cname)}`

**Answer: (b).** (c) is the reverse: loan but no account.

---

**Q8.** Which practical language is based on Domain Relational Calculus?
(a) SQL (b) QBE (c) PL/SQL (d) Datalog only

**Answer: (b).**

---

**Q9.** The RA operator that corresponds most naturally to the universal quantifier is:
(a) σ (b) Π (c) ÷ (d) ∪

**Answer: (c).**

---

**Q10.** Codd's theorem states that:
(a) SQL is more powerful than RA (b) RA and safe relational calculus are equivalent in expressive power (c) TRC is more powerful than DRC (d) Calculus can't express joins

**Answer: (b).**

---

**Q11.** In DRC, a join between Student(r, n) and Enrolled(r, c) is expressed by:
(a) a ⋈ symbol (b) using the same domain variable r in both membership conditions (c) a Cartesian product keyword (d) a ∀ quantifier

**Answer: (b).**
