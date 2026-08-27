# DCIT 426 Telecommunications
## Topic 6: Data Link Layer and Error Control

**Syllabus line (Week 6):** *Framing, error detection (parity, checksum, CRC), ARQ, flow control, HDLC.*

**Textbook mapping**
| Sub-topic | Where to read |
|---|---|
| Framing, byte and bit stuffing | Forouzan Ch. 11, Section 11.1; Tanenbaum Ch. 3.1 |
| Error detection and correction | Forouzan Ch. 10; Stallings Ch. 6.1 to 6.4 |
| Flow control and ARQ | Forouzan Ch. 11.2 and 11.3; Tanenbaum Ch. 3.3 |
| HDLC | Forouzan Ch. 11.5; Stallings Ch. 7.2 |

This topic is the most "computational" after Topic 2. CRC division and Hamming code are both step-by-step procedures that you can score full marks on with practice.

---

## 6.1 What the data link layer does

**Short answer:** the data link layer turns the raw, error-prone bit pipe provided by the physical layer into a reliable link between two directly connected nodes.

```
   +---------------------------+
   |      NETWORK LAYER        |   packets
   +---------------------------+
   |     DATA LINK LAYER       |   <-- frames. THIS TOPIC.
   |   +-------------------+   |
   |   | LLC sublayer      |   |   flow control, error control,
   |   +-------------------+   |   interface to the network layer
   |   | MAC sublayer      |   |   who may transmit and when,
   |   +-------------------+   |   physical addressing
   +---------------------------+
   |     PHYSICAL LAYER        |   bits
   +---------------------------+
```

Its five services:
1. **Framing**: grouping bits into meaningful units
2. **Physical addressing**: MAC addresses so the frame reaches the right adapter
3. **Error control**: detecting and recovering from corrupted frames
4. **Flow control**: stopping a fast sender from overwhelming a slow receiver
5. **Access control**: deciding who transmits on a shared medium

---

## 6.2 Framing

**Short answer:** framing marks where each frame begins and ends so the receiver can separate one message from the next.

### Method 1: Character count
The first field states how many characters follow.
**Fatal weakness:** if the count field itself is corrupted, the receiver loses all frame boundaries for the rest of the transmission and cannot resynchronise.

### Method 2: Byte (character) stuffing
Use a special **flag byte** to mark the start and end. If the flag pattern appears inside the data, insert an **escape byte (ESC)** before it.

```
Original data containing a flag:
   A   FLAG   B   ESC   C

After byte stuffing:
   FLAG | A  ESC FLAG  B  ESC ESC  C | FLAG
          ^^^^^^^^^^      ^^^^^^^
          ESC inserted    ESC itself must
          before the      also be escaped
          data FLAG
```

The receiver removes any ESC and treats the following byte as data, not as a delimiter.

### Method 3: Bit stuffing (used by HDLC)
The flag is the bit pattern **01111110**. To guarantee this pattern never appears in the data, the sender **inserts a 0 after any five consecutive 1s** in the payload. The receiver removes any 0 that follows five 1s.

```
   Original data:   0 1 1 1 1 1 1 0 1 1 1 1 1 1 1 1 0

   Sender inserts a 0 after every run of five 1s:

   Original:  0 11111 1 0 11111 111 0
   Stuffed:   0 11111 0 1 0 11111 0 111 0
                     ^          ^
                  stuffed    stuffed

   Result:    0 1 1 1 1 1 0 1 0 1 1 1 1 1 0 1 1 1 0

   Now the pattern 01111110 can only ever be a genuine flag.
```

> **Exam procedure for bit stuffing:** scan left to right, count consecutive 1s, and the moment you reach five, write a 0 and reset the count. Do it slowly and mark your insertions. Marks are lost through carelessness, not through misunderstanding.

---

## 6.3 Types of error

| Type | Description | Typical cause |
|---|---|---|
| **Single-bit error** | Exactly one bit changed | Rare in serial transmission. Common in parallel buses |
| **Burst error** | Two or more bits changed within a span of B bits, not necessarily all of them | **The normal case in real links.** Impulse noise, fading, scratches |

