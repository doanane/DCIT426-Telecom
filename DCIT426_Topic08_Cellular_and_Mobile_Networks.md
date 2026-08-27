# DCIT 426 Telecommunications
## Topic 8: Cellular and Mobile Networks

**Syllabus line (Week 9):** *Cellular concept, frequency reuse, handoff; GSM/GPRS to LTE; architecture and mobility management.*

> Note on numbering: Week 8 in your outline is the mid-semester examination and project checkpoint, not a teaching week. This is therefore the eighth taught topic.

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Cellular concept, reuse, handoff | Rappaport Ch. 3 (the definitive treatment); Stallings Ch. 14 |
| GSM architecture | Stallings Ch. 14.2; Forouzan Ch. 16 |
| GPRS, EDGE, UMTS | Molisch Ch. 24 and 26 |
| LTE and EPC | Molisch Ch. 27; 3GPP TS 36 series |

---

## 8.1 The cellular concept

**The problem it solved:** early mobile radio used one powerful transmitter covering a whole city on a handful of channels. Capacity was a few dozen simultaneous users for an entire metropolitan area, and there was no way to increase it.

**The insight:** instead of one high-power transmitter, use **many low-power transmitters each covering a small area (a cell)**, and **reuse the same frequencies** in cells that are far enough apart that their mutual interference is acceptable.

```
   ONE BIG TRANSMITTER              MANY SMALL CELLS
        ________                      __  __  __
      /        \                    /A \/B \/C \
     |   ONE    |                   \__/\__/\__/
     |  set of  |                   /D \/A \/B \    A is reused here
     | channels |                   \__/\__/\__/    and here
      \________/                    /C \/D \/A \
                                    \__/\__/\__/
   capacity = N channels        capacity = N x (number of clusters)
   for the WHOLE city           for the same city
```

**Why hexagons?** A circle is the true coverage shape, but circles either overlap or leave gaps. Of the three regular polygons that tile a plane without gaps (triangle, square, hexagon), the hexagon is closest to a circle and therefore covers the area with the fewest cells for a given radius. Hexagons are a **planning abstraction**, not physical reality.

---

## 8.2 Frequency reuse and cluster size

**Short answer:** the available channels are divided among the cells of a **cluster**, and the whole cluster pattern is repeated across the coverage area.

**Cluster size N** must satisfy:
```
N = i² + i j + j²        where i and j are non-negative integers

Valid values: 1, 3, 4, 7, 9, 12, 13, 19, 21 ...
```

**Key formulas:**
```
Channels per cell     = S / N          (S = total channels available)
System capacity       = M x S          (M = number of clusters in the area)
Reuse distance        D = R x sqrt(3N) (R = cell radius)
Co-channel reuse ratio Q = D/R = sqrt(3N)
Signal-to-interference ratio  S/I = (1/i0) x Q^n
                              where i0 = number of co-channel interferers,
                              n = path loss exponent (typically 3 to 4)
```

### Worked example 1
A system has 490 channels and uses a cluster size of 7.
- Channels per cell = 490 / 7 = **70 channels**
- If the city contains 20 clusters, total capacity = 20 x 490 = **9800 simultaneous calls**, versus 490 for a single-transmitter system. A twentyfold increase from the same spectrum.

### Worked example 2
Cell radius R = 2 km, cluster size N = 7. Find the reuse distance.
- Q = sqrt(3 x 7) = sqrt(21) = 4.58
- D = 4.58 x 2 = **9.17 km**

The same frequency may be re-used at a distance of about 9.2 km.

### The central trade-off (this is what examiners want)

```
SMALL N (e.g. N = 3)                  LARGE N (e.g. N = 12)
+ more channels per cell              + co-channel cells are further apart
+ higher capacity                     + better signal-to-interference ratio
+ frequency reused more often         + better call quality
- co-channel cells are CLOSER         - fewer channels per cell
- WORSE interference                  - LOWER capacity
```

**Capacity and quality pull in opposite directions.** Cluster size is the dial that trades one against the other. This one sentence answers a large family of exam questions.

---

## 8.3 Increasing capacity further

| Technique | What it does | Cost |
|---|---|---|
| **Cell splitting** | Replace one cell with several smaller cells of reduced radius and power | More base stations, more handovers, more planning |
| **Sectoring** | Replace the omnidirectional antenna with 3 or 6 directional antennas, reducing the number of co-channel interferers from 6 to 2 | Channels are divided among sectors, so trunking efficiency falls |
| **Microcells and picocells** | Very small cells for streets, buildings, stadiums | Dense backhaul required |
| **Higher-order modulation** | More bits per symbol where SNR permits (Topic 4) | Needs good signal quality |
| **More spectrum** | Buy or be licensed additional bands | Expensive, and requires regulator action |

