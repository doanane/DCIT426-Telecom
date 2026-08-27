# DCIT 426 Telecommunications
## Topic 12: Telecom Network Security, Regulation and Management

**Syllabus line (Week 13):** *Threats and countermeasures, encryption in transit; NCA regulation in Ghana, spectrum policy, QoS/SLAs, network management.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Network security fundamentals | Stallings Ch. 21 and 22; Forouzan Ch. 31 and 32 |
| Encryption and PKI | Forouzan Ch. 30 and 31 |
| QoS mechanisms | Forouzan Ch. 24, Section 24.4; Stallings Ch. 19 |
| Regulation | NCA publications, Electronic Communications Act 2008 (Act 775), ITU-D materials |

This is the topic where the outline's learning outcome 6 lives: *"Discuss telecom regulation, security, and emerging trends, and assess their impact on Ghana's digital economy."* It is the most essay-heavy topic in the course, and the most likely source of a long-answer question.

---

# PART A: SECURITY

## 12.1 The security objectives

**The CIA triad, plus two:**

| Objective | Meaning | Telecom example |
|---|---|---|
| **Confidentiality** | Only authorised parties can read the information | Air-interface encryption on a mobile call |
| **Integrity** | Information is not altered undetectably in transit | Message authentication codes on signalling |
| **Availability** | The service is there when needed | Resilience against DDoS and fibre cuts |
| **Authentication** | Parties are who they claim to be | SIM authentication against the AuC |
| **Non-repudiation** | A party cannot deny having sent a message | Digital signatures on transactions |

**AAA** in operator networks means **Authentication** (who are you), **Authorisation** (what may you do), and **Accounting** (what did you use, for billing).

---

## 12.2 Threats, mapped onto the layers you have studied

```
   PHYSICAL LAYER      fibre cuts, tower vandalism, cable theft,
                       jamming, wiretapping
        |
   LINK / RADIO        IMSI catchers, rogue access points,
                       eavesdropping on unencrypted air interfaces
        |
   NETWORK             IP spoofing, BGP hijacking, DDoS,
                       DNS poisoning
        |
   SIGNALLING          SS7 attacks: location tracking,
                       SMS and call interception
        |
   APPLICATION /       SIM swap fraud, SIM box fraud, phishing,
   SUBSCRIBER          mobile money social engineering
        |
   ORGANISATIONAL      insider threat, weak vendor security,
                       supply chain compromise
```

### The threats worth knowing in detail

| Threat | Mechanism | Countermeasure |
|---|---|---|
| **Eavesdropping** | Passive interception of the radio path or a tapped cable | Encryption in transit. Fibre is harder to tap than copper or radio |
| **IMSI catcher / false base station** | A fake BTS with a strong signal attracts handsets, forces them to 2G where authentication is one-way, and disables encryption | **Mutual authentication** (introduced in UMTS), and in 5G the **SUCI** concealed subscriber identifier which encrypts the permanent identity |
| **SS7 attacks** | SS7 has no authentication between operators. An attacker with signalling access can send MAP queries to locate a subscriber, redirect calls, or intercept SMS including one-time passwords | Signalling firewalls, filtering of MAP categories at network borders, and migration to the more secure 5G service-based architecture |
| **SIM swap fraud** | An attacker socially engineers or bribes a retail agent into porting a victim's number to a new SIM, then intercepts one-time passwords for banking and mobile money | Strong customer verification, delay periods after a swap, SIM-change detection APIs offered to banks, and moving away from SMS as an authentication factor |
| **SIM box fraud** | International calls are routed over the internet and injected into the local network through banks of ordinary SIMs, so they terminate as local calls, bypassing the international interconnect rate | Test-call generation, traffic pattern analysis to detect SIMs with abnormal call profiles, and regulator-operated interconnect clearing houses. **This is a major and specific issue in Ghana**, since it deprives both operators and government of interconnect revenue |
| **DDoS** | Overwhelming a service with traffic from many sources | Scrubbing centres, rate limiting, anycast, upstream filtering |
| **BGP hijacking** | Announcing routes for address space you do not own, diverting traffic | RPKI route origin validation, route filtering, IRR registration |
| **Physical attacks** | Fibre cuts by road contractors or thieves, tower battery theft, vandalism | Route diversity and ring topologies, physical security, community engagement |
| **Insider threat** | Staff with legitimate access misusing subscriber data or provisioning | Least privilege, separation of duties, logging and audit, background checks |

> **Exam point on availability:** in many African networks the largest single cause of outage is not a cyberattack at all but **fibre cuts from road construction and battery theft from base station sites**. An answer on threats that mentions only cryptographic attacks is incomplete. Physical and operational security matter at least as much.

---

## 12.3 Encryption in transit

### Symmetric versus asymmetric

