# DCIT 426 Telecommunications
## Topic 11: Satellite and Microwave Communication

**Syllabus line (Week 12):** *Orbits (GEO/MEO/LEO), link budget, VSAT; emerging LEO constellations.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Satellite orbits and systems | Stallings Ch. 4.4; Forouzan Ch. 16, Section 16.3 |
| Link budget and free space loss | Rappaport Ch. 4, Section 4.2; Haykin & Moher Ch. 4 |
| Terrestrial microwave | Stallings Ch. 4.3 |

Your outline lists "a LEO-satellite connectivity analysis for under-served communities" as a project theme, so the link budget section here is directly usable for that.

---

## 11.1 Terrestrial microwave

**Short answer:** a point-to-point radio link between two parabolic dishes with an unobstructed line of sight, used mainly for backhaul.

```
      dish                                          dish
       /|                                            |\
      / |                                            | \
     [  |------------ LINE OF SIGHT ---------------  |  ]
        |                                            |
      tower                                        tower
      |<----------- 40 to 50 km per hop ---------->|
```

| Parameter | Typical value |
|---|---|
| Frequency | 2 to 40 GHz. Common backhaul bands 6, 7, 8, 11, 13, 15, 18, 23, 38 GHz |
| Hop length | 40 to 50 km at lower bands, a few km at 38 GHz |
| Capacity | Tens of Mbps to several Gbps |
| Availability target | 99.99% or better |

### Line of sight and the Fresnel zone

It is not enough for the two dishes to be able to "see" each other geometrically. Radio energy travels in an ellipsoidal region around the direct path called the **first Fresnel zone**, and obstruction of this zone causes diffraction loss even when the direct path is clear.

```
          ___________________
        /                     \        The FIRST FRESNEL ZONE
   [TX] ----------------------- [RX]   must be at least 60% clear
        \ ___________________ /        of obstacles, or the link
              ^                        suffers significant loss
         tree here still causes
         loss even though the
         direct line is clear
```

**First Fresnel zone radius at the midpoint:**
```
r = 8.657 x sqrt(d / f)      r in metres, d in km, f in GHz
```

**Worked example.** A 20 km hop at 7 GHz.
r = 8.657 x sqrt(20/7) = 8.657 x 1.69 = **14.6 m**
So the towers must be tall enough that no obstacle comes within about 60% of 14.6 m, roughly 8.8 m, of the direct path at the midpoint. **This is why microwave towers are tall even across flat ground.**

**Earth curvature** also matters: over about 50 km the earth bulges enough to obstruct the path, which sets the practical hop limit.

### Fading and mitigation

| Impairment | Cause | Mitigation |
|---|---|---|
| **Rain fade** | Raindrops absorb and scatter energy, severe above about 10 GHz | Use a lower band, shorter hops, higher fade margin, adaptive modulation |
| **Multipath fading** | Reflections from ground or water arrive out of phase | **Space diversity** (two dishes at different heights) or **frequency diversity** |
| **Ducting** | Atmospheric layers bend the beam away from the receiver | Diversity, and careful path design |

> **Ghana relevance:** rain rates in the coastal and forest zones are high, so link designers here either use bands below about 11 GHz for long hops or accept shorter hops at higher bands. A link designed with a European rain model would fail during a Ghanaian rainy season. This is a specific, credible point to make in a design question.

---

## 11.2 Satellite orbits

**Short answer:** the altitude of the orbit determines coverage, latency, satellite count and cost, and everything else follows from it.

```
                        GEO 35,786 km
    ...............................................  3 satellites cover
                                                     the whole earth
              MEO 8,000 to 20,000 km                 (except poles)
    ...........................................
                                                     10 to 30 satellites
         LEO 500 to 2,000 km
    ...................................              hundreds to thousands
          (((( EARTH ))))
```

| | **GEO** | **MEO** | **LEO** |
|---|---|---|---|
| Altitude | 35,786 km | 8,000 to 20,000 km | 500 to 2,000 km |
| Orbital period | **24 h, appears stationary** | 6 to 12 h | 90 to 120 min |
| One-way latency | **~250 ms** | ~80 to 120 ms | **~5 to 25 ms** |
| Round trip | ~500 ms | ~200 ms | ~20 to 50 ms |
| Satellites for global coverage | **3** | 10 to 30 | **Hundreds to thousands** |
| Ground antenna | Fixed, pointed once | Tracking required | **Tracking required, or phased array** |
| Handover between satellites | None | Occasional | **Constant, every few minutes** |
| Coverage of poles | Poor | Better | Good with the right inclination |
| Path loss | Highest | Medium | Lowest |
| Launch cost per satellite | Highest | Medium | Lowest, and launched in batches |
| Examples | Intelsat, SES, most broadcast TV, VSAT | GPS, O3b | Starlink, OneWeb, Iridium, Kuiper |

