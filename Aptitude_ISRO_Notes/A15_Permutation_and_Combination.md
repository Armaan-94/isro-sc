# A15. Permutation and Combination

> **The first question to ask every time: does order matter?** If swapping two chosen items gives a **different** outcome (seating, passwords, ranks), it's a **permutation**. If not (committees, handfuls of balls), it's a **combination**. Then apply the right tricks for repetition, grouping, circles and restrictions.

---

## 1. Counting principles

- **Multiplication (AND):** task 1 in m ways and task 2 in n ways → **m × n**.
- **Addition (OR):** either option 1 (m ways) or option 2 (n ways), not both → **m + n**.

Example: 3 shirts and 4 trousers → 12 outfits.

**Factorial:** n! = n × (n − 1) × ... × 1; **0! = 1**.

---

## 2. Permutations and combinations

```
ⁿPᵣ = n! / (n − r)!          arrangements (order matters)
ⁿCᵣ = n! / (r!(n − r)!)      selections (order doesn't matter)
ⁿPᵣ = ⁿCᵣ × r!
```

- ⁿCᵣ = ⁿCₙ₋ᵣ. (If ¹⁰Cᵣ = ¹⁰Cᵣ₊₂ then r + (r + 2) = 10 → **r = 4**.)
- ⁿC₀ + ⁿC₁ + ... + ⁿCₙ = **2ⁿ** (subsets). Choosing **at least one** of n items: **2ⁿ − 1**.
- ⁿC₁ = n, ⁿC₂ = n(n − 1)/2.

| | Example |
|---|---|
| Arrange 5 people in a row | 5! = **120** |
| Choose 3 of 7 for a committee | ⁷C₃ = **35** |
| President, secretary, treasurer from 7 | ⁷P₃ = **210** |

---

## 3. Repetition

- **Repetition allowed:** n choices for each of r positions → **nʳ**. (3-letter codes from 26 letters: 26³ = **17,576**.)
- **Identical items in a word:** n! / (p₁! p₂! ...).

| Word | Count |
|---|---|
| APPLE | 5!/2! = **60** |
| BANANA | 6!/(3! 2!) = **60** |
| LEADER | 6!/2! = **360** |
| MISSISSIPPI | 11!/(4! 4! 2!) = **34,650** |

---

## 4. Restrictions

### Items together: treat them as one block

Arrange the block with the others, then multiply by arrangements **inside** the block.
- TABLE with vowels (A, E) together: units (AE), T, B, L → 4! × 2! = **48**.
- APPLE with the two P's together: (PP), A, L, E → 4! × 1 = **24** (identical letters, no internal arrangement).
- 3 boys and 2 girls in a row with the girls together: 4! × 2! = **48**.
- DAUGHTER with vowels (A, U, E) together: 6! × 3! = **4320**.

### Items never together: total − together

LEADER with vowels (E, E, A) never all together: together = 4! × (3!/2!) = 72 → **360 − 72 = 288**.

### No two of a kind adjacent: the gap method

Arrange the others first, then put the restricted items in the **gaps**.
5 boys and 4 girls in a row, no two girls adjacent: arrange boys 5! = 120; 6 gaps; place girls ⁶P₄ = 360 → **43,200**.

### Fixed positions / digits

