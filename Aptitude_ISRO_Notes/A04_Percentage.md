# A04. Percentage

> **The most important chapter in quant.** Profit and loss, interest, data interpretation, mixtures: all are percentages in disguise. Two skills make you fast: (1) **instant fraction-percentage conversion**, and (2) the **multiplying factor** idea (an increase of 20% means "multiply by 1.2").

---

## 1. Meaning

**Percent = per hundred.** x% = x/100.

- Fraction → % : multiply by 100. (3/8 = 37.5%)
- % → fraction: divide by 100. (35% = 7/20)
- **x% of y = y% of x.** (8% of 50 = 50% of 8 = 4.) Use whichever is easier.

---

## 2. The conversion table (memorise)

| Fraction | % | Fraction | % | Fraction | % |
|---|---|---|---|---|---|
| 1/2 | 50 | 1/7 | 14.28 | 1/12 | 8.33 |
| 1/3 | 33.33 | 1/8 | 12.5 | 1/13 | 7.69 |
| 1/4 | 25 | 1/9 | 11.11 | 1/15 | 6.67 |
| 1/5 | 20 | 1/10 | 10 | 1/16 | 6.25 |
| 1/6 | 16.67 | 1/11 | 9.09 | 1/20 | 5 |

Multiples follow: 3/8 = 37.5%, 5/6 = 83.33%, 2/7 = 28.57%, 4/9 = 44.44%.

12.5% of 640 = 640/8 = **80**. 16.67% of 330 = 330/6 = **55**.

---

## 3. Multiplying factors (the key idea)

| Change | Multiply by |
|---|---|
| Increase by r% | **(1 + r/100)** |
| Decrease by r% | **(1 − r/100)** |

Increase 400 by 15%: 400 × 1.15 = 460. Decrease 400 by 15%: 400 × 0.85 = 340.

**Percentage change** = (new − old)/old × 100. **Always relative to the old (original) value.**
40 → 50: +25%. 50 → 40: −20%. (Same gap, different bases.)

### Successive changes

Multiply the factors: +10% then +20% → 1.1 × 1.2 = 1.32 → **+32%** (not 30%).

Shortcut for two changes a% and b% (use negative for decreases):

```
Net % = a + b + ab/100
```

- +20% then −20%: 0 − 4 = **−4%**. In general **+p% then −p% = −p²/100 %** (always a loss).
- +25% then −20%: 5 − 5 = **0%** (factors 5/4 × 4/5 = 1).
- Discounts 20% and 10%: −20 − 10 + 2 = **−28%** (a 28% total discount).

### Area and volume

- Square side +10% → area × 1.21 → **+21%**.
- Length +20%, breadth −20% → area × 0.96 → **−4%**.
- Circle radius +50% → area × 2.25 → **+125%**.
- Cube side +10% → volume × 1.331 → **+33.1%**.

---

## 4. "Keep the product constant" (expenditure problems)

Expenditure = price × consumption. If price rises by r%, consumption must fall by

```
r / (100 + r) × 100 %
```

If price falls by r%, consumption may rise by r/(100 − r) × 100%.

**Fraction trick:** price +25% = ×5/4 → consumption must be ×4/5 → a **20%** cut. Flip the fraction.

| Price change | Consumption change |
|---|---|
| +10% (11/10) | −1/11 = −9.09% |
| +20% (6/5) | −1/6 = −16.67% |
| +25% (5/4) | −1/5 = −20% |
| +33.33% (4/3) | −1/4 = −25% |
| +50% (3/2) | −1/3 = −33.33% |
| −20% (4/5) | +1/4 = +25% |

---

## 5. "A is x% more than B" ≠ "B is x% less than A"

If A is r% more than B, then B is less than A by **r/(100 + r) × 100%**.
If A is r% less than B, then B is more than A by **r/(100 − r) × 100%**.

A = 25% more than B → B is 20% less than A.
A = 20% less than B → B is 25% more than A.
A = 50% more than B → B is 33.33% less than A.

