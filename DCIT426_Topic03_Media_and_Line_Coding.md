# DCIT 426 Telecommunications
## Topic 3: Transmission Media and Line Coding

**Syllabus line (Week 3):** *Twisted pair, coaxial, optical fibre, wireless spectrum; baseband coding (NRZ, Manchester, 4B/5B).*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Guided and unguided media | Forouzan Ch. 7; Stallings Ch. 4 |
| Line coding schemes | Forouzan Ch. 4, Sections 4.1 and 4.2; Stallings Ch. 5.1 |
| Block coding, scrambling | Forouzan Ch. 4, Sections 4.1.3 and 4.1.4 |

The topic splits cleanly in two: **what the signal travels through** (media) and **how the bits are shaped into a voltage pattern before they travel** (line coding). Study them as two separate halves.

---

# PART A: TRANSMISSION MEDIA

## 3.1 The taxonomy

```
                     TRANSMISSION MEDIA
                            |
          +-----------------+-----------------+
          |                                   |
      GUIDED (wired)                    UNGUIDED (wireless)
          |                                   |
   +------+------+------+          +----------+----------+
   |      |      |                 |          |          |
 Twisted Coaxial Fibre           Radio    Microwave   Infrared
  pair   cable   optic          3kHz-1GHz  1-300GHz   300GHz-400THz
```

**The single sentence that explains all of Part A:** as you move from twisted pair to coax to fibre, **bandwidth goes up, attenuation goes down, immunity to interference goes up, and cost goes up.**

---

## 3.2 Twisted pair

```
   Two insulated copper wires twisted together:

    \  /\  /\  /\  /\  /       Why twist?
     \/  \/  \/  \/  \/        Noise induced on one wire is
     /\  /\  /\  /\  /\        also induced on the other. Because
    /  \/  \/  \/  \/  \       the wires swap positions repeatedly,
                               the interference cancels at the receiver.
```

| Type | Description | Use |
|---|---|---|
| **UTP** Unshielded Twisted Pair | Just twisted pairs in a plastic jacket | LAN cabling, telephone local loop. Cheap and flexible |
| **STP** Shielded Twisted Pair | Adds a metallic foil or braid around the pairs | Noisy industrial environments. Costlier and stiffer |

### UTP categories (examinable table)

| Category | Bandwidth | Typical data rate | Application |
|---|---|---|---|
| Cat 3 | 16 MHz | 10 Mbps | Old telephone, 10BASE-T |
| Cat 5 | 100 MHz | 100 Mbps | 100BASE-TX |
| Cat 5e | 100 MHz | 1 Gbps | Gigabit Ethernet, most common in offices |
| Cat 6 | 250 MHz | 1 Gbps (10 Gbps to 55 m) | Modern structured cabling |
| Cat 6a | 500 MHz | 10 Gbps to 100 m | Data centres |
| Cat 7 | 600 MHz | 10 Gbps | Shielded, specialist |

**Connector:** RJ-45 for data, RJ-11 for telephone.
**Practical limit:** 100 m per run for Ethernet, because of attenuation.

**Advantages:** cheapest, easiest to install and terminate, widely available.
**Disadvantages:** highest attenuation, most vulnerable to noise and crosstalk, lowest bandwidth, short distance.

---

## 3.3 Coaxial cable

```
   Cross-section:

    +-----------------------------------+
    |  outer plastic jacket             |
    |  +-----------------------------+  |
    |  |  braided outer conductor    |  |   <-- acts as shield AND
    |  |  +-----------------------+  |  |       as return conductor
    |  |  |  insulating layer     |  |  |
    |  |  |  +-----------------+  |  |  |
    |  |  |  | inner conductor |  |  |  |   <-- carries the signal
    |  |  |  +-----------------+  |  |  |
    +-----------------------------------+
```

The two conductors share the same axis, hence "co-axial". The outer braid shields the inner conductor, which is why coax carries much higher frequencies than twisted pair with less interference.

