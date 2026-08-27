# DCIT 426 Telecommunications
## Topic 2: Signals, Transmission and Channel Impairments

**Syllabus line (Week 2):** *Time/frequency domains, Fourier view, Nyquist and Shannon capacity, attenuation, noise, distortion, SNR.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Time and frequency domains, Fourier | Forouzan Ch. 3, Sections 3.1 to 3.4 |
| Impairments, attenuation, dB | Forouzan Ch. 3, Section 3.5; Stallings Ch. 3.3 |
| Nyquist and Shannon | Forouzan Ch. 3, Section 3.6; Stallings Ch. 3.4 |
| Noise types | Stallings Ch. 3.3 |

This is the most mathematical topic in the course, and it is the one the exam can test with hard numbers. Learning outcome 2 in your outline says explicitly: *"Apply Nyquist and Shannon theory to estimate data rates and capacity for given channels and impairments."* Expect a calculation question.

---

## 2.1 Periodic and aperiodic signals

**Short answer:** a periodic signal repeats itself over a fixed interval called the period. An aperiodic signal does not.

```
PERIODIC                                   APERIODIC
  ^   /\    /\    /\                         ^    /\
  |  /  \  /  \  /  \                        |   /  \    /\_
  | /    \/    \/    \                       |  /    \__/   \___
  +--------------------> t                   +-------------------> t
   |<-T->|                                    no repeating pattern
   e.g. a 1 kHz tone                          e.g. real speech, data bits
```

Rule to memorise: **periodic analog signals have a discrete (line) spectrum. Aperiodic analog signals have a continuous spectrum.**

```
Periodic signal spectrum          Aperiodic signal spectrum
  amp ^                             amp ^
      | |                               |   ______
      | |   |                           |  /      \
      | |   |   |                       | /        \___
      +-+---+---+---> f                 +----------------> f
      discrete lines                    continuous band
```

---

## 2.2 Fourier: the single most useful idea in the course

**Short answer:** any signal, however complicated, can be represented as a sum of sine waves of different frequencies, amplitudes and phases.

- **Fourier series** applies to periodic signals and gives a sum of harmonics: f, 2f, 3f and so on
- **Fourier transform** applies to aperiodic signals and gives a continuous spectrum

### Building a square wave from sines

A square wave of fundamental frequency f is built from the odd harmonics:

```
s(t) = (4A/π) [ sin(2πft) + (1/3)sin(2π(3f)t) + (1/5)sin(2π(5f)t) + ... ]

  1 harmonic          3 harmonics          many harmonics
   /\    /\            _-_    _-_           ___    ___
  /  \  /  \          / \_/  / \_/         |   |  |   |
 /    \/    \        _/     _/             |   |__|   |__
 rounded blob        starting to square     nearly perfect square
```

**The consequence that matters:** a perfect square wave needs *infinite* bandwidth. No real channel has infinite bandwidth. Therefore **every digital signal sent through a real channel arrives rounded and smeared.** If the channel cuts off too many harmonics, adjacent bits blur into each other. This is called **intersymbol interference (ISI)**, and it is the physical reason there is a limit on data rate.

> **Analogy:** think of building a square metal box out of curved sheets. With one curved sheet you get a blob. With more and more sheets of smaller curvature you approach sharp corners. The corners are the high frequencies. Cut off the high frequencies and the corners are gone.

### Bandwidth of a composite signal

`B = f_highest - f_lowest`

**Worked example.** A periodic signal is decomposed into five sine waves with frequencies 100, 300, 500, 700 and 900 Hz.
Bandwidth = 900 - 100 = **800 Hz**.

**Worked example (reverse).** A signal has a bandwidth of 20 kHz and its highest frequency is 60 kHz. What is the lowest frequency?
f_low = 60 - 20 = **40 kHz**.

---

## 2.3 Digital signals: bit rate, bit interval and baud

