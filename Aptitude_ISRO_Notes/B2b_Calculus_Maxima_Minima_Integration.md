# B2b. Calculus: Maxima, Minima and Integration

> At the top of a hill and the bottom of a valley the ground is momentarily flat: **f′(x) = 0**. That's the whole idea of optimisation. **Integration** is the reverse of differentiation and, geometrically, the area under a curve.

---

## 1. Increasing, decreasing, critical points

- f′(x) > 0 on an interval → f increasing there; f′(x) < 0 → decreasing.
- **Critical point:** f′(c) = 0 or f′(c) undefined. Candidates for max/min.

## 2. Classifying critical points

**First derivative test:** watch the sign of f′ around c.
- + → − : **local maximum**.
- − → + : **local minimum**.
- No sign change: neither (an inflection-type point, like x³ at 0).

**Second derivative test:**
- f″(c) < 0 → maximum (curve bends down).
- f″(c) > 0 → minimum (curve bends up).
- f″(c) = 0 → **inconclusive**. x⁴ at 0 is a minimum though f″(0) = 0.

**Example:** f(x) = x³ − 3x. f′ = 3x² − 3 = 0 → x = ±1. f″ = 6x: f″(1) = 6 > 0 → local min, f(1) = −2; f″(−1) = −6 → local max, f(−1) = **2**. f increases where |x| > 1.

### Global (absolute) extremes on [a, b]

Check **all critical points and both endpoints**; pick the largest/smallest.
x³ − 3x on [0, 2]: f(0) = 0, f(1) = −2, f(2) = 2 → global max **2**, global min −2.

### Classic optimisation

- Max of x(10 − x): derivative 10 − 2x = 0 → x = 5 → **25**. (Two numbers with fixed sum have the largest product when equal.)
- Rectangle with perimeter 20: max area when it's a square of side 5 → **25**.
- x + 1/x for x > 0: minimum **2** at x = 1 (AM ≥ GM).

**Inflection point:** where f″ changes sign (concavity changes).

---

## 3. Integration basics

**Indefinite:** ∫f(x)dx = F(x) + C, where F′ = f.
**Definite:** ∫ₐᵇ f(x)dx = F(b) − F(a), a number (no C).

| f(x) | ∫f(x)dx |
|---|---|
| xⁿ (n ≠ −1) | xⁿ⁺¹/(n + 1) |
| 1/x | ln\|x\| |
| eˣ | eˣ |
| aˣ | aˣ/ln a |
| sin x | −cos x |
| cos x | sin x |
| sec²x | tan x |
| 1/(1 + x²) | tan⁻¹x |
| 1/√(1 − x²) | sin⁻¹x |

- ∫(3x² + 2x)dx = **x³ + x² + C**.
- ∫₀¹ x² dx = **1/3**. ∫₀^π sin x dx = [−cos x]₀^π = 1 + 1 = **2**.
- ∫₁ᵉ (1/x) dx = ln e − ln 1 = **1**.
- Area under y = x² from 0 to 3: 27/3 = **9**.

## 4. Properties of definite integrals

```
∫ₐᵇ f = −∫ᵦₐ f              ∫ₐᵃ f = 0
∫ₐᵇ f = ∫ₐᶜ f + ∫ᶜᵇ f
∫₀ᵃ f(x)dx = ∫₀ᵃ f(a − x)dx      ← the "King" property
∫₋ₐᵃ f = 2∫₀ᵃ f if f is even;  = 0 if f is odd
```

- ∫₋₁¹ x³ dx = **0** (odd).
- **King property trick:** I = ∫₀^(π/2) sin x/(sin x + cos x) dx. Replacing x by π/2 − x gives the same integral with cos on top. Adding: 2I = ∫₀^(π/2) 1 dx = π/2 → **I = π/4**.
- **Absolute values:** split at the zero. ∫₀² |x − 1| dx = two triangles of area 1/2 → **1**.

## 5. Techniques

### Substitution
If the derivative of an inner function sits outside, substitute u = inner.
∫2x e^(x²) dx: u = x² → ∫eᵘ du = **e^(x²) + C**. Likewise ∫2x cos(x²) dx = sin(x²) + C.

### Partial fractions
1/[(x − 1)(x − 2)] = A/(x − 1) + B/(x − 2). Cover-up: at x = 1, A = 1/(1 − 2) = −1; at x = 2, B = 1 → **1/(x − 2) − 1/(x − 1)**.
Repeated factor (x − a)² needs A/(x − a) + B/(x − a)²; irreducible quadratic needs (Ax + B)/(quadratic).

### Integration by parts
**∫u dv = uv − ∫v du.** Choose u by **LIATE** (Log, Inverse trig, Algebraic, Trig, Exponential): the earlier one is u.
- ∫x eˣ dx: u = x → x eˣ − eˣ = **eˣ(x − 1) + C**.
- ∫x ln x dx: u = ln x → (x²/2) ln x − x²/4 + C.
- ∫x² sin x dx: u = x².

