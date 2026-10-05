# 03. CPU Scheduling Algorithms

> **Why this chapter matters.** CPU scheduling numericals (Gantt charts, average waiting time) appear in almost every ISRO and GATE paper. They are free marks *if* you are systematic. This chapter teaches you a method you can apply mechanically under exam pressure.

---

## 1. The setting

You have one CPU and several processes in the ready queue. Who should run next? That choice is **CPU scheduling**, and the rule you use to make it is the **scheduling algorithm**.

### CPU bursts and I/O bursts

A process alternates between computing and waiting:

```
CPU burst -> I/O burst -> CPU burst -> I/O burst -> ... -> CPU burst -> exit
```

Scheduling is only about the **CPU bursts**. In exam problems, each process usually has just one CPU burst, called its **Burst Time (BT)**.

> **Trap.** A scheduling algorithm does **not** change how long a process spends doing I/O. It only changes how long a process **waits in the ready queue**.

---

## 2. Preemptive vs non-preemptive

**Non-preemptive:** once a process gets the CPU, it keeps it until it **finishes** or **voluntarily blocks** (for I/O). Nobody can snatch it away.

**Preemptive:** the OS can **forcibly take** the CPU away, for example when:
- a higher-priority (or shorter) process arrives,
- the time quantum expires.

Analogy: non-preemptive is a single-occupancy bathroom with a lock. Preemptive is a shared desk where the manager can tell you "move, someone more urgent needs it".

Scheduling decisions can happen at four moments:
1. Running -> Waiting (I/O request)
2. Running -> Ready (interrupt)
3. Waiting -> Ready (I/O completes)
4. Running -> Terminated

If scheduling happens **only at 1 and 4**, the scheme is **non-preemptive**. If it can also happen at 2 or 3, it's **preemptive**.

---

## 3. The formulas you must know cold

For each process:

```
Completion Time (CT)  = the moment the process finishes
Turnaround Time (TAT) = CT - AT          (AT = arrival time)
Waiting Time (WT)     = TAT - BT         (BT = burst time)
Response Time (RT)    = (time of FIRST CPU allocation) - AT
```

Intuition:
- **TAT** = total time from arrival to completion (the "door-to-door" time).
- **WT** = how much of that time was spent *not running* (sitting in the ready queue).
- **RT** = how long before you got your *first* bite of the CPU. Matters for interactive users.

For non-preemptive algorithms, **RT = WT** (since once you start, you run to completion). For preemptive algorithms they can differ.

### System-level criteria

| Criterion | Want it | Meaning |
|---|---|---|
| CPU utilization | **Max** | % of time CPU is busy |
| Throughput | **Max** | Processes completed per unit time |
| Turnaround time | **Min** | Arrival to completion |
| Waiting time | **Min** | Time in ready queue |
| Response time | **Min** | Arrival to first response |

---

## 4. The universal method for any scheduling numerical

1. Write a table: Process, AT, BT (and priority if given).
2. Start time at 0 (or at the earliest arrival).
3. At each decision point, list **which processes have arrived and are not finished**. Only those are candidates!
4. Apply the algorithm's rule to pick one.
5. Draw the Gantt chart segment.
6. Repeat until all finish.
7. Fill CT, then TAT = CT - AT, then WT = TAT - BT.
8. Sanity check: sum of BT = total busy time in the Gantt chart.

**If the CPU becomes idle** (no one has arrived yet), mark an idle gap in the Gantt chart and jump to the next arrival.

We'll use the same 4 processes for every algorithm so you can compare.

| Process | AT | BT |
|---|---|---|
| P1 | 0 | 5 |
| P2 | 1 | 3 |
| P3 | 2 | 8 |
| P4 | 3 | 6 |

---

## 5. FCFS (First Come, First Served)

**Rule:** run processes in order of arrival. Non-preemptive. Implemented with a FIFO queue.

Gantt chart:
```
| P1 | P2 |    P3    |   P4   |
0    5    8          16       22
```

