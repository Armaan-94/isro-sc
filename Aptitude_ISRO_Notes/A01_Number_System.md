# A01. Number System

> **Why this chapter first.** Factors, remainders and unit digits are the raw material of half the quant section (HCF/LCM, divisibility, simplification). Learn three tools here: **prime factorisation**, **remainder arithmetic**, and **cyclicity of unit digits**. Every question in this chapter is one of those tools in disguise.

---

## 1. Types of numbers

| Type | Members | Notes |
|---|---|---|
| Natural (N) | 1, 2, 3, ... | Counting numbers |
| Whole (W) | 0, 1, 2, ... | Naturals plus 0 |
| Integers (Z) | ..., −2, −1, 0, 1, 2, ... | |
| Rational (Q) | p/q with q ≠ 0 | Decimals that **terminate** or **repeat** (0.75, 0.333...) |
| Irrational | Non-terminating, non-repeating | √2, √3, π, e |
| Real | Rational + irrational | |

- **Prime:** exactly two factors (1 and itself): 2, 3, 5, 7, 11, 13, ...
  - **2 is the only even prime.**
  - **1 is neither prime nor composite.**
  - There are **25 primes below 100** and **15 below 50**.
- **Composite:** more than two factors (4, 6, 8, 9, ...).
- **Co-prime:** two numbers with HCF 1 (they need not be prime themselves: 8 and 15).
- **Twin primes:** primes differing by 2 (3 and 5, 11 and 13).

Quick primality test for N: check divisibility by primes up to √N. (Is 221 prime? √221 ≈ 14.9: 221 = 13 × 17, so no.)

---

## 2. Prime factorisation: the master key

Write N as a product of prime powers:

```
N = p₁^a₁ × p₂^a₂ × ... × pₖ^aₖ
```

Example: 360 = 2³ × 3² × 5.

### 2.1 Number of factors

```
Number of factors = (a₁ + 1)(a₂ + 1)...(aₖ + 1)
```

Why? A factor picks the power of each prime independently: p₁ can appear 0, 1, ..., a₁ times (a₁ + 1 choices), and so on.

360: (3 + 1)(2 + 1)(1 + 1) = **24** factors.

### 2.2 Sum of factors

```
Sum = [(p₁^(a₁+1) − 1)/(p₁ − 1)] × [(p₂^(a₂+1) − 1)/(p₂ − 1)] × ...
```

360: (2⁴ − 1)/1 × (3³ − 1)/2 × (5² − 1)/4 = 15 × 13 × 6 = **1170**.

(Same as (1 + 2 + 4 + 8)(1 + 3 + 9)(1 + 5).)

### 2.3 Odd and even factors

- **Odd factors:** ignore the power of 2 completely, then count.
- **Even factors = total − odd.**

360 = 2³ × 3² × 5: odd part 3² × 5 → (2 + 1)(1 + 1) = **6** odd factors; even = 24 − 6 = **18**.

### 2.4 Factors divisible by a given number Q

If Q divides N, every such factor is Q × (a factor of N/Q). So **count = number of factors of N/Q**.

Factors of 360 divisible by 12: 360/12 = 30 = 2 × 3 × 5 → **8**.

### 2.5 Perfect-square and perfect-cube factors

- A factor is a **perfect square** iff every exponent in it is **even**. Count = Π (⌊aᵢ/2⌋ + 1).
- A **perfect cube** iff every exponent is a multiple of 3. Count = Π (⌊aᵢ/3⌋ + 1).

N = 2⁴ × 3⁶: square factors: exponents of 2 in {0, 2, 4} (3 ways) × exponents of 3 in {0, 2, 4, 6} (4 ways) = **12**. Cube factors: {0, 3} × {0, 3, 6} = **6**.

### 2.6 A number with an odd number of factors is a perfect square

Factors pair up as (d, N/d). The only unpaired factor is √N, which exists only when N is a perfect square. So **odd factor count ⟺ perfect square**.

### 2.7 Ways to write N as a product of two factors

