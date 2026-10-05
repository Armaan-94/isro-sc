# 09. Network Layer: Routing (Distance Vector, Link State, RIP, OSPF, BGP)

> **The question routers keep asking.** "A packet for network X just arrived. Which of my links should it go out on?" The **routing table** answers it. **Routing protocols** are how routers build and update those tables automatically by talking to each other. There are two fundamentally different philosophies: gossip with your neighbours (distance vector), or learn the whole map and compute (link state).

---

## 1. Routing vs forwarding

- **Forwarding:** the per-packet action: look up the destination in the table, send it out the right interface. Fast, local.
- **Routing:** the background process of **building** the table (finding good paths). Network-wide.

### Routing table entries

Each entry: **destination network (and mask), next hop, outgoing interface, metric/cost**.

### Static vs dynamic

- **Static routing:** an administrator types routes manually. Simple, secure, predictable; doesn't adapt to failures. Fine for small, stable networks (and for the default route).
- **Dynamic routing:** routers exchange information and update automatically. Needed at Internet scale.

### Flooding

A routing-table-free approach: send every incoming packet out of **every** interface except the one it came in on.
- + Always finds the shortest path (some copy takes it), extremely robust.
- − Huge numbers of **duplicates**. Must be controlled with a hop counter (TTL) or by tracking sequence numbers so each router floods a packet only once.
- Used as a **building block** (link-state routing floods its link-state packets).

### Autonomous Systems (AS)

An **AS** is a set of networks and routers under **one administration** (an ISP, a university, a company), identified by an AS number.

- **Intra-domain (interior) routing** inside an AS: **RIP**, **OSPF** (and IS-IS, EIGRP).
- **Inter-domain (exterior) routing** between ASes: **BGP**.

---

## 2. Distance Vector Routing (DVR)

### 2.1 The idea

Each router keeps a **distance vector**: for every destination, its current best known **cost** and the **next hop**.

Periodically (and when something changes), each router sends its vector (**destination, cost** pairs) **to its neighbours only**.

When router X receives neighbour Y's vector, it applies the **Bellman-Ford equation**:

```
D_X(dest) = min over neighbours Y of { cost(X, Y) + D_Y(dest) }
```

"My distance to a destination = the cheapest of (link cost to a neighbour + that neighbour's advertised distance)."

Analogy: you don't have a map. You ask each neighbour "how far are you from Delhi?" and add how far that neighbour is from you. Pick the smallest.

### 2.2 Worked example

Router X has two neighbours: **Y (link cost 3)** and **Z (link cost 7)**. It receives:

| Destination | Y says | Z says |
|---|---|---|
| X | 3 | 7 |
| Y | 0 | 2 |
| Z | 2 | 0 |
| W | 6 | 1 |
| V | 9 | 3 |

X computes:

| Dest | Via Y (3 + Y's) | Via Z (7 + Z's) | Best | Next hop |
|---|---|---|---|---|
| Y | 3 + 0 = **3** | 7 + 2 = 9 | 3 | Y |
| Z | 3 + 2 = **5** | 7 + 0 = 7 | 5 | Y |
| W | 3 + 6 = 9 | 7 + 1 = **8** | 8 | Z |
| V | 3 + 9 = 12 | 7 + 3 = **10** | 10 | Z |

Note X reaches its own neighbour Z more cheaply **through Y** (5) than directly (7).

### 2.3 Updates

- **Periodic** updates (e.g. every 30 s).
- **Triggered** updates when a change is detected.

### 2.4 The count-to-infinity problem

DVR learns good news fast but **bad news slowly**.

Line topology **A — B — C**, each link cost 1. Tables: B reaches A at cost 1; C reaches A at cost 2 (via B).

The A—B link **breaks**.
1. B sees its direct link to A is gone. But C's last vector said "I can reach A at cost 2". B doesn't know that C's path goes **through B itself**! B updates: A = 2 + 1 = **3 via C**.
2. C hears B now says 3: C updates A = **4 via B**.
3. B → 5, C → 6, ... They keep bouncing, increasing slowly toward "infinity".

That's **count to infinity** (also called the two-node loop instability).