```
   SECTORING with 120-degree antennas

        \  |  /            Instead of one omni antenna radiating
         \ | /             interference in all directions, three
      ----[BS]----         directional antennas each cover 120 degrees.
         / | \             Co-channel interferers drop from 6 to 2,
        /  |  \            which improves S/I by about 5 dB and permits
                           a smaller cluster size N.
```

> **Ghana relevance:** Accra's dense areas such as Osu, Circle and Madina use heavily sectored cells with small radii, while a cell serving a stretch of the N1 highway or a rural district uses a large omnidirectional cell on a low band such as 800 or 900 MHz to maximise reach. This is the low-versus-high-frequency trade-off from Topic 3 applied in practice.

---

## 8.4 Handoff (handover)

**Short answer:** handoff transfers an ongoing call from one cell to another as the subscriber moves, without the user noticing.

```
   Signal strength as the user drives from cell A to cell B

   strength ^
            |  A ~~~~~~~~___
            |               \___          ___~~~~~~ B
            |                   \___  ___/
            | - - - - - - - - - - -X- - - - - - - - handoff threshold
            |                    __/ \__
            | - - - - - - - - - / - - - \- - - - -  minimum usable level
            +----------------------------------> distance
                            handoff occurs here,
                            BEFORE the level becomes unusable
```

The threshold must sit **above** the minimum usable level by a margin. If the margin is too small the call drops before handoff completes. If it is too large, handoffs occur unnecessarily and load the network. **Hysteresis** is applied so the handset does not oscillate between two cells at the boundary, a condition known as the **ping-pong effect**.

### Types of handoff

| Type | Description | Technology |
|---|---|---|
| **Hard handoff** | "Break before make". The old connection is released before the new one is established. A brief interruption occurs | GSM, FDMA and TDMA systems |
| **Soft handoff** | "Make before break". The handset communicates with two or more base stations at once and the network combines the signals | **CDMA / UMTS only**, because all cells use the same frequency |
| **Softer handoff** | Between two sectors of the same base station | CDMA |
| **Intra-cell** | Change of channel within the same cell, to escape interference | GSM |

**Why soft handoff is only possible in CDMA:** in GSM, adjacent cells use different frequencies, so the handset would need two receivers to hear both at once. In CDMA every cell uses the same frequency and cells are distinguished by code, so one receiver can track two base stations simultaneously. This yields **macro diversity**, improving reliability at the cell edge.

**MAHO (Mobile Assisted Handover):** the handset continuously measures the signal strength of neighbouring cells during its idle TDMA slots and reports the results to the network, which then decides when and where to hand over. This is a direct benefit of TDMA, since the handset is not transmitting for seven out of every eight slots and can use that time to listen elsewhere.

---

## 8.5 GSM architecture

**Short answer:** GSM divides into three subsystems: the mobile station, the base station subsystem, and the network switching subsystem, with an operations subsystem alongside.

```
  +-------+       +---------------------+      +------------------------------+
  |  MS   |  Um   |        BSS          | Abis |            NSS               |
  |       |<----->|                     |<---->|                              |
  | +---+ | air   |  +-----+   +-----+  |      |  +-----+   +-----+  +-----+  |
  | |ME | | iface |  | BTS |---| BSC |  |      |  | MSC |---| HLR |  | AuC |  |
  | +---+ |       |  +-----+   +-----+  |      |  +--+--+   +-----+  +-----+  |
  | +---+ |       |  +-----+      |     |      |     |      +-----+  +-----+  |
  | |SIM| |       |  | BTS |------+     |      |     |      | VLR |  | EIR |  |
  | +---+ |       |  +-----+            |      |     |      +-----+  +-----+  |
  +-------+       +---------------------+      |  +--+---+                    |
                                               |  | GMSC |--> PSTN            |
                                               +------------------------------+
```

### Every element and what it does

