# DCIT 426 Telecommunications

## Topic 1: Course Introduction and Telecom Fundamentals

**Syllabus line (Week 1):** *Evolution of telecommunications; signals,
bandwidth, channels; analog vs digital; standards bodies (ITU, IEEE,
IETF).*

### Textbook Mapping

| Sub-topic | Where to Read | Notes |
| :--- | :--- | :--- |
| System model, standards | Foruzan Ch. 1; Stallings Ch. 1 and Ch. 2 | |
| Signals, bandwidth, time/frequency domain | Foruzan Ch. 3; Stallings Ch. 3 | |
| Analog vs digital transmission | Stallings Ch. 3, Section 3.2 | |
| History and evolution | Haykin & Moher Ch. 1 | |


------------------------------------------------------------------------

## 1.1 What "telecommunication" actually means

**Short answer:** communication over a distance, using electrical,
electromagnetic or optical signals, where the message is carried by an
artificial system rather than by your own voice.

The word breaks into two parts: - *tele* (Greek): far, at a distance -
*communicare* (Latin): to share

So a person shouting across a room is communication. That same person
calling from Legon to Kumasi is **telecommunication**, because a system
had to convert the voice into a signal, carry it, and rebuild it at the
other end.

### The five-block model of any telecom system

Every telecom system you will study this semester, from a telegraph in
1840 to a 5G base station in Achimota, fits this same picture. Memorise
this diagram. Examiners love it.

      +--------+     +-------------+      +-----------+      +----------+     +-------------+
      | SOURCE | --> | TRANSMITTER | ---> |  CHANNEL  | ---> | RECEIVER | --> | DESTINATION |
      +--------+     +-------------+      +-----------+      +----------+     +-------------+
       message         converts the         the medium        undoes what        the person
       (voice,         message into a       (copper, air,      the transmitter    or device
       text, video)    signal suited to     fibre, space)      did                that uses
                       the channel               ^                                the message
                                                 |
                                            +---------+
                                            |  NOISE  |  <-- always present, never invited
                                            +---------+

**Worked example (map the blocks yourself):** you send a WhatsApp voice
note from Madina to a friend in Tamale.

  ---------------------------------------------------------------------
  Block                              What plays that role
  ---------------------------------- ----------------------------------
  Source                             Your voice, plus the phone
                                     microphone

  Transmitter                        Phone codec + modulator + antenna
                                     (turns bits into a radio wave)

  Channel                            Air to the MTN cell tower, then
                                     fibre backbone up north

  Noise                              Other phones on the same band,
                                     thermal noise in the amplifiers,
                                     rain fade

  Receiver                           Your friend's antenna,
                                     demodulator, decoder

  Destination                        Their earpiece, and their brain
  ---------------------------------------------------------------------

> **Exam tip:** when a question says "with the aid of a diagram,
> describe a general communication system," this five-block diagram plus
> one sentence per block is the full-mark answer. Always draw noise
> entering at the channel, not at the source.

------------------------------------------------------------------------

## 1.2 Evolution of telecommunications

You do not need every date. You need the **pattern**: each generation
solved the bottleneck of the previous one.

### Global timeline

  -----------------------------------------------------------------------
  Era                     Milestone               Bottleneck it solved
  ----------------------- ----------------------- -----------------------
  1837                    Telegraph (Morse). Dots Distance. First time a
                          and dashes over a wire. message moved faster
                                                  than a horse.

  1876                    Telephone (Bell).       Telegraph needed
                          Continuous voice over a trained operators and
                          wire.                   coded text.

  1895                    Radio (Marconi).        Wires cannot cross
                          Wireless telegraphy.    oceans cheaply or reach
                                                  ships.

  1948                    Shannon publishes       No one knew the
                          information theory;     theoretical limit of a
                          transistor invented.    channel.

  1950s to 60s            Microwave relay,        Capacity for long-haul
                          coaxial cable,          trunk routes.
                          satellite (Telstar,     
                          1962).                  

  1970s                   Optical fibre becomes   Copper capacity and
                          practical (Corning      noise accumulation.
                          low-loss glass);        
                          digital switching.      

  1980s to 90s            1G analog cellular,     Mobility, and
                          then 2G GSM digital.    incompatible networks.
                          Internet and TCP/IP     
                          spread.                 

  2000s                   3G data, broadband DSL, Data, not just voice.
                          submarine fibre reaches 
                          West Africa.            

  2010s to now            4G LTE, 5G NR, LEO      Latency, density, and
                          satellite               capacity per square
                          constellations,         kilometre.
                          everything is IP.       
  -----------------------------------------------------------------------

**The one sentence that ties it together:** telecommunications has moved
from *analog to digital*, from *fixed to mobile*, from *voice-centric to
data-centric*, and from *separate networks per service to one converged
IP network*.

