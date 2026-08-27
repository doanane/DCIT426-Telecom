# DCIT 426 Telecommunications
## Topic 9: 4G, 5G and Beyond

**Syllabus line (Week 10):** *LTE-A, 5G NR, network slicing, mmWave, massive MIMO; introduction to 6G research directions.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| LTE-Advanced, carrier aggregation, MIMO | Molisch Ch. 27; Rappaport Ch. 11 |
| 5G NR air interface | 3GPP TS 38.211 and 38.300 (standards, freely available) |
| Massive MIMO and beamforming | Molisch Ch. 20 |
| Network slicing, SDN and NFV | ITU-T and 3GPP TS 23.501 |

---

## 9.1 Where LTE left off

Topic 8 ended with LTE: all-IP, flat architecture, OFDMA down and SC-FDMA up. LTE Release 8 delivered around 100 Mbps down and 50 Mbps up. **LTE-Advanced (Release 10 onwards)** was the set of upgrades that got LTE to the ITU's official 4G requirement of 1 Gbps peak.

### The four LTE-Advanced techniques

| Technique | What it does | Effect |
|---|---|---|
| **Carrier aggregation** | Combine up to five 20 MHz carriers, possibly in different bands, into one logical 100 MHz pipe for a single user | Multiplies peak rate directly |
| **Higher-order MIMO** | Up to 8x8 downlink and 4x4 uplink | Multiple spatial streams multiply capacity |
| **CoMP** (Coordinated Multi-Point) | Neighbouring cells cooperate to serve a cell-edge user jointly rather than interfering with each other | Turns interference into useful signal |
| **Relay nodes and HetNets** | Small cells, picocells and relays layered under the macro cell | Densification, which is the main capacity lever |

**Carrier aggregation calculation.** An operator holds 20 MHz at 800 MHz, 20 MHz at 1800 MHz and 20 MHz at 2600 MHz. With carrier aggregation these become a single 60 MHz resource for a capable handset, roughly tripling its peak rate without any new spectrum being bought. This is why fragmented spectrum holdings became usable.

---

## 9.2 MIMO: the idea that carried 4G and 5G

**Short answer:** MIMO uses multiple antennas at both ends to send several independent data streams simultaneously on the same frequency at the same time.

```
   SISO (1 antenna each end)          MIMO 2x2
   [TX] ---------> [RX]               [TX]<   >[RX]
   one stream                              X          two independent
                                      [TX]<   >[RX]   streams on the
                                                      SAME frequency
```

**Why it works:** in a rich multipath environment, each transmit-receive antenna pair experiences a different channel. If those channels are sufficiently different, the receiver can solve a system of simultaneous equations and separate the streams. **Multipath, which was a problem in Topic 2, becomes a resource.**

**Capacity scaling:** for an NxN MIMO system, capacity scales roughly with min(Nt, Nr), the smaller of the transmit and receive antenna counts. A 4x4 system can carry roughly four times the data of a 1x1 system in the same bandwidth.

### The three MIMO modes (know the distinction)

| Mode | Purpose | When used |
|---|---|---|
| **Spatial multiplexing** | Send different data on each antenna | Good SNR, rich multipath. Maximises throughput |
| **Transmit diversity** | Send the same data redundantly | Poor SNR, cell edge. Maximises reliability |
| **Beamforming** | Shape the antenna pattern to point energy at the user | Improves SNR and reduces interference to others |

### Massive MIMO

5G takes this much further: **64, 128 or 256 antenna elements** at the base station.

- Enables very narrow, precisely steered beams instead of broad sector coverage
- Enables **MU-MIMO (multi-user MIMO)**: the base station serves several users simultaneously on the same time-frequency resource by pointing separate beams at each
- Higher frequencies help, because antenna elements are λ/2 apart. At 3.5 GHz, λ/2 is about 4 cm, so 64 elements fit in a panel roughly 35 cm square. At 900 MHz the same array would be over a metre across, which is why massive MIMO is a mid-band and mmWave technique.

