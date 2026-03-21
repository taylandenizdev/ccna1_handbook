# 🔢 Modül 5: Sayı Sistemleri
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 İçindekiler

- [Neden Sayı Sistemleri?](#-neden-sayı-sistemleri)
- [Üç Sayı Sistemine Genel Bakış](#-üç-sayı-sistemine-genel-bakış)
- [Onluk Sayı Sistemi](#-onluk-sayı-sistemi-decimal)
- [İkilik Sayı Sistemi](#-i̇kilik-sayı-sistemi-binary)
- [IPv4 Adresleri ve Binary](#-ipv4-adresleri-ve-binary)
- [Hexadecimal Sayı Sistemi](#-hexadecimal-sayı-sistemi)
- [MAC Adresleri ve Hexadecimal](#-mac-adresleri-ve-hexadecimal)
- [IPv6 Adresleri ve Hexadecimal](#-ipv6-adresleri-ve-hexadecimal)
- [Dönüşüm Yöntemleri](#-dönüşüm-yöntemleri)
- [Pratik Alıştırmalar](#-pratik-alıştırmalar)
- [Özet Tablosu](#-özet-tablosu)

---

## 🌐 Neden Sayı Sistemleri?

Network haberleşmesinde **3 farklı sayı sistemi** kullanılır:

```
┌─────────────────────────────────────────────────────────────┐
│  NEREDE KULLANILIR?                                        │
├────────────────┬───────────────────┬────────────────────────┤
│  Sayı Sistemi  │  Kullanım Yeri    │  Örnek                 │
├────────────────┼───────────────────┼────────────────────────┤
│  Onluk (10)   │  IP Adresleri     │  192.168.1.10          │
│               │  Port Numaraları  │  443, 80, 25           │
├────────────────┼───────────────────┼────────────────────────┤
│  İkilik (2)   │  Bilgisayar iç    │  11000000.10101000...  │
│               │  iletişimi        │  (gerçek IP yapısı)    │
├────────────────┼───────────────────┼────────────────────────┤
│  Hex (16)     │  MAC Adresleri    │  01:5F:6A:27:3C:D9     │
│               │  IPv6 Adresleri   │  2001:0DB8:AAAA::0100  │
└────────────────┴───────────────────┴────────────────────────┘
```

> 💡 **Ana fikir:** Bilgisayarlar sadece **ikilik** sistemi anlar. Biz insanlar **onluk** sistemi kullanırız. **Hexadecimal** ise uzun binary sayıları kısaltmak için kullanılır.

---

## 📐 Üç Sayı Sistemine Genel Bakış

```
┌──────────────┬──────────┬────────────────────────────────────┐
│  Sistem      │  Taban   │  Kullandığı Semboller               │
├──────────────┼──────────┼────────────────────────────────────┤
│  Onluk       │  10      │  0, 1, 2, 3, 4, 5, 6, 7, 8, 9     │
│  (Decimal)   │          │                                    │
├──────────────┼──────────┼────────────────────────────────────┤
│  İkilik      │  2       │  0, 1                              │
│  (Binary)    │          │                                    │
├──────────────┼──────────┼────────────────────────────────────┤
│  Hex         │  16      │  0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F  │
│  (Hex.)      │          │                                    │
└──────────────┴──────────┴────────────────────────────────────┘
```

---

## 🔟 Onluk Sayı Sistemi (Decimal)

### Konumsal Gösterim

Her basamağın değeri **10'un katları** şeklinde artar:

```
Sayı: 1 2 3 4
       │ │ │ └── 4 × 10⁰ = 4 × 1    =     4
       │ │ └──── 3 × 10¹ = 3 × 10   =    30
       │ └────── 2 × 10² = 2 × 100  =   200
       └──────── 1 × 10³ = 1 × 1000 =  1000
                                       ─────
                                       1234  ✓
```

| Basamak | Binler | Yüzler | Onlar | Birler |
|---------|--------|--------|-------|--------|
| 10^n | 10³ | 10² | 10¹ | 10⁰ |
| Değer | 1000 | 100 | 10 | 1 |

---

## 💻 İkilik Sayı Sistemi (Binary)

### 8-Bit Basamak Değerleri — Ezberlenecek!

```
┌─────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│ 128 │  64 │  32 │  16 │   8 │   4 │   2 │   1 │
└─────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘
  2⁷    2⁶    2⁵    2⁴    2³    2²    2¹    2⁰
```

### En Küçük ve En Büyük Değerler

```
00000000  →  0   (tüm bitler 0)
11111111  →  128+64+32+16+8+4+2+1 = 255  (tüm bitler 1)
```

> 📌 **Her oktet 0-255 arasında değer alır!**

### Binary → Decimal Dönüşümü

Dolu (1) olan basamakların değerlerini topla:

```
Örnek 1:  1 0 0 0 0 0 0 0
          │
          └── 128'ler basamağı dolu → 128

Örnek 2:  1 1 0 0 0 0 0 0
          │ │
          │ └── 64'ler basamağı dolu → 64
          └──── 128'ler basamağı dolu → 128
                                        ───
                                        192  ✓

Örnek 3:  0 1 0 1 1 0 0 1
              │   │ │     │
              │   │ │     └── 1'ler → 1
              │   │ └──────── 8'ler → 8
              │   └────────── 16'lar → 16
              └────────────── 64'ler → 64
                                       ──
                                       89  ✓
```

### Hızlı Hesap Püf Noktası

Ard arda 1'ler görünce, bir sonraki basamaktan 1 çıkar:

```
0 0 0 1 1 1 1 1
        └──────── 5 tane 1
                  → 32'ler basamağı sıfır
                  → 32 - 1 = 31  ✓

1 1 1 1 1 1 1 1  → 256 - 1 = 255  ✓
0 1 1 1 1 1 1 1  → 128 - 1 = 127  ✓
0 0 1 1 1 1 1 1  →  64 - 1 = 63   ✓
```

### Decimal → Binary Dönüşümü

En büyük basamaktan başla, sığıyorsa yerleştir ve çıkar:

```
168 sayısını binary'ye çevir:

Adım 1: 168 ≥ 128? EVET → bit=1, kalan: 168-128 = 40
Adım 2:  40 ≥  64? HAYIR → bit=0
Adım 3:  40 ≥  32? EVET → bit=1, kalan:  40-32  = 8
Adım 4:   8 ≥  16? HAYIR → bit=0
Adım 5:   8 ≥   8? EVET → bit=1, kalan:   8-8   = 0
Adım 6:   0 ≥   4? HAYIR → bit=0
Adım 7:   0 ≥   2? HAYIR → bit=0
Adım 8:   0 ≥   1? HAYIR → bit=0

Sonuç: 1 0 1 0 1 0 0 0  =  168  ✓
```

**Akış Diyagramı:**

```
    Başla: n = onluk sayı
          │
          ▼
    ┌─────────────┐
    │ n ≥ 128?    │──EVET──► bit=1, n = n-128
    └──────┬──────┘
           │HAYIR
           ▼
         bit=0
           │
           ▼
    ┌─────────────┐
    │ n ≥ 64?     │──EVET──► bit=1, n = n-64
    └──────┬──────┘
           │HAYIR
           ▼
         bit=0
           │
           ▼
    ... (32, 16, 8, 4, 2, 1 için devam et)
```

---

## 🌍 IPv4 Adresleri ve Binary

### IPv4 Yapısı

```
IPv4 Adresi:  192    .   168    .    11    .    10
              │              │         │          │
              ▼              ▼         ▼          ▼
Binary:   11000000    10101000    00001011    00001010
          └──────┘    └──────┘    └──────┘    └──────┘
           Oktet 1     Oktet 2     Oktet 3     Oktet 4
           8 bit       8 bit       8 bit       8 bit
                    └──────────────────────────┘
                           32 bit toplam
```

**"Noktalı Onluk Gösterim" (Dotted Decimal Notation):**

```
11000000.10101000.00001011.00001010
         ↓ ↓ ↓ ↓
       192.168.11.10
```

Bilgisayarlar 32 bitlik binary ile çalışır. Biz insanlar kolaylık için 4 oktete bölüp onluk sisteme çeviririz.

### IPv4 Dönüşüm Örneği (Binary → Decimal)

```
11000000.10101000.00001011.00001010

Oktet 1:  1 1 0 0 0 0 0 0
          128+64 = 192

Oktet 2:  1 0 1 0 1 0 0 0
          128+32+8 = 168

Oktet 3:  0 0 0 0 1 0 1 1
          8+2+1 = 11

Oktet 4:  0 0 0 0 1 0 1 0
          8+2 = 10

Sonuç: 192.168.11.10 ✓
```

### Hızlı Alıştırma Tablosu

| Binary | Decimal |
|--------|---------|
| `00000000` | 0 |
| `00000011` | ? |
| `01011001` | 89 |
| `11100000` | ? |
| `00011111` | ? |
| `11111111` | 255 |

> **Cevaplar:** 3, 224, 31

---

## 🔡 Hexadecimal Sayı Sistemi

### Neden Hexadecimal?

4 biti tek bir sembolle ifade eder → MAC ve IPv6 adresleri çok daha kısa görünür!

```
Binary:       1010  1000
              ↓       ↓
Hex:           A       8   →   A8
```

### Binary – Decimal – Hexadecimal Tablosu

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

> 💡 **Ezber:** 0-9 aynı kalır, 10=A, 11=B, 12=C, 13=D, 14=E, 15=F

### 4-Bit Basamak Değerleri

```
┌─────┬─────┬─────┬─────┐
│  8  │  4  │  2  │  1  │
└─────┴─────┴─────┴─────┘
  2³    2²    2¹    2⁰
```

### Binary → Hex Dönüşümü

4 bit 4 bit grupla, her grubu hex sembolüne çevir:

```
Binary:    1 0 1 0  |  1 0 0 0
           └──┬──┘     └──┬──┘
              │            │
              A             8
              │            │
Hex:        A8  ✓
```

**Daha uzun örnek:**

```
1000 0011 0111 1001 1010 0001 0000 0011 1111 1110
 8    3    7    9    A    1    0    3    F    E
→  83 79 A1 03 FE
```

### Hex → Binary Dönüşümü

Her hex sembolünü 4 bite çevir:

```
Hex:   A    B
       │    │
       ▼    ▼
    1010  1011
    
AB (hex) = 1010 1011 (binary)  ✓
```

---

## 🖧 MAC Adresleri ve Hexadecimal

### MAC Adres Yapısı

```
MAC Adresi:  01  :  5F  :  6A  :  27  :  3C  :  D9
             │       │       │       │       │       │
             └───────┴───────┘       └───────┴───────┘
                   OUI                  Device ID
              (Üretici Kodu)         (Cihaz Kodu)

Toplam: 48 bit = 12 hex karakter
```

### Binary → MAC Adresi Dönüşümü

```
48 bitlik MAC:
0001 0000 : 0010 0000 : 0011 0000 : 0100 0000 : 0101 0000 : 0110 0000
  1    0       2    0       3    0       4    0       5    0       6    0
   10      :    20      :    30      :    40      :    50      :    60
```

> 📌 **Not:** MAC adresleri 8 bit 8 bit gruplandırılmış ve araya ":" konmuş, **12 hexadecimal karakterden** oluşur.

---

## 🌐 IPv6 Adresleri ve Hexadecimal

### IPv6 Yapısı

```
IPv6:  2001 : 0DB8 : AAAA : 1111 : 0000 : 0000 : 0000 : 0100
        │       │       │       │       │       │       │       │
       16b     16b     16b     16b     16b     16b     16b     16b
       Hextet  Hextet  Hextet  Hextet  Hextet  Hextet  Hextet  Hextet
         1       2       3       4       5       6       7       8

Toplam: 8 × 16 bit = 128 bit
```

### IPv4 vs IPv6 vs MAC Karşılaştırması

```
┌─────────────┬──────────┬────────────┬───────────────────┐
│  Adres Türü │  Bit     │  Gösterim  │  Örnek            │
├─────────────┼──────────┼────────────┼───────────────────┤
│  IPv4       │  32 bit  │  Onluk     │  192.168.10.10    │
│             │  4 oktet │  (4 alan)  │                   │
├─────────────┼──────────┼────────────┼───────────────────┤
│  MAC        │  48 bit  │  Hex       │  10:20:30:40:50:60│
│             │  6 oktet │  (12 char) │                   │
├─────────────┼──────────┼────────────┼───────────────────┤
│  IPv6       │  128 bit │  Hex       │  2001:0DB8::0100  │
│             │  8 hextet│  (32 char) │                   │
└─────────────┴──────────┴────────────┴───────────────────┘
```

### Her Hextet'in Değer Aralığı

```
Her hextet 16 bit:
  En küçük: 0000  →  0
  En büyük: FFFF  →  65535

8 hextet × 16 bit = 128 bit
```

---

## 🔄 Dönüşüm Yöntemleri

### Tüm Dönüşüm Yolları

```
                    ┌─────────┐
                    │  ONLUK  │
                    │(Decimal)│
                    └────┬────┘
                    ↗    │    ↘
              ↗          │         ↘
        ┌──────┐    doğrudan    ┌──────┐
        │İKİLİK│◄──────────────►│ HEX  │
        │(Bin.)│                │      │
        └──────┘                └──────┘

En kolay yol: Onluk ↔ İkilik ↔ Hex
Doğrudan Onluk ↔ Hex: İkilik üzerinden geç!
```

### Decimal → Binary Adımları

```
1. 128'den başla
2. Sayı ≥ basamak değeri? → 1 yaz, çıkar
3. Sayı < basamak değeri? → 0 yaz, devam et
4. 1'e kadar tekrarla
```

### Binary → Decimal Adımları

```
1. Dolu (1) olan tüm basamak değerlerini belirle
2. Değerleri topla
```

### Decimal → Hex Adımları

```
Adım 1: Decimal'i Binary'ye çevir (128 64 32 16 8 4 2 1)
Adım 2: Binary'yi 4'lü gruplara böl (sağdan sola)
Adım 3: Her 4-bit grubu hex sembolüne çevir (8 4 2 1 tablosu)
```

### Hex → Decimal Adımları

```
Adım 1: Her hex sembolünü 4-bit binary'ye çevir
Adım 2: 8-bit gruplar oluştur
Adım 3: Her 8-bit grubu decimal'e çevir
```

### Tam Örnek: 168 → Hex

```
168 (decimal)
    │
    ▼ [Decimal → Binary]
10101000 (binary)
    │
    ▼ [4'lü gruplama]
1010 | 1000
    │
    ▼ [Her 4 bit → Hex]
 A  |  8
    │
    ▼
  A8 (hex)  ✓
```

### Tam Örnek: D2 (hex) → Decimal

```
D2 (hex)
    │
    ▼ [Her hex → 4 bit]
1101 | 0010
    │
    ▼ [Birleştir]
11010010 (binary)
    │
    ▼ [Binary → Decimal]
128+64+16+2 = 210 (decimal)  ✓
```

---

## 📝 Pratik Alıştırmalar

### 1. Binary → Decimal

```
Basamak değerleri: 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1

a) 10000000 = ?
b) 11000000 = ?
c) 10101000 = ?
d) 00001011 = ?
e) 11111110 = ?
f) 01111111 = ?
```

**Cevaplar:** a)128  b)192  c)168  d)11  e)254  f)127

### 2. Decimal → Binary

```
a) 10  = ?
b) 192 = ?
c) 255 = ?
d) 128 = ?
e) 127 = ?
f) 224 = ?
```

**Cevaplar:**
```
a) 00001010   b) 11000000   c) 11111111
d) 10000000   e) 01111111   f) 11100000
```

### 3. IPv4 Binary → Decimal

```
11000000.10101000.00001010.00001010  = ?
```

**Cevap:** 192.168.10.10

### 4. Binary → Hex

```
a) 1010 = ?
b) 1111 = ?
c) 1101 0010 = ?
d) 1010 1011 = ?
```

**Cevaplar:** a)A  b)F  c)D2  d)AB

### 5. Hex → Decimal

```
a) A8  = ?
b) FF  = ?
c) D2  = ?
d) 1F  = ?
```

**Cevaplar:** a)168  b)255  c)210  d)31

