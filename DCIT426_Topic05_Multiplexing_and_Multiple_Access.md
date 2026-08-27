# DCIT 426 Telecommunications
## Topic 5: Multiplexing and Multiple Access

**Syllabus line (Week 5):** *FDM, TDM, WDM, statistical muxing; FDMA, TDMA, CDMA, OFDMA.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| FDM, WDM, TDM, statistical TDM | Forouzan Ch. 6, Section 6.1; Stallings Ch. 8 |
| Digital hierarchy, T1/E1, SONET | Forouzan Ch. 6.2 and Ch. 14; Stallings Ch. 8.2 |
| FDMA, TDMA, CDMA | Stallings Ch. 9 and 14; Rappaport Ch. 9 |
| OFDM and OFDMA | Molisch Ch. 19; Rappaport Ch. 11 |

---

## 5.1 The distinction that the exam tests first

**Multiplexing** and **multiple access** solve the same physical problem in two different settings. Get this right and the whole topic falls into place.

| | Multiplexing | Multiple access |
|---|---|---|
| Problem | One link, several signals from **one place** | One shared medium, several **independent users in different places** |
| Coordination | A single multiplexer controls everything centrally | Users must be coordinated remotely, or contend for the medium |
| Example | 30 voice channels combined onto one E1 line at an exchange | 200 handsets in a cell sharing one base station's spectrum |
| Names | FDM, TDM, WDM | FDMA, TDMA, CDMA, OFDMA |

**Memory hook:** the letter **A** at the end means **Access**, which means **separate users in separate places**. Without the A, it is one device combining streams it already holds.

```
MULTIPLEXING                          MULTIPLE ACCESS

 ch1 --\                               [user1] \
 ch2 ---\   +-----+        +-----+     [user2] --\  ))))
 ch3 ----+->| MUX |======> | DEMUX|    [user3] ----+---> [BASE STATION]
 ch4 ---/   +-----+  one   +-----+     [user4] --/
 ch5 --/             link              users are physically apart
 all at one site                       and cannot see each other
```

---

# PART A: MULTIPLEXING

## 5.2 FDM, Frequency Division Multiplexing

**Short answer:** the link's bandwidth is divided into frequency bands, and each signal gets its own band for the whole time.

```
   f ^
     |  +-------------------------------+
     |  |  Channel 4    (guard band)    |
     |  +-------------------------------+
     |  |  Channel 3                    |
     |  +-------------------------------+
     |  |  Channel 2                    |
     |  +-------------------------------+
     |  |  Channel 1                    |
     |  +-------------------------------+
     +------------------------------------> t
     Every channel is present ALL the time,
     each in its own slice of FREQUENCY.
```

- **Analog technique**, though it can carry modulated digital signals
- Each signal modulates a different carrier frequency
- **Guard bands** separate the channels to prevent overlap and interference

**Worked example.** Five 100 kHz voice channels are multiplexed with 10 kHz guard bands between them. What is the minimum link bandwidth?
- Channels: 5 x 100 = 500 kHz
- Guard bands: 4 gaps x 10 kHz = 40 kHz
- Total = **540 kHz**

**Where used:** FM and AM radio broadcasting, analog cable TV, the old analog telephone carrier hierarchy, ADSL (which splits the copper pair into hundreds of sub-bands), and the uplink/downlink separation in FDD cellular.

---

## 5.3 WDM, Wavelength Division Multiplexing

**Short answer:** WDM is FDM applied to optical fibre. Different wavelengths (colours) of light carry different channels down the same strand of glass.

```
  λ1 ---\                                        /--- λ1
  λ2 ----\    +---------+          +---------+ /---- λ2
  λ3 -----+-->| PRISM / |=========>| PRISM / |------ λ3
  λ4 ----/    | GRATING |  ONE     | GRATING | \---- λ4
  λ5 ---/     +---------+  FIBRE   +---------+  \--- λ5
             combines colours              separates them
```

| Variant | Channel spacing | Channels | Use |
|---|---|---|---|
| **CWDM** Coarse | 20 nm | up to about 18 | Metro networks, cheaper uncooled lasers |
| **DWDM** Dense | 0.8 nm or less (100 GHz grid) | 40, 80, 160 or more | Long-haul and submarine backbone |

