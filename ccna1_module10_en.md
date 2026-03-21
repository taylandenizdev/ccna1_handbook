# CCNA1 – Module 10: Basic Router Configuration
> CCNA Introduction to Networks v7.0 (ITN) | Gökhan AKIN & Ozan BÜK – CCIE

---

## 📋 Module Objective
Apply **initial settings** on routers and end devices.

| Topic | Objective |
|-------|-----------|
| Router Initial Configuration | Configure initial settings on a Cisco IOS router |
| Configure Interfaces | Configure two active interfaces on a Cisco IOS router |
| Default Gateway Configuration | Configure devices to use a default gateway |

---

## 10.1 Router Initial Configuration

### Key Difference: Switch vs Router

```
┌──────────────────────────────────────────────────────────────┐
│                  SWITCH vs ROUTER                            │
├─────────────────────────────┬────────────────────────────────┤
│           SWITCH            │            ROUTER              │
├─────────────────────────────┼────────────────────────────────┤
│ • Layer 2 (MAC address)     │ • Layer 3 (IP address)         │
│ • Switching within one LAN  │ • Routing between networks     │
│ • Physical ports: NO direct │ • Physical ports: IP CAN be    │
│   IP → use SVI (VLAN 1)     │   assigned directly            │
│ • Not a gateway for PCs     │ • Serves as default gateway    │
│ • Ports: Layer 2 by default │ • Ports: Layer 3 interfaces    │
└─────────────────────────────┴────────────────────────────────┘
```

### Why Can Router Ports Receive IPs Directly?

```
PC (192.168.1.10)
        │
        ▼
    SWITCH (Layer 2 – only MAC-based)
        │
        ▼
 ROUTER G0/0/0 (192.168.1.1) ← This IP is the PC's default gateway!
        │
        ▼
   Internet / Other networks
```

Every physical port on a router is a separate **Layer 3 interface** → IPs can be assigned directly.

---

### Basic Router Configuration Steps

The basic configuration done on a switch (hostname, passwords, banner, save) uses **the exact same commands on a router**:

```bash
Router(config)# hostname hostname
Router(config)# enable secret password
Router(config)# line console 0
Router(config-line)# password password
Router(config-line)# login
Router(config)# line vty 0 4
Router(config-line)# password password
Router(config-line)# login
Router(config-line)# transport input {ssh | telnet}
Router(config)# service password-encryption
Router(config)# banner motd # message #
Router(config)# end
Router# copy running-config startup-config
```

> 📝 **Router uses VTY 0 4** (switch uses 0 15). This allows 5 simultaneous connections.
> `transport input ssh telnet` permits both SSH and Telnet connections.

---

### Full Configuration Example – R1

```bash
Router> enable
Router# configure terminal

! Set hostname
Router(config)# hostname R1

! Enable password
R1(config)# enable secret class

! Console password
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login

! VTY (SSH/Telnet) password
R1(config-line)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

! Encrypt all passwords
R1(config)# service password-encryption

! Warning banner
R1(config)# banner motd #
***********************************************
WARNING: Unauthorized access is prohibited!
***********************************************
#

! Save configuration
R1(config)# exit
R1# copy running-config startup-config
```

---

## 10.2 Configure Interfaces

### Router Interface Types

```
┌─────────────────────────────────────────────────────────┐
│           R1 - Cisco Router Interfaces                  │
│                                                         │
│  [GE0/0/0] ──────── Connected to LAN (192.168.10.0/24)  │
│   (Gigabit │                                            │
│   Ethernet)│                                            │
│            │                                            │
│  [GE0/0/1] ──────── Connected to WAN/Internet           │
│            │        (209.165.200.224/30)                │
│            │                                            │
│  [Serial0] ──────── Serial WAN connection               │
└─────────────────────────────────────────────────────────┘
```

### Router Interface Configuration Commands

```bash
Router(config)# interface type-and-number
Router(config-if)# description description-text
Router(config-if)# ip address ipv4-address subnet-mask
Router(config-if)# ipv6 address ipv6-address/prefix-length
Router(config-if)# no shutdown
```

