# A08. Simple Interest and Compound Interest

> **The one picture to keep.** Simple interest is a **straight line**: the same interest every year, always on the original principal. Compound interest is a **curve**: each year's interest also earns interest. Every formula below is just this picture written out.

---

## 1. Terms

- **Principal (P):** the amount lent or invested.
- **Rate (R):** % per annum (per year).
- **Time (T):** in years (convert months: 9 months = 3/4 year).
- **Interest (I)** and **Amount (A) = P + I**.

---

## 2. Simple interest

```
SI = P × R × T / 100          A = P + SI
```

- Interest is the **same every year**: P·R/100.
- Rearranged: P = 100·SI/(R·T), R = 100·SI/(P·T), T = 100·SI/(P·R).

Example: ₹5000 at 8% for 3 years → SI = **₹1200**.

### Multiples of the principal (SI)

Amount = n × P means SI = (n − 1)P, so **R × T = (n − 1) × 100**.
- Doubles in 10 years → R = 10%.
- At 25%, becomes 4 times in T = 300/25 = **12** years.
- Under SI, if it doubles in T years it **triples** in 2T (not quadruples).

### From two amounts

Amount ₹1500 after 2 years and ₹1800 after 4 years → SI per year = 300/2 = 150 → P = 1500 − 300 = **1200** → R = 150/1200 = **12.5%**.

### Varying rates

Total SI = P × (R₁T₁ + R₂T₂ + ...)/100.
₹1000 at 5% for 2 years, 6% for 3 years, 8% for 5 years: 1000 × (10 + 18 + 40)/100 = **₹680**.

---

## 3. Compound interest

```
A = P (1 + R/100)ᵀ          CI = A − P
```

### Mental math with successive percentages

Use net % = a + b + ab/100 repeatedly:
- 10% for 2 years: 10 + 10 + 1 = **21%**.
- 10% for 3 years: 21 + 10 + 2.1 = **33.1%**.
- 5% for 2 years: **10.25%**. 20% for 2 years: **44%**. 20% for 3 years: 44 + 20 + 8.8 = **72.8%**.

₹10,000 at 10% for 2 years → CI = **₹2100**. ₹1000 at 10% for 3 years → **₹331**.

### Half-yearly and quarterly compounding

| Compounding | Rate per period | Number of periods |
|---|---|---|
| Annually | R | T |
| Half-yearly | R/2 | 2T |
| Quarterly | R/4 | 4T |

₹4000 at 20% for 1 year, half-yearly: 10% for 2 periods → 4000 × 1.21 = 4840 → CI **₹840** (not 800).
₹16,000 at 20% for 9 months, quarterly: 5% for 3 periods → 16000 × 1.157625 = 18522 → CI **₹2522**.

**Effective annual rate** of 10% compounded half-yearly: 1.05² = 1.1025 → **10.25%**.

### Multiples (CI)

If a sum **doubles** in t years, it becomes **4×** in 2t, **8×** in 3t, **2ⁿ ×** in nt. (Growth multiplies.)
Doubles in 8 years → 8 times in **24** years. Doubles in 5 years → 16 times in **20** years.

**Rule of 72:** doubling time ≈ 72/R years (8% → about 9 years).

### Finding the rate from two consecutive amounts

₹8820 after 2 years and ₹9261 after 3 years: the 3rd year's interest 441 is interest on 8820 → R = 441/8820 = **5%** → P = 8820/1.05² = **₹8000**.

---

## 4. CI − SI difference (very high yield)

```
T = 2:   CI − SI = P (R/100)²
T = 3:   CI − SI = P (R/100)² (3 + R/100)
```

Why for 2 years? CI's second year earns interest on the **first year's interest**: (P·R/100) × R/100.

- ₹8000, 5%, 2 years → 8000 × 0.0025 = **₹20**.
- Difference ₹31 for 3 years at 10% → P × 0.01 × 3.1 = 31 → P = **₹1000**.