### Ghana timeline (very likely to appear in a "local context" question)

  ---------------------------------------------------------------------
  Year                               Event
  ---------------------------------- ----------------------------------
  1881                               First telegraph lines under
                                     colonial administration, mainly
                                     Cape Coast and Accra

  Post-1957                          Posts and Telecommunications (P&T)
                                     runs everything as a state
                                     monopoly

  1994 to 1995                       Sector reform. Ghana Telecom is
                                     separated out. Mobile licences
                                     issued.

  1996                               Ghana becomes one of the first
                                     African countries with commercial
                                     mobile and full internet
                                     connectivity

  2001                               SAT-3/WASC submarine cable lands
                                     at Accra, replacing satellite-only
                                     international links

  2008                               Vodafone acquires 70% of Ghana
                                     Telecom

  2010 to 2012                       MainOne, Glo-1 and ACE submarine
                                     cables land, prices per Mbps
                                     collapse

  2021                               Government acquires AirtelTigo,
                                     later rebranded **AT Ghana**

  2023 to 2024                       Telecel Group acquires Vodafone
                                     Ghana, rebranded **Telecel Ghana**
                                     in March 2024

  2024 to 2026                       5G introduced through a shared
                                     wholesale model (Next Gen
                                     InfraCo). The NCA later removed
                                     NGIC's exclusivity and, in July
                                     2026, opened a competitive request
                                     for applications for spectrum in
                                     the 700 MHz, 2.3 GHz and 3 GHz
                                     bands.
  ---------------------------------------------------------------------

**Ghana today:** three mobile network operators (MTN, Telecel, AT), with
MTN designated by the NCA as having **Significant Market Power**, which
means it faces stricter price and interconnection rules than the others.
Most subscribers are still on 3G and 4G, not 5G.

> **Why the lecturer cares:** this is the raw material for exam
> questions like "Discuss how liberalisation of the telecom sector
> affected service delivery in Ghana" or "Explain why submarine cable
> landings reduced internet cost in Ghana." Learn the causal story, not
> just the dates.

------------------------------------------------------------------------

## 1.3 Signals

**Short answer:** a signal is a physical quantity (usually voltage,
current, or light intensity) that varies with time and carries
information.

### Analog vs digital signals

    ANALOG SIGNAL                          DIGITAL SIGNAL
    (infinitely many values)               (finite set of values, here 2)

      amplitude                              amplitude
         ^      __                              ^   ___     ___
         |    /    \      __                    |  |   |   |   |
         |  /       \   /   \                   |  |   |   |   |
         |_/         \_/     \___               |__|   |___|   |____
         +----------------------> t             +--------------------> t
         smooth, continuous                     abrupt, discrete steps
         e.g. human voice on a landline         e.g. 1 0 1 1 0 on a LAN cable

  -----------------------------------------------------------------------
                          Analog                  Digital
  ----------------------- ----------------------- -----------------------
  Values                  Continuous, infinite    Discrete, finite
                                                  (usually 0 and 1)

  Example                 Human speech, FM radio  Computer data, CD
                          wave, old vinyl         audio, Ethernet

  Noise                   Noise blends into the   Noise can be rejected
                          signal and cannot be    as long as 0 and 1 are
                          removed                 still distinguishable
  -----------------------------------------------------------------------

**Do not confuse two different pairs.** This is the single most common
mix-up in this course. 1. **Analog vs digital *data*** (the information
itself: voice is analog data, a text file is digital data) 2. **Analog
vs digital *signal*** (how it is physically represented on the medium)

All four combinations exist:

  -----------------------------------------------------------------------
  Data                    Signal                  Real example
  ----------------------- ----------------------- -----------------------
  Analog data             Analog signal           FM radio broadcast

  Analog data             Digital signal          Your voice on a mobile
                                                  phone (codec digitises
                                                  it)

  Digital data            Analog signal           Old dial-up modem, or
                                                  Wi-Fi (bits ride on a
                                                  radio carrier)

  Digital data            Digital signal          Ethernet cable in the
                                                  lab
  -----------------------------------------------------------------------

### The sine wave and its three parameters

The sine wave is the atom of signal theory. Everything else is built
from sine waves.

`s(t) = A sin(2πft + φ)`

            A |     ___                       Peak amplitude A = height
              |   /     \                     Frequency f     = cycles per second (Hz)
            0 |--/-------\-------/---->  t    Phase φ         = starting position
              | /         \     /
           -A |/           \___/
              |<--- T --->|                   Period T = time for one cycle

  -----------------------------------------------------------------------
  Parameter         Meaning           Unit              Change it and...
  ----------------- ----------------- ----------------- -----------------
  Amplitude (A)     Strength or       volts             the sound gets
                    height of the                       louder
                    signal                              

  Frequency (f)     How many cycles   hertz (Hz)        the pitch gets
                    per second                          higher

  Phase (φ)         Where in the      degrees or        it shifts left or
                    cycle it starts   radians           right in time
  -----------------------------------------------------------------------

