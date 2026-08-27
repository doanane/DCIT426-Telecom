# DCIT 426 Telecommunications
## Topic 10: Optical and Broadband Access Networks

**Syllabus line (Week 11):** *DWDM, PON/FTTx, DSL, cable, fixed wireless; backbone and last-mile design.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| DWDM and optical transport | Stallings Ch. 8.3; Forouzan Ch. 6 |
| DSL family | Forouzan Ch. 14, Section 14.1; Stallings Ch. 8 |
| Cable and DOCSIS | Forouzan Ch. 14, Section 14.2 |
| PON and FTTx | ITU-T G.984 (GPON) and G.9807 (XGS-PON) |

**The organising idea for this whole topic:** a network has a **backbone** (long-haul, high capacity, shared by everyone) and an **access network** or **last mile** (from the nearest network point to the individual premises). Backbone capacity is comparatively easy and cheap per bit. **The last mile is where the cost, the difficulty and the policy problems live.** Your outline explicitly lists "rural broadband / last-mile design" as a project theme, so treat this topic as the core of that.

---

# PART A: THE OPTICAL BACKBONE

## 10.1 DWDM in the backbone

You met WDM in Topic 5. Here is what makes it work over thousands of kilometres.

```
   +------+   +------+                              +------+   +------+
   |TXλ1  |-->|      |    +-----+       +-----+     |      |-->|RXλ1  |
   |TXλ2  |-->| MUX  |===>|EDFA |=====>|EDFA |====>| DEMUX|-->|RXλ2  |
   |TXλ80 |-->|      |    +-----+       +-----+     |      |-->|RXλ80 |
   +------+   +------+   optical amplifiers,        +------+   +------+
                          NO conversion to
                          electrical form
```

| Component | Function |
|---|---|
| **DWDM mux/demux** | Combines and separates wavelengths on the 100 GHz or 50 GHz ITU grid, in the 1550 nm window |
| **EDFA** Erbium-Doped Fibre Amplifier | Amplifies **all wavelengths at once, optically**, without converting to electrical form. Typically spaced 80 to 100 km apart |
| **ROADM** Reconfigurable Optical Add-Drop Multiplexer | Adds or drops selected wavelengths at a node under software control, leaving the others untouched |
| **Coherent transceiver** | Uses phase and amplitude detection with digital signal processing, enabling 100G, 400G and beyond per wavelength and compensating dispersion electronically |

**Why the EDFA changed everything:** before it, every regenerator had to convert light to electricity, regenerate the bits, and convert back, once per wavelength. That is expensive and it limits the link to whatever bit rate the electronics support. An EDFA amplifies eighty wavelengths simultaneously in the optical domain and is transparent to bit rate and format, so a cable's capacity can be upgraded by replacing only the terminal equipment at each end.

**Capacity calculation.** A fibre pair carrying 80 wavelengths at 400 Gbps each delivers 80 x 400 = **32 Tbps**. A submarine cable with four fibre pairs carries 128 Tbps.

> **Ghana relevance:** this is exactly why the SAT-3/WASC cable, which landed at Accra in 2001, has been upgraded repeatedly in capacity without any new cable being laid. The glass in the ground did not change. The transceivers at Accra and at the far end did. This point, made precisely, is worth marks in any question about Ghana's international capacity.

## 10.2 OTN and the transport stack

**OTN (Optical Transport Network, ITU-T G.709)** is the digital wrapper carried on each wavelength. It provides forward error correction, performance monitoring and the ability to carry any client signal (Ethernet, SDH, Fibre Channel) transparently. It is often described as the "digital wrapper" that replaced SDH for high-rate transport.

```
   IP / MPLS          <- routing and services
   Ethernet           <- client signal
   OTN                <- digital wrapper, FEC, monitoring
   DWDM wavelength    <- optical channel
   Fibre              <- the glass
```

---

# PART B: THE ACCESS NETWORK

## 10.3 The FTTx taxonomy

**Short answer:** FTTx describes how far the fibre is pushed towards the customer before some other medium takes over.

