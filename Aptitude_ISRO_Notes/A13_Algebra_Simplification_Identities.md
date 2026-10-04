# A13. Algebra: Simplification and Identities

> **Identities are compression.** Instead of expanding or solving for unknowns, a well-chosen identity gets the answer in one step. The exam rewards recognising patterns: "a + b + c = 0", "x + 1/x = k", "difference of squares".

---

## 1. Order of operations: BODMAS

**B**rackets → **O**f → **D**ivision and **M**ultiplication (left to right) → **A**ddition and **S**ubtraction (left to right).

- "Of" means multiplication.
- **Division and multiplication have equal priority**: evaluate left to right. 8 ÷ 2 × 2 = 4 × 2 = **8** (not 2).
- Same for addition and subtraction.

Examples:
- 12 + 6 of 2 − 4 ÷ 2 = 12 + 12 − 2 = **22**.
- 24 ÷ 4 × 2 + 3² − 1 = 6 × 2 + 9 − 1 = **20**.

Bracket order: ( ) then { } then [ ]. A bar (vinculum) over terms is evaluated first.

---

## 2. Core identities

```
(a + b)² = a² + 2ab + b²
(a − b)² = a² − 2ab + b²
a² − b² = (a + b)(a − b)
(a + b)² + (a − b)² = 2(a² + b²)
(a + b)² − (a − b)² = 4ab

(a + b)³ = a³ + b³ + 3ab(a + b)
(a − b)³ = a³ − b³ − 3ab(a − b)
a³ + b³ = (a + b)(a² − ab + b²)
a³ − b³ = (a − b)(a² + ab + b²)

(a + b + c)² = a² + b² + c² + 2(ab + bc + ca)
a³ + b³ + c³ − 3abc = (a + b + c)(a² + b² + c² − ab − bc − ca)
```

### Using them

- a + b = 7, ab = 10 → a² + b² = 49 − 20 = **29**.
- a + b = 5, ab = 6 → a³ + b³ = 125 − 3·6·5 = **35**.
- a − b = 3, ab = 10 → a³ − b³ = 27 + 3·10·3 = **117**.
- x + y = 12, xy = 32 → x² + y² = 144 − 64 = **80**.

### Mental arithmetic with identities

- 999² = (1000 − 1)² = 1,000,000 − 2000 + 1 = **998,001**.
- 103 × 97 = (100 + 3)(100 − 3) = 10,000 − 9 = **9991**.
- (1.5² − 0.5²)/(1.5 − 0.5) = 1.5 + 0.5 = **2**.
- (0.75³ + 0.25³)/(0.75² − 0.75 × 0.25 + 0.25²) = 0.75 + 0.25 = **1** (a³ + b³ over the matching factor).

---

## 3. The a + b + c = 0 shortcut

If **a + b + c = 0**, then **a³ + b³ + c³ = 3abc**.

5, −3, −2: 125 − 27 − 8 = 90 = 3 × 5 × (−3) × (−2) ✓.

Classic disguise: (x − y)³ + (y − z)³ + (z − x)³ = 3(x − y)(y − z)(z − x), because the three brackets add to 0.

---

## 4. Reciprocal identities

Given **x + 1/x = k**:
- x² + 1/x² = **k² − 2**
- x³ + 1/x³ = **k³ − 3k**
- x⁴ + 1/x⁴ = (k² − 2)² − 2

Given **x − 1/x = k**:
- x² + 1/x² = **k² + 2**
- x³ − 1/x³ = **k³ + 3k**

Examples:
- x + 1/x = 4 → x² + 1/x² = **14**.
- x + 1/x = 5 → x² + 1/x² = 23, x³ + 1/x³ = 125 − 15 = **110**.
- x + 1/x = 6 → x³ + 1/x³ = 216 − 18 = **198**.
- x − 1/x = 3 → x² + 1/x² = **11**.
- x² + 1/x² = 47 → (x + 1/x)² = 49 → x + 1/x = **7**.
- x = 2 + √3 → 1/x = 2 − √3 → x + 1/x = 4 → x² + 1/x² = **14**.
- x + 1/x = 2 → x = 1 → any power: x¹⁰⁰ + 1/x¹⁰⁰ = **2**.

---

## 5. Linear equations

**Elimination:** make coefficients equal, then add/subtract.
2x + 3y = 13 and 3x − 2y = 0 → from the second, y = 1.5x → 6.5x = 13 → **x = 2, y = 3**.

**Symmetric systems:** x/2 + y/3 = 5 and x/3 + y/2 = 5. Swapping x and y gives the same equations, so x = y → 5x/6 = 5 → **x = y = 6**.

**Sum and difference:** two numbers with sum 25 and difference 7 → (25 + 7)/2 = **16** and 9.

---

## 6. Quadratics (basics)

ax² + bx + c = 0:
- Roots: x = [−b ± √(b² − 4ac)]/(2a).
- **Sum of roots = −b/a**, **product = c/a**.
- **Discriminant D = b² − 4ac**: D > 0 two real roots; D = 0 equal roots; D < 0 no real roots.

x² − 5x + 6 = 0 → (x − 2)(x − 3) → roots **2, 3**.
x² − 7x + 12 with roots α, β → α² + β² = (α + β)² − 2αβ = 49 − 24 = **25**.
x² + x + 1: D = −3 → **no real roots**.

---

## 7. Telescoping sums

1/(1·2) + 1/(2·3) + ... + 1/(n(n + 1)) = 1 − 1/(n + 1) = n/(n + 1). For n = 9: **9/10**.

