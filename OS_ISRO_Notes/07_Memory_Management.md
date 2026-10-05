# 07. Memory Management: Contiguous Allocation, Paging and Segmentation

> **The problem in one sentence.** Many processes want to live in RAM at the same time; the OS must decide **where** each one lives, **protect** them from each other, and **translate** the addresses programs use into real RAM addresses.

---

## 1. The memory hierarchy and why it works

| Level | Speed | Size | Cost per byte |
|---|---|---|---|
| Registers | Fastest | Bytes | Highest |
| Cache (L1, L2, L3) | Very fast | KB to MB | High |
| Main memory (RAM) | Fast | GB | Medium |
| Secondary storage (SSD/HDD) | Slow | TB | Low |

Going down: slower, bigger, cheaper. The trick is to keep **what you're using right now** in the fast levels.

### Locality of reference: the reason caching and paging work at all

Programs don't access memory randomly.

- **Temporal locality:** if you used something recently, you'll probably use it again soon. (Loop counters, a function called repeatedly.) This is the basis of **LRU** replacement.
- **Spatial locality:** if you used address X, you'll probably use X+1, X+2 soon. (Walking through an array, executing instructions in sequence.) This is why we load whole **blocks/pages** at once.

---

## 2. Logical vs physical addresses

- **Logical (virtual) address:** the address the CPU generates, the one your program "thinks" it's using. Every process sees addresses starting from 0.
- **Physical address:** the real location in RAM.

The hardware that converts logical to physical at runtime is the **MMU (Memory Management Unit)**.

Why bother? Because if programs used physical addresses directly, every program would need to know exactly where in RAM it was loaded, and it could read or overwrite anyone's memory.

### Address binding (when is the final address decided?)

| Time | How | Consequence |
|---|---|---|
| **Compile time** | Compiler generates absolute addresses | Must reload/recompile if start location changes |
| **Load time** | Loader adds the start address when loading | Can't move once loaded |
| **Execution (run) time** | MMU translates every access | Process can be moved during execution. **Used by modern OSes.** |

---

## 3. Contiguous allocation

The simplest scheme: each process occupies **one single continuous block** of RAM.

### 3.1 Protection with relocation and limit registers

The MMU has two registers per running process:

- **Relocation (base) register:** the physical start address of the process.
- **Limit register:** the size of the process's logical address space.

For every memory access:

```
if (logical_address >= limit)  -> TRAP: addressing error
else physical_address = logical_address + relocation
```

Example: relocation = 14000, limit = 3000. Logical address 346 → physical 14346. Logical address 3500 → trap (3500 ≥ 3000).

Loading these two registers is a privileged instruction (only the OS can do it during a context switch).

### 3.2 Fixed vs variable partitioning

**Fixed (static) partitioning:** RAM is divided into partitions of preset sizes at boot. Each partition holds one process.
- Simple.
- **Internal fragmentation:** a 3 MB process in a 4 MB partition wastes 1 MB **inside** the partition.
- The number of partitions limits the degree of multiprogramming; a process bigger than the largest partition can't run.

**Variable (dynamic) partitioning:** each process gets **exactly** as much as it needs, carved from free memory (a "hole").
- No internal fragmentation.
- Over time, as processes come and go, free memory breaks into many small scattered holes: **external fragmentation**.

### 3.3 Internal vs external fragmentation (a favourite exam contrast)

| | Internal | External |
|---|---|---|
| Where is the waste? | **Inside** an allocated block | **Between** allocated blocks |
| Cause | Block allocated is bigger than the request | Free memory is split into small, non-adjacent holes |
| Occurs in | **Fixed-size** schemes: fixed partitions, **paging** | **Variable-size** schemes: variable partitions, **segmentation** |
| Fix | Smaller blocks | **Compaction**, or non-contiguous allocation (paging) |

**Compaction** = shuffle processes so all free memory becomes one big hole. Only possible with execution-time binding, and it's expensive (copying lots of memory).

### 3.4 Placement strategies: which hole to use?

Given a request, pick a free hole:

| Strategy | Rule | Notes |
|---|---|---|
| **First Fit** | First hole big enough | **Fastest**; generally good |
| **Best Fit** | **Smallest** hole big enough | Must search whole list; leaves tiny useless slivers |
| **Worst Fit** | **Largest** hole | Leaves big leftover holes (useful in variable partitioning) |
| **Next Fit** | Like first fit, but start searching from where the last search ended | Spreads allocations around |