| P | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 5 | 5 | 5 | 0 |
| P2 | 1 | 3 | 8 | 7 | 4 |
| P3 | 2 | 8 | 16 | 14 | 6 |
| P4 | 3 | 6 | 22 | 19 | 13 |

Avg TAT = 45/4 = **11.25**, Avg WT = 23/4 = **5.75**.

### The convoy effect

Classic example: three processes arrive at time 0 with BT 24, 3, 3.

- Order P1(24), P2(3), P3(3): WT = 0, 24, 27, avg = **17**.
- Order P2, P3, P1: WT = 0, 3, 6, avg = **3**.

Same processes, same algorithm, wildly different results, just because a long process got in front. Short processes are stuck behind a long one like cars behind a slow truck. That is the **convoy effect**.

FCFS: simple, no starvation, but poor average waiting time and bad for interactive systems.

---

## 6. SJF (Shortest Job First) and SRTF (Shortest Remaining Time First)

### SJF (non-preemptive)

**Rule:** among the processes **that have arrived**, pick the one with the **smallest burst time**. Once started, it runs to completion.

Walkthrough:
- t=0: only P1 has arrived. Run P1 to t=5.
- t=5: P2(3), P3(8), P4(6) have arrived. Shortest is P2. Run 5 to 8.
- t=8: P3(8), P4(6). Shortest is P4. Run 8 to 14.
- t=14: P3. Run 14 to 22.

```
| P1 | P2 |  P4  |    P3    |
0    5    8      14         22
```

| P | CT | TAT | WT |
|---|---|---|---|
| P1 | 5 | 5 | 0 |
| P2 | 8 | 7 | 4 |
| P3 | 22 | 20 | 12 |
| P4 | 14 | 11 | 5 |

Avg TAT = 43/4 = **10.75**, Avg WT = 21/4 = **5.25**.

Common mistake: at t=0, students pick P2 because it's the shortest overall. But P2 hasn't arrived yet! **Only arrived processes are candidates.**

### SRTF (preemptive SJF)

**Rule:** every time a new process arrives, compare its burst with the **remaining** time of the running process. If the newcomer is shorter, **preempt**.

Walkthrough:
- t=0: P1 runs (remaining 5).
- t=1: P2 arrives with 3. P1's remaining is 4. 3 < 4, so **preempt** P1. Run P2.
- t=2: P3 arrives (8). P2 remaining 2. Keep P2.
- t=3: P4 arrives (6). P2 remaining 1. Keep P2.
- t=4: P2 done. Candidates: P1(4), P3(8), P4(6). Run P1.
- t=8: P1 done. Candidates: P3(8), P4(6). Run P4.
- t=14: P4 done. Run P3 until 22.

```
|P1| P2 |  P1  |  P4  |    P3    |
0  1    4      8      14         22
```

| P | CT | TAT | WT |
|---|---|---|---|
| P1 | 8 | 8 | 3 |
| P2 | 4 | 3 | 0 |
| P3 | 22 | 20 | 12 |
| P4 | 14 | 11 | 5 |

Avg TAT = 42/4 = **10.5**, Avg WT = 20/4 = **5.0**. The best so far.

### Why SRTF is "optimal but impossible"

- **SRTF gives the minimum average waiting time** of any algorithm (SJF is optimal among non-preemptive ones). Intuition: moving a short job ahead of a long one decreases the short job's wait by a lot and increases the long one's wait by a little.
- But the OS **cannot know the future burst time**. It can only **predict** it.

### Predicting the next burst: exponential averaging

```
τ(n+1) = α · t(n) + (1 − α) · τ(n)
```

- t(n) = actual length of the n-th burst (what just happened)
- τ(n) = what we predicted for it
- α between 0 and 1: weight on recent history

α = 0: ignore recent behaviour (prediction never changes). α = 1: only the last burst matters.

Example: α = 0.5, initial guess τ1 = 10, actual bursts 6, 4, 6.
- τ2 = 0.5·6 + 0.5·10 = 8
- τ3 = 0.5·4 + 0.5·8 = 6
- τ4 = 0.5·6 + 0.5·6 = 6

