# A06. Average

> **One equation solves every average problem: Sum = Average × Count.** Whenever a question talks about people joining, leaving, being replaced, or marks being corrected, convert averages to **totals**, do the arithmetic on totals, and convert back.

---

## 1. Basics

```
Average = Sum / Count          Sum = Average × Count
```

### Averages of special sequences

- Any **arithmetic progression** (equally spaced numbers): average = **(first + last)/2**.
- First n natural numbers: (n + 1)/2. 1 to 99: **50**.
- First n even numbers (2, 4, ..., 2n): **n + 1**. First 20 even numbers: 21.
- First n odd numbers (1, 3, ..., 2n − 1): **n**. First 15 odd numbers: 15.
- Consecutive numbers: the average is the middle one. 3 consecutive numbers with average 21 → 20, 21, 22.
- Squares of the first n naturals: (n + 1)(2n + 1)/6. First 10: 38.5.

### Shifting and scaling

- Add k to every value → average increases by k.
- Multiply every value by k → average is multiplied by k.

---

## 2. Joining, leaving, replacing

| Event | New average |
|---|---|
| Someone with value x **joins** | (old sum + x)/(n + 1) |
| Someone with value x **leaves** | (old sum − x)/(n − 1) |
| x is **replaced** by y | old average + (y − x)/n |

**Replacement shortcut:** if replacing someone raises the average of n people by d, the newcomer's value = **old value + n·d**.
The average age of 10 people rises by 2 when a 35-year-old is replaced → newcomer = 35 + 20 = **55**.

**Joining shortcut:** if a newcomer raises the average of n people by d, his value = **old average + (n + 1)·d**.
30 students, average age 15; with the teacher, the average rises by 1 → teacher = 15 + 31 × 1 = **46**.

If the newcomer's value equals the current average, the average doesn't change.

---

## 3. Weighted average

```
Combined average = (n₁a₁ + n₂a₂ + ...) / (n₁ + n₂ + ...)
```

Class A: 25 students averaging 60; class B: 15 averaging 80 → (1500 + 1200)/40 = **67.5** (not 70).

> **Trap:** the combined average is the simple average of the group averages **only when the groups are the same size**.

The combined average always lies **between** the group averages, closer to the bigger group. (Alligation, chapter A11, is this same idea drawn as a cross.)

---

## 4. Overlapping-group problems

"Average of 11 results is 50; first 6 average 49; last 6 average 52. Find the 6th."
The 6th result is counted in both groups: 6th = (6 × 49 + 6 × 52) − 11 × 50 = 294 + 312 − 550 = **56**.

"Average of a, b, c is 30; of b, c, d is 35; a = 25. Find d."
(b + c + d) − (a + b + c) = 105 − 90 → d − a = 15 → d = **40**.

---

## 5. Batting-average problems

After n innings the average is A. In the (n + 1)th innings he scores S and the average becomes A + d:

```
nA + S = (n + 1)(A + d)    →    S = A + (n + 1)d
```

After 16 innings average 36; scores 104 in the 17th: 104 = 36 + 17d → **d = 4**.

---

## 6. Average speed (preview of A10)

Same **distance** at speeds x and y → average speed = **2xy/(x + y)** (harmonic mean). 60 and 40 km/h → **48**, not 50.
Same **time** at speeds x and y → simple average (x + y)/2.

---

## 7. Correction problems

Wrong entries: change in average = (correct total − wrong total)/n.

---

## 8. Exam traps

1. Work with **totals**, not averages.
2. Combined average needs **weights**.
3. Overlapping groups: the shared item is counted twice.
4. Average speed over equal distances is the harmonic mean.
5. "Youngest member's birth" problems: the youngest wasn't in the family then; remove them before going back in time.

---

## 9. Practice questions (with solutions)

**Q1.** Average of the first 10 natural numbers?
(a) 5 (b) 5.5 (c) 6 (d) 10
**Answer: (b).**

**Q2.** Average of 5 numbers is 20. Excluding one, the average is 18. The excluded number?
(a) 24 (b) 26 (c) 28 (d) 30
**Answer: (c).** 100 − 72.

**Q3.** Average weight of 8 people rises by 1.5 kg when a 65 kg person is replaced. Newcomer's weight?
(a) 70 (b) 74 (c) 76 (d) 77
**Answer: (d).** 65 + 8 × 1.5.

**Q4.** Section A: 25 students, average 60. Section B: 15 students, average 80. Combined average?
(a) 70 (b) 67.5 (c) 66 (d) 68.5
**Answer: (b).**

**Q5.** A cricketer averages 40 after 10 innings and scores 84 in the 11th. New average?
(a) 42 (b) 43 (c) 44 (d) 45
**Answer: (c).** 484/11.

