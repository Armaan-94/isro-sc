# A17. Sequences and Series, Statistics, Surds and Indices

> Four short toolkits. **AP** adds a constant, **GP** multiplies by a constant. **Statistics** summarises data with a centre (mean, median, mode) and a spread (variance, SD). **Indices and surds** are the grammar of powers and roots. All four show up as quick 1-mark questions.

---

## 1. Arithmetic progression (AP)

Each term = previous + **d**.

```
nth term:   Tₙ = a + (n − 1)d
Sum:        Sₙ = n/2 [2a + (n − 1)d] = n × (first + last)/2
Number of terms from a to l: n = (l − a)/d + 1
```

- 10th term of 5, 8, 11, ... = 5 + 9 × 3 = **32**.
- Terms in 7, 11, ..., 99: (99 − 7)/4 + 1 = **24**.
- 3 + 6 + ... + 300: 100 terms → 100 × 303/2 = **15,150**.

**Standard sums:**

```
1 + 2 + ... + n = n(n + 1)/2              (1 to 20 → 210)
1² + 2² + ... + n² = n(n + 1)(2n + 1)/6   (1 to 10 → 385)
1³ + 2³ + ... + n³ = [n(n + 1)/2]²        (1 to 10 → 3025)
1 + 3 + 5 + ... (n odd numbers) = n²      (first 50 odd → 2500)
```

## 2. Geometric progression (GP)

Each term = previous × **r**.

```
nth term:   Tₙ = a rⁿ⁻¹
Sum:        Sₙ = a(rⁿ − 1)/(r − 1)   (r ≠ 1)
Infinite sum (|r| < 1): S∞ = a/(1 − r)
```

- 8th term of 3, 6, 12, ... = 3 × 2⁷ = **384**.
- 1 + 1/2 + 1/4 + ... = 1/(1 − 1/2) = **2**.
- 2nd term 6, 5th term 48 → r³ = 8 → r = 2, a = **3**.

## 3. Means

For positive numbers: **AM ≥ GM ≥ HM**, and for two numbers **GM² = AM × HM**.

4 and 16: AM = 10, GM = 8, HM = 2 × 4 × 16/20 = **6.4** (check: 64 = 10 × 6.4 ✓).

---

## 4. Statistics

### Central tendency

- **Mean** = sum/count.
- **Median** = middle value of sorted data (average of the two middle values if count is even).
- **Mode** = most frequent value.
- Empirical relation (moderately skewed data): **Mode = 3 Median − 2 Mean**.

Median of 3, 7, 1, 9, 5, 8 → sorted 1, 3, 5, 7, 8, 9 → (5 + 7)/2 = **6**.

**Removing an item:** mean of 10 numbers is 24 (total 240); remove 33 → 207/9 = **23**.

### Grouped data formulas

```
Mean (step deviation) = A + h × Σfᵢuᵢ / Σfᵢ,     uᵢ = (xᵢ − A)/h
Median = L + [(N/2 − CF)/f] × h
Mode   = L + [(f₁ − f₀)/(2f₁ − f₀ − f₂)] × h
```

L = lower boundary of the median/modal class, CF = cumulative frequency before it, f = its frequency, f₀ and f₂ = neighbouring frequencies, h = class width.

### Spread

```
Variance σ² = Σ(x − mean)²/n = (Σx²/n) − mean²
Standard deviation σ = √variance
```

2, 4, 6, 8, 10: mean 6, squared deviations 16 + 4 + 0 + 4 + 16 = 40 → variance **8**.

### Effect of transformations

| Change to every value | Mean | Median/Mode | SD / Variance |
|---|---|---|---|
| Add k | + k | + k | **unchanged** |
| Multiply by k | × k | × k | SD × |k|, variance × k² |

---

## 5. Laws of indices

```
aᵐ × aⁿ = aᵐ⁺ⁿ          aᵐ / aⁿ = aᵐ⁻ⁿ          (aᵐ)ⁿ = aᵐⁿ
a⁰ = 1                   a⁻ⁿ = 1/aⁿ               a^(1/n) = ⁿ√a
(ab)ⁿ = aⁿbⁿ             a^(m/n) = (ⁿ√a)ᵐ
```

- 27^(2/3) = (∛27)² = **9**.
- 16^(−3/4) = 1/(⁴√16)³ = **1/8**.
- (5⁷ × 5⁻³)/5² = 5² = **25**.
- If aˣ = b, bʸ = c, cᶻ = a then a^(xyz) = a → **xyz = 1**.

**Equal bases → equal exponents:** 3^(x + 1) = 81 = 3⁴ → x = 3.

### Comparing powers

Bring to a **common exponent**: 2³⁰⁰ = 8¹⁰⁰ and 3²⁰⁰ = 9¹⁰⁰ → **3²⁰⁰ is bigger**.

## 6. Surds

- Simplify: √12 + √27 = 2√3 + 3√3 = **5√3**.
- (√a + √b)(√a − √b) = a − b.
- **Rationalise** with the conjugate: 1/(√7 − √5) = (√7 + √5)/(7 − 5) = **(√7 + √5)/2**.
- **Compare roots of different orders** by raising to the LCM: ∛5 vs √3 → power 6: 25 vs 27 → **√3 bigger**.