Simulations show first fit and best fit beat worst fit in time and storage use; first fit is usually faster.

### 3.5 Worked example (variable partitioning: leftover becomes a new hole)

Holes (in order): **100, 500, 200, 300, 600** KB. Requests in order: **212, 417, 112, 426** KB.

**First Fit:**
- 212 → first hole ≥ 212 is 500. Left 288. Holes: 100, 288, 200, 300, 600.
- 417 → first ≥ 417 is 600. Left 183. Holes: 100, 288, 200, 300, 183.
- 112 → first ≥ 112 is 288. Left 176. Holes: 100, 176, 200, 300, 183.
- 426 → none ≥ 426. **Must wait.**

**Best Fit:**
- 212 → smallest ≥ 212 is 300. Left 88.
- 417 → smallest ≥ 417 is 500. Left 83.
- 112 → smallest ≥ 112 is 200. Left 88.
- 426 → 600. Left 174. **All four satisfied.**

**Worst Fit:**
- 212 → largest is 600. Left 388.
- 417 → largest is 500. Left 83.
- 112 → largest is 388. Left 276.
- 426 → largest is 300. **Must wait.**

Here best fit wins. In **fixed** partitioning (partitions are not split), the same question gives a different answer, so read whether partitions are fixed or variable.

### 3.6 The 50-percent rule

Statistical analysis of first fit shows that with N allocated blocks, about **0.5N** blocks are lost to fragmentation. So **one-third** of memory may be unusable.

---

## 4. Paging: the cure for external fragmentation

### 4.1 The idea

Why insist on one contiguous block? Chop everything into equal-sized pieces:

- Physical memory is divided into fixed-size **frames**.
- A process's logical memory is divided into **pages** of the **same size**.
- Any page can go into **any** free frame. They don't need to be adjacent.

A **page table** (one per process) records which frame each page is in.

```
 Logical memory          Page table          Physical memory
 page 0  ------------->  0 | 5   ----------> frame 5
 page 1  ------------->  1 | 2   ----------> frame 2
 page 2  ------------->  2 | 7   ----------> frame 7
 page 3  ------------->  3 | 0   ----------> frame 0
```

Results:
- **No external fragmentation**: any free frame fits any page.
- **Internal fragmentation** still exists, but only in the **last page** of a process. On average, half a page per process.

### 4.2 Address translation

A logical address is split into two parts:

```
| page number p | offset d |
```

- If the page size is **2ⁿ bytes**, the **offset** is the low **n bits**.
- If the logical address space is **2ᵐ bytes**, the page number is the remaining **m − n** high bits.
- Number of pages = 2^(m − n).

Translation:

```
f = page_table[p]
physical address = f × page_size + d          (i.e. | f | d | )
```

**The offset never changes.** Only the page number is replaced by a frame number.

### 4.3 Worked example

Page size = 1 KB = 1024 bytes. Logical address = 5000.

- p = floor(5000 / 1024) = 4 (page 4 covers 4096 to 5119).
- d = 5000 − 4 × 1024 = 5000 − 4096 = 904.
- Suppose page_table[4] = 7.
- Physical = 7 × 1024 + 904 = 7168 + 904 = **8072**.

### 4.4 Sizing questions (do these until they're automatic)

**Q.** 32-bit logical address, page size 4 KB, page table entry (PTE) 4 bytes. Size of page table?
- Offset bits = log₂(4096) = 12.
- Page number bits = 32 − 12 = 20 → 2²⁰ pages = 1 M entries.
- Table size = 2²⁰ × 4 B = **4 MB** per process.

**Q.** Physical memory 64 MB, page size 4 KB. How many bits in a frame number?
- Frames = 64 MB / 4 KB = 2²⁶ / 2¹² = 2¹⁴. So **14 bits**.

**Q.** Logical address space 2²⁰ bytes, page size 2¹² bytes. Bits for page number? 20 − 12 = **8**.

**General recipe:**

```
offset bits           = log2(page size)
page number bits      = (logical address bits) − (offset bits)
frame number bits     = (physical address bits) − (offset bits)
#entries in page table = 2^(page number bits)
page table size        = #entries × PTE size
PTE size              >= frame bits + extra bits (valid, dirty, protection...)
```

