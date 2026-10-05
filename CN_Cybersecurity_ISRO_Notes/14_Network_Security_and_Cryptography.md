# 14. Network Security Fundamentals and Cryptography

> **Security in one picture.** Alice wants to send Bob a message across a network that Eve can listen to, tamper with, or impersonate on. Every security tool exists to stop one of Eve's tricks. Ask "which trick does this stop?" and every concept in this chapter clicks into place.

---

## 1. Security goals (ITU-T X.800 security architecture)

### The CIA triad

| Goal | Meaning | Tools | Threats |
|---|---|---|---|
| **Confidentiality** | Only authorised parties can **read** the data | Encryption, access control, VPN, TLS | Eavesdropping, traffic analysis |
| **Integrity** | Data isn't **altered** undetected | Hashes, MACs, digital signatures | Modification, replay |
| **Availability** | Systems are **usable** when needed | Redundancy, backups, load balancing, DDoS protection | **DoS/DDoS**, hardware failure |

### Plus two more

- **Authentication:** "Are you who you claim to be?"
  - **Peer-entity authentication:** verifying the other party at the start of a session.
  - **Data-origin authentication:** verifying the sender of each message.
- **Non-repudiation:** the sender (or receiver) **can't later deny** sending (or receiving). Proof of origin / proof of receipt.

> **Trap.** Cryptography provides confidentiality, integrity, authentication and non-repudiation, **but not availability**. You can't encrypt your way out of a DDoS attack.

---

## 2. Attacks

### Passive vs active

| | Passive | Active |
|---|---|---|
| Modifies data? | **No** (just watches) | **Yes** |
| Detection | **Hard** | Easier |
| Prevention | **Easier** (encryption) | Harder |
| Threatens | Confidentiality | Integrity, availability, authentication |
| Types | **Release of message contents** (eavesdropping), **traffic analysis** | **Masquerade**, **replay**, **modification**, **denial of service** |

- **Traffic analysis:** even if messages are encrypted, Eve learns **who** talks to **whom**, **when**, **how often**, **how much**. Encryption doesn't hide this metadata.
- **Masquerade:** pretending to be someone else (often using stolen credentials).
- **Replay:** capturing a **valid** message and resending it later (e.g. a "transfer ₹1000" message replayed ten times). Defences: timestamps, nonces, sequence numbers.
- **Modification:** altering a message in transit.
- **Denial of Service (DoS):** making a service unavailable (flooding). **DDoS:** from many machines (a botnet).

### Common named attacks (recall)

- **Man-in-the-middle (MITM):** Eve sits between Alice and Bob, relaying and possibly altering messages, while each thinks they talk directly.
- **Phishing:** fake emails/sites tricking users into revealing secrets.
- **Spoofing:** faking an IP, MAC, email sender or DNS reply.
- **SQL injection:** malicious SQL inserted into input fields.
- **Cross-site scripting (XSS):** injecting scripts into web pages viewed by others.
- **Brute force / dictionary attacks** on passwords and keys.

### Malware

| Type | Key feature |
|---|---|
| **Virus** | Attaches to a host program; spreads when the program runs (needs a host and user action) |
| **Worm** | **Self-replicating**, spreads over networks **without** a host program |
| **Trojan horse** | Looks useful but hides malicious function; doesn't self-replicate |
| **Ransomware** | Encrypts your files, demands payment |
| **Spyware / keylogger** | Secretly collects information |
| **Rootkit** | Hides deep in the OS to keep privileged access |
| **Logic bomb** | Triggers on a condition/date |
| **Backdoor** | Secret way to bypass authentication |

---

## 3. Security services and the "guarantee ladder"

X.800 services: **authentication, access control, data confidentiality, data integrity, non-repudiation**. (Access control decides what an **authenticated** user may do.)

```
Hash function       ->  Integrity only
MAC (keyed hash)    ->  Integrity + Authentication
Digital signature   ->  Integrity + Authentication + Non-repudiation
```

([Chapter 15](15_Hash_MAC_Digital_Signature_Authentication_Tools.md) covers these in detail.)

---

## 4. Cryptography vocabulary

- **Plaintext:** the original readable message.
- **Ciphertext:** the scrambled output.
- **Encryption:** plaintext → ciphertext using a **key**. **Decryption:** the reverse.
- **Cipher:** the algorithm (AES, RSA, ...). **Key:** the secret parameter.
- **Cryptography:** designing ciphers. **Cryptanalysis:** breaking them without the key. **Cryptology** = both.

### Kerckhoffs's principle

A cryptosystem should remain secure **even if everything about it except the key is public**. Security must rest on the **key**, not on hiding the algorithm ("security through obscurity" is weak). That's why AES and RSA are fully public.

