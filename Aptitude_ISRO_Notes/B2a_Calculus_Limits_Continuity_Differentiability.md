# B2a. Calculus: Limits, Continuity and Differentiability

> A **limit** asks "where is the function heading?", not "where is it?". **Continuity** says the function arrives where it was heading (no gaps, no jumps). **Differentiability** says it arrives smoothly (no corners). Each is a stronger promise than the last.

---

## 1. Limits

lim(x→a) f(x) = L means f(x) gets as close as we like to L as x approaches a. f(a) itself may be different or undefined.

### Indeterminate forms (need work)

```
0/0   ∞/∞   ∞ − ∞   0 × ∞   1^∞   0⁰   ∞⁰
```

If direct substitution gives anything else (like 3/2, or 5/0 → ∞), you are done.

### Methods

1. **Direct substitution** when not indeterminate.
2. **Factorise and cancel:** (x² − 4)/(x − 2) = x + 2 → at 2: **4**.
3. **Rationalise:** (√(1 + x) − 1)/x × (√(1 + x) + 1)/(√(1 + x) + 1) = 1/(√(1 + x) + 1) → **1/2**.
4. **Standard limits** (memorise):

```
lim(x→0) sin x / x = 1            lim(x→0) tan x / x = 1
lim(x→0) (1 − cos x)/x² = 1/2     lim(x→0) (eˣ − 1)/x = 1
lim(x→0) (aˣ − 1)/x = ln a        lim(x→0) ln(1 + x)/x = 1
lim(x→a) (xⁿ − aⁿ)/(x − a) = n aⁿ⁻¹
lim(x→∞) (1 + k/x)ˣ = eᵏ          lim(x→0) (1 + x)^(1/x) = e
```

5. **L'Hôpital's rule** (0/0 or ∞/∞ only): replace f/g by f′/g′. Differentiate top and bottom **separately**.

### Scaling standard limits

- sin 3x / x = 3 · (sin 3x)/(3x) → **3**.
- (e²ˣ − 1)/x → **2**.
- tan 2x / sin 5x → **2/5**.

### Limits at infinity (rational functions)

Compare the highest powers:
- Equal degree → ratio of leading coefficients: (3x² + 2x)/(x² + 5) → **3**.
- Top degree lower → **0**.
- Top degree higher → **∞**.

### Squeeze

x sin(1/x) → **0** as x → 0, since |x sin(1/x)| ≤ |x|.

---

## 2. Continuity

f is continuous at a if **LHL = RHL = f(a)**.

| Discontinuity | What happens | Example |
|---|---|---|
| Removable | Limit exists but ≠ f(a) or f(a) undefined | (x² − 1)/(x − 1) at 1 |
| Jump | LHL ≠ RHL | step function |
| Infinite | Limit is infinite | 1/x at 0 |

Sums, products, compositions of continuous functions are continuous (quotients too, where the denominator ≠ 0). Polynomials, eˣ, sin, cos are continuous everywhere.

**Finding k for continuity:** f(x) = kx + 1 (x ≤ 2), 3x − 1 (x > 2). Match at 2: 2k + 1 = 5 → **k = 2**.

**Intermediate Value Theorem (IVT):** f continuous on [a, b] takes every value between f(a) and f(b). In particular, a sign change means a root in between.

---

## 3. Differentiability

f is differentiable at a if lim(h→0) [f(a + h) − f(a)]/h exists: the **left and right derivatives are equal**.

**Differentiable ⟹ continuous**, but not the other way. |x| is continuous at 0 with left derivative −1 and right derivative +1 → a corner → **not differentiable**.

Red flags: corners (|x − 3| at 3), cusps (x^(2/3) at 0), vertical tangents (x^(1/3) at 0), any discontinuity.

### Useful derivative facts

- Derivative of an even function is odd, and vice versa.
- d/dx of xⁿ = nxⁿ⁻¹; sin → cos; cos → −sin; eˣ → eˣ; ln x → 1/x.
- Product (uv)′ = u′v + uv′; quotient (u/v)′ = (u′v − uv′)/v²; chain rule f(g(x))′ = f′(g(x))g′(x).

---

## 4. Mean value theorems

**Rolle's:** f continuous on [a, b], differentiable on (a, b), f(a) = f(b) → some c in (a, b) has **f′(c) = 0**.

**Lagrange (LMVT):** same conditions without f(a) = f(b) → **f′(c) = [f(b) − f(a)]/(b − a)**. Geometrically: a tangent parallel to the chord.
f(x) = x² on [1, 3]: (9 − 1)/2 = 4 = 2c → **c = 2**.

**Cauchy:** [f(b) − f(a)]/[g(b) − g(a)] = f′(c)/g′(c).

Rolle's is LMVT with equal end values.

---

## 5. Worked GATE problems

**GATE 2015:** f(x) = x^(−1/3) on [−1, 1]. f blows up at 0 → not continuous, not bounded; area = 2∫₀¹ x^(−1/3) dx = 2 × 3/2 = 3 (finite, non-zero). Statements 2 and 3 true.

**GATE 2014:** f continuous on [0, 2], f(0) = f(2) = −1, f(1) = 1. Let g(y) = f(y) − f(y + 1) on [0, 1]: g(0) = −2, g(1) = 2 → by IVT some y has f(y) = f(y + 1).

**GATE 2016:** deg(f(x) + f(−x)) = 10 → f is even with degree 10 → f′ is odd → g(x) − g(−x) = 2f′(x), degree **9**.

