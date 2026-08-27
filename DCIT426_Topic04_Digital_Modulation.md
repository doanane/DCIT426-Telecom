# DCIT 426 Telecommunications
## Topic 4: Digital Modulation Techniques

**Syllabus line (Week 4):** *ASK, FSK, PSK, QAM; constellation diagrams; bit/baud rate; bandwidth efficiency.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| ASK, FSK, PSK, QAM | Forouzan Ch. 5, Section 5.1; Stallings Ch. 5.2 |
| Constellation diagrams | Forouzan Ch. 5, Section 5.1.5 |
| Bit rate and baud rate | Forouzan Ch. 4, Section 4.1.1 |
| Performance and BER | Haykin & Moher Ch. 7; Rappaport Ch. 6 |

Learning outcome 3 in your outline says: *"Compare digital modulation and multiple-access schemes and justify their use in real systems."* The word **justify** means the exam wants trade-off reasoning, not just definitions.

---

## 4.1 Why modulate at all

**Short answer:** modulation shifts a baseband signal onto a high-frequency carrier so that it can be transmitted efficiently over the medium, and so that many signals can share the medium.

Four concrete reasons:

| Reason | Explanation |
|---|---|
| **Antenna size** | An efficient antenna is roughly λ/4 long. A 3 kHz baseband voice signal has λ = 100 km, requiring a 25 km antenna. Move it to 900 MHz and the antenna is 8 cm |
| **Multiplexing** | Different users can be given different carrier frequencies and share the same medium (Week 5) |
| **Channel matching** | Some media only pass certain frequency ranges. Modulation places the signal where the channel works |
| **Noise and interference** | Higher bands can be chosen to avoid congested or noisy parts of the spectrum |

### The three things you can vary on a carrier

`s(t) = A cos(2πft + φ)`

```
  Vary A  -> AMPLITUDE modulation  -> ASK
  Vary f  -> FREQUENCY modulation  -> FSK
  Vary φ  -> PHASE modulation      -> PSK
  Vary A and φ together            -> QAM
```

That is the whole topic in four lines. Everything else is detail.

---

## 4.2 ASK, Amplitude Shift Keying

**Short answer:** the amplitude of the carrier is changed to represent bits, while frequency and phase stay constant.

The simplest form is **OOK (On-Off Keying)**: carrier present means 1, carrier absent means 0.

```
bits:     1        0        1        1        0
       /\/\/\/            /\/\/\/  /\/\/\/
      /\/\/\/\/  ______  /\/\/\/\ /\/\/\/\  ______
       carrier    silent   carrier  carrier  silent
        on                   on       on
```

**Bandwidth:** `B = (1 + d) x S`, where S is the baud rate and d is a factor between 0 and 1 chosen by the designer (0 in the ideal case). For a simple design B is roughly equal to the baud rate.

**Advantages:** simplest to implement, cheapest hardware.
**Disadvantages:** **extremely vulnerable to noise**, because noise is itself an amplitude variation. Any amplitude disturbance directly corrupts the data. Also inefficient in power.

**Where it is actually used:** optical fibre systems (light on, light off), infrared remote controls, low-cost RFID tags. It survives in fibre precisely because fibre has almost no amplitude noise.

---

## 4.3 FSK, Frequency Shift Keying

**Short answer:** the frequency of the carrier is switched between values to represent bits, with amplitude constant.

**BFSK** (binary) uses two frequencies, f1 for 0 and f2 for 1.

```
bits:     1        0        1        1        0
       /\/\/\/\  /  \/  \ /\/\/\/\ /\/\/\/\ /  \/  \
        high f    low f    high f   high f   low f
       (constant amplitude throughout)
```

**Bandwidth:** `B = (1 + d) x S + Δf`, where Δf is the frequency separation between the two tones. FSK needs more bandwidth than ASK.

