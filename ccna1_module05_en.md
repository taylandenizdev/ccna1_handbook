# 🔢 Module 5: Number Systems
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 Table of Contents

- [Why Number Systems?](#-why-number-systems)
- [Overview of Three Number Systems](#-overview-of-three-number-systems)
- [Decimal Number System](#-decimal-number-system)
- [Binary Number System](#-binary-number-system)
- [IPv4 Addresses and Binary](#-ipv4-addresses-and-binary)
- [Hexadecimal Number System](#-hexadecimal-number-system)
- [MAC Addresses and Hexadecimal](#-mac-addresses-and-hexadecimal)
- [IPv6 Addresses and Hexadecimal](#-ipv6-addresses-and-hexadecimal)
- [Conversion Methods](#-conversion-methods)
- [Practice Exercises](#-practice-exercises)
- [Summary Table](#-summary-table)

---

## 🌐 Why Number Systems?

**3 different number systems** are used in network communications:

```
┌─────────────────────────────────────────────────────────────┐
│  WHERE ARE THEY USED?                                       │
├────────────────┬───────────────────┬────────────────────────┤
│  System        │  Used For         │  Example               │
├────────────────┼───────────────────┼────────────────────────┤
│  Decimal (10)  │  IP Addresses     │  192.168.1.10          │
│                │  Port Numbers     │  443, 80, 25           │
├────────────────┼───────────────────┼────────────────────────┤
│  Binary (2)    │  Internal         │  11000000.10101000...  │
│                │  computer comms   │  (actual IP structure) │
├────────────────┼───────────────────┼────────────────────────┤
│  Hex (16)      │  MAC Addresses    │  01:5F:6A:27:3C:D9     │
│                │  IPv6 Addresses   │  2001:0DB8:AAAA::0100  │
└────────────────┴───────────────────┴────────────────────────┘
```

> 💡 **Key concept:** Computers only understand **binary**. Humans use **decimal**. **Hexadecimal** is used to shorten long binary numbers.

---

## 📐 Overview of Three Number Systems

```
┌──────────────┬──────────┬────────────────────────────────────┐
│  System      │  Base    │  Symbols Used                      │
├──────────────┼──────────┼────────────────────────────────────┤
│  Decimal     │  10      │  0, 1, 2, 3, 4, 5, 6, 7, 8, 9     │
├──────────────┼──────────┼────────────────────────────────────┤
│  Binary      │  2       │  0, 1                              │
├──────────────┼──────────┼────────────────────────────────────┤
│  Hexadecimal │  16      │  0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F  │
└──────────────┴──────────┴────────────────────────────────────┘
```

---

## 🔟 Decimal Number System

### Positional Notation

Each digit's value increases as **powers of 10**:

```
Number: 1 2 3 4
        │ │ │ └── 4 × 10⁰ = 4 × 1    =     4
        │ │ └──── 3 × 10¹ = 3 × 10   =    30
        │ └────── 2 × 10² = 2 × 100  =   200
        └──────── 1 × 10³ = 1 × 1000 =  1000
                                         ─────
                                         1234  ✓
```

| Position | Thousands | Hundreds | Tens | Ones |
|----------|-----------|----------|------|------|
| 10^n | 10³ | 10² | 10¹ | 10⁰ |
| Value | 1000 | 100 | 10 | 1 |

---

## 💻 Binary Number System

### 8-Bit Positional Values — Memorize This!

```
┌─────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│ 128 │  64 │  32 │  16 │   8 │   4 │   2 │   1 │
└─────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘
  2⁷    2⁶    2⁵    2⁴    2³    2²    2¹    2⁰
```

### Minimum and Maximum Values

```
00000000  →  0    (all bits 0)
11111111  →  128+64+32+16+8+4+2+1 = 255  (all bits 1)
```

> 📌 **Each octet holds a value between 0 and 255!**

### Binary → Decimal Conversion

Add up the values of all the positions that have a 1:

```
Example 1:  1 0 0 0 0 0 0 0
            │
            └── 128 position is set → 128

Example 2:  1 1 0 0 0 0 0 0
            │ │
            │ └── 64 position is set → 64
            └──── 128 position is set → 128
                                        ───
                                        192  ✓

Example 3:  0 1 0 1 1 0 0 1
                │   │ │     │
                │   │ │     └── 1's → 1
                │   │ └──────── 8's → 8
                │   └────────── 16's → 16
                └────────────── 64's → 64
                                       ──
                                       89  ✓
```

### Quick Calculation Trick

When you see consecutive 1s, subtract 1 from the next higher position:

```
0 0 0 1 1 1 1 1
        └──────── 5 consecutive 1s
                  → 32 position is zero
                  → 32 - 1 = 31  ✓

1 1 1 1 1 1 1 1  → 256 - 1 = 255  ✓
0 1 1 1 1 1 1 1  → 128 - 1 = 127  ✓
0 0 1 1 1 1 1 1  →  64 - 1 = 63   ✓
```

### Decimal → Binary Conversion

Start from the largest position, place if it fits, subtract:

```
Convert 168 to binary:

Step 1: 168 ≥ 128? YES → bit=1, remainder: 168-128 = 40
Step 2:  40 ≥  64? NO  → bit=0
Step 3:  40 ≥  32? YES → bit=1, remainder:  40-32  = 8
Step 4:   8 ≥  16? NO  → bit=0
Step 5:   8 ≥   8? YES → bit=1, remainder:   8-8   = 0
Step 6:   0 ≥   4? NO  → bit=0
Step 7:   0 ≥   2? NO  → bit=0
Step 8:   0 ≥   1? NO  → bit=0

Result:  1 0 1 0 1 0 0 0  =  168  ✓
```

**Flowchart:**

```
    Start: n = decimal number
          │
          ▼
    ┌─────────────┐
    │ n ≥ 128?    │──YES──► bit=1, n = n-128
    └──────┬──────┘
           │NO
           ▼
         bit=0
           │
           ▼
    ┌─────────────┐
    │ n ≥ 64?     │──YES──► bit=1, n = n-64
    └──────┬──────┘
           │NO
           ▼
         bit=0
           │
           ▼
    ... (continue for 32, 16, 8, 4, 2, 1)
```

---

## 🌍 IPv4 Addresses and Binary

### IPv4 Structure

```
IPv4 Address: 192    .   168    .    11    .    10
              │              │         │          │
              ▼              ▼         ▼          ▼
Binary:   11000000    10101000    00001011    00001010
          └──────┘    └──────┘    └──────┘    └──────┘
           Octet 1     Octet 2     Octet 3     Octet 4
           8 bits      8 bits      8 bits      8 bits
                    └──────────────────────────┘
                           32 bits total
```

**Dotted Decimal Notation:**

```
11000000.10101000.00001011.00001010
         ↓ ↓ ↓ ↓
       192.168.11.10
```

Computers work with 32-bit binary internally. For human readability, we split into 4 octets and convert to decimal.

### IPv4 Conversion Example (Binary → Decimal)

```
11000000.10101000.00001011.00001010

Octet 1:  1 1 0 0 0 0 0 0
          128+64 = 192

Octet 2:  1 0 1 0 1 0 0 0
          128+32+8 = 168

Octet 3:  0 0 0 0 1 0 1 1
          8+2+1 = 11

Octet 4:  0 0 0 0 1 0 1 0
          8+2 = 10

Result: 192.168.11.10 ✓
```

### Quick Reference Table

| Binary | Decimal |
|--------|---------|
| `00000000` | 0 |
| `00000011` | ? |
| `01011001` | 89 |
| `11100000` | ? |
| `00011111` | ? |
| `11111111` | 255 |

> **Answers:** 3, 224, 31

---

## 🔡 Hexadecimal Number System

### Why Hexadecimal?

Represents 4 bits with a single symbol → MAC and IPv6 addresses become much shorter!

```
Binary:       1010  1000
              ↓       ↓
Hex:           A       8   →   A8
```

### Binary – Decimal – Hexadecimal Table

| Binary | Decimal | Hex |   | Binary | Decimal | Hex |
|--------|---------|-----|---|--------|---------|-----|
| 0000 | 0 | **0** | | 1000 | 8 | **8** |
| 0001 | 1 | **1** | | 1001 | 9 | **9** |
| 0010 | 2 | **2** | | 1010 | 10 | **A** |
| 0011 | 3 | **3** | | 1011 | 11 | **B** |
| 0100 | 4 | **4** | | 1100 | 12 | **C** |
| 0101 | 5 | **5** | | 1101 | 13 | **D** |
| 0110 | 6 | **6** | | 1110 | 14 | **E** |
| 0111 | 7 | **7** | | 1111 | 15 | **F** |

> 💡 **Memory trick:** 0-9 stay the same, 10=A, 11=B, 12=C, 13=D, 14=E, 15=F

### 4-Bit Positional Values

```
┌─────┬─────┬─────┬─────┐
│  8  │  4  │  2  │  1  │
└─────┴─────┴─────┴─────┘
  2³    2²    2¹    2⁰
```

### Binary → Hex Conversion

Group in 4-bit chunks, convert each group to a hex symbol:

```
Binary:    1 0 1 0  |  1 0 0 0
           └──┬──┘     └──┬──┘
              │            │
              A             8
Hex:        A8  ✓
```

**Longer example:**

```
1000 0011 0111 1001 1010 0001 0000 0011 1111 1110
 8    3    7    9    A    1    0    3    F    E
→  8379 A103 FE
```

### Hex → Binary Conversion

Convert each hex symbol to 4 bits:

```
Hex:   A    B
       │    │
       ▼    ▼
    1010  1011

AB (hex) = 1010 1011 (binary)  ✓
```

---

## 🖧 MAC Addresses and Hexadecimal

### MAC Address Structure

```
MAC Address: 01  :  5F  :  6A  :  27  :  3C  :  D9
             │       │       │       │       │       │
             └───────┴───────┘       └───────┴───────┘
                   OUI                   Device ID
             (Manufacturer Code)       (Device Code)

Total: 48 bits = 12 hex characters
```

### Binary → MAC Address Conversion

```
48-bit MAC:
0001 0000 : 0010 0000 : 0011 0000 : 0100 0000 : 0101 0000 : 0110 0000
  1    0       2    0       3    0       4    0       5    0       6    0
   10      :    20      :    30      :    40      :    50      :    60
```

> 📌 **Note:** MAC addresses are grouped in 8-bit octets separated by ":", consisting of **12 hexadecimal characters**.

---

## 🌐 IPv6 Addresses and Hexadecimal

### IPv6 Structure

```
IPv6:  2001 : 0DB8 : AAAA : 1111 : 0000 : 0000 : 0000 : 0100
        │       │       │       │       │       │       │       │
       16b     16b     16b     16b     16b     16b     16b     16b
      Hextet  Hextet  Hextet  Hextet  Hextet  Hextet  Hextet  Hextet
        1       2       3       4       5       6       7       8

Total: 8 × 16 bits = 128 bits
```

### IPv4 vs IPv6 vs MAC Comparison

```
┌─────────────┬──────────┬────────────┬───────────────────┐
│  Address    │  Bits    │  Notation  │  Example          │
├─────────────┼──────────┼────────────┼───────────────────┤
│  IPv4       │  32 bit  │  Decimal   │  192.168.10.10    │
│             │  4 octets│  (4 fields)│                   │
├─────────────┼──────────┼────────────┼───────────────────┤
│  MAC        │  48 bit  │  Hex       │  10:20:30:40:50:60│
│             │  6 octets│  (12 chars)│                   │
├─────────────┼──────────┼────────────┼───────────────────┤
│  IPv6       │  128 bit │  Hex       │  2001:0DB8::0100  │
│             │  8 hextet│  (32 chars)│                   │
└─────────────┴──────────┴────────────┴───────────────────┘
```

### Each Hextet's Value Range

```
Each hextet is 16 bits:
  Minimum: 0000  →  0
  Maximum: FFFF  →  65535

8 hextets × 16 bits = 128 bits
```

---

## 🔄 Conversion Methods

### All Conversion Paths

```
                    ┌─────────┐
                    │ DECIMAL │
                    └────┬────┘
                    ↗    │    ↘
              ↗          │         ↘
        ┌──────┐    via binary    ┌──────┐
        │BINARY│◄──────────────►│  HEX │
        └──────┘                └──────┘

Easiest path: Decimal ↔ Binary ↔ Hex
Direct Decimal ↔ Hex: Go through Binary!
```

### Decimal → Binary Steps

```
1. Start at 128
2. Is n ≥ positional value? → write 1, subtract
3. Is n < positional value? → write 0, move on
4. Repeat down to 1
```

### Binary → Decimal Steps

```
1. Identify all positions that have a 1
2. Add up their positional values
```

### Decimal → Hex Steps

```
Step 1: Convert decimal to binary  (using 128 64 32 16 8 4 2 1)
Step 2: Split binary into 4-bit groups  (right to left)
Step 3: Convert each 4-bit group to hex symbol  (using 8 4 2 1 table)
```

### Hex → Decimal Steps

```
Step 1: Convert each hex symbol to 4-bit binary
Step 2: Form 8-bit groups
Step 3: Convert each 8-bit group to decimal
```

### Full Example: 168 → Hex

```
168 (decimal)
    │
    ▼ [Decimal → Binary]
10101000 (binary)
    │
    ▼ [Split into 4-bit groups]
1010 | 1000
    │
    ▼ [Each 4 bits → Hex]
 A  |  8
    │
    ▼
  A8 (hex)  ✓
```

### Full Example: D2 (hex) → Decimal

```
D2 (hex)
    │
    ▼ [Each hex → 4 bits]
1101 | 0010
    │
    ▼ [Combine]
11010010 (binary)
    │
    ▼ [Binary → Decimal]
128+64+16+2 = 210 (decimal)  ✓
```

---

## 📝 Practice Exercises

### 1. Binary → Decimal

```
Positional values: 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1

a) 10000000 = ?
b) 11000000 = ?
c) 10101000 = ?
d) 00001011 = ?
e) 11111110 = ?
f) 01111111 = ?
```

**Answers:** a)128  b)192  c)168  d)11  e)254  f)127

### 2. Decimal → Binary

```
a) 10  = ?
b) 192 = ?
c) 255 = ?
d) 128 = ?
e) 127 = ?
f) 224 = ?
```

**Answers:**
```
a) 00001010   b) 11000000   c) 11111111
d) 10000000   e) 01111111   f) 11100000
```

### 3. IPv4 Binary → Decimal

```
11000000.10101000.00001010.00001010  = ?
```

**Answer:** 192.168.10.10

### 4. Binary → Hex

```
a) 1010 = ?
b) 1111 = ?
c) 1101 0010 = ?
d) 1010 1011 = ?
```

**Answers:** a)A  b)F  c)D2  d)AB

### 5. Hex → Decimal

```
a) A8  = ?
b) FF  = ?
c) D2  = ?
d) 1F  = ?
```

**Answers:** a)168  b)255  c)210  d)31

