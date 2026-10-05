# 21. String Matching: Naive, Rabin-Karp, Finite Automata and KMP

> **The problem:** find every place a pattern P (length m) occurs in a text T (length n). The naive way re-checks characters over and over. Smarter algorithms avoid that waste: Rabin-Karp by comparing **hashes**, KMP by remembering **what has already matched**.

---

## 1. Setup

- Text **T[1..n]**, pattern **P[1..m]**, m ≤ n.
- A **valid shift** s means P occurs starting at T[s + 1] (or at position s + 1, 1-indexed).
- There are **n − m + 1** possible alignments.

---

## 2. Naive (brute-force) matching

```c
for (s = 0; s <= n - m; s++) {
    j = 0;
    while (j < m && T[s + j] == P[j]) j++;
    if (j == m) printf("match at %d\n", s);
}
```

Try every alignment; compare character by character.

- **Worst case: O((n − m + 1) · m) = O(nm).** Example: T = "AAAA...A", P = "AAAB": almost every alignment matches m − 1 characters before failing.
- Best case: O(n) (first character mismatches nearly everywhere).
- Space O(1). No preprocessing.

Number of character comparisons in the worst case = m(n − m + 1).

### Worked example

T = **AABAACAADAABAABA** (n = 16), P = **AABA** (m = 4). Positions (1-based):

```
pos: 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16
T:   A A B A A C A A D A  A  B  A  A  B  A
```

- s = 1: AABA ✓ **match at 1**.
- s = 10: T[10..13] = A A B A ✓ **match at 10**.
- s = 13: T[13..16] = A A B A ✓ **match at 13** (overlaps with the previous match: allowed).

Matches at **1, 10, 13**.

---

## 3. Rabin-Karp

### Idea

Treat each length-m string as a **number** (in base d, e.g. d = 10 for digits, 256 for bytes) **modulo a prime q**. Compare the pattern's hash with each window's hash. Only when hashes match, verify character by character.

### Rolling hash: O(1) update per shift

If window value is t_s, the next window is

```
t_{s+1} = ( d · (t_s − T[s+1] · h) + T[s+m+1] ) mod q,     h = d^(m−1) mod q
```

"Remove the leading digit, shift left, add the new trailing digit."

Example in decimal (no mod): window 31415 → next 14152: (31415 − 3·10000)·10 + 2 = 1415·10 + 2 = 14152.

### Spurious hits

Different strings can have the same hash (mod q). A hash match that turns out **not** to be a real match is a **spurious hit**. That's why verification is mandatory.

### Worked example (CLRS)

T = **2359023141526739921**, P = **31415**, q = 13, d = 10.
- P mod 13 = 31415 mod 13 = **7**.
- Windows with hash 7: **31415** (a **valid** match, starting at position **7**) and **67399** (67399 mod 13 = 7, a **spurious hit**).
- Number of windows = 19 − 5 + 1 = 15.

### Complexity

- Preprocessing: **O(m)**.
- Expected/average matching: **O(n + m)** (with a good q, few spurious hits).
- **Worst case: O(nm)** (every window a hash hit, e.g. T = "aaaa...", P = "aa...a").
- Great for **multiple patterns** at once (hash all patterns, scan once) and for 2-D pattern matching / plagiarism detection.

---

## 4. Finite automaton matcher

Build a DFA with states 0..m, where state q means "the last q characters read match the first q characters of P". From each state, for each alphabet symbol, the transition goes to the length of the **longest prefix of P that is a suffix** of (current matched prefix + new symbol). Reaching state m = a match.

- Preprocessing: O(m · |Σ|) (with a clever construction; naive is O(m³ |Σ|)).
- Matching: **O(n)**, examining each text character exactly once.

---

## 5. Knuth-Morris-Pratt (KMP)

### The insight

When a mismatch happens after matching q characters, we **already know** those q text characters (they equal P[1..q]). So we can shift the pattern to the next position that's consistent with them, **without re-reading text characters**.

### The prefix (failure) function π

**π[q] = length of the longest proper prefix of P[1..q] that is also a suffix of P[1..q].**

("Proper" means not the whole string.)

**Example: P = ABABACA**

| q | P[1..q] | Longest proper prefix = suffix | π[q] |
|---|---|---|---|
| 1 | A | (none) | 0 |
| 2 | AB | (none) | 0 |
| 3 | ABA | A | 1 |
| 4 | ABAB | AB | 2 |
| 5 | ABABA | ABA | 3 |
| 6 | ABABAC | (none) | 0 |
| 7 | ABABACA | A | 1 |

π = **[0, 0, 1, 2, 3, 0, 1]**.

More examples:
- **AAAA** → [0, 1, 2, 3].
- **ABCDABD** → [0, 0, 0, 0, 1, 2, 0].
- **AABAAAB** → [0, 1, 0, 1, 2, 2, 3].
- **ABCABCAB** → [0, 0, 0, 1, 2, 3, 4, 5].

Let's verify AABAAAB: A 0; AA 1; AAB 0; AABA 1; AABAA 2; AABAAA: longest prefix = suffix is "AA" → 2; AABAAAB: "AAB" → 3. ✓

### Matching

