# 12. Linux: Kernel, Job Scheduling and Memory Management

> **Context.** Linux is the OS that runs most servers, all Android phones, most supercomputers, and many spacecraft ground systems. This chapter builds on UNIX ([Chapter 11](11_Unix_OS_and_Shell_Scripting.md)) and focuses on what Linux adds: its kernel design, job scheduling tools (`cron`, `at`, `batch`, `anacron`), and its memory allocators.

---

## 1. What exactly is "Linux"?

Strictly, **Linux is a kernel**, written by **Linus Torvalds** in 1991, free and open source (GPL licence), UNIX-like.

A **distribution (distro)** packages the Linux kernel with system libraries, a shell, utilities, a package manager and applications. Examples: **Ubuntu, Debian, Fedora, Red Hat Enterprise Linux (RHEL), CentOS, Arch**.

### Architecture (bottom to top)

```
Applications and utilities (browser, editors, ls, grep)
Shell (bash, zsh, ksh, csh)
System libraries (glibc): wrap system calls in friendly functions
Kernel: process, memory, file system, device, network management
Hardware
```

### Kernel responsibilities

Process management and scheduling, memory management, file systems (ext4, XFS, Btrfs), device drivers, networking (the full TCP/IP stack), security and access control, and the system call interface.

---

## 2. Kernel types (general classification)

| Type | Design | Pros | Cons | Examples |
|---|---|---|---|---|
| **Monolithic** | All OS services in kernel space | **Fast** (direct calls) | Big, a bug can crash everything | **Linux**, traditional UNIX |
| **Microkernel** | Only essentials in kernel; rest are user-space servers | Secure, modular, reliable | IPC overhead, slower | Mach, MINIX, QNX |
| **Hybrid** | Mix: mostly monolithic structure with some microkernel ideas | Balance | Complexity | Windows NT, macOS (XNU) |
| **Exokernel** | Kernel does minimal protection; apps manage hardware abstractions themselves | Max flexibility and performance | Apps become complex | Research systems (MIT) |

> **Trap.** Linux is **monolithic**, even though it supports **loadable kernel modules (LKMs)** that can be inserted and removed at runtime (`insmod`, `rmmod`, `modprobe`, `lsmod`). Modules run **in kernel space**, so it's still monolithic.

---

## 3. Useful Linux commands (beyond Chapter 11)

| Area | Commands |
|---|---|
| Users | `useradd`, `userdel`, `passwd`, `usermod`, `whoami`, `id`, `su`, `sudo` |
| Disk | `df -h`, `du -sh`, `mount`, `umount`, `fdisk`, `lsblk` |
| Processes | `ps aux`, `top`, `htop`, `kill`, `killall`, `pgrep`, `systemctl start/stop/status service` |
| Memory | `free -h`, `vmstat`, `cat /proc/meminfo` |
| Logs | `dmesg` (kernel messages), `journalctl` |
| Search | `find`, `locate` (uses a prebuilt database, fast), `which`, `whereis` |
| Archives | `tar -cvf a.tar dir`, `tar -xvf a.tar`, `gzip`, `gunzip`, `zip`, `unzip` |
| Network | `ip addr` (modern) or `ifconfig` (old), `ping`, `netstat`/`ss`, `ssh`, `scp`, `curl`, `wget` |
| Packages | `apt install` (Debian/Ubuntu), `yum`/`dnf install` (Red Hat/Fedora) |

`tar` flags: **c** create, **x** extract, **v** verbose, **f** file name follows, **z** gzip compression.

---

## 4. Job scheduling: cron, at, batch, anacron

Two kinds of scheduled jobs:
- **One-time:** run once, at some future moment.
- **Recurring:** run repeatedly on a schedule.

### 4.1 cron (recurring, time-based)

- **cron** is the scheduling system; **crond** is the **daemon** (background service) that wakes up every minute, reads the schedules and runs due jobs.
- Each user's schedule is their **crontab** (cron table).

Commands:
- `crontab -e` edit your crontab
- `crontab -l` list it
- `crontab -r` remove it (all your jobs!)
- `crontab -u alice -l` list another user's (root only)

#### Crontab format (5 time fields, then the command)

