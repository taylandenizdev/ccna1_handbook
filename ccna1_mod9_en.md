# 📘 CCNA1 – Module 9: Address Resolution

> **Course:** Introduction to Networks v7.0 (ITN)  
> **Module Objective:** Explain how ARP and IPv6 Neighbor Discovery enable communication on a network.

---

## 📑 Table of Contents

1. [MAC and IP Addresses](#1-mac-and-ip-addresses)
2. [Address Resolution Protocol (ARP)](#2-address-resolution-protocol-arp)
3. [ARP Issues and Security](#3-arp-issues-and-security)
4. [IPv6 Neighbor Discovery (ND)](#4-ipv6-neighbor-discovery-nd)
5. [Command Reference](#5-command-reference)
6. [Module Summary](#6-module-summary)

---

## 1. MAC and IP Addresses

### 1.1 Two Types of Addresses

Every device on an Ethernet network is assigned **two different addresses**:

| Feature | MAC Address | IP Address |
|---|---|---|
| Layer | Layer 2 (Data Link) | Layer 3 (Network) |
| Type | Physical | Logical |
| Permanence | Burned into NIC, fixed | Changes per network |
| Size | 48 bits (6 bytes) | 32 bit (IPv4) / 128 bit (IPv6) |
| Format | `1C-3B-F3-19-E7-F9` | `192.168.1.5` |
| View command | `ipconfig /all` → Physical Address | `ipconfig` → IPv4 Address |

### 1.2 MAC Address Structure

```
|<------- 48 bits ------->|
+------------+-------------+
|    OUI     |  Serial No  |
|  (24 bits) |  (24 bits)  |
+------------+-------------+
  Manufacturer   Device
     code        number
```

- **First 24 bits (OUI):** Manufacturer's identifier (Apple, Intel, Cisco, etc.)
- **Last 24 bits:** Serial number assigned by the manufacturer

### 1.3 Communication on the Same Network

When source and destination are on the **same network** (e.g., both on `/24`):

```
PC1 (192.168.10.10)  ──────────────────►  PC2 (192.168.10.11)
     MAC: aa-aa-aa                              MAC: 55-55-55

Frame Header:
┌────────────────┬────────────────┬──────────────────┬──────────────────┐
│  Dest MAC      │  Source MAC    │  Source IPv4     │  Dest IPv4       │
│  55-55-55      │  aa-aa-aa      │  192.168.10.10   │  192.168.10.11   │
└────────────────┴────────────────┴──────────────────┴──────────────────┘
```

> **Rule:** Same network → Destination MAC = **Destination device's MAC address**

### 1.4 Communication on a Remote Network

When the destination is on a **different network**, the packet goes through the router first:

```
PC1 (192.168.10.10)  ──►  Router R1  ──►  ...  ──►  PC2 (10.1.1.10)
     MAC: aa-aa-aa          MAC: bb-bb-bb                 MAC: 55-55-55

Frame from PC1 → R1:
┌────────────────┬────────────────┬──────────────────┬──────────────────┐
│  Dest MAC      │  Source MAC    │  Source IPv4     │  Dest IPv4       │
│  bb-bb-bb      │  aa-aa-aa      │  192.168.10.10   │  10.1.1.10       │
└────────────────┴────────────────┴──────────────────┴──────────────────┘
          ▲
          └─── Default Gateway (Router) MAC address!
```

> **Rule:** Remote network → Destination MAC = **Default Gateway's (Router's) MAC address**

---

## 2. Address Resolution Protocol (ARP)

### 2.1 What is ARP?

**ARP (Address Resolution Protocol):**
- Used to learn the **MAC address from an IP address** on the same network.
- A Layer 2 protocol (no IP header).
- Specific to IPv4; IPv6 uses a different mechanism.

```
           ARP
  IP Address ──────────► MAC Address
  (known)                (discovered)
```

### 2.2 ARP Table

Every device stores learned IP–MAC mappings in a table:

```
C:\>arp -a
  Internet Address    Physical Address    Type
  192.168.1.1         0001.c7ee.eb01      dynamic
  192.168.1.20        000c.852e.33aa      dynamic
  192.168.1.30        0060.2fbe.8bce      dynamic
```

- **dynamic:** Automatically learned via ARP protocol
- **static:** Manually configured
- Entries are automatically removed after a timeout period (Linux: ~2 min, Windows: ~4–5 min)

### 2.3 ARP Operation – Step by Step

#### ARP Request – Broadcast

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ARP Request (Broadcast)                          │
│                                                                     │
│   PC1 (1.10)       Entire Network        PC2 (1.20)               │
│       │                                      │                      │
│       │──── "Hey ALL NETWORK! ──────────────►│                      │
│       │      Device with IP 192.168.1.20,    │                      │
│       │      please send me your MAC!"       │                      │
│       │                                      │                      │
└─────────────────────────────────────────────────────────────────────┘

Frame Header:
  Dest MAC  : FF:FF:FF:FF:FF:FF  ← Broadcast (goes to everyone)
  Source MAC: aa:bb:cc:dd:ee:ff  ← PC1's MAC address

ARP Data (Opcode 1 = Request):
  Sender MAC: aa:bb:cc:dd:ee:ff
  Sender IP : 192.168.1.10
  Target MAC: 00:00:00:00:00:00  ← Unknown (empty)
  Target IP : 192.168.1.20
```

#### ARP Reply – Unicast

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ARP Reply (Unicast)                              │
│                                                                     │
│   PC1 (1.10)                            PC2 (1.20)                 │
│       │                                      │                      │
│       │◄─── "Hi, I am 1.20.  ────────────────│                      │
│       │      My MAC address is MAC2!"         │                      │
│       │                                      │                      │
└─────────────────────────────────────────────────────────────────────┘

Frame Header:
  Dest MAC  : aa:bb:cc:dd:ee:ff  ← Only to PC1 (Unicast)
  Source MAC: 11:22:33:44:55:66  ← PC2's MAC address

ARP Data (Opcode 2 = Reply):
  Sender MAC: 11:22:33:44:55:66
  Sender IP : 192.168.1.20
  Target MAC: aa:bb:cc:dd:ee:ff
  Target IP : 192.168.1.10
```

### 2.4 ARP Flow Diagram – Same Network Communication

```
                PC1 wants to ping PC2 on the same network
                              │
                              ▼
              ┌───────────────────────────────┐
              │  Check ARP table:             │
              │  Is 192.168.1.20 in it?       │
              └───────────────────────────────┘
                       │           │
                      Yes          No
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ Send Broadcast          │
                       │  │ ARP Request             │
                       │  │ (FF:FF:FF:FF:FF:FF)     │
                       │  └─────────────────────────┘
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ PC2 sends Unicast       │
                       │  │ ARP Reply → to PC1      │
                       │  └─────────────────────────┘
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ Write to ARP table:     │
                       │  │ 192.168.1.20 → MAC2     │
                       │  └─────────────────────────┘
                       │           │
                       └─────┬─────┘
                             │
                             ▼
              ┌───────────────────────────────┐
              │  Send packet with target MAC  │
              │  Dest MAC: MAC2               │
              └───────────────────────────────┘
```

### 2.5 ARP Flow Diagram – Remote Network Communication

```
        PC1 wants to send a packet to a server on a different network
                              │
                              ▼
              ┌───────────────────────────────┐
              │  Subnet Mask check:           │
              │  Is destination on same net?  │
              └───────────────────────────────┘
                       │           │
                      Yes         No → Remote network!
                       │           │
                       │           ▼
                       │  ┌─────────────────────────────┐
                       │  │ Look up Default Gateway     │
                       │  │ MAC in ARP table            │
                       │  └─────────────────────────────┘
                       │           │           │
                       │         Found       Not found
                       │           │           │
                       │           │           ▼
                       │           │  ┌──────────────────────┐
                       │           │  │ Send ARP Request     │
                       │           │  │ for Router (Bcast)   │
                       │           │  └──────────────────────┘
                       │           │           │
                       │           │           ▼
                       │           │  ┌──────────────────────┐
                       │           │  │ Router sends ARP     │
                       │           │  │ Reply → Write table  │
                       │           │  └──────────────────────┘
                       │           │           │
                       │           └─────┬─────┘
                       └───────────┬─────┘
                                   │
                                   ▼
              ┌────────────────────────────────────────┐
              │  Dest MAC = Router (Gateway) MAC       │
              │  Dest IP  = Server's IP address        │
              │  Send the packet!                      │
              └────────────────────────────────────────┘
```

### 2.6 ARP on Routers

Routers also use ARP:

- They learn the MAC addresses of **all devices** in all connected networks.
- They need each device's MAC to forward packets to them.
- Command to view a router's ARP table:

```
Router1> show ip arp

Protocol  Address         Age(min)  Hardware Addr    Type    Interface
Internet  192.168.1.1     -         0001.C7EE.EB01   ARPA    GigabitEthernet0/0
Internet  192.168.1.10    19        0003.E4E7.0B72   ARPA    GigabitEthernet0/0
Internet  192.168.2.1     -         0001.C7EE.EB02   ARPA    GigabitEthernet0/1
Internet  192.168.2.20    19        00E0.A3A7.CE87   ARPA    GigabitEthernet0/1
```

---

## 3. ARP Issues and Security

### 3.1 ARP Broadcasting Problem

**Problem:** Excessive broadcast traffic on large networks

```
On a network with 1000 users:
  - Each device sends ARP queries to learn the Gateway MAC
  - 1000 broadcast packets hit the network simultaneously
  - Network performance degrades
  - NIC cards are burdened with unnecessary processing
```

**Solution:** Divide networks into smaller segments using VLANs

### 3.2 ARP Poisoning (ARP Spoofing) Attack

**How it works:**

```
Normal Operation:
  PC-A ──── ARP Request ────────────────► Router
  PC-A ◄─── ARP Reply (from Router) ───── Router

Attack Scenario (ARP Spoofing):
  PC-A ──── ARP Request ────────────────► Router + Attacker
  PC-A ◄─── Fake ARP Reply ──────────── Attacker
             "I am 192.168.1.1,
              my MAC address is 00-0C"
              (actually the attacker's MAC!)
```

**Attack flow:**

```
  ┌──────────────────────────────────────────────────────────┐
  │                  ARP Poisoning Attack                    │
  │                                                          │
  │   PC-A            Attacker (C)          Router (R1)     │
  │    │                   │                     │           │
  │    │── ARP Request ───►│─────────────────────►│          │
  │    │                   │                     │           │
  │    │◄─ Fake ARP Reply ─│  "I'm the Router!"  │           │
  │    │  (attacker's MAC) │                     │           │
  │    │                   │                     │           │
  │    │── Packet ────────►│  (sent to attacker!)│           │
  │    │                   │──── Forward ───────►│           │
  │    │                   │◄─── Return packet ──│           │
  │    │◄─ Return packet ──│                     │           │
  │                                                          │
  │   = Man in the Middle (MITM) Attack                     │
  └──────────────────────────────────────────────────────────┘
```

### 3.3 Mitigation Techniques

| Technique | Description |
|---|---|
| **Dynamic ARP Inspection (DAI)** | Enterprise switch feature; detects and blocks fake ARP packets |
| **Static ARP entries** | Manually map critical devices (e.g., gateway) MAC addresses |
| **802.1X Authentication** | Requires username/password to join the network |
| **Port Security** | Prevents unauthorized devices from connecting to switch ports |
| **HTTPS/Encryption** | Even if traffic is intercepted, content remains unreadable |

> ⚠️ **Note:** Dynamic ARP Inspection (DAI) configuration will be covered in CCNA 2 – LAN Security.

---

## 4. IPv6 Neighbor Discovery (ND)

### 4.1 What Replaces ARP in IPv6?

ARP is **not used** in IPv6. Instead:

```
IPv4  →  ARP (Address Resolution Protocol)
IPv6  →  ICMPv6 Neighbor Discovery (ND) Protocol
```

IPv6 also has **no broadcast** → **Multicast** is used instead.

### 4.2 Neighbor Discovery (ND) Message Types

ICMPv6 ND protocol has **5 message types**:

| Message | Abbreviation | IPv4 Equivalent | Purpose |
|---|---|---|---|
| Neighbor Solicitation | NS | ARP Request | Request to learn a neighbor's MAC |
| Neighbor Advertisement | NA | ARP Reply | MAC address response |
| Router Solicitation | RS | — | Request to discover routers |
| Router Advertisement | RA | — | Router's IP configuration response |
| Redirect | — | ICMP Redirect | Better path redirection |

### 4.3 Address Resolution – NS and NA

#### Neighbor Solicitation (NS) – equivalent to ARP Request

```
PC1 (2001:db8:acad:1::10)         PC2 (2001:db8:acad:1::11)
        │                                   │
        │── NS Message (Multicast) ────────►│
        │   "Hey whoever has               │
        │    2001:db8:acad:1::11,           │
        │    send me your MAC address!"     │
        │                                   │
```

#### Neighbor Advertisement (NA) – equivalent to ARP Reply

```
        │                                   │
        │◄── NA Message (Unicast) ──────────│
        │   "I am 2001:db8:acad:1::11,      │
        │    my MAC is f8-94-c2-e4-c5-0A"   │
        │                                   │
```

### 4.4 Router Discovery – RS and RA

**When a PC boots without a static IP:**

```
        PC (newly connected)                  Router
              │                                  │
              │── RS Message ──────────────────► │
              │   "Are there any routers here?   │
              │    How do I get IP information?" │
              │                                  │
              │◄── RA Message ───────────────────│
              │   "I'm a router! Your options:   │
              │    1) Create your own IP         │
              │    2) Create your own IP +       │
              │       ask DHCP for DNS           │
              │    3) Get everything from DHCP"  │
```

#### The 3 Options in RA Message (SLAAC)

```
┌──────────────────────────────────────────────────────────────────┐
│              Router Advertisement (RA) Options                   │
├──────┬───────────────────────────────────────────────────────────┤
│  1   │ SLAAC (Stateless): Build your own IP                     │
│      │ • Network prefix → Learn from Router                     │
│      │ • Host portion → Generate yourself (/64)                 │
│      │ • Default Gateway → Learn from Router                    │
│      │ → No DHCP server needed                                  │
├──────┼───────────────────────────────────────────────────────────┤
│  2   │ SLAAC + DHCP: Build own IP, ask for DNS                  │
│      │ • IP address → via SLAAC                                 │
│      │ • DNS address → from DHCPv6                              │
├──────┼───────────────────────────────────────────────────────────┤
│  3   │ Stateful DHCPv6: Get everything from DHCP               │
│      │ • Preferred in enterprise networks                       │
│      │ • DHCP tracks who has which IP                          │
└──────┴───────────────────────────────────────────────────────────┘
```

### 4.5 Route Redirection

```
PC-A                    Router A              Router B
  │                         │                     │
  │── Packet ──────────────►│                     │
  │                         │                     │
  │◄─ Redirect Message ─────│                     │
  │   "To reach that        │                     │
  │    destination, use     │                     │
  │    Router B instead!"   │                     │
  │                         │                     │
  │── Packet ───────────────────────────────────►│
```

> IPv6 routers use Redirect messages to point hosts toward a more preferable (better) router.

### 4.6 ARP vs Neighbor Discovery Comparison

| Feature | IPv4 – ARP | IPv6 – ICMPv6 ND |
|---|---|---|
| Request packet | ARP Request (Broadcast) | NS – Neighbor Solicitation (Multicast) |
| Reply packet | ARP Reply (Unicast) | NA – Neighbor Advertisement (Unicast) |
| Transmission type | Broadcast | Multicast |
| IP header | None (L2 protocol) | Yes (inside ICMPv6) |
| Router discovery | None (separate DHCP/static) | RS/RA messages |
| Path redirection | ICMP Redirect | ICMPv6 Redirect |

---

## 5. Command Reference

### Windows / PC Commands

```bash
# View ARP table
arp -a

# Delete ARP table (run as Administrator)
arp -d

# View IP and MAC information
ipconfig /all
```

### Cisco IOS Commands (Router)

```bash
# View ARP table
Router> show ip arp

# View routing table
Router> show ip route
```

---

## 6. Module Summary

```
┌──────────────────────────────────────────────────────────────────────┐
│                       MODULE 9 – SUMMARY                            │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  MAC Address     → Physical address, Layer 2, burned into NIC       │
│  IP Address      → Logical address, Layer 3, changes per network    │
│                                                                      │
│  Same network    → Dest MAC = Target device's MAC address           │
│  Remote network  → Dest MAC = Default Gateway's MAC address         │
│                                                                      │
│  ARP             → IPv4 protocol for IP → MAC resolution            │
│  ARP Request     → Broadcast (FF:FF:FF:FF:FF:FF)                    │
│  ARP Reply       → Unicast (only to requester)                      │
│  ARP Table       → arp -a (view), arp -d (delete)                   │
│  Router ARP      → show ip arp                                       │
│                                                                      │
│  ARP Issues      → Excessive broadcast / ARP Poisoning attack       │
│  DAI             → Dynamic ARP Inspection (switch protection)       │
│  MITM            → Man in the Middle attack                         │
│                                                                      │
│  IPv6 ND         → ARP equivalent for IPv6 (ICMPv6)                │
│  NS              → Neighbor Solicitation (like ARP Request)         │
│  NA              → Neighbor Advertisement (like ARP Reply)          │
│  RS              → Router Solicitation (router discovery)           │
│  RA              → Router Advertisement (IP config response)        │
│  IPv6            → No Broadcast, Multicast is used instead          │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Key Rules

> 1. **Ethernet communication uses MAC addresses** — IP addresses are only for routing decisions.
> 2. **Without ARP, Ethernet cannot function** — It cannot be disabled; static MAC entries can be added instead.
> 3. **ARP Request is Broadcast; ARP Reply is Unicast.**
> 4. **The ARP table is temporary** — Entries are automatically removed after a timeout.
> 5. **IPv6 does not use ARP** — ICMPv6 Neighbor Discovery is used instead.
> 6. **IPv6 has no broadcast** — Multicast is used.

---

*CCNA1 – Introduction to Networks v7.0 | Module 9: Address Resolution*
