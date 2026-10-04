# 11. Transport Layer: TCP Congestion Control, Timers, and UDP

> **Two different "slow down" problems.** **Flow control** protects the **receiver** ("my buffer is full"). **Congestion control** protects the **network** ("routers in between are overloaded"). TCP handles the second with a clever self-tuning window that grows when things go well and shrinks sharply when packets are lost.

---

## 1. Two windows, one rule

```
Sender's window = min(rwnd, cwnd)
```

- **rwnd** (receiver window): advertised by the receiver in every segment. Flow control.
- **cwnd** (congestion window): kept **only by the sender**, never sent on the wire. The sender's estimate of how much the network can absorb.

For a two-way connection there are four windows in total (each side has a send window and a receive window).

---

## 2. TCP congestion control (Tahoe/Reno style)

Units: we count cwnd in **MSS** (maximum segment size) for simplicity.

### Phase 1: Slow Start (exponential growth)

- Start with **cwnd = 1 MSS**.
- For **each ACK received**, cwnd += 1 MSS. Since a full window's worth of ACKs arrives per RTT, cwnd **doubles every RTT**: 1, 2, 4, 8, 16, ...
- Continue until cwnd reaches **ssthresh** (slow-start threshold).

> **Trap.** "Slow start" is **exponential**: the fastest-growing phase. It's "slow" only compared to blasting a full window instantly.

### Phase 2: Congestion Avoidance (linear growth)

- Once cwnd ≥ ssthresh, grow by about **1 MSS per RTT** (each ACK adds MSS × MSS / cwnd).
- This is **Additive Increase**.

### Phase 3: Reacting to loss (Multiplicative Decrease)

| Loss detected by | Interpretation | ssthresh | cwnd | Next phase |
|---|---|---|---|---|
| **Timeout** | Strong congestion | **cwnd / 2** | **1 MSS** | **Slow start** |
| **3 duplicate ACKs** (Reno: fast retransmit + fast recovery) | Mild congestion | **cwnd / 2** | **ssthresh** (Reno adds 3 temporarily) | **Congestion avoidance** |

(TCP **Tahoe** treated both cases like a timeout: cwnd = 1. TCP **Reno** introduced fast recovery for 3 dup ACKs.)

Overall pattern: **AIMD** (Additive Increase, Multiplicative Decrease), which produces the familiar **sawtooth** of cwnd over time.

### Initial ssthresh

Often set to a large value or to the receiver's window. Some textbooks (and this syllabus) use: **ssthresh = (rwnd / MSS) / 2**. Example: rwnd = 8000 bytes, MSS = 1000 → 8 segments → ssthresh = **4**.

---

## 3. A full trace (practise until automatic)

Initial: cwnd = 1 MSS, ssthresh = **32**. No loss until a **timeout** occurs when cwnd = **36**.

| RTT round | cwnd | Phase |
|---|---|---|
| 1 | 1 | Slow start |
| 2 | 2 | SS |
| 3 | 4 | SS |
| 4 | 8 | SS |
| 5 | 16 | SS |
| 6 | 32 | Reached ssthresh |
| 7 | 33 | Congestion avoidance |
| 8 | 34 | CA |
| 9 | 35 | CA |
| 10 | 36 | **Timeout here** |

After the timeout: ssthresh = 36/2 = **18**, cwnd = **1**.

| Round | cwnd | |
|---|---|---|
| 11 | 1 | SS |
| 12 | 2 | |
| 13 | 4 | |
| 14 | 8 | |
| 15 | 16 | |
| 16 | **18** | Doubling would give 32, but slow start stops at ssthresh, so cwnd = 18 |
| 17 | 19 | CA |
| 18 | 20 | CA |

If instead **3 duplicate ACKs** had occurred at cwnd = 36 (Reno): ssthresh = 18, cwnd = **18**, and growth continues linearly: 19, 20, 21, ...

> **Exam tip:** read whether the question counts "after n RTTs" starting at cwnd = 1 at RTT 0 or RTT 1, and whether slow start may **exceed** ssthresh when doubling (most exam solutions **cap** at ssthresh).

---

## 4. Retransmission timeout (RTO) estimation

The transport layer can't use a fixed timeout: RTTs vary hugely across paths and over time.
- **Too short** → needless retransmissions (worsening congestion).
- **Too long** → slow recovery from real losses.

### 4.1 Basic (exponential averaging)

```
IRTT(new) = α × IRTT(old) + (1 − α) × ARTT        (ARTT = measured RTT)
TOT = 2 × IRTT(new)
```

α weights history (e.g. 0.9). The "× 2" is a crude safety margin.