| Term | Symbol | Definition | Unit |
|---|---|---|---|
| Bit rate | N or R | number of bits sent per second | bps |
| Bit interval | Tb | time to send one bit, = 1/N | seconds |
| Baud rate (signal rate) | S | number of signal elements per second | baud |
| Bits per signal element | r | r = log2 L, where L = number of signal levels | bits |

**Relationship:** `S = N / r` (ignoring the case-dependent factor c used in some texts)

**Worked example.** A digital signal has eight levels. How many bits does each level carry, and what is the bit rate if the baud rate is 2000?
- r = log2 8 = 3 bits per signal element
- N = S x r = 2000 x 3 = **6000 bps**

> **Exam trap:** baud rate is never higher than bit rate. Baud rate equals bit rate only when L = 2, since log2 2 = 1. If a question says "the baud rate is greater than the bit rate," it is wrong.

### Bandwidth needed for a digital signal

A digital signal is aperiodic and therefore has a continuous, infinite spectrum. In practice we approximate: **the minimum bandwidth required is proportional to the bit rate.** A useful working rule is `B_min = N/2` for a two-level signal, which is exactly the Nyquist result from the other direction.

---

## 2.4 Transmission impairments

Three families. Learn to tell them apart instantly, because the exam asks you to.

```
   ORIGINAL              ATTENUATION           DISTORTION            NOISE
    /\  /\                 /\  /\               /\_  ,-\             /\/\ /\
   /  \/  \               _/  \/  \_           /   \/   \           /  \/\ \/\
   full height           smaller height        shape changed        random extra
                         same shape                                  energy added
```

| Impairment | Cause | Effect | Fix |
|---|---|---|---|
| **Attenuation** | Resistance of the medium converts signal energy to heat | Signal gets weaker with distance | Amplifiers (analog) or regenerative repeaters (digital) |
| **Distortion** | Different frequency components propagate at different speeds (delay distortion) or are attenuated unequally | Composite signal changes shape, causing ISI | Equalisers |
| **Noise** | Unwanted external or internal energy | Random corruption of the signal | Shielding, filtering, error control, more transmit power |

### Types of noise (this table is examinable)

| Noise type | Source | Notes |
|---|---|---|
| **Thermal (white/Johnson)** | Random motion of electrons in any conductor at temperature above absolute zero | Present in every device. Cannot be eliminated. Power N = kTB, where k = 1.38 x 10^-23 J/K |
| **Intermodulation** | Two or more signals sharing a non-linear device produce sums and differences of their frequencies | Signals at f1 and f2 create unwanted energy at f1+f2 and f1-f2 |
| **Crosstalk** | Unwanted electromagnetic coupling between adjacent cables or channels | Hearing another conversation faintly on a phone line |
| **Impulse** | Lightning, power surges, faulty switches, motor starts | Short, high-energy spikes. The most damaging to digital data because it wipes out a burst of bits |

> **Why impulse noise hurts digital more than analog:** a 10 ms spike on an analog voice call is a click that a human ignores. On a 1 Gbps link that same 10 ms destroys 10 million bits.

### Decibels

`dB = 10 log10 (P2 / P1)`

Positive dB means gain. Negative dB means loss. Because it is logarithmic, **you can add and subtract dB along a link instead of multiplying ratios.**

**Worked example.** A signal travels from point 1 to 4. Between 1 and 2 it loses 3 dB, between 2 and 3 an amplifier gives +7 dB, and between 3 and 4 it loses 3 dB. What is the overall gain or loss?
Total = (-3) + 7 + (-3) = **+1 dB**. The signal is slightly stronger than it started.

**Key reference values to memorise**
| dB | Power ratio |
|---|---|
| +3 dB | double |
| -3 dB | half |
| +10 dB | ten times |
| -10 dB | one tenth |
| +20 dB | hundred times |
| 0 dB | unchanged |

---

## 2.5 Signal-to-noise ratio