**Why bursts dominate:** noise events last much longer than one bit interval. At 1 Mbps a 1 ms noise spike corrupts 1000 bit positions. This is exactly why CRC, which is designed to catch bursts, is preferred over parity, which is not.

---

## 6.4 Error detection: the four techniques

The idea in all of them is **redundancy**: the sender adds extra bits computed from the data, and the receiver recomputes and compares.

```
   SENDER                                    RECEIVER
   data ---> [generator] ---> data+redundancy ---> [checker] ---> accept
                                                        |          or
                                                        +------> reject
```

### 6.4.1 Parity (VRC, Vertical Redundancy Check)

Add one bit so the total number of 1s is even (even parity) or odd (odd parity).

**Example, even parity.** Data 1011001 has four 1s, already even, so parity bit = **0**. Transmit 10110010.
Data 1010001 has three 1s, so parity bit = **1**. Transmit 10100011.

- Detects **all single-bit errors** and **all odd numbers of errors**
- **Fails on any even number of errors**, which is a serious limitation
- Overall detection probability for random burst errors is only about 50%

### 6.4.2 LRC (Longitudinal Redundancy Check), two-dimensional parity

Arrange the data into a table of rows and compute a parity bit per column, sent as an extra row.

```
   11100111   ->  row 1
   11011101   ->  row 2
   00111001   ->  row 3
   10101001   ->  row 4
   --------
   10101010   ->  LRC row (column parities)
```

- Catches all bursts up to the row length
- **Fails when two bits in the same two columns and same two rows are corrupted**, since the errors cancel in both directions

### 6.4.3 Checksum

Used by IP, TCP and UDP at higher layers.

**Sender:** divide the data into n-bit words, add them using one's complement arithmetic, then take the one's complement of the sum. That is the checksum.
**Receiver:** add all the words including the checksum. If the result is all 1s (or its complement is all 0s), accept.

**Worked example (4-bit words, data 7, 11, 12, 0, 6):**
- 7 + 11 = 18. In 4-bit one's complement, 18 = 10010, so wrap the carry: 0010 + 1 = 3
- 3 + 12 = 15
- 15 + 0 = 15
- 15 + 6 = 21 = 10101, wrap: 0101 + 1 = 6
- Sum = 6, checksum = one's complement of 6 = **9**
- Send 7, 11, 12, 0, 6, 9
- Receiver adds: 6 + 9 = 15 = 1111, all ones, so **accept**

- Simple and fast in software
- Weaker than CRC: it misses errors that cancel each other out, for example if one word increases by 1 and another decreases by 1

### 6.4.4 CRC (Cyclic Redundancy Check) — the one that matters

**Short answer:** treat the bit string as a polynomial, divide it by an agreed generator polynomial using modulo-2 arithmetic, and append the remainder.

**Modulo-2 arithmetic:** addition and subtraction are both just **XOR**. There are no carries and no borrows. This is why CRC is easy in hardware, needing only shift registers and XOR gates.

```
   XOR truth table
   0 XOR 0 = 0
   0 XOR 1 = 1
   1 XOR 0 = 1
   1 XOR 1 = 0
```

### The CRC procedure, step by step

```
1. Generator has n+1 bits.  Append n ZEROS to the data.
2. Divide (data + zeros) by the generator using modulo-2 division (XOR).
3. The REMAINDER is the CRC. It has n bits.
4. Transmit: original data followed by the CRC.
5. Receiver divides the WHOLE received string by the same generator.
   Remainder = 0  -> accept.   Remainder ≠ 0  -> error.
```

### Fully worked CRC example

**Data = 1101011011, Generator = 10011** (5 bits, so n = 4)

Step 1: append 4 zeros. Dividend = **11010110110000**

Step 2: divide by 10011 using XOR.

```
                      1100001010
            ----------------------
   10011 )  11010110110000
            10011
            -----
             10011                 (bring down)
             10011
             -----
              00001 01             (bring down, leading zeros -> quotient 0)
                 10011
                 -----
                  10110
                  10011
                  -----
                   01010 0
                     10011
                     -----
                      01110 0
                        1110  <- remainder has fewer bits than generator
            Remainder = 1110
```