| Element | Full name | Function |
|---|---|---|
| **ME** | Mobile Equipment | The physical handset. Identified by its **IMEI** |
| **SIM** | Subscriber Identity Module | Holds the **IMSI**, the secret key Ki, and the subscriber's identity. **Separating identity from hardware was a GSM innovation**: change phones, keep your number |
| **BTS** | Base Transceiver Station | The radio equipment and antennas of a cell. Handles the air interface, modulation, and encryption over the air |
| **BSC** | Base Station Controller | Controls many BTSs. Manages radio channel allocation, power control, frequency hopping, and handovers between its own BTSs |
| **MSC** | Mobile Switching Centre | The circuit switch. Sets up and releases calls, performs handovers between BSCs, and produces billing records |
| **GMSC** | Gateway MSC | The MSC that interconnects with the PSTN and other networks |
| **HLR** | Home Location Register | The **permanent** database of every subscriber of this network: IMSI, MSISDN, subscribed services, and a pointer to the VLR currently serving them |
| **VLR** | Visitor Location Register | A **temporary** database of subscribers currently in this MSC's area, whether home subscribers or roamers. Reduces the need to query the HLR constantly |
| **AuC** | Authentication Centre | Holds the secret key Ki for each SIM and generates the triplets used for authentication and ciphering |
| **EIR** | Equipment Identity Register | Lists IMEIs as white, grey or black. **Blacklisting a stolen handset's IMEI is done here** |
| **OMC** | Operations and Maintenance Centre | Network monitoring, configuration and fault management |

### Interfaces
| Interface | Between |
|---|---|
| **Um** | MS and BTS. The air interface |
| **Abis** | BTS and BSC |
| **A** | BSC and MSC |
| **MAP over SS7** | MSC, HLR, VLR, AuC, and other networks |

**Notice the connection to Topic 7:** all the signalling between MSC, HLR and VLR is **MAP running over SS7**. The mobile network's control plane is the SS7 network you studied last week.

### GSM air interface numbers (memorise)

| Parameter | Value |
|---|---|
| Uplink band (GSM 900) | 890 to 915 MHz |
| Downlink band (GSM 900) | 935 to 960 MHz |
| Duplex spacing | 45 MHz |
| Carrier spacing | **200 kHz** |
| Number of carriers | 124 |
| Access method | **FDMA + TDMA**, 8 time slots per carrier |
| Modulation | **GMSK** (constant envelope, see Topic 4) |
| Frame duration | 4.615 ms |
| Speech codec | 13 kbps full rate |
| Cell radius | 350 m to 35 km |

**Calculation you should be able to do:** 124 carriers x 8 slots = **992 traffic channels**, minus those used for control. This is the total per operator per band, shared out across cells by the reuse pattern.

### Identifiers

| Identifier | Meaning | Stored in |
|---|---|---|
| **IMSI** | International Mobile Subscriber Identity. Identifies the subscription globally | SIM and HLR |
| **MSISDN** | The dialled telephone number | HLR |
| **IMEI** | International Mobile Equipment Identity. Identifies the handset | The handset, checked against the EIR |
| **TMSI** | Temporary Mobile Subscriber Identity. Used over the air **instead of the IMSI to protect privacy** | Assigned by the VLR |

---

## 8.6 Mobility management

### Location update
When a handset moves into a new **location area**, it informs the network. The new VLR obtains the subscriber's profile from the HLR, and the HLR updates its pointer to show which VLR is now serving that subscriber. The old VLR is instructed to delete the record.

### Paging
When a call arrives for a subscriber:
```
1. Call reaches the GMSC of the home network
2. GMSC queries the HLR for the subscriber's location
3. HLR asks the serving VLR for a roaming number (MSRN)
4. Call is routed to the serving MSC
5. The MSC PAGES the handset across all cells in its location area
6. The handset responds, a traffic channel is assigned, and the phone rings
```

**Why location areas exist:** if the network tracked every cell change, signalling load would be enormous. If it tracked nothing, it would have to page the entire country. A location area is the compromise: update on crossing a location area boundary, page within the whole location area.

### Roaming
The same mechanism works internationally. A Ghanaian MTN subscriber in Nigeria registers with a Nigerian VLR, which uses **MAP over international SS7** to fetch the profile from the MTN Ghana HLR. Calls to the Ghanaian number are routed to Ghana first, then forwarded to Nigeria, which is why roaming has historically been expensive.

---

## 8.7 GSM security

| Function | Algorithm | Mechanism |
|---|---|---|
| **Authentication** | A3 | Network sends a random number RAND. SIM computes SRES = A3(RAND, Ki). The AuC computes the same. If they match, the subscriber is authentic |
| **Key generation** | A8 | Kc = A8(RAND, Ki), producing the ciphering key |
| **Encryption** | A5 | The air interface between MS and BTS is encrypted with Kc using A5/1, A5/2 or A5/3 |
| **Identity protection** | TMSI | A temporary identity replaces the IMSI over the air |