```
   TRADITIONAL SECTOR              MASSIVE MIMO BEAMFORMING
        /|\                              →→→ user A
       / | \    energy sprayed          ↗
      /  |  \   across the whole    [BS] →→→ user B
     /   |   \  sector                   ↘
    /____|____\                           →→→ user C
   most energy wasted              energy focused per user,
   interferes with neighbours      interference greatly reduced
```

---

## 9.3 What 5G actually is

**Short answer:** 5G is not simply "faster 4G". It is three different services built on one flexible platform, defined by ITU-R as IMT-2020.

### The three usage scenarios (this triangle appears in almost every 5G exam question)

```
                    eMBB
              enhanced Mobile
              BroadBand
              20 Gbps peak, 100 Mbps
              user-experienced
              /              \
             /                \
            /                  \
     URLLC ------------------ mMTC
   ultra-Reliable Low        massive Machine
   Latency Communication     Type Communication
   1 ms air latency          1,000,000 devices
   99.999% reliability       per km²
```

| Scenario | Requirement | Example applications |
|---|---|---|
| **eMBB** | Very high throughput | 4K/8K video, AR/VR, fixed wireless access to homes |
| **URLLC** | Very low latency and extreme reliability | Industrial automation, remote surgery, vehicle safety, smart grid protection |
| **mMTC** | Enormous device density, low power, low cost | Smart meters, agricultural sensors, asset tracking, environmental monitoring |

**The point examiners want:** these three sets of requirements conflict. High throughput wants large blocks of spectrum and high-order modulation. Low latency wants very short transmission intervals. Massive device count wants extremely low power and simple devices. **A single fixed network design cannot satisfy all three, which is precisely why network slicing exists.**

### IMT-2020 targets versus IMT-Advanced (4G)

| Parameter | 4G (IMT-Advanced) | 5G (IMT-2020) |
|---|---|---|
| Peak data rate | 1 Gbps | **20 Gbps** |
| User experienced rate | 10 Mbps | 100 Mbps |
| Latency (air interface) | 10 ms | **1 ms** |
| Connection density | 100,000 per km² | **1,000,000 per km²** |
| Mobility | 350 km/h | 500 km/h |
| Spectrum efficiency | Baseline | 3x |
| Energy efficiency | Baseline | 100x |

---

## 9.4 The 5G NR air interface

### Frequency ranges

| Range | Band | Characteristics |
|---|---|---|
| **FR1 (sub-6 GHz)** | 410 MHz to 7.125 GHz | The workhorse. Good coverage and building penetration. Includes the coverage bands (700, 800, 900 MHz) and the capacity band (3.3 to 4.2 GHz) |
| **FR2 (mmWave)** | 24.25 to 71 GHz | Enormous bandwidth, up to 400 MHz per carrier. Very short range, blocked by walls, foliage, and even a hand over the phone |

**The trade-off from Topic 3 in its most extreme form.** A mmWave cell may have a radius of 100 to 200 metres. Deploying mmWave means deploying thousands of small cells, each needing power and fibre backhaul. This is why mmWave has been deployed mainly in dense urban centres, stadiums and airports, and why almost no African deployment uses it.

### Flexible numerology (scalable OFDM)

LTE had one fixed subcarrier spacing of 15 kHz. 5G NR allows several, selected by a numerology index μ.

| μ | Subcarrier spacing | Slot duration | Best for |
|---|---|---|---|
| 0 | 15 kHz | 1 ms | Wide-area coverage, like LTE |
| 1 | 30 kHz | 0.5 ms | Mid-band, the common choice |
| 2 | 60 kHz | 0.25 ms | Low latency |
| 3 | 120 kHz | 0.125 ms | mmWave |
| 4 | 240 kHz | 0.0625 ms | mmWave synchronisation |

```
   SCS = 15 kHz x 2^μ
```

**Why this matters:** wider subcarrier spacing means shorter symbols, therefore shorter slots, therefore lower latency. It also resists the Doppler shift and phase noise found at mmWave frequencies. Narrow spacing gives longer symbols with better multipath tolerance for wide cells. **One air interface, tunable to the use case.** This is the technical mechanism behind URLLC.

### Other NR features

