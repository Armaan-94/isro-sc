# A19. Speed Mathematics (Fast Calculation Tricks)

> In a no-calculator exam with 80 questions in 120 minutes, arithmetic speed is a real advantage. These tricks aren't magic: each is an algebraic identity in disguise. Practise each one five times and it becomes automatic.

---

## 1. Squares

### Numbers ending in 5
(10a + 5)² = a(a + 1) followed by **25**.
65² → 6 × 7 = 42 → **4225**. 85² → **7225**. 75² → **5625**. 105² → 10 × 11 = 110 → **11,025**.

### Near a base: (B ± x)² = B² ± 2Bx + x²
- 48² = 2500 − 200 + 4 = **2304** (base 50).
- 999² = 1,000,000 − 2000 + 1 = **998,001**.
- 995² = 1,000,000 − 10,000 + 25 = **990,025**.

### Product of numbers equidistant from a midpoint
(m + x)(m − x) = m² − x²: 46 × 44 = 45² − 1 = **2024**. 103 × 97 = 9991.

## 2. Multiplication near a base

```
(B + x)(B + y) = B² + B(x + y) + xy
```

- 104 × 107 = 10,000 + 1100 + 28 = **11,128**.
- 97 × 96 = 10,000 − 700 + 12 = **9312**.
- 98 × 97 = 10,000 − 500 + 6 = **9506**.
- 112 × 108 = 10,000 + 2000 + 96 = **12,096**.
- 203 × 198 (base 200): 40,000 + 200 − 6 = **40,194**.

### Other shortcuts
- **× 5** = × 10 ÷ 2. **× 25** = × 100 ÷ 4 (25 × 48 = **1200**). **× 125** = × 1000 ÷ 8 (125 × 64 = **8000**).
- **× 11 (2-digit):** write the digits apart and put their sum in the middle, carrying if needed. 52 × 11 = 5|7|2 = **572**; 87 × 11 = 8|15|7 → **957**.
- **× 9, 99, 999:** n × 99 = 100n − n.

## 3. Square roots of perfect squares

1. Pair digits from the right: the number of pairs = digits in the root.
2. The **last digit** narrows the root's last digit:

| Square ends in | 1 | 4 | 5 | 6 | 9 | 0 |
|---|---|---|---|---|---|---|
| Root ends in | 1 or 9 | 2 or 8 | 5 | 4 or 6 | 3 or 7 | 0 |

(Squares never end in 2, 3, 7 or 8.)

3. Bracket with known squares to pick the leading part and choose between the two candidates.

- √7056: ends 6 → 4 or 6; between 80² and 90²; 85² = 7225 > 7056 → **84**.
- √5329: ends 9 → 3 or 7; between 70² and 80²; 75² = 5625 > 5329 → **73**.
- √9801: ends 1 → 1 or 9; just below 100² → **99**.
- √11025: ends 5 → **105**.
- √15625 → **125**.

## 4. Cube roots of perfect cubes

Cubes' last digits map uniquely: 1→1, 2→8, 3→7, 4→4, 5→5, 6→6, 7→3, 8→2, 9→9, 0→0.

Split off the last three digits; the remaining front part gives the tens digit by bracketing.

- ∛74088: ends 8 → units 2; front 74 between 4³ = 64 and 5³ = 125 → tens 4 → **42**.
- ∛2197: ends 7 → units 3; front 2 → 1 → **13**.

## 5. Approximating square roots

√N ≈ R + (N − R²)/(2R), with R the nearest root below.
- √50 ≈ 7 + 1/14 ≈ **7.07**.
- √65 ≈ 8 + 1/16 ≈ **8.06**.

## 6. Fractions to memorise

| Fraction | % | Fraction | % |
|---|---|---|---|
| 1/2 | 50 | 1/8 | 12.5 |
| 1/3 | 33.33 | 1/9 | 11.11 |
| 1/4 | 25 | 1/11 | 9.09 |
| 1/6 | 16.67 | 1/12 | 8.33 |
| 1/7 | 14.28 | 1/16 | 6.25 |

1/7 = 0.142857 repeating; 2/7, 3/7, ... are rotations of the same six digits (2/7 = 0.285714).

