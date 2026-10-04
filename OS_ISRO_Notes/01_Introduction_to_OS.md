# 01. Introduction to Operating Systems: Types, Structure and System Calls

> **How to read this chapter.** Don't try to memorise anything on the first read. Read it like a story. Every idea here exists because someone had a problem, and the idea was the fix. If you understand the problem, you will never forget the fix.

---

## 1. Start with a question: what would a computer be like without an OS?

Imagine you just bought a bare computer: a CPU, some RAM, a disk, a keyboard, a screen. No operating system.

You want to run a program that prints "Hello". What would you have to do?

- Load the program's bytes from the disk into memory yourself. That means knowing which sectors on the disk hold the file, how to talk to the disk controller, and where in RAM to put the bytes.
- Tell the CPU where the first instruction is.
- To print, write bytes directly to the display controller's memory, in whatever format that specific hardware expects.
- If you want to run a second program at the same time, you need to make sure it doesn't overwrite the first one's memory.

That is a nightmare. Every programmer would have to understand every piece of hardware. And any buggy program could wreck everything else.

**An operating system (OS) is the piece of software that sits between your programs and the hardware so that you don't have to do any of that.**

```
   +------------------------------------------+
   |   Users                                  |
   +------------------------------------------+
   |   Application programs (browser, gcc)    |
   +------------------------------------------+
   |   OPERATING SYSTEM                       |
   +------------------------------------------+
   |   Hardware (CPU, RAM, disk, I/O devices) |
   +------------------------------------------+
```

### Two ways to look at an OS

1. **The OS as a resource manager (allocator).** The computer has limited resources: CPU time, memory, disk space, I/O devices, buses. Many programs want them at the same time. The OS decides who gets what, when, and for how long. Think of it as a traffic police officer at a busy junction.

2. **The OS as an extended machine.** The raw hardware is ugly and complicated. The OS hides that ugliness and gives you clean abstractions: "a file" instead of "sectors 4013 to 4020 on platter 2", "a process" instead of "a set of register values and memory pages". Think of it as the dashboard of a car. You press the accelerator; you don't inject fuel manually.

### Goals of an OS

| Goal | Type | Meaning |
|---|---|---|
| **Convenience** | **Primary** | Make the computer easy to use |
| Efficiency | Secondary | Use hardware resources well |
| Reliability | Secondary | Keep working correctly even when programs misbehave |
| Maintainability | Secondary | Be easy to update, fix and extend |

> **Exam trap.** Students often answer "efficiency" as the primary goal. It is **convenience**. Efficiency matters, but the reason operating systems were invented in the first place was to make computers usable.

A neat way to remember it: *functions* (process management, memory management, file management, I/O management, security) are the **means**; *goals* (convenience, efficiency) are the **ends**.

---

## 2. How operating systems evolved (and why each step happened)

The history of OS types is really a history of one fight: **keep the expensive CPU busy**. Each new type of OS fixed a waste in the previous one.

### Step 1: Batch operating systems

In early days, a human operator loaded one job, waited for it to finish, then loaded the next. The CPU sat idle while the human fiddled with punched cards.

**Fix:** group similar jobs together into a *batch* and let a small program called a **batch monitor** run them one after another automatically.

- Good: no human delay between jobs.
- Bad: **no interaction** with the user while the job runs. And if a job is waiting for slow I/O (like reading a tape), the CPU still sits idle.

### Step 2: Spooling

**SPOOL** = **S**imultaneous **P**eripheral **O**perations **O**n-**L**ine.

Problem: a printer is super slow compared with the CPU. If the CPU must wait for the printer to finish before doing anything else, it wastes millions of cycles.

Fix: write the output into a buffer on disk (a queue), and let the printer slowly drain the queue on its own. Meanwhile the CPU moves on to another job.

Everyday example: when you click "Print" on three documents, they sit in the **print queue**. That queue is spooling.

The key idea: **overlap the I/O of one job with the computation of another.**

### Step 3: Multiprogramming

Spooling helped with output devices, but what if the running job itself needs to wait for input from the disk?

Fix: keep **several jobs in main memory at once**. When the running job blocks for I/O, the OS switches the CPU to another job that is ready.

