# CCNA1 – Module 2: Basic Switch and End Device Configuration
> CCNA Introduction to Networks v7.0 (ITN) | Gökhan AKIN & Ozan BÜK – CCIE

---

## 📋 Module Objective
Apply initial settings on network switches and end devices, including **passwords, IP addressing, and default gateway** parameters.

| Topic | Objective |
|-------|-----------|
| Cisco IOS Access | Explain how to access a Cisco IOS device for configuration purposes |
| IOS Navigation | Explain how to navigate Cisco IOS to configure network devices |
| Command Structure | Describe the command structure of Cisco IOS software |
| Basic Device Configuration | Configure a Cisco IOS device using CLI |
| Save Configurations | Use IOS commands to save the running configuration |
| Ports and Addresses | Explain how devices communicate across network media |
| IP Addressing Configuration | Configure a host device with an IP address |
| Verify Connectivity | Verify connectivity between two end devices |

---

## 2.1 Cisco IOS Access

### Operating System Layers

Every computing device (PC, phone, router, switch) has a three-layer architecture:

```
┌─────────────────────────────────┐
│           S H E L L             │  ← User Interface (CLI / GUI)
├─────────────────────────────────┤
│           K E R N E L           │  ← Hardware ↔ Software bridge
├─────────────────────────────────┤
│         H A R D W A R E         │  ← Physical components
└─────────────────────────────────┘
```

| Layer | Role |
|-------|------|
| **Shell** | Provides CLI or GUI interface to the user |
| **Kernel** | Manages hardware resources; bridges hardware and software |
| **Hardware** | Physical parts: CPU, RAM, ports, etc. |

> 💡 **Why is the Kernel important?** The kernel operates at a very low level and isn't user-friendly. That's why the Shell is written on top of it.

---

### GUI vs CLI

| Feature | GUI | CLI |
|---------|-----|-----|
| **Full name** | Graphical User Interface | Command Line Interface |
| **Interaction** | Mouse + icons + windows | Keyboard + command line |
| **Ease of use** | Easy | Hard (at first) |
| **Preferred on network devices** | ❌ Rarely | ✅ Standard |
| **Examples** | Windows, macOS, Android | Terminal, PuTTY, Tera Term |

> ⚠️ GUIs can crash or behave unexpectedly. For this reason, enterprise network devices are managed via **CLI**.

---

### Access Methods

```
┌──────────────────────────────────────────────────────────┐
│               Access to a Cisco Device                   │
├────────────────┬─────────────────────┬───────────────────┤
│    CONSOLE     │        SSH          │      TELNET       │
│  (Physical)    │  (Secure/Remote)    │ (Insecure/Remote) │
├────────────────┼─────────────────────┼───────────────────┤
│ Console cable  │ Encrypted           │ Plaintext         │
│ RJ-45 → DB9    │ Recommended method  │ Deprecated        │
│ Requires phys. │ Requires IP         │ Requires IP       │
│ proximity      │ Port 22             │ Port 23           │
└────────────────┴─────────────────────┴───────────────────┘
```

**Console Connection Setup:**
1. Connect the blue console cable to the device's **Console port** (RJ-45 end)
2. Connect the other end (DB9/USB) to your PC
3. Modern PCs may require a USB → Serial adapter
4. Open a terminal emulator like **PuTTY** or **Tera Term**
5. Select **Serial** connection type and enter the COM number (check Device Manager)
6. Press Enter → device CLI appears

---

### Terminal Emulator Programs

| Program | License | SSH | Telnet | Serial | Notes |
|---------|---------|-----|--------|--------|-------|
| **PuTTY** | Free/Open | ✅ | ✅ | ✅ | Most widely used |
| **Tera Term** | Free/Open | ✅ | ✅ | ✅ | PuTTY alternative |
| **SecureCRT** | Paid | ✅ | ✅ | ✅ | Professional solution |

> ⚠️ **Security Warning:** Never use cracked terminal software! You're entering critical device passwords; the software must be trustworthy.

---

## 2.2 IOS Navigation

### Primary Command Modes

```
Connected to device
        │
        ▼
┌───────────────────┐
│   USER EXEC MODE  │  Switch>   or  Router>
│                   │  → Limited monitoring commands only
│     Symbol: >     │  → Cannot configure
└────────┬──────────┘
         │  enable
         ▼
┌───────────────────┐
│ PRIVILEGED EXEC   │  Switch#   or  Router#
│      MODE         │  → Full access to all commands
│     Symbol: #     │  → show, debug, copy, etc.
└────────┬──────────┘
         │  configure terminal (conf t)
         ▼
┌───────────────────┐
│  GLOBAL CONFIG    │  Switch(config)#
│      MODE         │  → General device configuration
│  (config)#        │  → hostname, password, etc.
└────────┬──────────┘
         │
    ┌────┴─────┐
    ▼          ▼
┌────────┐  ┌──────────┐
│  LINE  │  │INTERFACE │
│CONFIG  │  │  CONFIG  │
│(config │  │(config   │
│-line)# │  │-if)#     │
└────────┘  └──────────┘
```

