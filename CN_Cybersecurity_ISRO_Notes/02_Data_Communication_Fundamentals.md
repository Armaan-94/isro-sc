# 02. Data Communication Fundamentals

> **What this chapter gives you.** The formulas that every later numerical uses: bit rate vs baud rate, transmission vs propagation delay, throughput, and the two channel-capacity limits (Nyquist and Shannon). Learn the **meaning** of each and the numericals become arithmetic.

---

## 1. Signals: analog vs digital

| | Analog | Digital |
|---|---|---|
| Values | Continuous, infinitely many | Discrete, limited levels (e.g. 0 and 1) |
| Waveform | Smooth (e.g. sine wave) | Square-ish steps |
| Noise | Accumulates; hard to remove | Can be **regenerated** (repeaters restore clean bits) |
| Examples | Human voice, old telephone, thermometer reading | Computer data, CDs, USB |

**Data** can be analog or digital, and so can **signals**. We often convert: digital data → analog signal (modem, Wi-Fi), analog data → digital signal (PCM in phones).

### Periodic signal properties

- **Frequency (f):** cycles per second, in **Hz**.
- **Period (T):** time for one cycle. **T = 1/f.**
- **Amplitude:** peak strength.
- **Phase:** position of the wave relative to time 0 (degrees).
- **Wavelength (λ) = propagation speed / f.**

Example: f = 500 Hz → T = 1/500 = **2 ms**. f = 1 kHz → T = 1 ms. 1 MHz → 1 µs.

### Bandwidth (two meanings)

- In **Hz**: the range of frequencies a channel passes (f_high − f_low). A voice line passes 300 to 3300 Hz → bandwidth **3000 Hz**.
- In **bps**: the data rate a link supports.

---

## 2. Bit rate vs baud rate

- **Bit rate (N):** bits per second.
- **Baud rate (S), signal rate:** signal elements (symbols) per second.
- **n:** bits carried per signal element. If there are L distinct signal levels/symbols, **n = log₂ L**.

```
Bit rate = Baud rate × n
```

Example: 2400 baud, 16 symbols → n = 4 → bit rate = **9600 bps**.
Example: Manchester encoding carries **0.5 bit per signal element** (two signal changes per bit): a 10 Mbps Manchester stream needs **20 Mbaud**.

Analogy: baud rate = number of trucks per hour; n = boxes per truck; bit rate = boxes per hour.

---

## 3. Delays: the four components

### 3.1 Transmission delay (Tt)

Time to **push all bits of the packet onto the link**.

```
Tt = Packet size (bits) / Bandwidth (bps)
```

Example: 1500 bytes on 100 Mbps: 12,000 / 10⁸ = **120 µs**.

### 3.2 Propagation delay (Tp)

Time for **one bit to travel** from sender to receiver.

```
Tp = Distance / Propagation speed
```

Speed in copper/fibre ≈ **2 × 10⁸ m/s**; in air/vacuum ≈ 3 × 10⁸ m/s.

Example: 2000 km of fibre: 2 × 10⁶ / 2 × 10⁸ = **10 ms**.

**Intuition:** Tt depends on **packet size and link speed** (how fast you can feed bits in). Tp depends on **distance and medium** (how long each bit takes to fly). A faster link reduces Tt, not Tp.

### 3.3 Queuing delay

Waiting in router buffers. Depends on traffic; varies.

### 3.4 Processing delay

Router time to examine the header, check errors, decide the output port.

```
Total delay = Tt + Tp + Queuing + Processing
```

### 3.5 Bandwidth-delay product

```
BDP = Bandwidth × Propagation delay       (bits "on the wire")
```

Think of the link as a pipe: BDP is the **volume** of the pipe. Example: 1 Gbps × 10 ms = 10⁷ bits in flight. (Some questions use RTT instead of one-way Tp; read carefully.) This idea drives window-size calculations in Chapter 04.

---

## 4. Throughput vs bandwidth

