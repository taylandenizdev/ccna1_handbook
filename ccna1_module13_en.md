# CCNA1 - Module 13: ICMP (Internet Control Message Protocol)
> **Source:** Cisco Networking Academy — Introduction to Networks v7.0 (ITN)  
> **Instructors:** Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 Module Objectives

| Topic | Objective |
|-------|-----------|
| ICMP Messages | Explain how ICMP is used to test network connectivity |
| Ping and Traceroute Tests | Use ping and traceroute utilities to test network connectivity |

---

## 13.1 ICMP Messages

### What is ICMP?

**ICMP (Internet Control Message Protocol)** is a **Layer 3 (Network Layer) protocol** that provides feedback about problems related to the processing of IP packets under certain conditions.

- **ICMPv4** → Messaging protocol for IPv4
- **ICMPv6** → Messaging protocol for IPv6 (includes additional functions)

> ⚠️ **Important:** ICMPv4 messages are **not required** and are often blocked within networks for security reasons.

---

### ICMP Packet Structure

ICMP is encapsulated directly into the IP packet — there is **no Layer 4 header** (TCP/UDP).

```
┌──────────────────────────────────────────────────────────────┐
│  Ethernet Header │  IP Header   │  ICMP Header  │   FCS     │
│   (Layer 2)      │  (Layer 3)   │  (Layer 3)    │           │
└──────────────────────────────────────────────────────────────┘

ICMP Header Structure:
┌──────────┬──────────┬──────────────────────┐
│   TYPE   │   CODE   │      CHECKSUM        │
├──────────┴──────────┴──────────────────────┤
│                  UNUSED                    │
├────────────────────────────────────────────┤
│                   DATA                     │
└────────────────────────────────────────────┘
```

> IP header **Protocol field = 1** → ICMP packet  
> IP header **Protocol field = 6** → TCP packet  
> IP header **Protocol field = 17** → UDP packet  
> IPv6 header **Next Header = 58** → ICMPv6 packet

---

### Common ICMP Message Types (ICMPv4 and ICMPv6)

| Message Type | Description |
|--------------|-------------|
| **Host Reachability** | Tests whether a destination device is reachable (ping) |
| **Destination/Service Unreachable** | Cannot reach the destination host or service |
| **Time Exceeded** | TTL (or Hop Limit) reached zero |

---

### ICMP Type and Code Values (ICMPv4)

| Type | Code | Description |
|------|------|-------------|
| **0** | 0 | Echo Reply (response to a ping) |
| **3** | 0 | Destination Network Unreachable |
| **3** | 1 | Destination Host Unreachable |
| **3** | 2 | Destination Protocol Unreachable |
| **3** | 3 | Destination Port Unreachable |
| **3** | 4 | Fragmentation Needed, DF flag set |
| **3** | 5 | Source Route Failed |
| **5** | 0 | Redirect Datagram (Network) |
| **5** | 1 | Redirect Datagram (Host) |
| **5** | 2 | Redirect Datagram (ToS + Network) |
| **5** | 3 | Redirect Datagram (ToS + Host) |
| **8** | 0 | Echo Request (ping request) |
| **9** | 0 | Router Advertisement |
| **10** | 0 | Router Solicitation |
| **11** | 0 | Time Exceeded (TTL expired in transit) |
| **11** | 1 | Time Exceeded (Fragment reassembly time exceeded) |
| **12** | 0 | Parameter Problem (Pointer indicates error) |
| **13** | 0 | Timestamp |
| **14** | 0 | Timestamp Reply |

---

### 1. Host Reachability (Echo Request / Echo Reply)

The **ping** command uses ICMP Echo messages to test whether a destination device is reachable.

```
┌──────────┐    Echo Request (Type 8, Code 0)    ┌──────────┐
│          │  ──────────────────────────────────► │          │
│  Host A  │                                      │  Host B  │
│          │  ◄──────────────────────────────────  │          │
└──────────┘    Echo Reply   (Type 0, Code 0)    └──────────┘
```