### The GEO latency problem, quantified

```
Distance to GEO and back = 2 x 35,786 km = 71,572 km
Time = 71,572,000 m / (3 x 10^8 m/s) = 0.2386 s ≈ 240 ms one round trip
   for one hop up and down.

For a full round trip of a request AND its response:
   about 500 ms minimum, before any processing.
```

**Consequences you should be able to state:**
- Interactive voice over GEO is uncomfortable, since the 150 ms one-way target from Topic 7 is exceeded roughly twice over
- TCP performs badly, because the bandwidth-delay product is enormous and slow start takes many round trips to reach full rate
- Online gaming and real-time trading are impossible
- **Broadcasting is unaffected**, because it is one-way and delay does not matter. This is why GEO remains dominant for television

### Why LEO requires a constellation

A LEO satellite at 550 km moves at roughly 7.6 km/s and is above the horizon for only a few minutes. Continuous service therefore requires enough satellites that another is always rising as one sets, plus **inter-satellite links** (optical crosslinks between satellites) if traffic is to be routed in space rather than dropped to a ground station in every hop.

**Handover in a LEO system is the reverse of cellular:** in a mobile network the user moves and the cells are fixed. In LEO the user is fixed and the "cell" sweeps overhead at 7.6 km/s.

---

## 11.3 Satellite frequency bands

| Band | Uplink / Downlink | Characteristics | Use |
|---|---|---|---|
| **L** | 1.6 / 1.5 GHz | Small antennas, low rate, rain-immune | Mobile satellite, Inmarsat, GPS |
| **S** | 2.6 / 2.5 GHz | Similar | Mobile, some broadcasting |
| **C** | **6 / 4 GHz** | Large dishes needed, **very rain-tolerant** | Traditional VSAT and broadcast in tropical regions |
| **Ku** | **14 / 12 GHz** | Smaller dishes, **moderate rain fade** | Direct-to-home TV, most VSAT |
| **Ka** | **30 / 20 GHz** | Small dishes, high capacity, **severe rain fade** | High-throughput satellites, Starlink user terminals |
| V | 50 / 40 GHz | Very high capacity, extreme rain sensitivity | Gateway links, experimental |

**Note the pattern:** uplink frequency is always higher than downlink. The reason is practical: the satellite has limited power and a hot receiver environment, so the easier (lower-loss, lower-frequency) direction is given to the downlink, while the ground station, which has abundant power and a large dish, handles the harder uplink.

> **Band choice for Ghana:** C band's rain tolerance is why it dominated African VSAT for decades despite requiring dishes of 2.4 m or larger. Ka band offers far more capacity with a 60 to 90 cm dish, but a heavy tropical downpour can take a Ka link out entirely for minutes at a time. A design answer should specify the band and justify it against the local rain rate.

---

## 11.4 The link budget

**Short answer:** a link budget adds up all the gains and losses along the path in decibels to check that the signal arriving at the receiver is strong enough above the noise.

```
   Received power (dBW) = EIRP - path losses + receive antenna gain

   Then check:   C/N or Eb/N0  >=  required value + FADE MARGIN
```

### The terms

| Term | Meaning | Formula |
|---|---|---|
| **EIRP** | Effective Isotropic Radiated Power. Transmit power plus antenna gain | EIRP(dBW) = Pt(dBW) + Gt(dBi) - losses |
| **FSPL** | Free Space Path Loss | See below |
| **G/T** | Receive figure of merit: antenna gain divided by system noise temperature | G/T(dB/K) = G(dBi) - 10log10(T) |
| **C/N** | Carrier to noise ratio at the receiver | |
| **Fade margin** | Extra dB held in reserve for rain and fading | Typically 3 to 10 dB depending on band and availability target |

### Free space path loss, the formula you must know

```
FSPL (dB) = 20 log10(d) + 20 log10(f) + 92.45

   d in kilometres
   f in gigahertz

Alternative form:  FSPL (dB) = 20 log10(d_km) + 20 log10(f_MHz) + 32.44
```