- **Bandwidth:** the theoretical maximum rate of the link.
- **Throughput:** the **actual** rate of successful delivery. Always **≤ bandwidth** (overheads, errors, congestion, protocol waits).

Example: a 10 Mbps link carries 12,000 frames per minute, each 10,000 bits.
- Frames per second = 12000/60 = 200.
- Throughput = 200 × 10,000 = **2 Mbps**.
- Utilisation = 2/10 = **20%**.

---

## 5. Channel capacity: Nyquist and Shannon

How fast can we possibly send data over a channel?

### 5.1 Nyquist (noiseless channel)

```
Max bit rate = 2 × B × log₂ L
```

B = bandwidth in Hz, L = number of signal levels.

Example: B = 3000 Hz, L = 2 → 2 × 3000 × 1 = **6000 bps**.
Example: B = 3000 Hz, L = 4 → 2 × 3000 × 2 = **12,000 bps**.
Example: send 265 kbps over a noiseless 20 kHz channel. 265,000 = 2 × 20,000 × log₂ L → log₂ L = 6.625 → L = 2^6.625 ≈ 98.7. Levels must be a power of 2 in practice, so use **128 levels** (or reduce the rate to 240 kbps with 64 levels).

Nyquist suggests "just add more levels", but in reality, more levels are closer together and **noise** makes them hard to distinguish. That's where Shannon comes in.

### 5.2 Shannon (noisy channel)

```
C = B × log₂(1 + SNR)
```

SNR = signal power / noise power (a **ratio**, not dB).

**Converting from dB:** SNR_dB = 10 log₁₀(SNR) → SNR = 10^(SNR_dB/10).
- 10 dB → 10. 20 dB → 100. 30 dB → 1000. 3 dB ≈ 2.

Examples:
- B = 3 kHz, SNR = 7 → C = 3000 × log₂ 8 = **9000 bps**.
- B = 1 MHz, SNR = 63 → C = 10⁶ × 6 = **6 Mbps**.
- Noiseless channel? SNR → ∞, capacity → ∞ (theoretically).
- SNR = 0 (noise only) → C = B log₂ 1 = **0**.

### 5.3 Using both together (classic two-step problem)

"Channel of 1 MHz with SNR 63. What bit rate and how many signal levels?"

1. Shannon gives the upper limit: C = 10⁶ × log₂ 64 = 6 Mbps. Choose something lower for safety, say **4 Mbps**.
2. Nyquist gives the levels: 4 × 10⁶ = 2 × 10⁶ × log₂ L → log₂ L = 2 → **L = 4**.

| | Nyquist | Shannon |
|---|---|---|
| Channel | Noiseless (ideal) | Noisy (real) |
| Depends on | Bandwidth and number of levels | Bandwidth and SNR |
| Gives | Bit rate for a chosen number of levels | The **absolute** upper limit, regardless of levels |

> **Trap.** Use Shannon whenever SNR or noise is mentioned. If both are computed for the same channel, the achievable rate is the **smaller**.

---

## 6. Transmission impairments

- **Attenuation:** loss of signal strength over distance. Fixed with **amplifiers** (analog) or **repeaters** (digital). Measured in **decibels**: dB = 10 log₁₀(P2/P1). Negative = loss.
  - Example: power halves → 10 log₁₀(0.5) = **−3 dB**.
- **Distortion:** the signal changes shape because different frequency components travel at different speeds.
- **Noise:** thermal, induced, crosstalk, impulse noise.

---

## 7. Encoding and modulation

### 7.1 Digital data → digital signal (line coding)