| | Symmetric | Asymmetric (public key) |
|---|---|---|
| Keys | One shared secret key for both encryption and decryption | A public key and a mathematically related private key |
| Speed | **Fast**, suitable for bulk data | Slow, roughly a thousand times slower |
| Key distribution | **The hard problem**: how do both parties get the key securely? | Solved: the public key can be published freely |
| Algorithms | AES, 3DES, ChaCha20 | RSA, Diffie-Hellman, ECC |
| Use | Encrypting the actual traffic | Key exchange, digital signatures, certificates |

**The standard hybrid pattern, which is what almost every real protocol does:**
```
1. Use ASYMMETRIC cryptography to authenticate the parties and
   agree on a shared secret (key exchange).
2. Derive a SYMMETRIC session key from that secret.
3. Encrypt all the actual data with the fast symmetric algorithm.
```

### Hashing and digital signatures

| Primitive | Provides | Example |
|---|---|---|
| **Hash function** | Integrity. A fixed-length fingerprint that changes completely if one bit changes | SHA-256 |
| **MAC / HMAC** | Integrity plus authentication, using a shared secret | HMAC-SHA256 |
| **Digital signature** | Integrity, authentication and non-repudiation. Hash the message and encrypt the hash with the sender's private key | RSA or ECDSA signature |
| **PKI and certificates** | Binds a public key to an identity, vouched for by a Certificate Authority | X.509 certificates, the basis of TLS |

### Where encryption sits in the stack

| Protocol | Layer | Protects |
|---|---|---|
| **TLS** | Transport (above TCP) | Application traffic. HTTPS, VoIP signalling over TLS, email |
| **IPsec** | Network | All IP traffic between two endpoints or gateways. The basis of site-to-site **VPNs** |
| **MACsec (802.1AE)** | Data link | A single Ethernet hop, used to protect links inside operator infrastructure |
| **A5 / UEA / NEA** | Radio link | The air interface in GSM, UMTS and 5G respectively |
| **SRTP** | Application | VoIP media streams |

**The critical distinction to state in an exam:** air-interface encryption protects **only the radio hop between handset and base station**. Beyond that the traffic is in the clear on the operator's transport network unless separately protected. **End-to-end encryption**, as used by messaging applications, protects the content from everyone including the operator. These are different guarantees, and confusing them is a common error.

### Security across the mobile generations

| Generation | Authentication | Encryption | Weakness |
|---|---|---|---|
| GSM (2G) | **One-way** (network authenticates subscriber only) | A5/1, A5/2 over the air | False base station attacks. A5/2 broken, A5/1 weak |
| UMTS (3G) | **Mutual** | KASUMI (UEA1) | Better, but still exposes the IMSI at initial attach |
| LTE (4G) | Mutual | SNOW 3G, AES | IMSI still sent in the clear at initial attach |
| 5G | Mutual | AES, SNOW 3G, ZUC | **SUCI conceals the permanent identifier**, closing the IMSI catcher gap |

---

# PART B: REGULATION

## 12.4 Why telecommunications is regulated

**Short answer:** because spectrum is a finite public resource, because networks are natural monopolies in parts, and because universal access is a public good that a purely commercial market will not deliver.

| Rationale | Explanation |
|---|---|
| **Scarce resource** | Radio spectrum is finite and shared. Uncoordinated use produces interference that makes it useless for everyone |
| **Natural monopoly tendencies** | The cost of building duplicate networks is enormous, so markets tend towards concentration |
| **Consumer protection** | Subscribers cannot individually verify network quality, billing accuracy or data handling |
| **Universal access** | Commercial operators will not serve unprofitable rural areas without an obligation or a subsidy |
| **National security and public safety** | Emergency services, lawful interception, critical infrastructure resilience |
| **Interconnection** | Networks must interconnect for the system to work, and a dominant player has an incentive not to |

---

## 12.5 The Ghanaian regulatory framework

### The National Communications Authority

The **NCA** is Ghana's regulator, established under the National Communications Authority Act and operating principally under the **Electronic Communications Act, 2008 (Act 775)**.

**Functions:**
| Function | What it involves |
|---|---|
| **Licensing** | Issuing and enforcing licences for network operators, ISPs, broadcasters and VSAT providers |
| **Spectrum management** | Planning the national frequency allocation table, assigning frequencies, monitoring for interference |
| **Type approval** | Certifying that equipment may be connected to networks or sold in Ghana |
| **Numbering** | Administering the E.164 numbering plan under +233 |
| **Quality of service** | Setting and monitoring QoS standards, publishing results, imposing sanctions |
| **Competition** | Designating operators with **Significant Market Power** and applying asymmetric remedies |
| **Consumer protection** | Complaint handling, tariff transparency, SIM registration enforcement |

### The legislative stack

