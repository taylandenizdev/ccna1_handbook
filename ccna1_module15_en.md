# CCNA1 – Module 15: Application Layer
> CCNA1 · Introduction to Networks v7.0 (ITN)  
> Gökhan AKIN – CCIE | Ozan BÜK – CCIE

---

## 📋 Module Objectives

| Topic | Objective |
|-------|-----------|
| Application, Presentation, and Session | Explain how the three upper layers work together to support end-user applications |
| Peer-to-Peer | Explain how end-user applications operate in a P2P network |
| Web and Email Protocols | Explain how HTTP, HTTPS, SMTP, POP, and IMAP work |
| IP Address Services | Explain how DNS and DHCP work |
| File Sharing Services | Explain how FTP, TFTP, and SMB work |

---

## 15.1 Application, Presentation, and Session Layers

### OSI Layer Overview

```
OSI Model (7 Layers)                 TCP/IP Model
────────────────────                 ────────────
7. APPLICATION  ┐                    ┐
6. PRESENTATION ├── Application      │ Application
5. SESSION      ┘   Layers           ┘
─────────────────────────────────────────────────
4. TRANSPORT    ────────────────────── Transport
─────────────────────────────────────────────────
3. NETWORK      ┐                    ┐
2. DATA LINK    ├── Data Flow        │ Internet /
1. PHYSICAL     ┘   Layers           ┘ Network Access
```

> 💡 Network engineers primarily focus on the **bottom 4 layers** (Transport, Network, Data Link, Physical). The application layers are also addressed by software developers and system administrators.

---

### Layer 7: Application Layer

**Function:** Provides the user interface through which network communication is initiated.

```
User
 │  (types www.google.com in browser)
 ▼
[ Application Layer – L7 ]
 │  Chrome, Firefox, Outlook, WhatsApp...
 │  → Creates data and passes it to lower layers
 ▼
[ Transport – L4 ] → [ Network – L3 ] → [ Data Link – L2 ] → [ Physical – L1 ]
```

Most common application layer protocols: **HTTP, FTP, TFTP, IMAP, DNS, DHCP, SMTP, Telnet, SSH**

---

### Layer 6: Presentation Layer

**Function:** Determines the format in which data is presented for transmission.

**3 core functions:**

```
1. DATA FORMATTING
   Converts data into a format the destination device understands.
   Examples: text/html, JPEG, MP4, GIF, PNG, MOV, MKV

2. COMPRESSION
   Reduces data size to use less bandwidth.
   Example: sending a webpage in gzip format

3. ENCRYPTION
   Encrypts data for secure transmission.
   Example: HTTPS/TLS encryption
   Note: In practice, TLS operates above the transport layer.
```

---

### Layer 5: Session Layer

**Function:** Manages and tracks sessions between client and server.

```
What the Session Layer does:
  ✅ Creates and maintains dialogs
  ✅ Tracks data exchange throughout the session
  ✅ Terminates sessions that time out
  ✅ Restarts interrupted sessions

Real-world examples:
  • Facebook login → long inactivity → session timeout
  • Twitter new tab → session continues without re-login
  • WhatsApp "...is typing" indicator → session tracking
  • File transfer progress tracking
```

---

### How the Layers Work Together

```
Example: Opening www.google.com

L7 Application:  Create a "fetch Google" request
                       │
L6 Presentation: Format data as HTML/gzip
                       │
L5 Session:      Start session, track it
                       │
L4 Transport:    TCP → 3-way handshake → segment data
                       │
L3 Network:      Add IP address, route
                       │
L2 Data Link:    Add MAC address, create Ethernet frame
                       │
L1 Physical:     Transmit bits over cable/air
```

---

### Key Application Layer Protocols (Port Table)

| Protocol | Port | Transport | Description |
|----------|------|-----------|-------------|
| DNS | UDP/TCP 53 | UDP (queries), TCP (zone transfer) | Domain name → IP resolution |
| DHCP | UDP 67 (server), 68 (client) | UDP | Automatic IP assignment |
| HTTP | TCP 80, 8080 | TCP | Web browsing |
| HTTPS | TCP 443 | TCP | Encrypted web browsing |
| SMTP | TCP 25, 587, 465 | TCP | Sending email |
| POP3 | TCP 110, 995 (secure) | TCP | Downloading email (deletes from server) |
| IMAP | TCP 143, 993 (secure) | TCP | Email synchronization |
| FTP | TCP 21 (control), 20 (data) | TCP | File transfer |
| TFTP | UDP 69 | UDP | Simple file transfer |
| Telnet | TCP 23 | TCP | Remote CLI (unencrypted) |
| SSH | TCP 22 | TCP | Remote CLI (encrypted) |
| SMB | TCP 445 | TCP | File/printer sharing |