**The critical weakness:** GSM provides **one-way authentication only**. The network authenticates the subscriber, but the subscriber cannot verify the network. This permits a **false base station (IMSI catcher)** attack, in which a fake BTS with a strong signal attracts handsets, disables encryption, and intercepts traffic. Encryption also protects only the radio link; traffic is in the clear on the wired portion. **UMTS fixed this by introducing mutual authentication.** This is a strong point for the Week 13 security essay.

---

## 8.8 The evolution: GPRS, EDGE, UMTS, LTE

### GPRS (2.5G) — adding packet switching

GSM was circuit switched, so data meant occupying a whole 9.6 kbps traffic channel for the duration. **GPRS overlays a packet-switched domain** on the same radio network.

```
   Two new core nodes:

   SGSN  Serving GPRS Support Node
         The packet equivalent of the MSC. Mobility management,
         authentication and session management for data.

   GGSN  Gateway GPRS Support Node
         The gateway to external packet networks (the internet).
         Assigns IP addresses and acts as the anchor point.
```

**Key change:** radio resources are allocated **only when data is actually being sent**, and a user can be allocated multiple time slots. This makes always-on data and per-megabyte billing possible for the first time. Theoretical rates reach about 171 kbps, with 40 to 50 kbps typical.

### EDGE (2.75G)
Replaces GMSK with **8-PSK** modulation (Topic 4), tripling the bits per symbol. Rates reach around 384 kbps. It requires only a software and transceiver upgrade, not new spectrum, which is why it was widely deployed across Africa as an inexpensive step up.

### UMTS / 3G
- Access method changes to **WCDMA** with 5 MHz carriers
- BTS becomes **Node B**, BSC becomes **RNC (Radio Network Controller)**
- Core keeps both a circuit-switched domain (MSC) and a packet-switched domain (SGSN, GGSN)
- Adds **mutual authentication**, fixing GSM's false base station weakness
- **HSPA** later raises rates to several Mbps by adding higher-order modulation, fast scheduling and HARQ

### LTE / 4G — the architectural break

```
  +-----+   +--------+        EVOLVED PACKET CORE (EPC)
  | UE  |---| eNodeB |----+--- [MME]  Mobility Management Entity
  +-----+   +--------+    |          signalling only, no user data
             (no separate |
              controller) +--- [S-GW] Serving Gateway
                          |          user data anchor during handover
                          +--- [P-GW] PDN Gateway
                          |          gateway to the internet, IP allocation
                          +--- [HSS]  Home Subscriber Server
                                     the evolved HLR + AuC
```

**Four things that changed fundamentally:**
1. **All-IP.** There is no circuit-switched domain at all. Voice is carried as **VoLTE**, which is SIP-based VoIP (Topic 7) over a dedicated bearer with guaranteed quality
2. **Flat architecture.** The RNC/BSC layer is removed. The eNodeB talks directly to the core and to neighbouring eNodeBs over the X2 interface, which reduces latency
3. **Control and user plane separated.** MME handles signalling only, gateways handle user data
4. **OFDMA down, SC-FDMA up** (Topic 5), with adaptive modulation up to 256-QAM (Topic 4)

---

## 8.9 Generation comparison table (very high exam value)

| | 1G | 2G GSM | 2.5G GPRS | 3G UMTS | 4G LTE |
|---|---|---|---|---|---|
| Signal | Analog | Digital | Digital | Digital | Digital |
| Switching | Circuit | Circuit | Circuit + packet | Circuit + packet | **All packet** |
| Access | FDMA | FDMA + TDMA | FDMA + TDMA | WCDMA | OFDMA / SC-FDMA |
| Modulation | FM | GMSK | GMSK | QPSK, 16-QAM | QPSK to 256-QAM |
| Carrier | 30 kHz | 200 kHz | 200 kHz | 5 MHz | 1.4 to 20 MHz |
| Peak rate | Voice only | 9.6 kbps | ~171 kbps | 2 Mbps, HSPA higher | 100 Mbps to 1 Gbps |
| Base station | BTS | BTS | BTS | Node B | eNodeB |
| Controller | - | BSC | BSC | RNC | **None** |
| Core | - | MSC, HLR, VLR | + SGSN, GGSN | + SGSN, GGSN | MME, S-GW, P-GW, HSS |
| Handover | Hard | Hard | Hard | **Soft** | Hard |
| Authentication | None | One-way | One-way | **Mutual** | Mutual |
| Voice | Analog | Circuit | Circuit | Circuit | **VoLTE (packet)** |

