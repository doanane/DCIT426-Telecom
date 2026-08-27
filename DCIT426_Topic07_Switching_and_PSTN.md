# DCIT 426 Telecommunications
## Topic 7: Switching and the Telephone Network (PSTN)

**Syllabus line (Week 7):** *Circuit vs packet vs message switching; PSTN architecture; signalling (SS7); VoIP fundamentals.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Switching taxonomy | Forouzan Ch. 8; Stallings Ch. 10 |
| Circuit switching and PSTN | Stallings Ch. 10.1 and 10.2 |
| SS7 signalling | Stallings Ch. 10.4 |
| VoIP, SIP, RTP | Forouzan Ch. 28; Stallings Ch. 22 |

---

## 7.1 Why switching exists

**Short answer:** you cannot give every pair of users a dedicated link, so a network of switches creates paths on demand.

```
   Fully connected mesh of n nodes needs n(n-1)/2 links.

   n = 5   ->  10 links       manageable
   n = 100 ->  4,950 links    absurd
   n = 40 million Ghanaian subscribers -> 8 x 10^14 links. Impossible.

   Solution: a hierarchy of SWITCHES.

        [A]   [B]   [C]
          \    |    /
           \   |   /
            [SWITCH]          n links instead of n(n-1)/2
           /   |   \
        [D]   [E]   [F]
```

---

## 7.2 The three switching methods

```
                    SWITCHING
                        |
       +----------------+----------------+
       |                |                |
   CIRCUIT          MESSAGE           PACKET
   switching        switching         switching
   dedicated path   store & forward       |
   for the whole    whole message    +----+----+
   call             (obsolete)       |         |
                                 DATAGRAM   VIRTUAL
                                            CIRCUIT
```

### Circuit switching

**Short answer:** a dedicated physical path is set up end to end before any data flows, held for the whole conversation, and released afterwards.

**Three phases:**
```
1. SETUP        signalling establishes the path across every switch
2. DATA TRANSFER  data flows with no addressing and no per-packet delay
3. TEARDOWN     the path is released for other users
```

```
   [A]===[SW1]===[SW2]===[SW3]===[B]
    the resource on every link is RESERVED for A-B
    even during silence, nobody else can use it
```

- **Advantages:** guaranteed and constant bandwidth, constant and low delay, no jitter, no per-packet header overhead, simple once established
- **Disadvantages:** setup delay before anything can be sent, **capacity wasted during silence** (a typical voice conversation is around 40% silence), inflexible since the rate is fixed, and if any switch on the path fails the entire call drops
- **Used by:** the traditional PSTN, ISDN, and the circuit-switched voice core of GSM

### Message switching

The entire message is stored at each intermediate node and forwarded when a link is free. No path is reserved.

- **Advantages:** links are used efficiently, no setup delay, priority can be applied, and messages can be queued if the destination is unavailable
- **Disadvantages:** each node needs enormous storage for whole messages, delay is large and unpredictable, and it is completely unsuitable for real-time traffic
- **Status:** essentially obsolete for networks, though email is conceptually message switching at the application layer

### Packet switching

The message is divided into small packets, each carrying a header with addressing information, each switched independently.

#### Datagram (connectionless)

```
   Each packet is routed INDEPENDENTLY.

   [A] --pkt1--> [SW1] --> [SW3] --> [B]
   [A] --pkt2--> [SW1] --> [SW2] --> [SW3] --> [B]   (different route)
   [A] --pkt3--> [SW1] --> [SW3] --> [B]

   Packets may arrive OUT OF ORDER or not at all.
   Every packet carries the FULL destination address.
```

- No setup, highly robust since packets route around failures, efficient link usage
- Variable delay, possible reordering and loss, larger headers
- **This is how IP works.** The internet is a datagram network.

#### Virtual circuit (connection-oriented)

```
   A logical path is set up first. Each packet then carries only a
   short VIRTUAL CIRCUIT IDENTIFIER, not a full address.

   [A] --VCI=5--> [SW1] --VCI=8--> [SW2] --VCI=3--> [B]
        switches hold a table mapping incoming VCI to outgoing VCI

   All packets follow the SAME path and arrive IN ORDER.
```