**Two formulas you must know cold:**

    T = 1 / f            period and frequency are reciprocals
    λ = c / f            wavelength, where c = 3 x 10^8 m/s in free space

**Quick numeric check.** A signal has f = 1 kHz. - T = 1/1000 = 1 ms - λ
= (3 x 10\^8) / 1000 = 300,000 m. That is why very low frequencies need
enormous antennas.

A GSM signal at 900 MHz: λ = (3 x 10\^8)/(900 x 10\^6) = 0.333 m. A
practical antenna is a fraction of that, roughly 8 cm. **This is the
reason higher frequency means smaller antennas**, which is why 5G mmWave
devices can pack many antennas into one phone.

### Time domain vs frequency domain

Same signal, two ways of drawing it. This idea underpins Week 2, 4 and
5, so get it now.

    TIME DOMAIN                             FREQUENCY DOMAIN
    "what the signal looks like"            "what ingredients it is made of"

     amp ^   /\    /\    /\                  amp ^
         |  /  \  /  \  /  \                     |     |
         | /    \/    \/    \                    |     |
         +-------------------> time              +-----+------------> frequency
                                                      1 kHz
       a 1 kHz sine wave                        a single line at 1 kHz

A **composite signal** (any real-world signal, like speech) is a sum of
many sine waves of different frequencies, amplitudes and phases.
**Fourier analysis** is the mathematics that decomposes it. In the
frequency domain a composite signal shows up as several lines or a
continuous band, called its **spectrum**.

     Composite signal made of 1 kHz + 3 kHz + 5 kHz (a square-ish wave)

     FREQUENCY DOMAIN
       amp ^
           |  |
           |  |    |
           |  |    |    |
           +--+----+----+-------> f (kHz)
              1    3    5

       Spectrum = the set of frequencies present
       Bandwidth = 5 - 1 = 4 kHz

> **Analogy:** a plate of jollof is the time domain, what you actually
> see and eat. The frequency domain is the recipe list: rice, tomato,
> oil, spice, in specific quantities. Fourier analysis is the chef who
> tastes the dish and writes out the recipe.

------------------------------------------------------------------------

## 1.4 Bandwidth

**Short answer:** bandwidth is the *width of a band of frequencies*,
measured in hertz. It is NOT the same as data rate, which is measured in
bits per second.

This distinction is examined nearly every year.

  -----------------------------------------------------------------------
  Term              Symbol            Unit              Meaning
  ----------------- ----------------- ----------------- -----------------
  Bandwidth (analog B                 Hz                highest frequency
  sense)                                                minus lowest
                                                        frequency

  Data rate         C or R            bps               how many bits
  (digital sense)                                       pass per second

  Throughput        \-                bps               the data rate
                                                        actually achieved
                                                        in practice
  -----------------------------------------------------------------------

**Formula:** `B = f_highest - f_lowest`

### Worked example: the telephone voice channel

Human speech has energy roughly from 100 Hz to 7 kHz, but intelligible
speech survives on far less. The telephone system therefore passes only
**300 Hz to 3400 Hz**.

       |<------------- 4000 Hz channel slot ------------->|
       |guard|<---- 3100 Hz usable voice band ---->|guard |
       0    300                                   3400   4000  Hz

- Signal bandwidth = 3400 - 300 = **3100 Hz**
- Allocated channel bandwidth = **4 kHz**, the extra 900 Hz being
  **guard bands** to stop neighbouring channels bleeding into each other

**Why bandwidth matters:** more bandwidth means more room to carry
information. Nyquist and Shannon (Week 2) turn this into hard numbers,
but the intuition is already available to you: a wider pipe carries more
water per second.

> **Analogy for the road:** bandwidth is the number of lanes on the
> Accra to Tema motorway. Data rate is how many cars actually arrive per
> minute. More lanes usually means more cars, but not if traffic is bad
> (that is throughput), and never more than the physics allows (that is
> Shannon capacity).

### Baseband vs broadband

  -----------------------------------------------------------------------
                          Baseband                Broadband
  ----------------------- ----------------------- -----------------------
  Meaning                 Signal sent at its      Signal shifted onto a
                          original frequencies    higher-frequency
                                                  carrier

  Channel usage           Whole medium used for   Medium divided into
                          one signal              several frequency
                                                  channels

  Example                 Ethernet over UTP       Cable TV, or FM radio,
                                                  or Wi-Fi
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 1.5 Channels

