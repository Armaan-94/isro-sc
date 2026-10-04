# Full-Length Mock Test 1

> **80 questions · 120 minutes · +3 for a correct answer, −1 for a wrong one, 0 if you leave it blank.**
> Questions 1–65 are Computer Science (Part A); 66–80 are Aptitude (Part B). Every question is new, so none of them repeats the topic files. Don't look at the key until you've finished the whole paper.

---

## Part A: Computer Science

**1.** Minimum number of 2-input NAND gates to implement f = AB + CD:
(a) 2 (b) 3 (c) 4 (d) 5

**2.** (0.375)₁₀ in binary:
(a) 0.101 (b) 0.011 (c) 0.110 (d) 0.0011

**3.** −37 in 8-bit 2's complement:
(a) 10100101 (b) 11011010 (c) 11011011 (d) 00100101

**4.** Minimum flip-flops for a mod-12 counter:
(a) 3 (b) 4 (c) 6 (d) 12

**5.** 32-bit addresses, 16 KB direct-mapped cache, 32-byte blocks. Tag bits:
(a) 16 (b) 17 (c) 18 (d) 19

**6.** An ideal 6-stage pipeline runs 200 instructions. Total cycles:
(a) 200 (b) 205 (c) 206 (d) 1200

**7.** Hit time 2 ns, miss rate 4%, miss penalty 50 ns. AMAT:
(a) 2 ns (b) 3 ns (c) 4 ns (d) 52 ns

**8.** A 24-bit address bus addresses 16-bit words. Total memory:
(a) 16 MB (b) 24 MB (c) 32 MB (d) 64 MB

**9.** Memory accesses needed to fetch the operand (after the instruction is fetched) in memory-indirect addressing:
(a) 0 (b) 1 (c) 2 (d) 3

**10.** Output?
```c
int i;
for (i = 0; i < 5; i += 2);
printf("%d", i);
```
(a) 4 (b) 5 (c) 6 (d) 0 2 4

**11.** `char s[] = "GATE";` sizeof(s) =
(a) 4 (b) 5 (c) 8 (d) 1

**12.** `int a[] = {2, 4, 6, 8}; int *p = a + 1;` p[1] + *(p − 1) =
(a) 6 (b) 8 (c) 10 (d) 12

**13.** `int f() { static int c = 0; return ++c; }` is called four times and the results are added. Sum:
(a) 4 (b) 6 (c) 10 (d) 16

**14.** `#define CUBE(x) x*x*x`. CUBE(1 + 1) =
(a) 8 (b) 4 (c) 6 (d) 3

**15.** Postfix of (A + B) * C − D / E:
(a) AB+C*DE/− (b) ABC*+DE/− (c) AB+CD*E/− (d) AB+C*D−E/

**16.** Evaluate postfix 4 5 + 2 3 * −:
(a) 3 (b) 15 (c) −3 (d) 9

**17.** Maximum nodes at level 5 of a binary tree (root at level 0):
(a) 16 (b) 31 (c) 32 (d) 63

**18.** Inorder D B E A F C G, preorder A B D E C F G. Postorder:
(a) D E B F G C A (b) D B E F C G A (c) E D B G F C A (d) D E F G B C A

**19.** Table size 7, h(k) = k mod 7, linear probing. Insert 10, 17, 24, 3 in order. Slot of 3:
(a) 3 (b) 4 (c) 5 (d) 6

**20.** Edges in K₆:
(a) 12 (b) 15 (c) 30 (d) 36

**21.** T(n) = 9T(n/3) + n² solves to:
(a) Θ(n²) (b) Θ(n² log n) (c) Θ(n³) (d) Θ(n log n)

**22.** Quicksort with first-element pivot on an already sorted array:
(a) Θ(n) (b) Θ(n log n) (c) Θ(n²) (d) Θ(log n)

**23.** Dijkstra's algorithm with a binary heap runs in:
(a) O(V²) (b) O((V + E) log V) (c) O(VE) (d) O(E)

**24.** Huffman coding with frequencies 5, 10, 15, 30, 40. Total encoded bits:
(a) 200 (b) 205 (c) 210 (d) 300

**25.** LCS length of "ABAB" and "BABA":
(a) 2 (b) 3 (c) 4 (d) 1

**26.** Greedy by value/weight ratio is optimal for:
(a) 0/1 knapsack (b) fractional knapsack (c) travelling salesman (d) longest path

**27.** Minimal DFA states for binary strings with an even number of 0s and an odd number of 1s:
(a) 2 (b) 3 (c) 4 (d) 6

**28.** Not regular:
(a) {aⁿbⁿ : n ≤ 10} (b) {aⁿbᵐ : n ≠ m} (c) {aⁿ : n even} (d) a*b*