**Why it matters:** a single fibre pair carrying 80 wavelengths at 100 Gbps each delivers 8 Tbps. This is how a submarine cable landing at Accra can be upgraded to many times its original capacity **without laying a new cable**, simply by replacing the terminal equipment. This is a strong point to make in any question about Ghana's international capacity growth.

---

## 5.4 TDM, Time Division Multiplexing

**Short answer:** the link's full bandwidth is given to each signal in turn, for a short slot of time.

```
   f ^
     |  +---+---+---+---+---+---+---+---+
     |  | 1 | 2 | 3 | 4 | 1 | 2 | 3 | 4 |   full bandwidth used
     |  +---+---+---+---+---+---+---+---+   by ONE channel at
     +------------------------------------> t   a time
        |<-- one frame -->|
```

- **Digital technique**
- Each input gets a **time slot**; a complete cycle of slots is a **frame**
- Requires **synchronisation** between multiplexer and demultiplexer, achieved with framing bits

### Synchronous TDM

Every input gets a slot in every frame, **whether it has data or not**. Empty slots are wasted.

**Key formulas:**
```
Frame duration = 1 / (frame rate)
Frame rate = input data rate / bits per slot
Output data rate = number of inputs x input rate (plus framing overhead)
Output slot duration = input bit duration / number of inputs
```

**Worked example.** Four channels, each 1 kbps, are multiplexed using synchronous TDM with one bit per slot.
- Frame = 4 bits, one per channel
- Frame rate = 1000 frames per second (since each channel sends 1000 bits per second, one bit per frame)
- Output rate = 4 x 1000 = **4 kbps**
- Each output bit occupies 1/4000 s = **0.25 ms**, while each input bit occupied 1 ms

### Interleaving

| Type | Slot contains | Used by |
|---|---|---|
| **Bit interleaving** | One bit from each input | Older PDH systems |
| **Byte (character) interleaving** | One byte from each input | T1 and E1 carriers |

### Statistical (asynchronous) TDM

**Short answer:** slots are allocated **on demand** to only those inputs that actually have data, so no capacity is wasted on idle channels.

```
SYNCHRONOUS TDM            STATISTICAL TDM
 +---+---+---+---+          +----+----+----+
 | A |   | C |   |          | A  | C  | A  |   only active
 +---+---+---+---+          +----+----+----+   inputs get slots
 B and D idle, but          but each slot must
 their slots are still      carry an ADDRESS to
 transmitted as empty       say whose data it is
```

| | Synchronous TDM | Statistical TDM |
|---|---|---|
| Slot allocation | Fixed, pre-assigned | Dynamic, on demand |
| Empty slots | Transmitted, wasted | Never transmitted |
| Addressing | Not needed, position identifies the source | **Required in every slot** |
| Output rate | Sum of all inputs | Can be less than the sum of inputs |
| Delay | Fixed and predictable | Variable, and buffering is needed |
| Suitable for | Constant-rate traffic like voice | Bursty traffic like data |

**The trade-off in one line:** statistical TDM buys efficiency with added overhead (addresses) and unpredictable delay. This is essentially the same argument as circuit versus packet switching in Week 7.

---

## 5.5 The digital hierarchy

This is where the numbers come from that you will see all semester.

### The building block: DS-0

One digitised voice channel using PCM:
- Voice band 300 to 3400 Hz, so sample at **8000 samples per second** (Nyquist sampling: at least twice the highest frequency of 4 kHz)
- **8 bits per sample**
- 8000 x 8 = **64,000 bps = 64 kbps**. This is **DS-0**, the fundamental unit of telephony.

### T-carrier (North America) and E-carrier (Europe, Africa, Ghana)

| Level | Channels | Rate | Region |
|---|---|---|---|
| **T1 / DS-1** | 24 DS-0 | 1.544 Mbps | North America, Japan |
| **E1** | 32 slots (30 voice + 2 control) | **2.048 Mbps** | **Europe, Africa, Ghana** |
| T3 / DS-3 | 672 DS-0 | 44.736 Mbps | North America |
| E3 | 16 E1 | 34.368 Mbps | Europe, Africa |