| Instrument | Covers |
|---|---|
| **Electronic Communications Act, 2008 (Act 775)** | The core licensing, spectrum and regulatory framework |
| **National Communications Authority Act, 2008 (Act 769)** | Establishes the NCA and its powers |
| **Electronic Transactions Act, 2008 (Act 772)** | Electronic records, signatures, cybercrime offences |
| **Data Protection Act, 2012 (Act 843)** | Personal data processing, establishes the Data Protection Commission |
| **Cybersecurity Act, 2020 (Act 1038)** | Establishes the Cyber Security Authority, critical information infrastructure protection, incident reporting |

**Note the institutional split:** the NCA regulates communications, the **Data Protection Commission** regulates personal data, and the **Cyber Security Authority** handles cybersecurity and critical infrastructure. An operator in Ghana answers to all three. Naming this correctly is a mark-earning detail.

### Significant Market Power

When one operator's share is large enough to allow it to act independently of competitors and customers, the regulator may designate it as having **SMP** and impose **asymmetric obligations** that do not apply to smaller players: price controls, obligations to publish reference interconnection offers, non-discrimination requirements, and accounting separation. **MTN holds this designation in Ghana.** The purpose is to prevent the dominant operator from using its scale to foreclose competition, for example by setting on-net prices far below off-net prices so that subscribers are locked in by network effects.

### Universal access

**GIFEC**, the Ghana Investment Fund for Electronic Communications, administers a universal service fund financed by levies on operators, used to subsidise infrastructure in commercially unviable areas: rural telephony sites, community ICT centres and connectivity for schools. This is the standard policy instrument for the market failure identified above.

### SIM registration

Ghana has conducted mandatory SIM registration linked to the national identity card. The stated rationale is crime reduction and fraud prevention, particularly against SIM box fraud and mobile money fraud. **The critique worth including in a balanced answer:** registration creates a large centralised database of identity and communications data, raising data protection concerns; it can exclude people who lack identity documents; and the evidence that it reduces crime is contested. A good essay presents both the rationale and the critique.

---

## 12.6 Spectrum policy

### Assignment methods

| Method | Description | Trade-off |
|---|---|---|
| **Administrative ("beauty contest")** | The regulator evaluates applications against stated criteria and assigns | Allows policy goals such as rural rollout to be weighted, but is slower and less transparent, and can be contested |
| **Auction** | Highest qualifying bidder wins | Transparent and reveals the market value, but can raise costs that are passed to consumers, and risks the winner's curse |
| **Hybrid** | Qualification against criteria, then a price or auction among the qualified | The common modern approach, and broadly the shape of the NCA's recent Request for Applications process |
| **Licence-exempt** | Anyone may use, subject to power limits | Wi-Fi bands. Enables innovation but offers no interference protection |

### Key spectrum policy concepts

| Concept | Meaning |
|---|---|
| **Technology neutrality** | A licence specifies the band, not the technology, so an operator may refarm 900 MHz from GSM to LTE without a new licence. Ghana introduced this to address competition concerns |
| **Refarming** | Reassigning a band from an older technology to a newer one as demand shifts |
| **Spectrum sharing** | Allowing multiple users in a band under coordination rules, for example dynamic access to underused bands |
| **Rollout obligations** | Conditions attached to a licence requiring coverage of a stated percentage of population or specified districts within a stated period |
| **Spectrum caps** | Limits on how much spectrum a single operator may hold, to preserve competition |
| **Harmonisation** | Aligning national band plans with ITU-R regional allocations so that equipment and devices are available at scale |

**Why harmonisation matters practically:** if Ghana allocated a band that no global manufacturer supports, no handsets would exist for it. Aligning with ITU-R decisions and regional plans is what makes affordable devices available, and it is the direct link between the international standards bodies from Topic 1 and national policy here.

---

## 12.7 Quality of Service and SLAs

### QoS parameters

| Parameter | Definition | Matters most for |
|---|---|---|
| **Throughput** | Sustained data rate delivered | File transfer, video |
| **Latency** | One-way or round-trip delay | Voice, gaming, trading |
| **Jitter** | Variation in delay | Voice and video |
| **Packet loss** | Fraction of packets not delivered | Everything, but especially voice |
| **Availability** | Percentage of time the service is usable | All services |

**Availability arithmetic worth memorising:**

| Availability | Downtime per year |
|---|---|
| 99% | 3.65 days |
| 99.9% ("three nines") | 8.76 hours |
| 99.99% | 52.6 minutes |
| 99.999% ("five nines") | **5.26 minutes** |

### How QoS is delivered in an IP network