### Downside: starvation

If short jobs keep arriving, a long job may **never** run. That's **starvation** (indefinite blocking).

---

## 7. Priority Scheduling

**Rule:** each process has a priority; the highest priority runs first. Can be non-preemptive or preemptive.

> **Always check the convention.** Some questions say "lower number = higher priority", others the opposite. Read carefully.

SJF is actually a special case of priority scheduling where priority = 1/(burst time).

Example (lower number = higher priority):

| P | AT | BT | Priority |
|---|---|---|---|
| P1 | 0 | 4 | 2 |
| P2 | 1 | 3 | 1 |
| P3 | 2 | 1 | 3 |
| P4 | 3 | 5 | 4 |

**Non-preemptive:**
- t=0: only P1. Run 0 to 4.
- t=4: P2(1), P3(3), P4(4). Run P2 4 to 7.
- t=7: P3 runs 7 to 8. Then P4 8 to 13.

CT: 4, 7, 8, 13. TAT: 4, 6, 6, 10 (avg 6.5). WT: 0, 3, 5, 5 (avg **3.25**).

**Preemptive:**
- t=0: P1 runs.
- t=1: P2 (priority 1) arrives, beats P1 (2). Preempt. P2 runs 1 to 4.
- t=4: P1(2), P3(3), P4(4). P1 runs 4 to 7.
- P3 7 to 8, P4 8 to 13.

CT: P1=7, P2=4, P3=8, P4=13. TAT: 7, 3, 6, 10 (avg 6.5). WT: 3, 0, 5, 5 (avg 3.25).

(Here the averages happen to match; in general they don't.)

### Starvation and aging

Low-priority processes may starve. The fix is **aging**: gradually **increase the priority** of a process the longer it waits. For example, +1 priority every 15 minutes. Eventually even the lowest-priority process becomes the highest and runs.

Fun fact often quoted: when MIT shut down its IBM 7094 in 1973, they found a low-priority job submitted in 1967 that had never run.

---

## 8. Round Robin (RR)

**Rule:** the ready queue is a circular FIFO. Each process runs for at most **one time quantum (q)**, then goes to the **back** of the queue if not finished. Preemptive.

Designed for **time-sharing**: it gives every process a fair slice and good **response time**.

### The tie-breaking rule (very important in numericals)

When a process's quantum expires at the same moment a new process arrives, who goes into the queue first? The **standard convention**: the **newly arrived process is added first**, then the preempted process goes behind it. (Always follow the question's convention if stated.)

### Worked example, q = 4

- t=0 to 4: P1 runs (remaining 1). During this, P2, P3, P4 arrive. Queue: P2, P3, P4, then P1.
- t=4 to 7: P2 runs 3 and finishes (CT=7).
- t=7 to 11: P3 runs 4 (remaining 4). Queue: P4, P1, P3.
- t=11 to 15: P4 runs 4 (remaining 2). Queue: P1, P3, P4.
- t=15 to 16: P1 runs 1, finishes (CT=16).
- t=16 to 20: P3 runs 4, finishes (CT=20).
- t=20 to 22: P4 runs 2, finishes (CT=22).

```
| P1 | P2 | P3 | P4 |P1| P3 | P4 |
0    4    7    11   15 16   20   22
```

| P | CT | TAT | WT | RT |
|---|---|---|---|---|
| P1 | 16 | 16 | 11 | 0 |
| P2 | 7 | 6 | 3 | 3 |
| P3 | 20 | 18 | 10 | 5 |
| P4 | 22 | 19 | 13 | 8 |

Avg TAT = 59/4 = **14.75**, Avg WT = 37/4 = **9.25**. That's *worse than FCFS* here! RR isn't trying to minimise waiting time. It's trying to give **everyone a quick first response**.

### Choosing the quantum

| Quantum | Behaviour |
|---|---|
| **Very large** (bigger than every burst) | Each process finishes in one turn, so RR becomes **FCFS** |
| **Very small** | Approaches perfect "processor sharing", but **context-switch overhead dominates** and CPU utilization drops |
| **Rule of thumb** | About 80% of CPU bursts should be shorter than q |

