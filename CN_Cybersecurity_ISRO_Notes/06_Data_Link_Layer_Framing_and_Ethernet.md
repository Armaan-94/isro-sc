# 06. Data Link Layer: Framing, Ethernet and PPP

> **Framing in one sentence.** The physical layer delivers an endless stream of bits; the data link layer must cut that stream into meaningful **frames**, so the receiver knows where each frame starts and ends. Ethernet is the most important real-world data link protocol, and its frame format is a favourite exam topic.

---

## 1. Why framing?

Imagine reading a book with no spaces, punctuation or page breaks. Framing adds those boundaries. Each frame carries:
- a **header** (addresses, type/length, control),
- the **payload** (network-layer packet),
- a **trailer** (usually the CRC for error detection).

---

## 2. Framing methods

### 2.1 Fixed-size framing

Every frame has the same size, so the size itself marks boundaries. No delimiters needed. Example: **ATM cells** (53 bytes).

### 2.2 Variable-size framing

Need to mark where each frame begins and ends.

#### Character (byte) count

The header states the number of bytes in the frame. Fragile: if the count gets corrupted, the receiver loses track of **every** subsequent frame boundary. Rarely used alone.

#### Character-oriented framing with byte stuffing

Each frame starts and ends with a special **flag byte**. Problem: what if the data itself contains a byte equal to the flag?

**Byte stuffing:** whenever the flag byte (or the escape byte itself) appears in the data, insert an **ESC** byte before it. The receiver removes any ESC and treats the next byte as data.

```
Data:    A  FLAG  B  ESC  C
Sent:    FLAG | A ESC FLAG B ESC ESC C | FLAG
```

Used by PPP (with flag 0x7E and escape 0x7D).

#### Bit-oriented framing with bit stuffing

The flag is the bit pattern **01111110** (0x7E). To stop this pattern from appearing inside the data:

**Bit stuffing rule:** after **five consecutive 1s** in the data, the sender **inserts a 0**. The receiver, upon seeing five 1s followed by a 0, **removes** that 0. If it sees five 1s followed by a 1 (i.e. six 1s), it's a flag (or an error/abort).

**Example:**
- Data: `0001111111001111101000`
- Find runs of 1s: `000` `1111111` `00` `11111` `01000`.
- Stuffed: `000` `11111`**0**`11` `00` `11111`**0** `01000`
- Sent: `000111110110011111001000`

Note: the stuffed 0 is inserted after **every** five 1s, even if the next data bit is already 0 (the second case above).

Used by HDLC.

> **Trap.** Byte stuffing adds an extra **byte** (ESC). Bit stuffing adds an extra **bit** (0) after five 1s.

---

## 3. Ethernet (IEEE 802.3)

### 3.1 Background