| Standard | Impedance | Use |
|---|---|---|
| RG-59 | 75 Ω | Cable television |
| RG-58 | 50 Ω | Thin Ethernet (10BASE2), legacy |
| RG-11 | 75 Ω | Cable TV trunk lines |

**Connector:** BNC (bayonet), F-type for TV.

**Advantages:** much higher bandwidth than twisted pair (hundreds of MHz to a few GHz), good noise immunity, supports long analog trunk runs.
**Disadvantages:** bulkier, more expensive, harder to install, still suffers attenuation at high frequency, largely superseded by fibre for backbone use.

**Where it survives:** cable TV and cable internet access networks (Week 11 covers DOCSIS), and antenna feeds at cell sites.

---

## 3.4 Optical fibre

**Short answer:** fibre carries information as pulses of light, guided down a thin glass core by **total internal reflection**.

### The physics you need

```
Light travelling from a denser medium into a less dense one bends AWAY
from the normal. Beyond a critical angle it does not escape at all:
it reflects entirely back into the core.

                 CLADDING (lower refractive index n2)
   ============================================
      \      /\      /\      /\      /
       \    /  \    /  \    /  \    /            CORE (higher index n1)
        \  /    \  /    \  /    \  /
   ============================================
                 CLADDING

   Total internal reflection occurs when the angle of incidence
   exceeds the critical angle. n1 > n2 is essential.
```

### Fibre types

| Type | Core diameter | Light path | Bandwidth | Distance | Source |
|---|---|---|---|---|---|
| **Multimode step-index** | 50 to 100 μm | Many rays, sharp boundary | Lowest | Shortest | LED |
| **Multimode graded-index** | 50 or 62.5 μm | Many rays, curved paths, refractive index varies gradually | Medium | Medium | LED |
| **Single-mode** | 8 to 10 μm | One straight ray only | Highest | Longest (tens to hundreds of km) | Laser |

```
STEP-INDEX MULTIMODE       GRADED-INDEX MULTIMODE      SINGLE-MODE
  \  /\  /\  /               \__/\__/\__/                ------------>
   \/  \/  \/                 curved, self-correcting     one direct path
   rays take different       paths partly equalise       no modal dispersion
   path lengths              the delay
   => MODAL DISPERSION       => less dispersion          => best of all
```

**Modal dispersion** is the reason multimode fibre is limited: different rays travel different distances, so a single pulse arrives spread out in time. Graded-index reduces it. Single-mode eliminates it by allowing only one path.

**Windows (low-loss wavelengths):** 850 nm, 1310 nm and 1550 nm. The 1550 nm window has the lowest attenuation, which is why long-haul and submarine systems use it. It is also where erbium-doped fibre amplifiers work, which is why DWDM (Week 11) lives there.

**Connectors:** SC, ST, LC, MT-RJ.

**Advantages:** enormous bandwidth (terabits per second per fibre with DWDM), extremely low attenuation (fractions of a dB per km), complete immunity to electromagnetic interference and crosstalk, no electrical signal to tap easily so more secure, light and thin, no corrosion.

**Disadvantages:** installation and splicing require skill and expensive equipment, fibre is fragile under bending, unidirectional so two fibres are needed for full duplex, and the cost of optical transceivers is higher than copper equivalents.

> **Ghana context:** every submarine cable landing at Accra (SAT-3, MainOne, Glo-1, ACE) is single-mode fibre operating with DWDM. The national backbone that runs along road and power corridors is also single-mode. The remaining bottleneck in Ghana is the **last mile**, where most homes still reach the network over wireless rather than fibre. That is exactly the "last-mile design" project theme listed in your outline.

---

## 3.5 Unguided media and the spectrum

```
 THREE PROPAGATION MODES

 GROUND WAVE (below ~2 MHz)          SKY WAVE (2 to 30 MHz)
    signal follows the curve            signal bounces off the
    of the earth                        ionosphere
       ___________                          ionosphere
      /  ~~~~~~   \                    ~~~~~~~~~~~~~~~~~
     |  EARTH      |                     \        /
      \___________/                       \      /
                                       [TX]      [RX]

 LINE OF SIGHT (above ~30 MHz)
    [TX] -----------------> [RX]     needs unobstructed straight path,
     tower                  tower    curvature of earth limits range
```