**Advantages:** far more noise-resistant than ASK, since noise changes amplitude but rarely frequency. Constant envelope means cheap non-linear power amplifiers can be used at full efficiency, which matters enormously for battery-powered devices.
**Disadvantages:** uses more bandwidth than ASK or PSK, and is less spectrally efficient.

**MFSK** extends this to M tones, carrying log2 M bits per symbol.

**Where it is used:** GSM uses **GMSK**, which is a filtered variant of FSK, chosen precisely for its constant envelope. Bluetooth uses GFSK. Old modems and caller ID used FSK.

---

## 4.4 PSK, Phase Shift Keying

**Short answer:** the phase of the carrier is shifted to represent bits, with amplitude and frequency constant.

### BPSK (Binary PSK), 2 phases

```
0 -> phase 0 degrees        1 -> phase 180 degrees

  bit 1:  /\  /\  /\        bit 0:  \/  \/  \/
         /  \/  \/  \              /\  /\  /\
         (the waveform simply inverts)
```

Carries 1 bit per symbol. Very robust, since the two points are as far apart as possible.

### QPSK (Quadrature PSK), 4 phases

Four phases at 45, 135, 225 and 315 degrees, each representing a **dibit**: 00, 01, 10, 11.

**QPSK carries 2 bits per symbol, so it doubles the data rate in the same bandwidth as BPSK.**

The trick is that QPSK is really two BPSK signals sent at once on carriers 90 degrees apart, called the **in-phase (I)** and **quadrature (Q)** components. They do not interfere because they are orthogonal.

### Constellation diagrams

**Short answer:** a constellation diagram plots each possible symbol as a point, with the I component on the horizontal axis and the Q component on the vertical axis. Distance from the origin is amplitude, angle is phase.

```
BPSK (2 points, 1 bit/symbol)         QPSK (4 points, 2 bits/symbol)
            Q                                     Q
            |                              01     |     00
            |                                 .   |   .
  ----•-----+-----•----  I                 -------+-------  I
      1     |     0                           .   |   .
            |                              11     |     10
            |                                     |

8-PSK (8 points, 3 bits/symbol)       16-QAM (16 points, 4 bits/symbol)
            Q                                     Q
        .   |   .                          .  .   |   .  .
      .     |     .                        .  .   |   .  .
  ----------+----------  I            ----------- + -----------  I
      .     |     .                        .  .   |   .  .
        .   |   .                          .  .   |   .  .
   all points on one circle           points vary in BOTH
   (constant amplitude)               amplitude and phase
```

**How to read a constellation in the exam:**
- Number of points = M = number of distinct symbols
- Bits per symbol = **r = log2 M**
- All points on one circle means **pure PSK** (constant amplitude)
- Points at different radii means **QAM** (amplitude varies too)
- The **closer the points, the more vulnerable to noise**, because a smaller noise vector can push a received point into the wrong decision region

> **The single most important insight in this topic:** as you add constellation points to carry more bits per symbol, the points get closer together, so the receiver needs a higher SNR to keep the same error rate. **Spectral efficiency is bought with SNR.** This is Shannon's law appearing in practical form.

---

## 4.5 QAM, Quadrature Amplitude Modulation

**Short answer:** QAM varies both amplitude and phase, giving many more distinguishable symbols than PSK for the same maximum power.

Why QAM beats high-order PSK: with 16-PSK, all 16 points sit on one circle and are cramped together around the circumference. With 16-QAM, the points spread over a two-dimensional grid, so neighbouring points are further apart for the same average power. **Better noise performance for the same bit rate.**

| Scheme | Points M | Bits/symbol r | Relative SNR needed | Typical use |
|---|---|---|---|---|
| BPSK | 2 | 1 | Lowest | Satellite, deep space, cell edge in LTE |
| QPSK | 4 | 2 | Low | 3G WCDMA, LTE control channels, satellite |
| 8-PSK | 8 | 3 | Medium | EDGE (2.75G) |
| 16-QAM | 16 | 4 | Higher | LTE mid-cell, cable modems |
| 64-QAM | 64 | 6 | High | LTE and 5G good conditions, DVB-T, Wi-Fi |
| 256-QAM | 256 | 8 | Very high | 5G NR near the cell, Wi-Fi 5/6, DOCSIS 3.1 |
| 1024-QAM | 1024 | 10 | Extreme | Wi-Fi 6, short range only |