**Verify E1 yourself:** 32 slots x 8 bits x 8000 frames per second = 2,048,000 bps. Exactly 2.048 Mbps, with no separate framing bit needed because slot 0 carries the frame alignment signal and slot 16 carries signalling.

**Verify T1:** (24 channels x 8 bits + 1 framing bit) x 8000 = 193 x 8000 = 1,544,000 bps.

> **Exam point for the Ghana context:** Ghana uses the **E1 hierarchy**, not T1. When a question asks about a 2 Mbps leased line to a bank branch in Kumasi, that is an E1. This also ties back to Topic 3: E1 uses **HDB3** line coding, T1 uses **B8ZS**.

### SONET and SDH

The optical hierarchy that replaced PDH.

| SDH (international) | SONET (North America) | Rate |
|---|---|---|
| STM-1 | OC-3 / STS-3 | 155.52 Mbps |
| STM-4 | OC-12 | 622.08 Mbps |
| STM-16 | OC-48 | 2.488 Gbps |
| STM-64 | OC-192 | 9.953 Gbps |
| STM-256 | OC-768 | 39.81 Gbps |

Key advantage over PDH: SONET/SDH is **synchronous**, so a single low-rate stream can be dropped out of a high-rate stream directly using an **add-drop multiplexer**, without demultiplexing the whole hierarchy. PDH required demultiplexing every level. SDH also has strong built-in protection switching, typically restoring traffic on a ring within 50 ms.

---

# PART B: MULTIPLE ACCESS

## 5.6 The four schemes side by side

```
FDMA                 TDMA                 CDMA                 OFDMA
 f ^                  f ^                  f ^                  f ^
   |U4|               |___U1_U2_U3_U4|      |all users|          |U1|U2|U1|U3|
   |U3|               |  each in turn|      |together |          |U3|U1|U4|U2|
   |U2|               |              |      |different|          |U2|U4|U3|U1|
   |U1|               |              |      | codes   |          |U4|U3|U2|U4|
   +--------> t       +--------> t         +--------> t          +--------> t
 separate            separate             same time and         separate
 FREQUENCIES         TIME SLOTS           frequency,            SUBCARRIERS in
 all the time        full band            separated by CODE     both time and
                                                                frequency
```

### FDMA, Frequency Division Multiple Access

Each user is permanently assigned a frequency channel for the duration of the call.

- **Advantages:** simple, no timing synchronisation needed between users, well proven
- **Disadvantages:** wasteful, since the channel is idle during pauses in speech but nobody else can use it. Needs guard bands. Requires costly duplexers and narrow filters in the handset
- **Used by:** 1G analog systems such as AMPS and TACS

### TDMA, Time Division Multiple Access

Each user transmits in short bursts in an assigned time slot, using the full channel bandwidth during that slot.

- **Advantages:** more efficient than FDMA, transmitter is off most of the time which saves handset battery, allows the handset to measure neighbouring cells during idle slots which enables **mobile-assisted handover**
- **Disadvantages:** requires precise **timing synchronisation** and guard times between bursts, and **timing advance** to compensate for propagation delay from distant users
- **Used by:** GSM, which combines FDMA and TDMA. Each 200 kHz carrier is divided into **8 time slots**, so 8 users share one carrier

### CDMA, Code Division Multiple Access

All users transmit **at the same time on the same frequency**. They are separated by unique orthogonal spreading codes.

```
   The mechanism:

   user data bit:      1  0  1        (low rate, wide bit)
   spreading code:  10110 01001 10110 (high rate CHIPS)
   transmitted:     XOR of the two, at the chip rate

   The signal is spread over a much wider bandwidth than
   it needs, at a much lower power density.

   At the receiver:
   - Correlate with the SAME code  -> the wanted signal pops out
   - Correlate with a DIFFERENT code -> the signal averages to
     near zero and appears as low-level background noise
```