- **Mini-slots**: transmission can start at any symbol rather than waiting for a slot boundary, cutting latency further
- **Bandwidth parts**: a device only needs to tune to a portion of the carrier, saving battery
- **Modulation** up to 256-QAM, and 1024-QAM in later releases
- **LDPC coding** for data and **polar codes** for control, replacing LTE's turbo codes

---

## 9.5 The 5G core and network slicing

### Service-Based Architecture

The 5G core (5GC) abandons the fixed point-to-point interfaces of the EPC. Network functions are software modules exposing APIs to one another over a common service bus.

| 5G function | Nearest LTE equivalent | Role |
|---|---|---|
| **AMF** Access and Mobility Management Function | MME | Registration, connection and mobility management |
| **SMF** Session Management Function | Part of MME and S-GW | Session setup, IP address allocation |
| **UPF** User Plane Function | S-GW and P-GW | The only user-data node. Can be placed near the edge |
| **UDM** Unified Data Management | HSS | Subscriber data |
| **AUSF** Authentication Server Function | Part of HSS | Authentication |
| **PCF** Policy Control Function | PCRF | Policy and charging rules |
| **NSSF** Network Slice Selection Function | New | Chooses which slice serves a device |

**Control and user plane separation (CUPS)** is complete: the UPF handles all user data and nothing else, so it can be physically relocated close to the user for low latency, which is the basis of **edge computing (MEC)**.

### Network slicing

**Short answer:** slicing creates several independent logical networks on one shared physical infrastructure, each configured for a different service.

```
   ONE PHYSICAL NETWORK, THREE LOGICAL SLICES

   +----------------------------------------------------+
   | SLICE 1: eMBB    high throughput, normal latency    |  video streaming
   +----------------------------------------------------+
   | SLICE 2: URLLC   low throughput, 1 ms latency,      |  factory robots
   |                  99.999% reliability                |
   +----------------------------------------------------+
   | SLICE 3: mMTC    tiny throughput, huge device count |  smart meters
   +----------------------------------------------------+
   |     SHARED RADIO, TRANSPORT AND COMPUTE HARDWARE    |
   +----------------------------------------------------+
```

Each slice has its own allocated resources, its own policies, and can even have its own core functions. A fault or congestion in one slice does not affect the others, which is called **slice isolation**.

**The enabling technologies:**
- **NFV (Network Function Virtualisation)**: network functions run as software on general-purpose servers instead of dedicated hardware boxes
- **SDN (Software Defined Networking)**: the control plane is centralised and separated from the forwarding plane, so paths can be programmed on demand

> **Ghana relevance:** slicing is the technical basis on which a single wholesale 5G network can serve multiple retail operators and enterprise customers with different guarantees. It is exactly the model behind a shared wholesale infrastructure approach, where one physical network is sliced and sold to several operators rather than each building its own.

---

## 9.6 Deployment: NSA versus SA

| | Non-Standalone (NSA) | Standalone (SA) |
|---|---|---|
| Core | Uses the existing **LTE EPC** | Uses the new **5G Core** |
| Control plane | Anchored on LTE | Fully 5G |
| Deployment cost | Low, reuses the 4G core | High, new core required |
| Features available | Faster speeds only | **Slicing, URLLC, mMTC all require SA** |
| Typical use | The first phase of almost every 5G rollout worldwide | The end state |

**Exam-worthy point:** most early "5G" services were NSA, delivering eMBB speed improvements but none of the URLLC or slicing capabilities that define 5G conceptually. When a question asks whether a country "has 5G", the NSA versus SA distinction is the substantive answer.

---

## 9.7 5G in Ghana

Ghana's approach differs from the standard model of each operator building its own network. Instead a **shared wholesale infrastructure model** was adopted, in which a wholesale entity (Next Gen InfraCo, NGIC) built 5G infrastructure and retail operators bought capacity from it.

The regulatory picture has since shifted. In 2026 the **NCA removed NGIC's exclusivity** as the sole wholesale 5G provider, concluding that a competitive wholesale market would better serve investment, innovation and service quality. In July 2026 the NCA published a **Request for Applications for spectrum licences in the 700 MHz, 2.3 GHz and 3 GHz bands**, open to licensed mobile operators, MVNOs, broadband wireless access providers, ISPs and wholly Ghanaian-owned new entrants.

