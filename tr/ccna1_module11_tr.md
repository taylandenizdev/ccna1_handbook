# 📘 Modül 11: IPv4 Adresleme
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Kaynak: Cisco Networking Academy  


---

## 📋 İçindekiler
1. [Kısa Hatırlatma: Kapsülleme ve IP Başlığı](#1-kısa-hatırlatma-kapsülleme-ve-ip-başlığı)
2. [IPv4 Adres Yapısı](#2-ipv4-adres-yapısı)
3. [Subnet Maskesi ve Prefix Uzunluğu](#3-subnet-maskesi-ve-prefix-uzunluğu)
4. [Ağ (Network) ve Broadcast Adresleri](#4-ağ-network-ve-broadcast-adresleri)
5. [IP Haberleşme Türleri](#5-ip-haberleşme-türleri)
6. [IP Adres Sınıfları (Classful Addressing)](#6-ip-adres-sınıfları-classful-addressing)
7. [Public ve Private IP Adresleri](#7-public-ve-private-ip-adresleri)
8. [IP Dağıtım Hiyerarşisi](#8-ip-dağıtım-hiyerarşisi)
9. [Özel IP Adresleri (Loopback, APIPA)](#9-özel-ip-adresleri-loopback-apipa)
10. [Subnetting Temelleri](#10-subnetting-temelleri)
11. [Subnetting Hesaplama Tabloları](#11-subnetting-hesaplama-tabloları)
12. [VLSM (Değişken Uzunluklu Subnet Maskesi)](#12-vlsm-değişken-uzunluklu-subnet-maskesi)
13. [Network Tasarımı Özeti](#13-network-tasarımı-özeti)

---

## 1. Kısa Hatırlatma: Kapsülleme ve IP Başlığı

### 🗂️ OSI Kapsülleme Özeti
```
┌──────────────────────────────────────────────────────────────────┐
│         UYGULAMA KATMANI VERİSİ (DATA)                          │
├───────────────────────────────────────────────────────────┬──────┤
│  UDP Header (8 byte)  │  UDP Data                         │      │
├───────────────────────┴───────────────────────────────────┤      │
│  TCP Header (20 byte) │  TCP Data                 Segment │      │
├───────────────────────┴───────────────────────────────────┤      │
│  IP Header  (20 byte) │  IP Data                   Packet │      │
├───────────┬───────────┴───────────────────────────────────┤      │
│ Eth.Header│         Ethernet Data (46–1500 byte)  │  CRC  │Frame │
│ (14 byte) │                                       │(4byte)│      │
└───────────┴───────────────────────────────────────┴───────┴──────┘
 Toplam başlık: 18 + 20 + 20 = 58 byte  |  Data: maks. 1460 byte
 Verimlilik: ~%96 data  |  ~%4 başlık
```

### 📋 IP Başlığının Önemli Alanları
| Alan | Açıklama |
|---|---|
| **Kaynak IP** | Paketin çıktığı cihazın IP adresi (yol boyunca DEĞİŞMEZ) |
| **Hedef IP** | Paketin gideceği cihazın IP adresi (yol boyunca DEĞİŞMEZ) |
| **TTL** | Time To Live — her router'da 1 azalır; 0 olunca paket silinir |
| **Protokol** | Üst katman protokolü: TCP=6, UDP=17, ICMP=1 |
| **Toplam Uzunluk** | Başlık + data boyutu (byte) |
| **Başlık Hata Kontrolü** | IP başlığının bütünlük kontrolü |

> **🔑 Temel Kural:** MAC adresleri her router atlamasında değişir. IP adresleri kaynaktan hedefe kadar değişmez.

---

## 2. IPv4 Adres Yapısı

### 🔢 32 Bit → Noktalı Ondalık Gösterim
```
┌──────────────────────────────────────────────────────────────────┐
│  IPv4 = 32 bit = 4 oktet (4 × 8 bit)                            │
│                                                                  │
│  İkilik (binary):                                                │
│  11000000 . 10101000 . 00000001 . 00000101                       │
│     ↓           ↓          ↓          ↓                          │
│    192    .    168    .     1     .    5                          │
│                                                                  │
│  Noktalı Ondalık (Dotted Decimal Notation): 192.168.1.5          │
└──────────────────────────────────────────────────────────────────┘
```

### 📊 8 Bit Basamak Değerleri (Ezber Tablosu)
```
┌───────┬───────┬───────┬───────┬───────┬───────┬───────┬───────┐
│  128  │  64   │  32   │  16   │   8   │   4   │   2   │   1   │
├───────┴───────┴───────┴───────┴───────┴───────┴───────┴───────┤
│  Minimum: 00000000 = 0                                         │
│  Maximum: 11111111 = 128+64+32+16+8+4+2+1 = 255               │
│  Her oktet: 0 ile 255 arasında değişir                         │
└────────────────────────────────────────────────────────────────┘
```

> **⚠️ 256 diye bir değer OLMAZ.** Birisi size "172.256.10.5" dese hemen yanlış dersiniz.

### 🏗️ IP Adresinin İki Bölümü
```
┌─────────────────────────────────────────────────────────────────┐
│  IP ADRESİ = NETWORK KISMI + HOST KISMI                         │
│                                                                 │
│  Örnek: 192.168.10.25  /24                                      │
│         ┌────────────┐  ┌──┐                                    │
│         │192.168.10  │  │25│                                    │
│         └────────────┘  └──┘                                    │
│         NETWORK KISMI   HOST                                    │
│         (değişmez)      (her cihaz farklı)                      │
│                                                                 │
│  Aynı network'teki tüm cihazlar 192.168.10.x olacak            │
│  x değeri: 1–254 arası                                         │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Subnet Maskesi ve Prefix Uzunluğu

### 🎯 Subnet Maskesi Nedir?
IP adresinin hangi kısmının **network**, hangi kısmının **host** olduğunu tanımlayan 32 bitlik özel bir adrestir. Başı **1'lerden**, sonu **0'lardan** oluşur.

```
┌──────────────────────────────────────────────────────────────────┐
│  IP Adresi:     192.168.10.25                                    │
│  Subnet Mask:   255.255.255.0                                    │
│                                                                  │
│  İkilik gösterim:                                                │
│  IP:   11000000.10101000.00001010.00011001                       │
│  Mask: 11111111.11111111.11111111.00000000                       │
│        ├──────────────────────────┤├───────┤                     │
│        NETWORK KISMI (1'ler)       HOST (0'lar)                  │
│        192.168.10                   .25                          │
└──────────────────────────────────────────────────────────────────┘
```

### 📐 Subnet Maskesi ↔ Prefix (Slash) Notasyonu
Prefix uzunluğu (slash notasyonu) = network kısmındaki **1'lerin sayısı**

```
┌──────────────────────────────────────────────────────────────────┐
│  Subnet Maskesi       Prefix    Network biti   Host biti         │
├────────────────────── ─────────────────────── ──────────────────┤
│  255.0.0.0          = /8        8 bit          24 bit            │
│  255.255.0.0        = /16       16 bit         16 bit            │
│  255.255.255.0      = /24       24 bit          8 bit            │
│  255.255.255.128    = /25       25 bit          7 bit            │
│  255.255.255.192    = /26       26 bit          6 bit            │
│  255.255.255.224    = /27       27 bit          5 bit            │
│  255.255.255.240    = /28       28 bit          4 bit            │
│  255.255.255.248    = /29       29 bit          3 bit            │
│  255.255.255.252    = /30       30 bit          2 bit            │
└──────────────────────────────────────────────────────────────────┘
```

> **📌 Önemli:** Subnet maskesi olmadan IP adresi tek başına ne kadar network, ne kadar host olduğunu söyleyemez. Her zaman beraber yazılmalı.

### 🔢 /28 Subnet Maskesini Nasıl Hesaplıyorum?
```
Slash 28 → network kısmında 28 bit var
8 + 8 + 8 = 24 bit zaten dolu → son oktette 4 bit daha lazım

Son oktet: 128 + 64 + 32 + 16 = 240

Subnet maskesi: 255.255.255.240
```

---

## 4. Ağ (Network) ve Broadcast Adresleri

### 📍 Temel Kurallar
```
┌──────────────────────────────────────────────────────────────────┐
│  Örnek Network: 192.168.1.0 /24                                  │
│                                                                  │
│  HOST KISMI                KULLANIM                             │
│  ─────────────────────     ─────────────────────────────────    │
│  Hepsi 0  → 192.168.1.0   = NETWORK ADRESİ                     │
│             (kullanılamaz, routing tablolarında kullanılır)      │
│                                                                  │
│  1 – 254  → 192.168.1.1   = İlk kullanılabilir IP               │
│           → 192.168.1.254 = Son kullanılabilir IP               │
│             (cihazlara verilebilir)                              │
│                                                                  │
│  Hepsi 1  → 192.168.1.255 = BROADCAST ADRESİ                   │
│             (kullanılamaz, o ağdaki herkese paket gönderir)      │
└──────────────────────────────────────────────────────────────────┘
```

### 🧮 IP Sayısı Formülü
```
  Toplam Adres Sayısı = 2^(host bit sayısı)
  Kullanılabilir IP   = 2^(host bit sayısı) − 2

  /24 → host=8 bit → 2^8 = 256 adres → 254 kullanılabilir IP
  /25 → host=7 bit → 2^7 = 128 adres → 126 kullanılabilir IP
  /26 → host=6 bit → 2^6 =  64 adres →  62 kullanılabilir IP
  /30 → host=2 bit → 2^2 =   4 adres →   2 kullanılabilir IP
```

### 📊 Network Hesap Tablosu (Örnek: 192.168.1.0/24)
```
┌─────────────────┬─────────────────┬───────────────────┬─────────┐
│ Network Adresi  │ Broadcast Adr.  │ Kullanılabilir IP │ IP Sayı │
├─────────────────┼─────────────────┼───────────────────┼─────────┤
│ 192.168.1.0     │ 192.168.1.255   │ 1.1 – 1.254       │   254   │
│ 192.168.2.0     │ 192.168.2.255   │ 2.1 – 2.254       │   254   │
│ 172.16.0.0/16   │ 172.16.255.255  │ 16.0.1–16.255.254 │ 65.534  │
└─────────────────┴─────────────────┴───────────────────┴─────────┘
```

---

## 5. IP Haberleşme Türleri

```
┌─────────────────────────────────────────────────────────────────┐
│                  IP HABERLEŞME TÜRLERİ                          │
├──────────────────┬──────────────────┬───────────────────────────┤
│   UNİCAST        │   BROADCAST       │   MULTİCAST               │
├──────────────────┼──────────────────┼───────────────────────────┤
│ Bire-bir iletişim│ Bir → Herkese     │ Bir → Gruba               │
│                  │                  │                           │
│  PC_A → PC_B     │  PC_A → tümü     │  PC_A → (dinleyenler)     │
│                  │                  │                           │
│ Kaynak IP her    │ İki tür var:      │ IP: 224.0.0.0 –           │
│ zaman unicast    │ 1) x.x.x.255     │     239.255.255.255        │
│                  │ 2) 255.255.255.255│ MAC: 01-00-5E-xx-xx-xx    │
│ Hedef MAC:       │    (limited bcast)│                           │
│ Karşının MAC'i   │                  │ Cihazlar gruba             │
│ (ya da router'ın)│ Router geçirmez! │ üye olur                  │
└──────────────────┴──────────────────┴───────────────────────────┘
```

### 📡 Broadcast Türleri
```
255.255.255.255   → Limited Broadcast  → Sadece aynı network
                   (Router GEÇİRMEZ)

x.x.x.255        → Directed Broadcast → Belirli bir ağa
(örn: 192.168.1.255)  (Router normalde GEÇİRMEZ, aktive edilebilir)
```

> **DHCP IP alımı:** Yeni cihaz açıldığında IP'si yok → Hedef: 255.255.255.255 (Limited Broadcast) → DHCP sunucu yanıtlar.

---

## 6. IP Adres Sınıfları (Classful Addressing)

### 🏫 Sınıf Tablosu
```
┌───────┬──────────────┬──────────┬──────────┬────────────────────┐
│ Sınıf │ İlk Oktet    │ Prefix   │ IP Sayısı│ Kullanım           │
├───────┼──────────────┼──────────┼──────────┼────────────────────┤
│   A   │  1  – 127    │   /8     │ ~16 M    │ Çok büyük şirketler│
│   B   │ 128 – 191    │   /16    │  65.534  │ Orta ölçekli       │
│   C   │ 192 – 223    │   /24    │     254  │ Küçük şirketler    │
│   D   │ 224 – 239    │  Yok     │    –     │ Multicast adresleri│
│   E   │ 240 – 255    │  Yok     │    –     │ Araştırma (rezerve)│
└───────┴──────────────┴──────────┴──────────┴────────────────────┘
```

### 🔍 Sınıfı Nasıl Anlarım?
```
İlk oktete bak:

  1  – 127  → A sınıfı  (0 ile başlayan ikili: 0xxxxxxx)
  128 – 191 → B sınıfı  (10 ile başlayan:      10xxxxxx)
  192 – 223 → C sınıfı  (110 ile başlayan:     110xxxxx)
  224 – 239 → D sınıfı  (1110 ile başlayan)
  240 – 255 → E sınıfı  (1111 ile başlayan)

Örnekler:
  10.0.0.1   → A sınıfı (10 → 1–127 arası)
  172.16.0.1 → B sınıfı (172 → 128–191 arası)
  192.168.1.1→ C sınıfı (192 → 192–223 arası)
```

### ⚠️ Classful Adreslemede Sorun
```
┌─────────────────────────────────────────────────────────────────┐
│  Verilen IP     Gerçek İhtiyaç    Verilen Blok    İsraf         │
├─────────────────────────────────────────────────────────────────┤
│  A sınıfı       10 bilgisayar     16 MILYON IP    ~16 milyon    │
│  B sınıfı       2.000 kullanıcı   65.534 IP       ~63.000       │
│  C sınıfı       30 kullanıcı      254 IP          ~224          │
│                                                                 │
│  → IP'ler bol keseden dağıtıldı → Hızla tükendi!               │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Classless Adreslemede Geçiş (CIDR)
Classful'ın yerini Classless Inter-Domain Routing aldı. Artık ihtiyaca göre herhangi bir prefix uzunluğu kullanılabiliyor:
- 16.000 IP → /18 verilebilir (A sınıfı IP bloğundan bile)
- 500 IP → /23 verilebilir
- İhtiyaç kadar, damlalık dağıtım

---

## 7. Public ve Private IP Adresleri

### 🔐 Private (Özel) IP Blokları
RFC 1918 standardı — internete çıkamaz, sadece iç ağda kullanılır:

```
┌──────────────────────────────────────────────────────────────────┐
│  PRIVATE IP BLOKLARI (RFC 1918)                                  │
├──────────────────┬─────────────────────────────────────────────┤
│  10.0.0.0/8      │ 1 × A sınıfı → ~16 milyon IP               │
│  172.16.0.0/12   │ 16 × B sınıfı (172.16 – 172.31) → ~1M IP   │
│  192.168.0.0/16  │ 256 × C sınıfı (192.168.0–255) → 65K IP    │
└──────────────────┴─────────────────────────────────────────────┘

  Bu adresler internette yönlendirilmez.
  Herkes kendi ağında istediği gibi kullanabilir.
  Evde kullandığınız: 192.168.1.x
```

### 🌐 NAT (Network Address Translation)
```
┌─────────────────────────────────────────────────────────────────┐
│                      NAT İŞLEMİ                                 │
│                                                                 │
│  İÇ AĞ (Private IP)           DIŞ AĞ (Public IP)              │
│                                                                 │
│  PC_A: 192.168.1.5             Router  ISP IP: 85.1.2.3        │
│  PC_B: 192.168.1.6    ──NAT──▶ ──────────────────▶  INTERNET   │
│  PC_C: 192.168.1.7             Google görür: 85.1.2.3           │
│                                                                 │
│  • Dışarıya çıkarken: Private IP → Public IP dönüşümü          │
│  • Dönerken: Public IP → Private IP dönüşümü                   │
│  • NAT tablosu tutulur (hangi özel IP, hangi port kullandı)    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. IP Dağıtım Hiyerarşisi

```
┌─────────────────────────────────────────────────────────────────┐
│                        ICANN                                    │
│              (İnternet Sayısal Değerler Kurumu)                 │
│                          │                                      │
│                        IANA                                     │
│           (Atanan İnternet Numaraları Otoritesi)                │
│      IP, Port, Domain yönetimi                                  │
│                          │                                      │
│          ┌───────────────┼───────────────┐                     │
│          ▼               ▼               ▼                     │
│  ┌──────────────┐  ┌──────────┐  ┌───────────┐               │
│  │    ARIN      │  │   RIPE   │  │  APNIC    │               │
│  │ Kuzey Amerika│  │ Avrupa,  │  │   Asya-   │               │
│  │              │  │ Orta Doğu│  │  Pasifik  │               │
│  │              │  │ Kuzey    │  │           │               │
│  │              │  │ Asya     │  │           │               │
│  └──────────────┘  └──────────┘  └───────────┘               │
│                                                                 │
│  + LACNIC (Latin Amerika)  + AFRINIC (Afrika)                  │
│                                                                 │
│  TÜRKİYE → RIPE NCC'den IP alır                               │
│  (ripe.net → Whois sorgusundan IP sahibi öğrenilir)            │
└─────────────────────────────────────────────────────────────────┘

  RIR → ISP'ye blok verir
  ISP → Şirketlere/bireylere kira olarak dağıtır
  Çift ISP hattı istiyorsanız → RIPE'den kendi IP bloğunuzu alın
```

---

## 9. Özel IP Adresleri (Loopback, APIPA)

```
┌──────────────────────────────────────────────────────────────────┐
│  ÖZEL IP BLOKLARI                                                │
├──────────────────┬────────────────────────────────────────────┤
│  127.0.0.0/8     │ LOOPBACK (Geri döngü)                      │
│  (genelde        │ • Cihazın kendisine ping atar              │
│  127.0.0.1       │ • TCP/IP çalışıyor mu test eder            │
│  kullanılır)     │ • Kablo gerekmez, NIC takılı yeterli       │
├──────────────────┼────────────────────────────────────────────┤
│  169.254.0.0/16  │ APIPA / Link-Local Adres                   │
│                  │ • DHCP sunucu bulunamadığında atanır        │
│                  │ • Windows: APIPA adı verilir               │
│                  │ • Cihaz rastgele 169.254.x.x seçer         │
│                  │ • Subnet mask: 255.255.0.0                 │
│                  │ • Default gateway YOK → internete çıkamaz  │
│                  │ • Sadece iç ağ haberleşmesi için           │
└──────────────────┴────────────────────────────────────────────┘
```

---

## 10. Subnetting Temelleri

### ❓ Neden Subnetting Yapılır?
```
SORUN (Tek Büyük Network):
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  [400 kullanıcı – tek /24 network]                              │
│                                                                 │
│  PC_A ──ARP broadcast──▶ TÜM 400 kullanıcıya gider             │
│  PC_B ──DHCP broadcast──▶ TÜM 400 kullanıcıya gider            │
│  PC_C ──ARP broadcast──▶ TÜM 400 kullanıcıya gider             │
│                                                                 │
│  Sorunlar:                                                      │
│  ✗ Aşırı broadcast trafiği → Network yavaşlar                  │
│  ✗ Güvenlik yok (herkese ulaşılabilir)                         │
│  ✗ Saldırı tüm ağı etkiler                                     │
│  ✗ Cihazın konumu bilinmez                                     │
└─────────────────────────────────────────────────────────────────┘

ÇÖZÜM (Subnetting):
┌─────────────────────────────────────────────────────────────────┐
│  [400 kullanıcı → 2 × /25 network: 200 + 200]                  │
│                                                                 │
│  Subnet 1 (192.168.1.0/25)  ──[ROUTER]──  Subnet 2             │
│  PC_A ──ARP──▶ sadece 200 kişiye          (192.168.1.128/25)   │
│                                                                 │
│  Avantajlar:                                                    │
│  ✓ Broadcast trafiği azaldı                                    │
│  ✓ Network performansı arttı                                   │
│  ✓ Ağlar arası güvenlik sağlandı                               │
│  ✓ IP konumları belirlenebilir (2.x = B blok gibi)             │
│  ✓ Ağ yönetimi kolaylaştı                                      │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Subnetting Mekanizması: Bit Ödünç Alma
```
Orijinal network: 192.168.1.0 /24

Host kısmından N bit "ödünç" alınır → Subnet biti olur

/24 + 1 bit → /25  →  2 subnet  ×  126 IP/subnet
/24 + 2 bit → /26  →  4 subnet  ×   62 IP/subnet
/24 + 3 bit → /27  →  8 subnet  ×   30 IP/subnet
/24 + 4 bit → /28  → 16 subnet  ×   14 IP/subnet
/24 + 5 bit → /29  → 32 subnet  ×    6 IP/subnet
/24 + 6 bit → /30  → 64 subnet  ×    2 IP/subnet

Formüller:
  Subnet Sayısı = 2^(ödünç alınan bit)
  Adres Sayısı  = 2^(kalan host biti)
  Kullanılabilir IP = 2^(kalan host biti) − 2
```

### 🏫 Broadcast Domain
```
Broadcast domain = Broadcast paketlerinin gidebildiği alan
Router broadcast paketlerini GEÇİRMEZ

┌──────────────────────────────────────────────────────────────────┐
│  [SW1]──PC1  ──[R1]──  [SW2]──PC3  ──[R2]──  [SW3]──PC5       │
│       └──PC2           └──PC4               └──PC6             │
│                                                                 │
│  Broadcast         Broadcast         Broadcast                 │
│  Domain 1          Domain 2          Domain 3                  │
│                                                                 │
│  3 Network = 3 Broadcast Domain                                │
└──────────────────────────────────────────────────────────────────┘
```

---

## 11. Subnetting Hesaplama Tabloları

### 📊 /24 Network'ü Bölerken Hızlı Tablo
```
┌────────────┬──────────┬────────────┬──────────────┬───────────┐
│  Prefix    │  Subnet  │  Adres/Sub │  IP/Subnet   │ Blok Boyut│
│            │  Sayısı  │            │  (−2)        │           │
├────────────┼──────────┼────────────┼──────────────┼───────────┤
│   /24      │    1     │    256     │    254       │    256    │
│   /25      │    2     │    128     │    126       │    128    │
│   /26      │    4     │     64     │     62       │     64    │
│   /27      │    8     │     32     │     30       │     32    │
│   /28      │   16     │     16     │     14       │     16    │
│   /29      │   32     │      8     │      6       │      8    │
│   /30      │   64     │      4     │      2       │      4    │
└────────────┴──────────┴────────────┴──────────────┴───────────┘
Kural: Blok boyutunu bilirsen → 0, blok, 2×blok, 3×blok... şeklinde
```

### 🗺️ /26 Subnetting Detaylı Örnek (192.168.1.0/24 → 4 subnet)
```
2 bit ödünç aldık → /26 → Blok: 64

┌───────────────────────────────────────────────────────────────┐
│  Subnet 0 (0. subnet)                                        │
│  Network  : 192.168.1.0                                      │
│  Broadcast: 192.168.1.63                                     │
│  IP Aralığı: .1 – .62    (62 IP)                            │
├───────────────────────────────────────────────────────────────┤
│  Subnet 1 (1. subnet)                                        │
│  Network  : 192.168.1.64                                     │
│  Broadcast: 192.168.1.127                                    │
│  IP Aralığı: .65 – .126  (62 IP)                            │
├───────────────────────────────────────────────────────────────┤
│  Subnet 2 (2. subnet)                                        │
│  Network  : 192.168.1.128                                    │
│  Broadcast: 192.168.1.191                                    │
│  IP Aralığı: .129 – .190 (62 IP)                            │
├───────────────────────────────────────────────────────────────┤
│  Subnet 3 (3. subnet)                                        │
│  Network  : 192.168.1.192                                    │
│  Broadcast: 192.168.1.255                                    │
│  IP Aralığı: .193 – .254 (62 IP)                            │
└───────────────────────────────────────────────────────────────┘
```

### 🧮 Hızlı Network Adresi Bulma
Verilen IP'nin ait olduğu subnet'i bulmak için:

```
Örnek: 192.168.1.99 /27 → Network adresi nedir?

/27 → Blok boyutu: 32
Network adresleri: 0, 32, 64, 96, 128, 160, 192, 224...

99 hangi bloğun içinde?  96 ≤ 99 < 128
→ Network adresi: 192.168.1.96
→ Broadcast:      192.168.1.127
→ IP aralığı:     .97 – .126
```

### 📊 /16 Network İçin Subnet Tablosu (Özet)
```
┌─────────┬───────────┬──────────────┬───────────────────────────┐
│ Prefix  │ Subnet /16│  IP/Subnet   │  Notlar                   │
├─────────┼───────────┼──────────────┼───────────────────────────┤
│ /17     │     2     │   32.766     │  1 bit ödünç              │
│ /18     │     4     │   16.382     │  2 bit ödünç              │
│ /19     │     8     │    8.190     │  3 bit ödünç              │
│ /23     │   128     │      510     │  7 bit ödünç              │
│ /24     │   256     │      254     │  8 bit ödünç (en yaygın)  │
│ /30     │ 16.384    │        2     │  14 bit ödünç             │
└─────────┴───────────┴──────────────┴───────────────────────────┘
```

---

## 12. VLSM (Değişken Uzunluklu Subnet Maskesi)

### ❓ VLSM Nedir?
Aynı IP bloğunda **farklı uzunluklarda subnet maskeleri** kullanmak. Aynı uzunluklarda böldüğünüzde IP israfı oluşur; VLSM bunu önler.

### ⚠️ VLSM Olmadan Sorun
```
İhtiyaç:
  3 × 28 kullanıcılı network
  3 × 2 IP'lik router bağlantısı

Çözüm (VLSM olmadan, hepsi /27):
  Her subnet: 30 IP  — toplam 8 subnet × 30 IP = 240 IP
  2 IP'lik bağlantılar da /27 aldı → her biri 28 IP boşa gitti!
  3 bağlantı × 28 IP israf = 84 IP ÇÖP
```

### ✅ VLSM ile Verimli Çözüm
```
┌─────────────────────────────────────────────────────────────────┐
│  Büyük networkler: /27 (30 IP) → 3 adet kullan                 │
│  Router bağlantıları: /30 (2 IP) → 3 adet kullan               │
│                                                                 │
│  192.168.1.0/27   → Kullanıcı ağı 1  (.1–.30)                  │
│  192.168.1.32/27  → Kullanıcı ağı 2  (.33–.62)                 │
│  192.168.1.64/27  → Kullanıcı ağı 3  (.65–.94)                 │
│                                                                 │
│  192.168.1.96/30  → Router link 1    (.97–.98)                 │
│  192.168.1.100/30 → Router link 2    (.101–.102)               │
│  192.168.1.104/30 → Router link 3    (.105–.106)               │
│                                                                 │
│  Çok daha az IP israfı!                                        │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 VLSM Görsel Yapısı
```
Orijinal blok: 192.168.1.0/24 (256 adres)
                          │
          ┌───────────────┴──────────────────┐
          ▼                                   ▼
   /25 (128 adres)                     /25 (128 adres)
    192.168.1.0/25                   192.168.1.128/25
          │                                   │
     ┌────┴────┐                         (kullanılıyor)
     ▼         ▼
/26 (64)    /26 (64)
 .0–.63     .64–.127
                │
           ┌────┴────┐
           ▼         ▼
      /28 (16)   /28 (16)   ...
      .64–.79   .80–.95
```

---

## 13. Network Tasarımı Özeti

### 📋 Tasarım Adımları
```
┌─────────────────────────────────────────────────────────────────┐
│  ADIM 1: IP Tipi Belirle                                       │
│  ─────────────────────────────────────────────────────────────  │
│  Private IP mi?  → 10.x.x.x / 172.16.x.x / 192.168.x.x        │
│  Public IP mi?   → RIPE/ARIN'den al, BGP ile duyur             │
│                                                                 │
│  ADIM 2: Subnet Planı Hazırla                                  │
│  ─────────────────────────────────────────────────────────────  │
│  Neye göre böleceğim?                                          │
│  → Katlara/Konuma göre (1.kat /24, 2.kat /24...)              │
│  → Kullanıcı tipine göre (Personel /24, Misafir /24...)        │
│  → Cihaz tipine göre (IP kameralar, IP telefonlar...)          │
│                                                                 │
│  ADIM 3: Her Subnet İçin Hesapla                               │
│  ─────────────────────────────────────────────────────────────  │
│  • Network adresi                                               │
│  • Broadcast adresi                                             │
│  • Kullanılabilir IP aralığı                                   │
│  • IP sayısı                                                    │
│                                                                 │
│  ADIM 4: Cihazlara IP Ver                                      │
│  ─────────────────────────────────────────────────────────────  │
│  Sunuculara: Statik IP                                          │
│  PC/Laptop: DHCP (otomatik)                                    │
│  Router'lar: Statik IP (her interfase)                          │
│  Switch'ler: Yönetim IP'si (statik)                            │
└─────────────────────────────────────────────────────────────────┘
```

### 🖥️ PC'ye IP Konfigürasyonu (Hatırlatma)
```
Statik IP için gerekli 3 bilgi:
  1. IP Adresi        → 192.168.1.25
  2. Subnet Maskesi   → 255.255.255.0  (/24)
  3. Default Gateway  → 192.168.1.1  (Router'ın IP'si)
  (+ DNS Sunucu      → 8.8.8.8 gibi)

ipconfig /all  → Windows'ta IP, MAC, DHCP, DNS bilgilerini gösterir
```

### 📐 Pratik Subnet Seçim Kılavuzu
```
Kaç kullanıcı var?  →  Hangi prefix?

      2 kullanıcı   →  /30  (2 IP)
   2 –   6          →  /29  (6 IP)
   7 –  14          →  /28  (14 IP)
  15 –  30          →  /27  (30 IP)
  31 –  62          →  /26  (62 IP)
  63 – 126          →  /25  (126 IP)
 127 – 254          →  /24  (254 IP)
255 – 510           →  /23  (510 IP)
511 –1022           →  /22  (1022 IP)
```

---

## 🔑 Modül 11 Özet

```
┌─────────────────────────────────────────────────────────────────┐
│  TEMEL KAVRAMLAR                                                │
├─────────────────────────────────────────────────────────────────┤
│  • IPv4 = 32 bit = 4 oktet, her oktet 0–255                    │
│  • IP = Network + Host kısmı                                   │
│  • Subnet maskesi: hangi kısmın ne olduğunu belirtir           │
│  • Network adresi: host bitleri hepsi 0 (routing için)         │
│  • Broadcast adresi: host bitleri hepsi 1 (herkese gönderim)   │
│  • IP sayısı: 2^host − 2                                       │
│                                                                 │
│  ADRES TİPLERİ                                                  │
├─────────────────────────────────────────────────────────────────┤
│  • Unicast: bire-bir                                           │
│  • Broadcast: bire-herkese (router geçirmez)                   │
│  • Multicast: bire-gruba (224–239.x.x.x)                       │
│  • Private: RFC1918 (10, 172.16–31, 192.168)                   │
│  • Loopback: 127.0.0.1 (kendini test)                          │
│  • APIPA: 169.254.x.x (DHCP yoksa otomatik)                    │
│                                                                 │
│  SUBNETTING                                                     │
├─────────────────────────────────────────────────────────────────┤
│  • Bit ödünç al → network böl                                  │
│  • Subnet sayısı = 2^(ödünç bit)                               │
│  • Blok boyutu = 2^(kalan host bit)                            │
│  • VLSM = farklı uzunluklarda subnet maskesi → verimli IP      │
│                                                                 │
│  IP DAĞITIMI                                                    │
├─────────────────────────────────────────────────────────────────┤
│  ICANN → IANA → RIR (RIPE/ARIN/APNIC/LACNIC/AFRINIC)           │
│  → ISP → Şirket/Bireyler                                       │
│  Türkiye: RIPE NCC bölgesinde                                  │
└─────────────────────────────────────────────────────────────────┘
```

---
> **📌 Sonraki Modül:** IPv4 başlık detayları ve IPv6'ya giriş.

---
*CCNA1 – Introduction to Networks v7.0 | Modül 11*
