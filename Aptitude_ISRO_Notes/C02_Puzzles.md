# C02. Puzzle Solving

> Puzzles reward **process, not cleverness**. Write everything on a grid, place the fixed facts first, then let each new fact unlock the next clue. If two cases survive, keep both and let a later clue kill one.

---

## 1. The method

1. **Read every clue once** before writing.
2. Place the **direct** clues first ("R is on floor 4").
3. Apply **negative** clues ("S is not on floor 1") as eliminations.
4. Handle **relative** clues ("Q is immediately above P") by listing the possible positions.
5. After each new fact, **re-scan** all clues.
6. If stuck, **split into cases** and push each until one contradicts.

Use a grid: rows = people, columns = attributes (floor, colour, job...). Tick what's confirmed, cross what's ruled out.

> **Traps:** "above/below" vs "immediately above/below"; floor 1 = bottom unless stated otherwise; "more than" vs "less than" (underline the comparison word).

## 2. Common types

| Type | Clue style |
|---|---|
| Floors / stacking | "X lives above Y", "two floors between" |
| Linear row | "left of", "at an end", "neighbours" |
| Circular | covered in C06 |
| Comparison | "taller than", "heavier than" |
| Multi-attribute | name + colour + sport, etc. |

### Row counting

```
Total = position from left + position from right − 1
Position from right = Total − position from left + 1
```

---

## 3. Worked example: floors

P, Q, R, S on floors 1 (bottom) to 4. (1) Q lives immediately above P. (2) S is not on floor 1 or 4. (3) R is on floor 4.

- R = 4.
- S ∈ {2, 3}.
- (P, Q) ∈ {(1, 2), (2, 3)}.
- If (P, Q) = (2, 3), S must be 1. Contradiction. So **P = 1, Q = 2, S = 3, R = 4**.

## 4. Worked example: a row

Five friends A, B, C, D, E sit in a row facing north (positions 1 to 5 from the left).
(1) C is in the middle. (2) A sits at one end. (3) D is immediately to the right of C. (4) B is not adjacent to C. (5) A is not at the right end.

- C = 3, D = 4.
- B not adjacent to C → B ∉ {2, 4} → B ∈ {1, 5}.
- A at an end and not the right end → A = 1, so B = 5, E = 2.
- **Order: A, E, C, D, B.**

## 5. Worked example: multi-attribute

Amit, Bina, Chetan each like a different colour (red, blue, green) and play a different game (chess, tennis, cricket).
(1) Amit doesn't like red and doesn't play chess. (2) The blue-lover plays tennis. (3) Bina likes green. (4) Chetan plays chess.

- Bina = green. Amit ≠ red → Amit = blue → tennis. Chetan = red, chess. Bina = cricket.

| | Colour | Game |
|---|---|---|
| Amit | Blue | Tennis |
| Bina | Green | Cricket |
| Chetan | Red | Chess |

## 6. Worked example: comparison chains

A is taller than B but shorter than C. D is taller than C. E is shorter than B.
Chain: **D > C > A > B > E**.

---

## 7. Practice questions (with solutions)

**Q1.** Boxes A to E stacked with A on top, E at the bottom, in order. Directly below C?
(a) A (b) B (c) D (d) E
**Answer: (c).**

**Q2.** Ravi is 2nd from the left and 4th from the right. People in the row?
(a) 4 (b) 5 (c) 6 (d) 7
**Answer: (b).**

**Q3.** A is taller than B; C is shorter than B. Shortest?
(a) A (b) B (c) C (d) can't say
**Answer: (c).**

**Q4.** Boxes W, X, Y, Z (1 = top). Z is at the bottom; W is directly above X; Y is not at the top. Order from the top?
(a) W, X, Y, Z (b) X, W, Y, Z (c) W, Y, X, Z (d) Y, W, X, Z
**Answer: (a).** W = 2, X = 3 would force Y = 1.

**Q5.** A taller than B, shorter than C; D taller than C; E shorter than B. Tallest and shortest?
(a) C, B (b) D, E (c) D, B (d) A, E
**Answer: (b).**

*Q6 to Q9 use the floors example in section 3.*

**Q6.** Who lives on floor 3?
(a) P (b) Q (c) S (d) R
**Answer: (c).**

**Q7.** Who lives directly below R?
(a) P (b) Q (c) S (d) nobody
**Answer: (c).**

**Q8.** How many people live between P and R?
(a) 0 (b) 1 (c) 2 (d) 3
**Answer: (c).**

**Q9.** Who is on the lowest floor?
(a) P (b) Q (c) S (d) R
**Answer: (a).**

*Q10 to Q13 use the row example in section 4.*

**Q10.** Second from the left?
(a) A (b) E (c) C (d) D
**Answer: (b).**

**Q11.** At the right end?
(a) A (b) D (c) B (d) E
**Answer: (c).**

**Q12.** Who sits between C and B?
(a) E (b) A (c) D (d) nobody
**Answer: (c).**

**Q13.** How many people sit between A and D?
(a) 1 (b) 2 (c) 3 (d) 0
**Answer: (b).**

*Q14 to Q16 use the multi-attribute example in section 5.*

**Q14.** Who plays cricket?
(a) Amit (b) Bina (c) Chetan (d) can't say
**Answer: (b).**

**Q15.** Chetan's colour?
(a) Red (b) Blue (c) Green (d) can't say
**Answer: (a).**

**Q16.** The tennis player likes:
(a) red (b) blue (c) green (d) can't say
**Answer: (b).**

**Q17.** Rohan is 7th from the left and 12th from the right. Total?
(a) 18 (b) 19 (c) 20 (d) 17
**Answer: (a).**

**Q18.** 40 students in a row; X is 15th from the left. Position from the right?
(a) 25 (b) 26 (c) 24 (d) 27
**Answer: (b).**

**Q19.** P, Q, R, S, T live on floors 1 (bottom) to 5. T is on the top floor. P is directly below T. Q is on floor 1. R lives above S. Who is on floor 3?
(a) P (b) R (c) S (d) Q
**Answer: (b).** T = 5, P = 4, Q = 1; R above S among {2, 3} → R = 3, S = 2.

**Q20.** Same building. How many floors between Q and P?
(a) 1 (b) 2 (c) 3 (d) 0
**Answer: (b).** Floors 2 and 3.

**Q21.** Six people Z, Y, X, U, V, W sit in a row. Z is at the left end and W at the right end. X is third from the left. U is immediately left of V, and V is immediately left of W. Who is second from the left?
(a) U (b) Y (c) X (d) V
**Answer: (b).** Z = 1, X = 3, W = 6, V = 5, U = 4 → Y = 2.

**Q22.** In a class, M is heavier than N, N is heavier than O, and P is lighter than O. Who is the second lightest?
(a) M (b) N (c) O (d) P
**Answer: (c).** M > N > O > P.