---

## 📊 Summary Table

### Number Systems Comparison

| Feature | Decimal | Binary | Hexadecimal |
|---------|---------|--------|-------------|
| **Base** | 10 | 2 | 16 |
| **Symbols** | 0-9 | 0, 1 | 0-9, A-F |
| **Network Use** | IP addresses, ports | Computer communication | MAC, IPv6 |
| **1 hex = how many bits** | — | — | 4 bits |
| **8-bit group name** | — | — | Octet |
| **16-bit group name** | — | — | Hextet |

### Address Size Summary

```
┌──────────────────────────────────────────────────────────┐
│                    ADDRESS SIZES                         │
├──────────────┬──────────┬────────────┬───────────────────┤
│  Address     │  Bits    │  Group     │  Notation         │
├──────────────┼──────────┼────────────┼───────────────────┤
│  IPv4        │  32 bits │  4 × Octet │  Dotted Decimal   │
│  MAC         │  48 bits │  6 × Octet │  Hex (12 chars)   │
│  IPv6        │  128 bits│  8 × Hextet│  Hex (32 chars)   │
└──────────────┴──────────┴────────────┴───────────────────┘
```

### 8-Bit Positional Values — Flash Card

```
┌───────────────────────────────────────────────────┐
│  128  │  64  │  32  │  16  │  8  │  4  │  2  │  1 │
│  2⁷   │  2⁶  │  2⁵  │  2⁴  │ 2³  │ 2²  │ 2¹  │ 2⁰ │
└───────────────────────────────────────────────────┘
Minimum: 0 (00000000)    Maximum: 255 (11111111)
```