---

## 8.10 Formula sheet for Topic 8

```
N = i² + ij + j²                cluster size, valid values 1,3,4,7,9,12,...
Channels per cell = S / N
System capacity = M x S          M = number of clusters
Q = D/R = sqrt(3N)               co-channel reuse ratio
D = R sqrt(3N)                   reuse distance
S/I = Q^n / i0                   i0 = co-channel interferers, n = path exponent
Traffic channels (GSM) = carriers x 8
GSM duplex spacing (900) = 45 MHz
```

---

## 8.11 Traps that cost marks

1. **Hexagons are a planning model, not physical coverage.** Say so if asked why they are used.
2. **Small cluster size gives more capacity but worse interference.** Do not state only half of the trade-off.
3. **Soft handoff requires CDMA.** GSM cannot do it because neighbouring cells use different frequencies.
4. **The HLR is permanent, the VLR is temporary.** This distinction is asked almost every year.
5. **The SIM holds identity, the handset holds the IMEI.** Blacklisting a stolen phone uses the EIR and the IMEI, not the SIM.
6. **GSM authentication is one-way**, which is the basis of the IMSI catcher attack. UMTS made it mutual.
7. **GPRS did not add spectrum.** It added packet switching on the existing radio channels.
8. **LTE has no circuit-switched domain.** Voice is VoLTE, which is packetised. Writing "LTE uses the MSC" is wrong.
9. **Sectoring reduces interferers from 6 to 2**, it does not increase the number of channels.

---

## 8.12 Glossary

| Term | One-line definition |
|---|---|
| Cell | The coverage area of one base station |
| Cluster | A group of N cells using the full set of channels once |
| Frequency reuse | Using the same frequencies again in cells sufficiently far apart |
| Cluster size N | Number of cells in a repeating pattern |
| Reuse distance D | Distance between co-channel cell centres |
| Co-channel interference | Interference from another cell using the same frequency |
| Cell splitting | Subdividing a cell into smaller cells to raise capacity |
| Sectoring | Using directional antennas to divide a cell into sectors |
| Handoff | Transfer of an active call from one cell to another |
| Hard handoff | Break before make |
| Soft handoff | Make before break, possible only in CDMA |
| Ping-pong effect | Repeated handovers between two cells at a boundary |
| MAHO | Mobile assisted handover, using handset measurements |
| MS | Mobile station, comprising the handset and the SIM |
| BTS | Base transceiver station, the radio part of a cell |
| BSC | Base station controller, managing several BTSs |
| MSC | Mobile switching centre, the circuit switch |
| HLR | Permanent subscriber database |
| VLR | Temporary database of subscribers currently in an MSC area |
| AuC | Authentication centre holding the secret keys |
| EIR | Equipment identity register, used to blacklist stolen handsets |
| IMSI | Permanent subscriber identity held on the SIM |
| MSISDN | The dialled telephone number |
| IMEI | Handset hardware identity |
| TMSI | Temporary identity used over the air for privacy |
| Location area | A group of cells within which a handset is paged |
| SGSN / GGSN | GPRS serving and gateway support nodes |
| Node B / RNC | The UMTS base station and radio network controller |
| eNodeB | The LTE base station, with no separate controller |
| EPC | Evolved Packet Core, the all-IP LTE core |
| MME | Mobility management entity, LTE control plane |
| S-GW / P-GW | Serving and packet data network gateways |
| HSS | Home subscriber server, the evolved HLR |
| VoLTE | Voice over LTE, SIP-based voice on a guaranteed bearer |

---

# Objective questions (Topic 8)

**Q1.** Hexagons are used to model cells because they:
A. Are the true shape of radio coverage
B. Tile the plane without gaps and approximate a circle most closely
C. Require the fewest antennas
D. Reduce interference physically

**Q2.** Which of the following is NOT a valid cluster size?
A. 4  B. 7  C. 8  D. 12

**Q3.** A system with 420 channels and a cluster size of 7 provides how many channels per cell?
A. 42  B. 60  C. 70  D. 420

**Q4.** For a cell radius of 3 km and cluster size 12, the reuse distance is approximately:
A. 6 km  B. 12 km  C. 18 km  D. 36 km

**Q5.** Reducing the cluster size N:
A. Increases capacity but worsens co-channel interference
B. Increases capacity and improves interference
C. Reduces capacity and worsens interference
D. Has no effect on capacity

**Q6.** Sectoring a cell with 120-degree antennas reduces the number of co-channel interferers from:
A. 6 to 2  B. 6 to 3  C. 3 to 1  D. 12 to 6

