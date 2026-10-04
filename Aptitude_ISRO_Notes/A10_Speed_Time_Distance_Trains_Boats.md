# A10. Speed, Time and Distance (Trains, Boats and Streams)

> **Everything flows from D = S × T**, plus one idea: **relative speed** (how fast the gap between two moving things changes). Trains, boats, chases and meetings are all relative speed in different costumes.

---

## 1. Core relation and units

```
Distance = Speed × Time
```

- km/h → m/s: **× 5/18**. (72 km/h = 20 m/s; 90 km/h = 25 m/s; 54 km/h = 15 m/s; 36 km/h = 10 m/s.)
- m/s → km/h: **× 18/5**.

### Proportionality

- Same distance: **time ∝ 1/speed**. Speed ratio 3 : 4 → time ratio 4 : 3.
- Same time: distance ∝ speed.

**Late/early problems:** walking at 3/4 of the usual speed, a man is 20 minutes late. New time = 4/3 × usual, so the extra 1/3 × usual = 20 → usual time **60 min**.

At 5 km/h a man is 7 minutes late; at 6 km/h he is 5 minutes early. Time difference 12 min = 1/5 h: d/5 − d/6 = 1/5 → d/30 = 1/5 → **d = 6 km**.

---

## 2. Average speed

```
Average speed = Total distance / Total time         (always correct)
```

| Situation | Shortcut |
|---|---|
| **Equal distances** at speeds x and y | **2xy/(x + y)** (harmonic mean) |
| Equal distances at n speeds | n / (1/v₁ + 1/v₂ + ... + 1/vₙ) |
| **Equal times** at speeds x and y | **(x + y)/2** |
| Anything else | Total distance / total time |

- To college at 6 km/h, back at 4 km/h → 2 × 6 × 4/10 = **4.8 km/h**.
- 40 km/h and 60 km/h over equal distances → **48 km/h** (not 50).
- Half the **time** at 40 and half at 60 → **50 km/h**.
- Thirds of the distance at 60, 75, 45 → 3/(1/60 + 1/75 + 1/45) = 3 × 900/47 = **57.45 km/h**.
- 10 km at 5, 30 km at 6, 20 km at 10: time = 2 + 5 + 2 = 9 h → 60/9 = **6.67 km/h**.

> **Trap:** the simple average only works for equal **times**.

---

## 3. Relative speed

| Directions | Relative speed |
|---|---|
| Same | **|S₁ − S₂|** |
| Opposite | **S₁ + S₂** |

**Meeting:** two people 60 km apart walk toward each other at 4 and 6 km/h → meet after 60/10 = **6 h**.

**Chasing:** a thief at 8 km/h is 100 m ahead of a policeman at 10 km/h. Relative 2 km/h = 5/9 m/s → caught after 100 ÷ 5/9 = **180 s**. The thief runs 8 km/h × 3 min = **400 m** meanwhile.

**Late start:** stations 300 km apart; train 1 leaves A at 7:00 at 50 km/h; train 2 leaves B at 8:00 at 100 km/h. By 8:00 the gap is 250 km, closing at 150 km/h → 1 h 40 min → meet at **9:40**.

**Meeting point difference:** trains at 54 and 72 km/h (15 and 20 m/s) toward each other; at the meeting one has gone 80 m more: 5t = 80 → t = 16 s → AB = 35 × 16 = **560 m**.

### Circular tracks

Track length L, speeds a > b:
- Same direction: first meeting after **L/(a − b)**.
- Opposite directions: first meeting after **L/(a + b)**.

500 m track, 15 and 10 m/s: same direction 100 s; opposite 20 s.

---

## 4. Trains

The **length** of the train counts as distance.

| Case | Time |
|---|---|
| Crosses a pole / standing man / signal | L / S |
| Crosses a platform, bridge, tunnel of length P | (L + P) / S |
| Crosses a moving man (same direction) | L / (S − s) |
| Crosses a moving man (opposite) | L / (S + s) |
| Two trains, opposite directions | (L₁ + L₂)/(S₁ + S₂) |
| Two trains, same direction | (L₁ + L₂)/(S₁ − S₂) |

