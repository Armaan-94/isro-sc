# 02. Process Management: PCB, States and Schedulers

> **Big idea of this chapter.** A *program* is a recipe. A *process* is someone actually cooking from that recipe, with ingredients on the counter and a note of which step they're on. The OS's job is to keep track of many cooks sharing one kitchen.

---

## 1. Program vs Process

| Program | Process |
|---|---|
| **Passive** entity | **Active** entity |
| A file of instructions sitting on disk (e.g. `a.out`, `chrome.exe`) | A program **in execution**, loaded into memory, with its own state |
| Needs no resources while sitting there | Needs CPU time, memory, registers, open files, I/O devices |
| Lives forever until deleted | Has a lifetime: created, runs, terminates |

**When does a program become a process?** When it is loaded into main memory **and** the OS creates a **PCB** (Process Control Block) for it.

### One program, many processes

Open two browser windows from the same `chrome.exe`. You now have **two separate processes**. They share the same *code* (the text section can even be shared in memory), but each has its **own data, heap and stack**. If one crashes, the other can keep going.

---

## 2. What a process looks like in memory

When a process is created, it gets an address space laid out like this (high addresses at the top):

```
 High address
 +-----------------+
 |     Stack       |  function parameters, return addresses, local variables
 |       |         |  (grows downward)
 |       v         |
 |                 |
 |       ^         |
 |       |         |
 |      Heap       |  memory from malloc()/new at run time (grows upward)
 +-----------------+
 |      Data       |  global and static variables
 +-----------------+
 |   Text (code)   |  the compiled machine instructions
 +-----------------+
 Low address
```

Why do stack and heap grow toward each other? So that the free space between them can be used by whichever one needs it more. If they ever collide, you're out of memory (stack overflow or malloc failure).

Example in C:

```c
int count = 0;            // Data section (global)
int main() {
    int x = 5;            // Stack (local)
    int *p = malloc(40);  // p itself is on the stack; the 40 bytes are on the heap
    count++;
}
```

---

## 3. The Process Control Block (PCB)

