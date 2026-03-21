# 📘 CCNA1 – Module 12: IPv6 Addressing

> **Course:** Introduction to Networks v7.0 (ITN)  
> **Module Objective:** Implement an IPv6 addressing scheme.

---

## 📑 Table of Contents

1. [Why IPv6?](#1-why-ipv6)
2. [IPv4 to IPv6 Migration Techniques](#2-ipv4-to-ipv6-migration-techniques)
3. [IPv6 Address Format and Compression Rules](#3-ipv6-address-format-and-compression-rules)
4. [IPv6 Address Types](#4-ipv6-address-types)
5. [Special IPv6 Addresses](#5-special-ipv6-addresses)
6. [Static GUA and LLA Configuration](#6-static-gua-and-lla-configuration)
7. [Dynamic IPv6 GUA Addressing (SLAAC & DHCPv6)](#7-dynamic-ipv6-gua-addressing-slaac--dhcpv6)
8. [Interface ID Generation: EUI-64 vs Random](#8-interface-id-generation-eui-64-vs-random)
9. [Dynamic IPv6 LLA Addressing](#9-dynamic-ipv6-lla-addressing)
10. [IPv6 Multicast Addresses](#10-ipv6-multicast-addresses)
11. [IPv6 Subnetting](#11-ipv6-subnetting)
12. [Command Reference](#12-command-reference)
13. [Module Summary](#13-module-summary)

---

## 1. Why IPv6?

### 1.1 IPv4 Limitations

| Feature | IPv4 | IPv6 |
|---|---|---|
| Bit length | 32 bits | 128 bits |
| Total addresses | ~4.3 billion (2³²) | 340 undecillion (2¹²⁸) |
| Subnet mask | Yes | **No** → Prefix Length used |
| Broadcast | Yes | **No** → Multicast used instead |
| NAT required | Yes | **Not needed** |

### 1.2 IPv4 Exhaustion Timeline

```
  ARIN    (North America)  →  July 2015
  RIPE    (Europe)         →  September 2012
  APNIC   (Asia-Pacific)   →  June 2014
  AFRINIC (Africa)         →  2020 (Projected)
  LACNIC  (Latin America)  →  April 2011
```

> IPv6 was designed in the early 2000s in anticipation of this exhaustion — over 10 years in advance.

---

## 2. IPv4 to IPv6 Migration Techniques

The entire internet infrastructure cannot switch to IPv6 overnight. Three transition techniques were developed:

```
┌─────────────────────────────────────────────────────────────────┐
│              IPv4 → IPv6 Transition Techniques                   │
├──────────────┬──────────────────────────────────────────────────┤
│  Dual Stack  │ Device runs both IPv4 and IPv6 simultaneously.    │
│              │ Both stacks active. Most widely used today.       │
├──────────────┼──────────────────────────────────────────────────┤
│  Tunneling   │ Carries IPv6 traffic over an IPv4 network.        │
│              │ IPv6 packet is encapsulated inside an IPv4 packet. │
│              │ Allows two IPv6 sites to communicate even when     │
│              │ the ISP only supports IPv4.                        │
├──────────────┼──────────────────────────────────────────────────┤
│  Translation │ NAT64 — Allows IPv6 devices to communicate with   │
│              │ IPv4-only devices. Only needed for legacy devices; │
│              │ Dual Stack is preferred today.                     │
└──────────────┴──────────────────────────────────────────────────┘
```

---

## 3. IPv6 Address Format and Compression Rules

### 3.1 Basic Structure

```
2001:0DB8:AC10:FE01:0000:0000:0000:0000
│    │    │    │    │    │    │    │
└─── Each is 16 bits (hextet) ────────┘
          Total 8 hextets = 128 bits

Separator: Colon ( : )  ← NOT a dot
```

- **8 hextets** — each 16 bits, written in hexadecimal
- Separated by **colons** `:` — not dots (to avoid confusion with IPv4)
- Case-insensitive: `2001:DB8` = `2001:db8`

### 3.2 Rule 1: Omit Leading Zeros

You can omit **leading zeros** within each hextet:

```
01ab  →  1ab      (drop leading zero)
09f0  →  9f0
0a00  →  a00
0000  →  0        (four zeros → single zero)

⚠️ Do NOT omit trailing zeros!
2000  →  2000     (writing "200" would cause ambiguity)
```

**Example:**
```
Full:       2001:0db8:0000:1111:0000:0000:0000:0200
Compressed: 2001:db8:0:1111:0:0:0:200
```

### 3.3 Rule 2: Double Colon ( :: ) Rule

Replace one or more consecutive all-zero hextets with `::`:

```
2001:db8:0:1111:0:0:0:200
                ↑ ↑ ↑
         3 zero hextets → replace with ::

→  2001:db8:0:1111::200
```

**⚠️ GOLDEN RULE: `::` can only be used ONCE in an address!**

```
VALID:    2001:db8:0:1111::200
VALID:    2001:db8::1111:0:0:0:200
INVALID:  2001:db8::1111::200   ← USED TWICE — NOT ACCEPTED!
```

**Why only once?**
```
If a device sees: 2001:db8::1111::200
8 hextets needed, 4 written, 2 double-colons present.
How many zeros go to each? → IMPOSSIBLE TO DETERMINE!
```

### 3.4 Compression Examples

```
Full address:
  2001:0DB8:0000:0000:0008:8000:0000:417A

Step 1 – Remove leading zeros:
  2001:DB8:0:0:8:8000:0:417A

Step 2 – Replace longest zero run with ::
  2001:DB8::8:8000:0:417A

Alternative (less compressed but still valid):
  2001:DB8:0:0:8:8000::417A
```

### 3.5 Prefix Length

IPv6 has **no subnet mask**. Prefix length is used instead:

```
IPv4: 192.168.1.0 / 255.255.255.0  →  192.168.1.0/24
IPv6: 2001:db8:acad:1::/64

Slash 64 → First 64 bits = Network (Prefix), last 64 bits = Host (Interface ID)

128-bit total:
┌──────────────────────┬──────────────────────┐
│   Network (Prefix)   │  Host (Interface ID)  │
│       64 bits        │       64 bits         │
└──────────────────────┴──────────────────────┘
```

> Note: IPv6 calls the network portion the **"prefix"** and the host portion the **"Interface ID"**. Same concept as IPv4's network and host parts.

---

## 4. IPv6 Address Types

### 4.1 Three Main Types

| Type | Description | IPv4 Equivalent |
|---|---|---|
| **Unicast** | Uniquely identifies a single interface | Unicast |
| **Multicast** | Sends one packet to multiple destinations | Multicast |
| **Anycast** | Same IP assigned to multiple devices; routed to nearest | Partial equivalent exists |

> ⚠️ **There is NO Broadcast in IPv6!** Instead, `ff02::1` (all-nodes multicast) achieves a similar result.

### 4.2 Unicast Subtypes

```
                    IPv6 Unicast Addresses
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   Global Unicast     Link-Local         Unique Local
      (GUA)             (LLA)               (ULA)
   2000::/3          fe80::/10          fc00::/7
   Internet-         Same link only.    Closed/private
   routable.         Cannot cross       networks only.
   (Like public      a router.          (NOT like IPv4
    IPv4 address)                        private space!)
```

---

## 5. Special IPv6 Addresses

### 5.1 Global Unicast Address (GUA)

Used for internet communication. Assigned by the ISP.

```
Active GUA range: 2000:: to 3FFF:FFFF:...
First 3 bits: 001 (starts with 2xxx or 3xxx)

GUA Structure (typical /48 allocation):
┌────────────────┬──────────┬──────────────────────┐
│ Global Routing │ Subnet   │    Interface ID       │
│    Prefix      │    ID    │                       │
│    48 bits     │  16 bits │       64 bits         │
└────────────────┴──────────┴──────────────────────┘
      ISP assigns   You use    Device/user creates
```

### 5.2 Link-Local Address (LLA)

Automatically created the moment IPv6 is enabled on an interface. Cannot cross a router.

```
Range: FE80:: to FEBF:FFFF:...
       (fe80::/10)

Characteristics:
  ✓ Automatically assigned when IPv6 is enabled
  ✓ No manual configuration needed
  ✓ Required for same-link communication
  ✗ Cannot cross a router
  ✗ Cannot reach the internet

Use cases:
  • Neighbor Discovery communication
  • Used as the Default Gateway address
  • Routing protocol messages (e.g., OSPFv3)
```

### 5.3 Unique Local Address (ULA)

```
Range: FC00:: to FDFF:FFFF:...
       (fc00::/7)

What it is NOT: NOT the IPv6 equivalent of IPv4 private addresses (192.168.x.x)
What it IS: For closed networks that will never connect to the internet
Examples: Military networks, railway signaling, power grid control
```

### 5.4 Loopback Address

```
IPv4: 127.0.0.0/8 range → 16 million addresses wasted!
IPv6: ::1/128            → Just ONE address!

Test command:
  ping ::1
  → Gets a reply from the local machine itself
```

### 5.5 Address Type Summary Table

| Address | Range | Purpose |
|---|---|---|
| GUA | `2000::/3` → `3FFF.../3` | Global internet |
| LLA | `fe80::/10` | Same link only |
| ULA | `fc00::/7` | Closed enterprise networks |
| Loopback | `::1/128` | Self-ping / testing |
| Multicast | `ff00::/8` | Group delivery |

---

## 6. Static GUA and LLA Configuration

### 6.1 Static GUA on a Cisco Router

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

> The only difference from IPv4 commands: use `ipv6 address` instead of `ip address`.

### 6.2 Static LLA on a Cisco Router

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address fe80::1:1 link-local
R1(config-if)# no shutdown
R1(config-if)# exit
```

### 6.3 Static GUA on a Windows Host

Network Adapter Properties → IPv6 Properties:

```
IPv6 address      : 2001:db8:acad:1::10
Subnet prefix len : 64
Default gateway   : 2001:db8:acad:1::1   (or router's LLA)
DNS server        : 2001:db8:acad:2::B
```

> **Best Practice:** Use the router's **LLA address** as the default gateway.  
> (When using DHCPv6 or SLAAC, the router's LLA is automatically specified as the default gateway.)

---

## 7. Dynamic IPv6 GUA Addressing (SLAAC & DHCPv6)

### 7.1 The Dynamic IP Process

IPv4 had one method: DHCP. IPv6 has **three methods**:

```
       PC boots up
           │
           ▼
   ┌───────────────────────────────┐
   │  Send RS message              │
   │  (Router Solicitation)        │
   │  "Are there any routers?"     │
   │  Destination: ff02::2         │
   └───────────────────────────────┘
           │
           ▼
   ┌───────────────────────────────┐
   │  Router sends RA message      │
   │  (Router Advertisement)       │
   │  "I'm here! Here's the info." │
   └───────────────────────────────┘
           │
    ┌──────┴──────┐
    │  Based on   │
    │  RA flags   │
    └──────┬──────┘
           │
   ┌───────┼───────────────┐
   ▼       ▼               ▼
Method 1  Method 2     Method 3
 SLAAC   SLAAC+DHCP  Stateful DHCP
```

### 7.2 Method 1: SLAAC (Stateless Address Autoconfiguration)

```
RA flags: Managed:"0"  Other:"0"

Router tells the client:
  • Prefix: 2001:db8:acad:1::/64
  • Gateway: FE80::1 (my own LLA)

Client action:
  • Learns network portion from router: 2001:db8:acad:1::
  • GENERATES its own host portion (EUI-64 or random)
  • Must configure DNS manually!

Property: Stateless — no record of who got which IP
Use case: Home users
```

### 7.3 Method 2: SLAAC + Stateless DHCPv6

```
RA flags: Managed:"0"  Other:"1"

Router tells the client:
  • Learn network prefix and gateway from me
  • Generate your own host portion
  • Ask a DHCPv6 server for DNS and other info

Client action:
  1. Learns prefix and gateway from router
  2. Generates host portion itself
  3. Queries DHCPv6 server only for DNS

Property: Stateless — still no record of who got which IP
Use case: Home users (fully automatic, DNS included)
```

### 7.4 Method 3: Stateful DHCPv6

```
RA flags: Managed:"1"  Other:"0"

Router tells the client:
  • Get all your IP details from the DHCPv6 server
  • I'll only provide the gateway

Client action:
  1. Learns gateway from router
  2. Gets IP, prefix, DNS — everything — from DHCPv6 server
  3. DHCPv6 server RECORDS who received which address

Property: Stateful — full audit trail!
Use case: Enterprise networks
```

### 7.5 Three Methods Comparison

```
┌─────────────────┬─────────┬──────────────┬──────────────────┐
│ Feature         │ SLAAC   │ SLAAC+DHCP   │ Stateful DHCP    │
├─────────────────┼─────────┼──────────────┼──────────────────┤
│ IP source       │ Self    │ Self         │ DHCPv6           │
│ DNS source      │ Manual  │ DHCPv6       │ DHCPv6           │
│ Gateway source  │ Router  │ Router       │ Router           │
│ Record kept     │ No      │ No           │ Yes              │
│ RA Managed bit  │ 0       │ 0            │ 1                │
│ RA Other bit    │ 0       │ 1            │ 0                │
│ Typical use     │ Home    │ Home (auto)  │ Enterprise       │
└─────────────────┴─────────┴──────────────┴──────────────────┘
```

---

## 8. Interface ID Generation: EUI-64 vs Random

### 8.1 EUI-64 Method (No Longer Used on End-User Devices)

Derives a 64-bit Interface ID from the 48-bit MAC address:

```
MAC address: FC:99:47:75:CE:E0
                │
                ▼
Step 1: Split MAC into two 24-bit halves
  FC:99:47   |   75:CE:E0

Step 2: Insert FFFE in the middle
  FC:99:47 : FF:FE : 75:CE:E0

Step 3: Flip the 7th bit of the first byte (0→1 or 1→0)
  FC = 1111 1100
  7th bit = 0 → flip to 1
  FE = 1111 1110

Resulting Interface ID: FE:99:47:FF:FE:75:CE:E0
Compressed:              fe99:47ff:fe75:cee0
```

**Why it was abandoned:**
```
Problem: The host portion stays the same everywhere!
  Go home   → same host portion
  Go to work → same host portion
  Go to café → same host portion

Result: The person can be tracked!
  "User visited office at 09:00, café at 13:00..."
→ Deprecated since Windows 7 (Windows Vista era).
```

### 8.2 Randomly Generated Interface ID (Current Method)

```
The OS generates the host portion completely at random.

Collision probability: 1 / 2^64 = 1 / 18,446,744,073,709,551,616
→ Far less likely than winning a lottery!

DAD (Duplicate Address Detection) provides an extra safety net:
  Device sends an NS to its own chosen address
  Gets a reply? → Collision! Generate a new address.
  No reply?     → Address is unique, proceed!

Windows ipconfig output (randomly generated):
  IPv6 Address: 2001:db8:acad:1:50a5:8a35:a5bb:66e1
  Link-local  : fe80::50a5:8a35:a5bb:66e1
  Default GW  : fe80::1
```

---

## 9. Dynamic IPv6 LLA Addressing

Every IPv6 interface **must** have a LLA.

```
LLA generation flow:
  IPv6 interface is enabled
           │
           ▼
  Use fe80::/10 prefix
           │
           ▼
  Generate Interface ID → EUI-64 OR Random
           │
           ▼
  LLA is ready!
  Example: fe80::fc99:47ff:fe75:cee0 (EUI-64)
  Example: fe80::50a5:8a35:a5bb:66e1 (Random)
```

Verifying LLA on a Cisco router:
```bash
R1# show ipv6 interface brief
GigabitEthernet0/0/0 [up/up]
    FE80::7279:B3FF:FE92:3640     ← LLA (auto via EUI-64)
    2001:DB8:ACAD:1::1            ← GUA (statically assigned)
```

---

## 10. IPv6 Multicast Addresses

### 10.1 Multicast Range

```
ff00::/8 range → All IPv6 Multicast addresses

NOTE: Multicast addresses can ONLY be destination addresses, never source!
```

### 10.2 Well-Known Multicast Addresses

```
ff02::1  →  All-Nodes Multicast   = All IPv6 devices
            (Closest equivalent to IPv4's 255.255.255.255)

ff02::2  →  All-Routers Multicast = All IPv6 routers
            (RS messages go here: "Are there any routers?")
```

**IPv4 Broadcast vs IPv6 Multicast:**
```
IPv4 Broadcast (255.255.255.255):
  Switch cannot filter → goes to everyone → no control

IPv6 ff02::1 (All-Nodes Multicast):
  Switch CAN filter → admin can shape the traffic
  → IPv6 gives network admins more power!
```

### 10.3 Solicited-Node Multicast Address

Instead of sending a Neighbor Solicitation (ARP equivalent) to everyone, IPv6 sends it to a device-specific multicast address:

```
Address format:
  ff02::1:ff  +  [Last 6 hex digits of the target IP]

Example:
  GUA: 2001:DB8::00AB:0002/64
  Last 6 digits: AB:0002

  Solicited-Node Multicast: ff02::1:ffAB:0002

How it works:
  1. Device registers its Solicited-Node address with the switch
  2. NS (Neighbor Solicitation) is sent to this address
  3. Switch forwards only to the relevant device (not everyone!)
```

```
Flow diagram:
  A wants to ping B and needs B's MAC address
                │
                ▼
  A sends NS to "ff02::1:ffAB:0002"
                │
                ▼
         Switch receives packet
                │
         ┌──────┴──────┐
         ▼             ▼
     B receives    C does not
    (ff02::1:ffAB:0002  (C has a different
     belongs to B)       solicited-node address)
         │
         ▼
   B replies with NA, gives its MAC address to A
```

---

## 11. IPv6 Subnetting

### 11.1 IPv6 Subnetting Logic

No complex bit-level calculations needed! IPv6 was designed with subnetting in mind.

```
Typical enterprise allocation:
┌──────────────────────────────────────────────────────────────┐
│  ISP gives you /48:  2001:A98:8000::/48                       │
│                                                              │
│  ┌────────────────┬──────────┬──────────────────────┐        │
│  │  Global Route  │ Subnet   │    Interface ID       │        │
│  │    Prefix      │    ID    │                       │        │
│  │    48 bits     │  16 bits │       64 bits         │        │
│  │  (ISP gives)   │(You use) │  (Device generates)  │        │
│  └────────────────┴──────────┴──────────────────────┘        │
└──────────────────────────────────────────────────────────────┘

With 16-bit Subnet ID: 2^16 = 65,536 subnets of /64 each!
```

### 11.2 Creating Subnets — Example

```
ISP assigned: 2001:A98:8000::/48

You increment the Subnet ID field:
  Subnet 0  →  2001:A98:8000:0000::/64  (Engineering Faculty)
  Subnet 1  →  2001:A98:8000:0001::/64  (Science Faculty)
  Subnet 2  →  2001:A98:8000:0002::/64  (Architecture Faculty)
  ...
  Subnet FFFF → 2001:A98:8000:ffff::/64

Each subnet has 2^64 possible host addresses!
```

### 11.3 Configuring Multiple Subnets on a Cisco Router

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:2::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/1/0
R1(config-if)# ipv6 address 2001:db8:acad:3::1/64
R1(config-if)# no shutdown
```

---

## 12. Command Reference

### Cisco IOS Commands

```bash
# Assign IPv6 address
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64

# Manually assign LLA
R1(config-if)# ipv6 address fe80::1:1 link-local

# Enable IPv6 routing
R1(config)# ipv6 unicast-routing

# Show IPv6 interface summary
R1# show ipv6 interface brief

# Show IPv6 routing table
R1# show ipv6 route

# Show neighbor table (IPv6 equivalent of ARP table)
R1# show ipv6 neighbors
```

### Windows Commands

```powershell
# Show IPv6 address
ipconfig

# Show all IPv6 addresses (including LLA)
ipconfig /all

# IPv6 ping
ping ::1                           # loopback
ping 2001:db8:acad:1::10           # GUA ping
ping fe80::1%12                    # LLA ping (with zone ID)
```

---

## 13. Module Summary

```
┌──────────────────────────────────────────────────────────────────────┐
│                       MODULE 12 – SUMMARY                           │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Why IPv6?      → IPv4 exhausted; 128-bit = 340 undecillion addrs   │
│                                                                      │
│  Transition     → Dual Stack / Tunneling / NAT64 Translation         │
│                                                                      │
│  Format         → 8 hextets × 16 bits = 128 bits, hex, ":" sep      │
│  Rule 1         → Leading zeros can be omitted                       │
│  Rule 2         → :: replaces zero blocks (ONLY ONCE per address!)   │
│                                                                      │
│  GUA            → 2000::/3, internet-routable, like public IPv4     │
│  LLA            → fe80::/10, same link only, cannot cross router     │
│  ULA            → fc00::/7, isolated enterprise networks             │
│  Loopback       → ::1/128, self-ping / local testing                 │
│  Multicast      → ff00::/8, no broadcast, multicast used instead     │
│                                                                      │
│  Prefix         → /64 replaces subnet mask                           │
│  Interface ID   → Host portion (64 bits), EUI-64 or random          │
│                                                                      │
│  SLAAC          → Self-generates IP, DNS manual — stateless          │
│  SLAAC+DHCP     → Self-generates IP, DNS from DHCPv6 — stateless    │
│  Stateful DHCP  → Everything from DHCPv6, records kept              │
│                                                                      │
│  EUI-64         → Interface ID from MAC address (deprecated)        │
│  Random         → Default since Windows Vista, privacy-preserving   │
│  DAD            → Duplicate Address Detection, prevents conflicts    │
│                                                                      │
│  ff02::1        → All-nodes multicast (all IPv6 devices)            │
│  ff02::2        → All-routers multicast (all routers)               │
│  Solicited-Node → ff02::1:ff + last 6 hex (optimized ARP)          │
│                                                                      │
│  Subnetting     → /48 prefix + 16-bit Subnet ID = /64 → simple!    │
│  65,536 subnets → possible with 16-bit Subnet ID                    │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Key Rules

> 1. **No Broadcast in IPv6** — `ff02::1` (all-nodes multicast) is used instead.
> 2. **No NAT in IPv6** — Every device gets its own real global address.
> 3. **LLA is mandatory** — Every IPv6 interface must have an `fe80::` address.
> 4. **`::` can only appear once** — Otherwise the address becomes ambiguous.
> 5. **Leading zeros can be omitted; trailing zeros cannot.**
> 6. **Recommended prefix: /64** — First 64 bits network, last 64 bits host.
> 7. **Device sends RS first** — Discovers the router, then obtains an IP address.
> 8. **EUI-64 is no longer used on end-user devices** — Random generation is preferred for privacy.

---

*CCNA1 – Introduction to Networks v7.0 | Module 12: IPv6 Addressing*