**Key terms:** chip (one element of the spreading code), **processing gain** (ratio of spread bandwidth to data bandwidth), **Walsh codes** (orthogonal codes for the downlink), **PN sequences** (pseudo-noise codes).

- **Advantages:** high capacity through soft frequency reuse (the same frequency is used in every cell, so reuse factor is 1), inherent security since the signal looks like noise without the code, resistance to narrowband interference and multipath fading, **soft handover** possible because the handset can talk to two base stations at once on the same frequency
- **Disadvantages:** requires very tight **power control** to avoid the **near-far problem**, where a nearby handset transmitting too loudly drowns out a distant one. CDMA systems adjust power hundreds of times per second. Capacity is **soft**: more users means more mutual interference and gradually worse quality for everyone, rather than a hard block
- **Used by:** IS-95 (cdmaOne), CDMA2000, and **UMTS/3G which uses WCDMA**

> **The near-far problem in one image:** imagine twenty people in a room all speaking at once in different languages. You can follow your own language (your code) as long as everyone speaks at similar volume. If one person right next to you shouts, you cannot hear your own conversation at all, even though they are speaking a language you are ignoring. Power control makes everyone at the base station arrive at the same loudness.

### OFDMA, Orthogonal Frequency Division Multiple Access

**Short answer:** the band is divided into a very large number of narrow, mathematically orthogonal subcarriers, and each user is assigned a group of subcarriers for a group of symbols.

**The problem OFDM solves:** at high data rates, symbols become very short. Multipath echoes arriving a fraction of a microsecond late then overlap the next symbol, causing severe intersymbol interference. Equalising a 20 MHz wideband channel is very difficult.

**The OFDM solution:** split the 20 MHz into, for example, 1200 subcarriers of 15 kHz each. Each subcarrier now carries a **much slower** symbol stream, so its symbols are long compared with the multipath delay spread. Each narrow subcarrier also sees a channel that is essentially flat, so equalisation reduces to a single complex multiplication per subcarrier.

```
  Wideband single carrier              OFDM
  ______________________          |||||||||||||||||||||
  one fast symbol stream          many slow parallel streams
  severe ISI from multipath       each stream immune to the
  hard to equalise                same multipath delay
```

**Orthogonality:** the subcarriers are spaced exactly so that each one's spectrum has a null at the centre of every other one. They can overlap without interfering, which saves about 50% of the bandwidth compared with conventional FDM guard bands.

```
   /\  /\  /\  /\        Peak of each subcarrier falls exactly
  /  \/  \/  \/  \       on the ZERO of all the others.
 -+---+---+---+---+-     No guard band needed.
```

**Cyclic prefix (guard interval):** a copy of the end of each symbol is prepended to the front. Any multipath echo arriving within the cyclic prefix duration falls into the prefix rather than the useful symbol, so ISI is eliminated entirely. The cost is overhead, typically about 7%.

- **Advantages:** excellent multipath resistance, very high spectral efficiency, simple frequency-domain equalisation, and **frequency-selective scheduling**, where the base station gives each user the subcarriers on which that particular user's channel happens to be good
- **Disadvantages:** high **peak-to-average power ratio (PAPR)**, because many subcarriers can add constructively, which demands a linear and therefore inefficient power amplifier. Also sensitive to frequency offset and Doppler, which destroys orthogonality
- **Used by:** LTE downlink, 5G NR, Wi-Fi (802.11a/g/n/ac/ax), DVB-T, WiMAX, and ADSL in wireline form (called DMT)

> **Why LTE uplink is different:** LTE uses **SC-FDMA** on the uplink rather than OFDMA, specifically because SC-FDMA has a much lower PAPR. The handset's battery and amplifier cannot afford OFDMA's peaks. The base station, plugged into the mains, can. This is exactly the constant-envelope argument from Topic 4 reappearing.

---

## 5.7 Which generation used which

