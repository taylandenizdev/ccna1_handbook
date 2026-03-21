# 📘 Modül 7: Ethernet Anahtarlama
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Kaynak: Cisco Networking Academy

---

## 📋 İçindekiler
1. [Ethernet Çerçeve Yapısı](#1-ethernet-çerçeve-yapısı)
2. [MAC Adresleri](#2-mac-adresleri)
3. [MAC Adres Türleri](#3-mac-adres-türleri)
4. [MAC Adres Tablosu ve Switch Çalışma Mantığı](#4-mac-adres-tablosu-ve-switch-çalışma-mantığı)
5. [Switch İletme Yöntemleri](#5-switch-i̇letme-yöntemleri)
6. [Hafıza Tamponlama](#6-hafıza-tamponlama)
7. [Port Ayarları: Duplex, Hız ve Auto-MDIX](#7-port-ayarları-duplex-hız-ve-auto-mdix)
8. [Modül Özeti](#8-modül-özeti)

---

## 1. Ethernet Çerçeve Yapısı

### 🏛️ Ethernet'in Kısa Tarihçesi
Ethernet, Xerox tarafından geliştirilmiş; daha sonra Digital, Intel ve Xerox iş birliğiyle **Ethernet II** standardı oluşturulmuştur. IEEE bu standardı **802.3** adıyla resmileştirmiştir. Günümüzde "Ethernet" ve "IEEE 802.3" kavramları büyük ölçüde eşanlamlı kullanılmaktadır.

### 📐 OSI Katmanlarıyla İlişkisi
Ethernet hem **2. Katman (Veri Bağlantısı)** hem de **1. Katman (Fiziksel)** protokolüdür.

```
┌─────────────────────────────────────────────────────────────────┐
│                   ETHERNETİN KATMAN YAPISI                      │
├────────────────────────────────┬────────────────────────────────┤
│       2. KATMAN (Data Link)    │       1. KATMAN (Physical)      │
│                                │                                 │
│  LLC Alt Katmanı (802.2):      │  • Kablo tipleri (UTP / Fiber) │
│  • Üst katman protokolünü      │  • Konnektörler                │
│    belirtir (IPv4/IPv6)        │  • Data hızları                │
│                                │  • Sinyal kodlama              │
│  MAC Alt Katmanı (802.3):      │                                 │
│  • Framing (çerçeveleme)       │  Desteklenen hızlar:           │
│  • Hata kontrolü (FCS/CRC)     │  10M / 100M / 1G / 10G         │
│  • Ortam erişim kontrolü       │  40G / 100G / 200G / 400G...   │
│    (CSMA/CD)                   │                                 │
└────────────────────────────────┴────────────────────────────────┘
```

### 🗂️ Ethernet II Çerçeve Yapısı

```
┌───────────┬─────┬──────────────┬──────────────┬───────┬────────────────────┬─────┐
│ Preamble  │ SFD │  Dest. MAC   │  Src. MAC    │ Type  │       Data         │ FCS │
│  7 byte   │1byte│   6 byte     │   6 byte     │2 byte │   46–1500 byte     │4byte│
└───────────┴─────┴──────────────┴──────────────┴───────┴────────────────────┴─────┘
 ← 1. Katman →    ← ─ ─ ─ ─ ─ ─ ─ ─ 2. Katman Frame  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ →
```

### 📋 Alan Açıklamaları

| Alan | Boyut | Açıklama |
|---|---|---|
| **Preamble** | 7 byte | 1-0 dizisi; senkronizasyon için |
| **SFD** (Start Frame Delimiter) | 1 byte | Frame'in başladığını belirtir (son 2 bit: 11) |
| **Hedef MAC Adresi** | 6 byte | Paketin gideceği cihazın adresi |
| **Kaynak MAC Adresi** | 6 byte | Gönderen cihazın adresi |
| **Type** | 2 byte | IPv4 = `0x0800`, IPv6 = `0x86DD` |
| **Data** | 46–1500 byte | 3. Katman paketi (IP + TCP + Veri) |
| **FCS** (Frame Check Sequence) | 4 byte | CRC algoritmasıyla hesaplanan hata kontrol değeri |

### 📏 Çerçeve Boyutları

```
Minimum frame boyutu: 64 byte   (46 byte data + 18 byte başlık/kuyruk)
Maksimum frame boyutu: 1518 byte (1500 byte data + 18 byte başlık/kuyruk)

  < 64 byte   → Runt Frame / Collision Fragment → Çöpe atılır
  > 1518 byte → Baby Giant Frame (VLAN tag eklenince 1522 byte olur)
  > 1600 byte → Jumbo Frame (9000 byte'a kadar, NAS yedekleme vb.)
```

### 🔍 FCS Hata Kontrolü Nasıl Çalışır?

```
GÖNDERME TARAFINDA:
  [Ethernet Header + Data] → CRC polinom bölümü → FCS değeri hesapla → Pakete ekle → Gönder

ALMA TARAFINDA:
  [Ethernet Header + Data] → CRC polinom bölümü → Değer hesapla
                                                          │
                              FCS alanındaki değerle karşılaştır
                              │
                    Eşleşiyor → Paketi kabul et ✓
                    Farklı    → Paketi çöpe at ✗ (kimseye bilgi vermez)

 ⚠ Ethernet hata düzeltme YAPMAZ, sadece hata TESPİT eder.
 ⚠ Bozuk paket üst katmanlara çıkmaz; TCP gerekirse yeniden ister.
```

---

## 2. MAC Adresleri

### 🔢 MAC Adres Yapısı
```
┌─────────────────────────────────────────────────────────────────┐
│  MAC ADRESİ = 48 bit = 6 byte = 12 hex basamak                  │
│                                                                 │
│  ┌──────────────────────────┬───────────────────────────────┐  │
│  │   OUI (Üretici Kodu)     │   Vendor Assigned (Seri No)   │  │
│  │   24 bit = 6 hex         │   24 bit = 6 hex              │  │
│  │   (ilk 3 byte)           │   (son 3 byte)                │  │
│  └──────────────────────────┴───────────────────────────────┘  │
│                                                                 │
│  Örnek:  1C - 39 - 47  -  2F - 3A - 24                         │
│          └── OUI ────┘  └──── Seri ────┘                       │
│          (Üretici: Cisco gibi)                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 📌 OUI (Organizationally Unique Identifier)
- IEEE tarafından üreticilere atanır (yaklaşık 3.000 USD, bir kez ödeme)
- Her OUI numarasıyla üretici **~16 milyon** benzersiz MAC adresi tanımlayabilir
- Bir üretici birden fazla OUI alabilir (Apple, Samsung vb.)
- OUI sorgulama: `maclookup.app` veya `Wireshark OUI lookup`

### 🖥️ MAC Adresini Görme
```bash
# Windows
ipconfig /all          # "Physical Address" satırı
                       # Örnek: 00-60-2F-3A-07-BC

# Linux / macOS
ifconfig               # ya da ip link show

# Notasyon farkları (hepsi aynı adres):
  00-60-2F-3A-07-BC   ← Windows stili (tire)
  00:60:2F:3A:07:BC   ← Linux/macOS stili (kolon)
  0060.2F3A.07BC      ← Cisco stili (nokta, 4'lü gruplar)
```

> **💡 Bir cihazda kaç MAC adresi olur?**  
> Her ağ arayüzü için bir tane: Ethernet NIC → 1 MAC, Wi-Fi → 1 MAC, Bluetooth → 1 MAC  
> Toplam 3 ağ arayüzü olan bir laptop'ta 3 ayrı MAC adresi bulunur.

### 🔒 MAC Adresi Güvenlik Notu
- MAC adresi **sadece aynı ağda** geçerlidir; farklı ağlara geçmez
- `macchanger` (Linux) ile yazılımsal olarak değiştirilebilir
- Modern cihazlarda **MAC randomization** özelliği var (takip önleme)
- Güvenlik analizinde OUI'dan cihaz markası/modeli hakkında ipucu alınabilir

---

## 3. MAC Adres Türleri

```
┌─────────────────────────────────────────────────────────────────┐
│                   MAC ADRES TÜRLERİ                             │
├──────────────────┬──────────────────┬───────────────────────────┤
│   UNİCAST        │   BROADCAST       │   MULTİCAST               │
├──────────────────┼──────────────────┼───────────────────────────┤
│ Tek bir cihaza   │ Ağdaki herkese   │ Belirli bir gruba         │
│                  │                  │                           │
│ 00-07-E9-63-CE-53│ FF-FF-FF-FF-FF-FF│ IPv4: 01-00-5E-xx-xx-xx  │
│                  │ (12 × F)         │ IPv6: 33-33-xx-xx-xx-xx  │
│ • Kaynak MAC her │                  │                           │
│   zaman unicast  │ • Switch: giriş  │ • Gruba üye cihazlar      │
│ • ARP ile hedef  │   port hariç     │   kabul eder              │
│   MAC öğrenilir  │   tüm portlara   │ • Akıllı switch (IGMP     │
│ • IPv4: ARP      │ • Router GEÇİRMEZ│   Snooping) sadece        │
│ • IPv6: NDP      │                  │   ilgililere yollar       │
└──────────────────┴──────────────────┴───────────────────────────┘
```

### 🌐 Unicast Haberleşme Akışı
```
PC_A (IP: 1.5, MAC: AA-AA)  →  Sunucu (IP: 1.200, MAC: ?)

  Adım 1: "1.200'ün MAC'i nedir?" → ARP Request (broadcast)
  Adım 2: Sunucu cevap verir      → ARP Reply (unicast)
  Adım 3: ARP tablosuna yaz       → 1.200 = SS-SS-SS-SS
  Adım 4: Frame gönder            → Dest MAC: SS-SS | Src MAC: AA-AA
```

### 📡 Broadcast Kullanım Alanları
```
Broadcast MAC (FF:FF:FF:FF:FF:FF) kullanılan durumlar:
  • ARP Request  (IP'den MAC sorma)
  • DHCP Discover (IP almak için sunucu arama)
  • 255.255.255.255 IP'li paketler

  ⚠ Router broadcast paketleri GEÇİRMEZ → Sadece aynı ağda kalır
```

### 📺 Multicast Haberleşme
```
Örnek: Radyo/video yayını
  Yayın IP'si: 224.1.2.3  (224–239 arası = Multicast)
  MAC Adresi : 01-00-5E-01-02-03

  A cihazı yayına üye oldu → Switch "A dinlemek istiyor" notu aldı
  B cihazı yayına üye oldu → Switch "B de istiyor" notu aldı
  C cihazı üye değil

  Yayın geldiğinde:
  - Akıllı switch (IGMP Snooping açık): Sadece A ve B'ye gönderir ✓
  - Aptal switch (IGMP Snooping yok):  Herkese gönderir (broadcast gibi)
```

---

## 4. MAC Adres Tablosu ve Switch Çalışma Mantığı

### ⚖️ Hub vs. Switch Karşılaştırması

```
┌───────────────────────────────┬───────────────────────────────┐
│           HUB                 │           SWITCH               │
├───────────────────────────────┼───────────────────────────────┤
│  1. Katman cihazı             │  2. Katman cihazı             │
│  MAC adresine BAKMAZ          │  MAC adresine BAKAR           │
│  Gelen sinyali tüm portlara   │  Sadece ilgili porta iletir   │
│  iletir (kuvvetlendirerek)    │                               │
│  HALF DUPLEX                  │  FULL DUPLEX                  │
│  (aynı anda tek kişi gönderir)│  (aynı anda herkes gönderir) │
│  Collision oluşabilir         │  Collision olmaz              │
│  CSMA/CD gerekli              │  CSMA/CD gerekmez             │
│  10 Mbps paylaşımlı           │  1 Gbps/port bağımsız        │
│  Günümüzde kullanılmıyor ✗    │  Günümüzde standart ✓        │
└───────────────────────────────┴───────────────────────────────┘
```

### 🔄 Switch MAC Adres Tablosu Oluşturma Akışı

```
┌─────────────────────────────────────────────────────────────────┐
│          SWITCH'E FRAME GELDİĞİNDE YAPILAN 2 ADIM              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ADIM 1 — ÖĞREN (Kaynak MAC'i tabloya ekle)                    │
│  ─────────────────────────────────────────────────────────────  │
│  Frame hangi porttan geldi? → Kaynak MAC hangi port = tabloya  │
│  Zaten varsa → 300 saniyelik sayacı sıfırla (yenile)           │
│  300 saniye boyunca paket gelmezse → tablodan sil              │
│                                                                 │
│  ADIM 2 — İLET (Hedef MAC'e göre karar ver)                    │
│  ─────────────────────────────────────────────────────────────  │
│  Hedef MAC tabloda VAR   → Sadece o porta ilet ✓               │
│  Hedef MAC tabloda YOK   → Giriş portu hariç tüm portlara ilet │
│    (Unknown Unicast = Flooding)                                 │
│  Hedef MAC = FF:FF:FF:FF → Giriş portu hariç tüm portlara ilet │
│    (Broadcast Flooding)                                         │
└─────────────────────────────────────────────────────────────────┘
```

### 🗺️ Çok Switch'li Topolojide Öğrenme

```
       [SW1]                    [SW2]
  1─A   2─B   3─(SW2'ye)    6─C   7─D   8─R
                Port 3         Port 6

A'dan D'ye ping:
  1. SW1: "A Port1'de" → tabloya ekle. D bilinmiyor → Flood et
  2. SW2: "A Port6'da" → tabloya ekle. D Port7'de → sadece D'ye yollar
  3. D cevap verir → SW2: "D Port7'de" tabloya ekle
                   → SW1: "D Port3'te" tabloya ekle

Sonuç: Her iki switch de 5 cihazın (A,B,C,D,R) MAC adresini öğrenir.
Switch'lerin IP'si veya MAC'i yoktur; sadece tablo tutarlar.
```

### 📊 Örnek MAC Adres Tablosu
```
SW1# show mac-address-table

Port    MAC Address        VLAN
──────────────────────────────
Fa0/1   AA-AA-AA-AA-AA-AA  1      ← A cihazı
Fa0/2   BB-BB-BB-BB-BB-BB  1      ← B cihazı
Fa0/3   CC-CC-CC-CC-CC-CC  1      ← C cihazı (SW2 üzerinden)
Fa0/3   DD-DD-DD-DD-DD-DD  1      ← D cihazı (SW2 üzerinden)
Fa0/3   RR-RR-RR-RR-RR-RR  1      ← Router (SW2 üzerinden)
```

> **📌 CAM Tablosu:** MAC adres tablosu özel bir bellekte tutulur: **Content Addressable Memory (CAM)**. Bu bellek türü, MAC adreslerini çok hızlı yazıp okuyabilir.

### ⚡ Switch Anahtarlama Kapasitesi Örneği
```
Cisco C9200L-24T-4X:
  24 × 1 Gbps port (TX + RX = 2 Gbps/port)
  4 × 10 Gbps uplink port

  24 × 2 Gbps = 48 Gbps
  4 × 20 Gbps = 80 Gbps
  ────────────────────
  Toplam: 128 Gbps anahtarlama kapasitesi
  MAC tablosu: 32.000 giriş
```

---

## 5. Switch İletme Yöntemleri

### 🔀 İki Temel Yöntem

```
┌─────────────────────────────────────────────────────────────────┐
│  STORE-AND-FORWARD SWITCHING (Depola ve İlet)                   │
│  ─────────────────────────────────────────────────────────────  │
│  1. Tüm frame'i al (1518 byte'ın tamamı)                        │
│  2. FCS/CRC hata kontrolü yap                                   │
│  3. Hatalıysa çöpe at ✗                                        │
│  4. Hatasızsa hedef MAC'e bak → ilgili porta ilet ✓            │
│                                                                 │
│  ✓ Avantaj: Bozuk frame'ler hedefe ulaşmaz (bant genişliği     │
│             israfı önlenir)                                     │
│  ✓ QoS (VoIP önceliklendirme) için gerekli                     │
│  ✗ Dezavantaj: Daha yavaş (tüm frame beklenir)                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│  CUT-THROUGH SWITCHING (Kesmeli Geçiş)                          │
│  ─────────────────────────────────────────────────────────────  │
│  Frame tamamen alınmadan iletim başlar — daha hızlı             │
│                                                                 │
│  İki alt yöntem:                                                │
│                                                                 │
│  a) FAST-FORWARD:                                               │
│     → İlk 6 byte (hedef MAC) okunur okumaz ilet                │
│     → En hızlı yöntem                                          │
│     → Hata kontrolü YOK                                        │
│                                                                 │
│  b) FRAGMENT-FREE:                                              │
│     → İlk 64 byte okunur, sonra iletim başlar                  │
│     → Çoğu collision hatası ilk 64 byte'ta oluşur              │
│     → Hızlı ve kısmi güvenlik sağlar                           │
└─────────────────────────────────────────────────────────────────┘
```

### 📊 Görsel Karşılaştırma
```
Frame (1518 byte)
│─── 6 ───│─── 6 ───│── 2 ──│─────────────────────────│─ 4 ─│
│ Dst MAC │ Src MAC │ Type  │           Data           │ FCS │

Fast-Forward:  ├──►         (6 byte okuyunca ilet)
Fragment-Free: ├──────────────────────────────►       (64 byte okuyunca ilet)
Store&Forward: ├───────────────────────────────────────────────────────────►
               (1518 byte'ın tamamı alınır, FCS kontrol edilir, sonra iletilir)
```

### 🔁 Otomatik Mod Değişimi
Kurumsal switch'ler istatistiklere bakarak otomatik geçiş yapabilir:
- Çok fazla hatalı frame geliyorsa → **Cut-Through'dan Store-and-Forward'a** geçer
- Hata oranı düştüğünde → tekrar Cut-Through'a döner

---

## 6. Hafıza Tamponlama

Farklı hızlardaki portlar arasında geçiş yapılırken paketler kuyrukta bekletilir.

```
┌──────────────────────┬────────────────────────────────────────┐
│  Port Bazlı Hafıza   │  Her port için ayrı kuyruk             │
│  (Port-based Memory) │  1 Gbps → 100 Mbps: kuyrukta tıkanma  │
│                      │  Bir meşgul port diğerlerini yavaşlatır│
├──────────────────────┼────────────────────────────────────────┤
│  Paylaşılan Hafıza   │  Tüm portlar ortak havuzdan kullanır   │
│  (Shared Memory)     │  Daha verimli kaynak kullanımı         │
│                      │  Yeni nesil Cisco switch'lerde kullanılır│
│                      │  Port başına dinamik alan tahsisi      │
└──────────────────────┴────────────────────────────────────────┘

Kuyruk dolarsa → Yeni gelen paketler çöpe atılır
TCP bunu fark eder ve eksik paketi yeniden ister.
```

---

## 7. Port Ayarları: Duplex, Hız ve Auto-MDIX

### ⚙️ Switch Port Konfigürasyon Komutları
```bash
SW1(config)# interface FastEthernet 0/1
SW1(config-if)# speed ?
    10    → 10 Mbps'ye zorla
    100   → 100 Mbps'ye zorla
    auto  → Otomatik anlaşma (varsayılan)

SW1(config-if)# duplex ?
    auto  → Otomatik anlaşma (varsayılan)
    full  → Full duplex'e zorla
    half  → Half duplex'e zorla

SW1(config-if)# mdix auto   ← Varsayılan (açık)
SW1(config-if)# no mdix auto ← Kapatır

# Tüm varsayılan değerler:
SW1(config-if)# speed auto
SW1(config-if)# duplex auto
SW1(config-if)# mdix auto
```

### 🔄 Duplex Ayarları

```
┌─────────────────────────────────────────────────────────────────┐
│  FULL DUPLEX                                                    │
│  • Aynı anda hem gönderip hem alabilirsiniz                    │
│  • Switch ortamı — birbirinden bağımsız kanallar               │
│  • Collision olmaz → CSMA/CD gerekmez                          │
│  • Gigabit Ethernet SADECE full duplex çalışır                 │
├─────────────────────────────────────────────────────────────────┤
│  HALF DUPLEX                                                    │
│  • Aynı anda ya gönderirsiniz ya alırsınız                     │
│  • Hub ortamı / Wi-Fi / bazı endüstriyel cihazlar              │
│  • Collision oluşabilir → CSMA/CD kullanılır                   │
│  • 10/100 Mbps bağlantılarda mümkün                            │
├─────────────────────────────────────────────────────────────────┤
│  AUTO NEGOTIATION (Varsayılan)                                  │
│  • Karşı tarafla en yüksek ortak hız ve duplex belirlenir      │
│  • 1 Gbps Full → 100 Mbps Full → 100 Mbps Half → ...          │
└─────────────────────────────────────────────────────────────────┘
```

### ⚠️ Duplex Uyumsuzluğu (Duplex Mismatch)
```
       [SW1 — Full Duplex]  ←→  [SW2 — Half Duplex]
                             💥
  SW1: "Aynı anda gönderebilirim"
  SW2: "Hat meşgulken gönderemem!"
  Sonuç: SW2 sürekli collision algılar, paket kayıpları yaşanır

  Çözüm: İkisini de "auto" yapın veya ikisini de aynı değere zorla
  En iyi uygulama: Her iki tarafı da full-duplex olarak yapılandır
```

### 🔌 Auto-MDIX (Otomatik Kablo Tipi Tanıma)
```
Eski sistemlerde kablo seçimi önemliydi:
  Switch ↔ Switch    → Crossover (Çapraz) kablo
  Switch ↔ Router    → Düz (Straight-through) kablo
  Switch ↔ PC        → Düz (Straight-through) kablo
  PC ↔ PC            → Crossover (Çapraz) kablo

Auto-MDIX ile (varsayılan olarak açık):
  Hangi kablo takarsanız takın → Çalışır ✓
  Cihaz kablo tipini otomatik algılar ve interface'i ayarlar

Komutlar:
  mdix auto    → Auto-MDIX açık (varsayılan)
  no mdix auto → Kapalı (eski kablo kuralları geçerli olur)
```

---

## 8. Modül Özeti

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODÜL 7 ÖZET                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ETHERNETİN KATMAN YAPISI                                       │
│  • Layer 1 + Layer 2 protokolü (IEEE 802.3)                    │
│  • LLC alt katmanı: IPv4/IPv6 üst katman yönlendirme           │
│  • MAC alt katmanı: Framing + Ortam erişim kontrolü            │
│                                                                 │
│  ETHERNET II ÇERÇEVE ALANLARI                                   │
│  Preamble | SFD | Dst MAC | Src MAC | Type | Data | FCS        │
│  Min: 64 byte  |  Maks: 1518 byte                              │
│  FCS: CRC algoritmasıyla hata tespiti                          │
│                                                                 │
│  MAC ADRESLERİ                                                  │
│  • 48 bit = 12 hex basamak                                     │
│  • İlk 6 hex = OUI (üretici kodu, IEEE atar)                   │
│  • Son 6 hex = Seri numarası (üretici atar)                    │
│  • ipconfig /all komutuyla görülebilir                         │
│                                                                 │
│  MAC ADRES TÜRLERİ                                              │
│  • Unicast: Tek cihaz (Kaynak her zaman unicast)               │
│  • Broadcast: FF-FF-FF-FF-FF-FF → Tüm ağ (router geçirmez)    │
│  • Multicast: 01-00-5E-xx-xx-xx → Gruba (IPv4)                │
│                                                                 │
│  SWITCH ÇALIŞMA MANTIĞI                                         │
│  Adım 1: Kaynak MAC → giriş portu ile tabloya ekle             │
│  Adım 2: Hedef MAC tabloda var → sadece o porta ilet           │
│          Tabloda yok (Unknown Unicast) → Flood (herkese)       │
│          Broadcast → Flood (herkese)                           │
│  300 saniye sessizlik → tablo girişi silindi                   │
│  Tablo adı: CAM (Content Addressable Memory)                   │
│                                                                 │
│  İLETME YÖNTEMLERİ                                             │
│  Store-and-Forward: Tüm frame → FCS kontrol → ilet (yavaş/sağlıklı)│
│  Cut-Through Fast-Forward: 6 byte (dst MAC) → ilet (en hızlı) │
│  Cut-Through Fragment-Free: 64 byte → ilet (orta)             │
│                                                                 │
│  PORT AYARLARI (Varsayılan: auto)                               │
│  • speed: 10 / 100 / auto                                      │
│  • duplex: full / half / auto                                  │
│  • mdix: auto (düz veya cross kablo fark etmez)                │
│  • Gigabit Ethernet SADECE full duplex çalışır                 │
└─────────────────────────────────────────────────────────────────┘
```

---

> **📌 Sonraki Modül:** IPv4 Adresleme — IP adres yapısı ve subnetting.

---
*CCNA1 – Introduction to Networks v7.0 | Modül 7*