### 4.5 Where does the page table live?

In **main memory**. A **Page Table Base Register (PTBR)** points to it. A context switch just changes the PTBR.

Problem: every memory reference now needs **two** memory accesses: one to read the page table, one for the actual data. **Memory access is twice as slow.**

---

## 5. TLB: making paging fast

The **Translation Lookaside Buffer (TLB)** is a small, very fast **associative** cache (searched in parallel) that stores recently used (page → frame) pairs. Typically 64 to 1024 entries.

```
CPU generates (p, d)
   |
   +--> look up p in TLB
          hit:  get f immediately           -> 1 memory access for data
          miss: read page table in memory   -> 1 extra memory access
                put (p, f) into TLB
```

### 5.1 Effective Access Time (EAT)

Let h = TLB hit ratio, t = TLB lookup time, m = memory access time.

```
EAT = h × (t + m) + (1 − h) × (t + 2m)
```

(Some questions treat the TLB lookup as overlapping/negligible; then drop t. Read carefully.)

**Example:** t = 20 ns, m = 100 ns, h = 0.8.
EAT = 0.8 × 120 + 0.2 × 220 = 96 + 44 = **140 ns**.
Without a TLB it would be 200 ns. With 98% hits: 0.98 × 120 + 0.02 × 220 = 117.6 + 4.4 = **122 ns**.

### 5.2 TLB and context switches

Each process has its own page table, so TLB entries of one process are wrong for another. Options:
- **Flush** the TLB on every context switch (simple, costly).
- Tag each entry with an **ASID (address-space identifier)**, so entries of many processes can coexist.

---

## 6. Multilevel (hierarchical) paging

### 6.1 Why

From 4.4: a 32-bit address space with 4 KB pages needs a **4 MB** page table **per process**, and it has to be **contiguous**. That's absurd, especially since most of the address space is unused.

**Fix: page the page table.** Break the page table itself into pages, and keep an **outer page table** that points to them. Only the pieces actually needed are allocated.

### 6.2 Two-level example (classic 32-bit)

4 KB pages, 4-byte PTEs. One page can hold 4096 / 4 = 1024 = 2¹⁰ entries.

```
| p1 (10 bits) | p2 (10 bits) | d (12 bits) |
```

- p1 indexes the **outer** page table → gives the frame holding the inner table.
- p2 indexes that **inner** page table → gives the data's frame.
- d is the offset.

### 6.3 Cost

Each level costs one memory access. With k levels and no TLB: **k + 1** memory accesses per reference. That's why the TLB is crucial.

EAT with TLB and k-level paging:
```
EAT = h × (t + m) + (1 − h) × (t + (k + 1) × m)
```

### 6.4 How many levels are needed?

Rule: **each page table (at every level) must fit in one page**.

Entries per page = page size / PTE size. Bits per level = log₂(that).

Levels = ceil(page-number bits / bits per level).

**Example:** 46-bit virtual address, 8 KB pages, 4-byte PTE.
- Offset = 13 bits. Page number bits = 46 − 13 = 33.
- Entries per page = 8192 / 4 = 2048 = 2¹¹, so 11 bits per level.
- Levels = ceil(33 / 11) = **3**.

### 6.5 Page size trade-off

| Bigger pages | Smaller pages |
|---|---|
| Smaller page table | Bigger page table |
| **More internal fragmentation** | Less internal fragmentation |
| Fewer page faults (more per load), better disk I/O efficiency | Better locality matching |

Optimal page size (minimising table overhead + fragmentation), with process size s and PTE size e: **p = √(2se)**.

### 6.6 Inverted page table

Instead of one table per process with an entry per **page**, keep **one table for the whole system** with an entry per **physical frame**. Each entry says: "this frame holds page p of process pid".

- Size depends on **physical memory**, not on virtual address space size. Huge savings on 64-bit systems.
- Lookup is slow: you must search the table for (pid, p). Fixed by **hashing** plus the TLB.
- Sharing pages between processes is awkward (one frame, one owner entry).

### 6.7 Hashed page tables

Hash the virtual page number into a table of linked lists. Common for address spaces larger than 32 bits.

