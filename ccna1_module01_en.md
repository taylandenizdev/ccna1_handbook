# 🌐 Network Fundamentals Handbook



---

## Table of Contents

- [1. What is a Network?](#1-what-is-a-network)
- [2. Client-Server and Peer-to-Peer Models](#2-client-server-and-peer-to-peer-models)
- [3. Network Components](#3-network-components)
- [4. Network Types](#4-network-types)
- [5. Topology Diagrams](#5-topology-diagrams)
- [6. ISP, Intranet, Extranet](#6-isp-intranet-extranet)
- [7. Reliable Network Criteria](#7-reliable-network-criteria)
- [8. Modern Trends](#8-modern-trends)
- [9. Internet Access Technologies](#9-internet-access-technologies)
- [10. Network Security Fundamentals](#10-network-security-fundamentals)
- [11. CIA Triad](#11-cia-triad)
- [12. CCNA Certification Path](#12-ccna-certification-path)
- [13. Glossary](#13-glossary)

---

## 1. What is a Network?

> **A network is any environment where two or more devices communicate with each other.**

Scale doesn't matter — the operating principles are exactly the same:

| Network Type | Example |
|-------------|---------|
| Home network | 2–5 devices |
| Small office / Home office (SOHO) | 10–50 devices |
| Campus | Multi-building |
| Enterprise | Worldwide branches |

**Internet growth milestones:**

| Era | Driver | Connected devices |
|-----|--------|------------------|
| Fixed computing | Desktop PCs | 200 million |
| Mobility / BYOD | Laptops, smartphones | 10 billion |
| Internet of Things | Smart devices, sensors | 30 billion (2020) |
| Internet of Everything | People, process, data, things | ~50 billion (2026 est.) |

---

## 2. Client-Server and Peer-to-Peer Models

### 2.1 Client-Server

| Role | Definition | Example |
|------|-----------|---------|
| **Server** | Provides a service via server software | Web server, file server, email server |
| **Client** | Consumes a service | Browser, desktop application |

Each service requires its own server software. A host providing web services must run web server software.

### 2.2 Peer-to-Peer (P2P)

Every device can act as both a server and a client simultaneously.

| | Advantages | Disadvantages |
|-|-----------|--------------|
| | Easy to set up | No centralised administration |
| | Less complex | Less secure |
| | Lower cost | Not scalable |
| | Good for simple tasks (file/printer sharing) | Slower performance |

> ⚠️ Being a server = accepting inbound traffic = security risk + CPU/RAM/Disk consumption. Large organisations typically **disable** P2P services.

---

## 3. Network Components

Every network — from a home setup to the global internet — is built from three categories:

```
┌─────────────────┐   ┌──────────────────────┐   ┌──────────────────┐
│   End Devices   │   │ Intermediate Devices │   │      Media       │
│                 │   │                      │   │                  │
│ Desktop/Laptop  │   │ Switch (wired LAN)   │   │ Copper (UTP)     │
│ Server          │   │ Access Point (WiFi)  │   │ Fiber optic      │
│ IP Phone        │   │ Router (WAN/internet)│   │ Wireless         │
│ IP Camera       │   │ Firewall             │   │                  │
│ Printer         │   │ Multilayer switch    │   │                  │
│ IoT device      │   │ Modem                │   │                  │
│ (NIC inside all)│   │                      │   │                  │
└─────────────────┘   └──────────────────────┘   └──────────────────┘
```

---

### 3.1 End Device (Host)

> The device where a packet originates or terminates. Also called a **host**.

**Examples:**
- Desktop / Laptop / Server
- IP Phone & IP Camera
- Printer, wireless tablet, TelePresence endpoint
- Handheld terminal (factory/warehouse barcode scanner, mobile payment reader)
- IoT devices (smart bulb, thermostat, refrigerator...)

| Term | Meaning |
|------|---------|
| **Server** | An end device that provides a service |
| **Client** | An end device that consumes a service |
| **Host** | General term for any end device |

---

### 3.2 NIC — Network Interface Card

> The card that handles data input/output between a device and the network.

| Type | Description |
|------|-------------|
| **Wired NIC** | Ethernet port; usually integrated into the motherboard |
| **Wireless NIC** | Wi-Fi; embedded in mobile phones and laptops |
| **USB NIC** | External, attached via USB |

- Every NIC has a globally unique **MAC address**.
- Each NIC interface is assigned a separate **IP address**.
- A device with both wired and wireless → 2 NICs = 2 MACs = 2 IPs.

> Wireshark asks on launch: *"Which interface do you want to capture on?"* — you need to know what a NIC is to answer that.

---

### 3.3 Intermediary Devices — What They Do

Intermediary devices perform some or all of these functions:

- Regenerate and retransmit data signals
- Maintain information about pathways through the network and internetwork
- Notify other devices of errors and communication failures
- Direct data along alternate pathways when there is a link failure
- Classify and direct messages according to priorities (QoS)
- Permit or deny the flow of data, based on security settings

#### Switch

> Connects end devices to the local network via **cable**.

| Property | Detail |
|----------|--------|
| **Ports** | Common models: 5 / 8 / 16 / 24 / 48 ports |
| **Port = Interface** | Each connection point is numbered (Port 1, Port 2...) |
| **Schematic symbol** | 3D: striped cylinder | 2D: square + arrows |

#### Access Point

> Connects end devices to the local network **wirelessly**.

| Context | Detail |
|---------|--------|
| **Home** | Built into DSL/fiber modems |
| **Enterprise** | Standalone device, connected to a switch via cable |
| **Architecture** | Wireless device → AP → Cable → Switch → Network |

> 📌 Wi-Fi provides mobility, but **wired connections are always superior** in terms of capacity and security.

#### Router

> Connects **different networks** to each other. The fundamental building block of the internet.

- **"Internet"** = *Inter-network* → No router, no internet.

| Rule | Explanation |
|------|-------------|
| **Rule 1** | Each interface must connect to a **different** network |
| **Rule 2** | You cannot connect the same network to a router twice |
| **Implication** | Every router interface represents a separate network |
| **Schematic symbol** | Cylindrical arrow icon; circle in simple diagrams |

**Home modem = Router + Switch + Access Point + Modem → 4 devices in one box**

#### Firewall

> Enforces security policy at network boundaries.

- Positioned between WAN and LAN.
- All inbound traffic hits the firewall first.
- Weak against insider threats (see [Section 10](#10-network-security-fundamentals)).

#### Modem

> Converts the ISP's incoming signal into a format the router can process.

---

### 3.4 Media (Transmission Medium)

| Type | Transmission | Notes |
|------|-------------|-------|
| **Copper (UTP)** | Electrical signal | Ethernet cable; Cat5e, Cat6, Cat6a |
| **Fiber Optic** | Light signal (laser) | Long distance, high speed; single-mode / multi-mode |
| **Wireless** | Electromagnetic wave | Wi-Fi (air), satellite (space) |

---

## 4. Network Types

### 4.1 LAN — Local Area Network

> Devices at the **same physical location** connected together.

| Property | Detail |
|----------|--------|
| **Core device** | Switch (wired) or Access Point (wireless) |
| **Standards** | Ethernet IEEE 802.3 (wired), IEEE 802.11 (wireless) |
| **Ownership** | Managed by a single organisation or person |
| **Speed** | High bandwidth within the LAN |

### 4.2 WAN — Wide Area Network

> Connects multiple LANs using **third-party ISP infrastructure**.

| Property | Detail |
|----------|--------|
| **Why needed?** | Connecting LANs at different physical locations |
| **Infrastructure owner** | ISP — managed by one or more service providers |
| **Speed** | Generally lower speed links between LANs |
| **WAN technologies** | ADSL, cable, fiber (Metro Ethernet), ATM, MPLS |

**Example topology:**
```
Branch A LAN → Router → [ISP / WAN] → Router → Branch B LAN
                        (3 separate networks total)
```

**MAN (Metropolitan Area Network):** WAN within a single city. Usually classified under WAN.

### 4.3 How the Internet is Formed

```
Customer LAN
     ↓
 ISP Router  ←→  ISP-A  ←→  ISP-B  ←→  ISP-C
                   ↕              ↕
             ISP-D (EU)      ISP-E (USA)
                   ↕
             More ISPs...
                   ↕
   Connect → Connect → Connect → 1 million+ networks = INTERNET
```

> Internet is not owned by any single person or organisation.  
> Governance bodies: **IETF** (standards), **ICANN** (addressing), **IAB** (architecture).

---

## 5. Topology Diagrams

A **topology** is a map of the network — which device connects to what and how.

| Type | Shows |
|------|-------|
| **Physical topology** | Physical location of intermediate devices and cable installation |
| **Logical topology** | Devices, ports, and the addressing scheme of the network |

**Standard schematic symbols:**

| Device | Symbol |
|--------|--------|
| Router | Cylinder with arrows (3D) or circle (2D) |
| LAN Switch | Flat striped cylinder or square with arrows |
| Wireless router / AP | Router symbol + wireless waves |
| Multilayer switch | Starburst / sun icon |
| Firewall | Brick wall icon |
| End device (PC) | Monitor icon |
| Server | Tall box / rack unit icon |

> In any network diagram: **circles = routers**, **squares with arrows = switches**. Memorise these two.

---

## 6. ISP, Intranet, Extranet

```
┌──────────────────────────────────────────────────────────────┐
│                  INTERNET (The World — public)                │
│  ┌────────────────────────────────────────────────────────┐  │
│  │       EXTRANET (Suppliers, Customers, Partners)        │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │        INTRANET (Company Only — private)         │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

| Concept | Definition | Example |
|---------|-----------|---------|
| **ISP** | Licensed telecom; provides internet and private circuits | AT&T, BT, Vodafone |
| **Internet** | Public — 1 million+ interconnected networks | Web, email, cloud |
| **Intranet** | Private network collection owned by an organisation | Bank's branch network |
| **Extranet** | Private inter-organisational network for business partners | Airline + travel agency |

- **Intranet servers** are not accessible from the internet (R&D files, HR systems, etc.).
- **Extranet** connections typically use **VPN** for secure communication.

---

## 7. Reliable Network Criteria

> **Reliable network** = a network that delivers the required quality of service 24/7.

Network architecture must address four characteristics to meet user expectations:

### 7.1 Fault Tolerance

> A failure at a single point must not bring down the entire system.

- Reliable networks provide **redundancy** by implementing packet-switched networks.
- Packet switching splits traffic into packets routed over the network — each packet can theoretically take a different path to the destination.
- This is not possible with circuit-switched networks which establish dedicated circuits.

**Single Point of Failure:** When one failure stops all service → eliminated by design.

### 7.2 Scalability

> The network must grow quickly and easily to support new users without impacting existing users.

- Network designers follow accepted **standards and protocols** to achieve scalability.
- Cloud infrastructure handles sudden spikes: virtual servers scale up/down on demand.

### 7.3 QoS — Quality of Service

> It's not enough for the service to arrive — it must arrive **on time and at quality**.

- QoS is the primary mechanism used to ensure reliable delivery for all users.
- With a QoS policy, the router can more easily manage the flow of data and voice traffic.

| Metric | Standard | Source |
|--------|----------|--------|
| **Voice delay** | Max **150 ms** | ITU standard |
| **If exceeded** | Conversation overlaps; natural flow breaks | — |
| **Web pages** | Can tolerate higher delay → lower QoS priority | — |
| **VoIP calls** | Require low delay → higher QoS priority | — |

### 7.4 Network Security

If the network is not secure, the three criteria above become meaningless. (see [Section 10](#10-network-security-fundamentals))

---

## 8. Modern Trends

### 8.1 BYOD — Bring Your Own Device

> Employees connecting personal devices to the corporate network.

BYOD gives users freedom to use personal tools — laptops, netbooks, tablets, smartphones, e-readers — to access information and communicate.

| | Detail |
|-|--------|
| **Advantage** | More opportunities and flexibility for end users |
| **Risk** | Malware on personal devices can spread to the corporate network |
| **Mitigation** | Guest Wi-Fi (isolated) or restrict to company-issued devices only |

BYOD is the scenario where the owner, type, and location of the device is **unlimited**.

### 8.2 Collaboration

- Zoom, Microsoft Teams, Cisco Webex, Google Meet, Cisco TelePresence
- Video conferencing is becoming a **critical requirement** for effective collaboration
- Real-time audio/video → **high bandwidth + low latency required** (QoS critical)
- Video calls to anyone, regardless of location

### 8.3 Cloud Computing

> Hosting servers in a third-party data centre and accessing them over the internet.

| Model | Detail |
|-------|--------|
| **On-Premises (old)** | In-house servers; HVAC, UPS, generator, fire suppression required |
| **Cloud (new)** | Rent capacity; provider handles maintenance |
| **Network impact** | Internet uptime becomes a **critical operational dependency** |
| **Scaling** | Virtual machines grow/shrink on demand |

**Four types of cloud:**

| Type | Description |
|------|-------------|
| **Public cloud** | Available to the general public — pay-per-use or free |
| **Private cloud** | Designed for a specific organisation (e.g. government) |
| **Hybrid cloud** | Two or more cloud types combined — parts remain distinct objects |
| **Custom cloud** | Built for a specific industry (healthcare, media) — can be public or private |

### 8.4 Smart Home Technology

> Growing technology trend integrating everyday devices so they communicate with each other.

- Ovens can communicate with calendar apps to know when you'll be home
- Smart home technology is currently being developed for every room in a home

### 8.5 Powerline Networking

> Allows devices to connect to a LAN using existing electrical wiring.

- Useful when wireless access points cannot reach all devices in the home
- Devices connect to the LAN via a standard powerline adapter plugged into an electrical outlet
- Data is sent at specific frequencies over the electrical wiring

### 8.6 Wireless Broadband

> Alternative to DSL and cable for connecting homes and small businesses to the internet.

- **WISP** (Wireless Internet Service Provider) — connects subscribers to access points or hotspots; common in rural areas
- Uses the same cellular technology used by smartphones
- An antenna is installed outside the home providing wireless or wired connectivity for devices inside

---

## 9. Internet Access Technologies

### Home / Small Office

| Technology | Description |
|-----------|-------------|
| **ADSL / VDSL** | Over phone lines; common residential technology |
| **Broadband cable** | Cable TV infrastructure |
| **Fiber (GPON)** | Gigabit Passive Optical Network; high speed and stability |
| **4G / 5G (cellular)** | Mobile; portable internet access |
| **Dial-up** | 50 Kbps; historical; no longer in use |
| **Satellite (GEO)** | ~40,000 km altitude; high latency; emergency/remote use |
| **Starlink (LEO)** | ~400 km altitude; low latency; expanding globally |
| **Power Line / HomePlug** | Data over electrical wiring |
| **Wireless broadband (WISP)** | Fixed wireless via cellular-style antenna |

### Enterprise / Business

| Technology | Description |
|-----------|-------------|
| **Metro Ethernet** | Most popular WAN solution; Ethernet over fiber |
| **IP/MPLS** | ISP service for interconnecting branches |
| **Business DSL** | Separate infrastructure from residential; SLA-guaranteed |
| **Dedicated leased lines** | Private point-to-point circuit; guaranteed bandwidth |
| **Satellite** | Remote/offshore locations where no terrestrial option exists |

### IPv4 → IPv6 Migration

```
IPv4 address space        :  ~4 billion
Internet-connected devices:  ~50 billion
                          ─────────────────
                          ADDRESS SPACE EXHAUSTED
```

| Property | IPv4 | IPv6 |
|----------|------|------|
| Address space | ~4 billion | 340 undecillion |
| NAT required | Yes | No |
| Direct IoT access | Difficult | Easy |
| Security architecture | Separate from protocol | Built into protocol |

> ⚠️ IPv6 security architecture differs from IPv4. Organisations must build test environments before migration.

---

## 10. Network Security Fundamentals

### 10.1 Security Threats

**External threats:**

| Threat | Description |
|--------|-------------|
| Viruses, worms, Trojan horses | Malicious software that damages or controls systems |
| Spyware and adware | Collects user data without consent |
| Zero-day attacks | Exploit unknown vulnerabilities before a patch exists |
| Threat actor attacks | Targeted attacks by skilled adversaries |
| Denial of Service (DoS) | Overwhelms network resources to deny service |
| Data interception and theft | Capturing data in transit |
| Identity theft | Stealing credentials to impersonate users |

**Internal threats:**

| Threat | Description |
|--------|-------------|
| Lost or stolen devices | Physical access enables data extraction |
| Accidental misuse by employees | Misconfiguration, phishing clicks |
| Malicious insiders | Intentional data theft or sabotage |

### 10.2 Insider vs. Outsider Threat

**Textbook answer:** Insider threats are considered more dangerous.  
**In practice, the most dangerous:** A remote attacker controlling malware already deployed inside the network.

#### Insider Threat — Attacker Advantages

| Advantage | Explanation |
|-----------|-------------|
| No firewall barrier | Already inside the LAN |
| High bandwidth | 1 Gbps+ LAN speeds |
| Low latency | Sub-millisecond |
| Credentials | Username / password / IP may already be known |
| Authorization | May already have legitimate server access |

#### Insider Threat — Attacker Disadvantages

- Activity is logged — every action is traceable
- Physical identification is possible
- Legal consequences are swift: termination, lawsuit, imprisonment

### 10.3 Security Solutions

**Home / small office:**
- Antivirus and anti-spyware software installed on end devices
- Firewall filtering to prevent unauthorised network access

**Larger organisations — additional requirements:**

| Tool | Purpose |
|------|---------|
| **Dedicated firewall systems** | Hardware-based perimeter security |
| **ACL** | Access Control List — rules on routers/switches |
| **IPS** | Intrusion Prevention System — detects and blocks attacks; the blue team's primary tool |
| **VPN** | Encrypted secure tunnel; mandatory for remote access |

> Network security study starts with a clear understanding of basic **switching and routing** infrastructure.

> **On VPN:** VPN ≠ "bypassing blocked websites". VPN = Encrypted, secure network tunnel. No mid-to-large organisation can operate without VPN.

### 10.4 Security Balance

```
Security ↑  (too strict)  →  Operational friction ↑
Security ↓  (too loose)   →  Attack surface ↑
                ──────────────────────────────────
                Balance is determined by the organisation's risk appetite
```

### 10.5 Converged Networks

Previously separate networks have merged into a single IP infrastructure:

| System | Old Infrastructure | Today |
|--------|--------------------|-------|
| Telephony | Dedicated PBX and separate cabling | IP Phone → computer network |
| Security cameras | Coaxial cable, separate recording system | IP Camera → computer network |
| Building management | Separate control systems | Over IP |
| Broadcasting | Separate broadcast network | Over IP |

> **Consequence:** When the network goes down, **all systems fail from a single point**. This makes network reliability mission-critical.

---

## 11. CIA Triad

> For a digital infrastructure to be considered **secure**, all three conditions must be met simultaneously.

```
              Confidentiality (C)
                    /\
                   /  \
                  /    \
                 /  CIA \
                / Triad  \
               /──────────\
     Integrity (I)    Availability (A)
```

| Letter | Criterion | Definition | Violation Example |
|--------|-----------|-----------|-------------------|
| **C** | Confidentiality | Only authorised parties can access the data | Unauthorised access to R&D files |
| **I** | Integrity | Data cannot be altered without authorisation | Wire transfer amount manipulated |
| **A** | Availability | Data must be accessible when needed | Ransomware — files are encrypted |

**Mnemonic:** **C**onfidentiality · **I**ntegrity · **A**vailability → **CIA**

**Network security goals (from Cisco CCNA):**
- Confidentiality — only intended recipients can read the data
- Integrity — assurance that the data has not been altered during transmission
- Availability — assurance of timely and reliable access to data for authorised users

---

## 12. CCNA Certification Path

**CCNA (Cisco Certified Network Associate)** demonstrates knowledge of foundational technologies and keeps you current for the skills required for next-generation technologies.

**New CCNA focus areas:**
- IP foundations and security topics
- Wireless, virtualisation, automation and network programmability

**Cisco certification levels:**

```
Associate → Specialist → Professional (CCNP) → Expert (CCIE)
```

**DevNet certifications** (also available at Associate, Specialist, Professional levels):
- Validate programming competencies for network automation

**Specialist certifications:**
- Validate skills relevant to your specific job role and interest area

> Career resources: [netacad.com](https://www.netacad.com) → Careers menu → Talent Bridge Matching Engine

---

## 13. Glossary

| Term | Definition |
|------|-----------|
| **Network** | An environment where two or more devices communicate |
| **Host / End Device** | The source or destination of a packet |
| **NIC** | Network Interface Card |
| **MAC Address** | Hardware-burned, globally unique identifier of a NIC |
| **IP Address** | Logical address on a network; used for routing |
| **Switch** | Connects end devices to the local network via cable |
| **Access Point** | Connects end devices to the local network wirelessly |
| **Router** | Connects different networks together |
| **Firewall** | Enforces security policy at network boundaries |
| **Modem** | Converts ISP signal to a format the router understands |
| **LAN** | Local Area Network |
| **WAN** | Wide Area Network |
| **MAN** | Metropolitan Area Network |
| **SOHO** | Small Office / Home Office |
| **ISP** | Internet Service Provider |
| **Internet** | Public global network of 1 million+ interconnected networks |
| **Intranet** | An organisation's private collection of networks |
| **Extranet** | Private inter-organisational network for business partners |
| **Topology** | A map of the network — which device connects to what |
| **Physical topology** | Shows physical location of devices and cables |
| **Logical topology** | Shows devices, ports, and addressing scheme |
| **Packet** | A unit of data transmitted over a network |
| **Packet switching** | Traffic split into packets; each can take a different path |
| **Circuit switching** | Dedicated circuit established for the duration of communication |
| **Bandwidth** | Data carrying capacity of a network (Mbps, Gbps) |
| **Latency / Delay** | Time for a packet to travel from source to destination (ms) |
| **Port (Physical)** | A connection point on a switch or router |
| **Port (Logical)** | An application-layer communication channel number |
| **QoS** | Quality of Service |
| **SLA** | Service Level Agreement |
| **Fault Tolerance** | System continues operating despite individual component failures |
| **Scalability** | Ability to grow or shrink according to demand |
| **Redundancy** | Duplicating critical components to prevent single points of failure |
| **Single Point of Failure** | One failure that stops the entire service |
| **BYOD** | Bring Your Own Device |
| **Cloud** | Servers hosted in a remote data centre, accessed over the internet |
| **Public Cloud** | Available to the general public; pay-per-use or free |
| **Private Cloud** | Designed for a specific organisation |
| **Hybrid Cloud** | Combination of two or more cloud types |
| **VPN** | Virtual Private Network — encrypted secure tunnel |
| **IDS** | Intrusion Detection System |
| **IPS** | Intrusion Prevention System |
| **ACL** | Access Control List |
| **DoS** | Denial of Service attack |
| **Zero-day** | Exploit targeting an unknown, unpatched vulnerability |
| **CIA Triad** | Confidentiality + Integrity + Availability |
| **IPv4** | Internet Protocol v4 — ~4 billion addresses; exhausted |
| **IPv6** | Internet Protocol v6 — 340 undecillion addresses |
| **UTP** | Unshielded Twisted Pair — copper ethernet cable |
| **Ethernet** | IEEE 802.3 — world-standard local area network protocol |
| **WLAN** | Wireless LAN — IEEE 802.11 |
| **IoT** | Internet of Things |
| **CCNA** | Cisco Certified Network Associate |
| **CCIE** | Cisco Certified Internetwork Expert |
| **IETF** | Internet Engineering Task Force — internet standards body |
| **ICANN** | Internet Corporation for Assigned Names and Numbers |
| **IAB** | Internet Architecture Board |
| **Cabinet / Rack** | Metal enclosure for mounting network and server equipment |
| **On-Premises** | Servers hosted at the organisation's own location |
| **Converged Network** | Phone, camera, and IT systems unified on a single IP infrastructure |
| **WISP** | Wireless Internet Service Provider |
| **SOHO** | Small Office / Home Office |
| **Powerline networking** | Data transmission over existing electrical wiring |

---

*CCNA Intro to Networks | Cybersecurity Operations Training Program*  
*Based on Cisco CCNA1 ITN v7.0 — Module 1*
