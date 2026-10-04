# 01. Introduction to Computer Networks

> **A good mental model to carry through every chapter.** Sending data across a network is like sending a parcel across the world. Someone packs it (application), someone labels it with the final address (network layer), each local courier moves it one hop at a time (data link), and trucks and planes physically carry it (physical). Each layer only cares about its own job. That's the whole idea of layering.

---

## 1. What is a computer network?

A **computer network** is a set of **autonomous computers** interconnected by a single technology so they can **exchange information and share resources**.

"Autonomous" matters: no computer controls another (that would be a master-slave system, not a network).

The **Internet** is a **network of networks**: millions of independent networks connected using the TCP/IP protocol suite.

### Goals

- **Resource sharing** (printers, files, compute).
- **Communication** (email, chat, video calls).
- **Reliability** (alternate paths and backup copies).
- **Cost reduction** (share expensive hardware).
- **Scalability** (grow by adding nodes).
- **Remote access**.

### Applications

Email and VoIP, the World Wide Web, cloud and distributed computing, e-commerce, video on demand, online gaming, IoT.

---

## 2. Data communication basics

### Five components

1. **Message**: the information (text, audio, video).
2. **Sender**.
3. **Receiver**.
4. **Transmission medium**: the physical path (cable, air).
5. **Protocol**: the agreed set of rules (syntax, semantics, timing).

### Four characteristics of effective data communication

1. **Delivery**: to the correct destination.
2. **Accuracy**: without errors.
3. **Timeliness**: on time (critical for audio/video).
4. **Jitter**: variation in packet **arrival time**. Ideally low.

> **Trap.** **Jitter ≠ delay.** Jitter is the **variation** in delay. If packets arrive after 30 ms, 31 ms, 30 ms, jitter is small even though delay is 30 ms. If they arrive after 10, 50, 20 ms, jitter is large.

### Network criteria

**Performance** (throughput, delay), **reliability** (failure frequency, recovery time), **security** (protection from unauthorized access and damage).

---

## 3. A short history (asked as recall)

