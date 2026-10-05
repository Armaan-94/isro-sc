# 13. Networking Hardware Devices

> **One question decides almost everything about a device:** at which **layer** does it work? That tells you what address it understands (none, MAC, or IP), whether it filters traffic intelligently, and which domains (collision, broadcast) it splits.

---

## 1. Two kinds of "domain"

- **Collision domain:** the set of devices whose transmissions can **collide** with each other (they share one half-duplex medium). Smaller is better.
- **Broadcast domain:** the set of devices that receive a **broadcast** frame (e.g. an ARP request) sent by any one of them. Too large means lots of broadcast noise.

---

## 2. Device by device

### 2.1 Repeater (Layer 1, Physical)

- **Regenerates** a weakened signal (rebuilds the clean bit pattern, not just amplifying noise too) to extend cable length.
- Knows nothing about frames or addresses.
- Splits **nothing**: same collision domain, same broadcast domain.

### 2.2 Hub (Layer 1)

- A **multiport repeater**: whatever arrives on one port is **copied out of every other port**.
- No MAC table, no filtering, half-duplex.
- **All ports form one collision domain** and one broadcast domain.
- Security risk: every host sees every frame.

### 2.3 Bridge (Layer 2, Data Link)

- Connects two (or a few) LAN **segments** of the same technology.
- **Learns** MAC addresses: reads the **source MAC** of incoming frames to build a table (MAC → port).
- **Filters:** forwards a frame only to the segment where the destination lives. Unknown destinations and broadcasts are **flooded**.
- **Separates collision domains** (one per segment), **not broadcast domains**.
- Uses the **Spanning Tree Protocol (STP)** to avoid loops when bridges are connected redundantly.

### 2.4 Switch (Layer 2)

- A fast, **multiport bridge** (hardware forwarding).
- **Every port is its own collision domain**; with full-duplex links, collisions vanish entirely.
- Still **one broadcast domain** for all ports, unless divided into **VLANs**.
- Forwarding methods: **store-and-forward** (receive whole frame, check CRC, then forward), **cut-through** (forward as soon as the destination address is read; faster, may forward bad frames), **fragment-free** (read the first 64 bytes).
- **Layer 3 switches** can also route between VLANs.

### 2.5 Router (Layer 3, Network)

- Connects **different networks**; forwards **packets** using **IP addresses** and a **routing table**.
- **Separates collision domains and broadcast domains**: it **does not forward broadcasts** (each interface is its own broadcast domain).
- Each interface has its own IP address (and MAC).
- Also performs NAT, packet filtering (ACLs), fragmentation, ICMP generation.

### 2.6 Gateway (any layer, often up to 7)

- Connects networks that use **different protocols**, translating between them (e.g. an email gateway, a VoIP-to-PSTN gateway).
- In everyday usage, "default gateway" just means the router a host sends off-subnet traffic to.

### 2.7 Others

- **Brouter:** bridge + router (routes routable protocols, bridges others).
- **Modem:** modulator-demodulator; converts digital data ↔ analog signals (Layer 1).
- **NIC:** network interface card; has the MAC address (Layer 1 and 2).
- **Access point (AP):** connects wireless clients to a wired LAN (Layer 2).
- **Firewall:** filters traffic by rules (Layers 3 to 7).
- **Load balancer, proxy:** Layers 4 to 7.

---

## 3. Summary table

| Device | Layer | Address used | Filters? | Splits collision domains? | Splits broadcast domains? |
|---|---|---|---|---|---|
| Repeater | 1 | None | No | No | No |
| Hub | 1 | None | No (floods all) | **No** | No |
| Bridge | 2 | MAC | Yes | **Yes** (per segment) | **No** |
| Switch | 2 | MAC | Yes | **Yes** (per port) | **No** (unless VLANs) |
| Router | 3 | IP | Yes | Yes | **Yes** |
| Gateway | Up to 7 | Protocol-dependent | Yes (translates) | Yes | Yes |

> **Most tested fact.** **Bridges and switches split collision domains but not broadcast domains. Only routers (and Layer 3 devices) split broadcast domains.** A **hub splits nothing.**

---

## 4. Counting domains (a common numerical)