- Developed at Xerox PARC (Metcalfe, 1970s); standardised as IEEE 802.3 (1983).
- Original 10BASE5: thick coaxial cable, **bus** topology, **10 Mbps**, **Manchester** encoding, **CSMA/CD**.
- Modern Ethernet: twisted pair or fibre, **star** topology with **switches**, full-duplex (no collisions, so CSMA/CD isn't actually needed on switched full-duplex links).
- Ethernet provides a **connectionless, unreliable** service: no ACKs. Damaged frames (bad CRC) are **silently dropped**. Reliability is left to higher layers (TCP).

Naming: **10BASE-T** = 10 Mbps, baseband, twisted pair. 100BASE-TX (Fast Ethernet), 1000BASE-T (Gigabit), 10GBASE-...

### 3.2 Frame format

```
| Preamble | SFD | Dest MAC | Src MAC | Type/Length | Data + Pad | CRC |
|    7     |  1  |    6     |    6    |      2      |  46-1500   |  4  |   bytes
```

| Field | Size | Purpose |
|---|---|---|
| **Preamble** | 7 bytes | Alternating 10101010... lets the receiver **synchronise** its clock. Added by the physical layer |
| **SFD** (start frame delimiter) | 1 byte | **10101011**: the final "11" says "the frame starts now" |
| **Destination address** | 6 bytes | MAC of receiver |
| **Source address** | 6 bytes | MAC of sender |
| **Type / Length** | 2 bytes | Ethernet II: protocol type (0x0800 = IPv4, 0x0806 = ARP, 0x86DD = IPv6). 802.3: length of data |
| **Data** | **46 to 1500 bytes** | The payload. If < 46 bytes, **padding** is added |
| **CRC** | 4 bytes | CRC-32 over addresses, type, data. Placed **last** because it must be computed over everything before it |

### 3.3 Minimum and maximum frame size

Counting from destination address to CRC (excluding preamble and SFD):

- **Minimum = 6 + 6 + 2 + 46 + 4 = 64 bytes** (512 bits).
- **Maximum = 6 + 6 + 2 + 1500 + 4 = 1518 bytes** (1522 with an 802.1Q VLAN tag).

Why a **minimum**? For **CSMA/CD**: a frame must still be in transmission when news of a collision returns from the far end (Tfr ≥ 2Tp). At 10 Mbps with the maximum network span, that's 512 bits = **64 bytes**. ([Chapter 03](03_Data_Link_Layer_Multiple_Access.md).)

Why a **maximum**? Historically, (1) memory (buffers) was expensive, and (2) to stop one station from hogging the shared medium for too long.

The data field's max, **1500 bytes**, is the Ethernet **MTU** used by IP ([Chapter 07](07_Network_Layer_IPv4_Protocol.md)).

**Pad example:** an IP packet of 30 bytes needs **16 bytes of padding** to reach the 46-byte minimum.

### 3.4 Efficiency of Ethernet framing

With 1500 bytes of data, overhead = 18 bytes (header + CRC) + 8 (preamble + SFD) + 12 (inter-frame gap) = 38 bytes. Efficiency ≈ 1500/1538 ≈ **97.5%**.

### 3.5 MAC addresses

**48 bits** (6 bytes), written in hex like **4A:30:10:21:10:1A**.
- First 24 bits: **OUI** (Organisationally Unique Identifier), assigned to the manufacturer.
- Last 24 bits: assigned by the manufacturer.

Types, decided by the **least significant bit of the first byte**:

| Type | Rule | Meaning |
|---|---|---|
| **Unicast** | LSB of first byte = **0** | One station |
| **Multicast** | LSB of first byte = **1** | A group |
| **Broadcast** | All 48 bits = 1: **FF:FF:FF:FF:FF:FF** | Every station on the LAN |

Example: **4A**:30:10:21:10:1A → 4A = 0100 101**0** → LSB 0 → **unicast**. **47**:20:1B:2E:08:EE → 47 = 0100 011**1** → **multicast**.

The **source address is always unicast**.

The second-least-significant bit of the first byte marks **locally administered** (1) vs universally administered (0) addresses.

### 3.6 Transmission order

Ethernet sends bytes left to right, but **each byte least-significant bit first**. That's why the "first bit on the wire" is the LSB of the first byte, which is exactly the unicast/multicast bit.

### 3.7 Manchester encoding in Ethernet

Each bit has a **transition in the middle**: in IEEE 802.3, **low-to-high = 1, high-to-low = 0**. This guarantees regular transitions for **clock recovery** (self-clocking), at the cost of **double the signalling rate** (10 Mbps data needs 20 Mbaud).

Fast Ethernet moved to 4B/5B + MLT-3; Gigabit uses 8B/10B or PAM-5.

---

## 4. Switched Ethernet and VLANs (brief)

- A **switch** learns which MAC addresses are on which port (by reading **source** addresses of incoming frames) and forwards each frame **only to the right port** (filtering). Unknown destination → **flood** to all ports except the incoming one.
- Each switch port is its own **collision domain**; with full-duplex links, collisions disappear entirely.
- The whole switched LAN is still **one broadcast domain** (broadcasts reach everyone) unless split into **VLANs** (Virtual LANs), which logically separate broadcast domains on the same physical switches. VLAN tags (IEEE 802.1Q) add **4 bytes** to the frame.

---

## 5. Other data link protocols

### 5.1 HDLC (High-level Data Link Control)

Bit-oriented, uses the 01111110 flag and **bit stuffing**. Three frame types:
- **I-frames** (information: user data + piggybacked ACKs),
- **S-frames** (supervisory: flow and error control, e.g. RR, RNR, REJ, SREJ),
- **U-frames** (unnumbered: link management).

### 5.2 PPP (Point-to-Point Protocol)

For direct links between two nodes (dial-up, DSL, serial lines). Byte-oriented (byte stuffing with 0x7E flag, 0x7D escape).

- Encapsulates network-layer packets (IP and others).
- **LCP (Link Control Protocol):** sets up, configures and tests the link.
- **NCP (Network Control Protocols),** e.g. IPCP: configure network-layer settings like the IP address.
- **Authentication:** **PAP** (Password Authentication Protocol: password sent in **plain text**, two-way handshake) and **CHAP** (Challenge Handshake: three-way handshake, the password is **never sent**; a hash of a challenge is sent instead; more secure, can re-challenge periodically).
- No flow control, limited error control (CRC detects, no retransmission).
- Replaced the older **SLIP** (which had no error detection or authentication).
- **PPPoE** carries PPP over Ethernet (common for broadband).

---

## 6. Exam traps

1. Bit stuffing: insert 0 after **five** consecutive 1s (flag = 01111110).
2. Byte stuffing: insert ESC before a flag/ESC byte in data.
3. Ethernet min frame **64 bytes**, max **1518 bytes**, data **46 to 1500**.
4. Preamble 7 bytes + SFD 1 byte are **not** counted in the 64-byte minimum.
5. CRC is the **last** field.
6. LSB of first byte: 0 unicast, 1 multicast; all 1s broadcast. Source is always unicast.
7. Ethernet is connectionless and unreliable (no ACKs).
8. PAP sends passwords in clear text; CHAP uses a challenge-response.
9. Switch: separate collision domain per port, one broadcast domain (unless VLANs).

---

## 7. Practice questions

**Q1.** Bit-stuff the data 011111111111110.
(a) 011111111111110 (b) 01111101111101110 (c) 011111011111011110 (d) 0111110111111110

**Answer: (b).** The data is a 0, then thirteen 1s, then a 0 (15 bits).
- After the first five 1s, insert a 0.
- After the next five 1s, insert another 0.
- The last three 1s don't reach five, so nothing more is inserted.

Result: `0` `11111` **`0`** `11111` **`0`** `111` `0` = **01111101111101110** (17 bits: 15 data + 2 stuffed). Option (c) wrongly stuffs a third time; option (d) stuffs only once.

---

**Q2.** Minimum Ethernet frame size (excluding preamble and SFD)?
(a) 46 bytes (b) 64 bytes (c) 72 bytes (d) 1518 bytes

**Answer: (b).**

---

**Q3.** An IP packet of 20 bytes is sent in an Ethernet frame. How many padding bytes are added?
(a) 0 (b) 20 (c) 26 (d) 44

**Answer: (c).** 46 − 20 = 26.

---

**Q4.** MAC address 4B:30:10:21:10:1A is:
(a) unicast (b) multicast (c) broadcast (d) invalid

**Answer: (b).** 4B = 0100 1011; LSB = 1.

---

**Q5.** The SFD field in Ethernet is:
(a) 10101010 (b) 10101011 (c) 01111110 (d) 11111111

**Answer: (b).**

---

**Q6.** Why is the CRC field at the end of the Ethernet frame?
(a) It's the smallest field (b) It's computed over the preceding fields, which must be known first (c) Switches read it first (d) Convention only

**Answer: (b).**

---

**Q7.** Which PPP authentication protocol never sends the password over the link?
(a) PAP (b) CHAP (c) LCP (d) NCP

**Answer: (b).**

---

**Q8.** HDLC frames used for flow and error control without user data are:
(a) I-frames (b) S-frames (c) U-frames (d) control frames

**Answer: (b).**

---

**Q9.** In a switched LAN with 8 ports (no VLANs), how many collision domains and broadcast domains?
(a) 1 and 1 (b) 8 and 1 (c) 8 and 8 (d) 1 and 8

**Answer: (b).**

---

**Q10.** After unstuffing, the received bit sequence 0111110110 becomes:
(a) 011111110 (b) 011111010 (c) 0111110110 (d) 01111110

**Answer: (a).** After five 1s (positions 2 to 6), the next bit 0 is a stuffed bit: remove it. 0 11111 [0] 110 → 0 11111 110 = 011111110.

---

**Practice questions:** [7.06 Framing and Ethernet](../ISRO_CS_Question_Bank/07_Computer_Networks/7.06_Framing_and_Ethernet.md)