**Why those three bands, in the language of this course:**

| Band | Role | Reasoning from Topic 3 |
|---|---|---|
| **700 MHz** | Coverage layer | Long wavelength, excellent propagation and building penetration, large cells. Ideal for rural coverage where cell sites are expensive |
| **2.3 GHz** | Capacity and coverage balance | Compromise band, often used in TDD mode |
| **3.3 to 3.4 and 3.6 to 3.65 GHz** | Capacity layer | Wide contiguous blocks available, suits massive MIMO with practical antenna sizes, but shorter range demands denser sites |

**The structural constraint on 5G uptake in Ghana** is not the network. It is that the large majority of subscribers are still on 3G and 4G handsets, and 5G-capable devices remain expensive relative to income. A network with no compatible devices generates no revenue, which is the standard argument for why operators delayed commercialising 5G.

> This is a strong, specific answer for the outline's "assess their impact on Ghana's digital economy" learning outcome. Note that regulatory positions change, so verify the current state of licensing before an exam if the question demands currency.

---

## 9.8 Beyond 5G: 6G research directions

6G is not standardised. Expect an exam question phrased as "discuss emerging trends", where naming credible directions with a reason for each earns the marks.

| Direction | Idea | Challenge |
|---|---|---|
| **Terahertz (0.1 to 10 THz)** | Enormous bandwidth for terabit rates | Extreme path loss, absorption by atmospheric water vapour, range of metres |
| **AI-native air interface** | Machine learning replaces hand-designed components such as channel estimation and scheduling | Explainability, training data, energy cost |
| **Integrated Sensing and Communication (ISAC)** | The same radio signal performs communication and radar-like sensing of the environment | Privacy implications, waveform design |
| **Non-Terrestrial Networks (NTN)** | LEO satellites integrated directly into the mobile standard, giving global coverage (see Topic 11) | Doppler shift, handover between fast-moving satellites |
| **Cell-free massive MIMO** | Many distributed antennas jointly serve users, so there are no cell boundaries at all | Enormous fronthaul capacity required |
| **Energy efficiency and sustainability** | Networks that scale power with load, and zero-energy IoT devices harvesting ambient energy | A genuine constraint, since radio access networks dominate operator energy cost |
| **Digital twin networks** | A live simulation of the network used to test changes before deployment | Modelling accuracy |

**The honest framing for an essay:** 5G's own targets are not yet fully realised in most markets, and much of what is discussed as 6G is a continuation of 5G's direction rather than a break with it. For a country like Ghana, the practical priority is closing the 4G coverage and affordability gap rather than pursuing terahertz research.

---

## 9.9 Formula sheet for Topic 9

```
SCS = 15 kHz x 2^μ                        5G NR subcarrier spacing
MIMO capacity scales with min(Nt, Nr)     spatial multiplexing gain
Aggregate bandwidth = sum of component carriers
Peak rate ≈ streams x bandwidth x spectral efficiency
Antenna element spacing = λ/2 = c/(2f)
```

**Worked example (antenna array size).** How wide is a 64-element (8x8) array at 3.5 GHz?
- λ = 3x10^8 / 3.5x10^9 = 0.0857 m
- λ/2 = 4.3 cm
- 8 elements x 4.3 cm = **about 34 cm per side**. Practical for a rooftop panel.
At 700 MHz, λ/2 = 21 cm, so the same array would be **1.7 m per side**. This is why massive MIMO belongs in mid-band and above.

---

## 9.10 Traps that cost marks

1. **5G is not just speed.** Any answer that discusses only throughput misses two of the three usage scenarios.
2. **URLLC and slicing require standalone 5G.** NSA gives eMBB only.
3. **Massive MIMO needs high frequency** because antenna spacing is λ/2. Explain the physical reason, not just the fact.
4. **mmWave is not the main 5G band.** FR1 sub-6 GHz carries almost all real deployments.
5. **Carrier aggregation does not create spectrum.** It combines spectrum the operator already holds.
6. **NFV and SDN are different.** NFV virtualises functions, SDN separates and centralises control.
7. **Spatial multiplexing and diversity are opposite uses of MIMO.** One maximises rate, the other reliability.
8. **Wider subcarrier spacing reduces latency but reduces multipath tolerance.** State both sides.

