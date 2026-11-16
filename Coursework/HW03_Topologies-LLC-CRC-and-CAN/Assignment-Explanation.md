# HW3 Assignment Explanation — Topologies, LLC, CRC, and CAN

[Repository Guide](../../README.md) | [English](Assignment-Explanation.md) | [한국어](Assignment-Explanation.ko.md)

> **Last Updated:** 2026-11-16

> **Learning Objectives:**
> 1. Explain the kinds of network interconnection devices and the characteristics of each
> 2. Name the factors for choosing topologies and transmission media, and choose a topology that fits given conditions with reasons
> 3. Explain what group addresses in LLC are and how they are used
> 4. Compute a CRC with a generator polynomial and check it at the receiver
> 5. Explain the characteristics of automotive CAN and LIN, the structure of the CAN 2.0A frame, and the differences between CAN and CAN FD

---

## Table of Contents

- [1. Assignment Layout](#1-assignment-layout)
- [2. Network Interconnection Devices (Question 1)](#2-network-interconnection-devices-question-1)
- [3. Choosing a Topology (Question 2)](#3-choosing-a-topology-question-2)
- [4. Choosing a Transmission Medium (Question 3)](#4-choosing-a-transmission-medium-question-3)
- [5. Group Addresses in LLC (Question 4)](#5-group-addresses-in-llc-question-4)
- [6. CRC Calculation (Question 5)](#6-crc-calculation-question-5)
  - [6.1 Principle of the CRC](#61-principle-of-the-crc)
  - [6.2 The Calculation](#62-the-calculation)
  - [6.3 Checking at the Receiver and Detection Capability](#63-checking-at-the-receiver-and-detection-capability)
- [7. MAC (Question 6)](#7-mac-question-6)
- [8. Characteristics of Automotive CAN (Question 7)](#8-characteristics-of-automotive-can-question-7)
- [9. Master and Slave in LIN (Question 8)](#9-master-and-slave-in-lin-question-8)
- [10. The CAN 2.0A Message Frame (Question 9)](#10-the-can-20a-message-frame-question-9)
- [11. CAN and CAN FD (Question 10)](#11-can-and-can-fd-question-10)
- [Summary](#summary)
- [Self-Check Questions](#self-check-questions)
- [References](#references)

---

<br>

## 1. Assignment Layout

> **Try It First:** This document contains the answers and solutions. Solve the problems in the [assignment handout](Original/Assignment.md) yourself before reading on.

This is an essay assignment on the material of weeks 6, 7, and 8. The assignment has ten questions: questions 1-3 ask about topologies and transmission media from Week 6, and questions 4-6 ask about LLC, CRC, and MAC from Week 7. The CAN, LIN, and CAN FD of questions 7-10, which the handout groups under week 8, are covered in Week 9. The original questions and their points are in the [assignment handout](Original/Assignment.md), and each of Sections 2-11 also opens with the question it answers.

```text
Coursework/HW03_Topologies-LLC-CRC-and-CAN/
├── Original/
│   ├── Assignment.ko.md            # assignment handout (Korean)
│   └── Assignment.md               # assignment handout (English)
├── Assignment-Explanation.ko.md    # assignment explanation (Korean)
└── Assignment-Explanation.md       # assignment explanation (English)
```

*Figure 1. Directory layout of HW3*

| Question | What It Asks | Related Units |
|:-----|:-----|:-----|
| 1 | Three kinds of network gateways and their characteristics (three lines or fewer each) | Week 6 |
| 2 | Three factors for choosing a topology, and the most suitable topology when signal noise is high and the configuration must be simple, with the reason | Week 6 |
| 3 | Five deciding factors for choosing a transmission medium | Week 6 |
| 4 | Group addresses in LLC | Week 7 |
| 5 | Finding the bit frame $c(x)$ to transmit for data 101011 and generator polynomial $x^4 + x + 1$ | Week 7 |
| 6 | Definition of MAC (three lines or fewer) | Week 7 |
| 7 | Characteristics of automotive CAN communication | Week 9 |
| 8 | Roles of the master and slaves in automotive LIN communication | Week 9 |
| 9 | Structure of the CAN 2.0A message frame (drawing and explanation) | Week 9 |
| 10 | Differences between CAN and CAN FD | Week 9 |

*Table 1. Questions of HW3*

---

<br>

## 2. Network Interconnection Devices (Question 1)

**Question 1.** List three kinds of network gateways and explain their characteristics in three lines or fewer.

Week 6 introduces the devices that join networks to networks and segments to segments under the broad name **gateway**. The devices are distinguished by how far up the OSI layers they interpret, and the higher the layer, the more information a device looks at and the more intelligently it forwards, but the more expensive and slower it becomes.

| Device | Layer | What It Does | Characteristics |
|:-----|:-----|:-----|:-----|
| Repeater | Physical layer (1) | Regenerates an attenuated signal and sends it to the next segment | It does not look at addresses, passes every signal as is, and extends the communication distance. Its function is now built into other devices, so it is seldom used alone |
| Hub | Physical layer (1) | A multiport repeater that joins several devices | It copies a received signal to every port. Physically a star but logically a bus, so collisions increase as devices are added, and all ports have the same speed |
| Bridge | Data link layer (2) | Joins segments and forwards or filters frames by MAC address | Through the five operations of learning, flooding, forwarding, filtering, and aging, it keeps traffic within a segment from spreading to other segments (separating collision domains), and its ports may have different speeds |
| Switch | Data link layer (2) | A multiport device that performs the bridge's functions in hardware | Faster than a bridge with more ports, and the speed can be set per port. L3, L4, and L7 switches also look at IP addresses, ports, and payloads |
| Router | Network layer (3) | Chooses the best path between networks from the IP address and forwards packets | It joins different networks. It is relatively expensive and complex to set up initially |

*Table 2. Comparison of network interconnection devices*

The five operations of a bridge are as follows. **Learning** records the MAC address of the host that sent a frame and the port it arrived on in the MAC address table, **flooding** sends a frame to every port except the one it arrived on when the destination is not in the table, and **forwarding** sends it only to the matching port when the destination is in the table. **Filtering** does not send a frame to other segments when the source and destination are on the same segment, and **aging** deletes from the table an address from which no frame has arrived for a set time (for example, 300 seconds).

Choosing three devices for a model answer gives the following.

1. **Repeater:** A physical layer device that simply joins segments and regenerates attenuated signals to extend the communication distance. It does not interpret addresses, so it passes every signal as is.
2. **Bridge:** A data link layer device that learns a MAC address table and forwards frames only to the segments that need them. It isolates traffic between segments to split collision domains, and its ports may have different speeds.
3. **Router:** A network layer device that chooses the best path between networks from the IP address and forwards packets. It joins different networks and is relatively expensive and complex to set up.

> **Note:** A **gateway** in the narrow sense is a device that joins networks with different protocols and converts between the protocols, interpreting up to the transport layer and above. An example is the central gateway ECU of a vehicle, which converts and forwards messages between the CAN, LIN, FlexRay, and Ethernet domains (Weeks 2 and 9).

---

<br>

## 3. Choosing a Topology (Question 2)

**Question 2.** Write the three factors for choosing a topology, and choose the most suitable topology when there is much signal noise and the network must be built simply, and explain why.

The three factors for choosing a topology are **reliability**, **expandability**, and **performance**. Reliability is how well communication continues when faults occur, expandability is how easily stations can be added or the coverage enlarged, and performance is the data rate and delay. Week 6 summarizes the three topologies as follows.

| Topology | Characteristic | Strengths and Weaknesses |
|:-----|:-----|:-----|
| Bus/tree | The most flexible | It accommodates a wide range of numbers of devices, data rates, and data types. A fault in the single shared medium affects everything |
| Ring | The fastest | With optical fiber it achieves the highest speed, but it is vulnerable if even one link is cut |
| Star | The easiest layout | It suits high-speed communication between a small number of devices over short distances. If the central device fails, everything stops |

*Table 3. Selection criteria by topology*

When signal noise is high and the configuration must be simple, the **star topology** is the most suitable. The reasons are as follows.

1. **How far the effect of noise spreads:** In a bus, all stations share one medium, so noise and faults entering at any one tap or connector disturb communication across the whole bus. Long multipoint lines also have large attenuation and noise, so signal balancing is needed to equalize the signal strength for each station. In a star, each station is connected to the central device by its own point-to-point link, so noise or a fault on one link does not spread to the other links.
2. **Applying countermeasures against noise:** With point-to-point links it is easy to choose a noise-resistant medium, such as shielded cable or optical fiber, for each link.
3. **Simplicity:** A star has the simplest layout and makes adding nodes and maintenance easy. A ring also keeps noise from accumulating because its repeaters regenerate the signal, but a single failed link or repeater stops the whole ring and installation is difficult, so it does not suit a simple configuration.

However, a star stops entirely if the central device fails, so the central device must be highly reliable.

> **Note:** The conditions in the question can be read in two ways. Read as building a simple network in an environment with much noise, the answer is the star, as above. Read as putting simplicity first even if some noise arises, one can choose the bus, in which all stations share one medium so that the cabling and configuration are the simplest. Even then, the limitation of the bus, that noise or a fault at one point affects the whole bus, should be stated as well.

> **Caution:** Robustness to noise depends more on the transmission medium and the signaling method than on the topology. Automotive CAN is a bus, but it sends differential signals, which represent bits by the voltage difference between two wires, over a twisted pair, so even if the same noise enters both wires the voltage difference stays the same and it is robust to noise (Week 9). In a topology question, the decision rests on how far noise has an effect and how simple the configuration is.

---

<br>

## 4. Choosing a Transmission Medium (Question 3)

**Question 3.** Write the five deciding factors for choosing a transmission medium.

The five deciding factors for choosing a transmission medium are as follows.

| Factor | Meaning |
|:-----|:-----|
| Topology | Each medium supports different topologies, so a medium that fits the chosen topology is selected |
| Capacity | It must handle the required data rate and bandwidth |
| Reliability | It must meet the acceptable error rate and tolerance to faults |
| Types of data | It must carry the kinds of data to be supported, such as data, voice, and video |
| Environment | It must withstand the environmental scope of the installation site, such as distance, electromagnetic noise, and temperature |

*Table 4. Deciding factors for choosing a transmission medium*

Week 6 compares the representative media as follows. Twisted pair is inexpensive and already installed in most office buildings, so it is the most economical for low-speed LANs within a single building. Coaxial cable is more expensive than twisted pair but has a larger capacity, so it suits office and factory automation that connects many devices with heavy traffic. Optical fiber is used mainly for point-to-point links and can be used in rings and stars.

| Medium | Ring | Bus | Tree | Star |
|:-----|:-----|:-----|:-----|:-----|
| Twisted pair | ● | ● | | ● |
| Baseband coaxial cable | ● | ● | | |
| Broadband coaxial cable | | ● | ● | |
| Optical fiber | ● | ● | | ● |
| Wireless | ● | ● | | ● |

*Table 5. Correlation between transmission media and topologies*

---

<br>

## 5. Group Addresses in LLC (Question 4)

**Question 4.** Explain group addresses in LLC (logical link control).

The IEEE 802 reference model divides the data link layer of a LAN into two sublayers, **LLC (logical link control)** and **MAC (medium access control)**, with the physical layer beneath them. LLC provides one or more **service access points (SAPs)** to the upper layer and handles error control and flow control. MAC assembles and disassembles frames and controls access to the shared medium.

Addressing in a LAN takes place at **two levels**.

1. **MAC address:** Points to the **station** (network interface) the frame is to reach. It goes in the MAC header.
2. **LSAP address (LLC SAP):** Points to the **LLC user** (a process or protocol) within that station that is to receive the data. It goes in the LLC header as the destination SAP (DSAP) and source SAP (SSAP).

A **group address** points not to a single entity but to a set of entities, and there are **multicast**, which points to a specific group, and **broadcast**, which points to all entities. The MAC address and the LSAP address each take three forms, individual, multicast, and broadcast, so combining them yields $3 \times 3 = 9$ kinds of recipients [1].

| Combination | MAC Address | LSAP Address | Recipients |
|:-----|:-----|:-----|:-----|
| (a) | Individual | Individual | One user at one station |
| (b) | Individual | Multicast | A group of users within one station |
| (c) | Individual | Broadcast | All users at one station |
| (d) | Multicast | Individual | The user with the same LSAP at each station of a station group |
| (e) | Multicast | Multicast | A group of users at each station of a station group |
| (f) | Multicast | Broadcast | All users at a station group |
| (g) | Broadcast | Individual | The user with the same LSAP at every station |
| (h) | Broadcast | Multicast | A group of users at every station |
| (i) | Broadcast | Broadcast | All users at every station |

*Table 6. The nine combinations of MAC addresses and LSAP addresses*

LSAP addresses are normally unique **only within one station**. Making LSAPs unique across the whole network would limit the number of processes that can be used and require SAP numbers to be assigned and managed centrally. Instead, the same LSAP number is assigned to processes common to all stations, such as station management. Then, as in (d), (e), (g), and (h), combining multicast or broadcast MAC addresses with LSAP addresses sends a message to the same process at several stations with a single frame [1].

In real IEEE 802 addresses, a group address has its least significant bit of the first byte (the I/G bit) set to 1, and FF-FF-FF-FF-FF-FF, with all bits 1, is the broadcast address [2]. The DSAP of LLC also distinguishes individual and group by its least significant bit, and 0xFF is the global address that points to all SAPs [3].

---

<br>

## 6. CRC Calculation (Question 5)

**Question 5.** This is a CRC (cyclic redundancy check) calculation problem. The original data bit string is 101011, and the given CRC generator polynomial $g(x)$ is $x^4 + x + 1$, that is, 10011. Find the bit frame $c(x)$ to transmit.

### 6.1 Principle of the CRC

The **CRC (cyclic redundancy check)** is a method in which the sender appends a check bit sequence, the **FCS (frame check sequence)**, to the data, and the receiver checks it by the same rule to find transmission errors. A bit sequence is viewed as a polynomial whose coefficients are 0 or 1, and **modulo-2 arithmetic** is used, in which addition and subtraction are XOR without carries.

The symbols follow the notation of Week 7.

- $d(x)$: the polynomial for the $k$ data bits to send
- $g(x)$: the **generator polynomial** of degree $m$ agreed on in advance by sender and receiver
- $p(x)$: the remainder of dividing $x^m d(x)$ by $g(x)$ ($m$ bits)
- $c(x)$: the bit frame to transmit ($k + m$ bits)

```math
p(x) = \text{Rem}\left[\frac{x^m d(x)}{g(x)}\right], \qquad c(x) = x^m d(x) + p(x)
```

$x^m d(x)$ is the data with $m$ zeros appended. In modulo-2 arithmetic addition and subtraction are the same, so adding the remainder $p(x)$ is the same as subtracting it, and $c(x)$ is **exactly divisible** by $g(x)$.

### 6.2 The Calculation

The values in the question are as follows.

- Data $d = 101011$, that is, $d(x) = x^5 + x^3 + x + 1$ ($`k = 6`$)
- Generator polynomial $g = 10011$, that is, $g(x) = x^4 + x + 1$ ($`m = 4`$)

**Step 1:** $x^4 d(x) = x^9 + x^7 + x^5 + x^4$, which as a bit sequence is the data with four zeros appended, 1010110000.

**Step 2:** Divide 1010110000 by 10011 in modulo-2 arithmetic. If the leading bit is 1, XOR 10011 aligned at that position; if it is 0, move on to the next position.

```text
x^4 d(x)   1 0 1 0 1 1 0 0 0 0
g          1 0 0 1 1
           ───────────────────
           0 0 1 1 0 1 0 0 0 0
g              1 0 0 1 1
           ───────────────────
           0 0 0 1 0 0 1 0 0 0
g                1 0 0 1 1
           ───────────────────
           0 0 0 0 0 0 0 1 0 0
                       └──┬──┘
                      p = 0100
```

*Figure 2. Modulo-2 division of 1010110000 by 10011*

The first bit is 1, so XORing 10101 with 10011 gives 00110. The second bit is 0, so it is skipped, and XORing 11010 from the third position with 10011 gives 01001. XORing 10010 from the fourth position with 10011 gives 00001. The remaining bits cannot be divided further, so the last four bits, 0100, are the remainder. The quotient has 1s at the positions where XOR was applied, the first, third, and fourth, so it is 101100, that is, $x^5 + x^3 + x^2$.

**Step 3:** Add the remainder $p = 0100$, that is, $p(x) = x^2$, to $x^4 d(x)$. This replaces the 0000 after the data with 0100.

```math
c(x) = x^4 d(x) + p(x) = x^9 + x^7 + x^5 + x^4 + x^2
```

Therefore the bit frame to transmit is **1010110100**. The first six bits, 101011, are the original data, and the last four bits, 0100, are the FCS.

The result can also be checked with the quotient. Multiplying the quotient by the generator polynomial gives the following.

```math
(x^5 + x^3 + x^2)(x^4 + x + 1) = x^9 + x^6 + x^5 + x^7 + x^4 + x^3 + x^6 + x^3 + x^2 = x^9 + x^7 + x^5 + x^4 + x^2
```

In modulo-2 arithmetic two identical terms (the two $x^6$ terms and the two $x^3$ terms) cancel, so the product equals $c(x)$ exactly. That is, $c(x)$ is a multiple of $g(x)$.

### 6.3 Checking at the Receiver and Detection Capability

The receiver divides the entire received bit sequence by $g(x)$. If the remainder is 0 it judges that there is no error, and if it is not 0 it judges that there is an error.

```text
c(x)       1 0 1 0 1 1 0 1 0 0
g          1 0 0 1 1
           ───────────────────
           0 0 1 1 0 1 0 1 0 0
g              1 0 0 1 1
           ───────────────────
           0 0 0 1 0 0 1 1 0 0
g                1 0 0 1 1
           ───────────────────
           0 0 0 0 0 0 0 0 0 0
```

*Figure 3. Checking the received 1010110100 by dividing it by 10011 (remainder 0)*

If the ninth bit were flipped in transit and 1010110110 were received, the remainder of the same division would be 0010, which is not 0, so the error would be detected.

The detection capability of a CRC is determined by the generator polynomial [4].

- A generator polynomial with a constant term detects all single-bit errors.
- A generator polynomial of degree $m$ with a constant term detects all burst errors (errors in a contiguous span) of length $m$ or less.
- If the generator polynomial has $x + 1$ as a factor, it detects every error in which an odd number of bits change.
- Errors outside these conditions may go undetected with low probability.

Substituting $x = 1$ into the $g(x) = x^4 + x + 1$ of this question gives $1 + 1 + 1 = 1 \ne 0$, so it does not have $x + 1$ as a factor. It therefore detects all single-bit errors and all burst errors of length 4 or less, but it is not guaranteed to detect every error with an odd number of bits.

---

<br>

## 7. MAC (Question 6)

**Question 6.** Explain what MAC (medium access control) is in three lines or fewer.

A model answer is as follows.

MAC (medium access control) is the means of deciding who transmits when, so that the capacity of the medium is used efficiently when several devices share one transmission medium. By where control is exercised, it divides into centralized control, in which a central controller grants the right to transmit, and distributed control, in which the stations decide together. By how control is exercised, it divides into synchronous control, which allocates capacity permanently, and asynchronous control, which allocates it dynamically as needed (round robin, reservation, contention).

MAC techniques are organized by how they answer these two questions.

| Category | Method | Description |
|:-----|:-----|:-----|
| Where | Centralized control | A designated central controller grants access at the request of stations. Control such as priorities and guaranteed capacity is easy and station logic is simple, but a single failure of the central controller stops everything and bottlenecks can arise |
| Where | Distributed control | The collective MAC function of the stations dynamically decides which station transmits next |
| How | Synchronous | Allocates capacity to each connection permanently, like TDM. It does not suit LANs, whose traffic cannot be predicted |
| How | Round robin | Stations get the chance to transmit in turn. There are polling (centralized) and token passing (distributed), and with light traffic the time spent waiting for one's turn lowers efficiency |
| How | Reservation | Divides the medium time into slots and reserves future slots. It suits long, steady transmissions such as voice and bulk files |
| How | Contention | Each station tries to transmit without control. It is simple to implement and efficient when traffic is light or sporadic, but with heavy traffic collisions make efficiency drop sharply |

*Table 7. Where and how MAC is controlled*

In the analogy of Week 7, reservation corresponds to an intersection with traffic lights, round robin to an intersection with stop signs, and contention to an intersection without signals. LANs mainly use contention (CSMA/CD in Ethernet, CSMA in wireless LANs) or round robin (token ring, token bus). The MAC layer **detects** errors in received frames with the CRC, and LLC handles error **recovery**.

---

<br>

## 8. Characteristics of Automotive CAN (Question 7)

**Question 7.** Explain the characteristics of automotive CAN communication.

**CAN (controller area network)** is a serial bus whose development as an in-vehicle network Bosch began in 1983 and which was standardized as ISO 11898 in 1993. It has a two-layer structure that defines only the physical layer and the data link layer, and it has the following characteristics.

1. **Multi-master broadcast:** Any node on the bus can start transmitting when the bus is idle. Frames carry a message ID instead of node addresses, and every node receives every frame and then filters by ID to accept only the messages it needs.
2. **Priority-based non-destructive arbitration:** When several nodes transmit at the same time, bit-wise arbitration on the ID lets the message with the smallest ID be transmitted without damage (CSMA/CD + AMP). It is event-triggered, transmitting when an event occurs.
3. **Differential signaling and robustness to noise:** It uses the two wires CAN_H and CAN_L as a twisted pair and represents dominant and recessive by the voltage difference between them, so even if the same noise enters both wires the difference does not change. A 120 Ω termination resistor is attached at each end of the bus.
4. **Speed and distance:** The maximum is 1 Mbit/s, and the lower the speed, the longer the bus can be, reaching about 1 km at 50 kbit/s. Real vehicles mainly run at 250 kbit/s or 500 kbit/s.
5. **Strong error handling:** Errors are detected in five ways, CRC, frame check, ACK check, bit monitoring, and bit stuffing, and a node that detects an error notifies all nodes with an error frame. The sending node retransmits automatically, and the transmit and receive error counters (TEC, REC) isolate a faulty node in stages through the error active, error passive, and bus off states.
6. **Small payload:** A frame carries at most 8 bytes of data.

These characteristics can be checked numerically from the viewpoint of load and delay. A standard data frame carrying 8 bytes is 108 bits from SOF to EOF (Section 10), or 111 bits with the 3-bit intermission between frames. At 500 kbit/s one bit takes 2 µs, so one such frame occupies the bus for about $111 \times 2 = 222$ µs (excluding stuff bits). Sending 1,000 of these frames per second gives the following bus load.

```math
\text{Bus load} = \frac{1000 \times 111\ \text{bit/s}}{500000\ \text{bit/s}} = 22.2\%
```

Week 9 recommends keeping traffic at about 50% of the total bandwidth or less, but the bus loads of high-end models keep rising, such as KH 75.9%, VI 53.6%, Benz 37.9%, and Infiniti M35 47.5%. Under high load, low-priority messages must wait until higher-priority messages finish, and the waiting time depends on which messages are on the bus at that moment. Therefore, even when the total traffic is nearly constant, the transmission delay of each message varies widely, which is called **jitter**, and jitter degrades the response of control loops. This limit is the background for the introduction of CAN FD, time-triggered FlexRay, and automotive Ethernet. CAN FD keeps the arbitration unchanged, sends the data phase at up to 8 Mbit/s, and extends the payload to 64 bytes (Section 11).

---

<br>

## 9. Master and Slave in LIN (Question 8)

**Question 8.** Explain the concepts of master and slave in automotive LIN communication, focusing on the role of each.

**LIN (local interconnect network)** is a subnetwork for connecting body devices that do not need fast communication, such as windows, mirrors, seats, wipers, and rain sensors, at low cost. It uses a single wire with a data rate of at most 20 kbit/s and is implemented with the UART/SCI interface found in most microcontrollers. The current standard is ISO 17987 [5]. A LIN cluster consists of one **master node** and several **slave nodes** (2-16 members on the bus), and because the master directs all communication, **no arbitration is needed**, unlike CAN.

A LIN frame consists of a **header** sent by the master and a **response** sent by one node.

```text
 Header (master task)                       Response (slave task)
┌─────────────┬────────────┬────────────┐  ┌─────────────────┬──────────┐
│ Sync break  │ Sync field │ Identifier │  │ Data (1-8 bytes)│ Checksum │
│ (13+ bits)  │   (0x55)   │ (6 + 2 P)  │  │                 │          │
└─────────────┴────────────┴────────────┘  └─────────────────┴──────────┘
```

*Figure 4. Header and response of a LIN frame*

The **roles of the master** are as follows.

1. **Deciding the schedule:** It decides which message is sent when according to a predefined schedule table. LIN therefore operates in a time-triggered way, and the delay of each message is predictable.
2. **Sending headers:** It signals the start of a frame with the sync break, made of 13 or more dominant bits, provides the reference clock with the sync byte (0x55), and then designates the message to be exchanged with the ID field, made of a 6-bit ID and 2 parity bits. With 6 bits, up to 64 IDs (0-63) can be used.
3. **Providing the reference clock:** All slaves align with the master's sync byte, so the master needs a stable clock (a crystal oscillator).
4. **Monitoring and error management:** It monitors the data bytes and checksum of the response, evaluates whether they agree, and manages errors.
5. **Managing sleep and wake-up:** It puts the bus into sleep mode to save power, and when it receives a wake-up signal sent by a slave while the bus is inactive, it resumes communication.

The **roles of a slave** are as follows.

1. **Watching the ID:** It monitors the ID of every header and, depending on the ID, decides whether to send data (publish), receive it (subscribe), or ignore it.
2. **Sending the response:** If the ID is one it publishes, it sends data of the defined length, such as 1, 2, 4, or 8 bytes, followed by the checksum.
3. **Self-synchronization:** The sync byte 0x55, including its start and stop bits, alternates between 0 and 1, so a slave measures the time between edges to adjust its own clock. A slave therefore needs no crystal oscillator or ceramic resonator, which lowers the parts cost of LIN.
4. **Requesting wake-up:** If an event such as a switch input occurs while the bus is asleep, it sends a wake-up signal.

The master node contains not only the master task but also a **slave task**. The master can therefore answer its own header to send data to the slaves (master to slave), receive a slave's response (slave to master), or have one slave's response received by another slave (slave to slave). That the master node can also act as a slave refers to this structure.

> **Caution:** LIN has no error signal like the CAN error frame. A slave that detects an error records it in a status signal (the response error bit in LIN 2.x), and the master learns of the error by regularly reading a frame that carries this signal [5]. A cluster also has only one master, and a slave never takes over the master role. The master node often doubles as the gateway between CAN and LIN.

---

<br>

## 10. The CAN 2.0A Message Frame (Question 9)

**Question 9.** Draw and explain the structure of the CAN 2.0A message frame.

CAN 2.0A is the standard format, which uses an 11-bit ID, and CAN 2.0B is the extended format, which uses a 29-bit ID. A CAN 2.0A data frame is structured as follows [6].

```text
   1        11        1     1    1     4     8n       15       1     1     1     7     3
┌─────┬────────────┬─────┬─────┬────┬─────┬──────┬──────────┬─────┬─────┬─────┬─────┬─────┐
│ SOF │ Identifier │ RTR │ IDE │ r0 │ DLC │ Data │ CRC seq. │ DEL │ ACK │ DEL │ EOF │ IFS │
└─────┴────────────┴─────┴─────┴────┴─────┴──────┴──────────┴─────┴─────┴─────┴─────┴─────┘
       └─ Arbitration ──┘ └── Control ───┘        └─ CRC field ──┘ └── ACK ──┘
```

*Figure 5. Structure of a CAN 2.0A data frame (the top row gives bit counts, n is the number of data bytes)*

| Field | Bits | Value and Role |
|:-----|:-----|:-----|
| SOF (start of frame) | 1 | Dominant (0). It signals the start of the frame, and every node synchronizes on this edge |
| Identifier | 11 | Indicates the content and priority of the message. The smaller the value, the higher the priority |
| RTR (remote transmission request) | 1 | Dominant (0) for a data frame and recessive (1) for a remote frame. Together with the ID it forms the arbitration field |
| IDE (identifier extension) | 1 | Dominant (0) for the standard format (11-bit ID) and recessive (1) for the extended format (29-bit ID) |
| r0 | 1 | A reserved bit sent as dominant (0). In CAN FD, the FDF bit in this position means a classical CAN frame if 0 and a CAN FD frame if 1 |
| DLC (data length code) | 4 | The number of bytes in the data field (0-8) |
| Data | 0-64 | The actual data, at most 8 bytes |
| CRC sequence | 15 | The CRC computed with $x^{15} + x^{14} + x^{10} + x^8 + x^7 + x^4 + x^3 + 1$ over the bits from SOF through the data field (excluding stuff bits) |
| CRC delimiter | 1 | Recessive (1). It marks the end of the CRC field |
| ACK slot | 1 | The sending node sends recessive (1), and receiving nodes that received the frame correctly overwrite this bit with dominant (0) to acknowledge it |
| ACK delimiter | 1 | Recessive (1). It marks the end of the ACK field |
| EOF (end of frame) | 7 | Seven recessive (1) bits that mark the end of the frame |
| IFS (intermission) | 3 | Three recessive bits left before the next frame |

*Table 8. Fields of a CAN 2.0A data frame*

The number of bits from SOF to EOF is $1 + 11 + 1 + 1 + 1 + 4 + 8n + 15 + 1 + 1 + 1 + 7 = 44 + 8n$. With 8 bytes of data this is $44 + 64 = 108$ bits, so the share of data is $`64 / 108 \approx 59\%`$, and stuff bits are added on top.

The behavior of the ACK slot is how the sending node judges the result. The sending node reads the bus while sending recessive, and if it reads dominant, it takes this to mean that at least one node received the frame correctly. If it reads recessive to the end, no node acknowledged the frame, so it judges an ACK error and retransmits. Conversely, a receiving node that detects a CRC error does not overwrite the ACK slot and sends an error frame after the ACK delimiter to notify all nodes. Meanwhile, the bus is regarded as idle when recessive bits continue through the 8 bits formed by the ACK delimiter and the 7-bit EOF, followed by the 3 bits of intermission.

---

<br>

## 11. CAN and CAN FD (Question 10)

**Question 10.** Explain the differences between CAN and CAN FD.

**CAN FD (CAN with flexible data-rate)** is an extension published by Bosch in 2012 and included in ISO 11898-1:2015, which keeps the arbitration of classical CAN unchanged and increases the length and speed of the data field [7], [8].

| Aspect | Classical CAN | CAN FD |
|:-----|:-----|:-----|
| Data field | 0-8 bytes | 0-64 bytes. DLC 0-8 keep their meaning, and 9-15 mean 12, 16, 20, 24, 32, 48, and 64 bytes |
| Bit rate | One rate for the whole frame (at most 1 Mbit/s) | The arbitration phase uses the nominal rate (at most 1 Mbit/s), and if BRS is 1 the data phase is sent at a faster rate (for example, 2 Mbit/s or 5 Mbit/s) |
| Remote frame | Yes (RTR bit) | None. The RTR position is RRS (remote request substitution), which is always dominant |
| Control field | IDE, r0, DLC | IDE, FDF (1 for CAN FD), res, BRS (bit rate switch), ESI (error state indicator), DLC |
| CRC | 15 bits | 17 bits (CRC-17) for 16 data bytes or less, 21 bits (CRC-21) for 20 bytes or more, preceded by a stuff count holding the number of stuff bits |
| Bit stuffing | Dynamic stuffing from SOF through the CRC sequence | Dynamic stuffing through the data field, and fixed stuff bits at set positions in the CRC field |

*Table 9. Comparison of classical CAN and CAN FD*

CAN FD is faster for two reasons. First, the data a frame can carry grows eightfold from 8 bytes to 64 bytes, so the share of overhead such as the header and CRC shrinks. Second, the speed of the arbitration phase, in which the bits of all nodes must overlap, is hard to raise because of the time a signal takes to travel to the end of the bus and back, but once arbitration is over only the sending node transmits, so the bit time can be shortened in the data phase. The span from the sampling point of the BRS bit to the CRC delimiter is sent at the faster rate.

The ESI bit reports the error state of the sending node (dominant for error active, recessive for error passive). The CRC was lengthened to 17 and 21 bits and the stuff count was added to keep the error detection capability even with long data fields. The generator polynomial of CRC-17 is 0x3685B, and that of CRC-21 is 0x302899 [8].

> **Caution:** A CAN FD controller can also send and receive classical CAN frames, but the reverse does not hold. A classical CAN controller that does not know CAN FD interprets the bits after FDF in the classical format, detects errors in the CAN FD frame, and destroys it by sending an error frame. Therefore, to use CAN FD frames on a bus, every node must support CAN FD or be an FD-tolerant controller made to ignore CAN FD frames.

---

<br>

## Summary

| Concept | Key Summary |
|:-----|:-----|
| Network interconnection devices | Repeaters and hubs operate at the physical layer, bridges and switches at the data link layer, and routers at the network layer, and a gateway in the narrow sense converts protocols. |
| Choosing a topology | Topologies are chosen by reliability, expandability, and performance, and when noise is high and simplicity is needed, a star suits best because noise stays on one link and the layout is easy. |
| Choosing a transmission medium | Media are chosen by five factors: topology, capacity, reliability, types of data, and environment. |
| LLC group addresses | The MAC address and the LSAP address each take individual, multicast, and broadcast forms, making nine kinds of recipients. |
| CRC | The remainder of dividing $x^m d(x)$ by $g(x)$ is appended so that the transmitted $c(x)$ is divisible by $g(x)$, and the answer to the question is 1010110100. |
| MAC | The means of controlling access to a shared medium, divided by where control is exercised (centralized, distributed) and how (synchronous, round robin, reservation, contention). |
| Automotive CAN | It features multi-master broadcast, ID-based non-destructive arbitration, differential signaling, at most 1 Mbit/s, and five error detection methods, and jitter grows under high load. |
| LIN | The master sends headers according to a schedule and provides the clock reference, and slaves respond according to the ID and synchronize themselves with the sync byte. |
| CAN 2.0A frame | It consists of the SOF, the 11-bit ID and RTR, IDE, r0, and DLC, the data, the 15-bit CRC, ACK, and EOF, and a data frame with 8 bytes is 108 bits. |
| CAN FD | Arbitration is the same as in classical CAN, while the data phase speed is raised, the data is extended to 64 bytes, and the CRC is strengthened to 17 and 21 bits. |

---

<br>

## Self-Check Questions

1. **Layer of a device:** How does a bridge behave when it receives a frame whose destination MAC address is not in its MAC address table?

   <details>
   <summary>Answer</summary>

   It floods the frame. It sends the frame to every port except the one it arrived on, and it learns the source MAC address and arrival port by recording them in the table.

   </details>

2. **Group addresses:** How are the MAC address and the LSAP address combined to send a message to the station management process of every station with a single frame?

   <details>
   <summary>Answer</summary>

   Combine the broadcast MAC address with the individual LSAP address assigned in common to the station management process (combination (g)). This works because a common process is assigned the same LSAP number at every station.

   </details>

3. **CRC calculation:** As in the Week 7 example, find the bit frame to transmit for data 110011 and generator polynomial $g(x) = x^4 + x^3 + 1$.

   <details>
   <summary>Answer</summary>

   Divide 1100110000, the data with four zeros appended, by 11001. XORing 11001 at the first position gives 0000010000, and XORing again at the sixth position gives 0000001001, so the remainder is 1001. Therefore $c(x) = x^9 + x^8 + x^5 + x^4 + x^3 + 1$, and the bit frame is 1100111001.

   </details>

4. **Bus load:** On a 500 kbit/s CAN bus, what is the bus load when 2,000 frames of 111 bits are sent per second? Does it meet the recommendation of Week 9?

   <details>
   <summary>Answer</summary>

   $2000 \times 111 / 500000 = 0.444$, so it is 44.4% (excluding stuff bits). It meets the recommendation of about 50% or less, but with little margin.

   </details>

5. **LIN synchronization:** Why do LIN slaves not need a crystal oscillator?

   <details>
   <summary>Answer</summary>

   The master sends the sync byte (0x55), which alternates between 0 and 1, in the header of every frame, so a slave can readjust its clock each time by measuring the time between edges. Only the master needs an accurate clock.

   </details>

6. **Frame length:** How many bits from SOF to EOF does a CAN 2.0A data frame with 2 bytes of data have (excluding stuff bits)?

   <details>
   <summary>Answer</summary>

   It has $44 + 8 \times 2 = 60$ bits.

   </details>

7. **CAN FD DLC:** If the DLC of a CAN FD frame is 13, how many bytes of data does it carry, and how many bits of CRC does it use?

   <details>
   <summary>Answer</summary>

   DLC 9-15 mean 12, 16, 20, 24, 32, 48, and 64 bytes in order, so 13 means 32 bytes. This exceeds 16 bytes, so CRC-21 is used.

   </details>

---

<br>

## References

1. W. Stallings, *Local and Metropolitan Area Networks*, 6th ed. Upper Saddle River, NJ, USA: Prentice Hall, 2000.
2. IEEE, "IEEE Standard for Local and Metropolitan Area Networks: Overview and Architecture," IEEE Std 802-2014, 2014.
3. IEEE, "Information Technology: Telecommunications and Information Exchange Between Systems: Local and Metropolitan Area Networks: Specific Requirements, Part 2: Logical Link Control," IEEE Std 802.2-1998, 1998.
4. B. A. Forouzan, *Data Communications and Networking*, 5th ed. New York, NY, USA: McGraw-Hill, 2013.
5. ISO, "Road Vehicles: Local Interconnect Network (LIN), Part 3: Protocol Specification," ISO 17987-3:2016, 2016.
6. Robert Bosch GmbH, "CAN Specification Version 2.0," Stuttgart, Germany, 1991.
7. Robert Bosch GmbH, "CAN with Flexible Data-Rate Specification Version 1.0," 2012.
8. ISO, "Road Vehicles: Controller Area Network (CAN), Part 1: Data Link Layer and Physical Signalling," ISO 11898-1:2015, 2015.