**Q7.** Soft handoff is possible only in:
A. GSM  B. CDMA-based systems  C. FDMA systems  D. LTE

**Q8.** "Break before make" describes:
A. Soft handoff  B. Hard handoff  C. Softer handoff  D. Intra-cell handoff

**Q9.** The repeated switching of a call between two cells at a boundary is called:
A. Soft handoff  B. The ping-pong effect  C. Cell splitting  D. Paging

**Q10.** In GSM, each 200 kHz carrier supports how many traffic time slots?
A. 4  B. 8  C. 16  D. 32

**Q11.** The GSM 900 uplink band is:
A. 890 to 915 MHz  B. 935 to 960 MHz  C. 1710 to 1785 MHz  D. 824 to 849 MHz

**Q12.** The database holding permanent subscriber information is the:
A. VLR  B. HLR  C. EIR  D. AuC

**Q13.** A stolen handset is blocked using its IMEI, which is checked against the:
A. HLR  B. VLR  C. EIR  D. AuC

**Q14.** The temporary identity used over the air to protect subscriber privacy is the:
A. IMSI  B. IMEI  C. TMSI  D. MSISDN

**Q15.** GSM authentication is described as one-way because:
A. Only the network authenticates the subscriber, not the reverse
B. Only the subscriber authenticates the network
C. No authentication occurs
D. Authentication happens once per year

**Q16.** The two core nodes introduced by GPRS are:
A. MSC and HLR  B. SGSN and GGSN  C. MME and S-GW  D. Node B and RNC

**Q17.** EDGE increased data rates over GPRS mainly by:
A. Adding spectrum  B. Using 8-PSK instead of GMSK  C. Adding more time slots  D. Using CDMA

**Q18.** In UMTS, the BSC is replaced by the:
A. MME  B. RNC  C. eNodeB  D. GGSN

**Q19.** A distinguishing architectural feature of LTE is that:
A. It retains the circuit-switched domain
B. It is all-IP with no separate radio network controller
C. It uses CDMA
D. It has no core network

**Q20.** In the LTE core, the node handling control-plane signalling only is the:
A. S-GW  B. P-GW  C. MME  D. HSS

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Of the tiling polygons, the hexagon is closest to a circle |
| 2 | **C** | 8 cannot be written as i² + ij + j². Valid: 1,3,4,7,9,12,13,19 |
| 3 | **B** | 420 / 7 = 60 |
| 4 | **C** | D = 3 x sqrt(36) = 3 x 6 = 18 km |
| 5 | **A** | More channels per cell but co-channel cells are closer |
| 6 | **A** | Directional antennas eliminate four of the six interferers |
| 7 | **B** | All cells share one frequency, so one receiver can hear two cells |
| 8 | **B** | The old link is released first, causing a brief gap |
| 9 | **B** | Hysteresis is applied to suppress it |
| 10 | **B** | GSM uses 8-slot TDMA per carrier |
| 11 | **A** | Uplink 890 to 915, downlink 935 to 960, 45 MHz spacing |
| 12 | **B** | The HLR is permanent, the VLR is temporary |
| 13 | **C** | The equipment identity register holds the IMEI blacklist |
| 14 | **C** | TMSI is assigned by the VLR to avoid sending the IMSI over the air |
| 15 | **A** | The subscriber cannot verify the network, enabling IMSI catchers |
| 16 | **B** | Serving and gateway GPRS support nodes |
| 17 | **B** | Three bits per symbol instead of one, with no new spectrum |
| 18 | **B** | Radio network controller |
| 19 | **B** | Flat all-IP architecture, eNodeB connects directly to the EPC |
| 20 | **C** | The mobility management entity carries no user data |

---

# Exam-style written questions with model answers

**W1. Explain the cellular concept and the principle of frequency reuse. A network is allocated 490 channels and uses a cluster size of 7 with a cell radius of 2 km. Calculate the channels per cell and the co-channel reuse distance, and discuss what would change if the cluster size were reduced to 4. (10 marks)**