```
 ┌───────── minute (0-59)
 │ ┌─────── hour (0-23)
 │ │ ┌───── day of month (1-31)
 │ │ │ ┌─── month (1-12)
 │ │ │ │ ┌─ day of week (0-6, Sunday = 0 or 7)
 │ │ │ │ │
 * * * * *  command
```

Special characters:
- `*` every value
- `,` list: `9,18` = at 9 and 18
- `-` range: `9-17` = 9 through 17
- `/` step: `*/5` = every 5 units

Examples:

| Entry | Meaning |
|---|---|
| `0 2 * * * /backup.sh` | Every day at **2:00 AM** |
| `*/15 * * * * /check.sh` | Every **15 minutes** |
| `30 9 * * 1-5 /report.sh` | 9:30 AM, **Monday to Friday** |
| `0 0 1 * * /bill.sh` | Midnight on the **1st of every month** |
| `0 9,18 * * * /remind.sh` | 9 AM and 6 PM every day |
| `0 * * * * /x.sh` | At minute 0 of **every hour** |

Memory trick for field order: "**M**y **H**amster **D**rinks **M**ilk **W**eekly" (Minute, Hour, Day, Month, Weekday).

### 4.2 at (one-time, time-based)

```
at 11pm
at> /home/me/backup.sh
at> <Ctrl+D>
```

- `atq` lists pending at-jobs with IDs.
- `atrm <id>` removes one.
- Daemon: `atd`.

### 4.3 batch (one-time, load-based)

Like `at`, but instead of a fixed time, the job runs **when the system load is low enough** (load average below a threshold, typically 1.5 or 0.8 depending on the system). Good for heavy jobs that shouldn't slow down interactive users.

### 4.4 anacron (recurring, tolerant of downtime)

cron assumes the machine is **always on**. If your laptop is off at 2 AM, the daily 2 AM cron job simply **doesn't run** that day.

**anacron** remembers when each job **last ran**. When the machine boots, it checks: "the daily job hasn't run in over a day" → run it now (after an optional delay).

- Config: `/etc/anacrontab`
- Format: `period(days)  delay(minutes)  job-identifier  command`
- Example: `1  5  daily.backup  /backup.sh` → daily, 5 minutes after boot if missed.
- Granularity is **days** (not minutes).

### 4.5 The comparison table (exam favourite)

| Tool | One-time or recurring | Trigger |
|---|---|---|
| **at** | **One-time** | **Time** |
| **batch** | **One-time** | **System load** |
| **cron** | **Recurring** | **Time** (machine must be on) |
| **anacron** | **Recurring** | Time, **catches up missed jobs** after downtime |

---

## 5. Linux memory management

### 5.1 Process memory layout

```
High addresses
  Stack           (function calls, locals; grows DOWN)
  Memory mappings (shared libraries, mmap'd files)
  Heap            (malloc; grows UP)
  BSS             (uninitialised globals and statics, zero-filled)
  Data            (initialised globals and statics)
  Text            (code, read-only)
Low addresses
```

Why separate BSS from Data? Initialised data must be stored in the executable file. Uninitialised data is all zeros, so the file just records its **size**; the OS zero-fills it at load time. This makes executables smaller.

```c
int a = 5;     // Data
int b;         // BSS
static int c;  // BSS
```

### 5.2 Address translation and page tables

CPU → virtual address → **MMU** (with TLB) → physical address.

A Linux page table entry contains: **frame number**, **present/valid bit**, **protection bits** (read/write/execute, user/kernel), **accessed bit** (set on access, used for LRU approximation), **dirty bit** (set on write).

Linux uses **multi-level page tables** (4 levels on x86-64, 5 levels on newer CPUs with very large address spaces). Default page size is **4 KB**; **huge pages** (2 MB or 1 GB) reduce TLB misses for big applications like databases.

Accessing memory you don't own → hardware fault → kernel sends **SIGSEGV** → "**Segmentation fault**".

### 5.3 Demand paging and page replacement

Linux uses demand paging ([Chapter 08](08_Virtual_Memory_and_Page_Replacement.md)). For replacement it uses an **LRU approximation** with two lists: **active** and **inactive** pages (a clock-like mechanism using the accessed bit). Recent kernels add **multi-generational LRU (MGLRU)**.