- 4-digit numbers from 1 to 9, no repetition, divisible by 5: last digit 5 → ⁸P₃ = **336**.
- 4-digit numbers from 0 to 9, no repetition: 9 × 9 × 8 × 7 = **4536** (first digit can't be 0).
- Even 3-digit numbers from {1, 2, 3, 4, 5}, no repetition: last digit 2 or 4 → 2 × 4 × 3 = **24**.

### Selection with conditions

- 5 from 6 men and 4 women with exactly 3 men: ⁶C₃ × ⁴C₂ = 20 × 6 = **120**.
- 3 from 5 men and 3 women with at least one woman: ⁸C₃ − ⁵C₃ = 56 − 10 = **46**.
- 2 balls of the same colour from 4 red and 3 green: ⁴C₂ + ³C₂ = **9**.

---

## 5. Circular arrangements

- n distinct people around a table: **(n − 1)!** (rotations are the same).
- Necklace/garland (also flipping is the same): **(n − 1)!/2**.
- 6 people at a round table: 5! = **120**. 6 beads on a necklace: **60**.
- 5 men and 5 women alternately around a table: seat men (4!), then women in the 5 gaps (5!) → 24 × 120 = **2880**.

---

## 6. Geometry counts

| Count | Formula |
|---|---|
| Handshakes / single round-robin matches among n | ⁿC₂ |
| Double round-robin (home and away) | 2 × ⁿC₂ = n(n − 1) |
| Lines through n points (no 3 collinear) | ⁿC₂ |
| Triangles from n points (no 3 collinear) | ⁿC₃ |
| Diagonals of an n-gon | ⁿC₂ − n = n(n − 3)/2 |
| Triangles from n points with m collinear | ⁿC₃ − ᵐC₃ |
| Grid paths (a right, b up) | ᵃ⁺ᵇCₐ |

- 8 people shaking hands: **28**. 10 teams playing twice: **90**.
- Hexagon diagonals: 15 − 6 = **9**. Decagon: 45 − 10 = **35**.
- 10 points: **45** lines, **120** triangles.
- 12 points with 5 collinear: 220 − 10 = **210** triangles.
- Paths from (0, 0) to (3, 2): ⁵C₂ = **10**.

---

## 7. Rank of a word (dictionary order)

For each position from left to right: count the **remaining** letters smaller than the current letter, multiply by (remaining letters after this position)!, add everything, then **+1**.

**CAB** (letters A, B, C): C has 2 smaller (A, B) → 2 × 2! = 4; A → 0; B → 0. Rank = **5**.

**RANK** (A, K, N, R): R → 3 smaller × 3! = 18; A → 0; N → K smaller → 1 × 1! = 1; K → 0. Rank = 19 + 1 = **20**.

**Repeated letters:** divide each term by the factorials of repeats among the remaining letters.
**BOOK** (B, K, O, O): B → 0; O → K is smaller: fixing K leaves {O, O} → 2!/2! = 1; O → K smaller: 1; K. Rank = 2 + 1 = **3** (BKOO, BOKO, BOOK).

---

## 8. Derangements

Arrangements where **no item is in its original place**:

```
D(n) = n! [1 − 1/1! + 1/2! − 1/3! + ... ± 1/n!]
```

D(1) = 0, D(2) = 1, D(3) = 2, **D(4) = 9**, D(5) = 44.
(Letters into wrong envelopes.)

---

## 9. Exam traps

1. Order matters → P; doesn't → C.
2. Circle (n − 1)!; necklace (n − 1)!/2.
3. Identical letters: divide.
4. "Together" = block × internal arrangements; "never together" = total − together.
5. Numbers: the first digit can't be 0.
6. Rank: add 1 at the end.

---

## 10. Practice questions (with solutions)

**Q1.** 5 people in a row?
(a) 60 (b) 120 (c) 24 (d) 720
**Answer: (b).**

**Q2.** 3-letter codes from A to Z with repetition?
(a) 15,600 (b) 17,576 (c) 2600 (d) 78
**Answer: (b).**

**Q3.** Arrangements of APPLE?
(a) 120 (b) 60 (c) 20 (d) 24
**Answer: (b).**

**Q4.** APPLE with both P's together?
(a) 24 (b) 12 (c) 48 (d) 60
**Answer: (a).**

**Q5.** Committee of 3 from 7?
(a) 210 (b) 35 (c) 21 (d) 42
**Answer: (b).**

**Q6.** 6 people around a round table?
(a) 720 (b) 120 (c) 360 (d) 24
**Answer: (b).**

**Q7.** 6 distinct beads in a necklace?
(a) 120 (b) 60 (c) 720 (d) 24
**Answer: (b).**

**Q8.** Rank of RANK?
(a) 18 (b) 19 (c) 20 (d) 21
**Answer: (c).**

**Q9.** Handshakes among 8 people?
(a) 56 (b) 28 (c) 64 (d) 36
**Answer: (b).**

**Q10.** Diagonals of a hexagon?
(a) 9 (b) 12 (c) 15 (d) 6
**Answer: (a).**

**Q11.** Derangements of 4 objects?
(a) 6 (b) 9 (c) 12 (d) 3
**Answer: (b).**

**Q12.** 4-digit numbers from digits 1 to 9 (no repetition) divisible by 5?
(a) 224 (b) 336 (c) 448 (d) 280
**Answer: (b).**

**Q13.** 3 boys and 2 girls in a row, girls together?
(a) 24 (b) 48 (c) 12 (d) 120
**Answer: (b).**

**Q14.** Arrangements of MISSISSIPPI?
(a) 34,650 (b) 39,916,800 (c) 69,300 (d) 11,550
**Answer: (a).**

**Q15.** Arrangements of TABLE with vowels together?
(a) 24 (b) 48 (c) 72 (d) 120
**Answer: (b).**

**Q16.** Arrangements of LEADER?
(a) 720 (b) 360 (c) 180 (d) 120
**Answer: (b).**

**Q17.** Arrangements of BANANA?
(a) 720 (b) 120 (c) 60 (d) 30
**Answer: (c).**

**Q18.** LEADER arrangements with the vowels never all together?
(a) 288 (b) 72 (c) 360 (d) 216
**Answer: (a).**

**Q19.** Committee of 5 from 6 men and 4 women with exactly 3 men?
(a) 60 (b) 120 (c) 80 (d) 150
**Answer: (b).**

**Q20.** Committee of 3 from 5 men and 3 women with at least one woman?
(a) 46 (b) 56 (c) 45 (d) 36
**Answer: (a).**

**Q21.** Triangles from 10 points, no three collinear?
(a) 30 (b) 45 (c) 120 (d) 720
**Answer: (c).**

**Q22.** Straight lines through 10 points, no three collinear?
(a) 20 (b) 45 (c) 90 (d) 100
**Answer: (b).**

**Q23.** 12 points of which 5 are collinear. Number of triangles?
(a) 220 (b) 210 (c) 200 (d) 215
**Answer: (b).**

**Q24.** 10 teams; each plays every other twice. Total matches?
(a) 45 (b) 90 (c) 100 (d) 180
**Answer: (b).**

**Q25.** 4-digit numbers with distinct digits (0 to 9)?
(a) 5040 (b) 4536 (c) 4500 (d) 9000
**Answer: (b).**

**Q26.** Even 3-digit numbers from {1, 2, 3, 4, 5} without repetition?
(a) 12 (b) 24 (c) 36 (d) 48
**Answer: (b).**

**Q27.** Ways to choose at least one fruit from 5 different fruits?
(a) 5 (b) 25 (c) 31 (d) 32
**Answer: (c).**

**Q28.** 5 boys and 4 girls in a row with no two girls together?
(a) 2880 (b) 43,200 (c) 14,400 (d) 86,400
**Answer: (b).**

**Q29.** 5 men and 5 women alternately around a round table?
(a) 2880 (b) 14,400 (c) 120 (d) 28,800
**Answer: (a).**

**Q30.** ¹⁰Cᵣ = ¹⁰Cᵣ₊₂. r = ?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (b).**

**Q31.** Arrangements of DAUGHTER with all vowels together?
(a) 720 (b) 4320 (c) 40,320 (d) 2160
**Answer: (b).**

**Q32.** Shortest grid paths from (0, 0) to (3, 2) moving only right or up?
(a) 6 (b) 10 (c) 12 (d) 20
**Answer: (b).**

**Q33.** Rank of BOOK among its arrangements in dictionary order?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (b).**
