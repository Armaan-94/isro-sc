# 08. Network Layer: IP Addressing, Subnetting, CIDR, VLSM and Supernetting

> **The most numerical topic in networking.** Almost every paper has one subnetting/CIDR question. The good news: it's all **binary AND/OR with powers of 2**. Once you can find the network address and broadcast address of any `a.b.c.d/n` in 30 seconds, you've won this chapter. We'll build that skill step by step.

---

## 1. IPv4 address basics

- **32 bits**, written as four **octets** in **dotted decimal**: `192.168.10.37`.
- Each octet is 0 to 255.
- Total addresses: 2³² ≈ **4.29 billion**.
- Every address has two parts:
  - **Network ID (prefix):** identifies the network. Same for all hosts in that network.
  - **Host ID (suffix):** identifies the host within the network.

Only **network-layer devices** (hosts' interfaces, router interfaces) have IP addresses. **Switches, hubs and repeaters don't** (layer 2 and 1). A router has **one IP address per interface**.

### Binary warm-up (you must be fast at this)

Powers of 2 in one octet: **128, 64, 32, 16, 8, 4, 2, 1**.

| Mask octet | Binary | Number of 1s |
|---|---|---|
| 128 | 10000000 | 1 |
| 192 | 11000000 | 2 |
| 224 | 11100000 | 3 |
| 240 | 11110000 | 4 |
| 248 | 11111000 | 5 |
| 252 | 11111100 | 6 |
| 254 | 11111110 | 7 |
| 255 | 11111111 | 8 |

Memorise this table. Every subnetting question uses it.

---

## 2. Classful addressing (the old scheme)

The first few bits fix the **class**:

| Class | Leading bits | First octet | Network bits | Host bits | Number of networks | Hosts per network | Default mask |
|---|---|---|---|---|---|---|---|
| **A** | 0 | **0 to 127** | 8 | 24 | 2⁷ − 2 = **126** | 2²⁴ − 2 = **16,777,214** | 255.0.0.0 (/8) |
| **B** | 10 | **128 to 191** | 16 | 16 | 2¹⁴ = **16,384** | 2¹⁶ − 2 = **65,534** | 255.255.0.0 (/16) |
| **C** | 110 | **192 to 223** | 24 | 8 | 2²¹ = **2,097,152** | 2⁸ − 2 = **254** | 255.255.255.0 (/24) |
| **D** | 1110 | **224 to 239** | Multicast | | | | |
| **E** | 1111 | **240 to 255** | Reserved/experimental | | | | |

Why **−2 hosts**? In every network, two host addresses are reserved:
- Host bits **all 0** = the **network address** (names the network itself).
- Host bits **all 1** = the **directed broadcast address**.

Why **126** Class A networks? 2⁷ = 128, minus network **0** (0.x.x.x, "this network") and network **127** (**loopback**).

### Problems with classful addressing

Class A and B blocks were **too big** (who needs 16 million hosts?), Class C **too small** (254 hosts). Huge waste, and the address space ran out. Solutions: **subnetting**, then **CIDR** (classless), then **NAT** and **IPv6**.

---

## 3. Special addresses

| Address | Meaning |
|---|---|
| **0.0.0.0** | "This host" / unspecified (used as source by a host that doesn't know its IP yet, e.g. in DHCP) |
| **127.0.0.0/8** (e.g. 127.0.0.1) | **Loopback**: packets never leave the host. For testing |
| **255.255.255.255** | **Limited broadcast**: all hosts on **this** network; routers never forward it |
| **Network ID + host all 1s** (e.g. 200.1.2.255 for 200.1.2.0/24) | **Directed broadcast**: all hosts on **that specific** network; can be routed to it |
| **169.254.0.0/16** | Link-local (auto-assigned when DHCP fails) |

### Private address ranges (RFC 1918), not routed on the public Internet

| Range | CIDR | Class equivalent |
|---|---|---|
| **10.0.0.0 to 10.255.255.255** | 10.0.0.0/8 | One Class A |
| **172.16.0.0 to 172.31.255.255** | 172.16.0.0/12 | 16 Class B networks |
| **192.168.0.0 to 192.168.255.255** | 192.168.0.0/16 | 256 Class C networks |

Hosts with private addresses reach the Internet via **NAT** (section 10).

### Casting

- **Unicast:** one to one.
- **Multicast:** one to a group (Class D).
- **Broadcast:** one to all (limited or directed).
- **Anycast** (IPv6 mostly): to the nearest of a group.

---

## 4. The subnet mask and the two magic operations

A **subnet mask** is 32 bits: **1s over the network (and subnet) part, 0s over the host part**. Written in dotted decimal (`255.255.255.192`) or as a prefix length (`/26`).

Given any address and its mask:

```
Network address   = address AND mask
Broadcast address = address OR (NOT mask)        (= network address + block size − 1)
First usable host = network address + 1
Last usable host  = broadcast address − 1
Number of addresses = 2^(32 − n)
Usable hosts        = 2^(32 − n) − 2
```

### The 30-second shortcut ("block size" method)

1. Find the **interesting octet**: the one where the mask is neither 255 nor 0.
2. **Block size** = 256 − (mask value in that octet).
3. The network starts at the **largest multiple of the block size ≤ the address's value** in that octet.
4. Broadcast = next multiple − 1 (in that octet), with all later octets = 255.

### Worked example 1: 167.199.170.82/27

- /27 = 255.255.255.**224**. Interesting octet: 4th.
- Block size = 256 − 224 = **32**.
- Multiples of 32: 0, 32, 64, 96, ... 82 falls in **64 to 95**.
- Network **167.199.170.64**, broadcast **167.199.170.95**.
- Hosts .65 to .94: **30** usable. Total 32 addresses.

### Worked example 2: 190.45.66.200/22

- /22 = 255.255.**252**.0. Interesting octet: 3rd.
- Block size = 256 − 252 = **4**.
- 66 lies in 64 to 67.
- Network **190.45.64.0**, broadcast **190.45.67.255**.
- Addresses = 2^(32 − 22) = 1024. Usable = **1022**.
- First host 190.45.64.1, last 190.45.67.254.

### Worked example 3: 172.16.45.14/20

- /20 = 255.255.**240**.0. Block = 16 in the 3rd octet.
- 45 lies in 32 to 47.
- Network **172.16.32.0**, broadcast **172.16.47.255**. Usable hosts 4094.

### Worked example 4: 10.15.200.33/27

- Block 32 in the 4th octet. 33 lies in 32 to 63.
- Network **10.15.200.32**, broadcast **10.15.200.63**, hosts .33 to .62.

### Same subnet or not?

Two hosts are on the same subnet iff **(A AND mask) = (B AND mask)**.

192.168.10.65/26 and 192.168.10.120/26: block 64. 65 → 64; 120 → 64. **Same.**
192.168.10.65/26 and 192.168.10.130/26: 130 → 128. **Different.**

---

## 5. Subnetting (Fixed-Length Subnet Masks, FLSM)

### 5.1 Why subnet?

Split one big network into smaller ones:
- better **security** and isolation (departments),
- **smaller broadcast domains** (less broadcast traffic),
- easier management,
- less address waste than one huge flat network.

Costs: **2 addresses lost per subnet**, more routing entries, more complexity.

### 5.2 How

**Borrow bits from the host part** to form a **subnet ID**.

```
Number of subnets = 2^(borrowed bits)
Addresses per subnet = 2^(remaining host bits)
Usable hosts per subnet = 2^(remaining host bits) − 2
```

(Old textbooks subtracted 2 from the **number of subnets** too, excluding the all-0s and all-1s subnet IDs. Modern practice (RFC 1878, "subnet zero") uses all of them. If a question says "excluding all-zeros and all-ones subnets", subtract 2.)

### 5.3 Worked example: 200.1.2.0/24 into 4 equal subnets

- 4 subnets → borrow **2** bits → **/26** (mask 255.255.255.192), block 64.

| Subnet | Network | Host range | Broadcast |
|---|---|---|---|
| 1 | 200.1.2.0 | .1 to .62 | .63 |
| 2 | 200.1.2.64 | .65 to .126 | .127 |
| 3 | 200.1.2.128 | .129 to .190 | .191 |
| 4 | 200.1.2.192 | .193 to .254 | .255 |

Each: 64 addresses, **62 usable**. Total usable: 248 (versus 254 unsubnetted: we lost 6).

### 5.4 Choosing the mask from requirements

**"Class C network; need 6 subnets, each with at least 25 hosts."**
- 6 subnets → need 3 borrowed bits (2³ = 8 ≥ 6).
- Remaining host bits: 5 → 30 usable hosts ≥ 25 ✓.
- Mask **/27** = 255.255.255.224.

**"Class B network 172.20.0.0; need subnets of at least 500 hosts; maximise the number of subnets."**
- 500 hosts → 9 host bits (2⁹ − 2 = 510).
- Subnet bits = 16 − 9 = 7 → **128 subnets**. Mask /23 = 255.255.254.0.

**"Class B with mask 255.255.255.0."**
- Borrowed 8 bits → **256 subnets**, **254 hosts** each.

**Mask 255.255.255.248:** /29 → 8 addresses, **6 hosts**.
**Mask 255.255.255.252:** /30 → 4 addresses, **2 hosts** (classic for point-to-point router links).

---

## 6. VLSM (Variable-Length Subnet Masks)

FLSM gives every subnet the same size, wasting space when needs differ. **VLSM** sizes each subnet to its need.

### Method

1. For each requirement of h hosts, find the smallest block 2^k with **2^k − 2 ≥ h**.
2. **Sort requirements largest first.**
3. Allocate blocks one after another from the start of the space.

Why largest first? A block of size S must **start at a multiple of S**. Placing big blocks first keeps every later (smaller) block naturally aligned.

### Worked example: 14.24.74.0/24, need 120, 60 and 10 hosts

| Need | Block | Prefix | Network | Host range | Broadcast |
|---|---|---|---|---|---|
| 120 | 128 | /25 | 14.24.74.0 | .1 to .126 | .127 |
| 60 | 64 | /26 | 14.24.74.128 | .129 to .190 | .191 |
| 10 | 16 | /28 | 14.24.74.192 | .193 to .206 | .207 |

Free for later: .208 to .255 (48 addresses).

### Point-to-point links

A link between two routers needs exactly 2 addresses → **/30** (4 addresses, 2 usable).

---

## 7. CIDR (Classless Inter-Domain Routing)

### 7.1 The idea

Forget classes. Any block is written **a.b.c.d/n**, where n is the prefix length (1 to 32). Blocks can be any power-of-2 size.

```
Addresses in block = 2^(32 − n)
```

### 7.2 Rules for a valid CIDR block

1. Addresses are **contiguous**.
2. The number of addresses is a **power of 2**.
3. The **first address is divisible by the block size** (in the relevant octet(s)).

**Checks:**
- 100.1.2.32 to 100.1.2.47: 16 addresses (2⁴ ✓), 32 divisible by 16 ✓ → **valid**, = 100.1.2.32/28.
- 150.10.20.64 to 150.10.20.127: 64 ✓, 64 divisible by 64 ✓ → **valid** /26.
- 100.1.2.20 to 100.1.2.35: 16 ✓, but 20 not divisible by 16 ✗ → **invalid**.
- 192.168.1.0 to 192.168.2.255: 512 (2⁹ ✓), start 192.168.1.0: in the 3rd octet the block spans 2 values, so the start must be even. 1 is odd ✗ → **invalid**.

### 7.3 ISP allocation example (classic)

An ISP holds **190.100.0.0/16** (65,536 addresses) and must serve:
- 64 customers needing **256** addresses each,
- 128 customers needing **128** each,
- 128 customers needing **64** each.

| Group | Each gets | Prefix | Total | Range |
|---|---|---|---|---|
| 1 | 256 | /24 | 64 × 256 = 16,384 | 190.100.0.0 to 190.100.63.255 |
| 2 | 128 | /25 | 128 × 128 = 16,384 | 190.100.64.0 to 190.100.127.255 |
| 3 | 64 | /26 | 128 × 64 = 8,192 | 190.100.128.0 to 190.100.159.255 |

Allocated 40,960; **24,576** remain for future customers. (Customer 1 of group 2: 190.100.64.0/25; customer 2: 190.100.64.128/25; etc.)

---

## 8. Supernetting (route aggregation)

The reverse of subnetting: combine several **contiguous** smaller blocks into **one** bigger block so a router needs **one** routing entry instead of many.

**Conditions:**
1. Blocks are **contiguous**.
2. The total number of addresses is a **power of 2** (number of blocks is a power of 2, for equal blocks).
3. The first block's network address is **divisible by the combined size**.

**Example (valid):** 128.56.24.0/24, .25.0/24, .26.0/24, .27.0/24 → 4 blocks (4 × 256 = 1024 = 2¹⁰). In the 3rd octet, 24 is divisible by 4 ✓ → supernet **128.56.24.0/22**.

**Example (invalid):** 200.10.5.0/24 to 200.10.8.0/24 → 4 blocks, but 5 is not divisible by 4 ✗.

**Example (invalid, gap):** 100.1.2.0/25, 100.1.2.128/26, 100.1.3.192/26 → not contiguous.

A quick way to find a supernet: write the network addresses in binary and keep the **common leading bits**. That count is the new prefix length (if the blocks exactly fill it).

---

## 9. Practical: what you get from an ISP / DHCP

To get on a network, a host needs:
- its **IP address**,
- the **subnet mask**,
- the **default gateway** (router for off-subnet traffic),
- a **DNS server** address.

DHCP ([Chapter 12](12_Application_Layer_Protocols.md)) hands all of these out automatically.

---

## 10. NAT (Network Address Translation)

Lets many hosts with **private** addresses share **one (or a few) public** address(es).

- The NAT router rewrites the **source IP** (and usually **source port**: that's **PAT/NAPT**, "port address translation") of outgoing packets to its public address, and keeps a **translation table** to map replies back.
- Saves public addresses; hides internal structure (a mild security benefit).
- Breaks the end-to-end principle; complicates peer-to-peer apps and incoming connections (need port forwarding).

---

## 11. Exam traps

1. Class ranges: A 0 to 127, B 128 to 191, C 192 to 223, D 224 to 239, E 240 to 255.
2. Hosts = 2^(host bits) − 2. Class A networks = 126.
3. Network = address AND mask; broadcast = address OR NOT mask.
4. Block size = 256 − mask octet.
5. Subnets = 2^(borrowed bits) (subtract 2 only if told to exclude all-0/all-1 subnets).
6. VLSM: allocate **largest first**.
7. CIDR validity: contiguous, power of 2, start divisible by size.
8. Supernet: contiguous + first network divisible by combined size.
9. Private ranges: 10/8, 172.16/12, 192.168/16. 127/8 is **loopback**, not private.
10. Limited broadcast 255.255.255.255 is never forwarded by routers.
11. Switches and hubs have no IP addresses (on their data plane).

---

## 12. Practice questions

**Q1.** Which class does 191.255.0.1 belong to?
(a) A (b) B (c) C (d) D

**Answer: (b).** First octet 191 is in 128 to 191.

---

**Q2.** Usable hosts in a /21 network?
(a) 2046 (b) 2048 (c) 1022 (d) 4094

**Answer: (a).** 2^11 − 2.

---

**Q3.** Network and broadcast address of 192.168.5.130/26?
(a) 192.168.5.128 and 192.168.5.191 (b) 192.168.5.0 and 192.168.5.63 (c) 192.168.5.128 and 192.168.5.255 (d) 192.168.5.64 and 192.168.5.127

**Answer: (a).** Block 64; 130 → 128.

---

**Q4.** A host has IP 172.16.100.200/19. Its network address?
(a) 172.16.96.0 (b) 172.16.64.0 (c) 172.16.100.0 (d) 172.16.0.0

**Answer: (a).** /19 = 255.255.224.0; block 32 in the 3rd octet; 100 → 96.

---

**Q5.** Broadcast address for Q4?
(a) 172.16.127.255 (b) 172.16.111.255 (c) 172.16.100.255 (d) 172.16.95.255

**Answer: (a).** 96 + 32 − 1 = 127.

---

**Q6.** A Class C network must be split into the maximum number of subnets with at least 14 hosts each. Mask?
(a) /27 (b) /28 (c) /29 (d) /26

**Answer: (b).** 14 hosts → 4 host bits (16 − 2 = 14). 32 − 4 = 28.

---

**Q7.** How many subnets does Q6 produce?
(a) 8 (b) 14 (c) 16 (d) 32

**Answer: (c).** 4 borrowed bits.

---

**Q8.** Which range is a valid CIDR block?
(a) 10.0.0.8 to 10.0.0.23 (b) 10.0.0.16 to 10.0.0.31 (c) 10.0.0.10 to 10.0.0.25 (d) 10.0.0.0 to 10.0.0.23

**Answer: (b).** 16 addresses, 16 divisible by 16. (a) 8 not divisible by 16; (c) 10 not divisible by 16; (d) 24 addresses is not a power of 2.

---

**Q9.** Aggregate 200.96.86.0/24 and 200.96.87.0/24.
(a) 200.96.86.0/23 (b) 200.96.84.0/22 (c) 200.96.86.0/25 (d) cannot be aggregated

**Answer: (a).** Two contiguous /24s; 86 is even (divisible by 2) ✓ → /23.

---

**Q10.** Can 200.96.87.0/24 and 200.96.88.0/24 be aggregated into one /23?
(a) Yes, 200.96.87.0/23 (b) No

**Answer: (b).** 87 is odd; a /23 must start at an even third octet. 200.96.86.0/23 covers 86 and 87; 200.96.88.0/23 covers 88 and 89.

---

**Q11.** VLSM on 192.168.1.0/24 for 50, 25 and 10 hosts (largest first). The 25-host subnet's network address?
(a) 192.168.1.64/27 (b) 192.168.1.32/27 (c) 192.168.1.64/26 (d) 192.168.1.96/27

**Answer: (a).** 50 → /26 (64 block): .0 to .63. 25 → /27 (32 block): .64 to .95. 10 → /28: .96 to .111.

---

**Q12.** Which address is a valid host address in 10.10.10.0/29?
(a) 10.10.10.0 (b) 10.10.10.7 (c) 10.10.10.6 (d) 10.10.10.8

**Answer: (c).** Block 8: .0 network, .7 broadcast, hosts .1 to .6. .8 is the next subnet.

---

**Q13.** Which of these is NOT a private address?
(a) 10.200.1.1 (b) 172.32.0.1 (c) 192.168.200.1 (d) 172.20.5.5

**Answer: (b).** 172.16 to 172.31 only.

---

**Q14.** The subnet mask 255.255.248.0 corresponds to:
(a) /19 (b) /20 (c) /21 (d) /22

**Answer: (c).** 8 + 8 + 5 = 21.

---

**Q15.** A Class B network uses mask 255.255.252.0. Number of subnets (all usable) and hosts per subnet?
(a) 64 and 1022 (b) 62 and 1022 (c) 64 and 1024 (d) 32 and 2046

**Answer: (a).** Borrowed 6 bits → 64 subnets; 10 host bits → 1022 hosts.

---

**Q16.** A packet is sent to 255.255.255.255. Which is TRUE?
(a) Routers forward it everywhere (b) It reaches all hosts on the local network only (c) It's a loopback address (d) It's a multicast address

**Answer: (b).**

---

**Q17.** Hosts 10.1.1.1/22 and 10.1.2.200/22 are on:
(a) the same network (b) different networks

**Answer: (a).** /22: block 4 in the 3rd octet. 1 → 0; 2 → 0. Both in 10.1.0.0/22.

---

**Practice questions:** [7.08 IP Addressing Subnetting CIDR](../ISRO_CS_Question_Bank/07_Computer_Networks/7.08_IP_Addressing_Subnetting_CIDR.md)