---

## 9.11 Glossary

| Term | One-line definition |
|---|---|
| LTE-Advanced | The Release 10 upgrades that met the ITU 4G requirement |
| Carrier aggregation | Combining several carriers into one logical channel for a user |
| MIMO | Multiple antennas at both ends carrying multiple streams |
| Spatial multiplexing | Sending different data on each antenna to raise throughput |
| Transmit diversity | Sending redundant copies on each antenna to raise reliability |
| Beamforming | Shaping the antenna pattern to direct energy at a specific user |
| Massive MIMO | Base station arrays of 64 or more antenna elements |
| MU-MIMO | Serving several users on the same resource using separate beams |
| CoMP | Coordinated transmission from several cells to a cell-edge user |
| HetNet | A network mixing macro cells with small cells and relays |
| eMBB | Enhanced mobile broadband, the high-throughput 5G scenario |
| URLLC | Ultra-reliable low-latency communication |
| mMTC | Massive machine type communication for dense IoT |
| IMT-2020 | The ITU requirement set defining 5G |
| FR1 / FR2 | 5G sub-6 GHz and millimetre wave frequency ranges |
| Numerology | The selectable subcarrier spacing in 5G NR |
| Mini-slot | A transmission shorter than a full slot, for low latency |
| 5GC | The service-based 5G core network |
| AMF / SMF / UPF | 5G access and mobility, session management, and user plane functions |
| CUPS | Control and user plane separation |
| MEC | Multi-access edge computing, placing compute near the user |
| Network slicing | Creating isolated logical networks on shared infrastructure |
| NFV | Running network functions as software on general-purpose servers |
| SDN | Separating and centralising the network control plane |
| NSA / SA | Non-standalone 5G on an LTE core, versus standalone on a 5G core |
| NTN | Non-terrestrial network, satellites integrated into mobile standards |
| ISAC | Integrated sensing and communication |

---

# Objective questions (Topic 9)

**Q1.** The ITU requirement set defining 5G is called:
A. IMT-Advanced  B. IMT-2000  C. IMT-2020  D. LTE-Advanced

**Q2.** The three 5G usage scenarios are:
A. Voice, video, data
B. eMBB, URLLC, mMTC
C. FDD, TDD, SDD
D. NSA, SA, NR

**Q3.** The 5G target for air interface latency is:
A. 100 ms  B. 10 ms  C. 1 ms  D. 0.01 ms

**Q4.** The 5G target for connection density is:
A. 1000 per km²  B. 100,000 per km²  C. 1,000,000 per km²  D. Unlimited

**Q5.** Carrier aggregation increases peak data rate by:
A. Adding more antennas
B. Combining multiple carriers into one logical channel
C. Increasing transmit power
D. Reducing the subcarrier spacing

**Q6.** MIMO spatial multiplexing capacity scales approximately with:
A. Nt + Nr  B. Nt x Nr  C. min(Nt, Nr)  D. max(Nt, Nr)

**Q7.** At the cell edge with poor SNR, MIMO is best used for:
A. Spatial multiplexing  B. Transmit diversity  C. Carrier aggregation  D. Slicing

**Q8.** Massive MIMO is impractical at 700 MHz mainly because:
A. The band is too narrow
B. Antenna elements must be λ/2 apart, making the array physically enormous
C. It causes too much interference
D. Handsets cannot support it

**Q9.** 5G FR2 refers to:
A. Sub-6 GHz  B. Millimetre wave bands above 24 GHz  C. The 700 MHz band  D. Satellite bands

**Q10.** In 5G NR, subcarrier spacing is given by:
A. 15 kHz x 2^μ  B. 15 kHz / μ  C. 200 kHz  D. Fixed at 15 kHz

**Q11.** Wider subcarrier spacing in 5G NR primarily gives:
A. Better multipath tolerance  B. Shorter slots and lower latency  C. Longer range  D. Higher transmit power

