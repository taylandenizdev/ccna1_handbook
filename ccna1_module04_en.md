# 📡 Module 4: Physical Layer (Layer 1)
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  


---

## 📋 Table of Contents

- [OSI Reference Model Review](#-osi-reference-model-review)
- [Purpose of the Physical Layer](#-purpose-of-the-physical-layer)
- [Physical Layer Characteristics](#-physical-layer-characteristics)
- [Bandwidth Terminology](#-bandwidth-terminology)
- [Copper Cabling](#-copper-cabling)
- [UTP Cabling](#-utp-cabling)
- [Fiber Optic Cabling](#-fiber-optic-cabling)
- [Wireless Media](#-wireless-media)
- [Summary Comparison Table](#-summary-comparison-table)

---

## 🔁 OSI Reference Model Review

### Layered Architecture and PDU Names

```
┌─────────────────────────────────────────────────────┐
│  7 - Application                ┐                   │
│  6 - Presentation               ├─ DATA             │
│  5 - Session                    ┘                   │
│  4 - Transport                  → SEGMENT           │
│  3 - Network                    → PACKET            │
│  2 - Data Link                  → FRAME             │
│  1 - Physical                   → BIT               │
└─────────────────────────────────────────────────────┘
```

### Packet Encapsulation

```
APPLICATION LAYER DATA
         │
         ▼
┌────────┬──────────────────────────────────┐  ← SEGMENT
│  TCP   │         TCP Data                 │
│ Header │                                  │
└────────┴──────────────────────────────────┘
         │
         ▼
┌────────┬──────────────────────────────────┐  ← PACKET
│   IP   │           IP Data                │
│ Header │                                  │
└────────┴──────────────────────────────────┘
         │
         ▼
┌────────┬──────────────────────────────────┬─────┐  ← FRAME
│  ETH   │       46…1500 Bytes Eth Data     │ CRC │
│ Header │                                  │     │
└────────┴──────────────────────────────────┴─────┘
```

### Device Layer Awareness

| Device | Works Up To | Looks At |
|--------|-------------|----------|
| **Hub** | Layer 1 | — |
| **Switch** | Layer 2 | MAC Address |
| **Router** | Layer 3 | IP Address |

---

## 🎯 Purpose of the Physical Layer

### Physical Connection

Before any network communication can occur, a **physical connection** to a local network must be established.

- Connection can be **wired** or **wireless**
- **NIC (Network Interface Card)** connects a device to the network
- Some devices may have multiple NICs (e.g., laptop → Ethernet NIC + Wireless NIC)
- Each NIC has its own unique **MAC address**

```
Laptop
 ├── Ethernet NIC  → MAC: AA:BB:CC:11:22:33
 ├── Wireless NIC  → MAC: AA:BB:CC:44:55:66
 └── Bluetooth     → MAC: AA:BB:CC:77:88:99
```

### What the Physical Layer Does

```
Accepts a complete FRAME
from the Data Link Layer
        │
        ▼
Converts to BIT stream (0s and 1s)
        │
        ▼
Encodes into signals
        │
        ▼
Transmits over the medium
(cable / wireless)
```

---

## ⚙️ Physical Layer Characteristics

### Standards Organizations

| Layer | Organization | Develops |
|-------|-------------|----------|
| Software (L3-L7) | **IETF** | TCP/IP protocols, RFC documents |
| Hardware (L1-L2) | **IEEE** | Ethernet, Wi-Fi standards |
| Hardware (L1-L2) | **EIA/TIA** | Cabling standards |
| Hardware (L1-L2) | **ANSI** | Various physical standards |
| Hardware (L1-L2) | **ITU-T** | Telecom standards |

### 3 Functional Areas of Physical Layer Standards

```
┌─────────────────────────────────────────────────┐
│        PHYSICAL LAYER STANDARDS                 │
├─────────────────┬───────────────┬───────────────┤
│  PHYSICAL       │   ENCODING    │  SIGNALING    │
│  COMPONENTS     │               │               │
├─────────────────┼───────────────┼───────────────┤
│ • NIC           │ • Manchester  │ • Voltage     │
│ • Cable         │ • 4B/5B       │   levels      │
│ • Connector     │ • 8B/10B      │ • Light pulses│
│ • Switch port   │               │ • Radio waves │
└─────────────────┴───────────────┴───────────────┘
```

### Encoding

Converts the bit stream into a format recognizable by the next device in the network path.

**Manchester Encoding example:**
```
Bit:    0    1    0    0    1    1    0
       ___       ___  ___       
Volt: _│  │___│  │  │___│___│  │___
       ↑       ↑
    Falling=0  Rising=1
```

### Signaling

| Medium | Signal Type |
|--------|------------|
| **Copper cable** | Electrical signals (voltage levels) |
| **Fiber optic** | Light pulses (laser / LED) |
| **Wireless** | Electromagnetic waves (AM / FM / PM) |

---

## 📊 Bandwidth Terminology

### Units

| Unit | Abbreviation | Equivalence |
|------|-------------|-------------|
| Bits per second | bps | 1 bps |
| Kilobits per second | Kbps | 1,000 bps |
| Megabits per second | Mbps | 1,000,000 bps |
| Gigabits per second | Gbps | 1,000,000,000 bps |
| Terabits per second | Tbps | 1,000,000,000,000 bps |

### Key Concepts

```
┌────────────────────────────────────────────────────┐
│  BANDWIDTH         → Theoretical maximum capacity  │
│                      e.g., 100 Mbps link           │
├────────────────────────────────────────────────────┤
│  THROUGHPUT        → Actual data transfer rate     │
│                      e.g., speed test shows 80 Mbps│
├────────────────────────────────────────────────────┤
│  GOODPUT           → Usable data rate              │
│                      Throughput - overhead         │
│                      e.g., 77 Mbps                 │
├────────────────────────────────────────────────────┤
│  LATENCY           → Delay time                    │
│                      Voice requires < 150ms        │
└────────────────────────────────────────────────────┘
```

### 💡 Practical Calculation: Bytes vs Bits

> ⚠️ **Important:** 1 Byte = 8 bits

**Example:**  
How long to transfer a 100 MB file over a 100 Mbps connection?

```
100 MB  →  100 × 8  =  800 Mbit
800 Mbit ÷ 100 Mbps  =  8 seconds (ideal)
```

---

## 🔌 Copper Cabling

### General Characteristics

- **Most common** cabling type in networks
- **Inexpensive** and **easy to install**
- Low electrical resistance

### 3 Core Problems and Solutions

```
┌─────────────────────────────────────────────────────────┐
│  PROBLEM 1: ATTENUATION (Signal Loss)                  │
│  Signal weakens as it travels along the cable          │
│  ✅ Solution: Keep cable length under 100 m            │
├─────────────────────────────────────────────────────────┤
│  PROBLEM 2: EMI / RFI (Electromagnetic Interference)   │
│  Fluorescent lights, power cables, UPS, AC units       │
│  ✅ Solution: Use shielded (STP) cable, ground properly │
├─────────────────────────────────────────────────────────┤
│  PROBLEM 3: CROSSTALK                                  │
│  Signal from one wire disturbs adjacent wires          │
│  ✅ Solution: Use twisted pair cable (Cancellation)    │
└─────────────────────────────────────────────────────────┘
```

### Cancellation Effect

```
Wire A (TX+)  →→→→→→→→→→→→
                              ↗ Magnetic fields
Wire B (TX-)  ←←←←←←←←←←←←   CANCEL each other
```

Opposite currents in twisted pairs create opposing magnetic fields that cancel out → Reduces crosstalk!

### Types of Copper Cable

```
┌──────────────────────────────────────────────────┐
│  1. UTP  (Unshielded Twisted Pair)               │
│     ✓ Most common  ✓ Cheap  ✗ Less protection    │
├──────────────────────────────────────────────────┤
│  2. STP  (Shielded Twisted Pair)                 │
│     ✓ Better protection  ✗ Expensive             │
│     ✗ Requires proper grounding                  │
├──────────────────────────────────────────────────┤
│  3. Coaxial Cable                                │
│     ✓ Antenna connections  ✓ Cable TV/internet  │
│     ✗ Rarely used for data networks today        │
└──────────────────────────────────────────────────┘
```

---

## 🔧 UTP Cabling

### Cable Categories

| Category | Speed | Year | Status |
|----------|-------|------|--------|
| Cat 3 | 10 Mbps | 1991 | Legacy |
| Cat 5 | 100 Mbps | 1995 | Legacy |
| **Cat 5e** | **1 Gbps** | **2001** | **Common** |
| **Cat 6** | **1 Gbps** | **2002** | **Common** |
| **Cat 6A** | **10 Gbps** | **2008** | **Common** |
| Cat 7A | 10 Gbps | 2013 | Available |
| Cat 8.1/8.2 | 25-40 Gbps | 2016 | Short range (30m) |

> 📌 All categories support maximum **100 meters** (except Cat 8: 30m)

### IEEE 802.3 Physical Layer Standards

| IEEE Standard | Name | Speed | Distance | Cable |
|---------------|------|-------|----------|-------|
| 802.3i | 10BASE-T | 10 Mbps | 100 m | Cat-3 |
| 802.3u | 100BASE-T | 100 Mbps | 100 m | Cat-5 |
| 802.3ab | 1000BASE-T | 1 Gbps | 100 m | Cat-5e |
| 802.3an | 10GBASE-T | 10 Gbps | 100 m | Cat-6A |
| 802.3bq | 25GBASE-T | 25 Gbps | 30 m | Cat-8 |
| 802.3bq | 40GBASE-T | 40 Gbps | 30 m | Cat-8 |

**Name Structure:** `10BASE-T`
```
 10    BASE    T
 │      │      │
 │      │      └── T = Twisted Pair
 │      └───────── BASE = Baseband (single channel)
 └──────────────── Speed (Mbps)
```

### T568A and T568B Color Codes

```
         T568B (Most Common)       T568A
Pin 1:   Orange/White      │    Green/White
Pin 2:   Orange            │    Green
Pin 3:   Green/White       │    Orange/White
Pin 4:   Blue              │    Blue
Pin 5:   Blue/White        │    Blue/White
Pin 6:   Green             │    Orange
Pin 7:   Brown/White       │    Brown/White
Pin 8:   Brown             │    Brown
```

**Pins used for 10/100 Mbps:** 1, 2 (TX) and 3, 6 (RX)  
**Pins used for 1 Gbps:** 1, 2, 3, 4, 5, 6, 7, 8 (all 8 wires)

### Cable Types

```
┌────────────────────────────────────────────────────────────┐
│  STRAIGHT-THROUGH CABLE                                   │
│  Both ends T568B or both ends T568A                       │
│  Use: PC → Switch, Router → Switch                        │
├────────────────────────────────────────────────────────────┤
│  CROSSOVER CABLE                                          │
│  One end T568A, other end T568B                           │
│  Use: PC → PC, Switch → Switch, Router → Router           │
├────────────────────────────────────────────────────────────┤
│  ROLLOVER CABLE (Console Cable)                           │
│  Cisco proprietary, for managing network devices          │
│  Use: Laptop → Console Port (9600 bps)                    │
└────────────────────────────────────────────────────────────┘
```

> 💡 **Modern note:** AutoMDIX feature allows devices to automatically detect and configure crossover. Straight-through cables work everywhere today!

### Cable Selection Guide

| Connection | Cable Type |
|------------|-----------|
| PC → Switch | Straight-through |
| Router → Switch | Straight-through |
| PC → PC | Crossover |
| Switch → Switch | Crossover |
| Router → Router | Crossover |
| Laptop → Console Port | Rollover |

### How a Switch Handles Signals

```
PC A ──(straight)──[Switch Port 1]
                         │
                    Switch performs
                    internal crossover
                         │
                   [Switch Port 2]──(straight)──PC B

TX from A becomes RX at B ✓
```

### Enterprise Cabling Infrastructure

```
[PC]──(5m patch)──[Wall Outlet]──(max 90m)──[Patch Panel]──(5m)──[Switch]
                                  HORIZONTAL CABLING
                                  Max total: 100 meters
```

**Vertical / Backbone Cabling:**
```
[Floor 1 Switch]──fiber──[Floor 2 Switch]──fiber──[Core Switch]──[Router]──[Internet]
```

---

## 💡 Fiber Optic Cabling

### General Characteristics

| Feature | Value |
|---------|-------|
| Speed | 10 Mbps – 800 Gbps+ |
| Distance | 550 m – 100+ km |
| EMI/RFI | **Completely immune** |
| Fragility | Care required (breaks at 90° bend) |
| Cost | More expensive than UTP |
| Signal | Light pulses (Laser / LED) |

### Types of Fiber Cable

```
┌──────────────────────────────────┬──────────────────────────────┐
│  MULTIMODE FIBER (MMF)           │  SINGLE-MODE FIBER (SMF)     │
├──────────────────────────────────┼──────────────────────────────┤
│ Core: 50/62.5 microns            │ Core: 9 microns              │
│ Cladding: 125 microns            │ Cladding: 125 microns        │
│ Color: ORANGE 🟠                  │ Color: YELLOW 🟡              │
│ Light source: LED                │ Light source: LASER          │
│ Distance: ~550 meters            │ Distance: 10 km – 70+ km     │
│ Cost: Less expensive             │ Cost: More expensive         │
└──────────────────────────────────┴──────────────────────────────┘
```

**Light Path Difference:**

```
MMF (Multimode):                    SMF (Single-mode):
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓    ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
░ ~~~~~~~~~~~~~~~~~~~~~ ░           ░ ─────────────────────── ░
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓    ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
Light bounces → Dispersion occurs   Light travels straight → Less loss
```

### SFP Modules

```
┌─────────────────────────────────────────────────────────────────┐
│  SFP = Small Form-factor Pluggable                             │
│  Hot-swappable modular transceiver for switch slots            │
├──────────────────┬────────────┬──────────────┬─────────────────┤
│  SFP Version     │  Medium    │  Wavelength  │  Max. Distance  │
├──────────────────┼────────────┼──────────────┼─────────────────┤
│  1000BASE-SX     │  MMF       │  850 nm      │  ~550 m         │
│  1000BASE-LX/LH  │  MMF/SMF  │  1300 nm     │  SMF ~10 km     │
│  1000BASE-EX     │  SMF       │  1310 nm     │  ~40 km         │
│  1000BASE-ZX     │  SMF       │  1550 nm     │  ~70 km         │
└──────────────────┴────────────┴──────────────┴─────────────────┘
```

> ⚠️ **Important:** SFP modules on **both ends** must be the **same type**! Different wavelengths won't communicate.

### Fiber Optic Connectors

| Connector | Shape | Usage |
|-----------|-------|-------|
| **ST** | Round, bayonet-lock | Older systems |
| **SC** | Square, push-pull | Enterprise networks |
| **LC** | Small square | Modern switches (most common) |

### Fiber Cable Testing

**OTDR (Optical Time Domain Reflectometer):**
```
Send signal ──────────────▶ BREAK POINT ◀── Reflection returns
                                 │
           ◀── Reflection received at 3 km!
           
→ Cable broke at 3 km, go there and repair!
```

### Fiber Installation Flow

```
[Fiber Cable]
      │
      ├── Fusion splice with pigtails
      │         │
      │    [Fiber Patch Panel]
      │         │
      │    (LC-LC fiber patch cable)
      │         │
      └────[SFP Module] ──── [Switch A]
      
[Switch B] ──── [SFP Module] ──── [Fiber Patch Panel] ──── fusion ──── [Fiber Cable]
```

### Where is Fiber Used?

| Application | Fiber Type | Reason |
|------------|-----------|--------|
| Floor-to-floor in building | MMF | ≤ 550m |
| Campus building-to-building | SMF | > 550m |
| City-to-city long haul | SMF | km-scale |
| Transatlantic submarine | SMF | 1000s of km |
| FTTH (Fiber to the Home) | SMF | GPON/EPON |

---

## 📶 Wireless Media

### Characteristics and Limitations

```
ADVANTAGES:                          LIMITATIONS:
✓ Enables mobility                   ✗ Limited coverage area
✓ No need to pull cables             ✗ Interference problems
✓ Easy installation                  ✗ Security risks
                                     ✗ Shared medium (Half-duplex)
```

**Half-Duplex Problem:**
```
Only 1 device can transmit at a time on the Access Point!

[AP] ─ A transmitting → B, C, D must wait
[AP] ─ B transmitting → A, C, D must wait

200 Mbps link, 10 users → ~20 Mbps per user
```

### Wireless Standards

| Standard | Technology | Description |
|----------|-----------|-------------|
| **IEEE 802.11** | Wi-Fi | Wireless LAN |
| **IEEE 802.15** | Bluetooth | Personal area network (PAN) |
| **IEEE 802.16** | WiMAX | Broadband wireless access |
| **IEEE 802.15.4** | Zigbee | IoT, low power |

### Wi-Fi Generations

| Wi-Fi Gen | IEEE Standard | Speed | Frequency |
|-----------|--------------|-------|-----------|
| Wi-Fi 4 | 802.11n | Up to 150 Mbps | 2.4 & 5 GHz |
| Wi-Fi 5 | 802.11ac | 433 Mbps – 6.9 Gbps | 5 GHz |
| **Wi-Fi 6** | **802.11ax** | **600 Mbps – 9.6 Gbps** | **2.4 & 5 GHz** |

### Bluetooth

| Feature | Value |
|---------|-------|
| Standard | IEEE 802.15, v5.0 |
| Range | ~240 meters |
| Speed | ~2 Mbps |
| Use | Headsets, CarPlay, mouse, keyboard |

> 📌 **Note:** CarPlay navigation data can exceed 2 Mbps → Bluetooth alone isn't enough, Wi-Fi connection also required!

### Zigbee

| Feature | Value |
|---------|-------|
| Standard | IEEE 802.15.4 |
| Range | 10 – 30 meters |
| Speed | 250 Kbps |
| Use | IoT sensors, medical devices, long battery life |

### WLAN Components

```
[Device]──(wireless)──[Access Point (AP)]──(UTP cable)──[Switch]──[Router]──[Internet]
           802.11ax                                        
           Wi-Fi 6
```

---

## 📊 Summary Comparison Table

### Wired vs Wireless vs Fiber

| Feature | UTP Cable | Fiber Optic | Wireless |
|---------|-----------|-------------|---------|
| **Bandwidth** | 10 Mb – 10 Gb | 10 Mb – 100 Gb | Low – Medium |
| **Distance** | 1 – 100 m | 1 – 100,000 m | 70 – 80 m (indoor) |
| **EMI/RFI Immunity** | Low | **Completely immune** | Medium |
| **Cost** | **Lowest** | High | Medium |
| **Installation** | **Easy** | Difficult (fusion) | Easy |
| **Security** | Medium | High | Low |
| **Mobility** | None | None | **Yes** |

### Which Medium for Which Scenario?

```
┌─────────────────────────────────────────────────────────┐
│  Desk → Switch connection       → UTP (Cat5e / Cat6A)  │
│  Switch → Switch (distant floor)→ Fiber (MMF/SMF)      │
│  Building → Building            → Fiber (SMF)          │
│  Laptop / Mobile device         → Wi-Fi                │
│  Headset / Accessory            → Bluetooth            │
│  IoT sensor                     → Zigbee               │
└─────────────────────────────────────────────────────────┘
```

---

## 🏠 What's Inside a Home Router/Modem?

```
┌─────────────────────────────────────────────────────────┐
│           "MODEM" (Actually Multi-Function)             │
│                                                         │
│  ┌──────┐  ┌──────────┐  ┌────────────┐  ┌─────────┐  │
│  │MODEM │  │  ROUTER  │  │   SWITCH   │  │ ACCESS  │  │
│  │      │  │          │  │ (4 ports)  │  │  POINT  │  │
│  │Signal│  │IP routing│  │LAN connect.│  │  Wi-Fi  │  │
│  │conv. │  │          │  │            │  │         │  │
│  └──────┘  └──────────┘  └────────────┘  └─────────┘  │
│                                                         │
│  + DHCP Server (Automatic IP assignment)               │
│  + Firewall (MAC filtering, security)                  │
└─────────────────────────────────────────────────────────┘
```

---

## 📝 Key Terms Glossary

| Term | Definition |
|------|-----------|
| **NIC** | Network Interface Card – hardware that connects to a network |
| **UTP** | Unshielded Twisted Pair – most common copper cabling |
| **STP** | Shielded Twisted Pair – copper cabling with EMI shielding |
| **Attenuation** | Signal loss over distance |
| **Crosstalk** | Interference between wires within a cable |
| **Cancellation** | Magnetic field cancellation effect in twisted pairs |
| **MMF** | Multimode Fiber – orange jacket, core 50/62.5µm, ≤550m |
| **SMF** | Single-Mode Fiber – yellow jacket, core 9µm, ≤70+ km |
| **SFP** | Small Form-factor Pluggable – modular transceiver |
| **OTDR** | Optical Time Domain Reflectometer – fiber test tool |
| **AutoMDIX** | Automatic crossover detection feature |
| **T568A/B** | UTP cable termination standards |
| **Patch Panel** | Panel where cables are terminated and organized |
| **Horizontal Cabling** | Cabling from workstations to patch panels |
| **Backbone/Vertical** | Inter-floor, inter-switch cabling |
| **Throughput** | Actual measured data transfer rate |
| **Goodput** | Net usable data rate (throughput minus overhead) |
| **Latency** | Delay time for data to travel from source to destination |
| **Dispersion** | Spreading of a light pulse over time in MMF |
| **Pigtail** | Pre-terminated short fiber stub for fusion splicing |
| **Fusion Splice** | Joining two fiber strands with heat |

---

*📚 CCNA1 – Introduction to Networks v7.0 | Module 4: Physical Layer*
