# 12. Application Layer Protocols (HTTP, DNS, Email, FTP, DHCP, Telnet/SSH, VoIP)

> **This is the layer users actually touch.** Every app you use speaks an application-layer protocol. Exams mostly ask **which protocol does what**, **which port and transport** it uses, and a few timing questions on HTTP. Start by memorising the port table; then understand each protocol's story.

---

## 1. The master reference table (memorise)

| Protocol | Port(s) | Transport | Job |
|---|---|---|---|
| **FTP** | **20 (data), 21 (control)** | TCP | File transfer, two connections |
| **SSH** | 22 | TCP | Secure remote login |
| **Telnet** | 23 | TCP | Remote login, **plain text** |
| **SMTP** | 25 (587 for submission) | TCP | **Send / push** email |
| **DNS** | 53 | **UDP** (TCP for zone transfers, big replies) | Name ↔ IP |
| **DHCP** | **67 (server), 68 (client)** | **UDP** | Automatic IP configuration |
| **TFTP** | 69 | UDP | Trivial file transfer |
| **HTTP** | 80 | TCP (HTTP/3 uses QUIC over UDP) | Web |
| **POP3** | 110 | TCP | **Retrieve** email (download) |
| **NTP** | 123 | UDP | Time sync |
| **IMAP** | 143 | TCP | **Retrieve** email (stays on server) |
| **SNMP** | 161 (agent), 162 (trap) | UDP | Network management |
| **BGP** | 179 | TCP | Inter-AS routing |
| **HTTPS** | 443 | TCP | Secure web |
| **RIP** | 520 | UDP | Routing |

---

## 2. The World Wide Web and HTTP

### 2.1 The Web

Invented by **Tim Berners-Lee** at **CERN** (proposed 1989, public 1991). Three pillars:
- **URL**: where things are,
- **HTTP**: how to fetch them,
- **HTML**: how to display them.

A **URL** = protocol://host:port/path, e.g. `https://www.isro.gov.in:443/missions/index.html`. The port is usually omitted (defaults: 80 for http, 443 for https).

### 2.2 HTTP basics

- **Client-server**, **request-response**, runs over **TCP port 80**.
- **Stateless**: the server keeps **no memory** of previous requests. Each request stands alone.

**Request methods:**

| Method | Purpose |
|---|---|
| **GET** | Fetch a resource (parameters in the URL) |
| **POST** | Send data to the server (in the body), e.g. a form |
| **HEAD** | Like GET but headers only (no body) |
| **PUT** | Upload/replace a resource |
| **DELETE** | Delete a resource |
| PATCH, OPTIONS, TRACE, CONNECT | Partial update, capabilities, echo, tunnel (for HTTPS proxies) |

**Status codes:**

| Class | Meaning | Examples |
|---|---|---|
| **1xx** | Informational | 100 Continue |
| **2xx** | **Success** | **200 OK**, 201 Created, 204 No Content |
| **3xx** | **Redirection** | **301 Moved Permanently**, 302 Found, **304 Not Modified** |
| **4xx** | **Client error** | 400 Bad Request, 401 Unauthorized, **403 Forbidden**, **404 Not Found** |
| **5xx** | **Server error** | **500 Internal Server Error**, 502 Bad Gateway, **503 Service Unavailable** |

### 2.3 Non-persistent vs persistent connections

- **Non-persistent (HTTP/1.0 default):** a **new TCP connection for every object**. Each object costs about **2 RTT** (1 for the TCP handshake, 1 for request/response) plus transmission time.
- **Persistent (HTTP/1.1 default):** **one TCP connection** reused for many objects.
  - **Without pipelining:** request the next object only after the previous one arrives: **1 RTT per object** after setup.
  - **With pipelining:** send all requests back-to-back: about **1 RTT for all** remaining objects.

**Worked example:** a page = 1 HTML file + **10 images**, all tiny (ignore transmission time).

| Scheme | Total time |
|---|---|
| Non-persistent, serial | (1 + 10) × 2 RTT = **22 RTT** |
| Non-persistent, 10 parallel connections | 2 RTT (HTML) + 2 RTT (all images in parallel) = **4 RTT** |
| Persistent, no pipelining | 2 RTT (setup + HTML) + 10 × 1 RTT = **12 RTT** |
| Persistent, pipelining | 2 RTT + 1 RTT = **3 RTT** |

(Add DNS lookup time if the question includes it.)

HTTP/2 adds multiplexing of many requests over one connection and header compression; **HTTP/3** runs over **QUIC (UDP)**.