| Mechanism | How it works |
|---|---|
| **Over-provisioning** | Build so much capacity that congestion never occurs. Crude but often the cheapest answer in a core network |
| **IntServ / RSVP** | Reserve resources per flow end to end. Precise but does not scale, since routers hold state per flow |
| **DiffServ** | Mark each packet with a class (DSCP), and have routers apply per-hop behaviours to classes rather than flows. **Scales, and is what is actually deployed** |
| **MPLS traffic engineering** | Steer labelled traffic along explicitly chosen paths with reserved bandwidth |
| **Queue management** | Priority queuing, weighted fair queuing, and active queue management such as RED to prevent buffer bloat |
| **Traffic policing and shaping** | Enforcing an agreed rate at the network edge |

**The core insight:** you cannot create bandwidth with QoS. QoS decides **who suffers when there is not enough**. If a link is persistently congested, QoS lets you protect voice at the expense of bulk downloads, but it does not solve the capacity shortage.

### Service Level Agreements

An SLA is a contractual commitment from a provider to a customer specifying:
- **Metrics**: availability, latency, jitter, loss, throughput
- **Measurement method**: how and where the metrics are measured, and over what period
- **Targets**: the committed value for each metric
- **Remedies**: service credits or penalties if targets are missed
- **Exclusions**: force majeure, scheduled maintenance windows, customer-caused faults

**A regulatory SLA is different from a commercial one.** The NCA sets **minimum quality of service standards** that operators must meet across the network, monitors them through drive testing and network measurement, publishes the results, and may impose financial penalties for persistent failure. This is a consumer protection instrument covering all subscribers, whereas a commercial SLA is a private contract benefiting one enterprise customer.

---

## 12.8 Network management

### The FCAPS model (ITU-T TMN)

This is a standard framework and a reliable exam answer.

| Letter | Area | Activities |
|---|---|---|
| **F** | **Fault management** | Detect, isolate, log and correct faults. Alarm correlation, root cause analysis, trouble ticketing |
| **C** | **Configuration management** | Track and control device configuration, inventory, software versions, provisioning of services |
| **A** | **Accounting management** | Usage measurement for billing and for internal cost allocation |
| **P** | **Performance management** | Collect and analyse metrics, capacity planning, trend analysis against SLA thresholds |
| **S** | **Security management** | Access control, key management, audit logs, incident response |

### Tools and architecture

| Element | Role |
|---|---|
| **SNMP** | The standard protocol for polling device counters and receiving traps. Managers, agents and MIBs. **Use SNMPv3, since v1 and v2c send the community string in the clear** |
| **NetFlow / IPFIX** | Traffic flow records for analysis and anomaly detection |
| **Syslog** | Centralised event logging |
| **NMS** | Network management system, the software presenting topology, alarms and performance |
| **NOC** | Network Operations Centre, the staffed facility monitoring the network around the clock |
| **OSS / BSS** | Operations Support Systems handle the network itself. Business Support Systems handle customers, billing, ordering and CRM |

### The TMN layered model

```
   Business Management Layer     strategy, financials
        |
   Service Management Layer      SLAs, service activation, customer view
        |
   Network Management Layer      end-to-end network view
        |
   Element Management Layer      individual vendor equipment
        |
   Network Element Layer         the actual switches, routers, base stations
```

**Automation trend worth naming:** modern operators increasingly use **intent-based networking** and **self-organising networks (SON)**, in which the network configures, optimises and heals itself. In radio networks SON handles automatic neighbour relation, handover parameter optimisation and cell outage compensation, tasks that were previously manual and did not scale to networks with tens of thousands of cells.

---

## 12.9 Traps that cost marks

1. **Air-interface encryption is not end-to-end encryption.** Say precisely what each protects.
2. **GSM authentication is one-way, UMTS onwards is mutual.** This underpins the IMSI catcher discussion.
3. **SS7's weakness is the absence of inter-operator authentication**, not weak cryptography. There is essentially no cryptography to be weak.
4. **QoS does not create capacity.** It allocates scarcity.
5. **DiffServ scales, IntServ does not.** State why: per-class versus per-flow state.
6. **The NCA, the Data Protection Commission and the Cyber Security Authority are different bodies** with different mandates.
7. **SMP designation brings asymmetric obligations**, meaning rules that apply to the dominant operator only.
8. **Auctions are not automatically better than administrative assignment.** Give both sides.
9. **Physical threats such as fibre cuts and battery theft** are a leading cause of unavailability. Include them.
10. **Five nines is 5.26 minutes per year**, not per month. Get the arithmetic right.

---

## 12.10 Glossary