---

## 4.6 Bit rate, baud rate and bandwidth efficiency

**The three formulas that carry the marks:**

```
r = log2 M                bits per symbol, M = constellation points
N = S x r                 bit rate = baud rate x bits per symbol
S = N / r                 baud rate = bit rate / bits per symbol

Bandwidth efficiency = N / B      in bits per second per hertz (bps/Hz)
```

### Worked example 1
A QPSK system operates at 2000 baud. What is the bit rate?
- r = log2 4 = 2
- N = 2000 x 2 = **4000 bps**

### Worked example 2
A 64-QAM system must deliver 36 Mbps. What baud rate is required?
- r = log2 64 = 6
- S = 36,000,000 / 6 = **6 Mbaud**

### Worked example 3
An 8-PSK signal has a bandwidth of 6 MHz with d = 0. Find the bit rate.
- For PSK, `B = (1 + d) x S`, so S = 6 MHz = 6,000,000 baud
- r = log2 8 = 3
- N = 6,000,000 x 3 = **18 Mbps**
- Bandwidth efficiency = 18 Mbps / 6 MHz = **3 bps/Hz**

### Worked example 4 (combining with Topic 2)
A channel has 200 kHz of bandwidth and SNR_dB of 24 dB. What is the highest-order QAM that can realistically be used?
- Shannon: SNR = 10^2.4 = 251, C = 200,000 x log2(252) = 200,000 x 7.98 = **1.6 Mbps**
- Bandwidth efficiency ceiling = 1.6 Mbps / 200 kHz = 8 bps/Hz
- 8 bits per symbol means M = 2^8 = **256-QAM** is the theoretical ceiling
- In practice you would choose 64-QAM to leave margin for fading and implementation loss

> **This chaining of Shannon into modulation choice is exactly what an examiner means by "justify their use in real systems".**

---

## 4.7 Comparing the schemes

| Criterion | ASK | FSK | PSK | QAM |
|---|---|---|---|---|
| Parameter varied | Amplitude | Frequency | Phase | Amplitude and phase |
| Noise immunity | Poor | Good | Very good | Good, but degrades at high order |
| Bandwidth needed | Low | High | Low | Low |
| Spectral efficiency | Low | Lowest | Good | Highest |
| Constant envelope | No | **Yes** | **Yes** | No |
| Implementation cost | Lowest | Low | Medium | Highest |
| Where used | Optical, IR remote | GSM (GMSK), Bluetooth | Satellite, 3G, LTE control | LTE/5G data, Wi-Fi, cable |

### Constant envelope: why it matters more than students expect

A signal with constant envelope (FSK and PSK) never changes amplitude. This means the transmitter can use a **non-linear class C power amplifier** running at maximum efficiency without distorting the signal. QAM changes amplitude, so it needs a **linear amplifier**, which is far less power-efficient and generates more heat.

**Consequence:** battery-powered handsets and rural base stations running on solar power care greatly about this. It is one reason GSM chose GMSK, and why LTE uplink uses SC-FDMA rather than plain OFDMA (lower peak-to-average power ratio, easier on the handset battery).

---

## 4.8 Adaptive modulation and coding

Modern systems do not pick one modulation and stick to it. **LTE and 5G change modulation dynamically per user, per subframe**, based on the channel quality the handset reports back.

```
   Near the base station          Cell edge
   strong signal, high SNR        weak signal, low SNR
        |                              |
        v                              v
      256-QAM                        QPSK
   8 bits per symbol             2 bits per symbol
   fast, fragile                 slow, robust
```

