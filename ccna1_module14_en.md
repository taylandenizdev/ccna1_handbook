# CCNA1 - Module 14: Transport Layer (L4)
> **Source:** Cisco Networking Academy — Introduction to Networks v7.0 (ITN)  
> **Instructors:** Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 Module Objectives

| Topic | Objective |
|-------|-----------|
| Transporting Data | Explain the purpose of the transport layer in managing end-to-end communications |
| TCP Overview | Explain TCP characteristics |
| UDP Overview | Explain UDP characteristics |
| Port Numbers | Explain how TCP and UDP use port numbers |
| TCP Communication Process | Explain how TCP session establishment and termination work |
| Reliability and Flow Control | Explain how TCP provides reliable delivery and flow control |
| UDP Communication | Explain the UDP communication process |

---

## Reminder: Layered Packet Structure

```
┌─────────────────────────────────────────────────────────────────────┐
│                       APPLICATION LAYER DATA                       │
├──────────────┬──────────────────────────────────────────────────────┤
│  UDP (8 B)   │               UDP Data                               │
├──────────────┼──────────────────────────────────────────────────────┤
│  TCP (20 B)  │               TCP Data                               │
├──────────────┼──────────────────────────────────────────────────────┤
│  IP  (20 B)  │               IP Data                                │
├──────────────┼─────────────────────────────────────────────┬────────┤
│ Ethernet(18B)│          46 – 1500 Bytes Ethernet Data      │  CRC   │
└──────────────┴─────────────────────────────────────────────┴────────┘
```

---

## 14.1 Transporting Data

### The Role of the Transport Layer

The transport layer (Layer 4) is responsible for **logical communication between applications** running on different hosts. It acts as a bridge between the application layer and the lower layers responsible for network transmission.

```
┌──────────────────────────────────────────────────────────────┐
│             WHY DO WE NEED THE TRANSPORT LAYER?              │
│                                                              │
│  Multiple apps running simultaneously on one PC:             │
│                                                              │
│   WhatsApp → port 50001                                      │
│   Spotify  → port 50002     Packet arrived!                  │
│   Firefox  → port 50003     Which app gets it?               │
│   Netflix  → port 50004     ─────────────────────────────    │
│                             ANSWER: Check the port number!   │
└──────────────────────────────────────────────────────────────┘
```

### Transport Layer Responsibilities

**Segmentation:** Breaks data into small pieces and adds header information to each piece.

**Reassembly:** At the destination, segments are reassembled in order using sequence numbers to recreate the original data.

**Identifying Applications:** Port numbers determine which application the packet should be delivered to.

**Multiplexing:** Allows multiple applications to communicate simultaneously over the same network.

```
Multiplexing example:
Traffic leaving a PC at the same time:
  Port 50001 → WhatsApp server
  Port 50002 → Spotify server
  Port 50003 → Google (HTTPS 443)
  Port 50004 → Netflix server
  ──────────────────────────────
  All share the same internet connection!
```

### Choosing the Right Protocol

