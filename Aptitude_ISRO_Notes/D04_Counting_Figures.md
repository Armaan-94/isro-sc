# D04. Counting Figures (Squares, Rectangles, Triangles, Lines)

> Never count by staring. **Squares and rectangles have formulas** (a rectangle is just a choice of two horizontal and two vertical lines). **Triangles need a system**: count by size, from the smallest pieces up to the whole figure.

---

## 1. Squares in a grid

n × n grid of unit squares:

```
k × k squares: (n − k + 1)²
Total = 1² + 2² + ... + n² = n(n + 1)(2n + 1)/6
```

- 3 × 3: 9 + 4 + 1 = **14**.
- 4 × 4: 16 + 9 + 4 + 1 = **30**.
- 5 × 5: **55**. 8 × 8 chessboard: **204**.

**m × n grid (m ≤ n):** k × k squares = (m − k + 1)(n − k + 1), for k = 1 to m.
4 × 6: 24 + 15 + 8 + 3 = **50**. 2 × 3: 6 + 2 = **8**.

## 2. Rectangles in a grid

A rectangle = 2 of the (m + 1) horizontal lines × 2 of the (n + 1) vertical lines:

```
Rectangles (including squares) = C(m + 1, 2) × C(n + 1, 2)
```

- 3 × 4 grid: C(4,2) × C(5,2) = 6 × 10 = **60**.
- 2 × 3 grid: 3 × 6 = **18**; non-square rectangles = 18 − 8 = **10**.
- 3 × 3: 6 × 6 = **36**. 8 × 8: 36 × 36 = **1296**.
- 1 × 4 strip: 1 × 10 = **10**.

## 3. Triangles

### Systematic counting
1. Label every vertex/intersection.
2. Count the smallest triangles.
3. Count triangles made of 2 pieces, then 3, and so on.
4. Check the largest triangle (outer boundary) last.

### Useful patterns
- **Square with both diagonals:** 4 small + 4 half-squares = **8**.
- **Lines from the apex to the base:** if a triangle has k lines from the apex to the base (k + 2 lines from the apex in total, including both sides), triangles = **C(k + 2, 2)**. One internal line → 3. Three internal lines → C(5, 2) = **10**.
- **Two cevians crossing** (AD from A to BC, BE from B to AC, meeting at F): ABF, AFE, BFD, ABD, ADC, ABE, BEC, ABC = **8**. (Region FDCE is a quadrilateral.)

> Common miss: triangles formed by **one** internal line plus the outer sides (like ADC), not just the smallest pieces.

## 4. Lines and intersection points

n lines, no two parallel, no three concurrent: **C(n, 2)** intersection points.

- **k parallel lines:** subtract C(k, 2).
- **k lines through one point:** they give 1 point instead of C(k, 2), so subtract C(k, 2) − 1.

Examples:
- 5 lines → 10; 6 lines → 15.
- 7 lines, 3 parallel → 21 − 3 = **18**.
- 6 lines, 3 concurrent → 15 − 2 = **13**.
- 8 lines, 4 concurrent → 28 − 5 = **23**.

---

## 5. Practice questions (with solutions)

**Q1.** Squares in a 3 × 3 grid?
(a) 9 (b) 12 (c) 14 (d) 16
**Answer: (c).**

**Q2.** Squares in a 5 × 5 grid?
(a) 40 (b) 55 (c) 30 (d) 25
**Answer: (b).**

**Q3.** Rectangles (including squares) in a 2 × 3 grid?
(a) 12 (b) 15 (c) 18 (d) 24
**Answer: (c).**

**Q4.** Non-square rectangles in a 2 × 3 grid?
(a) 8 (b) 10 (c) 14 (d) 18
**Answer: (b).**

**Q5.** Intersection points of 5 lines (none parallel, no three concurrent)?
(a) 5 (b) 10 (c) 15 (d) 20
**Answer: (b).**

**Q6.** 7 lines, 3 of them parallel. Intersection points?
(a) 21 (b) 18 (c) 15 (d) 12
**Answer: (b).**

**Q7.** Squares in a 4 × 6 grid?
(a) 24 (b) 40 (c) 50 (d) 55
**Answer: (c).**

**Q8.** Triangle ABC with cevians AD and BE meeting inside. Triangles?
(a) 4 (b) 6 (c) 8 (d) 10
**Answer: (c).**

**Q9.** 8 lines, no two parallel, 4 of them concurrent at one point. Intersection points?
(a) 22 (b) 25 (c) 28 (d) 23
**Answer: (d).**

**Q10.** Squares on an 8 × 8 chessboard?
(a) 64 (b) 204 (c) 196 (d) 256
**Answer: (b).**

**Q11.** Rectangles on an 8 × 8 chessboard?
(a) 1296 (b) 204 (c) 784 (d) 1024
**Answer: (a).**

**Q12.** Triangles in a square with both diagonals drawn?
(a) 4 (b) 6 (c) 8 (d) 10
**Answer: (c).**

**Q13.** A triangle has 3 lines drawn from its apex to the base. Triangles?
(a) 6 (b) 8 (c) 10 (d) 4
**Answer: (c).**

**Q14.** Rectangles in a 3 × 3 grid?
(a) 14 (b) 36 (c) 27 (d) 49
**Answer: (b).**

**Q15.** Rectangles in a 1 × 4 strip of squares?
(a) 4 (b) 8 (c) 10 (d) 12
**Answer: (c).**

**Q16.** 6 lines, 3 concurrent, no other coincidences. Intersection points?
(a) 15 (b) 13 (c) 12 (d) 14
**Answer: (b).**
