# 05. Data Link Layer: Error Detection and Correction (Parity, Hamming, Checksum, CRC)

> **The core trick of all error control.** Add a little **redundant** information computed from the data. The receiver recomputes it. If the two don't match, something went wrong in transit. Clever choices of redundancy let you not only **detect** errors but also **locate and fix** them.

---

## 1. Types of errors

- **Single-bit error:** exactly one bit flipped (0 → 1 or 1 → 0). Rare in serial transmission.
- **Burst error:** two or more bits changed. The **length of the burst** is measured from the **first** corrupted bit to the **last** corrupted bit (bits in between may or may not be corrupted). Common, because noise usually lasts longer than one bit time.

Example: at 1 Mbps, a 1 ms noise spike can corrupt up to 1000 bits.

### Detection vs correction

- **Detection:** "Is there an error?" Yes/no. Cheaper.
- **Correction:** "Which bits are wrong?" Needs more redundancy.

Two ways to handle a detected error:
- **Retransmission (ARQ):** ask the sender to send again (Chapter 04).
- **Forward Error Correction (FEC):** fix it at the receiver using redundancy, no retransmission. Used where retransmission is impossible or too slow (live video, deep-space links, satellite telemetry).

---

## 2. Block coding and Hamming distance

### 2.1 Block codes

Split data into **datawords** of **k** bits. Add **r** redundant bits to make **codewords** of **n = k + r** bits. Written **C(n, k)**.

There are 2ⁿ possible n-bit words but only 2ᵏ are **valid** codewords. If the receiver gets an **invalid** word, it knows an error occurred.

### 2.2 Hamming distance

The **Hamming distance d(x, y)** between two equal-length words = number of positions where they differ = **number of 1s in x XOR y**.

Example: d(10110, 11100): 10110 ⊕ 11100 = 01010 → **2**.

**d_min** = the smallest Hamming distance between any two **valid** codewords. It decides the code's power.

### 2.3 The three rules (memorise)

```
Detect up to s errors:                 d_min ≥ s + 1
Correct up to t errors:                d_min ≥ 2t + 1
Detect s and correct t (s ≥ t):        d_min ≥ s + t + 1
```

Why? Picture each valid codeword as a point, with a "sphere" of words within distance t around it.
- For **detection** of s errors: s flips must never land exactly on another valid codeword → need distance ≥ s + 1.
- For **correction** of t errors: the spheres of radius t around codewords must not overlap, so the received word is closest to exactly one codeword → need distance ≥ 2t + 1.

| d_min | Detects up to | Corrects up to |
|---|---|---|
| 2 | 1 | 0 |
| 3 | 2 | 1 |
| 4 | 3 | 1 |
| 5 | 4 | 2 |
| 7 | 6 | 3 |

> **Trap.** d_min = 3 lets you **either** detect 2 errors **or** correct 1 error, **not** detect 2 and correct 2.

### 2.4 Linear codes

In a **linear block code**, the XOR of any two valid codewords is another valid codeword. For linear codes, **d_min = the minimum number of 1s in any non-zero valid codeword** (minimum weight). Easier to compute.

---

## 3. Parity check codes

### 3.1 Simple (single) parity

Add **one bit** so that the total number of 1s is **even** (even parity) or **odd** (odd parity).

Example (even parity): data 1011011 has five 1s → parity bit 1 → codeword 10110111.

- C(k + 1, k), **d_min = 2**.
- Detects **any odd number** of errors.
- **Cannot detect an even number** of errors (two flips cancel out).
- Cannot correct anything.

### 3.2 Two-dimensional parity

Arrange data in a table; add a parity bit for **each row** and **each column** (and a corner bit).

- Detects **all 1, 2 and 3-bit errors** anywhere.
- Can **correct** a **single-bit** error (the failing row and column intersect at the bad bit).
- **Some 4-bit errors** go undetected (four errors forming a rectangle cancel in every row and column).

---