### Mode Navigation Commands

| Transition | Command |
|------------|---------|
| User EXEC → Privileged EXEC | `enable` |
| Privileged EXEC → User EXEC | `disable` |
| Privileged EXEC → Global Config | `configure terminal` (short: `conf t`) |
| Global Config → Privileged EXEC | `exit` |
| Sub-mode → Global Config | `exit` |
| Any mode → Privileged EXEC (fast) | `end` or `Ctrl+Z` |
| Exit device | `exit` (from User EXEC) |

### Understanding the Prompt

```
Switch(config-if)#
│      │          │
│      │          └── Mode indicator (# = Privileged or sub-mode)
│      └─────────── Current sub-mode
└──────────────── Device name (hostname)
```

---

## 2.3 Command Structure

### Basic IOS Command Structure

```
Switch> show    ip    protocols
│       │       │     │
│       │       │     └── Keyword – predefined in OS
│       │       └──────── Space
│       └────────────── Command
└────────────────────── Prompt
```

**Keyword vs Argument:**
- **Keyword** → A specific parameter predefined in the OS (e.g., `ip protocols`)
- **Argument** → A user-defined variable (e.g., `192.168.10.5`)

### Command Syntax Conventions

| Notation | Meaning |
|----------|---------|
| **boldface** | Commands and keywords – enter exactly as shown |
| *italics* | Argument – user provides the value |
| `[x]` | Square brackets → Optional element |
| `{x}` | Curly braces → Required element (must enter one) |
| `[x {y \| z}]` | Required choice within optional element |
| `x \| y` | Pipe → "or" (choose one) |

---

### IOS Help Features

#### Context-Sensitive Help

```bash
Switch> ?                    # Lists all commands in current mode
Switch> c?                   # Shows commands starting with 'c'
Switch> clock ?              # Shows parameters for 'clock' command
Switch> clock set ?          # Shows sub-parameters for 'set'
```

#### Error Messages

| Error | Meaning | Solution |
|-------|---------|---------|
| `% Ambiguous command` | Multiple commands match | Type more characters |
| `% Incomplete command` | Missing required argument | Use `?` to see sub-params |
| `% Invalid input detected at '^' marker` | Error starts at `^` | Fix that portion |

---

### Keyboard Shortcuts

| Key | Function |
|-----|---------|
| `Tab` | Completes partial command (if unique) |
| `Backspace` | Deletes character to the left |
| `↑` or `Ctrl+P` | Recalls previous command |
| `↓` or `Ctrl+N` | Recalls next command |
| `←` or `Ctrl+B` | Moves cursor one character left |
| `→` or `Ctrl+F` | Moves cursor one character right |
| `Ctrl+C` | Exits configuration mode / aborts operation |
| `Ctrl+Z` | Returns to Privileged EXEC from any config mode |
| `Ctrl+Shift+6` | Interrupts DNS lookups, pings, traceroutes |

**When `--More--` is displayed:**
- `Space` → Display next page
- `Enter` → Display next line
- Any other key → Exit output

---

## 2.4 Basic Device Configuration

### Setting the Device Name (Hostname)

```bash
Switch# configure terminal
Switch(config)# hostname Sw-Floor-1
Sw-Floor-1(config)#
```

**Hostname Rules:**
- Must begin with a letter
- Must not contain spaces
- Must end with a letter or digit
- Only letters, digits, and hyphens are allowed
- Maximum 64 characters (keep it short in practice!)
- To reset: `no hostname`

> 💡 **Best Practice:** Use names that indicate location and function. E.g., `Core-SW-Floor1`, `Access-SW-B2-K3`

---

### Password Configuration

Cisco devices prompt for a password at **two different points**:

```
Initial connection (Console/Telnet/SSH)
              │
              ▼
   [ Console / VTY Password ]   ← line console 0 / line vty 0 15
              │
              ▼
         User EXEC >
              │
          enable command
              │
              ▼
    [ Enable Secret Password ]  ← enable secret
              │
              ▼
       Privileged EXEC #
```

#### Console Password

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line console 0
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