**Q12.** Network slicing provides:
A. Physical separation of networks
B. Isolated logical networks with different characteristics on shared infrastructure
C. Faster handover
D. More spectrum

**Q13.** Which 5G capability requires standalone deployment?
A. Higher download speed  B. Network slicing and URLLC  C. Carrier aggregation  D. 256-QAM

**Q14.** The only user-plane node in the 5G core is the:
A. AMF  B. SMF  C. UPF  D. AUSF

**Q15.** NFV is best described as:
A. Centralising the control plane
B. Running network functions as software on general-purpose hardware
C. A new modulation scheme
D. A satellite technology

**Q16.** In an NSA 5G deployment, the control plane is anchored on:
A. The 5G core  B. The LTE EPC  C. A satellite link  D. The internet

**Q17.** The 700 MHz band is valued by operators mainly for:
A. Its very wide bandwidth
B. Its excellent propagation and building penetration
C. Its suitability for massive MIMO
D. Its low licensing cost

**Q18.** A significant practical barrier to 5G uptake in Ghana is:
A. Lack of any spectrum
B. The cost of 5G-capable handsets relative to income
C. Absence of a regulator
D. Lack of submarine cable capacity

**Q19.** Which is a recognised 6G research direction?
A. Returning to circuit switching
B. Terahertz communication and AI-native air interfaces
C. Replacing OFDM with FDMA
D. Eliminating base stations entirely

**Q20.** Integrated Sensing and Communication (ISAC) refers to:
A. Combining Wi-Fi and cellular
B. Using the same radio signal for both communication and environmental sensing
C. Sensor networks only
D. Satellite integration

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **C** | IMT-2020 is 5G, IMT-Advanced was 4G, IMT-2000 was 3G |
| 2 | **B** | The standard 5G triangle |
| 3 | **C** | 1 ms over the air, versus 10 ms for LTE |
| 4 | **C** | One million devices per square kilometre for mMTC |
| 5 | **B** | Several component carriers become one wider logical pipe |
| 6 | **C** | The smaller antenna count limits the number of independent streams |
| 7 | **B** | Poor SNR cannot support multiple streams, so redundancy is used instead |
| 8 | **B** | λ/2 at 700 MHz is 21 cm, so a 64-element array is metres across |
| 9 | **B** | FR1 is sub-6 GHz, FR2 is mmWave |
| 10 | **A** | Scalable numerology doubles the spacing with each index |
| 11 | **B** | Shorter symbols mean shorter slots, hence lower latency |
| 12 | **B** | Logical isolation on common physical resources |
| 13 | **B** | NSA reuses the LTE core and cannot support slicing or URLLC |
| 14 | **C** | The user plane function, all others are control plane |
| 15 | **B** | Virtualising functions, as distinct from SDN which centralises control |
| 16 | **B** | NSA anchors control on LTE and adds 5G for data capacity |
| 17 | **B** | Low frequency gives large cells and good indoor coverage |
| 18 | **B** | Most subscribers remain on 3G and 4G devices |
| 19 | **B** | Both are widely cited directions in current research |
| 20 | **B** | The waveform does double duty as communication and radar |

---

# Exam-style written questions with model answers

**W1. Describe the three usage scenarios defined for 5G, giving the key requirement and two example applications of each, and explain why they cannot be met by a single fixed network configuration. (8 marks)**

*Model answer:* **Enhanced Mobile Broadband (eMBB)** targets very high throughput, with a peak of 20 gigabits per second and a user-experienced rate of 100 megabits per second. Example applications include ultra-high-definition video streaming and fixed wireless broadband to homes. **Ultra-Reliable Low-Latency Communication (URLLC)** targets an air interface latency of one millisecond with reliability of 99.999 percent, at modest data rates. Example applications include industrial robot control and vehicle safety messaging. **Massive Machine Type Communication (mMTC)** targets a connection density of one million devices per square kilometre with very low device cost and very long battery life, at tiny data rates. Example applications include smart electricity metering and agricultural soil sensors. These requirements are mutually contradictory. High throughput requires large contiguous blocks of spectrum, high-order modulation and long transmission intervals to amortise overhead, whereas low latency requires very short transmission intervals and immediate access to the channel, which reduces efficiency. Massive device counts require extremely simple, low-power devices that cannot support wide bandwidths or complex signal processing, and require a random access procedure optimised for enormous numbers of very short transmissions rather than for sustained sessions. Any single fixed configuration must therefore compromise, delivering poor performance for at least two of the three. The 5G solution is to make the air interface configurable through selectable numerologies and mini-slots, and to make the core network sliceable, so that each service runs on a logical network tuned to its own requirements while sharing the same physical infrastructure.