## 4. Hamming code (single-error correction)

### 4.1 How many redundant bits?

With m data bits and r parity bits, the r check bits must be able to point to any of the m + r positions **or** say "no error":

```
2^r ≥ m + r + 1
```

| Data bits m | Parity bits r | Total n |
|---|---|---|
| 4 | 3 | 7 |
| 7 (ASCII) | 4 | 11 |
| 11 | 4 | 15 |
| 26 | 5 | 31 |

A "perfect" Hamming code has **n = 2^r − 1** and **k = 2^r − r − 1**: C(7, 4), C(15, 11), C(31, 26). **d_min = 3** → corrects 1, detects 2.

### 4.2 Where do parity bits go?

At positions that are **powers of 2**: **1, 2, 4, 8, ...** (1-indexed). Data bits fill the rest.

For C(7, 4): positions 1, 2, 4 are parity (P1, P2, P4); positions 3, 5, 6, 7 are data.

### 4.3 Which positions does each parity bit check?

Write each position in binary. **P1 checks every position whose bit 0 is 1; P2, bit 1; P4, bit 2.**

| Position | Binary | Checked by |
|---|---|---|
| 1 | 001 | P1 |
| 2 | 010 | P2 |
| 3 | 011 | P1, P2 |
| 4 | 100 | P4 |
| 5 | 101 | P1, P4 |
| 6 | 110 | P2, P4 |
| 7 | 111 | P1, P2, P4 |

- **P1** checks 1, 3, 5, 7.
- **P2** checks 2, 3, 6, 7.
- **P4** checks 4, 5, 6, 7.

### 4.4 Encoding example (even parity)

Data **1011** placed at positions 3, 5, 6, 7: d3 = 1, d5 = 0, d6 = 1, d7 = 1.

- P1 (1, 3, 5, 7): d3 + d5 + d7 = 1 + 0 + 1 = 2 → even already → **P1 = 0**.
- P2 (2, 3, 6, 7): 1 + 1 + 1 = 3 → **P2 = 1**.
- P4 (4, 5, 6, 7): 0 + 1 + 1 = 2 → **P4 = 0**.

Codeword (positions 1 to 7): **0 1 1 0 0 1 1**.

### 4.5 Decoding: the syndrome points at the error

Suppose position 6 flips: received **0 1 1 0 0 0 1**.

Recompute each check over its positions (including the parity bit):
- C1 (1, 3, 5, 7): 0 ⊕ 1 ⊕ 0 ⊕ 1 = **0**.
- C2 (2, 3, 6, 7): 1 ⊕ 1 ⊕ 0 ⊕ 1 = **1**.
- C4 (4, 5, 6, 7): 0 ⊕ 0 ⊕ 0 ⊕ 1 = **1**.

Syndrome (C4 C2 C1) = **110** = **6**. Flip bit 6 back. Corrected! A syndrome of 000 means no error.

That's the beauty: the failing checks literally **spell the binary address** of the bad bit.

**Extended Hamming (SECDED):** add one overall parity bit → d_min = 4 → corrects 1 **and** detects 2. Used in ECC memory.

---

## 5. Checksum

Used by **IP, TCP and UDP** (software-friendly).

### Sender

1. Split data into words (16-bit in the Internet).
2. Add them using **one's complement arithmetic** (any carry out of the top bit is **wrapped around** and added to the bottom: "end-around carry").
3. The **checksum** = the **one's complement** (flip all bits) of the sum. Send it with the data.

### Receiver

