# 11. UNIX: Architecture, File System, Commands and Shell Scripting

> **Why study UNIX in an OS exam?** Because UNIX is the ancestor of Linux, macOS, Android and most of the servers on Earth. Its ideas (everything is a file, small tools combined with pipes, permissions) show up everywhere. ISRO asks direct questions on commands, permissions and shell syntax.

---

## 1. A little history and the key features

UNIX was developed at **AT&T Bell Labs** (late 1960s and 1970s) by **Ken Thompson and Dennis Ritchie**. Ritchie created the **C language** largely to rewrite UNIX in it, which made UNIX **portable** (it could be moved to new hardware by recompiling, instead of rewriting assembly).

Key features:
- **Multi-user:** many users at once, each with their own account.
- **Multitasking:** many processes at once.
- **Portable:** written mostly in C.
- **Hierarchical file system** starting from the root `/`.
- **Security** via permissions (read/write/execute for owner/group/others).
- **Powerful shell** for scripting and automation.
- **Philosophy:** many small tools that each do one thing well, combined with **pipes**.

---

## 2. Architecture

Think of it as layers of an onion:

```
+--------------------------------------------------+
| Layer 4: Applications (browsers, databases)      |
+--------------------------------------------------+
| Layer 3: System programs & utilities             |
|          (shell, compilers, editors, grep, wc)   |
+--------------------------------------------------+
| Layer 2: KERNEL                                  |
|   process mgmt | memory mgmt | file system | I/O |
+--------------------------------------------------+
| Layer 1: Hardware                                |
+--------------------------------------------------+
```

Inside, the kernel is reached through the **system call interface**:

```
User programs
     | (library calls, e.g. printf)
System call & library interface   <- trap into kernel
     |
Kernel: File subsystem  (open, read, write, close, chmod, chown)
        Buffer cache / block I/O
        Device drivers (character and block)
        Process control subsystem (scheduling, memory, IPC)
     |
Hardware (raises interrupts handled by the kernel)
```

The **kernel** is the core that's always resident in memory. The **shell** is just a user program that talks to the kernel; it is **not** part of the kernel.

---

## 3. The UNIX file system

### 3.1 "Everything is a file"

In UNIX, almost everything looks like a file: regular documents, directories, devices (your disk is `/dev/sda`, the terminal is `/dev/tty`), pipes and sockets. This means the same calls (`open`, `read`, `write`, `close`) work for all of them. Elegant.

### 3.2 Inodes

Every file has an **inode** (index node) holding its metadata: type, permissions, owner, group, size, timestamps, link count, and pointers to data blocks. **The file name is NOT in the inode.** Names live in **directories**, which are just tables mapping names to inode numbers.

### 3.3 File types and their `ls -l` symbol

| Symbol | Type | Example |
|---|---|---|
| `-` | Regular file | `notes.txt`, `a.out` |
| `d` | Directory | `/home` |
| `l` | Symbolic link | shortcut |
| `c` | Character special (byte at a time) | keyboard, terminal `/dev/tty` |
| `b` | Block special (fixed-size blocks) | disk `/dev/sda` |
| `p` | Named pipe (FIFO) | for one-way IPC |
| `s` | Socket | client-server IPC |

### 3.4 Hard links vs symbolic (soft) links

| | Hard link | Symbolic (soft) link |
|---|---|---|
| What it is | Another **directory entry** pointing to the **same inode** | A small file containing a **path** to the target |
| Command | `ln target linkname` | `ln -s target linkname` |
| Inode | Same as the original | Its own, different inode |
| If original name is deleted | File **still exists** (link count > 0) | Link becomes **broken (dangling)** |
| Across file systems? | **No** | Yes |
| To directories? | Usually **not allowed** | Allowed |

Deleting a symbolic link never affects the target. Deleting the target breaks the symlink.

### 3.5 Important directories

`/` root, `/bin` essential binaries, `/etc` configuration files, `/home` user home directories, `/dev` device files, `/tmp` temporary files, `/var` variable data (logs), `/usr` user programs and libraries, `/root` the superuser's home.

---

## 4. File permissions

### 4.1 Reading a permission string

```
-rwxr-xr--
| |  |  |
| |  |  +-- others: r-- (read only)
| |  +----- group:  r-x (read, execute)
| +-------- owner:  rwx (read, write, execute)
+---------- file type: - (regular)
```