```
Time --->
CPU:   [Job A computes][Job B computes][Job A computes][Job C computes]
I/O:                   [Job A reads disk]              [Job B reads disk]
```

As long as at least one job is ready, the CPU is busy.

Cost: the OS now needs **memory management** (several jobs share RAM safely) and **CPU scheduling** (who runs next).

### Step 4: Multitasking / Time-sharing

Multiprogramming switches only when a job blocks. A job that never does I/O could hog the CPU for an hour, and a user typing at a terminal would see no response.

Fix: switch between jobs **very frequently** (every few milliseconds), even if the running job doesn't block. Each user feels the machine is theirs alone.

> **Exam trap.** "Multitasking" and "time-sharing" are essentially **the same idea**: multiprogramming plus fast switching for interactivity. Don't treat them as unrelated categories.

Cost: switching (called a **context switch**) is pure overhead, so switching too often wastes time.

### Step 5: Multiprocessing (tightly coupled systems)

Instead of one CPU pretending to run many things, use **two or more CPUs** that **share one main memory** (and usually one clock and bus).

Two flavours:

| | Symmetric (SMP) | Asymmetric |
|---|---|---|
| Relationship | All CPUs are **peers** | **Master-slave** |
| Who runs the OS? | Each CPU runs the OS code, scheduling itself | Only the master schedules; slaves run assigned tasks |
| Design | Harder | Simpler |
| Efficiency | Better | Master can become a bottleneck |
| Examples | Windows, Linux, macOS on multicore chips | Older or special-purpose systems |

Advantages of multiprocessing:

1. **Increased throughput.** More work done per unit time. But **N processors give a speedup less than N**, because the CPUs must coordinate and they compete for shared memory and the bus.
2. **Economy of scale.** CPUs share the same memory, disks and power supply, so it's cheaper than N separate computers.
3. **Increased reliability (graceful degradation).** If 1 of 10 CPUs fails, the system slows by about 10%, it doesn't crash.

> **Exam trap.** Never assume linear speedup. 4 CPUs do **not** make a program 4x faster.

### Step 6: Real-time operating systems (RTOS)

Some systems care less about average speed and more about **meeting deadlines**. An RTOS guarantees that tasks finish within a fixed time.

- **Hard real-time:** missing a deadline is a **total failure**. Examples: airbag deployment, anti-lock brakes, pacemakers, missile guidance, a rocket's flight computer.
- **Soft real-time:** missing a deadline **degrades quality** but is tolerable. Examples: video streaming (a frame drops), online games, digital cameras.

Intuition: if a late answer is a *wrong* answer, it's hard real-time. If a late answer is just an *annoying* answer, it's soft real-time.

### Step 7: Distributed operating systems

Multiple **independent computers** connected by a network, with **no shared memory and no shared clock** (loosely coupled), but the OS presents them to the user as **one single system**. This illusion is called **transparency** or a **single system image**.

Benefits: resource sharing, speedup, reliability, communication.
Cost: you now depend on the network.

### Summary table

| Type | Core idea | Main gain | Main cost |
|---|---|---|---|
| Batch | Group jobs, run automatically | No human delay | No interaction; CPU idle during I/O |
| Spooling | Buffer slow I/O on disk | CPU doesn't wait for slow devices | Disk space for buffers |
| Multiprogramming | Many jobs in memory; switch on block | CPU busy as long as any job is ready | Needs memory mgmt + scheduling |
| Time-sharing / Multitasking | Switch very frequently | Interactivity | Context-switch overhead |
| Multiprocessing | Many CPUs, shared memory | Throughput, reliability | Contention, complexity |
| Real-time | Deadline guarantees | Predictability | Lower average throughput |
| Distributed | Many computers, look like one | Sharing, scalability | Network dependence |

---

## 3. User interfaces

How does a human actually talk to the OS?

| Interface | How you use it | Who loves it |
|---|---|---|
| **CLI** (Command-Line Interface) | Type commands to a **shell** (command interpreter) like `bash` | Sysadmins and power users: fast, scriptable |
| **GUI** (Graphical User Interface) | Windows, icons, menus, mouse pointer | Most users: intuitive |
| **Touchscreen** | Taps, swipes, pinches | Phone and tablet users |