#### Privileged EXEC Password

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# enable secret class
Sw-Floor-1(config)# exit
```

#### VTY (Virtual Terminal – Telnet/SSH) Password

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line vty 0 15
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

> 📝 **VTY 0 15** → Allows 16 simultaneous remote connections (0 to 15 = 16 channels)

#### Password Security Guidelines

| Rule | Recommendation |
|------|---------------|
| Length | Minimum 8 characters |
| Complexity | Mix of uppercase/lowercase + numbers + special chars |
| Uniqueness | Different password for each device |
| Dictionary words | Avoid (vulnerable to dictionary attacks) |
| Lab environment | Simple passwords like `cisco`, `class` are acceptable (never in production!) |

---

### Password Encryption

By default, `show running-config` displays passwords as **plaintext**. To prevent this:

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# service password-encryption
Sw-Floor-1(config)# exit
```

**Verification:**
```
line con 0
 password 7 094F471A1A0A    ← Now encrypted
 login
Line vty 0 4
 Password 7 03095A0F034F38435B49150A1819
 Login
```

> ⚠️ `enable secret` is already stored with MD5 encryption. `service password-encryption` encrypts other passwords (console, vty).

---

### Banner (Login Warning Message)

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# banner motd #
Authorized access only. All activity is monitored.
Unauthorized access is prohibited and will be prosecuted.
#
```

**How it works:**
- `banner motd` → Message Of The Day
- `#` → Delimiter character – used before and after the message
- Don't use `#` inside the message text! Choose another character (e.g., `$`, `%`)

> ⚖️ **Legal Note:**
> - ❌ Don't write "Welcome" → You may be considered to have invited intruders
> - ❌ Don't write threats → May be legally problematic
> - ✅ Write neutral warnings like "Unauthorized access is prohibited and subject to legal action"

---

## 2.5 Save Configurations

### Configuration Files

```
┌──────────────────────────────────────────────────┐
│               DEVICE MEMORY                      │
│                                                  │
│  ┌─────────────────┐      ┌──────────────────┐   │
│  │  running-config │      │  startup-config  │   │
│  │    (in RAM)     │      │   (in NVRAM)     │   │
│  │                 │      │                  │   │
│  │ • Active config │ ───► │ • Persistent cfg │   │
│  │ • Changes immed.│ copy │ • Loaded at boot │   │
│  │ • Lost on power │      │ • Non-volatile   │   │
│  │   loss!         │      │   storage        │   │
│  └─────────────────┘      └──────────────────┘   │
└──────────────────────────────────────────────────┘
```

| File | Storage | Characteristic |
|------|---------|---------------|
| **running-config** | RAM | Current active config; lost when device powers off |
| **startup-config** | NVRAM (Flash) | Persistent; loaded on every boot |

### Saving Configuration

```bash
# Method 1: Standard (Recommended)
Router# copy running-config startup-config

# Method 2: Shortcut (wr = write)
Router# write

# Method 3: Full syntax
Router# write memory
```

### Viewing Configurations

```bash
Router# show running-config     # Show active configuration
Router# show startup-config     # Show saved configuration
```

### Resetting Configuration

```bash
Router# erase startup-config    # Delete startup config
Router# reload                  # Restart the device (confirmation required)
```

> ⚠️ The **reload** command should be used with extreme caution on critical devices! A restart can take up to 10 minutes and drops all network connections.

### Logging Sessions with PuTTY

1. PuTTY → **Change Settings** → **Session** → **Logging**
2. Select **All session output**
3. Specify a filename and location (e.g., `SwitchLog.txt`)
4. Click **Apply** → All terminal output is now written to file
5. When done → Select **None** to stop logging

---

## 2.6 Ports and Addresses

### IP Addresses

#### IPv4

```
192  .  168  .  1   .  10
 │       │       │      │
 └───────┴───────┴──────┘
         32 bits
   4 octets × 8 bits each
   Each octet: 0–255

Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1   ← Router's IP
```

#### IPv6

```
2001:0db8:acad:0010:0000:0000:0000:0001
└───┘└───┘└───┘└───┘└───────────────┘
 128 bits = 32 hexadecimal characters
 8 groups × 16 bits, separated by colons
 Case-insensitive
```

### Network Media Types

```
┌──────────────────────────────────────────────────────┐
│                  NETWORK MEDIA                       │
├──────────────┬──────────────┬───────────────────────┤
│  COPPER      │  FIBER OPTIC │      WIRELESS          │
│              │              │                        │
├──────────────┼──────────────┼───────────────────────┤
│ • UTP/STP    │ • Single-mode│ • Wi-Fi (802.11)      │
│ • RJ-45      │ • Multi-mode │ • Bluetooth            │
│ • 100m limit │ • Kilometers │ • Cellular             │
│ • Inexpensive│ • Expensive  │ • Flexible             │
│ • EMI suscept│ • EMI immune │ • Security risk        │
└──────────────┴──────────────┴───────────────────────┘
```