### Brute force

A key of k bits has **2^k** possibilities; on average an attacker must try **2^(k−1)**. Each extra bit **doubles** the work.

---

## 5. Classical ciphers (for intuition)

### Substitution: Caesar cipher

Shift each letter by a fixed amount k. With k = 3: A→D, B→E, ..., Z→C.
`HELLO` → `KHOOR`.
Only 25 possible keys: trivially brute-forced. Also vulnerable to **frequency analysis** (E is the most common English letter).

**Monoalphabetic substitution** (any permutation of the alphabet): 26! keys, but still broken by frequency analysis.

**Polyalphabetic (Vigenère):** the shift changes with each letter according to a keyword. Hides single-letter frequencies better.

**One-time pad:** a truly random key as long as the message, used **once**. **Perfectly secure** in theory, impractical (key distribution).

### Transposition

Rearrange the **positions** of letters without changing them. E.g. write row-wise, read column-wise. Letter frequencies stay the same (a clue for cryptanalysts).

Modern ciphers combine **substitution (confusion)** and **transposition/permutation (diffusion)** over many rounds (Shannon's principles).

---

## 6. Symmetric vs asymmetric cryptography

### 6.1 Symmetric (secret-key)

**One shared key** for both encryption and decryption.

```
Alice: C = E(K, P)  ------>  Bob: P = D(K, C)
```

- **Fast.** Ideal for **bulk data**.
- **Key distribution problem:** how do Alice and Bob share K securely in the first place?
- **Scalability:** n users each needing a private channel with every other need **n(n − 1)/2** keys. (10 users → 45; 100 users → 4950; 1000 users → 499,500.)
- **No non-repudiation:** both have the same key, so either could have produced a message.
- Examples: **DES, 3DES, AES**, Blowfish, RC4, ChaCha20.

### 6.2 Asymmetric (public-key)

Each user has a **key pair**: a **public key** (shared with everyone) and a **private key** (kept secret). What one key encrypts, **only the other key of the same pair** decrypts.

| Goal | Encrypt with | Decrypt with |
|---|---|---|
| **Confidentiality** (only Bob can read) | **Bob's public** key | **Bob's private** key |
| **Signature** (proves Alice sent it) | **Alice's private** key | **Alice's public** key |

- Solves key distribution (public keys can be published).
- Enables **digital signatures** and **non-repudiation**.
- **Slow** (1000 times slower than symmetric); not for bulk data.
- n users need **2n** keys in total (n pairs).
- Examples: **RSA**, **Diffie-Hellman**, ElGamal, **ECC** (elliptic curve).

### 6.3 Hybrid (how real systems like TLS work)

Use **asymmetric** crypto to **exchange/agree on a symmetric session key**, then use **symmetric** crypto (AES) for the actual data. Best of both.

| Feature | Symmetric | Asymmetric |
|---|---|---|
| Keys | 1 shared | 2 per user (public + private) |
| Speed | **Fast** | Slow |
| Key distribution | Hard | Easy |
| Keys for n users | n(n − 1)/2 | 2n |
| Non-repudiation | No | Yes (signatures) |
| Use | Bulk encryption | Key exchange, signatures |

---

## 7. Block ciphers and stream ciphers

- **Block cipher:** encrypts fixed-size **blocks** (64 or 128 bits). Needs **padding** for the last block. DES, AES.
- **Stream cipher:** encrypts bit/byte by byte by XORing with a **keystream**. No padding, low latency, good for real-time. RC4 (now broken), Salsa20, **ChaCha20**. Never reuse the same keystream (key + nonce) twice.

### Modes of operation (for block ciphers)

| Mode | Idea | Note |
|---|---|---|
| **ECB** (Electronic Codebook) | Each block encrypted independently | **Identical plaintext blocks → identical ciphertext blocks**: leaks patterns. Avoid |
| **CBC** (Cipher Block Chaining) | XOR each plaintext block with the previous ciphertext block (IV for the first) | Hides patterns; sequential encryption |
| **CFB** (Cipher Feedback) | Turns the block cipher into a self-synchronising stream cipher | |
| **OFB** (Output Feedback) | Generates a keystream independent of the data | Errors don't propagate |
| **CTR** (Counter) | Encrypts a counter to make a keystream | Parallelisable, random access; basis of **GCM** |

---

## 8. DES, 3DES, AES

| | DES | 3DES | AES |
|---|---|---|---|
| Year | 1977 (IBM, NBS/NIST) | 1998 | **2001** (NIST; Rijndael by Daemen and Rijmen) |
| Block size | **64 bits** | 64 bits | **128 bits** |
| Key size | 64 bits with 8 parity → **56 effective** | 112 or 168 bits | **128, 192 or 256** |
| Rounds | **16** | 48 (3 × 16) | **10, 12, 14** (for 128/192/256-bit keys) |
| Structure | **Feistel network** | DES applied as **Encrypt-Decrypt-Encrypt** (EDE) | **Substitution-permutation network** (not Feistel) |
| Status | **Broken** by brute force (1998) | Deprecated | **Current standard** |

Why 3DES uses E-D-E: with K1 = K2 = K3, E-D-E collapses to single DES, keeping backward compatibility.

Why not "double DES"? The **meet-in-the-middle** attack reduces its strength to about 2⁵⁷, barely better than single DES.

---

## 9. RSA (Rivest, Shamir, Adleman, 1977)

Security rests on the difficulty of **factoring** a large number n = p × q.

### Key generation

1. Choose two large primes **p** and **q**.
2. **n = p × q** (the modulus; part of both keys).
3. **φ(n) = (p − 1)(q − 1)**.
4. Choose **e** with 1 < e < φ(n) and **gcd(e, φ(n)) = 1**.
5. Find **d** such that **e × d ≡ 1 (mod φ(n))**.
6. **Public key = (e, n). Private key = (d, n).**

### Encryption / decryption

```
C = M^e mod n          (encrypt with the public key)
M = C^d mod n          (decrypt with the private key)
```

### Worked example 1

p = 3, q = 11.
- n = 33, φ = 2 × 10 = 20.
- e = 7 (gcd(7, 20) = 1).
- d: 7d ≡ 1 (mod 20) → d = 3 (7 × 3 = 21 = 20 + 1).
- Public (7, 33), private (3, 33).
- Encrypt M = 2: C = 2⁷ mod 33 = 128 mod 33 = 128 − 99 = **29**.
- Decrypt: 29³ mod 33. 29 ≡ −4 (mod 33); (−4)³ = −64; −64 + 66 = **2** ✓.

### Worked example 2

p = 7, q = 11 → n = 77, φ = 60. e = 7. d: 7d ≡ 1 (mod 60) → d = **43** (7 × 43 = 301 = 5 × 60 + 1).

### Worked example 3

p = 5, q = 11 → n = 55, φ = 40. e = 3. d: 3d ≡ 1 (mod 40) → d = **27** (81 = 2 × 40 + 1).

**Finding d quickly:** try d = (k × φ + 1)/e for k = 1, 2, 3, ... until it's an integer.

Uses: encryption of small data (like session keys), **digital signatures**, key exchange in TLS.

---

## 10. Diffie-Hellman key exchange (1976)

**Not an encryption algorithm.** It lets two parties **agree on a shared secret** over a public channel **without ever sending the secret**.

### Steps

Public: a large prime **p** and a generator **g**.

1. Alice picks secret **a**, sends **A = g^a mod p**.
2. Bob picks secret **b**, sends **B = g^b mod p**.
3. Alice computes **K = B^a mod p**. Bob computes **K = A^b mod p**.
4. Both get **g^(ab) mod p**.

Eve sees p, g, A, B but would need to solve the **discrete logarithm problem** to find a or b.

### Worked example

p = 23, g = 5, a = 6, b = 15.
- A = 5⁶ mod 23. 5² = 25 ≡ 2; 5⁴ ≡ 4; 5⁶ = 5⁴ × 5² ≡ 8. **A = 8.**
- B = 5¹⁵ mod 23. 5⁸ ≡ 16; 5¹⁵ = 5⁸ × 5⁴ × 5² × 5 ≡ 16 × 4 × 2 × 5 = 640 ≡ 640 − 621 = **19**.
- Alice: 19⁶ mod 23. 19 ≡ −4; (−4)⁶ = 4096; 4096 − 23 × 178 = 4096 − 4094 = **2**.
- Bob: 8¹⁵ mod 23. 8² ≡ 18; 8⁴ ≡ 18² = 324 ≡ 2; 8⁸ ≡ 4; 8¹⁵ = 8⁸ × 8⁴ × 8² × 8 ≡ 4 × 2 × 18 × 8 = 1152 ≡ 1152 − 1150 = **2**.
- **Shared key = 2** ✓.

### Weakness

**No authentication**: vulnerable to **man-in-the-middle**. Eve can do one DH exchange with Alice and another with Bob. Fix: authenticate the exchanged values (signatures, certificates), as TLS does.

| | RSA | Diffie-Hellman |
|---|---|---|
| Purpose | Encryption and signatures | **Key agreement only** |
| Encrypts data? | Yes | **No** |
| Provides authentication alone? | Yes (signatures) | **No** |
| Hard problem | Integer factorisation | Discrete logarithm |

---

## 11. Exam traps

1. Cryptography doesn't provide **availability**.
2. Passive attacks: no modification, hard to detect, prevented by encryption. Traffic analysis is passive.
3. Replay reuses a **valid** message; defend with nonces/timestamps.
4. Symmetric keys for n users: **n(n − 1)/2**. Asymmetric: **2n** keys.
5. Confidentiality: encrypt with the **receiver's public** key. Signature: sign with the **sender's private** key.
6. DES: 64-bit block, **56-bit** effective key, **16** Feistel rounds. AES: **128-bit** block, 128/192/256-bit keys, 10/12/14 rounds, not Feistel.
7. ECB leaks patterns.
8. RSA: e·d ≡ 1 mod φ(n), φ = (p − 1)(q − 1).
9. Diffie-Hellman exchanges keys, doesn't encrypt; vulnerable to MITM.
10. Kerckhoffs: only the key needs to be secret.

---

## 12. Practice questions

**Q1.** 50 users each need a unique symmetric key with every other user. Number of keys?
(a) 50 (b) 100 (c) 1225 (d) 2500

**Answer: (c).** 50 × 49 / 2.

---

**Q2.** Same 50 users with public-key cryptography. Total keys?
(a) 50 (b) 100 (c) 1225 (d) 2450

**Answer: (b).** One public + one private each.

---

**Q3.** To send Bob a confidential message using public-key crypto, Alice encrypts with:
(a) her private key (b) her public key (c) Bob's public key (d) Bob's private key

**Answer: (c).**

---

**Q4.** RSA with p = 7, q = 13, e = 5. The value of d is:
(a) 5 (b) 29 (c) 13 (d) 43

**Answer: (b).** n = 91, φ = 6 × 12 = 72. Need 5d ≡ 1 (mod 72). Try (k × 72 + 1)/5: k = 1 → 73/5 no; k = 2 → 145/5 = **29** ✓.

(Exam subtlety: for some small examples, d turns out equal to e, e.g. p = 5, q = 7, e = 5 gives φ = 24 and 5 × 5 = 25 ≡ 1, so d = 5. Always take the smallest positive solution of the congruence.)

---

**Q5.** RSA public key (e = 3, n = 33). Encrypt M = 4.
(a) 64 (b) 31 (c) 12 (d) 4

**Answer: (b).** 4³ = 64; 64 mod 33 = 31.

---

**Q6.** Diffie-Hellman with p = 11, g = 2, Alice's secret a = 3, Bob's secret b = 4. Shared key?
(a) 3 (b) 4 (c) 5 (d) 9

**Answer: (b).** A = 2³ mod 11 = 8. B = 2⁴ mod 11 = 16 mod 11 = 5. Alice: 5³ = 125 mod 11 = 125 − 121 = 4. Bob: 8⁴ = 4096 mod 11: 8² = 64 ≡ 9, 9² = 81 ≡ 4. Both get **4**.

---

**Q7.** Which attack is passive?
(a) Replay (b) Masquerade (c) Traffic analysis (d) Denial of service

**Answer: (c).**

---

**Q8.** Effective key length of DES?
(a) 64 (b) 56 (c) 128 (d) 48

**Answer: (b).**

---

**Q9.** AES block size?
(a) 64 bits (b) 128 bits (c) 192 bits (d) 256 bits

**Answer: (b).** (Key sizes vary; the block size is always 128.)

---

**Q10.** Which mode of operation produces identical ciphertext for identical plaintext blocks?
(a) CBC (b) CTR (c) ECB (d) OFB

**Answer: (c).**

---

**Q11.** Caesar cipher with key 3 encrypts "ISRO" as:
(a) LVUR (b) JTSP (c) KURQ (d) LVRU

**Answer: (a).** I→L, S→V, R→U, O→R.

---

**Q12.** Diffie-Hellman by itself is vulnerable to:
(a) brute force only (b) man-in-the-middle (c) replay only (d) nothing

**Answer: (b).**

---

**Q13.** Which is NOT a symmetric algorithm?
(a) AES (b) 3DES (c) RSA (d) Blowfish

**Answer: (c).**

---

**Q14.** A worm differs from a virus mainly because a worm:
(a) needs a host program (b) self-replicates across networks without a host program (c) is always harmless (d) only encrypts files

**Answer: (b).**

---

**Q15.** Kerckhoffs's principle says security should depend only on the secrecy of:
(a) the algorithm (b) the key (c) the ciphertext (d) the protocol

**Answer: (b).**

---

**Practice questions:** [7.14 Network Security Cryptography](../ISRO_CS_Question_Bank/07_Computer_Networks/7.14_Network_Security_Cryptography.md)