```
┌─────────────────────────────────────────────────────────────────────┐
│                       PROTOCOL SELECTION                           │
│                                                                     │
│          UDP                             TCP                        │
│  ┌──────────────────┐          ┌──────────────────────┐            │
│  │ Fast             │          │ Reliable             │            │
│  │ Low overhead     │          │ Ordered delivery     │            │
│  │ No acknowledgement│         │ Resends lost data    │            │
│  │ No sequence nums │          │ Connection-oriented  │            │
│  │ Connectionless   │          │                      │            │
│  └──────────────────┘          └──────────────────────┘            │
│  VoIP, DNS, DHCP,              HTTP/HTTPS, FTP, SMTP,              │
│  SNMP, TFTP, video conf.       SSH, email                          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 14.2 TCP Overview

### TCP Characteristics

TCP (Transmission Control Protocol) is a **connection-oriented** protocol providing **reliable** and **ordered** data delivery.

**Establishes a Session (Connection-Oriented):** Before sending data, a session is established between client and server via a 3-way handshake.

**Reliable Delivery:** Each segment is assigned a sequence number. Lost segments are detected and retransmitted.

**Same-Order Delivery:** Segments arriving out of order are reordered using sequence numbers.

**Flow Control:** Data is sent at a rate the receiver can handle; bandwidth is dynamically adjusted.

### TCP Header Structure (20 Bytes)

```
┌────────────────────────┬────────────────────────────────────────┐
│   Source Port (16 bit) │      Destination Port (16 bit)         │
├────────────────────────┴────────────────────────────────────────┤
│                      Sequence Number (32 bit)                   │
├─────────────────────────────────────────────────────────────────┤
│                   Acknowledgement Number (32 bit)               │
├──────────┬──────────────┬──────────────┬────────────────────────┤
│Hdr Len(4)│ Reserved (6) │ Flags (6 bit)│   Window Size (16 bit) │
├──────────┴──────────────┴──────────────┴────────────────────────┤
│              Checksum (16 bit)          │  Urgent (16 bit)       │
├─────────────────────────────────────────────────────────────────┤
│                     Options (0 or 32 bit)                       │
├─────────────────────────────────────────────────────────────────┤
│                Application Layer Data (variable length)          │
└─────────────────────────────────────────────────────────────────┘
```

### TCP Header Fields

| Field | Size | Description |
|-------|------|-------------|
| Source Port | 16 bit | Port number of the source application |
| Destination Port | 16 bit | Port number of the destination application |
| Sequence Number | 32 bit | Byte number of the segment (tracks position in stream) |
| Acknowledgement Number | 32 bit | Next expected byte number from the other side |
| Header Length | 4 bit | Length of the TCP header |
| Control Bits | 6 bit | SYN, ACK, FIN, RST, PSH, URG flags |
| Window Size | 16 bit | Bytes sender can send before waiting for an ACK |
| Checksum | 16 bit | Error detection |
| Urgent | 16 bit | Indicates urgent data |

### Applications Using TCP

| Protocol | Port | Description |
|----------|------|-------------|
| FTP (Data) | TCP 20 | File transfer (data) |
| FTP (Control) | TCP 21 | File transfer (control) |
| SSH | TCP 22 | Secure remote access |
| Telnet | TCP 23 | Remote command-line access |
| SMTP | TCP 25 | Email sending |
| HTTP | TCP 80 | Web pages |
| HTTPS | TCP 443 | Encrypted web pages |

---

## 14.3 UDP Overview

### UDP Characteristics

UDP (User Datagram Protocol) is a **connectionless** protocol offering **fast** and **low-overhead** data delivery.

- No sequence numbers → segments are not reordered
- No acknowledgement mechanism → lost packets are not retransmitted
- No connection establishment → connectionless
- No flow control

> UDP is a **"Best Effort Delivery"** protocol — it tries its best but makes no guarantees.

### UDP Header Structure (8 Bytes)

```
┌──────────────────────────┬──────────────────────────┐
│   Source Port (16 bit)   │   Destination Port (16b) │  8
├──────────────────────────┼──────────────────────────┤ Bytes
│    Length (16 bit)       │   Checksum (16 bit)      │
├──────────────────────────┴──────────────────────────┤
│          Application Layer Data (variable)          │
└─────────────────────────────────────────────────────┘
```

### UDP Header Fields

| Field | Size | Description |
|-------|------|-------------|
| Source Port | 16 bit | Port number of the source application |
| Destination Port | 16 bit | Port number of the destination application |
| Length | 16 bit | Total length of the datagram |
| Checksum | 16 bit | Error detection |

### Applications Using UDP

| Protocol | Port | Reason for UDP |
|----------|------|----------------|
| DNS | UDP 53 | Single question–single answer (speed sufficient) |
| DHCP | UDP 67/68 | Single question–single answer |
| TFTP | UDP 69 | Simple file transfer |
| SNMP | UDP 161 | Network device management queries |
| VoIP / Video Conf. | Dynamic | Real-time transmission required |

> **Why does real-time traffic use UDP?**  
> In a voice or video call, asking to "resend" a lost packet is pointless — that moment is gone. Speed is the priority.

---

## TCP vs UDP Comparison

```
┌──────────────────────────────┬──────────────────────────────────┐
│             TCP              │              UDP                 │
├──────────────────────────────┼──────────────────────────────────┤
│ Connection-oriented          │ Connectionless                   │
│ 3-way handshake              │ No handshake                     │
│ Sequence numbers             │ No sequence numbers              │
│ Acknowledgement (ACK)        │ No acknowledgement               │
│ Lost packets retransmitted   │ Lost packets not retransmitted   │
│ Flow control                 │ No flow control                  │
│ 20 byte header               │ 8 byte header                    │
│ Slower, more reliable        │ Faster, less reliable            │
│ HTTP, FTP, SMTP, SSH         │ DNS, DHCP, VoIP, SNMP, TFTP      │
└──────────────────────────────┴──────────────────────────────────┘
```

---

## 14.4 Port Numbers

### What Is a Port Number?

Port numbers are 16 bits wide and range from **0 to 65,535**. An IP address identifies a device; a port number identifies the **application** running on that device.

```
Socket = IP Address + Port Number
Example: 192.168.1.5:49152