### 4-Bit Positional Values (for Hex) — Flash Card

```
┌─────┬─────┬─────┬─────┐
│  8  │  4  │  2  │  1  │
└─────┴─────┴─────┴─────┘
Minimum: 0 (0000)    Maximum: F = 15 (1111)
```

---

## 🎮 Binary Game

Cisco's fun binary learning game:

> 🔗 **https://learningnetwork.cisco.com/s/binary-game**

A Tetris-style game: numbers fall from above, you need to build the correct binary value!

---

## 📝 Key Terms Glossary

| Term | Definition |
|------|-----------|
| **Bit** | Binary digit — 0 or 1 |
| **Byte** | 8 bits |
| **Octet** | 8-bit group (used in IPv4) |
| **Hextet** | 16-bit group (used in IPv6) |
| **Binary** | Base-2 number system (0 and 1) |
| **Decimal** | Base-10 number system (0-9) |
| **Hexadecimal** | Base-16 number system (0-9, A-F) |
| **Dotted Decimal** | IPv4 address in dotted decimal notation |
| **Positional Notation** | Each digit's value depends on its position |
| **MSB** | Most Significant Bit — leftmost (highest value) bit |
| **LSB** | Least Significant Bit — rightmost (lowest value) bit |
| **Subnetting** | Dividing IP addresses into sub-networks (requires binary knowledge!) |

---

*📚 CCNA1 – Introduction to Networks v7.0 | Module 5: Number Systems*
