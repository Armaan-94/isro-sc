# C09. Clocks

> A clock is a **race between two runners on a circular track**. The minute hand runs at 6° per minute, the hour hand at 0.5° per minute, so the minute hand gains **5.5° every minute**. Every clock formula is this relative-speed idea.

---

## 1. Speeds

- Dial = 360°. Each hour mark is 30° apart; each minute mark 6°.
- Minute hand: **6°/min**. Hour hand: **0.5°/min** (30° per hour).
- Relative speed: **5.5°/min**. The minute hand is 12 times as fast.

## 2. Angle between the hands

```
Angle = |30H − 5.5M|      (H = hour 0–11, M = minutes)
If the result exceeds 180°, take 360° − result.
```

Why: hour hand is at 30H + 0.5M from 12; minute hand at 6M. Subtract.

| Time | Calculation | Angle |
|---|---|---|
| 7:00 | 210 → 360 − 210 | **150°** |
| 5:30 | \|150 − 165\| | **15°** |
| 8:40 | \|240 − 220\| | **20°** |
| 2:13 | \|60 − 71.5\| | **11.5°** |
| 3:15 | \|90 − 82.5\| | **7.5°** |
| 9:45 | \|270 − 247.5\| | **22.5°** |
| 4:20 | \|120 − 110\| | **10°** |
| 12:30 | \|0 − 165\| | **165°** |
| 10:10 | \|300 − 55\| = 245 → 360 − 245 | **115°** |
| 3:40 | \|90 − 220\| | **130°** |

