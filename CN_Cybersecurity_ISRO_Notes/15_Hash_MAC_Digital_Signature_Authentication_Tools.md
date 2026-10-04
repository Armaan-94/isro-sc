# 15. Hashes, MACs, Digital Signatures, PKI, Authentication and Security Tools

> **Where Chapter 14 left off.** Encryption hides data. But how do we know data wasn't **changed**, who **sent** it, and that the sender can't **deny** it later? That's what hashes, MACs and signatures do. Then we look at how keys are trusted (certificates, PKI), how users prove identity (passwords to Kerberos), and the tools that guard networks (firewalls, IDS/IPS, VPNs, TLS).

---

## 1. Cryptographic hash functions

A **hash function** H takes input of **any length** and produces a **fixed-length** output (the **digest** or **fingerprint**): h = H(M).

### Properties of a good cryptographic hash

1. **Fixed-length output** regardless of input size.
2. **Deterministic:** same input, same output.
3. **Fast** to compute.
4. **One-way (preimage resistance):** given h, infeasible to find any M with H(M) = h.
5. **Second-preimage resistance:** given M, infeasible to find a different M' with H(M') = H(M).
6. **Collision resistance:** infeasible to find **any** two different messages with the same hash.
7. **Avalanche effect:** changing one input bit changes about half the output bits.

> **Trap.** Hashing is **not encryption**. There's **no key** and **no decryption**. A hash gives **integrity only**, not confidentiality.

### Birthday paradox

For an n-bit hash, finding **some** collision takes only about **2^(n/2)** attempts (not 2ⁿ). So a 128-bit hash gives only ~64-bit collision security. That's why 256-bit hashes are now standard.

### Common algorithms

| Algorithm | Output | Status |
|---|---|---|
| **MD5** | **128 bits** | **Broken** (practical collisions). Don't use for security |
| **SHA-1** | **160 bits** | **Deprecated** (collisions demonstrated, 2017) |
| **SHA-256** | 256 bits | Recommended (SHA-2 family) |
| SHA-384 / SHA-512 | 384 / 512 bits | Stronger SHA-2 variants |
| **SHA-3** | 224 to 512 (variable) | Modern (Keccak); different internal design |

### Uses

File integrity checks (downloads), password storage (with **salt** and slow hashes like bcrypt), digital signatures (sign the hash), blockchains, MACs.

**Salting:** add a random value to each password before hashing, so identical passwords produce different hashes and precomputed **rainbow tables** don't work.

---

## 2. MAC and HMAC

### 2.1 The problem with a bare hash