```
q = 0                                    // characters matched so far
for i = 1 to n:
    while q > 0 and P[q+1] != T[i]: q = π[q]     // fall back
    if P[q+1] == T[i]: q = q + 1
    if q == m: report match at i − m + 1; q = π[q]
```

The text pointer i **never moves backward**.

### Complexity

- Prefix function: **O(m)**.
- Matching: **O(n)** (amortised: q can't decrease more than it increases).
- **Total O(n + m) in the worst case.** Space O(m).

---

## 6. Other algorithms (recall)

- **Boyer-Moore:** compares the pattern **right to left**, uses "bad character" and "good suffix" rules to jump ahead; often **sublinear** in practice (skips text). Worst case O(nm) in the basic form.
- **Aho-Corasick:** many patterns at once with an automaton: O(n + total pattern length + matches).
- **Z-algorithm:** O(n + m) using the Z-array.
- **Suffix trees/arrays:** after O(n) preprocessing of the text, answer any pattern query in O(m) (+ occurrences).

---

## 7. Comparison

| Algorithm | Preprocessing | Matching (worst) | Matching (average) | Extra space |
|---|---|---|---|---|
| Naive | none | **O(nm)** | O(nm) (often fast in practice) | O(1) |
| Rabin-Karp | O(m) | **O(nm)** | **O(n + m)** | O(1) |
| Finite automaton | O(m·\|Σ\|) | **O(n)** | O(n) | O(m·\|Σ\|) |
| **KMP** | **O(m)** | **O(n)** | O(n) | O(m) |
| Boyer-Moore | O(m + \|Σ\|) | O(nm) basic | often sublinear | O(m + \|Σ\|) |

> **Trap:** Rabin-Karp's **average** is as good as KMP, but only KMP **guarantees** O(n + m) in the worst case.

---

## 8. Exam traps

1. Number of alignments = n − m + 1.
2. Naive worst case O(nm) (e.g. T = aaa...a, P = aa...ab).
3. Rabin-Karp: hash match must be verified (spurious hits); rolling hash O(1) per shift.
4. π[q] = longest **proper** prefix that's also a suffix.
5. KMP never moves the text pointer backward → O(n + m) worst case.
6. Finite automaton matching is O(n) after preprocessing.
7. Matches may overlap.

---

## 9. Practice questions

**Q1.** T = "AABAACAADAABAABA", P = "AABA". Match positions (1-based)?
(a) 1, 5, 9 (b) 1, 10, 13 (c) 1, 4, 10 (d) 10, 13, 16

**Answer: (b).**

---

**Q2.** Worst-case time of naive matching?
(a) O(n + m) (b) O(n log m) (c) O(nm) (d) O(log n)

**Answer: (c).**

---

**Q3.** Prefix function of P = "ABABACA"?
(a) [0, 0, 1, 2, 3, 0, 1] (b) [0, 1, 2, 3, 4, 0, 1] (c) [0, 0, 1, 2, 0, 0, 1] (d) [0, 0, 0, 1, 2, 0, 1]

**Answer: (a).**

---

**Q4.** Prefix function of P = "AAAA"?
(a) [0, 0, 0, 0] (b) [0, 1, 2, 3] (c) [1, 2, 3, 4] (d) [0, 1, 1, 1]

**Answer: (b).**

---

**Q5.** Rabin-Karp with q = 13: T = "2359023141526739921", P = "31415". Number of spurious hits?
(a) 0 (b) 1 (c) 2 (d) 3

**Answer: (b).** The window "67399".

---

**Q6.** Why must Rabin-Karp verify a hash match character by character?
(a) It doesn't need to (b) Different strings can share a hash value (c) To compute the next hash (d) Only for long patterns

**Answer: (b).**

---

**Q7.** Which algorithm guarantees O(n + m) in the worst case?
(a) Naive (b) Rabin-Karp (c) KMP (d) Basic Boyer-Moore

**Answer: (c).**

---

**Q8.** For n = 20 and m = 5, how many alignments does the naive algorithm check?
(a) 15 (b) 16 (c) 20 (d) 100

**Answer: (b).**

---

**Q9.** Worst-case character comparisons of the naive algorithm for n = 10, m = 3?
(a) 30 (b) 24 (c) 21 (d) 8

**Answer: (b).** m(n − m + 1) = 3 × 8.

---

**Q10.** Rolling hash: in decimal, the window "31415" slides to the next window when the incoming digit is 2. New value?
(a) 31452 (b) 14152 (c) 14155 (d) 41520

**Answer: (b).** (31415 − 3 × 10⁴) × 10 + 2.

---

**Q11.** π for P = "ABCABD" is:
(a) [0, 0, 0, 1, 2, 0] (b) [0, 0, 0, 1, 2, 3] (c) [0, 1, 2, 3, 4, 5] (d) [0, 0, 1, 1, 2, 0]

**Answer: (a).** ABCA → "A" (1), ABCAB → "AB" (2), ABCABD → 0.

---

**Q12.** Which algorithm compares the pattern from right to left and can skip large parts of the text?
(a) KMP (b) Rabin-Karp (c) Boyer-Moore (d) Naive

**Answer: (c).**

---

**Practice questions:** [2.21 String Matching](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.21_String_Matching.md)