**29.** Context-free languages are closed under:
(a) intersection (b) complement (c) union (d) difference

**30.** The halting problem is:
(a) recursive (b) RE but not recursive (c) not RE (d) regular

**31.** Binary strings containing "11" as a substring:
(a) (0 + 1)*11(0 + 1)* (b) 1*1 (c) (01)*11 (d) 11(0 + 1)

**32.** Tokens in `if (x >= 10) y = x * 2;`:
(a) 9 (b) 10 (c) 11 (d) 12

**33.** S → AB, A → a | ε, B → b | ε. FIRST(S):
(a) {a} (b) {a, b} (c) {a, b, ε} (d) {ε}

**34.** Compared with SLR(1), LALR(1) has:
(a) more states (b) the same number of states and more power (c) fewer states (d) less power

**35.** Replacing x = y + 0 with x = y is:
(a) constant folding (b) algebraic simplification (c) loop unrolling (d) dead-code elimination

**36.** P1 (AT 0, BT 6), P2 (1, 3), P3 (2, 1), non-preemptive SJF. Average waiting time:
(a) 2.67 (b) 3.33 (c) 4 (d) 1.67

**37.** Same processes under SRTF. Average waiting time:
(a) 1.33 (b) 1.67 (c) 2 (d) 3.33

**38.** Reference string 0, 1, 2, 0, 3, 0, 4, 2, 3 with 3 frames under LRU. Page faults:
(a) 6 (b) 7 (c) 8 (d) 9

**39.** Semaphore S = 5, then 8 P and 3 V operations. Final S:
(a) 0 (b) 5 (c) −3 (d) 10

**40.** 32-bit logical address, 8 KB pages, 4-byte page table entries. Single-level page table size:
(a) 1 MB (b) 2 MB (c) 4 MB (d) 512 KB

**41.** Head at 100; requests 55, 58, 39, 18, 90, 160, 150, 38, 184. SSTF total head movement:
(a) 208 (b) 248 (c) 322 (d) 498

**42.** 4 processes each need at most 3 units of a resource. Minimum units that guarantee no deadlock:
(a) 8 (b) 9 (c) 12 (d) 10

**43.** `for (i = 0; i < 4; i++) fork();` How many processes in total?
(a) 4 (b) 8 (c) 15 (d) 16

**44.** R(A, B, C, D, E), F = {A→B, B→C, C→D, D→E}. Highest normal form:
(a) 1NF (b) 2NF (c) 3NF (d) BCNF

**45.** R(A, B, C) with candidate keys A and B. Number of super keys:
(a) 4 (b) 5 (c) 6 (d) 7

**46.** Schedule R1(X), W2(X), W1(X):
(a) conflict serializable as T1, T2 (b) conflict serializable as T2, T1 (c) not conflict serializable (d) serial
**47.** R has 4 rows, S has 5 rows. Rows returned by `SELECT * FROM R, S` (no WHERE):
(a) 9 (b) 20 (c) 5 (d) 4

**48.** B+ tree internal node: block 1024 B, key 12 B, child pointer 8 B. Maximum children:
(a) 50 (b) 51 (c) 52 (d) 85

**49.** Two-phase locking guarantees:
(a) deadlock freedom (b) conflict serializability (c) cascadelessness (d) recoverability

**50.** R(A, B) has 10 tuples. Possible cardinality of σ_{A=5}(R):
(a) exactly 1 (b) 0 to 10 (c) 10 (d) 1 to 10

**51.** R(A, B, C, D), F = {A→B, A→C, C→D}, decomposed into R1(A, B, C) and R2(C, D). The decomposition is:
(a) lossy (b) lossless but not dependency-preserving (c) lossless and dependency-preserving (d) dependency-preserving only

**52.** A Class C network split with mask /26 gives:
(a) 2 subnets, 126 hosts (b) 4 subnets, 62 hosts (c) 8 subnets, 30 hosts (d) 4 subnets, 64 hosts

**53.** 172.16.45.200/20. Network address:
(a) 172.16.0.0 (b) 172.16.32.0 (c) 172.16.45.0 (d) 172.16.40.0

**54.** Stop-and-wait with Tt = 2 ms and Tp = 9 ms. Efficiency:
(a) 10% (b) 18% (c) 20% (d) 50%

**55.** Go-Back-N with a 4-bit sequence number. Maximum sender window:
(a) 8 (b) 15 (c) 16 (d) 7

**56.** Data 1101, generator 1001. Transmitted codeword:
(a) 1101100 (b) 1101001 (c) 1101110 (d) 1101010

