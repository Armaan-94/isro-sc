# A09. Time and Work, Pipes and Cisterns

> **The single biggest trick:** set the **total work = LCM of the given times**. Then everyone's daily work becomes a whole number of "units", and every problem is just addition and division. Pipes and cisterns are the same problem with a minus sign for leaks.

---

## 1. The unit (LCM) method

If A finishes a job in a days, A does 1/a of it per day. Fractions get messy, so:

1. Let **total work = LCM** of all the given times.
2. Each person's **efficiency** (units/day) = total work / their time.
3. Combined efficiency = sum of efficiencies.
4. Time = total work / combined efficiency.

**Example:** A takes 12 days, B takes 6 days. Work = 12 units. A = 1/day, B = 2/day. Together 3/day → **4 days**.

Formula version for two people: **T = ab/(a + b)** = 72/18 = 4.

### Three people

A, B, C take a, b, c days: together = 1/(1/a + 1/b + 1/c).

### Pairs given

A + B: 10 days, B + C: 15 days, C + A: 12 days. Work = 60 units: (A + B) = 6, (B + C) = 4, (C + A) = 5 per day. Adding: 2(A + B + C) = 15 → A + B + C = 7.5 per day → all three take 60/7.5 = **8 days**. A alone = 7.5 − 4 = 3.5 per day → 60/3.5 = **17 1/7 days**.

---

## 2. Efficiency and time are inversely proportional

If A is **k times as efficient** as B, A takes **1/k** of B's time.
- A twice as efficient as B, B takes 18 days → A takes **9 days**.
- A is 50% more efficient than B (ratio 3 : 2), B takes 15 days → A takes 10 days; together 6 days.

Efficiency ratio m : n → time ratio **n : m**.

---

## 3. Man-days (the "MDH/W" formula)

Work done is proportional to (men × days × hours):

```
M₁ D₁ H₁ / W₁ = M₂ D₂ H₂ / W₂
```

20 men working 8 h/day for 15 days build 30 m of wall. How many days for 12 men at 10 h/day to build 24 m?
20 × 8 × 15/30 = 12 × 10 × D/24 → 80 = 5D → **D = 16**.

**Men, women, children:** convert everyone to one unit. If 2 men = 3 women, then 6 women = 4 men.

**Workers leaving midway:** 12 men can finish in 18 days. After 6 days, 4 men leave. Remaining work = 12 × 12 = 144 man-days; with 8 men: 18 more days → total **24 days**.

---

## 4. Work and wages

Wages are shared in the ratio of **work done**. If everyone works the same number of days, that's the **efficiency** ratio.

A (6 days), B (8 days), C (12 days) together earn ₹1800. Work = 24: efficiencies 4 : 3 : 2 → A gets 4/9 × 1800 = **₹800**.

A (6 days) and B (8 days) take a job for ₹720 and finish in 3 days with C's help. Work = 24; needed 8/day; C = 8 − 4 − 3 = 1/day → C gets 1/8 × 720 = **₹90**.

---

## 5. Joining, leaving, alternating

**Phases:** work in phase 1 + work in phase 2 = total.
A alone takes 20 days; works 4 days; then B joins and they finish in 8 more days. Work = 20, A = 1/day; after 4 days 16 left; together 16/8 = 2/day → B = 1/day → B alone **20 days**.

**One person leaves before the end:** A (18 days) and B (15 days). B works 10 days and leaves; A finishes the rest. B did 10/15 = 2/3 → 1/3 left → A needs 18/3 = **6 days**.

**Alternate days:** compute one 2-day cycle, count full cycles, handle the remainder.
- A 4 days, B 12 days, alternately starting with A: work 12; A = 3, B = 1; a cycle (2 days) = 4 units → 3 cycles = 12 units → **6 days**.
- A 6 days, B 9 days, alternately starting with A: work 18; A = 3, B = 2; cycle = 5 units in 2 days; 3 cycles = 15 units in 6 days; 3 left → A does it on day 7 → **7 days**.

---

## 6. The "extra time" identity

If A alone takes **x days more** than A and B together, and B alone takes **y days more** than together, then

```
time together = √(xy)
```

x = 6, y = 24 → √144 = **12 days**.

---

## 7. Pipes and cisterns

- **Inlet** fills: positive rate. **Outlet / leak** empties: **negative** rate.
- Same LCM method.

Inlet fills in 6 h, outlet empties in 10 h: work 30; +5 and −3 → net 2/h → **15 h**.

A fills in 4 h, B in 6 h, leak empties in 12 h: work 12; 3 + 2 − 1 = 4/h → **3 h**. (Forgetting the leak gives 2.4 h.)

**Leak from a delay:** a pipe fills in 3 h, but due to a leak takes 3.5 h. Work 21: pipe 7/h, with leak 6/h → leak 1/h → leak empties a full tank in **21 h**.

**Never fills:** if the net rate is ≤ 0, the tank never fills. (10 h and 15 h fill, 6 h empties: work 30 → 3 + 2 − 5 = 0.)

**Closing a pipe midway:** A fills in 20 min, B in 30 min; both start, and B is closed so the tank fills in exactly 15 min. Work 60; A alone over 15 min does 45; B must do 15 → B ran **7.5 min**.

---

## 8. Exam traps

1. Efficiency ∝ 1/time.
2. Wages by work done, not equally.
3. Leaks subtract.
4. Alternate-day problems: the final partial day may be the second person's or first person's.
5. Check whether the question asks for **additional** days or **total** days.

---

## 9. Practice questions (with solutions)

**Q1.** A takes 12 days, B 6 days. Together?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (b).**

**Q2.** A is twice as efficient as B; B alone takes 18 days. A alone?
(a) 36 (b) 9 (c) 27 (d) 6
**Answer: (b).**