- Ordered delivery, smaller headers, easier to guarantee quality of service
- Setup delay, and a switch failure breaks the circuit
- **Used by:** Frame Relay, ATM, and MPLS in modern carrier networks

### The comparison table (very high exam value)

| Criterion | Circuit | Datagram packet | Virtual circuit packet |
|---|---|---|---|
| Path setup | Required | None | Required |
| Dedicated bandwidth | Yes | No | No |
| Addressing | Only at setup | Full address in every packet | Short VCI per packet |
| Order preserved | Yes | **No** | Yes |
| Delay | Constant, low | Variable | Fairly consistent |
| Jitter | None | High | Low |
| Efficiency during silence | Poor, capacity wasted | Excellent | Excellent |
| Robustness to node failure | Call drops | Reroutes automatically | Circuit breaks |
| Congestion effect | New calls blocked | Existing traffic slows and drops | Slows |
| Example | PSTN, GSM voice | The Internet, IP | ATM, Frame Relay, MPLS |

### Delay comparison diagram

```
CIRCUIT SWITCHING              PACKET SWITCHING
 |                              |
 | setup request                | pkt1 --> stored, forwarded
 |------------------>           |     pkt2 --> stored, forwarded
 |<---- connect ----            |         pkt3 --> ...
 |                              |
 | data flows continuously      | each hop adds a store-and-forward
 | with no per-hop queuing      | delay proportional to packet size
 |                              |
 | teardown                     | no teardown
```

---

## 7.3 How a switch is built

### Space-division switching: the crossbar

```
   Inputs
     |    |    |    |
   --X----X----X----X--   Outputs
     |    |    |    |
   --X----X----X----X--
     |    |    |    |
   --X----X----X----X--

   Each X is a crosspoint that can be closed to connect
   an input to an output.
```

- **n x n crossbar needs n² crosspoints.** For 1000 lines that is a million crosspoints, most of which are never used at once. Expensive and wasteful.
- **Non-blocking**: any free input can always reach any free output

### Multistage switching

Break the large crossbar into three stages of smaller ones.

```
   Stage 1        Stage 2        Stage 3
   +-----+        +-----+        +-----+
   | n x k |----->| N/n x N/n |->| k x n |
   +-----+        +-----+        +-----+
```

- Vastly fewer crosspoints, therefore cheaper
- Introduces the possibility of **blocking**, where a free input cannot reach a free output because all internal paths are busy
- **Clos's theorem:** a three-stage switch is non-blocking if `k >= 2n - 1`, where n is the number of inputs per first-stage switch and k the number of second-stage switches. This is a named result worth quoting.

**Worked example.** Design a three-stage switch for 200 lines using n = 10 inputs per first-stage crossbar.
- Number of first-stage switches = 200/10 = 20
- For non-blocking, k = 2(10) - 1 = **19** second-stage switches
- Crosspoints = 20 x (10 x 19) + 19 x (20 x 20) + 20 x (19 x 10) = 3800 + 7600 + 3800 = **15,200**
- A single crossbar would need 200² = **40,000**. The multistage design saves 62%.

### Time-division switching: TSI (Time Slot Interchange)

Instead of physical crosspoints, incoming TDM time slots are written into RAM and read out in a different order.

```
  Input frame:   [A][B][C][D]
                     |
                RAM + control unit
                     |
  Output frame:  [C][D][A][B]     slots have been INTERCHANGED
```

- No physical crosspoints at all, so it is cheap and fully digital
- Limited by memory access speed, since every slot must be written and read within one frame time
- Real exchanges use **combined time and space switching**, typically in TST or STS arrangements

---

## 7.4 PSTN architecture

**Short answer:** the PSTN is a hierarchical circuit-switched network built around the copper local loop, digital exchanges and a separate signalling network.

```
                    [INTERNATIONAL GATEWAY]
                              |
                       [TRANSIT / TOLL]
                        /            \
              [TANDEM]                [TANDEM]
              /      \                /      \
     [LOCAL EXCHANGE]  [LOCAL EXCHANGE]   ...
        /   |   \
       /    |    \       <-- LOCAL LOOP (copper twisted pair,
    [ph]  [ph]  [ph]         typically 1 to 5 km)
   subscribers
```