**Sample ping output:**
```
C:\>ping 208.67.220.220

Pinging 208.67.220.220 with 32 bytes of data:
Reply from 208.67.220.220: bytes=32 time=14ms TTL=56
Reply from 208.67.220.220: bytes=32 time=17ms TTL=56
Reply from 208.67.220.220: bytes=32 time=13ms TTL=56
Reply from 208.67.220.220: bytes=32 time=11ms TTL=56

Ping statistics for 208.67.220.220:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Approximate round trip times in milli-seconds:
    Minimum = 11ms, Maximum = 17ms, Average = 13ms
```

**Windows Ping Parameters:**

| Parameter | Description |
|-----------|-------------|
| `-t` | Ping continuously until stopped (Ctrl+C) |
| `-l <size>` | Specify packet size (default 32 bytes) |
| `-w <ms>` | Specify wait time in ms (default 2000ms) |
| `-n <count>` | Specify number of packets to send |

---

### 2. Destination Unreachable (Type 3)

When a router or destination server fails to forward a packet, it sends an ICMP Type 3 message back to the source.

```
┌──────────┐   Packet ──►  ┌──────────┐        ┌──────────┐
│          │               │          │   ✗     │          │
│  Host A  │               │  Router  │ ──────► │  Server  │
│          │               │          │         │          │
└──────────┘               └──────────┘         └──────────┘
      ▲                         │
      │   Type 3 (Destination   │
      └── Unreachable) ◄────────┘
```

**ICMPv4 — Destination Unreachable Codes (Type 3):**

| Code | Description | Sent By |
|------|-------------|---------|
| 0 | Net Unreachable | Router (no routing table entry) |
| 1 | Host Unreachable | Router (no ARP reply from host) |
| 2 | Protocol Unreachable | Server (unknown protocol) |
| 3 | Port Unreachable | Server (port is closed) |

**ICMPv6 — Destination Unreachable Codes (Type 1):**

| Code | Description |
|------|-------------|
| 0 | No Route to Destination |
| 1 | Administratively Prohibited (Firewall) |
| 2 | Beyond Scope of Source Address |
| 3 | Address Unreachable |
| 4 | Port Unreachable |

---

### 3. Time Exceeded (Type 11)

When the TTL (Time To Live) value of a packet reaches 0, the router discards the packet and sends an ICMP **Type 11** message back to the source.

```
PC ──(TTL=64)──► R1 ──(TTL=63)──► R2 ──(TTL=62)──► ...
                                          │
                               When TTL=0:
                                          │
                               Type 11 back to source ◄─────
```

**Routing Loop Scenario:**
```
        ┌─────────────────────────────────┐
        │                                 │
PC ──► R1 ──(TTL=64)──► R2 ──► R1 ──► R2 ──► ...
                                (Misconfigured routing)
                                          │
                               TTL=0, Type 11 message:
                               "TTL expired in transit"
```

> 📝 **Note:** Time Exceeded messages are critical to how the **traceroute** utility works.

---

### 4. Redirect Message (Type 5)

Used by a router to redirect traffic to a better path.

```
         ┌──── Better path available! ────┐
         │       (Type 5 Redirect)        │
         ▼                                │
┌──────────┐          ┌──────────┐   │   ┌──────────┐
│          │ ─packet─► │Default  │───┘   │   GW2    │
│    PC    │           │  GW1    │ ──────►│          │
└──────────┘          └──────────┘       └──────────┘
```

---

## ICMPv6 — Neighbor Discovery Protocol (NDP)

ICMPv6 also handles what ARP does in IPv4. IPv6 does **not use broadcast**; instead it uses **multicast**.

### ICMPv6 NDP Message Types

| Type | Name | Abbr. | Function |
|------|------|-------|----------|
| 133 | Router Solicitation | RS | Device asks router: "How do I get IP configuration?" |
| 134 | Router Advertisement | RA | Router sends prefix and gateway info to devices |
| 135 | Neighbor Solicitation | NS | "What is the MAC address for this IPv6 address?" (like ARP Request) |
| 136 | Neighbor Advertisement | NA | "My MAC address is..." (like ARP Reply) |