```
  EXCHANGE                                              PREMISES
     |=========fibre=========|-copper-|                  FTTC  (to the cabinet)
     |=============fibre============|-copper-|           FTTN  (to the node)
     |==================fibre==================|         FTTB  (to the building)
     |====================fibre===================|      FTTH  (to the home)
                                          the shorter the copper,
                                          the higher the achievable rate
```

| Acronym | Fibre reaches | Final drop | Typical rate |
|---|---|---|---|
| **FTTN** Fibre To The Node | A street cabinet, 1 to 3 km away | Copper (VDSL) | 25 to 50 Mbps |
| **FTTC** Fibre To The Cabinet/Curb | A cabinet within 300 m | Copper (VDSL2, G.fast) | 100 Mbps to 1 Gbps |
| **FTTB** Fibre To The Building | The building basement | In-building copper or Ethernet | High |
| **FTTH** Fibre To The Home | Inside the premises | None | 1 Gbps and above |

**The rule of thumb that explains the whole family:** copper's achievable rate falls steeply with length because attenuation rises with both distance and frequency. Push the fibre closer and the copper gets shorter, so the rate rises.

---

## 10.4 DSL: broadband over the existing telephone line

**Short answer:** DSL exploits the fact that the local loop copper pair can carry frequencies far above the 4 kHz used for voice, so data can be sent alongside a telephone call on the same wire.

```
   FREQUENCY USE ON THE COPPER PAIR (ADSL)

   0     4 kHz    25 kHz        138 kHz              1104 kHz
   |------|---------|--------------|-------------------|
   | VOICE| guard   |   UPSTREAM   |    DOWNSTREAM     |
   |      |         |              |                   |
   the splitter at each end separates voice from data

   ASYMMETRIC: far more spectrum is given to downstream,
   because users download much more than they upload.
```

DSL uses **DMT (Discrete Multi-Tone)**, which is the wireline form of OFDM from Topic 5. The band is divided into 4.3125 kHz sub-channels, each of which measures its own signal-to-noise ratio and loads as many bits as it can support, up to 15 bits per tone. **Bit loading is Shannon's theorem applied per sub-channel**, and it is why DSL rate falls smoothly with line length rather than failing abruptly.

### The DSL family

| Variant | Downstream | Upstream | Max useful reach |
|---|---|---|---|
| ADSL (G.992.1) | 8 Mbps | 1 Mbps | about 5 km |
| ADSL2+ | 24 Mbps | 1 Mbps | about 2.5 km for full rate |
| VDSL2 | 100 Mbps | 50 Mbps | about 300 m to 1 km |
| **G.fast** | up to 1 Gbps aggregate | shared | **under 250 m** |
| SDSL / SHDSL | Symmetric, up to 2 Mbps | Symmetric | Business lines |

```
   RATE versus LOOP LENGTH

  rate ^
       |\
       | \   VDSL2 collapses quickly
       |  \
       |   \___
       |       \____  ADSL2+ degrades more gently
       |            \_____
       +----------------------------> loop length (km)
       0    0.5   1    2    3    4    5

   The physics: attenuation increases with both frequency and
   distance, so the high-frequency tones that carry the most
   bits are the first to become unusable.
```

**Impairments specific to DSL:** **crosstalk** between pairs in the same binder is the dominant limit in dense deployments, and **bridged taps** (unterminated stubs of old wiring) cause reflections. **Vectoring** in VDSL2 measures and cancels crosstalk between pairs, which can roughly double the achievable rate.

**Why DSL matters less in Ghana than elsewhere:** DSL depends on a pre-existing copper local loop reaching every home. Ghana's fixed-line penetration was always low, and copper theft and deterioration have reduced it further. Countries with dense legacy copper (the UK, Germany) got a decade of value from DSL. Ghana largely **skipped it and went straight to mobile broadband**, which is a genuine case of leapfrogging and a strong point for an essay.

---

## 10.5 Cable broadband (HFC and DOCSIS)

**HFC (Hybrid Fibre-Coax):** fibre runs from the head end to an optical node serving a neighbourhood, and coaxial cable runs from that node to homes, shared among perhaps 100 to 500 premises.

```
  [HEAD END] ===fibre===> [OPTICAL NODE] --coax--+--[home]
   CMTS                                          +--[home]
                                                 +--[home]
                          the coax segment is SHARED,
                          so throughput per home falls
                          as neighbours use the service
```