### 2.4 Cookies: adding state to a stateless protocol

1. On the first visit, the server's response includes `Set-Cookie: id=1678`.
2. The browser stores it (per domain).
3. On every later request to that domain, the browser sends `Cookie: id=1678`.
4. The server looks up id 1678 in its database: "ah, this is the same user; here's their cart".

Cookies are created and interpreted **by the server**; the browser just stores and returns them. Uses: logins/sessions, shopping carts, preferences, tracking.

### 2.5 Web caching (proxy server)

A **proxy** sits between clients and servers, keeping copies of recently fetched objects.
- Faster responses (cache hits are local), less traffic on the access link, less load on origin servers.
- **Conditional GET** (`If-Modified-Since`) lets the cache check whether its copy is stale: the server replies **304 Not Modified** if it's still valid.

### 2.6 HTTPS

**HTTP over TLS (formerly SSL)**, port **443**. Adds:
- **Confidentiality** (encryption),
- **Integrity** (tamper detection),
- **Server authentication** (digital certificates).

Plain HTTP has none of these.

### 2.7 HTML vs XML, static vs dynamic

| HTML | XML |
|---|---|
| **Displays** data | **Stores/transports** data |
| **Predefined** tags (`<h1>`, `<img>`) | **User-defined** tags (`<price>`) |
| How content **looks** | What data **means** |

- **Static site:** same files for everyone (HTML/CSS).
- **Dynamic site:** pages generated per request by server-side code + a database.
- **Web server** = software serving files (Apache, Nginx). **Web hosting** = the rented service keeping it online.
- **Browser** (Chrome) ≠ **search engine** (Google Search, a website you visit with a browser).

---

## 3. DNS (Domain Name System)

### 3.1 Why

Humans remember `www.isro.gov.in`; computers need `x.x.x.x`. DNS is a **distributed, hierarchical database** that translates names to IP addresses (and more).

### 3.2 The name space

An **inverted tree**:

```
                      . (root)
          /      |       |       \
        com     org     in       edu ...        <- top-level domains (TLDs)
        /               |
     google            gov
                         |
                        isro
                         |
                        www
```

- Max **128 levels**; each label ≤ **63 characters**; the full name ≤ 255 characters.
- **Generic TLDs:** .com, .org, .net, .edu, .gov, .mil, ...
- **Country-code TLDs:** .in, .uk, .us, ...
- **Inverse domain** (in-addr.arpa): maps **IP → name** (reverse lookup, PTR records).
- **FQDN** (fully qualified domain name) ends with the root dot: `www.isro.gov.in.`

### 3.3 Server hierarchy

1. **Root servers** (13 logical root server names, many physical instances): know where the TLD servers are.
2. **TLD servers**: know the authoritative servers for domains under them.
3. **Authoritative servers**: hold the **actual records** for a domain.
4. **Local (recursive) resolver**: your ISP's or a public resolver; does the legwork and **caches** answers (each record has a **TTL**).

### 3.4 Recursive vs iterative resolution

- **Recursive:** "please find the full answer for me". Client → local resolver is usually recursive.
- **Iterative:** "tell me who to ask next". Local resolver → root → TLD → authoritative is usually iterative.

Example: resolving `www.isro.gov.in`: your PC asks the local resolver (recursive). The resolver asks a root server → "ask the .in TLD servers". Asks .in → "ask gov.in's servers". ... Eventually the authoritative server for isro.gov.in returns the A record. The resolver caches it and answers your PC.

### 3.5 Resource record types

| Type | Maps |
|---|---|
| **A** | Name → **IPv4** address |
| **AAAA** | Name → **IPv6** address |
| **CNAME** | Alias → canonical (real) name |
| **MX** | Domain → **mail server** |
| **NS** | Domain → its **authoritative name server** |
| **PTR** | IP → name (reverse) |
| SOA | Start of authority (zone info) |
| TXT | Arbitrary text (SPF, verification) |

### 3.6 Transport

**UDP port 53** for normal queries (fast, small). **TCP 53** for zone transfers between servers and for responses too large for UDP.

---

## 4. Electronic mail

### 4.1 Architecture

Email is **store-and-forward**: sender and receiver need not be online at the same time.

```
Alice's UA --SMTP--> Alice's mail server --SMTP--> Bob's mail server --POP3/IMAP--> Bob's UA
           (push)                         (push)                         (pull)
```

- **UA (User Agent):** the mail client (Outlook, Thunderbird, phone app).
- **MTA (Message Transfer Agent):** moves mail between servers using **SMTP**.
- **MAA (Message Access Agent):** lets the user **pull** mail from their mailbox: **POP3** or **IMAP**.