---

## 15.2 Peer-to-Peer

### Client/Server Model

```
        INTERNET
           │
    ┌────────────┐
    │   SERVER   │  ← Stores resources and provides services
    └────────────┘
           │  Network
    ┌────────────┐
    │   CLIENT   │  ← Requests services
    └────────────┘     Download ↓ / Upload ↑
```

**Key rules:**
- The device requesting information → **Client**
- The device responding to the request → **Server**
- Resources are stored on the server
- Download speed is typically higher than upload (end users consume more than they produce)

---

### Peer-to-Peer (P2P) Networks

```
IN A P2P NETWORK:
  Every device can act as both client and server

  [PC-A] ←──────────────────▶ [PC-B]
  File Server                  Print Client
  Print Client                 File Client
                         │
                     [Printer]
                  (USB-connected to B)
                     Print Server

Advantages:
  ✅ No dedicated server required
  ✅ Suitable for small offices
  ✅ Easy to set up

Disadvantages:
  ❌ No centralized management
  ❌ Security issues increase as user count grows
  ❌ Hard to track who accesses what
```

---

### Common P2P Applications

```
P2P APPLICATION ARCHITECTURE:

[Index Server]
  │  "Which peer has which file?"
  ▼
[Peer 1] ←──────────────────▶ [Peer 2]
  │    \                     /    │
  │     \       Network     /     │
  ▼      \                 /      ▼
[Peer 3]──────────────────────[Peer 4]
              Direct P2P connections

Common P2P networks: BitTorrent, Direct Connect, eDonkey, Freenet
```

> ⚠️ **Note:** WhatsApp is NOT P2P! Messages go to WhatsApp's servers first. In true P2P, there is no intermediary server.

---

## 15.3 Web and Email Protocols

### HTTP and HTML

**HTTP** (Hypertext Transfer Protocol) → Enables web browsing  
**HTML** (Hypertext Markup Language) → The language web pages are written in  
**URL** (Uniform Resource Locator) → Address of web resources

```
URL Anatomy:
http://www.cisco.com/index.html
 │        │    │        │
 │        │    │        └── File name
 │        │    └─────────── Domain name
 │        └──────────────── Host name (machine name)
 └───────────────────────── Protocol (scheme)
```

---

### Web Page Loading Steps

```
STEP 1: Browser parses the URL
  http  →  www.cisco.com  →  index.html
  (protocol)  (server name)  (filename)

STEP 2: DNS query → IP resolution
  www.cisco.com → DNS → 198.133.219.25

STEP 3: Client sends a GET request
  GET /index.html HTTP/1.1
  Host: www.cisco.com
  User-Agent: Firefox/39.0
  Accept-Encoding: gzip

STEP 4: Server responds with 200 OK + HTML
  HTTP/1.1 200 OK
  Server: Apache/1.3.41
  Content-Type: text/html
  <html>...content...</html>

STEP 5: Browser parses HTML and renders the page
```

---

### HTTP Message Types

| Message | Usage |
|---------|-------|
| **GET** | Request a web page or resource |
| **POST** | Submit form data (registration, login) |
| **PUT** | Upload a file to the server (Google Drive, etc.) |
| **DELETE** | Delete a resource on the server |
| **HEAD** | Request only header information |

**HTTP Status Codes:**

```
2xx → Success
  200 OK            → Request successful, content follows

4xx → Client Error
  404 Not Found     → Page not found

5xx → Server Error
  503 Unavailable   → Service currently unavailable
  500 Server Error  → Internal server error
```

---

### HTTP vs HTTPS

```
HTTP (TCP 80)                HTTPS (TCP 443)
─────────────                ───────────────
❌ Unencrypted (plain text)  ✅ Encrypted with TLS
❌ Readable by Wireshark     ✅ Cannot be sniffed
❌ Insecure                  ✅ Today's standard
❌ Nearly obsolete           ✅ All modern websites use it
```