For **directories**, the meanings shift:
- `r`: list the names in the directory,
- `w`: create/delete files in it,
- `x`: enter it (`cd`) and access files inside.

### 4.2 Octal notation

Each permission triplet is a 3-bit number: r = 4, w = 2, x = 1.

| Symbolic | Octal |
|---|---|
| `rwx` | 4+2+1 = 7 |
| `rw-` | 6 |
| `r-x` | 5 |
| `r--` | 4 |
| `---` | 0 |

So `rwxr-xr--` = **754**. `chmod 644 file` gives `rw-r--r--`. `chmod 755 script.sh` gives `rwxr-xr-x`.

Symbolic form: `chmod u+x file` (add execute for the owner), `chmod go-w file` (remove write from group and others), `chmod a=r file` (everyone read only).

### 4.3 `umask`

The **umask** removes permissions from newly created files. Default creation mode is 666 for files and 777 for directories; the actual mode = default AND NOT umask (for typical values, just subtract digit by digit).

Example: umask **022** → new files get 666 − 022 = **644** (`rw-r--r--`), new directories get 777 − 022 = **755**.

### 4.4 Special bits (brief)

- **setuid (4xxx):** a program runs with the **owner's** privileges (e.g. `passwd` runs as root so you can update `/etc/shadow`). Shows as `s` in the owner's x position.
- **setgid (2xxx):** runs with the group's privileges; on directories, new files inherit the directory's group.
- **Sticky bit (1xxx):** on a directory (like `/tmp`), users can delete only their **own** files. Shows as `t`.

---

## 5. Essential commands

### Navigation and files

| Command | Does |
|---|---|
| `pwd` | Print working directory |
| `ls`, `ls -l`, `ls -a` | List files; long format; include hidden (dot) files |
| `cd dir`, `cd ..`, `cd ~` | Change directory; parent; home |
| `mkdir`, `rmdir` | Make / remove (empty) directory |
| `cp src dst`, `mv src dst` | Copy / move or rename |
| `rm file`, `rm -r dir` | Remove file / directory recursively |
| `touch file` | Create empty file or update timestamp |
| `cat file` | Print file (con**cat**enate) |
| `more`, `less` | Page through a file |
| `head -n 5`, `tail -n 5` | First / last 5 lines |
| `ln`, `ln -s` | Hard link / symbolic link |
| `find / -name "*.c"` | Search for files by name |

### Text processing

| Command | Does |
|---|---|
| `grep pattern file` | Lines matching a pattern (`-i` ignore case, `-v` invert, `-c` count, `-n` line numbers) |
| `wc file` | Count lines, words, characters (`-l`, `-w`, `-c`) |
| `sort`, `uniq` | Sort lines; remove **adjacent** duplicates |
| `cut -d: -f1` | Cut fields |
| `sed 's/old/new/g'` | Stream editor (find and replace) |
| `awk '{print $1}'` | Pattern scanning and field processing |
| `tr 'a-z' 'A-Z'` | Translate characters |
| `diff f1 f2` | Show differences |

### Processes

| Command | Does |
|---|---|
| `ps`, `ps -ef` | List processes |
| `top` | Live view of processes |
| `kill PID`, `kill -9 PID` | Send SIGTERM / SIGKILL (cannot be caught) |
| `nice -n 10 cmd`, `renice` | Start with / change priority (higher nice = lower priority) |
| `cmd &` | Run in background |
| `jobs`, `bg`, `fg` | List jobs; resume in background; bring to foreground |
| `nohup cmd &` | Keep running after logout |

### System and users

`who` (who is logged in), `whoami`, `df` (disk free per file system), `du` (disk usage of files/dirs), `uname -a` (system info), `date`, `cal`, `man cmd` (manual), `chown user file` (change owner), `chgrp`.

### Networking

`ping`, `netstat`, `ssh user@host`, `scp file user@host:path`, `ftp`, `telnet`.

---

## 6. The shell

The **shell** is a command-line interpreter. It reads a command, interprets it (expanding variables, wildcards), creates processes via `fork()` and `exec()`, and shows the output.

Common shells: **sh** (Bourne), **bash** (Bourne Again, default on most Linux), **csh** (C shell), **ksh** (Korn), **zsh**.