### 4.2 Jacobson's algorithm (adds variance)

```
IRTT(new) = α × IRTT(old) + (1 − α) × ARTT
AD        = | IRTT(old) − ARTT |                 (actual deviation)
ID(new)   = α × ID(old) + (1 − α) × AD
TOT       = IRTT(new) + 4 × ID(new)
```

**Example:** α = 0.9, IRTT = 30 ms, ID = 5 ms, measured ARTT = 26 ms.
- IRTT(new) = 0.9 × 30 + 0.1 × 26 = 27 + 2.6 = **29.6 ms**.
- AD = |30 − 26| = 4. ID(new) = 0.9 × 5 + 0.1 × 4 = **4.9 ms**.
- Jacobson TOT = 29.6 + 4 × 4.9 = **49.2 ms**. (Basic: 2 × 29.6 = 59.2 ms.)

(Modern TCP, RFC 6298: SRTT = (7/8)SRTT + (1/8)RTT; RTTVAR = (3/4)RTTVAR + (1/4)|SRTT − RTT|; RTO = SRTT + 4·RTTVAR. Same idea.)

### 4.3 Karn's algorithm

When a segment is **retransmitted** and then an ACK arrives, we can't tell whether it acknowledges the original or the retransmission, so the RTT sample is **ambiguous**.

Karn's rule:
- **Don't use RTT samples from retransmitted segments.**
- On each retransmission, **double the timeout** (exponential back-off) until a segment gets through without retransmission.

---

## 5. TCP timers

| Timer | Purpose |
|---|---|
| **Retransmission** | Resend a segment not ACKed in time (RTO) |
| **Persist** | When the receiver advertises **window = 0**, periodically send a 1-byte **probe**, so a lost window-update can't cause a **deadlock** |
| **Keep-alive** | Detect a dead peer on an idle connection: after long idleness (often 2 hours), send probes (e.g. 10 probes, 75 s apart); close if no reply |
| **TIME-WAIT** | After closing, wait **2 × MSL** before reusing the connection identifiers |
| (Delayed) ACK | Delay ACKs briefly to combine them or piggyback them on data |

---

## 6. Silly Window Syndrome (SWS)

**Problem:** sending tiny segments (e.g. 1 byte of data with 40 bytes of headers) wastes bandwidth. Caused by either side:
- the **sender** application producing data slowly (one keystroke at a time), or
- the **receiver** application consuming slowly and advertising tiny windows as soon as a few bytes free up.

**Fixes:**
- **Nagle's algorithm (sender side):** send the first small segment immediately; then **buffer** further small data until either the outstanding data is ACKed or a full MSS has accumulated. Can be disabled (`TCP_NODELAY`) for latency-sensitive apps like games or SSH.
- **Clark's solution (receiver side):** don't advertise a tiny window. Advertise **0** until there's room for a full MSS or half the buffer is free.
- **Delayed acknowledgement (receiver side):** delay ACKs so the window updates are larger.

Nagle and Clark attack the problem from **opposite ends**; they're complementary.

---

## 7. UDP (User Datagram Protocol)

### 7.1 Header: fixed 8 bytes

```
+-------------------+-------------------+
|  Source port (16) |  Dest port (16)   |
+-------------------+-------------------+
|  Length (16)      |  Checksum (16)    |
+-------------------+-------------------+
```

- **Length** = header + data (minimum 8).
- **Checksum** covers header, data and a pseudo-header (protocol = **17**). **Optional in IPv4** (0 = not used), **mandatory in IPv6**.
- Max UDP data in one IPv4 datagram: 65,535 − 20 − 8 = **65,507 bytes**.

### 7.2 What UDP doesn't do

No connection setup, no ACKs, no retransmission, no ordering, no flow control, no congestion control. Each datagram stands alone; message boundaries **are preserved**.

### 7.3 Why use it?

