# B2c. Numerical Methods

> When exact algebra fails (x = cos x, ∫e^(−x²) dx, most real differential equations), we **approximate step by step**. Every method here trades accuracy for effort. The exam asks three kinds of questions: run one or two iterations by hand, name a method's order of error, or pick the right method for a situation.

---

## 1. Errors

Let xₜ be the true value and xₐ the approximation.

```
Absolute error = |xₜ − xₐ|
Relative error = |xₜ − xₐ| / |xₜ|
Percentage error = relative error × 100
Approximate relative error (true value unknown) = |xₙ − xₙ₋₁| / |xₙ| × 100
```

True 2.5, approximation 2.4 → relative error 0.04 = **4%**.

**Error sources:** round-off (finite digits), truncation (stopping an infinite process), input/measurement, discretisation.

> Keep extra digits during the calculation; round only the final answer.

---

## 2. Roots of f(x) = 0

**Bracketing idea (IVT):** if f is continuous and f(a)·f(b) < 0, a root lies in (a, b).

### Bisection
Midpoint m = (a + b)/2; keep the half where the sign changes.
- Width after n steps = (b − a)/2ⁿ. Width 3 after 5 steps → **3/32**.
- Steps for accuracy ε: (b − a)/2ⁿ ≤ ε. Interval width 1, ε = 0.01 → 2ⁿ ≥ 100 → **n = 7**.
- Always converges (if bracketed), but slowly (linear).

f(x) = x³ − x − 2 on [1, 2]: f(1) = −2, f(2) = 4. m = 1.5: f = −0.125 → new interval **[1.5, 2]**. Then 1.75 (+) → [1.5, 1.75]; 1.625 (+) → [1.5, 1.625]; 1.5625 (+) → [1.5, 1.5625].

### Regula falsi (false position)
Use the chord's x-intercept instead of the midpoint:
c = [a f(b) − b f(a)] / [f(b) − f(a)].
x² − 2 on [1, 2]: c = (2 + 2)/3 = **1.3333**. Usually faster than bisection, but one end can get stuck.

### Fixed-point iteration
Rewrite as x = g(x) and iterate xₙ₊₁ = g(xₙ). Converges near the root if **|g′(x)| < 1**.

### Newton-Raphson
```
xₙ₊₁ = xₙ − f(xₙ)/f′(xₙ)
```
Follows the tangent line to the x-axis. **Quadratic convergence** (digits of accuracy roughly double each step) near a simple root. Fails if f′ = 0, may jump or converge to another root with a bad start.

- √2 via x² − 2, x₀ = 1: x₁ = 1 − (−1)/2 = 1.5; x₂ = 1.5 − 0.25/3 = **1.4167**.
- eˣ − 2, x₀ = 1: x₁ = 1 − (e − 2)/e = 2/e ≈ **0.74**.
- x² − x − 1, x₀ = 1: x₁ = 1 − (−1)/1 = 2; x₂ = 2 − 1/3 = **1.67**.

### Secant
Newton without the derivative (slope from the last two points):
xₙ₊₁ = xₙ − f(xₙ)(xₙ − xₙ₋₁)/[f(xₙ) − f(xₙ₋₁)]. Order ≈ **1.618**.

| Method | Derivative? | Bracket? | Guaranteed? | Order |
|---|---|---|---|---|
| Bisection | No | Yes | Yes | 1 (linear) |
| Regula falsi | No | Yes | Usually | ~1 |
| Fixed point | No | No | If \|g′\| < 1 | 1 |
| Newton-Raphson | Yes | No | Near a good guess | **2** |
| Secant | No | No | No | ~1.618 |

---

## 3. Linear systems Ax = b

**Direct methods** (exact after finitely many steps):
- **Gaussian elimination:** reduce to upper triangular, back-substitute. Use **partial pivoting** (swap in the largest pivot) to control round-off.
- **Gauss-Jordan:** reduce all the way to the identity; gives the inverse directly.
- **LU decomposition:** A = LU; solve Lz = b then Ux = z. Best when the **same A** is solved for **many b**.

**Iterative methods** (for large sparse systems):
- **Jacobi:** each new iteration uses only **old** values (parallel-friendly).
- **Gauss-Seidel:** uses each **new** value immediately (usually faster).
- **Strict diagonal dominance** guarantees both converge (sufficient, not necessary).

Example: 10x + y + z = 12, 2x + 10y + z = 13, 2x + 2y + 10z = 14 from (0, 0, 0).
- Jacobi iteration 1: (1.2, 1.3, 1.4).
- Gauss-Seidel iteration 1: x = 1.2; y = (13 − 2.4)/10 = **1.06**; z = (14 − 2.4 − 2.12)/10 = 0.948. Closer to the true (1, 1, 1).