### Frequency bands (know the names and typical uses)

| Band | Range | Propagation | Typical use |
|---|---|---|---|
| VLF | 3 to 30 kHz | Ground wave | Submarine communication |
| LF | 30 to 300 kHz | Ground wave | Long-range navigation |
| MF | 300 kHz to 3 MHz | Ground and sky | AM radio |
| HF | 3 to 30 MHz | Sky wave | Shortwave, amateur radio, aviation over ocean |
| VHF | 30 to 300 MHz | Line of sight | FM radio, VHF television |
| UHF | 300 MHz to 3 GHz | Line of sight | Television, **mobile phones**, GPS, Wi-Fi 2.4 GHz |
| SHF | 3 to 30 GHz | Line of sight | Microwave links, satellite, Wi-Fi 5 GHz, 5G mid-band |
| EHF | 30 to 300 GHz | Line of sight | 5G mmWave, radar, short-range high-capacity |

**The fundamental trade-off you must be able to state:**

```
LOW FREQUENCY                          HIGH FREQUENCY
+ travels far                          + huge bandwidth available
+ penetrates buildings and foliage     + small antennas
+ few large cells needed               + high data rates
- very little bandwidth                - blocked by walls, rain, foliage
- large antennas                       - short range, many small cells needed
```

This one trade-off explains almost every design decision in Weeks 9 to 12. It is why the NCA's 700 MHz band is coveted for rural coverage while the 3 GHz band is for urban capacity.

### Terrestrial microwave and satellite

| | Terrestrial microwave | Satellite microwave |
|---|---|---|
| Frequency | 2 to 40 GHz | Uplink and downlink pairs, e.g. 6/4 GHz (C band), 14/12 GHz (Ku band) |
| Range | 40 to 50 km per hop, limited by earth curvature | Continental or global |
| Antenna | Parabolic dish on a tower | Dish plus satellite transponder |
| Use | Backhaul between cell sites, links across rivers or difficult terrain | Broadcasting, remote sites, maritime, VSAT |

**Rain fade** is severe above about 10 GHz, which is a real design factor in Ghana's rainy season for Ku-band VSAT and mmWave links.

### Infrared
Short range, cannot pass through walls, hence its use in remote controls and formerly in IrDA device links. The inability to penetrate walls is actually a security advantage for confined applications.

---

## 3.6 Media comparison summary (memorise this)

| Property | UTP | Coax | Fibre | Wireless |
|---|---|---|---|---|
| Bandwidth | Low | Medium | Very high | Medium, and shared |
| Attenuation | High | Medium | Very low | High, varies with environment |
| EMI immunity | Poor | Good | Complete | Poor |
| Security | Poor, easy to tap | Moderate | High | Poorest, broadcast in the open |
| Cost | Lowest | Medium | High for installation | Low per user, spectrum is costly |
| Mobility | None | None | None | Full |
| Typical distance | 100 m | Few km | Tens to hundreds of km | Cell dependent |

---

# PART B: LINE CODING

## 3.7 Why line coding exists

**Short answer:** line coding converts a sequence of bits into a sequence of voltage levels that the physical medium can actually carry reliably.

You cannot just put 5 V for a 1 and 0 V for a 0 and hope for the best. Four problems must be solved:

| Problem | Why it matters |
|---|---|
| **DC component** | A signal with a non-zero average cannot pass through transformers or AC-coupled equipment, and wastes power |
| **Synchronisation** | The receiver must know where each bit interval begins. A long run of identical bits gives it no reference and its clock drifts |
| **Baseline wandering** | With long runs of the same level, the receiver's running average (its baseline) drifts and it starts making wrong decisions |
| **Error detection and bandwidth** | Some schemes allow the receiver to spot violations, and some need less bandwidth than others |

### Vocabulary you must get right

