# A03. Divisibility Rules

> **Why bother?** Divisibility rules let you check factors, simplify fractions and eliminate MCQ options in seconds, without dividing. Every rule comes from one idea: how powers of 10 behave modulo the divisor.

---

## 1. Rules for 2 to 10 (and why they work)

| Divisor | Rule | Why |
|---|---|---|
| **2** | Last digit even | 10 is divisible by 2, so only the units digit matters |
| **3** | Digit sum divisible by 3 | 10 ≡ 1 (mod 3), so a number ≡ its digit sum |
| **4** | Last **two** digits divisible by 4 | 100 is divisible by 4 |
| **5** | Last digit 0 or 5 | |
| **6** | Divisible by 2 **and** 3 | 2 and 3 are co-prime |
| **7** | Osculator method (section 3) | |
| **8** | Last **three** digits divisible by 8 | 1000 is divisible by 8 |
| **9** | Digit sum divisible by 9 | 10 ≡ 1 (mod 9) |
| **10** | Last digit 0 | |

## 2. Rules for 11 to 25

| Divisor | Rule |
|---|---|
| **11** | (Sum of digits in odd places) − (sum in even places), counting from the right, is 0 or a multiple of 11 (because 10 ≡ −1 mod 11) |
| **12** | Divisible by 3 and 4 |
| **13** | Osculator (+4) |
| **14** | 2 and 7 |
| **15** | 3 and 5 |
| **16** | Last four digits divisible by 16 |
| **17** | Osculator (−5) |
| **18** | 2 and 9 |
| **19** | Osculator (+2) |
| **20** | Last two digits divisible by 20 |
| **24** | 3 and 8 |
| **25** | Last two digits 00, 25, 50, 75 |

**11 example:** 9152: from the right, odd places 2, 1 → 3; even places 5, 9 → 14; difference 11 → **divisible**.

---

## 3. The osculator method (7, 13, 17, 19)

Repeatedly chop off the last digit L of the number, multiply it by the osculator m, and **add or subtract** it from the remaining part R. Keep going until the number is small.

| Divisor | Operation |
|---|---|
| **7** | R − 2L |
| **13** | R + 4L |
| **17** | R − 5L |
| **19** | R + 2L |

**Examples:**
- 1071 ÷ 7? 107 − 2 = 105 → 10 − 10 = 0 ✓ **divisible**.
- 3059 ÷ 7? 305 − 18 = 287 → 28 − 14 = 14 ✓ **divisible**.
- 2145 ÷ 13? 214 + 20 = 234 → 23 + 16 = 39 = 3 × 13 ✓ **divisible**.
- 6499 ÷ 13? 649 + 36 = 685 → 68 + 20 = 88 → 8 + 32 = 40 ✗ **not divisible**.
- 2431 ÷ 17? 243 − 5 = 238 → 23 − 40 = −17 ✓ **divisible** (17 × 143).
- 1729 ÷ 19? 172 + 18 = 190 → 19 + 0 = 19 ✓ **divisible** (19 × 91).

Why it works for 7: 10R + L ≡ 0 (mod 7) ⟺ R − 2L ≡ 0 (mod 7), since multiplying 10R + L by −2 gives −20R − 2L ≡ R − 2L (as −20 ≡ 1 mod 7).

---

## 4. Composite divisors: use co-prime factors only

d = a × b with **a and b co-prime** → divisible by d ⟺ divisible by both a and b.

Good splits: 6 = 2 × 3, 12 = 3 × 4, 14 = 2 × 7, 15 = 3 × 5, 18 = 2 × 9, 24 = 3 × 8, 36 = 4 × 9, 45 = 5 × 9, 72 = 8 × 9, 88 = 8 × 11.

> **Trap:** 4 and 6 are **not** co-prime. A number divisible by both need not be divisible by 24 (e.g. 12, 36).

---

## 5. Handy number facts

- **abcabc** (a 3-digit block repeated) = abc × 1001 = abc × 7 × 11 × 13: always divisible by **7, 11, 13**.
- **aaa** = a × 111 = a × 3 × 37: always divisible by **37**.
- **ab − ba** = 9(a − b): divisible by **9**. **ab + ba** = 11(a + b): divisible by **11**.
- Product of any **3 consecutive integers** is divisible by **6**; any **4** consecutive by **24**; any **n** consecutive by **n!**.
- **n³ − n** = (n − 1)n(n + 1) is always divisible by **6**.
- The square of any odd number leaves remainder **1** when divided by **8**.

---

## 6. Missing-digit problems

**Divisible by 3 or 9:** make the digit sum a multiple.
47*8 by 3: 4 + 7 + 8 = 19 → need * ≡ 2 (mod 3) → smallest **2**.

