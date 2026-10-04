# 04. Data Link Layer: Flow Control and ARQ (Stop-and-Wait, Go-Back-N, Selective Repeat)

> **Why this chapter is gold.** Sliding window efficiency questions appear in almost every networking paper. They all come from **one idea**: how much of the "pipe" between sender and receiver is kept full of data. Once you can picture the pipe, every formula falls out naturally.

---

## 1. Flow control vs error control

- **Flow control:** don't overwhelm a slow receiver. The sender must wait for permission (ACKs) before sending more.
- **Error control:** detect lost, damaged or duplicate frames and **retransmit**. Done by **ARQ (Automatic Repeat reQuest)** protocols.

The three classic ARQ protocols: **Stop-and-Wait**, **Go-Back-N**, **Selective Repeat**. The last two are **sliding window** protocols.

---

## 2. The timing picture

```
Sender                                   Receiver
  |--- first bit leaves at t = 0 ------->
  |    ... last bit leaves at t = Tt
  |                    first bit arrives at Tp
  |                    last bit arrives at Tt + Tp
  |<----------- ACK (tiny) --------------|
  ACK arrives at  Tt + 2Tp
```

- **Tt** = frame size / bandwidth (time to put the frame on the wire).
- **Tp** = distance / speed (time for a bit to travel).
- **RTT** = 2Tp (ACK transmission time assumed negligible; processing ignored).

One cycle of Stop-and-Wait takes **Tt + 2Tp**, but only **Tt** of it is spent sending data.

---

## 3. Stop-and-Wait ARQ

### 3.1 How it works

1. Send one frame, start a timer.
2. Wait for its ACK.
3. ACK arrives → send the next frame. Timer expires → resend the same frame.

- Frames are numbered **0, 1, 0, 1, ...** (a 1-bit sequence number), so the receiver can detect a **duplicate** (when the ACK was lost and the sender resent).
- ACK numbers announce the **next** frame expected.
- A damaged frame is simply **discarded**; the receiver says nothing, and the sender's timer handles it.

Why 1 bit is enough: only one frame is ever outstanding, so we just need to distinguish "this one" from "the next one".

### 3.2 Efficiency

```
a = Tp / Tt
η (Stop-and-Wait) = Tt / (Tt + 2Tp) = 1 / (1 + 2a)
Throughput = η × Bandwidth
```

**Example:** Tt = 1 ms, Tp = 4 ms → a = 4 → η = 1/9 ≈ **11.1%**.

**Example:** 1 Mbps link, 1000-bit frames, Tp = 20 ms. Tt = 1 ms, a = 20, η = 1/41 ≈ **2.44%**. Throughput ≈ 24.4 kbps on a 1 Mbps link. Terrible! The sender spends 40 ms of every 41 ms just waiting.

Stop-and-Wait is fine when a is small (short links, big frames), awful on long or fast links.

### 3.3 With errors

If each frame is lost/damaged with probability p, the expected number of transmissions per frame is **1/(1 − p)**, so η = (1 − p)/(1 + 2a).

---

## 4. Sliding windows: keep the pipe full

Instead of one frame at a time, let the sender have up to **W frames outstanding** (sent but not yet ACKed).

In one cycle (Tt + 2Tp), the sender can push W frames, each taking Tt. If W × Tt ≥ Tt + 2Tp, the pipe never empties: **100% efficiency**.

```
Window needed for 100% utilisation:  W = 1 + 2a
Efficiency with window W:            η = W / (1 + 2a)    if W < 1 + 2a
                                     η = 1               if W ≥ 1 + 2a
```

**Example:** Tt = 1 ms, Tp = 4 ms → W = 1 + 8 = **9** for full utilisation. With W = 3, η = 3/9 = **33.3%**.

**Example:** 1 Mbps, 1000-bit frames, Tp = 20 ms: W = 1 + 40 = **41**.

### Bandwidth-delay product view

W frames should cover the data that fits in the pipe during one round trip:

```
W ≈ (Bandwidth × RTT) / frame size + 1
```

Same result as 1 + 2a.

### How many sequence-number bits?

With m bits there are 2^m sequence numbers.

| Protocol | Max sender window | Receiver window |
|---|---|---|
| Stop-and-Wait | 1 | 1 |
| **Go-Back-N** | **2^m − 1** | 1 |
| **Selective Repeat** | **2^(m−1)** | 2^(m−1) |

So, to support a required window W:
- GBN: need 2^m − 1 ≥ W → **m = ⌈log₂(W + 1)⌉**.
- SR: need 2^(m−1) ≥ W → **m = ⌈log₂ W⌉ + 1**.