Algorithms you should know conceptually (covered in [Chapter 08](08_Virtual_Memory_and_Page_Replacement.md)):

| Algorithm | Rule | Belady's anomaly? |
|---|---|---|
| FIFO | Oldest loaded | **Yes** |
| OPT | Farthest future use | No |
| LRU | Least recently used | No |
| NRU | Use (R, M) bits; evict from lowest class 00 < 01 < 10 < 11 | Not a stack algorithm in general; not typically exam-tested for anomaly |
| Clock / Second chance | FIFO + reference bit | Can in principle (it's FIFO-based), but it's generally much better behaved |

### 5.4 Swap and swappiness

When RAM is short, Linux moves inactive anonymous pages (heap, stack) to **swap** space on disk.

- **Swap partition:** a dedicated partition. Fixed size; slightly faster; needs partitioning.
- **Swap file:** an ordinary file used as swap. Easy to create and resize.

**Swappiness** (`/proc/sys/vm/swappiness`, default **60**, range 0 to 200 in modern kernels) tells the kernel how willing it is to swap out process memory versus dropping file cache.
- **High** value: swap more eagerly.
- **Low** value: prefer reclaiming page cache; avoid swapping.
- It is a **relative weight**, not "percentage of RAM".

### 5.5 OOM killer

If memory is completely exhausted and nothing can be reclaimed, the **Out-Of-Memory (OOM) killer** picks a process (based on an "OOM score", favouring big memory users) and kills it to free memory.

---

## 6. Linux kernel memory allocators

User programs use `malloc`. But the **kernel itself** needs memory for its own structures (PCBs, inodes, network buffers). It uses special allocators.

### 6.1 Buddy allocator (physical pages)

Manages physical memory in blocks of **2^k pages**.

Allocation:
1. Round the request **up to the next power of 2**.
2. If no free block of that size, **split** a larger block into two equal halves (**buddies**), repeatedly, until you get the right size.

Freeing:
- When a block is freed, check its buddy. If the buddy is also free, **merge (coalesce)** them into the larger block. Repeat upward.

**Worked example:** a 256 KB region; request for **21 KB**.
- Round up to 32 KB.
- Split 256 → 128 + 128. Split 128 → 64 + 64. Split 64 → 32 + 32.
- Allocate one 32 KB block.
- Internal fragmentation = 32 − 21 = **11 KB**.

Pros: fast splitting and **fast coalescing** (a block's buddy address is found by flipping one bit of its address).
Cons: **internal fragmentation** (rounding up to powers of 2; a 33 KB request wastes 31 KB of a 64 KB block).

### 6.2 SLAB allocator (kernel objects)

The kernel repeatedly allocates and frees objects of the **same type and size** (e.g. `task_struct`, inodes). SLAB keeps **caches** of pre-initialised objects:

- A **cache** per object type.
- Each cache has **slabs** (one or more contiguous pages) divided into object-sized slots.
- Slabs are **full, partial or empty**.
- Allocation grabs a free slot from a partial slab; free returns it. No fragmentation **within** a type, and very fast because objects can be reused without re-initialising.

### 6.3 SLUB and SLOB

- **SLUB:** a simpler, more scalable redesign of SLAB with less metadata and better multi-core performance. **The default in modern Linux.** (SLAB itself was removed in Linux 6.8.)
- **SLOB:** "Simple List Of Blocks", a tiny first-fit allocator for very small embedded systems. Low overhead, slow, fragments. **Removed** from the kernel (Linux 6.4).

| Allocator | Used for | Key idea | Status |
|---|---|---|---|
| Buddy | Physical page blocks | Power-of-2 split and merge | Core, always present |
| SLAB | Kernel objects | Per-type caches of slabs | Historical (removed 6.8) |
| SLUB | Kernel objects | Simplified SLAB | **Current default** |
| SLOB | Tiny systems | First-fit list | Removed |

---

## 7. Linux process scheduling (bonus)

- Linux schedules **tasks** (processes and threads are both tasks).
- The **Completely Fair Scheduler (CFS)** (2007 to 2023) gave each task a fair share of CPU by tracking **virtual runtime** in a red-black tree and always running the task with the smallest vruntime. Linux 6.6 replaced it with **EEVDF** (Earliest Eligible Virtual Deadline First), which follows the same fairness idea.
- **Nice values** range from **−20 (highest priority) to +19 (lowest)**. Default 0.
- Real-time policies `SCHED_FIFO` and `SCHED_RR` always beat normal tasks.

---

## 8. Exam traps

1. Linux kernel is **monolithic** (with loadable modules).
2. `at` = one-time, time. `batch` = one-time, load. `cron` = recurring, time. `anacron` = recurring, catches up missed runs.
3. Crontab field order: **minute hour day-of-month month day-of-week**.
4. `*/15` in the minute field = every 15 minutes.
5. crond is the daemon; crontab is the table.
6. Buddy allocator: power-of-2 blocks, **internal fragmentation**, fast merge.
7. SLUB is the current default slab allocator.
8. Swappiness is a weight (default 60), not a percentage.
9. Nice −20 = highest priority.
10. BSS = uninitialised globals/statics.

---

## 9. Practice questions

**Q1.** Which crontab entry runs a job at 6:30 PM every Sunday?
(a) `30 18 * * 0` (b) `18 30 * * 0` (c) `30 6 * * 7` (d) `0 18 30 * *`

**Answer: (a).** Minute 30, hour 18, any day of month, any month, day of week 0 (Sunday).

---

**Q2.** `0 */2 * * *` runs a job:
(a) every 2 minutes (b) every 2 hours, at minute 0 (c) twice a day (d) on the 2nd of each month

**Answer: (b).**

---

**Q3.** Which tool runs a one-time job when the system load drops below a threshold?
(a) cron (b) at (c) batch (d) anacron

**Answer: (c).**

---

**Q4.** Linux's kernel is best described as:
(a) Microkernel (b) Monolithic with loadable modules (c) Exokernel (d) Pure hybrid

**Answer: (b).**

---

**Q5.** In the buddy system with a 1 MB block, a request for 100 KB gets a block of size:
(a) 100 KB (b) 128 KB (c) 256 KB (d) 64 KB

**Answer: (b).** Next power of 2 ≥ 100 is 128.

---

**Q6.** In Q5, how much internal fragmentation results?
(a) 0 KB (b) 28 KB (c) 156 KB (d) 24 KB

**Answer: (b).** 128 − 100 = 28 KB.

---

**Q7.** In Q5, starting from one free 1 MB block, how many splits are needed to produce the 128 KB block?
(a) 2 (b) 3 (c) 4 (d) 8

**Answer: (b).** 1 MB → 512 → 256 → 128. Three splits.

---

**Q8.** An uninitialised global variable `int counter;` lives in which segment?
(a) Text (b) Data (c) BSS (d) Stack

**Answer: (c).**

---

**Q9.** Which nice value gives a process the highest priority?
(a) 19 (b) 0 (c) −20 (d) 20

**Answer: (c).**

---

**Q10.** A laptop is usually off at night, but a daily maintenance task must still run every day. Best tool?
(a) cron (b) at (c) anacron (d) batch

**Answer: (c).**

---

**Q11.** Which command lists your pending `at` jobs?
(a) at -l or atq (b) crontab -l (c) jobs (d) ps

**Answer: (a).** (`at -l` is an alias for `atq`.)

---

**Q12.** Which allocator is designed for frequently allocated kernel objects of identical size?
(a) Buddy (b) SLAB/SLUB (c) First-fit (d) Paging

**Answer: (b).**

---

**Q13.** Accessing an address outside your process's valid memory on Linux results in:
(a) page fault that is always serviced (b) SIGSEGV (segmentation fault) (c) SIGKILL (d) a new page being allocated

**Answer: (b).**

---

**Q14.** `tar -czvf backup.tar.gz /data` does what?
(a) extracts a gzip archive (b) creates a gzip-compressed archive of /data, verbosely (c) lists the archive (d) deletes /data

**Answer: (b).** c = create, z = gzip, v = verbose, f = file name.

---

**Practice questions:** [5.12 Linux Scheduling and Memory](../ISRO_CS_Question_Bank/05_Operating_Systems/5.12_Linux_Scheduling_and_Memory.md)