| Generation | Access scheme | Notes |
|---|---|---|
| 1G (AMPS, TACS) | FDMA | Analog, 30 kHz channels |
| 2G GSM | **FDMA + TDMA** | 200 kHz carriers, 8 slots each |
| 2G IS-95 | CDMA | 1.25 MHz carriers |
| 3G UMTS | **WCDMA** | 5 MHz carriers |
| 4G LTE | **OFDMA down, SC-FDMA up** | 15 kHz subcarriers |
| 5G NR | **OFDMA both directions**, flexible numerology | Subcarrier spacing 15, 30, 60, 120 kHz |

This table alone answers a large fraction of Week 9 and 10 questions too.

---

## 5.8 Duplexing: FDD and TDD

Do not confuse duplexing with multiple access. Duplexing separates **uplink from downlink**. Multiple access separates **users from each other**.

| | FDD, Frequency Division Duplex | TDD, Time Division Duplex |
|---|---|---|
| Method | Uplink and downlink on different frequencies | Uplink and downlink alternate in time on the same frequency |
| Needs | Paired spectrum, and a duplexer in the handset | Unpaired spectrum, and tight synchronisation between cells |
| Traffic | Symmetric split fixed by the band plan | **Asymmetric split can be tuned**, useful since downloads exceed uploads |
| Used by | GSM, most LTE bands, 700 MHz | 5G mid-band (3.5 GHz), Wi-Fi, WiMAX |

---

## 5.9 Formula sheet for Topic 5

```
FDM link bandwidth = (n x channel bandwidth) + ((n-1) x guard band)
DS-0 = 8000 samples/s x 8 bits = 64 kbps
E1 = 32 x 8 x 8000 = 2.048 Mbps        (30 voice channels)
T1 = (24 x 8 + 1) x 8000 = 1.544 Mbps  (24 voice channels)
Output rate (sync TDM) = n x input rate (+ framing overhead)
Frame rate = input rate / bits per slot per input
Output slot duration = input bit duration / n
Processing gain (CDMA) = spread bandwidth / data bandwidth
Nyquist sampling rate = 2 x highest frequency
```

---

## 5.10 Traps that cost marks

1. **FDM is not FDMA.** One is combining signals at a single site, the other is coordinating separate users. Say which you mean.
2. **E1 has 32 slots but only 30 carry voice.** Slot 0 is framing and slot 16 is signalling. Writing "E1 carries 32 voice channels" loses the mark.
3. **Statistical TDM needs addresses in every slot.** Synchronous TDM does not, because slot position identifies the source.
4. **CDMA's capacity is soft, not hard.** Adding a user degrades everyone slightly rather than being refused.
5. **The near-far problem is solved by power control**, not by better codes.
6. **OFDM subcarriers overlap deliberately.** They do not need guard bands between them because they are orthogonal. The cyclic prefix is a guard in *time*, not frequency.
7. **PAPR is OFDM's main weakness**, and it is why LTE uplink uses SC-FDMA.
8. **TDD versus FDD is duplexing, not multiple access.** Keep the two answers separate.

---

## 5.11 Glossary

| Term | One-line definition |
|---|---|
| Multiplexing | Combining several signals onto one link at a single point |
| Multiple access | Coordinating several geographically separate users on one shared medium |
| Guard band | Unused frequency space between adjacent channels |
| WDM | Optical FDM using different wavelengths of light |
| DWDM | Dense WDM with very close wavelength spacing for high channel counts |
| Frame | One complete cycle of TDM slots |
| Synchronous TDM | TDM with fixed pre-assigned slots |
| Statistical TDM | TDM allocating slots on demand, requiring addressing |
| DS-0 | One 64 kbps digitised voice channel |
| E1 | The 2.048 Mbps 32-slot carrier used in Europe and Africa |
| SDH | Synchronous Digital Hierarchy, the international optical transport standard |
| Add-drop multiplexer | A device that extracts or inserts one tributary without full demultiplexing |
| Chip | One element of a CDMA spreading code |
| Processing gain | Ratio of spread bandwidth to original data bandwidth |
| Near-far problem | Degradation caused by a nearby CDMA user overpowering a distant one |
| Power control | Continuous adjustment of transmit power so all signals arrive at similar strength |
| Soft handover | Simultaneous connection to two base stations, possible in CDMA |
| Subcarrier | One narrow frequency component of an OFDM signal |
| Orthogonality | The property that each subcarrier's peak falls on the others' nulls |
| Cyclic prefix | A copy of a symbol's tail prepended to it to absorb multipath echoes |
| PAPR | Peak-to-average power ratio, high in OFDM |
| FDD / TDD | Duplexing by separate frequencies or by alternating time periods |