| Standard | Downstream | Upstream |
|---|---|---|
| DOCSIS 3.0 | Up to about 1 Gbps with channel bonding | Up to about 200 Mbps |
| DOCSIS 3.1 | Up to 10 Gbps, using OFDM and 4096-QAM | Up to 1 to 2 Gbps |

**Key characteristic to state in comparisons:** cable is a **shared medium**. Advertised speeds are peak, not guaranteed, and performance degrades in the evening peak. DSL and PON differ here: DSL is dedicated copper per subscriber, and PON is shared but with a scheduler that can enforce guarantees.

Cable broadband is significant in North America and parts of Europe but has almost no presence in Ghana, since cable television infrastructure was never built out at scale.

---

## 10.6 PON: the dominant modern access technology

**Short answer:** a Passive Optical Network shares one fibre from the exchange among many subscribers using purely passive optical splitters, with no powered equipment between the exchange and the home.

```
                             PASSIVE SPLITTER
                             (no power, no
                              electronics)
                                   |
   [OLT]======one feeder fibre=====[/|\]====drop fibres====[ONT] home 1
    at the                          /|\                    [ONT] home 2
    exchange                       / | \                   [ONT] home 3
                                  ...                       ...
                                                           [ONT] home 32
```

| Element | Full name | Role |
|---|---|---|
| **OLT** | Optical Line Terminal | At the exchange. Serves many PONs, schedules all upstream transmission |
| **Splitter** | Passive optical splitter | Divides the optical signal, typically 1:32 or 1:64. **Needs no power at all** |
| **ONT / ONU** | Optical Network Terminal / Unit | At the customer premises. Terminates the fibre and provides Ethernet, Wi-Fi and voice ports |

### How the two directions differ (this is the standard exam question)

```
   DOWNSTREAM: BROADCAST
   The OLT transmits everything to everyone. Each ONT
   discards frames not addressed to it. Encryption (AES)
   prevents a subscriber reading a neighbour's traffic.

   UPSTREAM: TDMA
   All ONTs share one fibre and one wavelength, so they
   would collide. The OLT grants each ONT a precise time
   window. RANGING measures each ONT's distance so the
   OLT can apply an equalisation delay and the bursts
   arrive back to back without overlap.
```

**The two directions also use different wavelengths**, which is WDM within the PON: typically 1490 nm downstream and 1310 nm upstream, with 1550 nm reserved for overlaying video.

### PON standards

| Standard | Downstream | Upstream | Notes |
|---|---|---|---|
| **GPON** (G.984) | 2.488 Gbps | 1.244 Gbps | The volume-deployed standard |
| **XG-PON** | 10 Gbps | 2.5 Gbps | Asymmetric 10G |
| **XGS-PON** | 10 Gbps | 10 Gbps | Symmetric, the current build choice |
| **NG-PON2** | 40 Gbps | 10 Gbps | Uses four stacked wavelengths (TWDM) |

**Worked bandwidth example.** A GPON with a 1:32 split.
- Downstream 2.488 Gbps / 32 = **77.75 Mbps average per subscriber** if all transmit simultaneously
- But because usage is bursty and statistically multiplexed, an individual subscriber routinely achieves several hundred megabits when neighbours are idle
- With a 1:64 split, the average halves to about 39 Mbps, which is why split ratio is a direct cost-versus-performance decision

### Active versus passive optical networks

| | PON (passive) | AON (active / point-to-point) |
|---|---|---|
| Between exchange and home | Passive splitter only | Powered Ethernet switch |
| Fibre count from exchange | One feeder per 32 or 64 homes | One fibre per home |
| Power in the field | **None** | Required, with backup |
| Bandwidth per subscriber | Shared | Dedicated |
| Cost | Lower | Higher |
| Maintenance | Very low, nothing to fail in the field | Field equipment to power, cool and maintain |

**Why PON dominates, and why it matters especially in Ghana:** the passive splitter needs no electricity. In an environment where grid power is unreliable and generator or battery backup at every street cabinet would be a major operating cost and a theft target, having nothing powered between the exchange and the home is a decisive advantage.

