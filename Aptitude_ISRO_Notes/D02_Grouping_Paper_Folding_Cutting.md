# D02. Grouping, Shape Construction, Paper Folding and Cutting

> Paper folding is **reflection in disguise**: every crease is a mirror. Unfold in **reverse order**, reflecting every hole across each crease, and the hole count doubles at each fold (unless a hole sits on a crease).

---

## 1. Grouping and classification

Check attributes in a fixed order so nothing is missed:
1. Number of sides / elements.
2. Number of closed regions.
3. Straight vs curved lines.
4. Open vs closed figure.
5. Symmetry.
6. Shading, size, orientation.

The odd one out breaks the property the others share.

## 2. Shape construction

Given pieces, which target can (or can't) be formed?
1. **Area check first:** total piece area must equal target area. Fastest elimination.
2. Then match edge lengths and corner angles.

Two identical right isosceles triangles can make a square, a larger right isosceles triangle, or a parallelogram, but **not** a regular pentagon.

## 3. Paper folding (punching holes)

**Method (work backwards):**
1. Start with the punched hole(s) on the final folded piece.
2. Unfold the **last** fold: reflect each hole across that crease.
3. Unfold the previous fold: reflect **all** holes so far.
4. Continue to the first fold.

| Folds | Holes (punch away from creases) |
|---|---|
| 1 | 2 |
| 2 | 4 |
| 3 | 8 |
| n | 2ⁿ |

**Exceptions:**
- A hole **on** a crease reflects onto itself: fold in half, cut a semicircle on the fold → **one** full circle.
- Folded into quarters and punched exactly at the corner where both creases meet (the centre of the original sheet) → **1** hole.

**Diagonal folds** reflect across the diagonal: the copy sits at the mirror position across that line.

## 4. Paper cutting

Same as folding, but a shape is cut. Fold a square into quarters, cut a notch at the free corner → **4** notches, one per quadrant, symmetric about both creases.

## 5. Dot situation

A dot sits inside some shapes and outside others. Record its full membership (inside A? inside B? inside C?) and find an answer figure that has a region with **exactly the same** set of conditions, including the "outside" ones.

---

## 6. Practice questions (with solutions)

**Q1.** Pentagon, hexagon, heptagon, octagon, and a triangle with one curved side. Odd one?
(a) pentagon (b) hexagon (c) curved triangle (d) octagon
**Answer: (c).**

**Q2.** Pieces of area 4, 6, 9, 5 for a target of area 20. Possible?
(a) yes (b) no, total 24 ≠ 20 (c) can't say (d) yes with overlap
**Answer: (b).**

**Q3.** Folded once, one hole punched away from the crease. Holes after unfolding?
(a) 1 (b) 2 (c) 4 (d) depends
**Answer: (b).**

**Q4.** Folded twice; a student reflects only across the last fold and gets 2 holes. Correct count?
(a) 2 (b) 4 (c) 8 (d) 3
**Answer: (b).**

**Q5.** Folded in half three times, one hole punched away from all creases. Holes?
(a) 3 (b) 6 (c) 8 (d) 16
**Answer: (c).**

**Q6.** A square folded into quarters is punched exactly at the folded corner where both creases meet (the centre of the original sheet). Holes?
(a) 1 (b) 2 (c) 4 (d) 8
**Answer: (a).**

**Q7.** Folded in half; a semicircle is cut on the folded edge. After unfolding?
(a) two semicircles (b) one full circle (c) two circles (d) one semicircle
**Answer: (b).**

**Q8.** A circle and a square overlap; a triangle overlaps only the square. The dot is inside the circle and the square but outside the triangle. Region?
(a) circle only (b) square only (c) circle ∩ square, outside triangle (d) triangle ∩ square
**Answer: (c).**

**Q9.** An answer figure has a "circle ∩ square" region, but that whole region is also inside the triangle. Valid match for the dot in Q8?
(a) yes (b) no, it must also be outside the triangle
**Answer: (b).**

**Q10.** A square folded into quarters; a notch cut from the free corner. Notches when unfolded?
(a) 2 (b) 4 (c) 8 (d) 1
**Answer: (b).**

**Q11.** A square folded once along a diagonal, then punched once away from the crease. Holes and arrangement?
(a) 1 (b) 2, symmetric about the diagonal (c) 2, symmetric about the vertical centre line (d) 4
**Answer: (b).**

**Q12.** Two identical right isosceles triangles cannot form:
(a) a square (b) a larger right isosceles triangle (c) a parallelogram (d) a regular pentagon
**Answer: (d).**

**Q13.** Odd one: triangle, square, pentagon, circle, hexagon.
(a) triangle (b) circle (c) hexagon (d) square
**Answer: (b).** Only figure without straight sides.

**Q14.** Odd one by number of sides: 3, 5, 7, 4, 9.
(a) 3 (b) 4 (c) 7 (d) 9
**Answer: (b).** The only even count.