| Scheme | Idea | Bits per signal element | Notes |
|---|---|---|---|
| **Unipolar NRZ** | 1 = +V, 0 = 0 V | 1 | DC component, poor sync |
| **NRZ-L** | Level decides the bit (+V / −V) | 1 | Long runs of 0s or 1s lose sync |
| **NRZ-I** | **Transition** at the start of the bit = 1, no transition = 0 | 1 | Solves long 1s, not long 0s |
| **RZ** | Signal returns to 0 in mid-bit | 0.5 | Uses 3 levels, needs more bandwidth |
| **Manchester** | Transition **in the middle of every bit**: (IEEE 802.3) low-to-high = 1, high-to-low = 0 | 0.5 | **Self-clocking**; used in 10 Mbps Ethernet |
| **Differential Manchester** | Always a mid-bit transition; a transition at the **start** means 0, none means 1 | 0.5 | Used in Token Ring |
| **AMI (bipolar)** | 0 = 0 V, 1 = alternating +V and −V | 1 | No DC, error detection |

Higher bits per symbol = more efficient but needs a cleaner channel.

**Block coding (mB/nB):** replace each m-bit group with an n-bit code (n > m) chosen to guarantee enough transitions and some error detection. Example: **4B/5B** (100 Mbps Ethernet), **8B/10B** (Gigabit Ethernet). Overhead: 4B/5B uses 25% extra bits.

**Scrambling (B8ZS, HDB3):** replace long runs of zeros with special patterns, adding **no** extra bits, to keep synchronisation.

### 7.2 Analog data → digital signal: PCM

**Pulse Code Modulation:**
1. **Sampling** (Nyquist sampling theorem: sample at **at least 2 × highest frequency**).
2. **Quantisation** (round each sample to one of L levels).
3. **Encoding** (each level as n = log₂ L bits).

```
Bit rate = sampling rate × bits per sample
```

Example: voice up to 4 kHz → sample at 8000/s; 8 bits per sample → **64 kbps** (the standard digital telephone channel, DS0).

### 7.3 Digital data → analog signal (modulation)

- **ASK** (amplitude shift keying): vary amplitude.
- **FSK** (frequency shift keying): vary frequency.
- **PSK** (phase shift keying): vary phase. BPSK = 1 bit/symbol, QPSK = 2 bits/symbol.
- **QAM** (quadrature amplitude modulation): vary **amplitude and phase together**. 16-QAM = 4 bits/symbol, 64-QAM = 6, 256-QAM = 8, 1024-QAM = 10, 4096-QAM = 12 (Wi-Fi 7).

### 7.4 Base64

Encodes binary data as printable text (for email, JSON): **3 bytes (24 bits) → four 6-bit groups → 4 characters**. Size grows by **4/3** (about 33%).

### 7.5 Fixed vs variable-length codes

- Fixed: ASCII (7 bits), EBCDIC (8 bits), Unicode (UTF-16/32; UTF-8 is variable 1 to 4 bytes).
- Variable: **Huffman** (frequent symbols get shorter codes).

---

## 8. Multiplexing (sharing one link among many signals)

| Type | Shares by | Used for |
|---|---|---|
| **FDM** | Frequency bands | Radio/TV broadcast, old telephone trunks |
| **WDM** | Wavelengths of light (FDM for fibre) | Optical fibre backbones |
| **TDM** (synchronous) | Fixed time slots, round-robin | T1/E1 lines |
| **Statistical TDM** | Slots only to inputs that have data | Packet networks |

**TDM numerical:** 4 input lines of 1 kbps each, synchronous TDM with 1 bit per slot: output rate = **4 kbps**; each frame carries 4 bits; frame duration = 1 ms.

**T1 line:** 24 channels × 8 bits + 1 framing bit = 193 bits per frame, 8000 frames/s → **1.544 Mbps**. **E1:** 32 channels × 8 bits × 8000 = **2.048 Mbps**.

---

## 9. Exam traps

1. T = 1/f.
2. Bit rate = baud × log₂ L. Manchester: baud = 2 × bit rate.
3. Tt depends on size/bandwidth; Tp depends on distance/speed.
4. Throughput ≤ bandwidth.
5. Nyquist: 2B log₂ L (noiseless). Shannon: B log₂(1 + SNR) (noisy). Convert dB first!
6. PCM sampling ≥ 2 × max frequency.
7. Manchester is self-clocking but needs double the bandwidth.
8. QAM combines amplitude and phase.
9. Base64: 3 bytes → 4 characters.

