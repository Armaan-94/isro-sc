# A16. Venn Diagrams, Ages and Partnership

> Three small topics that each reduce to one idea. **Venn:** don't count the overlap twice. **Ages:** everyone ages at the same rate, so write equations in present ages. **Partnership:** profit is shared in the ratio of capital × time.

---

## 1. Venn diagrams (counting)

### Two sets

```
n(A ∪ B) = n(A) + n(B) − n(A ∩ B)
Only A = n(A) − n(A ∩ B)
Neither = Total − n(A ∪ B)
Exactly one = n(A) + n(B) − 2 n(A ∩ B)
```

Why subtract the intersection? People in both circles were counted once in A and once in B.

**Example:** 60% play cricket, 50% football, 30% both → at least one = **80%**, only cricket = **30%**, neither = 20%.

### Three sets

```
n(A ∪ B ∪ C) = n(A) + n(B) + n(C) − n(A∩B) − n(B∩C) − n(C∩A) + n(A∩B∩C)
```

The triple overlap is added three times, subtracted three times, so it must be added back once.

**Regions (draw them!):**

```
Only A = n(A) − n(A∩B) − n(A∩C) + n(A∩B∩C)
Exactly two = [n(A∩B) + n(B∩C) + n(C∩A)] − 3 n(A∩B∩C)
```

**Example:** A = 40, B = 35, C = 30, A∩B = 12, B∩C = 10, C∩A = 8, all three = 5.
Union = 105 − 30 + 5 = **80**. Only A = 40 − 12 − 8 + 5 = **25**. Exactly two = 30 − 15 = **15**.

> **The biggest trap:** "n(A∩B) = 12" usually means **at least** A and B (includes the triple). If the question says "**only** A and B" or "exactly two", it **excludes** the triple. Read carefully which one you are given.

**Language example:** 10 speak Spanish, 20 German, 8 Italian. 3 speak exactly Spanish + German, 2 speak all three, nobody else overlaps. Only Spanish = 10 − 3 − 2 = 5; only German = 20 − 3 − 2 = 15; only Italian = 8 − 2 = 6. Total = 5 + 15 + 6 + 3 + 2 = **31**.

### Counting numbers with Venn logic

From 1 to 100: divisible by 2 = 50, by 3 = 33, by both (6) = 16 → by 2 or 3 = 67, by neither = **33**.

---

## 2. Age problems

### The method

1. Let the **present** ages be variables (use ax, bx if a ratio is given).
2. "n years ago" → subtract n from **each** person. "After n years" → add n to **each**.
3. Translate each sentence into an equation and solve.

> The **difference** between two people's ages never changes. That alone solves many questions.

### Worked examples

- Father is 4 times his son now; 5 years ago 7 times. F = 4S, 4S − 5 = 7(S − 5) → 3S = 30 → **S = 10, F = 40**.
- Sum 90; 15 years ago father was 3 times the son: F + S = 90, F − 15 = 3(S − 15) → **F = 60, S = 30**.
- Twice the son's age + father's = 70; twice the father's + son's = 95 → **F = 40, S = 15**.
- Sum 35, product 150 → roots of x² − 35x + 150 = 0 → **30 and 5**.
- Ratio 2 years ago 5 : 3, after 2 years 7 : 5: (A − 2)/(B − 2) = 5/3 and (A + 2)/(B + 2) = 7/5 → **A = 12, B = 8**.
- Present ratio 4 : 5, after 5 years 5 : 6 → (4x + 5)/(5x + 5) = 5/6 → x = 5 → **20 and 25**.

**Children born at intervals:** 5 children born 3 years apart have ages summing to 50: a + (a + 3) + ... + (a + 12) = 5a + 30 = 50 → youngest **4**.

> **Shortcut by checking options:** age questions almost always have integer answers. Plug each option into the conditions; often faster than algebra.

---

## 3. Partnership

```
Profit share ∝ Capital × Time
```

- Same time for everyone → ratio of capitals.
- Different times → multiply each capital by its months.
- Capital changed mid-year → sum the pieces: 5000 for 4 months then 3000 for 8 months = 20,000 + 24,000 = **44,000** units.

**Examples:**
- A invests 6000 for 12 months, B 9000 for 8 months → 72,000 : 72,000 = **1 : 1**.
- A 2000 × 6, B 3000 × 4, C 6000 × 3 → 12 : 12 : 18 = 2 : 2 : 3. Profit 700 → C gets **300**.
- A starts with 5000; B joins after 4 months with 6000 → 60,000 : 48,000 = 5 : 4.

### Working and sleeping partners

A working partner first takes a salary or a fixed % of profit "off the top", then the **remainder** is split in the capital × time ratio.

Profit 10,000; A (working) takes 10% for management; the rest is split 3 : 2. A gets 1000 + 9000 × 3/5 = **6400**.

---

## 4. Exam traps

1. Venn: "both" may include the triple overlap; "only both" doesn't.
2. Only A ≠ n(A).
3. Ages: shift **every** person's age by the same years.
4. Partnership: never ignore the time each capital was invested.
5. Management fee comes out before the ratio split.

---

## 5. Practice questions (with solutions)