| Element | Function |
|---|---|
| **Subscriber line / local loop** | The twisted pair from the home to the exchange. Carries analog voice plus DC for powering the phone and for signalling |
| **Local exchange (end office)** | The first switch. Digitises voice into DS-0 at 64 kbps, provides dial tone, ringing, and billing records |
| **Trunk** | High-capacity link between exchanges, carrying many multiplexed channels (E1, E3, STM-n) |
| **Tandem / toll exchange** | Switches calls between local exchanges without any subscribers of its own |
| **International gateway** | Interconnects with foreign carriers |

### Key facts about the local loop
- Uses two wires, called **tip and ring**
- The exchange supplies about -48 V DC, which is why a basic corded telephone works during a power cut
- Signalling to the exchange is by **loop current** (off-hook closes the loop) and either pulse dialling or **DTMF** tones
- The loop is the reason DSL exists: it is the one wire already reaching every building, and DSL exploits the frequencies above the 4 kHz voiceband (Week 11)

### Numbering: E.164
The ITU-T **E.164** recommendation defines international numbering. A number is at most 15 digits: country code, then national destination code, then subscriber number.

For Ghana, the country code is **+233**, followed by a network or area identifier and the subscriber digits. This is the standard the NCA administers nationally.

---

## 7.5 Signalling and SS7

**Short answer:** signalling is the exchange of control information that sets up, manages and releases calls. It is entirely separate from the voice itself.

### In-band versus out-of-band

| | In-band (channel associated) | Out-of-band (common channel) |
|---|---|---|
| Path | Signalling travels on the same channel as the voice | Signalling travels on a **separate dedicated data network** |
| Speed | Slow, since the voice path must be seized first | Fast, since the signalling network is always available |
| Security | **Vulnerable**: a subscriber can inject tones into the voice path | Subscribers cannot reach the signalling network |
| Features | Very limited | Rich: caller ID, call forwarding, prepaid, roaming, SMS |
| Example | Old R2 and MF tone signalling | **SS7** |

> **The famous vulnerability:** old in-band systems used a 2600 Hz tone to indicate an idle trunk. Because it travelled on the same path as speech, anyone able to generate that tone into the handset could take control of the trunk and place free calls. This history is the standard exam justification for why out-of-band signalling was adopted.

### SS7 (Signalling System No. 7)

SS7 is a packet-switched network that runs **in parallel** with the circuit-switched voice network.

```
   VOICE PATH (circuit switched)
   [Phone] --- [SSP] ============ [SSP] --- [Phone]
                 \                  /
                  \                /       SIGNALLING PATH
                  [STP] ------ [STP]       (packet switched,
                     \          /           completely separate)
                      \        /
                       [SCP database]
```

| SS7 node | Full name | Function |
|---|---|---|
| **SSP** | Service Switching Point | The exchange itself. Originates and terminates signalling messages, and switches the voice circuits |
| **STP** | Signal Transfer Point | A packet router for SS7 messages. Deployed in mated pairs for reliability |
| **SCP** | Service Control Point | A database. Holds toll-free number translation, prepaid balances, subscriber profiles, and in mobile networks the HLR |

### SS7 protocol stack

| Layer | Component | Function |
|---|---|---|
| 1 to 3 | **MTP 1, 2, 3** | Message Transfer Part. Physical links, error control, and routing of signalling messages |
| 3+ | **SCCP** | Signalling Connection Control Part. Global title translation and enhanced addressing |
| 4 | **ISUP** | ISDN User Part. **Sets up, manages and releases trunk circuits for calls.** The core call-control protocol |
| 7 | **TCAP** | Transaction Capabilities Application Part. Query and response to databases, used for prepaid, roaming and SMS |
| 7 | **MAP** | Mobile Application Part. Runs over TCAP. Carries all GSM mobility signalling between HLR, VLR and MSC |

**MAP is the bridge to Week 9.** Every location update and roaming query in GSM is a MAP message riding on SS7.