---

## 10.7 Fixed Wireless Access

**Short answer:** FWA delivers broadband to a fixed premises over a radio link instead of a cable, avoiding the cost of trenching the last mile.

| Technology | Notes |
|---|---|
| Proprietary point-to-multipoint | Long-standing in African markets, various bands |
| WiMAX (802.16) | Largely superseded |
| **LTE and 5G FWA** | The dominant modern form. Uses the mobile network with a fixed outdoor or window-mounted CPE |
| mmWave FWA | Very high capacity, very short range, needs line of sight |

**Advantages:** deployment in days rather than months, no wayleaves or trenching, cost scales with subscribers taken rather than premises passed, and it reaches areas where trenching is impossible.

**Disadvantages:** capacity is shared with mobile users on the same spectrum, performance depends on line of sight and weather, and spectrum is finite so it does not scale to very high penetration in dense areas.

> **This is the technology that actually serves most of Ghana's broadband demand.** Any last-mile design answer that ignores it is incomplete.

---

## 10.8 Comparing the access technologies

| | DSL | Cable (HFC) | PON | FWA |
|---|---|---|---|---|
| Medium | Existing copper pair | Coax from an optical node | Fibre plus passive splitter | Radio |
| Dedicated or shared | Dedicated copper | **Shared coax** | Shared fibre with scheduling | **Shared spectrum** |
| Typical downstream | 8 to 100 Mbps | 100 Mbps to 1 Gbps | 100 Mbps to 1 Gbps+ | 10 to 100 Mbps |
| Distance sensitivity | **Severe** | Moderate | Up to 20 km, minimal | Depends on line of sight |
| Deployment cost | Very low if copper exists | High if coax must be built | **High capex, low opex** | **Low capex, fast** |
| Power in the field | Yes at cabinets | Yes at nodes and amplifiers | **None** | Yes at the tower only |
| Upgrade path | Limited by physics | DOCSIS 3.1 and beyond | Change transceivers only | New spectrum or generations |
| Fit for Ghana | Poor, little copper exists | Negligible, no cable plant | Best for dense urban and enterprise | Best for rapid and rural coverage |

---

## 10.9 Designing a last mile: the actual reasoning

This is the shape of the answer for the project theme and for a design question.

```
STEP 1: Characterise the demand
   How many premises? What take-up rate? What rate per premises?
   Residential or business? Willingness to pay?

STEP 2: Characterise the terrain and existing assets
   Distance to the nearest fibre point of presence.
   Is there a road, rail or power corridor to follow?
   Line of sight available? Existing towers? Grid power?

STEP 3: Shortlist technologies against those constraints
   Dense urban with high take-up  -> GPON or XGS-PON
   Scattered rural, low take-up   -> FWA on a low band
   Single large site (hospital, campus) -> dedicated fibre
   Very remote, no line of sight  -> satellite (Topic 11)

STEP 4: Cost it as capex versus opex, and per premises PASSED
        versus per premises CONNECTED
   Fibre: high cost to pass every premises, low cost per connection after
   FWA:  low cost to cover, higher cost per subscriber CPE

STEP 5: Check backhaul and power
   The access technology is useless without capacity behind it
   and without reliable power at the site

STEP 6: State a staged plan
   Deploy FWA now for revenue and coverage,
   overbuild with fibre where density justifies it later
```

**The economic concept to name explicitly:** in fibre, most of the cost is incurred **passing** premises, whether or not anyone subscribes. In wireless, most of the cost is incurred **connecting** a subscriber. This is why fibre suits high-density, high-take-up areas and wireless suits sparse, uncertain ones.

---

## 10.10 Formula sheet for Topic 10

```
DWDM capacity = wavelengths x rate per wavelength
PON average per-subscriber rate = line rate / split ratio
Bit loading per DMT tone: from Shannon on that tone's SNR
Fibre attenuation budget = TX power - RX sensitivity
Optical loss = fibre loss (dB/km x km) + splitter loss + connector/splice loss
Splitter loss ≈ 10 log10(split ratio) plus excess loss
   1:32 splitter ≈ 15 to 17 dB
```