The OS can't keep a process's state "in its head". It needs a data structure per process. That structure is the **PCB** (also called the **task control block**; in Linux it's `struct task_struct`).

Think of it as the process's **ID card + bookmark + medical file** combined.

| Field | What it stores | Why it's needed |
|---|---|---|
| **Process state** | new / ready / running / waiting / terminated | Scheduler needs to know who can run |
| **Process ID (PID)** | Unique number | To identify it |
| **Program counter (PC)** | Address of the next instruction | To resume exactly where it stopped |
| **CPU registers** | Accumulators, index registers, stack pointer, condition codes | To restore the CPU exactly as it was |
| **CPU-scheduling info** | Priority, pointers into scheduling queues | For the scheduler |
| **Memory-management info** | Base/limit registers, page tables, segment tables | To know where its memory is |
| **Accounting info** | CPU time used, time limits, job numbers | Billing, limits, statistics |
| **I/O status info** | List of open files, allocated devices | To clean up and continue I/O |

> **Exam trap.** The PCB is precisely what is **saved and restored during a context switch**. The most time-critical parts to save are the **program counter and CPU registers**. The PCB does **not** store things like "which compiler built the program" or the source code.

---

## 4. Process states: the life of a process

A process moves through five states:

```
              admitted           scheduler dispatch            exit
   [ NEW ] -----------> [ READY ] -----------------> [ RUNNING ] -----> [ TERMINATED ]
                           ^    <-----------------     |
                           |     interrupt /           |
                           |     time slice over       |  I/O or event wait
                           |                           v
                           +------------------- [ WAITING / BLOCKED ]
                              I/O or event completion
```

Meaning of each state:

- **New:** the process is being created (PCB being set up, memory being allocated).
- **Ready:** it is in memory and has everything it needs **except the CPU**. It's waiting in line.
- **Running:** its instructions are being executed on a CPU right now.
- **Waiting (Blocked):** it can't continue until some event happens (disk read finishes, a key is pressed, a signal arrives). **Even if the CPU were free, it couldn't use it.**
- **Terminated:** finished. The OS reclaims resources.

### The transitions (memorise the *trigger* for each)

| Transition | Trigger |
|---|---|
| New -> Ready | OS admits the process (long-term scheduler) |
| Ready -> Running | **Dispatch:** the short-term scheduler picks it |
| Running -> Ready | **Preemption:** timer interrupt (time slice over) or a higher-priority process arrives |
| Running -> Waiting | Process requests I/O or waits for an event |
| Waiting -> Ready | The I/O or event completes |
| Running -> Terminated | Process calls `exit()` or is killed |

### Transitions that **never** happen directly

- **Waiting -> Running.** When I/O completes, the process goes back to **Ready** and must wait its turn. It doesn't jump straight onto the CPU.
- **Ready -> Waiting.** A process can only *request* I/O by running an instruction, so it must be Running first.
- **New -> Running.** It must be admitted to Ready first.

These are classic MCQ distractors.

### Counting questions

On a system with **one CPU** and **n** processes:

| State | Minimum | Maximum |
|---|---|---|
| Running | 0 | **1** |
| Ready | 0 | n |
| Waiting | 0 | n |

With **P** CPUs, at most **P** processes can be Running at once (and at most n of course, so min(P, n)).

> **Exam trap.** "Maximum processes in Running state with n processes on a uniprocessor?" The answer is **1**, not n.

### Suspended states (seven-state model)

When memory is tight, the OS may **swap** a process out to disk. That adds two states:

- **Ready-Suspended:** ready to run but currently swapped out to disk.
- **Blocked-Suspended:** waiting for an event *and* swapped out.

Swapping in and out is the **medium-term scheduler's** job (see below).

---

## 5. Scheduling queues

The OS keeps PCBs in queues (usually linked lists of PCBs):

1. **Job queue:** all processes in the system.
2. **Ready queue:** processes in main memory, ready and waiting for the CPU.
3. **Device queues:** one per I/O device, holding processes waiting for that device.

A process spends its life moving between these queues.

---

## 6. The three schedulers

A **scheduler** is the part of the OS that chooses which process moves next. There are three, operating at three different time scales.

| Scheduler | Also called | Decides | How often | Must be |
|---|---|---|---|---|
| **Long-term** | Job scheduler | Which jobs from the disk pool are **admitted into memory** (new -> ready) | Rarely (seconds to minutes) | Can be slow, thoughtful |
| **Short-term** | CPU scheduler | Which **ready** process gets the CPU next | **Very often** (every ~10-100 ms) | **Very fast** |
| **Medium-term** | Swapper | Which processes to **swap out** of memory (and later back in) | As needed, under memory pressure | Moderate |

### Degree of multiprogramming

The **degree of multiprogramming** is the number of processes currently in memory. The **long-term scheduler controls it** (by deciding how many to admit). The medium-term scheduler adjusts it downwards temporarily by swapping processes out.

### Why the short-term scheduler must be fast

Suppose the scheduler runs every 100 ms and takes 10 ms to decide. Then 10/(100+10) ≈ 9% of the CPU is wasted just on deciding. That's terrible. So the CPU scheduler must be extremely lightweight.

### CPU-bound vs I/O-bound processes

- **CPU-bound:** spends most time computing; few, long CPU bursts. Example: video encoding, scientific simulation.
- **I/O-bound:** spends most time waiting for I/O; many short CPU bursts. Example: a text editor, a web server waiting on network.

The long-term scheduler should admit a **good mix**:

- All I/O-bound -> the ready queue is almost always empty, the CPU idles.
- All CPU-bound -> I/O devices sit idle, and the ready queue is always full.

> **Exam trap.** "What mix should the long-term scheduler aim for?" A **balanced mix** of CPU-bound and I/O-bound processes.

Some systems (like many time-sharing systems such as Windows and UNIX) have **no long-term scheduler** at all; every new process is simply put in memory.

---

## 7. Dispatcher and context switch

### The dispatcher

The short-term scheduler *chooses*. The **dispatcher** *does the actual handover*:

1. Performs the **context switch** (save old process state, load new process state).
2. **Switches to user mode.**
3. **Jumps** to the right location in the new process's program to resume it.

The time the dispatcher takes to stop one process and start another is called **dispatch latency**. It should be as small as possible.

### The context switch in detail

```
Process P0 running
   |  interrupt or system call
   v
OS: save P0's PC, registers, state  --->  into PCB0
OS: load P1's PC, registers, state  <---  from PCB1
   v
Process P1 running
```

Key facts:

- A context switch is **pure overhead**. During it, no user process makes progress.
- Its time depends on hardware: how many registers must be copied, memory speed, and whether special instructions exist (some CPUs have multiple register sets, so a switch is just changing a pointer).
- More frequent switches = more overhead. This trade-off comes back in Round Robin scheduling.

---

## 8. Process creation and termination (the essentials)

### Creation

Processes form a **tree**: a **parent** process creates **child** processes. In UNIX:

- `fork()` creates a child that is a **copy** of the parent.
- `exec()` replaces the child's memory with a new program.
- The parent can `wait()` for the child to finish.

Options a system can choose:

- Resource sharing: parent and child share all, some, or no resources.
- Execution: parent continues concurrently, or waits until children terminate.

(We'll do `fork()` counting questions in [Chapter 05](05_Threads_and_Multithreading.md).)

### Termination

- A process ends with `exit()`. The OS deallocates its resources.
- **Zombie process:** the child has terminated, but the parent hasn't yet called `wait()` to collect its exit status. Its PCB entry stays in the process table.
- **Orphan process:** the parent terminated first, without waiting. In UNIX, orphans are adopted by `init` (PID 1), which periodically calls `wait()`.
- **Cascading termination:** some systems kill all children when a parent dies.

---

## 9. Inter-process communication (IPC) in brief

Processes are isolated, but sometimes they need to cooperate (a producer and a consumer, a browser's tabs and its main process). Two basic models:

| | Shared memory | Message passing |
|---|---|---|
| How | A region of memory is mapped into both processes | Processes send/receive messages via the kernel |
| Speed | **Faster** (after setup, no kernel involvement) | Slower (kernel call per message) |
| Ease | Need to handle synchronisation yourself | Easier, built-in synchronisation |
| Good for | Large data | Small data, distributed systems |

Message passing can be **blocking (synchronous)** or **non-blocking (asynchronous)** on both send and receive.

---

## 10. Common traps

1. Program = passive, process = active.
2. A program becomes a process when **loaded into memory and given a PCB**.
3. The PCB is what's saved/restored in a context switch; **PC + registers** are the critical part.
4. **Waiting -> Running** is not a valid direct transition. It's Waiting -> Ready -> Running.
5. Max processes in Running = **number of CPUs** (1 on a uniprocessor).
6. Long-term scheduler controls the **degree of multiprogramming**.
7. Short-term scheduler must be **fast** because it runs **often**.
8. The **dispatcher** does the context switch; the scheduler only decides.
9. Context switching is **pure overhead**.

---

## 11. Practice questions

**Q1.** Which of these state transitions is NOT possible directly?
(a) Running -> Ready (b) Waiting -> Ready (c) Waiting -> Running (d) Running -> Waiting

**Answer: (c).** After I/O completes, the process must re-enter the ready queue and be dispatched again.

---

**Q2.** A process is in the Ready state. This means:
(a) it is executing (b) it is waiting for I/O (c) it is in memory and waiting only for the CPU (d) it has terminated

**Answer: (c).**

---

**Q3.** A system has 4 CPUs and 10 processes. The maximum number of processes in the Ready state at one moment is:
(a) 10 (b) 6 (c) 4 (d) 9

**Answer: (a).** All 10 could be ready if none is running at that instant (for example right after a burst of interrupts). Careful: some textbooks ask "if all 4 CPUs are busy", in which case it's 6. Read the question. With no extra condition, 10 is possible.

---

**Q4.** Which scheduler is invoked most frequently?
(a) Long-term (b) Medium-term (c) Short-term (d) All equally

**Answer: (c).**

---

**Q5.** Swapping a process out of memory to reduce load is done by:
(a) long-term scheduler (b) medium-term scheduler (c) short-term scheduler (d) dispatcher

**Answer: (b).**

---

**Q6.** Dispatch latency is:
(a) time to load a program from disk (b) time for the dispatcher to stop one process and start another (c) time a process waits in the ready queue (d) time to complete I/O

**Answer: (b).**

---

**Q7.** Which of the following is NOT part of a PCB?
(a) Program counter (b) List of open files (c) Page table pointer (d) The source code of the program

**Answer: (d).**

---

**Q8.** Local variables of a function are stored in the:
(a) text section (b) data section (c) heap (d) stack

**Answer: (d).**

---

**Q9.** Memory obtained using `malloc()` comes from the:
(a) stack (b) heap (c) data section (d) text section

**Answer: (b).**

---

**Q10.** A child process has finished, but its parent has not called `wait()`. The child is a:
(a) orphan (b) zombie (c) daemon (d) thread

**Answer: (b).** Zombie = dead child not yet reaped. Orphan = live child whose parent died.

---

**Q11.** If the long-term scheduler admits only I/O-bound processes, what happens?
(a) CPU utilization becomes very high (b) The ready queue is often empty and the CPU idles (c) I/O devices sit idle (d) No effect

**Answer: (b).**

---

**Q12.** Context switch time is:
(a) useful computation time (b) overhead (c) I/O time (d) part of burst time

**Answer: (b).**

---

**Q13.** A scheduler takes 2 ms to make a decision and processes run for 18 ms between decisions. What fraction of CPU time is wasted on scheduling?
(a) 2% (b) 10% (c) 11.1% (d) 18%

**Answer: (b).** Wasted fraction = 2 / (18 + 2) = 2/20 = 10%.

---

**Q14.** Which IPC model is generally faster for exchanging large amounts of data between two processes on the same machine?
(a) Message passing (b) Shared memory (c) Pipes (d) Sockets

**Answer: (b).** Once set up, shared memory needs no kernel call per access.

---

**Q15.** Which process state transition is caused by a timer interrupt?
(a) Ready -> Running (b) Running -> Ready (c) Running -> Waiting (d) Waiting -> Ready

**Answer: (b).** Time slice expires, the process is preempted back to Ready.

---

**Practice questions:** [5.02 Process Management PCB States](../ISRO_CS_Question_Bank/05_Operating_Systems/5.02_Process_Management_PCB_States.md)