| Term | One-line definition |
|---|---|
| CIA triad | Confidentiality, integrity and availability |
| AAA | Authentication, authorisation and accounting |
| IMSI catcher | A false base station used to intercept mobile traffic |
| SUCI | The 5G concealed subscriber identifier that hides the permanent identity |
| SS7 attack | Abuse of unauthenticated signalling to track or intercept subscribers |
| SIM swap fraud | Fraudulently porting a victim's number to an attacker's SIM |
| SIM box fraud | Terminating international calls as local calls to evade interconnect fees |
| DDoS | Distributed denial of service |
| BGP hijacking | Announcing routes for address space one does not own |
| Symmetric encryption | Encryption using one shared secret key |
| Asymmetric encryption | Encryption using a public and private key pair |
| Hash function | A one-way fixed-length fingerprint of data |
| Digital signature | A hash encrypted with the sender's private key, giving non-repudiation |
| PKI | Public key infrastructure, binding keys to identities via certificates |
| TLS | Transport layer security, protecting application traffic |
| IPsec | Network-layer security, the basis of site-to-site VPNs |
| End-to-end encryption | Encryption where only the endpoints, not the operator, can read the content |
| NCA | Ghana's National Communications Authority |
| Act 775 | The Electronic Communications Act 2008 |
| SMP | Significant Market Power, triggering asymmetric regulation |
| GIFEC | Ghana's universal access fund |
| Type approval | Certification that equipment may be used or sold in a country |
| Technology neutrality | Licensing a band without specifying the technology used in it |
| Refarming | Reassigning spectrum from an older technology to a newer one |
| Rollout obligation | A licence condition requiring specified coverage within a period |
| Spectrum cap | A limit on how much spectrum one operator may hold |
| QoS | The set of parameters describing delivered service quality |
| DiffServ | Class-based QoS using per-hop behaviours, which scales |
| IntServ | Per-flow resource reservation, which does not scale |
| SLA | A contractual commitment to defined service levels with remedies |
| FCAPS | Fault, configuration, accounting, performance and security management |
| SNMP | The simple network management protocol |
| NOC | Network operations centre |
| OSS / BSS | Operations and business support systems |
| SON | Self-organising network, automating radio configuration and optimisation |

---

# Objective questions (Topic 12)

**Q1.** The CIA triad in security stands for:
A. Control, integrity, access
B. Confidentiality, integrity, availability
C. Cipher, identity, authentication
D. Confidentiality, identity, availability

**Q2.** An IMSI catcher works by:
A. Cracking the encryption key
B. Impersonating a base station and forcing handsets onto a weakly authenticated connection
C. Stealing the SIM card
D. Flooding the network with traffic

**Q3.** The 5G feature that closes the IMSI catcher vulnerability is:
A. AES encryption  B. SUCI concealed subscriber identifier  C. Network slicing  D. Massive MIMO

**Q4.** The fundamental weakness of SS7 exploited in signalling attacks is:
A. Weak encryption algorithms
B. The absence of authentication between interconnected operators
C. Its low bandwidth
D. Its use of copper cabling

**Q5.** SIM box fraud harms operators by:
A. Overloading base stations
B. Terminating international calls as local calls, bypassing interconnect fees
C. Stealing subscriber data
D. Cutting fibre cables

**Q6.** Symmetric encryption is preferred for bulk data because it is:
A. More secure  B. Much faster than asymmetric encryption  C. Easier to distribute keys for  D. Older

**Q7.** In a typical secure protocol, asymmetric cryptography is used mainly for:
A. Encrypting all the data
B. Key exchange and authentication
C. Compression
D. Error correction

**Q8.** A digital signature provides confidentiality, integrity and:
A. Availability  B. Non-repudiation  C. Compression  D. Redundancy

**Q9.** IPsec operates at which layer?
A. Physical  B. Data link  C. Network  D. Application

**Q10.** Air-interface encryption in a mobile network protects:
A. The entire path from caller to called party
B. Only the radio hop between handset and base station
C. Only the core network
D. Nothing

**Q11.** GSM's authentication is described as one-way because:
A. Only the network authenticates the subscriber
B. Only the subscriber authenticates the network
C. Authentication occurs once only
D. There is no authentication

**Q12.** Ghana's principal telecommunications regulator is the:
A. Data Protection Commission  B. National Communications Authority  C. Cyber Security Authority  D. GIFEC

**Q13.** The core statute governing electronic communications licensing in Ghana is:
A. Act 772  B. Act 775  C. Act 843  D. Act 1038

**Q14.** Designating an operator as having Significant Market Power results in:
A. Its licence being revoked
B. Asymmetric obligations applying to it that do not apply to smaller operators
C. Additional spectrum being granted to it
D. Exemption from quality of service rules

**Q15.** GIFEC's role is to:
A. Regulate data protection
B. Fund electronic communications infrastructure in commercially unviable areas
C. Auction spectrum
D. Prosecute cybercrime

**Q16.** Technology neutrality in spectrum licensing means:
A. Any operator may use any band freely
B. The licence specifies the band but not the technology deployed in it
C. All technologies must be used equally
D. No licence is required