**Worked optical power budget.** A GPON with an OLT transmit power of +3 dBm and an ONT sensitivity of -28 dBm.
- Available budget = 3 - (-28) = **31 dB**
- 1:32 splitter loss ≈ 17 dB
- Connectors and splices ≈ 2 dB
- Remaining for fibre = 31 - 17 - 2 = 12 dB
- At 0.35 dB/km (1310 nm), maximum reach = 12 / 0.35 = **about 34 km**, comfortably above the 20 km GPON specification

---

## 10.11 Traps that cost marks

1. **PON downstream is broadcast, upstream is TDMA.** Both halves are needed for full marks.
2. **The splitter is passive.** That is the entire point of the name. Do not describe it as a switch.
3. **DSL rate falls with loop length because attenuation rises with frequency**, and it is the high tones that carry the most bits. Give the mechanism, not just the fact.
4. **Cable is shared at the coax segment**, which is why evening speeds drop.
5. **FTTC and FTTH are not the same.** FTTC still ends in copper.
6. **An EDFA amplifies all wavelengths optically.** It is not a regenerator and does not touch the bits.
7. **DWDM upgrades change the terminal equipment, not the fibre.** This is the key point in any submarine cable question.
8. **Fibre cost is dominated by premises passed, wireless by premises connected.** Name the distinction.

---

## 10.12 Glossary

| Term | One-line definition |
|---|---|
| Backbone | The high-capacity core carrying aggregated traffic |
| Last mile / access network | The link from the network edge to individual premises |
| DWDM | Dense wavelength division multiplexing on the ITU grid |
| EDFA | Erbium-doped fibre amplifier, amplifying all wavelengths optically |
| ROADM | Reconfigurable optical add-drop multiplexer |
| Coherent optics | Detection using amplitude and phase with DSP, enabling 100G and above |
| OTN | Optical transport network, the digital wrapper with FEC and monitoring |
| FTTx | The family of fibre-to-the-node, cabinet, building and home architectures |
| DSL | Digital subscriber line, broadband over the existing copper pair |
| DMT | Discrete multi-tone, the OFDM variant used by DSL |
| Bit loading | Assigning bits per tone according to that tone's measured SNR |
| Vectoring | Crosstalk cancellation between pairs in a VDSL2 binder |
| Splitter (DSL) | A filter separating voice from data on the same pair |
| HFC | Hybrid fibre-coax, fibre to a node and coax to homes |
| DOCSIS | The cable modem standard |
| CMTS | Cable modem termination system at the head end |
| PON | Passive optical network |
| OLT | Optical line terminal at the exchange |
| ONT / ONU | Optical network terminal at the customer premises |
| Split ratio | The number of subscribers sharing one PON feeder fibre |
| Ranging | Measuring ONT distance so upstream bursts do not collide |
| GPON / XGS-PON | The 2.5G and symmetric 10G passive optical network standards |
| AON | Active optical network, with powered switches in the field |
| FWA | Fixed wireless access, broadband to a fixed premises over radio |
| CPE | Customer premises equipment |
| Premises passed | Premises the network is capable of connecting, whether or not they subscribe |
| Optical power budget | Available loss between transmitter power and receiver sensitivity |

---

# Objective questions (Topic 10)

**Q1.** An EDFA differs from a regenerator in that it:
A. Converts light to electricity and back
B. Amplifies all wavelengths optically without electrical conversion
C. Adds error correction
D. Only works at 1310 nm

**Q2.** A DWDM system with 80 wavelengths at 200 Gbps each carries:
A. 200 Gbps  B. 1.6 Tbps  C. 16 Tbps  D. 160 Tbps

**Q3.** A submarine cable's capacity can be upgraded without laying new cable by:
A. Increasing the laser power only
B. Replacing the terminal transceivers and multiplexers
C. Adding more fibre to the existing cable
D. Reducing the number of wavelengths

**Q4.** In FTTC, the final connection to the premises is made using:
A. Fibre  B. Copper  C. Radio  D. Coaxial cable

**Q5.** DSL is able to coexist with voice on the same pair because it:
A. Uses time division  B. Uses frequencies above the 4 kHz voiceband  C. Uses a second pair  D. Compresses the voice

