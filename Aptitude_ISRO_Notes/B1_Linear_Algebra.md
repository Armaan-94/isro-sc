# B1. Linear Algebra

> **A matrix is a machine that transforms vectors.** The determinant tells you how much it stretches area/volume (0 means it squashes space flat, so it can't be undone). Rank tells you how many independent directions survive. Eigenvectors are the special directions the machine only stretches, never turns. Keep that picture and every formula below has a reason.

---

## 1. Matrix types

An m × n matrix has m rows and n columns. Two matrices are equal only if they have the same order and identical entries.

| Type | Property |
|---|---|
| Square | m = n |
| Diagonal | Square; off-diagonal entries 0 |
| Scalar | Diagonal with all diagonal entries equal |
| Identity I | Scalar with 1s on the diagonal |
| Upper/lower triangular | Zeros below/above the diagonal |
| Symmetric | Aᵀ = A |
| Skew-symmetric | Aᵀ = −A (diagonal must be 0) |
| Orthogonal | AᵀA = I, so A⁻¹ = Aᵀ; det = ±1 |
| Idempotent | A² = A |
| Involutory | A² = I |
| Nilpotent | Aᵏ = 0 for some k |
| Singular | det A = 0 |

Every square matrix = symmetric part (A + Aᵀ)/2 + skew part (A − Aᵀ)/2.

---

## 2. Operations

- **Addition:** same order only, entry by entry.
- **Multiplication:** (m × n)(n × p) = (m × p). Inner dimensions must match. **AB ≠ BA** in general; AB may exist when BA doesn't.
- **Transpose:** (Aᵀ)ᵀ = A, (A + B)ᵀ = Aᵀ + Bᵀ, **(AB)ᵀ = BᵀAᵀ** (order reverses, like taking off socks and shoes).
- **Inverse:** (AB)⁻¹ = B⁻¹A⁻¹ (also reverses).
- **Trace** = sum of the diagonal. tr(A + B) = trA + trB; **tr(AB) = tr(BA)** even though AB ≠ BA.

---

## 3. Determinant

```
|a b; c d| = ad − bc
|a b c; d e f; g h i| = a(ei − fh) − b(di − fg) + c(dh − eg)
```

**Properties (the real exam content):**

| Property | Result |
|---|---|
| det(AB) | det A · det B |
| det(Aᵀ) | det A |
| det(kA), A is n × n | **kⁿ det A** |
| det(A⁻¹) | 1/det A |
| det(adj A) | (det A)ⁿ⁻¹ |
| Swap two rows | sign flips |
| Two equal (or proportional) rows, or a zero row | det = 0 |
| Add a multiple of one row to another | unchanged |
| Triangular/diagonal | product of diagonal entries |
| Skew-symmetric of **odd** order | 0 |

- 3 × 3 with det 5 → det(2A) = 2³ × 5 = **40**.
- [[1, 2, 3], [4, 5, 6], [7, 8, 9]]: row 3 − row 2 = row 2 − row 1, so rows are dependent → **0**.

## 4. Cofactors, adjoint, inverse

- Cofactor Cᵢⱼ = (−1)^(i + j) × minor.
- adj A = transpose of the cofactor matrix. A · adj A = (det A) I.
- **A⁻¹ = adj A / det A**, only when det A ≠ 0.
- **2 × 2 shortcut:** for [[a, b], [c, d]], inverse = (1/(ad − bc)) [[d, −b], [−c, a]]. Swap the diagonal, negate the off-diagonal.

[[2, 1], [5, 3]]: det = 1 → inverse **[[3, −1], [−5, 2]]**.

---

## 5. Rank

**Rank = number of linearly independent rows (= columns)** = number of non-zero rows in row-echelon form.

- rank ≤ min(m, n).
- n × n matrix: full rank n ⟺ det ≠ 0 ⟺ invertible.
- [[1, 2], [2, 4]]: row 2 = 2 × row 1 → **rank 1**.

## 6. Systems of linear equations Ax = b

Compare rank(A) with rank of the augmented matrix [A | b], n = number of unknowns:

| Condition | Solutions |
|---|---|
| rank A = rank [A\|b] = n | **Unique** |
| rank A = rank [A\|b] < n | **Infinitely many** |
| rank A ≠ rank [A\|b] | **None** (inconsistent) |

Picture: two lines in the plane meet at one point, coincide (infinite), or are parallel (none).
- x + y = 2, 2x + 2y = 4 → same line → **infinite**.
- x + y = 2, 2x + 2y = 5 → parallel → **none**.

**Homogeneous Ax = 0** always has x = 0. A **non-trivial** solution exists ⟺ **det A = 0**.

Methods: Gaussian elimination, Cramer's rule (xᵢ = det Aᵢ / det A), x = A⁻¹b.

---

## 7. Eigenvalues and eigenvectors

**Av = λv** (v ≠ 0). Find λ from **det(A − λI) = 0**.

**Shortcuts:**
- **Sum of eigenvalues = trace**; **product = determinant**.
- Triangular/diagonal: eigenvalues = diagonal entries.
- 2 × 2: λ² − (trace)λ + det = 0.
- If λ is an eigenvalue of A: **λᵏ** for Aᵏ, **1/λ** for A⁻¹, **λ + k** for A + kI, **kλ** for kA; Aᵀ has the same eigenvalues.
- Real symmetric matrices have **real** eigenvalues and orthogonal eigenvectors.
- A is singular ⟺ 0 is an eigenvalue.
- Eigenvectors for distinct eigenvalues are linearly independent.

**Cayley-Hamilton theorem:** every square matrix satisfies its own characteristic equation. For a 2 × 2: A² − (tr A)A + (det A)I = 0.

**Examples:**
- [[2, 1], [1, 2]]: trace 4, det 3 → λ² − 4λ + 3 → **1, 3**.
- [[4, 1], [2, 3]]: trace 7, det 10 → **2, 5**.
- 3 × 3, trace 9, two eigenvalues 2 and 3 → third **4** (check: 2 × 3 × 4 = 24 = det).
- [[1, 1], [0, 1]]: λ = 1 twice, but A − I = [[0, 1], [0, 0]] has rank 1 → only **1** independent eigenvector.

---

## 8. Exam traps

1. det(kA) = kⁿ det A, not k det A.
2. (AB)ᵀ = BᵀAᵀ.
3. Homogeneous systems are never inconsistent.
4. Repeated eigenvalue does not guarantee two independent eigenvectors.
5. tr(AB) = tr(BA) always.

---

## 9. Practice questions (with solutions)

**Q1.** det [[2, 3], [1, 4]]?
(a) 5 (b) 8 (c) 11 (d) −5
**Answer: (a).**

**Q2.** Which matrix is always invertible?
(a) zero matrix (b) det = 0 (c) det ≠ 0 (d) singular matrix
**Answer: (c).**

**Q3.** A is 3 × 3 with det 5. det(2A)?
(a) 10 (b) 20 (c) 40 (d) 5
**Answer: (c).**

**Q4.** A is 2 × 3, B is 3 × 2. Which is generally false?
(a) AB is 2 × 2 (b) BA is 3 × 3 (c) AB = BA (d) both products exist
**Answer: (c).**

**Q5.** A² = A means A is:
(a) nilpotent (b) idempotent (c) involutory (d) orthogonal
**Answer: (b).**

**Q6.** 4 × 4 upper triangular with diagonal 2, 3, 5, 7. det?
(a) 17 (b) 210 (c) 35 (d) 12
**Answer: (b).**

**Q7.** 3 equations, 3 unknowns, rank A = 2, rank [A|b] = 3:
(a) unique (b) infinite (c) no solution (d) trivial only
**Answer: (c).**

**Q8.** 3 × 3 with trace 9, det 24, two eigenvalues 2 and 3. Third?
(a) 4 (b) 3 (c) 6 (d) 9
**Answer: (a).**

**Q9.** (AB)ᵀ = ?
(a) AᵀBᵀ (b) BᵀAᵀ (c) (BA)ᵀ (d) AB
**Answer: (b).**

**Q10.** A homogeneous 3 × 3 system has a non-trivial solution. Then:
(a) det ≠ 0 (b) det = 0 (c) rank = 3 (d) inconsistent
**Answer: (b).**

**Q11.** det [[1, 2, 3], [4, 5, 6], [7, 8, 9]]?
(a) 0 (b) 1 (c) −3 (d) 6
**Answer: (a).**

**Q12.** Rank of [[1, 2], [2, 4]]?
(a) 0 (b) 1 (c) 2 (d) 4
**Answer: (b).**

**Q13.** Eigenvalues of [[2, 1], [1, 2]]?
(a) 1, 3 (b) 2, 2 (c) 0, 4 (d) 1, 4
**Answer: (a).**

**Q14.** Eigenvalues of [[4, 1], [2, 3]]?
(a) 1, 6 (b) 2, 5 (c) 3, 4 (d) −2, −5
**Answer: (b).**

**Q15.** Eigenvalues of A are 2 and 3. Eigenvalues of A² + I?
(a) 4, 9 (b) 5, 10 (c) 3, 4 (d) 5, 7
**Answer: (b).**

**Q16.** Inverse of [[2, 1], [5, 3]]?
(a) [[3, −1], [−5, 2]] (b) [[3, 1], [5, 2]] (c) [[−3, 1], [5, −2]] (d) [[2, −5], [−1, 3]]
**Answer: (a).**

**Q17.** Determinant of a 3 × 3 skew-symmetric matrix?
(a) 1 (b) 0 (c) −1 (d) depends
**Answer: (b).**

**Q18.** For square A, B of the same order, always true:
(a) AB = BA (b) tr(AB) = tr(BA) (c) det(A + B) = det A + det B (d) (AB)⁻¹ = A⁻¹B⁻¹
**Answer: (b).**

**Q19.** det A = 4. det(A⁻¹)?
(a) 4 (b) −4 (c) 1/4 (d) 16
**Answer: (c).**

**Q20.** A is 3 × 3 with det 5. det(adj A)?
(a) 5 (b) 25 (c) 125 (d) 15
**Answer: (b).**

**Q21.** Maximum rank of a 3 × 4 matrix?
(a) 3 (b) 4 (c) 7 (d) 12
**Answer: (a).**

**Q22.** Determinant of an orthogonal matrix?
(a) 0 (b) ±1 (c) any real (d) 2
**Answer: (b).**

**Q23.** x + y = 2 and 2x + 2y = 4 have:
(a) unique solution (b) infinitely many (c) no solution (d) exactly two
**Answer: (b).**

**Q24.** x + y = 2 and 2x + 2y = 5 have:
(a) unique (b) infinite (c) no solution (d) two
**Answer: (c).**

**Q25.** For A = [[1, 2], [3, 4]], Cayley-Hamilton gives:
(a) A² − 5A − 2I = 0 (b) A² + 5A − 2I = 0 (c) A² − 5A + 2I = 0 (d) A² − 4A − 2I = 0
**Answer: (a).** trace 5, det −2.

**Q26.** Eigenvalues of [[3, 5], [0, 7]]?
(a) 3, 7 (b) 3, 5 (c) 5, 7 (d) 0, 10
**Answer: (a).**

**Q27.** Order of AB with A 2 × 3 and B 3 × 4?
(a) 3 × 3 (b) 2 × 4 (c) 4 × 2 (d) undefined
**Answer: (b).**

**Q28.** Number of linearly independent eigenvectors of [[1, 1], [0, 1]]?
(a) 0 (b) 1 (c) 2 (d) infinite
**Answer: (b).**

**Q29.** Eigenvalues of a real symmetric matrix are always:
(a) complex (b) real (c) zero (d) positive
**Answer: (b).**

**Q30.** A has an eigenvalue 0. Then A is:
(a) identity (b) singular (c) orthogonal (d) symmetric
**Answer: (b).**
