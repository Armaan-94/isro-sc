# 07. Network Layer: The IPv4 Protocol (Header, Fragmentation, ARP, ICMP)

> **The network layer's job.** Get a packet from the **source host** to the **destination host**, possibly across dozens of different networks, by deciding the path hop by hop. IPv4 does this with a simple promise: "best effort". It tries, but guarantees nothing. Everything else (reliability, order) is someone else's job.

---

## 1. Network layer services

- **Logical addressing:** IP addresses identify hosts globally (Chapter 08).
- **Routing:** choosing paths through routers (Chapter 09).
- **Forwarding:** at each router, moving a packet from an input to the correct output interface.
- **Packetizing:** encapsulating transport segments into datagrams.
- **Fragmentation and reassembly.**
- Some error reporting (via ICMP) and congestion signalling.

> **Trap.** The network layer does **not** provide **flow control** (that's transport and data link). It also doesn't guarantee delivery.

---

## 2. What IPv4 promises (and doesn't)

IPv4 is:
- **Connectionless:** no setup; each datagram is independent.
- **Unreliable / best-effort:** datagrams may be **lost, duplicated, corrupted, delayed or reordered**. No ACKs, no retransmission.
- **Datagram-based** (packet switched).

Reliability is added by **TCP** above it. This "dumb network, smart ends" design is why the Internet scaled so well.

---

## 3. The IPv4 header

```
 0               8               16              24             31
+-------+-------+---------------+-------------------------------+
|Version| HLEN  | Service type  |        Total length           |
+-------+-------+---------------+-----+-------------------------+
|        Identification         |Flags|   Fragment offset       |
+---------------+---------------+-----+-------------------------+
|     TTL       |   Protocol    |       Header checksum         |
+---------------+---------------+-------------------------------+
|                       Source IP address                       |
+---------------------------------------------------------------+
|                    Destination IP address                     |
+---------------------------------------------------------------+
|                Options (0 to 40 bytes) + padding              |
+---------------------------------------------------------------+
```

| Field | Bits | Meaning |
|---|---|---|
| **Version** | 4 | 4 for IPv4 |
| **HLEN** | 4 | Header length **in 4-byte words** |
| **Service type / DSCP** | 8 | Priority / QoS (differentiated services) |
| **Total length** | 16 | Header + data, **in bytes**. Max **65,535** |
| **Identification** | 16 | Same value for all fragments of one original datagram |
| **Flags** | 3 | Reserved, **DF** (Don't Fragment), **MF** (More Fragments) |
| **Fragment offset** | 13 | Position of this fragment's data in the original, **in units of 8 bytes** |
| **TTL** | 8 | Max hops remaining |
| **Protocol** | 8 | Which upper protocol: **ICMP = 1, IGMP = 2, TCP = 6, UDP = 17** |
| **Header checksum** | 16 | Covers the **header only** |
| **Source / destination address** | 32 each | IPv4 addresses |
| **Options** | 0 to 320 bits | Rarely used |

### 3.1 HLEN arithmetic

- Header length (bytes) = **HLEN × 4**.
- HLEN ranges **5 to 15**: header **20 to 60 bytes**.
- 20 bytes is the fixed part; up to **40 bytes** of options.

Examples:
- HLEN = 0110 (6) → header = **24 bytes** (4 bytes of options).
- HLEN = 1000 (8) → **32 bytes**.
- HLEN = 0101 (5) → **20 bytes**, no options.
- HLEN < 5 is **invalid**: the receiver discards the packet.

### 3.2 Total length arithmetic

- Data length = Total length − HLEN × 4.
- Example: total length 1500, HLEN 5 → data = 1500 − 20 = **1480 bytes**.
- Max data in a datagram = 65,535 − 20 = **65,515 bytes**.

### 3.3 TTL (Time To Live)

- Set by the sender (e.g. 64 or 128).
- **Every router decrements it by 1.**
- When it hits **0**, the router **discards** the datagram and sends an **ICMP Time Exceeded** message to the source.
- Purpose: stop packets looping forever if routing tables are wrong.
- **traceroute** exploits it: send packets with TTL = 1, 2, 3, ...; each router where TTL expires reveals itself.
- TTL = 1 keeps a packet on the local network.

### 3.4 Header checksum

- Covers **only the header**.
- **Recomputed at every router** because **TTL changes** at every hop (and options may change).
- The payload isn't covered because (1) recomputing over all data at every hop would be slow, and (2) TCP/UDP have their own checksums covering the data.

(IPv6 drops the header checksum entirely.)

---

## 4. Fragmentation

### 4.1 Why

Each link layer has a **Maximum Transmission Unit (MTU)**: the largest payload a frame can carry. Ethernet: **1500 bytes**. If a datagram is bigger than the next link's MTU, it must be split into **fragments**.

- Fragmentation can happen at the **source** or at **any router**.
- **Reassembly happens only at the final destination** (routers never reassemble, since fragments might take different paths).
- A fragment can be fragmented again on a later, smaller-MTU link.

### 4.2 The three fields involved

- **Identification:** copied to every fragment, so the destination knows which fragments belong together.
- **Flags:**
  - **DF = 1:** "don't fragment". If the datagram is too big, it's **dropped** and an ICMP "fragmentation needed" (Destination Unreachable) is sent back. (Used by **Path MTU discovery**.)
  - **MF = 1:** more fragments follow. **MF = 0** on the **last** fragment (or an unfragmented datagram).
- **Fragment offset:** the position of this fragment's **first data byte** in the original data, **divided by 8**.

Because the offset counts 8-byte units, **every fragment's data size except the last must be a multiple of 8**.

### 4.3 Recipe

1. Data per fragment = largest **multiple of 8** ≤ (MTU − header length).
2. Split the original **data** (not counting the original header) into chunks of that size.
3. Each fragment gets its own header (usually 20 bytes). Total length of each fragment = chunk + 20.
4. Offset = (starting byte of the chunk) / 8.
5. MF = 1 for all but the last.

### 4.4 Worked example 1 (classic)

A 4000-byte datagram (20-byte header + **3980** bytes data) must cross a link with MTU **1500**.

- Max data per fragment = 1500 − 20 = 1480 (a multiple of 8 ✓).
- 3980 = 1480 + 1480 + 1020.

| Fragment | Data bytes | Data size | Total length | Offset | MF |
|---|---|---|---|---|---|
| 1 | 0 to 1479 | 1480 | 1500 | 0 | 1 |
| 2 | 1480 to 2959 | 1480 | 1500 | 1480/8 = **185** | 1 |
| 3 | 2960 to 3979 | 1020 | 1040 | 2960/8 = **370** | 0 |

### 4.5 Worked example 2

Total length 2000 bytes (20 header + 1980 data), MTU 620.
- Max data = 620 − 20 = 600 (multiple of 8 ✓).
- 1980 = 600 + 600 + 600 + 180 → **4 fragments**.
- Offsets: 0, 75, 150, 225. Total lengths: 620, 620, 620, 200. MF: 1, 1, 1, 0.

### 4.6 Worked example 3 (reverse)

A fragment has offset **100**, HLEN **5**, total length **100**, MF = 1.
- Data length = 100 − 20 = 80 bytes.
- First data byte = 100 × 8 = **800**. Last = 800 + 80 − 1 = **879**.
- MF = 1 → not the last fragment. Offset ≠ 0 → not the first. It's a **middle** fragment.

### 4.7 MTU not a multiple of 8 trap

MTU 1000, header 20 → 980 available, but 980 isn't a multiple of 8 → use **976** bytes of data per fragment.

---

## 5. IP options (rare, but asked)

Up to 40 bytes:
- **No operation / End of option**: padding.
- **Record route**: routers add their addresses (max 9, since 40 bytes allow 9 four-byte addresses after a 3-byte option header).
- **Strict source route**: the sender lists the **exact** path; any deviation → discard.
- **Loose source route**: the listed routers must be visited, but others may be in between.
- **Timestamp**: routers record processing times.

---

## 6. Companion protocols at the network layer

### 6.1 ARP (Address Resolution Protocol)

**Problem:** IP knows the next hop's **IP address**, but the frame needs its **MAC address**.

**ARP: IP → MAC.**
1. Host checks its **ARP cache**.
2. If not found, it **broadcasts** an ARP request: "Who has 192.168.1.1? Tell 192.168.1.20."
3. The owner replies with a **unicast** ARP reply containing its MAC.
4. The answer is cached for a while.

ARP works only **within a local network**. To reach a remote host, a host ARPs for its **default gateway's** MAC, not the remote host's.

ARP messages are carried **directly in Ethernet frames** (EtherType 0x0806), not inside IP.

Variants:
- **Gratuitous ARP:** a host announces its own IP-MAC mapping (detects IP conflicts, updates caches).
- **Proxy ARP:** a router answers ARP requests on behalf of hosts on another network.
- **ARP spoofing/poisoning:** an attacker sends fake replies to redirect traffic (a man-in-the-middle attack).

### 6.2 RARP: MAC → IP

The **reverse**: a diskless machine knows its MAC (burned into the NIC) and asks a RARP server for its IP. Obsolete; replaced by **BOOTP** and then **DHCP**.

> **Trap.** **ARP: IP → MAC.** **RARP: MAC → IP.**

### 6.3 ICMP (Internet Control Message Protocol)

IP has no way to report problems. ICMP fills that gap. ICMP messages are **carried inside IP datagrams** (protocol = 1).

**Error-reporting messages:**

| Type | Message | When |
|---|---|---|
| 3 | **Destination unreachable** | Network/host/port unreachable; fragmentation needed but DF set |
| 11 | **Time exceeded** | TTL hit 0 (type 11 code 0), or reassembly timer expired (code 1) |
| 12 | **Parameter problem** | Bad header field |
| 5 | **Redirect** | Router tells host about a better first-hop router |
| 4 | Source quench | (Deprecated) congestion signal |

**Query messages:**

| Type | Message | Use |
|---|---|---|
| **8 / 0** | **Echo request / echo reply** | **ping** |
| 13 / 14 | Timestamp request/reply | RTT, clock sync |
| 9 / 10 | Router advertisement / solicitation | Discover routers |

**ICMP does not correct errors**; it only reports them to the **source**.

**No ICMP error message is generated for:**
- a datagram that itself carries an **ICMP error message** (prevents infinite loops),
- a **fragment other than the first**,
- a datagram with a **multicast** destination,
- a datagram with special addresses such as 127.0.0.0 or 0.0.0.0.

### 6.4 IGMP (Internet Group Management Protocol)

Hosts use IGMP to tell local routers which **multicast groups** they want to join or leave. Routers use it to know where to forward multicast traffic. Protocol number 2.

---

## 7. IPv6 in brief (bonus)

Designed because the 32-bit IPv4 space (~4.3 billion) ran out.

| | IPv4 | IPv6 |
|---|---|---|
| Address size | 32 bits | **128 bits** |
| Notation | Dotted decimal: 192.168.1.1 | Hex groups: 2001:0db8:0000:0000:0000:ff00:0042:8329, shortened to 2001:db8::ff00:42:8329 |
| Header | 20 to 60 bytes, variable | **40 bytes fixed** (+ optional extension headers) |
| Header checksum | Yes | **No** |
| Fragmentation | Source and routers | **Source only** (routers never fragment; use path MTU discovery) |
| Broadcast | Yes | **No** (uses multicast, anycast) |
| Configuration | Manual or DHCP | Also **stateless autoconfiguration (SLAAC)** |
| Security | Optional (IPsec added later) | IPsec designed in |
| New fields | | **Flow label** (QoS for flows), Hop limit (= TTL), Next header |

Shortening rules: drop leading zeros in each group; replace **one** run of all-zero groups with `::` (only once per address).

Transition mechanisms: **dual stack**, **tunneling** (IPv6 inside IPv4), **header translation** (NAT64).

---

## 8. Exam traps

1. IPv4: connectionless, unreliable, best-effort. No flow control at the network layer.
2. Header = HLEN × 4 bytes; HLEN 5 to 15 → 20 to 60 bytes.
3. Total length max 65,535 bytes.
4. Fragment offset in **8-byte units**; non-last fragment data must be a multiple of 8.
5. Only the **destination** reassembles.
6. DF = 1 and too big → drop + ICMP. MF = 0 marks the last fragment.
7. TTL decremented per hop; 0 → drop + ICMP Time Exceeded.
8. Header checksum covers only the header; recomputed per hop.
9. Protocol numbers: ICMP 1, IGMP 2, TCP 6, UDP 17.
10. ARP: IP → MAC (broadcast request, unicast reply). RARP: MAC → IP.
11. No ICMP error about an ICMP error, non-first fragments, multicast.

---

## 9. Practice questions

**Q1.** HLEN = 1111. Header length and maximum options length?
(a) 15 and 0 bytes (b) 60 and 40 bytes (c) 60 and 20 bytes (d) 30 and 10 bytes

**Answer: (b).** 15 × 4 = 60; options = 60 − 20 = 40.

---

**Q2.** Total length = 0x0028, HLEN = 5. How many data bytes?
(a) 40 (b) 20 (c) 28 (d) 0

**Answer: (b).** 0x28 = 40; 40 − 20 = 20.

---

**Q3.** A datagram of 5000 bytes (20-byte header) crosses a link with MTU 1500. Number of fragments?
(a) 3 (b) 4 (c) 5 (d) 2

**Answer: (b).** Data = 4980. Per fragment 1480. 4980/1480 = 3.36 → 4 fragments (1480, 1480, 1480, 540).

---

**Q4.** In Q3, the fragment offset of the last fragment?
(a) 444 (b) 555 (c) 370 (d) 185

**Answer: (b).** It starts at byte 3 × 1480 = 4440. 4440/8 = 555.

---

**Q5.** A fragment has offset 300 and total length 520 (HLEN = 5). Byte range of its data?
(a) 300 to 799 (b) 2400 to 2899 (c) 2400 to 2919 (d) 300 to 819

**Answer: (b).** First byte = 2400. Data = 500 bytes → last = 2899.

---

**Q6.** Which protocol maps an IP address to a MAC address?
(a) RARP (b) ARP (c) ICMP (d) DHCP

**Answer: (b).**

---

**Q7.** An ARP request is sent as a:
(a) unicast (b) broadcast (c) multicast (d) anycast

**Answer: (b).**

---

**Q8.** Which ICMP message does `ping` use?
(a) Destination unreachable (b) Echo request/reply (c) Time exceeded (d) Redirect

**Answer: (b).**

---

**Q9.** Which field ensures datagrams don't circulate forever?
(a) Identification (b) Checksum (c) TTL (d) Fragment offset

**Answer: (c).**

---

**Q10.** Why is the IPv4 header checksum recalculated at every router?
(a) The payload changes (b) The TTL field changes (c) IP addresses change (d) It isn't

**Answer: (b).**

---

**Q11.** The Protocol field value for UDP is:
(a) 1 (b) 6 (c) 17 (d) 2

**Answer: (c).**

---

**Q12.** Who reassembles IP fragments?
(a) Every router (b) The first router (c) The final destination (d) The source

**Answer: (c).**

---

**Q13.** Which is TRUE about IPv6?
(a) It has a header checksum (b) Routers fragment packets (c) The base header is 40 bytes (d) Addresses are 64 bits

**Answer: (c).**

---

**Q14.** MTU is 1000 bytes and headers are 20 bytes. Maximum data bytes in a non-last fragment?
(a) 980 (b) 976 (c) 1000 (d) 984

**Answer: (b).** 980 is not a multiple of 8; the largest multiple of 8 ≤ 980 is 976.

---

**Q15.** An ICMP error message is NOT generated for:
(a) a unicast datagram whose TTL expired (b) a datagram carrying an ICMP error message (c) an unreachable port (d) a datagram too big with DF set

**Answer: (b).**
