# A14. Probability

> **Probability is counting in disguise.** P(event) = favourable outcomes ÷ total outcomes, as long as all outcomes are equally likely. The two power tools: the **complement** ("at least one" = 1 − "none") and **multiplication for independent events**.

---

## 1. Basic definitions

- **Experiment:** something with uncertain outcomes (rolling a die).
- **Sample space S:** all possible outcomes ({1, 2, 3, 4, 5, 6}).
- **Event:** a subset of outcomes ("even number" = {2, 4, 6}).

```
P(E) = number of favourable outcomes / total number of equally likely outcomes
0 ≤ P(E) ≤ 1;   P(sure event) = 1;   P(impossible event) = 0
```

---

## 2. Complement: the "at least one" trick

```
P(not E) = 1 − P(E)
P(at least one) = 1 − P(none)
```

3 coins, at least one head: 1 − P(TTT) = 1 − 1/8 = **7/8**.

---

## 3. Addition and multiplication

```
P(A or B) = P(A) + P(B) − P(A and B)
Mutually exclusive (can't both happen): P(A or B) = P(A) + P(B)
Independent (one doesn't affect the other): P(A and B) = P(A) × P(B)
Conditional: P(A | B) = P(A and B) / P(B)
```

- P(A) = 0.6, P(B) = 0.5, P(A ∩ B) = 0.3 → P(A ∪ B) = **0.8**, P(neither) = **0.2**, P(A | B) = 0.6.
- Independent with P(A) = 0.5, P(B) = 0.4 → P(A and B) = 0.2, P(A or B) = 0.7.

> **Trap:** mutually exclusive ≠ independent. If A and B are mutually exclusive with non-zero probabilities, P(A and B) = 0 ≠ P(A)P(B), so they're **dependent**.

**Exhaustive:** events that together cover the whole sample space (probabilities of disjoint exhaustive events sum to 1).

---

## 4. Odds

- **Odds in favour** = favourable : unfavourable. **Odds against** = unfavourable : favourable.
- Odds in favour a : b → **P = a/(a + b)**.

Odds 4 : 3 in favour → P = **4/7**. Odds 7 : 5 against → P = **5/12**.

---

## 5. Standard sample spaces (know cold)

### Coins
n coins: **2ⁿ** outcomes. P(exactly k heads) = C(n, k)/2ⁿ.
4 coins, exactly 2 heads: 6/16 = **3/8**.

### Dice
One die: 6. Two dice: **36**. Three dice: 216.

Sums with two dice (number of ways):

| Sum | 2 | 3 | 4 | 5 | 6 | **7** | 8 | 9 | 10 | 11 | 12 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Ways | 1 | 2 | 3 | 4 | 5 | **6** | 5 | 4 | 3 | 2 | 1 |

- P(sum 7) = 6/36 = **1/6** (most likely sum).
- P(doublet) = 6/36 = **1/6**.
- P(sum > 9) = (3 + 2 + 1)/36 = **1/6**.
- P(product even) = 1 − P(both odd) = 1 − 9/36 = **3/4**.
- Three dice all the same: 6/216 = **1/36**.

### Cards
52 cards = 4 suits × 13. Hearts and diamonds red (26), spades and clubs black (26). **12 face cards** (J, Q, K). 4 aces.

- P(face card) = 12/52 = **3/13**.
- P(king or heart) = (4 + 13 − 1)/52 = **4/13**.
- P(red or king) = (26 + 2)/52 = **7/13**.
- Two cards without replacement, both aces: (4/52)(3/51) = **1/221**.

### Balls in a bag
Use combinations: 5 red, 4 blue; 2 drawn; P(both blue) = C(4,2)/C(9,2) = 6/36 → P(at least one red) = **5/6**.

---

## 6. Classic problem types

**53 Sundays:**
- Leap year: 366 = 52 weeks + 2 days. The 2 extra days are a consecutive pair (7 possible pairs: Sun-Mon, Mon-Tue, ..., Sat-Sun), 2 of which contain Sunday → **2/7**.
- Non-leap year: 1 extra day → **1/7**.

**Hitting a target:** A hits with 1/3, B with 1/2, independently. P(hit) = 1 − (2/3)(1/2) = **2/3**.

**Truth-tellers contradicting:** A tells the truth 60% of the time, B 70%. P(they contradict) = P(A true, B false) + P(A false, B true) = 0.6 × 0.3 + 0.4 × 0.7 = **0.46**.

**Divisibility from 1 to 100:** divisible by 3 or 5: 33 + 20 − 6 = 47 → **47/100**.

**Committee selection:** 6 men, 4 women, choose 3; P(at least 2 women) = [C(4,2)C(6,1) + C(4,3)]/C(10,3) = (36 + 4)/120 = **1/3**.

**Distinct days:** 3 people born on random weekdays, all different: (7 × 6 × 5)/7³ = **30/49**.

---

## 7. Exam traps

1. Odds → probability: denominator is a + b.
2. Mutually exclusive events (non-zero probabilities) are not independent.
3. Use 1 − P(none) for "at least one".
4. "With" vs "without" replacement changes the second probability.
5. Leap year 2/7, normal year 1/7.

---

## 8. Practice questions (with solutions)

**Q1.** A die is rolled. P(number > 4)?
(a) 1/6 (b) 1/3 (c) 1/2 (d) 2/3
**Answer: (b).**

