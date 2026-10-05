# 10. Transport Layer: Services, Ports and TCP

> **The gap the transport layer fills.** IP delivers a packet to the right **computer**. But that computer is running a browser, an email client, a music app and a game at once. Which **program** should get the data? And IP may lose or reorder packets; who fixes that? The transport layer answers both: **ports** for process-to-process delivery, and **TCP** for reliability.

---

## 1. What the transport layer does

| Layer | Delivery |
|---|---|
| Data link | **Node to node** (one hop) |
| Network | **Host to host** (source computer to destination computer) |
| **Transport** | **Process to process** (application to application) |

Services:
- **Multiplexing / demultiplexing** using **port numbers**.
- **Segmentation** of application messages and **reassembly**.
- (TCP) **connection management**, **reliable in-order delivery**, **end-to-end flow control**, **error control**, **congestion control**.

Note: error and flow control appear at both the data link layer (per link) and the transport layer (end to end). A router can corrupt or drop data even if every link was perfect, so end-to-end checks are still needed.

### The three Internet transport protocols

| | UDP | TCP | SCTP |
|---|---|---|---|
| Connection | Connectionless | Connection-oriented | Connection-oriented (association) |
| Reliability | No | Yes | Yes |
| Unit | Message (datagram) | **Byte stream** | Message, with **multiple streams** |
| Extra | Minimal | Flow + congestion control | Multihoming, multistreaming |

---

## 2. Ports and sockets

- **Port number:** **16 bits** (0 to 65,535). Identifies a process on a host.
- **Socket address** = **IP address + port**, e.g. 203.0.113.5:443.
- A **TCP connection** is uniquely identified by the **pair of sockets**: (client IP, client port, server IP, server port) plus the protocol.

| Range | Name | Notes |
|---|---|---|
| **0 to 1023** | **Well-known** | Assigned by IANA to standard services |
| **1024 to 49,151** | **Registered** | Can be registered with IANA |
| **49,152 to 65,535** | **Dynamic / ephemeral** | Picked temporarily by clients |

Common well-known ports (learn these):

| Port | Service | Transport |
|---|---|---|
| 20, 21 | FTP data, FTP control | TCP |
| 22 | SSH | TCP |
| 23 | Telnet | TCP |
| 25 | SMTP | TCP |
| 53 | DNS | **UDP** (and TCP for zone transfers/large replies) |
| 67, 68 | DHCP server, client | UDP |
| 69 | TFTP | UDP |
| 80 | HTTP | TCP |
| 110 | POP3 | TCP |
| 143 | IMAP | TCP |
| 161, 162 | SNMP, SNMP trap | UDP |
| 179 | BGP | TCP |
| 443 | HTTPS | TCP |
| 520 | RIP | UDP |

---

## 3. TCP: the big picture

TCP provides:
- **Connection-oriented** service (handshake before data).
- **Reliable**: lost or corrupted data is retransmitted.
- **In-order** delivery.
- **Full-duplex**: data flows both ways at once.
- **Byte-stream**: TCP doesn't preserve message boundaries; it numbers **bytes**, not messages.
- **Flow control** (don't swamp the receiver) and **congestion control** (don't swamp the network).