**Example:** W = 41. GBN: 2^m − 1 ≥ 41 → m = 6 (63). SR: 2^(m−1) ≥ 41 → m − 1 = 6 → m = 7.

---

## 5. Go-Back-N (GBN)

### 5.1 Rules

- Sender window up to **2^m − 1**.
- **Receiver window = 1**: the receiver accepts **only the next expected frame**. Anything out of order is **discarded** (even if it's perfectly fine).
- **Cumulative ACKs**: "ACK n" means "all frames before n received; I expect n next".
- **One timer** (for the oldest unacknowledged frame).
- On timeout: resend the oldest unACKed frame **and every frame after it** ("go back N").

### 5.2 Example

Window 4. Frames 0, 1, 2, 3 sent. Frame 1 is lost.
- Receiver gets 0 → ACK 1. Gets 2 → out of order, discard (maybe re-ACK 1). Gets 3 → discard.
- Sender's timer for frame 1 expires → resend **1, 2, 3**.

Wasteful on noisy links: one loss costs a whole window of retransmissions.

### 5.3 Why the window is 2^m − 1, not 2^m

m = 2 (sequence numbers 0 to 3). Suppose the window were 4: send 0, 1, 2, 3. All arrive; receiver now expects **0** (the next cycle). All 4 ACKs are lost. Sender times out and resends old frame **0**. The receiver accepts it as the **new** frame 0. **Wrong!** With window 3, the receiver would be expecting 3 after 0, 1, 2, and a resent 0 would be correctly rejected.

---

## 6. Selective Repeat (SR)

### 6.1 Rules

- Sender **and** receiver windows of up to **2^(m−1)**.
- The receiver **accepts and buffers out-of-order frames** within its window, delivering them to the network layer **in order** once the gap is filled.
- **Individual ACKs** (and optionally **NAKs** for a specific damaged frame).
- **A timer per frame**; on timeout, resend **only that frame**.

### 6.2 Example

Window 4. Frames 0, 1, 2, 3 sent, frame 1 lost.
- Receiver gets 0 (deliver), buffers 2 and 3, sends NAK 1 (or just ACKs 2, 3).
- Sender resends **only frame 1**. Receiver delivers 1, 2, 3.

### 6.3 Why the window is only half the sequence space

m = 2 (0 to 3), suppose window 3. Send 0, 1, 2; all arrive, receiver window slides to **3, 0, 1**. All ACKs lost. Sender resends old 0. Receiver's window includes **0** (as a new frame!) → accepts a duplicate as new. **Wrong.** With window 2 (= 2^(m−1)), the old and new windows never overlap, so this can't happen.

---

## 7. Comparison

| Feature | Stop-and-Wait | Go-Back-N | Selective Repeat |
|---|---|---|---|
| Sender window | 1 | ≤ 2^m − 1 | ≤ 2^(m−1) |
| Receiver window | 1 | 1 | ≤ 2^(m−1) |
| Out-of-order frames | n/a | **Discarded** | **Buffered** |
| ACKs | Individual | **Cumulative** | Individual (+ NAK) |
| Timers | 1 | 1 (oldest) | **One per frame** |
| On loss, resend | That frame | **That frame and all after it** | **Only that frame** |
| Efficiency (large a) | Poor | Good | Best (especially on noisy links) |
| Complexity / buffers | Lowest | Low | Highest |

---

## 8. Piggybacking

In two-way communication, instead of sending a separate ACK frame, attach the ACK to an outgoing **data** frame going the other way. Saves bandwidth. If there's no data to send for a while, a separate ACK is sent after a timeout.

---

## 9. A full worked numerical

**Problem:** A 10 Mbps satellite link has a one-way propagation delay of 270 ms. Frames are 1250 bytes. Find (a) Stop-and-Wait efficiency, (b) window for 100% utilisation, (c) sequence bits for GBN and SR, (d) throughput with GBN and window 127.

- Frame = 10,000 bits. Tt = 10,000 / 10⁷ = **1 ms**.
- a = 270 / 1 = 270.
- (a) η = 1/(1 + 540) = 1/541 ≈ **0.18%**.
- (b) W = 1 + 540 = **541**.
- (c) GBN: 2^m − 1 ≥ 541 → m = **10** (1023). SR: 2^(m−1) ≥ 541 → m − 1 = 10 → m = **11**.
- (d) η = 127/541 ≈ 23.5%. Throughput ≈ 0.235 × 10 Mbps ≈ **2.35 Mbps**.

---

## 10. Exam traps

1. a = Tp/Tt. Stop-and-Wait η = 1/(1 + 2a).
2. Window for 100%: **1 + 2a**. With smaller W: η = W/(1 + 2a).
3. If the question gives RTT, then 2Tp = RTT.
4. GBN: sender ≤ 2^m − 1, receiver 1, cumulative ACKs, resend from the lost frame onward.
5. SR: both windows ≤ 2^(m−1), per-frame timers, resend only the lost frame.
6. Stop-and-Wait sequence numbers: 1 bit.
7. Throughput = η × bandwidth.
8. Read whether the question asks for **bits** needed for sequence numbers or the **window size**.

---

## 11. Practice questions

**Q1.** Tt = 2 ms, Tp = 9 ms. Stop-and-Wait efficiency?
(a) 10% (b) 18.2% (c) 22.2% (d) 50%

**Answer: (a).** a = 4.5 → 1/(1 + 9) = 10%.

---

**Q2.** For the link in Q1, window size for 100% utilisation?
(a) 9 (b) 10 (c) 5 (d) 11

**Answer: (b).** 1 + 2a = 10.

---

**Q3.** With a 4-bit sequence number, maximum sender windows for GBN and SR?
(a) 15 and 8 (b) 16 and 8 (c) 15 and 15 (d) 8 and 8

**Answer: (a).**

---

**Q4.** GBN, window 7. Frames 0 to 6 sent; frame 3 is lost; the rest arrive. How many frames are retransmitted?
(a) 1 (b) 3 (c) 4 (d) 7

**Answer: (c).** Frames 3, 4, 5, 6.

---

**Q5.** Same situation with Selective Repeat?
(a) 1 (b) 3 (c) 4 (d) 0

**Answer: (a).**

---

**Q6.** A 1 Mbps link, 1000-bit frames, RTT = 50 ms. What window gives maximum throughput?
(a) 50 (b) 51 (c) 25 (d) 101

**Answer: (b).** Tt = 1 ms. 2Tp = 50 ms → 2a = 50 → W = 51.

---

**Q7.** In Q6, how many sequence-number bits does Selective Repeat need?
(a) 6 (b) 7 (c) 5 (d) 8

**Answer: (b).** 2^(m−1) ≥ 51 → m − 1 = 6 → m = 7.

---

**Q8.** A sliding window protocol uses W = 5 on a link with a = 7. Efficiency?
(a) 33.3% (b) 71.4% (c) 50% (d) 100%

**Answer: (a).** 1 + 2a = 15. Since W = 5 < 15, η = 5/15 = 33.3%. (Option (b), 5/7, is the common mistake of dividing by a instead of 1 + 2a.)

---

**Q9.** Which protocol needs the most buffer space at the receiver?
(a) Stop-and-Wait (b) GBN (c) Selective Repeat (d) all equal

**Answer: (c).**

---

**Q10.** Which statement about GBN is TRUE?
(a) The receiver buffers out-of-order frames (b) It uses cumulative acknowledgements (c) It needs a timer per frame (d) Its receiver window equals its sender window

**Answer: (b).**

---

**Q11.** Stop-and-Wait on a 4 Mbps link with 1000-byte frames achieves 50% efficiency. One-way propagation delay?
(a) 1 ms (b) 2 ms (c) 0.5 ms (d) 4 ms

**Answer: (a).** Tt = 8000/4×10⁶ = 2 ms. 1/(1 + 2a) = 0.5 → a = 0.5 → Tp = 1 ms.

---

**Q12.** Why does SR limit its window to 2^(m−1)?
(a) To save timers (b) To ensure old and new receiver windows never overlap, so a retransmitted old frame isn't accepted as new (c) To use cumulative ACKs (d) Because ACKs are piggybacked

**Answer: (b).**

---

**Q13.** Attaching acknowledgements to outgoing data frames is called:
(a) pipelining (b) piggybacking (c) multiplexing (d) bit stuffing

**Answer: (b).**

---

**Q14.** A channel has bandwidth 64 kbps and propagation delay 20 ms. For Stop-and-Wait to reach at least 50% efficiency, the minimum frame size is:
(a) 1280 bits (b) 2560 bits (c) 640 bits (d) 5120 bits

**Answer: (b).** Need 1/(1 + 2a) ≥ 0.5 → a ≤ 0.5 → Tt ≥ 2Tp = 40 ms → frame ≥ 64,000 × 0.04 = 2560 bits.