**Remedies:**
- **Define infinity small:** RIP uses **16 = infinity**, so the counting stops quickly (but this limits network size).
- **Split horizon:** never advertise a route **back** to the neighbour you learned it from. (C wouldn't tell B about A, since C learned it from B.)
- **Split horizon with poison reverse:** advertise it back, but with cost **∞**, explicitly saying "don't route to A through me".
- **Hold-down timers:** after a route goes down, ignore "good news" about it for a while.

These help a lot for two-node loops but **don't fully solve** loops involving three or more routers.

---

## 3. RIP (Routing Information Protocol): DVR in practice

| Feature | Value |
|---|---|
| Algorithm | Distance vector (Bellman-Ford) |
| Metric | **Hop count** (every link = 1, regardless of speed) |
| Max hops | **15**; **16 = unreachable (infinity)** |
| Transport | **UDP port 520** (RIP is an application-layer process) |
| Periodic update | Every **30 s** (whole table to neighbours) |
| Expiration (timeout) | **180 s** without an update → route marked invalid (cost 16) |
| Garbage collection | **120 s** after that → route deleted |
| Versions | RIPv1: classful, broadcast. **RIPv2**: supports **subnet masks/CIDR**, authentication, multicast (224.0.0.9). RIPng: IPv6 |

Good: simple, easy to configure. Bad: slow convergence, small networks only (15 hops), hop count ignores bandwidth (a 2-hop path over slow links beats a 3-hop path over fast links).

---

## 4. Link State Routing (LSR)

### 4.1 The idea

**Every router learns the complete topology** (the whole map), then computes shortest paths **itself** using **Dijkstra's algorithm**.

Steps for every router:
1. **Discover neighbours** (Hello packets) and measure link costs.
2. Build a **Link State Packet (LSP)** describing **only its own links**: "I am R, my neighbours are ... with costs ...". Include a **sequence number** and age.
3. **Flood** its LSP to **all** routers in the network (reliably; each router forwards a given LSP only once, using sequence numbers).
4. Every router collects all LSPs into an identical **Link State Database (LSDB)**: the full map.
5. Each router runs **Dijkstra** with **itself as the root** to get a shortest-path tree, and from it the routing table.

The LSDB is the **same** in every router; the **shortest-path trees differ** (each router is the root of its own).

### 4.2 The scope inversion (very common trap)

| | Distance Vector | Link State |
|---|---|---|
| **What** is shared | My view of the **whole network** (distances to all destinations) | Only **my own links** |
| **With whom** | **Neighbours only** | **Everyone** (flooding) |

### 4.3 Dijkstra worked example

Graph: A-B 4, A-C 2, B-C 1, B-D 5, C-D 8, C-E 10, D-E 2. Compute from **A**.

| Step | Visit | B | C | D | E |
|---|---|---|---|---|---|
| 0 | A | 4 (A) | **2** (A) | ∞ | ∞ |
| 1 | C | **3** (C) | ✓ | 10 (C) | 12 (C) |
| 2 | B | ✓ | ✓ | **8** (B) | 12 |
| 3 | D | ✓ | ✓ | ✓ | **10** (D) |
| 4 | E | ✓ | ✓ | ✓ | ✓ |

Shortest paths from A: B = 3 (A-C-B), C = 2 (A-C), D = 8 (A-C-B-D), E = 10 (A-C-B-D-E).

A's routing table: **every destination's next hop is C** (all shortest paths start A → C).

### 4.4 Pros and cons

- + **Fast convergence**, **no count-to-infinity** (every router computes from the same complete, consistent data).
- + Flexible metrics.
- − More **memory** (whole LSDB), more **CPU** (Dijkstra), more **bandwidth** during flooding.

---

## 5. OSPF (Open Shortest Path First): LSR in practice

| Feature | Value |
|---|---|
| Algorithm | **Link state + Dijkstra** |
| Metric | **Cost**, configurable; commonly **reference bandwidth / interface bandwidth** (e.g. 100 Mbps / BW, so a 10 Mbps link costs 10) |
| Max hops | No 15-hop limit |
| Transport | **Directly over IP** (protocol number **89**), not TCP/UDP |
| Hierarchy | AS split into **areas**, all connected to the **backbone area (Area 0)**. Limits flooding to within an area |
| Routers | Internal, Area Border Routers (ABR), Backbone, AS Boundary Routers (ASBR) |
| On LANs | Elects a **Designated Router (DR)** and Backup DR to reduce flooding |
| Messages | Hello, Database Description, Link State Request, Link State Update, Link State Ack |
| Other | Supports VLSM/CIDR, authentication, equal-cost multipath load balancing |

---

## 6. Comparison

| Feature | Distance Vector (RIP) | Link State (OSPF) |
|---|---|---|
| Algorithm | **Bellman-Ford** | **Dijkstra** |
| Knowledge | Local (neighbours' views) | Global (full map) |
| Shares | Whole routing table | Own link states |
| Shared with | **Neighbours** | **All routers** (flooding) |
| Convergence | **Slow** | **Fast** |
| Count to infinity | **Yes** | No |
| Bandwidth use | Low per update, periodic | High during flooding, then quiet |
| CPU/memory | Low | High |
| Metric | Hop count (max 15) | Configurable cost |
| Runs over | UDP 520 | IP (protocol 89) |
| Scale | Small networks | Large enterprise networks |

---

## 7. BGP (Border Gateway Protocol): routing between ASes

- The protocol that glues the Internet's ASes together. Current version **BGP-4**.
- **Path vector** routing: each advertisement carries the full **list of ASes** (AS-PATH) to reach a destination prefix.
- **Loop prevention:** a router **rejects** any route whose AS-PATH already contains its **own AS number**.
- **Policy-based:** route choice depends on business policy (who pays whom), not just shortest path.
- Runs over **TCP port 179** (reliable sessions between BGP peers).
- **eBGP** (between ASes) and **iBGP** (distributing external routes inside an AS).

---

## 8. Forwarding with subnets: longest prefix match

A router's table may contain overlapping prefixes. For each destination:

1. For each entry, compute **destination AND entry's mask**; check if it equals the entry's network.
2. **One match** → use it.
3. **Several matches** → choose the one with the **longest prefix** (most specific).
4. **No match** → **default route** (0.0.0.0/0).

### Worked example

| Network | Interface |
|---|---|
| 192.168.0.0/16 | if1 |
| 192.168.4.0/22 | if2 |
| 192.168.4.0/24 | if3 |
| 0.0.0.0/0 (default) | if4 |

- **192.168.4.77**: matches /16 ✓, /22 (covers 192.168.4.0 to 192.168.7.255) ✓, /24 ✓ → longest = /24 → **if3**.
- **192.168.5.10**: /16 ✓, /22 ✓, /24 ✗ → **if2**.
- **192.168.9.1**: only /16 → **if1**.
- **10.0.0.1**: none → **if4** (default).

Real routers use tries (prefix trees) or TCAM hardware to do this at line rate.

---

## 9. Multicast and other routing (recall level)

- **Multicast routing** builds distribution trees: source-based trees (DVMRP, PIM-DM) or shared trees (PIM-SM, CBT).
- **Hierarchical routing**: routers know details of their own region and only summaries of others (reduces table size). OSPF areas and Internet ASes are examples.
- **Broadcast routing**: reverse path forwarding (forward a broadcast packet only if it arrived on the interface you'd use to reach the source).

---

## 10. Exam traps

1. DVR: share **whole table** with **neighbours**. LSR: share **own links** with **everyone**.
2. DVR uses **Bellman-Ford**; LSR uses **Dijkstra**.
3. Count to infinity: DVR only; split horizon / poison reverse / hold-down mitigate it.
4. RIP: hop count, max 15, 16 = ∞, UDP 520, 30 s updates, 180 s timeout, 120 s garbage.
5. OSPF: cost metric, areas with backbone 0, runs directly on IP (89).
6. BGP: path vector, inter-AS, TCP 179, policy-based.
7. Longest prefix match wins; default route if nothing matches.

---

## 11. Practice questions

**Q1.** Router R has neighbours P (cost 2) and Q (cost 4). P reports distance 5 to network N; Q reports distance 2. R's distance to N and next hop?
(a) 6 via Q (b) 7 via P (c) 5 via P (d) 2 via Q

**Answer: (a).** Via P: 2 + 5 = 7. Via Q: 4 + 2 = 6.

---

**Q2.** In RIP, the maximum number of hops to a reachable destination is:
(a) 15 (b) 16 (c) 255 (d) unlimited

**Answer: (a).**

---

**Q3.** Which technique prevents a router from advertising a route back to the neighbour it learned it from?
(a) Flooding (b) Split horizon (c) Hold-down (d) Dijkstra

**Answer: (b).**

---

**Q4.** OSPF messages are carried:
(a) over TCP port 179 (b) over UDP port 520 (c) directly in IP (protocol 89) (d) over UDP port 89

**Answer: (c).**

---

**Q5.** BGP is classified as a:
(a) distance vector protocol (b) link state protocol (c) path vector protocol (d) flooding protocol

**Answer: (c).**

---

**Q6.** Using the Dijkstra example in section 4.3, what is the shortest distance from A to E if link D-E's cost changes to 5?
(a) 10 (b) 12 (c) 13 (d) 11

**Answer: (b).** Via D: 8 + 5 = 13. Via C directly: 2 + 10 = 12. Minimum = 12.

---

**Q7.** Routing table entries 10.0.0.0/8 → eth0, 10.1.0.0/16 → eth1, 10.1.2.0/24 → eth2. Packet to 10.1.3.4 goes out:
(a) eth0 (b) eth1 (c) eth2 (d) dropped

**Answer: (b).** Matches /8 and /16 (not /24, since 3 ≠ 2). Longest = /16.

---

**Q8.** The count-to-infinity problem occurs in:
(a) link state routing (b) distance vector routing (c) static routing (d) flooding

**Answer: (b).**

---

**Q9.** In link state routing, each router floods:
(a) its full routing table to neighbours (b) information about its directly connected links to all routers (c) only its default route (d) nothing

**Answer: (b).**

---

**Q10.** Which statement about RIP is FALSE?
(a) It uses hop count (b) It uses UDP port 520 (c) It converges faster than OSPF (d) RIPv2 supports subnet masks

**Answer: (c).**

---

**Q11.** In OSPF, the area to which all other areas must connect is:
(a) Area 1 (b) Area 0 (backbone) (c) Stub area (d) Any area

**Answer: (b).**

---

**Q12.** How does BGP prevent routing loops?
(a) Hop count limit of 15 (b) Rejecting routes whose AS-PATH contains its own AS number (c) Split horizon (d) Sequence numbers in LSPs

**Answer: (b).**

---

**Q13.** Distance vector routing is based on which algorithm?
(a) Dijkstra (b) Bellman-Ford (c) Prim (d) Kruskal

**Answer: (b).**

---

**Practice questions:** [7.09 Routing Protocols](../ISRO_CS_Question_Bank/07_Computer_Networks/7.09_Routing_Protocols.md)