### Advantages of SS7
- Call setup takes well under a second instead of several seconds
- Trunks are only seized once the called party is confirmed available, so no capacity is wasted on unanswered calls
- Enables all intelligent network services: prepaid billing, number portability, caller ID, roaming, SMS
- Signalling is unreachable from the subscriber, so it is far more secure than in-band

**However:** SS7 was designed when all operators trusted one another. Its lack of authentication is now a well-documented weakness that permits location tracking and SMS interception if an attacker gains access to the signalling network. That is a good point for the Week 13 security question.

---

## 7.6 Traffic engineering: the Erlang

**Short answer:** an Erlang is a dimensionless unit of traffic intensity equal to one circuit occupied continuously for the observation period.

```
A = (number of calls per hour x average call duration in hours)

or equivalently

A = total call minutes in the busy hour / 60
```

**Worked example.** An exchange handles 300 calls in the busy hour with an average duration of 3 minutes.
A = 300 x 3 / 60 = **15 Erlangs**

**Grade of Service (GoS)** is the probability that a call is blocked. A GoS of 0.01 means one call in a hundred is blocked in the busy hour. The **Erlang B formula** relates offered traffic, number of circuits and blocking probability, and is used to decide how many trunks an exchange needs. You do not usually need to evaluate Erlang B by hand, but you should know what it is for.

**Key insight, worth stating in an essay:** circuit-switched networks are dimensioned for the **busy hour** and are deliberately allowed to block. Packet networks instead degrade gracefully by delaying and dropping. This is the fundamental difference in how the two philosophies handle overload.

---

## 7.7 VoIP fundamentals

**Short answer:** VoIP carries voice as IP packets over a packet-switched network instead of over dedicated circuits.

### The pipeline

```
  [Mic] -> [Sample &   ] -> [Codec    ] -> [Packetise ] -> [IP network]
           [ digitise  ]    [ compress]    [ RTP/UDP/IP]        |
                                                                v
  [Speaker] <- [D/A] <- [Decode] <- [Jitter buffer] <- [Receive packets]
```

| Stage | Detail |
|---|---|
| Digitise | 8 kHz sampling, 8-bit PCM, giving 64 kbps |
| Compress | A codec reduces the rate |
| Packetise | Typically 20 ms of audio per packet, so 50 packets per second |
| Transport | **RTP over UDP over IP**. UDP is used because retransmission is useless for real-time audio |
| Jitter buffer | Holds packets briefly to smooth out variable arrival times, at the cost of added delay |

### Codecs

| Codec | Rate | Notes |
|---|---|---|
| **G.711** | 64 kbps | Uncompressed PCM. Best quality, most bandwidth. The PSTN standard |
| **G.729** | 8 kbps | Good quality at one eighth the rate. Widely used on constrained links |
| **G.723.1** | 5.3 or 6.3 kbps | Very low rate, lower quality |
| **Opus** | 6 to 510 kbps | Modern, adaptive, used by WhatsApp and WebRTC |

**Bandwidth in practice is higher than the codec rate**, because every 20 ms packet carries 40 bytes of RTP, UDP and IP headers plus link-layer overhead. G.711 at 20 ms packetisation consumes roughly 80 to 90 kbps per direction on Ethernet, not 64 kbps.

### Signalling protocols

| Protocol | Role |
|---|---|
| **SIP** (Session Initiation Protocol) | Text-based, HTTP-like. Sets up, modifies and tears down sessions. **The dominant standard**. Uses INVITE, ACK, BYE, REGISTER |
| **H.323** | The older ITU-T suite. Binary, more complex, largely superseded |
| **RTP** | Carries the actual media, with sequence numbers and timestamps |
| **RTCP** | Reports on quality: packet loss, jitter, round-trip time |
| **SDP** | Describes the media parameters inside SIP messages |

**Remember the split:** SIP sets up the call, RTP carries the voice. They are separate flows and often take different paths.

### The four quality metrics

| Metric | Target for good voice | Effect if exceeded |
|---|---|---|
| **One-way delay** | Under 150 ms | Speakers start talking over each other |
| **Jitter** | Under 30 ms | Choppy audio, or added buffer delay |
| **Packet loss** | Under 1% | Clipped syllables and dropouts |
| **MOS** (Mean Opinion Score) | 4.0 or above out of 5 | Subjective quality rating |