The shell itself is usually **not part of the kernel**. It's an ordinary program that reads your commands and asks the kernel to do things.

---

## 4. How an OS is structured internally

An OS is a huge program (Linux has tens of millions of lines). How do you organise something that big? There are three classic answers.

### 4.1 Simple / Monolithic structure

Everything (file system, scheduler, memory manager, device drivers) lives inside **one big program running in kernel mode**. Functions inside it call each other directly.

- Example: original MS-DOS, early UNIX. (Modern Linux is mostly monolithic too, with loadable modules.)
- **Fast:** a direct function call is cheap.
- **Fragile:** a bug in one driver can crash the whole kernel. Hard to maintain and extend.

Analogy: one giant room where everyone works. Communication is instant, but one person spilling coffee ruins everyone's papers.

### 4.2 Layered structure

Split the OS into layers. Layer 0 is the hardware, layer N is the user interface. **Each layer uses only the services of the layer directly below it.**

```
Layer N  : user interface
   ...
Layer 2  : memory management
Layer 1  : CPU scheduling
Layer 0  : hardware
```

- Good: easy to **debug and verify**. Test layer 1 first; once it's correct, build layer 2 on top, and so on. Each layer hides its details from the layers above (information hiding).
- Bad: **overhead**. A request from the top has to pass through every layer. Also, deciding the right order of layers is tricky (does memory management sit above or below the disk driver? Both need each other).

### 4.3 Microkernel structure

Keep the kernel **tiny**: only the absolutely essential things (inter-process communication (IPC), basic scheduling, low-level memory management). Move everything else (file systems, device drivers, network stacks) out into **user-space server processes**. They talk via **message passing**.

- Examples: **Mach**, **MINIX**, QNX.
- Good: **more reliable and secure** (a crashed driver is just a crashed user process; restart it). **Easy to extend** (add a new service without touching the kernel).
- Bad: **slower**, because every service request now involves messages crossing between user mode and kernel mode.

> **Exam trap (very common).** Students flip this trade-off.
> - **Monolithic** = **faster**, less reliable, less modular.
> - **Microkernel** = **more reliable/extensible**, **slower** (IPC overhead).

---

## 5. System calls and dual-mode operation

This is the most important section of the chapter. Read it slowly.

### 5.1 The danger problem

Your program and the OS both run on the same CPU. What stops your program from doing something dangerous, like:

- writing directly to the disk and erasing other people's files,
- turning off interrupts so the OS never gets control back,
- reading another process's memory?

Answer: **the hardware itself** helps the OS.

### 5.2 Dual mode and the mode bit

The CPU has a **mode bit**:

| Mode bit | Mode | Who runs here | Can execute privileged instructions? |
|---|---|---|---|
| **0** | **Kernel mode** (supervisor / system / privileged mode) | OS kernel | **Yes** |
| **1** | **User mode** | Your programs | **No** |

**Privileged instructions** are the dangerous ones: I/O instructions, changing the timer, turning interrupts on/off, changing memory-protection registers, switching the mode bit itself.

If a user-mode program tries to execute a privileged instruction, the hardware refuses and raises a **trap** to the OS, which typically kills the program.

### 5.3 So how does a user program get anything done?

If your program can't do I/O itself, how does `printf` ever print anything?

It **asks the OS politely** through a **system call**.

A **system call** is the programming interface through which a user program requests a service from the OS kernel.

Here's the sequence step by step:

```
User program (mode bit = 1)
   |  calls printf("Hi")
   |  printf (a library function) eventually calls write(...)
   |  write() executes a special TRAP instruction (e.g. syscall / int 0x80)
   v
Hardware: switch mode bit 1 -> 0, jump to a fixed OS entry point
   v
Kernel (mode bit = 0)
   |  looks up the system call number in a table
   |  checks the arguments are valid (is this buffer really yours?)
   |  performs the I/O
   v
Kernel sets mode bit 0 -> 1 and returns to the user program
```

Key points:

- A **trap** is a **software-generated interrupt**. It's the *only* door from user mode into kernel mode.
- A **normal function call does NOT change the mode bit.** Calling your own `add(a, b)` function stays entirely in user mode.