---

## 10. Practice questions

**Q1.** A signal has period 0.25 ms. Its frequency?
(a) 250 Hz (b) 4 kHz (c) 400 Hz (d) 40 kHz

**Answer: (b).** 1/0.00025 = 4000 Hz.

---

**Q2.** A modem uses 64 different signal elements at 1000 baud. Bit rate?
(a) 64 kbps (b) 6000 bps (c) 1000 bps (d) 8000 bps

**Answer: (b).** log₂ 64 = 6; 6 × 1000.

---

**Q3.** Noiseless channel, bandwidth 20 kHz, 8 signal levels. Maximum bit rate?
(a) 60 kbps (b) 120 kbps (c) 160 kbps (d) 40 kbps

**Answer: (b).** 2 × 20,000 × 3.

---

**Q4.** Channel bandwidth 4 kHz, SNR 30 dB. Shannon capacity is about:
(a) 12 kbps (b) 40 kbps (c) 120 kbps (d) 4 kbps

**Answer: (b).** SNR = 1000. log₂ 1001 ≈ 9.97. C ≈ 4000 × 9.97 ≈ 39.9 kbps.

---

**Q5.** Transmission delay for a 1 KB packet (1024 bytes) on a 1 Mbps link?
(a) 1.024 ms (b) 8.192 ms (c) 8 ms (d) 1 ms

**Answer: (b).** 8192 bits / 10⁶ bps = 8.192 ms.

---

**Q6.** Propagation delay over 3000 km of fibre (speed 2 × 10⁸ m/s)?
(a) 1.5 ms (b) 15 ms (c) 10 ms (d) 150 ms

**Answer: (b).** 3 × 10⁶ / 2 × 10⁸ = 0.015 s.

---

**Q7.** A 1 Gbps link has a one-way propagation delay of 5 ms. Bandwidth-delay product?
(a) 5 × 10⁵ bits (b) 5 × 10⁶ bits (c) 5 × 10⁷ bits (d) 5 × 10⁹ bits

**Answer: (b).** 10⁹ × 0.005 = 5 × 10⁶ bits (5 megabits, or 625 kilobytes). In the exam, watch whether "MB" means megabytes or "Mb" megabits.

---

**Q8.** Voice band up to 4 kHz, quantised to 256 levels. PCM bit rate?
(a) 32 kbps (b) 64 kbps (c) 128 kbps (d) 8 kbps

**Answer: (b).** Sample at 8000/s × 8 bits.

---

**Q9.** Which encoding guarantees a transition in the middle of every bit?
(a) NRZ-L (b) NRZ-I (c) Manchester (d) AMI

**Answer: (c).**

---

**Q10.** 300 bytes of binary data encoded in Base64 produce how many characters?
(a) 300 (b) 400 (c) 450 (d) 600

**Answer: (b).** 300/3 × 4 = 400.

---

**Q11.** A signal's power drops from 100 mW to 10 mW. The loss in dB is:
(a) −10 dB (b) −20 dB (c) −1 dB (d) −90 dB

**Answer: (a).** 10 log₁₀(10/100) = −10 dB.

---

**Q12.** Which theorem gives the theoretical maximum data rate of a **noisy** channel?
(a) Nyquist (b) Shannon (c) Fourier (d) Huffman

**Answer: (b).**

---

**Q13.** Which is the correct formula relating bit rate (N), baud (S) and signal levels (L)?
(a) N = S × L (b) N = S × log₂ L (c) S = N × log₂ L (d) N = S / log₂ L

**Answer: (b).**

---

**Q14.** In synchronous TDM, 5 sources each send 100 kbps, interleaved one byte at a time. Output link rate (no overhead)?
(a) 100 kbps (b) 500 kbps (c) 50 kbps (d) 800 kbps

**Answer: (b).**