**Q3.** A (6 days), B (8 days), C (12 days) together earn ₹1800. A's share?
(a) ₹600 (b) ₹800 (c) ₹900 (d) ₹400
**Answer: (b).**

**Q4.** A takes 20 days; after 4 days B joins and they finish in 8 days. B alone?
(a) 15 (b) 16 (c) 20 (d) 24
**Answer: (c).**

**Q5.** Inlet fills in 6 h, outlet empties in 10 h. Both open: time to fill?
(a) 12 h (b) 15 h (c) 8 h (d) 16 h
**Answer: (b).**

**Q6.** Pipes A (4 h) and B (6 h) fill; C empties in 12 h. All open: time?
(a) 3 h (b) 2.4 h (c) 4 h (d) 6 h
**Answer: (a).**

**Q7.** A (6 days) and B (8 days) take a job for ₹720 and finish in 3 days with C. C's share?
(a) ₹90 (b) ₹180 (c) ₹60 (d) ₹120
**Answer: (a).**

**Q8.** A alone takes 6 days more than A + B together; B alone takes 24 days more. Together?
(a) 8 (b) 12 (c) 10 (d) 6
**Answer: (b).**

**Q9.** Efficiencies of A, B, C are 5 : 4 : 3; together 4 days. C alone?
(a) 10 (b) 16 (c) 9 (d) 15
**Answer: (b).** Work 48; C = 3/day.

**Q10.** A and B together take 12 days; A alone 20 days. B alone?
(a) 25 (b) 30 (c) 32 (d) 40
**Answer: (b).** Work 60: A + B = 5, A = 3 → B = 2 → 30.

**Q11.** A + B: 10 days, B + C: 15 days, C + A: 12 days. All three together?
(a) 6 (b) 8 (c) 9 (d) 10
**Answer: (b).**

**Q12.** 10 men finish a job in 12 days. 15 men take?
(a) 6 (b) 8 (c) 9 (d) 18
**Answer: (b).**

**Q13.** 20 men, 8 h/day, 15 days build 30 m. 12 men, 10 h/day build 24 m in how many days?
(a) 12 (b) 15 (c) 16 (d) 18
**Answer: (c).**

**Q14.** A (10 days) and B (15 days) work on alternate days starting with A. Days to finish?
(a) 11 (b) 12 (c) 13 (d) 12.5
**Answer: (b).** Work 30; cycle 3 + 2 = 5 in 2 days; 6 cycles = 12 days.

**Q15.** A (4 days) and B (12 days) alternate, starting with A. Days?
(a) 5 (b) 6 (c) 7 (d) 8
**Answer: (b).**

**Q16.** A (6 days) and B (9 days) alternate, starting with A. Days?
(a) 6 (b) 7 (c) 7.5 (d) 8
**Answer: (b).**

**Q17.** Pipes A (20 min) and B (30 min) start together. After how long must B be closed so the tank fills in 15 min?
(a) 5 min (b) 7.5 min (c) 10 min (d) 12 min
**Answer: (b).**

**Q18.** A pipe fills a tank in 3 h; with a leak it takes 3.5 h. The leak alone empties the full tank in:
(a) 7 h (b) 10.5 h (c) 21 h (d) 24 h
**Answer: (c).**

**Q19.** 2 men = 3 women in work. 4 men finish a job in 15 days. 6 women take?
(a) 10 (b) 15 (c) 20 (d) 22.5
**Answer: (b).**

**Q20.** A is 50% more efficient than B; B alone takes 15 days. Together?
(a) 5 (b) 6 (c) 7.5 (d) 9
**Answer: (b).**

**Q21.** A (18 days) and B (15 days). B works 10 days and leaves. A finishes the rest in:
(a) 5 (b) 6 (c) 8 (d) 9
**Answer: (b).**

**Q22.** 12 men can finish a job in 18 days. After 6 days, 4 men leave. Total days taken?
(a) 21 (b) 24 (c) 27 (d) 30
**Answer: (b).**

**Q23.** A (10 days) and B (15 days) earn ₹3000 working together. A's share?
(a) ₹1200 (b) ₹1500 (c) ₹1800 (d) ₹2000
**Answer: (c).** Efficiency 3 : 2.

**Q24.** Pipes fill in 10 h and 15 h; a third empties in 6 h. All open. The tank:
(a) fills in 30 h (b) fills in 10 h (c) never fills (d) fills in 6 h
**Answer: (c).**

**Q25.** A does half as much work as B in three-fourths of the time. Together they take 18 days. B alone?
(a) 27 (b) 30 (c) 36 (d) 45
**Answer: (b).** A's rate = (1/2)/(3/4) = 2/3 of B's; together 5/3 B → B alone 18 × 5/3.

**Q26.** 6 men and 8 boys finish a job in 10 days; 26 men and 48 boys in 2 days. 15 men and 20 boys take?
(a) 3 (b) 4 (c) 5 (d) 6
**Answer: (b).** 1 man = 2 boys; work = 200 boy-days; 15m + 20b = 50 boys.

**Q27.** A tap fills a tank in 8 h; another empties it in 16 h. Both open on an empty tank, how long to fill?
(a) 8 h (b) 12 h (c) 16 h (d) 24 h
**Answer: (c).** Work 16: +2 − 1 = 1/h.

**Q28.** A can do 1/3 of a job in 5 days and B can do 2/5 of it in 10 days. Together they finish the whole job in:
(a) 8 1/3 days (b) 9 3/8 days (c) 10 days (d) 7 1/2 days
**Answer: (b).** A: 15 days; B: 25 days; together 15 × 25/40 = 375/40 = 9.375.