*Model answer:* The cellular concept replaces a single high-power transmitter covering a large area with a large number of low-power transmitters, each serving a small area called a cell. Because each transmitter has limited range, the same set of frequencies can be reused in another cell sufficiently far away that the mutual co-channel interference is acceptable. The available channels are divided among the N cells of a cluster, and the cluster pattern is then repeated across the whole service area, so total system capacity becomes the number of clusters multiplied by the total channel count rather than being fixed at the channel count. **Channels per cell** = 490 / 7 = **70**. The co-channel reuse ratio Q = sqrt(3N) = sqrt(21) = 4.583, so the **reuse distance** D = QR = 4.583 x 2 = **9.17 km**. If the cluster size were reduced to 4, channels per cell would rise to 490 / 4 = 122.5, so roughly 122 channels per cell, a substantial capacity increase because each cell can now carry almost twice as many simultaneous calls and the pattern repeats more often across the area. However Q would fall to sqrt(12) = 3.464 and the reuse distance would fall to 6.93 km, so co-channel cells would be considerably closer together and the signal-to-interference ratio would deteriorate. Since S/I is proportional to Q raised to the path loss exponent, typically between 3 and 4, this reduction would lower S/I by several decibels and could push call quality below the acceptable threshold. The choice of cluster size is therefore a direct trade-off between capacity and quality, and an operator would normally compensate for a smaller N by introducing sectoring, which reduces the number of co-channel interferers from six to two and recovers roughly 5 dB of S/I.

**W2. With the aid of a diagram, describe the GSM network architecture, explaining the function of each major element. (10 marks)**

*Model answer:* Draw the three subsystems with the Um, Abis and A interfaces labelled. The **Mobile Station** consists of the **Mobile Equipment**, the physical handset identified by its IMEI, and the **Subscriber Identity Module**, a smart card holding the IMSI and the secret key Ki. Separating the subscriber's identity onto a removable card was a defining GSM innovation, since it allows a subscriber to change handsets while retaining their identity and number. The **Base Station Subsystem** comprises the **Base Transceiver Station**, which contains the radio transceivers and antennas of one cell and performs modulation, demodulation and air-interface encryption, and the **Base Station Controller**, which manages a group of BTSs and is responsible for radio channel allocation, power control, frequency hopping and handovers between the cells it controls. The **Network Switching Subsystem** is built around the **Mobile Switching Centre**, a circuit switch that establishes and releases calls, performs handovers between different BSCs, and generates billing records; the **Gateway MSC** is the MSC that interconnects with the PSTN and other operators. Supporting the MSC are four databases. The **Home Location Register** permanently stores every subscriber's IMSI, MSISDN, subscribed services and a pointer to the VLR currently serving them. The **Visitor Location Register** temporarily stores the details of all subscribers presently within its MSC's area, including inbound roamers, so that routine operations do not require a query to the HLR. The **Authentication Centre** securely holds the key Ki for every SIM and generates the challenge, response and ciphering key used for authentication. The **Equipment Identity Register** holds lists of IMEIs classified as valid, suspect or barred, allowing stolen handsets to be blocked from the network. Finally the **Operations and Maintenance Centre** provides network monitoring, configuration and fault management. All signalling between the MSC, HLR, VLR and AuC uses the Mobile Application Part carried over the SS7 signalling network.

**W3. Distinguish between the HLR and the VLR, and describe the sequence of events when a call is made to a subscriber who is roaming. (8 marks)**

*Model answer:* The **Home Location Register** is a permanent, centralised database maintained by the subscriber's home operator. It holds one record per subscription containing the IMSI, the MSISDN, the services the subscriber has subscribed to, and a pointer indicating which VLR is currently serving that subscriber. The record persists for as long as the subscription exists. The **Visitor Location Register** is a temporary database associated with a particular MSC. It holds records only for those subscribers currently located within that MSC's service area, including subscribers from other networks who are roaming, and each record is deleted once the subscriber moves away. Its purpose is to hold a local copy of the information needed to serve the subscriber so that the MSC does not have to query the distant HLR for every operation, which reduces signalling load and delay. When a call is made to a roaming subscriber, the call is first routed on the basis of the dialled MSISDN to the **Gateway MSC of the subscriber's home network**, because the number belongs to that network. The GMSC then queries the **HLR**, which looks up its pointer and identifies the VLR currently serving the subscriber. The HLR sends a request to that **VLR**, which allocates a temporary **Mobile Station Roaming Number** identifying the serving MSC and returns it to the HLR, which relays it to the GMSC. The GMSC now routes the call using the roaming number to the **serving MSC**, which consults its VLR to determine the subscriber's current location area and issues a **paging** message across all cells in that location area. The handset detects the page, responds, is authenticated, is assigned a traffic channel, and finally rings. This mechanism is why calls to a roaming subscriber are routed to the home country first and then onward, which is the source of the traditional cost of international roaming.

**W4. Explain what handoff is, distinguish between hard and soft handoff, and explain why soft handoff is not possible in GSM. (7 marks)**

