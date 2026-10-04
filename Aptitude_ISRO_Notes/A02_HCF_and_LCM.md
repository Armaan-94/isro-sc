# A02. HCF and LCM

> **The intuition.** HCF is the biggest "ruler" that measures all your lengths exactly. LCM is the first moment when several repeating events line up again. Once you see word problems as "measuring" (HCF) or "lining up" (LCM), choosing the right tool is automatic.

---

## 1. Definitions

- **HCF (GCD):** the largest number dividing all the given numbers exactly.
- **LCM:** the smallest number divisible by all the given numbers.
- **Co-prime:** HCF = 1.

**When to use which in word problems:**
| Clue in the question | Tool |
|---|---|
| "greatest/largest number that divides", "largest tile/measure/container", "maximum number of equal groups" | **HCF** |
| "least/smallest number divisible by", "when will they meet/ring/coincide again" | **LCM** |

---

## 2. Finding the HCF

**Prime factorisation:** take the **common** primes with the **lowest** powers.
24 = 2³ × 3, 36 = 2² × 3² → HCF = 2² × 3 = **12**.

**Euclid's division method** (fast for big numbers): divide the larger by the smaller, then divide the divisor by the remainder, and repeat. The last non-zero divisor is the HCF.

```
1071 = 462 × 2 + 147
 462 = 147 × 3 + 21
 147 =  21 × 7 + 0     → HCF = 21
```

Why it works: any common divisor of a and b also divides a − kb (the remainder).

---

## 3. Finding the LCM

**Prime factorisation:** take **all** primes with the **highest** powers.
12 = 2² × 3, 18 = 2 × 3² → LCM = 2² × 3² = **36**.

**Division (ladder) method:** divide all numbers by common primes repeatedly; multiply all divisors and leftovers.

---

## 4. The HCF × LCM property (two numbers only)

```
HCF(a, b) × LCM(a, b) = a × b
```

Example: product 4032, HCF 12 → LCM = 4032/12 = **336**.

> **Trap:** this holds for **exactly two** numbers. For three or more it's false in general.

**Structure of two numbers:** if HCF = h, the numbers are **h·x** and **h·y** with x, y **co-prime**, and LCM = h·x·y.

Example: HCF 23, the "other factors" of the LCM are 13 and 14 → numbers 23 × 13 = 299 and 23 × 14 = **322**.

---

## 5. HCF and LCM of fractions

```
HCF of fractions = HCF(numerators) / LCM(denominators)
LCM of fractions = LCM(numerators) / HCF(denominators)
```

(Each uses the **opposite** operation on the denominators.)

2/3, 8/9, 10/27: HCF = HCF(2, 8, 10)/LCM(3, 9, 27) = **2/27**; LCM = LCM(2, 8, 10)/HCF(3, 9, 27) = **40/3**.

Reduce each fraction to lowest terms first.

---

## 6. HCF and LCM of decimals

Make the decimal places equal, drop the point, compute with integers, put the point back.

HCF(1.2, 0.48) → 120 and 48 → HCF 24 → **0.24**.
LCM(0.6, 9.6, 0.36) → 60, 960, 36 → LCM 2880 → **28.80**.

---

## 7. Remainder patterns (very high yield)

| Pattern | Answer |
|---|---|
| Greatest number dividing x, y, z leaving the **same remainder r** (r given) | **HCF(x − r, y − r, z − r)** |
| Greatest number dividing x, y, z leaving the **same remainder** (r not given) | **HCF(y − x, z − y, z − x)** (differences) |
| Greatest number dividing x, y leaving remainders r₁, r₂ | **HCF(x − r₁, y − r₂)** |
| Smallest number which divided by a, b, c leaves the **same remainder r** | **LCM(a, b, c) + r** |
| Smallest number which divided by a, b, c leaves remainders that are each **k less than the divisor** | **LCM(a, b, c) − k** |
| Largest k-digit number divisible by a, b, c | Largest multiple of LCM with k digits |

Recognising the "k less" pattern: divisors 5, 6, 7 with remainders 3, 4, 5. Differences 5 − 3 = 6 − 4 = 7 − 5 = 2 → answer LCM − 2 = 210 − 2 = **208**.

---

## 8. Word-problem patterns

- **Bells/lights/runners coinciding:** LCM of the intervals.
  Bells toll every 2, 4, 6, 8, 10, 12 seconds. LCM = 120 s. In 30 minutes (1800 s) they toll together 1800/120 = 15 times **plus once at the start** = **16** times.
- **Largest square tile for a floor:** HCF of the dimensions; number of tiles = area / tile area.
- **Cutting rods into equal largest pieces:** HCF.
- **Ratio + HCF:** numbers in ratio a : b with HCF h are h·a and h·b (if a, b co-prime); LCM = h·a·b.

---

## 9. Exam traps

1. HCF × LCM = product: **only for two numbers**.
2. Fractions: HCF uses LCM of denominators and vice versa.
3. "Same remainder (unknown)" → HCF of **differences**.
4. "Remainder = divisor − k" → LCM − k, not LCM + r.
5. Counting coincidences: include the starting moment if asked "how many times together".
6. LCM is always a multiple of HCF.

---

## 10. Practice questions (with solutions)

**Q1.** HCF of 24 and 36?
(a) 6 (b) 8 (c) 12 (d) 18
**Answer: (c).**

**Q2.** LCM of 12 and 18?
(a) 24 (b) 36 (c) 48 (d) 72
**Answer: (b).**

**Q3.** Product of two numbers is 4032, HCF is 12. LCM?
(a) 288 (b) 336 (c) 360 (d) 432
**Answer: (b).**