**Short answer:** the channel is the path the signal travels between
transmitter and receiver, together with everything that path does to the
signal.

### Guided vs unguided

    GUIDED (wired)                             UNGUIDED (wireless)
    signal is confined inside a medium         signal spreads through space

     [TX]===[twisted pair]===[RX]                [TX] ))) ))) ))) [RX]
     [TX]===[coaxial cable]===[RX]                     radio waves,
     [TX]===[optical fibre]===[RX]                     microwave, infrared

  -----------------------------------------------------------------------
                          Guided                  Unguided
  ----------------------- ----------------------- -----------------------
  Medium                  Copper pair, coax,      Air, vacuum, water
                          fibre                   

  Direction control       Physical path decides   Antenna pattern decides
                          it                      it

  Security                Harder to intercept     Easier to intercept

  Mobility                None                    Full

  Ghana example           ECG poles carrying      MTN 4G on 800 MHz in
                          fibre along the N1      Adenta
  -----------------------------------------------------------------------

Week 3 covers each medium properly. For now just know the categories.

### Transmission modes: simplex, half-duplex, full-duplex

    SIMPLEX             A ------------------>  B      one direction only
                        e.g. radio broadcast, keyboard to computer

    HALF DUPLEX         A <-----------------> B      both directions,
                        but only one at a time       e.g. walkie-talkie,
                                                     "over"

    FULL DUPLEX         A ==================> B      both directions
                        A <================== B      simultaneously
                                                     e.g. phone call

  Mode          Both directions?   At the same time?   Example
  ------------- ------------------ ------------------- ------------------------------------------
  Simplex       No                 \-                  TV broadcast, GPS receiver
  Half-duplex   Yes                No                  Walkie-talkie, old CSMA/CD Ethernet hub
  Full-duplex   Yes                Yes                 Telephone call, modern switched Ethernet

> **Common exam trap:** a mobile phone call is full-duplex even though
> there is only one antenna. It achieves this using **FDD** (separate
> uplink and downlink frequencies) or **TDD** (rapid alternation in
> time). You will meet both in Week 5 and Week 9.

### What the channel does to your signal

Every channel degrades the signal. The three main effects (detailed in
Week 2):

  -----------------------------------------------------------------------
  Impairment              What it does            Everyday sign
  ----------------------- ----------------------- -----------------------
  **Attenuation**         Signal loses strength   Voice gets faint on a
                          with distance           long line

  **Distortion**          Different frequencies   Muffled or smeared
                          travel at different     audio
                          speeds, so the shape    
                          changes                 

  **Noise**               Unwanted energy added   Hiss, crackle, static
                          to the signal           
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 1.6 Analog vs digital transmission, and why digital won

This is a classic essay question. Learn the argument, not just the list.

### The key mechanism: repeaters vs amplifiers

    ANALOG LINK
                 noise         noise         noise
                   |             |             |
      [TX]---------v-----[AMP]---v----[AMP]----v----[RX]
      Signal is amplified. But the amplifier cannot tell signal from
      noise, so it amplifies BOTH. Noise accumulates and never leaves.
      After many hops the signal is unusable.

    DIGITAL LINK
                 noise         noise         noise
                   |             |             |
      [TX]---------v---[REGEN]---v---[REGEN]---v----[RX]
      A regenerative repeater DECIDES whether it sees a 0 or a 1,
      then transmits a brand new clean pulse. Noise is discarded
      at every hop. The signal at the far end is as clean as at hop 1.

**That single difference is the heart of why the world went digital.**
Everything else follows from it.

### Full advantages of digital transmission

  ---------------------------------------------------------------------
  Advantage                          Explanation
  ---------------------------------- ----------------------------------
  **Noise immunity**                 Regeneration removes accumulated
                                     noise, as shown above

  **Error detection and correction** You can add parity, checksums,
                                     CRC, FEC to bits. You cannot do
                                     that to an analog waveform. (Week
                                     6)

  **Encryption**                     Bits can be scrambled with a key.
                                     Analog scrambling is weak. (Week
                                     13)

  **Easy multiplexing**              Time-division multiplexing of bit
                                     streams is simple and cheap. (Week
                                     5)

  **Integration of services**        Voice, video, text and data all
                                     become "just bits," so one network
                                     carries everything. This is
                                     *convergence*.

  **Cheap hardware**                 Digital circuits ride the cost
                                     curve of VLSI and software. Analog
                                     circuits do not.

  **Storage and processing**         Bits can be buffered, compressed,
                                     and processed by computers
  ---------------------------------------------------------------------

### Disadvantages of digital