Step 3: **CRC = 1110**

Step 4: **Transmit 1101011011 1110**

Step 5: at the receiver, divide 11010110111110 by 10011. If nothing was corrupted, the remainder is **0000** and the frame is accepted.

> **Exam technique for CRC:** work in a clearly ruled column. At each step, if the leading bit of the current partial remainder is 1, XOR with the generator; if it is 0, XOR with zeros (which is the same as bringing down the next bit and writing 0 in the quotient). Keep going until the remaining bits are fewer than the generator length. Do not attempt it mentally.

### Standard CRC polynomials

| Name | Bits | Polynomial | Used by |
|---|---|---|---|
| CRC-8 | 8 | x^8 + x^2 + x + 1 | ATM header |
| CRC-10 | 10 | x^10 + x^9 + x^5 + x^4 + x + 1 | ATM AAL |
| CRC-16 | 16 | x^16 + x^12 + x^5 + 1 | HDLC, older modems |
| CRC-32 | 32 | (long) | **Ethernet, Wi-Fi, ZIP, PNG** |

### CRC detection strength (memorise this list)

A well-chosen CRC with n check bits detects:
- **All single-bit errors**
- **All double-bit errors**, if the generator has at least three terms
- **All odd numbers of errors**, if the generator contains the factor (x + 1)
- **All burst errors of length ≤ n**
- Longer bursts with probability approaching 1 - 2^-n. For CRC-32, the miss probability is about 1 in 4 billion.

**This is why CRC is used everywhere and parity is used almost nowhere.**

---

## 6.5 Error correction: Hamming code

Detection tells you something is wrong. **Correction** lets you fix it without retransmission.

**Short answer:** Hamming code inserts parity bits at power-of-two positions, each checking a specific subset of bit positions, so that the pattern of failed checks points directly at the position of the bad bit.

**How many redundancy bits?** Find the smallest r satisfying:
```
2^r >= m + r + 1
```
where m is the number of data bits.

For m = 7: 2^4 = 16 >= 7 + 4 + 1 = 12. So **r = 4**, giving an 11-bit codeword.

### Construction for m = 7 data bits (the classic (11,7) example)

```
Position:  1  2  3  4  5  6  7  8  9 10 11
Content:  r1 r2 d1 r4 d2 d3 d4 r8 d5 d6 d7

r1 (pos 1) checks positions:  1,3,5,7,9,11   (bit 0 of position set)
r2 (pos 2) checks positions:  2,3,6,7,10,11  (bit 1 of position set)
r4 (pos 4) checks positions:  4,5,6,7        (bit 2 of position set)
r8 (pos 8) checks positions:  8,9,10,11      (bit 3 of position set)
```

**Why this works:** every position number written in binary tells you which parity bits check it. Position 11 = 1011 in binary, so it is checked by r8, r2 and r1. When the receiver recomputes the parities, the failing checks written as a binary number give exactly the position of the error.

### Worked correction example

Suppose the receiver recomputes and finds r1 fails, r2 fails, r4 passes, r8 fails.
Write them with r8 as the most significant bit:
```
r8 r4 r2 r1
 1  0  1  1   =  binary 1011  =  decimal 11
```
**Bit position 11 is wrong.** Flip it and the data is correct.

If all checks pass, the syndrome is 0000 and there is no single-bit error.

- Hamming code corrects **one bit** and detects **two bits** (with an extra overall parity bit)
- It cannot correct burst errors on its own, but **interleaving** spreads a burst across many codewords so each codeword sees only a single-bit error, which is exactly how CD players and mobile systems handle bursts

---

## 6.6 FEC versus ARQ

| | FEC, Forward Error Correction | ARQ, Automatic Repeat reQuest |
|---|---|---|
| Method | Send enough redundancy to correct errors at the receiver | Detect errors and ask for retransmission |
| Return channel | Not needed | **Required** |
| Overhead | Constant, whether errors occur or not | Low when the link is clean, high when noisy |
| Delay | Constant and low | Variable, at least one round trip per retransmission |
| Suited to | One-way links, satellite, broadcast, real-time voice where retransmission is useless | Terrestrial data links, TCP, where delay is tolerable |