---

## 📊 Özet Tablosu

### Sayı Sistemlerinin Karşılaştırması

| Özellik | Onluk (Decimal) | İkilik (Binary) | Hexadecimal |
|---------|----------------|-----------------|-------------|
| **Taban** | 10 | 2 | 16 |
| **Semboller** | 0-9 | 0, 1 | 0-9, A-F |
| **Network Kullanımı** | IP adresleri, portlar | Bilgisayar iletişimi | MAC, IPv6 |
| **1 hex → kaç bit** | — | — | 4 bit |
| **8 bit grup adı** | — | — | Octet |
| **16 bit grup adı** | — | — | Hextet |
| **Gruplama** | 4 oktet | 8 bit × 4 | 4 bit × N |

### Adres Boyutları Özeti

```
┌──────────────────────────────────────────────────────────┐
│                    ADRES BOYUTLARI                       │
├──────────────┬──────────┬────────────┬───────────────────┤
│  Adres       │  Bit     │  Grup/İsim │  Gösterim         │
├──────────────┼──────────┼────────────┼───────────────────┤
│  IPv4        │  32 bit  │  4 × Oktet │  Noktalı Onluk    │
│  MAC         │  48 bit  │  6 × Oktet │  Hex (12 char)    │
│  IPv6        │  128 bit │  8 × Hextet│  Hex (32 char)    │
└──────────────┴──────────┴────────────┴───────────────────┘
```