---

## 6. Exam traps

1. f″(c) = 0 is inconclusive, not "no extremum".
2. Global extremes: don't forget the endpoints.
3. No "+C" for definite integrals; always "+C" for indefinite.
4. Odd function over symmetric limits → 0.
5. ∫(1/x) = ln|x|, not x⁰/0.

---

## 7. Practice questions (with solutions)

**Q1.** Critical point of x² − 4x + 3?
(a) 1 (b) 2 (c) 3 (d) 4
**Answer: (b).**

**Q2.** It is a:
(a) maximum (b) minimum (c) inflection (d) can't say
**Answer: (b).** f″ = 2 > 0.

**Q3.** ∫(3x² + 2x)dx?
(a) x³ + x² + C (b) 6x + 2 + C (c) x³ + 2x² + C (d) 3x³ + x² + C
**Answer: (a).**

**Q4.** A student writes ∫₀² 3x² dx = 8 + C. Error?
(a) none (b) definite integrals have no C; answer 8 (c) wrong formula (d) limits swapped
**Answer: (b).**

**Q5.** x⁴ at x = 0 is:
(a) a max by the 2nd-derivative test (b) a min, but the 2nd-derivative test is inconclusive (c) not critical (d) an inflection point
**Answer: (b).**

**Q6.** In ∫x² sin x dx, u should be:
(a) sin x (b) x² (c) either (d) neither
**Answer: (b).**

**Q7.** ∫2x e^(x²) dx?
(a) e^(x²) + C (b) 2e^(x²) + C (c) x²e^(x²) + C (d) e^(x²)/2 + C
**Answer: (a).**

**Q8.** Partial fractions of 1/[(x − 1)(x − 2)]?
(a) 1/(x − 1) + 1/(x − 2) (b) 1/(x − 2) − 1/(x − 1) (c) 1/(x − 1) − 1/(x − 2) (d) −1/(x − 1) − 1/(x − 2)
**Answer: (b).**

**Q9.** ∫x ln x dx?
(a) (x²/2)ln x − x²/4 + C (b) x ln x − x + C (c) (x²/2)ln x + C (d) (ln x)²/2 + C
**Answer: (a).**

**Q10.** I = ∫₀^π x sin x/(1 + cos²x) dx (the integrand without x is unchanged by x → π − x).
(a) 0 (b) π²/4 (c) π (d) 2π
**Answer: (b).** 2I = π∫₀^π sin x/(1 + cos²x) dx = π × π/2.

**Q11.** Local maximum value of x³ − 3x?
(a) −2 (b) 2 (c) 0 (d) 1
**Answer: (b).**

**Q12.** Maximum of x(10 − x)?
(a) 20 (b) 25 (c) 50 (d) 100
**Answer: (b).**

**Q13.** ∫₀¹ x² dx?
(a) 1/2 (b) 1/3 (c) 1 (d) 2/3
**Answer: (b).**

**Q14.** ∫₀^π sin x dx?
(a) 0 (b) 1 (c) 2 (d) π
**Answer: (c).**

**Q15.** ∫₀^(π/2) sin x/(sin x + cos x) dx?
(a) π/2 (b) π/4 (c) 1 (d) 0
**Answer: (b).**

**Q16.** ∫₋₁¹ x³ dx?
(a) 1/2 (b) 0 (c) 1/4 (d) 2
**Answer: (b).**

**Q17.** ∫₁ᵉ dx/x?
(a) e (b) 1 (c) 0 (d) e − 1
**Answer: (b).**

**Q18.** ∫x eˣ dx?
(a) x eˣ + C (b) eˣ(x − 1) + C (c) eˣ(x + 1) + C (d) x²eˣ/2 + C
**Answer: (b).**

**Q19.** Area under y = x² from 0 to 3?
(a) 3 (b) 9 (c) 27 (d) 6
**Answer: (b).**

**Q20.** Minimum of x + 1/x for x > 0?
(a) 0 (b) 1 (c) 2 (d) none
**Answer: (c).**

**Q21.** Maximum area of a rectangle with perimeter 20?
(a) 20 (b) 24 (c) 25 (d) 100
**Answer: (c).**

**Q22.** Global maximum of x³ − 3x on [0, 2]?
(a) 0 (b) 2 (c) −2 (d) 4
**Answer: (b).** At the endpoint x = 2.

**Q23.** ∫₀² |x − 1| dx?
(a) 0 (b) 1 (c) 2 (d) 1/2
**Answer: (b).**

**Q24.** ∫sec²x dx?
(a) tan x + C (b) sec x + C (c) −cot x + C (d) sec x tan x + C
**Answer: (a).**

**Q25.** x³ − 3x is decreasing on:
(a) (−1, 1) (b) x > 1 (c) x < −1 (d) everywhere
**Answer: (a).**

**Q26.** f(x) = x³ at x = 0 is:
(a) max (b) min (c) neither (point of inflection) (d) undefined
**Answer: (c).**