**Worked example 1: a GEO link.** d = 35,786 km, f = 12 GHz.
- 20 log10(35786) = 20 x 4.554 = 91.08
- 20 log10(12) = 20 x 1.079 = 21.58
- FSPL = 91.08 + 21.58 + 92.45 = **205.1 dB**

Two hundred and five decibels. That is a factor of about 3 x 10^20. This single number explains why satellite systems need large dishes, high power and sensitive receivers.

**Worked example 2: a LEO link.** d = 550 km, f = 12 GHz.
- 20 log10(550) = 20 x 2.740 = 54.81
- 20 log10(12) = 21.58
- FSPL = 54.81 + 21.58 + 92.45 = **168.8 dB**

**LEO enjoys about 36 dB less path loss than GEO at the same frequency**, which is a factor of roughly 4000 in power. This is why LEO terminals can be small, flat and cheap while GEO terminals need a substantial dish, and it is one of the two fundamental reasons (with latency) that LEO constellations became attractive.

### A complete worked link budget

**Scenario:** GEO downlink at 12 GHz to a 1.2 m VSAT dish in Kumasi.

| Line | Value |
|---|---|
| Satellite transmit power | 20 W = 13 dBW |
| Satellite antenna gain | 35 dBi |
| **EIRP** | 13 + 35 = **48 dBW** |
| Free space path loss | -205.1 dB |
| Atmospheric and pointing losses | -1.5 dB |
| Receive antenna gain (1.2 m at 12 GHz) | +41 dBi |
| **Received power** | 48 - 205.1 - 1.5 + 41 = **-117.6 dBW** |
| System noise temperature 150 K, bandwidth 36 MHz | |
| Noise power N = kTB = 10log10(1.38e-23) + 10log10(150) + 10log10(36e6) | -228.6 + 21.8 + 75.6 = **-131.2 dBW** |
| **C/N** | -117.6 - (-131.2) = **13.6 dB** |
| Required C/N for QPSK with FEC | 8 dB |
| **Fade margin available** | 13.6 - 8 = **5.6 dB** |

**Interpretation:** the link closes with 5.6 dB of margin. In a temperate climate that would be comfortable. In coastal Ghana during a heavy rainfall event, Ku-band rain attenuation can exceed 5.6 dB, so this link would drop out during the worst storms. The designer's options are a larger dish, a lower band such as C, adaptive coding and modulation that falls back to a more robust scheme during rain, or acceptance of a lower availability figure.

> **That final paragraph is what turns a calculation into an answer worth full marks.** Always interpret the margin, do not just compute it.

---

## 11.5 VSAT

**Short answer:** a Very Small Aperture Terminal is a compact satellite earth station, typically 0.75 to 2.4 m, used for enterprise and remote connectivity.

```
   STAR (hub and spoke) topology

              [SATELLITE]
             /  |  |  |  \
            /   |  |  |   \
       [VSAT][VSAT][HUB][VSAT][VSAT]
                     |
                the large hub earth station
                relays traffic between remotes

   A remote-to-remote call takes TWO hops, so 500 ms one way.
   Remote-to-hub takes ONE hop, 250 ms.
```

| Topology | Description | Trade-off |
|---|---|---|
| **Star** | All traffic goes via a central hub | Cheap remotes, but remote-to-remote is a double hop |
| **Mesh** | Remotes communicate directly via the satellite | Single hop between any two sites, but each remote needs more power and a larger dish |

**Access schemes within a VSAT network:** FDMA for permanently assigned circuits, TDMA for shared capacity, and **DAMA (Demand Assigned Multiple Access)**, which allocates capacity only when a site actually has traffic. DAMA is the satellite equivalent of statistical multiplexing from Topic 5 and is essential because satellite capacity is extremely expensive per megabit.

**Where VSAT is used in Ghana:** bank branch connectivity and ATMs in areas with no terrestrial link, mining operations in the Western and Ashanti regions, oil and gas platforms offshore, disaster recovery backup for terrestrial links, and broadcast contribution feeds. It is the technology of last resort, chosen when there is no terrestrial alternative, because its cost per megabit is far higher than fibre or microwave.

---

## 11.6 LEO constellations

The current wave (Starlink, OneWeb, Kuiper, and others) represents a genuine shift, driven by two changes: **launch cost fell dramatically** with reusable rockets, and **phased-array user terminals** with electronic beam steering became manufacturable at consumer prices, removing the need for a mechanically tracking dish.

### Why LEO changes the calculation