Modern systems use **hybrid ARQ (HARQ)**, which combines both: FEC is applied first, and if decoding still fails a retransmission is requested, with the receiver combining the old and new copies rather than discarding the old one. **LTE and 5G both use HARQ.**

---

## 6.7 Flow control and ARQ protocols

### Stop-and-Wait ARQ

Send one frame, wait for its acknowledgement, then send the next.

```
   Sender                    Receiver
     |---- Frame 0 --------->|
     |<--------- ACK 1 ------|
     |---- Frame 1 --------->|   X  (lost)
     |     (timeout)         |
     |---- Frame 1 --------->|   (retransmit)
     |<--------- ACK 0 ------|
```

- Sequence numbers alternate 0, 1, 0, 1 (one bit is enough)
- **Simple, but disastrously inefficient on long or fast links**, because the sender is idle for a whole round-trip time after every frame

**Efficiency** = Tt / (Tt + 2Tp), where Tt is transmission time and Tp is propagation time.

**Worked example.** A 1 Mbps link with 20 ms one-way propagation sends 1000-bit frames.
- Tt = 1000/10^6 = 1 ms
- Efficiency = 1 / (1 + 40) = **2.4%**. The link is idle 97.6% of the time.

### Sliding window: Go-Back-N ARQ

The sender may have up to **N** unacknowledged frames outstanding. If a frame is lost, the sender retransmits that frame **and every frame after it**.

```
   Sender window size N = 4
     |-- 0 -->|
     |-- 1 -->|  X lost
     |-- 2 -->|  (receiver discards, out of order)
     |-- 3 -->|  (receiver discards)
     |<- ACK0 |
     | timeout on 1
     |-- 1 -->|  retransmit 1, 2 AND 3
     |-- 2 -->|
     |-- 3 -->|
```

- Receiver is simple: it accepts frames only in order and needs no buffer
- Wasteful when the error rate is high, since correctly received frames are thrown away
- **Sender window ≤ 2^m - 1** where m is the number of sequence-number bits

### Selective Repeat ARQ

Only the specific damaged frame is retransmitted.

```
     |-- 0 -->|
     |-- 1 -->|  X lost
     |-- 2 -->|  buffered
     |-- 3 -->|  buffered
     |<- NAK1 |
     |-- 1 -->|  ONLY frame 1 retransmitted
     |        |  receiver reorders and delivers 1,2,3
```

- Most efficient use of bandwidth
- Receiver must **buffer out-of-order frames and reorder them**, so it is more complex
- **Both windows ≤ 2^(m-1)**, half the sequence space, otherwise ambiguity arises

### Comparison

| | Stop-and-Wait | Go-Back-N | Selective Repeat |
|---|---|---|---|
| Sender window | 1 | 2^m - 1 | 2^(m-1) |
| Receiver window | 1 | 1 | 2^(m-1) |
| Receiver buffering | None | None | Required |
| Retransmits | One frame | The frame and all after it | Only the damaged frame |
| Complexity | Lowest | Medium | Highest |
| Best for | Very short links | Low error rates | High error rates, long links |

**Window size needed to fill a link** = bandwidth-delay product / frame size. This connects directly to Topic 2.

---

## 6.8 HDLC

**Short answer:** HDLC is the bit-oriented data link protocol from which most modern link protocols descend.

### Station types and modes

| Station | Role |
|---|---|
| Primary | Issues commands, controls the link |
| Secondary | Responds to the primary |
| Combined | Can both command and respond |

| Mode | Configuration | Use |
|---|---|---|
| **NRM** Normal Response Mode | One primary, one or more secondaries. Secondary may transmit only when polled | Multipoint links |
| **ABM** Asynchronous Balanced Mode | Two combined stations, either may initiate | **Point-to-point links. The common mode** |

### Frame format