---

# Objective questions (Topic 5)

**Q1.** The essential difference between multiplexing and multiple access is that multiple access involves:
A. Only digital signals
B. Users at physically separate locations sharing a medium
C. Fewer channels
D. Optical fibre only

**Q2.** In FDM, each channel is allocated:
A. A time slot  B. A frequency band for the whole time  C. A unique code  D. A separate cable

**Q3.** Guard bands in FDM are used to:
A. Increase the data rate
B. Prevent adjacent channels from interfering
C. Provide error correction
D. Synchronise the receiver

**Q4.** Six 20 kHz channels are multiplexed by FDM with 4 kHz guard bands. The minimum link bandwidth is:
A. 120 kHz  B. 140 kHz  C. 144 kHz  D. 148 kHz

**Q5.** WDM is essentially:
A. TDM applied to fibre  B. FDM applied to fibre  C. CDMA applied to fibre  D. A form of error control

**Q6.** In synchronous TDM, a slot belonging to an idle input is:
A. Given to another input  B. Transmitted empty  C. Deleted from the frame  D. Filled with an address

**Q7.** Statistical TDM requires addressing in each slot because:
A. Slots are longer
B. Slot position no longer identifies the source
C. It uses more bandwidth
D. It is analog

**Q8.** One DS-0 channel has a rate of:
A. 8 kbps  B. 56 kbps  C. 64 kbps  D. 1.544 Mbps

**Q9.** An E1 carrier operates at:
A. 1.544 Mbps  B. 2.048 Mbps  C. 34.368 Mbps  D. 44.736 Mbps

**Q10.** Of the 32 slots in an E1 frame, the number carrying voice is:
A. 24  B. 30  C. 31  D. 32

**Q11.** In GSM, each 200 kHz carrier is divided into:
A. 4 time slots  B. 8 time slots  C. 16 time slots  D. 32 time slots

**Q12.** In CDMA, users are separated by:
A. Different frequencies  B. Different time slots  C. Different orthogonal codes  D. Different antennas

**Q13.** The near-far problem in CDMA is addressed by:
A. Longer codes  B. Tight power control  C. More bandwidth  D. Adding guard bands

**Q14.** CDMA capacity is described as "soft" because:
A. The codes are easy to break
B. Adding users gradually degrades quality rather than causing a hard block
C. It uses soft handover
D. The signal power is low

**Q15.** The main advantage of OFDM over a single wideband carrier is:
A. Lower cost
B. Robustness against multipath by using many slow parallel streams
C. Constant envelope
D. Lower bandwidth requirement

**Q16.** The cyclic prefix in OFDM is used to:
A. Increase the data rate
B. Absorb multipath echoes and eliminate intersymbol interference
C. Provide error correction
D. Identify the user

**Q17.** The principal disadvantage of OFDM is:
A. High peak-to-average power ratio
B. Poor spectral efficiency
C. Inability to handle multipath
D. Need for guard bands between subcarriers

**Q18.** LTE uses SC-FDMA rather than OFDMA on the uplink because SC-FDMA has:
A. Higher data rate  B. Lower PAPR, saving handset battery  C. Better security  D. Simpler codes

**Q19.** LTE downlink multiple access is:
A. TDMA  B. CDMA  C. OFDMA  D. FDMA

