# 10. File Management: Allocation, Free Space, Directories and RAID

> **The question this chapter answers.** A disk is just a long array of numbered blocks. A "file" is a human idea. How does the OS turn "my_report.pdf" into "blocks 9182, 9183, 20007 and 51"? And how does it keep track of which blocks are free?

---

## 1. What is a file?

A **file** is a named collection of related information stored on secondary storage. To the user, it's the smallest unit of storage.

### File attributes (stored in the file's metadata)

Name, identifier (a unique number like an inode number), type, location (pointers to blocks), size, protection (who can read/write/execute), timestamps (created, modified, accessed), owner.

### File operations (all are system calls)

Create, write, read, reposition (seek), delete, truncate, open, close.

**Why `open()`?** Searching the directory for a file every time you read it would be slow. `open()` searches once, copies the file's metadata into an in-memory **open-file table**, and returns a small integer (a **file descriptor** / handle). Later reads use that handle directly.

---

## 2. File access methods

| Method | How it works | Analogy | Good for |
|---|---|---|---|
| **Sequential** | Read/write records in order; a pointer advances automatically | A cassette tape | Editors, compilers, streaming |
| **Direct (relative)** | Jump to any block by number: `read(n)` | A CD: jump to track 7 | Databases, random lookups |
| **Indexed sequential** | An index points into a sequential file; look up the index, then jump | A book's index | Large record files with key search **and** ordered scans |

---

## 3. Directory structures

A **directory** maps file names to file metadata. How should directories be organised?

### 3.1 Single-level directory

All files of all users in **one** directory.
- Simple.
- **Naming collisions**: two users can't both have `test.c`. Hard to manage thousands of files.

### 3.2 Two-level directory

A **Master File Directory (MFD)** with one entry per user, pointing to that user's **User File Directory (UFD)**.
- Solves cross-user name collisions.
- Users are **isolated** (sharing is awkward), and **users can't create sub-directories** to organise their own files.

### 3.3 Tree-structured directory

The familiar hierarchy of folders inside folders. **Most common today.**

- **Absolute path:** from the root, e.g. `/home/armaan/notes/os.md`.
- **Relative path:** from the current working directory, e.g. `notes/os.md`.
- Each directory entry has a bit telling whether it's a **file (0)** or a **subdirectory (1)**.

Limitation: a file belongs to exactly one directory, so **no sharing** of a file across directories.

### 3.4 Acyclic-graph directory

Allows a file or subdirectory to appear in **multiple directories** via **links** (shared files), but **no cycles**.

Problems:
- A file may now have multiple path names (aliases), so traversals might count it twice.
- **Deletion:** if one user deletes a shared file, the others' links dangle. Fix: keep a **reference count** (number of links); actually delete the file only when the count reaches **0**. (UNIX hard links work this way.)

### 3.5 General graph directory

Links may create **cycles**.
- Traversal algorithms can loop **forever**.
- A self-referencing cycle can keep a reference count above 0 even after it's unreachable, so space is never freed.
- Fixes: **garbage collection** (expensive), checking for cycles on every new link (expensive), or **allowing links only to files, not directories** (which guarantees no cycles).

| Structure | Sharing | Sub-dirs | Cycles | Main problem |
|---|---|---|---|---|
| Single-level | n/a | No | No | Name collisions |
| Two-level | Hard | No | No | No sub-directories |
| Tree | No | Yes | No | No sharing |
| Acyclic graph | Yes | Yes | No | Deletion (dangling links) |
| General graph | Yes | Yes | **Yes** | Infinite loops, garbage |

---

## 4. File allocation methods

The core question: **which disk blocks does each file use, and how do we find them?**

### 4.1 Contiguous allocation

Each file occupies a **set of consecutive blocks**. The directory stores (start block, length).

```
Directory:  file "mail" -> start 19, length 6   => blocks 19, 20, 21, 22, 23, 24
```

- **Fast** sequential **and** direct access: block i is at `start + i`. Minimal head movement.
- **External fragmentation** (same problem as contiguous memory allocation).
- **Hard to grow** a file: the next block may already belong to someone else. You must guess the size in advance.

### 4.2 Linked allocation

Each file is a **linked list of blocks**, scattered anywhere. Each block contains a pointer to the next. The directory stores the first (and maybe last) block.

```
start = 9:   9 -> 16 -> 1 -> 10 -> 25 -> null
```