Socket Pair:
  Source: 192.168.1.5:49152   ──►  Destination: 216.58.213.46:443
```

### Port Number Groups (IANA)

```
┌─────────────────────────────────────────────────────────────────────┐
│                      PORT NUMBER HIERARCHY                         │
│                                                                     │
│  0 – 1023       Well-Known Ports                                   │
│                 Reserved for common, widely known applications      │
│                 Example: HTTP=80, HTTPS=443, FTP=21, SSH=22         │
│                                                                     │
│  1024 – 49151   Registered Ports                                   │
│                 Assigned to commercial applications (IANA-registered)│
│                 Example: RDP=3389, MySQL=3306, RADIUS=1812          │
│                                                                     │
│  49152 – 65535  Dynamic / Private Ports                            │
│                 Temporarily assigned by client applications         │
│                 (used as source port numbers)                       │
└─────────────────────────────────────────────────────────────────────┘
```

### Common Port Numbers (Memorize These!)

| Port | Protocol | Description |
|------|----------|-------------|
| 20 | TCP | FTP – Data |
| 21 | TCP | FTP – Control |
| 22 | TCP | SSH – Secure remote access |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP – Email sending |
| 53 | UDP/TCP | DNS |
| 67 | UDP | DHCP – Server |
| 68 | UDP | DHCP – Client |
| 69 | UDP | TFTP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 – Email retrieval |
| 143 | TCP | IMAP – Email retrieval |
| 161 | UDP | SNMP |
| 443 | TCP | HTTPS – Secure web |
| 1812 | UDP | RADIUS – Authentication |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP – Remote Desktop |

### The Multiple-Tab Problem: Why Source Port Matters

```
┌──────────────────────────────────────────────────────────────────┐
│  Browser has 2 tabs open to the same website:                   │
│                                                                  │
│  Tab 1: Source port 50001 → Destination 443 (google.com)       │
│  Tab 2: Source port 50002 → Destination 443 (google.com)       │
│                                                                  │
│  Server response arrives:                                        │
│    ├── Destination port 50001 → routed to Tab 1                │
│    └── Destination port 50002 → routed to Tab 2                │
│                                                                  │
│  Source port = return address for the response                   │
└──────────────────────────────────────────────────────────────────┘
```

### The netstat Command

Used to display active network connections on the local machine.

```
C:\> netstat -n        → Show all connections numerically
C:\> netstat -a        → Show TCP + UDP connections
C:\> netstat -b        → Show which app owns which port (Admin required)
C:\> netstat -o        → Include process ID
C:\> netstat -ano      → Show all of the above (most useful)

Sample output:
Proto  Local Address          Foreign Address        State
TCP    192.168.1.5:50573       216.58.213.46:443      ESTABLISHED
TCP    192.168.1.5:50574       52.114.132.46:443      ESTABLISHED
UDP    0.0.0.0:5353            *:*

Connection states:
  ESTABLISHED  → Active connection (data transfer)
  SYN_SENT     → 3-way handshake initiated
  CLOSE_WAIT   → FIN sent, waiting for termination
  LISTEN       → Server accepting requests on this port