---

## 2.7 IP Addressing Configuration

### Assigning IP Addresses to End Devices

#### Manual (Static) – Windows PC

**Control Panel > Network and Sharing Center > Change adapter settings > Right-click > Properties > IPv4**

| Field | Example Value |
|-------|--------------|
| IP Address | 192.168.1.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |
| DNS Server | 8.8.8.8 |

#### Automatic (DHCP)

- Select **Obtain an IP address automatically**
- DHCP server automatically assigns IP, mask, gateway, and DNS
- IPv6: Uses **DHCPv6** or **SLAAC** (Stateless Address Autoconfiguration)

---

### Assigning IP Address to a Switch (SVI – Switch Virtual Interface)

> 📌 **Why SVI?** Switch physical ports operate at Layer 2 (MAC address-based). An IP address requires Layer 3. Therefore, the IP is assigned to the **VLAN 1** virtual interface.

```
Physical Port (Layer 2 – works with MAC addresses)
       │
       ▼
  SVI - interface VLAN 1  (Layer 3 – can have IP)
       │
       ▼
  IP address for remote management
```

**Configuration:**

```bash
Switch# configure terminal
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.20 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.1.1
Switch(config)# end
Switch# copy running-config startup-config
```

> ⚠️ Layer 3 interfaces are **administratively shut down** by default. Use `no shutdown` to enable them.

---

## 2.8 Verify Connectivity

### Basic Verification Commands

```bash
# Show brief status of all interfaces
Switch# show ip interface brief

# Show running configuration
Switch# show running-config

# Show system clock
Switch# show clock

# Show device version and hardware info
Switch# show version
```

### Interpreting `show ip interface brief` Output

```
Interface     IP-Address    OK? Method  Status             Protocol
Vlan1         192.168.1.20  YES manual  up                 up       ← Working normally
Vlan1         unassigned    YES unset   up                 down     ← No IP or problem
Vlan1         192.168.1.20  YES manual  administratively   down     ← shutdown applied
```

| Status | Protocol | Meaning |
|--------|----------|---------|
| up | up | ✅ Working correctly |
| up | down | ⚠️ Layer 2 issue |
| administratively down | down | 🔴 shutdown command applied |

### Testing Connectivity with Ping

```bash
Switch# ping 192.168.1.1
!!!!!                    ← 5/5 successful
.....                    ← 5/5 failed (timeout)
```

---

## 📝 Basic Switch Configuration Summary (Cheat Sheet)

```bash
! 1. Enter device and go to Privileged mode
Switch> enable

! 2. Enter Config mode
Switch# configure terminal

! 3. Set hostname
Switch(config)# hostname Sw-Floor-1

! 4. Enable password
Sw-Floor-1(config)# enable secret SecurePass123!

! 5. Console password
Sw-Floor-1(config)# line console 0
Sw-Floor-1(config-line)# password ConsolePass1!
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# exit

! 6. VTY (remote access) password
Sw-Floor-1(config)# line vty 0 15
Sw-Floor-1(config-line)# password VtyPass1!
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# exit

! 7. Encrypt all passwords
Sw-Floor-1(config)# service password-encryption

! 8. Warning banner
Sw-Floor-1(config)# banner motd #
Authorized access only. Unauthorized access is prohibited!
#

! 9. Assign IP address (via SVI)
Sw-Floor-1(config)# interface vlan 1
Sw-Floor-1(config-if)# ip address 192.168.1.20 255.255.255.0
Sw-Floor-1(config-if)# no shutdown
Sw-Floor-1(config-if)# exit

! 10. Set default gateway
Sw-Floor-1(config)# ip default-gateway 192.168.1.1

! 11. Return to Privileged mode and save
Sw-Floor-1(config)# end
Sw-Floor-1# copy running-config startup-config
```

---

## 🔑 Key Reminders

- **`enable secret`** → More secure than `enable password` (uses MD5 hashing)
- **`no` prefix** → Removes any command / returns to default
- **RAM** (running-config) → Fast but volatile; lost when device powers off
- **NVRAM** (startup-config) → Persistent; loaded on every boot
- **SVI** → Virtual Layer 3 interface used for remote switch management
- **`Ctrl+Shift+6`** → Interrupt stuck commands (DNS lookup, long pings)
- **`conf t`** = `configure terminal` → Cisco accepts abbreviations (if unique)

---

*CCNA1 ITN v7.0 – Module 2 | agyoneticileri.org*