---

## 7. Exam traps

1. Number of terms = (last − first)/d **+ 1**.
2. Infinite GP sum needs |r| < 1.
3. Adding a constant doesn't change SD.
4. a⁰ = 1; negative exponent = reciprocal, not negative number.
5. Median needs sorted data.

---

## 8. Practice questions (with solutions)

**Q1.** Sum of the first 20 natural numbers?
(a) 200 (b) 210 (c) 190 (d) 220
**Answer: (b).**

**Q2.** 2⁵ × 2⁻² = ?
(a) 8 (b) 2 (c) 16 (d) 4
**Answer: (a).**

**Q3.** Rationalise 1/(√7 − √5).
(a) (√7 + √5)/2 (b) (√7 − √5)/2 (c) √7 + √5 (d) (√7 + √5)/12
**Answer: (a).**

**Q4.** 85² = ?
(a) 7225 (b) 7025 (c) 7125 (d) 7325
**Answer: (a).** 8 × 9 = 72, then 25.

**Q5.** Which is greater, ∛5 or √3?
(a) ∛5 (b) √3 (c) equal (d) can't say
**Answer: (b).**

**Q6.** Which is greater, 2³⁰⁰ or 3²⁰⁰?
(a) 2³⁰⁰ (b) 3²⁰⁰ (c) equal (d) can't say
**Answer: (b).**

**Q7.** Mean of 10 numbers is 24. Remove 33. New mean?
(a) 22 (b) 23 (c) 24 (d) 25
**Answer: (b).**

**Q8.** 3 + 6 + 9 + ... + 300 = ?
(a) 15,150 (b) 14,850 (c) 15,000 (d) 15,300
**Answer: (a).**

**Q9.** 10th term of 5, 8, 11, ...?
(a) 30 (b) 32 (c) 35 (d) 29
**Answer: (b).**

**Q10.** Number of terms in 7, 11, 15, ..., 99?
(a) 23 (b) 24 (c) 25 (d) 22
**Answer: (b).**

**Q11.** 8th term of 3, 6, 12, ...?
(a) 192 (b) 384 (c) 768 (d) 256
**Answer: (b).**

**Q12.** 1 + 1/2 + 1/4 + 1/8 + ... = ?
(a) 1.5 (b) 2 (c) 2.5 (d) ∞
**Answer: (b).**

**Q13.** 1² + 2² + ... + 10² = ?
(a) 285 (b) 385 (c) 505 (d) 355
**Answer: (b).**

**Q14.** 1³ + 2³ + ... + 10³ = ?
(a) 3025 (b) 2025 (c) 4025 (d) 55
**Answer: (a).**

**Q15.** Sum of the first 50 odd numbers?
(a) 2500 (b) 2550 (c) 2450 (d) 1250
**Answer: (a).**

**Q16.** Median of 3, 7, 1, 9, 5, 8?
(a) 5 (b) 6 (c) 7 (d) 5.5
**Answer: (b).**

**Q17.** Mode of 2, 3, 3, 5, 5, 5, 7?
(a) 3 (b) 5 (c) 7 (d) 4.3
**Answer: (b).**

**Q18.** Mean 40, median 42. Mode by the empirical formula?
(a) 44 (b) 46 (c) 38 (d) 48
**Answer: (b).**

**Q19.** SD of a data set is 4. Each value is multiplied by 3. New SD?
(a) 4 (b) 7 (c) 12 (d) 36
**Answer: (c).**

**Q20.** SD of a data set is 4. 10 is added to each value. New SD?
(a) 14 (b) 4 (c) 40 (d) 0
**Answer: (b).**

**Q21.** Variance of 2, 4, 6, 8, 10?
(a) 4 (b) 8 (c) 10 (d) 2√2
**Answer: (b).**

**Q22.** HM of 4 and 16?
(a) 6.4 (b) 8 (c) 10 (d) 6
**Answer: (a).**

**Q23.** 27^(2/3) = ?
(a) 3 (b) 9 (c) 18 (d) 6
**Answer: (b).**

**Q24.** 16^(−3/4) = ?
(a) 8 (b) −8 (c) 1/8 (d) −1/8
**Answer: (c).**

**Q25.** 3^(x + 1) = 81. x = ?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (b).**

**Q26.** √12 + √27 = ?
(a) √39 (b) 5√3 (c) 6√3 (d) 4√3
**Answer: (b).**

**Q27.** (√5 + √3)(√5 − √3) = ?
(a) 2 (b) 8 (c) √2 (d) 4
**Answer: (a).**

**Q28.** (5⁷ × 5⁻³)/5² = ?
(a) 5 (b) 25 (c) 125 (d) 625
**Answer: (b).**

**Q29.** aˣ = b, bʸ = c, cᶻ = a. xyz = ?
(a) 0 (b) 1 (c) abc (d) −1
**Answer: (b).**

**Q30.** Mean of the first n natural numbers is 15. n = ?
(a) 28 (b) 29 (c) 30 (d) 31
**Answer: (b).** (n + 1)/2 = 15.

**Q31.** In a GP the 2nd term is 6 and the 5th is 48. First term?
(a) 2 (b) 3 (c) 4 (d) 6
**Answer: (b).**