**Short answer:** SNR is the ratio of average signal power to average noise power. It tells you how clearly the receiver can see the signal.

```
SNR = average signal power / average noise power        (a plain ratio, no unit)

SNR_dB = 10 log10 (SNR)
```

```
 HIGH SNR                              LOW SNR
   ___    ___                            _-_.,_-.
  |   |  |   |     signal clearly       ,'  `'  `.  signal buried
  |   |__|   |__   above the noise      noise everywhere,
  ~~~~~~~~~~~~~~   floor                bits hard to decide
  small noise floor
```

**Worked example.** Signal power is 10 mW, noise power is 1 μW.
- SNR = 10 x 10^-3 / 1 x 10^-6 = 10,000
- SNR_dB = 10 log10 10,000 = **40 dB**

> **Exam trap:** Shannon's formula needs the plain ratio, not decibels. If the question gives SNR in dB, convert first: `SNR = 10^(SNR_dB / 10)`.

---

## 2.6 Nyquist: the noiseless channel limit

**Short answer:** Nyquist tells you the maximum bit rate of a channel that has bandwidth but **no noise**, and it depends on how many signal levels you use.

```
C = 2 B log2 L

C = channel capacity in bps
B = bandwidth in Hz
L = number of signal levels
```

**The insight:** in a noiseless channel you can increase capacity without limit simply by adding more levels. Two levels gives 2B. Four levels gives 4B. Sixteen levels gives 8B. There is no ceiling, because with no noise the receiver can always distinguish levels no matter how close together they are.

**Worked example 1.** A noiseless channel of 3 kHz transmits a binary signal (L = 2).
C = 2 x 3000 x log2 2 = 2 x 3000 x 1 = **6000 bps**

**Worked example 2.** Same channel, but with four signal levels.
C = 2 x 3000 x log2 4 = 2 x 3000 x 2 = **12,000 bps**

**Worked example 3 (reverse).** We need to send 265 kbps over a 20 kHz noiseless channel. How many signal levels?
265,000 = 2 x 20,000 x log2 L
log2 L = 6.625, so L = 2^6.625 = 98.7
Since L must be a power of 2 that we can actually build, we would round up to **128 levels**, which gives 280 kbps and comfortably meets the requirement.

---

## 2.7 Shannon: the noisy channel limit

**Short answer:** Shannon tells you the absolute maximum error-free bit rate of a channel that has noise. No amount of cleverness in coding or modulation can beat it.

```
C = B log2 (1 + SNR)

C = capacity in bps
B = bandwidth in Hz
SNR = signal-to-noise power ratio (plain number, not dB)
```

Notice what is **not** in the formula: the number of signal levels. Shannon does not care. Noise sets the ceiling.

**Worked example 1.** An extremely noisy channel with SNR close to 0.
C = B log2 (1 + 0) = B log2 1 = **0 bps**. You cannot send anything reliably.

**Worked example 2.** A telephone line of 3000 Hz bandwidth with SNR_dB = 35.
- Convert: SNR = 10^(35/10) = 10^3.5 = 3162
- C = 3000 x log2 (1 + 3162) = 3000 x log2 3163
- log2 3163 = ln 3163 / ln 2 = 8.059 / 0.693 = 11.63
- C = 3000 x 11.63 = **34,881 bps**, which is why 33.6 kbps modems were near the theoretical limit of an analog phone line.

**Useful shortcut:** when SNR is large, `C ≈ B x SNR_dB / 3`. Check: 3000 x 35/3 = 35,000 bps. Very close to the exact answer. Handy for a quick sanity check under exam pressure.

---

## 2.8 Using Nyquist and Shannon together

This is the classic full-mark question. The method is always the same:

```
STEP 1: Use SHANNON to find the upper limit set by noise.
STEP 2: Use NYQUIST to choose the number of signal levels
        that achieves a practical rate at or below that limit.