**57.** Slow start begins at cwnd = 1 MSS. cwnd after 3 RTTs with no loss:
(a) 3 (b) 4 (c) 8 (d) 16

**58.** Maximum throughput of pure ALOHA:
(a) 18.4% (b) 36.8% (c) 50% (d) 100%

**59.** DNS queries normally use:
(a) TCP 53 (b) UDP 53 (c) UDP 67 (d) TCP 80

**60.** Hamming parity bits needed for 16 data bits (single-error correction):
(a) 4 (b) 5 (c) 6 (d) 16

**61.** Control-flow graph with 12 edges and 10 nodes (one component). Cyclomatic complexity:
(a) 2 (b) 3 (c) 4 (d) 22

**62.** In Basic COCOMO, the organic-mode effort exponent is:
(a) 1.05 (b) 1.12 (c) 1.20 (d) 0.38

**63.** Top-down integration testing uses:
(a) drivers (b) stubs (c) both always (d) neither

**64.** Best kind of cohesion:
(a) logical (b) temporal (c) functional (d) coincidental

**65.** RSA with p = 7, q = 11, e = 7. Private key d:
(a) 13 (b) 37 (c) 43 (d) 49

## Part B: Aptitude

**66.** A 150 m train at 54 km/h crosses a 250 m bridge in:
(a) 16.67 s (b) 26.67 s (c) 20 s (d) 30 s

**67.** Simple interest on ₹5000 at 8% a year for 3 years:
(a) ₹1000 (b) ₹1200 (c) ₹1260 (d) ₹1500

**68.** Compound interest on ₹10,000 at 10% a year for 2 years:
(a) ₹2000 (b) ₹2100 (c) ₹2200 (d) ₹1100

**69.** A finishes a job in 12 days and B in 18 days. Working together they take:
(a) 6 days (b) 7.2 days (c) 7.5 days (d) 15 days

**70.** Two numbers are in the ratio 3 : 5 and add up to 64. The larger is:
(a) 24 (b) 32 (c) 40 (d) 48

**71.** Two dice are thrown. P(sum = 8):
(a) 1/6 (b) 5/36 (c) 1/9 (d) 7/36

**72.** Number of arrangements of the letters of LEVEL:
(a) 120 (b) 60 (c) 30 (d) 20

**73.** Average of the first 10 prime numbers:
(a) 12.9 (b) 12.5 (c) 13 (d) 11.9

**74.** Angle between the hands of a clock at 4:20:
(a) 0° (b) 10° (c) 20° (d) 30°

**75.** 2, 6, 12, 20, 30, ?
(a) 40 (b) 42 (c) 44 (d) 36

**76.** CAT = 24 when each letter is replaced by its position in the alphabet and the values added. DOG = ?
(a) 24 (b) 25 (c) 26 (d) 27

**77.** 1 January 2025 was a Wednesday. 1 January 2026 is a:
(a) Wednesday (b) Thursday (c) Friday (d) Tuesday

**78.** All A are B. Some B are C. Does "Some A are C" follow?
(a) yes (b) no (c) only if all B are C (d) it is definitely false

**79.** Pointing to a man, Rita says, "His mother is the only daughter of my mother." The man is Rita's:
(a) brother (b) son (c) nephew (d) father

**80.** Sales rose from 120 units to 150 units. Percentage increase:
(a) 20% (b) 25% (c) 30% (d) 15%

---

## Answer Key with Explanations