> **Exam trap.** "Does calling a library function switch to kernel mode?" Only if that library function internally makes a system call. `strlen()` never does; `printf()` eventually does (via `write`).

### 5.4 APIs: why programmers rarely see raw system calls

Programmers usually call an **API** (Application Programming Interface), and the API calls the system call for you.

- **POSIX API**: UNIX, Linux, macOS
- **Windows API** (Win32): Windows
- **Java API**: for the Java Virtual Machine

Why use an API instead of raw system calls?

1. **Portability:** a program written to POSIX runs on any POSIX system.
2. **Simplicity:** raw system calls are low-level and fiddly.
3. The caller only needs to know the **contract** (what goes in, what comes out), not how the kernel implements it.

### 5.5 Passing parameters to a system call

Three standard ways:

1. **In registers** (fastest, but limited number of registers).
2. **In a block/table in memory**, and pass the block's address in a register (used by Linux for many parameters).
3. **Pushed on the stack** by the program, popped by the OS.

### 5.6 Categories of system calls

| Category | Examples (UNIX) | What they do |
|---|---|---|
| **Process control** | `fork()`, `exec()`, `exit()`, `wait()` | Create, run, end, wait for processes |
| **File management** | `open()`, `read()`, `write()`, `close()` | Work with files |
| **Device management** | `ioctl()`, `read()`, `write()` | Request/release devices, control them |
| **Information maintenance** | `getpid()`, `alarm()`, `sleep()`, time calls | Get/set time, date, process attributes |
| **Communication** | `pipe()`, `shmget()`, `mmap()`, sockets | Create connections, send/receive messages, shared memory |
| **Protection** | `chmod()`, `umask()`, `chown()` | Control access to resources |

A handy memory hook: **"P-F-D-I-C"** (Process, File, Device, Information, Communication), plus Protection.

---

## 6. Interrupts, traps and exceptions (quick clarity)

These three words get mixed up. Here's a clean way to separate them:

| Term | Caused by | Synchronous with the program? | Example |
|---|---|---|---|
| **Hardware interrupt** | An external device | No (can arrive at any time) | Keyboard key pressed, disk read finished, timer tick |
| **Trap** (software interrupt) | The program, **on purpose** | Yes | System call instruction |
| **Exception** (fault) | The program, **by accident** | Yes | Divide by zero, invalid memory access, page fault |

All three cause the CPU to switch to kernel mode and jump to an OS handler.

The **timer interrupt** deserves a special mention: the OS sets a timer before giving the CPU to a user program. When it fires, control returns to the OS, so a program stuck in an infinite loop cannot hold the CPU forever. Setting the timer is, of course, a privileged instruction.

---

## 7. Booting in one paragraph

When you power on, the CPU runs a small program stored in ROM/firmware (the **bootstrap program**, part of BIOS/UEFI). It initialises hardware, then finds the **boot loader** on disk (e.g. GRUB), which loads the **kernel** into memory and starts it. The kernel initialises its data structures and starts the first user process (`init` or `systemd` on Linux). From then on, the OS is **interrupt-driven**: it sits waiting for interrupts, traps and exceptions.

---

## 8. Common traps (read twice)

1. **Primary goal = convenience**, not efficiency.
2. **Multitasking = time-sharing** (same concept).
3. **N processors -> speedup < N.**
4. **SMP = peers**, asymmetric = master/slave.
5. **Hard real-time** = deadline miss is failure (pacemaker, airbag). **Soft** = degraded quality (streaming).
6. **Microkernel** = reliable + extensible + **slower**. **Monolithic** = **faster** + fragile.
7. **Mode bit 0 = kernel**, 1 = user.
8. **Only a trap / interrupt / exception** switches user -> kernel mode; a normal function call doesn't.
9. A distributed OS has **no shared memory and no shared clock**; multiprocessing has **shared memory**.

---

## 9. Quick recap

- OS = intermediary between user and hardware; resource manager + extended machine.
- OS types evolved to keep the CPU busy: batch -> spooling -> multiprogramming -> time-sharing -> multiprocessing; plus RTOS and distributed OS for special needs.
- Structures: monolithic (fast, fragile), layered (clean, slow), microkernel (reliable, slower).
- Dual mode protects the system; system calls (via traps) are the controlled doorway into the kernel.