### Maximum wait between turns

With **n** processes in the ready queue and quantum **q**, a process waits at most **(n − 1) × q** before its next turn. If each context switch costs **s**, then it's (n − 1)(q + s) roughly, depending on how the question counts switches.

### CPU utilization with context switching

If every quantum q is followed by a switch costing s (and processes always use full quanta):

```
CPU efficiency = q / (q + s)
```

Example: q = 10 ms, s = 1 ms → 10/11 ≈ 90.9%.

---

## 9. LJF / LRTF and HRRN

### LJF and LRTF

**Longest Job First** (non-preemptive) and **Longest Remaining Time First** (preemptive) are the opposite of SJF/SRTF. They're mainly asked for contrast. They give poor average waiting time and starve short jobs.

LRTF tie-breaking: when remaining times tie, usually choose the process with the **lower arrival time** (or lower process ID). Read the question.

### HRRN (Highest Response Ratio Next)

Non-preemptive. Designed to fix SJF's starvation.

```
Response Ratio = (Waiting time so far + Burst time) / Burst time
               = 1 + W / S
```

- A short job has a small S, so a big ratio. Short jobs are favoured.
- A long job's W keeps growing while it waits, so its ratio keeps growing. **Eventually it wins**. No starvation.

Worked on our example:
- t=0 to 5: P1 (only one).
- t=5: P2: (4+3)/3 = 2.33. P3: (3+8)/8 = 1.375. P4: (2+6)/6 = 1.33. Pick **P2**.
- t=8: P3: (6+8)/8 = 1.75. P4: (5+6)/6 = 1.83. Pick **P4**.
- t=14: P3.

Same schedule as SJF here: avg TAT 10.75, avg WT 5.25.

---

## 10. Multilevel Queue and Multilevel Feedback Queue

### Multilevel Queue (MLQ)

Split the ready queue into several separate queues, for example:

```
Highest priority:  System processes          (RR, small q)
                   Interactive processes      (RR)
                   Batch processes            (FCFS)
Lowest priority:   Student processes          (FCFS)
```

- Each process is **permanently** assigned to one queue (by type).
- Each queue has its own algorithm.
- Between queues: either **fixed priority** (lower queue runs only if higher ones are empty, so it can starve) or **time slicing** (e.g. 80% of CPU to foreground, 20% to background).

### Multilevel Feedback Queue (MLFQ)

Same as above, but processes **can move between queues** based on behaviour:

- A process that uses its full quantum (CPU-hungry) is **demoted** to a lower queue.
- A process that waits too long is **promoted** (this is aging built in).

Typical setup: Q0 with q=8, Q1 with q=16, Q2 FCFS. A new process enters Q0. If it doesn't finish in 8, it drops to Q1. If not done in 16 more, it drops to Q2.

This naturally favours short and I/O-bound (interactive) processes, which are exactly the ones users care about.

MLFQ is defined by five parameters:
1. Number of queues
2. Scheduling algorithm for each queue
3. When to **upgrade** a process
4. When to **demote** a process
5. Which queue a new process enters

It's the **most general** and most complex scheduling algorithm.

> **Trap.** MLQ = **fixed** membership. ML**F**Q = processes **move** (F for Feedback, think F for "Flexible").

---

## 11. Summary comparison

| Algorithm | Preemptive? | Starvation? | Notes |
|---|---|---|---|
| FCFS | No | No | Convoy effect |
| SJF | No | Yes (long jobs) | Optimal among non-preemptive |
| SRTF | Yes | Yes (long jobs) | **Optimal overall** for avg WT; needs future knowledge |
| Priority | Either | Yes (low priority) | Fixed by aging |
| Round Robin | Yes | No | Best response time; avg WT can be poor |
| LJF / LRTF | No / Yes | Yes (short jobs) | Rarely useful |
| HRRN | No | No | Favours short jobs, no starvation |
| MLQ | Usually | Yes (lower queues) | Fixed queues |
| MLFQ | Yes | No (aging) | Most general |