**Q4.** HCF of 3/4, 9/10, 15/16?
(a) 3/80 (b) 1/80 (c) 3/160 (d) 3/40
**Answer: (a).** HCF(3, 9, 15) = 3; LCM(4, 10, 16) = 80.

**Q5.** LCM of 2/3, 8/9, 10/27?
(a) 40/3 (b) 40/27 (c) 80/3 (d) 2/27
**Answer: (a).**

**Q6.** Greatest number dividing 285 and 1249 leaving remainders 9 and 7?
(a) 69 (b) 92 (c) 138 (d) 276
**Answer: (c).** HCF(276, 1242): 1242 = 276 × 4 + 138; 276 = 138 × 2 → 138.

**Q7.** Smallest number leaving remainder 5 when divided by 6, 15, 18?
(a) 90 (b) 91 (c) 95 (d) 85
**Answer: (c).**

**Q8.** Largest number dividing 70 and 125 leaving remainders 5 and 8?
(a) 13 (b) 15 (c) 65 (d) 117
**Answer: (a).** HCF(65, 117) = 13.

**Q9.** Greatest number dividing 62, 132 and 237 leaving remainder 2 each time?
(a) 5 (b) 10 (c) 15 (d) 35
**Answer: (a).** HCF(60, 130, 235) = 5.

**Q10.** HCF(1.2, 0.48)?
(a) 0.024 (b) 0.24 (c) 2.4 (d) 0.12
**Answer: (b).**

**Q11.** LCM(0.6, 9.6, 0.36)?
(a) 2.88 (b) 28.8 (c) 288 (d) 0.288
**Answer: (b).**

**Q12.** Bells toll at intervals of 2, 4, 6, 8, 10, 12 seconds, starting together. How many times do they toll together in 30 minutes?
(a) 15 (b) 16 (c) 4 (d) 10
**Answer: (b).**

**Q13.** Two numbers are in the ratio 3 : 4 and their HCF is 4. LCM?
(a) 12 (b) 16 (c) 48 (d) 24
**Answer: (c).** Numbers 12 and 16.

**Q14.** LCM of two numbers is 48 and they are in the ratio 2 : 3. Their sum?
(a) 32 (b) 40 (c) 48 (d) 64
**Answer: (b).** LCM = 6x = 48 → x = 8 → 16 + 24.

**Q15.** Greatest number that divides 43, 91 and 183 leaving the same remainder in each case?
(a) 4 (b) 7 (c) 9 (d) 13
**Answer: (a).** HCF(48, 92, 140) = 4.

**Q16.** Least number which divided by 12, 15, 20 and 54 leaves remainder 8 each time?
(a) 504 (b) 536 (c) 544 (d) 548
**Answer: (d).** LCM = 540.

**Q17.** Least number which divided by 5, 6, 7 leaves remainders 3, 4, 5 respectively?
(a) 208 (b) 212 (c) 205 (d) 213
**Answer: (a).**

**Q18.** A room is 15.17 m by 9.02 m. Least number of equal square tiles to cover it exactly?
(a) 814 (b) 820 (c) 840 (d) 804
**Answer: (a).** HCF(1517, 902) = 41 cm. Tiles = 37 × 22.

**Q19.** Largest 4-digit number divisible by 12, 15, 18 and 27?
(a) 9720 (b) 9960 (c) 9690 (d) 9930
**Answer: (a).** LCM = 540; 540 × 18 = 9720.

**Q20.** Smallest 4-digit number divisible by 12, 15, 20 and 35?
(a) 1200 (b) 1260 (c) 1140 (d) 1680
**Answer: (b).** LCM = 420; 420 × 3.

**Q21.** HCF of two numbers is 23 and the other two factors of their LCM are 13 and 14. The larger number?
(a) 276 (b) 299 (c) 322 (d) 345
**Answer: (c).**

**Q22.** Sum of two numbers is 528 and their HCF is 33. Number of such pairs?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (b).** a + b = 16 with a, b co-prime: (1, 15), (3, 13), (5, 11), (7, 9).

**Q23.** HCF of 2⁴ × 3² × 5 and 2³ × 3³ × 7?
(a) 36 (b) 72 (c) 144 (d) 216
**Answer: (b).** 2³ × 3².

**Q24.** HCF(1071, 462)?
(a) 7 (b) 14 (c) 21 (d) 42
**Answer: (c).**

**Q25.** Three runners complete a lap in 12, 18 and 30 minutes, starting together. After how long are they together at the start again?
(a) 1 h 30 min (b) 2 h (c) 3 h (d) 6 h
**Answer: (c).** LCM = 180 min.

**Q26.** HCF and LCM of two numbers are 12 and 144. If one is 48, the other?
(a) 24 (b) 36 (c) 72 (d) 96
**Answer: (b).** 12 × 144 / 48.

**Q27.** Which can NOT be the HCF and LCM of two numbers?
(a) 8 and 48 (b) 12 and 60 (c) 15 and 40 (d) 6 and 36
**Answer: (c).** LCM must be a multiple of HCF; 40 is not a multiple of 15.

**Q28.** Three cans hold 403, 434 and 465 litres. Largest measure that fills each an exact number of times?
(a) 31 L (b) 41 L (c) 51 L (d) 62 L
**Answer: (a).** HCF = 31 (403 = 31 × 13, 434 = 31 × 14, 465 = 31 × 15).

**Q29.** Least number of soldiers that can be arranged in rows of 15, 20, 25 and also form a perfect square?
(a) 300 (b) 600 (c) 900 (d) 1200
**Answer: (c).** LCM = 300 = 2² × 3 × 5²; to be a square, multiply by 3 → 900.

**Q30.** Product of two co-prime numbers is 117. Their LCM?
(a) 1 (b) 117 (c) 39 (d) 13
**Answer: (b).** Co-prime → HCF 1 → LCM = product.