- **No external fragmentation.** Any free block works. Files grow easily.
- **Direct access is terrible**: to reach block i, you must read blocks 0 to i−1 first (i disk reads).
- **Space for pointers** in every block (e.g. 4 bytes of each 512-byte block).
- **Reliability**: one corrupted pointer loses the rest of the file.

Improvement: **clusters** (group several blocks), so fewer pointers.

#### FAT (File Allocation Table)

A clever variant used by MS-DOS and USB drives. Pull all the "next" pointers out of the blocks and into **one table at the start of the disk**, with one entry per block.

```
FAT index:  ...  217  ...  339  ...  618
FAT entry:  ...  618  ...  EOF  ...  339
Directory: "test" starts at 217  => 217 -> 618 -> 339 -> EOF
```

- If the FAT is cached in memory, you can follow the chain **in memory** and then jump directly to block i. Direct access becomes fast.
- Free blocks are marked with 0 in the FAT.

### 4.3 Indexed allocation

Bring all pointers for one file together into one **index block**. The i-th entry of the index block points to the i-th data block. The directory stores the index block's address.

```
index block 19:  [9, 16, 1, 10, 25, -1, -1, ...]
```

- **Direct access** without external fragmentation.
- **Overhead**: every file needs a whole index block, even a 1-block file. Wasteful for small files.
- What if a file needs more pointers than fit in one index block? Options:
  - **Linked scheme:** chain several index blocks.
  - **Multilevel index:** a first-level index block points to second-level index blocks, which point to data.
  - **Combined scheme (UNIX inode):** see below.

### 4.4 The UNIX inode (combined scheme)

Each file has an **inode** containing metadata plus, typically, **15 pointers**:

- **12 direct** pointers → data blocks.
- **1 single indirect** → a block full of pointers to data blocks.
- **1 double indirect** → a block of pointers to blocks of pointers to data.
- **1 triple indirect** → three levels.

Small files (most files!) use only the direct pointers: **fast**. Huge files can still be represented.

#### Maximum file size calculation (always asked)

Let block size = B, pointer size = p. Pointers per block: **k = B / p**.

```
Max blocks   = (#direct) + k + k² + k³
Max file size = Max blocks × B
```

**Example:** B = 4 KB, p = 4 bytes → k = 1024. 12 direct.
- Direct: 12 × 4 KB = 48 KB
- Single: 1024 × 4 KB = 4 MB
- Double: 1024² × 4 KB = 4 GB
- Triple: 1024³ × 4 KB = 4 TB
- **Max ≈ 4 TB + 4 GB + 4 MB + 48 KB**, roughly **4 TB**.

**Example:** B = 1 KB, p = 4 bytes → k = 256. 10 direct, 1 single, 1 double (no triple).
Max = (10 + 256 + 65,536) × 1 KB = **65,802 KB** (about 64.26 MB).

### 4.5 Comparison

| Method | Sequential access | Direct access | External frag. | Growth | Overhead |
|---|---|---|---|---|---|
| Contiguous | Excellent | Excellent | **Yes** | Hard | None |
| Linked | Good | **Poor** | No | Easy | Pointer in each block |
| FAT | Good | Good (FAT cached) | No | Easy | The FAT itself |
| Indexed | Good | Good | No | Easy | Index block per file |

---

## 5. Free-space management

The OS must also track **free** blocks.

### 5.1 Bit vector (bitmap)

One bit per block: **1 = free, 0 = allocated** (some books use the opposite; read the question).

```
blocks:   0 1 2 3 4 5 6 7 8 ...
bits:     0 0 1 1 1 1 0 0 1 ...
```

- Simple; easy to find the first free block or **n consecutive free blocks** (good for contiguous allocation). CPUs have instructions to find the first 1 bit in a word quickly.
- Must be in memory to be fast; big disks need big bitmaps.

**Size:** `bitmap bytes = number of blocks / 8`.

- 1 TB disk, 4 KB blocks → 2⁴⁰ / 2¹² = 2²⁸ blocks → 2²⁸ bits = 2²⁵ bytes = **32 MB**.
- 800,000 blocks → 100,000 bytes.

### 5.2 Linked list

Link all free blocks together; keep a pointer to the first one in memory.
- No extra space (pointers live inside free blocks).
- Traversing the list is slow (a disk read per block), but we rarely traverse; we usually just need **one** free block, and that's the head.

### 5.3 Grouping

The first free block stores the addresses of n free blocks; the last of those stores the next n, and so on. Finds many free blocks quickly.

### 5.4 Counting