> **Trap.** TCP numbers **bytes**. (IP's identification numbers packets; HDLC counts frames.)

---

## 4. The TCP header

```
 0                   16                  31
+--------------------+--------------------+
|    Source port     |  Destination port  |
+--------------------+--------------------+
|            Sequence number              |
+-----------------------------------------+
|         Acknowledgment number           |
+----+------+--------+--------------------+
|HLEN| Rsvd | Flags  |    Window size     |
+----+------+--------+--------------------+
|     Checksum       |   Urgent pointer   |
+--------------------+--------------------+
|        Options (0 to 40 bytes)          |
+-----------------------------------------+
```

| Field | Bits | Meaning |
|---|---|---|
| Source / destination port | 16 each | Processes |
| **Sequence number** | 32 | Number of the **first data byte** in this segment |
| **Acknowledgment number** | 32 | The **next byte expected** from the other side (cumulative) |
| **HLEN** (data offset) | 4 | Header length in **4-byte words**: 5 to 15 → **20 to 60 bytes** |
| Reserved | 6 (or 3 + more flags in newer RFCs) | |
| **Flags** | 6 | **URG, ACK, PSH, RST, SYN, FIN** |
| **Window** | 16 | Receiver's free buffer space (**rwnd**), in bytes. Max **65,535** without scaling |
| **Checksum** | 16 | Over header + data + a **pseudo-header** (source IP, destination IP, protocol = **6**, TCP length). **Mandatory** |
| Urgent pointer | 16 | Valid if URG set: where urgent data ends |
| Options | 0 to 40 bytes | MSS, **window scale**, SACK, timestamps |

### Flags

| Flag | Meaning |
|---|---|
| **SYN** | Synchronise sequence numbers: start a connection |
| **ACK** | The acknowledgment number is valid |
| **FIN** | Sender has finished sending |
| **RST** | Reset (abort) the connection |
| **PSH** | Push: deliver data to the application immediately (e.g. interactive/chat) |
| **URG** | Urgent data present |

### Window scaling

16 bits allow only 64 KB in flight, too small for fast long links. The **window scale option** shifts the window left by up to 14 bits, allowing about **1 GB** (2³⁰ bytes).

**Max throughput limited by window:** throughput ≤ window / RTT. With 65,535 bytes and RTT 100 ms: 655,350 B/s ≈ **5.24 Mbps**, no matter how fast the link is.

---

## 5. Sequence and acknowledgment numbers

- The **Initial Sequence Number (ISN)** is chosen **randomly** at connection setup (not 0). Reasons: avoid confusing segments of an old connection with a new one using the same ports, and make sequence-prediction attacks harder.
- Sequence number of a segment = number of its **first data byte**.
- **Next segment's seq = this seq + bytes carried.**
- **ACK number = last byte received in order + 1** (the next byte expected). It's **cumulative**.
- **SYN and FIN each consume one sequence number** even though they carry no data. A pure ACK consumes **none**.

### Worked example 1

A 5000-byte file, first byte numbered **10001**, sent in five 1000-byte segments:

| Segment | Seq | Bytes |
|---|---|---|
| 1 | 10001 | 10001 to 11000 |
| 2 | 11001 | 11001 to 12000 |
| 3 | 12001 | 12001 to 13000 |
| 4 | 13001 | 13001 to 14000 |
| 5 | 14001 | 14001 to 15000 |

After receiving all, the receiver's ACK = **15001**.

If segment 3 is lost but 4 and 5 arrive, the receiver keeps sending **ACK 12001** (duplicate ACKs), since 12001 is the next byte it needs in order.

### Worked example 2 (handshake numbers)

Client ISN = 8000, server ISN = 15000.
1. Client → **SYN**, seq = 8000.
2. Server → **SYN + ACK**, seq = 15000, ack = **8001**.
3. Client → **ACK**, seq = 8001, ack = **15001**.
4. Client's first data byte is numbered **8001**.

### Sequence-number wrap-around

32-bit sequence numbers wrap after 2³² bytes. The **wrap-around time**:

```
WAT = 2^32 / bandwidth (in bytes per second)
```

If WAT is shorter than the maximum segment lifetime (often taken as **180 s**), an old delayed segment could be confused with a new one.

- 1 Gbps = 1.25 × 10⁸ B/s → WAT = 4.29 × 10⁹ / 1.25 × 10⁸ ≈ **34.4 s** < 180 s → **risk**.
- Solution: the **timestamp option** (PAWS: Protection Against Wrapped Sequences).

**Bits needed** to avoid wrap within lifetime LT: 2^bits ≥ bandwidth × LT. For 1 Gbps and 180 s: 1.25 × 10⁸ × 180 = 2.25 × 10¹⁰ → log₂ ≈ 34.4 → **35 bits**.

> **Trap.** Higher bandwidth → **shorter** WAT → **more** risk.

---

## 6. Connection establishment: the three-way handshake

```
Client                                  Server
  | --- SYN (seq = x) ------------------> |   client: SYN-SENT
  | <-- SYN + ACK (seq = y, ack = x+1) -- |   server: SYN-RCVD
  | --- ACK (seq = x+1, ack = y+1) -----> |   both: ESTABLISHED
```

Why three, not two? Both sides must **choose and confirm** an ISN. Two messages can't confirm both directions; three can.

### SYN flooding attack

An attacker sends many SYNs with **spoofed** source addresses. The server allocates state (a Transmission Control Block) for each and waits for the final ACK that never comes. Its backlog fills; real clients are denied (**DoS**).

Defences: **SYN cookies** (don't allocate state until the final ACK, encode state in the ISN), backlog limits, timeouts, firewalls.

---

## 7. Connection termination

Since TCP is full-duplex, **each direction closes separately**.

### Four-way close

```
A --- FIN -----> B      (A has no more data)
A <-- ACK ------ B      (half-close: B can still send)
A <-- FIN ------ B      (B is done too)
A --- ACK -----> B
```

### Three-way close

B combines its ACK and FIN into one segment: FIN, FIN+ACK, ACK.

### TIME-WAIT

The side that sends the **last ACK** waits **2 × MSL** (maximum segment lifetime) before fully closing, so that:
1. if the last ACK is lost, it can be resent when the peer retransmits its FIN,
2. old duplicate segments from this connection die out before the same port pair is reused.

### Half-close

One side stops sending but keeps receiving (after one FIN and its ACK).

### RST

Abort immediately: e.g. a segment arrives for a port with no listener, or something goes badly wrong.

---

## 8. TCP state diagram (key states)

Client: CLOSED → **SYN-SENT** → **ESTABLISHED** → FIN-WAIT-1 → FIN-WAIT-2 → **TIME-WAIT** → CLOSED.
Server: CLOSED → **LISTEN** → **SYN-RCVD** → **ESTABLISHED** → CLOSE-WAIT → LAST-ACK → CLOSED.

---

## 9. Reliability mechanisms

- **Checksum** on every segment; corrupted segments are discarded.
- **Cumulative ACKs.**
- **Retransmission timer (RTO):** if a segment isn't ACKed in time, resend it.
- **Fast retransmit:** if **3 duplicate ACKs** arrive (4 identical ACKs in total), resend the missing segment **immediately**, without waiting for the timeout.
- Out-of-order segments are **buffered** (not discarded) by most implementations; **SACK** (selective ACK option) tells the sender exactly which blocks arrived.

So TCP is a **hybrid**: cumulative ACKs like **Go-Back-N**, buffering of out-of-order data like **Selective Repeat**.

### Two loss signals and their strength

| Signal | What it means | Congestion severity |
|---|---|---|
| **Timeout** | Nothing came back at all | **Strong** |
| **3 duplicate ACKs** | Later segments are getting through; one was lost | **Mild** |

(This difference drives TCP congestion control in [Chapter 11](11_Transport_Layer_Congestion_Control_and_UDP.md).)

### When ACKs are generated (delayed ACK rules)

- In-order segment arrives, all earlier ones ACKed: wait up to ~500 ms (often 200 ms) for another segment, then ACK (delayed ACK).
- In-order segment arrives and another is waiting for an ACK: ACK both now (cumulative).
- Out-of-order segment arrives: send a **duplicate ACK** immediately.
- A segment fills a gap: ACK immediately.

---

## 10. Flow control (receiver window)

The receiver advertises **rwnd** (Window field) = free space in its buffer. The sender must keep

```
LastByteSent − LastByteAcked ≤ rwnd
```

If rwnd = 0, the sender stops and uses the **persist timer** to send small probes until the window reopens ([Chapter 11](11_Transport_Layer_Congestion_Control_and_UDP.md)).

---

## 11. Exam traps

1. Transport = **process to process**; network = host to host.
2. Ports: 16 bits; well-known 0 to 1023.
3. TCP header 20 to 60 bytes (HLEN × 4). UDP header fixed **8** bytes.
4. TCP numbers **bytes**; ISN is random.
5. SYN and FIN consume one sequence number each.
6. ACK = next byte expected (cumulative).
7. Three-way handshake; termination is usually four segments (or three if combined).
8. TIME-WAIT = 2 × MSL, on the side sending the final ACK.
9. 3 duplicate ACKs → fast retransmit; timeout → stronger signal.
10. WAT = 2³² / bandwidth (bytes/s): higher bandwidth, shorter WAT.
11. Window field max 65,535 bytes; window scale allows ~1 GB.
12. Pseudo-header protocol value for TCP = 6.

---

## 12. Practice questions

**Q1.** A TCP header has HLEN = 1000 (binary). Header size and options length?
(a) 32 bytes, 12 bytes (b) 8 bytes, 0 (c) 40 bytes, 20 bytes (d) 32 bytes, 20 bytes

**Answer: (a).** 8 × 4 = 32; options = 32 − 20 = 12.

---

**Q2.** A segment has seq = 2001 and carries 500 bytes. The next segment's sequence number is:
(a) 2500 (b) 2501 (c) 2502 (d) 2001

**Answer: (b).**

---

**Q3.** Client ISN = 1200. In the SYN+ACK from the server, the acknowledgment number is:
(a) 1200 (b) 1201 (c) 0 (d) the server's ISN + 1

**Answer: (b).** The SYN consumed sequence number 1200.

---

**Q4.** Bandwidth 100 Mbps. Sequence-number wrap-around time is about:
(a) 43 s (b) 344 s (c) 34 s (d) 4.3 s

**Answer: (b).** 100 Mbps = 1.25 × 10⁷ B/s. 2³² / 1.25 × 10⁷ ≈ 343.6 s.

---

**Q5.** Which flag aborts a connection immediately?
(a) FIN (b) RST (c) PSH (d) URG

**Answer: (b).**

---

**Q6.** A receiver has received bytes 1 to 1000 and 2001 to 3000. Its ACK number is:
(a) 1000 (b) 1001 (c) 3001 (d) 2001

**Answer: (b).** Cumulative: next in-order byte needed.

---

**Q7.** The maximum throughput of a TCP connection with a 64 KB window (65,536 bytes) and RTT of 20 ms is about:
(a) 3.28 MB/s (b) 26.2 Mbps (c) both (a) and (b) (d) 1.3 Mbps

**Answer: (c).** 65,536 / 0.02 = 3,276,800 B/s ≈ 3.28 MB/s = 26.2 Mbps.

---

**Q8.** Which port does DNS use primarily?
(a) TCP 53 (b) UDP 53 (c) UDP 67 (d) TCP 25

**Answer: (b).**

---

**Q9.** In TCP, fast retransmit is triggered by:
(a) a timeout (b) 3 duplicate ACKs (c) an RST (d) a zero window

**Answer: (b).**

---

**Q10.** Which side enters TIME-WAIT?
(a) The side that sends the final ACK (b) The server, always (c) The client, always (d) Neither side

**Answer: (a).** In a normal close, that's the side that **initiated** the close (sent the first FIN), which may be either client or server.

---

**Q11.** Purpose of a random ISN:
(a) faster handshakes (b) avoid confusion with old segments and resist sequence-prediction attacks (c) encryption (d) compression

**Answer: (b).**

---

**Q12.** A SYN flood attack exploits:
(a) the 4-way termination (b) half-open connections in the 3-way handshake (c) the urgent pointer (d) the window scale option

**Answer: (b).**

---

**Q13.** Maximum application data in one TCP segment carried in one IPv4 datagram (no options anywhere)?
(a) 65,535 (b) 65,515 (c) 65,495 (d) 65,507

**Answer: (c).** 65,535 − 20 (IP) − 20 (TCP).

---

**Q14.** TCP's acknowledgement scheme most resembles which ARQ protocol?
(a) Stop-and-Wait (b) Go-Back-N (cumulative ACKs), with Selective-Repeat-like buffering (c) pure Selective Repeat (d) none

**Answer: (b).**

---

**Practice questions:** [7.10 Transport Layer TCP](../ISRO_CS_Question_Bank/07_Computer_Networks/7.10_Transport_Layer_TCP.md)