> 💡 Google ranks HTTPS websites higher in search results, which is why virtually all modern websites have migrated to HTTPS.

> 📄 RFC documentation: Official specifications that define how protocols work, published by the IETF. Example: RFC 2616 → HTTP protocol.

---

### Email Protocols

#### Email Flow Diagram:

```
SENDER                                    RECIPIENT
(ozan@gmail.com)                    (gokhan@yahoo.com)

[Mail Application]                    [Mail Application]
       │                                      ▲
       │ SMTP (sending)                       │ IMAP/POP3 (receiving)
       ▼                                      │
[Gmail                    SMTP           Yahoo
 Mail      ────────────────────────────▶ Mail
 Server]                                 Server]
                   INTERNET
```

---

### SMTP (Simple Mail Transfer Protocol)

**Function:** Used for sending email.

```
Ports:
  TCP 25   → Server-to-server (often blocked by ISPs)
  TCP 587  → Client → Server (TLS, recommended)
  TCP 465  → Client → Server (SSL)
```

> ⚠️ Many ISPs block TCP 25 due to spam. Use TCP 587 or TCP 465 for sending mail from client applications.

---

### POP3 vs IMAP

| Feature | POP3 (TCP 110/995) | IMAP (TCP 143/993) |
|---------|--------------------|--------------------|
| Core behavior | Download email, delete from server | Sync email, keep on server |
| Multiple devices | ❌ Not ideal | ✅ Excellent |
| Offline use | ✅ Local copies | ✅ Possible with caching |
| Server storage | ✅ Uses little | Uses more |
| Today's preference | ❌ Legacy | ✅ Standard |

```
IMAP synchronization:
  Yahoo Server
       │
       ├──────▶ Laptop (copy)
       ├──────▶ Smartphone (copy)
       └──────▶ Tablet (copy)

  Delete on any device → deleted on all devices
  Read on any device → marked as read everywhere
```

---

### Mail Client Configuration

```
Required settings in Outlook/Thunderbird:

  Display Name:            Any name (not verified)
  Email Address:           user@gmail.com

  Incoming mail server:
    Protocol:              IMAP (recommended) or POP3
    Server:                imap.gmail.com
    Port:                  993 (SSL/TLS)

  Outgoing mail server:
    Protocol:              SMTP
    Server:                smtp.gmail.com
    Port:                  587 (TLS) or 465 (SSL)

  Username:                user@gmail.com
  Password:                ••••••••
```

---

## 15.4 IP Address Services

### DNS (Domain Name System)

**Function:** Translates human-readable domain names (www.google.com) into numeric IP addresses.

```
www.cisco.com  ──DNS──▶  198.133.219.25
      │                         │
  Domain name              IP address
(readable by humans)    (used by machines)
```

#### DNS Record Types:

| Record | Description | Example |
|--------|-------------|---------|
| **A** | IPv4 address record | www.google.com → 8.1.1.1 |
| **AAAA** | IPv6 address record | www.google.com → 2001:db8::1 |
| **NS** | DNS name server record | google.com → ns1.google.com |
| **MX** | Mail exchange record | google.com → mx1.google.com |
| **CNAME** | Canonical name (alias) | example.com → www.example.com |

---

### DNS Hierarchy

```
              [ROOT DNS]
              (13 root servers)
                    │
         ┌──────────┼──────────┐
         ▼          ▼          ▼
      [.com]     [.org]      [.au]
    TLD Server  TLD Server  TLD Server
         │
         ▼
    [cisco.com]
    Authoritative Name Server
         │
    ┌────┼────┐
    ▼    ▼    ▼
  www  ftp  mail
```

#### How Resolution Works:

```
Scenario: Finding the IP of www.cisco.com

1. Client → Local DNS: "Where is www.cisco.com?"
2. Local DNS → Root Server: "Who manages .com?"
   Root → "6.1.1.1 manages it"
3. Local DNS → .com Server: "Who manages cisco.com?"
   .com → "7.1.1.1 manages it"
4. Local DNS → cisco.com Server: "What is the A record for www?"
   cisco.com → "198.133.219.25"
5. Local DNS → Client: "www.cisco.com = 198.133.219.25"
6. Client → initiates TCP connection to 198.133.219.25

NOTE: The local DNS CACHES this result.
      If the same query arrives again, it answers from cache.
```