```

---

## 14.5 TCP Communication Process

### The 3 Phases of TCP Communication

```
┌─────────────────────────────────────────────────────────────────┐
│                  TCP COMMUNICATION PROCESS                      │
│                                                                 │
│  Phase 1: CONNECTION ESTABLISHMENT  (3-Way Handshake)          │
│  Phase 2: DATA TRANSFER                                         │
│  Phase 3: CONNECTION TERMINATION    (4 Packets)                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3-Way Handshake

```
Client                                           Server
  │                                                │
  │  Step 1: SYN  (SYN=1, ACK=0)                 │
  │  Seq=100, Src Port=50000, Dst Port=80         │
  │ ─────────────────────────────────────────────► │  LISTEN
  │                                                │
  │  Step 2: SYN + ACK  (SYN=1, ACK=1)           │
  │  Seq=300, Ack=101                             │
  │  Src Port=80, Dst Port=50000                  │
  │ ◄─────────────────────────────────────────────  │
  │                                                │
  │  Step 3: ACK  (SYN=0, ACK=1)                 │
  │  Seq=101, Ack=301                             │
  │  Src Port=50000, Dst Port=80                  │
  │ ─────────────────────────────────────────────► │
  │                                                │
  │             *** ESTABLISHED ***                │
  │  ──────────── DATA TRANSFER ────────►          │
```

**Step 1 – SYN:** Client sends a connection request. SYN=1, a random Initial Sequence Number (ISN) is chosen.

**Step 2 – SYN+ACK:** Server acknowledges the client's SYN (ACK = client's Seq+1) and sends its own SYN.

**Step 3 – ACK:** Client acknowledges the server's SYN. Session is established.

> No data is carried in these 3 packets — they only negotiate session parameters.

### TCP Control Bits (Flags)

| Flag | Meaning | Usage |
|------|---------|-------|
| **SYN** | Synchronize — connection request | 3-way handshake |
| **ACK** | Acknowledgement — confirmation | Confirming received packets |
| **FIN** | Finish — end of data | Session termination |
| **RST** | Reset — force close | Error handling or forceful disconnect |
| **PSH** | Push — deliver immediately | Pass data to app without buffering |
| **URG** | Urgent — priority data | Marks urgent data |

### TCP Connection Termination (4 Packets)

```
Client (A)                                     Server (B)
     │                                               │
     │  Step 1: FIN  ─────────────────────────────► │
     │                                               │
     │  Step 2: ACK  ◄─────────────────────────────  │
     │                                               │
     │  Step 3: FIN  ◄─────────────────────────────  │
     │                                               │
     │  Step 4: ACK  ─────────────────────────────► │
     │                                               │
     │             *** SESSION CLOSED ***            │
```

---

## 14.6 Reliability and Flow Control

### Sequence Numbers and Byte Tracking

In TCP, the sequence number is actually a **byte number**, not a packet number.

```
Example — sending a 3000-byte file:

Packet 1: Seq=1,    bytes 1–1460 sent      →
Response: Ack=1461  "Got up to byte 1460"  ←

Packet 2: Seq=1461, bytes 1461–2920 sent   →
Response: Ack=2921  "Got up to byte 2920"  ←

Packet 3: Seq=2921, bytes 2921–3000 sent   →
Response: Ack=3001  "All done!"            ←
```

### Lost Packets and Retransmission

```
Standard retransmission (older method):
  Seg 1  -->  arrived
  Seg 2  -->  arrived
  Seg 3  -->  LOST!
  Seg 4  -->  arrived
  Seg 5  -->  arrived
               ACK=3 (send from segment 3 onwards!)
  Seg 3,4,5 are retransmitted (4 and 5 are unnecessary duplicates)
```

```
With SACK (modern method):
  Seg 1  -->  arrived
  Seg 2  -->  arrived
  Seg 3  -->  LOST!
  Seg 4  -->  arrived
  Seg 5  -->  arrived
               ACK=3, SACK=4-5 (only send 3, I have 4 and 5)
  Only Seg 3 is retransmitted
               ACK=6 (continue)
```