**Q6.** A family of 5 has an average age of 24. The youngest is 8. Average age of the family when the youngest was born?
(a) 18 (b) 19 (c) 20 (d) 21
**Answer: (c).** Others now total 112; 8 years ago 112 − 32 = 80 over 4.

**Q7.** Average after 16 innings is 36; he scores 104 in the 17th. Increase in average?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (c).**

**Q8.** Average of the first 20 even numbers?
(a) 20 (b) 21 (c) 22 (d) 40
**Answer: (b).**

**Q9.** Average of the first 15 odd numbers?
(a) 14 (b) 15 (c) 16 (d) 30
**Answer: (b).**

**Q10.** Average of three consecutive numbers is 21. The largest?
(a) 21 (b) 22 (c) 23 (d) 24
**Answer: (b).**

**Q11.** Average of five consecutive odd numbers is 27. The largest?
(a) 29 (b) 31 (c) 33 (d) 35
**Answer: (b).** 23, 25, 27, 29, 31.

**Q12.** Average of 11 results is 50. First 6 average 49, last 6 average 52. The 6th result?
(a) 50 (b) 52 (c) 56 (d) 60
**Answer: (c).**

**Q13.** 30 students average 15 years. Including the teacher, the average rises by 1. Teacher's age?
(a) 31 (b) 45 (c) 46 (d) 47
**Answer: (c).**

**Q14.** Average weight of 10 men rises by 2.5 kg when a 50 kg man is replaced. New man's weight?
(a) 70 (b) 72.5 (c) 75 (d) 80
**Answer: (c).**

**Q15.** Average of 7 numbers is 30. Each is multiplied by 3. New average?
(a) 30 (b) 33 (c) 60 (d) 90
**Answer: (d).**

**Q16.** Average of a, b, c is 30; of b, c, d is 35; a = 25. d = ?
(a) 35 (b) 40 (c) 45 (d) 30
**Answer: (b).**

**Q17.** A batsman scores 65 in his 12th innings, raising his average by 2. New average?
(a) 41 (b) 43 (c) 45 (d) 47
**Answer: (b).** 65 = A + 12 × 2 → old A = 41 → new 43.

**Q18.** Average temperature Mon to Wed is 37°, Tue to Thu is 34°. Thursday is 4/5 of Monday. Thursday's temperature?
(a) 34° (b) 35.5° (c) 36° (d) 36.5°
**Answer: (c).** Mon − Thu = 111 − 102 = 9; M − 0.8M = 9 → M = 45 → Thu = 36.

**Q19.** Average of 5 numbers is 18. First two average 15, last two average 20. The middle number?
(a) 18 (b) 20 (c) 22 (d) 16
**Answer: (b).** 90 − 30 − 40.

**Q20.** Class of 30, average 70. Three marks were wrongly recorded as 60, 70, 80 instead of 90 each. Correct average?
(a) 70 (b) 71 (c) 72 (d) 73
**Answer: (c).** Error = 270 − 210 = 60 → +2.

**Q21.** 20 boys average 50 kg and 30 girls average 45 kg. Average of the class?
(a) 47 (b) 47.5 (c) 48 (d) 46
**Answer: (a).** (1000 + 1350)/50.

**Q22.** A car covers equal distances at 60 km/h and 40 km/h. Average speed?
(a) 50 (b) 48 (c) 45 (d) 52
**Answer: (b).**

**Q23.** Average of all integers from 1 to 99?
(a) 49 (b) 49.5 (c) 50 (d) 50.5
**Answer: (c).**

**Q24.** Average of the squares of the first 10 natural numbers?
(a) 30.5 (b) 38.5 (c) 35 (d) 55
**Answer: (b).** 385/10.

**Q25.** Average salary of all workers is ₹8000. 7 technicians average ₹12,000 and the rest average ₹6000. Total workers?
(a) 14 (b) 21 (c) 28 (d) 35
**Answer: (b).** 84000 + 6000(n − 7) = 8000n → n = 21.

**Q26.** Average of 50 numbers is 38. If 45 and 55 are removed, the new average?
(a) 36.5 (b) 37 (c) 37.5 (d) 37.52
**Answer: (c).** 1800/48.

**Q27.** Average of n numbers is A. If each is increased by 5, the new average is:
(a) A (b) A + 5 (c) 5A (d) A + 5n
**Answer: (b).**

**Q28.** Average age of husband and wife at marriage was 25. After 5 years, with one child, the family average is 21. Child's age?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (b).** Couple now totals 50 + 10 = 60. Family total = 63 → child 3.