### From SI and CI of 2 years

SI for 2 years = 2·(PR/100). CI − SI = (PR/100)·(R/100). So **(CI − SI) / (SI/2) = R/100**.
CI = 410, SI = 400 → 10/200 = 5% → P = 400/(2 × 0.05) = **₹4000**.

---

## 5. Depreciation and population

Value after T years falling at R%: **P (1 − R/100)ᵀ**. ₹10,000 machine at 10% for 2 years → **₹8100**.

---

## 6. Instalments (CI)

Equal annual instalment x repaying a loan P at R% over 2 years:

```
P = x/(1 + R/100) + x/(1 + R/100)²
```

₹2100 at 10% over 2 years: 2100 = x/1.1 + x/1.21 = x(1.1 + 1)/1.21 → x = **₹1210**.

---

## 7. Splitting a sum

**Equal SI from two parts at different times/rates:** P₁R₁T₁ = P₂R₂T₂.
SI on part 1 at 5% for 4 years = SI on part 2 at 4% for 5 years → 20P₁ = 20P₂ → **1 : 1**.

**Mixed rates:** ₹5000 lent partly at 4% and partly at 5%, total yearly interest ₹230. 4x + 5(5000 − x) = 23000 → x = **₹2000** at 4%.

---

## 8. Exam traps

1. Adjust **both** rate and time for half-yearly/quarterly compounding.
2. SI doubling → tripling in twice the time; CI doubling → quadrupling.
3. CI − SI formulas differ for 2 and 3 years.
4. Convert months to years.
5. For CI, interest of year n+1 ÷ amount at end of year n = R.

---

## 9. Practice questions (with solutions)

**Q1.** SI on ₹5000 at 8% for 3 years?
(a) ₹1000 (b) ₹1100 (c) ₹1200 (d) ₹1500
**Answer: (c).**

**Q2.** CI on ₹10,000 at 10% for 2 years (annually)?
(a) ₹2000 (b) ₹2100 (c) ₹2200 (d) ₹2500
**Answer: (b).**

**Q3.** CI − SI on ₹8000 for 2 years at 5%?
(a) ₹15 (b) ₹18 (c) ₹20 (d) ₹25
**Answer: (c).**

**Q4.** CI on ₹4000 at 20% for 1 year, compounded half-yearly?
(a) ₹800 (b) ₹820 (c) ₹840 (d) ₹861
**Answer: (c).**

**Q5.** A sum doubles in 8 years at CI. Time to become 8 times?
(a) 16 (b) 20 (c) 24 (d) 32
**Answer: (c).**

**Q6.** CI − SI for 3 years at 10% is ₹31. Principal?
(a) ₹1000 (b) ₹1500 (c) ₹2000 (d) ₹3100
**Answer: (a).**

**Q7.** ₹12,000 becomes ₹15,600 in 3 years at SI. If the rate rises by 3%, the amount?
(a) ₹16,080 (b) ₹16,410 (c) ₹16,680 (d) ₹15,900
**Answer: (c).** R = 10% → 13% → SI 4680.

**Q8.** SI on part 1 at 5% for 4 years equals SI on part 2 at 4% for 5 years. Ratio of parts?
(a) 1:1 (b) 4:5 (c) 5:4 (d) 16:25
**Answer: (a).**

**Q9.** Time for a sum to become 4 times at 25% SI?
(a) 9 (b) 12 (c) 16 (d) 20
**Answer: (b).**

**Q10.** A sum doubles in 10 years at SI. Rate?
(a) 5% (b) 8% (c) 10% (d) 12%
**Answer: (c).**

**Q11.** A sum triples in 20 years at SI. It doubles in:
(a) 10 years (b) 12 years (c) 15 years (d) 8 years
**Answer: (a).** R = 10%.