---

### DNS Message Format

```
DNS Uses Two Message Types:

QUERY:                    RESPONSE:
┌─────────────┐           ┌─────────────────┐
│   Header    │           │     Header      │
├─────────────┤           ├─────────────────┤
│  Question   │           │    Question     │
│  Section    │           ├─────────────────┤
└─────────────┘           │  Answer Section │
                          ├─────────────────┤
                          │  Auth. Section  │
                          ├─────────────────┤
                          │  Additional     │
                          └─────────────────┘
```

---

### nslookup Command

```bash
# Basic DNS query
nslookup www.cisco.com

# Interactive mode queries
nslookup
> set type=A       → Query IPv4 records
> cisco.com

> set type=AAAA    → Query IPv6 records
> cisco.com

> set type=MX      → Query mail server record
> cisco.com

> set type=NS      → Query DNS server record
> cisco.com

> set type=CNAME   → Query canonical name (alias)
> www.hotmail.com
```

---

### DHCP (Dynamic Host Configuration Protocol)

**Function:** Automatically assigns IP configuration to devices joining the network.

**Static vs Dynamic addressing:**

```
Static IP:                    DHCP (Dynamic IP):
──────────                    ──────────────────
Manually entered:             Automatically received:
  IP: 192.168.1.10             IP: 192.168.1.201
  Subnet: 255.255.255.0        Subnet: 255.255.255.0
  Gateway: 192.168.1.1         Gateway: 192.168.1.1
  DNS: 8.8.8.8                 DNS: 8.8.8.8

Typically used for:           Typically used for:
Servers, routers, printers    User PCs, laptops, phones
```

---

### DHCP DORA Process

```
Client                               DHCP Server
   │                                      │
   │──DHCPDISCOVER (Broadcast)───────────▶│
   │  "Is there anyone who can give me    │
   │   an IP address?"                    │
   │  Source IP: 0.0.0.0                  │
   │  Dest IP: 255.255.255.255            │
   │  Source Port: 68, Dest Port: 67      │
   │                                      │
   │◀─DHCPOFFER (Unicast)─────────────────│
   │  "Would you like to use              │
   │   192.168.1.56?"                     │
   │                                      │
   │──DHCPREQUEST (Broadcast)────────────▶│
   │  "Yes, I want that IP!               │
   │   I'm broadcasting to inform all."   │
   │                                      │
   │◀─DHCPACK (Unicast)───────────────────│
   │  "IP reserved for you for 48 hours.  │
   │   You can use it!"                   │
   │                                      │
   ↓  IP usage begins                     │

DORA = Discover → Offer → Request → Acknowledge

Before lease expires (at half-time = 24 hours):
Client sends new DHCPREQUEST → DHCPACK renews the lease
```

**ipconfig /all output with DHCP info:**

```
DHCP Enabled......: Yes
IPv4 Address......: 10.64.98.119
Subnet Mask.......: 255.255.240.0
Default Gateway...: 10.64.0.1
DHCP Server.......: 10.11.0.20
DNS Servers.......: 8.8.8.8
Lease Obtained....: 10 August 2015
Lease Expires.....: 12 August 2015   ← 48-hour lease
```

> 💡 **DHCPv6 difference:** DHCPv6 does NOT provide the default gateway address! That information is obtained from Router Advertisement messages.

---

## 15.5 File Sharing Services

### FTP (File Transfer Protocol)

**Function:** Bidirectional file transfer between client and server.

```
FTP USES TWO CONNECTIONS:

Client                     Server
  │                           │
  │──TCP 21 (Control)────────▶│  ← Commands, filenames
  │                           │
  │──TCP 20 (Data)───────────▶│  ← Actual file data
  │◀──────────────────────────│    (opened for each transfer)

FTP client software: FileZilla, WinSCP, Qt FTP
FTP server software: vsftpd (Linux), FileZilla Server (Windows)
```

**FTP Use Cases:**
- Uploading web pages to a web server
- Downloading web page backups
- Loading operating systems onto network devices (TFTP often preferred)

> ⚠️ FTP is unencrypted! Secure alternative: **SFTP (TCP 22, over SSH)**

