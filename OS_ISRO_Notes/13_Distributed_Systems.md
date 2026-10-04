# 13. Distributed Systems

> **The core difficulty.** On one computer, there's one memory and one clock, so "what happened first" is obvious. In a distributed system, every machine has its own clock, and the only way to communicate is by sending messages that take unpredictable time. Suddenly even "who asked first?" is hard. This chapter is about solving classic OS problems (mutual exclusion, deadlock, agreement, commit) under those conditions.

---

## 1. What is a distributed system?

A **distributed system** is a collection of **independent computers** (nodes), each with:
- its **own processor**,
- its **own local memory** (**no shared memory**),
- its **own local clock** (**no global clock**),

connected by a **network**, communicating **only by message passing**, yet appearing to users as **one coherent system**.

Examples: Google search, cloud storage, online banking, the internet's DNS, satellite ground-station networks.

### Why build them?

- **Resource sharing** (printers, data, compute).
- **Speedup** (split work across machines).
- **Reliability** (if one machine fails, others continue).
- **Communication** between geographically separate users.
- **Scalability** (add machines as load grows).

### Transparency (hiding the distribution)

| Type | Hides |
|---|---|
| Access | Differences in how local vs remote resources are accessed |
| Location | **Where** a resource is |
| Migration | That a resource moved |
| Replication | That there are **multiple copies** |
| Concurrency | That others are using it simultaneously |
| Failure | That something failed and was recovered |

### Distributed File System (DFS)

Provides access to files stored on many machines through **one common interface**, as if they were local. Examples: NFS, AFS, HDFS, Google File System.

---

## 2. Events and ordering

Each process performs three kinds of events:
- **Internal event** (local computation),
- **Send event** (sends a message),
- **Receive event** (receives a message).

### Happened-before relation (→), by Lamport

- If a and b are in the **same process** and a occurs before b, then **a → b**.
- If a is the **sending** of a message and b is its **receipt**, then **a → b**.
- **Transitive:** a → b and b → c ⟹ a → c.

If neither a → b nor b → a, the events are **concurrent** (a ∥ b).

### Lamport logical clocks

Each process keeps a counter C.
1. Before each event, **C = C + 1**.
2. When sending, attach C to the message.
3. On receiving a message with timestamp t: **C = max(C, t) + 1**.

Property: **a → b ⟹ C(a) < C(b)**. But the converse is **not** true: C(a) < C(b) doesn't mean a happened before b.

**Example:** P1 does events a (C=1), then sends m with timestamp 2 (event b, C=2). P2's clock is at 5 before receiving. On receipt: C = max(5, 2) + 1 = 6.

### Vector clocks

Each process keeps a vector V of length N (one entry per process).
- On a local event at Pi: V[i]++.
- Send: attach the whole vector.
- Receive: V[j] = max(V[j], received[j]) for all j, then V[i]++.

Vector clocks capture causality exactly: **a → b ⟺ V(a) < V(b)** (every component ≤, at least one <).

---

## 3. Distributed mutual exclusion

Same three requirements as Chapter 04 (**mutual exclusion, progress, bounded waiting/fairness**), but now **without shared memory**: only messages.

### 3.1 Centralized algorithm

One process is the **coordinator**.
- To enter: send **REQUEST** to the coordinator. If the CS is free, it replies **GRANT**; otherwise it queues the request.
- To exit: send **RELEASE**. The coordinator grants the next in the queue.

**Messages per CS entry: 3** (request, grant, release).
- Simple, fair (FIFO queue), fewest messages.
- **Single point of failure** and **bottleneck**.

### 3.2 Lamport's algorithm (fully distributed)

- Broadcast a timestamped REQUEST to all; each process keeps a queue ordered by timestamp.
- Each recipient sends a REPLY.
- Enter the CS when your request is at the head of your queue **and** you've received a message with a later timestamp from everyone.
- On exit, broadcast RELEASE.

**Messages: 3(N − 1).**

### 3.3 Ricart-Agrawala algorithm

An optimisation of Lamport's that merges RELEASE into REPLY.