```
 +--------+---------+---------+-------------+--------+--------+
 |  FLAG  | ADDRESS | CONTROL | INFORMATION |  FCS   |  FLAG  |
 |01111110| 8 bits  | 8 or 16 |  variable   | 16/32  |01111110|
 +--------+---------+---------+-------------+--------+--------+
```

| Field | Purpose |
|---|---|
| Flag | 01111110, marks frame boundaries. Bit stuffing guarantees uniqueness |
| Address | Identifies the secondary station |
| Control | Determines the frame type and carries sequence numbers |
| Information | The payload, present only in I-frames |
| FCS | Frame Check Sequence, a **CRC** over address, control and information |

### The three frame types (from the control field)

```
   Control field first bits:

   0 . . .  ->  I-FRAME   (Information)
                carries user data + N(S) send sequence + N(R) ack

   1 0 . .  ->  S-FRAME   (Supervisory)
                no data. Carries control:
                  RR   Receive Ready        (positive ack)
                  RNR  Receive Not Ready    (flow control, pause)
                  REJ  Reject               (Go-Back-N)
                  SREJ Selective Reject     (Selective Repeat)

   1 1 . .  ->  U-FRAME   (Unnumbered)
                link management: SABM, UA, DISC, FRMR
```

**Memory hook: I carries data, S carries control, U sets up and tears down the link.**

**PPP** (Point-to-Point Protocol) is HDLC's descendant, adds a protocol field so it can carry multiple network-layer protocols, and adds LCP for link setup and NCP for network-layer configuration. It is the protocol behind dial-up and much of DSL.

---

## 6.9 Formula sheet for Topic 6

```
Hamming redundancy:   2^r >= m + r + 1
CRC:                  append n zeros, n = generator length - 1
Stop-and-wait efficiency = Tt / (Tt + 2Tp)
Go-Back-N sender window <= 2^m - 1
Selective Repeat windows <= 2^(m-1)
Window needed to fill link = bandwidth-delay product / frame size
Tt = frame size / bandwidth
Tp = distance / propagation speed
```

---

## 6.10 Traps that cost marks

1. **CRC uses modulo-2 arithmetic, so XOR only.** No borrowing, no carrying.
2. **Append n zeros where n = generator length minus 1**, not the generator length itself.
3. **Bit stuffing inserts a 0 after five 1s, not after six.** The flag has six 1s, so five triggers the stuff.
4. **Parity detects but never corrects.** Hamming corrects.
5. **Go-Back-N and Selective Repeat have different window limits.** 2^m - 1 versus 2^(m-1).
6. **The FCS in HDLC is a CRC**, not a checksum, and not a parity bit.
7. **ARQ requires a return channel.** It is useless for one-way broadcast, which is why satellite broadcast uses FEC.
8. **Burst errors are the normal case in real links.** Do not build an answer around single-bit errors as if they were typical.

---

## 6.11 Glossary

| Term | One-line definition |
|---|---|
| Frame | The data link layer's unit of transmission |
| Framing | Marking where each frame begins and ends |
| Bit stuffing | Inserting a 0 after five 1s so the flag pattern is unique |
| Byte stuffing | Inserting an escape byte before an accidental flag byte in data |
| Single-bit error | Exactly one bit changed |
| Burst error | Several bits corrupted within a short span |
| Redundancy | Extra bits added to allow error detection or correction |
| Parity | A single bit making the total count of ones even or odd |
| Checksum | A sum-based detection code used by IP, TCP and UDP |
| CRC | Cyclic redundancy check, a polynomial division remainder |
| Generator polynomial | The agreed divisor used in CRC |
| Modulo-2 arithmetic | Arithmetic where addition and subtraction are XOR |
| Syndrome | The pattern of failed parity checks in a Hamming code |
| Hamming distance | The number of bit positions in which two codewords differ |
| FEC | Forward error correction, fixing errors without retransmission |
| ARQ | Automatic repeat request, retransmitting on detected error |
| HARQ | Hybrid ARQ, combining FEC with retransmission and soft combining |
| Sliding window | A protocol allowing several unacknowledged frames in flight |
| Go-Back-N | ARQ that retransmits the damaged frame and all subsequent frames |
| Selective Repeat | ARQ that retransmits only the damaged frame |
| HDLC | The bit-oriented data link protocol using 01111110 flags |
| FCS | Frame check sequence, the CRC field in an HDLC frame |
| I, S, U frames | Information, supervisory and unnumbered HDLC frame types |