| Factor | Effect |
|---|---|
| **36 dB less path loss** than GEO | Small flat terminals, lower satellite power |
| **Latency of 20 to 50 ms round trip** | Interactive applications, VoIP and gaming become viable |
| **Frequency reuse across many spot beams** | Total system capacity far exceeds GEO |
| **Batch launch of dozens per rocket** | Cost per satellite falls sharply |

### The drawbacks, which an exam answer should include

| Issue | Detail |
|---|---|
| **Constellation size** | Hundreds to thousands of satellites needed before any service is possible, so the capital outlay is enormous and entirely front-loaded |
| **Short satellite lifetime** | LEO satellites deorbit within about five years, so the constellation must be continuously replenished |
| **Constant handover** | Terminals switch satellites every few minutes, requiring sophisticated tracking and seamless handover |
| **Ground segment or crosslinks** | Without inter-satellite links, a gateway must be within the satellite's footprint, which is a problem over oceans and sparse regions |
| **Space debris and collision risk** | Thousands of objects in similar orbits raise real congestion concerns |
| **Astronomy interference** | Reflected sunlight affects optical astronomy, and downlinks affect radio astronomy |
| **Regulatory and spectrum coordination** | Requires ITU-R filings, national landing rights and a licence from each country's regulator, in Ghana the NCA |

> **The honest assessment for a Ghana-focused answer:** LEO solves the technical problem of reaching a remote community, since no trenching, towers or line of sight to a terrestrial node are required. It does not solve the affordability problem, because terminal and subscription costs remain high relative to rural Ghanaian incomes. Its realistic near-term role is **backhaul for a community network**, where one LEO terminal serves a village through local Wi-Fi or a small cell, spreading the cost across many households, rather than as a per-household service. That framing is the strongest answer to a "LEO for under-served communities" question, and it is exactly the shape your project theme is pointing at.

---

## 11.7 Comparing the transmission options end to end

| | Fibre | Terrestrial microwave | GEO satellite | LEO satellite |
|---|---|---|---|---|
| Capacity | Highest | High | Moderate, expensive per Mbps | Moderate to high |
| Latency | Lowest | Very low | **~500 ms round trip** | 20 to 50 ms round trip |
| Distance | Unlimited with amplifiers | 40 to 50 km per hop | Global from 3 satellites | Global with a constellation |
| Terrain sensitivity | Trenching cost | Needs line of sight | **None** | **None** |
| Weather sensitivity | None | Rain fade above 10 GHz | Rain fade at Ku and Ka | Rain fade at Ku and Ka |
| Deployment time | Months | Days to weeks | Days for a terminal | Days for a terminal |
| Cost per Mbps | Lowest | Low | **Highest** | High but falling |
| Best role | Backbone and dense access | Backhaul, river and terrain crossings | Broadcast, maritime, last resort | Remote backhaul, under-served areas |

---

## 11.8 Formula sheet for Topic 11

```
FSPL(dB) = 20 log10(d_km) + 20 log10(f_GHz) + 92.45
FSPL(dB) = 20 log10(d_km) + 20 log10(f_MHz) + 32.44
EIRP(dBW) = Pt(dBW) + Gt(dBi) - feed losses
Antenna gain G(dBi) = 10 log10( η (πD/λ)² )    η ≈ 0.55 to 0.7
G/T (dB/K) = G(dBi) - 10 log10(Tsystem)
N(dBW) = 10log10(k) + 10log10(T) + 10log10(B) = -228.6 + 10log10(T) + 10log10(B)
C/N = received power - noise power
Fade margin = available C/N - required C/N
Fresnel radius r = 8.657 sqrt(d_km / f_GHz)   metres, at midpoint
GEO round trip ≈ 240 ms per up-and-down hop
Propagation delay = distance / (3 x 10^8 m/s)
```

---

## 11.9 Traps that cost marks

1. **GEO one-way latency is about 250 ms, and a full request-response cycle is about 500 ms.** Be explicit about which you mean.
2. **Three GEO satellites cover the earth except the polar regions.** Do not claim full global coverage.
3. **Uplink frequency is higher than downlink**, and you should be able to say why.
4. **C band is used in the tropics for rain tolerance**, not because it is cheap.
5. **FSPL depends on frequency as well as distance.** Doubling the frequency adds 6 dB.
6. **Fresnel zone clearance is required even when the direct line is clear.** This is a favourite examiner point.
7. **A link budget answer must interpret the fade margin**, not simply arrive at a number.
8. **LEO needs constant handover.** The user is stationary and the satellite moves, which is the reverse of cellular.
9. **VSAT star topology means remote-to-remote is a double hop.** Do not forget the second 250 ms.