### 6.1 Wildcards (globbing)

- `*` matches any string (including empty): `*.c`
- `?` matches exactly one character: `file?.txt`
- `[abc]` matches one of a, b, c; `[0-9]` a digit.

### 6.2 I/O redirection and pipes

| Symbol | Meaning |
|---|---|
| `>` | Redirect output to a file (**overwrite**) |
| `>>` | Redirect output, **append** |
| `<` | Take input from a file |
| `2>` | Redirect **error** output (file descriptor 2) |
| `2>&1` | Send errors to the same place as output |
| `|` | **Pipe**: output of one command becomes input of the next |

File descriptors: **0 = stdin, 1 = stdout, 2 = stderr**.

Example: count how many users are logged in: `who | wc -l`.
Example: the 3 biggest files: `ls -l | sort -k5 -n | tail -3`.

---

## 7. Shell scripting

A **shell script** is a text file of commands. First line is the **shebang**:

```bash
#!/bin/bash
echo "Hello, $USER"
```

Make it executable with `chmod +x script.sh` and run with `./script.sh`.

### 7.1 Variables

```bash
name="Armaan"      # NO spaces around =
echo $name         # use with $
echo "${name}s"    # braces when followed by other text
readonly PI=3.14   # cannot be changed
```

- `name = "Armaan"` (with spaces) is an **error**: the shell thinks `name` is a command.
- Variables are untyped strings; case-sensitive.
- **Environment variables** (`PATH`, `HOME`, `USER`) are inherited by child processes; use `export` to make your own variable an environment variable.

### 7.2 Special variables

| Variable | Meaning |
|---|---|
| `$0` | Script name |
| `$1`, `$2`, ... | Positional arguments |
| `$#` | **Number** of arguments |
| `$*` | All arguments as **one string** |
| `$@` | All arguments as **separate words** |
| `$?` | **Exit status** of the last command (0 = success) |
| `$$` | PID of the current shell |
| `$!` | PID of the last background process |

Example: `./run.sh a b c` → `$0` = ./run.sh, `$1` = a, `$#` = 3.

### 7.3 Input

```bash
read name          # read a line into name
read -s password   # silent (for passwords)
read -p "Age: " age
```

### 7.4 Arithmetic

```bash
a=5; b=3
sum=`expr $a + $b`        # old style: spaces REQUIRED, * must be escaped: expr $a \* $b
sum=$((a + b))            # modern, no escaping
prod=$((a * b))
```

### 7.5 Test operators

| Kind | Operators |
|---|---|
| Numeric | `-eq -ne -gt -lt -ge -le` |
| String | `=` `!=` `-z` (empty) `-n` (not empty) |
| File | `-e` exists, `-f` regular file, `-d` directory, `-r -w -x` permissions, `-s` non-empty |
| Logical | `&&` `||` `!` (and `-a`, `-o` inside `[ ]`) |

> **Trap.** Inside `[ ]`, use `-gt` for numbers, not `>`. In `[ $a > $b ]`, `>` is treated as **output redirection** and creates a file named after `$b`!

### 7.6 Control flow

```bash
if [ $x -gt 10 ]; then
    echo "big"
elif [ $x -eq 10 ]; then
    echo "ten"
else
    echo "small"
fi

case $choice in
    1) echo "one" ;;
    2|3) echo "two or three" ;;
    *) echo "other" ;;
esac

for f in *.txt; do echo $f; done

i=1
while [ $i -le 5 ]; do echo $i; i=$((i+1)); done   # runs WHILE true

i=1
until [ $i -gt 5 ]; do echo $i; i=$((i+1)); done   # runs UNTIL true (while false)
```

`break` exits the loop, `continue` skips to the next iteration.

### 7.7 Functions

```bash
add() {
    echo $(( $1 + $2 ))     # "return" a value by printing it
}
result=$(add 3 4)           # capture with command substitution
echo $result                # 7

check() {
    return 3                # sets exit status ONLY (0 to 255)
}
check
echo $?                     # 3
```

> **Trap.** `return` in a shell function only sets an **exit status (0 to 255)**. To return real data, **echo** it and capture with `$(...)`.

### 7.8 Command substitution and quoting

- `` `cmd` `` or `$(cmd)`: replaced by the command's output. `today=$(date)`.
- Double quotes `"..."`: variables and `$(...)` **are expanded**.
- Single quotes `'...'`: **nothing** is expanded; literal.