**Q1.** 60% play cricket, 50% football, 30% both. At least one?
(a) 70% (b) 80% (c) 90% (d) 100%
**Answer: (b).**

**Q2.** In Q1, only cricket?
(a) 60% (b) 30% (c) 20% (d) 40%
**Answer: (b).**

**Q3.** Father is 4 times his son; 5 years ago he was 7 times. Present ages?
(a) 32, 8 (b) 28, 7 (c) 40, 10 (d) 36, 9
**Answer: (c).**

**Q4.** Ram is 3 times Shyam; after 15 years, twice. Shyam's age?
(a) 10 (b) 12 (c) 15 (d) 20
**Answer: (c).**

**Q5.** A: ₹2000 for 6 months, B: ₹3000 for 4 months, C: ₹6000 for 3 months. C's share of ₹700?
(a) ₹200 (b) ₹250 (c) ₹300 (d) ₹350
**Answer: (c).**

**Q6.** 10 speak Spanish, 20 German, 8 Italian; 3 speak exactly Spanish and German; 2 speak all three; no other overlaps. Group size?
(a) 28 (b) 29 (c) 31 (d) 33
**Answer: (c).**

**Q7.** Class of 50: 30 like tea, 25 like coffee, 10 like neither. How many like both?
(a) 5 (b) 10 (c) 15 (d) 20
**Answer: (c).** At least one = 40 → 55 − 40.

**Q8.** In Q7, how many like exactly one?
(a) 25 (b) 30 (c) 35 (d) 40
**Answer: (a).** 15 + 10.

**Q9.** A = 40, B = 35, C = 30, A∩B = 12, B∩C = 10, C∩A = 8, A∩B∩C = 5. n(A ∪ B ∪ C)?
(a) 75 (b) 80 (c) 85 (d) 105
**Answer: (b).**

**Q10.** Same data. Only A?
(a) 20 (b) 25 (c) 28 (d) 33
**Answer: (b).**

**Q11.** Same data. Exactly two of the three?
(a) 15 (b) 20 (c) 25 (d) 30
**Answer: (a).**

**Q12.** Numbers from 1 to 100 divisible by neither 2 nor 3?
(a) 33 (b) 34 (c) 67 (d) 50
**Answer: (a).**

**Q13.** Twice the son's age plus the father's = 70; twice the father's plus the son's = 95. Father's age?
(a) 35 (b) 40 (c) 45 (d) 50
**Answer: (b).**

**Q14.** Sum of father's and son's ages 90; 15 years ago the father was 3 times the son. Son's age?
(a) 25 (b) 30 (c) 35 (d) 20
**Answer: (b).**

**Q15.** Ages A : B were 5 : 3 two years ago and will be 7 : 5 two years from now. A's present age?
(a) 10 (b) 12 (c) 14 (d) 16
**Answer: (b).**

**Q16.** Present ages are 4 : 5; after 5 years 5 : 6. Elder's present age?
(a) 20 (b) 25 (c) 30 (d) 15
**Answer: (b).**

**Q17.** 5 children born 3 years apart; ages total 50. Youngest?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (b).**

**Q18.** Ten years ago A was half of B; present ratio 3 : 4. Sum of present ages?
(a) 30 (b) 35 (c) 40 (d) 45
**Answer: (b).** 3x − 10 = (4x − 10)/2 → x = 5.

**Q19.** A mother is 3 times her daughter's age; in 4 years she'll be 2.5 times. Daughter's age?
(a) 10 (b) 12 (c) 14 (d) 8
**Answer: (b).** 3D + 4 = 2.5D + 10.

**Q20.** Sum of two ages 35, product 150. Elder's age?
(a) 25 (b) 30 (c) 15 (d) 20
**Answer: (b).**

**Q21.** A invests ₹6000 for 12 months; B ₹9000 for 8 months. Profit ratio?
(a) 2:3 (b) 3:2 (c) 1:1 (d) 4:3
**Answer: (c).**

**Q22.** A starts with ₹5000; B joins after 4 months with ₹6000. Annual profit ₹9000. B's share?
(a) ₹4000 (b) ₹4500 (c) ₹5000 (d) ₹3600
**Answer: (a).** 5 : 4.

**Q23.** A invests ₹5000 for 4 months, then ₹3000 for 8 months; B invests ₹4000 for 12 months. Profit ₹2300. A's share?
(a) ₹1000 (b) ₹1100 (c) ₹1200 (d) ₹1150
**Answer: (b).** 44,000 : 48,000 = 11 : 12.

**Q24.** Profit ₹10,000. A, the working partner, gets 10% for management; the rest is split 3 : 2 (A : B). A's total?
(a) ₹6000 (b) ₹6400 (c) ₹5400 (d) ₹7000
**Answer: (b).**

**Q25.** Capitals 2 : 3 : 5 invested for 4, 3, 2 months. Profit ₹2700. C's share?
(a) ₹900 (b) ₹1000 (c) ₹800 (d) ₹1350
**Answer: (b).** 8 : 9 : 10.

**Q26.** The difference between two brothers' ages is 6 years. After 10 years the difference will be:
(a) 16 (b) 6 (c) 4 (d) depends on ages
**Answer: (b).**