- **Low latency, low overhead**: no handshake, tiny header.
- **Real-time** media (VoIP, video calls, live streaming, online games) prefers a late packet to be **dropped** rather than waited for.
- **Simple request-reply** protocols: **DNS**, **DHCP**, **SNMP**, **TFTP** (which adds its own simple reliability), **RIP**, NTP.
- **Multicast/broadcast** (TCP can't do these).
- Applications can build their own reliability on top (e.g. **QUIC**, used by HTTP/3, runs over UDP).

### 7.4 TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Yes (3-way handshake) | No |
| Reliability | Yes | No |
| Ordering | Yes | No |
| Flow control | Yes | No |
| Congestion control | Yes | No |
| Header | 20 to 60 bytes | **8 bytes** |
| Data unit | Byte stream (no boundaries) | Datagram (boundaries preserved) |
| Speed / overhead | Slower, more overhead | Faster, lighter |
| Broadcast/multicast | No | Yes |
| Typical apps | HTTP(S), FTP, SMTP, SSH, Telnet | DNS, DHCP, SNMP, TFTP, VoIP, streaming, RIP |

---

## 8. Exam traps

1. Sender window = **min(rwnd, cwnd)**; cwnd is never transmitted.
2. Slow start is **exponential** (doubles per RTT) up to ssthresh.
3. Congestion avoidance: +1 MSS per RTT.
4. Timeout: ssthresh = cwnd/2, **cwnd = 1**, slow start. 3 dup ACKs (Reno): ssthresh = cwnd/2, **cwnd = ssthresh**, congestion avoidance.
5. AIMD.
6. Jacobson: TOT = IRTT + 4·ID. Karn: ignore retransmitted samples, double the timer.
7. Persist timer solves the zero-window deadlock.
8. Nagle = sender-side SWS fix; Clark = receiver-side.
9. UDP header 8 bytes; checksum optional in IPv4.

---

## 9. Practice questions

**Q1.** cwnd = 1 MSS initially, ssthresh = 16. After how many RTTs does cwnd first reach 16?
(a) 3 (b) 4 (c) 5 (d) 16

**Answer: (b).** 1 → 2 → 4 → 8 → 16: four doublings.

---

**Q2.** cwnd = 40 MSS when a timeout occurs. New ssthresh and cwnd?
(a) 20, 20 (b) 20, 1 (c) 40, 1 (d) 10, 1

**Answer: (b).**

---

**Q3.** cwnd = 40 MSS when 3 duplicate ACKs arrive (TCP Reno). New ssthresh and cwnd?
(a) 20, 20 (b) 20, 1 (c) 40, 20 (d) 10, 10

**Answer: (a).**

---

**Q4.** Initial ssthresh = 8 MSS. cwnd is 1 MSS in RTT round 1. A timeout occurs during round 6. What is cwnd in round 10 (slow start is capped at ssthresh)?
(a) 4 (b) 5 (c) 6 (d) 8

**Answer: (b).**
- Rounds 1 to 6: 1, 2, 4, 8 (reached ssthresh), 9, 10. Timeout at cwnd = 10.
- New ssthresh = 10/2 = 5; cwnd = 1.
- Rounds 7 to 10: 1, 2, 4, **5** (doubling to 8 is capped at the new ssthresh 5).

---

**Q5.** Which timer prevents deadlock when the receiver advertises a zero window?
(a) Retransmission (b) Persist (c) Keep-alive (d) TIME-WAIT

**Answer: (b).**

---

**Q6.** Jacobson: IRTT = 20 ms, ID = 4 ms, new sample ARTT = 24 ms, α = 0.75. New TOT?
(a) 21 + 4 × 4 = 37 ms (b) 41 ms (c) 42 ms (d) 36 ms

**Answer: (a).**
- IRTT(new) = 0.75 × 20 + 0.25 × 24 = 15 + 6 = 21.
- AD = |20 − 24| = 4. ID(new) = 0.75 × 4 + 0.25 × 4 = 4.
- TOT = 21 + 4 × 4 = **37 ms**.

---

**Q7.** Karn's algorithm says that after a retransmission:
(a) the RTT sample is used normally (b) the RTT sample is ignored and the timeout is doubled (c) the connection is reset (d) cwnd is doubled

**Answer: (b).**

---

**Q8.** Nagle's algorithm addresses silly window syndrome caused by:
(a) the receiver (b) the sender (c) routers (d) the network layer

**Answer: (b).**

---

**Q9.** UDP length field = 120. How many data bytes?
(a) 120 (b) 112 (c) 100 (d) 92

**Answer: (b).** 120 − 8.

---

**Q10.** Which application is most likely to use UDP?
(a) Email (SMTP) (b) File transfer (FTP) (c) Live video call (d) Web browsing (HTTP/1.1)

**Answer: (c).**

---

**Q11.** rwnd = 20 KB and cwnd = 12 KB. How much unacknowledged data may the sender have in flight?
(a) 20 KB (b) 12 KB (c) 32 KB (d) 8 KB

**Answer: (b).** min(20, 12).

---

**Q12.** TCP congestion control as a whole follows:
(a) MIAD (b) AIMD (c) AIAD (d) MIMD

**Answer: (b).**

---

**Q13.** In IPv4, the UDP checksum is:
(a) mandatory (b) optional (c) not present (d) covers only the header

**Answer: (b).**