- Digitising analog data needs **more bandwidth**. A 4 kHz voice channel
  becomes 64 kbps PCM, which needs far more than 4 kHz of raw spectrum
  unless clever modulation is used.
- **Quantisation error** is introduced when converting analog to
  digital, and it can never be undone.
- Requires **synchronisation** between sender and receiver, which analog
  does not.

> **Scenario to remember:** compare an old analog trunk line from Accra
> to Bolgatanga with today's fibre link. On the analog line each
> amplifier along the way added hiss, and by Bolga the caller sounded
> like they were in a storm. On the fibre link, every regenerator
> rebuilds clean bits, so the voice at Bolga is bit-for-bit identical to
> the voice at Accra. Distance stops degrading quality.

------------------------------------------------------------------------

## 1.7 Standards and standards bodies

### Why standards exist

**Short answer:** so that equipment from different manufacturers in
different countries can interoperate.

Without standards, an MTN SIM would not work on a Telecel tower, a
Huawei router would not talk to a Cisco switch, and a call from Accra to
London would stop at the border. Standards give you: -
**Interoperability** across vendors - **Competition**, which lowers
prices, because buyers are not locked to one vendor - **Economies of
scale** in manufacturing - **A common technical language** for engineers
and regulators

### De jure vs de facto

  -----------------------------------------------------------------------
                          De jure ("by law")      De facto ("by fact")
  ----------------------- ----------------------- -----------------------
  Origin                  Formally approved by a  Became standard through
                          recognised body         widespread adoption

  Example                 ITU-T G.711, IEEE       TCP/IP originally, PDF,
                          802.11                  HTTP cookies

  Two sub-types of de     *Proprietary/closed*    
  facto                   (owned by one company,  
                          e.g. early Cisco IGRP)  
                          and *open* (published   
                          freely)                 
  -----------------------------------------------------------------------

### The bodies you must know

  ------------------------------------------------------------------------------------------
  Body       Full name            Scope           Output is called      Examples
  ---------- -------------------- --------------- --------------------- --------------------
  **ITU**    International        UN agency.      **Recommendations**   See below
             Telecommunication    Global telecom                        
             Union                coordination.                         

  **IEEE**   Institute of         LAN, MAN,       **Standards**         802.3 Ethernet,
             Electrical and       physical and    (numbered)            802.11 Wi-Fi, 802.15
             Electronics          data-link                             Bluetooth/Zigbee,
             Engineers            layers                                802.16 WiMAX

  **IETF**   Internet Engineering Internet        **RFCs** (Request for RFC 791 IP, RFC 793
             Task Force           protocols,      Comments)             TCP, RFC 2616/7230
                                  layer 3 and                           HTTP
                                  above                                 

  **ISO**    International        Broad, all      ISO standards         ISO/IEC 7498, the
             Organization for     industries                            **OSI model**
             Standardization                                            

  **3GPP**   3rd Generation       Mobile: GSM,    **Releases** and TS   Release 15 = first
             Partnership Project  UMTS, LTE, 5G   documents             5G
                                  NR                                    

  **ETSI**   European             European        ETSI standards        GSM
             Telecommunications   telecom,                              
             Standards Institute  founded GSM                           

  **ANSI**   American National    US national     ANSI standards        ANSI T1
             Standards Institute  coordination                          

  **IANA /   Internet Assigned    IP address and  Registries            .gh domain
  ICANN**    Numbers Authority /  domain name                           delegation
             ICANN                allocation                            

  **NCA**    National             **Ghana's       Licences, directives, Spectrum licensing,
             Communications       national        type approval         SIM registration,
             Authority            regulator**                           quality-of-service
                                                                        standards
  ------------------------------------------------------------------------------------------

### The three ITU sectors (frequently asked)

                              ITU
                               |
           +-------------------+-------------------+
           |                   |                   |
         ITU-T               ITU-R               ITU-D
      Telecommunication    Radiocommunication   Development
      Standardization
      wireline, protocols,  spectrum allocation,  helping developing
      codecs, signalling    satellite orbits,     countries build
      e.g. G.711, H.264,    World Radio           telecom capacity
      V.90, X.25            Conferences (WRC)

Memory hook: **T** for **T**elephone and **T**echnical standards, **R**
for **R**adio and spectrum, **D** for **D**evelopment.

> **Ghana connection:** the ITU-R decides globally which frequency bands
> are for mobile. The NCA then decides who in Ghana may use those bands,
> and on what terms. That is exactly why the NCA's July 2026 request for
> applications names the 700 MHz, 2.3 GHz and 3 GHz bands. Those band
> designations came from ITU-R decisions, and the NCA is turning them
> into national licences. This is the layered relationship between
> international standards and national regulation, which is a strong
> point to make in Week 13's regulation essay.