---

## 4. Numerical integration

h = (b − a)/n, ordinates y₀ ... yₙ.

```
Trapezoidal:   h/2 [y₀ + yₙ + 2(y₁ + ... + yₙ₋₁)]                   any n
Simpson 1/3:   h/3 [y₀ + yₙ + 4(odd-index y) + 2(even-index interior y)]   n even
Simpson 3/8:   3h/8 [y₀ + 3y₁ + 3y₂ + 2y₃ + 3y₄ + ... + yₙ]            n multiple of 3
```

| Rule | Exact for polynomials up to | Error order | Halving h cuts error to |
|---|---|---|---|
| Trapezoidal | degree 1 | O(h²) | ~1/4 |
| Simpson 1/3 | degree 3 | O(h⁴) | ~1/16 |
| Simpson 3/8 | degree 3 | O(h⁴) | ~1/16 |

- Table x = 0..4, y = 1, 3, 6, 9, 12, trapezoidal: ½[13 + 36] = **24.5**.
- ∫₀¹ x² dx, trapezoidal h = 0.5: 0.25[0 + 1 + 2(0.25)] = **0.375** (true 1/3; trapezoid overestimates a convex curve).
- ∫₀² x² dx, Simpson 1/3 h = 1: ⅓[0 + 4 + 4] = **8/3** (exact).
- ∫₀³ x³ dx, Simpson 3/8 h = 1: ⅜[0 + 3 + 24 + 27] = **20.25** (exact).

> n counts **intervals**; there are n + 1 nodes.

---

## 5. ODEs: dy/dx = f(x, y), y(x₀) = y₀

### Euler
**yₙ₊₁ = yₙ + h f(xₙ, yₙ)**. Global error O(h).
- dy/dx = x + y, y(0) = 1, h = 0.1 → y(0.1) = **1.10**.
- dy/dx = x − y, y(0) = 1, h = 0.2: y₁ = 0.8; y₂ = 0.8 + 0.2(0.2 − 0.8) = **0.68**.

### Modified Euler (Heun): predictor-corrector
Predict yᵖ = yₙ + h f(xₙ, yₙ); correct yₙ₊₁ = yₙ + (h/2)[f(xₙ, yₙ) + f(xₙ₊₁, yᵖ)]. Global O(h²).
dy/dx = x + y, y(0) = 1, h = 0.2: yᵖ = 1.2; end slope 1.4; y = 1 + 0.1(2.4) = **1.24**.

### Runge-Kutta 4
```
k₁ = h f(xₙ, yₙ)
k₂ = h f(xₙ + h/2, yₙ + k₁/2)
k₃ = h f(xₙ + h/2, yₙ + k₂/2)
k₄ = h f(xₙ + h, yₙ + k₃)
yₙ₊₁ = yₙ + (k₁ + 2k₂ + 2k₃ + k₄)/6
```
Global **O(h⁴)**. dy/dx = 2x + y, y(0) = 1, h = 0.2: k = 0.2, 0.26, 0.266, 0.3332 → **1.264**.

| Method | f-evaluations per step | Global error |
|---|---|---|
| Euler | 1 | O(h) |
| Modified Euler / RK2 | 2 | O(h²) |
| RK4 | 4 | O(h⁴) |

### Multi-step methods
Adams-Bashforth (predictor), Adams-Moulton (corrector), Milne. They use several previous points, so they are **not self-starting** (RK4 usually supplies the first values). Euler and RK methods **are** self-starting.

---

## 6. Exam traps

1. Simpson 1/3 needs an even number of intervals; 3/8 needs a multiple of 3.
2. In RK4, h is already inside each k; don't multiply again.
3. Jacobi = old values; Gauss-Seidel = newest values.
4. Newton-Raphson is fast but not guaranteed.
5. Intervals vs nodes.

---

## 7. Practice questions (with solutions)

**Q1.** Bisection on an interval of width 3. Width after 5 steps?
(a) 3/8 (b) 3/16 (c) 3/32 (d) 3/64
**Answer: (c).**

**Q2.** Solve 2x + y = 5, x − y = 1.
(a) (1, 3) (b) (2, 1) (c) (3, −1) (d) (1, 2)
**Answer: (b).**

**Q3.** Simpson 1/3 with h = 1 for ∫₀² x² dx?
(a) 2 (b) 7/3 (c) 8/3 (d) 3
**Answer: (c).**

**Q4.** One Euler step: dy/dx = x + y, y(0) = 1, h = 0.1. y(0.1)?
(a) 1.00 (b) 1.05 (c) 1.10 (d) 1.11
**Answer: (c).**