---

# Objective questions (Topic 6)

**Q1.** The data link layer's unit of data is called a:
A. Packet  B. Frame  C. Segment  D. Datagram

**Q2.** In HDLC bit stuffing, a 0 is inserted after:
A. Three consecutive 1s  B. Four consecutive 1s  C. Five consecutive 1s  D. Six consecutive 1s

**Q3.** The HDLC flag pattern is:
A. 01111110  B. 10000001  C. 11111111  D. 01010101

**Q4.** Simple parity checking fails to detect:
A. Single-bit errors  B. An odd number of errors  C. An even number of errors  D. All errors

**Q5.** In CRC, the arithmetic used for division is:
A. Ordinary binary with carries  B. Modulo-2 using XOR  C. Decimal  D. One's complement

**Q6.** If the generator polynomial has 5 bits, the number of zeros appended to the data is:
A. 3  B. 4  C. 5  D. 6

**Q7.** At the receiver, a CRC-protected frame is accepted if the remainder is:
A. All ones  B. Zero  C. Equal to the generator  D. Equal to the data

**Q8.** A CRC with n check bits detects all burst errors of length:
A. Less than n/2  B. Less than or equal to n  C. Exactly n  D. Any length

**Q9.** For 7 data bits, the number of Hamming redundancy bits required is:
A. 2  B. 3  C. 4  D. 5

**Q10.** In a Hamming code, redundancy bits are placed at positions that are:
A. Prime numbers  B. Powers of two  C. Even numbers  D. The first r positions

**Q11.** If the Hamming syndrome computes to binary 0101, the erroneous bit is at position:
A. 4  B. 5  C. 9  D. 101

**Q12.** ARQ cannot be used on a pure broadcast satellite link because it requires:
A. More bandwidth  B. A return channel  C. Digital modulation  D. A CRC

**Q13.** In Go-Back-N with m sequence-number bits, the maximum sender window is:
A. 2^m  B. 2^m - 1  C. 2^(m-1)  D. m

**Q14.** In Selective Repeat, the receiver must:
A. Discard out-of-order frames
B. Buffer out-of-order frames and reorder them
C. Send no acknowledgements
D. Use stop-and-wait

**Q15.** The main disadvantage of Go-Back-N is that:
A. It requires large receiver buffers
B. Correctly received frames after a lost frame are discarded and resent
C. It cannot detect errors
D. It has no sequence numbers

**Q16.** Stop-and-wait ARQ performs badly on:
A. Short, slow links  B. Links with a large bandwidth-delay product  C. Error-free links  D. Half-duplex links

**Q17.** Which HDLC frame type carries user data?
A. I-frame  B. S-frame  C. U-frame  D. F-frame

**Q18.** An RNR supervisory frame indicates:
A. Positive acknowledgement
B. The receiver is temporarily unable to accept more frames
C. A frame was rejected
D. The link is being disconnected

**Q19.** The FCS field in an HDLC frame contains:
A. A parity bit  B. A checksum  C. A CRC  D. The frame length

**Q20.** Hybrid ARQ as used in LTE combines:
A. Parity and checksum
B. Forward error correction with retransmission and soft combining
C. Two CRCs
D. Bit stuffing and byte stuffing

---

## Answers with explanations

