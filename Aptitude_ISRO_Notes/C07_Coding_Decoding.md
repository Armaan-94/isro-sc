# C07. Coding-Decoding

> A hidden rule turns a word into a code. Your job is to **compare input and output letter by letter** (never the whole word at once), find the rule, check it on a second letter, then apply it.

---

## 1. Alphabet toolkit

```
A B C D E F G H I J K  L  M  N  O  P  Q  R  S  T  U  V  W  X  Y  Z
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26
```

- **EJOTY** = 5, 10, 15, 20, 25 (anchor positions).
- **Opposite letter:** position + opposite = 27 (A ↔ Z, B ↔ Y, M ↔ N). Mnemonic pairs: AZ, BY, CX, DW, EV, FU, GT, HS, IR, JQ, KP, LO, MN.
- **Wrap-around:** after Z comes A (subtract 26).

## 2. Types

### Letter shift
- **Fixed:** CAT → DBU (+1 each).
- **Variable:** MONEY → NQQID: +1, +2, +3, +4, +5 (Y + 5 = 30 → 4 = D).
> Check at least two letters before assuming a fixed shift.

### Opposite letters
GOLD → TLOW (G ↔ T, O ↔ L, L ↔ O, D ↔ W). So SILVER → **HROEVI**.

### Reversal
PENCIL → LICNEP (letters reversed). ERASER → **RESARE**.

### Letter to number
- Positions: GOOD → 7 15 15 4.
- Sum of positions: RED = 18 + 5 + 4 = 27; GREEN = 7 + 18 + 5 + 5 + 14 = **49**.
- Position minus place in the word: STRONG, O is 15th letter and 4th in the word → **11**.

### Substitution tables
ROSE = 6821 and CHAIR = 73456 → R = 6, O = 8, S = 2, E = 1, C = 7, H = 3, A = 4, I = 5. SEARCH = **214673**.

### Symbols
Pure lookup: build the table from the examples, fill gaps by elimination.

### Sentence ("language") coding
Find a code word common to two sentences and the English word common to their meanings; they match. Remove and repeat.

"sim tam kol" = "roses are red", "sim del pic" = "roses smell nice" → **sim = roses**.

> If two code words remain unmatched with two English words and no third sentence connects them, the answer **cannot be determined**.

---

## 3. Practice questions (with solutions)

**Q1.** CAT → DBU. Rule?
(a) −1 (b) +1 (c) reverse (d) opposite letters
**Answer: (b).**

**Q2.** Same rule: DOG → ?
(a) EPH (b) CNF (c) EOG (d) EPI
**Answer: (a).**

**Q3.** BAT → YZG (opposite letters). CAT → ?
(a) XZG (b) XZH (c) ZYG (d) YZH
**Answer: (a).**

**Q4.** ROSE = 6821, CHAIR = 73456. SEARCH = ?
(a) 214673 (b) 246173 (c) 214763 (d) 146273
**Answer: (a).**

**Q5.** MONEY → NQQID. Pattern?
(a) +1 fixed (b) +2 fixed (c) +1, +2, +3, ... (d) reversal
**Answer: (c).**

**Q6.** @ = A, # = B. BAG = #@%. % = ?
(a) A (b) B (c) G (d) can't say
**Answer: (c).**

**Q7.** "pil mit sog" = "flowers are beautiful"; "rin mit kot" = "roses are fragrant". mit = ?
(a) flowers (b) are (c) beautiful (d) fragrant
**Answer: (b).**

**Q8.** Same data. pil = ?
(a) flowers (b) are (c) beautiful (d) can't be determined
**Answer: (d).**

**Q9.** Each letter of STRONG is coded as (alphabet position − position in word). Code for O?
(a) 11 (b) 15 (c) 19 (d) 9
**Answer: (a).**

**Q10.** If APPLE is coded 50 (sum of positions), CAT is coded:
(a) 23 (b) 24 (c) 25 (d) 26
**Answer: (b).**

**Q11.** PENCIL → LICNEP. ERASER → ?
(a) RESARE (b) RESAER (c) ERASER (d) RASERE
**Answer: (a).**

**Q12.** TIGER → UJHFS. LION → ?
(a) MJPO (b) KHNM (c) MJOP (d) NKQP
**Answer: (a).**

**Q13.** GOLD → TLOW. SILVER → ?
(a) HROEVI (b) HRPEVI (c) GROEVI (d) HQOEVI
**Answer: (a).**

**Q14.** RED = 27. GREEN = ?
(a) 45 (b) 47 (c) 49 (d) 51
**Answer: (c).**

**Q15.** "ka pa ta" = "I love India", "na ka sa" = "you love cricket", "ta ra ve" = "India is great". Code for "I"?
(a) ka (b) pa (c) ta (d) na
**Answer: (b).** ka = love (sentences 1, 2), ta = India (1, 3), leaving pa = I.

**Q16.** In the same code, "love" is:
(a) ka (b) pa (c) ta (d) sa
**Answer: (a).**

**Q17.** If A = 1 and FAT = 27, then FAITH = ?
(a) 41 (b) 44 (c) 42 (d) 40
**Answer: (b).** F6 + A1 + I9 + T20 + H8 = 44. (Check FAT: 6 + 1 + 20 = 27.)

**Q18.** If each letter is replaced by the letter two positions before it, FROG is coded:
(a) DPME (b) HTQI (c) DQME (d) EPME
**Answer: (a).** F → D, R → P, O → M, G → E.

**Q19.** Opposite letter of M?
(a) L (b) N (c) O (d) K
**Answer: (b).**

**Q20.** If BOX = 2 15 24, then CUP = ?
(a) 3 21 16 (b) 3 20 16 (c) 4 21 16 (d) 3 21 15
**Answer: (a).**
