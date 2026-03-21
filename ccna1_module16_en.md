# CCNA1 – Module 16: Network Security Fundamentals
> CCNA1 · Introduction to Networks v7.0 (ITN)  


---

## 📋 Module Objectives

| Topic | Objective |
|-------|-----------|
| Security Threats and Vulnerabilities | Explain why basic security measures are necessary on network devices |
| Network Attacks | Identify the types of network attacks and vulnerabilities |
| Network Attack Mitigations | Describe general mitigation techniques |
| Device Security | Configure network devices with device hardening features |

---

## 16.1 Security Threats and Vulnerabilities

### Types of Threats

Network attacks can be devastating and result in significant loss of time and money due to damaged or stolen information or assets. Intruders can gain access through software vulnerabilities, hardware attacks, or by guessing a username and password.

> 💡 **Threat Actor:** An intruder who gains access by modifying software or exploiting software vulnerabilities.

### 4 Types of Threats After Network Access:

```
┌──────────────────────────────────────────────────────────┐
│                    4 MAIN THREAT TYPES                   │
├─────────────────────┬────────────────────────────────────┤
│ 🔓 Information Theft │ Stealing corporate/personal data   │
│ 💾 Data Loss /       │ Deleting, altering, or encrypting  │
│    Manipulation      │ data for ransom                    │
│ 👤 Identity Theft    │ Capturing personal/credentials     │
│ 🚫 Disruption of     │ Preventing access to network       │
│    Service           │ resources and services             │
└─────────────────────┴────────────────────────────────────┘
```

---

### Types of Vulnerabilities

**Vulnerability:** The degree of weakness in a network or device. Routers, switches, desktops, servers, and even security devices have some degree of vulnerability.

#### 3 Main Sources of Security Vulnerabilities:

```
                   VULNERABILITY SOURCES
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
   TECHNOLOGICAL      CONFIGURATION      SECURITY
   VULNERABILITIES    VULNERABILITIES    POLICY
                                         VULNERABILITIES
  • TCP/IP protocol  • Weak passwords   • No written policy
    weaknesses       • Guessable system • No authentication
  • OS weaknesses      passwords          continuity
  • Network          • Misconfigured    • No logical access
    equipment          services           controls
    weaknesses       • Insecure default • No disaster
                       settings           recovery plan
```

> ⚠️ **Note:** Protocol weaknesses (IP spoofing, ARP poisoning) cannot be fixed with updates — they require additional control mechanisms.

---

### Physical Security Threats

If network resources can be physically compromised, a threat actor can deny use of those resources.

| Threat Class | Examples |
|--------------|----------|
| **Hardware Threats** | Physical damage to servers, routers, switches, wiring |
| **Environmental Threats** | Extreme temperatures, excessive humidity |
| **Electrical Threats** | Voltage spikes, power failures, power loss |
| **Maintenance Threats** | Poor cabling, missing spare parts, bad labeling |

> 🏢 **Real World:** A broken server room AC unit can cause servers to overheat and shut down. Cutting cables or physically damaging equipment is also a cyber threat!

---

## 16.2 Network Attacks

### Types of Malware

**Malware** (Malicious Software): Code or software specifically designed to damage, disrupt, steal, or perform illegitimate actions on data, hosts, or networks.

```
                      MALWARE TYPES
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
       VIRUS              WORM          TROJAN HORSE
          │                 │                 │
  Attaches to other   Standalone,       Looks legitimate,
  programs, spreads   no host needed,   doesn't replicate,
  file-to-file        spreads via       provides remote
                      network           access backdoor
```

| Feature | Virus | Worm | Trojan Horse |
|---------|-------|------|--------------|
| Spreading method | Attaches to files | Independent, spreads via network | Requires user interaction |
| Host program needed | Yes | No | No |
| Replication | Infects other files | Self-replicates | Does not replicate |
| Main purpose | Spread/damage | Spread/damage | Backdoor remote access |

> 💡 **Ransomware:** Operates as a worm; encrypts files and demands ransom. Modern malware often combines worm + trojan characteristics simultaneously.

---

### 3 Main Categories of Network Attacks

```
                  NETWORK ATTACK CATEGORIES
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
   RECONNAISSANCE        ACCESS           DENIAL OF
      ATTACKS            ATTACKS           SERVICE
        │                  │                  │
  Discover and map    Unauthorized       Disable or
  systems, services,  manipulation of    disrupt
  vulnerabilities     data/systems       services
```

---

### 1) Reconnaissance Attacks

Discovery and mapping of systems, services, or vulnerabilities.

#### Passive vs. Active Reconnaissance:

```
PASSIVE RECONNAISSANCE                ACTIVE RECONNAISSANCE
──────────────────────                ─────────────────────
Gathering information without         Sending traffic directly to
direct interaction with target        target to gather information
(low risk of detection)               (detectable by IDS/IPS)

Examples:                             Examples:
• DNS queries (nslookup)              • Port scanning (Nmap)
• WHOIS/RIPE lookups                  • Open port detection
• Website analysis                    • Service version detection
• Social media research               • Vulnerability scanning
• Metadata analysis                   • Banner grabbing
```

> 🎯 **Attack Flow:** Reconnaissance → Vulnerability identification → Exploitation

---

### 2) Access Attacks

Exploiting known vulnerabilities in authentication services, FTP services, and web services.

| Type | Description |
|------|-------------|
| **Password Attacks** | Brute force, trojan, packet sniffers |
| **Trust Exploitation** | Using trusted system relationships to access unauthorized resources |
| **Port Redirection** | Using a compromised host as a pivot to attack other targets |
| **Man-in-the-Middle** | Positioning between two parties to read or modify data |

> ⚠️ **Man-in-the-Middle:** Requires being on the same network (public WiFi at cafés, airports, hotels is risky!). Can be done via ARP poisoning, rogue AP, or fake DHCP server.

---

### 3) Denial of Service Attacks (DoS / DDoS)

```
DoS (Denial of Service)              DDoS (Distributed DoS)
────────────────────────              ──────────────────────
Overwhelming a target from           Coordinated attack from
a single source to prevent           thousands/millions of
legitimate access                    zombie machines simultaneously

     [Attacker]                       [C&C Server]
          │                                 │
          ▼                       ┌─────────┼─────────┐
       [Target]                   ▼         ▼         ▼
                               [Zombie] [Zombie]  [Zombie]
                                   └─────────┼─────────┘
                                             ▼
                                          [Target]
```

**DoS Attack Types:**

| Type | Description |
|------|-------------|
| **Application-Centric** | HTTP flood, SYN flood — fills connection tables |
| **Volumetric** | Overwhelms with massive bandwidth; still no 100% solution |

**Botnet Architecture:**
```
Attacker
    │
    ▼
C&C Server (Command & Control)
    │
    ├──▶ Sub-C&C 1 ──▶ [Zombie x1000]
    ├──▶ Sub-C&C 2 ──▶ [Zombie x1000]
    └──▶ Sub-C&C 3 ──▶ [Zombie x1000]
                               │
                               ▼
                            [Target]
```

> 💡 **Zombie:** A compromised computer awaiting remote commands.  
> 💡 **Botnet:** A network of zombie machines.  
> 💡 **C&C (Command & Control):** The server that sends attack orders to zombies.

---

## 16.3 Network Attack Mitigations

### The Defense-in-Depth Approach

Layered security: No single defense layer is sufficient — multiple security layers must work together.

```
INTERNET
    │
    ▼
 [VPN]  ◄── Secures remote access
    │
    ▼
[ASA FIREWALL]  ◄── Blocks unauthorized access
    │
    ▼
  [IPS]  ◄── Analyzes allowed traffic, detects attacks
    │
    ▼
[ESA/WSA]  ◄── Filters email and web traffic
    │
    ▼
[Layer 3 Switch]
    │
    ▼
[Layer 2 Switch]
    │
    ├──▶ [AAA Server]  ◄── Authentication/authorization/accounting
    ├──▶ [Email Server]
    ├──▶ [Web Server]
    ├──▶ [DHCP Server]
    └──▶ [Clients/Hosts]
```

---

### Security Devices and Services

#### 1. Firewall

> 🛡️ **Firewall = Bouncer:** Passes or blocks traffic based on defined rules.

```
INSIDE (Internal Network)         OUTSIDE (Internet)
─────────────────────              ────────────────
                   [FIREWALL]
   PC ──────────▶     │    ──────────▶ Google
   PC ◀──────────     │    ◀────X──── Attacker
                      │
               [DMZ Zone]
                   Web Server
                   (Accessible from outside,
                    isolated from inside)
```

**Firewall Types:**

| Type | How It Works |
|------|-------------|
| **Packet Filtering** | Allow/deny based on IP/MAC address |
| **Application Filtering** | Filter by port number (80=HTTP, 22=SSH) |
| **URL Filtering** | Filter access to specific URLs or keywords |
| **Stateful Packet Inspection (SPI)** | Maintains session table; only allows responses to internally initiated connections |

**DMZ (Demilitarized Zone):**
- Servers that need internet access (web, mail) are placed here
- Isolated from the internal network; if a DMZ server is hacked, internal network stays protected