> At x:30 the hour hand is halfway between marks, so 3:30 is **not** 90° (it's 75°).

## 3. When do the hands coincide, oppose, or meet at right angles?

Set the angle formula to the target and solve for M.

| Target | Equation | Between H and H + 1 |
|---|---|---|
| Coincide (0°) | 30H = 5.5M | M = 60H/11 |
| Opposite (180°) | 5.5M − 30H = 180 | |
| Right angle (90°) | 30H − 5.5M = ±90 | two solutions per hour (usually) |

- Coincide between 6 and 7: M = 360/11 = **32 8/11 min**.
- Opposite between 4 and 5: 5.5M = 300 → M = **54 6/11 min**.
- Right angle between 3 and 4 (other than 3:00): 5.5M = 180 → **3:32 8/11**.

### How often

| Event | Per 12 hours | Per day |
|---|---|---|
| Coincide | **11** | 22 |
| Opposite (straight line, opposite directions) | 11 | 22 |
| Straight line (coincide or opposite) | 22 | 44 |
| Right angle | **22** | **44** |

Hands coincide every **720/11 = 65 5/11 minutes**, not every 60. Between 11 and 1 o'clock they meet only once (at 12), which is why it's 11, not 12.

> A clock whose hands coincide every 64 minutes (instead of 65 5/11) is running **fast**.

## 4. Mirror and water images

```
Mirror image (mirror held at the side)  = 12:00 − time   (use 11:60 to subtract)
Water image (mirror below the clock)    = 18:30 − time   (use 17:90 to subtract)
```

- **Mirror:** a left-right flip sends minute m to 60 − m, so subtract from 12:00. 8:40 → **3:20**; 4:12 → **7:48**; 2:35 → **9:25**.
- **Water:** a top-bottom flip swaps 12 ↔ 6, 1 ↔ 5, 2 ↔ 4 (3 and 9 stay), sending minute m to 30 − m, so subtract from 18:30. 10:10 → 18:30 − 10:10 = **8:20** (the minute hand at "2" reflects to "4" = 20 min ✓).

(More practice in D01, Mirror and Water Images.)

## 5. Distance covered by the tips

Minute hand: 1 revolution per hour. Hour hand: 1 per 12 hours. Distance = revolutions × 2πr.
4 days (96 h), minute hand 8 cm, hour hand 7 cm: 96 × 16π + 8 × 14π = 1536π + 112π = **1648π cm**.

## 6. Faulty clocks (gaining or losing)

Set up a **proportion between clock minutes and real minutes**; don't just multiply hours by the error.

**Loses 6 min/hour** (54 clock minutes per 60 real minutes). Set right at Monday 10 AM. When it shows Friday 3 PM, the clock has counted 101 h = 6060 clock minutes. Real time = 6060 × 60/54 = 6733⅓ min = 112 h 13 min 20 s → **Saturday 2:13:20 AM**.
(The tempting wrong answer adds 101 × 6 minutes: Saturday 1:06 AM.)

**Uniform drift:** 2 min slow at noon Monday, 4 min 48 s fast at 2 PM the next Monday. Total gain 6.8 min over 170 h → 0.04 min/h. Correct when the 2-minute lag is recovered: 2/0.04 = 50 h → **2 PM Wednesday**.

**Percentage drift:** loses 1% in week 1, gains 2% in week 2 (168 h each): net +1.68 h = 1 h 40 min 48 s → shows **1:40:48** at real noon 14 days later.

---

## 7. Exam traps

1. Take the smaller angle (≤ 180°).
2. Coincidences: 11 per 12 hours, not 12.
3. Right angles: 22 per 12 hours, 44 per day.
4. Faulty clocks: proportion, not direct addition.

---

## 8. Practice questions (with solutions)

**Q1.** Angle at 7:00?
(a) 150° (b) 180° (c) 210° (d) 120°
**Answer: (a).**

**Q2.** Angle at 5:30?
(a) 45° (b) 30° (c) 15° (d) 60°
**Answer: (c).**

**Q3.** Angle at 2:13?
(a) 16.5° (b) 18° (c) 13.5° (d) 11.5°
**Answer: (d).**

**Q4.** Hands coincide between 6 and 7 at:
(a) 6:30 (b) 6:32 8/11 (c) 6:33 (d) 6:35 5/11
**Answer: (b).**

**Q5.** Times the hands coincide between 9 AM and 9 PM?
(a) 9 (b) 10 (c) 11 (d) 12
**Answer: (c).**

**Q6.** Times per day the hands point in exactly opposite directions?
(a) 20 (b) 22 (c) 24 (d) 48
**Answer: (b).**

**Q7.** Mirror image of 8:40?
(a) 3:20 (b) 2:30 (c) 8:20 (d) 9:20
**Answer: (a).**

**Q8.** Mirror image of 4:12?
(a) 8:48 (b) 9:48 (c) 7:48 (d) 7:52
**Answer: (c).**

**Q9.** Minute hand 8 cm, hour hand 7 cm, 4 days. Total distance by both tips?
(a) 1824π (b) 1724π (c) 1648π (d) 2028π cm
**Answer: (c).**

**Q10.** A clock set right at Monday 10 AM loses 6 min per hour. Actual time when it shows Friday 3 PM?
(a) Saturday 1:06 AM (b) Friday 4:54 AM (c) Saturday 2:13:20 AM (d) Friday 3:00 AM
**Answer: (c).**

**Q11.** Loses 1% in week 1, gains 2% in week 2; set right at Sunday noon. What does it show 14 days later?
(a) 1:40:48 (b) 1:36:48 (c) 1:41:24 (d) 10:19:12
**Answer: (a).**

**Q12.** 2 min slow at noon Monday, 4 min 48 s fast at 2 PM the next Monday. When was it correct?
(a) 2 PM Tuesday (b) 2 PM Wednesday (c) 3 PM Thursday (d) 1 PM Friday
**Answer: (b).**

**Q13.** Angle at 3:15?
(a) 0° (b) 7.5° (c) 15° (d) 22.5°
**Answer: (b).**

**Q14.** Angle at 10:10?
(a) 115° (b) 245° (c) 125° (d) 105°
**Answer: (a).**

**Q15.** Angle at 12:30?
(a) 180° (b) 165° (c) 150° (d) 195°
**Answer: (b).**

**Q16.** Right angle between 3 and 4 (other than 3:00)?
(a) 3:30 (b) 3:32 8/11 (c) 3:35 (d) 3:27 3/11
**Answer: (b).**

**Q17.** Hands opposite between 4 and 5 at:
(a) 4:50 (b) 4:54 6/11 (c) 4:52 (d) 4:56 4/11
**Answer: (b).**

**Q18.** Right angles in a day?
(a) 24 (b) 44 (c) 48 (d) 22
**Answer: (b).**

**Q19.** Interval between consecutive coincidences of a correct clock?
(a) 60 min (b) 65 min (c) 65 5/11 min (d) 66 min
**Answer: (c).**

**Q20.** A clock's hands coincide every 64 minutes. The clock is:
(a) correct (b) fast (c) slow (d) stopped
**Answer: (b).**

**Q21.** Water image of 10:10?
(a) 1:50 (b) 8:20 (c) 7:50 (d) 2:50
**Answer: (b).** 18:30 − 10:10.

**Q22.** Angle at 3:30?
(a) 90° (b) 75° (c) 60° (d) 105°
**Answer: (b).**