**Q20.** FDD differs from TDD in that FDD:
A. Uses separate frequencies for uplink and downlink
B. Uses codes to separate users
C. Requires no spectrum
D. Is used only in Wi-Fi

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | The "A" for access means distributed, independent users |
| 2 | **B** | Frequency division allocates bands, not slots |
| 3 | **B** | Guard bands stop spectral overlap |
| 4 | **B** | (6 x 20) + (5 x 4) = 120 + 20 = 140 kHz |
| 5 | **B** | Different wavelengths are just different optical frequencies |
| 6 | **B** | Fixed allocation means empty slots are still transmitted |
| 7 | **B** | Dynamic allocation removes the positional identity of a slot |
| 8 | **C** | 8000 samples x 8 bits = 64 kbps |
| 9 | **B** | 32 x 8 x 8000 = 2.048 Mbps |
| 10 | **B** | Slot 0 is framing, slot 16 is signalling, leaving 30 |
| 11 | **B** | GSM uses 8-slot TDMA per 200 kHz carrier |
| 12 | **C** | Same time, same frequency, different code |
| 13 | **B** | Power control equalises received power at the base station |
| 14 | **B** | Interference rises gradually with load |
| 15 | **B** | Long symbols on narrow subcarriers resist multipath delay spread |
| 16 | **B** | Echoes arriving within the prefix do not corrupt the useful symbol |
| 17 | **A** | Constructive addition of many subcarriers creates large peaks |
| 18 | **B** | Lower PAPR permits a more efficient handset amplifier |
| 19 | **C** | OFDMA down, SC-FDMA up |
| 20 | **A** | Frequency division duplex uses paired spectrum |

---

# Exam-style written questions with model answers

**W1. Distinguish between multiplexing and multiple access, and classify FDM, TDM, WDM, FDMA, TDMA, CDMA and OFDMA accordingly. (5 marks)**

*Model answer:* Multiplexing is the combining of several information streams onto a single transmission link at a single physical location, under the control of one multiplexer which has access to all the streams simultaneously. Multiple access is the coordination of several geographically separated and independent users so that they can share a single common medium, typically the radio channel of a cell, without mutually destructive interference. The essential difference is that a multiplexer sees all inputs locally and can schedule them perfectly, whereas users in a multiple access system cannot see one another and must be coordinated by a central controller or must contend for the medium. **FDM, TDM and WDM** are multiplexing techniques. **FDMA, TDMA, CDMA and OFDMA** are multiple access techniques. WDM is the optical implementation of FDM, and each multiple access scheme is broadly the access counterpart of the corresponding multiplexing technique.

**W2. Compare synchronous TDM with statistical TDM, and state a traffic type for which each is suited. (6 marks)**

*Model answer:* In **synchronous TDM** every input is pre-assigned a fixed slot in every frame. The slot is transmitted whether or not the input has data, so idle inputs waste capacity, but no addressing information is required because the position of a slot within the frame uniquely identifies its source. Delay is constant and predictable, and the output rate must be at least the sum of all input rates. In **statistical TDM** slots are allocated dynamically only to inputs that currently have data. No capacity is wasted on idle sources, so the output rate can be lower than the sum of the input rates and the link can be statistically oversubscribed. However each slot must carry an address identifying its source, which is overhead, and because allocation depends on instantaneous demand the delay is variable and buffering is required, with the possibility of loss under overload. Synchronous TDM suits **constant bit rate traffic such as digitised voice**, where every source produces data continuously at a fixed rate and predictable delay is essential. Statistical TDM suits **bursty data traffic such as internet access**, where sources are idle most of the time and variable delay is tolerable.

**W3. Explain how CDMA allows many users to occupy the same frequency at the same time, and discuss the near-far problem and its solution. (8 marks)**

*Model answer:* In CDMA each user's data bits are multiplied by a unique high-rate spreading code consisting of chips, whose rate is many times the data rate. This spreads the transmitted energy over a bandwidth much wider than the data requires, at a correspondingly reduced power spectral density, so the signal resembles low-level noise. All users transmit simultaneously on the same carrier frequency, and their signals sum in the air. At the receiver, correlating the composite received signal with a particular user's spreading code causes that user's signal to add coherently and rise above the background, while every other user's signal, being spread by a different and ideally orthogonal code, correlates to approximately zero and contributes only a small residual noise term. The ratio of spread bandwidth to data bandwidth is the processing gain and determines how much interference the system can tolerate. The **near-far problem** arises because signals from handsets close to the base station arrive far stronger than those from handsets at the cell edge, given that received power falls with roughly the fourth power of distance in a typical urban environment. Since code separation is not perfect, the residual correlation from a very strong nearby signal can exceed the entire wanted signal from a distant user, blocking it completely. The solution is **fast closed-loop power control**: the base station continuously measures each handset's received power and commands it to raise or lower transmit power, in UMTS as often as 1500 times per second, so that all signals arrive at the base station at approximately equal power. Without effective power control, CDMA capacity collapses.