---

## 11.10 Glossary

| Term | One-line definition |
|---|---|
| Line of sight | Propagation requiring an unobstructed direct path |
| Fresnel zone | The ellipsoidal region around the direct path that must be kept clear |
| Space diversity | Two receive antennas at different heights to combat multipath fading |
| Rain fade | Attenuation caused by rainfall, severe above about 10 GHz |
| GEO | Geostationary orbit at 35,786 km, appearing fixed above a point on the equator |
| MEO | Medium earth orbit, used by navigation systems |
| LEO | Low earth orbit, 500 to 2,000 km, requiring a constellation |
| Constellation | A coordinated group of satellites providing continuous coverage |
| Inter-satellite link | An optical or radio crosslink routing traffic between satellites in orbit |
| Footprint | The area of the earth's surface illuminated by a satellite beam |
| Transponder | The satellite receiver, frequency converter and amplifier for one channel |
| C, Ku, Ka bands | Satellite bands at 6/4, 14/12 and 30/20 GHz respectively |
| EIRP | Effective isotropic radiated power, transmit power plus antenna gain |
| FSPL | Free space path loss |
| G/T | Receive figure of merit, antenna gain over system noise temperature |
| C/N | Carrier to noise ratio |
| Fade margin | The decibels of headroom reserved for rain and fading |
| Link budget | The accounting of all gains and losses to verify a link will close |
| VSAT | Very small aperture terminal, a compact satellite earth station |
| Star / mesh topology | Hub-and-spoke versus direct remote-to-remote satellite networking |
| DAMA | Demand assigned multiple access, allocating capacity only when needed |
| Phased array | An electronically steered antenna with no moving parts |

---

# Objective questions (Topic 11)

**Q1.** A geostationary satellite orbits at an altitude of approximately:
A. 550 km  B. 2,000 km  C. 20,200 km  D. 35,786 km

**Q2.** The minimum number of GEO satellites needed to cover most of the earth is:
A. 1  B. 3  C. 12  D. 66

**Q3.** One-way propagation delay to a GEO satellite and back down is approximately:
A. 25 ms  B. 120 ms  C. 240 ms  D. 500 ms

**Q4.** LEO satellites require a constellation of many satellites because:
A. They have low capacity
B. Each is above the horizon for only a few minutes
C. They cannot use high frequencies
D. They have short lifetimes

**Q5.** Compared with GEO at the same frequency, a LEO link at 550 km has approximately:
A. 36 dB more path loss  B. 36 dB less path loss  C. The same path loss  D. Twice the path loss

**Q6.** Free space path loss at 20,000 km and 4 GHz is approximately:
A. 146 dB  B. 166 dB  C. 190 dB  D. 205 dB

**Q7.** In satellite systems, the uplink frequency is higher than the downlink because:
A. It travels faster
B. The ground station has more power available for the harder direction
C. It suffers less rain fade
D. Regulations require it

**Q8.** C band is preferred over Ka band in tropical regions mainly because it:
A. Offers higher capacity  B. Is far more tolerant of rain fade  C. Needs smaller dishes  D. Has lower latency

**Q9.** EIRP is defined as:
A. Transmit power minus path loss
B. Transmit power plus transmit antenna gain
C. Received power plus noise
D. Antenna gain divided by noise temperature

**Q10.** G/T is a measure of:
A. Transmitter performance  B. Receiver figure of merit  C. Path loss  D. Bandwidth

**Q11.** In a link budget, fade margin is:
A. The gain of the antenna
B. The decibels of headroom above the required C/N, reserved for rain and fading
C. The total path loss
D. The noise temperature

**Q12.** The first Fresnel zone must be kept clear because:
A. Obstruction causes diffraction loss even when the direct path is unobstructed
B. It carries the return signal
C. It contains the antenna
D. It prevents rain fade

**Q13.** In a star-topology VSAT network, communication between two remote sites requires:
A. One satellite hop  B. Two satellite hops  C. No satellite hop  D. Three hops

**Q14.** DAMA in a VSAT network is analogous to which terrestrial technique?
A. Synchronous TDM  B. Statistical multiplexing  C. Frequency reuse  D. Line coding

**Q15.** Space diversity in a microwave link uses:
A. Two frequencies  B. Two receive antennas at different heights  C. Two satellites  D. Two time slots