**Q5.** Global error order of RK4?
(a) O(h) (b) O(h²) (c) O(h³) (d) O(h⁴)
**Answer: (d).**

**Q6.** Which is self-starting?
(a) Adams-Bashforth (b) Milne (c) RK4 (d) Adams-Moulton
**Answer: (c).**

**Q7.** Newton-Raphson on eˣ − 2 = 0, x₀ = 1. x₁ ≈ ?
(a) 0.63 (b) 0.69 (c) 0.74 (d) 0.82
**Answer: (c).**

**Q8.** Newton-Raphson on x² − x − 1 = 0, x₀ = 1. x₂ ≈ ?
(a) 1.50 (b) 1.60 (c) 1.67 (d) 1.75
**Answer: (c).**

**Q9.** Strict diagonal dominance guarantees:
(a) no solution (b) Jacobi and Gauss-Seidel converge (c) det = 0 (d) elimination fails
**Answer: (b).**

**Q10.** Trapezoidal rule on x = 0..4, y = 1, 3, 6, 9, 12?
(a) 22.0 (b) 23.5 (c) 24.5 (d) 26.0
**Answer: (c).**

**Q11.** RK4: dy/dx = 2x + y, y(0) = 1, h = 0.2. y(0.2)?
(a) 1.240 (b) 1.264 (c) 1.280 (d) 1.300
**Answer: (b).**

**Q12.** Newton-Raphson for √2 (f = x² − 2) from x₀ = 1. x₂ ≈ ?
(a) 1.5 (b) 1.4167 (c) 1.4142 (d) 1.4
**Answer: (b).**

**Q13.** Bisection on x³ − x − 2 over [1, 2]. Interval after one step?
(a) [1, 1.5] (b) [1.5, 2] (c) [1.25, 1.5] (d) [1, 2]
**Answer: (b).** f(1.5) = −0.125 < 0.

**Q14.** Minimum bisection steps to bring an interval of width 1 down to 0.01?
(a) 5 (b) 6 (c) 7 (d) 10
**Answer: (c).**

**Q15.** Trapezoidal rule, h = 0.5, for ∫₀¹ x² dx?
(a) 0.333 (b) 0.375 (c) 0.5 (d) 0.25
**Answer: (b).**

**Q16.** Simpson 3/8 with h = 1 for ∫₀³ x³ dx?
(a) 20 (b) 20.25 (c) 21 (d) 27
**Answer: (b).**

**Q17.** Simpson's 1/3 rule requires the number of intervals to be:
(a) odd (b) even (c) multiple of 3 (d) any
**Answer: (b).**

**Q18.** Modified Euler: dy/dx = x + y, y(0) = 1, h = 0.2. y(0.2)?
(a) 1.20 (b) 1.22 (c) 1.24 (d) 1.26
**Answer: (c).**

**Q19.** Euler: dy/dx = x − y, y(0) = 1, h = 0.2. y(0.4)?
(a) 0.64 (b) 0.68 (c) 0.72 (d) 0.80
**Answer: (b).**

**Q20.** Order of convergence of Newton-Raphson (simple root)?
(a) 1 (b) 1.618 (c) 2 (d) 3
**Answer: (c).**

**Q21.** The method that uses each newly computed value immediately:
(a) Jacobi (b) Gauss-Seidel (c) Gauss-Jordan (d) LU
**Answer: (b).**

**Q22.** Best direct method when the same A is solved for many right-hand sides:
(a) Gauss-Jordan (b) Jacobi (c) LU decomposition (d) bisection
**Answer: (c).**

**Q23.** Trapezoidal rule is exact for polynomials up to degree:
(a) 0 (b) 1 (c) 2 (d) 3
**Answer: (b).**

**Q24.** True value 2.5, approximation 2.4. Percentage error?
(a) 0.1% (b) 4% (c) 4.17% (d) 10%
**Answer: (b).**

**Q25.** Halving h in the trapezoidal rule reduces the error roughly by a factor of:
(a) 2 (b) 4 (c) 8 (d) 16
**Answer: (b).**

**Q26.** Gauss-Seidel on 10x + y + z = 12, 2x + 10y + z = 13, 2x + 2y + 10z = 14 from zeros. y after iteration 1?
(a) 1.3 (b) 1.06 (c) 1.2 (d) 0.948
**Answer: (b).**

**Q27.** Regula falsi on x² − 2 over [1, 2]. First estimate?
(a) 1.5 (b) 1.3333 (c) 1.4142 (d) 1.25
**Answer: (b).**
