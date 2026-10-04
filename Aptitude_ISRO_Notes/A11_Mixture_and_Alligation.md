# A11. Mixture and Alligation

> **Alligation is just the weighted average turned around.** If you mix a cheap thing and a costly thing, the mixture's price lands in between, closer to whichever you used more of. The alligation "cross" tells you the mixing ratio from the three prices in two seconds.

---

## 1. The alligation rule

Two ingredients with values d₁ (cheaper) and d₂ (dearer) are mixed to get mean value m (d₁ < m < d₂):

```
Quantity of cheaper : Quantity of dearer = (d₂ − m) : (m − d₁)
```

Picture:

```
   d₁            d₂
      \        /
         m
      /        \
 (d₂ − m)    (m − d₁)
 cheaper  :  dearer
```

Each ingredient gets the **difference on the opposite side**.

**Example:** rice at ₹450/kg and ₹510/kg mixed to ₹475/kg → (510 − 475) : (475 − 450) = 35 : 25 = **7 : 5**.

**Sanity check:** the mean is closer to the ingredient used more. 475 is closer to 450, so the ₹450 rice should get the bigger share. 7 > 5 ✓.

---

## 2. Where alligation applies

Anything that is a **weighted average** of two values:

| Context | d₁, d₂ | m |
|---|---|---|
| Prices | Price per kg of each | Price of mixture |
| Concentrations | % of acid/milk/alcohol in each | % in mixture |
| Group averages | Average of each group | Combined average |
| Speeds (equal times) | Two speeds | Average speed |
| Interest | Two rates | Overall rate |
| Profit % | Profit % on each part | Overall profit % |

**Group averages:** boys average 40 kg, girls 30 kg, class of 80 averages 33. Boys : girls = (33 − 30) : (40 − 33) = 3 : 7 → **24 boys**.

**Interest:** ₹1000 lent partly at 6% and partly at 8%; yearly interest ₹75 → mean 7.5%. Ratio (6%) : (8%) = 0.5 : 1.5 = 1 : 3 → **₹750** at 8%.

**Profit:** 91 kg of rice, some sold at 7% profit and the rest at 33%, overall 25%. Ratio = (33 − 25) : (25 − 7) = 8 : 18 = 4 : 9 → **28 kg** at 7%.

**Speeds (by time):** a person covers 80 km in 8 h, partly walking at 4 km/h and partly cycling at 20 km/h. Mean speed 10. Time walking : cycling = (20 − 10) : (10 − 4) = 5 : 3 → walking 5 h = **20 km**.

---

## 3. Mixtures with a selling price and profit

First convert the selling price to the **mean cost price**, then alligate.

Rice at ₹18 and ₹24 per kg is mixed and sold at ₹23, gaining 15%. Mean CP = 23/1.15 = **20**. Ratio (18) : (24) = (24 − 20) : (20 − 18) = 2 : 1. If 40 kg of the ₹24 rice was used, the ₹18 rice = **80 kg**.

Salt at 42 p/kg mixed with 25 kg at 24 p/kg, sold at 40 p/kg for 25% gain: mean CP = 32 p. Ratio (42) : (24) = (32 − 24) : (42 − 32) = 8 : 10 = 4 : 5 → 25 kg is 5 parts → **20 kg**.

---

## 4. Water (free ingredient) problems

Water has **value 0** (costs nothing, 0% concentration).

### "Sold at cost price and still makes a profit"

A milkman adds water and sells the mixture at the **cost price of pure milk**. His profit comes entirely from the water:

```
Water : Milk = profit% : 100   (as a fraction: profit fraction a/b → water : milk = a : b)
```

- 25% profit (1/4) → water : milk = **1 : 4**.
- 16.67% profit (1/6) → **1 : 6**.
- 20% profit (1/5) → 1 : 5.

### Diluting to a lower concentration

The amount of the **pure substance stays fixed**.
40 L that is 90% acid (36 L acid) diluted to 60%: 36 = 0.6 × new total → 60 L → add **20 L** of water.

### Adding the pure ingredient

80 L that is 70% milk (24 L water). To reach 95% milk, water must be 5%: 24 = 0.05 × total → 480 L → add **400 L** of milk.

### Changing a ratio by adding one ingredient

729 mL with milk : water 7 : 2 (milk 567, water 162). To make 7 : 3, water must be 567 × 3/7 = 243 → add **81 mL**.

---

## 5. Repeated removal and replacement

From a container of V litres of pure liquid, remove x litres and replace with water, n times:

```
Pure liquid left = V × (1 − x/V)ⁿ
```

It's a **geometric** decay, not linear.

- 81 L of milk, 1/3 replaced three times → 81 × (2/3)³ = **24 L**.
- 40 L of milk, 4 L replaced three times → 40 × 0.9³ = **29.16 L**.
- 30% replaced twice → 0.7² = **49%** of the original remains.

### Replacing to reach a ratio

60 L with milk : water 5 : 1 (50 : 10). How much to replace with water to make 1 : 1?
Removing x L takes out 5x/6 milk: 50 − 5x/6 = 30 → **x = 24 L**.

---

## 6. Mixing two mixtures

Find each mixture's **fraction** of the ingredient, then alligate.

Vessel A has milk : water 5 : 3 (milk 15/24), vessel B 3 : 1 (milk 18/24). To get milk : water 2 : 1 (milk 16/24): A : B = (18 − 16) : (16 − 15) = **2 : 1**.

---

## 7. Exam traps

1. Each ingredient gets the difference from the **other** side.
2. Convert SP to mean CP before alligating.
3. Water has value 0.
4. Repeated replacement: (1 − fraction)ⁿ.
5. In dilution, keep the unchanged component fixed.

