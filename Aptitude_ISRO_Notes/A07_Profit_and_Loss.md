# A07. Profit and Loss

> **Remember two anchors.** Profit and loss percentages are on the **cost price**. Discounts are on the **marked price**. Every problem is a chain: **CP → (markup) → MP → (discount) → SP**, and you compare SP with CP. Use multiplying factors and the chain becomes one line.

---

## 1. Vocabulary

- **CP (cost price):** what the seller paid.
- **SP (selling price):** what the buyer pays.
- **MP (marked/list price):** the tag price before discount.
- **Profit = SP − CP**; **Loss = CP − SP**.

```
Profit % = Profit / CP × 100        Loss % = Loss / CP × 100
Discount % = Discount / MP × 100
```

> **Trap:** profit/loss % is **on CP** (unless told otherwise). Discount % is **on MP**.

---

## 2. Multiplying factors

| | Formula |
|---|---|
| SP from CP with r% profit | SP = CP × (100 + r)/100 |
| SP from CP with r% loss | SP = CP × (100 − r)/100 |
| CP from SP with r% profit | CP = SP × 100/(100 + r) |
| SP from MP with d% discount | SP = MP × (100 − d)/100 |

### Ratio shortcut (memorise)

| Profit | SP : CP | Loss | SP : CP |
|---|---|---|---|
| 10% | 11 : 10 | 10% | 9 : 10 |
| 20% | 6 : 5 | 20% | 4 : 5 |
| 25% | 5 : 4 | 25% | 3 : 4 |
| 33.33% | 4 : 3 | 33.33% | 2 : 3 |
| 50% | 3 : 2 | 50% | 1 : 2 |

SP ₹720 with 20% profit → CP = 720 × 5/6 = **600**.

---

## 3. Markup and discount chains

SP = CP × (1 + markup) × (1 − discount).

- Mark up 40%, discount 10%: 1.4 × 0.9 = 1.26 → **26% profit**.
- Mark up 50%, discount 20%: 1.5 × 0.8 = 1.2 → **20% profit**.

**Finding the markup needed:** to get profit p% after discount d%, MP/CP = (1 + p)/(1 − d).
20% profit after 20% discount → 1.2/0.8 = 1.5 → mark **50%** above CP.

### Successive discounts

Multiply factors (or use a + b − ab/100):
- 20% and 10%: 0.8 × 0.9 = 0.72 → **28%**.
- 10%, 20%, 10%: 0.9 × 0.8 × 0.9 = 0.648 → **35.2%**.

### "Buy x get y free"

Discount = **y/(x + y)**. Buy 4 get 1 free → 1/5 = **20%**.

---

## 4. False weights (dishonest dealer)

Claims to sell at cost but gives less:

```
Profit % = (true weight − false weight) / false weight × 100 = error / (true − error) × 100
```

900 g for 1 kg → 100/900 = **11.11%**.

**Cheating in both buying and selling:** gets 10% extra when buying and gives 10% less when selling: profit = (1.1/0.9 − 1) × 100 = **22.22%**.

**Sells at a profit and also uses a false weight:** multiply factors. E.g. sells at 10% above CP and uses 900 g: 1.1 × (1000/900) = 1.2222 → **22.22%**.

---

## 5. Equal selling prices, equal ± percentages

Two items sold at the **same SP**, one at x% profit, one at x% loss → always a **net loss of x²/100 %**.

Two watches at ₹1000 each, ±20% → **4% loss**. CPs: 1000/1.2 = 833.33 and 1000/0.8 = 1250; total CP 2083.33, SP 2000.

(If the **CPs** are equal instead, the profit and loss cancel: no net change.)

---

## 6. "Articles" problems

- **CP of a articles = SP of b articles** → profit% = (a − b)/b × 100 (if a > b; loss if a < b).
  CP of 20 = SP of 16 → (20 − 16)/16 = **25% profit**.
- **Gain = SP of x articles** when selling n: profit% = x/(n − x) × 100.
- **Loss = SP of x articles** when selling n: loss% = x/(n + x) × 100.
  Loss = SP of 4 on selling 20 → 4/24 = **16.67%**.
- **Gain = CP of x articles** when selling n: profit% = x/n × 100. Gain = CP of 5 on 25 → 20%.

### Buying and selling at "a for ₹b"

Buy 12 for ₹10 and sell 10 for ₹12: CP per item = 10/12, SP per item = 12/10. Profit = (1.2 − 0.8333)/0.8333 = **44%**. (Shortcut: (12 × 12 − 10 × 10)/(10 × 10) = 44/100.)

---

## 7. Chains of sellers

A sells to B at 20% profit, B to C at 25% profit, C pays ₹225 → A's CP = 225/(1.2 × 1.25) = **150**.

---

## 8. Overall profit on several deals

Use **totals**, never average the percentages.
CP 400 at 10% profit and CP 600 at 20% profit → SP 440 + 720 = 1160 on CP 1000 → **16%**.

---

## 9. Exam traps

1. Profit % on CP; discount on MP.
2. Successive discounts aren't additive.
3. Same SP with ±x% → loss x²/100 %.
4. False weight base is the **false** weight.
5. Use totals for combined deals.

---

## 10. Practice questions (with solutions)

**Q1.** Bought for ₹400, sold for ₹460. Profit %?
(a) 10% (b) 12% (c) 15% (d) 20%
**Answer: (c).**

**Q2.** Sold at 25% profit. SP : CP = ?
(a) 4:5 (b) 5:4 (c) 6:5 (d) 3:2
**Answer: (b).**

**Q3.** Marked 40% above CP, discount 10%. Profit?
(a) 20% (b) 26% (c) 30% (d) 36%
**Answer: (b).**