*Model answer:* Handoff, or handover, is the process by which an active call or data session is transferred from one cell to another as the subscriber moves, so that the connection is maintained without interruption as the signal from the serving cell weakens and that from a neighbouring cell strengthens. The network monitors signal strength, in GSM assisted by measurements reported by the handset itself during its idle time slots, and initiates the transfer once the serving signal falls below a threshold which is deliberately set above the minimum usable level so that there is time to complete the procedure before the call would drop. Hysteresis is applied so that the handset does not oscillate repeatedly between two cells at the boundary. In a **hard handoff**, described as break before make, the connection to the original cell is released before the connection to the new cell is established, so there is a brief interruption of a few tens of milliseconds. In a **soft handoff**, described as make before break, the handset establishes a connection with the new base station while still connected to the old one, and for a period communicates with both simultaneously; the network combines the two received signals, giving macro diversity, and the old link is released only once the new one is firmly established, so there is no interruption at all and reliability at the cell edge is improved. Soft handoff is **not possible in GSM** because GSM assigns different carrier frequencies to adjacent cells in accordance with its frequency reuse plan, so a handset would require two complete receiver chains tuned to two different frequencies in order to communicate with both cells at once, which was not economically feasible in handset design. In CDMA systems such as UMTS, every cell transmits on the same carrier frequency and cells are distinguished only by their scrambling codes, so a single receiver can correlate against two codes simultaneously and soft handoff comes naturally.

**W5. Trace the evolution from GSM through GPRS and EDGE to UMTS and LTE, identifying the principal technical change at each step and what problem it solved. (10 marks)**

*Model answer:* **GSM** established digital cellular telephony using a combination of FDMA and TDMA, with 200 kHz carriers each divided into eight time slots, GMSK modulation chosen for its constant envelope and consequent amplifier efficiency, and a wholly circuit-switched core built around the MSC and the HLR and VLR databases. It solved the capacity and quality limitations of analog 1G and introduced the SIM card, which separated subscriber identity from handset. Its limitation was that data had to occupy a dedicated circuit at 9.6 kbps, which was slow and billed by time. **GPRS** solved this by overlaying a packet-switched domain on the same radio network, introducing the SGSN for packet mobility and session management and the GGSN as the gateway to external IP networks. Radio resources were now allocated only when data was actually transferred and a user could be given several time slots at once, enabling always-on connectivity and volume-based billing at rates around 40 to 50 kbps in practice. **EDGE** raised throughput further by replacing GMSK with 8-PSK modulation, tripling the number of bits carried by each symbol and lifting peak rates to roughly 384 kbps. Because it required only transceiver and software upgrades rather than new spectrum, it was an inexpensive improvement and was widely deployed across Africa. **UMTS** changed the air interface fundamentally to WCDMA on 5 MHz carriers, replacing the BTS with the Node B and the BSC with the Radio Network Controller. This delivered several megabits per second, particularly once HSPA added higher-order modulation, fast scheduling and hybrid ARQ, and it introduced soft handover through the common-frequency CDMA structure and, importantly, **mutual authentication**, which closed the false base station vulnerability inherent in GSM's one-way authentication. **LTE** made the most radical architectural break. The air interface moved to OFDMA on the downlink and SC-FDMA on the uplink, giving strong multipath resistance and flexible bandwidth from 1.4 to 20 MHz with adaptive modulation up to 256-QAM. The radio network controller was eliminated entirely, leaving a flat architecture in which the eNodeB connects directly to the core and to its neighbours, substantially reducing latency. The core became the all-IP Evolved Packet Core, with the MME handling control-plane signalling, the Serving and PDN Gateways handling user data, and the HSS replacing the HLR and AuC. There is no circuit-switched domain at all, so voice is carried as VoLTE, a SIP-based VoIP service running over a dedicated bearer with guaranteed quality of service. The result is peak rates of hundreds of megabits per second and latency low enough for interactive applications.

---

## Checkpoint before Topic 9 (4G/5G and Beyond)

You should be able to, without notes:
1. Explain the cellular concept and why hexagons are used
2. Compute channels per cell, Q and D from N, S and R
3. State the capacity versus interference trade-off in one sentence
4. Draw the full GSM architecture with all interfaces labelled
5. Distinguish HLR from VLR and IMSI from IMEI from TMSI
6. Explain why soft handoff needs CDMA
7. Fill in the 1G to 4G comparison table from memory
8. Explain GSM's one-way authentication weakness and how UMTS fixed it