---

### ICMPv6 — IPv6 Dynamic Address Assignment Flow

```
┌────────────────────────────────────────────────────────────────────┐
│              IPv6 Automatic Address Configuration Process          │
│                                                                    │
│  PC Powers On                                                      │
│      │                                                             │
│      ▼                                                             │
│  [Send RS Message] ──Type 133──► Router                           │
│  "How do I get my IP configuration?"                               │
│      │                                                             │
│      ▼                                                             │
│  [Receive RA Message] ◄──Type 134── Router                        │
│  PREFIX + GW information received                                  │
│      │                                                             │
│      ▼                                                             │
│  Check Managed/Other bits in RA:                                   │
│      │                                                             │
│      ├── M=0, O=0 ──► SLAAC                                       │
│      │               (Self-generate IP, enter DNS statically)     │
│      │                                                             │
│      ├── M=0, O=1 ──► SLAAC + DHCPv6                             │
│      │               (Self-generate IP, get DNS from DHCPv6)     │
│      │                                                             │
│      └── M=1, O=0 ──► Stateful DHCPv6                            │
│                        (Get everything from DHCPv6)               │
└────────────────────────────────────────────────────────────────────┘
```

---

### SLAAC (Stateless Address AutoConfiguration)

The router sends RA messages every **200 seconds**. To skip the wait, a PC can send an RS message immediately upon startup.

**RA Message Contents:**
```
RA Message (Type 134):
├── PREFIX:  2001:db8:acad:1::/64
├── Gateway: FE80::1  (Link-Local address)
├── Managed: 0 or 1
└── Other:   0 or 1
```

**SLAAC IP Generation:**
```
Network portion (from Router):   2001:db8:acad:1::
                                          +
Interface ID (self-generated):   xxxx:xxxx:xxxx:xxxx
                                          =
Full IPv6 Address:               2001:db8:acad:1:xxxx:xxxx:xxxx:xxxx/64
```

---

### Neighbor Solicitation / Advertisement (IPv6 ARP Equivalent)

```
                NS (Type 135)
R1 ─────────────────────────────────────────► PC1
"What is the MAC address for 2001:db8:acad:1::10?"

                NA (Type 136)
R1 ◄─────────────────────────────────────────  PC1
"My MAC address is: 00:aa:bb:cc:dd:ee"
```

**Solicited-Node Multicast Address:**
```
FF02:0000:0000:0000:0000:0001:FF??:???? 
                                   └── Last 24 bits of the IPv6 address
```

---

### Duplicate Address Detection (DAD)

Before using a newly assigned IP, a device checks whether the address is already in use on the network.

**In IPv4:**
```
PC ──[ARP Request]──► "Is anyone using 192.168.1.10?"
   ◄──[ARP Reply]────  If yes: IP Conflict warning!
   ──[Silence]─────    If no: IP is used
```

**In IPv6 (DAD):**
```
PC ──[NS, Type 135]──► "Is anyone using 2001:db8::10?"
   ◄──[NA, Type 136]──  If yes: Cannot use this IP!
   ──[Silence]─────     If no: IP is used
```

> 📝 **Note:** DAD is not required, but RFC 4861 recommends performing DAD on unicast addresses.

---

## 13.2 Ping and Traceroute Tests

### Ping — Connectivity Test

**Step-by-Step Connectivity Troubleshooting Methodology:**