### VoIP versus PSTN

| | PSTN | VoIP |
|---|---|---|
| Switching | Circuit | Packet |
| Bandwidth per call | Fixed 64 kbps, reserved | Variable, shared, 8 to 90 kbps |
| Quality | Consistently good by design | Depends on network conditions |
| Cost | High, distance-sensitive | Low, largely distance-independent |
| Power | Phone powered from the exchange, works in outages | Needs local power |
| Emergency calls | Location known from the line | Location harder to determine |
| Features | Require network upgrades | Software, easy to add |

> **Ghana context:** VoIP is exactly why calling a relative in London over WhatsApp costs nothing while an international PSTN call is billed per minute. The economic consequence is that operators lost international voice revenue, which is one reason regulators in several African countries, Ghana included, have taken an interest in how over-the-top voice services are treated. That tension is good material for the Week 13 regulation essay.

---

## 7.8 Formula sheet for Topic 7

```
Mesh links = n(n-1)/2
Crossbar crosspoints = n²
Clos non-blocking condition: k >= 2n - 1
Three-stage crosspoints = 2 x (N/n) x (n x k) + k x (N/n)²
Traffic A (Erlangs) = calls per hour x mean holding time in hours
DS-0 = 64 kbps
VoIP packets per second = 1000 / packetisation interval in ms
```

---

## 7.9 Traps that cost marks

1. **Circuit switching wastes capacity during silence.** That is its central weakness. Say it explicitly.
2. **Datagram packets can arrive out of order. Virtual circuit packets cannot.** This is the cleanest way to distinguish them.
3. **A virtual circuit is not a circuit.** No bandwidth is reserved. Only the path is fixed.
4. **SS7 is packet switched even though it controls a circuit-switched network.** Students frequently get this backwards.
5. **ISUP sets up calls, MAP handles mobility, TCAP queries databases.** Do not blur them.
6. **VoIP uses UDP, not TCP.** Retransmitting late audio is worse than losing it.
7. **SIP is signalling, RTP is media.** Two different protocols with two different jobs.
8. **VoIP bandwidth exceeds the codec rate** because of headers. A question asking for actual bandwidth expects you to account for overhead.

---

## 7.10 Glossary

| Term | One-line definition |
|---|---|
| Circuit switching | Establishing a dedicated path for the duration of a call |
| Packet switching | Dividing data into independently forwarded packets |
| Datagram | A packet routed independently with a full destination address |
| Virtual circuit | A pre-established logical path identified by short labels |
| VCI | Virtual circuit identifier carried in each packet |
| Crossbar | A switch matrix with a crosspoint for every input-output pair |
| Blocking | Inability to connect a free input to a free output |
| Clos theorem | The condition k >= 2n - 1 for a non-blocking three-stage switch |
| TSI | Time slot interchange, digital switching by reordering slots in memory |
| Local loop | The copper pair from subscriber to local exchange |
| Local exchange | The first switch, providing dial tone and digitisation |
| Trunk | A high-capacity multiplexed link between exchanges |
| Tandem exchange | A switch connecting other exchanges, with no subscribers |
| E.164 | The ITU-T international telephone numbering plan |
| Signalling | Control information that sets up and releases calls |
| In-band signalling | Signalling carried on the voice path itself |
| SS7 | The out-of-band common channel signalling network |
| SSP, STP, SCP | Service switching point, signal transfer point, service control point |
| ISUP | The SS7 protocol that sets up and releases call circuits |
| MAP | The SS7 application part carrying GSM mobility signalling |
| Erlang | A unit of traffic equal to one circuit fully occupied |
| Grade of service | The probability that a call attempt is blocked |
| VoIP | Voice carried as IP packets |
| SIP | The dominant VoIP session signalling protocol |
| RTP | The protocol carrying VoIP media with sequencing and timestamps |
| Jitter buffer | A receive buffer smoothing variable packet arrival times |
| MOS | Mean opinion score, a subjective voice quality rating out of five |

---

# Objective questions (Topic 7)

**Q1.** In a fully connected mesh of 20 nodes, the number of links required is:
A. 20  B. 40  C. 190  D. 400