Free blocks often come in runs. Store (first free block, count of consecutive free blocks) pairs. Compact when runs are long.

---

## 6. File system implementation

### On-disk structures

- **Boot control block:** info needed to boot the OS from this volume (first block).
- **Volume control block (superblock in UNIX):** number of blocks, block size, free-block count and pointers, free-FCB count.
- **Directory structure.**
- **File Control Block (FCB) / inode** per file: permissions, owner, size, timestamps, data block pointers.

### In-memory structures

- **Mount table:** which file systems are mounted where.
- **System-wide open-file table:** one entry (copy of FCB) per open file.
- **Per-process open-file table:** pointers into the system-wide table, plus the current file position for that process.
- Directory cache.

### Layered file system (bottom to top)

1. **I/O control:** device drivers, interrupt handlers.
2. **Basic file system:** issues generic commands like "read physical block 123"; manages buffers and caches.
3. **File-organization module:** translates logical block numbers (0, 1, 2 of the file) into physical blocks; manages free space.
4. **Logical file system:** manages metadata (FCBs, directory structure, protection). Everything except the actual data.

### Directory implementation

- **Linear list** of (name, pointer): simple, but every lookup is a **linear search**, O(n).
- **Hash table**: hash the file name to find the entry. **O(1)** average. Need to handle collisions and fixed table size.

### Mounting

Before use, a file system must be **mounted** at a **mount point** (usually an empty directory). E.g. plugging in a USB drive and mounting it at `/media/usb`. After that, its files appear as part of the single directory tree.

### Protection and access rights

Graded access types: **None, Knowledge (know it exists), Execute, Read, Append, Update, Change protection, Delete**.

UNIX uses **owner / group / others** × **read / write / execute** bits (`rwxr-x---` = 750).

---

## 7. Mass storage and RAID

### Storage types

| | HDD | SSD | Magnetic tape |
|---|---|---|---|
| Moving parts | Yes | **No** | Yes |
| Seek/rotational latency | Yes | **None** | Very slow (sequential) |
| Cost per GB | Low | Higher | **Lowest** |
| Notes | Bulk storage | Fast; limited write cycles | **Backup, archival** |

### Disk attachment

- **Host-attached:** directly via a local bus (SATA, SCSI, Fibre Channel).
- **NAS (Network-Attached Storage):** storage accessed over a normal network at the **file** level (NFS, CIFS/SMB).
- **SAN (Storage Area Network):** a private high-speed network giving servers **block-level** access to storage.

### RAID (Redundant Array of Independent Disks)

Combine multiple disks for **performance** (striping) and/or **reliability** (redundancy).

Two basic techniques:
- **Striping:** spread data across disks so reads/writes happen in parallel. Speed.
- **Mirroring / parity:** store extra information so data survives a disk failure. Safety.

| Level | Technique | Fault tolerance | Usable capacity (n disks of size D) | Notes |
|---|---|---|---|---|
| **RAID 0** | Block striping, **no redundancy** | **None** (any failure loses everything) | nD | Fastest; "RAID" in name only |
| **RAID 1** | **Mirroring** | 1 disk (per mirror pair) | nD / 2 | Great reads, writes go to both, 50% efficiency |
| RAID 2 | Bit-level striping + Hamming code | 1 | Less | Obsolete |
| RAID 3 | Byte/bit striping + dedicated parity | 1 | (n−1)D | Rare |
| **RAID 4** | Block striping + **one dedicated parity disk** | 1 | (n−1)D | Parity disk is a **write bottleneck** |
| **RAID 5** | Block striping + **distributed parity** | **1** | (n−1)D | Most popular balance |
| **RAID 6** | Like RAID 5 with **two** independent parities (P + Q) | **2** | (n−2)D | Safer for big arrays |
| RAID 10 (1+0) | Mirrored pairs, then striped | 1 per pair | nD / 2 | Fast and reliable, costly |

How parity works: parity block = XOR of the data blocks in the stripe. If one disk fails, XOR the remaining blocks to rebuild the lost one.

Example: data blocks 1011, 0110, 1100. Parity = 1011 ⊕ 0110 ⊕ 1100 = 0001. If the second block is lost: 1011 ⊕ 1100 ⊕ 0001 = 0110. Recovered.

> **Trap.** RAID 0 gives **zero** fault tolerance. And no RAID level protects against **silent data corruption**, accidental deletion, or viruses. RAID is not a backup. File systems like **ZFS** add checksums to detect corruption.

---

## 8. Exam traps

