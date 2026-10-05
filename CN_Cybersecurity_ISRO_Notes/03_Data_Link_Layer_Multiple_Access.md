# 03. Data Link Layer: Multiple Access Control (ALOHA, CSMA, CSMA/CD, CSMA/CA)

> **The problem.** Many stations share one medium (a cable, or the air). If two talk at once, their signals collide and both are garbled. **Multiple access protocols** are the etiquette that decides who talks when. Think of a group conversation: either anyone can speak and sometimes people talk over each other (random access), or there's a moderator (controlled access), or everyone gets their own channel (channelization).

---

## 1. Where this lives: the two sublayers of the data link layer

- **LLC (Logical Link Control)** (upper): flow control, error control, interface to the network layer.
- **MAC (Media Access Control)** (lower): **who may use the shared medium**, and framing details specific to the medium (e.g. CSMA/CD for Ethernet).

Families of multiple-access protocols:

```
Multiple access
├── Random access (contention):   ALOHA, CSMA, CSMA/CD, CSMA/CA
├── Controlled access:            Reservation, Polling, Token passing
└── Channelization:               FDMA, TDMA, CDMA
```

---

## 2. Two delays you need

- **Tfr (= Tt), frame transmission time** = frame size / bandwidth.
- **Tp, propagation time** = distance / signal speed.

---

## 3. ALOHA (University of Hawaii, 1970s)

### 3.1 Pure ALOHA

Rule: **send whenever you have data.** If no ACK comes back in time, assume a collision, wait a **random back-off time**, and resend. Give up after **K_max** attempts (typically 15).

**Vulnerable time:** a frame from station A (sent at time t) collides with any frame started between **t − Tfr** and **t + Tfr**.

```
Pure ALOHA vulnerable time = 2 × Tfr
```

Picture: A's frame occupies [t, t + Tfr]. Any frame starting in (t − Tfr, t) overlaps A's start; any starting in (t, t + Tfr) overlaps A's end.

**Throughput:** let **G** = average number of frames generated per frame-time. Then

```
S (pure) = G × e^(−2G)          max = 1/(2e) ≈ 0.184 at G = 0.5
```

Only about **18.4%** of the channel is used for successful frames at best.

### 3.2 Slotted ALOHA

Divide time into **slots of length Tfr**. Stations may start sending **only at the beginning of a slot**. Now frames either overlap completely (same slot) or not at all.

```
Slotted ALOHA vulnerable time = Tfr
S (slotted) = G × e^(−G)        max = 1/e ≈ 0.368 at G = 1
```

**Double** pure ALOHA's maximum.

### 3.3 Worked example

Channel 200 kbps, frames of 200 bits → Tfr = 1 ms.

- **Vulnerable time:** pure 2 ms; slotted 1 ms.
- System produces **1000 frames/s** → G = 1000 × 1 ms = **1**.
  - Pure: S = 1 × e⁻² ≈ 0.135 → about **135** successful frames/s.
  - Slotted: S = 1 × e⁻¹ ≈ 0.368 → about **368** frames/s.
- **500 frames/s** → G = 0.5.
  - Pure: S = 0.5 × e⁻¹ ≈ 0.184 → **92** frames/s (the pure maximum).
  - Slotted: S = 0.5 × e^(−0.5) ≈ 0.303 → **151** frames/s.
- **250 frames/s** → G = 0.25.
  - Pure: S = 0.25 × e^(−0.5) ≈ 0.152 → **38** frames/s.
  - Slotted: S = 0.25 × e^(−0.25) ≈ 0.195 → **49** frames/s.

### 3.4 Binary exponential back-off

After the k-th collision, pick R randomly from {0, 1, ..., 2^k − 1} and wait R × (slot time). The range doubles each time, so repeated collisions spread stations out. (Ethernet caps k at 10 for the range and gives up after 16 attempts.)

---

## 4. CSMA: Carrier Sense Multiple Access

**Improvement:** **listen before you talk.** If the channel is busy, don't send.

But collisions still happen! Because of **propagation delay**, station B may sense the channel idle when A's signal simply hasn't reached it yet.

```
CSMA vulnerable time = Tp
```

