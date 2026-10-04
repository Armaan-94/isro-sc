# D01. Mirror Images, Water Images and Rotation

> Two reflections, two axes. A **mirror** stands beside the object: left and right swap, top and bottom stay. **Water** lies below the object: top and bottom swap, left and right stay. Rotation is neither: it turns the figure without flipping it.

---

## 1. Mirror image (vertical mirror)

- **Left-right flip.** Top/bottom unchanged.
- For a word: the **letter order reverses** and every letter is flipped.
- Letters that look **unchanged** in a mirror (vertical line of symmetry):

```
A  H  I  M  O  T  U  V  W  X  Y      ("AHIMOTUVWXY")
```

- So **MOM**, **TOOT**, **WOW** look identical in a mirror (symmetric letters + palindrome).
- Digits 0, 1, 8 look the same in most fonts.

## 2. Water image (horizontal mirror)

- **Top-bottom flip.** Left/right unchanged; the **letter order stays**.
- Letters unchanged in water (horizontal line of symmetry):

```
B  C  D  E  H  I  K  O  X      ("BCDEHIKOX")
```

- So **BOX**, **CODE**, **HIDE** look identical in water.

**Only H, I, O, X** survive both reflections.

## 3. Rotation

- Rotation **keeps handedness** (a right hand stays a right hand); reflection reverses it.
- **180° rotation = mirror + water** (in either order).
- 90° clockwise = 270° anticlockwise.
- Letters unchanged by 180° rotation (point symmetry): **H, I, N, O, S, X, Z**. N, S, Z change under both mirror and water but survive rotation.

**Method for figures:** track 2 or 3 distinctive features (a notch, a dot, the longest side) and move each one; don't try to move the whole picture at once.

## 4. Clock reflections

```
Mirror image time = 12:00 − time   (write 12:00 as 11:60)
Water image time  = 18:30 − time   (write 18:30 as 17:90)
```

- Mirror of 3:50 → **8:10**. Mirror of 9:20 → **2:40**. Mirror of 12:20 → **11:40**.
- Water of 4:20 → 14:10 → **2:10**. Water of 2:15 → **4:15**. Water of 9:20 → **9:10**.

Why 18:30 for water? A top-bottom flip swaps 12 ↔ 6, 1 ↔ 5, 2 ↔ 4 and fixes 3 and 9, so minute m becomes 30 − m. Check 4:20: the minute hand at "4" reflects to "2" (10 minutes) ✓.

---

## 5. Exam traps

1. Mirror reverses letter order; water doesn't.
2. The two symmetric-letter lists differ.
3. Rotation ≠ reflection.
4. Mirror uses 12:00; water uses 18:30.

---

## 6. Practice questions (with solutions)

**Q1.** Which letter looks the same in a mirror?
(a) B (b) E (c) H (d) S
**Answer: (c).**

**Q2.** Which letter looks the same in a water image?
(a) F (b) D (c) N (d) Z
**Answer: (b).**

**Q3.** Unchanged under both mirror and water reflections?
(a) A (b) O (c) T (d) C
**Answer: (b).**

**Q4.** Mirror image of the word TOOT?
(a) TOOT (b) TUOT (c) a reversed, different-looking word (d) can't say
**Answer: (a).**

**Q5.** Mirror image of a clock showing 3:50?
(a) 8:10 (b) 7:10 (c) 9:10 (d) 8:50
**Answer: (a).**

**Q6.** Water image of a clock showing 2:15?
(a) 3:45 (b) 4:15 (c) 9:45 (d) 4:45
**Answer: (b).** 18:30 − 2:15 = 16:15.

**Q7.** A clock shows 9:20. Mirror and water images?
(a) 2:40 and 9:10 (b) 2:40 and 8:40 (c) 3:40 and 9:10 (d) 2:40 and 3:10
**Answer: (a).**

**Q8.** Water image of FOX:
(a) order reverses; all change (b) order kept; O and X unchanged, F changes (c) order kept; all unchanged (d) order reverses; only O unchanged
**Answer: (b).**

**Q9.** A 180° rotation equals:
(a) mirror only (b) water only (c) mirror then water (d) nothing
**Answer: (c).**

**Q10.** N looks the same after:
(a) all three (b) only a 180° rotation (c) none (d) mirror and water only
**Answer: (b).**

**Q11.** Which word looks identical in a mirror?
(a) DAD (b) MOM (c) BOB (d) NUN
**Answer: (b).**

**Q12.** Which word looks identical in a water image?
(a) BOX (b) TOY (c) MAX (d) HAT
**Answer: (a).**

**Q13.** Water image of 4:20?
(a) 1:40 (b) 2:10 (c) 7:40 (d) 2:40
**Answer: (b).**

**Q14.** Mirror image of 12:20?
(a) 11:40 (b) 12:40 (c) 11:20 (d) 6:40
**Answer: (a).**

**Q15.** Which letter has point (180°) symmetry but no mirror or water symmetry?
(a) O (b) S (c) H (d) X
**Answer: (b).**

**Q16.** How many capital letters remain unchanged under both a mirror and a water reflection?
(a) 2 (b) 4 (c) 6 (d) 11
**Answer: (b).** H, I, O, X.

**Q17.** A figure is rotated 90° clockwise three times. Equivalent single move?
(a) 90° clockwise (b) 90° anticlockwise (c) 180° (d) no change
**Answer: (b).** 270° clockwise = 90° anticlockwise.

**Q18.** Which statement is true?
(a) A mirror image can always be obtained by rotation (b) Rotation preserves handedness; reflection reverses it (c) Water image reverses letter order (d) Mirror images keep left and right
**Answer: (b).**