---

### TFTP (Trivial File Transfer Protocol)

**Function:** Simple, lightweight file transfer.

```
TFTP Characteristics:
  Port: UDP 69
  Protocol: UDP (connectionless)
  Acknowledgment: Yes (at application level)
  Encryption: None
  Authentication: None
  Use case: Loading IOS onto Cisco devices

TFTP Transfer Flow:
  Server → [Block 1] → Client → [ACK 1] → Server
  Server → [Block 2] → Client → [ACK 2] → Server
  ...
```

> 💡 Although TFTP uses UDP, it has its own acknowledgment mechanism. It is slow but simple.

---

### SMB / Samba (Server Message Block)

**Function:** Network-based file and printer sharing.

```
SMB Connection:
\\192.168.1.6 → username/password → files

Remote files appear as a local disk:
  ┌─────────────────────────────┐
  │  Shared Resources           │
  │  ├── Desktop                │
  │  ├── My Documents           │
  │  └── Local Disk (C:)        │
  │       ├── _Cisco            │
  │       └── _Contracts        │
  └─────────────────────────────┘

Three functions of SMB messages:
  1. Session start, authentication, and termination
  2. File and printer access control
  3. Application-to-application messaging

Port: TCP 445
```

> ⚠️ **Security Note:** SMB v1 had critical vulnerabilities exploited by the WannaCry ransomware. Always use **SMB v3** and disable SMB v1.

---

### Telnet vs SSH

| Parameter | SSH (TCP 22) | Telnet (TCP 23) |
|-----------|-------------|-----------------|
| **Security** | Highly secured | Less secured |
| **Data format** | Encrypted | Plain text |
| **Authentication** | Public key encryption | No mechanism |
| **Network type** | Suitable for public networks | Only private networks |
| **Vulnerabilities** | Overcomes Telnet's issues | Many vulnerabilities |
| **RFC** | RFC 4253 | RFC 854 |
| **Wireshark** | Unreadable | Everything visible |

> ❌ **NEVER use Telnet over the internet!** Usernames, passwords, and all commands are transmitted in clear text.  
> ✅ **Always use SSH** — this applies to Cisco devices too.

---

## 📊 Protocol Summary Diagram

```
              APPLICATION LAYER PROTOCOLS
                          │
       ┌──────────────────┼──────────────────┐
       ▼                  ▼                  ▼
      WEB              EMAIL           FILE/OTHER
   ─────────          ───────         ────────────
   HTTP (80)          SMTP (25)       FTP (21/20)
   HTTPS (443)        POP3 (110)      TFTP (UDP 69)
                      IMAP (143)      SMB (445)
                                      SSH (22)
                                      Telnet (23)
       ▼                  ▼                  ▼
   ─────────────────────────────────────────────
                  IP ADDRESSING
                  ─────────────
                  DNS (UDP 53)
                  DHCP (UDP 67/68)
```

---

## 📝 Module Summary

1. The **application layer** (L7) provides the user interface; the **presentation layer** (L6) handles data formatting/compression/encryption; the **session layer** (L5) tracks and manages sessions.
2. **Client/Server model:** The client requests a service; the server provides it — the most widely used model today.
3. **P2P networks:** Every device can act as both client and server; BitTorrent is the most common example.
4. **HTTP messages:** GET (request page), POST (submit form), PUT (upload file).
5. **Email trio:** SMTP for sending; POP3 for downloading (deletes from server); IMAP for synchronizing (keeps on server).
6. **DNS:** Resolves domain names to IPs; A, AAAA, MX, NS, CNAME record types; hierarchical structure.
7. **DHCP DORA:** Discover → Offer → Request → Acknowledge; lease time concept.
8. **FTP:** TCP 21 (control) + TCP 20 (data) — dual connections; insecure → prefer SFTP.
9. **SMB:** File/printer sharing, TCP 445; SMB v3 is secure, SMB v1 is dangerous.
10. **SSH vs Telnet:** Telnet is unencrypted, SSH is encrypted — always use SSH.

---

*Cisco CCNA1 · Introduction to Networks v7.0 · Module 15*  
*Gökhan AKIN – CCIE · gokhan@agyoneticileri.org*  
*Ozan BÜK – CCIE · ozan@agyoneticileri.org*