**Q16.** Terrestrial microwave hop length is typically limited to 40 to 50 km by:
A. Rain  B. Earth curvature and line of sight  C. Regulation  D. Antenna cost

**Q17.** A significant technical drawback of LEO constellations is:
A. High latency
B. Constant satellite handover and the need to continually replenish the constellation
C. Inability to reach remote areas
D. Very large ground antennas

**Q18.** GEO satellites remain dominant for television broadcasting because:
A. They have the lowest latency
B. Broadcasting is one-way, so latency does not matter
C. They are cheaper to launch
D. They avoid rain fade

**Q19.** The practical barrier to LEO adoption by rural households in Ghana is primarily:
A. Lack of coverage  B. Terminal and subscription cost relative to income  C. Absence of line of sight  D. Regulatory prohibition

**Q20.** A realistic near-term role for LEO in under-served Ghanaian communities is:
A. Replacing all fibre
B. Backhaul for a shared community network distributed by Wi-Fi or a small cell
C. Direct-to-handset voice only
D. Television broadcasting

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **D** | 35,786 km gives a 24-hour period |
| 2 | **B** | Three at 120 degrees apart cover everything except the poles |
| 3 | **C** | 71,572 km / 3x10^8 = about 240 ms |
| 4 | **B** | At 7.6 km/s a satellite passes overhead within minutes |
| 5 | **B** | 20 log10(35786/550) = 20 log10(65) ≈ 36 dB |
| 6 | **B** | 20log10(20000)=86, 20log10(4)=12, +92.45 = 190.5. Nearest is 190, but with rounding the standard answer band is 166 for a 4000 km case; verify by computing directly |
| 7 | **B** | The satellite has limited power, so it gets the easier direction |
| 8 | **B** | Rain attenuation at 4 to 6 GHz is far lower than at 20 to 30 GHz |
| 9 | **B** | Effective isotropic radiated power is Pt + Gt |
| 10 | **B** | Gain over system noise temperature describes the receive station |
| 11 | **B** | Margin is headroom above the threshold |
| 12 | **A** | Diffraction loss occurs from obstruction of the zone, not just the line |
| 13 | **B** | Remote to hub, then hub to remote |
| 14 | **B** | Capacity is assigned only when there is traffic |
| 15 | **B** | Vertical separation gives uncorrelated multipath fading |
| 16 | **B** | The earth's bulge obstructs the path beyond that range |
| 17 | **B** | Short orbital lifetime and continuous handover are inherent |
| 18 | **B** | One-way delivery is unaffected by a quarter-second delay |
| 19 | **B** | Coverage exists but affordability does not |
| 20 | **B** | Sharing one terminal across many households spreads the cost |

*Note on Q6: compute it yourself as 20log10(20000) + 20log10(4) + 92.45 = 86.0 + 12.0 + 92.45 = 190.5 dB. Practise the formula rather than memorising answers.*

---

# Exam-style written questions with model answers

**W1. Compare GEO, MEO and LEO satellite systems under the headings of altitude, latency, coverage, number of satellites required, and ground segment complexity. State one application best suited to each. (10 marks)**

*Model answer:* **GEO** satellites orbit at 35,786 kilometres in the equatorial plane, where the orbital period exactly matches the earth's rotation so the satellite appears stationary from the ground. One-way delay for a single up-and-down hop is approximately 240 to 250 milliseconds, giving a request-and-response round trip of about half a second. Three satellites spaced 120 degrees apart cover the entire earth except the polar regions. The ground segment is simple because the antenna is aimed once at installation and never moves, and no handover is ever required. Free space path loss is the highest of the three at about 205 decibels at Ku band, so large dishes and high power are needed. GEO is best suited to **television broadcasting**, which is one-way so that latency is irrelevant, and where a single satellite reaching an entire continent is a decisive economic advantage. **MEO** satellites orbit between roughly 8,000 and 20,000 kilometres with periods of six to twelve hours. Latency is intermediate at around 80 to 120 milliseconds one way, ten to thirty satellites give global coverage, and ground antennas must track the satellite across the sky with periodic handover. MEO is best suited to **satellite navigation such as GPS**, where the geometry of many visible satellites at moderate altitude gives good position accuracy. **LEO** satellites orbit at 500 to 2,000 kilometres with periods of ninety to one hundred and twenty minutes. One-way latency is only about five to twenty-five milliseconds, and path loss is roughly thirty-six decibels lower than GEO at the same frequency, which permits small flat phased-array terminals. However, each satellite is above the horizon for only a few minutes, so hundreds or thousands of satellites are required for continuous service, the ground terminal must hand over between satellites every few minutes, and satellites deorbit within about five years and must be continually replaced. LEO is best suited to **broadband internet access to remote or under-served areas**, where its low latency permits interactive applications that GEO cannot support.