**GATE 2014:** f = x sin x; f″ = −x sin x + 2 cos x; f″ + f + t cos x = 0 → (2 + t)cos x = 0 → **t = −2**.

---

## 6. Exam traps

1. Use L'Hôpital only on 0/0 or ∞/∞, and re-check after each round.
2. Continuity does not imply differentiability.
3. Differentiate numerator and denominator separately (not the quotient rule).
4. 1^∞ is indeterminate (it's where e comes from).

---

## 7. Practice questions (with solutions)

**Q1.** lim(x→0) sin 3x / x?
(a) 0 (b) 1 (c) 3 (d) 1/3
**Answer: (c).**

**Q2.** lim(x→2) (x² − 4)/(x − 2)?
(a) 0 (b) 2 (c) 4 (d) does not exist
**Answer: (c).**

**Q3.** lim(x→0) (e²ˣ − 1)/x?
(a) 0 (b) 1 (c) 2 (d) e²
**Answer: (c).**

**Q4.** lim(x→∞) (3x² + 2x)/(x² + 5)?
(a) 0 (b) 1 (c) 3 (d) ∞
**Answer: (c).**

**Q5.** Why is applying L'Hôpital to lim(x→0) (x + 1)/(x + 2) wrong?
(a) it isn't (b) the form isn't indeterminate (c) needs trig (d) only works at ∞
**Answer: (b).** Direct substitution gives 1/2.

**Q6.** f(x) = (x² − 1)/(x − 1) for x ≠ 1, f(1) = 5. Discontinuity at 1?
(a) jump (b) infinite (c) removable (d) none
**Answer: (c).** Limit 2 ≠ 5.

**Q7.** Is |x| differentiable at 0?
(a) yes, it's continuous (b) no, a sharp corner (c) yes, derivative 1 (d) no, it's discontinuous
**Answer: (b).**

**Q8.** f continuous on [1, 5], differentiable on (1, 5), f(1) = f(5) = 7. Some c has f′(c) = 0 by:
(a) LMVT (b) Rolle's (c) Cauchy (d) L'Hôpital
**Answer: (b).**

**Q9.** f(x) = x^(−1/3), A = area between f and the x-axis on [−1, 1]. True statements: (1) f continuous on [−1, 1]; (2) f not bounded; (3) A non-zero and finite.
(a) 2 only (b) 3 only (c) 2 and 3 (d) 1, 2, 3
**Answer: (c).**

**Q10.** f continuous on [0, 2], f(0) = f(2) = −1, f(1) = 1. Which must be true?
(a) ∃ y ∈ (0, 1): f(y) = f(y + 1) (b) ∀ y: f(y) = f(2 − y) (c) max on (0, 2) is 1 (d) ∃ y: f(y) = −f(2 − y)
**Answer: (a).**

**Q11.** f polynomial, g = f′, deg(f(x) + f(−x)) = 10. deg(g(x) − g(−x))?
(a) 8 (b) 9 (c) 10 (d) 11
**Answer: (b).**

**Q12.** f(x) = x sin x satisfies f″ + f + t cos x = 0. t = ?
(a) −1 (b) −2 (c) 1 (d) 2
**Answer: (b).**

**Q13.** lim(x→0) (1 − cos x)/x²?
(a) 0 (b) 1 (c) 1/2 (d) 2
**Answer: (c).**

**Q14.** lim(x→∞) (1 + 2/x)ˣ?
(a) e (b) e² (c) 2 (d) 1
**Answer: (b).**

**Q15.** lim(x→0) (√(1 + x) − 1)/x?
(a) 0 (b) 1/2 (c) 1 (d) 2
**Answer: (b).**

**Q16.** lim(x→∞) (2x³ + 1)/(x² + 5)?
(a) 0 (b) 2 (c) ∞ (d) 1/5
**Answer: (c).**

**Q17.** lim(x→0) x sin(1/x)?
(a) 0 (b) 1 (c) ∞ (d) does not exist
**Answer: (a).**

**Q18.** lim(x→0) tan 2x / sin 5x?
(a) 5/2 (b) 2/5 (c) 1 (d) 10
**Answer: (b).**

**Q19.** lim(x→1) (x⁵ − 1)/(x − 1)?
(a) 1 (b) 4 (c) 5 (d) 0
**Answer: (c).**

**Q20.** f(x) = kx + 1 (x ≤ 2), 3x − 1 (x > 2) is continuous. k = ?
(a) 1 (b) 2 (c) 3 (d) 5/2
**Answer: (b).**

**Q21.** f(x) = |x − 3| is not differentiable at:
(a) 0 (b) 3 (c) −3 (d) everywhere
**Answer: (b).**

**Q22.** LMVT for f(x) = x² on [1, 3] gives c = ?
(a) 1.5 (b) 2 (c) 2.5 (d) √5
**Answer: (b).**

**Q23.** lim(x→0) (2ˣ − 1)/x?
(a) 1 (b) 2 (c) ln 2 (d) 0
**Answer: (c).**

**Q24.** Which is true?
(a) continuous ⟹ differentiable (b) differentiable ⟹ continuous (c) both (d) neither
**Answer: (b).**

**Q25.** Which is NOT an indeterminate form?
(a) 0/0 (b) 1^∞ (c) 0 × ∞ (d) 0/∞
**Answer: (d).** 0/∞ = 0.