The handset measures the reference signals and reports a **Channel Quality Indicator (CQI)**. The scheduler at the base station selects a **Modulation and Coding Scheme (MCS)** index. This is why your phone shows full bars and fast speeds near a tower and slow speeds at the edge of coverage, even though the tower's total spectrum has not changed.

> **Ghana scenario worth writing in an exam:** in a densely built area of Accra, a user standing beside the base station gets 256-QAM and very high throughput. The same user in Anyaa near the edge of the cell, behind concrete walls, falls back to QPSK. The operator has not reduced its spectrum. The physics of SNR has forced a lower-order constellation, and the throughput drops by a factor of four for that user.

---

## 4.9 Bit error rate and the Eb/N0 curve

**BER** is the fraction of received bits that are wrong. It is plotted against **Eb/N0**, the energy per bit divided by the noise power spectral density, which is a normalised form of SNR.

```
   BER
  10^-1 |\  \    \
        | \  \    \
  10^-3 |  \  \    \        Left curve  = BPSK (most robust)
        |   \  \    \       Middle      = QPSK / 16-QAM
  10^-5 |    \  \    \      Right curve = 64-QAM (needs most Eb/N0)
        |     \  \    \
  10^-7 +------------------> Eb/N0 (dB)
        0   5   10   15  20

   Reading: to hit BER = 10^-5, BPSK needs about 9.6 dB,
   while 64-QAM needs several dB more.
```

**What to take away:** for a fixed target error rate, every step up in constellation order costs roughly 3 to 6 dB more Eb/N0. That extra dB has to come from more transmit power, a better antenna, or being closer to the tower.

---

## 4.10 Formula sheet for Topic 4

```
r = log2 M                          bits per symbol
N = S x r                           bit rate from baud rate
S = N / r                           baud rate from bit rate
B_ASK = (1 + d) S                   ASK bandwidth
B_PSK = (1 + d) S                   PSK and QAM bandwidth
B_FSK = (1 + d) S + Δf              FSK bandwidth, wider
Bandwidth efficiency = N / B        bps per Hz
C = B log2 (1 + SNR)                Shannon ceiling on efficiency
```

---

## 4.11 Traps that cost marks

1. **Bit rate is not baud rate.** In QPSK the bit rate is twice the baud rate. In 64-QAM it is six times.
2. **FSK needs more bandwidth than PSK**, not less. Adding Δf widens it.
3. **16-QAM outperforms 16-PSK** for the same power, because its points are spread over two dimensions instead of one circle.
4. **ASK is not "the worst modulation".** In optical fibre, where amplitude noise is negligible, on-off keying is the natural choice.
5. **Higher-order modulation does not increase bandwidth.** It increases bits per hertz and requires more SNR. Students often write "64-QAM needs more bandwidth", which is wrong.
6. **Constant envelope is a property of FSK and PSK, not QAM.** State it correctly when discussing amplifier efficiency.
7. **In a constellation, distance between points determines noise immunity.** Say this explicitly, examiners look for it.

---

## 4.12 Glossary

| Term | One-line definition |
|---|---|
| Carrier | A high-frequency sinusoid that is modified to carry information |
| Modulation | Varying a property of a carrier according to a message signal |
| ASK | Modulation that varies carrier amplitude |
| OOK | The simplest ASK, carrier on or off |
| FSK | Modulation that varies carrier frequency |
| GMSK | Gaussian minimum shift keying, the filtered FSK variant used by GSM |
| PSK | Modulation that varies carrier phase |
| BPSK | Two-phase PSK, one bit per symbol |
| QPSK | Four-phase PSK, two bits per symbol |
| QAM | Modulation varying both amplitude and phase |
| Symbol | One signal element carrying one or more bits |
| Constellation diagram | A plot of all possible symbols in the I-Q plane |
| I and Q | In-phase and quadrature components, 90 degrees apart |
| Bandwidth efficiency | Bit rate divided by bandwidth, in bps/Hz |
| Constant envelope | A modulated signal whose amplitude never varies |
| BER | Bit error rate, fraction of bits received incorrectly |
| Eb/N0 | Energy per bit divided by noise power spectral density |
| Adaptive modulation | Changing the modulation order dynamically with channel quality |
| CQI | Channel quality indicator reported by a handset to the base station |
| MCS | Modulation and coding scheme index selected by the scheduler |