If N is not a perfect square: (number of factors)/2. If it is: (number of factors + 1)/2.
36 has 9 factors → (9 + 1)/2 = **5** ways (1×36, 2×18, 3×12, 4×9, 6×6).

---

## 3. Remainders

### 3.1 The rules

```
(a + b) mod n = [(a mod n) + (b mod n)] mod n
(a × b) mod n = [(a mod n) × (b mod n)] mod n
(aᵇ)    mod n = [(a mod n)ᵇ] mod n
```

**Always reduce each number to its remainder first.** Never multiply out huge numbers.

### 3.2 Negative remainders

If a remainder is close to n, write it as a negative number. 49 mod 50 = −1, so 49²⁵ mod 50 = (−1)²⁵ = −1 → **49**.

### 3.3 Find a cycle

Remainders of powers repeat. 2ⁿ mod 5: 2, 4, 3, 1, 2, 4, 3, 1, ... (cycle length 4).
2³¹ mod 5: 31 = 4·7 + 3 → same as 2³ → **3**.

### 3.4 Useful facts

- If N divided by d leaves remainder r, and k divides d, then N mod k = r mod k. (N = 56q + 29 → N mod 8 = 29 mod 8 = **5**.)
- (a − b) divides (aⁿ − bⁿ) for all n. (a + b) divides (aⁿ + bⁿ) when n is odd, and (aⁿ − bⁿ) when n is even.

---

## 4. Successive division

"N divided by 5 gives quotient Q₁, remainder 3; Q₁ divided by 4 gives quotient Q₂, remainder 2; Q₂ divided by 3 gives quotient 1, remainder 0." Find N.

**Work backwards** from the last quotient:
- Q₂ = 3 × 1 + 0 = 3
- Q₁ = 4 × 3 + 2 = 14
- N = 5 × 14 + 3 = **73**

---

## 5. Unit (last) digit

### 5.1 Cyclicity

| Last digit of base | Pattern of last digits of powers | Cycle length |
|---|---|---|
| 0, 1, 5, 6 | always the same digit | 1 |
| 4 | 4, 6, 4, 6 | 2 |
| 9 | 9, 1, 9, 1 | 2 |
| 2 | 2, 4, 8, 6 | 4 |
| 3 | 3, 9, 7, 1 | 4 |
| 7 | 7, 9, 3, 1 | 4 |
| 8 | 8, 4, 2, 6 | 4 |

### 5.2 Method for aᵇ

1. Take the last digit of a.
2. Find b mod 4 (use 4 if the remainder is 0). (For 4 and 9, just check odd/even.)
3. Read the digit at that position in the cycle.

7⁹⁴: 94 mod 4 = 2 → 2nd in (7, 9, 3, 1) → **9**.
2¹⁰⁰: 100 mod 4 = 0 → 4th in (2, 4, 8, 6) → **6**.

### 5.3 Sums and products

Find each term's unit digit first, then combine.
7³⁵ + 3⁴⁷: 35 mod 4 = 3 → 3; 47 mod 4 = 3 → 7; 3 + 7 = 10 → **0**.

---

## 6. Factorials

### 6.1 Trailing zeros of n!

Each trailing zero needs a factor 10 = 2 × 5; 5s are scarcer, so count 5s:

```
zeros = ⌊n/5⌋ + ⌊n/25⌋ + ⌊n/125⌋ + ...
```

100!: 20 + 4 = **24**. 125!: 25 + 5 + 1 = **31**.

### 6.2 Highest power of a prime p in n!

