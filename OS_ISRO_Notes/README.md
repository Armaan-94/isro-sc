# Operating Systems: Deep, Beginner-Friendly Notes

Read these in order. Each chapter starts from intuition, builds the concept step by step, works through examples by hand, lists the traps examiners love, and ends with practice questions (with full explanations).

| # | Chapter | What you'll be able to do after it |
|---|---|---|
| 01 | [Introduction to OS](01_Introduction_to_OS.md) | Explain OS types, structures, dual mode, system calls |
| 02 | [Process Management](02_Process_Management.md) | Trace process states, PCB, schedulers, context switches |
| 03 | [CPU Scheduling Algorithms](03_CPU_Scheduling_Algorithms.md) | Solve any Gantt-chart numerical (FCFS, SJF, SRTF, RR, Priority, HRRN) |
| 04 | [Process Synchronization](04_Process_Synchronization.md) | Reason about race conditions, Peterson's, semaphores, classical problems |
| 05 | [Threads and Multithreading](05_Threads_and_Multithreading.md) | Count `fork()` processes, compare threading models |
| 06 | [Deadlock](06_Deadlock.md) | Run Banker's algorithm, apply the n(k−1)+1 rule, read RAGs |
| 07 | [Memory Management](07_Memory_Management.md) | Do paging/TLB/multilevel numericals, segmentation, fit strategies |
| 08 | [Virtual Memory and Page Replacement](08_Virtual_Memory_and_Page_Replacement.md) | Count page faults (FIFO/LRU/OPT), EAT, thrashing |
| 09 | [Disk Scheduling](09_Disk_Scheduling.md) | Compute head movement for all six algorithms |
| 10 | [File Management and RAID](10_File_Management_and_RAID.md) | Inode max-size, bitmap size, allocation methods, RAID levels |
| 11 | [UNIX and Shell Scripting](11_Unix_OS_and_Shell_Scripting.md) | Permissions in octal, commands, shell syntax traps |
| 12 | [Linux OS](12_Linux_OS.md) | cron/at/batch/anacron, buddy allocator, SLAB/SLUB |
| 13 | [Distributed Systems](13_Distributed_Systems.md) | Message counts for mutual exclusion, Lamport clocks, 2PC, Byzantine |

**How to study:** read a chapter once fully, then attempt its practice questions **without** looking at answers. Re-read only the sections where you went wrong. Do one numerical-heavy chapter (03, 06, 07, 08, 09) per day in the last week.