| Year | Event |
|---|---|
| **1969** | **ARPANET** (by ARPA/DARPA, US Dept of Defense). First packet-switched network. First link: **UCLA ↔ SRI**. Goal: a network that survives partial failure |
| 1971 | Email invented (Ray Tomlinson; the @ sign) |
| 1974 | Cerf and Kahn publish TCP/IP design |
| **1983** | ARPANET switches from NCP to **TCP/IP** (1 January 1983): considered the **birth of the Internet** |
| 1984 | DNS introduced |
| **1986** | **NSFNET**: high-speed academic backbone (replaced ARPANET's role; ARPANET retired in 1990) |
| **1991** | **World Wide Web** made public by **Tim Berners-Lee** (CERN): HTTP, HTML, URLs |
| 1995 | NSFNET decommissioned; Internet backbone becomes commercial |

> **Trap.** ARPANET ≠ the Internet. ARPANET was the ancestor; the Internet "begins" with TCP/IP in 1983. And the **Web** (1991) is an application **on** the Internet, not the Internet itself.

---

## 4. Transmission modes (direction of data flow)

| Mode | Direction | Example |
|---|---|---|
| **Simplex** | One way only | Keyboard to CPU, TV broadcast, radio |
| **Half-duplex** | Both ways, **but not at the same time** | Walkie-talkie |
| **Full-duplex** | Both ways **simultaneously** | Telephone call |

Full-duplex is like two simplex channels in opposite directions.

### Connection types

- **Point-to-point:** a dedicated link between exactly two devices.
- **Multipoint (multidrop):** one link shared by more than two devices.

---

## 5. Network topologies (physical layout)

### Mesh

Every device has a **dedicated point-to-point link to every other** device.

- Links needed: **n(n − 1)/2** (duplex links).
- Ports per device: **n − 1**.
- + Robust (one link failing doesn't affect others), private, easy fault identification, no traffic sharing.
- − Expensive, lots of cabling, hard to install.

Example: n = 5 → 5 × 4/2 = **10** links, each device needs **4** ports. n = 8 → **28** links.

### Star

Each device connects to a **central hub/switch**.

- Links: **n**.
- + Easy to install and reconfigure, one cable failure affects only one device, easy fault isolation.
- − **Hub is a single point of failure.**

### Bus

All devices share **one backbone cable** via drop lines and taps.

- + Least cabling, easy installation.
- − A break in the backbone stops **everything**; hard fault isolation; signal degrades with more taps; collisions.

### Ring

Each device connects to exactly two neighbours, forming a closed loop. Signals travel in one direction (often using a token).

- Links: **n**.
- + Easy to install and isolate faults.
- − One break can disable the whole ring (unless dual ring).

### Tree

A hierarchy of stars (a "star of stars", hybrid of bus and star).

- + Scalable; faults isolated to branches.
- − If the backbone/root fails, the network fails.

### Hybrid

A combination of two or more topologies (e.g. star-bus). Flexible but complex.

| Topology | Links | Main advantage | Main disadvantage |
|---|---|---|---|
| Mesh | n(n−1)/2 | Robust, private | Costly cabling |
| Star | n | Easy management | Hub failure kills all |
| Bus | 1 backbone | Cheap | Backbone break kills all |
| Ring | n | Simple, orderly | One break can kill all |
| Tree | n − 1 | Scalable | Root/backbone failure |

---

## 6. Types of networks by size

| | PAN | LAN | MAN | WAN |
|---|---|---|---|---|
| Range | ~10 m | Building/campus (up to a few km) | City (5 to 50 km) | Country/continent/world |
| Technology | Bluetooth (IEEE 802.15) | Ethernet (802.3), Wi-Fi (802.11) | Fibre, cable TV networks, WiMAX (802.16) | Leased lines, satellites, undersea cables, MPLS |
| Ownership | Personal | Private (one organisation) | ISP/government/consortium | Many owners |
| Speed | Moderate | **Very high** | High | Lower (than LAN), higher latency |
| Example | Phone + earbuds | Office/school network | City-wide cable network | The Internet |

---

## 7. Switching techniques

How do we move data through intermediate nodes?

### 7.1 Circuit switching

A **dedicated path** is set up end to end **before** data flows, and held for the whole session. Like an old landline call.

Three phases: **connection setup → data transfer → connection teardown**.

- + Guaranteed bandwidth, constant delay, **in-order** delivery.
- − **Setup delay**; reserved bandwidth is **wasted when idle** (silence in a call still holds the circuit).

### 7.2 Message switching

The **entire message** is stored at each node and forwarded when the next link is free (**store-and-forward**). Telegram, early email.

- − Huge delays, needs big storage at nodes, not for real-time.

### 7.3 Packet switching

Data is split into **packets**; each packet is routed independently and shares links with other traffic.

- **Datagram** approach (connectionless, like IP): each packet may take a different path, may arrive **out of order**, may be lost.
- **Virtual circuit** approach (connection-oriented, like X.25, Frame Relay, ATM, MPLS): a logical path is set up first; all packets follow it, in order, but bandwidth is still shared.

| | Circuit switching | Packet switching (datagram) |
|---|---|---|
| Path | Dedicated, reserved before transfer | Per packet, dynamic |
| Setup | Required | None |
| Bandwidth | Reserved (wasted if idle) | **Shared dynamically: better utilisation** |
| Delay | Setup delay, then constant | Variable (queuing) |
| Order | In order | Possibly out of order |
| Example | Telephone network | **Internet** |

### Store-and-forward delay (useful numerical)

Sending one packet of L bits over N links each of rate R (store-and-forward at each router): total transmission delay = **N × L/R** (ignoring propagation and queuing).

Sending P packets over N links (pipelined): **(N + P − 1) × L/R**.

Example: 3 packets of 1000 bits, 2 links (1 router), 1 Mbps: (2 + 3 − 1) × 1 ms = **4 ms**.

---

## 8. X-series standards (ITU-T, for public data networks)

- **X.21:** physical-layer interface between DTE (your computer/terminal) and DCE (modem/network equipment).
- **X.25:** classic **connection-oriented packet switching** (virtual circuits) for public data networks. Its layers:
  - Physical: **X.21**
  - Data link: **LAPB** (Link Access Procedure, Balanced)
  - Network: **PLP** (Packet Layer Protocol)
- X.25 does error checking **at every hop**, so it's reliable but slow. Replaced by **Frame Relay** (error checking only at the ends) and then TCP/IP.

---

## 9. Transmission media

### 9.1 Guided (wired)

| Medium | How | Use | Notes |
|---|---|---|---|
| **Twisted pair** | Two insulated copper wires twisted together (twisting reduces crosstalk and noise) | Telephone lines, Ethernet (Cat5e, Cat6) | UTP (unshielded), STP (shielded) |
| **Coaxial cable** | Central conductor, insulation, metal shield, outer jacket | Cable TV, old Ethernet | Higher bandwidth than twisted pair |
| **Optical fibre** | Light pulses through glass, guided by **total internal reflection** | Backbones, long distance | Highest bandwidth, **immune to electromagnetic interference**, light, secure; costly to install, fragile; one fibre carries light one way |

Fibre types: **multimode** (step-index, graded-index; short distances) and **single-mode** (very thin core, long distances, highest bandwidth).

### 9.2 Unguided (wireless)

| | Radio waves | Microwaves | Infrared |
|---|---|---|---|
| Frequency | 3 kHz to 1 GHz | 1 GHz to 300 GHz | 300 GHz to 400 THz |
| Direction | **Omnidirectional** | **Unidirectional** (line of sight) | Line of sight |
| Penetrates walls? | **Yes** | No | No |
| Uses | AM/FM radio, TV, paging | Satellite links, cellular backhaul, point-to-point towers | TV remotes, short-range indoor links |

Because infrared can't pass walls, an infrared link in one room doesn't interfere with the next room (a security plus).

> **Trap.** Microwaves need **precise antenna alignment** (line of sight) and are blocked by obstacles. Radio waves penetrate buildings.

---

## 10. Layered architecture and the OSI model

### 10.1 Why layers?

Networking is complex. Layering splits it into manageable pieces, where each layer:
- provides **services** to the layer above,
- uses services of the layer below,
- talks to its **peer** layer on the other machine using a **protocol**.

Change one layer's implementation (e.g. Wi-Fi instead of Ethernet) without changing the others.

### 10.2 The OSI reference model (ISO)

7 layers. Mnemonic from top: "**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing". From bottom: "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way".

| # | Layer | Job | Data unit | Address/key item | Devices/protocols |
|---|---|---|---|---|---|
| 7 | **Application** | Network services to the user (mail, file transfer, web, directory) | Data/message | | HTTP, FTP, SMTP, DNS |
| 6 | **Presentation** | **Translation** (formats, character codes), **encryption/decryption**, **compression** | Data | | SSL/TLS (roughly), JPEG, ASCII |
| 5 | **Session** | **Dialog control** (who talks when, half/full duplex), **synchronisation** (checkpoints), session setup/teardown | Data | | RPC, NetBIOS |
| 4 | **Transport** | **Process-to-process** delivery, segmentation/reassembly, **end-to-end** flow and error control, connection control | **Segment** (TCP) / datagram (UDP) | **Port number** | TCP, UDP |
| 3 | **Network** | **Host-to-host** delivery across networks: **logical addressing**, **routing** | **Packet / datagram** | **IP address** | IP, ICMP, routers |
| 2 | **Data link** | **Node-to-node (hop-to-hop)** delivery: **framing**, **physical addressing**, flow and error control per link, **access control** (MAC) | **Frame** | **MAC address** | Ethernet, PPP, switches, bridges |
| 1 | **Physical** | Transmit **raw bits**: signals, bit rate, synchronisation, topology, transmission mode | **Bit** | | Cables, hubs, repeaters |

> **Traps.**
> - **Encryption and compression → Presentation**, not Application.
> - **Error control** appears at **both** Data Link (hop-to-hop) and Transport (end-to-end). Flow control too.
> - **Dialog control and synchronisation → Session.**
> - **Routing → Network.** **Framing → Data link.**
> - OSI is a **reference model**, not an implemented protocol suite. TCP/IP is the actual protocol suite.

### 10.3 Encapsulation

Going down the stack, each layer adds its **header** (data link also adds a **trailer**, e.g. the CRC):

```
Application:  [ Data ]
Transport:    [ TCP hdr | Data ]                         = segment
Network:      [ IP hdr | TCP hdr | Data ]                = packet
Data link:    [ Eth hdr | IP hdr | TCP hdr | Data | CRC ] = frame
Physical:     1011001010...                              = bits
```

The receiver strips headers in reverse (**decapsulation**).

### 10.4 The TCP/IP model

| TCP/IP layer | Corresponds to OSI |
|---|---|
| Application | Application + Presentation + Session |
| Transport | Transport |
| Internet | Network |
| Network access (Link) | Data link + Physical |

(Some books use a 5-layer hybrid: Application, Transport, Network, Data link, Physical.)

| OSI | TCP/IP |
|---|---|
| Reference model, designed before protocols | Practical model built around existing protocols |
| 7 layers | 4 (or 5) layers |
| Clear distinction of service, interface, protocol | Less clear |
| Network layer: connectionless and connection-oriented | Internet layer: connectionless only; transport supports both |

### 10.5 Addresses at each layer

| Address | Layer | Size | Example | Scope |
|---|---|---|---|---|
| **Physical (MAC)** | Data link | **48 bits** | 07:01:02:01:2C:4B | Changes hop to hop (per link) |
| **Logical (IP)** | Network | 32 bits (IPv4), 128 bits (IPv6) | 192.168.1.5 | Unchanged end to end (except NAT) |
| **Port** | Transport | **16 bits** | 80 (HTTP) | Identifies the process |
| Specific (URL, email) | Application | | www.isro.gov.in | |

---

## 11. Exam traps

1. Jitter is variation in delay.
2. ARPANET 1969; TCP/IP switch 1983 = birth of the Internet; WWW 1991.
3. Mesh: n(n−1)/2 links, n−1 ports per device.
4. Star: hub is single point of failure. Bus: backbone break halts all.
5. Packet switching: better utilisation, possible reordering/loss. Circuit: reserved, in order.
6. X.25: X.21 / LAPB / PLP.
7. Microwave and infrared are line of sight; radio penetrates walls.
8. Presentation: encryption, compression, translation. Session: dialog control, synchronisation.
9. Transport: process to process (ports). Network: host to host (IP). Data link: hop to hop (MAC).
10. Data units: bits, frames, packets, segments, messages.

---

## 12. Practice questions

**Q1.** Number of links for a fully connected mesh of 10 devices?
(a) 45 (b) 90 (c) 10 (d) 100

**Answer: (a).** 10 × 9/2.

---

**Q2.** In a mesh of 10 devices, how many I/O ports does each device need?
(a) 10 (b) 9 (c) 45 (d) 5

**Answer: (b).**

---

**Q3.** Which OSI layer is responsible for process-to-process delivery?
(a) Network (b) Data link (c) Transport (d) Session

**Answer: (c).**

---

**Q4.** Which layer handles encryption and compression?
(a) Application (b) Presentation (c) Session (d) Transport

**Answer: (b).**

---

**Q5.** Which layer adds a trailer (e.g. CRC) to the data unit?
(a) Network (b) Transport (c) Data link (d) Physical

**Answer: (c).**

---

**Q6.** The size of a MAC address and a port number are respectively:
(a) 32 bits, 16 bits (b) 48 bits, 16 bits (c) 48 bits, 32 bits (d) 64 bits, 16 bits

**Answer: (b).**

---

**Q7.** A walkie-talkie works in which mode?
(a) Simplex (b) Half-duplex (c) Full-duplex (d) Multiplex

**Answer: (b).**

---

**Q8.** Which statement about circuit switching is TRUE?
(a) No setup phase (b) Bandwidth is shared dynamically (c) A dedicated path is reserved for the entire session (d) Packets may arrive out of order

**Answer: (c).**

---

**Q9.** A message of 3 packets, each 1000 bits, is sent over 3 links (2 routers) of 1 Mbps each, store-and-forward, ignoring propagation and queuing. Total time until the last bit arrives?
(a) 3 ms (b) 5 ms (c) 9 ms (d) 6 ms

**Answer: (b).** (N + P − 1) × L/R = (3 + 3 − 1) × 1 ms = 5 ms.

---

**Q10.** Which medium is immune to electromagnetic interference?
(a) UTP (b) Coaxial (c) Optical fibre (d) Radio waves

**Answer: (c).**

---

**Q11.** Dialog control and synchronisation checkpoints are functions of the:
(a) transport layer (b) session layer (c) presentation layer (d) application layer

**Answer: (b).**

---

**Q12.** Which network typically covers a city?
(a) PAN (b) LAN (c) MAN (d) WAN

**Answer: (c).**

---

**Q13.** In the TCP/IP model, the OSI session and presentation layers are merged into:
(a) Transport (b) Application (c) Internet (d) Network access

**Answer: (b).**

---

**Q14.** Which address changes at every hop as a packet travels across the Internet?
(a) IP address (b) Port number (c) MAC address (d) URL

**Answer: (c).** Each link rewrites the frame with new source/destination MAC addresses; the IP addresses stay the same (ignoring NAT).