| Term | Meaning |
|---|---|
| **Data element** | The smallest piece of information, that is, one bit |
| **Signal element** | The shortest unit of the transmitted signal |
| **r** | Number of data elements carried by each signal element |
| **Data rate N** | bits per second |
| **Signal rate S** | signal elements per second (baud). `S = c x N / r`, where c depends on the case |

---

## 3.8 The schemes

Use this bit sequence for every diagram below: **0 1 0 0 1 1 0 0 0 1**

### NRZ-L (Non-Return-to-Zero, Level)
Voltage level directly represents the bit. Positive for 0, negative for 1 (conventions vary).

```
bits:   0    1    0    0    1    1    0    0    0    1
     ___      ____ ____           ____ ____ ____
        |    |         |         |              |
        |____|         |_________|              |____
```

- Simple, uses minimum bandwidth (S = N/2 average)
- **Fails badly on long runs of the same bit**: no transitions, so no synchronisation and severe baseline wandering
- Has a DC component

### NRZ-I (Non-Return-to-Zero, Inverted)
A **1 causes a transition** at the start of the bit interval. A 0 causes no transition. The level itself carries no meaning, only the change does. This is **differential encoding**.

```
bits:   0    1    0    0    1    1    0    0    0    1
     ____     ____ ____      ____           ____
         |   |         |    |     |        |    
         |___|         |____|     |________|     ...
        (no  (flip)   (no) (no) (flip)(flip)
        flip)
```

- Immune to polarity reversal, which matters if a technician swaps the two wires
- Solves synchronisation for long runs of **1s** but not for long runs of **0s**

### RZ (Return-to-Zero)
Three levels: positive, zero, negative. The signal returns to zero in the **middle** of every bit interval.

```
bits:   0    1    0    0    1
      _      _         _      ...     1 = positive then zero
     | |_   | |_      | |_            0 = negative then zero
     ----   ----      ----            always a mid-bit transition
        |_|      |_|
```

- Guaranteed synchronisation, since there is a transition in every bit
- **Costs double the bandwidth**, because each bit needs two signal elements
- Needs three voltage levels, which increases complexity

### Manchester
Combines NRZ-L with a **mandatory transition in the middle of every bit**. The direction of the mid-bit transition carries the data.

```
Convention (IEEE 802.3): 0 = high-to-low, 1 = low-to-high

bits:   0    1    0    0    1    1    0    0    0    1
      _|   |_   _|   _|   |_   |_   _|   _|   _|   |_
     | |_| |  | |  | |  |  | |  |  | |  | |  | |  | |
     (each bit has a transition at its midpoint)
```

- **Self-synchronising**, always
- **No DC component**, because every bit has equal time high and low
- **Costs double bandwidth**, S = 2N. This is why 10 Mbps Ethernet needed 20 MHz of cable bandwidth

### Differential Manchester
Mid-bit transition is always present **for clocking only**. The data is carried by the presence or absence of a transition **at the start** of the bit.

```
0 = there IS a transition at the beginning of the bit
1 = there is NO transition at the beginning of the bit
(plus the mandatory mid-bit transition in every case)
```

- Self-synchronising and no DC component, like Manchester
- Additionally immune to polarity inversion, like NRZ-I
- Used by Token Ring (IEEE 802.5)

### AMI (Alternate Mark Inversion), also called bipolar
0 is zero voltage. 1 alternates between positive and negative.

```
bits:   0    1    0    0    1    1    0    0    0    1
             _              _                        _
     _______| |____________| |______________________| |
                             (this one goes negative)
             +              +    -                   +
```

- **No DC component**, because successive 1s cancel
- Built-in error detection: two consecutive 1s of the same polarity is a **bipolar violation**, which signals an error
- **Still fails on long runs of 0s**, which is why scrambling was invented

---

## 3.9 Scrambling: B8ZS and HDB3

These fix AMI's long-zero problem by **deliberately inserting bipolar violations** in a pattern the receiver recognises and removes.