---

## 12. Exam traps

1. **Only arrived processes are candidates.** Don't pick a process that hasn't arrived.
2. **Idle CPU:** if nobody has arrived, the CPU is idle. Include the idle gap.
3. **WT = TAT − BT**, not CT − BT.
4. **RR with huge q = FCFS.**
5. **SRTF is optimal** for average waiting time, but **not implementable** exactly.
6. **RR optimises response time**, not waiting time.
7. **Aging** fixes starvation in priority scheduling.
8. **HRRN is non-preemptive.**
9. Check the priority convention (lower number high, or higher number high?).
10. In RR, the new arrival usually enters the queue **before** the preempted process at the same instant.

---

## 13. Practice questions (with full working)

**Q1.** Three processes arrive at time 0 with burst times 24, 3, 3 in the order P1, P2, P3. Average waiting time under FCFS?
(a) 3 (b) 9 (c) 17 (d) 27

**Answer: (c).** WT: P1=0, P2=24, P3=27. Sum 51, avg 17.

---

**Q2.** Same processes, using SJF?
(a) 3 (b) 9 (c) 13 (d) 17

**Answer: (a).** Order P2, P3, P1. WT: 0, 3, 6. Avg 3.

---

**Q3.** Processes P1(AT 0, BT 5), P2(AT 0, BT 3), P3(AT 0, BT 1). Round Robin with q = 2 in order P1, P2, P3. Average waiting time?
(a) 3.67 (b) 4.33 (c) 5 (d) 4

**Answer: (b).**
Gantt: P1 0-2, P2 2-4, P3 4-5 (done), P1 5-7, P2 7-8 (done), P1 8-9 (done).
CT: P1=9, P2=8, P3=5. WT = CT − BT (AT=0): 4, 5, 4. Avg = 13/3 = 4.33.

---

**Q4.** Exponential averaging with α = 0.5, τ1 = 10, actual first two bursts 6 and 4. Predicted third burst τ3?
(a) 5 (b) 6 (c) 7 (d) 8

**Answer: (b).** τ2 = 0.5(6) + 0.5(10) = 8. τ3 = 0.5(4) + 0.5(8) = 6.

---

**Q5.** With α = 0 in exponential averaging, the prediction:
(a) equals the last burst (b) never changes from the initial guess (c) is always zero (d) doubles each time

**Answer: (b).** τ(n+1) = τ(n).

---

**Q6.** P1(AT 0, BT 8), P2(AT 1, BT 4), P3(AT 2, BT 9), P4(AT 3, BT 5). SRTF average waiting time?
(a) 6.5 (b) 7.75 (c) 6.75 (d) 5.75

**Answer: (a).**
- t=0: P1 runs. t=1: P2(4) < P1 remaining 7, preempt. P2 runs 1 to 5.
- t=5: P1(7), P3(9), P4(5). Run P4 5 to 10.
- t=10: P1(7), P3(9). P1 10 to 17. P3 17 to 26.

CT: P1=17, P2=5, P3=26, P4=10. TAT: 17, 4, 24, 7. WT: 9, 0, 15, 2. Sum 26, avg **6.5**.

---

**Q7.** Same data, non-preemptive SJF average waiting time?
(a) 7.75 (b) 6.5 (c) 8.25 (d) 7.0

**Answer: (a).**
P1 0 to 8. At 8: P2(4), P3(9), P4(5). P2 8 to 12, P4 12 to 17, P3 17 to 26.
WT: P1=0, P2=12−1−4=7, P3=26−2−9=15, P4=17−3−5=9. Sum 31, avg **7.75**.

---

**Q8.** In Round Robin, 6 processes are in the ready queue, q = 20 ms, context switch = 0. Maximum time a process waits before its next turn?
(a) 100 ms (b) 120 ms (c) 20 ms (d) 80 ms

**Answer: (a).** (n − 1)q = 5 × 20 = 100 ms.