---

## 6. Population and depreciation

```
After n years:  P (1 + r/100)ⁿ        (growth)
                P (1 − r/100)ⁿ        (decline / depreciation)
n years ago:    P / (1 + r/100)ⁿ
```

Different rates in different years: multiply each year's factor.

10,000 growing 10% per year for 2 years: 10000 × 1.21 = **12,100**.
1,25,000 falling 4% per year for 2 years: 125000 × 0.9216 = **1,15,200**.

---

## 7. Exam and election problems

**Votes:** if the winner gets W% and the loser L% (two candidates), the **margin** is (W − L)% of the votes counted. Total = margin ÷ (W − L)%.
Winner 60%, wins by 4800 → 20% of total = 4800 → total **24,000**.

With invalid votes: work with valid votes first (valid = (100 − invalid)% of total).

**Pass marks:** pass mark = (marks obtained) + (marks short) = pass% of max.
Need 40%; got 180 and failed by 20 → 200 = 40% → max = **500**.

**Two subjects (Venn):** passed both = 100 − (failed A + failed B − failed both).

---

## 8. Dry fruit / water content problems

Track the **non-water part**, which doesn't change.
Fresh fruit 68% water (32% pulp); dry fruit 20% water (80% pulp). From 100 kg fresh: pulp 32 kg = 80% of dry → dry = **40 kg**.

---

## 9. Exam traps

1. +p% then −p% is a **loss** of p²/100 %.
2. Percentage change uses the **original** as base.
3. A more than B by r% ≠ B less than A by r%.
4. Successive changes multiply; don't add.
5. In vote problems, check whether percentages are of **total** or **valid** votes.

---

## 10. Practice questions (with solutions)

**Q1.** 12.5% of 480?
(a) 40 (b) 50 (c) 60 (d) 65
**Answer: (c).**

**Q2.** 3/8 as a percentage?
(a) 32.5% (b) 35% (c) 37.5% (d) 40%
**Answer: (c).**

**Q3.** Price increased by 20% then decreased by 20%. Net change?
(a) 0 (b) +4% (c) −4% (d) −40%
**Answer: (c).**

**Q4.** Increased by 25% then decreased by 20%. Net?
(a) No change (b) +5% (c) −5% (d) +10%
**Answer: (a).**

**Q5.** Rice price rises 30%. Reduction in consumption to keep expenditure same?
(a) 20% (b) 23.07% (c) 30% (d) 33.33%
**Answer: (b).** 30/130.

**Q6.** A's salary is 25% more than B's. B's salary is less than A's by:
(a) 25% (b) 20% (c) 30% (d) 33.33%
**Answer: (b).**

**Q7.** Population rises 10% then falls 10%, ending at 9900. Original?
(a) 9800 (b) 10000 (c) 10100 (d) 9900
**Answer: (b).** 9900/0.99.

**Q8.** Winner gets 60% of votes and wins by 4800. Total votes?
(a) 20000 (b) 22000 (c) 24000 (d) 25000
**Answer: (c).**

**Q9.** If 60% of A = 3/4 of B, A : B = ?
(a) 3:4 (b) 4:5 (c) 5:4 (d) 5:6
**Answer: (c).**

**Q10.** What percent of 75 is 15?
(a) 15% (b) 20% (c) 25% (d) 5%
**Answer: (b).**

**Q11.** 15% of a number is 45. The number?
(a) 250 (b) 300 (c) 350 (d) 675
**Answer: (b).**

**Q12.** A value rises from 40 to 50. Percentage increase?
(a) 20% (b) 25% (c) 10% (d) 50%
**Answer: (b).**

**Q13.** Two successive increases of 10% and 20%. Net increase?
(a) 30% (b) 31% (c) 32% (d) 33%
**Answer: (c).**

**Q14.** Successive discounts of 20% and 10% equal a single discount of:
(a) 30% (b) 28% (c) 27% (d) 25%
**Answer: (b).**