```
┌────────────────────────────────────────────────────────────────┐
│           Systematic Network Troubleshooting Steps             │
│                                                                │
│  STEP 1: Loopback Test                                         │
│  ─────────────────────                                         │
│  ping 127.0.0.1 (IPv4) or ping ::1 (IPv6)                     │
│  ✓ Success → TCP/IP stack is working                           │
│  ✗ Failure → TCP/IP installation issue                        │
│                    │                                           │
│                    ▼                                           │
│  STEP 2: Own IP Address                                        │
│  ───────────────────────                                       │
│  ping <your_own_ip_address>                                    │
│  ✓ Success → NIC working, IP configuration correct            │
│                    │                                           │
│                    ▼                                           │
│  STEP 3: Default Gateway                                       │
│  ──────────────────────                                        │
│  ping <default_gateway_ip>                                     │
│  ✓ Success → Local network connection is good                  │
│  ✗ Failure → Cable / switch / router issue                    │
│                    │                                           │
│                    ▼                                           │
│  STEP 4: Remote Host                                           │
│  ────────────────────                                          │
│  ping 8.8.8.8 (or any ping-responding IP)                     │
│  ✓ Success → Internet connectivity is working                  │
│  ✗ Failure → ISP connection issue                             │
└────────────────────────────────────────────────────────────────┘
```

---

### Interpreting Ping Output

**Windows/Linux PC:**
```
Response Types:
  Reply from x.x.x.x: bytes=32 time=14ms → ✅ Success
  Request timed out.                       → ❌ No response (waited 2s)
  Destination host unreachable.            → ❌ Received ICMP Type 3
  TTL expired in transit.                  → ❌ Received ICMP Type 11
```

**Cisco Router/Switch:**
```
S1#ping 192.168.20.2
Sending 5, 100-byte ICMP Echos to 192.168.20.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/1 ms

Symbols:
  !  → Echo reply received (success)
  .  → Timeout
  U  → Destination Unreachable
```

> ⚠️ **First packet timeout is normal!** This occurs because ARP (IPv4) or ND (IPv6) address resolution must happen before the echo request is sent. This process may exceed the first packet's 2-second window.

---

### Loopback Ping

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│   C:\>ping 127.0.0.1    →  IPv4 Loopback test       │
│   C:\>ping ::1          →  IPv6 Loopback test       │
│                                                     │
│   Success  → TCP/IP protocol stack is working       │
│   Failure  → Check TCP/IP installation              │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### Traceroute — Path Test

**How Traceroute Works:**

```
┌────────────────────────────────────────────────────────────────────┐
│                    Traceroute Working Principle                    │
│                                                                    │
│  Round 1: Send TTL=1                                               │
│  PC ──(TTL=1)──► R1                                               │
│                  R1: TTL=0, sends "Time Exceeded" (Type 11)       │
│                  PC: Learned R1's IP address ✓                    │
│                                                                    │
│  Round 2: Send TTL=2                                               │
│  PC ──(TTL=2)──► R1 ──(TTL=1)──► R2                              │
│                                   R2: TTL=0, sends Type 11        │
│                                   PC: Learned R2's IP address ✓   │
│                                                                    │
│  Round 3: Send TTL=3                                               │
│  PC ──(TTL=3)──► R1 ──(TTL=2)──► R2 ──(TTL=1)──► R3             │
│                                                    sends Type 11   │
│                                                                    │
│  Final Round: Destination responds                                 │
│  Dest: "Port Unreachable" (Type 3, Code 3) → Trace complete      │
└────────────────────────────────────────────────────────────────────┘
```

**3 packets are sent per hop**, measuring round-trip time:
```
tracert -d 8.8.8.8

  1     7 ms    5 ms    2 ms  192.168.0.1
  2       *       *       *   Request timed out.
  3    12 ms   10 ms   10 ms  172.25.34.29
  4    14 ms   15 ms   14 ms  10.59.10.109
  ...
 13    64 ms   69 ms   68 ms  8.8.8.8
```

| Symbol | Meaning |
|--------|---------|
| `ms` values | Round-Trip Time per hop |
| `*` | No response (router may be filtering ICMP) |
| `Request timed out` | None of the 3 packets received a reply |

**Traceroute Commands:**

