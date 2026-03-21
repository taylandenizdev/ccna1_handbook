# 📡 Modül 4: Fiziksel Katman (1. Katman)
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  


---

## 📋 İçindekiler

- [OSI Referans Modeli Hatırlatma](#-osi-referans-modeli-hatırlatma)
- [Fiziksel Katmanın Amacı](#-fiziksel-katmanın-amacı)
- [Fiziksel Katman Özellikleri](#-fiziksel-katman-özellikleri)
- [Bant Genişliği Terminolojisi](#-bant-genişliği-terminolojisi)
- [Bakır Kablolama](#-bakır-kablolama)
- [UTP Kablolama](#-utp-kablolama)
- [Fiber Optik Kablolama](#-fiber-optik-kablolama)
- [Kablosuz Medya](#-kablosuz-medya)
- [Özet Karşılaştırma Tablosu](#-özet-karşılaştırma-tablosu)

---

## 🔁 OSI Referans Modeli Hatırlatma

### Katmanlı Yapı ve PDU İsimleri

```
┌─────────────────────────────────────────────────────┐
│  7 - Uygulama   (Application)   ┐                   │
│  6 - Sunum      (Presentation)  ├─ DATA              │
│  5 - Oturum     (Session)       ┘                   │
│  4 - Taşıma     (Transport)     → SEGMENT           │
│  3 - Ağ         (Network)       → PAKET             │
│  2 - Veri Bağ.  (Data Link)     → FRAME             │
│  1 - Fiziksel   (Physical)      → BIT               │
└─────────────────────────────────────────────────────┘
```

### Paket Kapsülleme Yapısı

```
APPLICATION LAYER DATA
         │
         ▼
┌────────┬──────────────────────────────────┐  ← SEGMENT
│  TCP   │         TCP Data                 │
│ Header │                                  │
└────────┴──────────────────────────────────┘
         │
         ▼
┌────────┬──────────────────────────────────┐  ← PAKET
│   IP   │           IP Data                │
│ Header │                                  │
└────────┴──────────────────────────────────┘
         │
         ▼
┌────────┬──────────────────────────────────┬─────┐  ← FRAME
│  ETH   │       46…1500 Bytes Eth Data     │ CRC │
│ Header │                                  │     │
└────────┴──────────────────────────────────┴─────┘
```

### Cihazların Katman Düzeyleri

| Cihaz | Hangi Katmana Kadar Çalışır | Hangi Adrese Bakar |
|-------|----------------------------|--------------------|
| **Hub** | Katman 1 | — |
| **Switch** | Katman 2 | MAC Adresi |
| **Router** | Katman 3 | IP Adresi |

---

## 🎯 Fiziksel Katmanın Amacı

### Fiziksel Bağlantı

Herhangi bir ağ iletişimi gerçekleşmeden önce, yerel bir ağa **fiziksel bağlantı** kurulmalıdır.

- Bağlantı **kablolu** veya **kablosuz** olabilir
- **NIC (Network Interface Card)** cihazı ağa bağlar
- Bazı cihazlarda birden çok NIC bulunabilir (örn: laptop → Ethernet NIC + Wireless NIC)
- Her NIC'in kendine özgü bir **MAC adresi** vardır

```
Laptop
 ├── Ethernet NIC  → MAC: AA:BB:CC:11:22:33
 ├── Wireless NIC  → MAC: AA:BB:CC:44:55:66
 └── Bluetooth     → MAC: AA:BB:CC:77:88:99
```

### Fiziksel Katmanın Temel Görevi

```
Veri Bağlantısı Katmanından
tam bir FRAME alır
        │
        ▼
BİT dizisine dönüştürür (0 ve 1)
        │
        ▼
Sinyallere çevirir
        │
        ▼
Ortama (kablo/kablosuz) iletir
```

---

## ⚙️ Fiziksel Katman Özellikleri

### Standartları Geliştiren Kuruluşlar

| Katman | Kuruluş | Ne Geliştirir |
|--------|---------|---------------|
| Yazılım (L3-L7) | **IETF** | TCP/IP protokolleri, RFC dökümanları |
| Donanım (L1-L2) | **IEEE** | Ethernet, Wi-Fi standartları |
| Donanım (L1-L2) | **EIA/TIA** | Kablolama standartları |
| Donanım (L1-L2) | **ANSI** | Çeşitli fiziksel standartlar |
| Donanım (L1-L2) | **ITU-T** | Telekom standartları |

### Fiziksel Katmanın 3 İşlevsel Alanı

```
┌─────────────────────────────────────────────────┐
│         FİZİKSEL KATMAN STANDARTLARI            │
├─────────────────┬───────────────┬───────────────┤
│  FİZİKSEL       │   KODLAMA     │  SİNYALLEME   │
│  BİLEŞENLER     │  (Encoding)   │  (Signaling)  │
├─────────────────┼───────────────┼───────────────┤
│ • NIC           │ • Manchester  │ • Voltaj      │
│ • Kablo         │ • 4B/5B       │   seviyeleri  │
│ • Konektör      │ • 8B/10B      │ • Işık darb.  │
│ • Switch portu  │               │ • Radyo dalg. │
└─────────────────┴───────────────┴───────────────┘
```

### Kodlama (Encoding)

Bit akışını ortamdaki cihazların tanıyabileceği formata dönüştürme işlemi.

**Manchester Encoding örneği:**
```
Bit:    0    1    0    0    1    1    0
       ___       ___  ___       
Volt: _│  │___│  │  │___│___│  │___
       ↑       ↑
      Düşüş=0  Yükseliş=1
```

### Sinyalleme (Signaling)

| Ortam | Sinyal Türü |
|-------|-------------|
| **Bakır kablo** | Elektrik sinyalleri (voltaj seviyeleri) |
| **Fiber optik** | Işık darbeleri (lazer / LED) |
| **Kablosuz** | Elektromanyetik dalgalar (AM / FM / PM) |

---

## 📊 Bant Genişliği Terminolojisi

### Birimler

| Birim | Kısaltma | Denklik |
|-------|----------|---------|
| Saniyede bit | bps | 1 bps |
| Saniyede kilobit | Kbps | 1.000 bps |
| Saniyede megabit | Mbps | 1.000.000 bps |
| Saniyede gigabit | Gbps | 1.000.000.000 bps |
| Saniyede terabit | Tbps | 1.000.000.000.000 bps |

### Önemli Kavramlar

```
┌──────────────────────────────────────────────────┐
│  BANT GENİŞLİĞİ   → Teorik maksimum kapasite    │
│  (Bandwidth)         Örn: 100 Mbps hat           │
├──────────────────────────────────────────────────┤
│  THROUGHPUT        → Gerçek veri akış hızı       │
│  (Verim)             Örn: Hız testinde 80 Mbps   │
├──────────────────────────────────────────────────┤
│  GOODPUT           → Saf data iletim hızı        │
│                      Throughput - başlık bilgisi  │
│                      Örn: 77 Mbps                │
├──────────────────────────────────────────────────┤
│  LATENCY           → Gecikme süresi              │
│  (Gecikme)           Ses için < 150ms olmalı     │
└──────────────────────────────────────────────────┘
```

### 💡 Pratik Hesap: Byte/Bit Dönüşümü

> ⚠️ **Dikkat:** 1 Byte = 8 bit

**Örnek:**  
100 MB'lık dosyayı 100 Mbps hattan kaç saniyede iletirsiniz?

```
100 MB  →  100 × 8  =  800 Mbit
800 Mbit ÷ 100 Mbps  =  8 saniye (ideal)
```

---

## 🔌 Bakır Kablolama

### Genel Özellikler

- En **yaygın** kullanılan kablo türü
- **Ucuz** ve kurulumu **kolay**
- Elektrik akımına karşı direnci **düşük**

### 3 Temel Sorun ve Çözümleri

```
┌─────────────────────────────────────────────────────────┐
│  SORUN 1: ATTENUATİON (Zayıflama)                      │
│  Sinyal mesafe arttıkça güç kaybeder                   │
│  ✅ Çözüm: Kablo mesafesini 100 m altında tut          │
├─────────────────────────────────────────────────────────┤
│  SORUN 2: EMI / RFI (Elektromanyetik Girişim)          │
│  Floresan lambalar, elektrik kabloları, UPS, klima     │
│  ✅ Çözüm: Korumalı (STP) kablo kullan, toprakla       │
├─────────────────────────────────────────────────────────┤
│  SORUN 3: CROSSTALK (Çapraz Konuşma)                   │
│  Kablodaki bir telin diğer telleri bozması             │
│  ✅ Çözüm: Bükümlü çift kablo kullan (Cancellation)    │
└─────────────────────────────────────────────────────────┘
```

### Cancellation (İptal) Etkisi

```
Tel A (TX+)  →→→→→→→→→→→→
                              ↗ Manyetik alanlar
Tel B (TX-)  ←←←←←←←←←←←←   birbirini SÖNDÜRÜR
```

Bükümlü çiftlerde zıt yönlü akımlar oluşturduğu manyetik alanları iptal eder → Crosstalk azalır!

### Bakır Kablo Türleri

```
┌──────────────────────────────────────────────────┐
│  1. UTP  (Unshielded Twisted Pair)               │
│     Korumasız Bükümlü Çift                       │
│     ✓ En yaygın  ✓ Ucuz  ✗ Az koruma            │
├──────────────────────────────────────────────────┤
│  2. STP  (Shielded Twisted Pair)                 │
│     Korumalı Bükümlü Çift                        │
│     ✓ İyi koruma  ✗ Pahalı  ✗ Topraklama gerek  │
├──────────────────────────────────────────────────┤
│  3. Koaksiyel Kablo (Coaxial)                    │
│     ✓ Anten bağlantısı  ✓ Kablo TV/internet     │
│     ✗ Data ağlarında artık az kullanılıyor       │
└──────────────────────────────────────────────────┘
```

---

## 🔧 UTP Kablolama

### Kablo Kategorileri

| Kategori | Hız | Yıl | Durum |
|----------|-----|-----|-------|
| Cat 3 | 10 Mbps | 1991 | Eski |
| Cat 5 | 100 Mbps | 1995 | Eski |
| **Cat 5e** | **1 Gbps** | **2001** | **Yaygın** |
| **Cat 6** | **1 Gbps** | **2002** | **Yaygın** |
| **Cat 6A** | **10 Gbps** | **2008** | **Yaygın** |
| Cat 7A | 10 Gbps | 2013 | Mevcut |
| Cat 8.1/8.2 | 25-40 Gbps | 2016 | Kısa mesafe (30m) |

> 📌 Tüm kategoriler maksimum **100 metre** destekler (Cat 8 hariç: 30m)

### IEEE 802.3 Fiziksel Katman Standartları

| IEEE Standartı | İsim | Hız | Mesafe | Kablo |
|----------------|------|-----|--------|-------|
| 802.3i | 10BASE-T | 10 Mbps | 100 m | Cat-3 |
| 802.3u | 100BASE-T | 100 Mbps | 100 m | Cat-5 |
| 802.3ab | 1000BASE-T | 1 Gbps | 100 m | Cat-5e |
| 802.3an | 10GBASE-T | 10 Gbps | 100 m | Cat-6A |
| 802.3bq | 25GBASE-T | 25 Gbps | 30 m | Cat-8 |
| 802.3bq | 40GBASE-T | 40 Gbps | 30 m | Cat-8 |

**İsim Yapısı:** `10BASE-T`
```
 10    BASE    T
 │      │      │
 │      │      └── T = Twisted Pair (Bükümlü çift)
 │      └───────── BASE = Baseband (Tek kanal)
 └──────────────── Hız (Mbps)
```

### T568A ve T568B Renk Kodları

```
         T568B (En Yaygın)        T568A
Pin 1:   Turuncu/Beyaz    │    Yeşil/Beyaz
Pin 2:   Turuncu          │    Yeşil
Pin 3:   Yeşil/Beyaz      │    Turuncu/Beyaz
Pin 4:   Mavi             │    Mavi
Pin 5:   Mavi/Beyaz       │    Mavi/Beyaz
Pin 6:   Yeşil            │    Turuncu
Pin 7:   Kahve/Beyaz      │    Kahve/Beyaz
Pin 8:   Kahve            │    Kahve
```

**10/100 Mbps için kullanılan pinler:** 1, 2 (TX) ve 3, 6 (RX)  
**1 Gbps için kullanılan pinler:** 1, 2, 3, 4, 5, 6, 7, 8 (tüm 8 tel)

### Kablo Türleri

```
┌────────────────────────────────────────────────────────────┐
│  DÜZ KABLO (Straight-through)                             │
│  Her iki ucu T568B veya her iki ucu T568A                 │
│  Kullanım: PC → Switch, Router → Switch                   │
├────────────────────────────────────────────────────────────┤
│  ÇAPRAZ KABLO (Crossover)                                 │
│  Bir ucu T568A, diğer ucu T568B                           │
│  Kullanım: PC → PC, Switch → Switch, Router → Router      │
├────────────────────────────────────────────────────────────┤
│  ROLLOVER KABLO (Console Kablosu)                         │
│  Cisco'ya özel, ağ cihazlarını yönetmek için              │
│  Kullanım: Laptop → Console Port (9600 bps)               │
└────────────────────────────────────────────────────────────┘
```

> 💡 **Günümüzde:** AutoMDIX özelliği sayesinde cihazlar otomatik crosslama yapabilir. Artık düz kablo her yerde çalışır!

### Kablo Seçim Tablosu

| Bağlantı | Kablo Türü |
|----------|-----------|
| PC → Switch | Düz |
| Router → Switch | Düz |
| PC → PC | Çapraz |
| Switch → Switch | Çapraz |
| Router → Router | Çapraz |
| Laptop → Console Port | Rollover |

### Kurumsal Kablolama Altyapısı

```
[PC]──(5mt patch)──[Duvar Prizi]──(max 90mt)──[Patch Panel]──(5mt)──[Switch]
                                    YATAY KABLOLAMA
                                    (Horizontal Cabling)
                                    Max toplam: 100 metre
```

**Dikey Kablolama (Vertical / Backbone Cabling):**
```
[Kat 1 Switch]──fiber──[Kat 2 Switch]──fiber──[Core Switch]──[Router]──[İnternet]
```

---

## 💡 Fiber Optik Kablolama

### Genel Özellikler

| Özellik | Değer |
|---------|-------|
| Hız | 10 Mbps – 800 Gbps+ |
| Mesafe | 550 m – 100+ km |
| EMI/RFI | **Hiç etkilenmez** |
| Kırılganlık | Dikkat gerektirir (90° bükme kırılır) |
| Maliyet | UTP'ye göre daha pahalı |
| Sinyal | Işık darbeleri (Lazer / LED) |

### Fiber Kablo Türleri

```
┌──────────────────────────────────┬──────────────────────────────┐
│  ÇOK MODLU FİBER (MMF)          │  TEK MODLU FİBER (SMF)       │
│  Multimode Fiber                 │  Single-Mode Fiber            │
├──────────────────────────────────┼──────────────────────────────┤
│ Çekirdek (Core): 50/62.5 mikron  │ Çekirdek (Core): 9 mikron    │
│ Kaplama (Cladding): 125 mikron   │ Kaplama (Cladding): 125 mikron│
│ Renk: TURUNCU 🟠                 │ Renk: SARI 🟡                 │
│ Işık kaynağı: LED                │ Işık kaynağı: LAZER          │
│ Mesafe: ~550 metre               │ Mesafe: 10 km – 70+ km       │
│ Maliyet: Daha ucuz               │ Maliyet: Daha pahalı         │
└──────────────────────────────────┴──────────────────────────────┘
```

**Işık Yolu Farkı:**

```
MMF (Çok Modlu):                    SMF (Tek Modlu):
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓    ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
░ ~~~~~~~~~~~~~~~~~~~~~ ░           ░ ─────────────────────── ░
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓    ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
Işık çarpa çarpa gider → Dağılım    Işık düz gider → Az kayıp
```

### SFP Modülleri

```
┌─────────────────────────────────────────────────────────────────┐
│  SFP = Small Form-factor Pluggable                             │
│  Switch üzerindeki boş yuvaya takılan modüler transceiver      │
├──────────────────┬────────────┬──────────────┬─────────────────┤
│  SFP Versiyon    │  Ortam     │  Dalga Boyu  │  Max. Mesafe    │
├──────────────────┼────────────┼──────────────┼─────────────────┤
│  1000BASE-SX     │  MMF       │  850 nm      │  ~550 m         │
│  1000BASE-LX/LH  │  MMF/SMF  │  1300 nm     │  SMF ~10 km     │
│  1000BASE-EX     │  SMF       │  1310 nm     │  ~40 km         │
│  1000BASE-ZX     │  SMF       │  1550 nm     │  ~70 km         │
└──────────────────┴────────────┴──────────────┴─────────────────┘
```

> ⚠️ **Dikkat:** Her iki uçtaki SFP modülleri **aynı tip** olmalıdır! Farklı dalga boyları çalışmaz.

### Fiber Optik Konektörler

| Konektör | Şekil | Kullanım |
|----------|-------|---------|
| **ST** | Yuvarlak, kilitlemeli | Eski sistemler |
| **SC** | Kare, geçmeli | Kurumsal ağlar |
| **LC** | Küçük kare | Modern switch'ler (en yaygın) |

### Fiber Kablo Test Cihazı

**OTDR (Optical Time Domain Reflectometer):**
```
Sinyal gönder ──────────────▶ KOPMA NOKTASI ◀── Yansıma döner
                                    │
              ◀── 3. km'de yansıma aldım!
              
→ Kablo 3. km'de kopmuş, oraya git ve onar!
```

### Fiber Kurulum Akışı

```
[Fiber Kablo]
      │
      ├── Pigtail kaynağı (Füzyon)
      │         │
      │    [Fiber Patch Panel]
      │         │
      │    (Fiber patch kablo, LC-LC)
      │         │
      └────[SFP Modül] ──── [Switch A]
      
      [Switch B] ──── [SFP Modül] ──── [Fiber Patch Panel] ──── pigtail ──── [Fiber Kablo]
```

---

## 📶 Kablosuz Medya

### Özellikleri ve Sınırlamaları

```
AVANTAJLAR:                          KISITLAMALAR:
✓ Mobilite sağlar                    ✗ Kapsama alanı sınırlı
✓ Kablo çekmeye gerek yok            ✗ Girişim (Interference)
✓ Kolay kurulum                      ✗ Güvenlik riskleri
                                     ✗ Paylaşılan ortam (Half-duplex)
```

**Half-Duplex Sorun:**
```
Access Point'te aynı anda sadece 1 cihaz yayın yapabilir!

[AP] ─ A gönderirken → B, C, D bekler
[AP] ─ B gönderirken → A, C, D bekler

200 Mbps hat, 10 kişi → kişi başı ~20 Mbps
```

### Kablosuz Standartlar

| Standart | Teknoloji | Açıklama |
|----------|-----------|---------|
| **IEEE 802.11** | Wi-Fi | Kablosuz LAN |
| **IEEE 802.15** | Bluetooth | Kişisel alan ağı (PAN) |
| **IEEE 802.16** | WiMAX | Geniş alan kablosuz |
| **IEEE 802.15.4** | Zigbee | IoT, düşük güç |

### Wi-Fi Nesilleri

| Wi-Fi Nesli | IEEE Standartı | Hız | Frekans |
|-------------|----------------|-----|---------|
| Wi-Fi 4 | 802.11n | 150 Mbps | 2.4 & 5 GHz |
| Wi-Fi 5 | 802.11ac | 433 Mbps – 6.9 Gbps | 5 GHz |
| **Wi-Fi 6** | **802.11ax** | **600 Mbps – 9.6 Gbps** | **2.4 & 5 GHz** |

### Bluetooth

| Özellik | Değer |
|---------|-------|
| Standart | IEEE 802.15, v5.0 |
| Mesafe | ~240 metre |
| Hız | ~2 Mbps |
| Kullanım | Kulaklık, CarPlay, fare, klavye |

> 📌 **Not:** CarPlay navigasyon verisi 2 Mbps'i aşabilir → Bluetooth yetmez, Wi-Fi bağlantısı da gerekir!

### Zigbee

| Özellik | Değer |
|---------|-------|
| Standart | IEEE 802.15.4 |
| Mesafe | 10 – 30 metre |
| Hız | 250 Kbps |
| Kullanım | IoT, medikal cihazlar, pil uzun ömür |

### WLAN Bileşenleri

```
[Cihaz]──(kablosuz)──[Access Point (AP)]──(UTP kablo)──[Switch]──[Router]──[İnternet]
          802.11ax                                        
          Wi-Fi 6
```

---

## 📊 Özet Karşılaştırma Tablosu

### Kablolu vs Kablosuz vs Fiber

| Özellik | UTP Kablo | Fiber Optik | Kablosuz |
|---------|-----------|-------------|---------|
| **Bant Genişliği** | 10 Mb – 10 Gb | 10 Mb – 100 Gb | Düşük – Orta |
| **Mesafe** | 1 – 100 m | 1 – 100.000 m | 70 – 80 m (iç) |
| **EMI/RFI Koruması** | Düşük | **Hiç etkilenmez** | Orta |
| **Maliyet** | **En düşük** | Yüksek | Orta |
| **Kurulum** | **Kolay** | Zor (füzyon) | Kolay |
| **Güvenlik** | Orta | Yüksek | Düşük |
| **Hareketlilik** | Yok | Yok | **Var** |

### Hangi Ortamda Ne Kullanılır?

```
┌─────────────────────────────────────────────────────────┐
│  Masa → Switch bağlantısı     → UTP (Cat5e / Cat6A)    │
│  Switch → Switch (uzak kat)   → Fiber (MMF/SMF)        │
│  Bina → Bina bağlantısı       → Fiber (SMF)            │
│  Dizüstü / Mobil cihaz        → Wi-Fi                  │
│  Kulaklık / Aksesuvar         → Bluetooth              │
│  IoT sensör                   → Zigbee                 │
└─────────────────────────────────────────────────────────┘
```

---

## 🏠 Ev Modemi (ADSL/Fiber) İçinde Ne Var?

```
┌─────────────────────────────────────────────────────────┐
│             "MODEM" (Aslında Çok Fonksiyonlu)          │
│                                                         │
│  ┌──────┐  ┌──────────┐  ┌────────────┐  ┌─────────┐  │
│  │MODEM │  │  ROUTER  │  │   SWITCH   │  │ ACCESS  │  │
│  │      │  │          │  │ (4 port)   │  │  POINT  │  │
│  │Sinyal│  │IP yönlen.│  │LAN bağlan. │  │ Wi-Fi   │  │
│  │dönüş.│  │          │  │            │  │         │  │
│  └──────┘  └──────────┘  └────────────┘  └─────────┘  │
│                                                         │
│  + DHCP Sunucusu (Otomatik IP dağıtımı)                │
│  + Firewall (MAC filtresi, güvenlik)                   │
└─────────────────────────────────────────────────────────┘
```

---

## 📝 Anahtar Kavramlar Özeti

| Terim | Açıklama |
|-------|---------|
| **NIC** | Network Interface Card – Ağ kartı |
| **UTP** | Unshielded Twisted Pair – Korumasız bükümlü çift |
| **STP** | Shielded Twisted Pair – Korumalı bükümlü çift |
| **Attenuation** | Sinyal zayıflaması |
| **Crosstalk** | Kablo içi tel girişimi |
| **Cancellation** | Bükümlü çiftlerin manyetik alan iptal etkisi |
| **MMF** | Multimode Fiber – Çok modlu fiber (turuncu, ≤550m) |
| **SMF** | Single-Mode Fiber – Tek modlu fiber (sarı, ≤70km) |
| **SFP** | Small Form-factor Pluggable – Modüler transceiver |
| **OTDR** | Optical Time Domain Reflectometer – Fiber test cihazı |
| **AutoMDIX** | Otomatik crossover algılama özelliği |
| **T568A/B** | UTP kablo sonlandırma standartları |
| **Patch Panel** | Kabloların toplandığı pano |
| **Horizontal Cabling** | Yatay kablolama (masa → patch panel) |
| **Backbone/Vertical** | Dikey kablolama (kat arası, switch arası) |
| **Throughput** | Gerçek veri akış hızı |
| **Goodput** | Saf data iletim hızı (başlıklar çıkarılmış) |
| **Latency** | Gecikme süresi |

---

*📚 CCNA1 – Introduction to Networks v7.0 | Modül 4: Fiziksel Katman*