**Method:**
- **Collision domains:** each **switch/bridge port** and each **router port** starts a separate collision domain. A **hub** and everything attached to it count as **one** collision domain (together with the switch port it's plugged into).
- **Broadcast domains:** count the **router interfaces** in use (each LAN behind a router interface is one broadcast domain). With no router, the whole switched network is **one**.

### Worked example

```
             Router R
            /        \
       Switch S1    Switch S2
      / |  |   \        |||||
    H1 H2 H3  Hub      5 hosts
              ||||
            4 hosts
```

**Collision domains:**
- S1's ports: H1, H2, H3, and the Hub (with its 4 hosts) → **4**.
- S2's ports to 5 hosts → **5**.
- R–S1 link → **1**. R–S2 link → **1**.
- Total = 4 + 5 + 1 + 1 = **11**.

**Broadcast domains:** the router has 2 interfaces in use → **2**.

### Quick variants

- 1 hub with 10 hosts: **1** collision domain, **1** broadcast domain.
- 1 switch with 10 hosts: **10** collision domains, **1** broadcast domain.
- 1 router with 3 interfaces, each to a hub of 5 hosts: **3** collision domains, **3** broadcast domains.

---

## 5. How a switch learns (backward learning)

1. A frame arrives on port 3 from MAC **A**. The switch records "A is on port 3".
2. Destination **B** is unknown → **flood** to all ports except 3.
3. B replies on port 7 → record "B is on port 7". Now traffic between A and B goes only through ports 3 and 7.
4. Entries **age out** (e.g. after 300 s) so the table follows moved devices.

---

## 6. Exam traps

1. Repeater **regenerates**, it doesn't merely amplify.
2. Hub = multiport repeater: one collision domain for all ports.
3. Switch = multiport bridge: collision domain per port, one broadcast domain.
4. Router separates broadcast domains.
5. Gateway = protocol conversion, can work at any layer.
6. Bridges/switches learn from **source** MACs and forward by **destination** MACs.
7. Store-and-forward checks the CRC; cut-through doesn't.

---

## 7. Practice questions

**Q1.** Which device operates only at the physical layer and floods every port?
(a) Bridge (b) Switch (c) Hub (d) Router

**Answer: (c).**

---

**Q2.** A 24-port switch with a host on every port (no VLANs). Collision and broadcast domains?
(a) 1 and 1 (b) 24 and 1 (c) 24 and 24 (d) 1 and 24

**Answer: (b).**

---

**Q3.** Which device separates broadcast domains?
(a) Hub (b) Repeater (c) Switch (d) Router

**Answer: (d).**

---

**Q4.** A bridge builds its table using:
(a) destination IP addresses (b) source MAC addresses of received frames (c) destination MAC addresses (d) port numbers

**Answer: (b).**

---

**Q5.** A router connects three LANs. Each LAN is a single hub with 6 hosts. Collision domains and broadcast domains?
(a) 3 and 3 (b) 18 and 3 (c) 3 and 1 (d) 21 and 3

**Answer: (a).** Each hub segment (including the router port) is one collision domain; each router interface is a broadcast domain.

---

**Q6.** Which switching method forwards frames as soon as the destination MAC address is read?
(a) Store-and-forward (b) Cut-through (c) Fragment-free (d) Flooding

**Answer: (b).**

---

**Q7.** Which device would you use to connect a TCP/IP network to a network running a completely different protocol suite?
(a) Bridge (b) Hub (c) Gateway (d) Repeater

**Answer: (c).**

---

**Q8.** What does a switch do with a frame whose destination MAC isn't in its table?
(a) Drops it (b) Floods it to all ports except the incoming one (c) Sends it to the router (d) Returns it

**Answer: (b).**

---

**Q9.** Which device works at the network layer?
(a) Switch (b) Bridge (c) Router (d) Hub

**Answer: (c).**

---

**Q10.** Network: router R with 3 interfaces. Interface 1 → switch with 4 hosts. Interface 2 → hub with 3 hosts. Interface 3 → single host directly. Total collision domains?
(a) 6 (b) 7 (c) 8 (d) 9

**Answer: (b).**
- Switch side: 4 host ports + the R–switch link = 5.
- Hub side: the hub with its 3 hosts and the router port = 1.
- Direct host link = 1.
- Total = 5 + 1 + 1 = **7**. Broadcast domains = 3.

---

**Practice questions:** [7.13 Networking Devices](../ISRO_CS_Question_Bank/07_Computer_Networks/7.13_Networking_Devices.md)