**Q4.** Marked 50% above CP, discount 20%. Result?
(a) 20% gain (b) 30% gain (c) 20% loss (d) 30% loss
**Answer: (a).**

**Q5.** Successive discounts of 20% and 10% on ₹500. Final price?
(a) ₹350 (b) ₹355 (c) ₹360 (d) ₹400
**Answer: (c).**

**Q6.** A dealer uses 900 g instead of 1 kg and sells at cost price. Profit?
(a) 9% (b) 10% (c) 11.11% (d) 12%
**Answer: (c).**

**Q7.** Two watches sold at ₹1000 each, one at 20% gain, one at 20% loss. Overall?
(a) No change (b) 4% loss (c) 4% gain (d) 2% loss
**Answer: (b).**

**Q8.** Two articles sold at ₹2400 each, +20% and −20%. Overall?
(a) Loss ₹100 (b) Loss ₹200 (c) Gain ₹200 (d) No loss
**Answer: (b).** CPs 2000 and 3000; SP 4800.

**Q9.** Single discount equal to 10%, 20%, 10%?
(a) 35.2% (b) 36.8% (c) 38% (d) 40%
**Answer: (a).**

**Q10.** Selling 45 lemons for ₹40 gives a 20% loss. How many lemons for ₹24 to gain 20%?
(a) 12 (b) 15 (c) 18 (d) 20
**Answer: (c).** CP of 45 = 50; per lemon 10/9; SP needed 4/3 each.

**Q11.** CP of 20 articles = SP of 16. Profit %?
(a) 20% (b) 25% (c) 16% (d) 30%
**Answer: (b).**

**Q12.** SP of 25 articles = CP of 20. Profit or loss?
(a) 20% loss (b) 25% loss (c) 20% gain (d) 25% gain
**Answer: (a).** SP/CP = 20/25.

**Q13.** SP ₹720 gives 20% profit. CP?
(a) ₹576 (b) ₹600 (c) ₹620 (d) ₹640
**Answer: (b).**

**Q14.** Sold at 10% loss for ₹540. SP for 10% gain?
(a) ₹594 (b) ₹600 (c) ₹650 (d) ₹660
**Answer: (d).** CP 600.

**Q15.** Profit on selling at ₹425 equals the loss on selling at ₹355. CP?
(a) ₹380 (b) ₹385 (c) ₹390 (d) ₹400
**Answer: (c).** Average of the two prices.

**Q16.** Buy 12 for ₹10, sell 10 for ₹12. Profit %?
(a) 20% (b) 40% (c) 44% (d) 50%
**Answer: (c).**

**Q17.** "Buy 4, get 1 free" is equivalent to a discount of:
(a) 25% (b) 20% (c) 15% (d) 10%
**Answer: (b).**

**Q18.** How much above CP must goods be marked to make 20% profit after a 20% discount?
(a) 40% (b) 45% (c) 50% (d) 60%
**Answer: (c).**

**Q19.** A 10% discount gives a 26% profit. Profit without discount?
(a) 36% (b) 38% (c) 40% (d) 42%
**Answer: (c).** 1.26/0.9 = 1.4.

**Q20.** A trader cheats 10% in buying and 10% in selling. Profit?
(a) 20% (b) 21% (c) 22.22% (d) 25%
**Answer: (c).**

**Q21.** An article sold at 20% profit. If CP were 20% less and SP ₹5 less, profit would be 25%. CP?
(a) ₹20 (b) ₹25 (c) ₹30 (d) ₹40
**Answer: (b).** 1.2x − 5 = 1.25 × 0.8x = x → x = 25.

**Q22.** Selling at 5% profit instead of 5% loss earns ₹20 more. CP?
(a) ₹100 (b) ₹200 (c) ₹400 (d) ₹150
**Answer: (b).** 10% of CP = 20.

**Q23.** Items with CP ₹400 and ₹600 sold at 10% and 20% profit respectively. Overall profit?
(a) 15% (b) 16% (c) 17% (d) 18%
**Answer: (b).**

**Q24.** A sells to B at 20% profit, B to C at 25% profit. C pays ₹225. A's cost?
(a) ₹150 (b) ₹160 (c) ₹175 (d) ₹180
**Answer: (a).**

**Q25.** Doubling the SP triples the profit. Profit %?
(a) 50% (b) 66.67% (c) 100% (d) 120%
**Answer: (c).** 2SP − CP = 3(SP − CP) → SP = 2CP.

**Q26.** Gain is 1/3 of the SP. Gain % on CP?
(a) 33.33% (b) 50% (c) 25% (d) 66.67%
**Answer: (b).**

**Q27.** On selling 20 articles, the loss equals the SP of 4. Loss %?
(a) 20% (b) 16.67% (c) 25% (d) 15%
**Answer: (b).**

**Q28.** On selling 25 articles, the gain equals the CP of 5. Gain %?
(a) 20% (b) 25% (c) 16.67% (d) 15%
**Answer: (a).**

**Q29.** MP ₹1500, discount 20%, profit 20%. CP?
(a) ₹900 (b) ₹1000 (c) ₹1100 (d) ₹1200
**Answer: (b).** SP 1200 → CP 1000.

**Q30.** A shopkeeper sells at 10% above CP and uses a 900 g weight for 1 kg. Actual profit?
(a) 10% (b) 21% (c) 22.22% (d) 20%
**Answer: (c).** 1.1 × 10/9.

**Q31.** By selling an article for ₹96, a person gains as much percent as its CP (in rupees). CP?
(a) ₹40 (b) ₹50 (c) ₹60 (d) ₹64
**Answer: (c).** x + x²/100 = 96 → x² + 100x − 9600 = 0 → x = 60.