(Much smaller than ALOHA's, since usually Tp << Tfr.)

> **Trap.** CSMA **reduces** collisions, it does **not eliminate** them.

### Persistence strategies (what to do when the channel is busy/idle)

| Strategy | If idle | If busy | Behaviour |
|---|---|---|---|
| **1-persistent** | Send immediately (probability 1) | Keep sensing continuously, send the moment it's idle | **Highest collision chance** (everyone waiting jumps at once). Used by Ethernet |
| **Non-persistent** | Send immediately | Wait a **random time**, then sense again | Fewer collisions, but **wasted idle time** (low efficiency) |
| **p-persistent** (slotted channels) | Send with probability p; with probability 1 − p wait one slot and check again | Wait until idle, then apply the rule | Balance of the two. Used in Wi-Fi-like systems |

---

## 5. CSMA/CD (Collision Detection): wired Ethernet

**Idea:** keep listening **while** transmitting. If a collision is detected, **stop immediately**, send a short **jam signal** (so everyone knows), back off, retry.

Collision is detected by **energy level**: idle (zero), one transmitter (normal), collision (abnormal, about double).

### 5.1 The minimum frame size rule

A station can only detect a collision **while it is still transmitting**. Worst case: two stations at opposite ends.

1. A starts sending at time 0.
2. Just before A's first bit reaches B (at time Tp − ε), B senses idle and starts sending.
3. The collision happens near B; the corrupted signal travels back to A, arriving at about **2Tp**.

So A must still be transmitting at 2Tp:

```
Tfr ≥ 2 × Tp
Minimum frame size = 2 × Tp × Bandwidth
```

**Classic Ethernet:** 10 Mbps, worst-case round trip (with repeaters) 51.2 µs → minimum frame = 10⁷ × 51.2 × 10⁻⁶ = **512 bits = 64 bytes**. That's why Ethernet frames are at least 64 bytes.

**Example:** 1 Gbps, 1 km cable, signal speed 2 × 10⁸ m/s. Tp = 1000/2×10⁸ = 5 µs. Min frame = 2 × 5 µs × 10⁹ = **10,000 bits = 1250 bytes**.

**Example (inverse):** 100 Mbps, minimum frame 64 bytes = 512 bits → Tfr = 5.12 µs → Tp ≤ 2.56 µs → with speed 2 × 10⁸ m/s, max cable length = **512 m**.

Note: as bandwidth increases (with the same minimum frame), the maximum length shrinks. That's why Gigabit Ethernet needed carrier extension.

### 5.2 Efficiency of CSMA/CD

```
η = 1 / (1 + 6.44a),     a = Tp / Tfr
```

Small a (short cables, big frames) → high efficiency.

Example: a = 0.1 → η = 1/1.644 ≈ **60.8%**.

---

## 6. CSMA/CA (Collision Avoidance): wireless (Wi-Fi, IEEE 802.11)

In wireless, a station **can't reliably detect collisions** while transmitting:
- its own signal drowns out the weak incoming one,
- the **hidden terminal** problem: A and C can both reach B but can't hear each other, so they don't know they're colliding at B.

So instead of detecting, **avoid** collisions:

1. **IFS (Interframe Space):** even when the channel is idle, wait an IFS first (a distant station may have just started). Shorter IFS = higher priority (SIFS for ACKs, DIFS for data).
2. **Contention window:** then wait a random number of slots (binary exponential back-off). If the channel becomes busy during the countdown, **pause** the timer (don't reset it), and resume when idle. So stations that have waited longer keep their advantage.
3. **Acknowledgement:** the receiver sends an **ACK**; no ACK means retransmit (since collisions can't be sensed).

Optional **RTS/CTS** handshake (Request To Send / Clear To Send) reserves the channel and solves the hidden terminal problem: everyone who hears the CTS stays quiet for the stated duration (the **NAV**, network allocation vector).

> **Trap.** CSMA/**CD** = **Detection** = **wired** Ethernet. CSMA/**CA** = **Avoidance** = **wireless**.

---

## 7. Controlled access

Stations take turns, coordinated so there are **no collisions**.

### 7.1 Reservation

Time is split into intervals. Each interval starts with a **reservation frame** of N mini-slots (one per station). A station with data sets its mini-slot; then the reserved stations send their data frames in order.

### 7.2 Polling

One **primary** station controls N **secondaries** (all traffic goes via the primary).
- **Poll:** primary asks each secondary "anything to send?". The secondary replies with **data** or a **NAK** (nothing to send).
- **Select:** primary has data for a secondary; it sends SEL, waits for the secondary's ACK (ready), then sends.

Downside: single point of failure (primary), polling overhead.

### 7.3 Token passing

Stations form a **logical ring**. A special frame, the **token**, circulates. **Only the station holding the token may transmit**, for at most a **token holding time**, then it passes the token on.

Issues: token **loss** (must be regenerated by a monitor station), token **duplication**, a station failing to pass it on.

Efficiency of token ring with **early token release** (the token is released right after the frame is sent), N stations and a = Tp/Tt (Tp = ring latency): **η = 1/(1 + a/N)**. The key idea: efficiency drops as ring latency grows relative to frame time, and rises with more active stations sharing that latency.

---

## 8. Channelization

Split the channel's capacity among stations **permanently**.

| Method | Split by | Idea |
|---|---|---|
| **FDMA** | **Frequency** | Each station has its own frequency band all the time (guard bands between) |
| **TDMA** | **Time** | Each station uses the full band but only in its own time slot (needs synchronisation, guard times) |
| **CDMA** | **Code** | **All** stations transmit **simultaneously** over the **whole** band; each data bit is multiplied by a unique **orthogonal chip sequence**; the receiver extracts one station's data by multiplying by that station's code |

CDMA example (chips): station 1 code [+1 +1 +1 +1], station 2 code [+1 −1 +1 −1]. Orthogonal: their dot product is 0. If station 1 sends bit 1 (+1) and station 2 sends bit 0 (−1), the channel carries [+1 +1 +1 +1] + [−1 +1 −1 +1] = [0 2 0 2]. Receiver for station 1: [0 2 0 2] · [+1 +1 +1 +1] = 4, divide by 4 → **+1** (bit 1). Receiver for station 2: [0 2 0 2] · [+1 −1 +1 −1] = −4 → **−1** (bit 0). ✓

Chip sequences are generated using **Walsh tables**.

> **Trap.** CDMA uses **neither** separate frequencies **nor** separate time slots. Separation is purely by **code**.

---

## 9. Exam traps

1. Pure ALOHA vulnerable time **2Tfr**, max throughput **18.4%** (G = 0.5).
2. Slotted ALOHA vulnerable time **Tfr**, max **36.8%** (G = 1), double pure.
3. CSMA reduces, not eliminates, collisions. Vulnerable time = Tp.
4. 1-persistent: most collisions. Non-persistent: wastes idle time.
5. CSMA/CD: **Tfr ≥ 2Tp**, min frame = 2 × Tp × B. 10 Mbps Ethernet: 64 bytes.
6. CSMA/CD efficiency = 1/(1 + 6.44a).
7. CSMA/CA: IFS, contention window (timer pauses), ACK. RTS/CTS for hidden terminals.
8. Polling: NAK means "nothing to send".
9. CDMA separates by code; codes are orthogonal.

---

## 10. Practice questions

**Q1.** A pure ALOHA network transmits 200-bit frames on a 200 kbps channel. Vulnerable time?
(a) 1 ms (b) 2 ms (c) 0.5 ms (d) 4 ms

**Answer: (b).** Tfr = 1 ms, vulnerable = 2 ms.

---

**Q2.** Maximum throughput of slotted ALOHA is achieved when G equals:
(a) 0.5 (b) 1 (c) 2 (d) 0.368

**Answer: (b).**

---

**Q3.** In slotted ALOHA with G = 2, the throughput S is about:
(a) 0.27 (b) 0.37 (c) 0.135 (d) 0.018

**Answer: (a).** S = 2e⁻² = 2 × 0.135 = 0.27.

---

**Q4.** A CSMA/CD network runs at 10 Mbps and the maximum propagation delay is 25.6 µs. Minimum frame size?
(a) 256 bits (b) 512 bits (c) 1024 bits (d) 64 bits

**Answer: (b).** 2 × 25.6 µs × 10⁷ = 512 bits.

---

**Q5.** CSMA/CD at 1 Gbps on a 2 km cable, signal speed 2 × 10⁸ m/s. Minimum frame size in bytes?
(a) 1250 (b) 2500 (c) 10,000 (d) 20,000

**Answer: (b).** Tp = 10 µs. Min frame = 2 × 10 µs × 10⁹ = 20,000 bits = 2500 bytes.

---

**Q6.** Which protocol is used in wireless LANs because collisions can't be detected reliably?
(a) CSMA/CD (b) CSMA/CA (c) Pure ALOHA (d) Token passing

**Answer: (b).**

---

**Q7.** In CSMA/CA, if the channel becomes busy during back-off, the station:
(a) restarts the timer (b) pauses the timer (c) sends immediately (d) doubles the window

**Answer: (b).**

---

**Q8.** The hidden terminal problem is solved in 802.11 using:
(a) jam signals (b) RTS/CTS (c) token passing (d) Manchester encoding

**Answer: (b).**

---

**Q9.** Which persistence method has the highest probability of collision?
(a) 1-persistent (b) non-persistent (c) p-persistent with p = 0.1 (d) none

**Answer: (a).**

---

**Q10.** In polling, a secondary with no data replies with:
(a) ACK (b) NAK (c) SEL (d) token

**Answer: (b).**

---

**Q11.** CSMA/CD efficiency with Tp = 5 µs and Tfr = 50 µs is about:
(a) 61% (b) 91% (c) 39% (d) 75%

**Answer: (a).** a = 0.1 → 1/(1 + 0.644) ≈ 0.608.

---

**Q12.** Two CDMA stations use codes [+1, +1] and [+1, −1]. Station 1 sends bit 1, station 2 sends bit 1. The combined channel signal is:
(a) [2, 0] (b) [0, 2] (c) [1, 1] (d) [2, 2]

**Answer: (a).** [+1, +1] + [+1, −1] = [2, 0]. (Decoding for station 2: [2, 0] · [+1, −1] = 2, divide by 2 → +1 → bit 1 ✓.)

---

**Q13.** After the 3rd consecutive collision, binary exponential back-off chooses R from:
(a) {0, 1, 2} (b) {0, ..., 7} (c) {0, ..., 3} (d) {0, ..., 15}

**Answer: (b).** 0 to 2³ − 1.

---

**Q14.** Which channelization technique lets all users transmit at the same time over the entire bandwidth?
(a) FDMA (b) TDMA (c) CDMA (d) Polling

**Answer: (c).**

---

**Practice questions:** [7.03 Multiple Access ALOHA CSMA](../ISRO_CS_Question_Bank/07_Computer_Networks/7.03_Multiple_Access_ALOHA_CSMA.md)