**Q17.** An availability of 99.99% corresponds to annual downtime of about:
A. 5 minutes  B. 53 minutes  C. 8.8 hours  D. 3.6 days

**Q18.** DiffServ scales better than IntServ because DiffServ:
A. Uses less bandwidth
B. Maintains state per traffic class rather than per individual flow
C. Requires no configuration
D. Works only in the core

**Q19.** In the FCAPS model, the "P" stands for:
A. Provisioning  B. Performance  C. Protocol  D. Policy

**Q20.** SNMPv3 is preferred over SNMPv2c because v2c:
A. Is slower
B. Sends the community string in the clear, providing no real security
C. Cannot poll counters
D. Is proprietary

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Confidentiality, integrity, availability |
| 2 | **B** | It exploits one-way authentication, typically by downgrading to 2G |
| 3 | **B** | SUCI encrypts the permanent identifier at initial attach |
| 4 | **B** | SS7 assumed a closed, trusted operator community |
| 5 | **B** | International traffic is disguised as on-net local traffic |
| 6 | **B** | Roughly a thousand times faster, hence its use for bulk data |
| 7 | **B** | It solves key distribution, then symmetric takes over |
| 8 | **B** | Only the holder of the private key could have produced it |
| 9 | **C** | IPsec secures IP packets, hence network layer |
| 10 | **B** | Beyond the base station the traffic is on the operator's transport network |
| 11 | **A** | The subscriber cannot verify the network, hence the attack |
| 12 | **B** | The NCA licenses and regulates communications |
| 13 | **B** | Electronic Communications Act, 2008 (Act 775) |
| 14 | **B** | Asymmetric remedies apply to the dominant operator only |
| 15 | **B** | It is the universal access fund |
| 16 | **B** | This is what permits refarming without a new licence |
| 17 | **B** | 0.01% of 525,600 minutes is 52.6 minutes |
| 18 | **B** | Per-class per-hop behaviour avoids per-flow state in every router |
| 19 | **B** | Fault, Configuration, Accounting, Performance, Security |
| 20 | **B** | The community string is effectively a plaintext password |

---

# Exam-style written questions with model answers

**W1. Identify five categories of threat to a mobile operator's network, giving a mechanism and a countermeasure for each. (10 marks)**

*Model answer:* **Radio interception and false base stations.** An attacker deploys equipment impersonating a legitimate base station with a strong signal, attracting nearby handsets and, exploiting the fact that GSM authenticates the subscriber to the network but not the network to the subscriber, forcing a downgrade to 2G with encryption disabled so that calls, messages and the subscriber's permanent identity can be captured. The countermeasure is mutual authentication, introduced with UMTS and retained in LTE and 5G, together with 5G's concealed subscriber identifier which encrypts the permanent identity at initial attach, and operator policy disabling 2G fallback where feasible. **Signalling attacks on SS7.** Because SS7 was designed for a small community of mutually trusting national operators, it contains essentially no authentication between interconnected networks, so an attacker who obtains signalling access can issue Mobile Application Part queries to determine a subscriber's location, redirect their calls, or intercept SMS messages including one-time passwords used for banking. The countermeasure is a signalling firewall at the network border filtering inappropriate MAP message categories by origin, together with migration towards the more securely designed 5G service-based architecture. **Subscriber-level fraud, notably SIM swap.** An attacker socially engineers or bribes a retail agent into transferring a victim's number to a new SIM, then intercepts authentication codes for banking and mobile money. Countermeasures include strengthened identity verification at the point of swap, a mandatory delay before the new SIM becomes active for financial services, provision of SIM-change notification interfaces to banks, and reducing reliance on SMS as an authentication factor. **Interconnect fraud, notably SIM boxes.** International calls are carried over the internet to a device in Ghana holding many ordinary SIMs and injected into the local network as on-net calls, so that the operator and the state lose the international termination rate. Countermeasures include test-call generation to detect calls arriving with incorrect origin information, statistical analysis of SIMs exhibiting abnormal call patterns such as very high outbound volume with no inbound traffic, and regulator-operated interconnect monitoring. **Availability attacks, both cyber and physical.** These range from distributed denial of service against the operator's internet-facing services and BGP route hijacking, to fibre cuts by road contractors and theft of batteries and generators from base station sites, the latter being in practice a leading cause of outage in the region. Countermeasures include traffic scrubbing and upstream filtering, route origin validation using RPKI, physical site security, and network design employing ring topologies and diverse routing so that no single cable cut isolates a region.

**W2. Explain the difference between symmetric and asymmetric cryptography, describe how they are combined in a typical secure session, and distinguish air-interface encryption from end-to-end encryption. (8 marks)**