- To enter: send timestamped **REQUEST** to all **N − 1** others.
- A process receiving a request:
  - replies **immediately** if it's not interested in the CS, or if its own request has a **later** timestamp (lower priority);
  - otherwise (it's in the CS, or its own request is earlier), it **defers** the reply.
- Enter the CS after receiving **N − 1 replies**.
- On exit, send all deferred replies.

**Messages: 2(N − 1)** (N − 1 requests + N − 1 replies).

Ties in timestamps are broken by process ID.

### 3.4 Token-based algorithms

A unique **token** circulates. **Only the holder of the token may enter the CS.** Mutual exclusion is automatic (one token).

**Token ring:** processes are arranged in a logical ring; the token passes around. A process wanting the CS waits until the token arrives.
- Messages per entry: from 1 to ∞ (the token circulates even when nobody wants it).

**Suzuki-Kasami (broadcast-based token algorithm):**
- A process wanting the CS broadcasts a REQUEST (with a sequence number) to all others.
- The token carries a queue of waiting processes and the last-served sequence numbers, so outdated requests are recognised.
- **Messages: 0** if the requester already holds the token; otherwise **N** (N − 1 requests + 1 token message).

**Raymond's tree-based algorithm:** processes form a tree; requests travel up toward the token holder. **O(log N)** messages on average.

Problems specific to token algorithms:
- **Token loss** (a crash while holding it): everyone waits forever. Needs detection and **regeneration**.
- **Token duplication** (e.g. after a faulty regeneration): breaks mutual exclusion.
- **Starvation** with a bad passing policy.

### 3.5 Maekawa's algorithm (quorum-based)

Each process needs permission only from a **subset (quorum)** of about **√N** processes, chosen so any two quorums overlap. **Messages: 3√N to 5√N.** Can deadlock without extra care.

### Summary

| Algorithm | Type | Messages per CS entry | Weakness |
|---|---|---|---|
| Centralized | Coordinator | **3** | Single point of failure |
| Lamport | Distributed, timestamps | 3(N − 1) | Many messages |
| **Ricart-Agrawala** | Distributed, timestamps | **2(N − 1)** | N points of failure (any silent process blocks others) |
| Token ring | Token | 1 to ∞ | Token loss |
| **Suzuki-Kasami** | Token, broadcast | **0 or N** | Token loss |
| Raymond | Token, tree | O(log N) | Tree maintenance |
| Maekawa | Quorum | 3√N to 5√N | Possible deadlock |

---

## 4. Distributed deadlock

Processes at **different sites** wait for resources held by each other, forming a cycle that spans machines.

### Wait-for graph (WFG)

Each site has a **local** WFG. A deadlock may only be visible in the **global** WFG (the union of all local graphs), which must be assembled through messages.

Detection approaches:
- **Centralized:** one site collects all local WFGs and checks for a global cycle.
- **Distributed (e.g. Chandy-Misra-Haas edge chasing):** a blocked process sends a **probe** message along wait-for edges. If the probe ever comes back to the initiator, there's a cycle, so deadlock.
- **Hierarchical:** sites arranged in a tree; each node detects deadlocks among its descendants.

### Phantom deadlock

Message delays mean the collected global state can be **stale and inconsistent**. The detector may see a cycle that **no longer exists** (one process already released a resource, but that message hadn't arrived yet). This **false positive** is a **phantom deadlock**. It cannot happen in single-machine detection, which looks at one consistent instant.

---

## 5. Agreement problems

### Consensus

All **non-faulty** processes must **agree on one value**, which must be a value **proposed by some process** (validity), and must eventually decide (termination), despite some processes **crashing**.

Famous result (**FLP impossibility**, 1985): in a fully **asynchronous** system, no deterministic algorithm can guarantee consensus if even **one** process may crash. Practical systems (Paxos, Raft) get around this using timeouts / partial synchrony.

### Byzantine Generals problem

Several generals surround a city and must agree to **attack** or **retreat**. Some generals are **traitors** who may send **different, contradictory, malicious** messages to different generals.

**Byzantine faults** are much worse than crash faults: a faulty process can **lie**.

Classic result: to tolerate **f** Byzantine-faulty processes, you need at least **3f + 1** processes in total.

| f (traitors) | Minimum total processes |
|---|---|
| 1 | 4 |
| 2 | 7 |
| 3 | 10 |

Equivalently, with N processes you can tolerate at most **floor((N − 1)/3)** traitors. With 3 generals and 1 traitor, agreement is **impossible**.

(For **crash** faults with majority-based protocols like Paxos/Raft, you need **2f + 1** nodes to tolerate f crashes.)

---

## 6. Distributed transactions and Two-Phase Commit

A **distributed transaction** touches data at **multiple sites** (e.g. transfer money from an account in Mumbai's database to one in Bengaluru's). **Atomicity** must hold across all sites: **all commit or all abort**.

### Two-Phase Commit (2PC)

Roles: one **coordinator** and several **participants**.

**Phase 1: Voting (prepare)**
1. Coordinator sends **PREPARE** ("can you commit?") to all participants.
2. Each participant: if it can commit, it writes its changes to a durable log, enters the **ready** state, **keeps its locks**, and votes **YES**. Otherwise it votes **NO** (and aborts locally).

**Phase 2: Decision (commit/abort)**
3. If **all** voted YES → coordinator logs and sends **COMMIT**. If **any** voted NO (or timed out) → sends **ABORT**.
4. Participants carry out the decision, release locks, and send ACK.

Messages for N participants: about 4N (prepare, vote, decision, ack).

### The blocking problem

Suppose all participants voted YES, and the **coordinator crashes before sending the decision**.
- A YES voter **cannot abort** on its own (maybe the coordinator decided COMMIT and others committed).
- It **cannot commit** on its own (maybe someone else voted NO).
- So it must **wait (block)**, holding its locks, until the coordinator recovers.

This is 2PC's key weakness: it's a **blocking protocol**.

**Three-Phase Commit (3PC)** adds a **pre-commit** phase between voting and committing, so that participants can safely decide among themselves if the coordinator fails (assuming no network partitions and bounded delays). It's **non-blocking** under those assumptions but costs more messages.

---

## 7. Fault tolerance techniques

- **Checkpointing:** periodically save a consistent state of each process.
- **Rollback recovery:** after a failure, revert to a checkpoint.
  - Uncoordinated checkpoints can cause the **domino effect** (rolling back one process forces others to roll back further and further). Coordinated checkpointing avoids it.
- **Message logging:** log messages so that after a rollback they can be replayed.
- **Replication:** keep copies of data/services on several nodes for availability. Costs: keeping replicas consistent.

### CAP theorem (useful context)

A distributed data store can guarantee at most **two** of: **Consistency, Availability, Partition tolerance**. Since network partitions will happen, systems choose between C and A during a partition.

---

## 8. Exam traps

1. Distributed system: **no shared memory, no global clock**, only messages.
2. Lamport clock: a → b ⟹ C(a) < C(b), **not** the reverse. Vector clocks give both directions.
3. Centralized mutual exclusion: **3 messages**, single point of failure.
4. Ricart-Agrawala: **2(N − 1)** messages.
5. Lamport mutual exclusion: **3(N − 1)**.
6. Suzuki-Kasami: **0 or N**.
7. Phantom deadlock: **false** deadlock due to stale global state; only in distributed detection.
8. Byzantine: need **3f + 1** processes for f traitors.
9. 2PC is **blocking** if the coordinator fails after votes. 3PC tries to fix it.

---

## 9. Practice questions

**Q1.** How many messages does the Ricart-Agrawala algorithm need per CS entry with 10 processes?
(a) 9 (b) 18 (c) 27 (d) 10

**Answer: (b).** 2(N − 1) = 18.

---

**Q2.** With Lamport's distributed mutual exclusion algorithm and N = 6, messages per CS entry?
(a) 10 (b) 12 (c) 15 (d) 18

**Answer: (c).** 3(N − 1) = 15.

---

**Q3.** Minimum number of generals needed to tolerate 2 traitors in the Byzantine Generals problem?
(a) 5 (b) 6 (c) 7 (d) 4

**Answer: (c).** 3f + 1 = 7.

---

**Q4.** With 10 processes, the maximum number of Byzantine faults that can be tolerated is:
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (b).** floor((10 − 1)/3) = 3.

---

**Q5.** Process P has Lamport clock 7. It receives a message with timestamp 12. Its clock becomes:
(a) 8 (b) 12 (c) 13 (d) 19

**Answer: (c).** max(7, 12) + 1 = 13.

---

**Q6.** Process P has Lamport clock 15. It receives a message with timestamp 4. Its clock becomes:
(a) 16 (b) 5 (c) 15 (d) 19

**Answer: (a).** max(15, 4) + 1 = 16.

---

**Q7.** In the centralized mutual exclusion algorithm, the number of messages per CS entry is:
(a) 2 (b) 3 (c) N (d) 2(N − 1)

**Answer: (b).**

---

**Q8.** A deadlock detected in a distributed system that does not actually exist is called:
(a) livelock (b) phantom deadlock (c) starvation (d) false sharing

**Answer: (b).**

---

**Q9.** Which statement about 2PC is TRUE?
(a) It is non-blocking (b) A participant that voted YES may block if the coordinator crashes before the decision (c) Participants may unilaterally commit after voting YES (d) It needs only one phase

**Answer: (b).**

---

**Q10.** Vector clocks V(a) = [2, 1, 0] and V(b) = [1, 2, 0]. The events are:
(a) a → b (b) b → a (c) concurrent (d) identical

**Answer: (c).** Neither vector is ≤ the other in every component (2 > 1 in the first, 1 < 2 in the second).

---

**Q11.** Vector clocks V(a) = [1, 0, 0] and V(b) = [2, 1, 0]. Then:
(a) a → b (b) b → a (c) concurrent (d) cannot say

**Answer: (a).** Every component of V(a) ≤ V(b), and at least one is strictly less.

---

**Q12.** In Suzuki-Kasami, a process that does not hold the token needs how many messages to enter the CS (N processes)?
(a) 0 (b) N − 1 (c) N (d) 2N

**Answer: (c).** N − 1 request broadcasts + 1 token transfer.

---

**Q13.** Which is NOT a characteristic of a distributed system?
(a) No shared memory (b) A global clock shared by all nodes (c) Communication by message passing (d) Independent failures of nodes

**Answer: (b).**

---

**Q14.** The main disadvantage of token-based mutual exclusion is:
(a) too many messages always (b) token loss requires regeneration (c) violates mutual exclusion normally (d) needs a global clock

**Answer: (b).**
