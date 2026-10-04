# B4. Vectors

> A vector is an arrow: **how much** (magnitude) and **which way** (direction). The two products answer two questions. **Dot product:** how much do the arrows point the same way? (A number; zero means perpendicular.) **Cross product:** how much area do they span, and which way is "up"? (A vector; zero means parallel.)

---

## 1. Basics

- **Scalar:** magnitude only (mass, time). **Vector:** magnitude and direction (velocity, force).
- Component form: **a = a₁i + a₂j + a₃k**, magnitude **|a| = √(a₁² + a₂² + a₃²)**.
- **Unit vector** along a: **â = a/|a|**. Along 3i + 4j: (3i + 4j)/5.
- |2i − 3j + 6k| = √(4 + 9 + 36) = **7**.

**Types:** position vector (from the origin), zero vector, unit vector, collinear (parallel), coplanar (in one plane), coinitial (same start), free vector.

## 2. Addition

**Triangle law:** place b's tail at a's head; a + b goes from a's tail to b's head.
**Parallelogram law:** draw a and b from the same point; a + b is the diagonal. Same result, two pictures.

**Section formula:** P divides AB internally in ratio m : n → **P = (mB + nA)/(m + n)**.
A(1, 2), B(6, 7), 2 : 3 → (2(6, 7) + 3(1, 2))/5 = **(3, 4)**. Midpoint = (A + B)/2.

## 3. Direction cosines and ratios

Direction cosines l, m, n = cosines of the angles with the x, y, z axes; **l² + m² + n² = 1**.
For (1, 2, 2): |a| = 3 → DCs **(1/3, 2/3, 2/3)**. The components themselves are direction ratios.

**Parallel test:** a = λb ⟺ a₁/b₁ = a₂/b₂ = a₃/b₃. (2, 4, 6) = 2(1, 2, 3) → parallel.

---

## 4. Dot product (scalar)

```
a · b = |a||b| cos θ = a₁b₁ + a₂b₂ + a₃b₃
```

- **a · b = 0 ⟺ perpendicular** (non-zero vectors).
- a · a = |a|²; i · i = 1, i · j = 0.
- Commutative: a · b = b · a.
- Angle: cos θ = a · b / (|a||b|).
- **Projection** of a on b = a · b / |b|.
- |a + b|² = |a|² + |b|² + 2 a · b.

Examples:
- (1, 2, 3) · (4, −5, 6) = 4 − 10 + 18 = **12**.
- (i + j) · (i − j) = 0 → **90°**.
- (1, 0, 0) and (1, 1, 0): cos θ = 1/√2 → **45°**.
- (2, λ, 1) ⟂ (1, −2, 3): 2 − 2λ + 3 = 0 → **λ = 2.5**.
- Projection of (2, 3, 6) on (1, 2, 2): 20/3.
- |a| = 3, |b| = 4, perpendicular → |a + b| = **5**.

## 5. Cross product (vector)

```
a × b = |a||b| sin θ n̂       (perpendicular to both a and b, right-hand rule)

a × b = | i   j   k  |
        | a₁  a₂  a₃ |
        | b₁  b₂  b₃ |  = (a₂b₃ − a₃b₂) i − (a₁b₃ − a₃b₁) j + (a₁b₂ − a₂b₁) k
```

- **a × b = 0 ⟺ parallel** (non-zero vectors).
- **Anti-commutative:** b × a = −(a × b). i × j = k, j × k = i, k × i = j; j × i = −k.
- **|a × b| = area of the parallelogram**; half of it = area of the triangle.

Examples:
- (1, 2, 3) × (4, 5, 6) = (12 − 15, 12 − 6, 5 − 8) = **(−3, 6, −3)**.
- Triangle with sides 2i + 3j and i + 4j: k-component 8 − 3 = 5 → area **2.5**.
- Parallelogram on i + j and i − j: |−2k| = **2**.

If |a × b| = a · b, then sin θ = cos θ → **θ = 45°**.

## 6. Scalar triple product

[a b c] = a · (b × c) = determinant of the three component rows.
- |[a b c]| = **volume of the parallelepiped**. For i, j, k: **1**.
- [a b c] = 0 ⟺ **coplanar**.

---

## 7. Exam traps