**Q2.** The three phases of a circuit-switched connection are:
A. Setup, transfer, teardown
B. Request, route, reply
C. Open, send, close
D. Dial, ring, answer

**Q3.** The principal disadvantage of circuit switching is:
A. Variable delay
B. Capacity is wasted during periods of silence
C. Packets arrive out of order
D. No bandwidth guarantee

**Q4.** In a datagram network, each packet carries:
A. A short virtual circuit identifier
B. The full destination address
C. No addressing at all
D. Only a sequence number

**Q5.** Which switching method can deliver packets out of order?
A. Circuit  B. Virtual circuit  C. Datagram  D. Message

**Q6.** An n x n crossbar switch requires how many crosspoints?
A. n  B. 2n  C. n²  D. n(n-1)/2

**Q7.** By Clos's theorem, a three-stage switch with n = 8 inputs per first-stage crossbar is non-blocking when k equals at least:
A. 8  B. 15  C. 16  D. 64

**Q8.** Time slot interchange performs switching by:
A. Closing physical crosspoints
B. Reordering time slots in memory
C. Changing the carrier frequency
D. Using different codes

**Q9.** The copper pair from a subscriber's home to the local exchange is called the:
A. Trunk  B. Local loop  C. Backbone  D. Tandem

**Q10.** A basic corded telephone works during a power cut because:
A. It has a battery
B. The exchange supplies DC over the local loop
C. It uses solar power
D. It requires no power

**Q11.** The ITU-T recommendation defining international telephone numbering is:
A. E.164  B. G.711  C. H.323  D. Q.931

**Q12.** SS7 is best described as:
A. A circuit-switched signalling system carried in the voice band
B. A packet-switched out-of-band signalling network
C. A modulation scheme
D. A line coding technique

**Q13.** In SS7, the node that acts as a router for signalling messages is the:
A. SSP  B. STP  C. SCP  D. HLR

**Q14.** The SS7 protocol responsible for setting up and releasing call circuits is:
A. MTP  B. SCCP  C. ISUP  D. TCAP

**Q15.** The SS7 application part carrying GSM mobility signalling is:
A. ISUP  B. MAP  C. MTP3  D. SCCP

**Q16.** A major security weakness of in-band signalling was that:
A. It was too slow
B. Subscribers could inject control tones into the voice path
C. It needed too much bandwidth
D. It could not carry caller ID

**Q17.** An exchange carries 600 calls in the busy hour with an average duration of 2 minutes. The offered traffic is:
A. 10 Erlangs  B. 20 Erlangs  C. 30 Erlangs  D. 1200 Erlangs

**Q18.** VoIP media is normally carried over:
A. RTP over UDP  B. RTP over TCP  C. SIP over TCP  D. HTTP

**Q19.** VoIP uses UDP rather than TCP because:
A. UDP is more reliable
B. Retransmitted audio would arrive too late to be useful
C. TCP cannot carry voice
D. UDP has larger headers

**Q20.** For acceptable interactive voice quality, one-way delay should not exceed approximately:
A. 15 ms  B. 50 ms  C. 150 ms  D. 500 ms

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **C** | 20 x 19 / 2 = 190 |
| 2 | **A** | Setup, data transfer, teardown |
| 3 | **B** | The reserved path is idle during pauses but unusable by others |
| 4 | **B** | Independent routing requires the full address in every packet |
| 5 | **C** | Independent routing means different paths and different delays |
| 6 | **C** | One crosspoint per input-output pair |
| 7 | **B** | k >= 2(8) - 1 = 15 |
| 8 | **B** | TSI writes slots to RAM and reads them in a different order |
| 9 | **B** | The local loop, also called the subscriber line |
| 10 | **B** | About -48 V DC is fed from the exchange, which has battery backup |
| 11 | **A** | E.164 defines the numbering plan |
| 12 | **B** | SS7 is a separate packet network controlling a circuit network |
| 13 | **B** | The signal transfer point routes SS7 messages |
| 14 | **C** | ISDN User Part handles call control |
| 15 | **B** | Mobile Application Part, running over TCAP |
| 16 | **B** | The 2600 Hz tone exploit is the classic example |
| 17 | **B** | 600 x 2 / 60 = 20 Erlangs |
| 18 | **A** | RTP over UDP over IP |
| 19 | **B** | Late audio is useless, so retransmission adds delay for no benefit |
| 20 | **C** | Beyond about 150 ms one-way, conversation becomes awkward |