**W4. Explain the principle of OFDM, including orthogonality and the cyclic prefix, and state one advantage and one disadvantage. (8 marks)**

*Model answer:* OFDM divides the available bandwidth into a large number of closely spaced narrowband subcarriers, and transmits the data as many parallel low-rate streams rather than one high-rate stream. Because each subcarrier carries a much lower symbol rate, its symbol duration is long compared with the delay spread introduced by multipath propagation, so intersymbol interference is greatly reduced. Furthermore, each narrow subcarrier experiences an essentially flat channel response, so equalisation reduces to a single complex multiplication per subcarrier instead of a complex time-domain equaliser. **Orthogonality** means the subcarrier spacing is chosen to equal the reciprocal of the symbol duration, so that the sinc-shaped spectrum of each subcarrier has a null at the centre frequency of every other subcarrier. The subcarriers therefore overlap in frequency without interfering, which saves roughly half the bandwidth that conventional FDM would waste on guard bands. The **cyclic prefix** is a copy of the last portion of each OFDM symbol prepended to its start, forming a guard interval in time. Any multipath echo delayed by less than the cyclic prefix duration falls entirely within the prefix, which is discarded at the receiver, so no energy from the previous symbol contaminates the useful part of the current symbol and intersymbol interference is eliminated. The cost is an overhead of typically around seven percent. A key **advantage** is excellent robustness to frequency-selective multipath fading combined with high spectral efficiency, which is why LTE, 5G NR, Wi-Fi and DVB-T all use it. A key **disadvantage** is a high peak-to-average power ratio, since many independently modulated subcarriers may add constructively, which requires a highly linear and therefore power-inefficient amplifier. This is why LTE adopts SC-FDMA rather than OFDMA on the uplink, where handset battery efficiency is critical.

**W5. A telecom operator in Ghana needs to carry 120 voice channels from a district exchange to the regional exchange. Explain how this would be done using the E-carrier hierarchy, showing all calculations. (6 marks)**

*Model answer:* Each voice channel is digitised using PCM. The voiceband is limited to 4 kHz, so by the Nyquist sampling criterion it is sampled at 8000 samples per second, and each sample is quantised to 8 bits, giving 8000 x 8 = **64 kbps per channel**, one DS-0. The E1 frame used in Ghana comprises 32 timeslots of 8 bits each, transmitted 8000 times per second, giving 32 x 8 x 8000 = **2.048 Mbps**. Of the 32 slots, slot 0 carries the frame alignment signal and slot 16 carries signalling, leaving **30 slots for voice channels**. To carry 120 voice channels therefore requires 120 / 30 = **four E1 links**, that is 4 x 2.048 = 8.192 Mbps of aggregate capacity. These four E1s would themselves be multiplexed to a higher order, typically into an E2 or directly into an E3 at 34.368 Mbps, or in a modern network mapped into an STM-1 SDH frame at 155.52 Mbps over fibre, which would also leave substantial spare capacity for growth and for data services. The line coding on each E1 would be HDB3, which substitutes patterns containing deliberate bipolar violations for runs of four zeros so that the receiver retains timing regardless of the data pattern.

---

## Checkpoint before Topic 6

You should be able to, without notes:
1. State the multiplexing versus multiple access distinction in one sentence
2. Compute an FDM link bandwidth including guard bands
3. Derive 64 kbps, 2.048 Mbps and 1.544 Mbps from first principles
4. Explain why statistical TDM needs addresses
5. Explain CDMA spreading, processing gain and the near-far problem
6. Explain orthogonality and the cyclic prefix in OFDM
7. Fill in the generation-to-access-scheme table from memory