1. Dot → scalar, uses cos, zero means perpendicular. Cross → vector, uses sin, zero means parallel.
2. a × b = −b × a.
3. a · a = |a|², not |a|.
4. l² + m² + n² = 1 for direction cosines only, not ratios.

---

## 8. Practice questions (with solutions)

**Q1.** Unit vector along 3i + 4j?
(a) (3i + 4j)/5 (b) (3i + 4j)/7 (c) 3i + 4j (d) (4i + 3j)/5
**Answer: (a).**

**Q2.** A dot product gives a:
(a) vector (b) scalar (c) matrix (d) unit vector
**Answer: (b).**

**Q3.** Are 2i + 4j + 6k and i + 2j + 3k collinear?
(a) yes, ratio 2 throughout (b) no (c) only if equal magnitude (d) can't say
**Answer: (a).**

**Q4.** a · b = 0 for non-zero vectors means:
(a) parallel (b) perpendicular (c) equal (d) one is zero
**Answer: (b).**

**Q5.** a × b = 0 for non-zero vectors means:
(a) perpendicular (b) parallel (c) equal magnitude (d) a · b = 0
**Answer: (b).**

**Q6.** Area of the triangle with sides 2i + 3j and i + 4j?
(a) 2.5 (b) 5 (c) 10 (d) 11
**Answer: (a).**

**Q7.** Angle between i + j and i − j?
(a) 0° (b) 45° (c) 90° (d) 180°
**Answer: (c).**

**Q8.** P divides A(1, 2), B(6, 7) internally in 2 : 3. P?
(a) (3, 4) (b) (2, 4) (c) (4, 5) (d) (3, 5)
**Answer: (a).**

**Q9.** |2i − 3j + 6k|?
(a) 5 (b) 7 (c) 11 (d) 49
**Answer: (b).**

**Q10.** (i + 2j + 3k) · (4i − 5j + 6k)?
(a) 12 (b) 32 (c) −12 (d) 0
**Answer: (a).**

**Q11.** j × i = ?
(a) k (b) −k (c) 0 (d) 1
**Answer: (b).**

**Q12.** (1, 2, 3) × (4, 5, 6) = ?
(a) (−3, 6, −3) (b) (3, −6, 3) (c) (−3, −6, −3) (d) (4, 10, 18)
**Answer: (a).**

**Q13.** Area of the parallelogram on i + j and i − j?
(a) 1 (b) 2 (c) √2 (d) 0
**Answer: (b).**

**Q14.** 2i + λj + k is perpendicular to i − 2j + 3k. λ = ?
(a) 2 (b) 2.5 (c) 3 (d) −2.5
**Answer: (b).**

**Q15.** Scalar projection of 2i + 3j + 6k on i + 2j + 2k?
(a) 20/3 (b) 20/7 (c) 20 (d) 3
**Answer: (a).**

**Q16.** Direction cosines of i + 2j + 2k?
(a) (1, 2, 2) (b) (1/3, 2/3, 2/3) (c) (1/5, 2/5, 2/5) (d) (1/√5, 2/√5, 2/√5)
**Answer: (b).**

**Q17.** The scalar triple product of three vectors is 0. They are:
(a) perpendicular (b) coplanar (c) unit vectors (d) equal
**Answer: (b).**

**Q18.** a · a equals:
(a) |a| (b) |a|² (c) 0 (d) 1
**Answer: (b).**

**Q19.** Angle between i and i + j?
(a) 30° (b) 45° (c) 60° (d) 90°
**Answer: (b).**

**Q20.** |a| = 3, |b| = 4, a ⟂ b. |a + b|?
(a) 7 (b) 5 (c) 1 (d) 12
**Answer: (b).**

**Q21.** |a × b| = a · b for non-zero a, b. Angle?
(a) 0° (b) 45° (c) 60° (d) 90°
**Answer: (b).**

**Q22.** Volume of the parallelepiped formed by i, j, k?
(a) 0 (b) 1 (c) 3 (d) 6
**Answer: (b).**

**Q23.** Two direction cosines are 1/2 and 1/2. The third?
(a) ±1/2 (b) ±1/√2 (c) 0 (d) 1
**Answer: (b).** 1 − 1/4 − 1/4 = 1/2.

**Q24.** Which product is commutative?
(a) cross (b) dot (c) both (d) neither
**Answer: (b).**