If Alice sends (M, H(M)), Eve can change M to M' and send (M', H(M')). Bob can't tell. A hash proves integrity **only if the hash itself is protected**.

### 2.2 MAC (Message Authentication Code)

Mix in a **shared secret key** K:

```
MAC = F(K, M)
```

Bob, who also has K, recomputes the MAC and compares. Eve can't forge it without K.

- Provides **integrity + authentication** (only someone with K could have made it).
- **No non-repudiation:** Alice and Bob **share** K, so either could have produced the MAC. Bob can't prove to a judge that Alice sent it.

### 2.3 HMAC

A standard, secure way to build a MAC from a hash function:

```
HMAC(K, M) = H( (K ⊕ opad) || H( (K ⊕ ipad) || M ) )
```

Used in TLS, IPsec, JWTs, APIs. E.g. HMAC-SHA256.

| | Hash | MAC / HMAC | Digital signature |
|---|---|---|---|
| Key | None | **Shared secret** | **Private key** to sign, public to verify |
| Integrity | ✓ | ✓ | ✓ |
| Authentication | ✗ | ✓ | ✓ |
| Non-repudiation | ✗ | **✗** | **✓** |

---

## 3. Digital signatures

### 3.1 How they work

**Signing (Alice):**
1. Compute the digest: h = H(M).
2. **Encrypt h with Alice's private key**: S = E(PR_Alice, h). That's the signature.
3. Send **(M, S)**.

**Verifying (Bob):**
1. **Decrypt S with Alice's public key**: h1 = D(PU_Alice, S).
2. Compute h2 = H(M) himself.
3. If h1 = h2 → **valid**: the message is unchanged (integrity) and was signed by whoever holds Alice's private key (authentication), and Alice can't deny it (non-repudiation).

Why sign the **hash** instead of the whole message? Asymmetric operations are slow; the hash is short.

### 3.2 What a signature gives (and doesn't)

- ✓ **Authentication**, ✓ **integrity**, ✓ **non-repudiation**.
- ✗ **Confidentiality**: M is sent in the clear. To also hide it, **encrypt** (e.g. with Bob's public key or a session key) in addition to signing.

> **Trap.** For **signatures** you use the **sender's private** key; for **confidentiality** you use the **receiver's public** key. Opposite roles.

### 3.3 Signature algorithms

**RSA signatures**, **DSA** (Digital Signature Algorithm, NIST), **ECDSA** (elliptic curve), EdDSA.

---

## 4. Digital certificates and PKI

### 4.1 The problem

Public keys solve key distribution only if you're sure a public key **really belongs** to who it claims. Otherwise Eve can publish her own key under Bob's name (MITM).

### 4.2 Digital certificate

A document that **binds an identity to a public key**, **signed by a trusted Certificate Authority (CA)**. Standard format: **X.509**. It contains:
- Subject (owner's name, e.g. www.isro.gov.in),
- Subject's **public key**,
- **Issuer** (the CA),
- Serial number,
- **Validity period** (not before / not after),
- Signature algorithm,
- **The CA's digital signature** over all of the above.

To verify: check the CA's signature using the CA's public key (which your browser/OS already trusts), check the dates, check it isn't revoked.

### 4.3 PKI (Public Key Infrastructure)

The whole system that makes public keys trustworthy at scale:
- **Certificate Authority (CA):** issues and signs certificates.
- **Registration Authority (RA):** verifies identities before the CA issues certificates.
- **Certificates**, **key pairs**, repositories.
- **Revocation:** **CRL** (Certificate Revocation List) or **OCSP** (Online Certificate Status Protocol) for compromised or withdrawn certificates.
- **Chain of trust:** root CA → intermediate CA → server certificate. Browsers ship with trusted root certificates.

HTTPS depends entirely on PKI.

---

## 5. User authentication

### 5.1 Factors

| Factor | Examples |
|---|---|
| Something you **know** | Password, PIN |
| Something you **have** | Phone (OTP app), smart card, hardware token |
| Something you **are** | Fingerprint, face, iris (biometrics) |

- **Password:** weakest alone (guessable, reusable, phishable).
- **OTP (One-Time Password):** valid once/briefly (TOTP: time-based; HOTP: counter-based).
- **2FA:** exactly **two different** factors (password + OTP).
- **MFA:** **two or more** factors.

(Two passwords are still **one** factor: both are "something you know".)

### 5.2 Kerberos

A **network authentication** protocol from MIT, used by **Windows Active Directory**.

- Based on **symmetric (secret-key) cryptography** and a trusted third party, the **KDC (Key Distribution Center)**, which has two parts:
  - **AS (Authentication Server):** verifies the user, issues a **TGT (Ticket-Granting Ticket)**.
  - **TGS (Ticket-Granting Server):** using the TGT, issues **service tickets** for specific servers.
- The user's password **never travels over the network**.
- Gives **mutual authentication** and **Single Sign-On (SSO)**: log in once, access many services.
- Tickets have **timestamps/lifetimes**, so clocks must be roughly synchronised; timestamps also prevent replay.

Flow: User → AS (gets TGT) → TGS (shows TGT, gets service ticket) → Server (shows service ticket) → access.

> **Trap.** Kerberos uses **symmetric** keys and a KDC, **not** public-key certificates (that's PKI).

### 5.3 Other mechanisms

- **Challenge-response** (e.g. CHAP): the server sends a random challenge; the client returns a function of the challenge and the secret. The secret never crosses the wire.
- **Needham-Schroeder:** the classic KDC protocol (Kerberos is based on it).
- **RADIUS / TACACS+:** centralised authentication for network access.

---

## 6. Security tools

### 6.1 Firewalls

A firewall sits between a trusted network and an untrusted one and **filters traffic by rules**.

| Type | Works at | Inspects | Notes |
|---|---|---|---|
| **Packet filtering** | Network/transport (L3/L4) | Source/destination IP, ports, protocol, per packet | Fast, simple, **stateless** |
| **Stateful inspection** | L3/L4 | Also tracks **connection state** (is this packet part of an established connection?) | Most common traditional type |
| **Circuit-level gateway** | Session (L5) | Verifies TCP handshakes/sessions, not contents | |
| **Application-level gateway (proxy firewall)** | Application (L7) | **Payload contents** of HTTP, FTP, SMTP | Slowest, most thorough |
| **Next-generation firewall (NGFW)** | L3 to L7 | Adds **deep packet inspection**, intrusion prevention, app awareness | Modern |

**DMZ (demilitarised zone):** a separate network segment for public-facing servers (web, mail), between the Internet and the internal network.

### 6.2 IDS vs IPS

| | IDS (Intrusion Detection System) | IPS (Intrusion Prevention System) |
|---|---|---|
| Action | **Detects and alerts** | **Detects and blocks** automatically |
| Placement | Out of band (monitors a copy of traffic) | **Inline** (traffic passes through it) |
| Passive/active | **Passive** | **Active** |

Detection methods:
- **Signature-based:** matches known attack patterns. Accurate for known attacks, blind to new ones (zero-days).
- **Anomaly-based:** learns "normal" behaviour and flags deviations. Can catch new attacks; more false positives.

Also **HIDS** (host-based) vs **NIDS** (network-based).

> **Trap.** **IDS only alerts. IPS blocks.**

### 6.3 VPN (Virtual Private Network)

An **encrypted tunnel** over a public network, making remote users or sites appear to be on a private network.
- **Remote-access VPN:** a user to the company network.
- **Site-to-site VPN:** connecting two offices.
- Technologies: **IPsec**, SSL/TLS VPNs, WireGuard.

### 6.4 IPsec (network-layer security)

- **AH (Authentication Header):** integrity and authentication, **no encryption**.
- **ESP (Encapsulating Security Payload):** **encryption** plus (optionally) integrity/authentication.
- **Transport mode:** protects the **payload** only (host to host).
- **Tunnel mode:** protects the **entire original IP packet** inside a new one (used by VPN gateways).
- **IKE:** negotiates keys and **Security Associations (SAs)**.

### 6.5 SSL/TLS (transport-layer security)

- **SSL** (Netscape, 1990s) is obsolete; **TLS** is its successor (TLS 1.2, **TLS 1.3** current).
- Secures HTTP (HTTPS, port 443), SMTP, IMAP, etc.
- **Handshake (simplified):**
  1. Client Hello: supported versions, cipher suites, random number.
  2. Server Hello + **certificate** (server's public key, signed by a CA).
  3. Key exchange (e.g. **ECDHE**: Diffie-Hellman with signatures) → both derive the same **session key**.
  4. Finished messages (verified with MACs). Then application data is encrypted with the **symmetric** session key (e.g. AES-GCM).
- Gives confidentiality, integrity, server (optionally client) authentication. ECDHE gives **forward secrecy** (stealing the server's private key later doesn't decrypt past sessions).

### 6.6 Other tools

- **Antivirus/anti-malware**, **honeypots** (decoy systems to lure attackers), **SIEM** (collects and correlates logs), **proxy servers**, **network access control**.

---

## 7. Exam traps

1. Hash: integrity only, no key, not reversible. MAC: integrity + authentication (shared key). Signature: + non-repudiation.
2. MD5 (128-bit) broken; SHA-1 (160-bit) deprecated; SHA-256/SHA-3 recommended.
3. Birthday attack: ~2^(n/2) for collisions.
4. Sign with **sender's private** key; verify with sender's public key.
5. Signatures don't give confidentiality.
6. Certificates bind identity ↔ public key, signed by a **CA**; format X.509.
7. Kerberos: **symmetric** crypto, KDC (AS + TGS), TGT, SSO.
8. 2FA = two **different** factors.
9. IDS alerts; IPS blocks.
10. Application-level gateway inspects payloads.
11. AH: no encryption. ESP: encryption. Tunnel mode: whole packet.
12. TLS uses asymmetric crypto for the handshake, symmetric for data.

---

## 8. Practice questions

**Q1.** Output size of SHA-1?
(a) 128 bits (b) 160 bits (c) 256 bits (d) 512 bits

**Answer: (b).**

---

**Q2.** Which provides integrity and authentication but NOT non-repudiation?
(a) Hash (b) MAC (c) Digital signature (d) Encryption with a public key

**Answer: (b).**

---

**Q3.** Bob verifies Alice's digital signature using:
(a) Alice's private key (b) Alice's public key (c) Bob's private key (d) a shared secret key

**Answer: (b).**

---

**Q4.** For a 128-bit hash, roughly how many random messages must be hashed to find a collision with good probability?
(a) 2¹²⁸ (b) 2⁶⁴ (c) 2³² (d) 128

**Answer: (b).** Birthday bound 2^(n/2).

---

**Q5.** A digital certificate is signed by:
(a) the certificate owner (b) the Certificate Authority (c) the browser (d) the registration authority's user

**Answer: (b).**

---

**Q6.** In Kerberos, the ticket that lets a user request service tickets without re-entering a password is the:
(a) session key (b) TGT (Ticket-Granting Ticket) (c) certificate (d) OTP

**Answer: (b).**

---

**Q7.** Password + PIN is:
(a) 2FA (b) single-factor authentication (c) MFA with three factors (d) biometric authentication

**Answer: (b).** Both are "something you know".

---

**Q8.** Which device detects an attack and automatically blocks it inline?
(a) IDS (b) IPS (c) Packet-filter router (d) Proxy cache

**Answer: (b).**

---

**Q9.** Which IPsec protocol provides encryption?
(a) AH (b) ESP (c) IKE (d) SA

**Answer: (b).**

---

**Q10.** In TLS, bulk application data is encrypted using:
(a) RSA with the server's public key (b) a symmetric session key (c) the CA's private key (d) a hash

**Answer: (b).**

---

**Q11.** Which firewall type can block a specific FTP command inside an allowed connection?
(a) Packet filter (b) Stateful inspection (c) Circuit-level gateway (d) Application-level gateway

**Answer: (d).**

---

**Q12.** Adding a random salt to passwords before hashing mainly defends against:
(a) DoS (b) precomputed rainbow-table attacks (c) replay attacks (d) traffic analysis

**Answer: (b).**

---

**Q13.** Which statement about digital signatures is FALSE?
(a) They provide non-repudiation (b) They detect message modification (c) They keep the message secret (d) They usually sign a hash of the message

**Answer: (c).**

---

**Q14.** A signature-based IDS is weakest against:
(a) known worms (b) zero-day (previously unseen) attacks (c) port scans with known patterns (d) old viruses

**Answer: (b).**