| Scheme | Region | Rule |
|---|---|---|
| **B8ZS** (Bipolar with 8-Zero Substitution) | North America, T1 | Eight consecutive 0s are replaced by `000VB0VB`, where V is a violation and B is a normal pulse |
| **HDB3** (High Density Bipolar 3-zero) | Europe and Africa, E1 | Four consecutive 0s are replaced by a pattern containing a violation, chosen so the DC balance is preserved |

**Why this matters for Ghana:** the E1 hierarchy is used here, not T1, so **HDB3 is the relevant scrambling scheme** for Ghanaian PDH links.

---

## 3.10 Block coding: 4B/5B and 8B/10B

**Short answer:** block coding maps m bits onto n bits (n > m), choosing only those output patterns that guarantee frequent transitions.

```
   4B/5B: every 4 data bits become a 5-bit code group.

   0000 -> 11110        No code group has more than three
   0001 -> 01001        consecutive 0s. This guarantees
   0010 -> 10100        enough transitions for the receiver
   ...                  to stay synchronised.
   1111 -> 11101

   Overhead: 25% more bits. 100 Mbps of data becomes 125 Mbaud on the wire.
```

The standard pairing is **4B/5B followed by NRZ-I**. NRZ-I only has trouble with runs of zeros, and 4B/5B guarantees there are never more than three zeros in a row. Together they solve the problem with only 25% overhead, far cheaper than Manchester's 100% overhead. This is what **100BASE-FX and 100BASE-TX Fast Ethernet** use.

**8B/10B** does the same job with 8 to 10 bits, has better DC balance control, and is used by Gigabit Ethernet, Fibre Channel, PCI Express and USB 3.

**MLT-3** uses three levels with a cycling pattern to reduce the required bandwidth further, and is used by 100BASE-TX over copper.

### Line coding scheme comparison (high-value exam table)

| Scheme | Levels | DC component | Self-sync | Bandwidth (S/N) | Notes |
|---|---|---|---|---|---|
| NRZ-L | 2 | Yes | No | 0.5 | Simplest, worst clocking |
| NRZ-I | 2 | Yes | Partial (1s only) | 0.5 | Polarity independent |
| RZ | 3 | No | Yes | 1.0 | Double bandwidth |
| Manchester | 2 | **No** | **Yes** | 1.0 | Used by 10 Mbps Ethernet |
| Differential Manchester | 2 | **No** | **Yes** | 1.0 | Used by Token Ring, polarity independent |
| AMI | 3 | **No** | No (long 0s) | 0.5 | Bipolar violations detect errors |
| B8ZS / HDB3 | 3 | No | **Yes** | 0.5 | AMI plus scrambling. HDB3 used with E1 |
| 4B/5B + NRZ-I | 2 | Reduced | **Yes** | 0.5 x 1.25 | Fast Ethernet. 25% overhead |

---

## 3.11 Traps that cost marks

1. **Manchester and Differential Manchester are not the same.** Manchester encodes data in the *direction* of the mid-bit transition. Differential Manchester encodes data in the *presence or absence* of a transition at the *start* of the bit.
2. **NRZ-I solves long runs of 1s, not 0s.** State it correctly.
3. **Doubling the transitions doubles the required bandwidth.** Manchester is not free.
4. **Single-mode fibre has a smaller core than multimode**, not larger. Students routinely get this backwards.
5. **Modal dispersion does not exist in single-mode fibre**, because there is only one mode.
6. **Coaxial has two conductors sharing one axis.** It is not simply "a thick wire".
7. **Higher frequency is not automatically better.** It gives more bandwidth but less range and worse penetration.
8. **HDB3 goes with E1 (Europe and Africa), B8ZS goes with T1 (North America).** Do not mix them.

---

## 3.12 Glossary