Same idea (Legendre's formula): ⌊n/p⌋ + ⌊n/p²⌋ + ...
Power of 3 in 50!: 16 + 5 + 1 = **22**.

### 6.3 Unit digit of a sum of factorials

From 5! onward every factorial ends in 0. So 1! + 2! + ... + n! (n ≥ 4) ends in the last digit of 1 + 2 + 6 + 24 = 33 → **3**.

---

## 7. Useful sums

- 1 + 2 + ... + n = n(n + 1)/2
- 1² + 2² + ... + n² = n(n + 1)(2n + 1)/6
- 1³ + 2³ + ... + n³ = [n(n + 1)/2]²
- Sum of the first n odd numbers = n²; first n even numbers = n(n + 1).

---

## 8. Divisible-by-d bounds

- **Largest k-digit number divisible by d:** take the largest k-digit number, subtract its remainder.
  Largest 4-digit divisible by 88: 9999 = 88 × 113 + 55 → 9999 − 55 = **9944**.
- **Smallest k-digit number divisible by d:** take the smallest k-digit number, add (d − remainder).
  Smallest 5-digit divisible by 41: 10000 = 41 × 243 + 37 → 10000 + 4 = **10004**.
- **Count of multiples of d from 1 to N:** ⌊N/d⌋. Multiples of 7 between 100 and 500: ⌊500/7⌋ − ⌊99/7⌋ = 71 − 14 = **57**.

---

## 9. Exam traps

1. 2 is the only even prime; 1 is neither prime nor composite.
2. Odd number of factors ⟺ perfect square.
3. Even factors = total − odd.
4. Reduce before multiplying when finding remainders.
5. Cyclicity exponent remainder 0 means the **last** element of the cycle.
6. Trailing zeros: count 5s (including 25, 125, ...).

---

## 10. Practice questions (with solutions)

**Q1.** Number of factors of 72?
(a) 10 (b) 12 (c) 14 (d) 16
**Answer: (b).** 72 = 2³ × 3² → 4 × 3 = 12.

**Q2.** Unit digit of 3⁴⁵?
(a) 1 (b) 3 (c) 7 (d) 9
**Answer: (b).** 45 mod 4 = 1 → 3.

**Q3.** Odd factors of 240?
(a) 2 (b) 4 (c) 6 (d) 8
**Answer: (b).** 240 = 2⁴ × 3 × 5 → odd part 3 × 5 → 4.

**Q4.** Even factors of 240?
(a) 16 (b) 18 (c) 20 (d) 12
**Answer: (a).** Total (5)(2)(2) = 20; 20 − 4 = 16.

**Q5.** Factors of 900 divisible by 15?
(a) 6 (b) 9 (c) 12 (d) 15
**Answer: (c).** 900/15 = 60 = 2² × 3 × 5 → 3 × 2 × 2 = 12.

**Q6.** Unit digit of 17²³ × 13¹⁵?
(a) 1 (b) 3 (c) 7 (d) 9
**Answer: (a).** 7²³: 23 mod 4 = 3 → 3. 3¹⁵: 15 mod 4 = 3 → 7. 3 × 7 = 21 → 1.

**Q7.** A number divided successively by 4 and 5 leaves remainders 2 and 3, final quotient 6. The number?
(a) 126 (b) 134 (c) 146 (d) 152
**Answer: (b).** Q₁ = 5 × 6 + 3 = 33; N = 4 × 33 + 2 = 134.

**Q8.** Perfect-square factors of 2⁶ × 3⁴?
(a) 8 (b) 10 (c) 12 (d) 15
**Answer: (c).** {0, 2, 4, 6} × {0, 2, 4} = 4 × 3.

**Q9.** A number has exactly 9 factors. It must be:
(a) prime (b) a perfect cube (c) a perfect square (d) even
**Answer: (c).**

**Q10.** Sum of all factors of 72?
(a) 168 (b) 195 (c) 180 (d) 210
**Answer: (b).** (1 + 2 + 4 + 8)(1 + 3 + 9) = 15 × 13.

**Q11.** Number of factors of 10⁴?
(a) 16 (b) 20 (c) 25 (d) 30
**Answer: (c).** 2⁴ × 5⁴ → 5 × 5.

**Q12.** Unit digit of 2¹⁰⁰?
(a) 2 (b) 4 (c) 6 (d) 8
**Answer: (c).**

**Q13.** Unit digit of 4³⁷?
(a) 4 (b) 6 (c) 2 (d) 8
**Answer: (a).** Odd power of 4 ends in 4.

**Q14.** Unit digit of 9¹⁰⁰?
(a) 9 (b) 1 (c) 3 (d) 7
**Answer: (b).**

**Q15.** Unit digit of 7³⁵ + 3⁴⁷?
(a) 0 (b) 4 (c) 6 (d) 8
**Answer: (a).**

**Q16.** Remainder when 2³¹ is divided by 5?
(a) 1 (b) 2 (c) 3 (d) 4
**Answer: (c).**

**Q17.** Remainder when 49²⁵ is divided by 50?
(a) 1 (b) 49 (c) 25 (d) 0
**Answer: (b).** (−1)²⁵ = −1 ≡ 49.

**Q18.** Remainder when 17 × 23 × 31 is divided by 8?
(a) 1 (b) 3 (c) 5 (d) 7
**Answer: (a).** 1 × 7 × 7 = 49 → 49 mod 8 = 1.

**Q19.** Remainder when 7¹⁰⁰ is divided by 8?
(a) 1 (b) 7 (c) 0 (d) 3
**Answer: (a).** 7 ≡ −1, even power → 1.

**Q20.** A number divided by 56 leaves remainder 29. Remainder when it is divided by 8?
(a) 1 (b) 3 (c) 5 (d) 7
**Answer: (c).**

**Q21.** Number of trailing zeros in 100!?
(a) 20 (b) 22 (c) 24 (d) 25
**Answer: (c).**

**Q22.** Number of trailing zeros in 125!?
(a) 25 (b) 30 (c) 31 (d) 32
**Answer: (c).** 25 + 5 + 1.

**Q23.** Highest power of 3 dividing 50!?
(a) 16 (b) 21 (c) 22 (d) 24
**Answer: (c).** 16 + 5 + 1.

**Q24.** Largest 4-digit number divisible by 88?
(a) 9944 (b) 9988 (c) 9900 (d) 9856
**Answer: (a).**

**Q25.** Smallest 5-digit number divisible by 41?
(a) 10004 (b) 10045 (c) 10041 (d) 10025
**Answer: (a).**

**Q26.** How many numbers from 100 to 500 are divisible by 7?
(a) 56 (b) 57 (c) 58 (d) 55
**Answer: (b).** 71 − 14.

**Q27.** In how many ways can 36 be written as a product of two natural numbers (order doesn't matter)?
(a) 4 (b) 5 (c) 9 (d) 6
**Answer: (b).**

**Q28.** Unit digit of 1! + 2! + 3! + ... + 50!?
(a) 0 (b) 3 (c) 4 (d) 9
**Answer: (b).**

**Q29.** Perfect-cube factors of 2⁶ × 3⁴?
(a) 4 (b) 6 (c) 8 (d) 9
**Answer: (b).** {0, 3, 6} × {0, 3} = 3 × 2.

**Q30.** Factors of 2³ × 3² that are multiples of 6?
(a) 4 (b) 6 (c) 8 (d) 12
**Answer: (b).** N/6 = 2² × 3 → 3 × 2 = 6.

**Q31.** Which is irrational?
(a) 0.25 (b) 0.333... (c) √16 (d) √3
**Answer: (d).**

**Q32.** Sum of the first 50 natural numbers?
(a) 1250 (b) 1275 (c) 1300 (d) 2550
**Answer: (b).** 50 × 51 / 2.

**Q33.** Sum of squares 1² + 2² + ... + 10²?
(a) 285 (b) 385 (c) 400 (d) 355
**Answer: (b).** 10 × 11 × 21 / 6.

**Q34.** Sum of the first 20 odd numbers?
(a) 200 (b) 400 (c) 420 (d) 380
**Answer: (b).** 20².

**Q35.** Smallest number that leaves remainder 5 when divided by 6, 15 and 20?
(a) 55 (b) 60 (c) 65 (d) 125
**Answer: (c).** LCM 60 + 5.
