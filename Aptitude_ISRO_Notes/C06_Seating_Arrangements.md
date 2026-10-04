# C06. Seating Arrangements

> The whole chapter hinges on **whose left and right**. Fix one viewpoint, convert every clue into that viewpoint, then place people like a puzzle (C02).

---

## 1. Left and right

### In a row
- Facing **north** (towards the top of your page): their left/right = **your** left/right.
- Facing **south**: their left/right is **mirrored** (their right is your left).

### Around a circle

Picture someone at 6 o'clock facing the centre (north). Their right hand points to 3 o'clock. Going 6 → 3 is **anticlockwise**.

| Facing | Right neighbour | Left neighbour |
|---|---|---|
| **Centre** | anticlockwise | clockwise |
| **Outward** | clockwise | anticlockwise |

> If people face different ways, apply the rule **per person**.

### Double rows facing each other
People in the same column are "opposite". The two rows face opposite ways, so one row's right is the other row's left.

## 2. Method

1. Draw the seat template (numbered seats, row ends, or a circle).
2. Place **absolute** clues ("at an end", "opposite", "middle").
3. Apply **relative** clues using the correct left/right rule.
4. Apply **negative** clues to choose among cases.
5. Re-scan clues after each placement.

---

## 3. Worked example: circle facing the centre

Six people A to F sit around a circular table facing the centre. Number seats 1 to 6 **clockwise**.
(1) A and D are opposite. (2) B is to A's immediate right. (3) C is to D's immediate left. (4) E is not adjacent to A.

- A = 1, D = 4.
- B = A's right = anticlockwise from 1 = **6**.
- C = D's left = clockwise from 4 = **5**.
- E, F take 2 and 3; E not adjacent to A (A's neighbours are 2 and 6) → E = **3**, F = **2**.

Clockwise: **A, F, E, D, C, B**.

## 4. Worked example: circle facing outward

Six friends A to F face **outward**. E is between B and C. B and F are opposite. A is to B's immediate right.

- B = 1, F = 4.
- A = B's right = clockwise from 1 = **2**.
- E is between B and C, so E is B's other neighbour, **6**, and C = **5**. D = **3**.

Clockwise: **B, A, D, F, C, E**.
- D's immediate right (clockwise from 3) = **F**.
- A's neighbours: **B and D**.

## 5. Worked example: double row

Row 1 (top of the page) faces **south**; row 2 (bottom) faces **north**. Columns 1 to 3 from the observer's left.
(1) Q sits in the middle of row 2. (2) P is to Q's immediate right. (3) B faces Q. (4) A is to B's immediate right. Row 1 is A, B, C; row 2 is P, Q, R.

- Q = column 2 (row 2). Row 2 faces north, so Q's right is the observer's right → P = 3, R = 1.
- B faces Q → B = column 2 (row 1).
- Row 1 faces south, so B's right is the observer's **left** → A = column 1, C = column 3.

| | Col 1 | Col 2 | Col 3 |
|---|---|---|---|
| Row 1 (facing south) | A | B | C |
| Row 2 (facing north) | R | Q | P |

---

## 6. Practice questions (with solutions)

**Q1.** In a row facing north, P is immediately left of Q and Q immediately left of R. P relative to R?
(a) immediately right (b) two places to the left (c) immediately left (d) can't say
**Answer: (b).**

**Q2.** A faces north, B faces south, in the same row. C is on A's right. Is C on B's right?
(a) yes (b) no, it's on B's left (c) can't say (d) only if C is between them
**Answer: (b).**

**Q3.** Six friends in a row facing north. Q is 2nd from the left. T is immediately left of Q. Who is at the left end?
(a) T (b) P (c) U (d) can't say
**Answer: (a).**

**Q4.** People around a table switch from facing the centre to facing outward. A was B's immediate right. Is A still B's immediate right?
(a) yes (b) no, A is now B's immediate left (c) only if others move (d) can't say
**Answer: (b).** Seats don't change; the meaning of "right" flips.

*Q5 to Q8 use section 3 (facing the centre).*

**Q5.** Who sits between A and E?
(a) B (b) F (c) D (d) C
**Answer: (b).**

**Q6.** Who is opposite F?
(a) B (b) C (c) D (d) E
**Answer: (b).** Seat 2 ↔ seat 5.

**Q7.** Who is to B's immediate left?
(a) A (b) C (c) F (d) D
**Answer: (a).** Left = clockwise from 6 → 1.

**Q8.** Who is to E's immediate right?
(a) D (b) F (c) A (d) C
**Answer: (b).** Right = anticlockwise from 3 → 2.

*Q9 to Q11 use section 4 (facing outward).*

**Q9.** Who is to D's immediate right?
(a) A (b) F (c) C (d) E
**Answer: (b).**

**Q10.** A's neighbours?
(a) B and E (b) B and D (c) D and F (d) C and E
**Answer: (b).**

**Q11.** B and D swap seats. Who is now to F's immediate right?
(a) C (b) B (c) A (d) E
**Answer: (a).** Clockwise from seat 4 is seat 5 = C, unaffected by the swap.

*Q12 to Q14 use section 5 (double row).*

**Q12.** Who faces A?
(a) P (b) Q (c) R (d) C
**Answer: (c).**

**Q13.** Who faces P?
(a) A (b) B (c) C (d) R
**Answer: (c).**

**Q14.** Who is at C's immediate right?
(a) B (b) nobody (C is at C's own right end) (c) A (d) P
**Answer: (b).** C faces south at the observer's column 3; C's right is towards column 4, which doesn't exist.

**Q15.** At a square table, four people sit at the corners facing the centre and four at the edge midpoints facing outward. Q (edge, facing outward) has P immediately clockwise. Is P on Q's left or right?
(a) left (b) right (c) depends on P's facing (d) can't say
**Answer: (b).** Facing outward: right = clockwise.

**Q16.** 8 people around a circle facing the centre. X sits 3rd to the right of Y. How many sit between them on the other side?
(a) 2 (b) 3 (c) 4 (d) 5
**Answer: (c).** 2 between on one side; 8 − 2 − 2 = 4 on the other.

**Q17.** In a row of 7 facing north, M is 3rd from the right. Position from the left?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (c).** 7 − 3 + 1.