| Term | One-line definition |
|---|---|
| Guided medium | A medium that physically confines the signal |
| UTP | Unshielded twisted pair copper cable |
| Crosstalk | Signal coupling between adjacent conductors |
| Total internal reflection | Complete reflection of light at a boundary beyond the critical angle |
| Cladding | The lower-index glass layer surrounding a fibre core |
| Modal dispersion | Pulse spreading caused by rays taking different path lengths |
| Single-mode fibre | Fibre with a core small enough that only one propagation mode exists |
| Rain fade | Attenuation of high-frequency radio signals by rainfall |
| Line of sight | Propagation requiring an unobstructed straight path |
| Line coding | Conversion of bits into a pattern of signal levels |
| DC component | A non-zero average voltage in a transmitted signal |
| Baseline wandering | Drift of the receiver's decision threshold during long runs of one level |
| Self-synchronising code | A code whose transitions let the receiver recover the clock |
| Bipolar violation | Two consecutive pulses of the same polarity in AMI |
| Block coding | Mapping m data bits onto n code bits to guarantee transitions |
| Scrambling | Substituting patterns to break up long runs of zeros without added bits |

---

# Objective questions (Topic 3)

**Q1.** Wires are twisted in a twisted-pair cable primarily to:
A. Increase bandwidth  B. Cancel induced interference  C. Reduce cost  D. Improve flexibility

**Q2.** The maximum recommended cable run for Cat 5e Ethernet is:
A. 10 m  B. 50 m  C. 100 m  D. 500 m

**Q3.** In optical fibre, light is confined to the core by:
A. Reflection from a metal coating  B. Total internal reflection  C. Magnetic focusing  D. Electrostatic attraction

**Q4.** For total internal reflection to occur, the refractive index of the core must be:
A. Lower than the cladding  B. Equal to the cladding  C. Higher than the cladding  D. Zero

**Q5.** Single-mode fibre differs from multimode fibre mainly in that it has:
A. A larger core and many propagation paths
B. A smaller core and a single propagation path
C. No cladding
D. A metallic core

**Q6.** Modal dispersion is eliminated in:
A. Step-index multimode fibre  B. Graded-index multimode fibre  C. Single-mode fibre  D. Coaxial cable

**Q7.** Which fibre window has the lowest attenuation and is used for long-haul links?
A. 650 nm  B. 850 nm  C. 1310 nm  D. 1550 nm

**Q8.** Sky-wave propagation relies on reflection from the:
A. Ground  B. Troposphere  C. Ionosphere  D. Moon

**Q9.** Mobile phone networks mainly operate in which band?
A. VLF  B. HF  C. UHF  D. EHF only

**Q10.** Compared with lower frequencies, higher radio frequencies generally offer:
A. More bandwidth but poorer building penetration
B. Less bandwidth but better range
C. More bandwidth and better range
D. No difference

**Q11.** Rain fade becomes a significant design concern above approximately:
A. 100 kHz  B. 10 MHz  C. 10 GHz  D. 10 THz

**Q12.** Which line coding scheme has a guaranteed transition in the middle of every bit?
A. NRZ-L  B. NRZ-I  C. Manchester  D. AMI

**Q13.** The main disadvantage of Manchester encoding is:
A. It has a large DC component
B. It requires twice the bandwidth
C. It cannot detect errors
D. It requires three voltage levels

**Q14.** In NRZ-I encoding, a binary 1 is represented by:
A. A positive voltage  B. A transition at the start of the bit  C. Zero voltage  D. A mid-bit transition

**Q15.** A long sequence of zeros causes problems in AMI because:
A. The DC component grows
B. There are no transitions, so synchronisation is lost
C. The voltage becomes too high
D. Bipolar violations occur

**Q16.** HDB3 scrambling is normally associated with:
A. T1 carriers in North America
B. E1 carriers in Europe and Africa
C. Gigabit Ethernet
D. Optical fibre only

**Q17.** In 4B/5B block coding, the overhead is:
A. 10%  B. 20%  C. 25%  D. 50%

**Q18.** 4B/5B is normally combined with which line code?
A. Manchester  B. NRZ-I  C. AMI  D. RZ

**Q19.** Baseline wandering refers to:
A. Movement of the transmitter
B. Drift in the receiver's decision threshold during long runs of the same level
C. Variation in cable impedance
D. Random frequency shift of the carrier