**Q2.** Two coins tossed. P(exactly one head)?
(a) 1/4 (b) 1/2 (c) 3/4 (d) 1
**Answer: (b).**

**Q3.** 5 red, 4 blue; two drawn. P(at least one red)?
(a) 5/6 (b) 7/9 (c) 4/9 (d) 8/9
**Answer: (a).**

**Q4.** Odds in favour 4 : 3. Probability?
(a) 4/3 (b) 3/7 (c) 4/7 (d) 3/4
**Answer: (c).**

**Q5.** P(face card) from a deck?
(a) 3/13 (b) 1/13 (c) 4/13 (d) 1/4
**Answer: (a).**

**Q6.** P(a leap year has 53 Sundays)?
(a) 1/7 (b) 2/7 (c) 3/7 (d) 1/2
**Answer: (b).**

**Q7.** Two dice. P(sum = 7)?
(a) 1/6 (b) 1/12 (c) 5/36 (d) 1/9
**Answer: (a).**

**Q8.** Committee of 3 from 6 men and 4 women. P(at least 2 women)?
(a) 1/6 (b) 3/10 (c) 1/3 (d) 2/5
**Answer: (c).**

**Q9.** A and B are mutually exclusive, P(A) = 0.3, P(B) = 0.4. Are they independent?
(a) Yes (b) No (c) can't say (d) only if P(A) = P(B)
**Answer: (b).**

**Q10.** 3 coins. P(at least one head)?
(a) 1/8 (b) 3/8 (c) 7/8 (d) 1/2
**Answer: (c).**

**Q11.** P(a non-leap year has 53 Sundays)?
(a) 1/7 (b) 2/7 (c) 1/365 (d) 1/52
**Answer: (a).**

**Q12.** Two dice. P(doublet)?
(a) 1/36 (b) 1/6 (c) 1/12 (d) 1/3
**Answer: (b).**

**Q13.** Two dice. P(sum = 10)?
(a) 1/12 (b) 1/9 (c) 1/18 (d) 5/36
**Answer: (a).**

**Q14.** Two dice. P(sum > 9)?
(a) 1/6 (b) 1/4 (c) 5/36 (d) 1/12
**Answer: (a).**

**Q15.** Two dice. P(product is even)?
(a) 1/2 (b) 3/4 (c) 1/4 (d) 2/3
**Answer: (b).**

**Q16.** One card. P(king or heart)?
(a) 17/52 (b) 4/13 (c) 1/4 (d) 1/13
**Answer: (b).**

**Q17.** One card. P(red or king)?
(a) 7/13 (b) 15/26 (c) 1/2 (d) 6/13
**Answer: (a).**

**Q18.** Two cards without replacement. P(both aces)?
(a) 1/169 (b) 1/221 (c) 1/13 (d) 1/26
**Answer: (b).**

**Q19.** Independent A, B with P(A) = 0.5, P(B) = 0.4. P(A or B)?
(a) 0.9 (b) 0.7 (c) 0.2 (d) 0.6
**Answer: (b).** 0.5 + 0.4 − 0.2.

**Q20.** A hits a target with probability 1/3, B with 1/2. Both fire. P(target hit)?
(a) 1/6 (b) 1/2 (c) 2/3 (d) 5/6
**Answer: (c).**

**Q21.** A speaks the truth 60%, B 70%. P(they contradict each other on a statement)?
(a) 0.42 (b) 0.46 (c) 0.54 (d) 0.58
**Answer: (b).**

**Q22.** 3 red and 5 black balls; one drawn. P(red)?
(a) 3/5 (b) 3/8 (c) 5/8 (d) 1/3
**Answer: (b).**

**Q23.** Same bag, two drawn. P(both red)?
(a) 3/28 (b) 9/64 (c) 3/8 (d) 1/8
**Answer: (a).** C(3,2)/C(8,2) = 3/28.

**Q24.** 4 coins. P(exactly 2 heads)?
(a) 1/4 (b) 3/8 (c) 1/2 (d) 5/16
**Answer: (b).**

**Q25.** A letter is chosen from "PROBABILITY". P(vowel)?
(a) 3/11 (b) 4/11 (c) 5/11 (d) 2/11
**Answer: (b).** O, A, I, I.

**Q26.** A number from 1 to 100. P(divisible by 3 or 5)?
(a) 47/100 (b) 53/100 (c) 33/100 (d) 2/5
**Answer: (a).**

**Q27.** Three dice. P(all show the same number)?
(a) 1/216 (b) 1/36 (c) 1/6 (d) 1/72
**Answer: (b).**

**Q28.** A two-digit number is chosen. P(perfect square)?
(a) 1/15 (b) 1/10 (c) 7/90 (d) 1/9
**Answer: (a).** 16, 25, 36, 49, 64, 81 → 6/90.

**Q29.** Odds against an event are 7 : 5. P(event)?
(a) 7/12 (b) 5/12 (c) 5/7 (d) 7/5
**Answer: (b).**

**Q30.** P(A) = 0.6, P(B) = 0.5, P(A ∩ B) = 0.3. P(neither A nor B)?
(a) 0.1 (b) 0.2 (c) 0.3 (d) 0.4
**Answer: (b).**

**Q31.** Same data. P(A | B)?
(a) 0.5 (b) 0.6 (c) 0.3 (d) 0.8
**Answer: (b).**

**Q32.** Three people. P(all born on different days of the week)?
(a) 30/49 (b) 1/7 (c) 6/7 (d) 210/49
**Answer: (a).**