> **Trap.** **SMTP only pushes/sends.** It is **never** used to download mail from your mailbox. POP3 and IMAP **pull**.

### 4.2 SMTP

- TCP port **25**. Text commands: HELO/EHLO, MAIL FROM, RCPT TO, DATA, QUIT.
- Originally supports only **7-bit ASCII** text.

### 4.3 MIME (Multipurpose Internet Mail Extensions)

Extends email to carry **non-ASCII text, images, audio, video, attachments** by encoding them into ASCII (e.g. Base64) with headers like `Content-Type: image/jpeg`.

### 4.4 POP3 vs IMAP

| POP3 (port 110) | IMAP4 (port 143) |
|---|---|
| Simple: **download** mail to the client | Mail **stays on the server** |
| **Delete mode** (remove from server after download) or **keep mode** | Server-side **folders**, **multi-device sync** |
| No server-side folders, no partial download | Can view headers first, **partial download**, **search on server** |
| Best for one permanent computer | Best for many devices |

### 4.5 Webmail

Gmail in a browser: the browser talks **HTTP(S)** to the mail server (for both sending and reading). Server-to-server transfer still uses **SMTP**.

### 4.6 Small facts

- An email address has exactly one **@**; the domain part is case-insensitive.
- **Bcc** recipients are hidden from everyone else; Bcc recipients can see the To and Cc lists.

---

## 5. FTP (File Transfer Protocol)

- **Two TCP connections**:
  - **Control connection, port 21:** commands and replies (USER, PASS, LIST, RETR, STOR). Stays open for the whole session. **Persistent.**
  - **Data connection, port 20** (in active mode): opened **for each file transfer** and closed after. **Non-persistent.**
- FTP is said to send control information **out-of-band** (on a separate connection). (HTTP is in-band.)
- **Active mode:** the server opens the data connection to the client (from port 20). **Passive mode:** the client opens it to a server-chosen port (firewall-friendly).
- Sends username/password in **plain text**. Secure versions: **FTPS** (FTP over TLS) and **SFTP** (file transfer over SSH).
- **TFTP:** a tiny version over **UDP port 69**, no authentication, used for booting devices and firmware loads.

> **Trap.** "FTP uses port 21" is incomplete. **21 for control, 20 for data.**

---

## 6. Telnet and SSH

- **Telnet (port 23):** remote terminal login. Everything, including passwords, travels in **plain text**. Still handy for testing whether a port is open.
- **SSH (port 22):** the secure replacement: encryption, server authentication, integrity. Also supports tunnelling and file transfer (SCP, SFTP).

---

## 7. DHCP (Dynamic Host Configuration Protocol)

Automatically gives a host its **IP address, subnet mask, default gateway, DNS servers**, and a **lease time**.

- Runs over **UDP**: server port **67**, client port **68**.
- **DORA** sequence:
  1. **Discover**: client **broadcasts** (source 0.0.0.0, destination 255.255.255.255) "any DHCP servers?"
  2. **Offer**: server offers an address.
  3. **Request**: client requests that offer (broadcast, so other servers know).
  4. **Acknowledge**: server confirms; the lease begins.
- Leases must be **renewed** (typically at 50% of lease time).
- A **DHCP relay agent** forwards DHCP broadcasts to a server on another subnet.
- If no server answers, many OSes self-assign a **169.254.x.x** (APIPA / link-local) address.
- DHCP replaced **BOOTP**, which replaced **RARP**.

---

## 8. SNMP (Simple Network Management Protocol)

- Monitors and manages network devices (routers, switches, servers).
- **Manager** (NMS) polls **agents** on devices; agents keep variables in a **MIB** (Management Information Base), named using **SMI**.
- UDP **161** (requests to agents), **162** (traps: unsolicited alerts from agents).
- Messages: GetRequest, GetNextRequest, SetRequest, Response, Trap.

---

## 9. VoIP and multimedia

- **VoIP** carries voice as IP packets (packet switching) instead of a dedicated circuit (PSTN).
- **SIP (Session Initiation Protocol):** **signalling**: set up, ring, modify and tear down calls. (H.323 is an older alternative.)
- **RTP (Real-time Transport Protocol):** carries the **actual media** (timestamps, sequence numbers), usually over **UDP**. **RTCP** gives quality feedback.
- Real-time traffic tolerates small loss better than delay or jitter; playout buffers smooth jitter.