---

# Objective questions (Topic 4)

**Q1.** The main reason for modulating a baseband signal onto a carrier is to:
A. Reduce the data rate
B. Allow practical antenna sizes and enable multiplexing
C. Eliminate noise entirely
D. Reduce the number of bits

**Q2.** In ASK, the parameter of the carrier that is varied is:
A. Frequency  B. Phase  C. Amplitude  D. Wavelength

**Q3.** Which modulation scheme is most vulnerable to amplitude noise?
A. ASK  B. FSK  C. PSK  D. QAM

**Q4.** QPSK carries how many bits per symbol?
A. 1  B. 2  C. 4  D. 8

**Q5.** A constellation diagram with 16 points represents a scheme carrying:
A. 2 bits per symbol  B. 4 bits per symbol  C. 8 bits per symbol  D. 16 bits per symbol

**Q6.** A 16-QAM system operating at 3000 baud has a bit rate of:
A. 3000 bps  B. 6000 bps  C. 12,000 bps  D. 48,000 bps

**Q7.** To transmit 48 Mbps using 64-QAM, the required baud rate is:
A. 6 Mbaud  B. 8 Mbaud  C. 12 Mbaud  D. 48 Mbaud

**Q8.** In a constellation diagram, all points lying on a single circle indicates:
A. QAM  B. Pure PSK  C. ASK  D. FSK

**Q9.** GSM uses GMSK primarily because it:
A. Gives the highest possible data rate
B. Has a constant envelope allowing efficient non-linear amplifiers
C. Requires the least bandwidth of all schemes
D. Is the simplest to implement

**Q10.** Compared with 16-PSK, 16-QAM offers:
A. Lower bit rate
B. Greater separation between constellation points for the same average power
C. Constant envelope
D. Less bandwidth efficiency

**Q11.** Increasing the constellation order from QPSK to 64-QAM at a fixed baud rate:
A. Increases the required bandwidth
B. Increases the bit rate and the required SNR
C. Decreases the bit rate
D. Has no effect on error rate

**Q12.** The bandwidth efficiency of a system carrying 24 Mbps in 6 MHz is:
A. 0.25 bps/Hz  B. 4 bps/Hz  C. 6 bps/Hz  D. 24 bps/Hz

**Q13.** FSK requires more bandwidth than PSK because:
A. It uses more amplitude levels
B. The two carrier frequencies must be separated by Δf
C. It sends more bits per symbol
D. It has no carrier

**Q14.** In LTE, a handset at the cell edge is most likely to be assigned:
A. 256-QAM  B. 64-QAM  C. 16-QAM  D. QPSK

**Q15.** The report a handset sends to help the base station choose a modulation scheme is the:
A. MCS  B. CQI  C. BER  D. SNR

**Q16.** On-off keying survives in optical fibre systems because fibre has:
A. Very high amplitude noise
B. Very low amplitude noise
C. No bandwidth limit
D. Constant phase drift

**Q17.** For a fixed target bit error rate, moving to a higher-order constellation requires:
A. Less transmit power  B. More Eb/N0  C. Less bandwidth  D. Fewer antennas

**Q18.** Which two schemes have a constant envelope?
A. ASK and QAM  B. FSK and PSK  C. ASK and PSK  D. QAM and FSK

**Q19.** 8-PSK was the modulation introduced to upgrade GPRS to:
A. GSM  B. EDGE  C. UMTS  D. LTE