**Important Notes:**
- `description` → Optional but highly recommended (documents what's connected)
- `no shutdown` → **Mandatory!** Router interfaces are shut down by default
- Layer 3 interfaces (router) → Default is shutdown
- Layer 2 ports (switch) → Default is no shutdown (open)

---

### Configuration Example – Topology

```
                    Internet
                       │
        ┌──────────────┤
        │         G0/0/1│.225
   PC1  │               │              PC2
.10 │   │          [R1] │  .226 [R2] .1
────┤[SW]├──G0/0/0──────┤─────────────────── .10
    │   │  .1      .1   │
    │   │               │
 192.168.10.0/24   209.165.200.224/30   10.1.1.0/24

IPv6:
2001:db8:acad:10::/64  |  2001:db8:feed:224::/64  |  2001:db8:cafe:1::/64
```

### G0/0/0 Configuration (LAN Side)

```bash
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Output when link comes up:**
```
*Aug 1 01:43:53.435: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to down
*Aug 1 01:43:56.447: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to up
*Aug 1 01:43:57.447: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
```

### G0/0/1 Configuration (WAN Side)

```bash
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description Link to R2
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

---

### Understanding Interface Status Messages

```
Two layers determine interface status:

Layer 1 (Physical):  up / down / administratively down
Layer 2 (Protocol):  up / down

┌──────────────────┬─────────────┬───────────────────────────────────┐
│ Status (L1)      │ Protocol(L2)│ Meaning                           │
├──────────────────┼─────────────┼───────────────────────────────────┤
│ up               │ up          │ ✅ Everything normal, working      │
│ up               │ down        │ ⚠️ Encapsulation or L2 problem    │
│ down             │ down        │ 🔴 No physical connection          │
│ admin down       │ down        │ 🔴 shutdown command was applied    │
└──────────────────┴─────────────┴───────────────────────────────────┘
```

---

### Interface Verification Commands

#### show ip interface brief

```bash
R1# show ip interface brief
```

```
Interface              IP-Address      OK? Method  Status               Protocol
GigabitEthernet0/0/0   192.168.10.1    YES manual  up                   up
GigabitEthernet0/0/1   209.165.200.225 YES manual  up                   up
Vlan1                  unassigned      YES unset   administratively down down
```

#### show ipv6 interface brief

```bash
R1# show ipv6 interface brief
```

```
GigabitEthernet0/0/0 [up/up]
  FE80::201:C9FF:FE89:4501
  2001:DB8:ACAD:10::1
GigabitEthernet0/0/1 [up/up]
  FE80::201:C9FF:FE89:4502
  2001:DB8:FEED:224::1
```

#### show ip route (Routing Table)

```bash
R1# show ip route
```

```
Gateway of last resort is not set

      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L        192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
      209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
C        209.165.200.224/30 is directly connected, GigabitEthernet0/0/1
L        209.165.200.225/32 is directly connected, GigabitEthernet0/0/1
```

**Routing table code legend:**

| Code | Meaning |
|------|---------|
| `C` | Connected – Directly connected network |
| `L` | Local – Interface's own IP address (/32) |
| `S` | Static – Manually configured route |
| `O` | OSPF – Learned via OSPF protocol |
| `R` | RIP – Learned via RIP protocol |

#### show interfaces (Detailed Statistics)

```bash
R1# show interfaces gigabitEthernet 0/0/0
```

```
GigabitEthernet0/0/0 is up, line protocol is up
  Hardware is ISR4321-2x1GE, address is a0e0.af0d.e140
  Description: Link to LAN
  Internet address is 192.168.10.1/24
  MTU 1500 bytes, BW 100000 Kbit/sec
  ...
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
  1180 packets input, 109486 bytes, 0 no buffer
  0 runts, 0 giants, 0 throttles
  0 input errors, 0 CRC, 0 frame
  ...
```

**Important counters to monitor:**

| Counter | Meaning | High Value = Problem? |
|---------|---------|----------------------|
| `CRC` | Corrupted frames (cable/hardware) | ✅ Yes |
| `runts` | Frames too short | ✅ Yes |
| `giants` | Frames too long | ✅ Yes |
| `input errors` | Total input errors | ✅ Yes |
| `collisions` | Collisions (old hub networks) | ✅ Yes |

---

### Verification Commands Summary

| Command | Function |
|---------|---------|
| `show ip interface brief` | IPv4 summary of all interfaces (IP + status) |
| `show ipv6 interface brief` | IPv6 summary of all interfaces |
| `show ip route` | IPv4 routing table |
| `show ipv6 route` | IPv6 routing table |
| `show interfaces` | Detailed statistics for all interfaces (L1/L2) |
| `show ip interface` | IPv4 statistics for router interfaces |
| `show ipv6 interface` | IPv6 statistics for router interfaces |

---

## 10.3 Default Gateway Configuration

### What is a Default Gateway?

```
192.168.10.0/24 network               192.168.11.0/24 network
       │                                       │
 ┌─────┴──────┐    ┌──────────────┐    ┌───────┴─────┐
 │    PC1     │    │     R1       │    │    PC3      │
 │ 192.168.   │───►│ G0/0/0: .1  │───►│ 192.168.    │
 │ 10.10      │    │ G0/0/1: .1  │    │ 11.10       │
 └────────────┘    └──────────────┘    └─────────────┘
        │
   PC1 wants to send a packet to another network:
   1. Destination IP: 192.168.11.10 (different network!)
   2. Send packet to default gateway (192.168.10.1)
   3. Router forwards packet to the correct network
```

**Rule:** The host's IP address and the router interface's IP address must be **on the same network**!

### Default Gateway on a Host (PC)

| Configuration | Method |
|--------------|--------|
| **Manual** | Enter the router's IP in the "Default Gateway" field of IP settings |
| **Automatic (DHCP)** | DHCP server automatically provides it |

### Default Gateway on a Switch

The switch needs a default gateway to be remotely managed (from another network via SSH/Telnet):

```bash
Switch# configure terminal
Switch(config)# ip default-gateway 192.168.1.1
Switch(config)# end
```

```
┌─────────────────────────────────────────────────────────┐
│  Why Does a Switch Need a Default Gateway?              │
│                                                         │
│  You want to SSH to the switch from another network     │
│  (e.g., 10.0.0.0/8). The switch needs a default        │
│  gateway to know where to send return traffic.          │
│                                                         │
│  [Mgmt PC] ──► [Router] ──► [Switch SVI 192.168.1.20]  │
│  10.0.0.5       192.168.1.1     ↑                       │
│                 Gateway         |                       │
│                            Switch needs to              │
│                            know this gateway            │
└─────────────────────────────────────────────────────────┘
```

---

## 📝 Complete Router Configuration Summary (Cheat Sheet)

```bash
! ═══════════════════════════════════════════
! BASIC ROUTER CONFIGURATION (R1 Example)
! ═══════════════════════════════════════════

! 1. Enter Privileged mode
Router> enable

! 2. Enter Configuration mode
Router# configure terminal

! ── INITIAL SETTINGS ───────────────────────

! 3. Set hostname
Router(config)# hostname R1

! 4. Enable password
R1(config)# enable secret SecurePass1!

! 5. Console password
R1(config)# line console 0
R1(config-line)# password ConsoleP1!
R1(config-line)# login
R1(config-line)# exit

! 6. VTY password (SSH/Telnet)
R1(config)# line vty 0 4
R1(config-line)# password VTYPass1!
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

! 7. Encrypt all passwords
R1(config)# service password-encryption

! 8. Banner
R1(config)# banner motd #
Authorized access only. Unauthorized access is prohibited!
#

! ── INTERFACE CONFIGURATION ─────────────────

! 9. LAN interface (G0/0/0)
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

! 10. WAN interface (G0/0/1)
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description Link to R2
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

! ── SAVE ────────────────────────────────────

! 11. Return to Privileged mode and save
R1(config)# end
R1# copy running-config startup-config
```

---

## 🔍 Quick Verification Flow

```
Router configured?
        │
        ▼
show ip interface brief
        │
    ┌───┴───┐
    │ up/up │─── Yes ──► Continue
    └───────┘
        │
      down
        │
        ▼
  Did you apply
  no shutdown?
        │
   ─── No ──► interface X → no shutdown
        │
      Yes
        │
        ▼
  Is cable connected?
  Is other device on?
        │
   Troubleshoot
```

---

## 🔑 Key Reminders

- **Router ports default to shutdown** → `no shutdown` is always required
- **Switch ports default to open** → `no shutdown` rarely needed
- **Switch SVI** → `interface vlan 1` for IP (virtual interface)
- **Router physical port** → `interface gigabitEthernet 0/0/X` for IP (physical)
- **`description`** → Optional but invaluable for maintenance
- **Default Gateway** → Required for end devices and switches; routers use routing instead
- **`C`** in routing table = Directly Connected network
- **`L`** in routing table = Local address (/32 host route for the interface itself)
- **show interfaces** → Shows Layer 1/2 statistics including error counters
- **`transport input ssh`** → Allows only SSH (blocks Telnet – more secure)

---

*CCNA1 ITN v7.0 – Module 10 | agyoneticileri.org*
