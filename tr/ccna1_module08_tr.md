# CCNA1 – Modül 8: Ağ Katmanı (3. Katman)
> **Cisco CCNA1 – Introduction to Networks v7.0 (ITN)**  
> Kaynak: Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 İçindekiler
1. [Ağ Katmanına Giriş](#1-ağ-katmanına-giriş)
2. [IP Protokolünün Özellikleri](#2-ip-protokolünün-özellikleri)
3. [IPv4 Paket Başlığı](#3-ipv4-paket-başlığı)
4. [IPv6 Paket Başlığı](#4-ipv6-paket-başlığı)
5. [Host Yönlendirme Kararları](#5-host-yönlendirme-kararları)
6. [Router Routing Tablosu](#6-router-routing-tablosu)
7. [Statik ve Dinamik Yönlendirme](#7-statik-ve-dinamik-yönlendirme)
8. [Özet ve Komutlar](#8-özet-ve-komutlar)

---

## 1. Ağ Katmanına Giriş

### 3. Katman Nedir?
Ağ katmanı (**Network Layer / L3**), farklı ağlardaki (networklerdeki) cihazlar arasında uçtan uca paket iletimini sağlar.

> 💡 **2. Katman vs 3. Katman:**
> - **Switch (2. Katman)** → Aynı network içinde MAC adresleri ile iletişim
> - **Router (3. Katman)** → Farklı networkler arasında IP adresleri ile yönlendirme

### Ağ Katmanının 4 Temel İşlemi

```
┌─────────────────────────────────────────────────────────────┐
│                    AĞ KATMANI İŞLEMLERİ                     │
├──────────────┬──────────────┬──────────────┬────────────────┤
│  Adresleme   │  Kapsülleme  │ Yönlendirme  │ Kapsül Açma   │
│ (Addressing) │(Encapsulation│  (Routing)   │(De-encapsulat)│
│              │    )         │              │               │
│ Kaynak ve    │ IP başlığı   │ En iyi yolu  │ Başlığı çıkar │
│ hedef IP     │ eklenir →    │ seç, paketi  │ üst katmana   │
│ adresleme    │ PAKET oluşur │ ilet         │ ver           │
└──────────────┴──────────────┴──────────────┴────────────────┘
```

### OSI Modelinde Kapsülleme

```
Uygulama (L7-L5)   │  DATA
                   ↓
Transport (L4)     │  [TCP Başlık | DATA]          → SEGMENT
                   ↓
Network (L3)       │  [IP Başlık | TCP | DATA]     → PAKET
                   ↓
Data Link (L2)     │  [Eth.H | IP | TCP | DATA | FCS] → FRAME
                   ↓
Physical (L1)      │  Bitler (0 ve 1'ler)
```

### Bir PC'nin Haberleşmesi İçin Gereken 3 Adres

| Adres | Açıklama | Kullanım Amacı |
|-------|----------|----------------|
| **IP Adresi** | Cihazın mantıksal adresi | Kaynak/hedef tanımı |
| **Subnet Maskesi** | Hangi networkte olduğunu belirtir | Network kısmını ayırır |
| **Default Gateway** | Router'ın IP adresi | Farklı networklere çıkış |

> ⚠️ **Default Gateway yoksa:** Aynı networkte haberleşebilirsin ama farklı networklere gidemezsin!

---

## 2. IP Protokolünün Özellikleri

### 2.1 Bağlantısız (Connectionless)

```
    Gönderen                                    Alıcı
       │                                          │
       │  Paket gönder (önceden haber vermeden)   │
       │─────────────────────────────────────────►│
       │                                          │
       │  ← Hiçbir onay beklenmez                 │
       │  ← Hedef açık mı? Bilinmez               │
       │  ← Paket ulaştı mı? Bilinmez             │
```

IP, paketi göndermeden önce **hedefe bağlantı kurmaz**. Mektup atmak gibi: zarfı postaya bırakırsın, ulaşıp ulaşmadığını bilmezsin.

### 2.2 En İyi Çaba (Best Effort)

IP bir **"Best Effort Delivery Protocol"**'dür:
- ✗ Paket teslimini **garanti etmez**
- ✗ Kayıp paketleri **yeniden göndermez**
- ✗ Bozuk paketleri **düzeltemez**
- ✗ Sıralama **yapmaz**
- ✓ Bunlar için **TCP (4. Katman)** kullanılır

### 2.3 Medyadan Bağımsız (Media Independent)

```
                        ┌──────────┐
Bakır Kablo ────────────►          │
Fiber Optik ────────────►  IP PAKETİ  ─────► Hedef
WiFi (Kablosuz) ────────►          │
4G / 5G ────────────────►          │
                        └──────────┘
```

IP, taşıma ortamından **tamamen bağımsız** çalışır. Aynı IP paketi bakır, fiber veya kablosuz ortamdan taşınabilir.

### 2.4 MTU (Maximum Transmission Unit) ve Fragmentation

```
Ethernet MTU = 1500 byte

┌─────────────────────────────────────────┐
│         1500 byte'lık IP paketi          │
└─────────────────────────────────────────┘
           │
           ▼ (Eski WAN, MTU=500 byte)
  ┌───────────┐ ┌───────────┐ ┌───────────┐
  │ Parça 1   │ │ Parça 2   │ │ Parça 3   │
  │ 500 byte  │ │ 500 byte  │ │ 500 byte  │
  └───────────┘ └───────────┘ └───────────┘
           │
           ▼ (Karşı tarafta birleştirme)
┌─────────────────────────────────────────┐
│         Orijinal 1500 byte paket        │
└─────────────────────────────────────────┘
```

> ⚠️ **Fragmentation istenilen bir özellik değildir!**
> - Gecikmeye neden olur
> - Firewall'lar parçalanmış paketleri atabilir
> - IPv6'da fragmentation **yoktur**

---

## 3. IPv4 Paket Başlığı

### 3.1 IPv4 Başlık Yapısı (20 Byte)

```
 Byte 1          Byte 2          Byte 3          Byte 4
┌───────────────┬───────────────┬───────────────────────────────┐
│    Version    │ Header Length │  DS (DSCP/ECN)  │ Total Length│
│      (4)      │   (20 byte)   │  [QoS Alanı]    │             │
├───────────────────────────────┬───────────┬────────────────────┤
│         Identification        │   Flags   │  Fragment Offset   │
│                               │           │                    │
├───────────────┬───────────────┴────────────────────────────────┤
│  Time-to-Live │    Protocol   │       Header Checksum          │
│    (TTL)      │  (6=TCP,17=UDP│                                │
├───────────────────────────────────────────────────────────────┤
│                    Source IP Address (32 bit)                  │
├───────────────────────────────────────────────────────────────┤
│                  Destination IP Address (32 bit)              │
└───────────────────────────────────────────────────────────────┘
                                               Toplam: 20 Byte
```

### 3.2 Başlık Alanlarının Açıklaması

| Alan | Değer/Boyut | Açıklama |
|------|------------|----------|
| **Version** | 4 | IPv4 olduğunu belirtir |
| **Header Length** | 20 byte | Başlık uzunluğu |
| **DS (DSCP)** | Değişken | QoS – paket önceliği (ses=5, video=4) |
| **Total Length** | 46–1500 byte | Başlık + Data toplam boyutu |
| **Identification** | – | Fragmentation için parça numarası |
| **Flags/Fragment Offset** | – | Parçalanma bilgisi (günümüzde kullanılmıyor) |
| **TTL** | 64/128/255 | Yaşam süresi – her hop'ta 1 azalır |
| **Protocol** | 6=TCP, 17=UDP | Üst katman protokolü |
| **Header Checksum** | – | Başlık hata kontrolü |
| **Source IP** | 32 bit | Kaynak IP adresi |
| **Destination IP** | 32 bit | **En önemli alan** – hedef IP adresi |

### 3.3 TTL (Time-to-Live) – Yaşam Süresi

```
İşletim Sistemi       TTL Başlangıç Değeri
─────────────────────────────────────────
Linux / macOS              64
Windows XP/Vista/7/10     128
Cisco Router IOS          255
```

**TTL Nasıl Çalışır?**

```
A Cihazı                   İnternet                    Sunucu
(TTL=64)  →  Router1(63) →  Router2(62) → ... → Router10(54) → Sunucu
```

**TTL'nin Amacı:** Routing döngüsüne giren paketlerin internette sonsuza dek dolaşmasını önlemek.

```
         Routing Loop (Döngü) Örneği:
         
Router1 → Router2 → Router3 → Router1 → Router2 → ...
                                ↑
                          TTL=0 olunca paket çöpe atılır!
```

**Ping ile TTL Analizi:**
- Google'dan gelen cevap TTL=116 ise → 128 - 116 = **12 hop** geçmiş
- Hürriyet'ten gelen cevap TTL=54 ise → 64 - 54 = **10 hop** geçmiş

### 3.4 ARP Protokolü (IP'den MAC Öğrenme)

```
Aynı Networkte Paket Gönderme:

A (192.168.1.5)  →  Hedef: 192.168.1.12 (B)

Adım 1: "B'nin MAC adresi nedir?" → ARP Broadcast (FF:FF:FF:FF:FF:FF)
         Network'teki HERKESE gider

Adım 2: B cevap verir → "Benim MAC adresim: AA:BB:CC:DD:EE:FF"

Adım 3: A, ARP tablosuna yazar: 192.168.1.12 → AA:BB:CC:DD:EE:FF

Adım 4: Paketi gönderir:
         [Src MAC: A | Dst MAC: B | Src IP: A | Dst IP: B]
```

**Farklı Network'e Paket Gönderme (Default Gateway):**

```
A (192.168.1.5) → Hedef: 8.8.8.8 (Google – farklı network)

"8.8.8.8 benim network'ümde değil!" → Router'a gönder!

Adım 1: ARP ile Router'ın MAC adresini öğren
         "Hey 192.168.1.1 (Default GW), MAC adresin nedir?"

Adım 2: Router cevap verir → MAC: RR:RR:RR:RR:RR:RR

Adım 3: Paketi gönder:
         [Src MAC: A | Dst MAC: ROUTER | Src IP: A | Dst IP: 8.8.8.8]
         └─── 2. Katman ──────────────┘ └─── 3. Katman ──────────────┘
```

> ⚠️ **Kritik Kural:** Kaynak IP ve Hedef IP yol boyunca **değişmez**. Sadece MAC adresleri her hop'ta değişir!

### 3.5 NAT (Network Address Translation)

```
Ev Ağı (Private IP'ler)           İnternet (Public IP)

PC1: 192.168.1.5  ─┐
PC2: 192.168.1.6  ─┤                        Google
TV:  192.168.1.7  ─┤→ Router → NAT → 85.x.x.x ────────► 8.8.8.8
Tablet:192.168.1.8─┤        (Tek Public IP)
Telefon:192.168.1.9┘

NAT Tablosu:
┌──────────────────┬─────────────┬──────────────┐
│  İç IP:Port      │  Dış IP:Port│  Hedef       │
├──────────────────┼─────────────┼──────────────┤
│ 192.168.1.5:1234 │ 85.x.x.x:5000 │ 8.8.8.8:80  │
│ 192.168.1.6:5678 │ 85.x.x.x:5001 │ 8.8.8.8:443 │
└──────────────────┴─────────────┴──────────────┘
```

**Private IP Aralıkları:**

| Sınıf | Aralık |
|-------|--------|
| A | 10.0.0.0 – 10.255.255.255 |
| B | 172.16.0.0 – 172.31.255.255 |
| C | 192.168.0.0 – 192.168.255.255 |

---

## 4. IPv6 Paket Başlığı

### 4.1 Neden IPv6?

| Sorun (IPv4) | Çözüm (IPv6) |
|-------------|-------------|
| 4.3 milyar adres tükendi | 340 undecilyon adres (2¹²⁸) |
| NAT zorunlu → uçtan uca bağlantı yok | Her cihaza gerçek IP → NAT gerekmez |
| Karmaşık ağ yönetimi | Basitleştirilmiş başlık yapısı |
| Fragmentation var | Fragmentation yok |

### 4.2 IPv6 Başlık Yapısı (40 Byte – Sabit)

```
 Byte 1          Byte 2          Byte 3          Byte 4
┌───────────┬───────────────────┬────────────────────────────────┐
│  Version  │   Traffic Class   │          Flow Label            │
│    (6)    │   [QoS Alanı]     │    [Akış Etiketi - 20 bit]     │
├───────────────────────────────┬───────────────┬────────────────┤
│         Payload Length        │  Next Header  │   Hop Limit    │
│       [Veri boyutu]           │ (6=TCP,17=UDP)│    [= TTL]     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│              Source IPv6 Address (128 bit = 16 byte)          │
│                                                               │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│            Destination IPv6 Address (128 bit = 16 byte)       │
│                                                               │
└───────────────────────────────────────────────────────────────┘
                                               Toplam: 40 Byte
```

### 4.3 IPv4 vs IPv6 Karşılaştırması

| Özellik | IPv4 | IPv6 |
|---------|------|------|
| Adres Boyutu | 32 bit | 128 bit |
| Toplam Adres | ~4.3 milyar | 340 undecilyon |
| Başlık Boyutu | 20 byte (değişken) | 40 byte (sabit) |
| Header Checksum | ✓ Var | ✗ Yok |
| Fragmentation | ✓ Var | ✗ Yok |
| NAT | Gerekli | Gerekmez |
| Başlık Alan Sayısı | 12 alan | 8 alan |
| TTL Adı | TTL | Hop Limit |

### 4.4 IPv6 Extension Headers (Uzantı Başlıkları)

```
Temel IPv6 Paketi:
┌──────────────┬─────────────────┐
│ IPv6 Başlık  │   TCP Segment   │
│ Next=6(TCP)  │                 │
└──────────────┴─────────────────┘

Routing Header ile:
┌──────────────┬─────────────────┬─────────────────┐
│ IPv6 Başlık  │ Routing Header  │   TCP Segment   │
│ Next=43      │ Next=6(TCP)     │                 │
└──────────────┴─────────────────┴─────────────────┘

Authentication Header ile:
┌──────────────┬─────────────────┬──────────────────┬──────────┐
│ IPv6 Başlık  │ Routing Header  │ Auth. Header     │  TCP Seg │
│ Next=43      │ Next=51(AH)     │ Next=6(TCP)      │          │
└──────────────┴─────────────────┴──────────────────┴──────────┘
```

### 4.5 IPv6 Adres Yapısı

```
2001:0DB8:20B:A11:1:2233:4444:5555/64

└──────────────────┘└──────────────────┘
   Network Kısmı        Host Kısmı
   (ilk 64 bit)         (son 64 bit)
   Prefix = /64
```

---

## 5. Host Yönlendirme Kararları

### 5.1 Bir Host Nereye Paket Gönderebilir?

```
                        ┌─────────────────────────────────┐
                        │     PAKET GÖNDERİM KARARI        │
                        └───────────────┬─────────────────┘
                                        │
           ┌────────────────────────────┼────────────────────────────┐
           │                            │                            │
           ▼                            ▼                            ▼
    ┌─────────────┐            ┌─────────────────┐          ┌───────────────┐
    │   KENDİSİ   │            │  YEREL HOST     │          │  UZAK HOST    │
    │  Loopback   │            │  (Aynı Network) │          │(Farklı Network│
    │  127.0.0.1  │            │                 │          │               │
    │  (IPv6: ::1)│            │  Direkt MAC ile │          │ Default GW'ye │
    └─────────────┘            └─────────────────┘          └───────────────┘
```

### 5.2 Loopback Adresi

- **127.0.0.0 – 127.255.255.255** → Tümü loopback
- En yaygın: **127.0.0.1** (localhost)
- TCP/IP protokolü yüklü ve NIC takılıysa çalışır
- İnternet bağlantısı, kablo bağlı olmasa bile ping atılabilir!
- Web sunucu testi için: `http://127.0.0.1` veya `http://localhost`

### 5.3 Aynı Network'te Haberleşme

```
PC1: 192.168.10.10 / 255.255.255.0 / GW: 192.168.10.1
PC2: 192.168.10.15 / 255.255.255.0 / GW: 192.168.10.1

PC1 → PC2 haberleşmesi:
  "192.168.10.15 benim network'ümde mi?" 
  → Subnet maskesine bak: 255.255.255.0
  → Network: 192.168.10.0
  → PC2 de 192.168.10.x → EVET aynı network!
  → ARP ile MAC öğren → Direkt gönder (router gerekmez)
```

### 5.4 Default Gateway Zorunluluğu

```
Default GW VARSA:          Default GW YOKSA:
─────────────────          ──────────────────
✓ Aynı networkte haberleş  ✓ Aynı networkte haberleş
✓ Farklı networklere git   ✗ Farklı networklere GİDEMEZSİN!
✓ İnternete çık
```

### 5.5 PC'nin Routing Tablosu

```
C:\Users\PC1> netstat -r   (veya: route print)

IPv4 Route Table
=================================================================
Active Routes:
Network Destination    Netmask     Gateway        Interface   Metric
        0.0.0.0        0.0.0.0   192.168.10.1  192.168.10.10    25
      127.0.0.0      255.0.0.0       On-link      127.0.0.1   306
      127.0.0.1  255.255.255.255     On-link      127.0.0.1   306
   192.168.10.0  255.255.255.0       On-link   192.168.10.10   281
  192.168.10.10  255.255.255.255     On-link   192.168.10.10   281
```

| Satır | Anlamı |
|-------|--------|
| `0.0.0.0 / 0.0.0.0` | Default rota – tüm bilinmeyen ağlar → GW'ye gönder |
| `127.0.0.0` | Loopback – kendi kendine |
| `192.168.10.0` | Yerel ağ – direkt gönder |

### 5.6 IP Bilgisi Alma Yöntemleri

| Yöntem | IPv4 | IPv6 |
|--------|------|------|
| Manuel (Statik) | Elle IP, SM, GW, DNS yaz | Elle IPv6 adresi yaz |
| Otomatik | **DHCP** sunucusundan al | **SLAAC** (Router'dan) |
| Karma | – | SLAAC + DHCPv6 |

---

## 6. Router Routing Tablosu

### 6.1 Routing Tablosu Nasıl Oluşur?

```
         ROUTING TABLOSU OLUŞUMU
         
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  1. DOĞRUDAN BAĞLI (Connected) - Otomatik              │
│     Interface'e IP verildiği an → tablo güncellenir     │
│                                                         │
│  2. UZAK AĞLAR (Remote Networks)                        │
│     a) Statik Rota   → Yönetici elle yazar             │
│     b) Dinamik Rota  → OSPF, EIGRP, RIP ile öğrenir   │
│                                                         │
│  3. DEFAULT ROTA (0.0.0.0/0)                           │
│     → Tabloda eşleşme bulunamazsa buraya gönder         │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 6.2 Routing Tablosu Örneği

```
Topoloji:
                    10.0.0.1        20.0.0.1  20.0.0.2        30.0.0.1
[Network1:10.0.0.0]──[R1:Fa0]──[R1:Fa1]──[R2:Fa0]──[Network3:30.0.0.0]
                          Network2: 20.0.0.0

R1 Routing Tablosu:
┌───┬────────────────┬───────────────┬──────────────────────┐
│ C │ 10.0.0.0/8     │ 255.0.0.0     │ FastEth 0            │
│ L │ 10.0.0.1/32    │ 255.255.255.255│ FastEth 0           │
│ C │ 20.0.0.0/8     │ 255.0.0.0     │ FastEth 1            │
│ L │ 20.0.0.1/32    │ 255.255.255.255│ FastEth 1           │
│ S │ 30.0.0.0/8     │ 255.0.0.0     │ 20.0.0.2             │
│S* │ 0.0.0.0/0      │ 0.0.0.0       │ 40.0.0.2 (ISP)       │
└───┴────────────────┴───────────────┴──────────────────────┘
  C = Connected (Doğrudan bağlı)
  L = Local (Router'ın kendi arayüz IP'si)
  S = Static (Statik rota)
  S* = Static Default Route
```

### 6.3 Router Paket İşleme Adımları

```
        ROUTER'A PAKET GELDİĞİNDE NE OLUR?

Paket Gelir
    │
    ▼
2. Katman başlığını sök
(MAC adresi benim mi? Evet → devam)
    │
    ▼
3. Katman IP başlığına bak
Destination IP = 30.0.0.5
    │
    ▼
Routing tablosuna bak:
"30.0.0.0 network'üne nasıl gidebilirim?"
    │
    ├── Eşleşme bulunamadı → Default rotaya gönder
    │
    └── 30.0.0.0/8 → 20.0.0.2'ye gönder ✓
              │
              ▼
    Yeni 2. katman başlığı ekle
    (Src MAC: R1_Fa1, Dst MAC: R2_Fa0)
              │
              ▼
    Paketi Fa1 interface'inden gönder
```

### 6.4 Show ip route Komutu

```
R1# show ip route
Codes: L - local, C - connected, S - static, R - RIP,
       O - OSPF, D - EIGRP, * - candidate default

Gateway of last resort is 209.165.200.226 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 209.165.200.226, GigabitEth0/0/1
O       10.1.1.0/24 [110/2] via 209.165.200.226, GigabitEth0/0/1
C       192.168.10.0/24 is directly connected, GigabitEth0/0/0
L       192.168.10.1/32 is directly connected, GigabitEth0/0/0
C       209.165.200.224/30 is directly connected, GigabitEth0/0/1
L       209.165.200.225/32 is directly connected, GigabitEth0/0/1

Kod Açıklamaları:
  C  = Connected (Doğrudan bağlı ağ)
  L  = Local (Arayüzün kendi IP'si)
  S  = Static (Elle yazılmış rota)
  S* = Default static route
  O  = OSPF protokolüyle öğrenilen rota
  D  = EIGRP protokolüyle öğrenilen rota
  R  = RIP protokolüyle öğrenilen rota
```

---

## 7. Statik ve Dinamik Yönlendirme

### 7.1 Statik Yönlendirme

```
Komut Yapısı:
R1(config)# ip route [hedef-network] [subnet-mask] [next-hop-IP]

Örnek:
R1(config)# ip route 10.1.1.0 255.255.255.0 209.165.200.226
             └── Hedef ──────┘ └─ Mask ─────┘ └─ Sonraki Router ─┘
```

**Statik Yönlendirme Özellikleri:**

| Özellik | Durum |
|---------|-------|
| Manuel yapılandırma | Gerekli |
| Topoloji değişince | Yönetici elle günceller |
| Küçük ağlar için | Uygun |
| Büyük/karmaşık ağlar için | Zor |
| Yedekleme | Otomatik değil |

**Senaryo – Statik Rotanın Sorunu:**

```
Normal Durum:           Kablo Kopunca:
PC1 → R1 → R2 → PC2   PC1 → R1 → R2 ✗ (kopuk!)
                               ↓
                        Yönetici uyanıp gelir
                        Yeni statik rota yazar:
                        R1(config)# ip route ... via R3
                               ↓
                        PC1 → R1 → R3 → PC2 ✓
```

### 7.2 Dinamik Yönlendirme

```
Dinamik Yönlendirme Protokolleri:
┌──────────────────────────────────────────────────────────┐
│  OSPF (Open Shortest Path First)  ← En yaygın, standart │
│  EIGRP (Enhanced IGRP)            ← Cisco'ya özel        │
│  RIP (Routing Information Protocol) ← Eski, hantal      │
└──────────────────────────────────────────────────────────┘

Router'lar birbirlerine şunu söyler:
"30.0.0.0 network'ü bende, benim IP'm 20.0.0.2"
      ↓
Komşu router tabloya ekler: 30.0.0.0 → 20.0.0.2 via OSPF
```

**Dinamik Yönlendirme Avantajları:**

| Özellik | Açıklama |
|---------|----------|
| Otomatik keşif | Uzak ağları otomatik öğrenir |
| Güncel tablo | Değişikliklere anında uyum sağlar |
| En iyi yol | Bant genişliği, gecikme, hop sayısına göre |
| Failover | Bağlantı kopunca alternatif yola geçer |

**Senaryo – Dinamik Rotanın Avantajı:**

```
Gece uyurken:

PC1 → R1 ──(10Gbit)──► R2 → PC2   (Ana yol, aktif)
          └─(1Gbit)──► R3 ┘        (Yedek yol)

Sabah 3'te R2 bağlantısı kopar:
Router'lar anons göndermeyi kesince tablo güncellenir
→ Milisaniyeler içinde yol değişir:

PC1 → R1 ──(1Gbit)──► R3 → PC2   (Otomatik failover!)
```

### 7.3 Default Rota

```
Kullanım Amacı: Routing tablosunda hiç eşleşme olmayan
                tüm paketleri göndermek için son çıkış noktası

ip route 0.0.0.0 0.0.0.0 [ISP-Router-IP]
               │
               └── "0.0.0.0/0" = Tüm bilinmeyen ağlar

Evdeki modem de default rota kullanır:
  Bilinmeyen tüm IP'ler → ADSL/FIBER → ISP
```

### 7.4 Yönlendirme Karar Akışı

```
                    Paket Geldi
                         │
                         ▼
              Routing tablosuna bak
                         │
              ┌──────────┴──────────┐
              │                     │
        Eşleşme VAR           Eşleşme YOK
              │                     │
              ▼                     ▼
        En spesifik          Default rota
        rotayı seç           var mı?
              │               /      \
              │             EVET     HAYIR
              │              │         │
              ▼              ▼         ▼
         Paketi ilet    Paketi       Paketi çöpe at
                        Default'a   (ICMP: Unreachable)
                        gönder
```

---

## 8. Özet ve Komutlar

### 8.1 Temel Kavramlar Özeti

```
┌─────────────────────────────────────────────────────────────┐
│                    MODÜL 8 ÖZETİ                            │
├──────────────────────┬──────────────────────────────────────┤
│ Katman               │ 3. Katman (Network Layer)            │
│ Protokoller          │ IPv4, IPv6                           │
│ Cihaz                │ Router                              │
│ Tablo                │ Routing Tablosu                     │
│ Adres Türleri        │ IP adresi (32bit IPv4 / 128bit IPv6) │
├──────────────────────┼──────────────────────────────────────┤
│ IP Özellikleri       │ Connectionless, Best Effort,         │
│                      │ Media Independent                    │
├──────────────────────┼──────────────────────────────────────┤
│ IPv4 Başlık          │ 20 byte, değişken                    │
│ IPv6 Başlık          │ 40 byte, sabit                       │
├──────────────────────┼──────────────────────────────────────┤
│ Routing Türleri      │ Connected, Static, Dynamic, Default  │
├──────────────────────┼──────────────────────────────────────┤
│ Dinamik Protokoller  │ OSPF, EIGRP, RIP                    │
└──────────────────────┴──────────────────────────────────────┘
```

### 8.2 Önemli Komutlar

| Komut | Platform | Açıklama |
|-------|---------|----------|
| `show ip route` | Cisco Router | Routing tablosunu göster |
| `show mac address-table` | Cisco Switch | MAC adres tablosunu göster |
| `route print` | Windows | PC routing tablosunu göster |
| `netstat -r` | Windows/Linux | PC routing tablosunu göster |
| `ip route [ağ] [mask] [nexthop]` | Cisco Router | Statik rota ekle |
| `ping 127.0.0.1` | Herhangi | Loopback testi |
| `ping [IP]` | Herhangi | Bağlantı testi |
| `tracert [IP]` | Windows | Hop'ları göster (TTL analizi) |

### 8.3 Routing Tablosu Kodu Referansı

| Kod | Anlamı |
|-----|--------|
| `C` | Connected – Doğrudan bağlı ağ |
| `L` | Local – Arayüzün kendi IP'si |
| `S` | Static – Elle yazılmış rota |
| `S*` | Static Default Route |
| `O` | OSPF dinamik protokolü |
| `D` | EIGRP dinamik protokolü |
| `R` | RIP dinamik protokolü |

### 8.4 Hızlı Karşılaştırma: Switch vs Router

| Özellik | Switch | Router |
|---------|--------|--------|
| Çalıştığı katman | 2. Katman | 3. Katman |
| Tuttuğu tablo | MAC Adres Tablosu | Routing Tablosu |
| Adresleme | MAC Adresi | IP Adresi |
| Görevi | Aynı networkte iletişim | Farklı networkler arası iletişim |
| Protokol | Ethernet | IP, IPv6 |

---

> 📘 **Sonraki Modül:** Modül 9 – Adres Çözümleme (Subnetting & IPv4 Addressing)

---

*CCNA1 Modül 8 | Ağ Yöneticileri | gokhan@agyoneticileri.org | ozan@agyoneticileri.org*
