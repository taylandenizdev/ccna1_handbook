# 📘 Module 11: IPv4 Addressing
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Source: Cisco Networking Academy  
> *(Compiled from two video transcripts)*

---

## 📋 Table of Contents
1. [Quick Recap: Encapsulation and IP Header](#1-quick-recap-encapsulation-and-ip-header)
2. [IPv4 Address Structure](#2-ipv4-address-structure)
3. [Subnet Mask and Prefix Length](#3-subnet-mask-and-prefix-length)
4. [Network and Broadcast Addresses](#4-network-and-broadcast-addresses)
5. [IP Communication Types](#5-ip-communication-types)
6. [IP Address Classes (Classful Addressing)](#6-ip-address-classes-classful-addressing)
7. [Public and Private IP Addresses](#7-public-and-private-ip-addresses)
8. [IP Distribution Hierarchy](#8-ip-distribution-hierarchy)
9. [Special IP Addresses (Loopback, APIPA)](#9-special-ip-addresses-loopback-apipa)
10. [Subnetting Fundamentals](#10-subnetting-fundamentals)
11. [Subnetting Calculation Tables](#11-subnetting-calculation-tables)
12. [VLSM (Variable Length Subnet Mask)](#12-vlsm-variable-length-subnet-mask)
13. [Network Design Summary](#13-network-design-summary)

---

## 1. Quick Recap: Encapsulation and IP Header

### 🗂️ OSI Encapsulation Summary
```
┌──────────────────────────────────────────────────────────────────┐
│         APPLICATION LAYER DATA                                   │
├───────────────────────────────────────────────────────────┬──────┤
│  UDP Header (8 bytes)  │  UDP Data                        │      │
├────────────────────────┴──────────────────────────────────┤      │
│  TCP Header (20 bytes) │  TCP Data                Segment │      │
├────────────────────────┴──────────────────────────────────┤      │
│  IP Header  (20 bytes) │  IP Data                  Packet │      │
├──────────┬─────────────┴──────────────────────────────────┤      │
│Eth.Header│       Ethernet Data (46–1500 bytes)    │  CRC  │Frame │
│(14 bytes)│                                        │(4byte)│      │
└──────────┴────────────────────────────────────────┴───────┴──────┘
 Total headers: 18 + 20 + 20 = 58 bytes  |  Data: max. 1460 bytes
 Efficiency: ~96% data  |  ~4% headers
```

### 📋 Key IP Header Fields
| Field | Description |
|---|---|
| **Source IP** | Sender's IP address — does NOT change along the path |
| **Destination IP** | Receiver's IP address — does NOT change along the path |
| **TTL** | Time To Live — decremented by 1 at each router; packet dropped at 0 |
| **Protocol** | Upper layer: TCP=6, UDP=17, ICMP=1 |
| **Total Length** | Header + data size in bytes |
| **Header Checksum** | IP header integrity check |

> **🔑 Core Rule:** MAC addresses change at every router hop. IP addresses remain constant from source to destination.

---

## 2. IPv4 Address Structure

### 🔢 32 Bits → Dotted Decimal Notation
```
┌──────────────────────────────────────────────────────────────────┐
│  IPv4 = 32 bits = 4 octets (4 × 8 bits)                         │
│                                                                  │
│  Binary:                                                         │
│  11000000 . 10101000 . 00000001 . 00000101                       │
│     ↓           ↓          ↓          ↓                          │
│    192    .    168    .     1     .    5                          │
│                                                                  │
│  Dotted Decimal Notation: 192.168.1.5                            │
└──────────────────────────────────────────────────────────────────┘
```

### 📊 8-Bit Place Values (Memorization Table)
```
┌───────┬───────┬───────┬───────┬───────┬───────┬───────┬───────┐
│  128  │  64   │  32   │  16   │   8   │   4   │   2   │   1   │
├───────┴───────┴───────┴───────┴───────┴───────┴───────┴───────┤
│  Minimum: 00000000 = 0                                         │
│  Maximum: 11111111 = 128+64+32+16+8+4+2+1 = 255               │
│  Each octet ranges from 0 to 255                               │
└────────────────────────────────────────────────────────────────┘
```

> **⚠️ 256 does NOT exist.** If someone says "172.256.10.5" — that's an invalid address.

### 🏗️ Two Parts of an IP Address
```
┌─────────────────────────────────────────────────────────────────┐
│  IP ADDRESS = NETWORK PORTION + HOST PORTION                    │
│                                                                 │
│  Example: 192.168.10.25  /24                                    │
│           ┌────────────┐  ┌──┐                                  │
│           │192.168.10  │  │25│                                  │
│           └────────────┘  └──┘                                  │
│           NETWORK PART     HOST                                 │
│           (fixed)          (unique per device)                  │
│                                                                 │
│  All devices on the same network start with 192.168.10.x       │
│  x can be: 1–254                                               │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Subnet Mask and Prefix Length

### 🎯 What is a Subnet Mask?
A 32-bit value that identifies which portion of an IP address is the **network** and which is the **host**. It always starts with **1s** and ends with **0s**.

```
┌──────────────────────────────────────────────────────────────────┐
│  IP Address:   192.168.10.25                                     │
│  Subnet Mask:  255.255.255.0                                     │
│                                                                  │
│  Binary:                                                         │
│  IP:   11000000.10101000.00001010.00011001                       │
│  Mask: 11111111.11111111.11111111.00000000                       │
│        ├──────────────────────────┤├───────┤                     │
│        NETWORK (1s)                HOST (0s)                     │
│        192.168.10                   .25                          │
└──────────────────────────────────────────────────────────────────┘
```

### 📐 Subnet Mask ↔ Prefix (Slash) Notation
Prefix length = number of **1-bits** in the subnet mask

```
┌──────────────────────────────────────────────────────────────────┐
│  Subnet Mask          Prefix    Network bits   Host bits         │
├───────────────────────┼─────────────────────── ──────────────────┤
│  255.0.0.0          = /8        8 bits          24 bits          │
│  255.255.0.0        = /16       16 bits         16 bits          │
│  255.255.255.0      = /24       24 bits          8 bits          │
│  255.255.255.128    = /25       25 bits          7 bits          │
│  255.255.255.192    = /26       26 bits          6 bits          │
│  255.255.255.224    = /27       27 bits          5 bits          │
│  255.255.255.240    = /28       28 bits          4 bits          │
│  255.255.255.248    = /29       29 bits          3 bits          │
│  255.255.255.252    = /30       30 bits          2 bits          │
└──────────────────────────────────────────────────────────────────┘
```

> **📌 Key Point:** An IP address alone cannot tell you how much is network and how much is host. The subnet mask must always accompany it.

### 🔢 How to Calculate /28 Subnet Mask?
```
/28 → 28 bits in the network portion
8 + 8 + 8 = 24 bits already used → need 4 more bits in last octet

Last octet: 128 + 64 + 32 + 16 = 240

Subnet mask: 255.255.255.240
```

---

## 4. Network and Broadcast Addresses

### 📍 Core Rules
```
┌──────────────────────────────────────────────────────────────────┐
│  Example Network: 192.168.1.0 /24                                │
│                                                                  │
│  HOST PORTION                PURPOSE                             │
│  ─────────────────────       ────────────────────────────────── │
│  All 0s  → 192.168.1.0   = NETWORK ADDRESS                      │
│             (cannot assign; used in routing tables)              │
│                                                                  │
│  1 – 254 → 192.168.1.1   = First usable IP                      │
│           → 192.168.1.254 = Last usable IP                      │
│             (can be assigned to devices)                         │
│                                                                  │
│  All 1s  → 192.168.1.255 = BROADCAST ADDRESS                    │
│             (cannot assign; sends packet to all on this network) │
└──────────────────────────────────────────────────────────────────┘
```

### 🧮 IP Count Formula
```
  Total Addresses     = 2^(host bit count)
  Usable IP Addresses = 2^(host bit count) − 2

  /24 → host=8 bits → 2^8 = 256 addresses → 254 usable IPs
  /25 → host=7 bits → 2^7 = 128 addresses → 126 usable IPs
  /26 → host=6 bits → 2^6 =  64 addresses →  62 usable IPs
  /30 → host=2 bits → 2^2 =   4 addresses →   2 usable IPs
```

### 📊 Network Calculation Table (Example: 192.168.1.0/24)
```
┌─────────────────┬─────────────────┬───────────────────┬─────────┐
│ Network Address │ Broadcast Addr. │ Usable IP Range   │ IP Count│
├─────────────────┼─────────────────┼───────────────────┼─────────┤
│ 192.168.1.0     │ 192.168.1.255   │ .1 – .254         │   254   │
│ 192.168.2.0     │ 192.168.2.255   │ .1 – .254         │   254   │
│ 172.16.0.0/16   │ 172.16.255.255  │ 16.0.1–16.255.254 │ 65,534  │
└─────────────────┴─────────────────┴───────────────────┴─────────┘
```

---

## 5. IP Communication Types

```
┌─────────────────────────────────────────────────────────────────┐
│                  IP COMMUNICATION TYPES                         │
├──────────────────┬──────────────────┬───────────────────────────┤
│   UNICAST        │   BROADCAST       │   MULTICAST               │
├──────────────────┼──────────────────┼───────────────────────────┤
│ One-to-one       │ One → All        │ One → Group               │
│                  │                  │                           │
│  PC_A → PC_B     │  PC_A → everyone │  PC_A → (subscribers)     │
│                  │                  │                           │
│ Source IP is     │ Two types:        │ IP: 224.0.0.0 –           │
│ always unicast   │ 1) x.x.x.255     │     239.255.255.255        │
│                  │ 2) 255.255.255.255│ MAC: 01-00-5E-xx-xx-xx    │
│ Dest MAC:        │    (limited bcast)│                           │
│ Target's MAC or  │                  │ Devices join a group       │
│ router's MAC     │ Routers BLOCK it! │                           │
└──────────────────┴──────────────────┴───────────────────────────┘
```

### 📡 Broadcast Types
```
255.255.255.255   → Limited Broadcast → Same network only
                   (Router BLOCKS this)

x.x.x.255        → Directed Broadcast → Specific remote network
(e.g. 192.168.1.255)  (Router normally BLOCKS; can be enabled)
```

> **DHCP IP acquisition:** New device has no IP → Destination: 255.255.255.255 (Limited Broadcast) → DHCP server responds.

---

## 6. IP Address Classes (Classful Addressing)

### 🏫 Class Table
```
┌───────┬──────────────┬──────────┬──────────┬────────────────────┐
│ Class │ First Octet  │ Prefix   │ IP Count │ Intended Use       │
├───────┼──────────────┼──────────┼──────────┼────────────────────┤
│   A   │  1  – 127    │   /8     │ ~16 M    │ Very large corps   │
│   B   │ 128 – 191    │   /16    │  65,534  │ Medium-sized orgs  │
│   C   │ 192 – 223    │   /24    │     254  │ Small businesses   │
│   D   │ 224 – 239    │  N/A     │    –     │ Multicast          │
│   E   │ 240 – 255    │  N/A     │    –     │ Research (reserved)│
└───────┴──────────────┴──────────┴──────────┴────────────────────┘
```

### 🔍 How to Identify the Class?
```
Look at the first octet:

  1  – 127  → Class A  (binary starts with 0:   0xxxxxxx)
  128 – 191 → Class B  (binary starts with 10:  10xxxxxx)
  192 – 223 → Class C  (binary starts with 110: 110xxxxx)
  224 – 239 → Class D  (starts with 1110)
  240 – 255 → Class E  (starts with 1111)

Examples:
  10.0.0.1    → Class A (10 → between 1–127)
  172.16.0.1  → Class B (172 → between 128–191)
  192.168.1.1 → Class C (192 → between 192–223)
```

### ⚠️ Problem with Classful Addressing
```
┌─────────────────────────────────────────────────────────────────┐
│  IP Block Given    Actual Need      Block Assigned    Waste     │
├─────────────────────────────────────────────────────────────────┤
│  Class A           10 computers     16 MILLION IPs   ~16M      │
│  Class B           2,000 users      65,534 IPs       ~63,000   │
│  Class C           30 users         254 IPs          ~224      │
│                                                                 │
│  → IPs distributed too generously → Depleted quickly!          │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Transition to Classless Addressing (CIDR)
Classless Inter-Domain Routing replaced classful addressing. Now any prefix length can be assigned based on need:
- Need 16,000 IPs → get /18 (even from a Class A block)
- Need 500 IPs → get /23
- Just-in-time, efficient distribution

---

## 7. Public and Private IP Addresses

### 🔐 Private (Special) IP Blocks
RFC 1918 standard — cannot reach the internet; internal network only:

```
┌──────────────────────────────────────────────────────────────────┐
│  PRIVATE IP BLOCKS (RFC 1918)                                    │
├──────────────────┬──────────────────────────────────────────────┤
│  10.0.0.0/8      │ 1 × Class A block → ~16 million IPs         │
│  172.16.0.0/12   │ 16 × Class B blocks (172.16–172.31)         │
│  192.168.0.0/16  │ 256 × Class C blocks (192.168.0–255)        │
└──────────────────┴──────────────────────────────────────────────┘

  These addresses are not routable on the internet.
  Anyone can use them freely inside their network.
  Home networks typically use: 192.168.1.x
```

### 🌐 NAT (Network Address Translation)
```
┌─────────────────────────────────────────────────────────────────┐
│                      HOW NAT WORKS                              │
│                                                                 │
│  INSIDE (Private IP)                OUTSIDE (Public IP)        │
│                                                                 │
│  PC_A: 192.168.1.5             Router  ISP IP: 85.1.2.3        │
│  PC_B: 192.168.1.6    ──NAT──▶ ──────────────────▶  INTERNET   │
│  PC_C: 192.168.1.7             Google sees: 85.1.2.3            │
│                                                                 │
│  Outgoing: Private IP → Public IP translation                  │
│  Incoming: Public IP → Private IP translation                  │
│  NAT table maintained (which private IP used which port)       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. IP Distribution Hierarchy

```
┌─────────────────────────────────────────────────────────────────┐
│                        ICANN                                    │
│           (Internet Corporation for Assigned Names)             │
│                          │                                      │
│                        IANA                                     │
│         (Internet Assigned Numbers Authority)                   │
│           IP, Port, Domain management                           │
│                          │                                      │
│          ┌───────────────┼───────────────┐                     │
│          ▼               ▼               ▼                     │
│  ┌──────────────┐  ┌──────────┐  ┌───────────┐               │
│  │    ARIN      │  │   RIPE   │  │  APNIC    │               │
│  │ North America│  │  Europe, │  │   Asia-   │               │
│  │              │  │  Middle  │  │  Pacific  │               │
│  │              │  │  East,   │  │           │               │
│  │              │  │  N. Asia │  │           │               │
│  └──────────────┘  └──────────┘  └───────────┘               │
│                                                                 │
│  + LACNIC (Latin America)  + AFRINIC (Africa)                  │
│                                                                 │
│  TURKEY → Gets IPs from RIPE NCC                               │
│  (ripe.net → Whois query to find IP owner)                     │
└─────────────────────────────────────────────────────────────────┘

  RIR → Allocates blocks to ISPs
  ISP → Distributes to companies/individuals as leases
  Dual ISP? → Get your own block from RIPE, announce via BGP
```

---

## 9. Special IP Addresses (Loopback, APIPA)

```
┌──────────────────────────────────────────────────────────────────┐
│  SPECIAL IP BLOCKS                                               │
├──────────────────┬──────────────────────────────────────────────┤
│  127.0.0.0/8     │ LOOPBACK                                     │
│  (typically      │ • Ping device's own TCP/IP stack             │
│  127.0.0.1)      │ • Tests if TCP/IP is working                 │
│                  │ • No cable needed; just an installed NIC     │
├──────────────────┼──────────────────────────────────────────────┤
│  169.254.0.0/16  │ APIPA / Link-Local Address                   │
│                  │ • Assigned when no DHCP server found         │
│                  │ • Windows calls this APIPA                   │
│                  │ • Device picks a random 169.254.x.x          │
│                  │ • Subnet mask: 255.255.0.0                   │
│                  │ • NO default gateway → cannot reach internet │
│                  │ • For local network communication only       │
└──────────────────┴──────────────────────────────────────────────┘
```

---

## 10. Subnetting Fundamentals

### ❓ Why Subnet?
```
PROBLEM (Single Large Network):
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  [400 users – one /24 network]                                  │
│                                                                 │
│  PC_A ──ARP broadcast──▶ goes to ALL 400 users                  │
│  PC_B ──DHCP broadcast──▶ goes to ALL 400 users                 │
│  PC_C ──ARP broadcast──▶ goes to ALL 400 users                  │
│                                                                 │
│  Problems:                                                      │
│  ✗ Excessive broadcast traffic → network slows down            │
│  ✗ No security (everyone can reach everyone)                   │
│  ✗ Attack affects the entire network                           │
│  ✗ Cannot determine device location                            │
└─────────────────────────────────────────────────────────────────┘

SOLUTION (Subnetting):
┌─────────────────────────────────────────────────────────────────┐
│  [400 users → 2 × /25 networks: 200 + 200]                     │
│                                                                 │
│  Subnet 1 (192.168.1.0/25)  ──[ROUTER]──  Subnet 2             │
│  PC_A ──ARP──▶ only 200 users             (192.168.1.128/25)   │
│                                                                 │
│  Benefits:                                                      │
│  ✓ Broadcast traffic reduced                                   │
│  ✓ Network performance improved                                │
│  ✓ Inter-network security established                          │
│  ✓ IP location is predictable (2.x = Block B, etc.)            │
│  ✓ Network management simplified                               │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Subnetting Mechanism: Borrowing Bits
```
Original network: 192.168.1.0 /24

Borrow N bits from the host portion → they become subnet bits

/24 + 1 bit → /25  →  2 subnets  ×  126 IPs/subnet
/24 + 2 bit → /26  →  4 subnets  ×   62 IPs/subnet
/24 + 3 bit → /27  →  8 subnets  ×   30 IPs/subnet
/24 + 4 bit → /28  → 16 subnets  ×   14 IPs/subnet
/24 + 5 bit → /29  → 32 subnets  ×    6 IPs/subnet
/24 + 6 bit → /30  → 64 subnets  ×    2 IPs/subnet

Formulas:
  Number of Subnets = 2^(borrowed bits)
  Addresses/Subnet  = 2^(remaining host bits)
  Usable IPs        = 2^(remaining host bits) − 2
```

### 🏫 Broadcast Domain
```
Broadcast domain = area where broadcast packets can travel
Routers DO NOT forward broadcast packets

┌──────────────────────────────────────────────────────────────────┐
│  [SW1]──PC1  ──[R1]──  [SW2]──PC3  ──[R2]──  [SW3]──PC5       │
│       └──PC2           └──PC4               └──PC6             │
│                                                                 │
│  Broadcast         Broadcast         Broadcast                 │
│  Domain 1          Domain 2          Domain 3                  │
│                                                                 │
│  3 Networks = 3 Broadcast Domains                              │
└──────────────────────────────────────────────────────────────────┘
```

---

## 11. Subnetting Calculation Tables

### 📊 Quick Reference: Dividing a /24 Network
```
┌────────────┬──────────┬────────────┬──────────────┬───────────┐
│  Prefix    │  Subnet  │  Addr/Sub  │  IPs/Subnet  │ Block Size│
│            │  Count   │            │    (−2)      │           │
├────────────┼──────────┼────────────┼──────────────┼───────────┤
│   /24      │    1     │    256     │    254       │    256    │
│   /25      │    2     │    128     │    126       │    128    │
│   /26      │    4     │     64     │     62       │     64    │
│   /27      │    8     │     32     │     30       │     32    │
│   /28      │   16     │     16     │     14       │     16    │
│   /29      │   32     │      8     │      6       │      8    │
│   /30      │   64     │      4     │      2       │      4    │
└────────────┴──────────┴────────────┴──────────────┴───────────┘
Rule: Know the block size → network addresses are: 0, block, 2×block...
```

### 🗺️ /26 Subnetting Detailed Example (192.168.1.0/24 → 4 subnets)
```
Borrowed 2 bits → /26 → Block size: 64

┌───────────────────────────────────────────────────────────────┐
│  Subnet 0                                                    │
│  Network  : 192.168.1.0                                      │
│  Broadcast: 192.168.1.63                                     │
│  IP Range : .1 – .62      (62 IPs)                          │
├───────────────────────────────────────────────────────────────┤
│  Subnet 1                                                    │
│  Network  : 192.168.1.64                                     │
│  Broadcast: 192.168.1.127                                    │
│  IP Range : .65 – .126    (62 IPs)                          │
├───────────────────────────────────────────────────────────────┤
│  Subnet 2                                                    │
│  Network  : 192.168.1.128                                    │
│  Broadcast: 192.168.1.191                                    │
│  IP Range : .129 – .190   (62 IPs)                          │
├───────────────────────────────────────────────────────────────┤
│  Subnet 3                                                    │
│  Network  : 192.168.1.192                                    │
│  Broadcast: 192.168.1.255                                    │
│  IP Range : .193 – .254   (62 IPs)                          │
└───────────────────────────────────────────────────────────────┘
```

### 🧮 Quick Network Address Lookup
To find which subnet an IP belongs to:

```
Example: 192.168.1.99 /27 → What is the network address?

/27 → Block size: 32
Network addresses: 0, 32, 64, 96, 128, 160, 192, 224...

Which block contains 99?  96 ≤ 99 < 128
→ Network address: 192.168.1.96
→ Broadcast:       192.168.1.127
→ IP range:        .97 – .126
```

### 📊 /16 Network Subnet Summary
```
┌─────────┬───────────┬──────────────┬───────────────────────────┐
│ Prefix  │ Subnets   │  IPs/Subnet  │  Notes                    │
├─────────┼───────────┼──────────────┼───────────────────────────┤
│ /17     │     2     │   32,766     │  1 bit borrowed           │
│ /18     │     4     │   16,382     │  2 bits borrowed          │
│ /19     │     8     │    8,190     │  3 bits borrowed          │
│ /23     │   128     │      510     │  7 bits borrowed          │
│ /24     │   256     │      254     │  8 bits borrowed (common) │
│ /30     │ 16,384    │        2     │  14 bits borrowed         │
└─────────┴───────────┴──────────────┴───────────────────────────┘
```

---

## 12. VLSM (Variable Length Subnet Mask)

### ❓ What is VLSM?
Using **different prefix lengths** within the same IP block. Without VLSM, fixed-size subnets waste IPs.

### ⚠️ Problem Without VLSM
```
Requirement:
  3 × 28-user networks
  3 × 2-IP router links

Solution (without VLSM, all /27):
  Each subnet: 30 IPs — 6 subnets × 30 IPs = 180 IPs
  2-IP links also got /27 → 28 IPs wasted per link!
  3 links × 28 wasted IPs = 84 IPs WASTED
```

### ✅ Efficient Solution with VLSM
```
┌─────────────────────────────────────────────────────────────────┐
│  Large networks:   /27 (30 IPs) → use 3                        │
│  Router links:     /30  (2 IPs) → use 3                        │
│                                                                 │
│  192.168.1.0/27   → User network 1  (.1–.30)                   │
│  192.168.1.32/27  → User network 2  (.33–.62)                  │
│  192.168.1.64/27  → User network 3  (.65–.94)                  │
│                                                                 │
│  192.168.1.96/30  → Router link 1   (.97–.98)                  │
│  192.168.1.100/30 → Router link 2   (.101–.102)                │
│  192.168.1.104/30 → Router link 3   (.105–.106)                │
│                                                                 │
│  Much less IP waste!                                           │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 VLSM Visual Structure
```
Original block: 192.168.1.0/24 (256 addresses)
                          │
          ┌───────────────┴──────────────────┐
          ▼                                   ▼
   /25 (128 addresses)               /25 (128 addresses)
    192.168.1.0/25                   192.168.1.128/25
          │                                   │
     ┌────┴────┐                         (in use)
     ▼         ▼
/26 (64)    /26 (64)
 .0–.63     .64–.127
                │
           ┌────┴────┐
           ▼         ▼
      /28 (16)   /28 (16)   ...
      .64–.79   .80–.95
```

---

## 13. Network Design Summary

### 📋 Design Steps
```
┌─────────────────────────────────────────────────────────────────┐
│  STEP 1: Determine IP Type                                     │
│  ─────────────────────────────────────────────────────────────  │
│  Private IP? → 10.x.x.x / 172.16.x.x / 192.168.x.x            │
│  Public IP?  → Obtain from RIPE/ARIN, announce via BGP         │
│                                                                 │
│  STEP 2: Plan Subnets                                          │
│  ─────────────────────────────────────────────────────────────  │
│  What to divide by?                                             │
│  → By floor/location (floor 1 /24, floor 2 /24...)            │
│  → By user type (Staff /24, Guest /24...)                      │
│  → By device type (IP cameras, IP phones...)                   │
│                                                                 │
│  STEP 3: Calculate Each Subnet                                 │
│  ─────────────────────────────────────────────────────────────  │
│  • Network address                                              │
│  • Broadcast address                                            │
│  • Usable IP range                                              │
│  • IP count                                                     │
│                                                                 │
│  STEP 4: Assign IPs to Devices                                 │
│  ─────────────────────────────────────────────────────────────  │
│  Servers:      Static IP                                        │
│  PCs/Laptops:  DHCP (automatic)                                │
│  Routers:      Static IP (each interface)                      │
│  Switches:     Management IP (static)                          │
└─────────────────────────────────────────────────────────────────┘
```

### 🖥️ PC IP Configuration (Reminder)
```
Three values needed for static IP:
  1. IP Address        → 192.168.1.25
  2. Subnet Mask       → 255.255.255.0  (/24)
  3. Default Gateway   → 192.168.1.1  (Router's IP)
  (+ DNS Server       → 8.8.8.8 for example)

ipconfig /all  → Shows IP, MAC, DHCP server, DNS in Windows
```

### 📐 Practical Subnet Selection Guide
```
How many users?    →  Which prefix?

      2 users       →  /30  (2 IPs)
   2 –   6          →  /29  (6 IPs)
   7 –  14          →  /28  (14 IPs)
  15 –  30          →  /27  (30 IPs)
  31 –  62          →  /26  (62 IPs)
  63 – 126          →  /25  (126 IPs)
 127 – 254          →  /24  (254 IPs)
255 – 510           →  /23  (510 IPs)
511 – 1022          →  /22  (1022 IPs)
```

---

## 🔑 Module 11 Summary

```
┌─────────────────────────────────────────────────────────────────┐
│  CORE CONCEPTS                                                  │
├─────────────────────────────────────────────────────────────────┤
│  • IPv4 = 32 bits = 4 octets, each 0–255                       │
│  • IP = Network portion + Host portion                         │
│  • Subnet mask: defines which is which                         │
│  • Network address: all host bits = 0 (used in routing)        │
│  • Broadcast address: all host bits = 1 (send to all)          │
│  • IP count: 2^host − 2                                        │
│                                                                 │
│  ADDRESS TYPES                                                  │
├─────────────────────────────────────────────────────────────────┤
│  • Unicast: one-to-one                                         │
│  • Broadcast: one-to-all (router does NOT forward)             │
│  • Multicast: one-to-group (224–239.x.x.x)                     │
│  • Private: RFC1918 (10, 172.16–31, 192.168)                   │
│  • Loopback: 127.0.0.1 (self-test)                             │
│  • APIPA: 169.254.x.x (auto-assigned when DHCP fails)          │
│                                                                 │
│  SUBNETTING                                                     │
├─────────────────────────────────────────────────────────────────┤
│  • Borrow bits → create subnets                                │
│  • Subnet count = 2^(borrowed bits)                            │
│  • Block size = 2^(remaining host bits)                        │
│  • VLSM = different prefix lengths = efficient IP use          │
│                                                                 │
│  IP DISTRIBUTION                                                │
├─────────────────────────────────────────────────────────────────┤
│  ICANN → IANA → RIR (RIPE/ARIN/APNIC/LACNIC/AFRINIC)           │
│  → ISP → Companies/Individuals                                 │
│  Turkey: in RIPE NCC region                                    │
└─────────────────────────────────────────────────────────────────┘
```

---
> **📌 Next Module:** IPv4 header details and introduction to IPv6.

---
*CCNA1 – Introduction to Networks v7.0 | Module 11*