---

# Exam-style written questions with model answers

**W1. Compare circuit switching and packet switching under the headings of setup, bandwidth allocation, delay, efficiency and behaviour under congestion. State one application best served by each. (8 marks)**

*Model answer:* In **circuit switching** a dedicated end-to-end path is established during a setup phase before any user data is sent, and the resources on every link along that path are reserved exclusively for the connection until it is released. Bandwidth allocation is therefore fixed and guaranteed. Delay consists only of propagation and a one-off setup delay, and once established the delay is constant with essentially no jitter. Efficiency is poor for bursty traffic because the reserved capacity remains idle during silences, and a typical voice conversation contains a substantial proportion of silence. Under congestion, new call attempts are simply **blocked**, while existing calls are entirely unaffected. In **packet switching** there is no reservation. Data is divided into packets, each carrying addressing information, and each is forwarded independently or along a pre-established logical path. Bandwidth is shared statistically, so efficiency for bursty traffic is far higher. Delay is variable because each hop involves queuing and store-and-forward processing, which introduces jitter. Under congestion, all traffic is affected: queues lengthen, delay increases and packets are eventually discarded, so quality **degrades gracefully** rather than blocking new users. Circuit switching best serves **traditional telephony**, where a constant low-delay stream is required and the traffic is inherently continuous. Packet switching best serves **internet data traffic such as web browsing and file transfer**, which is highly bursty and tolerant of variable delay.

**W2. Distinguish between datagram and virtual circuit approaches to packet switching. (5 marks)**

*Model answer:* In the **datagram** approach there is no connection establishment. Every packet is treated as an independent unit and carries the complete destination address in its header. Each switch makes an independent forwarding decision using its current routing table, so successive packets belonging to the same conversation may follow different paths, may experience different delays, and may therefore arrive out of order or be lost without the network noticing. The advantage is robustness, since traffic reroutes automatically around failures, and simplicity, since switches hold no per-connection state. In the **virtual circuit** approach a setup phase establishes a logical path through the network, and each switch stores a table entry mapping an incoming virtual circuit identifier and port to an outgoing identifier and port. Packets then carry only the short identifier rather than a full address, which reduces header overhead and lookup cost. All packets of a connection follow the same path, so they arrive in order and experience similar delay, which makes quality of service guarantees far easier to provide. The disadvantages are the setup delay, the per-connection state held in every switch, and the fact that a switch failure breaks the circuit. IP is a datagram network, while ATM, Frame Relay and MPLS are virtual circuit technologies.

**W3. With the aid of a diagram, describe the architecture of the PSTN, explaining the function of the local loop, the local exchange, trunks and tandem exchanges. (8 marks)**

*Model answer:* Draw the hierarchy from subscribers through local exchanges, tandem exchanges, toll exchanges and the international gateway. The **local loop** is the pair of copper wires, known as tip and ring, running from the subscriber's premises to the local exchange, typically one to five kilometres in length. It carries the analog voice signal in the band 300 to 3400 Hz and simultaneously carries direct current supplied by the exchange at about minus 48 volts, which powers the telephone instrument and conveys basic signalling through loop closure and dialling. Because the exchange has battery backup, a corded telephone continues to work during a mains power failure. The **local exchange**, also called the end office, is the first switching point. It provides dial tone, detects dialled digits, digitises the analog voice using pulse code modulation at 8000 samples per second with 8 bits per sample to produce a 64 kbps DS-0 stream, applies ringing current to the called line, and generates billing records. **Trunks** are high-capacity links between exchanges. They carry many conversations simultaneously using time division multiplexing, in Ghana as E1 systems at 2.048 Mbps carrying thirty voice channels, and at higher levels as E3 or as SDH streams over optical fibre. **Tandem or toll exchanges** have no directly connected subscribers; their sole purpose is to switch calls between local exchanges, which prevents the need for a direct trunk between every pair of local exchanges and thereby drastically reduces the total number of trunks required in the network. At the top of the hierarchy the **international gateway** interconnects the national network with foreign carriers.