---

#### 2. IPS (Intrusion Prevention System)

Performs deeper analysis of traffic that the firewall already permitted, detecting and blocking attacks.

```
Internet ──▶ Firewall ──▶ [IPS] ──▶ Web Server
                            │
                     SQL injection?
                     Buffer overflow?
                         │
                    Attack:  BLOCK
                    Normal:  PASS
```

> ⚠️ **False Positive:** When IPS mistakenly blocks legitimate traffic as an attack. Requires careful tuning to avoid disrupting critical services.

---

#### 3. ESA / WSA (Email and Web Security Appliances)

```
Email Security (ESA):              Web Security (WSA):
─────────────────────               ──────────────────
Internet                            User
    │                                   │
    ▼                                   ▼ (web request)
  [ESA]  ◄── Spam, phishing,       [WSA]  ◄── Malicious site?
    │         malicious attachment      │         URL filter?
    ▼         analysis                  ▼
Email                               Website
Server                              (deliver to user if clean)
    │
    ▼
User
```

> 💡 Two main channels where end users are exposed to threats: **web** and **email** — both must be protected.

---

#### 4. AAA Server (Authentication, Authorization, Accounting)

```
AAA = AUTHENTICATION + AUTHORIZATION + ACCOUNTING

Authentication  →  "Who are you?"           (Username/password)
Authorization   →  "What can you do?"        (Permission levels)
Accounting      →  "What did you do?"         (Log records)

Credit card analogy:
  Authentication  = Who can use the card
  Authorization   = How much can be spent (credit limit)
  Accounting      = Itemized statement
```

**802.1X Network Access Control:**
```
When user plugs in cable:
    Switch ──▶ "Who are you? Provide credentials."
    User ──▶ Enters credentials
    Switch ──▶ Queries AAA Server
    AAA ──▶ "Approved! Permissions: internet only"
    Switch ──▶ Applies rules to port
```

---

#### 5. VPN (Virtual Private Network)

Enables remote users to connect to the corporate network through a secure, encrypted tunnel.

```
Home/Remote Office ──[Encrypted Tunnel]──▶ Corporate Firewall ──▶ Internal Network
                       (Secure communication
                        over internet)
```

> 💡 **Corporate VPN ≠ NordVPN/ExpressVPN.** Companies set up their own VPN servers; employees connect to the company's own firewall via VPN.

---

### Backup

> 🏆 **The most powerful security measure: TAKING BACKUPS!**

Most effective protection against ransomware, data deletion, and manipulation.

| Consideration | Description |
|---------------|-------------|
| **Frequency** | Regular intervals (weekly full + daily incremental) |
| **Storage** | Stored offsite/offline, independent from the network |
| **Security** | Protected with strong password; ideally stored encrypted |
| **Validation** | Restore procedure must be regularly tested |

> ⚠️ **Warning:** A permanently connected USB drive, network share, or auto-synced cloud drive is NOT a real backup — attackers can encrypt/delete those too.

---

### Upgrade, Update, and Patch

- The most effective way to mitigate a worm attack: download security updates from OS vendor and patch all vulnerable systems
- Ensure all endpoint systems download updates automatically
- Mobile phones, computers, network devices, IoT devices — **everything must be kept current**

---

### Endpoint Security

```
Endpoint = Laptop, desktop, server, smartphone, tablet

Key measures:
  ✅ Create security policy and have staff sign it
  ✅ Train employees on cybersecurity awareness
  ✅ Antivirus software must be installed and active
  ✅ Use host-based IPS
  ✅ Apply security patches regularly
  ✅ Block cracked/pirated software installation
  ✅ Avoid banking on public WiFi
  ✅ Keep personal firewall (Windows Defender, etc.) active
```

---

## 16.4 Device Security

### Cisco AutoSecure

When new IOS is installed, security settings default to factory values (often insufficient). The `auto secure` command automatically applies basic security configuration.

**General principles (for all operating systems):**
- Default usernames and passwords must be changed immediately
- Access to system resources should be limited to authorized individuals
- Unnecessary services and applications should be disabled and removed
- Software updates and security patches should be applied before deployment

---

### Strong Password Guidelines

| Rule | Description |
|------|-------------|
| **Length** | Minimum 8 characters, preferably 10+ |
| **Complexity** | Mix of uppercase/lowercase, numbers, symbols, and spaces |
| **Avoid** | Dictionary words, birthdays, names, ID numbers |
| **Rotation** | Change passwords regularly |
| **Storage** | Never write in visible locations (under monitor, on desk) |

**Passphrase Technique:**