**W2. Explain the principle of MIMO, distinguishing spatial multiplexing, transmit diversity and beamforming, and explain why massive MIMO is associated with mid-band and millimetre wave frequencies. (8 marks)**

*Model answer:* MIMO uses multiple antennas at both the transmitter and the receiver. Because each transmit-receive antenna pair experiences a physically different propagation path, the channel between them differs, and in a sufficiently rich multipath environment these differences allow the receiver to separate signals that were transmitted simultaneously on the same frequency. Multipath, which is an impairment for a single-antenna system, therefore becomes an exploitable resource. In **spatial multiplexing**, genuinely different data streams are transmitted from each antenna and separated at the receiver by solving the resulting system of equations; capacity scales approximately with the smaller of the transmit and receive antenna counts, so this mode maximises throughput but requires good signal-to-noise ratio and decorrelated channels. In **transmit diversity**, the same data is sent redundantly from multiple antennas using a scheme such as space-time block coding, so that a deep fade on one path does not destroy the message; this mode does not raise throughput but greatly improves reliability, which makes it the correct choice at the cell edge where signal-to-noise ratio is poor. In **beamforming**, the relative phases of the antenna elements are adjusted so that the transmitted wavefronts add constructively in the direction of the intended user and destructively elsewhere, which raises received power for that user and simultaneously reduces interference to others. **Massive MIMO**, using arrays of sixty-four or more elements, permits very narrow beams and multi-user MIMO in which several users are served on the same time-frequency resource by separate beams. It is associated with mid-band and millimetre wave frequencies because antenna elements must be spaced approximately half a wavelength apart to form a useful array. At 3.5 gigahertz, half a wavelength is about 4.3 centimetres, so an eight-by-eight array fits in a panel roughly 34 centimetres square, which is practical on a rooftop. At 700 megahertz, half a wavelength is about 21 centimetres and the same array would measure over 1.7 metres on each side, which is impractical in terms of size, weight and wind loading on a mast.

**W3. Explain network slicing, the technologies that enable it, and give a practical example of two slices with different requirements. (7 marks)**

*Model answer:* Network slicing is the creation of multiple independent, logically isolated end-to-end networks over a single shared physical infrastructure, each configured with the resources, policies and performance characteristics required by a particular service. Each slice extends across the radio access network, the transport network and the core, and each behaves to its users as though it were a dedicated network. Isolation means that congestion, faults or security incidents within one slice do not affect the others. Slicing is enabled by two complementary technologies. **Network Function Virtualisation** replaces dedicated hardware appliances with software network functions running on general-purpose servers, so that a new instance of a function can be created for a slice simply by starting another software instance. **Software Defined Networking** separates the network control plane from the forwarding plane and centralises it, so that traffic paths and resource allocations can be programmed dynamically per slice rather than configured manually per device. In the 5G core, the Network Slice Selection Function determines which slice should serve each device, and complete control and user plane separation allows a slice's user plane function to be placed close to the users when low latency is required. As a practical example, an operator might run one slice for **consumer mobile broadband**, configured for maximum throughput with large scheduling blocks, high-order modulation and a centralised user plane function, since occasional delay of tens of milliseconds is unimportant for video streaming. In parallel it might run a second slice for a **factory automation customer**, configured with very short transmission intervals, wide subcarrier spacing, aggressive redundancy for reliability, and a user plane function placed physically at the factory site so that control traffic never leaves the premises. The same base stations, transport links and servers carry both, but neither can degrade the other.