------------------------------------------------------------------------

## 1.8 Formula sheet for Topic 1

    T = 1/f                     period from frequency
    f = 1/T                     frequency from period
    λ = c/f                     wavelength, c = 3 x 10^8 m/s
    B = f_high - f_low          bandwidth of a composite signal
    dB = 10 log10 (P2/P1)       power gain or loss in decibels

**Decibel worked example.** A signal enters a cable at 10 mW and leaves
at 5 mW. `dB = 10 log10 (5/10) = 10 x (-0.301) = -3.01 dB` A loss of
about 3 dB means the power halved. Learn this pair: **3 dB = double or
half the power. 10 dB = ten times.**

------------------------------------------------------------------------

## 1.9 Traps that cost marks

1.  **Bandwidth is in Hz, data rate is in bps.** Never write "bandwidth
    of 10 Mbps" in this exam, even though people say it casually.
2.  **Analog data is not the same as an analog signal.** Check which one
    the question asks about.
3.  **Noise enters at the channel**, not at the source. Draw it that
    way.
4.  **Amplifier ≠ repeater.** Amplifiers boost signal and noise
    together. Regenerative repeaters rebuild clean digital pulses.
5.  **ITU-T vs ITU-R.** Anything about spectrum or satellite orbits is
    ITU-R. Anything about codecs, protocols or wireline is ITU-T.
6.  **IEEE handles layers 1 and 2. IETF handles layer 3 upward.** Wi-Fi
    is IEEE. IP is IETF.
7.  **Full-duplex does not require two cables.** It requires two
    separated channels, which can be by frequency or by time.

------------------------------------------------------------------------

## 1.10 Glossary

  ---------------------------------------------------------------------
  Term                               Definition in one line
  ---------------------------------- ----------------------------------
  Signal                             A physical quantity varying with
                                     time that carries information

  Amplitude                          The strength or height of a signal

  Frequency                          Cycles per second, measured in
                                     hertz

  Period                             Time for one complete cycle, the
                                     reciprocal of frequency

  Phase                              The position of the waveform
                                     relative to time zero

  Spectrum                           The set of frequencies contained
                                     in a signal

  Bandwidth                          The width of that set, in hertz

  Data rate                          Bits transferred per second

  Throughput                         The data rate actually achieved

  Channel                            The path plus medium between
                                     transmitter and receiver

  Guided medium                      A medium that physically confines
                                     the signal, such as fibre

  Unguided medium                    Free space propagation, such as
                                     radio

  Attenuation                        Loss of signal strength over
                                     distance

  Distortion                         Change in signal shape caused by
                                     frequency-dependent delay

  Noise                              Unwanted energy added to a signal

  Baseband                           Transmission at the signal's
                                     original frequencies

  Broadband                          Transmission after shifting the
                                     signal onto a carrier

  Regenerative repeater              A device that decides the bit
                                     value and retransmits a clean
                                     pulse

  Convergence                        Carrying voice, video and data
                                     over one common network

  De jure standard                   A standard formally approved by a
                                     recognised body

  De facto standard                  A standard established by
                                     widespread use
  ---------------------------------------------------------------------

------------------------------------------------------------------------

# Objective questions (Topic 1)

Attempt them all before checking the answers at the bottom.

**Q1.** In a general communication system, noise is normally shown as
entering at the: A. Source B. Transmitter C. Channel D. Destination

**Q2.** A signal has a period of 4 ms. Its frequency is: A. 4 Hz B. 250
Hz C. 400 Hz D. 4 kHz

**Q3.** The bandwidth of a composite signal that contains frequencies
from 500 Hz to 4500 Hz is: A. 500 Hz B. 4000 Hz C. 4500 Hz D. 5000 Hz

**Q4.** Which of the following is measured in hertz? A. Throughput B.
Data rate C. Bandwidth D. Bit error rate

**Q5.** Human voice carried over a normal mobile phone network is an
example of: A. Analog data on an analog signal B. Analog data on a
digital signal C. Digital data on an analog signal D. Digital data on a
digital signal

**Q6.** The main advantage of a regenerative repeater over an amplifier
is that it: A. Increases the bandwidth of the channel B. Removes
accumulated noise by rebuilding the pulse C. Reduces the propagation
delay D. Converts digital signals to analog

**Q7.** A walkie-talkie operates in which transmission mode? A. Simplex
B. Half-duplex C. Full-duplex D. Multiplex

**Q8.** Which body publishes documents known as RFCs? A. ITU B. IEEE C.
IETF D. ISO

**Q9.** Which ITU sector is responsible for allocating radio frequency
spectrum? A. ITU-T B. ITU-R C. ITU-D D. ITU-S