**W4. Explain what SS7 is, why out-of-band signalling replaced in-band signalling, and describe the roles of the SSP, STP and SCP. (8 marks)**

*Model answer:* Signalling System Number 7 is a **common channel, out-of-band signalling system**: a packet-switched data network operating entirely separately from, and in parallel with, the circuit-switched voice network whose calls it controls. In **in-band signalling**, control information was carried as tones within the same voice channel used for speech. This had three serious problems. It was slow, because the entire voice path had to be seized and held while digits were passed forward exchange by exchange, wasting trunk capacity on calls that were never answered. It was limited, because the signalling capacity available within a voice channel could not support services such as caller identification, number portability or prepaid billing. Most importantly it was **insecure**, since any subscriber able to generate the appropriate tones into the handset, most famously the 2600 Hz idle-trunk tone, could take direct control of network trunks and obtain free calls. Out-of-band signalling removes the signalling entirely from the subscriber's reach. The **SSP, or Service Switching Point**, is the exchange itself; it originates, terminates and acts upon signalling messages and performs the actual switching of the voice circuits. The **STP, or Signal Transfer Point**, is a specialised packet router that relays SS7 messages between nodes; STPs are always deployed in mated pairs with diverse links so that no single failure can isolate part of the network. The **SCP, or Service Control Point**, is a database queried by exchanges to obtain the information needed to complete a call, such as the translation of a toll-free number to a real destination, a subscriber's prepaid balance, or, in a mobile network, the subscriber's current location held in the Home Location Register.

**W5. Describe how a VoIP call is established and carried, naming the protocols involved, and discuss three factors that determine the perceived quality of the call. (8 marks)**

*Model answer:* A VoIP call begins with **signalling**, most commonly using the Session Initiation Protocol. The calling user agent registers its current address with a SIP registrar, then sends an INVITE message towards the called party, which is routed by SIP proxy servers. The INVITE carries a Session Description Protocol body listing the codecs and transport addresses the caller supports. The called user agent responds with a provisional ringing indication and then a 200 OK containing its own SDP answer, after which the caller sends an ACK and the session is established. Media then flows **directly between the endpoints**, independently of the signalling path. The analog voice is sampled at 8 kHz, encoded by a codec such as G.711 at 64 kbps or G.729 at 8 kbps, packetised typically into 20 millisecond segments giving fifty packets per second, and carried in the **Real-time Transport Protocol over UDP over IP**. UDP is chosen rather than TCP because a retransmitted audio packet would arrive far too late to be played out, so retransmission adds delay without adding value. RTP supplies sequence numbers and timestamps that allow the receiver to detect loss and to reconstruct correct timing, while **RTCP** carries periodic reports of loss, jitter and round-trip time. At the receiver a **jitter buffer** holds arriving packets briefly so that variable network delay can be smoothed into an even playout stream. The call is released by a SIP BYE message. Three factors determine perceived quality. **One-way delay** must remain below roughly 150 milliseconds; beyond this, the natural turn-taking of conversation breaks down and speakers begin to talk over one another. **Jitter**, the variation in packet arrival time, must be small enough for the jitter buffer to absorb, since a buffer large enough to absorb heavy jitter itself adds delay, creating a direct trade-off. **Packet loss** should be below about one percent; above this, syllables are clipped and intelligibility falls sharply, although modern codecs apply packet loss concealment to mask isolated losses. These are usually summarised as a Mean Opinion Score, where a value of four or above out of five is regarded as good quality.

---

## Checkpoint before Topic 8

You should be able to, without notes:
1. Draw and explain the switching taxonomy tree
2. Fill in the circuit versus datagram versus virtual circuit comparison table
3. Apply Clos's theorem and compute crosspoint savings
4. Draw the PSTN hierarchy and name every element
5. Draw the SS7 architecture and state what SSP, STP and SCP each do
6. Explain why in-band signalling was insecure, with the 2600 Hz example
7. Trace a VoIP call from INVITE to BYE and name every protocol involved
