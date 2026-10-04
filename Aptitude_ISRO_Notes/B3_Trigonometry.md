# B3. Trigonometry

> Trigonometry is the study of a point moving around a circle of radius 1: **cos θ is its x-coordinate, sin θ its y-coordinate**. Signs in each quadrant, the allied-angle rules and sin² + cos² = 1 all fall straight out of that picture.

---

## 1. Ratios and signs

In a right triangle with angle θ: sin = opposite/hypotenuse, cos = adjacent/hypotenuse, tan = opposite/adjacent = sin/cos. Reciprocals: cosec = 1/sin, sec = 1/cos, cot = 1/tan.

**ASTC ("All Silver Tea Cups"):** which functions are positive in each quadrant.

| Quadrant | Angles | Positive |
|---|---|---|
| I | 0° to 90° | All |
| II | 90° to 180° | Sin (cosec) |
| III | 180° to 270° | Tan (cot) |
| IV | 270° to 360° | Cos (sec) |

## 2. Standard values

| | 0° | 30° | 45° | 60° | 90° |
|---|---|---|---|---|---|
| sin | 0 | 1/2 | 1/√2 | √3/2 | 1 |
| cos | 1 | √3/2 | 1/√2 | 1/2 | 0 |
| tan | 0 | 1/√3 | 1 | √3 | undefined |

Memory trick: sin goes √0/2, √1/2, √2/2, √3/2, √4/2; cos is the same list reversed.

**Radians:** π rad = 180°. π/6 = 30°, π/4 = 45°, π/3 = 60°, π/2 = 90°.

## 3. Allied angles (n·90° ± θ)

1. **Odd multiple of 90°** (90°, 270°): the function **changes** name (sin ↔ cos, tan ↔ cot, sec ↔ cosec).
2. **Even multiple** (180°, 360°): the name **stays**.
3. The **sign** is whatever the original function has in the quadrant where the angle lands (treat θ as small and acute).

- sin(90° + θ) = cos θ (QII, sin +).
- cos(90° + θ) = −sin θ (QII, cos −).
- cos(180° + θ) = −cos θ.
- sin 120° = sin(180° − 60°) = **√3/2**.
- sin 210° = −sin 30° = **−1/2**.
- cos 300° = cos(360° − 60°) = **1/2**.
- tan 135° = −tan 45° = **−1**.

## 4. Identities

```
sin²θ + cos²θ = 1
1 + tan²θ = sec²θ       (divide by cos²)
1 + cot²θ = cosec²θ     (divide by sin²)
```

### Compound and double angles

```
sin(A ± B) = sin A cos B ± cos A sin B
cos(A ± B) = cos A cos B ∓ sin A sin B
tan(A + B) = (tan A + tan B)/(1 − tan A tan B)
sin 2A = 2 sin A cos A
cos 2A = cos²A − sin²A = 2cos²A − 1 = 1 − 2sin²A
```

- sin 75° = sin(45° + 30°) = **(√6 + √2)/4**.
- tan 15° = tan(45° − 30°) = **2 − √3**.
- sin A = 3/5 (A acute) → cos A = 4/5 → sin 2A = **24/25**.
- cos A = 3/5 → cos 2A = 2(9/25) − 1 = **−7/25**.

**Maximum of a sin x + b cos x = √(a² + b²).** 3 sin x + 4 cos x → max **5**.

**Periods:** sin, cos: 2π. tan, cot: π.

---

## 5. Triangles

```
Sine rule:    a/sin A = b/sin B = c/sin C = 2R
Cosine rule:  a² = b² + c² − 2bc cos A   →   cos A = (b² + c² − a²)/(2bc)
Area = ½ ab sin C = rs = abc/(4R)
```

- a = 7, b = 8, c = 9 → cos A = (64 + 81 − 49)/144 = **0.6**.
- a = 5, b = 8, C = 30° → area = ½ × 5 × 8 × ½ = **10**.
- a = 10, A = 30° → 2R = 10/0.5 = 20 → **R = 10**.

## 6. Heights and distances

**tan(angle) = height / horizontal distance.** Angles of elevation (looking up) and depression (looking down) are both measured from the **horizontal**.

- 40 m from a tower, elevation 30° → height 40/√3 ≈ **23.09 m**.
- 10 m from a tower, elevation 60° → **10√3 m**.
- Shadow equals height → **45°**.
- Ladder 10 m at 60° to the ground → height 10 sin 60° = **5√3 m**.
- Cliff 60 m high, boat 60 m from the base → depression **45°**.
- Towers 50 m and 80 m, 100 m apart; equal elevation point: 50/x = 80/(100 − x) → x = 500/13 ≈ **38.46 m** from the shorter.

## 7. Inverse trigonometric functions

| Function | Principal range |
|---|---|
| sin⁻¹x | [−π/2, π/2] |
| cos⁻¹x | [0, π] |
| tan⁻¹x | (−π/2, π/2) |
| cot⁻¹x | (0, π) |

- sin⁻¹(1/2) = π/6; cos⁻¹(−1/2) = **2π/3** (must lie in [0, π]).
- **sin⁻¹x + cos⁻¹x = π/2**; tan⁻¹x + cot⁻¹x = π/2.

