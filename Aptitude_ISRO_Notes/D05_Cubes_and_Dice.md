# D05. Cubes and Dice

> **Dice:** two faces you can see at the same time are never opposite; when that isn't enough, eliminate. **Painted cubes:** every small cube is a corner, an edge piece, a face piece, or hidden inside; four formulas cover everything.

---

## 1. Standard die

- Faces 1 to 6; **opposite faces add to 7**: (1, 6), (2, 5), (3, 4).
- Only assume this if the question says "standard die".

## 2. Opposite faces from several views

1. **Faces visible together are adjacent, never opposite.** Each view rules out three pairs.
2. **Elimination:** every face has 4 neighbours and 1 opposite. Once you have seen 4 different neighbours of a face, the remaining number is its opposite.

**Example:** views show {2, 3, 4} and {2, 4, 6}. So 2's neighbours include 3, 4, 6 and 4's include 2, 3, 6. 2 and 4 must be opposite to numbers from {1, 5}, one each. That uses 1, 2, 4, 5, leaving **3 opposite 6**.

**Example:** the faces seen next to 1 across views are 2, 3, 4 and 5 → **6 is opposite 1**.

## 3. Rolling a die

Track six faces (Top, Bottom, North, South, East, West).
- **Roll east (right):** Top → East → Bottom → West → Top. North/South unchanged.
- **Roll north (forward):** Top → North → Bottom → South → Top. East/West unchanged.

Standard die, 1 on top, 3 facing east. Roll east once: old West (4, opposite 3) comes to the top → **4**.

## 4. Nets

In a straight line of squares in a net, two squares with **exactly one square between them** become **opposite**.

```
        [2]
[3] [1] [4] [6]
        [5]
```

Row: (3, 4) and (1, 6) opposite. Column 2-4-5: (2, 5) opposite.

## 5. Painted cube formulas

An n × n × n cube, painted on all faces, cut into n³ unit cubes (n ≥ 2):

| Painted faces | Location | Count |
|---|---|---|
| 3 | corners | **8** |
| 2 | edges (not corners) | **12(n − 2)** |
| 1 | face centres | **6(n − 2)²** |
| 0 | inside | **(n − 2)³** |

Check for n = 4: 8 + 24 + 24 + 8 = 64 ✓.

- "At least one face" = n³ − (n − 2)³.
- "At most one face" = 6(n − 2)² + (n − 2)³.
- n = 2: all 8 are corners; 0 cubes with 1 or 2 faces.

### Cuts
- Cutting into n³ equal cubes needs **3(n − 1)** cuts (n − 1 in each direction).
- Maximum pieces from 3 cuts: **8**.

---

## 6. Practice questions (with solutions)

**Q1.** Standard die, 1 on top. Bottom?
(a) 2 (b) 5 (c) 6 (d) 4
**Answer: (c).**

**Q2.** n = 3. Cubes with exactly 2 faces painted?
(a) 8 (b) 12 (c) 6 (d) 1
**Answer: (b).**

**Q3.** n = 5. Exactly 1 face painted?
(a) 24 (b) 36 (c) 54 (d) 64
**Answer: (c).**

**Q4.** n = 6. No paint?
(a) 27 (b) 64 (c) 16 (d) 8
**Answer: (b).**

**Q5.** n = 4. At least one face painted?
(a) 8 (b) 24 (c) 32 (d) 56
**Answer: (d).**

**Q6.** n = 5. At most one face painted?
(a) 54 (b) 27 (c) 81 (d) 62
**Answer: (c).**

**Q7.** Views {2, 3, 4} and {2, 4, 6}. Opposite 3?
(a) 1 (b) 5 (c) 6 (d) can't say
**Answer: (c).**

**Q8.** n = 10. Exactly 2 faces painted?
(a) 8 (b) 96 (c) 480 (d) 64
**Answer: (b).**

**Q9.** Net: P-Q-R-S in a row, T above Q, U below Q. Opposite pairs?
(a) P-R, Q-S, T-U (b) P-S, Q-U, R-T (c) P-Q, R-S, T-U (d) P-R only
**Answer: (a).**

**Q10.** n = 2. Exactly 1 face painted?
(a) 0 (b) 6 (c) 8 (d) 4
**Answer: (a).**

**Q11.** A painted cube is cut into 216 unit cubes. Exactly 2 faces painted?
(a) 96 (b) 48 (c) 54 (d) 60
**Answer: (b).** n = 6.

**Q12.** n = 4. Exactly 3 faces painted?
(a) 4 (b) 8 (c) 12 (d) 24
**Answer: (b).**

**Q13.** Standard die: 1 on top, 3 facing east. Roll it once to the east. New top?
(a) 3 (b) 4 (c) 6 (d) 2
**Answer: (b).**

**Q14.** Across several views, the faces adjacent to 1 are 2, 3, 4 and 5. Opposite 1?
(a) 2 (b) 6 (c) 4 (d) can't say
**Answer: (b).**

**Q15.** Minimum cuts to divide a cube into 64 equal cubes?
(a) 6 (b) 9 (c) 12 (d) 16
**Answer: (b).** 3 × (4 − 1).

**Q16.** Maximum pieces from 3 straight cuts of a cube?
(a) 6 (b) 7 (c) 8 (d) 9
**Answer: (c).**

**Q17.** n = 3. Cube with no painted face?
(a) 0 (b) 1 (c) 8 (d) 6
**Answer: (b).**