**Examples:**
- 120 m train crosses a pole in 6 s → 20 m/s = **72 km/h**.
- 150 m train crosses a 250 m platform in 20 s → 400/20 = 20 m/s = **72 km/h**.
- Trains of 100 m and 150 m, opposite directions at 30 and 20 km/h: relative 50 km/h = 125/9 m/s → 250 × 9/125 = **18 s**.
- Same idea, same direction 60 and 40 km/h, lengths 140 and 160 m: relative 20 km/h = 50/9 m/s → 300 × 9/50 = **54 s**.
- 110 m train at 60 km/h passes a man running at 6 km/h in the opposite direction: relative 66 km/h = 55/3 m/s → **6 s**.
- Train crosses a pole in 15 s and a 100 m platform in 25 s: L/15 = (L + 100)/25 → **L = 150 m**.
- Two equal trains cross a man in 6 s and 8 s. Crossing each other: opposite = 2L/(L/6 + L/8) = **48/7 s**; same direction = 2L/(L/6 − L/8) = **48 s**.

### Stoppages

Average speed without stops S, with stops S'. Stoppage time per hour = **(S − S')/S × 60 min**. 54 and 45 km/h → 9/54 × 60 = **10 min/h**.

---

## 5. Boats and streams

Boat speed in still water **b**, stream speed **s**:

```
Downstream D = b + s          Upstream U = b − s
b = (D + U)/2                 s = (D − U)/2
```

- Downstream 14, upstream 5 → b = **9.5**, s = 4.5.
- 24 km upstream in 6 h (U = 4), back in 4 h (D = 6) → b = **5**, s = **1**.
- b = 15, s = 3: 36 km up and back = 36/12 + 36/18 = **5 h**.
- b = 10, s = 2, round trip takes 5 h: d/12 + d/8 = 5 → **d = 24 km**.

**Classic:** a man rows 48 km and back in 14 h. He rows 4 km downstream in the time he rows 3 km upstream. So D : U = 4 : 3 → 48/4k + 48/3k = 14 → k = 2 → D = 8, U = 6 → stream **1 km/h**.

Round-trip average speed = 2DU/(D + U) (equal distances).

---

## 6. Exam traps

1. Convert units before mixing m and km/h.
2. Average speed: harmonic mean for equal distances only.
3. Train lengths add to the distance; the pole/man has zero length.
4. Same direction subtract speeds; opposite add.
5. Boat: b and s are half-sum and half-difference.

---

## 7. Practice questions (with solutions)

**Q1.** 90 km/h in m/s?
(a) 20 (b) 25 (c) 30 (d) 15
**Answer: (b).**

**Q2.** A 120 m train crosses a pole in 6 s. Speed?
(a) 60 km/h (b) 72 km/h (c) 80 km/h (d) 20 km/h
**Answer: (b).**

**Q3.** A to B at 40 km/h, back at 60 km/h. Average speed?
(a) 50 (b) 48 (c) 52 (d) 45
**Answer: (b).**

**Q4.** A 150 m train crosses a 250 m platform in 20 s. Speed?
(a) 54 (b) 60 (c) 72 (d) 45 km/h
**Answer: (c).**

**Q5.** Trains 100 m and 150 m, opposite directions, 30 and 20 km/h. Time to cross?
(a) 18 s (b) 20 s (c) 15 s (d) 10 s
**Answer: (a).**

**Q6.** 24 km upstream in 6 h, back in 4 h. Boat and stream speeds?
(a) 5, 1 (b) 4, 1 (c) 5, 2 (d) 6, 1
**Answer: (a).**

**Q7.** Half the **time** at 40 km/h and half at 60 km/h. Average speed?
(a) 48 (b) 50 (c) 52 (d) 46
**Answer: (b).**

**Q8.** Train 1 leaves A at 7:00 at 50 km/h; train 2 leaves B (300 km away) at 8:00 at 100 km/h. Meeting time?
(a) 9:00 (b) 9:40 (c) 9:20 (d) 10:00
**Answer: (b).**

**Q9.** To college at 6 km/h, back at 4 km/h. Average speed?
(a) 5 (b) 4.8 (c) 4.5 (d) 5.2
**Answer: (b).**

**Q10.** 10 km at 5 km/h, 30 km at 6 km/h, 20 km at 10 km/h. Average speed?
(a) 7 (b) 6.67 (c) 6 (d) 7.5
**Answer: (b).**