**Q6.** The modulation technique used by DSL is:
A. GMSK  B. DMT, a wireline form of OFDM  C. Manchester  D. CDMA

**Q7.** DSL data rate falls with loop length principally because:
A. Propagation delay increases
B. Attenuation increases with both frequency and distance, killing the high-bit-carrying tones first
C. The exchange reduces power
D. Crosstalk disappears

**Q8.** Vectoring in VDSL2 is used to:
A. Increase transmit power  B. Cancel crosstalk between pairs  C. Add error correction  D. Extend the loop length

**Q9.** In an HFC network, the segment shared among many homes is the:
A. Fibre feeder  B. Coaxial segment  C. Head end  D. Splitter

**Q10.** In a PON, the splitter is:
A. An active switch  B. A passive device requiring no power  C. A router  D. An amplifier

**Q11.** PON downstream transmission is:
A. Broadcast to all ONTs, which discard traffic not addressed to them
B. Point to point per subscriber
C. TDMA
D. CDMA

**Q12.** PON upstream transmission uses:
A. Broadcast  B. TDMA scheduled by the OLT  C. FDMA  D. CDMA

**Q13.** Ranging in a PON is performed to:
A. Measure signal strength
B. Determine each ONT's distance so upstream bursts do not collide
C. Assign IP addresses
D. Select the wavelength

**Q14.** GPON downstream line rate is:
A. 1.244 Gbps  B. 2.488 Gbps  C. 10 Gbps  D. 40 Gbps

**Q15.** With a GPON at a 1:64 split, the average downstream per subscriber if all are active simultaneously is approximately:
A. 39 Mbps  B. 78 Mbps  C. 155 Mbps  D. 622 Mbps

**Q16.** The key operational advantage of PON over an active optical network in areas with unreliable grid power is that:
A. It is faster
B. There is no powered equipment between the exchange and the home
C. It uses less fibre in total
D. It needs no ONT

**Q17.** In fibre access economics, the dominant cost is incurred:
A. Per subscriber connected  B. Per premises passed  C. In the core network  D. In the ONT

**Q18.** Fixed wireless access is attractive for rural Ghana mainly because:
A. It offers the highest possible capacity
B. It avoids trenching and can be deployed rapidly at low capital cost
C. It is immune to weather
D. It requires no spectrum

**Q19.** DSL had limited impact in Ghana primarily because:
A. The technology was unavailable
B. There was never a dense copper local loop reaching most homes
C. It was banned by the regulator
D. It was too fast

**Q20.** XGS-PON differs from XG-PON in that XGS-PON is:
A. Slower  B. Symmetric at 10 Gbps in both directions  C. Passive  D. Wireless

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | It is transparent to bit rate and format and amplifies the whole band |
| 2 | **C** | 80 x 200 Gbps = 16 Tbps |
| 3 | **B** | The glass is unchanged; the electronics at each end define capacity |
| 4 | **B** | Fibre to the cabinet, copper for the final drop |
| 5 | **B** | Voice occupies 0 to 4 kHz, data occupies 25 kHz upward |
| 6 | **B** | Discrete multi-tone with per-tone bit loading |
| 7 | **B** | The tones carrying the most bits attenuate fastest |
| 8 | **B** | Crosstalk between pairs in a binder is the dominant limit |
| 9 | **B** | The coax from the optical node to homes is shared |
| 10 | **B** | Passive is the defining feature of a PON |
| 11 | **A** | Broadcast downstream with AES encryption for privacy |
| 12 | **B** | Time slots granted by the OLT to prevent collisions |
| 13 | **B** | Equalisation delay is applied so bursts arrive back to back |
| 14 | **B** | 2.488 Gbps down, 1.244 Gbps up |
| 15 | **A** | 2488 / 64 ≈ 39 Mbps |
| 16 | **B** | Nothing in the field needs electricity or backup |
| 17 | **B** | Trenching and passing cost is incurred regardless of take-up |
| 18 | **B** | Speed of deployment and avoidance of civil works |
| 19 | **B** | DSL requires a legacy copper loop that was never widely built |
| 20 | **B** | The S denotes symmetric 10 Gbps |

---

# Exam-style written questions with model answers

