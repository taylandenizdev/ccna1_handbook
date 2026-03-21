# 📘 Modül 6: Veri Bağlantısı Katmanı (2. Katman — Data Link Layer)
> **CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Kaynak: Cisco Networking Academy

---

## 📋 İçindekiler
1. [Veri Bağlantısı Katmanının Amacı](#1-veri-bağlantısı-katmanının-amacı)
2. [LLC ve MAC Alt Katmanları](#2-llc-ve-mac-alt-katmanları)
3. [Topolojiler](#3-topolojiler)
4. [Yarı ve Tam Çift Yönlü İletişim](#4-yarı-ve-tam-çift-yönlü-i̇letişim)
5. [Ortam Erişim Kontrol Yöntemleri](#5-ortam-erişim-kontrol-yöntemleri)
6. [Veri Bağlantısı Çerçevesi](#6-veri-bağlantısı-çerçevesi)
7. [Modül Özeti](#7-modül-özeti)

---

## 1. Veri Bağlantısı Katmanının Amacı

### 🎯 Temel Görev
Veri Bağlantısı Katmanı (Layer 2), **aynı ağdaki** uç cihazların ağ arayüz kartları (NIC) arasındaki iletişimden sorumludur.

### ⚙️ İki Temel İşlev

```
┌─────────────────────────────────────────────────────────────────┐
│              VERİ BAĞLANTISI KATMANININ İŞLEVLERİ               │
├──────────────────────────────┬──────────────────────────────────┤
│        1. FRAMING            │    2. MEDIA ACCESS CONTROL       │
│                              │           (MAC)                  │
│  • 2. Katman başlık (header) │  • Fiziksel ortama geçişi        │
│    bilgisi ekler             │    kontrol eder                  │
│  • 2. Katman kuyruk          │  • Kim, ne zaman gönderebilir?   │
│    (trailer) bilgisi ekler   │  • Örn: CSMA/CD, CSMA/CA         │
│  • Hata kontrol bilgisi      │  • Token Passing algoritması     │
│    (CRC) içerir              │                                  │
│  Protokol örnekleri:         │                                  │
│  IEEE 802.3 (Ethernet)       │                                  │
│  IEEE 802.11 (Wi-Fi)         │                                  │
│  PPP, HDLC, Frame Relay      │                                  │
└──────────────────────────────┴──────────────────────────────────┘
```

### 🔄 Kapsülleme Hatırlatması (OSI Modeli)
```
┌───────────────────────────────────────────────────────────────┐
│   UYGULAMA KATMANI VERİSİ                                     │
│   ┌──────┬──────────────────────────────────────────────────┐ │
│   │ UDP  │  UDP Data                               8 BYTE   │ │
│   ├──────┴──────────────────────────────────────────────────┤ │
│   │ TCP Header  │  TCP Data                        20 BYTE  │ │
│   ├─────────────┴──────────────────────────────────────────┤ │
│   │ IP Header   │  IP Data                         20 BYTE  │ │
│   ├─────────────┴──────────────────────────────────────────┤ │
│   │ Ethernet    │  46–1500 Bytes Ethernet Data  │   CRC  │  │ │
│   │ Header      │                               │        │  │ │
│   └─────────────┴───────────────────────────────┴────────┘  │ │
│                                                  18 BYTE      │
└───────────────────────────────────────────────────────────────┘
```

### 📦 Yönlendiricide (Router) 2. Katman İşlemi
Bir router, paket alıp iletirken şu 4 işlemi yapar:

```
Gelen çerçeve
     │
     ▼
[1] Çerçeveyi ortamdan al
     │
     ▼
[2] 2. Katman bilgisini çıkar (kapsülden çıkar)
     │
     ▼
[3] 3. Katmanda yol seçimi yap (routing)
     │
     ▼
[4] Paketi yeni 2. Katman çerçevesiyle yeniden kapsülle
     │
     ▼
Giden çerçeve (farklı protokol olabilir: Ethernet → PPP → HDLC)
```

> **Önemli:** Layer 2 adresleri (MAC adresleri) her atlamada (hop) değişir.  
> Layer 3 adresleri (IP adresleri) kaynak ve hedef boyunca sabit kalır.

---

## 2. LLC ve MAC Alt Katmanları

Veri Bağlantısı Katmanı, IEEE 802 standardına göre **iki alt katmana** ayrılır:

```
┌─────────────────────────────────────────────────────────────────┐
│                        AĞ KATMANI (Layer 3)                     │
│              IPv4 / IPv6 Protokol Paketi                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │         LLC ALT KATMANI  (IEEE 802.2)                   │  │
│   │  • Üst katman protokolü tanımlar (IPv4 mı? IPv6 mı?)    │  │
│   │  • Ağ yazılımı ↔ Donanım arasında köprü kurar           │  │
│   └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│   ┌──────────────┬───────────────┬─────────────────────────┐   │
│   │   Ethernet   │  WLAN         │  WPAN                   │   │
│   │  IEEE 802.3  │  IEEE 802.11  │  IEEE 802.15            │   │
│   │  MAC ALT KATMANI                                        │   │
│   │  • Veri kapsülleme (framing)                           │   │
│   │  • Ortam erişim kontrolü (CSMA/CD, CSMA/CA)            │   │
│   └──────────────┴───────────────┴─────────────────────────┘   │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│                    FİZİKSEL KATMAN (Layer 1)                    │
└─────────────────────────────────────────────────────────────────┘
```

| Alt Katman | Açıklama |
|---|---|
| **LLC** (Logical Link Control) | Üst katmana geçiş bilgisi; hangi L3 protokolüne gidileceği (IPv4/IPv6) |
| **MAC** (Media Access Control) | Framing + fiziksel ortama geçiş algoritması |

---

## 3. Topolojiler

### 📐 Fiziksel vs. Mantıksal Topoloji

| Özellik | Fiziksel Topoloji | Mantıksal Topoloji |
|---|---|---|
| **Gösterir** | Kablo, cihaz konumu, fiziksel bağlantılar | IP adresi planı, ağ segmentleri |
| **Örnek** | Hangi odada hangi switch var | Sunuculara 192.168.3.x, WiFi'a 192.168.2.x |
| **Kullanım** | Kablo çekimi, bakım, arıza tespiti | Ağ tasarımı, IP planlama |

---

### 🌐 WAN Topolojileri

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 1. POINT-TO-POINT (Noktadan Noktaya)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [Router İstanbul] ─────────── [Router Ankara]
         Node 1     ISP Hattı       Node 2

  ✔ En basit WAN topolojisi
  ✔ İki uç nokta, doğrudan bağlantı
  ✔ PPP, HDLC protokolleri kullanılır
  ✔ MAC adresi gerekmez (sadece 8-bit 1'ler)
  Örnek: MPLS, Site-to-Site VPN, Leased Line

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 2. HUB AND SPOKE (Merkez ve Kol)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

          [Ankara - Merkez]
          ┌───────┴───────┐
    [İstanbul]    [İzmir]    [Bursa]
         (Spoke)   (Spoke)  (Spoke)

  ✔ Merkez çöküncemüştür herkes etkilenir
  ✔ Şubeler merkez üzerinden haberleşir
  Örnek: Frame Relay, eski WAN yapıları

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 3. MESH (Örgü)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Full Mesh (5 cihaz = 5×4/2 = 10 bağlantı):

      [A]───[B]
      │╲   ╱│╲
      │ ╲ ╱ │ ╲
      │  ╳  │  [E]
      │ ╱ ╲ │ ╱
      [C]───[D]

  ✔ Yüksek erişilebilirlik
  ✔ Pahalı (çok bağlantı gerekir)
  ✔ Partial Mesh: Bazı bağlantılar eksik bırakılır
  Formül: N × (N-1) / 2
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### 🏢 LAN Topolojileri

```
┌──────────────────┐    ┌──────────────────────────────┐
│  STAR (Yıldız)   │    │  EXTENDED STAR (Genişletilmiş│
│                  │    │  Yıldız) — Günümüz Ethernet  │
│    PC1  PC2      │    │                              │
│     \   /        │    │   SW──────SW──────SW         │
│      [SW]        │    │   |       |       |          │
│     /   \        │    │  PC'ler  PC'ler  PC'ler      │
│   PC3   PC4      │    │                              │
└──────────────────┘    └──────────────────────────────┘

┌──────────────────┐    ┌──────────────────────────────┐
│  BUS (Veriyolu)  │    │  RING (Halka)                │
│  İlk Ethernet    │    │  Token Ring teknolojisi       │
│                  │    │                              │
│ ●──[PC]──[PC]──● │    │    PC1──PC2                  │
│  (Koaksiyel kablo)│    │    |        |                │
│  CSMA/CD kullanır│    │   PC4──PC3                  │
│  10 Mbps         │    │  Token Passing algoritması   │
└──────────────────┘    └──────────────────────────────┘
```

> **Not:** Günümüzde Ethernet, **Extended Star** topolojisini switch'lerle kullanır. Bus ve Ring artık tarihsel öneme sahiptir.

---

## 4. Yarı ve Tam Çift Yönlü İletişim

```
╔══════════════════════════════════════════════════════════════════╗
║           HALF DUPLEX (Yarı Çift Yönlü)                        ║
╠══════════════════════════════════════════════════════════════════╣
║  • Aynı anda sadece BİR cihaz gönderebilir                      ║
║  • Gönderirken alamazsın, alırken gönderemezsin                 ║
║  • Paylaşılan tek iletim ortamı var                             ║
║  • Çarpışma (collision) oluşabilir                              ║
║                                                                  ║
║  Ortamlar: Hub, Access Point (Wi-Fi), Eski Ethernet             ║
║  Algoritma: CSMA/CD (kablolu), CSMA/CA (kablosuz)               ║
║                                                                  ║
║  PC_A → → → → [HUB] → → → → PC_B                               ║
║         (PC_B bu sırada gönderemez)                             ║
╠══════════════════════════════════════════════════════════════════╣
║           FULL DUPLEX (Tam Çift Yönlü)                         ║
╠══════════════════════════════════════════════════════════════════╣
║  • Aynı anda hem gönderip hem alabilirsin                       ║
║  • Her port için bağımsız kanal var                             ║
║  • Çarpışma OLMAZ                                               ║
║  • Bekleme yok, verimlilik çok yüksek                          ║
║                                                                  ║
║  Ortam: Switch                                                   ║
║  CSMA/CD algoritmasına GEREK YOK                                ║
║                                                                  ║
║  PC_A ──→ [SWITCH] ──→ PC_B   (aynı anda)                      ║
║  PC_C ──→ [SWITCH] ──→ PC_D   (aynı anda)                      ║
╚══════════════════════════════════════════════════════════════════╝
```

### 📊 Hub vs. Switch Karşılaştırması

| Özellik | Hub | Switch |
|---|---|---|
| Çalışma modu | Half Duplex | Full Duplex |
| Hız | 10 Mbps (paylaşımlı) | 1 Gbps (port başına bağımsız) |
| Çarpışma | Olabilir | Olmaz |
| Algoritma | CSMA/CD gerekli | CSMA/CD gerekmez |
| Kanal yapısı | Tek paylaşımlı hat | Her port için ayrı kanal |
| 24 port örneği | ~0.4 Mbps/port | 24×1 Gbps = 24 Gbps (TX) + 24 Gbps (RX) = **48 Gbps** anahtarlama |

---

## 5. Ortam Erişim Kontrol Yöntemleri

### 🔀 Erişim Yöntemi Genel Bakış

```
┌─────────────────────────────────────────────────────────────────┐
│             ORTAM ERİŞİM KONTROL YÖNTEMLERİ                    │
├──────────────────────────────┬──────────────────────────────────┤
│   CONTENTION-BASED           │   CONTROLLED ACCESS              │
│   (Rekabete Dayalı)          │   (Kontrollü Erişim)             │
├──────────────────────────────┼──────────────────────────────────┤
│  • Kimsenin önceliği yok     │  • Sıralı erişim                │
│  • Hat boş → gönder          │  • Jeton (token) bazlı          │
│  • Hat dolu → bekle          │  • Token Ring ağlarda kullanıldı│
│                              │                                  │
│  CSMA/CD → Ethernet          │  TOKEN PASSING algoritması      │
│  CSMA/CA → Wi-Fi (802.11)    │  (artık kullanılmıyor)          │
└──────────────────────────────┴──────────────────────────────────┘
```

---

### ⚡ CSMA/CD Algoritması (Carrier Sense Multiple Access / Collision Detection)

**Kullanım yeri:** Eski Ethernet / Hub ortamları (802.3)

```
┌─────────────────────────────────────────────────────────────────┐
│                   CSMA/CD AKIŞ DİYAGRAMI                       │
└─────────────────────────────────────────────────────────────────┘

  Gönderilecek frame var
           │
           ▼
    ┌─────────────┐
    │  Hattı dinle │
    └──────┬──────┘
           │
    ┌──────▼──────┐      DOLU
    │ Hat boş mu? ├─────────────→ Bekle → Tekrar dinle
    └──────┬──────┘
           │ BOŞ
           ▼
    ┌──────────────────┐
    │ Frame'i ilet     │
    │ (iletirken dinle)│
    └──────┬───────────┘
           │
    ┌──────▼───────────┐   HAYIR
    │ Collision var mı? ├────────→ İletim tamamlandı ✓
    └──────┬────────────┘
           │ EVET
           ▼
    ┌──────────────────────────────┐
    │ 1. İletimi DURDUR            │
    │ 2. JAM sinyali gönder (96 bit│
    │    bozucu sinyal)            │
    │ 3. Rastgele süre bekle       │
    │    (Backoff Algoritması)     │
    └──────┬───────────────────────┘
           │
           ▼
    Tekrar göndermeye çalış
    (Yeniden collision → bekleme süresini artır)
```

**Özet:**
- Hat boşsa → gönder
- İletirken çarpışma algılanırsa → dur, jam sinyali gönder, rastgele bekle
- Yeniden gönder

---

### 📡 CSMA/CA Algoritması (Carrier Sense Multiple Access / Collision Avoidance)

**Kullanım yeri:** Wi-Fi / WLAN (IEEE 802.11)

```
┌─────────────────────────────────────────────────────────────────┐
│                   CSMA/CA AKIŞ DİYAGRAMI                       │
└─────────────────────────────────────────────────────────────────┘

  Gönderilecek frame var
           │
           ▼
    ┌──────────────────────┐
    │ Hattı dinle          │
    └──────┬───────────────┘
           │
    ┌──────▼──────┐     DOLU
    │ Hat boş mu? ├──────────→ Bekle
    └──────┬──────┘
           │ BOŞ
           ▼
    ┌──────────────────────────────────┐
    │ Rastgele sayaç başlat            │
    │ (A: 3ms, B: 4ms gibi)            │
    │ Sayaç = 0 olanın gönderme hakkı  │
    └──────┬───────────────────────────┘
           │ Sayaç doldu
           ▼
    ┌──────────────────────────────────┐
    │ Frame'i ilet                     │
    └──────┬───────────────────────────┘
           │
    ┌──────▼──────────────────────────┐
    │ Access Point'ten ACK geldi mi?   │
    └──────┬──────────────┬────────────┘
      EVET │              │ HAYIR
           ▼              ▼
      İletim OK ✓    Collision varsayılır
                     Yeniden sayaç başlat
                     (daha uzun süre)
```

**CSMA/CD ile CSMA/CA Farkı:**

| Özellik | CSMA/CD | CSMA/CA |
|---|---|---|
| Açılım | Collision **Detection** | Collision **Avoidance** |
| Çarpışma | Sonradan tespit eder | Önceden önlemeye çalışır |
| Kullanım | Ethernet / Hub | Wi-Fi (802.11) |
| Onay (ACK) | Yok | Var (Access Point onaylar) |

---

### 🎫 Token Passing (Tarihsel)

Token Ring ağlarda kullanılırdı. Tek bir jeton bütün cihazları sırayla dolaşır:

```
  [A] → jeton boş → [B] → jeton boş → [C]
   ↑                                     │
   └─────────── [D] ← jeton boş ─────────┘

  • Jeton sana geldi + gönderecek veri var → veriyi gönder, 1 tur dolaş
  • Jeton sana geldi + veri yok → jetonü bir sonrakine ilet
  • Günümüzde kullanılmıyor
```

---

## 6. Veri Bağlantısı Çerçevesi

### 🗂️ Çerçeve Yapısı

```
┌────────────────────────────────────────────────────────────────────────┐
│                     GENEL ÇERÇEVE YAPISI                               │
├──────────────┬───────────────┬─────────┬──────────────────┬───────────┤
│  HEADER (Başlık)             │  DATA   │   TRAILER (Kuyruk)           │
├────────┬─────┴──┬─────┬──────┴─────────┴───────────┬──────────────────┤
│ Frame  │Address-│Type │Control│    DATA (Paket)    │Error   │Frame    │
│ Start  │  ing   │     │       │  (Layer 3 paketi)  │Detect. │ Stop    │
└────────┴────────┴─────┴───────┴────────────────────┴────────┴─────────┘
```

### 📋 Çerçeve Alanları

| Alan | Açıklama |
|---|---|
| **Frame Start** | Çerçevenin başladığını belirten özel bit dizisi |
| **Addressing** | Kaynak ve hedef node adresleri (MAC adresleri) |
| **Type** | Kapsüllenmiş Layer 3 protokolünü tanımlar (IPv4/IPv6) |
| **Control** | Akış kontrolü, önceliklendirme (günümüzde az kullanılır) |
| **Data** | Layer 3 paketi (frame yükü) |
| **Error Detection** | CRC — iletim hatalarını belirlemek için |
| **Frame Stop** | Çerçevenin bittiğini belirten özel bit dizisi |

---

### 🔢 Layer 2 Adresleri (MAC Adresleri)

```
┌─────────────────────────────────────────────────────────────────┐
│                  AYNI NETWORKTE İLETİŞİM                        │
│                                                                 │
│  PC1 (192.168.1.110)                  Web Server (172.16.1.99)  │
│  MAC: AA-AA-AA-AA-AA-AA               MAC: AB-CD-EF-12-34-56   │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ L2 Header         │          L3 IP Packet                │   │
│  ├────────────┬───────┤  ┌──────────────┬──────────────┐    │   │
│  │ Dest. NIC  │  Src  │  │  Source IP   │  Dest. IP    │    │   │
│  │11-11-11-11 │AA-AA- │  │192.168.1.110 │172.16.1.99   │    │   │
│  │    (R1)    │AA-AA  │  └──────────────┴──────────────┘    │   │
│  └────────────┴───────┴──────────────────────────────────────┘  │
│                                                                 │
│  ⚠ MAC adresi sadece aynı networkte geçerlidir!               │
│  ⚠ Her router atlamasında MAC adresi değişir                  │
│  ✓ IP adresi kaynak–hedef boyunca değişmez                    │
└─────────────────────────────────────────────────────────────────┘
```

### 🌐 Farklı Protokollerde Çerçeve Yapısı

```
Ethernet (802.3) LAN:
┌──────────────┬──────────────────────────┬─────────┐
│Ethernet Hdr  │       DATA               │  CRC    │
│(Src+Dst MAC) │   (Layer 3 Paketi)       │(Trailer)│
└──────────────┴──────────────────────────┴─────────┘

Wi-Fi (802.11) — En uzun başlık:
┌───────────────────────┬──────────────────────────┬─────────┐
│802.11 Hdr             │       DATA               │Trailer  │
│(Src MAC + Dst MAC +   │   (Layer 3 Paketi)       │         │
│ AP MAC + kontrol bilg.)│                          │         │
└───────────────────────┴──────────────────────────┴─────────┘
⚠ 3 MAC adresi var (Kaynak, Hedef, Access Point)

PPP — En kısa başlık (noktadan noktaya):
┌──────────────┬──────────────────────────┬─────────┐
│PPP Header    │       DATA               │PPP Trail│
│(8 bit: 1111  │   (Layer 3 Paketi)       │         │
│ 11111111)    │                          │         │
└──────────────┴──────────────────────────┴─────────┘
⚠ MAC adresine gerek yok (sadece 2 uç nokta var)
```

---

### 📊 Günümüzde Yaygın 2. Katman Protokolleri

| Protokol | Ortam | Adres | Topoloji |
|---|---|---|---|
| **Ethernet (802.3)** | Kablolu LAN | MAC adresi | Star/Extended Star |
| **Wi-Fi (802.11)** | Kablosuz LAN | MAC adresi | Star (AP merkezli) |
| **PPP** | WAN (ISP bağlantısı) | 8-bit (FF) | Point-to-Point |
| **HDLC** | WAN (Router arası) | Cisco özel | Point-to-Point |
| **Frame Relay** | WAN (eski) | DLCI | Hub and Spoke |

---

## 7. Modül Özeti

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODÜL 6 ÖZET                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  2. KATMAN (Data Link Layer) NE YAPAR?                          │
│                                                                 │
│  ➊ FRAMING                                                      │
│     • 2. Katman başlık + kuyruk bilgisi ekler                   │
│     • Kaynak ve hedef adresleri (genelde MAC)                   │
│     • Hata kontrol bilgisi (CRC/FCS)                            │
│                                                                 │
│  ➋ MEDIA ACCESS CONTROL (MAC)                                   │
│     • Fiziksel ortama geçişi kontrol eder                       │
│     • Kablolu: CSMA/CD (hub ortamı)                             │
│     • Kablosuz: CSMA/CA (Wi-Fi)                                 │
│     • Switch: Algoritma gerekmez (full duplex)                  │
│                                                                 │
│  ➌ KATMAN 2 ADRESLERİ                                           │
│     • MAC adresleri (fiziksel adresler)                         │
│     • Sadece aynı ağ içinde geçerli                             │
│     • Her router atlamasında yenilenir                          │
│                                                                 │
│  ➍ TOPOLOJİLER                                                  │
│     WAN: Point-to-Point, Hub & Spoke, Mesh                      │
│     LAN: Star, Extended Star (güncel), Bus, Ring (eski)         │
│                                                                 │
│  ➎ STANDARTLARl GELİŞTİREN KURULUŞLAR                         │
│     • IEEE (802.3 Ethernet, 802.11 Wi-Fi)                       │
│     • ITU, ISO, ANSI                                            │
│     • IETF (Layer 3 ve üzeri)                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

> **📌 Sonraki Modül:** Ethernet teknolojisi ve MAC adresleri detaylı incelenecek.

---
*CCNA1 – Introduction to Networks v7.0 | Modül 6*