---

## 7. Segmentation: the programmer's view

### 7.1 The idea

Programmers don't think of a program as "pages 0 to 37". They think: **main code, a few functions, global data, the stack, the heap, a symbol table**. These are **logical units** of different sizes.

**Segmentation** divides a process into such variable-sized **segments**. A logical address is:

```
< segment number s, offset d >
```

### 7.2 Segment table

Each entry has:
- **Base**: the physical start address of the segment.
- **Limit**: the length of the segment.

```
if (d >= limit[s])  -> TRAP (addressing error)
else physical = base[s] + d
```

### 7.3 Worked example

| Segment | Limit | Base |
|---|---|---|
| 0 | 500 | 1200 |
| 1 | 275 | 550 |
| 2 | 212 | 880 |
| 3 | 420 | 1400 |
| 4 | 118 | 200 |

- (0, 450): 450 < 500 → 1200 + 450 = **1650**.
- (1, 300): 300 ≥ 275 → **trap**.
- (2, 210): 210 < 212 → 880 + 210 = **1090**.
- (2, 212): 212 is **not** < 212 → **trap**. (Valid offsets are 0 to limit − 1.)
- (3, 450): ≥ 420 → **trap**.
- (4, 80): → 200 + 80 = **280**.

### 7.4 Paging vs segmentation

| | Paging | Segmentation |
|---|---|---|
| Unit size | **Fixed** | **Variable** |
| View | **Physical / system** view; invisible to programmer | **Logical / user** view |
| Address | (page, offset), computed by splitting bits | (segment, offset), two separate values |
| Fragmentation | **Internal** | **External** |
| Table entry | Frame number | Base and limit |
| Protection & sharing | Per page (awkward for logical units) | Per segment (natural: "code is read-only") |

---

## 8. Segmentation with paging (hybrid)

Get the best of both: divide the program into **segments** (logical view, natural protection), then divide each segment into **pages** (no external fragmentation).

Address: `< s, p, d >`. The segment table entry points to that segment's page table.

Used historically by Intel x86 (segmentation unit followed by paging unit). Modern 64-bit x86 essentially uses flat segments plus paging.

---

## 9. Swapping (briefly)

A process can be temporarily moved **out of memory to a backing store** (disk) and later brought back. This lets the total memory of all processes exceed RAM. The **medium-term scheduler** decides. Swap time is mostly transfer time, proportional to the amount swapped.

Example: a 100 MB process, disk transfer 50 MB/s → 2 s to swap out, 2 s to swap in, 4 s total. Swapping whole processes is slow, which is why modern systems swap **pages** ([Chapter 08](08_Virtual_Memory_and_Page_Replacement.md)).

---

## 10. Exam traps

1. **Internal fragmentation → fixed-size** (paging, fixed partitions). **External → variable-size** (segmentation, variable partitions).
2. Paging eliminates **external**, not internal fragmentation.
3. The **offset** is identical in logical and physical addresses.
4. Page table lives in **main memory**; without a TLB, 2 accesses per reference.
5. EAT formula: hit = t + m, miss = t + 2m (single-level).
6. Segment offset must be **strictly less** than the limit.
7. Inverted page table size ∝ **number of frames**.
8. k-level paging (no TLB): **k + 1** memory accesses.
9. Each level of a multilevel page table must fit in **one page**.
10. Best fit is not always best; first fit is usually fastest.

---

## 11. Practice questions

**Q1.** Logical address 32 bits, page size 8 KB. Number of pages in the logical address space?
(a) 2¹³ (b) 2¹⁹ (c) 2²⁰ (d) 2³²

**Answer: (b).** Offset = 13 bits, page number = 32 − 13 = 19 bits, so 2¹⁹ pages.

---

**Q2.** Physical memory 1 GB, page size 4 KB, PTE holds only a frame number plus 4 extra bits (valid, dirty, etc.). Minimum PTE size in bits?
(a) 18 (b) 22 (c) 24 (d) 30

**Answer: (b).** Frames = 2³⁰ / 2¹² = 2¹⁸, so 18 bits for the frame + 4 = 22 bits.

---

**Q3.** TLB lookup 10 ns, memory access 90 ns, hit ratio 90%. EAT (single-level paging)?
(a) 100 ns (b) 109 ns (c) 119 ns (d) 190 ns