**W4. Distinguish between non-standalone and standalone 5G deployment, and discuss the implications for the services an operator can offer. (6 marks)**

*Model answer:* In a **non-standalone** deployment the operator installs 5G New Radio base stations but continues to use the existing LTE Evolved Packet Core. The control plane is anchored on LTE, so a device must maintain an LTE connection and uses the 5G radio only as an additional data-carrying leg. This is inexpensive and fast to deploy because it reuses the entire existing core investment, and it delivers the most visible benefit, which is a substantial increase in downlink throughput. However, because the core is still the LTE EPC, the operator cannot offer network slicing, cannot guarantee the millisecond latency required for ultra-reliable low-latency communication, and cannot support the massive machine type communication features that depend on the 5G core's service-based architecture. In a **standalone** deployment the operator installs the full 5G core, with its service-based architecture, complete control and user plane separation, and the Network Slice Selection Function. The device connects to 5G alone with no LTE anchor. This requires substantial additional investment and operational change, but it unlocks the entire 5G feature set: slicing for enterprise customers, edge computing through relocatable user plane functions, ultra-reliable low-latency services for industrial and vehicular applications, and efficient support for very large numbers of low-power devices. The practical implication is that a market advertising "5G" on a non-standalone basis is offering faster mobile broadband, which is an incremental improvement, whereas the transformational enterprise and industrial applications that justify 5G's strategic significance require the standalone core.

**W5. Discuss the current state and prospects of 5G in Ghana, identifying the technical, regulatory and economic factors that shape it. (10 marks)**

*Points to build the answer on:* On the **regulatory** side, Ghana adopted an unusual shared wholesale infrastructure model in which a single wholesale entity built 5G infrastructure and retail operators purchased capacity, rather than each operator constructing its own network. The stated rationale was to avoid duplicated capital expenditure in a market where returns are uncertain, and to extend coverage to rural areas. The NCA has since amended that licence to remove the exclusivity condition, concluding that a competitive wholesale market better serves investment, innovation, network resilience and service quality, and has opened a competitive Request for Applications for spectrum in the 700 MHz, 2.3 GHz and 3 GHz bands, with eligibility extended to mobile operators, MVNOs, broadband wireless access providers, ISPs and wholly Ghanaian-owned new entrants. On the **technical** side, the band choice is coherent with propagation physics: 700 MHz provides the coverage layer with large cells and good building penetration suitable for rural areas, while the 3.3 to 3.4 and 3.6 to 3.65 GHz segments provide the capacity layer with wide contiguous blocks that suit massive MIMO at practical antenna sizes but require far denser site deployment. Deploying the capacity layer meaningfully requires fibre backhaul to a large number of new sites, and reliable power at those sites, both of which are significant constraints. On the **economic** side, the binding constraint is the device base. The great majority of Ghanaian subscribers remain on 3G and 4G handsets, and 5G-capable devices remain expensive relative to average income, so a deployed network has few compatible users and generates limited revenue, which in turn weakens the business case for further deployment. The market structure compounds this: MTN holds a dominant share and is designated by the NCA as having Significant Market Power, subject to asymmetric price regulation, while the second and third operators have far smaller subscriber bases and correspondingly less investment capacity. A balanced conclusion is that the more valuable near-term objective for Ghana's digital economy is extending affordable 4G coverage and improving device affordability, with 5G capacity concentrated initially in dense urban areas and in enterprise applications such as mining, ports and manufacturing where the value of low latency and high reliability can be monetised directly. Note that the regulatory position continues to develop, so current NCA publications should be checked.

---

## Checkpoint before Topic 10

You should be able to, without notes:
1. Draw the eMBB, URLLC, mMTC triangle with a requirement and example for each
2. State the IMT-2020 targets for peak rate, latency and connection density
3. Distinguish the three MIMO modes and say when each is used
4. Explain the λ/2 argument for why massive MIMO needs higher frequencies
5. Compute 5G NR subcarrier spacing from the numerology index
6. Explain slicing, NFV and SDN and how they relate
7. Distinguish NSA from SA and state what each can and cannot deliver
8. Give a specific, current account of Ghana's 5G position with band reasoning