## 8. General solutions

```
sin θ = sin α  →  θ = nπ + (−1)ⁿ α
cos θ = cos α  →  θ = 2nπ ± α
tan θ = tan α  →  θ = nπ + α
```

---

## 9. Exam traps

1. Elevation/depression from the horizontal, not the vertical.
2. Odd multiples of 90° change the function name.
3. cos⁻¹ range is [0, π], sin⁻¹ range is [−π/2, π/2].
4. Calculator-free: know the standard values cold.

---

## 10. Practice questions (with solutions)

**Q1.** tan positive but sin negative in quadrant:
(a) I (b) II (c) III (d) IV
**Answer: (c).**

**Q2.** sin 30° + cos 60°?
(a) 0.5 (b) 1 (c) 1.5 (d) 2
**Answer: (b).**

**Q3.** sin 120°?
(a) 1/2 (b) −1/2 (c) √3/2 (d) −√3/2
**Answer: (c).**

**Q4.** cos(90° + θ)?
(a) cos θ (b) −cos θ (c) sin θ (d) −sin θ
**Answer: (d).**

**Q5.** A 10 m ladder makes 60° with the ground. Height reached?
(a) 5 m (b) 5√3 m (c) 10√3 m (d) 10 m
**Answer: (b).**

**Q6.** 40 m from a tower's base, elevation 30°. Height?
(a) 20 m (b) 23.09 m (c) 40 m (d) 69.28 m
**Answer: (b).**

**Q7.** a = 7, b = 8, c = 9. cos A?
(a) 0.5 (b) 0.6 (c) 0.7 (d) 0.4
**Answer: (b).**

**Q8.** Principal range of cos⁻¹x?
(a) [−π/2, π/2] (b) [0, π] (c) (−π/2, π/2) (d) [−π, π]
**Answer: (b).**

**Q9.** General solution of sin θ = 1/2?
(a) nπ + (−1)ⁿ π/6 (b) 2nπ ± π/6 (c) nπ + π/6 (d) nπ/6
**Answer: (a).**

**Q10.** Towers 50 m and 80 m are 100 m apart. Distance from the 50 m tower to the point of equal elevation?
(a) 30.77 m (b) 38.46 m (c) 50 m (d) 61.54 m
**Answer: (b).**

**Q11.** sec²45° − tan²45°?
(a) 0 (b) 1 (c) 2 (d) √2
**Answer: (b).**

**Q12.** tan 45° + cot 45°?
(a) 1 (b) 2 (c) 0 (d) √2
**Answer: (b).**

**Q13.** sin A = 3/5, A acute. sin 2A?
(a) 6/5 (b) 24/25 (c) 12/25 (d) 7/25
**Answer: (b).**

**Q14.** cos A = 3/5. cos 2A?
(a) 7/25 (b) −7/25 (c) 1/5 (d) 24/25
**Answer: (b).**

**Q15.** sin 75°?
(a) (√6 + √2)/4 (b) (√6 − √2)/4 (c) √3/2 (d) (√3 + 1)/2
**Answer: (a).**

**Q16.** tan 15°?
(a) 2 + √3 (b) 2 − √3 (c) √3 − 1 (d) 1/√3
**Answer: (b).**

**Q17.** sin 210°?
(a) 1/2 (b) −1/2 (c) √3/2 (d) −√3/2
**Answer: (b).**

**Q18.** cos 300°?
(a) 1/2 (b) −1/2 (c) √3/2 (d) −√3/2
**Answer: (a).**

**Q19.** tan 135°?
(a) 1 (b) −1 (c) √3 (d) 0
**Answer: (b).**

**Q20.** A pole's shadow equals its height. Sun's elevation?
(a) 30° (b) 45° (c) 60° (d) 90°
**Answer: (b).**

**Q21.** Triangle with a = 5, b = 8, C = 30°. Area?
(a) 20 (b) 10 (c) 10√3 (d) 40
**Answer: (b).**

**Q22.** a = 10, A = 30°. Circumradius R?
(a) 5 (b) 10 (c) 20 (d) 10√3
**Answer: (b).**

**Q23.** cos⁻¹(−1/2)?
(a) −π/3 (b) 2π/3 (c) 4π/3 (d) π/3
**Answer: (b).**

**Q24.** sin⁻¹x + cos⁻¹x?
(a) 0 (b) π/2 (c) π (d) depends on x
**Answer: (b).**

**Q25.** Maximum of 3 sin x + 4 cos x?
(a) 7 (b) 5 (c) 4 (d) 1
**Answer: (b).**

**Q26.** Period of tan x?
(a) π/2 (b) π (c) 2π (d) 4π
**Answer: (b).**

**Q27.** From the top of a 60 m cliff, a boat 60 m from the base is seen. Angle of depression?
(a) 30° (b) 45° (c) 60° (d) 90°
**Answer: (b).**

**Q28.** 5π/6 radians in degrees?
(a) 120° (b) 135° (c) 150° (d) 165°
**Answer: (c).**