```bash
name=Ram
echo "Hi $name"    # Hi Ram
echo 'Hi $name'    # Hi $name
```

---

## 8. Exam traps

1. Shell is **not** part of the kernel.
2. File name is **not** stored in the inode.
3. Hard links share an inode and can't cross file systems; symlinks store a path and can dangle.
4. `rwxr-xr--` = **754**. r=4, w=2, x=1.
5. umask 022 → files 644, dirs 755.
6. `>` overwrites, `>>` appends; fd 2 = stderr.
7. No spaces around `=` in shell assignment.
8. `while` loops while true; `until` loops while false.
9. `return` gives only an exit status.
10. `$#` = count, `$?` = last exit status, `$0` = script name.
11. `kill -9` sends SIGKILL, which can't be caught or ignored.
12. `uniq` removes only **adjacent** duplicates (so usually `sort | uniq`).

---

## 9. Practice questions

**Q1.** The octal permission for `rw-r-----` is:
(a) 640 (b) 644 (c) 740 (d) 604

**Answer: (a).** rw- = 6, r-- = 4, --- = 0.

---

**Q2.** `chmod 751 file` gives which permission string?
(a) rwxr-x--x (b) rwxrw---x (c) rwxr-xr-x (d) rw-r-x--x

**Answer: (a).** 7 = rwx, 5 = r-x, 1 = --x.

---

**Q3.** With umask 027, a newly created regular file gets permissions:
(a) 640 (b) 750 (c) 644 (d) 600

**Answer: (a).** 666 − 027 → 6−0=6, 6−2=4, 6−7 → 0 (can't go below 0) = 640.

---

**Q4.** Which statement about hard links is TRUE?
(a) They can span file systems (b) They have a different inode from the original (c) Deleting the original name doesn't delete the data while a hard link exists (d) They become broken if the original is deleted

**Answer: (c).**

---

**Q5.** In `ls -l` output, the first character `b` indicates:
(a) binary file (b) block special device (c) backup (d) broken link

**Answer: (b).**

---

**Q6.** A script is run as `./s.sh x y z w`. What is `$#`?
(a) 5 (b) 4 (c) 3 (d) 1

**Answer: (b).** Count of arguments, excluding `$0`.

---

**Q7.** What does `ls | wc -l` print?
(a) number of lines in ls's source code (b) number of entries listed by ls (c) number of words in each file (d) an error

**Answer: (b).**

---

**Q8.** Which redirects **only errors** to `err.txt`?
(a) `cmd > err.txt` (b) `cmd 2> err.txt` (c) `cmd >> err.txt` (d) `cmd < err.txt`

**Answer: (b).**

---

**Q9.** Output of:
```bash
x=5
echo '$x'
```
(a) 5 (b) $x (c) x (d) error

**Answer: (b).** Single quotes prevent expansion.

---

**Q10.** Which loop keeps running as long as the condition is FALSE?
(a) while (b) for (c) until (d) case

**Answer: (c).**

---

**Q11.** Which signal cannot be caught or ignored by a process?
(a) SIGTERM (b) SIGINT (c) SIGKILL (d) SIGHUP

**Answer: (c).** `kill -9`.

---

**Q12.** What is wrong with `count = 10` in a bash script?
(a) Nothing (b) Spaces around `=` make the shell treat `count` as a command (c) Variables must be uppercase (d) Numbers need quotes

**Answer: (b).**

---

**Q13.** Which command shows disk space usage of each mounted file system?
(a) du (b) df (c) ls -s (d) free

**Answer: (b).** `du` shows space used by files/directories; `df` shows free space per file system.

---

**Q14.** The sticky bit on `/tmp` ensures that:
(a) files are never deleted (b) only a file's owner (or root) can delete it (c) files are executable (d) files stay in memory

**Answer: (b).**

---

**Q15.** Which command searches for lines containing "error", ignoring case, in log.txt?
(a) `grep -v error log.txt` (b) `grep -i error log.txt` (c) `grep -c error log.txt` (d) `find error log.txt`

**Answer: (b).**

---

**Q16.** File descriptor 0 refers to:
(a) stdout (b) stderr (c) stdin (d) the first opened file

**Answer: (c).**