---

**Q9.** Time quantum 10 ms, context switch 2 ms, all processes are CPU-bound and long. CPU efficiency?
(a) 80% (b) 83.3% (c) 90% (d) 100%

**Answer: (b).** 10/(10 + 2) = 83.3%.

---

**Q10.** Which algorithm can be thought of as Priority Scheduling where priority is the inverse of the predicted next burst?
(a) FCFS (b) RR (c) SJF (d) MLFQ

**Answer: (c).**

---

**Q11.** HRRN: at time t, process A has waited 9 units and has burst 3; process B has waited 12 units and has burst 6. Which runs next?
(a) A (b) B (c) tie (d) cannot say

**Answer: (a).** A: (9+3)/3 = 4. B: (12+6)/6 = 3. A has the higher ratio.

---

**Q12.** P1(AT 0, BT 3), P2(AT 5, BT 2). FCFS average turnaround time?
(a) 2.5 (b) 3 (c) 2 (d) 5

**Answer: (a).** P1 runs 0-3. CPU idle 3-5. P2 runs 5-7. TAT: 3 and 2. Avg 2.5. (Don't forget the idle gap; P2 can't start at 3.)

---

**Q13.** Which scheduling algorithm gives the minimum average waiting time for a given set of processes (all arriving at time 0)?
(a) FCFS (b) SJF (c) RR (d) Priority

**Answer: (b).** When all arrive at 0, SJF and SRTF give the same schedule, and it's optimal.

---

**Q14.** Which statement about MLFQ is FALSE?
(a) Processes may move between queues (b) It can implement aging (c) Each process is permanently bound to one queue (d) It can favour I/O-bound processes

**Answer: (c).** That describes plain MLQ.

---

**Q15.** Under LRTF (preemptive longest remaining time first, ties broken by lower process number), P1(AT 0, BT 2), P2(AT 0, BT 4), P3(AT 0, BT 8). Completion time of P1?
(a) 12 (b) 13 (c) 14 (d) 11

**Answer: (a) 12.** Re-evaluate at **every** time unit:
- t=0 to 4: P3 is longest (8). It runs down to 4.
- t=4: P2 = 4, P3 = 4 (tie, pick P2). P2 runs 1 unit, P2 = 3.
- t=5: P3 (4) is longest, runs, P3 = 3. t=6: tie, P2 runs, P2 = 2. t=7: P3 runs, P3 = 2.
- t=8: all three have 2. Pick P1, P1 = 1.
- t=9: P2 and P3 tie at 2, pick P2, P2 = 1. t=10: P3 runs, P3 = 1.
- t=11: all have 1. P1 runs and **finishes at 12**. P2 finishes at 13, P3 at 14.

Shortcut worth remembering: under LRTF, when all processes arrive together, they all finish **at the very end, one unit apart** (12, 13, 14 here), because the longest one keeps getting cut down to match the others.

---

**Q16.** Which of these is most likely to suffer from the convoy effect?
(a) RR (b) FCFS (c) SRTF (d) MLFQ

**Answer: (b).**

---

**Q17.** For non-preemptive scheduling algorithms, Response Time of each process equals its:
(a) Turnaround time (b) Waiting time (c) Burst time (d) Completion time

**Answer: (b).** It starts once and runs to completion, so the first response happens at the end of its wait.

---

**Q18.** Three CPU-bound processes, bursts 10, 20, 30, all arrive at time 0. Using RR with q = 10, what is the average turnaround time?
(a) 40 (b) 46.67 (c) 50 (d) 36.67

**Answer: (d).**
P1 0-10 (done), P2 10-20, P3 20-30, P2 30-40 (done), P3 40-50, P3 50-60 (done).
CT = 10, 40, 60. Avg TAT = 110/3 = **36.67**. (Since all arrive at 0, TAT = CT.)

---

**Practice questions:** [5.03 CPU Scheduling](../ISRO_CS_Question_Bank/05_Operating_Systems/5.03_CPU_Scheduling.md)
