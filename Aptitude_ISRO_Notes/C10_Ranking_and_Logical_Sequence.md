# C10. Ranking, Order and Logical Sequence of Words

> One formula solves most ranking questions: **total = rank from one end + rank from the other end − 1** (the person is counted from both sides, so subtract them once). Logical sequence questions need you to name the ordering dimension (size, time, process) before arranging.

---

## 1. Ranking formula

```
Total = (rank from left/top) + (rank from right/bottom) − 1
Rank from other end = Total − rank from this end + 1
```

- Meena is 9th from the left and 15th from the right → **23**.
- 30 in a queue, 12th from the front → from the back 30 − 12 + 1 = **19**.
- 35 students, Arjun 10th from the top → **26th** from the bottom.

## 2. People between two positions

```
Between positions x and y (same end) = |x − y| − 1
```

- 5th and 15th from the left → **9** between.
- Row of 40; A is 12th from the left, B is 20th from the right (= 21st from the left) → between = 21 − 12 − 1 = **8**.

> If two people's positions **overlap** (e.g., A 25th from the left and B 20th from the right in a row of 40), B is 21st from the left, which is left of A: between = 25 − 21 − 1 = 3.

## 3. Interchange problems

A is 10th from the left, B is 9th from the right. They swap; A becomes 15th from the left. A now occupies B's old seat, which is 15th from the left **and** 9th from the right → total = 15 + 9 − 1 = **23**.

## 4. Pass/fail and rank changes

- 5 students failed; Kamal is 15th from the top and 26th from the bottom **among those who passed** → passed = 40, total = **45**.
- Rank improves from 15th to 10th → overtook **5** students.
- Adding a person to the row changes the total, not the people already counted.

---

## 5. Logical sequence of words

Identify the dimension first:

| Dimension | Example |
|---|---|
| Life cycle / growth | Seed → Plant → Flower → Fruit |
| Age | Infant → Child → Adolescent → Adult → Old |
| Size / hierarchy | Village → District → State → Country |
| Process | Advertisement → Application → Interview → Offer → Joining |
| Dictionary order | compare letter by letter |

**Dictionary order:** Pradesh, Prepare, Preach, Pravin → compare the 4th letter where needed: Pra**d**esh < Pra**v**in < Pre**a**ch < Pre**p**are.

> Choose the order that uses **all** the words consistently.

---

## 6. Practice questions (with solutions)

**Q1.** Meena is 9th from the left and 15th from the right. Total?
(a) 24 (b) 23 (c) 25 (d) 22
**Answer: (b).**

**Q2.** 30 people; a person is 12th from the front. From the back?
(a) 18 (b) 19 (c) 20 (d) 17
**Answer: (b).**

**Q3.** People between the 5th and 15th from the left?
(a) 10 (b) 9 (c) 11 (d) 8
**Answer: (b).**

**Q4.** Arjun is 10th from the top in a class of 35. Rank from the bottom?
(a) 25 (b) 26 (c) 24 (d) 27
**Answer: (b).**

**Q5.** Logical order: (1) Seed (2) Fruit (3) Flower (4) Plant
(a) 1-4-3-2 (b) 1-3-4-2 (c) 4-1-3-2 (d) 1-2-3-4
**Answer: (a).**

**Q6.** Logical order: (1) Country (2) Village (3) State (4) District
(a) 2-4-3-1 (b) 2-3-4-1 (c) 4-2-3-1 (d) 1-3-4-2
**Answer: (a).**

**Q7.** Rakesh is 7th from the left and 4th from the right. One more child joins. New total?
(a) 10 (b) 11 (c) 9 (d) 12
**Answer: (b).**

**Q8.** Priya's rank improves from 15th to 10th. Students overtaken?
(a) 5 (b) 10 (c) 15 (d) 25
**Answer: (a).**

**Q9.** Row of 40; A is 12th from the left, B is 20th from the right. People between them?
(a) 7 (b) 8 (c) 9 (d) 10
**Answer: (b).**

**Q10.** A is 10th from the left, B 9th from the right. After swapping, A is 15th from the left. Total?
(a) 22 (b) 23 (c) 24 (d) 25
**Answer: (b).**

**Q11.** 5 students failed. Kamal is 15th from the top and 26th from the bottom among those who passed. Total students?
(a) 40 (b) 45 (c) 46 (d) 41
**Answer: (b).**

**Q12.** Logical order: (1) Adult (2) Infant (3) Old (4) Child (5) Adolescent
(a) 2-4-5-1-3 (b) 2-5-4-1-3 (c) 4-2-5-1-3 (d) 2-4-1-5-3
**Answer: (a).**

**Q13.** Dictionary order: (1) Pradesh (2) Prepare (3) Preach (4) Pravin
(a) 1-4-3-2 (b) 1-3-4-2 (c) 4-1-3-2 (d) 1-4-2-3
**Answer: (a).**

**Q14.** Logical order: (1) Interview (2) Joining (3) Application (4) Advertisement (5) Offer
(a) 4-3-1-5-2 (b) 3-4-1-5-2 (c) 4-1-3-5-2 (d) 4-3-5-1-2
**Answer: (a).**

**Q15.** Row of 40: A is 25th from the left, B is 20th from the right. How many between them?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (b).** B = 21st from the left.

**Q16.** In a class, X ranks 16th from the top and 29th from the bottom. Students in the class?
(a) 44 (b) 45 (c) 46 (d) 43
**Answer: (a).**

**Q17.** Logical order: (1) Word (2) Letter (3) Sentence (4) Paragraph
(a) 2-1-3-4 (b) 1-2-3-4 (c) 2-3-1-4 (d) 4-3-1-2
**Answer: (a).**