### 8-Bit Basamak Değerleri — Ezber Kartı

```
┌───────────────────────────────────────────────────┐
│  128  │  64  │  32  │  16  │  8  │  4  │  2  │  1 │
│  2⁷   │  2⁶  │  2⁵  │  2⁴  │ 2³  │ 2²  │ 2¹  │ 2⁰ │
└───────────────────────────────────────────────────┘
Minimum: 0 (00000000)    Maximum: 255 (11111111)
```

### 4-Bit Basamak Değerleri (Hex için) — Ezber Kartı

```
┌─────┬─────┬─────┬─────┐
│  8  │  4  │  2  │  1  │
└─────┴─────┴─────┴─────┘
Minimum: 0 (0000)    Maximum: F = 15 (1111)
```

---

## 🎮 Binary Game

Cisco'nun eğlenceli binary öğrenme oyunu:

> 🔗 **https://learningnetwork.cisco.com/s/binary-game**

Tetris benzeri bir oyun: Aşağıdan sayılar geliyor, doğru binary değerini oluşturman gerekiyor!

---

## 📝 Anahtar Kavramlar Sözlüğü

| Terim | Açıklama |
|-------|---------|
| **Bit** | Binary digit — 0 veya 1 |
| **Byte** | 8 bit = 1 bayt |
| **Octet** | 8-bit grup (IPv4'te kullanılır) |
| **Hextet** | 16-bit grup (IPv6'da kullanılır) |
| **Binary** | 2 tabanlı sayı sistemi (0 ve 1) |
| **Decimal** | 10 tabanlı sayı sistemi (0-9) |
| **Hexadecimal** | 16 tabanlı sayı sistemi (0-9, A-F) |
| **Dotted Decimal** | IPv4 adresinin noktalı onluk gösterimi |
| **Positional Notation** | Konumsal gösterim — her basamağın yere göre değeri |
| **MSB** | Most Significant Bit — en değerli bit (soldan ilk) |
| **LSB** | Least Significant Bit — en az değerli bit (sağdan ilk) |
| **Subnetting** | IP adreslerini alt ağlara bölme (binary bilgisi gerektirir!) |

---

*📚 CCNA1 – Introduction to Networks v7.0 | Modül 5: Sayı Sistemleri*