1. Contiguous: fast but **external fragmentation**.
2. Linked: **no direct access**; one bad pointer breaks the chain.
3. FAT = linked allocation with pointers moved to a table; direct access becomes feasible.
4. Indexed: direct access, but **index block overhead for small files**.
5. Inode max size = (direct + k + k² + k³) × B, with k = B/p.
6. Bitmap size = blocks / 8 bytes.
7. Two-level directory: no sub-directories. Acyclic graph: deletion needs reference counts. General graph: cycles need garbage collection.
8. RAID 0: no redundancy. RAID 5: distributed parity, 1 failure. RAID 6: 2 failures. RAID 4: parity bottleneck.
9. Hash-table directories: O(1) lookup.

---

## 9. Practice questions

**Q1.** A file system uses 4 KB blocks and 4-byte pointers. An inode has 10 direct, 1 single indirect and 1 double indirect pointer. Maximum file size is approximately:
(a) 4 MB (b) 4 GB (c) 40 KB (d) 4 TB

**Answer: (b).** k = 1024. (10 + 1024 + 1024²) × 4 KB ≈ 40 KB + 4 MB + 4 GB ≈ 4 GB.

---

**Q2.** Block size 1 KB, pointer 4 bytes, inode with 8 direct pointers and 1 single indirect pointer. Maximum file size?
(a) 264 KB (b) 256 KB (c) 8 KB (d) 1 MB

**Answer: (a).** k = 256. (8 + 256) × 1 KB = 264 KB.

---

**Q3.** A disk has 2²⁰ blocks. Size of the free-space bitmap?
(a) 128 KB (b) 1 MB (c) 64 KB (d) 256 KB

**Answer: (a).** 2²⁰ bits = 2¹⁷ bytes = 128 KB.

---

**Q4.** Which allocation method gives the best performance for both sequential and direct access but suffers from external fragmentation?
(a) Linked (b) Indexed (c) Contiguous (d) FAT

**Answer: (c).**

---

**Q5.** To read the 50th block of a file under linked allocation (directory knows the first block), how many disk reads are needed (no caching)?
(a) 1 (b) 49 (c) 50 (d) 51

**Answer: (c).** You must read blocks 1 through 49 to follow pointers, then block 50: 50 reads.

---

**Q6.** Same file with indexed allocation, index block not in memory. Disk reads to get block 50?
(a) 1 (b) 2 (c) 50 (d) 51

**Answer: (b).** One read for the index block, one for the data block.

---

**Q7.** Same with contiguous allocation (start block known)?
(a) 1 (b) 2 (c) 50 (d) 0

**Answer: (a).** Compute start + 49 and read it.

---

**Q8.** 6 disks of 2 TB each in RAID 5. Usable capacity?
(a) 12 TB (b) 10 TB (c) 8 TB (d) 6 TB

**Answer: (b).** (6 − 1) × 2 = 10 TB.

---

**Q9.** Same 6 disks in RAID 6?
(a) 10 TB (b) 8 TB (c) 6 TB (d) 12 TB

**Answer: (b).** (6 − 2) × 2 = 8 TB.

---

**Q10.** Which RAID level has a single parity disk that becomes a write bottleneck?
(a) RAID 1 (b) RAID 4 (c) RAID 5 (d) RAID 6

**Answer: (b).**

---

**Q11.** In an acyclic-graph directory, a shared file should be physically deleted when:
(a) any user deletes it (b) the owner deletes it (c) its reference (link) count becomes 0 (d) never

**Answer: (c).**

---

**Q12.** Which free-space technique stores (start, count) pairs?
(a) Bitmap (b) Linked list (c) Grouping (d) Counting

**Answer: (d).**

---

**Q13.** FAT is an example of which allocation method?
(a) Contiguous (b) Linked (c) Indexed (d) Hashed

**Answer: (b).** It's linked allocation with the links stored in a table.

---

**Q14.** Which file system layer translates logical block addresses to physical block addresses?
(a) I/O control (b) Basic file system (c) File-organization module (d) Logical file system

**Answer: (c).**

---

**Q15.** RAID parity: data blocks 1010 and 0110 on two disks; parity = their XOR. If the disk holding 0110 fails, the reconstructed value is:
(a) 1100 ⊕ 1010 = 0110 (b) 1111 (c) 0000 (d) 1010

**Answer: (a).** Parity = 1010 ⊕ 0110 = 1100. Rebuild = 1010 ⊕ 1100 = 0110.