**Q10.** IEEE 802.11 is the standard for: A. Ethernet B. Wireless LAN C.
Bluetooth D. Token Ring

**Q11.** The wavelength of a 1.8 GHz GSM signal in free space is
approximately: A. 1.67 cm B. 16.7 cm C. 1.67 m D. 16.7 m

**Q12.** The standard voice channel in the telephone network is
allocated: A. 3.1 kHz B. 4 kHz C. 8 kHz D. 64 kHz

**Q13.** Which of the following is a de facto standard? A. ITU-T G.711
B. IEEE 802.3 C. TCP/IP in its early years D. ISO 9001

**Q14.** Attenuation refers to: A. Addition of unwanted energy to a
signal B. Loss of signal strength with distance C. Change in signal
shape due to unequal delays D. Reflection of a signal from an obstacle

**Q15.** Which regulator issues telecommunications licences in Ghana? A.
ITU B. NCA C. GIFEC D. Data Protection Commission

**Q16.** A power loss of 3 dB corresponds to approximately: A. One tenth
of the power B. One half of the power C. Double the power D. Three times
the power

**Q17.** Which of the following is NOT an advantage of digital
transmission? A. Easier encryption B. Error detection and correction C.
Lower bandwidth requirement for voice D. Noise immunity through
regeneration

**Q18.** The mathematical technique that decomposes a composite signal
into its constituent sine waves is called: A. Modulation B. Fourier
analysis C. Quantisation D. Multiplexing

**Q19.** 3GPP is the body responsible for standardising: A. Wireless
LANs B. Mobile cellular systems such as LTE and 5G NR C. Optical fibre
connectors D. Internet routing protocols

**Q20.** Guided media differ from unguided media mainly in that guided
media: A. Are always faster B. Physically confine the signal within a
medium C. Cannot carry digital signals D. Require no transmitter

------------------------------------------------------------------------

## Answers with explanations

  -----------------------------------------------------------------------
  Q                       Ans                     Why
  ----------------------- ----------------------- -----------------------
  1                       **C**                   Noise is a property of
                                                  the channel and the
                                                  receiver front end, not
                                                  of the message

  2                       **B**                   f = 1/T = 1/0.004 = 250
                                                  Hz

  3                       **B**                   4500 - 500 = 4000 Hz

  4                       **C**                   Bandwidth is a width of
                                                  frequencies. The other
                                                  three are in bits per
                                                  second or a ratio

  5                       **B**                   Voice is analog data.
                                                  The phone digitises it
                                                  with a codec, so it
                                                  travels as a digital
                                                  signal

  6                       **B**                   It makes a hard
                                                  decision on the bit and
                                                  transmits a fresh
                                                  pulse, discarding noise

  7                       **B**                   Both parties can speak,
                                                  but not at the same
                                                  time, hence "over"

  8                       **C**                   IETF documents are
                                                  RFCs. ITU issues
                                                  Recommendations, IEEE
                                                  issues numbered
                                                  Standards

  9                       **B**                   R for
                                                  Radiocommunication,
                                                  which manages spectrum
                                                  and satellite orbits

  10                      **B**                   802.3 is Ethernet,
                                                  802.15 is Bluetooth,
                                                  802.11 is Wi-Fi

  11                      **B**                   λ = 3x10\^8 / 1.8x10\^9
                                                  = 0.167 m = 16.7 cm

  12                      **B**                   Usable band is 300 to
                                                  3400 Hz (3.1 kHz),
                                                  allocated slot is 4 kHz
                                                  including guard bands

  13                      **C**                   TCP/IP spread through
                                                  use before formal
                                                  endorsement. The others
                                                  were formally approved

  14                      **B**                   A is noise, C is
                                                  distortion, D is
                                                  reflection

  15                      **B**                   The National
                                                  Communications
                                                  Authority regulates and
                                                  licenses the sector

  16                      **B**                   10 log10(0.5) = -3.01
                                                  dB, so 3 dB down is
                                                  half the power

  17                      **C**                   Digitised voice needs
                                                  MORE raw bandwidth, not
                                                  less. That is a
                                                  disadvantage

  18                      **B**                   Fourier analysis moves
                                                  a signal from the time
                                                  domain to the frequency
                                                  domain

  19                      **B**                   3GPP produces the
                                                  Releases that define
                                                  GSM through 5G NR

  20                      **B**                   Confinement within a
                                                  physical medium is the
                                                  defining property
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Exam-style written questions with model answers

**W1. With the aid of a labelled diagram, describe the components of a
general telecommunication system. (6 marks)**