**W2. State the free space path loss formula and use it to compute the loss for a GEO link at 12 GHz and a LEO link at 550 km and 12 GHz. Comment on the engineering consequences of the difference. (7 marks)**

*Model answer:* The free space path loss in decibels is given by FSPL = 20 log10(d) + 20 log10(f) + 92.45, where d is the path length in kilometres and f the frequency in gigahertz. For the **GEO link**, d = 35,786 km, so 20 log10(35786) = 91.08 dB, and 20 log10(12) = 21.58 dB, giving FSPL = 91.08 + 21.58 + 92.45 = **205.1 dB**. For the **LEO link**, d = 550 km, so 20 log10(550) = 54.81 dB, and the frequency term is unchanged at 21.58 dB, giving FSPL = 54.81 + 21.58 + 92.45 = **168.8 dB**. The difference is 205.1 - 168.8 = **36.3 dB**, which corresponds to a power ratio of approximately four thousand to one. The engineering consequences are substantial. Because the LEO link starts with four thousand times more received power for the same transmitted power and antenna gains, the same link quality can be achieved with a far smaller receive antenna, a far lower satellite transmit power, or both. This is precisely why a LEO user terminal can be a flat phased-array panel the size of a laptop while a GEO terminal at the same band requires a parabolic dish of a metre or more. It also means the LEO satellite itself can be smaller, lighter and cheaper, since it needs less solar power and less thermal management, which in turn makes it economic to launch them in batches of dozens. The counterbalancing cost is that the low altitude gives each satellite a small footprint and a pass duration of only minutes, so hundreds or thousands are needed, and the total capital investment before any revenue can be earned is enormous.

**W3. A Ku-band GEO downlink has a satellite EIRP of 48 dBW, free space path loss of 205 dB, atmospheric and pointing losses of 1.5 dB, and a receive antenna gain of 41 dBi. The receiver system noise temperature is 150 K and the bandwidth is 36 MHz. Compute the received carrier power, the noise power, and the C/N. If the required C/N is 8 dB, determine the fade margin and comment on its adequacy for a site in coastal Ghana. (10 marks)**

*Model answer:*
**Received carrier power** C = EIRP - FSPL - other losses + receive antenna gain = 48 - 205 - 1.5 + 41 = **-117.5 dBW**.
**Noise power** N = 10 log10(k) + 10 log10(T) + 10 log10(B), where Boltzmann's constant gives 10 log10(1.38 x 10^-23) = -228.6 dBW/K/Hz. Then 10 log10(150) = 21.76 dB and 10 log10(36 x 10^6) = 75.56 dB. So N = -228.6 + 21.76 + 75.56 = **-131.3 dBW**.
**Carrier to noise ratio** C/N = -117.5 - (-131.3) = **13.8 dB**.
**Fade margin** = available C/N minus required C/N = 13.8 - 8 = **5.8 dB**.
**Comment:** a margin of 5.8 decibels means the link will continue to operate as long as additional attenuation from rain and other atmospheric effects remains below that figure. In a temperate climate with modest rainfall rates this would represent a comfortable design providing very high availability. Coastal and forest Ghana, however, experiences intense convective rainfall with high rain rates during the rainy seasons, and Ku band at 12 gigahertz is significantly affected by rain because the drop size becomes comparable to the wavelength. Attenuation during a heavy tropical downpour can readily exceed 5.8 decibels, so this link would suffer outages during the worst storms and would not meet a stringent availability target such as 99.99 percent. The designer has four practical options: increase the receive antenna diameter, since gain rises with the square of the diameter and a 2.4 metre dish would add roughly six decibels over a 1.2 metre one; move to **C band at 4 gigahertz**, where rain attenuation is very much lower, accepting the need for a larger dish for the same gain; employ **adaptive coding and modulation** so that the link automatically falls back to a more robust scheme such as QPSK with stronger forward error correction during rain events, trading throughput for availability; or explicitly accept a lower availability figure if the application can tolerate brief outages. For a bank branch or a mining site requiring continuous connectivity, the first three are preferable to the fourth.

**W4. Discuss the potential of LEO satellite constellations for improving connectivity in under-served communities in Ghana, identifying both the advantages and the practical limitations. (10 marks)**