**Q12.** A sum amounts to ₹1500 in 2 years and ₹1800 in 4 years at SI. Rate?
(a) 10% (b) 12% (c) 12.5% (d) 15%
**Answer: (c).**

**Q13.** A sum amounts to ₹8820 in 2 years and ₹9261 in 3 years at CI. Principal?
(a) ₹7500 (b) ₹8000 (c) ₹8400 (d) ₹8500
**Answer: (b).**

**Q14.** CI on ₹1000 at 10% for 3 years?
(a) ₹300 (b) ₹310 (c) ₹331 (d) ₹333
**Answer: (c).**

**Q15.** CI on ₹16,000 at 20% per annum for 9 months, compounded quarterly?
(a) ₹2400 (b) ₹2522 (c) ₹2500 (d) ₹2600
**Answer: (b).**

**Q16.** Effective annual rate of 10% compounded half-yearly?
(a) 10% (b) 10.25% (c) 10.5% (d) 11%
**Answer: (b).**

**Q17.** A sum doubles in 5 years at CI. In how many years does it become 16 times?
(a) 15 (b) 20 (c) 25 (d) 80
**Answer: (b).**

**Q18.** SI on a sum for 2 years at 10% is ₹400. CI for the same period and rate?
(a) ₹410 (b) ₹420 (c) ₹440 (d) ₹400
**Answer: (b).** P = 2000; CI = 21% of 2000.

**Q19.** CI for 2 years is ₹410 and SI is ₹400. Rate and principal?
(a) 5%, ₹4000 (b) 10%, ₹2000 (c) 5%, ₹2000 (d) 2.5%, ₹8000
**Answer: (a).**

**Q20.** A machine worth ₹10,000 depreciates 10% per year. Value after 2 years?
(a) ₹8000 (b) ₹8100 (c) ₹8200 (d) ₹9000
**Answer: (b).**

**Q21.** Equal annual instalment to repay ₹2100 in 2 years at 10% CI?
(a) ₹1100 (b) ₹1155 (c) ₹1210 (d) ₹1250
**Answer: (c).**

**Q22.** SI on ₹1000 at 5% for 2 years, 6% for the next 3 years and 8% for the next 5 years?
(a) ₹580 (b) ₹680 (c) ₹700 (d) ₹640
**Answer: (b).**

**Q23.** ₹5000 lent in two parts at 4% and 5%; total yearly interest ₹230. Amount at 4%?
(a) ₹1500 (b) ₹2000 (c) ₹2500 (d) ₹3000
**Answer: (b).**

**Q24.** Ratio of SI earned on the same sum at the same rate for 3 years and 5 years?
(a) 3:5 (b) 5:3 (c) 9:25 (d) 1:1
**Answer: (a).**

**Q25.** What sum amounts to ₹6050 in 2 years at 10% CI?
(a) ₹4800 (b) ₹5000 (c) ₹5500 (d) ₹5200
**Answer: (b).** 6050/1.21.

**Q26.** CI on ₹2000 at 20% for 2 years?
(a) ₹800 (b) ₹840 (c) ₹880 (d) ₹900
**Answer: (c).** 44% of 2000.

**Q27.** At what rate of CI will ₹1000 become ₹1331 in 3 years?
(a) 9% (b) 10% (c) 11% (d) 12%
**Answer: (b).** 1.1³ = 1.331.

**Q28.** The SI on a sum for 3 years is ₹225 and the CI at the same rate for 2 years is ₹153. Rate and principal?
(a) 4%, ₹1875 (b) 4%, ₹1500 (c) 5%, ₹1500 (d) 6%, ₹1250
**Answer: (a).** SI per year = 225/3 = 75, so the 2-year SI = 150 and CI − SI = 153 − 150 = 3. The extra is interest on one year's interest: 75 × R/100 = 3 → **R = 4%**. Then P = 75/0.04 = **₹1875**. (Check: 1875 × (1.04² − 1) = 1875 × 0.0816 = 153 ✓.)