Add all words **including** the checksum (one's complement). If the result is all 1s, complementing gives **0** → accept. Otherwise → error.

### Example (8-bit words)

Words: 10110011 and 01101010.
- Sum: 10110011 + 01101010 = 1 00011101 (9 bits). Wrap the carry: 00011101 + 1 = **00011110**.
- Checksum = complement = **11100001**.

Receiver: 10110011 + 01101010 + 11100001 → 00011110 + 11100001 = **11111111** → complement = 0 → OK.

### Example (4-bit, decimal)

Data 7, 11, 12, 0, 6. Sum = 36. In 4 bits: 36 = 10 0100 → wrap: 0100 + 10 = 0110 = **6**. Checksum = complement of 6 = **9**. Receiver: 7 + 11 + 12 + 0 + 6 + 9 = 45 = 10 1101 → 1101 + 10 = 1111 → complement 0 ✓.

### Weakness

If one word increases by some amount and another decreases by the same amount, the sum doesn't change: the error is **missed**. Swapping the order of words is also invisible. CRC is much stronger.

---

## 6. Cyclic Redundancy Check (CRC)

Used in **Ethernet, Wi-Fi, HDLC**, disks. Implemented cheaply in **hardware** with shift registers.

### 6.1 Bits as polynomials

A bit string is a polynomial with coefficients 0/1: **1011 = x³ + x + 1**. Arithmetic is **modulo 2**: addition and subtraction are both **XOR**, no carries.

### 6.2 Procedure

Sender and receiver agree on a **generator** G(x) of degree r (r + 1 bits).

**Sender:**
1. Append **r zeros** to the dataword (multiply by x^r).
2. Divide by the generator using **modulo-2 division** (XOR at each step).
3. The **r-bit remainder** is the CRC. Replace the appended zeros with it.

**Receiver:** divide the received codeword by the same generator. **Remainder 0 → no error detected.** Non-zero remainder (the **syndrome**) → error.

### 6.3 Worked example 1

Dataword **1001**, generator **1011** (x³ + x + 1, so r = 3).

Augmented: **1001 000**.

```
        1010          (quotient, not needed)
      ________
1011 ) 1001000
       1011           1001 XOR 1011
       ----
        0100          bring down 0 -> leading bit 0, XOR 0000
        0000
        ----
         1000         bring down 0
         1011         XOR
         ----
          0110        bring down 0 -> leading bit 0, XOR 0000
          0000
          ----
           110        remainder
```

CRC = **110**. Codeword = **1001110**. (Check: dividing 1001110 by 1011 gives remainder 000.)

### 6.4 Worked example 2

Dataword **1101**, generator **1011**. Augmented **1101000**.
- 1101 ⊕ 1011 = 0110 → bring down 0 → 1100 ⊕ 1011 = 0111 → bring down 0 → 1110 ⊕ 1011 = 0101 → bring down 0 → 1010 ⊕ 1011 = 0001.
- Remainder **001**. Codeword **1101001**.

### 6.5 What CRC detects

With a well-chosen generator of degree r:
- **All single-bit errors** (if G has at least two terms).
- **All double-bit errors** (for suitable G).
- **All odd numbers of errors** if G has the factor (x + 1).
- **All burst errors of length ≤ r.**
- Bursts of length r + 1 with probability 1 − (1/2)^(r−1); longer bursts with probability 1 − (1/2)^r.

Standard generators: CRC-8, CRC-16, **CRC-32** (Ethernet: x³² + x²⁶ + ... + 1).

### 6.6 Cyclic code property

A cyclic shift (rotation) of any valid codeword is also a valid codeword.

---

## 7. Comparison

| Method | Redundancy | Detects | Corrects | Used in |
|---|---|---|---|---|
| Simple parity | 1 bit | Odd number of errors | No | Memory, serial ports |
| 2-D parity | Row + column bits | Up to 3 errors | 1 error | Older systems |
| Hamming | r bits (2^r ≥ m + r + 1) | 2 errors | **1 error** | ECC memory |
| Checksum | 16 bits | Many, but misses compensating errors | No | **IP, TCP, UDP** |
| CRC | r bits | **All bursts ≤ r**, most others | No | **Ethernet, Wi-Fi, disks** |

---

## 8. Exam traps

1. Burst length = first bad bit to last bad bit.
2. Detect s: d_min ≥ s + 1. Correct t: d_min ≥ 2t + 1.
3. Simple parity misses even numbers of errors; d_min = 2.
4. Hamming parity bits at **powers of 2** (1-indexed). 2^r ≥ m + r + 1.
5. Syndrome = position of the error.
6. Checksum uses **one's complement** with wraparound carry.
7. CRC uses **modulo-2 (XOR)** division; append r zeros where r = degree of G = (bits in G) − 1.
8. CRC detects all bursts of length ≤ r.
9. Checksum at transport/IP; CRC at data link.

---

## 9. Practice questions

**Q1.** Hamming distance between 1101101 and 1001110?
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (b).** XOR = 0100011 → three 1s.

---

**Q2.** A code has d_min = 4. It can correct up to:
(a) 1 error (b) 2 errors (c) 3 errors (d) 4 errors

**Answer: (a).** 4 ≥ 2t + 1 → t ≤ 1.5 → 1.

---

**Q3.** Same code (d_min = 4) can detect up to:
(a) 2 (b) 3 (c) 4 (d) 1

**Answer: (b).**

---

**Q4.** How many parity bits are needed for single-error correction of 16 data bits?
(a) 4 (b) 5 (c) 6 (d) 8

**Answer: (b).** 2^r ≥ 16 + r + 1. r = 4: 16 ≥ 21? No. r = 5: 32 ≥ 22? Yes.

---

**Q5.** In Hamming C(7, 4), the received word is 1 0 1 1 0 1 1 (positions 1 to 7, even parity). Which bit is in error?
(a) none (b) position 1 (c) position 5 (d) position 7

**Answer: (d).** Compute all three checks:
- C1 (1, 3, 5, 7): 1 ⊕ 1 ⊕ 0 ⊕ 1 = 1.
- C2 (2, 3, 6, 7): 0 ⊕ 1 ⊕ 1 ⊕ 1 = 1.
- C4 (4, 5, 6, 7): 1 ⊕ 0 ⊕ 1 ⊕ 1 = 1.

Syndrome (C4 C2 C1) = 111 = **7**. Flip bit 7. (Never guess "no error" from a quick glance; always compute every check.)

---

**Q6.** Dataword 1010, generator 1011. CRC remainder?
(a) 011 (b) 101 (c) 001 (d) 110

**Answer: (a).** Augmented 1010000. 1010 ⊕ 1011 = 0001 → bring down 0: 0010 (leading 0) → bring down 0: 0100 (leading 0) → bring down 0: 1000 ⊕ 1011 = 0011 → remainder **011**.

---

**Q7.** A CRC generator is x⁴ + x + 1. How many CRC bits are appended?
(a) 3 (b) 4 (c) 5 (d) 2

**Answer: (b).** Degree 4.

---

**Q8.** Which error pattern can simple even parity NOT detect?
(a) 1-bit (b) 3-bit (c) 2-bit (d) 5-bit

**Answer: (c).**

---

**Q9.** One's complement sum of 8-bit words 11110000 and 00010001, then checksum?
(a) sum 00000010, checksum 11111101 (b) sum 00000001, checksum 11111110 (c) sum 11111111, checksum 00000000 (d) sum 00000010, checksum 11111110

**Answer: (a).** 11110000 + 00010001 = 1 00000001 → wrap: 00000001 + 1 = 00000010. Complement: 11111101.

---

**Q10.** CRC with a generator of degree 16 detects all burst errors of length up to:
(a) 8 (b) 15 (c) 16 (d) 17

**Answer: (c).**

---

**Q11.** Which technique is used by Ethernet for error detection?
(a) Checksum (b) Hamming code (c) CRC-32 (d) 2-D parity

**Answer: (c).**

---

**Q12.** A burst error corrupts bits 3 and 10 of a frame (bits 4 to 9 are fine). Burst length?
(a) 2 (b) 6 (c) 7 (d) 8

**Answer: (d).** From bit 3 to bit 10 inclusive: 10 − 3 + 1 = 8.