**Answer: (b).** 0.9 × 100 + 0.1 × 190 = 90 + 19 = 109 ns.

---

**Q4.** Memory access 100 ns, TLB lookup negligible. What hit ratio gives EAT = 120 ns?
(a) 70% (b) 80% (c) 90% (d) 95%

**Answer: (b).** EAT = h(100) + (1 − h)(200) = 200 − 100h = 120 → h = 0.8.

---

**Q5.** Page size 2 KB. Logical address 7000. Page number and offset?
(a) 3, 856 (b) 3, 1000 (c) 4, 856 (d) 2, 2904

**Answer: (a).** 7000 / 2048 = 3.41, so p = 3. d = 7000 − 6144 = 856.

---

**Q6.** Which scheme suffers from external fragmentation?
(a) Paging (b) Segmentation (c) Fixed partitioning (d) Inverted page table

**Answer: (b).**

---

**Q7.** Segment table entry for segment 2 is (base 4300, limit 600). Physical address for logical address (2, 53)?
(a) 4353 (b) 4300 (c) 653 (d) Trap

**Answer: (a).** 53 < 600, so 4300 + 53.

---

**Q8.** A system uses 3-level paging. Memory access = 100 ns, TLB hit ratio = 90%, TLB lookup negligible. EAT?
(a) 130 ns (b) 140 ns (c) 400 ns (d) 120 ns

**Answer: (a).** Hit: 100. Miss: 3 table accesses + 1 data = 400. EAT = 0.9 × 100 + 0.1 × 400 = 90 + 40 = 130 ns.

---

**Q9.** Virtual address 48 bits, page size 4 KB, PTE 8 bytes. Each page table must fit in one page. Number of levels?
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (c).** Offset 12 bits → page number 36 bits. Entries per page = 4096 / 8 = 512 = 2⁹ → 9 bits per level. 36 / 9 = 4 levels. (This is exactly x86-64's 4-level paging.)

---

**Q10.** Memory holes of sizes 150, 350, 200, 500 KB (in order, variable partitioning). Requests 300 and 180 arrive. With Best Fit, which holes are used?
(a) 350 and 200 (b) 500 and 350 (c) 350 and 500 (d) 500 and 200

**Answer: (a).** 300 → smallest ≥ 300 is 350 (left 50). 180 → smallest ≥ 180 is 200.

---

**Q11.** Same holes, First Fit, requests 300 and 180?
(a) 350 and 200 (b) 350 and 500 (c) 500 and 350 (d) 350 and 150

**Answer: (a).** 300 → first ≥ 300 is 350 (left 50). Holes: 150, 50, 200, 500. 180 → first ≥ 180 is 200.

---

**Q12.** Same holes, Worst Fit, requests 300 and 180?
(a) 500 and 350 (b) 500 and 200 (c) 500 and 500 (d) 350 and 200

**Answer: (a).** 300 → largest is 500 (left 200). Holes: 150, 350, 200, 200. 180 → largest is 350.

---

**Q13.** An inverted page table has one entry for each:
(a) logical page of every process (b) physical frame (c) process (d) segment

**Answer: (b).**

---

**Q14.** Relocation register = 1000, limit register = 500. Logical address 520 results in:
(a) physical 1520 (b) physical 520 (c) trap (d) physical 1500

**Answer: (c).** 520 ≥ 500.

---

**Q15.** Page size 4 KB, a process of size 72,766 bytes. Internal fragmentation?
(a) 0 (b) 962 bytes (c) 1086 bytes (d) 4096 bytes

**Answer: (b).** 72,766 / 4096 = 17.76, so the process needs 18 pages = 18 × 4096 = 73,728 bytes. Waste in the last page = 73,728 − 72,766 = **962 bytes**.

---

**Q16.** Which statement about paging is FALSE?
(a) Pages and frames have the same size (b) The page table maps pages to frames (c) Paging eliminates internal fragmentation (d) The offset is copied unchanged into the physical address

**Answer: (c).** It eliminates **external** fragmentation; internal remains in the last page.

---

**Practice questions:** [5.07 Memory Management Paging Segmentation](../ISRO_CS_Question_Bank/05_Operating_Systems/5.07_Memory_Management_Paging_Segmentation.md)
