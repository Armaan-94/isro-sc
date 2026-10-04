# 09. Disk Scheduling Algorithms

> **Mental image.** A hard disk is like a record player with a single arm. The arm must physically swing to the right track before it can read. Swinging is slow. Disk scheduling is about choosing the order of requests so the arm swings as little as possible.

---

## 1. How a hard disk works (just enough)

```
        spindle
          |
   ==============   <- platter (surface 0 on top, surface 1 below)
   ==============
   ==============
          |
   arm with read/write heads moves in and out together
```

- **Platters**: spinning disks coated with magnetic material. Each has two **surfaces**.
- **Tracks**: concentric circles on a surface.
- **Sectors**: each track is divided into sectors (the smallest unit read/written, e.g. 512 bytes or 4 KB).
- **Cylinder**: the set of tracks at the same arm position across all surfaces. Moving between cylinders requires moving the arm.
- All heads move **together** on one arm assembly.

### Disk capacity

```
Capacity = surfaces × tracks per surface × sectors per track × bytes per sector
```

Example: 8 surfaces, 1024 tracks per surface, 64 sectors per track, 512 bytes per sector.
= 8 × 1024 × 64 × 512 = 2³ × 2¹⁰ × 2⁶ × 2⁹ = 2²⁸ bytes = **256 MB**.

### Disk access time = seek + rotational latency + transfer

1. **Seek time:** move the arm to the right cylinder. **The biggest component**, several milliseconds.
2. **Rotational latency:** wait for the right sector to spin under the head. **Average = half a rotation.**
3. **Transfer time:** actually read the data.

**Rotational latency example:** 7200 RPM.
- One rotation = 60 / 7200 s = 8.33 ms.
- Average rotational latency = 8.33 / 2 = **4.17 ms**.

**Transfer time example:** a track holds 64 sectors, disk rotates in 8.33 ms. Reading one sector = 8.33 / 64 ≈ 0.13 ms.

**Total example:** average seek 5 ms, 7200 RPM, read one 4 KB sector at 100 MB/s:
= 5 + 4.17 + (4 KB / 100 MB/s ≈ 0.04 ms) ≈ **9.21 ms**.

Since **seek time dominates**, disk scheduling algorithms try to minimise **total head movement** (measured in cylinders).

> SSDs have no moving parts, so seek time is essentially zero; they usually just use FCFS (the "NOOP" scheduler in Linux).

---

## 2. The standard example

We use this throughout:

- Cylinders **0 to 199**.
- Head currently at **53**, moving toward **higher** numbers.
- Request queue (in arrival order): **98, 183, 37, 122, 14, 124, 65, 67**.

Sorted, for reference: 14, 37, 65, 67, 98, 122, 124, 183.

---

## 3. FCFS (First Come, First Served)

**Rule:** serve requests in arrival order.

53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67

| Move | Distance |
|---|---|
| 53 → 98 | 45 |
| 98 → 183 | 85 |
| 183 → 37 | 146 |
| 37 → 122 | 85 |
| 122 → 14 | 108 |
| 14 → 124 | 110 |
| 124 → 65 | 59 |
| 65 → 67 | 2 |
| **Total** | **640** |

Fair, no starvation, but the arm swings wildly back and forth.

---

## 4. SSTF (Shortest Seek Time First)

**Rule:** from the current position, go to the **nearest** pending request.

From 53: nearest is 65 (12) vs 37 (16). → 65.
From 65: 67 (2). → 67.
From 67: 37 (30) vs 98 (31). → 37.
From 37: 14 (23) vs 98 (61). → 14.
From 14: only higher ones left. → 98 (84), 122 (24), 124 (2), 183 (59).

53 → 65 → 67 → 37 → 14 → 98 → 122 → 124 → 183

Total = 12 + 2 + 30 + 23 + 84 + 24 + 2 + 59 = **236**.

Notes:
- SSTF is the **"SJF of disk scheduling"**: greedy, locally optimal.
- **Starvation:** a request far from a busy region may wait forever if new nearby requests keep coming.
- **Not globally optimal.** For this set, the true minimum is **208** (53 → 37 → 14 → 65 → 67 → 98 → 122 → 124 → 183 = 16 + 23 + 51 + 2 + 31 + 24 + 2 + 59 = 208).

> **Trap.** "SSTF gives the minimum total head movement" is **false** in general.

---

## 5. SCAN (Elevator algorithm)

**Rule:** the arm moves in one direction to the **physical end of the disk**, serving all requests on the way, then reverses and serves requests on the way back. Like an elevator going all the way to the top floor, then coming down.

Moving up from 53: 65, 67, 98, 122, 124, 183, **199** (end), then reverse: 37, 14.

Total = (199 − 53) + (199 − 14) = 146 + 185 = **331**.

Notes:
- No starvation.
- A request that arrives just behind the head (just missed) must wait for the arm to go to the end and come all the way back: up to a full round trip.
- Middle cylinders are served more often than the edges (the arm passes the middle twice per cycle).

---

## 6. C-SCAN (Circular SCAN)

**Rule:** move in one direction to the **physical end**, serving requests. Then **jump straight back to the other end (cylinder 0) without serving anything**, and continue in the same direction.

53 → 65 → 67 → 98 → 122 → 124 → 183 → **199** → jump to **0** → 14 → 37

Total = (199 − 53) + (199 − 0) + (37 − 0) = 146 + 199 + 37 = **382**.

> **Convention alert.** Some textbooks **don't count** the return jump (199 → 0) as head movement, because it's a fast return with no service. Then the total would be 146 + 37 = 183. Always check what the question expects. ISRO/GATE usually **count** the jump unless told otherwise.