**W1. With the aid of a diagram, describe the architecture of a Passive Optical Network, explaining the role of the OLT, the splitter and the ONT, and how the downstream and upstream directions differ. (10 marks)**

*Model answer:* Draw the OLT at the exchange, a single feeder fibre to a passive splitter, and drop fibres to individual ONTs. The **Optical Line Terminal** sits at the exchange or a local point of presence, aggregates traffic from the core network, and controls every aspect of the PON, in particular the scheduling of upstream transmission. The **passive optical splitter** divides the incoming optical signal among a number of output fibres, typically thirty-two or sixty-four, and combines the returning signals. It is entirely passive, containing no electronics and requiring no power supply, which is why the architecture is called passive; this is its decisive operational advantage because there is nothing in the field to power, cool, back up or maintain. The **Optical Network Terminal** at the customer premises terminates the fibre and presents Ethernet, telephone and Wi-Fi interfaces to the subscriber. The two directions operate quite differently. **Downstream**, the OLT transmits a single continuous stream on one wavelength, typically 1490 nanometres, which the splitter copies to every ONT. Transmission is therefore effectively a broadcast, and each ONT extracts only the frames addressed to it and discards the rest; because every ONT physically receives every other subscriber's data, the payload is encrypted with AES so that a subscriber cannot read a neighbour's traffic. **Upstream**, all ONTs share one fibre and one wavelength, typically 1310 nanometres, so simultaneous transmission would cause collisions. The OLT therefore operates a time division multiple access scheme, granting each ONT a precise time window in which to transmit a burst. Because ONTs are at different distances from the splitter and their propagation delays differ, the OLT performs a **ranging** procedure to measure each ONT's round-trip delay and instructs it to apply a corresponding equalisation delay, so that bursts from ONTs at different distances arrive at the OLT tightly packed without overlapping. The use of two different wavelengths for the two directions is itself an application of wavelength division multiplexing within the PON.

**W2. Explain why DSL data rate depends so strongly on loop length, and compare ADSL2+, VDSL2 and G.fast in terms of rate and reach. (7 marks)**

*Model answer:* DSL transmits on the copper local loop using discrete multi-tone modulation, which divides the available spectrum into a large number of narrow sub-channels of 4.3125 kilohertz each. During training, the modem measures the signal-to-noise ratio of every individual sub-channel and loads onto each one as many bits per symbol as that channel's SNR will support, up to a maximum of fifteen, which is Shannon's capacity theorem applied per tone. The total rate is the sum across all tones. Attenuation on a copper pair increases both with distance and with frequency, so on a long loop the highest-frequency tones, which are precisely those that would otherwise carry the most bits, are the first to fall below the usable signal-to-noise ratio and are switched off entirely. As the loop lengthens, progressively more of the upper spectrum becomes unusable and the achievable rate falls smoothly rather than failing abruptly. Crosstalk from neighbouring pairs in the same cable binder compounds this, since crosstalk coupling also increases with frequency. **ADSL2+** uses spectrum up to 2.2 megahertz and achieves up to 24 megabits per second downstream, but only close to the exchange; at three kilometres the rate falls to a few megabits and it becomes unusable beyond about five kilometres. **VDSL2** uses spectrum up to 17 or 30 megahertz and achieves around 100 megabits per second downstream, but only within roughly 300 metres to one kilometre, which is why it is always deployed from a street cabinet rather than the exchange, in an FTTC architecture. **G.fast** uses spectrum up to 106 or 212 megahertz and achieves aggregate rates approaching one gigabit, but only over runs of under 250 metres, so it is deployed from a distribution point serving a handful of premises. The consistent pattern is that each generation buys higher rate by using higher frequencies, and pays for it with drastically shorter reach, which forces fibre progressively closer to the customer.

**W3. Compare PON, DSL, cable and fixed wireless access as last-mile technologies, and recommend an approach for (a) a dense residential district in Accra and (b) a rural community of 400 households in the Northern Region. (10 marks)**