*Model answer:* Draw the five-block diagram with noise entering at the
channel. Then: the **source** produces the message. The **transmitter**
converts the message into a signal suited to the channel, typically by
encoding and modulating it. The **channel** is the medium that carries
the signal and is the point at which noise and impairments are
introduced. The **receiver** performs the inverse of the transmitter,
demodulating and decoding the signal. The **destination** consumes the
recovered message. One mark per correctly labelled block, one for
showing noise at the channel.

**W2. Distinguish between bandwidth and data rate, giving units and one
example of each. (4 marks)**

*Model answer:* Bandwidth is the width of the range of frequencies a
signal occupies or a channel can pass, measured in hertz. A telephone
voice channel has a bandwidth of about 3.1 kHz. Data rate is the number
of bits transferred per second, measured in bits per second. A digitised
voice channel using PCM has a data rate of 64 kbps. They are related,
since a wider bandwidth permits a higher data rate, and Nyquist and
Shannon quantify this relationship, but they are different quantities
with different units.

**W3. Explain, with reference to noise, why digital transmission has
largely replaced analog transmission in long-distance networks. (6
marks)**

*Model answer:* In an analog link, signal strength is restored by
amplifiers. An amplifier cannot distinguish signal from noise, so any
noise picked up on the preceding section is amplified along with the
signal. Noise therefore accumulates cumulatively along the route, and
after many sections the signal-to-noise ratio becomes too poor for the
message to be recovered. In a digital link, regenerative repeaters make
a decision on whether each received pulse represents a 0 or a 1 and then
transmit an entirely new, clean pulse. Provided the noise has not been
severe enough to cause a wrong decision, the noise is discarded at every
repeater rather than accumulated. The received signal at the far end is
therefore effectively identical to the transmitted one regardless of
distance. Digital transmission additionally permits error detection and
correction, encryption, and simple time-division multiplexing, and it
allows voice, video and data to be carried over a single converged
network.

**W4. Differentiate between de jure and de facto standards, and name two
standards bodies relevant to telecommunications, stating the scope of
each. (6 marks)**

*Model answer:* A de jure standard is one formally developed and
approved by a recognised standards organisation. A de facto standard is
one that has become widely accepted through market adoption without such
formal approval, and it may be proprietary or open. Two bodies: the
**ITU**, a United Nations agency whose ITU-T sector standardises
wireline telecommunication protocols and codecs while ITU-R allocates
radio spectrum and satellite orbital positions; and the **IETF**, which
develops internet protocols at the network layer and above and publishes
them as RFCs, for example IP in RFC 791.

**W5. Discuss how the evolution of Ghana's telecommunications sector
since 1994 has affected service delivery and cost. (8 marks)**

*Points to build the answer on:* the state monopoly under P&T gave low
penetration and long waiting lists for fixed lines. Liberalisation from
1994 to 1995 separated Ghana Telecom and licensed private mobile
operators, introducing competition. Mobile technology bypassed the cost
of laying fixed copper to every home, so penetration rose rapidly and
now exceeds 100% by SIM count. The arrival of submarine cables (SAT-3 in
2001, then MainOne, Glo-1 and ACE from 2010 to 2012) replaced expensive
satellite backhaul, greatly reducing wholesale bandwidth cost per Mbps
and enabling affordable retail data. Consolidation followed, with
Vodafone becoming Telecel and AirtelTigo becoming state-owned AT,
leaving three MNOs. Because MTN holds a very large share, the NCA has
designated it as having Significant Market Power and applies asymmetric
price regulation to protect competition. Most recently 5G has been
introduced through a shared wholesale infrastructure model, and in 2026
the NCA opened competitive spectrum licensing in the 700 MHz, 2.3 GHz
and 3 GHz bands. Conclude with the trade-off: competition and cable
capacity lowered prices and expanded access, but rural coverage, quality
of service and affordability of devices remain the constraints.

**W6. A sinusoidal signal has a peak amplitude of 5 V and completes 2000
cycles in one second. Determine its frequency, period, and wavelength in
free space. (4 marks)**

*Model answer:* - f = 2000 Hz - T = 1/f = 1/2000 = 0.5 ms - λ = c/f = (3
x 10\^8)/2000 = 150,000 m = 150 km State the general expression as
`s(t) = 5 sin(2π x 2000 t + φ)`.

------------------------------------------------------------------------

## What to do before moving on

You should be able to, without notes: 1. Draw and label the five-block
system diagram including noise 2. State three parameters of a sine wave
and the relationship between period and frequency 3. Explain the
difference between bandwidth and data rate with correct units 4. Give
all four combinations of analog/digital data and signal with an example
each 5. Explain the amplifier versus regenerative repeater argument 6.
Name ITU, IEEE, IETF and NCA and say precisely what each is responsible
for

Once all six are solid, move to Topic 2, which turns the intuition about
bandwidth into the Nyquist and Shannon equations.