---

## 8. Practice questions (with solutions)

**Q1.** Tea at ₹60/kg and ₹65/kg mixed to ₹62/kg. Ratio?
(a) 2:3 (b) 3:2 (c) 1:1 (d) 3:5
**Answer: (b).** (65 − 62) : (62 − 60).

**Q2.** 20 kg of rice at ₹20 mixed with rice at ₹24 to get ₹22/kg. Quantity of the second?
(a) 15 (b) 20 (c) 25 (d) 30 kg
**Answer: (b).** Ratio 1 : 1.

**Q3.** 60 L of milk : water = 5 : 1. Mixture to be replaced with water to make 1 : 1?
(a) 20 L (b) 24 L (c) 30 L (d) 25 L
**Answer: (b).**

**Q4.** Boys average 60, girls 80; combined 68; 30 girls. Number of boys?
(a) 20 (b) 30 (c) 45 (d) 50
**Answer: (c).** Ratio 12 : 8 = 3 : 2.

**Q5.** 40 L of 90% acid. Water to add to reach 60%?
(a) 15 L (b) 18 L (c) 20 L (d) 25 L
**Answer: (c).**

**Q6.** Rice at ₹18 and ₹24 mixed and sold at ₹23 for 15% gain; 40 kg of ₹24 rice used. Amount of ₹18 rice?
(a) 40 (b) 60 (c) 80 (d) 100 kg
**Answer: (c).**

**Q7.** 729 mL, milk : water 7 : 2. Water to add for 7 : 3?
(a) 81 (b) 91 (c) 100 (d) 72 mL
**Answer: (a).**

**Q8.** Pure milk; 30% replaced with water, repeated once more. Milk remaining?
(a) 40% (b) 49% (c) 51% (d) 60%
**Answer: (b).**

**Q9.** Water : milk ratio so that selling at the cost price of milk gives 25% profit?
(a) 1:3 (b) 1:4 (c) 1:5 (d) 3:1
**Answer: (b).**

**Q10.** Water : milk for a 16.67% profit when selling at the milk's cost price?
(a) 1:5 (b) 1:6 (c) 1:7 (d) 1:4
**Answer: (b).**

**Q11.** Items at ₹450 and ₹510 per kg mixed to ₹475 per kg. Ratio?
(a) 5:7 (b) 7:5 (c) 3:2 (d) 2:3
**Answer: (b).**

**Q12.** Boys average 40 kg, girls 30 kg; 80 students average 33 kg. Number of boys?
(a) 24 (b) 56 (c) 30 (d) 40
**Answer: (a).**

**Q13.** ₹1000 lent in two parts at 6% and 8% SI earns ₹75 a year. Amount at 8%?
(a) ₹250 (b) ₹500 (c) ₹750 (d) ₹600
**Answer: (c).**

**Q14.** 91 kg of rice: part at 7% profit, rest at 33%, overall 25%. Quantity at 7%?
(a) 28 (b) 63 (c) 35 (d) 21 kg
**Answer: (a).**

**Q15.** 81 L of milk; 1/3 replaced with water three times. Milk left?
(a) 24 L (b) 27 L (c) 36 L (d) 54 L
**Answer: (a).**

**Q16.** 80 L of 70% milk. Pure milk to add to make it 95% milk?
(a) 320 L (b) 400 L (c) 480 L (d) 240 L
**Answer: (b).**

**Q17.** Vessel A (milk : water 5 : 3) and vessel B (3 : 1). Mixing ratio A : B for milk : water 2 : 1?
(a) 1:2 (b) 2:1 (c) 1:1 (d) 3:2
**Answer: (b).**

**Q18.** Sugar at ₹7/kg and ₹9/kg mixed and sold at ₹9.24/kg for 10% gain. Ratio (₹7 : ₹9)?
(a) 3:7 (b) 7:3 (c) 2:3 (d) 1:2
**Answer: (a).** Mean CP 8.4 → (9 − 8.4) : (8.4 − 7) = 0.6 : 1.4.

**Q19.** 80 km in 8 h, partly walking at 4 km/h and partly cycling at 20 km/h. Distance walked?
(a) 16 km (b) 20 km (c) 24 km (d) 30 km
**Answer: (b).**

**Q20.** 40 L of milk; 4 L removed and replaced with water, three times. Milk left?
(a) 28 L (b) 29.16 L (c) 30 L (d) 32.4 L
**Answer: (b).**

**Q21.** Salt at 42 p/kg mixed with 25 kg at 24 p/kg; selling at 40 p/kg gives 25% gain. Quantity of the 42 p salt?
(a) 15 kg (b) 20 kg (c) 25 kg (d) 30 kg
**Answer: (b).**

**Q22.** 20 L of 30% alcohol. How much 60% alcohol must be added to get 40%?
(a) 5 L (b) 10 L (c) 15 L (d) 20 L
**Answer: (b).** Ratio (30%) : (60%) = 20 : 10 = 2 : 1.

**Q23.** Section averages 50 and 70; overall 56. Ratio of students?
(a) 7:3 (b) 3:7 (c) 2:1 (d) 1:2
**Answer: (a).**

**Q24.** A vessel of pure milk; 10% replaced by water twice. Percentage of water now?
(a) 19% (b) 20% (c) 81% (d) 10%
**Answer: (a).** Milk 0.9² = 81%.

**Q25.** Two alloys have gold : copper 3 : 2 and 2 : 3. In what ratio should they be mixed for gold : copper 1 : 1?
(a) 1:1 (b) 2:3 (c) 3:2 (d) 1:2
**Answer: (a).** Gold fractions 3/5 and 2/5; target 1/2 is exactly halfway.