**Q20.** Which medium offers complete immunity to electromagnetic interference?
A. UTP  B. STP  C. Coaxial cable  D. Optical fibre

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Twisting makes interference affect both wires equally so it cancels |
| 2 | **C** | 100 m is the standard Ethernet horizontal cabling limit |
| 3 | **B** | The core-cladding index difference produces total internal reflection |
| 4 | **C** | n1 must exceed n2 |
| 5 | **B** | 8 to 10 μm core allows only one mode |
| 6 | **C** | With one mode there are no differing path lengths |
| 7 | **D** | 1550 nm is the lowest-loss window and supports EDFA amplification |
| 8 | **C** | HF signals refract off the ionosphere back to earth |
| 9 | **C** | 300 MHz to 3 GHz covers GSM, UMTS and most LTE bands |
| 10 | **A** | The core trade-off of the spectrum |
| 11 | **C** | Raindrop size becomes comparable to the wavelength above about 10 GHz |
| 12 | **C** | Manchester's mid-bit transition is mandatory |
| 13 | **B** | Two signal elements per bit means S = 2N |
| 14 | **B** | NRZ-I is differential: change means 1 |
| 15 | **B** | AMI represents 0 as no pulse, so zeros give no timing information |
| 16 | **B** | HDB3 pairs with E1, B8ZS pairs with T1 |
| 17 | **C** | 5 bits carry 4 bits of data, so 1/4 = 25% |
| 18 | **B** | 4B/5B guarantees no long zero runs, which is exactly NRZ-I's weakness |
| 19 | **B** | The running average drifts and corrupts the decision level |
| 20 | **D** | Light in glass is unaffected by electromagnetic fields |

---

# Exam-style written questions with model answers

**W1. Compare twisted pair, coaxial cable and optical fibre with respect to bandwidth, attenuation, interference immunity, cost and typical application. (8 marks)**

*Model answer:* Present as a table. **Twisted pair** offers the lowest bandwidth, typically up to 500 MHz for Cat 6a, suffers the highest attenuation so runs are limited to about 100 m, is the most vulnerable to crosstalk and external interference although twisting mitigates this, is the cheapest to buy and terminate, and is used for the telephone local loop and LAN horizontal cabling. **Coaxial cable** offers intermediate bandwidth of several hundred MHz to a few GHz, has lower attenuation than twisted pair at high frequency, has good interference immunity because the outer braid shields the inner conductor, costs more and is bulkier, and is used for cable television distribution and antenna feeds. **Optical fibre** offers by far the greatest bandwidth, with terabits per second achievable using wavelength division multiplexing, has extremely low attenuation of a fraction of a dB per kilometre allowing spans of tens to hundreds of kilometres, is completely immune to electromagnetic interference and crosstalk since it carries light rather than current, is the most expensive to install and splice although the fibre itself is inexpensive, and is used for submarine and terrestrial backbone links and increasingly for fibre to the home.

**W2. Explain the principle of total internal reflection and distinguish between single-mode and multimode fibre. (6 marks)**

*Model answer:* When light passes from a medium of higher refractive index into one of lower refractive index, it bends away from the normal. Beyond a specific critical angle of incidence, no light is refracted into the second medium and all of it is reflected back into the first. Optical fibre exploits this by surrounding a core of refractive index n1 with cladding of lower index n2, so that light launched within the acceptance cone repeatedly reflects off the core-cladding boundary and is guided along the fibre. **Multimode fibre** has a core of 50 to 62.5 μm, wide enough that light can propagate along many distinct paths or modes. Because these paths differ in length, a single input pulse arrives spread out in time, an effect called modal dispersion, which limits bandwidth and distance. Graded-index multimode reduces but does not remove this by varying the refractive index gradually so that outer rays travel faster. **Single-mode fibre** has a core of only 8 to 10 μm, comparable to the wavelength of the light, so only one mode can propagate. Modal dispersion is therefore eliminated, giving far greater bandwidth and distance, at the cost of requiring a laser source and tighter alignment tolerances.