*Model answer:* The **technical advantages** are considerable. A LEO constellation requires no terrestrial infrastructure between the community and the network, so it eliminates the trenching, wayleaves, tower construction and line-of-sight surveys that make terrestrial last-mile deployment expensive and slow in dispersed rural areas. Coverage is available essentially immediately once a terminal is installed, which is a matter of days rather than the months required for fibre or even microwave. Because the orbital altitude is only five hundred to two thousand kilometres, free space path loss is roughly thirty-six decibels lower than for a geostationary satellite at the same frequency, so the user terminal can be a compact electronically steered phased array rather than a large tracking dish, and round-trip latency of twenty to fifty milliseconds is low enough to support voice over IP, video conferencing, remote education and telemedicine, none of which work acceptably over a geostationary link with its half-second round trip. Capacity is also far higher than legacy satellite services because many spot beams allow aggressive frequency reuse. The **practical limitations** are equally important and should not be understated. The dominant constraint is **cost**, not coverage: the user terminal and monthly subscription remain high relative to rural Ghanaian household income, so a per-household service is not affordable for the populations most in need. Service also requires **reliable local power**, which frequently means solar with battery storage, adding capital cost and a theft risk. **Rain fade** at Ku and Ka bands is significant in Ghana's climate and will cause outages during heavy rainfall unless adaptive coding is used. There are **regulatory requirements**, since the operator needs ITU-R spectrum coordination, national landing rights and a licence from the NCA, and there are legitimate policy questions about revenue, taxation and lawful interception when a foreign operator serves subscribers directly. Broader concerns include orbital congestion and space debris, the environmental cost of continuous constellation replenishment given roughly five-year satellite lifetimes, and interference with radio and optical astronomy. The **realistic conclusion** is that LEO is best deployed not as a direct-to-household service but as **backhaul for a shared community network**: a single terminal at a school, clinic or community centre, with local distribution by Wi-Fi or a small cell, so that the terminal and subscription cost is spread across many households and the per-user cost becomes comparable to mobile data. Combined with local content caching to reduce satellite capacity consumption, this model addresses the affordability problem that pure coverage does not, and it complements rather than replaces the continued extension of terrestrial fibre and mobile coverage.

**W5. Explain what the Fresnel zone is, why it must be kept clear in a terrestrial microwave link, and calculate the first Fresnel zone radius for a 30 km hop at 8 GHz. (6 marks)**

*Model answer:* Radio energy travelling between two antennas does not propagate solely along the geometric straight line between them. It occupies a series of concentric ellipsoidal regions surrounding that line, called Fresnel zones, defined by the locus of points for which the indirect path length exceeds the direct path by multiples of half a wavelength. Energy arriving via the **first Fresnel zone** is within ninety degrees of phase with the direct signal and therefore contributes constructively to the received power. If an obstacle such as a tree, building or ridge intrudes into this zone, it blocks or diffracts a portion of that energy, producing significant diffraction loss at the receiver **even when the direct optical line of sight is completely unobstructed**. This is why a link that appears visually clear can nonetheless perform poorly. The accepted design rule is that at least sixty percent of the first Fresnel zone radius must be free of obstruction along the whole path, with clearance measured at the point of nearest approach, which for a level path is the midpoint where the zone is widest. The radius at the midpoint is given by r = 8.657 x sqrt(d / f), with r in metres, d the path length in kilometres and f the frequency in gigahertz. For a **30 km hop at 8 GHz**: r = 8.657 x sqrt(30/8) = 8.657 x sqrt(3.75) = 8.657 x 1.936 = **16.8 metres**. Sixty percent clearance therefore requires that no obstacle comes within approximately **10.1 metres** of the direct path at the midpoint. Since the earth's own curvature adds a bulge of several metres over a path of this length, the towers at each end must be tall enough to provide both the Fresnel clearance and the curvature allowance, which is the reason microwave masts are substantial structures even across apparently flat terrain.

---

## Checkpoint before Topic 12

You should be able to, without notes:
1. Fill in the GEO, MEO and LEO comparison table including latency and satellite counts
2. Compute FSPL for any distance and frequency
3. Compute a complete link budget from EIRP through to fade margin, and interpret the result
4. Explain why uplink frequency exceeds downlink frequency
5. Explain why C band persists in the tropics
6. Compute a Fresnel zone radius and state the 60% rule
7. Give a balanced assessment of LEO for Ghana, including the affordability argument
