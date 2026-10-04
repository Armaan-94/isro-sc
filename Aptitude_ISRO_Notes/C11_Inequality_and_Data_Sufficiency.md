# C11. Inequality and Data Sufficiency

> **Inequalities:** decode the symbols, write one chain, then walk from one element to the other. The walk works only if every step points the same way. **Data sufficiency:** you never solve the problem; you only ask "do I get exactly one answer?"

---

## 1. Decoding

Questions often disguise >, <, =, ≥, ≤ as symbols (e.g., @ = >, # = <, $ = =, & = ≥, * = ≤). **Translate everything first**, then reason.

## 2. Combining inequalities

Walk along the chain from X to Y.

| Links on the path | Conclusion |
|---|---|
| All point the same way, **at least one strict** (> or <) | **Strict**: X > Y |
| All the same way, but only ≥ and = | X ≥ Y (neither X > Y nor X = Y is definite) |
| All = | X = Y |
| Directions **change** (e.g., > then <) | **No relation** |

Examples:
- A > B > C = D ≥ E → **A > E** (A > D and D ≥ E means A > E).
- C = D ≥ E → only C ≥ E. "C > E" and "C = E" individually don't follow, but **either one does** (they're a complementary pair).
- P ≥ Q < R → no relation between P and R.
- X > Y and Z > Y (both above the same element) → **no relation** between X and Z.

### Either-or rule
If the chain gives X ≥ Y, then the conclusions "X > Y" and "X = Y" form an **either-or** pair. Same for ≤ with < and =.

---

## 3. Data sufficiency

Question + Statement I + Statement II. Standard options:

| Option | Meaning |
|---|---|
| (a) | I alone is sufficient, II alone is not |
| (b) | II alone is sufficient, I alone is not |
| (c) | Both together are sufficient, neither alone |
| (d) | Each alone is sufficient |
| (e) | Not sufficient even together |

### Method

1. Decide exactly what must be pinned down (a single value, or a definite yes/no).
2. Test **I alone**. Forget II.
3. Test **II alone**. Forget I.
4. Only if both fail, combine.

**"Sufficient"** means one unique answer. For yes/no questions, a definite **No** is also sufficient.

### Classic traps
- **Quadratics:** x² = 49 gives ±7, not unique.
- **Hidden assumptions:** "x is an integer" may not be given.
- **Leaking:** using II while judging I.

---

## 4. Practice questions (with solutions)

**Q1.** A > B, B > C. Definitely:
(a) A > C (b) A = C (c) A < C (d) can't say
**Answer: (a).**

**Q2.** P ≥ Q and Q < R. Relation between P and R?
(a) P > R (b) P ≥ R (c) no definite relation (d) P < R
**Answer: (c).** Directions change at Q.

**Q3.** A > B > C = D ≥ E. Does A > E follow?
(a) yes (b) no, only A ≥ E (c) no relation (d) A < E
**Answer: (a).**

**Q4.** X > Y and Z > Y. Definitely:
(a) X > Z (b) Z > X (c) X = Z (d) no definite relation
**Answer: (d).**

**Q5.** C = D ≥ E. Conclusions: I. C > E. II. C = E.
(a) only I (b) only II (c) either I or II (d) neither
**Answer: (c).**

**Q6.** A > B = C > D ≥ E > F. Which is NOT definitely true?
(a) A > F (b) B > D (c) D > F (d) E > D
**Answer: (d).**

**Q7.** @ = >, # = <, $ = =, & = ≥. Given P & Q, Q $ R, R @ S. Then:
(a) P > S (b) P ≥ S only (c) P = S (d) no relation
**Answer: (a).** P ≥ Q = R > S.

**Q8.** What is x? I: x + 5 = 12. II: x² = 49.
**Answer: (a).** I gives 7; II gives ±7.

**Q9.** Is n divisible by 3? I: n is divisible by 9. II: n is divisible by 4.
**Answer: (a).**

**Q10.** What is y? I: x + y = 10. II: x − y = 2.
**Answer: (c).** Together: x = 6, y = 4.

**Q11.** What is x? I: x² − 5x + 6 = 0. II: x is positive.
**Answer: (e).** x = 2 or 3, both positive.

**Q12.** Is x > y? I: x > 0. II: y < 0.
**Answer: (c).**

**Q13.** Perimeter of a rectangle? I: length 10 cm. II: area 80 cm².
**Answer: (c).** Width 8 → perimeter 36.

**Q14.** x is an integer. Is x even? I: x² is even. II: 3x is even.
**Answer: (d).** Each alone forces x even.

**Q15.** How old is A? I: A is 5 years older than B. II: B is 20.
**Answer: (c).**

**Q16.** What day of the week is the 10th of a month? I: The 3rd is a Monday. II: The 17th is a Monday.
**Answer: (d).** Each gives Monday by ±7.

**Q17.** What is the ratio of boys to girls in a class? I: The class has 40 students. II: There are 25 girls.
**Answer: (c).** 15 : 25 = 3 : 5.

**Q18.** Is p a prime number? I: p is odd. II: 10 < p < 14.
**Answer: (c).** II alone gives 11, 12, 13 (12 isn't prime, so not sufficient). Together: 11 or 13, both prime → definite yes.

**Q19.** Is x positive? I: x³ > 0. II: x² > 0.
**Answer: (a).** x³ > 0 forces x > 0; x² > 0 allows negatives.