```

**Worked example.** A channel has bandwidth 1 MHz and SNR = 63. Find the appropriate bit rate and signal level count.

*Step 1, Shannon:*
C = 10^6 x log2 (1 + 63) = 10^6 x log2 64 = 10^6 x 6 = **6 Mbps**
This is the ceiling. We should design for something at or slightly below it.

*Step 2, Nyquist:* choose 6 Mbps and solve for L.
6 x 10^6 = 2 x 10^6 x log2 L
log2 L = 3, so **L = 8 levels**

*Answer:* use 8 signal levels to achieve 6 Mbps, which is exactly the Shannon limit for this channel. In practice engineers design a little below the limit to leave margin.

> **Memory hook:** **Shannon says how much. Nyquist says how.**

---

## 2.9 Performance: latency, jitter and bandwidth-delay product

**Latency (delay)** has four components. Learn all four with their formulas.

```
Total delay = propagation + transmission + queuing + processing
```

| Component | Formula | Depends on |
|---|---|---|
| **Propagation** | distance / propagation speed | Physical length of the link |
| **Transmission** | message size / bandwidth | Size of the message and link speed |
| **Queuing** | variable | How congested the intermediate devices are |
| **Processing** | usually small | Router or switch performance |

**Worked example.** Send a 5 MB file over a 1 Gbps link that is 12,000 km long, with propagation speed 2.4 x 10^8 m/s.
- Propagation = 12,000,000 / (2.4 x 10^8) = **0.05 s (50 ms)**
- Transmission = (5 x 10^6 x 8) / (10^9) = **0.04 s (40 ms)**
- Total ≈ **90 ms** ignoring queuing and processing

Notice that on a long, fast link **propagation dominates**. This is why a Ghana to Europe fibre call has an unavoidable delay no matter how much bandwidth you buy, and why LEO satellites (Week 12) beat GEO satellites on latency.

**Bandwidth-delay product** = bandwidth x delay. It is the number of bits "in flight" in the link at any instant, and it tells you how large a sliding window must be to keep the link full (Week 6).

**Jitter** is variation in delay between packets. It does not matter for a file download but it destroys VoIP quality (Week 7).

---

## 2.10 Formula sheet for Topic 2

```
B = f_high - f_low                  bandwidth of a composite signal
Tb = 1/N                            bit interval from bit rate
r = log2 L                          bits per signal element
S = N / r                           baud rate from bit rate
dB = 10 log10 (P2/P1)               power gain or loss
SNR = P_signal / P_noise            plain ratio
SNR_dB = 10 log10 SNR               ratio in decibels
SNR = 10^(SNR_dB / 10)              back to plain ratio
C = 2 B log2 L                      NYQUIST, noiseless
C = B log2 (1 + SNR)                SHANNON, noisy
N = k T B                           thermal noise power
delay = prop + trans + queue + proc
BDP = bandwidth x delay             bits in flight
```

---

## 2.11 Traps that cost marks

1. **Shannon takes SNR as a ratio, not dB.** Convert first, every time.
2. **Nyquist has a factor of 2, Shannon does not.** Writing `C = 2B log2(1+SNR)` is an instant loss of marks.
3. **log2 x = ln x / ln 2** or `log10 x / log10 2`. Your calculator has no log2 button. Practise this conversion.
4. **Attenuation, distortion and noise are three different things.** Do not use "noise" as a catch-all.
5. **Thermal noise cannot be removed**, only reduced by cooling or narrower bandwidth. Do not write "use shielding to remove thermal noise". Shielding fixes crosstalk and external interference.
6. **Higher bit rate always needs more bandwidth**, all else equal. A question asking why 4G needs more spectrum than 2G has this at its core.
7. **Bandwidth does not reduce propagation delay.** Upgrading a link from 100 Mbps to 1 Gbps cuts transmission time, not the time light takes to cross the ocean.

---

## 2.12 Glossary

| Term | One-line definition |
|---|---|
| Fourier series | Decomposition of a periodic signal into harmonically related sine waves |
| Harmonic | An integer multiple of the fundamental frequency |
| Spectrum | The set of frequencies present in a signal |
| Intersymbol interference | Blurring of adjacent symbols caused by limited channel bandwidth |
| Bit interval | The time occupied by one bit |
| Baud | One signal element per second |
| Attenuation | Loss of signal power with distance |
| Delay distortion | Unequal propagation speed of different frequency components |
| Thermal noise | Noise from random electron motion, present in all conductors |
| Crosstalk | Coupling of a signal from one path into an adjacent path |
| Impulse noise | Short high-amplitude noise spikes from external events |
| SNR | Ratio of signal power to noise power |
| Channel capacity | The maximum error-free data rate of a channel |
| Jitter | Variation in packet delay |
| Bandwidth-delay product | The quantity of data in transit on a link at any moment |

---

# Objective questions (Topic 2)

**Q1.** A periodic signal has a spectrum that is:
A. Continuous  B. Discrete  C. Always flat  D. Always zero

**Q2.** A square wave requires infinite bandwidth because it contains:
A. A single frequency  B. Only even harmonics  C. An infinite number of odd harmonics  D. No harmonics at all

**Q3.** A signal contains frequencies from 2 kHz to 12 kHz. Its bandwidth is:
A. 2 kHz  B. 10 kHz  C. 12 kHz  D. 14 kHz

**Q4.** A digital signal uses 16 levels. Each signal element carries:
A. 2 bits  B. 4 bits  C. 8 bits  D. 16 bits

**Q5.** If the bit rate is 8000 bps and each signal element carries 4 bits, the baud rate is:
A. 500 baud  B. 2000 baud  C. 8000 baud  D. 32,000 baud

**Q6.** Noise caused by the random motion of electrons in a conductor is called:
A. Impulse noise  B. Crosstalk  C. Thermal noise  D. Intermodulation noise

**Q7.** Two signals sharing a non-linear amplifier produce unwanted frequencies at their sum and difference. This is:
A. Crosstalk  B. Intermodulation noise  C. Thermal noise  D. Delay distortion

**Q8.** A signal loses half its power passing through a cable. The loss in decibels is approximately:
A. -1 dB  B. -3 dB  C. -6 dB  D. -10 dB

**Q9.** An SNR of 1000 expressed in decibels is:
A. 10 dB  B. 20 dB  C. 30 dB  D. 100 dB

**Q10.** Nyquist's formula for a noiseless channel is:
A. C = B log2(1+SNR)  B. C = 2B log2 L  C. C = B/2  D. C = BL

**Q11.** A noiseless channel of 4 kHz using binary signalling has a maximum capacity of:
A. 2 kbps  B. 4 kbps  C. 8 kbps  D. 16 kbps

**Q12.** A channel of 2 MHz with SNR = 3 has a Shannon capacity of:
A. 2 Mbps  B. 4 Mbps  C. 6 Mbps  D. 8 Mbps

**Q13.** In Shannon's formula, increasing the number of signal levels:
A. Increases capacity indefinitely
B. Has no effect on the theoretical capacity
C. Decreases capacity
D. Doubles capacity

**Q14.** Which type of noise is most damaging to high-speed digital transmission?
A. Thermal  B. Crosstalk  C. Impulse  D. White noise

**Q15.** Intersymbol interference is mainly caused by:
A. Excess transmit power
B. Limited channel bandwidth removing high-frequency components
C. Using too few signal levels
D. Poor grounding

**Q16.** On a 12,000 km satellite link, the dominant component of total delay is usually:
A. Processing delay  B. Queuing delay  C. Transmission delay  D. Propagation delay

**Q17.** Bandwidth-delay product represents:
A. The number of bits in transit on the link
B. The total error rate
C. The signal-to-noise ratio
D. The number of routers on the path

**Q18.** An amplifier provides +12 dB and is followed by a cable with -5 dB loss and a second cable with -4 dB loss. The net result is:
A. -21 dB  B. -3 dB  C. +3 dB  D. +21 dB

**Q19.** A channel has 1 MHz bandwidth and SNR_dB of 24 dB. The approximate Shannon capacity is:
A. 2 Mbps  B. 4 Mbps  C. 8 Mbps  D. 24 Mbps

**Q20.** Which statement is correct?
A. Baud rate is always greater than bit rate
B. Bit rate equals baud rate only when there are two signal levels
C. Bit rate and baud rate are always equal
D. Baud rate is measured in bits per second

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Periodic gives discrete harmonic lines. Aperiodic gives a continuous spectrum |
| 2 | **C** | Its Fourier series is an infinite sum of odd harmonics |
| 3 | **B** | 12 - 2 = 10 kHz |
| 4 | **B** | r = log2 16 = 4 |
| 5 | **B** | S = N/r = 8000/4 = 2000 baud |
| 6 | **C** | Thermal, also called white or Johnson noise |
| 7 | **B** | Sum and difference products are the signature of intermodulation |
| 8 | **B** | 10 log10(0.5) = -3.01 dB |
| 9 | **C** | 10 log10 1000 = 30 dB |
| 10 | **B** | Nyquist has the factor 2 and depends on L. Shannon depends on SNR |
| 11 | **C** | C = 2 x 4000 x log2 2 = 8000 bps |
| 12 | **B** | C = 2x10^6 x log2 4 = 2x10^6 x 2 = 4 Mbps |
| 13 | **B** | Shannon's limit is set by bandwidth and noise only. Levels do not appear |
| 14 | **C** | A short spike wipes out a large burst of bits at high rates |
| 15 | **B** | Cutting harmonics rounds the pulses so they spill into neighbouring intervals |
| 16 | **D** | Long distance makes propagation dominate |
| 17 | **A** | Bandwidth x delay gives the bits currently in flight |
| 18 | **C** | 12 - 5 - 4 = +3 dB |
| 19 | **C** | SNR = 10^2.4 = 251. log2 252 ≈ 8. C ≈ 8 Mbps. Shortcut: 24/3 = 8 |
| 20 | **B** | When L = 2, r = 1 so S = N. Otherwise baud is lower than bit rate |

---

# Exam-style written questions with model answers

**W1. State Nyquist's and Shannon's formulas, explaining every symbol, and state the essential difference between the two. (5 marks)**

*Model answer:* Nyquist: `C = 2B log2 L`, where C is capacity in bps, B is bandwidth in Hz, and L is the number of discrete signal levels. It applies to an ideal noiseless channel and shows that capacity can in principle be raised without limit by increasing L. Shannon: `C = B log2(1 + SNR)`, where SNR is the ratio of signal power to noise power. It applies to a noisy channel and gives an absolute upper bound on error-free capacity. The essential difference is that Nyquist expresses a limit imposed by bandwidth and signalling scheme, while Shannon expresses a limit imposed by bandwidth and noise, and no coding or modulation scheme can exceed the Shannon bound.

**W2. A telephone channel has a bandwidth of 3.4 kHz and a measured signal-to-noise ratio of 30 dB. (a) Compute the Shannon capacity. (b) Using Nyquist, determine the number of signal levels needed to reach that capacity. (c) Comment on the practicality of your answer. (6 marks)**

*Model answer:*
(a) SNR = 10^(30/10) = 1000. C = 3400 x log2(1001) = 3400 x 9.97 = **33,890 bps**, about 33.9 kbps.
(b) 33,890 = 2 x 3400 x log2 L, so log2 L = 4.98, giving L ≈ 31.6, which rounds to a practical **32 levels**.
(c) Thirty-two levels means the receiver must distinguish 32 distinct amplitudes or phase-amplitude points. As levels increase, the separation between them shrinks, so the system becomes extremely sensitive to any increase in noise. In practice designers operate below the Shannon limit to leave a noise margin, which is why commercial voiceband modems settled around 33.6 kbps rather than attempting more.

**W3. Distinguish between attenuation, distortion and noise, giving the cause and a remedy for each. (6 marks)**

*Model answer:* **Attenuation** is the loss of signal power as it travels, caused by resistance in the medium converting electrical energy to heat. It is remedied by amplifiers in analog systems and regenerative repeaters in digital systems. **Distortion** is a change in the shape of a composite signal, caused by different frequency components travelling at different velocities or being attenuated unequally, which leads to intersymbol interference. It is remedied by equalisation, which applies the inverse frequency response. **Noise** is unwanted energy added to the signal from thermal agitation, crosstalk, intermodulation or impulse sources. It is reduced by shielding, filtering, twisting of pairs, increasing transmit power, and at the receiving end by error detection and correction.

**W4. Explain, using Fourier reasoning, why a digital signal cannot be transmitted perfectly over a band-limited channel. (5 marks)**

*Model answer:* A digital pulse train is aperiodic and, by Fourier analysis, contains an infinite range of frequency components, with a square pulse in particular comprising an infinite series of odd harmonics of decreasing amplitude. Any real channel has finite bandwidth and therefore removes all components above its cutoff. The removal of the higher harmonics rounds the edges of the pulses and spreads their energy in time. When the spread of one pulse extends into the interval of the next, the receiver's decision on each bit is corrupted by neighbouring bits, which is intersymbol interference. The higher the bit rate for a given bandwidth, the shorter each bit interval and the more severe this effect, which is why bandwidth ultimately limits data rate.

**W5. List four types of noise in telecommunication systems, giving one source and one consequence of each. (8 marks)**

*Model answer:* **Thermal noise** arises from random electron motion in any conductor above absolute zero, has power kTB, is present in every receiver and sets the fundamental noise floor which determines SNR. **Intermodulation noise** arises when two or more signals pass through a non-linear device such as an overdriven amplifier, producing energy at sum and difference frequencies that falls into and corrupts other channels. **Crosstalk** arises from electromagnetic coupling between physically adjacent conductors or between adjacent radio channels, and results in one conversation or data stream being faintly heard in another. **Impulse noise** arises from lightning, power switching or faulty equipment, and consists of short high-energy spikes which cause bursts of bit errors that are the most damaging impairment for high-speed digital transmission.

**W6. A 100 Mbps link between Accra and Kumasi is 250 km long with a propagation speed of 2 x 10^8 m/s. Calculate the propagation delay, the transmission delay for a 1 kB packet, and the bandwidth-delay product. Comment on what the result implies for the sender's window size. (6 marks)**

*Model answer:*
- Propagation delay = 250,000 / (2 x 10^8) = 1.25 x 10^-3 s = **1.25 ms**
- Transmission delay = (1000 x 8) / (100 x 10^6) = 8 x 10^-5 s = **0.08 ms**
- Bandwidth-delay product = 100 x 10^6 x 1.25 x 10^-3 = **125,000 bits**, about 15.6 kB

The link can hold about 125 kbit in flight at any instant. If the sender uses stop-and-wait with 1 kB packets, it would transmit for 0.08 ms then wait roughly 2.5 ms for an acknowledgement, leaving the link idle for most of the time and giving very low utilisation. To keep the link full, the sender must be able to have at least 125,000 bits, roughly sixteen 1 kB packets, unacknowledged at once, which requires a sliding-window protocol with a window of at least that size.

---

## Checkpoint before Topic 3

You should be able to, without notes:
1. Write both capacity formulas correctly, including the factor of 2 in Nyquist only
2. Convert SNR between dB and plain ratio in both directions
3. Compute log2 using natural or common logarithms
4. Name four noise types with a source and a consequence for each
5. Explain intersymbol interference using Fourier reasoning
6. Break total delay into its four components and compute each
