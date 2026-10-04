# 08. Virtual Memory and Page Replacement

> **The magic trick.** Virtual memory lets a 10 GB program run on a machine with 4 GB of RAM. How? By keeping only the parts you're **currently using** in RAM, and the rest on disk, swapping pieces in when needed. The program never notices.

---

## 1. Why virtual memory?

From Chapter 07, paging lets a process's pages sit in any frames. Now take one more step: **why must all pages be in memory at all?**

Most programs don't use all their code and data at once:
- error-handling code that rarely runs,
- arrays declared with 10,000 elements but only 100 used,
- features you never click.

**Virtual memory** separates the logical memory a program sees from physical memory. Only the needed pages are in RAM.

Benefits:
1. Programs can be **larger than physical memory**.
2. Each process uses less RAM, so **more processes fit** (higher degree of multiprogramming, better CPU use).
3. **Less I/O** to load or swap a program (load only what's needed).
4. Easy **sharing** of pages (shared libraries, `fork()` with copy-on-write).

Cost: complexity, and serious slowdowns if done badly (thrashing).

---

## 2. Demand paging

**Demand paging** = load a page into memory **only when it is actually referenced** ("on demand"). It's like a lazy swapper.

**Pure demand paging**: start a process with **zero** pages in memory. The very first instruction causes a fault, and the process faults its way up to the pages it needs.

### 2.1 The valid-invalid bit

Each page table entry has a **valid-invalid bit**:

- **Valid (v):** the page is legal **and** currently in memory.
- **Invalid (i):** either the page is **not part of the process's address space at all**, or it's legal but **currently on disk**.

### 2.2 What happens on a page fault (memorise the order)

When the CPU references a page marked invalid, the MMU raises a **page fault** trap.

1. **Trap** to the OS.
2. OS checks an internal table: is the reference **illegal** (outside the address space)? If yes, **abort** the process (segmentation fault). If it's just not loaded yet, continue.
3. **Find a free frame.** If none, choose a **victim** page to evict (page replacement).
4. **Schedule a disk read** to bring the page into that frame. (The process waits; the CPU runs someone else.)
5. When the read finishes, **update the page table** (set frame number, mark valid).
6. **Restart the instruction** that caused the fault. The process now proceeds as if the page had always been there.

> **Trap.** A page fault is **not an error**. It's a normal, expected event in demand paging. Only an **illegal** address is an error.

### 2.3 Effective access time with page faults

Let p = page fault rate (0 ≤ p ≤ 1), ma = memory access time.

```
EAT = (1 − p) × ma + p × (page fault service time)
```

**Example:** ma = 100 ns, fault service = 8 ms = 8,000,000 ns, p = 0.001.
EAT = 0.999 × 100 + 0.001 × 8,000,000 = 99.9 + 8000 ≈ **8100 ns**.

The machine is **81 times slower** because of 1 fault per 1000 accesses. Disk is roughly 100,000 times slower than RAM, so even tiny fault rates dominate.

**How low must p be?** With ma = 200 ns and service = 8 ms, for under 10% slowdown we need EAT < 220:
200 + p × (8,000,000 − 200) < 220 → p < 20 / 7,999,800 ≈ **2.5 × 10⁻⁶**, about one fault per 400,000 accesses.

### 2.4 Copy-on-write (COW)

After `fork()`, parent and child **share** the same pages, marked read-only. Only when one of them **writes** to a page is that page copied. Since the child usually calls `exec()` immediately, most pages are never copied. Huge saving.

---

## 3. Page replacement

When a fault happens and **no frame is free**, we must evict a page.

### 3.1 The dirty (modify) bit

Each page has a **dirty bit**, set by hardware whenever the page is written to.

- **Dirty page** chosen as victim: must be **written back to disk** first, then the new page is read in. Two disk transfers.
- **Clean page**: the disk copy is already identical, so just overwrite the frame. One disk transfer.

So the dirty bit can **halve** page fault service time for clean victims.

### 3.2 How we compare algorithms

Use a **reference string** (the sequence of page numbers accessed) and a fixed number of frames, then count **page faults**. Fewer is better.

We'll use the reference string **7, 0, 1, 2, 0, 3, 0, 4, 2, 3, 0, 3, 2** with **3 frames**.

---

## 4. FIFO (First In, First Out)

**Rule:** evict the page that was **loaded earliest**. A simple queue.

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| F1 | 7 | 7 | 7 | 2 | 2 | 2 | 2 | 4 | 4 | 4 | 0 | 0 | 0 |
| F2 | | 0 | 0 | 0 | 0 | 3 | 3 | 3 | 2 | 2 | 2 | 2 | 2 |
| F3 | | | 1 | 1 | 1 | 1 | 0 | 0 | 0 | 3 | 3 | 3 | 3 |
| Fault? | F | F | F | F | H | F | F | F | F | F | F | H | H |

**10 faults.**

### Belady's anomaly

You'd expect more frames → fewer faults. With FIFO, **not always**!

Reference string **1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5**:
- 3 frames: **9 faults**.
- 4 frames: **10 faults**.

Adding a frame made it worse. This is **Belady's anomaly**. It can happen with FIFO (and FIFO-like algorithms such as Second Chance in some cases), but **never** with LRU or OPT.

---

## 5. OPT (Optimal)

**Rule:** evict the page that **will not be used for the longest time in the future** (or never again).

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| F1 | 7 | 7 | 7 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
| F2 | | 0 | 0 | 0 | 0 | 0 | 0 | 4 | 4 | 4 | 0 | 0 | 0 |
| F3 | | | 1 | 1 | 1 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 3 |
| Fault? | F | F | F | F | H | F | H | F | H | H | F | H | H |

Reasoning at each eviction:
- Ref 2: 7 is never used again. Evict 7.
- Ref 3: future is 0, 4, 2, 3, 0, 3, 2. Page 1 never appears. Evict 1.
- Ref 4: future is 2, 3, 0, ... Among {2, 0, 3}, 0 is needed last. Evict 0.
- Ref 0: future is 3, 2. Page 4 never used again. Evict 4.

**7 faults.** That's the **minimum possible** for 3 frames.

OPT is **impossible to implement** (needs the future), but it's the **benchmark** against which other algorithms are measured.

---

## 6. LRU (Least Recently Used)

**Rule:** evict the page that has **not been used for the longest time in the past**. Use the recent past as a predictor of the near future (temporal locality).

| Ref | 7 | 0 | 1 | 2 | 0 | 3 | 0 | 4 | 2 | 3 | 0 | 3 | 2 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| F1 | 7 | 7 | 7 | 2 | 2 | 2 | 2 | 4 | 4 | 4 | 0 | 0 | 0 |
| F2 | | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 3 | 3 | 3 | 3 |
| F3 | | | 1 | 1 | 1 | 3 | 3 | 3 | 2 | 2 | 2 | 2 | 2 |
| Fault? | F | F | F | F | H | F | H | F | F | F | F | H | H |

Reasoning at each eviction:
- Ref 2: last uses: 7 (t1), 0 (t2), 1 (t3). Evict 7.
- Ref 3: 2 (t4), 0 (t5), 1 (t3). Evict 1.
- Ref 4: 2 (t4), 0 (t7), 3 (t6). Evict 2.
- Ref 2: 4 (t8), 0 (t7), 3 (t6). Evict 3.
- Ref 3: 4 (t8), 0 (t7), 2 (t9). Evict 0.
- Ref 0: 4 (t8), 2 (t9), 3 (t10). Evict 4.

**9 faults.**

Result for this string: **OPT (7) ≤ LRU (9) ≤ FIFO (10)**. That ordering is typical, though LRU isn't always better than FIFO on every string.

### 6.1 Implementing LRU (hard in hardware)

1. **Counters:** stamp each page table entry with a clock value on every reference; evict the smallest. Needs a search and a write on every access.
2. **Stack:** keep a stack of page numbers; on reference, move that page to the top. The bottom is always the LRU page. Implemented as a doubly linked list.

Both are too expensive to do on every memory access without special hardware, so real systems use **approximations**.

### 6.2 Stack algorithms (why LRU and OPT never show Belady's anomaly)

An algorithm is a **stack algorithm** if the set of pages in memory with n frames is always a **subset** of the set with n + 1 frames. For LRU, the n frames hold the n most recently used pages; with n + 1 frames, you hold those plus one more. So adding a frame can never cause a fault that didn't happen before. **LRU and OPT are stack algorithms**; FIFO is not.

---

## 7. LRU approximations

### 7.1 Reference bit

Hardware sets a page's **reference bit** to 1 whenever it's accessed. The OS periodically clears them. Pages with bit 0 haven't been used recently.

### 7.2 Second Chance (Clock) algorithm

FIFO, but with mercy:
- Look at the oldest page. If its reference bit is **0**, evict it.
- If it's **1**, give it a **second chance**: clear the bit, move it to the back (or advance the clock hand), and check the next page.

Arranged as a circular list with a "clock hand". If all bits are 1, it degenerates to FIFO.

### 7.3 Enhanced Second Chance (using reference + dirty bits)

Classify pages by (reference, modify):

| Class | (R, M) | Meaning | Evict? |
|---|---|---|---|
| 1 | (0, 0) | Not recently used, clean | **Best** victim |
| 2 | (0, 1) | Not recently used, dirty | Must write back |
| 3 | (1, 0) | Recently used, clean | Likely needed soon |
| 4 | (1, 1) | Recently used, dirty | **Worst** victim |

Evict from the lowest non-empty class.

---

## 8. Counting-based algorithms

- **LFU (Least Frequently Used):** evict the page with the smallest reference count. Problem: a page heavily used early but now unused keeps a high count and stays forever. Fix: decay (shift counts right periodically).
- **MFU (Most Frequently Used):** evict the page with the **highest** count, arguing that a low-count page was just brought in and is about to be used. Counter-intuitive and rare.

Neither is common; both approximate OPT poorly and are expensive.

---

## 9. Frame allocation

How many frames does each process get?

- **Minimum:** set by the architecture. An instruction may touch several pages (instruction itself spanning two pages, plus operands); a process needs enough frames for any single instruction to complete.
- **Equal allocation:** m frames, n processes → m/n each.
- **Proportional allocation:** process i of size sᵢ gets

```
aᵢ = (sᵢ / S) × m          where S = Σ sᵢ
```

Example: 62 frames, processes of 10 pages and 127 pages. S = 137.
a₁ = 10/137 × 62 ≈ 4. a₂ = 127/137 × 62 ≈ 57.

- **Priority allocation:** proportional to priority instead of size (or a mix).

### Local vs global replacement

| | Local | Global |
|---|---|---|
| Victim chosen from | Only the faulting process's own frames | **Any** frame in the system |
| Frames per process | Fixed | Can grow and shrink |
| Isolation | A process's fault rate depends only on itself | One process's behaviour can hurt another |
| Throughput | Lower | **Generally higher**; more common in practice |

---

## 10. Thrashing

### 10.1 What it is

A process (or the whole system) is **thrashing** when it spends **more time paging than executing**.

### 10.2 How it happens (the vicious cycle)

1. Too many processes are in memory; each has fewer frames than it actively needs.
2. Processes fault constantly; each fault evicts a page another process (or itself) needs right away.
3. Everyone is waiting on the disk. **CPU utilization drops.**
4. The OS sees low CPU utilization and thinks "the CPU is underused, let's **admit more processes**".
5. Even fewer frames per process → even more faults → CPU utilization drops further.

```
CPU utilization
   ^
   |         ____
   |       /      \
   |     /          \     <- thrashing begins
   |   /              \
   | /                  \___
   +----------------------------> degree of multiprogramming
```

> **Exam trap (very common).** The fix for thrashing is to **decrease** the degree of multiprogramming (suspend or swap out processes), **not** increase it.

### 10.3 Working-set model

The **locality model**: a process moves from one **locality** (a set of pages actively used together, like a function and its data) to another.

The **working set** WS(t, Δ) is the set of distinct pages referenced in the most recent **Δ** references (the working-set window).

- Δ too small: doesn't cover the whole current locality.
- Δ too large: overlaps several localities, overestimates.
- Δ = ∞: the entire program.

Let D = Σ WSSᵢ (total demand of all processes). If **D > m** (total frames), thrashing will occur, so **suspend a process**. If D is well below m, you can admit another.

**Example:** reference string … 2 6 1 5 7 7 7 7 5 1 | (Δ = 10) → WS = {1, 2, 5, 6, 7}, size 5.

### 10.4 Page-fault frequency (PFF)

A more direct approach: set an upper and lower bound on the acceptable page fault rate.
- Rate too **high** → give the process **more frames**.
- Rate too **low** → **take frames away**.
- If no free frames are available for a high-rate process → suspend some process.

---

## 11. Program structure affects page faults

Classic question. `int A[128][128];` stored **row-major**. Page size = 128 integers (one row per page). The process has **1 frame** for the array.

```c
// Version 1: column by column
for (j = 0; j < 128; j++)
    for (i = 0; i < 128; i++)
        A[i][j] = 0;
```
Each access A[i][j] with increasing i touches a different row = different page. 128 × 128 = **16,384 faults**.

```c
// Version 2: row by row
for (i = 0; i < 128; i++)
    for (j = 0; j < 128; j++)
        A[i][j] = 0;
```
Finishes one row (page) before moving on. **128 faults**.

Lesson: traversing arrays in storage order respects spatial locality.

---

## 12. Exam traps

1. A page fault is **normal**, not an error (unless the address is illegal).
2. Page fault steps end with **restarting the instruction**.
3. Even tiny fault rates dominate EAT.
4. **Belady's anomaly: FIFO only** (among FIFO, LRU, OPT).
5. **LRU and OPT are stack algorithms.**
6. OPT = minimum faults, not implementable.
7. Dirty victims cost an extra disk write.
8. Thrashing fix: **reduce** multiprogramming.
9. Working set too large: covers multiple localities.
10. Global replacement: more common, higher throughput.

---

## 13. Practice questions

**Q1.** Reference string 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5 with 3 frames under FIFO. Number of faults?
(a) 8 (b) 9 (c) 10 (d) 12

**Answer: (b).** 1F 2F 3F 4F(out 1) 1F(out 2) 2F(out 3) 5F(out 4) 1H 2H 3F(out 1) 4F(out 2) 5H = 9.

---

**Q2.** Same string with 4 frames under FIFO?
(a) 8 (b) 9 (c) 10 (d) 6

**Answer: (c).** 1F 2F 3F 4F 1H 2H 5F(out 1) 1F(out 2) 2F(out 3) 3F(out 4) 4F(out 5) 5F(out 1) = 10. More frames, more faults: Belady's anomaly.

---

**Q3.** Reference string 1, 2, 3, 4, 2, 1, 5, 6, 2, 1, 2, 3, 7, 6, 3, 2, 1, 2, 3, 6 with 3 frames. Faults under FIFO, LRU, OPT respectively?
(a) 16, 15, 11 (b) 15, 16, 11 (c) 16, 15, 12 (d) 14, 10, 8

**Answer: (a).** (This is a standard textbook string. With 4 frames the answers are 14, 10, 8; that's option (d), a deliberate distractor.)

---

**Q4.** Memory access time 200 ns, page fault service 10 ms, fault rate 0.0005. EAT?
(a) 200 ns (b) 5200 ns (c) 5000 ns (d) 10,000 ns

**Answer: (b).** 0.9995 × 200 + 0.0005 × 10,000,000 ≈ 199.9 + 5000 ≈ 5200 ns.

---

**Q5.** Which algorithm suffers from Belady's anomaly?
(a) LRU (b) OPT (c) FIFO (d) None

**Answer: (c).**

---

**Q6.** CPU utilization is low and the paging disk is 99% busy. The best action is:
(a) Install a faster CPU (b) Increase the degree of multiprogramming (c) Decrease the degree of multiprogramming (d) Increase page size drastically

**Answer: (c).** Classic thrashing symptoms.

---

**Q7.** In the enhanced second-chance algorithm, which (reference, modify) class is the best victim?
(a) (1, 1) (b) (1, 0) (c) (0, 1) (d) (0, 0)

**Answer: (d).**

---

**Q8.** Reference string 1, 2, 3, 1, 4, 5 with 3 frames under LRU. Faults?
(a) 4 (b) 5 (c) 6 (d) 3

**Answer: (b).** 1F 2F 3F 1H 4F (evict 2, the LRU) 5F (evict 3) = 5.

---

**Q9.** A page needs to be written back to disk before replacement only if:
(a) its reference bit is 1 (b) its dirty bit is 1 (c) its valid bit is 0 (d) it was loaded first

**Answer: (b).**

---

**Q10.** Two processes of sizes 40 and 160 pages share 50 frames under proportional allocation. Frames for the larger process?
(a) 25 (b) 40 (c) 10 (d) 45

**Answer: (b).** 160/200 × 50 = 40.

---

**Q11.** In pure demand paging, when a process starts executing it has:
(a) all pages loaded (b) half its pages loaded (c) no pages loaded (d) only the stack loaded

**Answer: (c).**

---

**Q12.** OPT page replacement replaces the page:
(a) used least recently (b) loaded earliest (c) that will not be used for the longest time in future (d) with the smallest count

**Answer: (c).**

---

**Q13.** `int A[256][256]` stored row-major, page size holds 256 integers, 1 frame for the array. A loop accesses the array column by column. Page faults?
(a) 256 (b) 512 (c) 65,536 (d) 1

**Answer: (c).** 256 × 256 = 65,536. (Row by row would be 256.)

---

**Q14.** The working-set window Δ = 4. Reference string: 1, 2, 1, 3, 4, 4, 4, 2. Working set at the end?
(a) {2, 4} (b) {3, 4, 2} (c) {1, 2, 3, 4} (d) {1, 2, 4}

**Answer: (a).** The last 4 references are 4, 4, 4, 2. Distinct pages: {2, 4}. The working set counts **distinct** pages in the window, so repeated 4s don't add anything.

---

**Q15.** Which statement about LRU is TRUE?
(a) It can show Belady's anomaly (b) It requires knowing future references (c) It is a stack algorithm (d) It always performs worse than FIFO

**Answer: (c).**

---

**Q16.** The last step of servicing a page fault is:
(a) find a free frame (b) update the page table (c) restart the faulting instruction (d) trap to the OS

**Answer: (c).**