> **SACK (Selective Acknowledgement):** An optional TCP feature negotiated during the 3-way handshake. Allows the receiver to explicitly confirm which segments arrived (including gaps). Only truly missing segments are retransmitted.

### Flow Control — Window Size

**Window Size:** Specifies the maximum number of bytes that can be sent before waiting for an acknowledgement.

```
┌─────────────────────────────────────────────────────────────────────┐
│                     WINDOW SIZE MECHANISM                          │
│                                                                     │
│  Server buffer empty    → Window Size large  → Send fast           │
│  Server buffer filling  → Window Size shrinks → Send slower        │
│  Server buffer full     → Window Size = 0    → Stop! Wait          │
│                                                                     │
│  Example flow:                                                      │
│    Ack: 10,001, Window: 10,000  → Send 10 packets                  │
│    Ack: 20,001, Window:  5,000  → Send 5 packets (congestion)      │
│    Ack: 25,001, Window:  1,000  → Send 1 packet (buffer ~90%)      │
│    Ack: 26,001, Window:      0  → Stop! Buffer full                │
│    Ack: 26,001, Window:  5,000  → Buffer cleared, continue         │
└─────────────────────────────────────────────────────────────────────┘
```

### Sliding Window

The server sends acknowledgements inline — before the full window is exhausted — so the sender can transmit continuously without pausing.

```
Window=10,000 bytes, MSS=1460:

Packet 1 (bytes 1-1460)      ──────────────────────────────►
Packet 2 (bytes 1461-2920)   ──────────────────────────►
          ◄── ACK=2921, Window=10,000 (inline ACK!)
Packet 3 (bytes 2921-4380)   ──────────────────────►
Packet 4 (bytes 4381-5840)   ────────────────────►
          ◄── ACK=5841, Window=10,000 (inline ACK!)
  Continuous flow — no pausing...
```

### Maximum Segment Size (MSS)

```
Ethernet MTU   = 1500 bytes
  – IP Header  =   20 bytes
  – TCP Header =   20 bytes
  ───────────────────────────
  MSS          = 1460 bytes  ← max data per TCP segment
```

> When using a VPN, a second IP header is added, so MSS becomes 1440 bytes.

### Congestion Avoidance

If acknowledgements stop arriving, the sender reduces the window size, slows down transmission, then gradually increases speed again once the network recovers.

---

## SUPPLEMENT: SYN Attack (DoS)

```
┌─────────────────────────────────────────────────────────────────────┐
│                      HOW A SYN ATTACK WORKS                        │
│                                                                     │
│  Attacker → SYN ──────────────────────────────► Server            │
│  Attacker → SYN ──────────────────────────────► Server            │
│  (repeated 1000 times)                                              │
│                                                                     │
│  Server: For each SYN → open port, send SYN+ACK, wait for ACK     │
│  But the attacker never sends the 3rd ACK!                          │
│                                                                     │
│  Result: 1000 half-open sessions → all connection slots filled     │
│          Legitimate user can't connect → Denial of Service!         │
│                                                                     │
│  This attack is called SYN FLOOD or SYN ATTACK.                    │
│  When launched from multiple IPs → DDoS (Distributed DoS)          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 14.7 UDP Communication

### UDP Communication Flow

```
          Client (DNS Query)                 Server (DNS)
               │                                  │
               │   IP Header + UDP Header         │
               │   + "What is google.com's IP?"   │
               │ ────────────────────────────────► │
               │                                  │
               │   IP Header + UDP Header         │
               │   + "google.com = 1.2.3.4"       │
               │ ◄────────────────────────────────  │

With TCP: 3 + 2 + 4 = 9+ packets
With UDP: 2 packets — much faster!
```

### UDP Out-of-Order Problem

```
Sent:     Datagram 1, 2, 3, 4, 5, 6
Received: Datagram 1, 2, 6, 5, 4     (wrong order, 3 missing)