*Model answer:* **Symmetric cryptography** uses a single secret key for both encryption and decryption, with algorithms such as AES. It is computationally efficient and therefore suitable for encrypting large volumes of data in real time, but it presents the key distribution problem: both parties must somehow come to share the secret without an eavesdropper obtaining it. **Asymmetric or public key cryptography** uses a mathematically related key pair in which a public key may be freely published while the corresponding private key is kept secret, with algorithms such as RSA, Diffie-Hellman and elliptic curve schemes. It solves key distribution and additionally enables digital signatures, but it is roughly three orders of magnitude slower and is therefore impractical for bulk data. Real protocols combine them in a **hybrid scheme**: asymmetric cryptography is used at the start of a session to authenticate the parties, typically by verifying an X.509 certificate issued by a trusted certificate authority, and to perform a key exchange establishing a shared secret; a symmetric session key is then derived from that secret and used to encrypt all subsequent traffic quickly. This is exactly the structure of the TLS handshake. **Air-interface encryption** protects only the radio hop between the handset and the base station, using algorithms such as A5 in GSM or AES-based schemes in LTE and 5G. Once traffic reaches the base station it is decrypted and travels across the operator's backhaul and core network, where it is protected only by whatever separate measures the operator applies. The operator can therefore read the content, which is what makes lawful interception technically possible. **End-to-end encryption**, as implemented by messaging applications, encrypts the content at the sending device and decrypts it only at the receiving device, so no intermediate party, including the operator and the application provider, can read it. The two protect against quite different adversaries: air-interface encryption protects against someone listening to the radio path, while end-to-end encryption protects against everyone in between including the network itself. Conflating the two is a common and costly error.

**W3. Explain the rationale for regulating the telecommunications sector, and describe the principal functions of the National Communications Authority in Ghana. (10 marks)**

*Model answer:* Telecommunications is regulated for several distinct reasons. First, **radio spectrum is a finite, shared natural resource**: uncoordinated use produces mutual interference that renders the resource useless for everyone, so an authority must plan allocations and assign frequencies exclusively. Second, parts of the sector exhibit **natural monopoly characteristics**, since the fixed cost of constructing a network is very large relative to the marginal cost of serving an additional subscriber, so markets tend towards concentration and a dominant firm can foreclose competition, for example by refusing reasonable interconnection or by pricing on-net calls far below off-net calls to exploit network effects. Third, **consumers cannot individually verify** network quality, billing accuracy or the handling of their personal data, so a regulator must set and enforce standards on their behalf. Fourth, **universal access is a public good** that a purely commercial market will not deliver, because serving sparse rural populations is unprofitable, so an obligation or a subsidy mechanism is required. Fifth, **national security, emergency services and critical infrastructure resilience** require obligations that commercial incentives alone would not produce. In Ghana these functions are exercised by the **National Communications Authority**, established under Act 769 and operating principally under the **Electronic Communications Act, 2008 (Act 775)**. Its functions include **licensing** network operators, internet service providers, broadcasters and satellite service providers, and enforcing licence conditions; **spectrum management**, comprising maintenance of the national frequency allocation table in harmony with ITU-R allocations, assignment of frequencies to users, and monitoring for unauthorised use and interference; **type approval**, certifying that communications equipment may be imported, sold and connected to networks in Ghana; **numbering administration** under the ITU-T E.164 plan within the +233 country code; **quality of service regulation**, setting minimum standards, measuring performance through drive testing and network monitoring, publishing results and imposing sanctions for persistent failure; **competition regulation**, including designation of operators with Significant Market Power and the imposition of asymmetric remedies such as price control and non-discrimination obligations, a designation currently applied to MTN; and **consumer protection**, covering tariff transparency, complaint resolution and enforcement of SIM registration. It is important to note that the NCA does not act alone: the **Data Protection Commission** established under Act 843 regulates the processing of personal data, and the **Cyber Security Authority** established under the Cybersecurity Act 2020 (Act 1038) is responsible for cybersecurity and the protection of critical information infrastructure, so a Ghanaian operator is accountable to three separate regulators with distinct mandates.

**W4. Compare administrative assignment and auctioning as methods of allocating spectrum, and discuss what conditions a regulator might attach to a licence. (8 marks)**