| Q | Ans | Why |
|---|---|---|
| 1 | b | NAND-NAND form: two NANDs for AB and CD, and one to combine them. |
| 2 | b | 0.375 = 1/4 + 1/8. |
| 3 | c | 37 = 00100101; invert to 11011010, add 1. |
| 4 | b | 2³ = 8 < 12 ≤ 16. |
| 5 | c | 512 lines → 9 index bits, 5 offset bits; 32 − 14 = 18. |
| 6 | b | k + n − 1 = 6 + 199. |
| 7 | c | 2 + 0.04 × 50. |
| 8 | c | 2²⁴ words × 2 B = 32 MB. |
| 9 | c | One access to read the pointer, one to read the operand. |
| 10 | c | The stray `;` ends the loop body; i goes 0, 2, 4, 6. |
| 11 | b | Four characters plus '\0'. |
| 12 | b | p[1] = a[2] = 6; *(p − 1) = a[0] = 2. |
| 13 | c | 1 + 2 + 3 + 4; a static variable keeps its value. |
| 14 | b | Expands to 1 + 1*1 + 1*1 + 1. |
| 15 | a | Compute (A + B) * C, then D / E, then subtract. |
| 16 | a | 9 − 6. |
| 17 | c | 2⁵. |
| 18 | a | Root A; left subtree B(D, E), right subtree C(F, G). |
| 19 | d | 10→3, 17→4, 24→5, 3→6. |
| 20 | b | 6 × 5/2. |
| 21 | b | log₃ 9 = 2 = exponent of f: master theorem case 2. |
| 22 | c | Each partition splits into sizes 0 and n − 1. |
| 23 | b | Each extract-min and decrease-key costs O(log V). |
| 24 | b | Code lengths 4, 4, 3, 2, 1: 20 + 40 + 45 + 60 + 40. |
| 25 | b | "ABA" or "BAB". |
| 26 | b | 0/1 knapsack needs DP. |
| 27 | c | Track the parity of 0s and the parity of 1s: 2 × 2 states. |
| 28 | b | Its complement within a*b* is aⁿbⁿ, which is not regular. (a) is finite. |
| 29 | c | CFLs are not closed under intersection, complement or difference. |
| 30 | b | Simulating the machine confirms "halts" but can never confirm "doesn't halt". |
| 31 | a | Anything, then 11, then anything. |
| 32 | d | if ( x >= 10 ) y = x * 2 ; (count them: 12). |
| 33 | c | Both A and B can derive ε. |
| 34 | b | LALR merges CLR states with the same core. |
| 35 | b | Uses the identity y + 0 = y. |
| 36 | b | P1 runs 0–6, P3 6–7, P2 7–10. WT: 0, 6, 4. |
| 37 | b | P1 0–1, P2 1–2, P3 2–3, P2 3–5, P1 5–10. WT: 4, 1, 0. |
| 38 | b | Faults on 0, 1, 2, 3, 4, 2, 3. |
| 39 | a | 5 − 8 + 3. |
| 40 | b | 2¹⁹ entries × 4 B. |
| 41 | b | 100 → 90 → 58 → 55 → 39 → 38 → 18 → 150 → 160 → 184. |
| 42 | b | n(k − 1) + 1 = 4 × 2 + 1. |
| 43 | d | 2⁴. |
| 44 | b | Key A (single attribute, so 2NF holds); B→C is transitive. |
| 45 | c | Supersets of A (4) + supersets of B (4) − supersets of AB (2). |
| 46 | c | R1(X) before W2(X) gives T1 → T2; W2(X) before W1(X) gives T2 → T1. |
| 47 | b | Cartesian product. |
| 48 | b | 8p + 12(p − 1) ≤ 1024 → p ≤ 51.8. |
| 49 | b | Not deadlock freedom; strict 2PL adds cascadelessness. |
| 50 | b | Anywhere from no matches to all of them. |
| 51 | c | C is a key of R2; every FD lies inside one table. |
| 52 | b | 2 borrowed bits; 2⁶ − 2 hosts. |
| 53 | b | Third octet in blocks of 16: 45 lies in 32–47. |
| 54 | a | a = 4.5; 1/(1 + 9). |
| 55 | b | 2⁴ − 1. |
| 56 | a | 1101000 mod 1001 leaves 100. |
| 57 | c | 1 → 2 → 4 → 8. |
| 58 | a | 1/(2e). |
| 59 | b | Zone transfers use TCP 53. |
| 60 | b | 2⁵ = 32 ≥ 16 + 5 + 1. |
| 61 | c | 12 − 10 + 2. |
| 62 | a | Semi-detached 1.12, embedded 1.20. |
| 63 | b | Stubs stand in for lower modules not yet integrated. |
| 64 | c | Every element serves one task. |
| 65 | c | φ = 60; 7 × 43 = 301 ≡ 1 (mod 60). |
| 66 | b | 400 m at 15 m/s. |
| 67 | b | 5000 × 8 × 3/100. |
| 68 | b | 10,000 × (1.21 − 1). |
| 69 | b | 1/12 + 1/18 = 5/36 per day. |
| 70 | c | 64 × 5/8. |
| 71 | b | (2,6), (3,5), (4,4), (5,3), (6,2). |
| 72 | c | 5!/(2! 2!). |
| 73 | a | Sum 129. |
| 74 | b | Hour hand at 4 × 30 + 20 × 0.5 = 130°, minute hand at 120°. |
| 75 | b | n(n + 1): differences 4, 6, 8, 10, 12. |
| 76 | c | 4 + 15 + 7. |
| 77 | b | 2025 isn't a leap year: one odd day. |
| 78 | b | The C's in B might all lie outside A. |
| 79 | b | Her mother's only daughter is Rita herself. |
| 80 | b | 30/120. |

**Scoring:** in the real exam 3 marks per question, minus 1 per wrong answer. A wrong answer costs a third of a correct one, so guess only when you can eliminate at least two options.