```
Sentence: "Gokhan goes to the cinema at 5 PM."

Building the password:
  G  (Gokhan)
  g  (goes)
  t  (to the)
  c  (cinema)
  @  (at)
  5  (5)
  PM (PM)

Result: "GgtC@5PM." — or use the whole phrase: "GokhanGoesToCinema@5PM."

✅ 10+ characters
✅ Uppercase and lowercase letters
✅ Numbers included
✅ Symbols included
✅ Easy to remember
```

---

### Additional Password Security Commands

```
! Encrypt all plain-text passwords
Router(config)# service password-encryption

! Set minimum password length
Router(config)# security passwords min-length 8

! Deter brute-force attacks
! (3 failed attempts within 60 sec = lock for 120 sec)
Router(config)# login block-for 120 attempts 3 within 60

! Configure VTY lines
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# exec-timeout 5 30      ! Disconnect after 5m 30s idle
Router(config-line)# transport input ssh     ! SSH only
Router(config-line)# end
```

---

### Enabling SSH

**Telnet vs SSH:**

```
TELNET (Port 23)                SSH (Port 22)
──────────────────               ──────────────
❌ Plaintext (no encryption)     ✅ Encrypted
❌ Readable by Wireshark         ✅ Unreadable by Wireshark
❌ Insecure                      ✅ Secure
```

**SSH Configuration Steps:**

```
! 1. Configure a unique hostname
Switch(config)# hostname CAT1-SW

! 2. Configure the IP domain name
CAT1-SW(config)# ip domain-name example.com

! 3. Generate RSA crypto key (min 1024 bit, recommended 2048 bit)
CAT1-SW(config)# crypto key generate rsa general-keys modulus 2048
! → "SSH has been enabled" message appears

! 4. Create local user accounts
CAT1-SW(config)# username gokhan password Cisco123
CAT1-SW(config)# username ozan password Cisco456

! 5. Configure VTY lines
CAT1-SW(config)# line vty 0 15
CAT1-SW(config-line)# login local          ! Authenticate against local DB
CAT1-SW(config-line)# transport input ssh  ! SSH only
CAT1-SW(config-line)# exec-timeout 5 0    ! 5-minute timeout
CAT1-SW(config-line)# end
```

> 💡 **Important:** When using SSH, replace `login` with `login local` — SSH requires a username, not just a password.

---

### Disabling Unused Services

```
! List open ports (IOS-XE)
Router# show ip ports all

! List open ports (older IOS)
Router# show control-plane host open-ports

! Disable HTTP server service
Switch(config)# no ip http server
Switch(config)# no ip http secure-server
```

**Why it matters:**
- Unused services = unnecessary attack surface
- Every open port is a potential entry point
- Reduces unnecessary CPU/RAM consumption

---

## 📊 Summary: Security Layers

```
    EXTERNAL ATTACKERS (Internet)
               │
               ▼
          ┌─────────┐
          │   VPN   │  ← Secures remote access
          └─────────┘
               │
               ▼
       ┌──────────────┐
       │   FIREWALL   │  ← Blocks unauthorized traffic (~70% of attacks)
       └──────────────┘
               │
               ▼
           ┌───────┐
           │  IPS  │  ← Deep inspection of allowed traffic
           └───────┘
               │
               ▼
         ┌──────────┐
         │ ESA/WSA  │  ← Cleans email and web traffic
         └──────────┘
               │
               ▼
         ┌──────────┐
         │ AAA/802.1X│  ← Who connects? What can they do? What did they do?
         └──────────┘
               │
               ▼
     ┌─────────────────┐
     │  APPLICATION /  │  ← Antivirus, host firewall, updates
     │      HOST       │
     └─────────────────┘
               │
               ▼
           YOUR DATA
     (Protected by Backup)
```

---

## 📝 Module Summary

1. **4 threat types:** Information theft, data loss/manipulation, identity theft, disruption of service
2. **3 vulnerability sources:** Technological, configuration, security policy
3. **4 physical threat classes:** Hardware, environmental, electrical, maintenance
4. **Malware types:** Virus (attaches to files), Worm (spreads independently), Trojan (remote access backdoor)
5. **3 attack categories:** Reconnaissance, access, denial of service (DoS/DDoS)
6. **Defense-in-depth:** VPN + Firewall + IPS + ESA/WSA + AAA
7. **Backup strategy:** Frequency, offsite storage, security, validation
8. **Device security:** AutoSecure, strong passwords, SSH, disable unnecessary services
9. **SSH configuration:** hostname → domain → crypto key → username → login local → transport input ssh

---

*Cisco CCNA1 · Introduction to Networks v7.0 · Module 16*  