*Model answer:* Under **administrative assignment**, sometimes called a comparative selection or beauty contest, applicants submit proposals which the regulator evaluates against published criteria such as technical capability, financial standing, proposed coverage and pricing commitments, and the licence is awarded to the applicant judged best against those criteria. Its advantage is that it allows the regulator to weight public policy objectives directly, for instance prioritising rural coverage or new entry over the ability to pay, and it avoids the risk that a very high auction price is recovered through elevated retail tariffs or leaves the winner without capital to build the network. Its disadvantages are that evaluation is inherently discretionary, which invites disputes and allegations of favouritism, the process can be slow, and it does not reveal the market value of the resource, so the spectrum may be assigned below its economic worth. Under **auctioning**, qualified applicants bid and the highest bidder wins. Its advantages are transparency, speed, and efficient allocation in the economic sense, since the party that values the spectrum most highly is normally the one that can use it most productively, and it generates revenue for the state. Its disadvantages are that the outcome favours incumbents with the deepest pockets, which can entrench concentration; that very high prices may be passed to consumers or leave insufficient capital for network construction; and that in a thin market with few bidders an auction may raise little revenue while still delaying the process. Most modern regulators, including the NCA in its recent spectrum licensing process, use a **hybrid** in which applicants must first meet published eligibility and technical criteria and then compete on price or through a structured selection procedure, and eligibility may be deliberately widened to include new entrants and wholly nationally owned firms in order to promote competition. Whichever method is used, a regulator typically attaches conditions including **rollout obligations** specifying the percentage of population or the named districts that must be covered within stated periods, with penalties for failure; **spectrum caps** limiting total holdings so that no operator can accumulate a dominant share of the resource; **quality of service standards** that the licensee must meet; **technology neutrality**, so that the licensee may deploy any suitable technology in the band and may refarm it as demand evolves; **interconnection and, where applicable, wholesale access obligations**; **licence duration and renewal terms**; and requirements relating to type approval, lawful interception capability and reporting.

**W5. Discuss quality of service in a converged IP network. Explain why QoS mechanisms are necessary, describe two mechanisms, and explain the relationship between a commercial SLA and regulatory QoS standards. (10 marks)**

*Model answer:* In a converged network, a single IP infrastructure carries traffic with radically different requirements: voice needs one-way delay below roughly 150 milliseconds, jitter below about 30 milliseconds and packet loss below about one percent, but needs only tens of kilobits per second; bulk file transfer needs high throughput but is entirely indifferent to delay and jitter; interactive video needs both moderate throughput and low jitter. Because these classes share the same links and routers, congestion at any point causes queues to build, and without differentiation every class suffers equally, which means the delay-sensitive traffic that degrades most visibly is damaged in order to protect traffic that would not have noticed the delay. QoS mechanisms exist to make that allocation deliberate rather than accidental. The essential point to grasp is that **QoS does not create capacity**; it determines who bears the shortfall when capacity is insufficient. On a persistently congested link, QoS can protect voice at the expense of downloads, but it cannot make both work, and where capacity can be added cheaply, **over-provisioning** is often the more economical answer, which is why core networks frequently rely on it. Two mechanisms illustrate the design space. **Integrated Services with RSVP** reserves resources for each individual flow along its end-to-end path, so every router on the path holds state for every flow traversing it and can offer a hard guarantee. This is precise but does not scale, since a core router carrying hundreds of thousands of simultaneous flows cannot maintain per-flow state and signalling. **Differentiated Services** instead marks each packet with a six-bit DSCP value identifying a small number of traffic classes, and each router applies a per-hop behaviour to each class, such as expedited forwarding for voice and assured forwarding for business data. Routers hold state per class rather than per flow, typically fewer than a dozen classes, so the scheme scales to any network size at the cost of offering statistical rather than absolute guarantees. In practice DiffServ is what is deployed, often reinforced by **MPLS traffic engineering** which steers labelled traffic along explicitly chosen paths with reserved bandwidth. A **commercial SLA** is a private contract between a provider and a specific customer specifying the metrics committed to, how and where they are measured, the target values, the service credits or penalties payable if targets are missed, and the exclusions such as scheduled maintenance and force majeure. It benefits only the contracting customer, who is usually an enterprise with the bargaining power to negotiate it. A **regulatory quality of service standard**, by contrast, is imposed by the regulator on all licensed operators for the benefit of all subscribers, including ordinary consumers who have no bargaining power. In Ghana the NCA sets minimum standards for parameters such as call setup success rate, dropped call rate and data throughput, measures compliance independently through drive testing and network measurement, publishes the results so that consumers can compare operators, and may impose financial penalties for persistent non-compliance. The two are complementary: the regulatory floor protects the general subscriber base, while commercial SLAs allow enterprise customers to purchase guarantees above that floor.

---

## Final checkpoint for the course

You should be able to, without notes:
1. Name five threat categories with a mechanism and countermeasure for each
2. Explain the hybrid symmetric and asymmetric pattern and the TLS handshake shape
3. Distinguish air-interface encryption from end-to-end encryption precisely
4. State GSM's one-way authentication weakness and how UMTS and 5G addressed it
5. Give the rationale for regulation and list the NCA's functions
6. Name Acts 775, 772, 843 and 1038 and what each covers
7. Compare auction and administrative spectrum assignment with licence conditions
8. Explain SMP and asymmetric remedies with the Ghanaian example
9. Distinguish IntServ from DiffServ and explain why one scales
10. Recite FCAPS and give two activities under each letter