**Q11.** Trains at 54 and 72 km/h toward each other; at the meeting one has gone 80 m more. Distance between the stations?
(a) 480 m (b) 560 m (c) 640 m (d) 700 m
**Answer: (b).**

**Q12.** Two equal trains cross a man in 6 s and 8 s. Time to cross each other moving in the same direction?
(a) 24 s (b) 48 s (c) 48/7 s (d) 14 s
**Answer: (b).**

**Q13.** Downstream 14 km/h, upstream 5 km/h. Boat speed in still water?
(a) 9 (b) 9.5 (c) 4.5 (d) 19
**Answer: (b).**

**Q14.** Boat 10 km/h, stream 2 km/h; a round trip takes 5 h. One-way distance?
(a) 20 km (b) 24 km (c) 25 km (d) 30 km
**Answer: (b).**

**Q15.** A 110 m train at 60 km/h passes a man running at 6 km/h in the opposite direction. Time?
(a) 5 s (b) 6 s (c) 7 s (d) 10 s
**Answer: (b).**

**Q16.** Without stoppages 54 km/h; with stoppages 45 km/h. Minutes stopped per hour?
(a) 9 (b) 10 (c) 12 (d) 15
**Answer: (b).**

**Q17.** At 3/4 of his usual speed a man is 20 min late. Usual time?
(a) 40 min (b) 60 min (c) 75 min (d) 80 min
**Answer: (b).**

**Q18.** Two people 60 km apart walk toward each other at 4 and 6 km/h. They meet after:
(a) 5 h (b) 6 h (c) 10 h (d) 12 h
**Answer: (b).**

**Q19.** A thief 100 m ahead runs at 8 km/h; a policeman chases at 10 km/h. Distance the thief runs before being caught?
(a) 300 m (b) 400 m (c) 500 m (d) 450 m
**Answer: (b).**

**Q20.** A train crosses a pole in 15 s and a 100 m platform in 25 s. Train length?
(a) 100 m (b) 125 m (c) 150 m (d) 200 m
**Answer: (c).**

**Q21.** Trains 140 m and 160 m, same direction, 60 and 40 km/h. Time for the faster to pass the slower?
(a) 30 s (b) 45 s (c) 54 s (d) 60 s
**Answer: (c).**

**Q22.** 32 km downstream in 4 h and 24 km upstream in 4 h. Stream speed?
(a) 1 (b) 2 (c) 3 (d) 4 km/h
**Answer: (a).** D = 8, U = 6.

**Q23.** Boat 15 km/h, stream 3 km/h. Time to go 36 km upstream and come back?
(a) 4 h (b) 4.5 h (c) 5 h (d) 6 h
**Answer: (c).**

**Q24.** A man rows 48 km and back in 14 h; he rows 4 km downstream in the time he rows 3 km upstream. Stream speed?
(a) 1 km/h (b) 1.5 km/h (c) 2 km/h (d) 0.5 km/h
**Answer: (a).**

**Q25.** At 5 km/h a man is 7 min late; at 6 km/h he is 5 min early. Distance?
(a) 5 km (b) 6 km (c) 7 km (d) 8 km
**Answer: (b).**

**Q26.** A bird flies 400 km: 100 km each at 100, 200, 300, 400 km/h. Average speed?
(a) 250 (b) 192 (c) 200 (d) 180 km/h
**Answer: (b).** Time = 1 + 0.5 + 0.333 + 0.25 = 2.083 h.

**Q27.** A car goes 8, 6 and 16 km in three successive quarter-hours. Average speed?
(a) 30 (b) 35 (c) 40 (d) 45 km/h
**Answer: (c).**

**Q28.** On a 500 m circular track, runners at 15 m/s and 10 m/s start together in opposite directions. First meeting after:
(a) 20 s (b) 50 s (c) 100 s (d) 25 s
**Answer: (a).**

**Q29.** Speeds of A and B are in ratio 3 : 4; A takes 30 minutes more than B for the same distance. B's time?
(a) 60 min (b) 90 min (c) 120 min (d) 75 min
**Answer: (b).** Times 4x and 3x; x = 30.

**Q30.** One-third of a distance at 60, one-third at 75 and one-third at 45 km/h. Average speed?
(a) 60 (b) 57.45 (c) 55 (d) 58.5
**Answer: (b).**