| Q | Ans | Why |
|---|---|---|
| 1 | **B** | Packets are layer 3, frames are layer 2 |
| 2 | **C** | Five ones triggers the stuff so six can never appear in data |
| 3 | **A** | 01111110, six ones flanked by zeros |
| 4 | **C** | Two errors cancel each other in the parity count |
| 5 | **B** | XOR, with no carries or borrows |
| 6 | **B** | n = generator length - 1 = 4 |
| 7 | **B** | A zero remainder means no detected error |
| 8 | **B** | Any burst up to the length of the CRC is caught |
| 9 | **C** | 2^4 = 16 >= 7 + 4 + 1 = 12, and r = 3 fails |
| 10 | **B** | Positions 1, 2, 4, 8, 16 |
| 11 | **B** | 0101 binary = 5 |
| 12 | **B** | Retransmission requests need a path back to the sender |
| 13 | **B** | One sequence number must be reserved to avoid ambiguity |
| 14 | **B** | Only the damaged frame is resent, so the rest must be held |
| 15 | **B** | Bandwidth is wasted resending frames that arrived correctly |
| 16 | **B** | The sender idles for a full round trip after each frame |
| 17 | **A** | I for Information |
| 18 | **B** | Receive Not Ready is a flow control pause |
| 19 | **C** | The frame check sequence is a CRC-16 or CRC-32 |
| 20 | **B** | Failed decodes are combined with retransmitted copies |

---

# Exam-style written questions with model answers

**W1. Given the data 1101011011 and the generator 10011, compute the CRC and show the transmitted frame. Show all steps of the modulo-2 division. (8 marks)**

*Model answer:* The generator has five bits, so four zeros are appended to the data, giving the dividend 11010110110000. Perform modulo-2 division, in which each subtraction step is a bitwise XOR with the generator whenever the leading bit of the current partial remainder is 1, and a shift with a zero quotient bit otherwise. Carrying out the division gives a quotient of 1100001010 and a remainder of **1110**. The CRC is therefore 1110, and the transmitted frame is the original data followed by the CRC: **1101011011 1110**. At the receiver, the whole 14-bit received string is divided by the same generator 10011. If no bits have been corrupted the remainder is 0000 and the frame is accepted; any non-zero remainder indicates that an error has occurred and the frame is discarded. (Show the full long division working in the answer booklet, since most of the marks are for the working rather than the final value.)

**W2. Explain, with an example, how bit stuffing guarantees that the HDLC flag can never appear inside the payload. (5 marks)**

*Model answer:* The HDLC flag is the fixed pattern 01111110, which contains exactly six consecutive 1 bits. If this pattern occurred by chance within the payload, the receiver would incorrectly interpret it as the end of the frame. To prevent this, the transmitter monitors the outgoing bit stream between the opening and closing flags and, whenever it has sent five consecutive 1 bits, it automatically inserts an additional 0 bit regardless of what the next data bit is. As a result, six consecutive 1s can never occur in the payload, so the flag pattern is unambiguous. The receiver performs the inverse operation: whenever it detects five consecutive 1s followed by a 0, it discards that 0 as a stuffed bit and continues; if it instead sees five 1s followed by a 1 and then a 0, it recognises a genuine flag. For example, the payload 01111111 becomes 011111011 after stuffing, where the inserted 0 appears after the fifth 1. The technique costs a small and data-dependent overhead but makes framing completely reliable and independent of the payload content.

**W3. A system uses Hamming code to protect 7 data bits. Determine the number of redundancy bits required, state their positions and the positions each one checks, and explain how a single-bit error is located. (8 marks)**

*Model answer:* The number of redundancy bits r must satisfy 2^r >= m + r + 1 where m = 7. For r = 3, 2^3 = 8 which is less than 7 + 3 + 1 = 11, so r = 3 is insufficient. For r = 4, 2^4 = 16 which is greater than or equal to 7 + 4 + 1 = 12, so **r = 4** redundancy bits are required, giving an 11-bit codeword. The redundancy bits occupy the positions that are powers of two, namely **positions 1, 2, 4 and 8**, and the data bits occupy the remaining positions 3, 5, 6, 7, 9, 10 and 11. Each redundancy bit provides even parity over the set of positions whose binary representation has a 1 in the corresponding bit place: **r1 checks positions 1, 3, 5, 7, 9 and 11; r2 checks 2, 3, 6, 7, 10 and 11; r4 checks 4, 5, 6 and 7; and r8 checks 8, 9, 10 and 11.** At the receiver, each parity check is recomputed and the results are assembled into a syndrome, written with r8 as the most significant bit and r1 as the least significant. If the syndrome is 0000, no single-bit error has occurred. Otherwise, the syndrome read as a binary number gives directly the position of the corrupted bit, because every position is checked by a unique combination of parity bits corresponding to its own binary representation. For instance a syndrome of 1011 identifies position 11, and the receiver simply inverts that bit to recover the original codeword.