**Divisible by 11:** make the alternating sum difference 0 or ±11.
5x32 by 11: odd places (from the right) 2 + x, even places 3 + 5 = 8 → 2 + x − 8 = 0 → **x = 6** (5632 = 11 × 512).

**Divisible by 8:** last three digits must be a multiple of 8.

**Make a number divisible by d:** add (d − remainder) or subtract the remainder.
Smallest number to add to 1000 to make it divisible by 7: 1000 mod 7 = 6 → add **1** (1001).

---

## 7. Exam traps

1. Count places for the 11-rule from the **right**.
2. Combine rules only for **co-prime** factors.
3. Osculator signs: 7 and 17 subtract, 13 and 19 add.
4. abcabc is divisible by 7, 11, 13 (and 1001).

---

## 8. Practice questions (with solutions)

**Q1.** Which is divisible by 4?
(a) 1234 (b) 1354 (c) 1372 (d) 1358
**Answer: (c).** 72 ÷ 4 = 18.

**Q2.** Is 78,912 divisible by 9?
(a) Yes (b) No
**Answer: (a).** Digit sum 27.

**Q3.** Is 9152 divisible by 11?
(a) Yes (b) No
**Answer: (a).**

**Q4.** Is 3059 divisible by 7?
(a) Yes (b) No
**Answer: (a).**

**Q5.** A number divisible by both 4 and 6 is necessarily divisible by:
(a) 24 (b) 12 (c) 48 (d) 36
**Answer: (b).** LCM(4, 6) = 12. Not necessarily 24.

**Q6.** Is 6499 divisible by 13?
(a) Yes (b) No
**Answer: (b).**

**Q7.** Smallest digit * making 47*8 divisible by 3?
(a) 0 (b) 2 (c) 3 (d) 4
**Answer: (b).**

**Q8.** Smallest 3-digit number divisible by both 15 and 8?
(a) 120 (b) 240 (c) 360 (d) 480
**Answer: (a).**

**Q9.** Is 2,345,678 divisible by 8?
(a) Yes (b) No
**Answer: (b).** 678 ÷ 8 = 84.75.

**Q10.** Is 123,456 divisible by 6?
(a) Yes (b) No
**Answer: (a).** Even, digit sum 21.

**Q11.** Digit x such that 5x32 is divisible by 11?
(a) 4 (b) 5 (c) 6 (d) 7
**Answer: (c).**

**Q12.** Is 3672 divisible by 72?
(a) Yes (b) No
**Answer: (a).** 672 ÷ 8 = 84 and digit sum 18.

**Q13.** Least number to be added to 1000 to make it divisible by 7?
(a) 1 (b) 3 (c) 6 (d) 7
**Answer: (a).**

**Q14.** Any number of the form abcabc is always divisible by:
(a) 7 only (b) 11 only (c) 1001 (d) 111
**Answer: (c).**

**Q15.** A number of the form aaa (e.g. 777) is always divisible by:
(a) 7 (b) 11 (c) 37 (d) 13
**Answer: (c).**

**Q16.** For a two-digit number ab, ab − ba is always divisible by:
(a) 9 (b) 11 (c) 10 (d) 7
**Answer: (a).**

**Q17.** Is 7256 divisible by 12?
(a) Yes (b) No
**Answer: (b).** Divisible by 4 (56), but digit sum 20 isn't a multiple of 3.

**Q18.** Is 2431 divisible by 17?
(a) Yes (b) No
**Answer: (a).**

**Q19.** Is 1729 divisible by 19?
(a) Yes (b) No
**Answer: (a).**

**Q20.** The product of any three consecutive natural numbers is always divisible by:
(a) 4 (b) 6 (c) 9 (d) 12
**Answer: (b).**

**Q21.** n³ − n (n a natural number > 1) is always divisible by:
(a) 4 (b) 5 (c) 6 (d) 9
**Answer: (c).**

**Q22.** Which number is divisible by 99?
(a) 114345 (b) 135792 (c) 913464 (d) 3572404
**Answer: (a).** 99 = 9 × 11. 114345: digit sum 18 ✓; 11-test from the right: odd places 5 + 3 + 1 = 9, even places 4 + 4 + 1 = 9 → 0 ✓.

**Q23.** If 7 1 x 2 is divisible by 9 (four digits 7, 1, x, 2), x = ?
(a) 5 (b) 6 (c) 7 (d) 8
**Answer: (d).** 7 + 1 + 2 = 10 → x = 8 makes 18.

**Q24.** The largest number that always divides the product of any 4 consecutive integers?
(a) 12 (b) 24 (c) 48 (d) 120
**Answer: (b).**

**Q25.** Which of these numbers is divisible by 25?
(a) 1255 (b) 3450 (c) 2375 (d) 7620
**Answer: (c).** Ends in 75.

**Q26.** When the square of any odd number is divided by 8, the remainder is:
(a) 0 (b) 1 (c) 3 (d) 7
**Answer: (b).**
