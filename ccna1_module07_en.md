# 📘 Module 7: Ethernet Switching
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Source: Cisco Networking Academy

---

## 📋 Table of Contents
1. [Ethernet Frame Structure](#1-ethernet-frame-structure)
2. [MAC Addresses](#2-mac-addresses)
3. [MAC Address Types](#3-mac-address-types)
4. [MAC Address Table and Switch Operation](#4-mac-address-table-and-switch-operation)
5. [Switch Forwarding Methods](#5-switch-forwarding-methods)
6. [Memory Buffering](#6-memory-buffering)
7. [Port Settings: Duplex, Speed, and Auto-MDIX](#7-port-settings-duplex-speed-and-auto-mdix)
8. [Module Summary](#8-module-summary)

---

## 1. Ethernet Frame Structure

### 🏛️ Brief History of Ethernet
Ethernet was developed by Xerox, then refined jointly by Digital, Intel, and Xerox into **Ethernet II**. IEEE standardized it as **802.3**. Today "Ethernet" and "IEEE 802.3" are essentially synonymous.

### 📐 Relationship to OSI Layers
Ethernet operates at both **Layer 2 (Data Link)** and **Layer 1 (Physical)**.

```
┌─────────────────────────────────────────────────────────────────┐
│                  ETHERNET LAYER STRUCTURE                       │
├────────────────────────────────┬────────────────────────────────┤
│    LAYER 2 (Data Link)         │    LAYER 1 (Physical)          │
│                                │                                 │
│  LLC Sublayer (802.2):         │  • Cable types (UTP / Fiber)   │
│  • Identifies upper-layer      │  • Connectors                  │
│    protocol (IPv4/IPv6)        │  • Data rates                  │
│                                │  • Signal encoding             │
│  MAC Sublayer (802.3):         │                                 │
│  • Framing                     │  Supported speeds:             │
│  • Error detection (FCS/CRC)   │  10M / 100M / 1G / 10G        │
│  • Media access control        │  40G / 100G / 200G / 400G...  │
│    (CSMA/CD)                   │                                 │
└────────────────────────────────┴────────────────────────────────┘
```

### 🗂️ Ethernet II Frame Structure

```
┌───────────┬─────┬──────────────┬──────────────┬───────┬────────────────────┬─────┐
│ Preamble  │ SFD │  Dest. MAC   │  Src. MAC    │ Type  │       Data         │ FCS │
│  7 bytes  │1byte│   6 bytes    │   6 bytes    │2 bytes│   46–1500 bytes    │4byte│
└───────────┴─────┴──────────────┴──────────────┴───────┴────────────────────┴─────┘
 ← Layer 1 →      ← ─ ─ ─ ─ ─ ─ ─ ─ Layer 2 Frame  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─→
```

### 📋 Field Descriptions

| Field | Size | Description |
|---|---|---|
| **Preamble** | 7 bytes | 1-0 pattern; synchronization |
| **SFD** (Start Frame Delimiter) | 1 byte | Marks start of frame (last 2 bits: 11) |
| **Destination MAC Address** | 6 bytes | Address of the receiving device |
| **Source MAC Address** | 6 bytes | Address of the sending device |
| **Type** | 2 bytes | IPv4 = `0x0800`, IPv6 = `0x86DD` |
| **Data** | 46–1500 bytes | Layer 3 packet (IP + TCP + payload) |
| **FCS** (Frame Check Sequence) | 4 bytes | Error-check value computed by CRC algorithm |

### 📏 Frame Size Rules

```
Minimum frame size: 64 bytes   (46 bytes data + 18 bytes header/trailer)
Maximum frame size: 1518 bytes (1500 bytes data + 18 bytes header/trailer)

  < 64 bytes    → Runt Frame / Collision Fragment → Discarded
  > 1518 bytes  → Baby Giant Frame (VLAN tag adds 4 bytes → 1522 bytes)
  > 1600 bytes  → Jumbo Frame (up to 9000 bytes, used in NAS backup etc.)
```

### 🔍 How FCS Error Detection Works

```
SENDER SIDE:
  [Ethernet Header + Data] → CRC polynomial division → Compute FCS → Append → Send

RECEIVER SIDE:
  [Ethernet Header + Data] → CRC polynomial division → Compute value
                                                               │
                             Compare with FCS field value
                             │
                   Match     → Accept frame ✓
                   No match  → Discard frame ✗ (no notification sent)

 ⚠ Ethernet performs error DETECTION only, not correction.
 ⚠ Corrupted frames never reach upper layers; TCP requests retransmission if needed.
```

---

## 2. MAC Addresses

### 🔢 MAC Address Structure
```
┌─────────────────────────────────────────────────────────────────┐
│  MAC ADDRESS = 48 bits = 6 bytes = 12 hex digits                │
│                                                                 │
│  ┌──────────────────────────┬───────────────────────────────┐  │
│  │    OUI (Vendor Code)     │   Vendor Assigned (Serial)    │  │
│  │   24 bits = 6 hex        │   24 bits = 6 hex             │  │
│  │   (first 3 bytes)        │   (last 3 bytes)              │  │
│  └──────────────────────────┴───────────────────────────────┘  │
│                                                                 │
│  Example:  1C - 39 - 47  -  2F - 3A - 24                       │
│            └── OUI ────┘  └──── Serial ────┘                   │
│            (Manufacturer: e.g., Cisco)                          │
└─────────────────────────────────────────────────────────────────┘
```

### 📌 OUI (Organizationally Unique Identifier)
- Assigned to manufacturers by IEEE (~$3,000 USD one-time fee)
- Each OUI allows a manufacturer to define **~16 million** unique MAC addresses
- A manufacturer can obtain multiple OUIs (Apple, Samsung, etc.)
- OUI lookup: `maclookup.app` or Wireshark's OUI database

### 🖥️ How to View Your MAC Address
```bash
# Windows
ipconfig /all          # Look for "Physical Address"
                       # Example: 00-60-2F-3A-07-BC

# Linux / macOS
ifconfig               # or: ip link show

# Notation variations (all represent the same address):
  00-60-2F-3A-07-BC   ← Windows style (dashes)
  00:60:2F:3A:07:BC   ← Linux/macOS style (colons)
  0060.2F3A.07BC      ← Cisco style (dots, 4-digit groups)
```

> **💡 How many MAC addresses does a device have?**  
> One per network interface: Ethernet NIC → 1 MAC, Wi-Fi → 1 MAC, Bluetooth → 1 MAC  
> A laptop with all three has 3 separate MAC addresses.

### 🔒 MAC Address Security Notes
- MAC addresses are **valid only within the same network segment**
- Can be changed in software using tools like `macchanger` (Linux)
- Modern devices support **MAC randomization** to prevent tracking
- The OUI portion can reveal device brand/model during security analysis

---

## 3. MAC Address Types

```
┌─────────────────────────────────────────────────────────────────┐
│                    MAC ADDRESS TYPES                            │
├──────────────────┬──────────────────┬───────────────────────────┤
│   UNICAST        │   BROADCAST       │   MULTICAST               │
├──────────────────┼──────────────────┼───────────────────────────┤
│ Single device    │ All devices       │ Specific group            │
│                  │ on the segment   │                           │
│ 00-07-E9-63-CE-53│ FF-FF-FF-FF-FF-FF│ IPv4: 01-00-5E-xx-xx-xx  │
│                  │ (12 × F)         │ IPv6: 33-33-xx-xx-xx-xx  │
│ • Source MAC is  │                  │                           │
│   always unicast │ • Switch floods: │ • Only subscribing        │
│ • Use ARP to     │   all ports      │   devices accept          │
│   learn dest MAC │   except ingress │ • Smart switch (IGMP      │
│ • IPv4: ARP      │ • Router does    │   Snooping): delivers     │
│ • IPv6: NDP      │   NOT forward    │   only to subscribers     │
└──────────────────┴──────────────────┴───────────────────────────┘
```

### 🌐 Unicast Communication Flow
```
PC_A (IP: 1.5, MAC: AA-AA)  →  Server (IP: 1.200, MAC: ?)

  Step 1: "What is the MAC for 1.200?" → ARP Request (broadcast)
  Step 2: Server replies              → ARP Reply (unicast)
  Step 3: Store in ARP table         → 1.200 = SS-SS-SS-SS
  Step 4: Send frame                 → Dest MAC: SS-SS | Src MAC: AA-AA
```

### 📡 Broadcast Use Cases
```
Broadcast MAC (FF:FF:FF:FF:FF:FF) is used for:
  • ARP Request  (resolving IP to MAC)
  • DHCP Discover (finding a DHCP server)
  • Packets destined to 255.255.255.255

  ⚠ Routers do NOT forward broadcasts → Stays within the same network
```

### 📺 Multicast Communication
```
Example: Radio/video streaming
  Stream IP:   224.1.2.3   (224–239 range = Multicast)
  MAC Address: 01-00-5E-01-02-03

  Device A joined stream → Switch noted "A wants to receive"
  Device B joined stream → Switch noted "B wants to receive"
  Device C did not join

  When stream arrives:
  - Smart switch (IGMP Snooping enabled): Sends only to A and B ✓
  - Dumb switch (IGMP Snooping off):      Floods to everyone (like broadcast)
```

---

## 4. MAC Address Table and Switch Operation

### ⚖️ Hub vs. Switch Comparison

```
┌───────────────────────────────┬───────────────────────────────┐
│           HUB                 │           SWITCH               │
├───────────────────────────────┼───────────────────────────────┤
│  Layer 1 device               │  Layer 2 device               │
│  Does NOT look at MAC         │  Looks at MAC addresses       │
│  Forwards signal to ALL ports │  Forwards to correct port only│
│  (amplified)                  │                               │
│  HALF DUPLEX                  │  FULL DUPLEX                  │
│  (one sender at a time)       │  (all can send simultaneously)│
│  Collisions can occur         │  No collisions                │
│  CSMA/CD required             │  CSMA/CD not needed           │
│  10 Mbps shared               │  1 Gbps/port independent      │
│  No longer used ✗             │  Today's standard ✓           │
└───────────────────────────────┴───────────────────────────────┘
```

### 🔄 Switch MAC Address Table — 2-Step Process

```
┌─────────────────────────────────────────────────────────────────┐
│         WHAT HAPPENS WHEN A FRAME ARRIVES AT A SWITCH           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  STEP 1 — LEARN (Add source MAC to table)                      │
│  ─────────────────────────────────────────────────────────────  │
│  Which port did the frame arrive on? → Source MAC = that port  │
│  Already in table → Reset 300-second timer (refresh)           │
│  No packet for 300 seconds → Entry is deleted                  │
│                                                                 │
│  STEP 2 — FORWARD (Make decision based on destination MAC)     │
│  ─────────────────────────────────────────────────────────────  │
│  Dest MAC found in table   → Forward only to that port ✓      │
│  Dest MAC NOT in table     → Flood (all ports except ingress)  │
│    (Unknown Unicast)                                           │
│  Dest MAC = FF:FF:FF:FF:FF → Flood (all ports except ingress)  │
│    (Broadcast)                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 🗺️ Learning in a Multi-Switch Topology

```
       [SW1]                    [SW2]
  1─A   2─B   3─(to SW2)    6─C   7─D   8─R
                Port 3         Port 6

A pings D:
  1. SW1: "A is on Port1" → add to table. D unknown → Flood
  2. SW2: "A is on Port6" → add to table. D on Port7 → forward to D only
  3. D replies → SW2: "D is on Port7" added to table
               → SW1: "D is on Port3" added to table

Result: Both switches learn MAC addresses of all 5 devices (A,B,C,D,R).
Switches have no IP or MAC of their own — they only maintain the table.
```

### 📊 Sample MAC Address Table
```
SW1# show mac-address-table

Port    MAC Address        VLAN
──────────────────────────────
Fa0/1   AA-AA-AA-AA-AA-AA  1      ← Device A
Fa0/2   BB-BB-BB-BB-BB-BB  1      ← Device B
Fa0/3   CC-CC-CC-CC-CC-CC  1      ← Device C (via SW2)
Fa0/3   DD-DD-DD-DD-DD-DD  1      ← Device D (via SW2)
Fa0/3   RR-RR-RR-RR-RR-RR  1      ← Router (via SW2)
```

> **📌 CAM Table:** The MAC address table is stored in a specialized memory unit: **Content Addressable Memory (CAM)** — optimized for extremely fast MAC lookups.

### ⚡ Switch Capacity Example
```
Cisco C9200L-24T-4X:
  24 × 1 Gbps ports (TX + RX = 2 Gbps/port)
  4 × 10 Gbps uplink ports

  24 × 2 Gbps = 48 Gbps
  4 × 20 Gbps = 80 Gbps
  ─────────────────────
  Total: 128 Gbps switching capacity
  MAC table: 32,000 entries
```

---

## 5. Switch Forwarding Methods

### 🔀 Two Primary Methods

```
┌─────────────────────────────────────────────────────────────────┐
│  STORE-AND-FORWARD SWITCHING                                    │
│  ─────────────────────────────────────────────────────────────  │
│  1. Receive the entire frame (all 1518 bytes)                   │
│  2. Perform FCS/CRC error check                                 │
│  3. If error found → discard ✗                                 │
│  4. If valid → look up destination MAC → forward to port ✓     │
│                                                                 │
│  ✓ Advantage: Bad frames never reach destination (saves BW)    │
│  ✓ Required for QoS (VoIP prioritization)                      │
│  ✗ Disadvantage: Slower (must wait for full frame)             │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│  CUT-THROUGH SWITCHING                                          │
│  ─────────────────────────────────────────────────────────────  │
│  Forwarding begins before the frame is fully received — faster  │
│                                                                 │
│  Two sub-methods:                                               │
│                                                                 │
│  a) FAST-FORWARD:                                               │
│     → Forward as soon as first 6 bytes (destination MAC) read  │
│     → Lowest latency                                           │
│     → NO error checking                                        │
│                                                                 │
│  b) FRAGMENT-FREE:                                              │
│     → Read first 64 bytes, then start forwarding              │
│     → Most collisions occur in first 64 bytes                  │
│     → Balances speed and partial error protection              │
└─────────────────────────────────────────────────────────────────┘
```

### 📊 Visual Comparison
```
Frame (1518 bytes)
│─── 6 ───│─── 6 ───│── 2 ──│─────────────────────────│─ 4 ─│
│ Dst MAC │ Src MAC │ Type  │           Data           │ FCS │

Fast-Forward:  ├──►         (forward after 6 bytes)
Fragment-Free: ├──────────────────────────────►       (forward after 64 bytes)
Store&Forward: ├───────────────────────────────────────────────────────────►
               (receive all 1518 bytes, check FCS, then forward)
```

### 🔁 Automatic Mode Switching
Enterprise switches can automatically change methods based on statistics:
- Too many corrupted frames → **Switch from Cut-Through to Store-and-Forward**
- Error rate drops → Switch back to Cut-Through

---

## 6. Memory Buffering

When frames move between ports of different speeds, packets wait in a queue.

```
┌──────────────────────┬────────────────────────────────────────┐
│  Port-based Memory   │  Separate queue per port               │
│                      │  1 Gbps → 100 Mbps: possible congestion│
│                      │  One busy port can slow others         │
├──────────────────────┼────────────────────────────────────────┤
│  Shared Memory       │  All ports draw from a common pool     │
│                      │  More efficient resource utilization   │
│                      │  Used in modern Cisco switches         │
│                      │  Dynamic allocation per-port           │
└──────────────────────┴────────────────────────────────────────┘

If queue fills up → Newly arriving packets are dropped
TCP detects the loss and requests retransmission.
```

---

## 7. Port Settings: Duplex, Speed, and Auto-MDIX

### ⚙️ Switch Port Configuration Commands
```bash
SW1(config)# interface FastEthernet 0/1
SW1(config-if)# speed ?
    10    → Force 10 Mbps
    100   → Force 100 Mbps
    auto  → Auto-negotiate (default)

SW1(config-if)# duplex ?
    auto  → Auto-negotiate (default)
    full  → Force full duplex
    half  → Force half duplex

SW1(config-if)# mdix auto    ← Default (enabled)
SW1(config-if)# no mdix auto ← Disable

# All default values:
SW1(config-if)# speed auto
SW1(config-if)# duplex auto
SW1(config-if)# mdix auto
```

### 🔄 Duplex Settings

```
┌─────────────────────────────────────────────────────────────────┐
│  FULL DUPLEX                                                    │
│  • Send and receive simultaneously                             │
│  • Switch environment — independent channels per port          │
│  • No collisions → CSMA/CD not needed                         │
│  • Gigabit Ethernet operates ONLY in full duplex               │
├─────────────────────────────────────────────────────────────────┤
│  HALF DUPLEX                                                    │
│  • Either send or receive — not both at once                   │
│  • Hub environments / Wi-Fi / some industrial devices          │
│  • Collisions possible → CSMA/CD used                         │
│  • Possible at 10/100 Mbps                                     │
├─────────────────────────────────────────────────────────────────┤
│  AUTO NEGOTIATION (Default)                                     │
│  • Negotiates highest mutually supported speed and duplex      │
│  • Order: 1G Full → 100M Full → 100M Half → 10M Full → ...    │
└─────────────────────────────────────────────────────────────────┘
```

### ⚠️ Duplex Mismatch
```
       [SW1 — Full Duplex]  ←→  [SW2 — Half Duplex]
                             💥
  SW1: "I can send anytime"
  SW2: "I can only send when the line is clear!"
  Result: SW2 constantly detects collisions, heavy packet loss

  Fix: Set both sides to "auto" or force both to the same setting
  Best practice: Configure both Ethernet switch ports as full-duplex
```

### 🔌 Auto-MDIX (Automatic Cable Type Detection)
```
Legacy systems required careful cable selection:
  Switch ↔ Switch   → Crossover cable
  Switch ↔ Router   → Straight-through cable
  Switch ↔ PC       → Straight-through cable
  PC ↔ PC           → Crossover cable

With Auto-MDIX (enabled by default):
  Use any cable type → It just works ✓
  Device detects cable type and configures the interface automatically

Commands:
  mdix auto    → Auto-MDIX on (default)
  no mdix auto → Off (legacy cable rules apply)
```

---

## 8. Module Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODULE 7 SUMMARY                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ETHERNET LAYER STRUCTURE                                       │
│  • Layer 1 + Layer 2 protocol (IEEE 802.3)                     │
│  • LLC sublayer: upper-layer protocol identification           │
│  • MAC sublayer: Framing + Media access control                │
│                                                                 │
│  ETHERNET II FRAME FIELDS                                       │
│  Preamble | SFD | Dst MAC | Src MAC | Type | Data | FCS        │
│  Min: 64 bytes  |  Max: 1518 bytes                             │
│  FCS: CRC-based error detection                                │
│                                                                 │
│  MAC ADDRESSES                                                  │
│  • 48 bits = 12 hex digits                                     │
│  • First 6 hex = OUI (vendor code, assigned by IEEE)           │
│  • Last 6 hex = Serial number (assigned by vendor)             │
│  • View with: ipconfig /all (Windows)                          │
│                                                                 │
│  MAC ADDRESS TYPES                                              │
│  • Unicast: Single device (Source is always unicast)           │
│  • Broadcast: FF-FF-FF-FF-FF-FF → All (router doesn't forward) │
│  • Multicast: 01-00-5E-xx-xx-xx → Group (IPv4)                │
│                                                                 │
│  SWITCH OPERATION LOGIC                                         │
│  Step 1: Source MAC → paired with ingress port → add to table  │
│  Step 2: Dest MAC in table   → forward to that port only       │
│          Not in table (Unknown Unicast) → Flood all ports      │
│          Broadcast → Flood all ports                           │
│  After 300 seconds of silence → entry deleted from table       │
│  Table name: CAM (Content Addressable Memory)                  │
│                                                                 │
│  FORWARDING METHODS                                             │
│  Store-and-Forward: Full frame → FCS check → forward (safe)   │
│  Cut-Through Fast-Forward: 6 bytes (dst MAC) → forward (fast) │
│  Cut-Through Fragment-Free: 64 bytes → forward (balanced)     │
│                                                                 │
│  PORT SETTINGS (Default: auto)                                  │
│  • speed: 10 / 100 / auto                                      │
│  • duplex: full / half / auto                                  │
│  • mdix: auto (straight or crossover cable — doesn't matter)  │
│  • Gigabit Ethernet operates in full duplex ONLY               │
└─────────────────────────────────────────────────────────────────┘
```

---

> **📌 Next Module:** IPv4 Addressing — IP address structure and subnetting.

---
*CCNA1 – Introduction to Networks v7.0 | Module 7*