*Model answer:* Present the comparison across medium, sharing, rate, distance sensitivity, capital and operating cost, and power requirements. **DSL** reuses an existing copper local loop, so its capital cost is negligible where copper exists, but its rate collapses with loop length and it is limited by crosstalk. **Cable** uses fibre to an optical node and shared coaxial cable to homes, offering high rates through DOCSIS 3.1 but with performance that degrades in the evening peak because the coax segment is shared. **PON** uses fibre with a passive splitter, offering the highest and most future-proof capacity, minimal distance sensitivity out to twenty kilometres, and no powered equipment in the field, at the cost of high civil works expenditure incurred per premises passed regardless of take-up. **FWA** delivers broadband over radio, requiring no trenching, deployable within days, with capital cost incurred largely per subscriber connected, but with capacity shared across all users of the spectrum and dependent on line of sight and weather. **(a) For a dense residential district in Accra**, recommend **GPON or XGS-PON**. Density means the cost of trenching is amortised across a large number of premises within a short distance, take-up is likely to be high and willingness to pay is greatest, and the sustained demand from many simultaneous users would exhaust the shared capacity of any wireless solution. The absence of powered field equipment is a decisive advantage given the cost and vulnerability of powering street cabinets. A 1:32 split on GPON gives roughly 78 megabits per second average per subscriber under full simultaneous load and far more in practice through statistical multiplexing. **(b) For 400 rural households in the Northern Region**, recommend **fixed wireless access on a low band such as 700 or 800 megahertz**, backhauled by terrestrial microwave to the nearest fibre point of presence. Fibre would require many kilometres of trenching to reach a small number of dispersed households with uncertain and low take-up and limited willingness to pay, so the cost per connected subscriber would be prohibitive. FWA on a low band gives large cells and good propagation over the terrain, can be deployed within weeks, and its cost scales with the number of households that actually subscribe. Solar with battery backup should be specified at the site given grid unreliability. The design should be staged, with fibre considered later only if a road or power corridor becomes available and demand grows sufficiently to justify the capital outlay.

**W4. Explain how DWDM allows a submarine cable's capacity to be increased without laying new cable, and discuss the significance of this for Ghana. (7 marks)**

*Model answer:* A submarine cable contains a small number of single-mode fibre pairs together with a power conductor and optical amplifiers spaced along its length. Dense wavelength division multiplexing carries many independent optical channels on a single fibre by assigning each to a distinct wavelength on the ITU grid within the low-loss 1550 nanometre window, where erbium-doped fibre amplifiers operate. The critical property is that an EDFA amplifies **all wavelengths simultaneously in the optical domain**, without converting the signals to electrical form, and is therefore transparent both to the number of wavelengths and to the bit rate and modulation format carried on each. Consequently the capacity of the system is determined almost entirely by the terminal equipment at each landing station rather than by the cable itself. To increase capacity, the operator replaces the terminal transceivers and multiplexers with equipment supporting more wavelengths and higher rates per wavelength, most recently using coherent detection with digital signal processing to reach four hundred gigabits or more per wavelength while compensating dispersion electronically. The glass in the seabed is untouched. For Ghana this has been economically decisive. The SAT-3/WASC cable landed at Accra in 2001, and MainOne, Glo-1 and the ACE cable followed between 2010 and 2012. The capacity available to Ghana today is many times what those cables were originally commissioned to carry, achieved through successive terminal upgrades rather than new construction, and the marginal cost of each additional upgrade is small compared with the enormous fixed cost of laying a cable. This is the principal reason why the wholesale cost of international bandwidth in Ghana has fallen so steeply, which in turn made affordable retail mobile data possible and underpins the growth of digital services and mobile money. It also explains why the binding constraint on Ghanaian connectivity has shifted decisively from international capacity, which is now plentiful, to the domestic backbone and the last mile.

---

## Checkpoint before Topic 11

You should be able to, without notes:
1. Explain the EDFA argument for why DWDM upgrades do not require new cable
2. Draw the FTTx ladder and say where copper starts in each variant
3. Explain DSL bit loading and the rate-versus-reach mechanism
4. Draw a PON and explain broadcast downstream versus TDMA upstream with ranging
5. Compute per-subscriber bandwidth from line rate and split ratio
6. Compute an optical power budget and reach
7. State the premises-passed versus premises-connected cost distinction
8. Recommend and justify an access technology for a described scenario
