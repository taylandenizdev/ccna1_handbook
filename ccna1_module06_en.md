# 📘 Module 6: Data Link Layer (Layer 2)
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Source: Cisco Networking Academy

---

## 📋 Table of Contents
1. [Purpose of the Data Link Layer](#1-purpose-of-the-data-link-layer)
2. [LLC and MAC Sublayers](#2-llc-and-mac-sublayers)
3. [Topologies](#3-topologies)
4. [Half and Full Duplex Communication](#4-half-and-full-duplex-communication)
5. [Media Access Control Methods](#5-media-access-control-methods)
6. [Data Link Frame](#6-data-link-frame)
7. [Module Summary](#7-module-summary)

---

## 1. Purpose of the Data Link Layer

### 🎯 Core Responsibility
The Data Link Layer (Layer 2) is responsible for communications between **end device network interface cards (NICs) on the same network**.

### ⚙️ Two Core Functions

```
┌─────────────────────────────────────────────────────────────────┐
│               DATA LINK LAYER FUNCTIONS                         │
├──────────────────────────────┬──────────────────────────────────┤
│        1. FRAMING            │    2. MEDIA ACCESS CONTROL       │
│                              │           (MAC)                  │
│  • Adds Layer 2 header       │  • Controls access to the        │
│  • Adds Layer 2 trailer      │    physical medium               │
│  • Contains error-check      │  • Defines who sends and when    │
│    info (CRC/FCS)            │  • Ex: CSMA/CD, CSMA/CA          │
│                              │  • Token Passing algorithm       │
│  Protocol examples:          │                                  │
│  IEEE 802.3 (Ethernet)       │                                  │
│  IEEE 802.11 (Wi-Fi)         │                                  │
│  PPP, HDLC, Frame Relay      │                                  │
└──────────────────────────────┴──────────────────────────────────┘
```

### 🔄 Encapsulation Reminder (OSI Model)
```
┌───────────────────────────────────────────────────────────────┐
│   APPLICATION LAYER DATA                                      │
│   ┌──────┬──────────────────────────────────────────────────┐ │
│   │ UDP  │  UDP Data                               8 BYTES  │ │
│   ├──────┴──────────────────────────────────────────────────┤ │
│   │ TCP Header  │  TCP Data                        20 BYTES │ │
│   ├─────────────┴──────────────────────────────────────────┤ │
│   │ IP Header   │  IP Data                         20 BYTES │ │
│   ├─────────────┴──────────────────────────────────────────┤ │
│   │ Ethernet    │  46–1500 Bytes Ethernet Data  │   CRC  │  │ │
│   │ Header      │                               │        │  │ │
│   └─────────────┴───────────────────────────────┴────────┘  │ │
│                                                  18 BYTES     │
└───────────────────────────────────────────────────────────────┘
```

### 📦 Router Layer 2 Processing
At each hop, a router performs these 4 Layer 2 operations:

```
Incoming frame
     │
     ▼
[1] Accept frame from the medium
     │
     ▼
[2] De-encapsulate: remove Layer 2 header/trailer
     │
     ▼
[3] Layer 3 routing decision
     │
     ▼
[4] Re-encapsulate with new Layer 2 frame
     │
     ▼
Outgoing frame (may use different protocol: Ethernet → PPP → HDLC)
```

> **Key Point:** Layer 2 addresses (MAC) change at every hop.  
> Layer 3 addresses (IP) remain constant from source to destination.

---

## 2. LLC and MAC Sublayers

The Data Link Layer is divided into **two sublayers** per IEEE 802:

```
┌─────────────────────────────────────────────────────────────────┐
│                    NETWORK LAYER (Layer 3)                      │
│                   IPv4 / IPv6 Protocol                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │         LLC SUBLAYER  (IEEE 802.2)                      │  │
│   │  • Identifies upper-layer protocol (IPv4 or IPv6?)      │  │
│   │  • Bridge between network software & device hardware    │  │
│   └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│   ┌──────────────┬───────────────┬─────────────────────────┐   │
│   │   Ethernet   │  WLAN         │  WPAN                   │   │
│   │  IEEE 802.3  │  IEEE 802.11  │  IEEE 802.15            │   │
│   │  MAC SUBLAYER                                           │   │
│   │  • Data encapsulation (framing)                        │   │
│   │  • Media access control (CSMA/CD, CSMA/CA)             │   │
│   └──────────────┴───────────────┴─────────────────────────┘   │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                    PHYSICAL LAYER (Layer 1)                     │
└─────────────────────────────────────────────────────────────────┘
```

| Sublayer | Description |
|---|---|
| **LLC** (Logical Link Control) | Upper-layer handoff info; identifies which L3 protocol (IPv4/IPv6) |
| **MAC** (Media Access Control) | Framing + physical medium access algorithm |

---

## 3. Topologies

### 📐 Physical vs. Logical Topology

| Feature | Physical Topology | Logical Topology |
|---|---|---|
| **Shows** | Cables, device locations, physical connections | IP address plan, network segments |
| **Example** | Which switch is in which room | Servers on 192.168.3.x, Wi-Fi on 192.168.2.x |
| **Used For** | Cabling, maintenance, troubleshooting | Network design, IP planning |

---

### 🌐 WAN Topologies

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 1. POINT-TO-POINT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [Router Istanbul] ─────────── [Router Ankara]
        Node 1       ISP Link       Node 2

  ✔ Simplest and most common WAN topology
  ✔ Permanent link between two endpoints
  ✔ Uses PPP, HDLC protocols
  ✔ No MAC address needed (just 8-bit all-ones)
  Examples: MPLS, Site-to-Site VPN, Leased Line

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 2. HUB AND SPOKE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

          [Ankara - Hub]
          ┌───────┴───────┐
    [Istanbul]  [Izmir]  [Bursa]
      (Spoke)   (Spoke)  (Spoke)

  ✔ Star-like: branches connect to center
  ✔ If hub fails, all branches lose connectivity
  Examples: Frame Relay, legacy WAN

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 3. MESH
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Full Mesh (5 devices = 5×4/2 = 10 links):

      [A]───[B]
      │╲   ╱│╲
      │ ╲ ╱ │ ╲
      │  ╳  │  [E]
      │ ╱ ╲ │ ╱
      [C]───[D]

  ✔ High availability
  ✔ Expensive (many connections required)
  ✔ Partial Mesh: some links omitted
  Formula: N × (N-1) / 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### 🏢 LAN Topologies

```
┌──────────────────┐    ┌──────────────────────────────┐
│  STAR            │    │  EXTENDED STAR               │
│                  │    │  (Modern Ethernet)            │
│    PC1  PC2      │    │                              │
│     \   /        │    │   SW──────SW──────SW         │
│      [SW]        │    │   |       |       |          │
│     /   \        │    │  PCs     PCs     PCs         │
│   PC3   PC4      │    │                              │
└──────────────────┘    └──────────────────────────────┘

┌──────────────────┐    ┌──────────────────────────────┐
│  BUS             │    │  RING                        │
│  (Early Ethernet)│    │  (Token Ring technology)      │
│                  │    │                              │
│ ●──[PC]──[PC]──● │    │    PC1──PC2                  │
│  (Coaxial cable) │    │    |        |                │
│  Uses CSMA/CD    │    │   PC4──PC3                  │
│  10 Mbps         │    │  Token Passing algorithm     │
└──────────────────┘    └──────────────────────────────┘
```

> **Note:** Today's Ethernet uses **Extended Star** topology with switches. Bus and Ring are historical.

---

## 4. Half and Full Duplex Communication

```
╔══════════════════════════════════════════════════════════════════╗
║           HALF DUPLEX                                           ║
╠══════════════════════════════════════════════════════════════════╣
║  • Only ONE device can send at a time                           ║
║  • Can't send and receive simultaneously                        ║
║  • Single shared transmission medium                           ║
║  • Collisions can occur                                         ║
║                                                                  ║
║  Media: Hub, Access Point (Wi-Fi), Legacy Ethernet              ║
║  Algorithm: CSMA/CD (wired), CSMA/CA (wireless)                 ║
║                                                                  ║
║  PC_A → → → → [HUB] → → → → PC_B                               ║
║         (PC_B cannot send during this time)                     ║
╠══════════════════════════════════════════════════════════════════╣
║           FULL DUPLEX                                           ║
╠══════════════════════════════════════════════════════════════════╣
║  • Can send AND receive at the same time                        ║
║  • Each port has its own independent channel                    ║
║  • No collisions                                                ║
║  • No waiting — very efficient                                  ║
║                                                                  ║
║  Media: Switch                                                   ║
║  CSMA/CD algorithm NOT needed                                   ║
║                                                                  ║
║  PC_A ──→ [SWITCH] ──→ PC_B   (simultaneously)                 ║
║  PC_C ──→ [SWITCH] ──→ PC_D   (simultaneously)                 ║
╚══════════════════════════════════════════════════════════════════╝
```

### 📊 Hub vs. Switch Comparison

| Feature | Hub | Switch |
|---|---|---|
| Mode | Half Duplex | Full Duplex |
| Speed | 10 Mbps (shared) | 1 Gbps (per port, dedicated) |
| Collisions | Possible | Never |
| Algorithm | CSMA/CD required | CSMA/CD not needed |
| Channel structure | Single shared medium | Independent channel per port |
| 24-port example | ~0.4 Mbps/port | 24×1 Gbps TX + 24×1 Gbps RX = **48 Gbps** switching |

---

## 5. Media Access Control Methods

### 🔀 Access Method Overview

```
┌─────────────────────────────────────────────────────────────────┐
│               MEDIA ACCESS CONTROL METHODS                      │
├──────────────────────────────┬──────────────────────────────────┤
│   CONTENTION-BASED           │   CONTROLLED ACCESS              │
├──────────────────────────────┼──────────────────────────────────┤
│  • No device has priority    │  • Ordered access                │
│  • Channel free → send       │  • Token-based                   │
│  • Channel busy → wait       │  • Used in Token Ring networks   │
│                              │                                  │
│  CSMA/CD → Ethernet          │  TOKEN PASSING algorithm         │
│  CSMA/CA → Wi-Fi (802.11)    │  (no longer in use)              │
└──────────────────────────────┴──────────────────────────────────┘
```

---

### ⚡ CSMA/CD Algorithm (Carrier Sense Multiple Access / Collision Detection)

**Used in:** Legacy Ethernet / Hub environments (IEEE 802.3)

```
┌─────────────────────────────────────────────────────────────────┐
│                   CSMA/CD FLOW DIAGRAM                          │
└─────────────────────────────────────────────────────────────────┘

  Frame ready to send
           │
           ▼
    ┌─────────────┐
    │ Listen to   │
    │ the channel │
    └──────┬──────┘
           │
    ┌──────▼──────┐      BUSY
    │ Channel     ├─────────────→ Wait → Listen again
    │ free?       │
    └──────┬──────┘
           │ FREE
           ▼
    ┌──────────────────┐
    │ Transmit frame   │
    │ (listen while    │
    │  transmitting)   │
    └──────┬───────────┘
           │
    ┌──────▼───────────┐   NO
    │ Collision        ├────────→ Transmission complete ✓
    │ detected?        │
    └──────┬────────────┘
           │ YES
           ▼
    ┌──────────────────────────────┐
    │ 1. STOP transmission         │
    │ 2. Send JAM signal (96 bits  │
    │    of distortion)            │
    │ 3. Wait random time          │
    │    (Backoff Algorithm)       │
    └──────┬───────────────────────┘
           │
           ▼
    Retry transmission
    (Another collision → increase wait time)
```

**Summary:**
- Channel free → send
- Collision while transmitting → stop, send jam signal, wait randomly
- Retransmit

---

### 📡 CSMA/CA Algorithm (Carrier Sense Multiple Access / Collision Avoidance)

**Used in:** Wi-Fi / WLAN (IEEE 802.11)

```
┌─────────────────────────────────────────────────────────────────┐
│                   CSMA/CA FLOW DIAGRAM                          │
└─────────────────────────────────────────────────────────────────┘

  Frame ready to send
           │
           ▼
    ┌──────────────────────┐
    │ Listen to channel    │
    └──────┬───────────────┘
           │
    ┌──────▼──────┐     BUSY
    │ Channel     ├──────────→ Wait
    │ free?       │
    └──────┬──────┘
           │ FREE
           ▼
    ┌──────────────────────────────────┐
    │ Start random backoff timer       │
    │ (A: 3ms, B: 4ms, etc.)           │
    │ Device with timer = 0 sends first│
    └──────┬───────────────────────────┘
           │ Timer expired
           ▼
    ┌──────────────────────────────────┐
    │ Transmit frame                   │
    └──────┬───────────────────────────┘
           │
    ┌──────▼──────────────────────────┐
    │ ACK received from Access Point? │
    └──────┬──────────────┬────────────┘
       YES │              │ NO
           ▼              ▼
     Success ✓       Assume collision
                     Restart timer
                     (longer duration)
```

**CSMA/CD vs. CSMA/CA Comparison:**

| Feature | CSMA/CD | CSMA/CA |
|---|---|---|
| Full name | Collision **Detection** | Collision **Avoidance** |
| Collision handling | Detects after it happens | Tries to prevent before it happens |
| Used in | Ethernet / Hub | Wi-Fi (802.11) |
| Acknowledgment (ACK) | No | Yes (Access Point confirms) |

---

### 🎫 Token Passing (Historical)

Used in Token Ring networks. A single token circulates around all devices:

```
  [A] → empty token → [B] → empty token → [C]
   ↑                                         │
   └──────── [D] ← empty token ──────────────┘

  • Token arrives + data to send → send data, token circulates one round
  • Token arrives + no data → pass token to next device
  • No longer in use today
```

---

## 6. Data Link Frame

### 🗂️ Frame Structure

```
┌────────────────────────────────────────────────────────────────────────┐
│                     GENERIC FRAME STRUCTURE                            │
├──────────────┬───────────────┬─────────┬──────────────────┬───────────┤
│  HEADER                      │  DATA   │   TRAILER                    │
├────────┬─────┴──┬─────┬──────┴─────────┴───────────┬──────────────────┤
│ Frame  │Address-│Type │Control│    DATA (Packet)   │Error   │Frame    │
│ Start  │  ing   │     │       │  (Layer 3 packet)  │Detect. │ Stop    │
└────────┴────────┴─────┴───────┴────────────────────┴────────┴─────────┘
```

### 📋 Frame Fields

| Field | Description |
|---|---|
| **Frame Start** | Special bit pattern indicating the beginning of a frame |
| **Addressing** | Source and destination node addresses (MAC addresses) |
| **Type** | Identifies the encapsulated Layer 3 protocol (IPv4/IPv6) |
| **Control** | Flow control, prioritization (rarely used today) |
| **Data** | Layer 3 packet (frame payload) |
| **Error Detection** | CRC/FCS — used to detect transmission errors |
| **Frame Stop** | Special bit pattern indicating the end of a frame |

---

### 🔢 Layer 2 Addresses (MAC Addresses)

```
┌─────────────────────────────────────────────────────────────────┐
│              COMMUNICATION ON THE SAME NETWORK                  │
│                                                                 │
│  PC1 (192.168.1.110)                  Web Server (172.16.1.99)  │
│  MAC: AA-AA-AA-AA-AA-AA               MAC: AB-CD-EF-12-34-56   │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ L2 Header         │          L3 IP Packet                │   │
│  ├────────────┬───────┤  ┌──────────────┬──────────────┐    │   │
│  │ Dest. NIC  │  Src  │  │  Source IP   │  Dest. IP    │    │   │
│  │11-11-11-11 │AA-AA- │  │192.168.1.110 │172.16.1.99   │    │   │
│  │    (R1)    │AA-AA  │  └──────────────┴──────────────┘    │   │
│  └────────────┴───────┴──────────────────────────────────────┘  │
│                                                                 │
│  ⚠ MAC address is only valid on the same network segment!     │
│  ⚠ MAC address changes at every router hop                    │
│  ✓ IP address stays constant from source to destination       │
└─────────────────────────────────────────────────────────────────┘
```

### 🌐 Frame Structure by Protocol

```
Ethernet (802.3) LAN:
┌──────────────┬──────────────────────────┬─────────┐
│Ethernet Hdr  │       DATA               │  CRC    │
│(Src+Dst MAC) │   (Layer 3 Packet)       │(Trailer)│
└──────────────┴──────────────────────────┴─────────┘

Wi-Fi (802.11) — Longest header:
┌───────────────────────┬──────────────────────────┬─────────┐
│802.11 Header          │       DATA               │Trailer  │
│(Src MAC + Dst MAC +   │   (Layer 3 Packet)       │         │
│ AP MAC + control info)│                          │         │
└───────────────────────┴──────────────────────────┴─────────┘
⚠ Contains 3 MAC addresses: Source, Destination, Access Point

PPP — Shortest header (point-to-point):
┌──────────────┬──────────────────────────┬─────────┐
│PPP Header    │       DATA               │PPP Trail│
│(8-bit: all   │   (Layer 3 Packet)       │         │
│  ones = FF)  │                          │         │
└──────────────┴──────────────────────────┴─────────┘
⚠ No MAC address needed — only two endpoints exist
```

---

### 📊 Common Layer 2 Protocols Today

| Protocol | Medium | Addressing | Topology |
|---|---|---|---|
| **Ethernet (802.3)** | Wired LAN | MAC address | Star / Extended Star |
| **Wi-Fi (802.11)** | Wireless LAN | MAC address | Star (AP-centered) |
| **PPP** | WAN (ISP connection) | 8-bit (FF) | Point-to-Point |
| **HDLC** | WAN (Router-to-router) | Cisco proprietary | Point-to-Point |
| **Frame Relay** | WAN (legacy) | DLCI | Hub and Spoke |

---

## 7. Module Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODULE 6 SUMMARY                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  WHAT DOES LAYER 2 (Data Link Layer) DO?                        │
│                                                                 │
│  ➊ FRAMING                                                      │
│     • Adds Layer 2 header + trailer                             │
│     • Source and destination addresses (usually MAC)            │
│     • Error detection info (CRC/FCS)                            │
│                                                                 │
│  ➋ MEDIA ACCESS CONTROL (MAC)                                   │
│     • Controls access to the physical medium                    │
│     • Wired (hub): CSMA/CD                                      │
│     • Wireless: CSMA/CA                                         │
│     • Switch: No algorithm needed (full duplex)                 │
│                                                                 │
│  ➌ LAYER 2 ADDRESSES                                            │
│     • MAC addresses (physical addresses)                        │
│     • Valid only within the same network                        │
│     • Renewed at each router hop                                │
│                                                                 │
│  ➍ TOPOLOGIES                                                   │
│     WAN: Point-to-Point, Hub & Spoke, Mesh                      │
│     LAN: Star, Extended Star (current), Bus, Ring (legacy)      │
│                                                                 │
│  ➎ STANDARDS ORGANIZATIONS                                      │
│     • IEEE (802.3 Ethernet, 802.11 Wi-Fi)                       │
│     • ITU, ISO, ANSI                                            │
│     • IETF (Layer 3 and above)                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

> **📌 Next Module:** Detailed look at Ethernet technology and MAC addresses.

---
*CCNA1 – Introduction to Networks v7.0 | Module 6*