## 7. Checking answers: digit sums (casting out nines)

Reduce each number to its digit sum (repeatedly) mod 9; the operation on digit sums must match.
234 × 56: 234 → 9 ≡ 0, so the product's digit sum must also be 0 mod 9. 13,104 → 1 + 3 + 1 + 0 + 4 = 9 ✓.

Quick way to eliminate options without full multiplication.

## 8. Clock angle (preview)

Angle = |30H − 5.5M|; if > 180°, subtract from 360°. 3:40 → |90 − 220| = **130°**. (Full chapter: C09 Clocks.)

---

## 9. Practice questions (with solutions)

**Q1.** √11025 = ?
(a) 105 (b) 115 (c) 95 (d) 125
**Answer: (a).**

**Q2.** 98 × 97 = ?
(a) 9506 (b) 9606 (c) 9412 (d) 9706
**Answer: (a).**

**Q3.** √65 ≈ ?
(a) 7.8 (b) 8.06 (c) 8.5 (d) 7.5
**Answer: (b).**

**Q4.** 112 × 108 = ?
(a) 12,086 (b) 12,096 (c) 12,106 (d) 12,076
**Answer: (b).**

**Q5.** Angle between the hands at 6:00?
(a) 90° (b) 180° (c) 150° (d) 120°
**Answer: (b).**

**Q6.** Angle at 7:20?
(a) 90° (b) 95° (c) 100° (d) 105°
**Answer: (c).** |210 − 110|.

**Q7.** √9801 = ?
(a) 97 (b) 99 (c) 101 (d) 93
**Answer: (b).**

**Q8.** 203 × 198 = ?
(a) 40,114 (b) 40,214 (c) 40,194 (d) 39,994
**Answer: (c).**

**Q9.** 75² = ?
(a) 5525 (b) 5625 (c) 5725 (d) 4925
**Answer: (b).**

**Q10.** 995² = ?
(a) 990,025 (b) 990,250 (c) 999,025 (d) 985,025
**Answer: (a).**

**Q11.** 46 × 44 = ?
(a) 2024 (b) 2026 (c) 2016 (d) 2044
**Answer: (a).**

**Q12.** 25 × 48 = ?
(a) 1100 (b) 1200 (c) 1250 (d) 960
**Answer: (b).**

**Q13.** 87 × 11 = ?
(a) 857 (b) 957 (c) 967 (d) 947
**Answer: (b).**

**Q14.** √5329 = ?
(a) 77 (b) 73 (c) 67 (d) 63
**Answer: (b).**

**Q15.** ∛74088 = ?
(a) 38 (b) 42 (c) 48 (d) 44
**Answer: (b).**

**Q16.** ∛2197 = ?
(a) 11 (b) 13 (c) 17 (d) 23
**Answer: (b).**

**Q17.** 48² = ?
(a) 2304 (b) 2404 (c) 2204 (d) 2314
**Answer: (a).**

**Q18.** 104 × 107 = ?
(a) 11,128 (b) 11,028 (c) 11,218 (d) 11,138
**Answer: (a).**

**Q19.** 97 × 96 = ?
(a) 9312 (b) 9412 (c) 9212 (d) 9322
**Answer: (a).**

**Q20.** Which can't be a perfect square?
(a) 7056 (b) 4489 (c) 3528 (d) 6561
**Answer: (c).** Squares never end in 8.

**Q21.** 125 × 64 = ?
(a) 7000 (b) 8000 (c) 8200 (d) 6400
**Answer: (b).**

**Q22.** 2/7 as a decimal?
(a) 0.285714... (b) 0.287514... (c) 0.257142... (d) 0.28
**Answer: (a).**

**Q23.** Without multiplying fully, which could be 234 × 56?
(a) 13,024 (b) 13,104 (c) 13,114 (d) 13,204
**Answer: (b).** 234 has digit sum 9, so the product must be a multiple of 9. Only 13,104 has a digit sum divisible by 9.

**Q24.** √15625 = ?
(a) 115 (b) 125 (c) 135 (d) 105
**Answer: (b).**

**Q25.** 1/16 as a percentage?
(a) 6.25% (b) 6.5% (c) 16% (d) 0.625%
**Answer: (a).**