Why C-SCAN? It gives **more uniform waiting time**. Every cylinder is visited once per cycle, from the same direction.

---

## 7. LOOK

**Rule:** like SCAN, but the arm only goes as far as the **last request** in each direction, then reverses. It "looks" ahead to see if there's anything further.

Up from 53: 65, 67, 98, 122, 124, **183** (last request up), reverse: 37, 14.

Total = (183 − 53) + (183 − 14) = 130 + 169 = **299**.

---

## 8. C-LOOK

**Rule:** like C-SCAN, but go only as far as the last request, then **jump to the lowest pending request** (not to cylinder 0), and continue upward.

53 → ... → 183 → jump to **14** → 37

Total = (183 − 53) + (183 − 14) + (37 − 14) = 130 + 169 + 23 = **322**.

(Again, if the jump isn't counted: 130 + 23 = 153.)

---

## 9. Summary for this example

| Algorithm | Total head movement |
|---|---|
| FCFS | 640 |
| SSTF | 236 |
| SCAN | 331 |
| C-SCAN | 382 |
| LOOK | 299 |
| C-LOOK | 322 |
| (true optimum) | 208 |

| Algorithm | Starvation? | Key feature |
|---|---|---|
| FCFS | No | Fair, inefficient |
| SSTF | **Yes** | Greedy nearest; not optimal |
| SCAN | No | Goes to physical ends |
| C-SCAN | No | One-direction service; uniform wait |
| LOOK | No | Reverses at last request |
| C-LOOK | No | Jumps to lowest request |

### Choosing an algorithm

- SSTF is common and a natural improvement over FCFS.
- SCAN and C-SCAN perform better under **heavy load** (no starvation).
- LOOK and C-LOOK avoid useless travel to the ends.
- If there's **only one request** in the queue, **all algorithms behave the same**.

---

## 10. A method that never fails

1. Sort the requests.
2. Mark the head position and direction.
3. Write the service order according to the rule.
4. Add up the distances. For sweep algorithms, use the shortcut: **(far point − start) + (far point − other extreme)**, plus the extra leg for circular variants.
5. Check whether the question counts the circular jump.

---

## 11. Practice questions

Use this second data set for Q1 to Q6: cylinders 0 to 199, head at **50**, moving toward **higher** cylinders, requests **82, 170, 43, 140, 24, 16, 190**.

**Q1.** Total head movement under FCFS?
(a) 600 (b) 642 (c) 680 (d) 520

**Answer: (b).** 32 + 88 + 127 + 97 + 116 + 8 + 174 = 642.

---

**Q2.** Under SSTF?
(a) 208 (b) 236 (c) 314 (d) 332

**Answer: (a).** 50 → 43 (7) → 24 (19) → 16 (8) → 82 (66) → 140 (58) → 170 (30) → 190 (20). Total 208.

---

**Q3.** Under SCAN?
(a) 314 (b) 332 (c) 391 (d) 341

**Answer: (b).** Up to 199: 149. Then down to 16: 183. Total 332.

---

**Q4.** Under LOOK?
(a) 314 (b) 332 (c) 341 (d) 208

**Answer: (a).** Up to 190: 140. Then down to 16: 174. Total 314.

---

**Q5.** Under C-SCAN (counting the return jump)?
(a) 332 (b) 391 (c) 341 (d) 382

**Answer: (b).** 50 → 199: 149. 199 → 0: 199. 0 → 43: 43. Total 391.

---

**Q6.** Under C-LOOK (counting the jump)?
(a) 341 (b) 314 (c) 391 (d) 300

**Answer: (a).** 50 → 190: 140. 190 → 16: 174. 16 → 43: 27. Total 341.

---

**Q7.** Which algorithm can cause starvation?
(a) FCFS (b) SSTF (c) SCAN (d) C-LOOK

**Answer: (b).**

---

**Q8.** A disk rotates at 6000 RPM. Average rotational latency?
(a) 10 ms (b) 5 ms (c) 2.5 ms (d) 1 ms

**Answer: (b).** One rotation = 60/6000 s = 10 ms. Half = 5 ms.

---

**Q9.** A disk has 16 surfaces, 512 tracks per surface, 128 sectors per track, 1 KB per sector. Capacity?
(a) 512 MB (b) 1 GB (c) 256 MB (d) 2 GB

**Answer: (b).** 2⁴ × 2⁹ × 2⁷ × 2¹⁰ = 2³⁰ bytes = 1 GB.

---

**Q10.** Which algorithm is often called the "elevator algorithm"?
(a) SSTF (b) SCAN (c) FCFS (d) C-LOOK

**Answer: (b).**

---

**Q11.** Which algorithm provides the most uniform waiting time across cylinders?
(a) SCAN (b) SSTF (c) C-SCAN (d) FCFS

**Answer: (c).**

---

**Q12.** Which component usually dominates disk access time on an HDD?
(a) Transfer time (b) Seek time (c) Controller overhead (d) Bus time

**Answer: (b).**

---

**Q13.** Head at 100, requests 120 and 80 only, moving up. Total movement under LOOK?
(a) 40 (b) 60 (c) 20 (d) 80

**Answer: (b).** 100 → 120 (20), then reverse to 80 (40). Total 60.

---

**Q14.** Disk with 7200 RPM, average seek 4 ms, 500 sectors per track. Average time to read one random sector?
(a) about 8.2 ms (b) about 4.2 ms (c) about 12.3 ms (d) about 8.3 ms

**Answer: (a).** Rotation = 8.33 ms, latency = 4.17 ms, transfer = 8.33/500 ≈ 0.017 ms. Total ≈ 4 + 4.17 + 0.02 ≈ 8.19 ms.