**Q20.** In the I-Q plane, the distance of a constellation point from the origin represents:
A. Its frequency  B. Its amplitude  C. Its bit rate  D. Its bandwidth

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Antenna length scales with wavelength, and carriers allow frequency-division sharing |
| 2 | **C** | Amplitude shift keying by definition |
| 3 | **A** | Noise is itself an amplitude disturbance, so it directly corrupts ASK |
| 4 | **B** | log2 4 = 2 |
| 5 | **B** | log2 16 = 4 |
| 6 | **C** | r = 4, N = 3000 x 4 = 12,000 bps |
| 7 | **B** | r = 6, S = 48/6 = 8 Mbaud |
| 8 | **B** | Constant radius means constant amplitude, which is pure PSK |
| 9 | **B** | Constant envelope allows class C amplifiers at high efficiency |
| 10 | **B** | Two-dimensional spread beats a crowded circle |
| 11 | **B** | Same symbol rate but more bits per symbol, and closer points need more SNR |
| 12 | **B** | 24/6 = 4 bps/Hz |
| 13 | **B** | The frequency separation adds to the occupied bandwidth |
| 14 | **D** | Low SNR at the edge forces the most robust constellation |
| 15 | **B** | The CQI reports channel quality, from which the MCS is chosen |
| 16 | **B** | Optical detection is amplitude based and the channel is clean |
| 17 | **B** | Points are closer, so more energy per bit is needed to keep errors low |
| 18 | **B** | Only amplitude-varying schemes lack a constant envelope |
| 19 | **B** | EDGE replaced GMSK with 8-PSK to raise the data rate |
| 20 | **B** | Radius is amplitude, angle is phase |

---

# Exam-style written questions with model answers

**W1. With the aid of waveforms, describe ASK, FSK and PSK, and compare them in terms of noise immunity and bandwidth requirement. (8 marks)**

*Model answer:* Draw three waveforms for the same bit sequence. In **ASK** the carrier amplitude is switched between two or more levels while frequency and phase remain fixed, with the simplest case being on-off keying. In **FSK** the carrier frequency is switched between two or more values while the amplitude remains constant. In **PSK** the carrier phase is shifted, most simply by 180 degrees for binary PSK, while amplitude and frequency remain constant. On noise immunity, ASK is the poorest because channel noise appears principally as amplitude fluctuation and therefore corrupts the modulating parameter directly. FSK is substantially better because noise rarely alters frequency, and PSK is the best of the three for a given number of levels because the decision regions are maximally separated in phase. On bandwidth, ASK and PSK both occupy approximately (1+d)S hertz, where S is the baud rate, while FSK occupies (1+d)S plus the frequency separation Δf and is therefore the widest. This makes PSK the preferred scheme where spectrum is scarce, ASK the choice for optically clean channels, and FSK attractive where its constant envelope permits efficient non-linear amplification.

**W2. Explain what a constellation diagram shows and use it to explain why 16-QAM is preferred to 16-PSK. (6 marks)**

*Model answer:* A constellation diagram plots every possible transmitted symbol as a point in a two-dimensional plane whose horizontal axis is the in-phase component and whose vertical axis is the quadrature component. The distance of a point from the origin represents the amplitude of that symbol and the angle from the positive horizontal axis represents its phase. The number of points equals the number of distinct symbols M, and each symbol carries log2 M bits. The minimum distance between neighbouring points determines noise immunity, because a received point is decoded as whichever constellation point it lies nearest to, so a smaller minimum distance means that less noise is needed to push the received sample into a wrong decision region. In 16-PSK all sixteen points must lie on a single circle of fixed radius, so they are separated by only 22.5 degrees and their minimum separation is small. In 16-QAM the same sixteen points are arranged on a four-by-four grid, using both amplitude and phase dimensions, so for the same average transmitted power the minimum distance between neighbouring points is substantially greater. 16-QAM therefore achieves the same four bits per symbol with a lower bit error rate for the same signal-to-noise ratio, which is why practical systems use QAM rather than high-order PSK.