### Mobile generations (recall)

| Gen | Key point |
|---|---|
| 1G | Analog voice (FDMA) |
| **2G (GSM)** | Digital voice + SMS, **TDMA**, introduced the **SIM** card; circuit-switched voice |
| **2.5G (GPRS)** | **Packet-switched** "always-on" data (~114 kbps); EDGE (2.75G) faster |
| 3G (UMTS/WCDMA) | Mobile Internet, video calls (CDMA-based) |
| 4G (LTE) | All-IP, OFDMA, high-speed broadband |
| 5G | Very high speed, ultra-low latency, massive IoT |

**WLL (Wireless Local Loop):** a fixed radio link replacing the copper "last mile" to the telephone exchange (rural areas).

---

## 10. Exam traps

1. FTP: **20 data + 21 control**, two TCP connections, control out-of-band.
2. SMTP pushes; POP3/IMAP pull. IMAP keeps mail on the server.
3. HTTP is **stateless**; cookies add state.
4. HTTP non-persistent = 2 RTT per object; persistent saves the handshakes.
5. DNS uses **UDP 53** (TCP for zone transfers).
6. DHCP uses **UDP 67/68**; DORA order.
7. Telnet plain text → SSH.
8. HTTPS port 443 adds confidentiality, integrity, authentication.
9. MX record = mail server; CNAME = alias; PTR = reverse.
10. SIP signals; RTP carries media.
11. GSM uses TDMA; GPRS brought packet switching.

---

## 11. Practice questions

**Q1.** Which protocol and port does an email client use to send mail to its server?
(a) POP3, 110 (b) IMAP, 143 (c) SMTP, 25 (d) HTTP, 80

**Answer: (c).**

---

**Q2.** A user reads mail on a phone, laptop and tablet and wants folders synchronised. Which protocol?
(a) POP3 delete mode (b) IMAP (c) SMTP (d) FTP

**Answer: (b).**

---

**Q3.** A web page has a base HTML file and 4 small images. With non-persistent HTTP and serial connections (ignore transmission time), total time?
(a) 5 RTT (b) 6 RTT (c) 10 RTT (d) 8 RTT

**Answer: (c).** 5 objects × 2 RTT.

---

**Q4.** Same page with persistent HTTP and pipelining?
(a) 3 RTT (b) 2 RTT (c) 6 RTT (d) 5 RTT

**Answer: (a).** 2 RTT (handshake + HTML) + 1 RTT for all 4 images.

---

**Q5.** Which DNS record gives the mail server for a domain?
(a) A (b) CNAME (c) MX (d) PTR

**Answer: (c).**

---

**Q6.** The DHCP Discover message is sent:
(a) as unicast to the server (b) as a broadcast from 0.0.0.0 to 255.255.255.255 (c) over TCP (d) to port 68 of the server

**Answer: (b).**

---

**Q7.** HTTP status code 404 means:
(a) Server error (b) Moved permanently (c) Not found (d) Forbidden

**Answer: (c).**

---

**Q8.** In FTP, the connection that stays open for the whole session is:
(a) data connection, port 20 (b) control connection, port 21 (c) both (d) neither

**Answer: (b).**

---

**Q9.** Which pair is correct?
(a) SSH: 23 (b) Telnet: 22 (c) SNMP trap: 162 (d) HTTPS: 8080

**Answer: (c).**

---

**Q10.** Which DNS query type means "give me the final answer, do the work yourself"?
(a) Iterative (b) Recursive (c) Inverse (d) Zone transfer

**Answer: (b).**

---

**Q11.** MIME is needed because SMTP originally supports only:
(a) binary data (b) 7-bit ASCII text (c) UTF-16 (d) HTML

**Answer: (b).**

---

**Q12.** Which protocol carries the actual voice packets in a VoIP call?
(a) SIP (b) RTP (c) SMTP (d) SNMP

**Answer: (b).**

---

**Q13.** A host has the address 169.254.12.7. The most likely reason is:
(a) it's a public server (b) DHCP failed and it self-assigned a link-local address (c) it's the loopback (d) it's a multicast group

**Answer: (b).**

---

**Q14.** Which statement about cookies is TRUE?
(a) The browser generates their content (b) The server sets them; the browser stores and returns them to the same domain (c) They make HTTP stateful at the protocol level (d) They're sent only over HTTPS

**Answer: (b).**

---

**Practice questions:** [7.12 Application Layer Protocols](../ISRO_CS_Question_Bank/07_Computer_Networks/7.12_Application_Layer_Protocols.md)