**W4. Compare Stop-and-Wait, Go-Back-N and Selective Repeat ARQ in terms of efficiency, buffering and complexity, and recommend one for a geostationary satellite link. (8 marks)**

*Model answer:* **Stop-and-Wait** permits only one outstanding frame, so after transmitting a frame the sender must wait a full round-trip time before sending the next. Its efficiency is Tt/(Tt + 2Tp), which is very poor whenever propagation delay is large relative to transmission time. It needs no buffering at either end and is the simplest to implement. **Go-Back-N** permits up to 2^m - 1 outstanding frames, so the sender can keep the pipe full. The receiver accepts frames only in strict order and therefore requires no buffer, which keeps it simple, but when a frame is lost the sender must retransmit that frame and every subsequent frame, discarding correctly received data. Efficiency therefore degrades sharply as the error rate rises, and the penalty is proportional to the window size. **Selective Repeat** also allows a large window but retransmits only the specific damaged frame. It is the most bandwidth-efficient, but the receiver must buffer out-of-order frames and reorder them before delivery, and both windows are limited to 2^(m-1), so it is the most complex to implement. For a **geostationary satellite link**, one-way propagation delay is approximately 250 ms, so the round-trip delay is about 500 ms and the bandwidth-delay product is very large. Stop-and-Wait would give utilisation of well under one percent and is unusable. Go-Back-N would keep the pipe full but a single error would force retransmission of an entire window of frames, which at this delay could be hundreds of frames. **Selective Repeat is therefore the correct recommendation**, since it keeps the link full while retransmitting only what was actually lost, and the additional receiver buffering is an acceptable cost given the value of the satellite capacity. In practice such links also apply strong forward error correction so that most errors are corrected without any retransmission at all.

**W5. Describe the HDLC frame format and the three frame types, explaining the function of each field. (8 marks)**

*Model answer:* An HDLC frame begins and ends with an eight-bit **flag** field containing the pattern 01111110, which delimits the frame; bit stuffing ensures this pattern cannot occur in between. The **address** field, normally eight bits but extendable, identifies the secondary station, which is necessary on multipoint links and retained for uniformity on point-to-point links. The **control** field, eight or sixteen bits, determines the frame type and carries sequence numbers and control codes. The **information** field carries the payload from the network layer and is of variable length; it is present only in information frames and in certain unnumbered frames. The **frame check sequence** is a sixteen-bit or thirty-two-bit CRC computed over the address, control and information fields, allowing the receiver to detect corruption. The three frame types are distinguished by the leading bits of the control field. **Information frames (I-frames)** begin with 0 and carry user data together with a send sequence number N(S) identifying the frame and a receive sequence number N(R) acknowledging frames received from the other direction, so acknowledgement is piggybacked on data. **Supervisory frames (S-frames)** begin with 10, carry no user data, and perform error and flow control using four codes: Receive Ready to acknowledge and indicate readiness, Receive Not Ready to acknowledge but request a pause, Reject to request Go-Back-N retransmission, and Selective Reject to request retransmission of a single frame. **Unnumbered frames (U-frames)** begin with 11 and manage the link itself, including SABM to set asynchronous balanced mode, UA to acknowledge such a request, DISC to disconnect, and FRMR to report an unrecoverable protocol error.

---

## Checkpoint before Topic 7

You should be able to, without notes:
1. Perform a full CRC division and verify at the receiver
2. Bit-stuff a given bit string correctly
3. Compute r from 2^r >= m + r + 1 and lay out a Hamming codeword
4. Convert a Hamming syndrome to an error position
5. State the window limits for Go-Back-N and Selective Repeat
6. Draw the HDLC frame and name all six fields
7. Say when FEC beats ARQ and why
