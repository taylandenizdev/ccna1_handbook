# CCNA1 Module 3: Protocols and Models


---

## TABLE OF CONTENTS

1. [Core Concepts: MAC & IP Addresses](#core-concepts)
2. [Communication Rules (Protocols)](#communication-rules)
3. [Protocol Suites](#protocol-suites)
4. [Standards Organizations](#standards-organizations)
5. [Reference Models: OSI & TCP/IP](#reference-models)
6. [Data Encapsulation & De-encapsulation](#data-encapsulation)
7. [Data Access: Same & Different Networks](#data-access)
8. [Summary & Exam Notes](#summary-exam-notes)

---

## Core Concepts

### MAC Address (Physical Address)

A MAC (Media Access Control) address is a hardware address permanently embedded in the Network Interface Card (NIC).

**Key Properties:**
- 48 bits in length
- Expressed as 12 hexadecimal characters
- Example: `9C-35-5B-5F-AA-BB`
- Also called "Burned-in Address"
- Unique worldwide — no two NICs share the same MAC address
- Used **only within the local network (LAN)**
- Every NIC (wired Ethernet + wireless Wi-Fi) has its own MAC address

```
MAC Address Structure (48 bits = 6 bytes):
┌─────────────────┬─────────────────┐
│   OUI (24-bit)  │  Device ID(24b) │
│  Vendor Code    │  Serial Number  │
└─────────────────┴─────────────────┘
    9C-35-5B     -    5F-AA-BB
```

---

### IP Address (Logical Address)

An IP address is a software-assigned address that identifies where a device sits in the network.

**Key Properties:**
- IPv4: 32 bits, 4 octets (e.g., `192.168.1.5`)
- Each octet ranges 0–255
- Logical address — changes when moving between networks
- Two-part structure: **Network portion** + **Host portion**

```
IP Address Structure:
┌──────────────────┬──────────┐
│  Network Portion │  Host P. │
│  (Which network) │(Which host)
└──────────────────┴──────────┘
   192.168.1          .5
```

**4 Settings Needed to Connect to Internet:**

| Setting | Purpose | Example |
|---------|---------|---------|
| IP Address | Device identity on the network | 192.168.1.5 |
| Subnet Mask | Defines network/host boundary | 255.255.255.0 |
| Default Gateway | IP of the local router | 192.168.1.1 |
| DNS Server | Resolves names to IPs | 8.8.8.8 |

---

### Switch vs Router

```
Network Topology:
────────────────────────────────────────────────────────
[PC1]──┐                                    ┌──[WebSrv]
.1.5   │ [Switch1]──[R1]──[R2]──[R3]──[Switch2]
[PC2]──┘                                    └──[DNSSrv]
.1.6   192.168.1.0  .2.0/.3.0  192.168.4.0

Switch → forwards based on MAC address (Layer 2)
Router → forwards based on IP address  (Layer 3)
────────────────────────────────────────────────────────
```

| Device | Layer | Table | Has IP? |
|--------|-------|-------|---------|
| Switch | Layer 2 | MAC Table | No* |
| Router | Layer 3 | Routing Table | Yes (per interface) |
| PC/Server | All layers | — | Yes |

---

## Communication Rules

### What Is a Protocol?

A **protocol** is a predefined set of rules that govern communication. Like postal conventions (recipient center, sender top-left), computer protocols define how data must be structured and transmitted.

### Network Protocol Requirements

**a) Message Encoding**
- Converting data into signals appropriate for the medium
- Copper cable → voltage levels (electrical pulses)
- Fiber optic → light pulses
- Wireless → electromagnetic waves
- Receiver decodes signals back to data

**b) Message Formatting and Encapsulation**
- Every message must follow a specific format/structure
- Headers and trailers are added to the data
- Analogy: data = letter contents, headers = information written on the envelope

**c) Message Size**
- Large data is broken into smaller pieces (segments)
- Why? If 1 bit is corrupted in a 500 MB single transmission, everything must be re-sent
- With segmentation, only the corrupted segment is re-requested

**d) Message Timing**

| Concept | Description |
|---------|-------------|
| Flow Control | Manages transmission rate; speeds up/slows down based on capacity |
| Response Timeout | Retransmits if no acknowledgment received |
| Access Method | Determines who can send and when (e.g., CSMA/CA for Wi-Fi) |

**e) Message Delivery Options**

```
Three Delivery Types:
┌──────────┐    ┌────────────┐    ┌───────────┐
│ UNICAST  │    │ MULTICAST  │    │ BROADCAST │
│          │    │            │    │           │
│  A → B   │    │  A → Group │    │ A → All   │
│ (1-to-1) │    │ (1-to-many)│    │ (1-to-all)│
└──────────┘    └────────────┘    └───────────┘

Note: IPv6 does NOT use Broadcast → uses Anycast instead
```

---

## Protocol Suites

### Historical Evolution

```
Protocol Suite Evolution:
──────────────────────────────────────────
1985  AppleTalk     → For Apple devices
1983  SPX/IPX       → For Novell NetWare
1969  TCP/IP        → ARPA / DoD
──────────────────────────────────────────
         ↓
TCP/IP encompassed all platforms and
became the de facto internet standard
──────────────────────────────────────────
```

### TCP/IP Protocol Suite

```
TCP/IP Layers and Protocols:
┌─────────────────────────────────────────────────────┐
│                APPLICATION LAYER                    │
│  DNS │ DHCP │ SMTP │ POP3 │ IMAP │ FTP │ HTTP │HTTPS│
├─────────────────────────────────────────────────────┤
│                TRANSPORT LAYER                      │
│         TCP (reliable)  │  UDP (fast)               │
├─────────────────────────────────────────────────────┤
│                INTERNET LAYER                       │
│    IPv4 │ IPv6 │ ICMPv4 │ ICMPv6 │ OSPF │ BGP      │
├─────────────────────────────────────────────────────┤
│              NETWORK ACCESS LAYER                   │
│         ARP │ Ethernet │ WLAN (802.11)              │
└─────────────────────────────────────────────────────┘
```

---

## Standards Organizations

### Internet Standards Hierarchy

```
Internet Governance Hierarchy:
┌─────────────────────────────┐
│    ISOC (Internet Society)  │  ← Overall internet development
└──────────────┬──────────────┘
               │
┌──────────────▼──────────────┐
│    IAB (Internet Arch. Bd.) │  ← Architecture management
└─────────┬────────┬──────────┘
          │        │
┌─────────▼──┐ ┌───▼────────┐
│    IETF    │ │    IRTF    │
│(Protocol   │ │(Research)  │
│development)│ │            │
└────────────┘ └────────────┘
      │
  RFC Documents
  (Technical specs for HTTP, TCP, IP
   — publicly available)
```

### Organizations and Responsibilities

| Organization | Role | Key Standards |
|-------------|------|--------------|
| **ISOC** | Supports open internet development | — |
| **IAB** | Manages internet architecture | — |
| **IETF** | Develops TCP/IP protocols | RFC documents |
| **IRTF** | Long-term internet research | AI, cryptography |
| **ICANN** | Coordinates IP allocation & domain names | — |
| **IANA** | Manages IP blocks, port numbers, domains | Port 80=HTTP, 443=HTTPS |
| **IEEE** | Hardware standards | 802.3 (Ethernet), 802.11 (Wi-Fi) |
| **EIA/TIA** | Cable & connector standards | TIA-568B wiring |
| **ITU-T** | Telecommunications standards | ADSL, VDSL2 |

### Common Port Numbers

| Protocol | Port | Description |
|---------|------|-------------|
| HTTP | 80 | Unencrypted web |
| HTTPS | 443 | Encrypted web (TLS) |
| SMTP | 25 | Email sending |
| FTP | 20/21 | File transfer |
| DNS | 53 | Name resolution |
| DHCP | 67/68 | Auto IP assignment |
| SSH | 22 | Secure remote access |

---

## Reference Models

### Why Layered Models?

Benefits of using layered models:
- Each layer can be updated independently (IPv4 → IPv6 transition didn't break TCP or HTTP)
- Products from different vendors interoperate
- Easier troubleshooting
- Provides common language/terminology

### OSI Reference Model (7 Layers)

```
OSI Reference Model:
┌───┬──────────────┬─────────────────────────────────────────┐
│ 7 │ APPLICATION  │ End-user interface                      │
│   │              │ HTTP, HTTPS, FTP, SMTP, DNS, DHCP       │
├───┼──────────────┼─────────────────────────────────────────┤
│ 6 │PRESENTATION  │ Data format: JPEG, PDF, ASCII           │
│   │              │ Encryption, Compression                 │
├───┼──────────────┼─────────────────────────────────────────┤
│ 5 │   SESSION    │ Session management, synchronization     │
│   │              │ Login sessions, file transfer tracking  │
├───┼──────────────┼─────────────────────────────────────────┤
│ 4 │  TRANSPORT   │ Port numbers, segmentation              │
│   │              │ Sequence no., ACK no., flow control     │
│   │              │ TCP (reliable), UDP (fast)              │
├───┼──────────────┼─────────────────────────────────────────┤
│ 3 │   NETWORK    │ IP addresses, path selection (routing)  │
│   │              │ IP, ICMP, routing protocols             │
├───┼──────────────┼─────────────────────────────────────────┤
│ 2 │  DATA LINK   │ MAC addresses, error control (CRC)      │
│   │              │ Ethernet, PPP, Framing                  │
├───┼──────────────┼─────────────────────────────────────────┤
│ 1 │   PHYSICAL   │ Bit transmission, cables, connectors    │
│   │              │ Encoding, copper/fiber/wireless         │
└───┴──────────────┴─────────────────────────────────────────┘

 ←── APPLICATION LAYERS (7-6-5): Data creation
 ←── DATA TRANSMISSION LAYERS (4-3-2-1): Transportation
```

**OSI Layer Mnemonic (bottom to top):**
> **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

### Encapsulation Process

```
Encapsulation (at the Sender):

 7-6-5  ┌──────────────────────────┐
        │          DATA            │  ← HTTP request created
        └──────────────────────────┘
   4    ┌────────┬─────────────────┐
        │TCP HDR │      DATA       │  ← Source/Dest PORT added
        │Src:5001│                 │    Seq No, ACK No added
        │Dst:443 │                 │
        └────────┴─────────────────┘
   3    ┌──────┬──────┬────────────┐
        │IP HDR│TCHDR │    DATA    │  ← Source/Dest IP added
        │SrcIP │      │            │
        │DstIP │      │            │
        └──────┴──────┴────────────┘
   2    ┌────┬──────┬──────┬──────┬────┐
        │FHDR│IP HDR│TCHDR │ DATA │FCS │  ← MAC addresses
        │MAC │      │      │      │CRC │    + Error check added
        └────┴──────┴──────┴──────┴────┘
   1    01001011010110101001011010001...    ← Bits transmitted
```

### TCP/IP Model (4 Layers)

```
TCP/IP vs OSI Model Comparison:

  OSI Model        TCP/IP Model
 ┌───────────┐    ┌──────────────┐
 │Application│    │              │
 │     7     │    │  Application │  HTTP, DNS, FTP, SMTP
 │Presentation│   │      4       │
 │     6     │    │              │
 │  Session  │    └──────────────┘
 │     5     │    ┌──────────────┐
 ├───────────┤    │  Transport   │  TCP, UDP
 │ Transport │    │      3       │
 │     4     │    └──────────────┘
 ├───────────┤    ┌──────────────┐
 │  Network  │    │   Internet   │  IPv4, IPv6, ICMP
 │     3     │    │      2       │
 ├───────────┤    └──────────────┘
 │ Data Link │    ┌──────────────┐
 │     2     │    │Network Access│  Ethernet, WLAN, ARP
 ├───────────┤    │      1       │
 │ Physical  │    │              │
 │     1     │    └──────────────┘
 └───────────┘
```

### Critical Layer Mapping (Exam Ready)

| OSI Layer | TCP/IP Layer | Protocols | Address Added |
|-----------|-------------|-----------|--------------|
| 7-6-5 App/Pres/Session | Application | HTTP, HTTPS, FTP, DNS, DHCP | — |
| 4 Transport | Transport | TCP, UDP | Port number |
| 3 Network | Internet | IPv4, IPv6, ICMP | IP address |
| 2 Data Link | Network Access | Ethernet, WLAN | MAC address |
| 1 Physical | Network Access | — | — (bits) |

---

## Data Encapsulation

### PDU (Protocol Data Unit) — Layer-by-Layer Data Names

```
PDU Names by Layer:
┌─────────────┬──────────────┬──────────────────────────────┐
│    Layer    │   PDU Name   │         Content              │
├─────────────┼──────────────┼──────────────────────────────┤
│ 7-6-5       │    DATA      │ Application data             │
│ 4 Transport │  SEGMENT     │ TCP/UDP header + data        │
│ 3 Network   │   PACKET     │ IP header + segment          │
│ 2 Data Link │   FRAME      │ MAC headers + packet + FCS   │
│ 1 Physical  │    BIT       │ 0s and 1s                    │
└─────────────┴──────────────┴──────────────────────────────┘
```

### Segmentation and Multiplexing

```
Segmentation Benefit:
─────────────────────────────────────────────────────
500 MB file → SINGLE piece:
   • 1 bit corrupted → 500 MB must be retransmitted!

500 MB file → SEGMENTED:
   • 1 segment corrupted → only that segment is re-requested
   • Multiple apps can send simultaneously
─────────────────────────────────────────────────────
```

### TCP Reliable Delivery

```
TCP Reliable Transmission:
─────────────────────────────────────────────────────
Sender             Network          Receiver
   │                                    │
   ├─── Segment 1 ─────────────────────►│  (received)
   ├─── Segment 2 ──────────► ✗ LOST   │  (lost)
   ├─── Segment 3 ─────────────────────►│  (received)
   │                                    │
   │          ◄─── ACK: "Send Segment 2!" ──────────┤
   │                                    │
   ├─── Segment 2 (retransmit) ─────────►│  (received)
   │                                    │
   │          ◄─── ACK: "Send Segment 4!" ──────────┤
─────────────────────────────────────────────────────
```

**TCP vs UDP:**

| Feature | TCP | UDP |
|---------|-----|-----|
| Reliability | ✅ Yes (ACK) | ❌ No |
| Sequence numbers | ✅ Yes | ❌ No |
| Speed | Slower | **Faster** |
| Use cases | Web, email, FTP | DNS, VoIP, video streaming |

---

## Data Access

### Communication on the Same Network

```
Same-Network Communication (192.168.1.0/24):
────────────────────────────────────────────────
[PC1: IP=192.168.1.5, MAC=AA:AA:AA:AA:AA:AA]
    │
[Switch]
    │
[PC2: IP=192.168.1.6, MAC=BB:BB:BB:BB:BB:BB]

Frame Structure:
┌──────────────────┬──────────┬──────────┐
│   Frame Header   │ IP Packet│   DATA   │
│ Dst: BB:BB...    │ SrcIP:   │          │
│ Src: AA:AA...    │ .1.5     │          │
│                  │ DstIP:   │          │
│                  │ .1.6     │          │
└──────────────────┴──────────┴──────────┘
Dest MAC = Destination device's MAC (learned via ARP)
────────────────────────────────────────────────
```

**ARP (Address Resolution Protocol):** When IP is known, ARP broadcasts to discover the MAC.

### Communication to a Different Network

```
Packet Traveling Across Networks:
──────────────────────────────────────────────────────────
[PC1]          [Router1]         [Router2]         [WebSrv]
192.168.1.5    .1.1|.2.1        .2.2|.3.1         172.16.1.99
MAC: AAAA      R1M1  R1M2        R2M1  R2M2         MAC: DDDD

─── Hop 1 ──────────────────────────────────────────────────
 Frame: [Dst=R1M1 | Src=AAAA]           ← CHANGES each hop
 IP:    [Src=192.168.1.5 | Dst=172.16.1.99] ← STAYS SAME
 DATA:  ...

─── Hop 2 (after Router1) ──────────────────────────────────
 Frame: [Dst=R2M1 | Src=R1M2]           ← CHANGED
 IP:    [Src=192.168.1.5 | Dst=172.16.1.99] ← SAME
 DATA:  ...

─── Hop 3 (after Router2) ──────────────────────────────────
 Frame: [Dst=DDDD | Src=R2M2]           ← CHANGED
 IP:    [Src=192.168.1.5 | Dst=172.16.1.99] ← SAME
 DATA:  ...
──────────────────────────────────────────────────────────
RULE: L3 IP address stays the SAME end-to-end.
      L2 MAC address CHANGES at every hop.
```

**The Golden Rule:**
- **Same network** → Destination MAC = Target device's MAC address
- **Different network** → Destination MAC = **Default Gateway (Router)'s MAC address**

---

## Summary & Exam Notes

### Critical Facts to Memorize

```
OSI Layers Summary:
┌─────┬──────────────┬──────────────┬────────────────┐
│ No  │    Layer     │   PDU Name   │  Key Info      │
├─────┼──────────────┼──────────────┼────────────────┤
│  7  │ Application  │    Data      │ HTTP, HTTPS    │
│  6  │Presentation  │    Data      │ Encrypt/Format │
│  5  │   Session    │    Data      │ Session mgmt.  │
│  4  │  Transport   │  Segment     │ Port numbers   │
│  3  │   Network    │   Packet     │ IP address     │
│  2  │  Data Link   │   Frame      │ MAC address    │
│  1  │   Physical   │    Bit       │ Cable/Signal   │
└─────┴──────────────┴──────────────┴────────────────┘
```

### Quick-Fire Exam Q&A

**Q: How many bits is a MAC address?** → 48 bits (12 hex chars)
**Q: How many bits is an IPv4 address?** → 32 bits
**Q: Port numbers are added at which layer?** → Layer 4 (Transport)
**Q: IP addresses are added at which layer?** → Layer 3 (Network)
**Q: MAC addresses are added at which layer?** → Layer 2 (Data Link)
**Q: Which layer does a switch operate at?** → Layer 2
**Q: Which layer does a router operate at?** → Layer 3
**Q: How many layers does TCP/IP have?** → 4 layers
**Q: How many layers does OSI have?** → 7 layers
**Q: Which port does HTTPS use?** → 443
**Q: Which port does HTTP use?** → 80
**Q: When going to a different network, what is the destination MAC?** → The Default Gateway (Router)'s MAC
**Q: Does the IP address change as a packet travels?** → No, it remains constant
**Q: Does the MAC address change as a packet travels?** → Yes, it changes at every hop

### Full Packet Journey Recap

```
Complete Data Communication Flow:
──────────────────────────────────────────────────────────────
[Browser types www.google.com]
      │
      ▼
[Layer 7-6-5: HTTP GET request created → DATA]
      │
      ▼
[Layer 4: TCP header added → SEGMENT]
      Port src: 50001  Port dst: 443
      Seq No: 1        ACK: 0
      │
      ▼
[Layer 3: IP header added → PACKET]
      Src IP: 192.168.1.5   Dst IP: 172.217.x.x
      │
      ▼
[Layer 2: Ethernet frame added → FRAME]
      Src MAC: AA:AA...   Dst MAC: 11:11... (Router)
      + FCS error check
      │
      ▼
[Layer 1: Bits transmitted over medium]
      010110101010110001...
      │
      ▼ (travels through routers, MAC changes each hop, IP stays)
      │
      ▼
[Google Server receives bits]
      └→ Layer 1: receive bits
      └→ Layer 2: check FCS + check MAC → strip frame
      └→ Layer 3: check IP → strip IP header
      └→ Layer 4: check port 443 → strip TCP header
      └→ Layer 5-6-7: HTTP processed → response sent
──────────────────────────────────────────────────────────────
```

---

## Glossary / Terimler Sözlüğü

| English | Türkçe | Açıklama |
|---------|--------|---------|
| Protocol | Protokol | Kurallar dizisi |
| Encapsulation | Kapsülleme | Başlık ekleme |
| De-encapsulation | Kapsül açma | Başlık sökme |
| Segment | Segment | TCP PDU'su |
| Packet | Paket | IP PDU'su |
| Frame | Çerçeve | Ethernet PDU'su |
| Default Gateway | Varsayılan Ağ Geçidi | Router IP |
| Subnet Mask | Alt Ağ Maskesi | Ağ/host sınırı |
| Broadcast | Genel Yayın | Ağdaki herkese |
| Multicast | Çoklu Yayın | Gruba |
| Unicast | Tekil Yayın | Tek alıcıya |
| Flow Control | Akış Kontrolü | Hız yönetimi |
| ARP | Adres Çözümleme P. | IP'den MAC bulma |
| PDU | Protokol Veri Birimi | Katman bazlı veri adı |
| NIC | Ağ Arabirim Kartı | Ağ kartı |
| Routing Table | Yönlendirme Tablosu | Router'ın haritası |
| MAC Table | MAC Tablosu | Switch'in haritası |
| Acknowledgement | Onay | ACK, alındı bildirimi |

---

*© CCNA1 Introduction to Networks v7.0 | AYD Network Academy*
*Gökhan AKIN – CCIE | Ozan BÜK – CCIE*