**Q15.** Population 10,000 grows 10% per year. After 2 years?
(a) 12000 (b) 12100 (c) 12200 (d) 11000
**Answer: (b).**

**Q16.** A machine worth 1,25,000 depreciates 4% per year. Value after 2 years?
(a) 1,15,000 (b) 1,15,200 (c) 1,20,000 (d) 1,16,000
**Answer: (b).**

**Q17.** Petrol price rises 20%. Cut in consumption to keep spending unchanged?
(a) 20% (b) 16.67% (c) 15% (d) 25%
**Answer: (b).**

**Q18.** A is 50% more than B. B is less than A by:
(a) 50% (b) 33.33% (c) 25% (d) 66.67%
**Answer: (b).**

**Q19.** A is 20% less than B. B is more than A by:
(a) 20% (b) 25% (c) 16.67% (d) 30%
**Answer: (b).**

**Q20.** Pass mark is 40%. A student scores 180 and fails by 20 marks. Maximum marks?
(a) 400 (b) 450 (c) 500 (d) 550
**Answer: (c).**

**Q21.** 35% failed in Hindi, 45% failed in English, 20% failed in both. Percentage who passed both?
(a) 30% (b) 40% (c) 45% (d) 60%
**Answer: (b).** Failed at least one = 35 + 45 − 20 = 60.

**Q22.** A salary is raised 20% then cut 20%, ending at 9600. Original?
(a) 9600 (b) 10000 (c) 10400 (d) 9800
**Answer: (b).** 9600/0.96.

**Q23.** A number is increased by 10%, again by 10%, then decreased by 20%. Net change?
(a) 0% (b) −3.2% (c) +3.2% (d) −2%
**Answer: (b).** 1.1 × 1.1 × 0.8 = 0.968.

**Q24.** Fresh fruit has 68% water, dry fruit 20%. Dry fruit obtained from 100 kg of fresh fruit?
(a) 32 kg (b) 40 kg (c) 52 kg (d) 80 kg
**Answer: (b).**

**Q25.** Length increased by 20% and breadth decreased by 20%. Area changes by:
(a) 0% (b) −4% (c) +4% (d) −2%
**Answer: (b).**

**Q26.** Side of a square increased by 10%. Area increases by:
(a) 10% (b) 20% (c) 21% (d) 11%
**Answer: (c).**

**Q27.** Radius of a circle increased by 50%. Area increases by:
(a) 50% (b) 100% (c) 125% (d) 150%
**Answer: (c).**

**Q28.** 10% of votes were invalid. The winner got 55% of the valid votes and won by 900 votes. Total votes?
(a) 9000 (b) 10000 (c) 12000 (d) 8000
**Answer: (b).** Margin = 10% of valid = 9% of total = 900.

**Q29.** When 35% of a number is added to it, the result is 54. The number?
(a) 35 (b) 40 (c) 45 (d) 50
**Answer: (b).** 1.35x = 54.

**Q30.** x% of y + y% of x = ?
(a) xy% (b) 2xy/100 (c) (x + y)% (d) xy/200
**Answer: (b).**

**Q31.** Price of sugar falls 20%. A person can now buy 5 kg more for ₹500. Original price per kg?
(a) ₹20 (b) ₹25 (c) ₹30 (d) ₹15
**Answer: (b).** The 20% saving = ₹100 buys 5 kg → new price ₹20 → original 20/0.8 = 25.

**Q32.** A cube's side increases by 10%. Volume increases by:
(a) 30% (b) 33.1% (c) 21% (d) 10%
**Answer: (b).**

**Q33.** A student got 30% marks and failed by 15; another got 40% and got 35 more than the pass mark. Pass mark?
(a) 150 (b) 165 (c) 180 (d) 200
**Answer: (b).** 10% of max = 50 → max 500; pass = 150 + 15 = 165.

**Q34.** If the price of an article increases by 1/3, by what fraction must consumption fall to keep spending same?
(a) 1/3 (b) 1/4 (c) 1/5 (d) 2/3
**Answer: (b).**
