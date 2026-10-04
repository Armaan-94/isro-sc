# C03. Syllogisms

> A syllogism is a logic game played **only with the given statements**. Real-world truth is irrelevant: if the statement says "All cats are trees", accept it. The golden rule: **a conclusion follows only if it is true in every possible diagram** that fits the statements.

---

## 1. The four statement types

| Code | Form | Example |
|---|---|---|
| **A** | All A are B | All cats are animals |
| **E** | No A is B | No cat is a dog |
| **I** | Some A are B | Some cats are black |
| **O** | Some A are not B | Some cats are not black |

"Some" means "at least one, possibly all". So "Some A are B" is still true if all A are B.

## 2. Venn diagram method

1. Draw the statements with the **minimum** overlap that satisfies them.
2. Test each conclusion. If you can draw **any** valid diagram where it fails, it does **not** follow.
3. A conclusion follows only if it holds in **all** valid diagrams.

## 3. Conversion (swapping subject and predicate)

| Statement | Valid conversion |
|---|---|
| All A are B | **Some** B are A (not "All B are A") |
| No A is B | No B is A |
| Some A are B | Some B are A |
| Some A are not B | **No valid conversion** |

## 4. Combining two statements (the middle term must link them)

| Statement 1 | Statement 2 | Conclusion |
|---|---|---|
| All A are B | All B are C | **All A are C** |
| All A are B | No B is C | **No A is C** |
| Some A are B | All B are C | **Some A are C** |
| Some A are B | No B is C | **Some A are not C** |
| No A is B | All B are C | Some C are not A |
| All A are B | All C are B | **No conclusion** about A and C |
| Some A are B | Some B are C | **No conclusion** about A and C |
| No A is B | No B is C | **No conclusion** about A and C |

Memory aids:
- Two "Some" statements → no definite conclusion.
- Two negatives → no definite conclusion.
- A negative premise → any conclusion must be negative.
- A "Some" premise → any conclusion must be "Some".

> **The same-B fallacy:** "All doctors are engineers" + "All lawyers are engineers" says nothing about doctors and lawyers. Both groups sit inside the same big circle, but they may or may not overlap.

## 5. Complementary pairs: "either I or II"

If two conclusions have the same subject and predicate, neither follows on its own, but **one of them must be true**, choose "either I or II". Valid pairs:
- Some A are B / No A is B.
- All A are B / Some A are not B.
- Some A are B / Some A are not B (when nothing else is known, at least one holds, since A exists).

## 6. Possibility questions

"X is a possibility" is true if **at least one** valid diagram makes X true (and it doesn't contradict a definite statement).

All cars are buses, some buses are trucks. "All trucks being cars is a possibility"? Draw trucks inside cars, which are inside buses: still "some buses are trucks" ✓. **Possible.**

---

## 7. Practice questions (with solutions)

**Q1.** All pens are books. All books are tables. I: All pens are tables. II: Some tables are pens.
(a) only I (b) only II (c) both (d) neither
**Answer: (c).**

**Q2.** No apple is a mango. All mangoes are fruits. Conclusion: Some fruits are not apples.
(a) follows (b) doesn't follow (c) possible only (d) can't say
**Answer: (a).** The mangoes are fruits that aren't apples.

**Q3.** All doctors are engineers. All lawyers are engineers. Conclusion: Some doctors are lawyers.
(a) follows (b) doesn't follow (c) definitely false (d) can't say
**Answer: (b).**

**Q4.** Some students are athletes. All athletes are disciplined. Conclusion: Some students are disciplined.
(a) follows (b) doesn't follow (c) possible only (d) can't say
**Answer: (a).**

**Q5.** Some flowers are red. Conclusion: Some flowers are not red.
(a) follows (b) doesn't follow (possible but not forced) (c) definitely false (d) both always true
**Answer: (b).**

**Q6.** All squares are rectangles. No rectangle is a triangle. Some triangles are polygons. I: No square is a triangle. II: Some polygons are not squares.
(a) only I (b) only II (c) both (d) neither
**Answer: (c).**

**Q7.** Some books are pens. Some pens are pencils. All pencils are erasers. Conclusion: Some erasers are books.
(a) definitely follows (b) possible only (c) definitely false (d) can't say
**Answer: (b).**

**Q8.** All coins are metals. Valid conclusion:
(a) All metals are coins (b) Some metals are coins (c) No metal is a coin (d) Some coins are not metals
**Answer: (b).**

**Q9.** No teacher is lazy. Some lazy people are students. Conclusion: Some students are not teachers.
(a) follows (b) doesn't follow (c) possible only (d) definitely false
**Answer: (a).**

**Q10.** Some cats are dogs. I: All cats are dogs. II: Some cats are not dogs.
(a) only I (b) only II (c) either I or II (d) neither
**Answer: (c).**

**Q11.** All A are B. No B is C. Conclusion: No A is C.
(a) follows (b) doesn't follow (c) possible (d) can't say
**Answer: (a).**

**Q12.** Some A are B. No B is C. Conclusion: Some A are not C.
(a) follows (b) doesn't follow (c) possible only (d) false
**Answer: (a).**

**Q13.** No A is B. No B is C. I: No A is C. II: Some A are C.
(a) only I (b) only II (c) either I or II (d) neither
**Answer: (c).** Neither is forced, but they are a complementary pair about A and C.

**Q14.** All roses are flowers. Some flowers fade quickly. Conclusion: Some roses fade quickly.
(a) follows (b) doesn't follow (c) definitely false (d) can't say
**Answer: (b).**

**Q15.** All cars are buses. Some buses are trucks. Conclusion: All trucks being cars is a possibility.
(a) true (b) false (c) can't say (d) definitely true for all diagrams
**Answer: (a).**

**Q16.** Valid conversion of "No A is B":
(a) All B are A (b) No B is A (c) Some B are A (d) none
**Answer: (b).**

**Q17.** Which statement has no valid conversion?
(a) All A are B (b) No A is B (c) Some A are B (d) Some A are not B
**Answer: (d).**

**Q18.** All A are B. Some C are A. Conclusion: Some C are B.
(a) follows (b) doesn't follow (c) possible only (d) false
**Answer: (a).**

**Q19.** All A are B. Conclusion: All B are A.
(a) follows (b) doesn't follow, but is possible (c) definitely false (d) can't say
**Answer: (b).**

**Q20.** Some pens are pencils. All pencils are erasers. I: Some pens are erasers. II: All erasers are pens.
(a) only I (b) only II (c) both (d) neither
**Answer: (a).**

**Q21.** All birds are animals. No animal is a plant. I: No bird is a plant. II: Some animals are birds.
(a) only I (b) only II (c) both (d) neither
**Answer: (c).**

**Q22.** Some boys are tall. Some tall people are strong. Conclusion: Some boys are strong.
(a) follows (b) doesn't follow (c) definitely false (d) either-or
**Answer: (b).** Two "Some" premises give no definite conclusion.
