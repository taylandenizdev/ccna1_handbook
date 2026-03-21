# CCNA1 – Module 8: The Network Layer (Layer 3)
> **Cisco CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Source: Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 Table of Contents
1. [Introduction to the Network Layer](#1-introduction-to-the-network-layer)
2. [IP Protocol Characteristics](#2-ip-protocol-characteristics)
3. [IPv4 Packet Header](#3-ipv4-packet-header)
4. [IPv6 Packet Header](#4-ipv6-packet-header)
5. [Host Routing Decisions](#5-host-routing-decisions)
6. [Router Routing Table](#6-router-routing-table)
7. [Static and Dynamic Routing](#7-static-and-dynamic-routing)
8. [Summary and Commands](#8-summary-and-commands)

---

## 1. Introduction to the Network Layer

### What is Layer 3?
The **Network Layer (L3)** provides end-to-end packet delivery between devices on different networks.

> 💡 **Layer 2 vs Layer 3:**
> - **Switch (Layer 2)** → Communicates within the same network using MAC addresses
> - **Router (Layer 3)** → Routes between different networks using IP addresses

### 4 Core Operations of the Network Layer

```
┌───────────────────────────────────────────────────────────────┐
│                  NETWORK LAYER OPERATIONS                     │
├────────────────┬───────────────┬───────────────┬─────────────┤
│   Addressing   │ Encapsulation │   Routing     │De-encapsula-│
│                │               │               │    tion     │
│ Assigns source │ Adds IP header│ Selects best  │ Strips      │
│ & destination  │ → PACKET is   │ path, forwards│ header,     │
│ IP addresses   │ created       │ packet        │ passes up   │
└────────────────┴───────────────┴───────────────┴─────────────┘
```

### Encapsulation through OSI Layers

```
Application (L7-L5)  │  DATA
                     ↓
Transport (L4)       │  [TCP Header | DATA]          → SEGMENT
                     ↓
Network (L3)         │  [IP Header | TCP | DATA]     → PACKET
                     ↓
Data Link (L2)       │  [EthH | IP | TCP | DATA | FCS] → FRAME
                     ↓
Physical (L1)        │  Bits (0s and 1s)
```

### 3 Addresses Required for a PC to Communicate

| Address | Description | Purpose |
|---------|-------------|---------|
| **IP Address** | Logical device address | Identifies source/destination |
| **Subnet Mask** | Identifies which network | Separates network/host portion |
| **Default Gateway** | Router's IP address | Exit point to other networks |

> ⚠️ **Without Default Gateway:** You can communicate on the same network, but cannot reach other networks!

---

## 2. IP Protocol Characteristics

### 2.1 Connectionless

```
    Sender                                      Receiver
       │                                            │
       │  Send packet (no prior notification)       │
       │───────────────────────────────────────────►│
       │                                            │
       │  ← No acknowledgment expected              │
       │  ← Is destination alive? Unknown           │
       │  ← Did packet arrive? Unknown              │
```

IP does **not establish a connection** before sending. Like sending a letter: you drop it in the mailbox without knowing if it arrives.

### 2.2 Best Effort

IP is a **"Best Effort Delivery Protocol"**:
- ✗ Does **not** guarantee packet delivery
- ✗ Does **not** retransmit lost packets
- ✗ Does **not** correct corrupt packets
- ✗ Does **not** reorder out-of-sequence packets
- ✓ These functions are handled by **TCP (Layer 4)**

### 2.3 Media Independent

```
                          ┌──────────┐
Copper Cable ─────────────►          │
Fiber Optic  ─────────────►  IP PACKET  ─────► Destination
WiFi (Wireless) ──────────►          │
4G / 5G      ─────────────►          │
                          └──────────┘
```

IP works **completely independently** of the transmission medium. The same IP packet can travel over copper, fiber, or wireless.

### 2.4 MTU (Maximum Transmission Unit) and Fragmentation

```
Ethernet MTU = 1500 bytes

┌────────────────────────────────────────────┐
│         1500-byte IP packet                │
└────────────────────────────────────────────┘
              │
              ▼ (Old WAN link, MTU=500 bytes)
  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
  │  Fragment 1  │ │  Fragment 2  │ │  Fragment 3  │
  │  500 bytes   │ │  500 bytes   │ │  500 bytes   │
  └──────────────┘ └──────────────┘ └──────────────┘
              │
              ▼ (Reassembly at destination)
┌────────────────────────────────────────────┐
│         Original 1500-byte packet          │
└────────────────────────────────────────────┘
```

> ⚠️ **Fragmentation is undesirable!**
> - Causes delay
> - Firewalls may drop fragmented packets
> - IPv6 does **not** support fragmentation

---

## 3. IPv4 Packet Header

### 3.1 IPv4 Header Structure (20 Bytes)

```
 Byte 1          Byte 2          Byte 3          Byte 4
┌───────────────┬───────────────┬───────────────────────────────┐
│    Version    │ Header Length │   DS (DSCP/ECN) │Total Length │
│      (4)      │   (20 bytes)  │   [QoS Field]   │             │
├───────────────────────────────┬───────────┬────────────────────┤
│         Identification        │   Flags   │  Fragment Offset   │
│                               │           │                    │
├───────────────┬───────────────┴────────────────────────────────┤
│  Time-to-Live │    Protocol   │       Header Checksum          │
│    (TTL)      │(6=TCP,17=UDP) │                                │
├───────────────────────────────────────────────────────────────┤
│                    Source IP Address (32 bits)                 │
├───────────────────────────────────────────────────────────────┤
│                  Destination IP Address (32 bits)             │
└───────────────────────────────────────────────────────────────┘
                                                Total: 20 Bytes
```

### 3.2 Header Field Descriptions

| Field | Value/Size | Description |
|-------|-----------|-------------|
| **Version** | 4 | Indicates IPv4 |
| **Header Length** | 20 bytes | Size of the IP header |
| **DS (DSCP)** | Variable | QoS – packet priority (voice=5, video=4) |
| **Total Length** | 46–1500 bytes | Header + Data total size |
| **Identification** | – | Identifies fragments |
| **Flags/Fragment Offset** | – | Fragmentation info (not used today) |
| **TTL** | 64/128/255 | Hop limit – decremented by 1 at each router |
| **Protocol** | 6=TCP, 17=UDP | Upper layer protocol identifier |
| **Header Checksum** | – | Header error detection |
| **Source IP** | 32 bits | Source IP address |
| **Destination IP** | 32 bits | **Most important** – destination IP address |

### 3.3 TTL (Time-to-Live)

```
Operating System          Default TTL Value
──────────────────────────────────────────
Linux / macOS                   64
Windows XP/Vista/7/10          128
Cisco Router IOS               255
```

**How TTL Works:**

```
Host A                   Internet                    Server
(TTL=64)  → Router1(63) → Router2(62) → ... → Router10(54) → Server
```

**Purpose of TTL:** Prevents packets trapped in a routing loop from circulating forever.

```
         Routing Loop Example:
         
Router1 → Router2 → Router3 → Router1 → Router2 → ...
                                ↑
                         When TTL=0 → packet is dropped!
```

**Analyzing TTL from ping:**
- Reply from Google with TTL=116 → 128 - 116 = **12 hops** away
- Reply from a site with TTL=54 → 64 - 54 = **10 hops** away

### 3.4 ARP Protocol (Learning MAC from IP)

```
Sending a Packet on the Same Network:

A (192.168.1.5)  →  Destination: 192.168.1.12 (B)

Step 1: "What is B's MAC address?" → ARP Broadcast (FF:FF:FF:FF:FF:FF)
         Goes to EVERYONE on the network

Step 2: B replies → "My MAC is: AA:BB:CC:DD:EE:FF"

Step 3: A writes to ARP table: 192.168.1.12 → AA:BB:CC:DD:EE:FF

Step 4: Send the packet:
         [Src MAC: A | Dst MAC: B | Src IP: A | Dst IP: B]
```

**Sending a Packet to a Different Network (Default Gateway):**

```
A (192.168.1.5) → Destination: 8.8.8.8 (Google – different network)

"8.8.8.8 is not on my network!" → Send to the Router!

Step 1: Use ARP to learn the Router's MAC address
         "Hey 192.168.1.1 (Default GW), what is your MAC?"

Step 2: Router replies → MAC: RR:RR:RR:RR:RR:RR

Step 3: Send packet:
         [Src MAC: A | Dst MAC: ROUTER | Src IP: A | Dst IP: 8.8.8.8]
         └── Layer 2 ──────────────────┘ └── Layer 3 ─────────────────┘
```

> ⚠️ **Critical Rule:** Source IP and Destination IP do **NOT change** along the path. Only MAC addresses change at each hop!

### 3.5 NAT (Network Address Translation)

```
Home Network (Private IPs)          Internet (Public IP)

PC1: 192.168.1.5  ─┐
PC2: 192.168.1.6  ─┤                         Google
TV:  192.168.1.7  ─┤→ Router → NAT → 85.x.x.x ─────────► 8.8.8.8
Tablet:192.168.1.8─┤          (One Public IP)
Phone:192.168.1.9 ─┘

NAT Table:
┌──────────────────┬──────────────────┬──────────────┐
│  Inside IP:Port  │  Outside IP:Port │  Destination │
├──────────────────┼──────────────────┼──────────────┤
│ 192.168.1.5:1234 │ 85.x.x.x:5000   │ 8.8.8.8:80   │
│ 192.168.1.6:5678 │ 85.x.x.x:5001   │ 8.8.8.8:443  │
└──────────────────┴──────────────────┴──────────────┘
```

**Private IP Ranges:**

| Class | Range |
|-------|-------|
| A | 10.0.0.0 – 10.255.255.255 |
| B | 172.16.0.0 – 172.31.255.255 |
| C | 192.168.0.0 – 192.168.255.255 |

---

## 4. IPv6 Packet Header

### 4.1 Why IPv6?

| Problem (IPv4) | Solution (IPv6) |
|----------------|-----------------|
| 4.3 billion addresses exhausted | 340 undecillion addresses (2¹²⁸) |
| NAT required → no end-to-end connectivity | Every device gets a real IP → no NAT needed |
| Increasing network complexity | Simplified header structure |
| Fragmentation supported | No fragmentation |

### 4.2 IPv6 Header Structure (40 Bytes – Fixed)

```
 Byte 1          Byte 2          Byte 3          Byte 4
┌───────────┬───────────────────┬────────────────────────────────┐
│  Version  │   Traffic Class   │          Flow Label            │
│    (6)    │   [QoS Field]     │   [Flow Label – 20 bits]       │
├───────────────────────────────┬───────────────┬────────────────┤
│         Payload Length        │  Next Header  │   Hop Limit    │
│       [Data size]             │(6=TCP,17=UDP) │    [= TTL]     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│            Source IPv6 Address (128 bits = 16 bytes)          │
│                                                               │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│          Destination IPv6 Address (128 bits = 16 bytes)       │
│                                                               │
└───────────────────────────────────────────────────────────────┘
                                                Total: 40 Bytes
```

### 4.3 IPv4 vs IPv6 Comparison

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Address Size | 32 bits | 128 bits |
| Total Addresses | ~4.3 billion | 340 undecillion |
| Header Size | 20 bytes (variable) | 40 bytes (fixed) |
| Header Checksum | ✓ Present | ✗ Removed |
| Fragmentation | ✓ Present | ✗ Removed |
| NAT | Required | Not needed |
| Header Fields | 12 fields | 8 fields |
| TTL Field Name | TTL | Hop Limit |

### 4.4 IPv6 Extension Headers

```
Basic IPv6 Packet:
┌──────────────┬──────────────────┐
│ IPv6 Header  │   TCP Segment    │
│ Next=6 (TCP) │                  │
└──────────────┴──────────────────┘

With Routing Header:
┌──────────────┬──────────────────┬──────────────────┐
│ IPv6 Header  │ Routing Header   │   TCP Segment    │
│ Next=43      │ Next=6 (TCP)     │                  │
└──────────────┴──────────────────┴──────────────────┘

With Authentication Header:
┌──────────────┬──────────────────┬───────────────────┬──────────┐
│ IPv6 Header  │ Routing Header   │ Auth. Header      │  TCP Seg │
│ Next=43      │ Next=51 (AH)     │ Next=6 (TCP)      │          │
└──────────────┴──────────────────┴───────────────────┴──────────┘
```

### 4.5 IPv6 Address Structure

```
2001:0DB8:020B:A11:0001:2233:4444:5555/64

└──────────────────────┘└──────────────────────┘
     Network Portion          Host Portion
      (first 64 bits)         (last 64 bits)
      Prefix = /64
```

---

## 5. Host Routing Decisions

### 5.1 Where Can a Host Send Packets?

```
                     ┌────────────────────────────────────┐
                     │       PACKET FORWARDING DECISION   │
                     └────────────────┬───────────────────┘
                                      │
          ┌───────────────────────────┼───────────────────────────┐
          │                           │                           │
          ▼                           ▼                           ▼
   ┌─────────────┐           ┌────────────────┐          ┌───────────────┐
   │   ITSELF    │           │   LOCAL HOST   │          │  REMOTE HOST  │
   │  Loopback   │           │ (Same Network) │          │(Other Network)│
   │  127.0.0.1  │           │                │          │               │
   │ (IPv6: ::1) │           │ Direct via MAC │          │ Send to DGW   │
   └─────────────┘           └────────────────┘          └───────────────┘
```

### 5.2 Loopback Address

- **127.0.0.0 – 127.255.255.255** → All are loopback
- Most common: **127.0.0.1** (localhost)
- Works if TCP/IP stack is installed and NIC is present
- Works even without internet connection or network cable!
- Test local web server: `http://127.0.0.1` or `http://localhost`

### 5.3 Same-Network Communication

```
PC1: 192.168.10.10 / 255.255.255.0 / GW: 192.168.10.1
PC2: 192.168.10.15 / 255.255.255.0 / GW: 192.168.10.1

PC1 → PC2 communication:
  "Is 192.168.10.15 on my network?"
  → Check subnet mask: 255.255.255.0
  → Network: 192.168.10.0
  → PC2 is 192.168.10.x → YES, same network!
  → Learn MAC via ARP → Send directly (no router needed)
```

### 5.4 Default Gateway Requirement

```
With Default GW:            Without Default GW:
────────────────            ─────────────────────
✓ Same network comms        ✓ Same network comms
✓ Different networks        ✗ CANNOT reach other networks!
✓ Access internet
```

### 5.5 PC Routing Table

```
C:\Users\PC1> netstat -r   (or: route print)

IPv4 Route Table
=================================================================
Active Routes:
Network Destination    Netmask      Gateway       Interface   Metric
        0.0.0.0        0.0.0.0   192.168.10.1  192.168.10.10    25
      127.0.0.0      255.0.0.0       On-link      127.0.0.1   306
      127.0.0.1  255.255.255.255     On-link      127.0.0.1   306
   192.168.10.0  255.255.255.0       On-link   192.168.10.10   281
  192.168.10.10  255.255.255.255     On-link   192.168.10.10   281
```

| Entry | Meaning |
|-------|---------|
| `0.0.0.0 / 0.0.0.0` | Default route – all unknown networks → send to GW |
| `127.0.0.0` | Loopback – to itself |
| `192.168.10.0` | Local network – forward directly |

### 5.6 IP Address Assignment Methods

| Method | IPv4 | IPv6 |
|--------|------|------|
| Manual (Static) | Set IP, SM, GW, DNS manually | Set IPv6 address manually |
| Automatic | **DHCP** server | **SLAAC** (from Router) |
| Hybrid | – | SLAAC + DHCPv6 |

---

## 6. Router Routing Table

### 6.1 How is the Routing Table Built?

```
         ROUTING TABLE CONSTRUCTION
         
┌────────────────────────────────────────────────────────────┐
│                                                            │
│  1. DIRECTLY CONNECTED (C/L) – Automatic                  │
│     Added automatically when IP is assigned to interface   │
│                                                            │
│  2. REMOTE NETWORKS                                        │
│     a) Static Route    → Administrator manually adds       │
│     b) Dynamic Route   → Learned via OSPF, EIGRP, RIP     │
│                                                            │
│  3. DEFAULT ROUTE (0.0.0.0/0)                             │
│     → Used when no match is found in routing table         │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### 6.2 Routing Table Example

```
Topology:
                   10.0.0.1      20.0.0.1 20.0.0.2      30.0.0.1
[Network1:10.0.0.0]──[R1:Fa0]──[R1:Fa1]──[R2:Fa0]──[Network3:30.0.0.0]
                          Network2: 20.0.0.0

R1 Routing Table:
┌───┬────────────────┬───────────────────┬──────────────────────┐
│ C │ 10.0.0.0/8     │ 255.0.0.0         │ FastEth 0            │
│ L │ 10.0.0.1/32    │ 255.255.255.255   │ FastEth 0            │
│ C │ 20.0.0.0/8     │ 255.0.0.0         │ FastEth 1            │
│ L │ 20.0.0.1/32    │ 255.255.255.255   │ FastEth 1            │
│ S │ 30.0.0.0/8     │ 255.0.0.0         │ 20.0.0.2             │
│S* │ 0.0.0.0/0      │ 0.0.0.0           │ 40.0.0.2 (ISP)       │
└───┴────────────────┴───────────────────┴──────────────────────┘
  C  = Connected (directly attached network)
  L  = Local (router's own interface IP)
  S  = Static (manually configured route)
  S* = Static Default Route
```

### 6.3 Router Packet Processing Steps

```
        WHAT HAPPENS WHEN A PACKET ARRIVES AT A ROUTER?

Packet Arrives
     │
     ▼
Strip Layer 2 header
(Is MAC address mine? Yes → continue)
     │
     ▼
Examine Layer 3 IP header
Destination IP = 30.0.0.5
     │
     ▼
Look up routing table:
"How can I reach 30.0.0.0 network?"
     │
     ├── No match found → Send to default route
     │
     └── 30.0.0.0/8 → Send to 20.0.0.2 ✓
               │
               ▼
     Add new Layer 2 header
     (Src MAC: R1_Fa1, Dst MAC: R2_Fa0)
               │
               ▼
     Forward packet out Fa1 interface
```

### 6.4 The show ip route Command

```
R1# show ip route
Codes: L - local, C - connected, S - static, R - RIP,
       O - OSPF, D - EIGRP, * - candidate default

Gateway of last resort is 209.165.200.226 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 209.165.200.226, GigabitEth0/0/1
O       10.1.1.0/24 [110/2] via 209.165.200.226, GigabitEth0/0/1
C       192.168.10.0/24 is directly connected, GigabitEth0/0/0
L       192.168.10.1/32 is directly connected, GigabitEth0/0/0
C       209.165.200.224/30 is directly connected, GigabitEth0/0/1
L       209.165.200.225/32 is directly connected, GigabitEth0/0/1

Code Legend:
  C  = Connected (directly attached network)
  L  = Local (interface's own IP address)
  S  = Static (manually entered route)
  S* = Default static route (last resort)
  O  = Route learned via OSPF
  D  = Route learned via EIGRP
  R  = Route learned via RIP
```

---

## 7. Static and Dynamic Routing

### 7.1 Static Routing

```
Command Syntax:
R1(config)# ip route [dest-network] [subnet-mask] [next-hop-IP]

Example:
R1(config)# ip route 10.1.1.0 255.255.255.0 209.165.200.226
             └── Destination ─┘ └─── Mask ───┘ └── Next Hop ──────┘
```

**Static Routing Characteristics:**

| Feature | Status |
|---------|--------|
| Manual configuration | Required |
| On topology change | Admin must update manually |
| Small networks | Well-suited |
| Large/complex networks | Difficult to manage |
| Automatic failover | Not supported |

**Scenario – The Problem with Static Routes:**

```
Normal State:            After Cable Failure:
PC1 → R1 → R2 → PC2    PC1 → R1 → R2 ✗ (link down!)
                                  ↓
                          Admin wakes up, drives in
                          Manually adds new static route:
                          R1(config)# ip route ... via R3
                                  ↓
                          PC1 → R1 → R3 → PC2 ✓
```

### 7.2 Dynamic Routing

```
Dynamic Routing Protocols:
┌────────────────────────────────────────────────────────────┐
│  OSPF (Open Shortest Path First)  ← Most common, standard │
│  EIGRP (Enhanced IGRP)            ← Cisco proprietary      │
│  RIP (Routing Information Protocol) ← Old, basic          │
└────────────────────────────────────────────────────────────┘

Routers announce to each other:
"I have the 30.0.0.0 network; my IP is 20.0.0.2"
      ↓
Neighbor router adds to table: 30.0.0.0 → 20.0.0.2 via OSPF
```

**Dynamic Routing Advantages:**

| Feature | Description |
|---------|-------------|
| Automatic discovery | Learns remote networks automatically |
| Up-to-date table | Adapts instantly to changes |
| Best path selection | Based on bandwidth, delay, hop count |
| Automatic failover | Switches to alternative path when link fails |

**Scenario – The Advantage of Dynamic Routing:**

```
While you sleep at night:

PC1 → R1 ──(10Gbit)──► R2 → PC2    (Primary path, active)
          └─(1Gbit)──► R3 ┘         (Backup path)

At 3am R2 link goes down:
Routers stop receiving announcements → table updates automatically
→ Path switches in milliseconds:

PC1 → R1 ──(1Gbit)──► R3 → PC2    (Automatic failover!)
```

### 7.3 Default Route

```
Purpose: The "last resort" exit for all packets that
         have no match in the routing table

ip route 0.0.0.0 0.0.0.0 [ISP-Router-IP]
               │
               └── "0.0.0.0/0" = All unknown networks

Your home modem also uses a default route:
  All unknown IPs → ADSL/FIBER → ISP
```

### 7.4 Routing Decision Flowchart

```
                     Packet Arrives
                           │
                           ▼
                Look up routing table
                           │
                ┌──────────┴──────────┐
                │                     │
          Match FOUND            No match
                │                     │
                ▼                     ▼
        Select most            Default route
        specific match         exists?
                │               /      \
                │             YES       NO
                │              │         │
                ▼              ▼         ▼
         Forward packet   Forward to   Drop packet
                          default     (ICMP: Unreachable)
                          gateway
```

---

## 8. Summary and Commands

### 8.1 Key Concepts Summary

```
┌──────────────────────────────────────────────────────────────┐
│                       MODULE 8 SUMMARY                       │
├──────────────────────┬───────────────────────────────────────┤
│ Layer                │ Layer 3 (Network Layer)               │
│ Protocols            │ IPv4, IPv6                            │
│ Device               │ Router                               │
│ Table                │ Routing Table                        │
│ Address Types        │ IP address (32-bit IPv4 / 128-bit IPv6│
├──────────────────────┼───────────────────────────────────────┤
│ IP Characteristics   │ Connectionless, Best Effort,          │
│                      │ Media Independent                     │
├──────────────────────┼───────────────────────────────────────┤
│ IPv4 Header          │ 20 bytes, variable                    │
│ IPv6 Header          │ 40 bytes, fixed                       │
├──────────────────────┼───────────────────────────────────────┤
│ Routing Types        │ Connected, Static, Dynamic, Default   │
├──────────────────────┼───────────────────────────────────────┤
│ Dynamic Protocols    │ OSPF, EIGRP, RIP                      │
└──────────────────────┴───────────────────────────────────────┘
```

### 8.2 Important Commands

| Command | Platform | Description |
|---------|----------|-------------|
| `show ip route` | Cisco Router | Display routing table |
| `show mac address-table` | Cisco Switch | Display MAC address table |
| `route print` | Windows | Display PC routing table |
| `netstat -r` | Windows/Linux | Display PC routing table |
| `ip route [net] [mask] [nexthop]` | Cisco Router | Add static route |
| `ping 127.0.0.1` | Any | Loopback test |
| `ping [IP]` | Any | Connectivity test |
| `tracert [IP]` | Windows | Show hops (TTL analysis) |

### 8.3 Routing Table Code Reference

| Code | Meaning |
|------|---------|
| `C` | Connected – directly attached network |
| `L` | Local – interface's own IP address |
| `S` | Static – manually configured route |
| `S*` | Static Default Route (last resort) |
| `O` | Learned via OSPF |
| `D` | Learned via EIGRP |
| `R` | Learned via RIP |

### 8.4 Quick Comparison: Switch vs Router

| Feature | Switch | Router |
|---------|--------|--------|
| Operating Layer | Layer 2 | Layer 3 |
| Table Maintained | MAC Address Table | Routing Table |
| Addressing | MAC Address | IP Address |
| Purpose | Intra-network communication | Inter-network communication |
| Protocol | Ethernet | IP, IPv6 |

### 8.5 IP Header Fields – Quick Reference Card

```
IPv4 Header (20 bytes):
Version | IHL | DSCP/ECN | Total Length | ID | Flags | Frag Offset
TTL | Protocol | Header Checksum | Source IP | Destination IP

IPv6 Header (40 bytes):
Version | Traffic Class | Flow Label | Payload Length
Next Header | Hop Limit | Source IPv6 | Destination IPv6

Key Fields to Remember:
  TTL (IPv4) / Hop Limit (IPv6) → Prevents routing loops
  Protocol / Next Header        → Identifies upper layer (TCP=6, UDP=17)
  Source + Destination IP       → Unchanged end-to-end (except NAT)
  DS / Traffic Class            → QoS prioritization
```

---

> 📘 **Next Module:** Module 9 – Address Resolution (Subnetting & IPv4 Addressing)

---

*CCNA1 Module 8 | Ağ Yöneticileri | gokhan@agyoneticileri.org | ozan@agyoneticileri.org*