**W3. State four objectives of line coding and explain how Manchester encoding meets them. (6 marks)**

*Model answer:* The objectives are: to eliminate or minimise the **DC component** so the signal can pass through transformers and AC-coupled equipment; to provide **self-synchronisation** so the receiver can recover the transmitter's clock from the signal itself; to avoid **baseline wandering** caused by long runs of one level; and to use **bandwidth efficiently** while ideally providing some **error detection** capability. Manchester encoding places a mandatory transition at the midpoint of every bit interval, with the direction of that transition carrying the data. Because every bit spends exactly half its interval high and half low, the average voltage is zero and there is **no DC component**. Because there is a transition in every single bit, the receiver always has a timing reference, so the code is fully **self-synchronising** and immune to baseline wandering regardless of the data pattern. Its weakness is bandwidth: it uses two signal elements per bit, so the signal rate is twice the data rate, which is why 10 Mbps Ethernet required 20 MHz of cable bandwidth and why faster Ethernet standards moved to 4B/5B and 8B/10B instead.

**W4. Why is 4B/5B encoding usually combined with NRZ-I rather than used alone? (5 marks)**

*Model answer:* NRZ-I represents a binary 1 by a transition and a binary 0 by no transition. It is therefore self-synchronising during long runs of 1s but provides no transitions at all during long runs of 0s, so the receiver's clock drifts and baseline wandering occurs. 4B/5B block coding maps each group of four data bits onto a five-bit code group, and the code groups are chosen so that no group begins with more than one 0, ends with more than two 0s, or contains more than three consecutive 0s in total. When these code groups are then transmitted using NRZ-I, the guarantee that there are never more than three consecutive 0s means there is never a gap of more than three bit intervals without a transition, so synchronisation is maintained. The combination therefore fixes NRZ-I's only serious weakness at a cost of just 25% extra bits, compared with the 100% overhead that Manchester encoding would impose. This is why 100BASE-FX Fast Ethernet uses 4B/5B with NRZ-I and runs at 125 Mbaud to carry 100 Mbps.

**W5. A telecommunications engineer must connect a rural community 18 km from the nearest fibre point of presence in the Volta Region. Discuss the media options and justify a recommendation. (8 marks)**

*Points to build the answer on:* **Twisted pair** is immediately ruled out because attenuation limits it to about 100 m for data rates of interest, and even DSL over the copper local loop degrades badly beyond about 5 km. **Coaxial cable** could carry the distance with amplifiers but the trenching cost per kilometre is the same as fibre while the capacity is far lower, so it is not economically rational for a new build. **Optical fibre** would give the best capacity, the lowest running cost and the longest useful life, but the capital cost of trenching 18 km through difficult terrain, obtaining wayleaves, and the risk of cable cuts from road works and farming activity are significant. **Terrestrial microwave** is attractive: 18 km is well within the 40 to 50 km line-of-sight range of a single hop, deployment takes days rather than months, and capacity of hundreds of Mbps is achievable. It requires a clear Fresnel zone, which needs a site survey given the hilly terrain, masts at both ends, reliable power, and it is subject to rain fade if a high band is used. **VSAT** is a fallback if line of sight cannot be achieved, but it has higher latency, higher per-megabit cost and rain-fade vulnerability. Recommend microwave backhaul for immediate service, using a lower band such as 7 or 8 GHz to reduce rain fade, with fibre as the medium-term upgrade once demand justifies the capital cost and if a power or road corridor becomes available to reduce trenching expense. Note that this staged approach matches the last-mile design theme in the course project.

---

## Checkpoint before Topic 4

You should be able to, without notes:
1. Draw a coaxial cross-section and a fibre showing total internal reflection
2. Give the three fibre types with core sizes and state which has modal dispersion
3. Recite the frequency band table with one application per band
4. State the low frequency versus high frequency trade-off in one sentence
5. Draw NRZ-L, NRZ-I, Manchester and AMI for a given bit sequence
6. Explain why 4B/5B pairs with NRZ-I
7. Say which scrambling scheme goes with E1 and which with T1