(Because 1/(k(k + 1)) = 1/k − 1/(k + 1), and the middle terms cancel.)

---

## 8. Exam traps

1. ÷ and × left to right; "of" is multiplication.
2. x + 1/x → subtract 2 for squares; x − 1/x → add 2.
3. Don't forget the −3k in x³ + 1/x³.
4. Check for a + b + c = 0 whenever cubes appear.

---

## 9. Practice questions (with solutions)

**Q1.** 12 + 6 of 2 − 4 ÷ 2 = ?
(a) 22 (b) 8 (c) 14 (d) 10
**Answer: (a).**

**Q2.** a − b = 4, a + b = 10. a² − b² = ?
(a) 40 (b) 36 (c) 44 (d) 14
**Answer: (a).**

**Q3.** x + y + z = 0 with x = 3, y = −1. x³ + y³ + z³ = ?
(a) 12 (b) 18 (c) −18 (d) 6
**Answer: (b).** z = −2; 3xyz = 18.

**Q4.** a + 1/a = 4. a² + 1/a² = ?
(a) 14 (b) 16 (c) 18 (d) 12
**Answer: (a).**

**Q5.** a − 1/a = 3. a² + 1/a² = ?
(a) 9 (b) 7 (c) 11 (d) 13
**Answer: (c).**

**Q6.** a + 1/a = 6. a³ + 1/a³ = ?
(a) 216 (b) 198 (c) 180 (d) 174
**Answer: (b).**

**Q7.** 24 ÷ 4 × 2 + 3² − 1 = ?
(a) 19 (b) 12 (c) 20 (d) 14
**Answer: (c).**

**Q8.** 2x + 3y = 13, 3x − 2y = 0. (x, y) = ?
(a) (2, 3) (b) (3, 2) (c) (2, 4.5) (d) (4, 1.67)
**Answer: (a).**

**Q9.** x² + 1/x² = 47. Positive value of x + 1/x?
(a) 6 (b) 7 (c) 8 (d) 9
**Answer: (b).**

**Q10.** a + b = 7, ab = 10. a² + b² = ?
(a) 29 (b) 39 (c) 49 (d) 19
**Answer: (a).**

**Q11.** a + b = 5, ab = 6. a³ + b³ = ?
(a) 35 (b) 125 (c) 90 (d) 215
**Answer: (a).**

**Q12.** a − b = 3, ab = 10. a³ − b³ = ?
(a) 117 (b) 27 (c) 90 (d) 63
**Answer: (a).**

**Q13.** (0.75³ + 0.25³)/(0.75² − 0.75 × 0.25 + 0.25²) = ?
(a) 0.5 (b) 1 (c) 0.75 (d) 1.25
**Answer: (b).**

**Q14.** 999² = ?
(a) 998001 (b) 999001 (c) 998999 (d) 997001
**Answer: (a).**

**Q15.** 103 × 97 = ?
(a) 9991 (b) 9999 (c) 10009 (d) 9901
**Answer: (a).**

**Q16.** x = 2 + √3. x² + 1/x² = ?
(a) 12 (b) 14 (c) 16 (d) 10
**Answer: (b).**

**Q17.** x/2 + y/3 = 5 and x/3 + y/2 = 5. x + y = ?
(a) 10 (b) 12 (c) 15 (d) 6
**Answer: (b).**

**Q18.** Two numbers have sum 25 and difference 7. The larger?
(a) 14 (b) 15 (c) 16 (d) 18
**Answer: (c).**

**Q19.** Roots of x² − 5x + 6 = 0?
(a) 1, 6 (b) 2, 3 (c) −2, −3 (d) 3, 4
**Answer: (b).**

**Q20.** α, β are roots of x² − 7x + 12 = 0. α² + β² = ?
(a) 25 (b) 49 (c) 24 (d) 37
**Answer: (a).**

**Q21.** x² + x + 1 = 0 has:
(a) two real roots (b) equal roots (c) no real roots (d) one zero root
**Answer: (c).**

**Q22.** x + y = 12, xy = 32. x² + y² = ?
(a) 80 (b) 112 (c) 144 (d) 64
**Answer: (a).**

**Q23.** a + 1/a = 2. a¹⁰⁰ + 1/a¹⁰⁰ = ?
(a) 2¹⁰⁰ (b) 2 (c) 0 (d) 1
**Answer: (b).**

**Q24.** 1/(1·2) + 1/(2·3) + ... + 1/(9·10) = ?
(a) 1/10 (b) 9/10 (c) 1 (d) 10/9
**Answer: (b).**

**Q25.** (x − y)³ + (y − z)³ + (z − x)³ = ?
(a) 0 (b) 3(x − y)(y − z)(z − x) (c) (x − y)(y − z)(z − x) (d) 3xyz
**Answer: (b).**

**Q26.** a = 1, b = 2, c = 3. a³ + b³ + c³ − 3abc = ?
(a) 18 (b) 36 (c) 0 (d) 6
**Answer: (a).** 36 − 18.

**Q27.** 8 ÷ 2 × (2 + 2) = ?
(a) 1 (b) 16 (c) 4 (d) 8
**Answer: (b).** Bracket = 4; then 8 ÷ 2 × 4 = 4 × 4.

**Q28.** If x − 1/x = 2, then x³ − 1/x³ = ?
(a) 8 (b) 14 (c) 6 (d) 10
**Answer: (b).** k³ + 3k = 8 + 6.