| Platform | Command |
|----------|---------|
| Windows | `tracert <ip_address>` |
| Windows (no DNS resolution) | `tracert -d <ip_address>` |
| Cisco IOS (Router) | `traceroute <ip_address>` |
| Linux/macOS | `traceroute <ip_address>` |

---

### Routing Loop Detection

```
Traceroute output showing a loop:

  1    1 ms   192.168.1.1
  2    2 ms   10.0.0.1
  3    3 ms   10.0.0.2
  4    4 ms   10.0.0.1    ← Repeated!
  5    5 ms   10.0.0.2    ← Loop detected!
  6    6 ms   10.0.0.1
  ...
 30    *    *    *         (TTL=30 reached, packet dropped)
```

---

## 📊 Quick Reference Tables

### ICMP vs ICMPv6 Comparison

| Feature | ICMPv4 | ICMPv6 |
|---------|--------|--------|
| Echo Request | Type 8, Code 0 | Type 128, Code 0 |
| Echo Reply | Type 0, Code 0 | Type 129, Code 0 |
| Dest. Unreachable | Type 3 | Type 1 |
| Time Exceeded | Type 11 | Type 3 |
| Router Solicitation | ✗ (N/A) | Type 133 |
| Router Advertisement | ✗ (N/A) | Type 134 |
| Neighbor Solicitation | ✗ (uses ARP) | Type 135 |
| Neighbor Advertisement | ✗ (uses ARP) | Type 136 |
| Loopback Address | 127.0.0.1 | ::1 |

---

### Network Troubleshooting Tools Summary

| Tool | Command | Function |
|------|---------|----------|
| **ipconfig** | `ipconfig /all` | Show IP configuration |
| **Ping (loopback)** | `ping 127.0.0.1` | Is TCP/IP stack working? |
| **Ping (gateway)** | `ping <gateway_ip>` | Is local network reachable? |
| **Ping (remote)** | `ping 8.8.8.8` | Is the internet reachable? |
| **Traceroute** | `tracert <ip>` | Which path does the packet take? |
| **NSLookup** | `nslookup <domain>` | Is DNS working? |
| **Netstat** | `netstat` | Open ports and connections |

---

## 🧠 Key Takeaways

1. **ICMP is a Layer 3 protocol** — no port numbers, no TCP/UDP header.
2. **No ping reply ≠ device is down!** A firewall or security policy may be blocking ICMP.
3. **First ping packet may time out** — This is normal due to ARP/ND resolution time.
4. **Traceroute starts TTL at 1 and increments** — It learns each router's IP address one hop at a time.
5. **IPv6 does not use ARP** — ICMPv6 Neighbor Solicitation/Advertisement replaces it.
6. **SLAAC eliminates the need for static IP** — The router sends the prefix via RA; the device generates its own IP.
7. **DAD (Duplicate Address Detection)** — Checks whether the new IP is already in use before assigning it.

---

## 📝 Module Summary

```
ICMP
├── ICMPv4
│   ├── Echo Request/Reply       (Ping → Type 8/0)
│   ├── Destination Unreachable  (Type 3)
│   │   ├── Code 0: Net Unreachable
│   │   ├── Code 1: Host Unreachable
│   │   ├── Code 2: Protocol Unreachable
│   │   └── Code 3: Port Unreachable
│   ├── Time Exceeded            (Type 11 → used by Traceroute)
│   └── Redirect                 (Type 5)
│
└── ICMPv6
    ├── Echo Request/Reply       (Ping → Type 128/129)
    ├── Destination Unreachable  (Type 1)
    ├── Time Exceeded            (Type 3)
    └── Neighbor Discovery Protocol (NDP)
        ├── Router Solicitation    (Type 133) ─── Device → Router
        ├── Router Advertisement   (Type 134) ─── Router → Device
        ├── Neighbor Solicitation  (Type 135) ─── replaces ARP Request
        └── Neighbor Advertisement (Type 136) ─── replaces ARP Reply
```

---

*CCNA1 Module 13 Handbook — agyoneticileri.org*