UDP's response: Deliver as-is, no reordering.
In a voice call, this sounds like a "glitch" or dropout.
```

---

## SUPPLEMENT: NAT and Port Forwarding

### Multiple Devices Sharing One Public IP (NAT + PAT)

```
┌──────────────────────────────────────────────────────────────────┐
│                        NAT TABLE                                │
│                                                                  │
│  Internal IP:Port       External IP:Port    Destination          │
│  192.168.1.2:49152  →  200.100.10.1:1024  → Google:443          │
│  192.168.1.3:49152  →  200.100.10.1:1025  → Yahoo:443           │
│  192.168.1.4:49153  →  200.100.10.1:1026  → YouTube:443         │
│                                                                  │
│  Reply arrives to 200.100.10.1:1025 → forward to 192.168.1.3   │
└──────────────────────────────────────────────────────────────────┘
```

### Port Forwarding

Allows external traffic to reach an internal server:

```
Incoming request from the internet:
  166.124.130.26:10443
        ↓  (modem port forwarding rule)
  192.168.0.186:443  (internal web server)
```

---

## 🧠 Key Takeaways

1. **TCP = reliable, UDP = fast** — Use TCP for guaranteed delivery, UDP for speed.
2. **Segment** = TCP's data unit. **Datagram** = UDP's data unit.
3. **Source port** = the return address ensuring a response reaches the right application.
4. **3-way handshake** = SYN → SYN+ACK → ACK (3 packets, 0 data).
5. **Session termination** = FIN → ACK → FIN → ACK (4 packets).
6. **Window Size = 0** means "stop, wait — buffer is full."
7. **MSS = 1460 bytes** (Ethernet MTU 1500 – IP header 20 – TCP header 20).
8. **SYN Attack** = flooding a server with half-open sessions to exhaust connection slots.
9. **Port numbers range 0–65535** (16 bits). Dynamic ports: 49152–65535.
10. **netstat -ano** shows which application owns which port on your machine.

---

## 📝 Module Summary

```
TRANSPORT LAYER (L4)
├── TCP (Transmission Control Protocol)
│   ├── Connection-Oriented
│   ├── 3-Way Handshake: SYN → SYN+ACK → ACK
│   ├── Sequence Number — byte-level tracking
│   ├── Acknowledgement Number
│   ├── Retransmission of unacknowledged segments
│   ├── SACK (Selective Acknowledgement)
│   ├── Window Size — flow control
│   ├── Sliding Window — continuous transmission
│   ├── MSS = 1460 bytes
│   └── Connection Termination: FIN → ACK → FIN → ACK
│
├── UDP (User Datagram Protocol)
│   ├── Connectionless
│   ├── 8-byte header (port, length, checksum only)
│   ├── No sequence numbers
│   ├── No acknowledgements
│   └── Uses: DNS, DHCP, VoIP, SNMP, TFTP
│
└── PORT NUMBERS
    ├── 0–1023:       Well-Known (SSH=22, HTTP=80, HTTPS=443)
    ├── 1024–49151:   Registered (RDP=3389, MySQL=3306)
    └── 49152–65535:  Dynamic / Ephemeral (client source ports)
```

---

## 📖 New Terms and Commands

| Term | Description |
|------|-------------|
| Segment | TCP's data unit |
| Datagram | UDP's data unit |
| Multiplexing | Multiple applications communicating simultaneously |
| Three-Way Handshake | TCP session establishment mechanism |
| SYN | Synchronize — session initiation flag |
| ACK | Acknowledgement flag |
| FIN | Finish — session termination flag |
| RST | Reset — forces session closure |
| ISN | Initial Sequence Number |
| SACK | Selective Acknowledgement |
| Sliding Window | Continuous flow mechanism |
| MSS | Maximum Segment Size = 1460 bytes |
| MTU | Maximum Transmission Unit = 1500 bytes (Ethernet) |
| Socket | IP Address + Port Number combination |
| Socket Pair | Source socket + Destination socket pair |
| Flow Control | Regulating transmission rate |
| Congestion Avoidance | Reducing transmission rate when network is congested |
| netstat | Command displaying active network connections |

---

*CCNA1 Module 14 Handbook — agyoneticileri.org*