---

## 10. Practice questions

**Q1.** The main purpose of spooling is to:
(a) increase memory size (b) overlap slow I/O of one job with the computation of another (c) provide security (d) compile programs faster

**Answer: (b).** Spooling buffers data for slow devices on disk so the CPU doesn't wait for them.

---

**Q2.** Which of the following instructions should be privileged?
(a) Read the time of day (b) Set the value of the timer (c) Add two registers (d) Call a library function

**Answer: (b).** If a user could set the timer, it could prevent the OS from ever regaining control. Reading the clock is harmless; adding registers and calling library code are ordinary user-mode actions.

---

**Q3.** In multiprogramming, the CPU switches from one job to another when:
(a) a fixed time slice expires (b) the running job needs to wait (e.g. for I/O) (c) the user presses a key (d) memory is full

**Answer: (b).** Plain multiprogramming switches on blocking. Switching on a time slice is what time-sharing adds.

---

**Q4.** A system has 8 identical CPUs sharing one memory. A highly parallel job takes 80 s on one CPU. A realistic run time on all 8 is:
(a) exactly 10 s (b) less than 10 s (c) a bit more than 10 s (d) 80 s

**Answer: (c).** Coordination and contention overheads make speedup less than 8, so time is a bit more than 80/8 = 10 s.

---

**Q5.** Which of these is a hard real-time application?
(a) Video conferencing (b) Online multiplayer game (c) Anti-lock braking system (d) Music streaming

**Answer: (c).** Missing a braking deadline can cause a crash, which is a total failure.

---

**Q6.** In a microkernel OS, the file system typically runs:
(a) inside the kernel (b) as a user-space server process (c) in firmware (d) inside the hardware

**Answer: (b).** Microkernels move non-essential services out into user space.

---

**Q7.** Which structure is easiest to debug layer by layer but suffers from overhead of crossing many layers?
(a) Monolithic (b) Layered (c) Microkernel (d) Simple

**Answer: (b).**

---

**Q8.** What happens when a user-mode program tries to execute a privileged instruction?
(a) It runs normally (b) The CPU ignores it silently (c) The hardware traps to the OS, which usually terminates the program (d) The mode bit becomes 0 automatically and it runs

**Answer: (c).** Hardware never silently grants kernel power to user code.

---

**Q9.** `getpid()` belongs to which category of system calls?
(a) Process control (b) File management (c) Information maintenance (d) Communication

**Answer: (c).** It returns information (the process ID) about the process. Students sometimes say "process control", but process control means create/terminate/wait.

---

**Q10.** Which statement about a distributed operating system is TRUE?
(a) All nodes share a single main memory (b) All nodes share a global clock (c) Nodes are loosely coupled but the system appears as one machine (d) It requires exactly one CPU

**Answer: (c).**

---

**Q11.** Which of the following causes a switch from user mode to kernel mode? (Choose the best answer.)
(a) A call to a user-defined function (b) A system call (c) A `for` loop (d) Declaring a variable

**Answer: (b).**

---

**Q12.** A divide-by-zero in a user program is an example of:
(a) hardware interrupt (b) trap/exception raised synchronously by the program (c) system call (d) spooling

**Answer: (b).** It's caused by the program's own instruction, synchronously, by accident (an exception).

---

**Q13.** The timer interrupt exists mainly to:
(a) keep the clock accurate (b) ensure the OS regains control from a running user program (c) speed up the CPU (d) count disk accesses

**Answer: (b).**

---

**Q14.** Which is NOT an advantage of multiprocessor systems?
(a) Increased throughput (b) Economy of scale (c) Increased reliability (d) Linear speedup guaranteed

**Answer: (d).**

---

**Q15.** Which of the following is the primary reason POSIX APIs are used instead of raw system calls?
(a) They are faster than system calls (b) Program portability and simplicity (c) They run in kernel mode (d) They avoid the mode switch

**Answer: (b).** An API still ends up making the system call (so it's not faster and doesn't avoid the mode switch). Its value is portability and ease of use.