**W3. A wireless channel has a bandwidth of 20 MHz and a measured SNR of 30 dB. (a) Find the Shannon capacity. (b) Determine the highest QAM order that could theoretically achieve this in the given bandwidth. (c) State two reasons why a real system would choose a lower order. (7 marks)**

*Model answer:*
(a) SNR = 10^(30/10) = 1000. C = 20 x 10^6 x log2(1001) = 20 x 10^6 x 9.97 = **199.4 Mbps**, approximately 200 Mbps.
(b) Bandwidth efficiency = 199.4 Mbps / 20 MHz ≈ 9.97 bps/Hz, so approximately 10 bits per symbol, giving M = 2^10 = **1024-QAM**.
(c) First, the Shannon limit assumes ideal capacity-achieving coding with unbounded delay, whereas real coders operate several decibels away from it, so an implementation margin must be left. Second, the reported SNR is an average, but a mobile channel fades, so a scheme chosen for the average SNR will fail during fades. Third, high-order QAM requires a highly linear power amplifier and very accurate phase and frequency synchronisation, which increases cost and power consumption in the handset. A practical design would therefore select 64-QAM or 256-QAM with strong forward error correction and use adaptive modulation to fall back when conditions worsen.

**W4. Explain adaptive modulation and coding and why it is essential in LTE and 5G networks. (6 marks)**

*Model answer:* Adaptive modulation and coding is the practice of dynamically selecting the modulation order and the channel-coding rate for each user, and in LTE for each scheduling interval of one millisecond, according to the instantaneous quality of that user's radio channel. The handset continuously measures the base station's reference signals, computes an estimate of the achievable quality and reports it as a Channel Quality Indicator. The scheduler at the base station uses this to select a Modulation and Coding Scheme index, which fixes both the constellation order and the amount of redundancy added by the coder. It is essential because the radio channel varies enormously across a cell and over time: a user close to the base station may enjoy a signal-to-noise ratio permitting 256-QAM, while a user at the cell edge or behind a building may only sustain QPSK, and any single fixed choice would be wrong for most users most of the time. A fixed high-order scheme would give unusable error rates at the edge, while a fixed low-order scheme would waste the capacity available near the centre. Adaptive selection therefore maximises the total cell throughput while maintaining an acceptable error rate for every user, and it allows graceful degradation as a user moves away from the base station rather than an abrupt loss of service.

**W5. Distinguish between bit rate and baud rate. A modem transmits at 4800 baud using 8-PSK. Find the bit rate. If the same modem is upgraded to 64-QAM at the same baud rate, find the new bit rate and comment on the additional requirement this imposes. (6 marks)**

*Model answer:* The **bit rate** is the number of information bits transmitted per second and is measured in bits per second. The **baud rate**, or signal rate, is the number of signal elements or symbols transmitted per second and is measured in baud. They are related by N = S x r, where r = log2 M is the number of bits carried by each symbol. The two are equal only when M = 2. For **8-PSK**: r = log2 8 = 3, so N = 4800 x 3 = **14,400 bps**. For **64-QAM**: r = log2 64 = 6, so N = 4800 x 6 = **28,800 bps**. The bit rate has doubled without any change in baud rate and therefore without any increase in occupied bandwidth. The additional requirement is signal-to-noise ratio. The 64-QAM constellation has 64 points rather than 8, packed into the same signal space, so the minimum distance between adjacent points is much smaller and the receiver needs a substantially higher Eb/N0, of the order of several decibels more, to maintain the same bit error rate. In practice this means a stronger received signal, a cleaner line, or stronger forward error correction.

---

## Checkpoint before Topic 5

You should be able to, without notes:
1. State which carrier parameter each of ASK, FSK, PSK and QAM varies
2. Sketch BPSK, QPSK and 16-QAM constellations and give bits per symbol for each
3. Convert freely between M, r, S and N
4. Explain in one sentence why 16-QAM beats 16-PSK
5. Explain why constant envelope matters for handset battery life
6. Chain a Shannon capacity result into a modulation-order recommendation
