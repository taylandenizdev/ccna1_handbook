# 📘 CCNA1 – Modül 9: Adres Çözümleme

> **Kurs:** Introduction to Networks v7.0 (ITN)  
> **Modül Amacı:** ARP ve IPv6 Komşu Saptama protokollerinin ağ iletişimini nasıl sağladığını açıklamak.

---

## 📑 İçindekiler

1. [MAC ve IP Adresleri](#1-mac-ve-ip-adresleri)
2. [Adres Çözümleme Protokolü (ARP)](#2-adres-çözümleme-protokolü-arp)
3. [ARP Sorunları ve Güvenlik](#3-arp-sorunları-ve-güvenlik)
4. [IPv6 Komşu Saptama (Neighbor Discovery)](#4-ipv6-komşu-saptama-neighbor-discovery)
5. [Komut Özeti](#5-komut-özeti)
6. [Modül Özeti](#6-modül-özeti)

---

## 1. MAC ve IP Adresleri

### 1.1 İki Tür Adres

Bir Ethernet ağındaki her cihaza **iki farklı adres** atanır:

| Özellik | MAC Adresi | IP Adresi |
|---|---|---|
| Katman | Katman 2 (Data Link) | Katman 3 (Network) |
| Tür | Fiziksel (Physical) | Mantıksal (Logical) |
| Kalıcılık | Karta sabit gelir, değişmez | Ağa göre değişir |
| Boyut | 48 bit (6 byte) | 32 bit (IPv4) / 128 bit (IPv6) |
| Format | `1C-3B-F3-19-E7-F9` | `192.168.1.5` |
| Görme komutu | `ipconfig /all` → Physical Address | `ipconfig` → IPv4 Address |

### 1.2 MAC Adresi Yapısı

```
|<------ 48 bit ------>|
+----------+-----------+
| OUI      | Seri No   |
| (24 bit) | (24 bit)  |
+----------+-----------+
  Üretici     Cihaz
  kodu        numarası
```

- **İlk 24 bit (OUI):** Üretici firmanın kodu (Apple, Intel, Cisco vb.)
- **Son 24 bit:** Üreticinin o karta verdiği seri numarası

### 1.3 Aynı Ağda İletişim

Kaynak ve hedef **aynı ağdaysa** (örn. ikisi de `/24`'te):

```
PC1 (192.168.10.10)  ──────────────────►  PC2 (192.168.10.11)
     MAC: aa-aa-aa                              MAC: 55-55-55

Çerçeve başlığı:
┌────────────────┬────────────────┬──────────────────┬──────────────────┐
│  Dest MAC      │  Source MAC    │  Source IPv4     │  Dest IPv4       │
│  55-55-55      │  aa-aa-aa      │  192.168.10.10   │  192.168.10.11   │
└────────────────┴────────────────┴──────────────────┴──────────────────┘
```

> **Kural:** Aynı ağda → Hedef MAC = **Hedef cihazın MAC adresi**

### 1.4 Farklı Ağda İletişim

Hedef **farklı bir ağdaysa** paket önce router'a gönderilir:

```
PC1 (192.168.10.10)  ──►  Router R1  ──►  ...  ──►  PC2 (10.1.1.10)
     MAC: aa-aa-aa          MAC: bb-bb-bb                 MAC: 55-55-55

PC1 → R1 arası çerçeve:
┌────────────────┬────────────────┬──────────────────┬──────────────────┐
│  Dest MAC      │  Source MAC    │  Source IPv4     │  Dest IPv4       │
│  bb-bb-bb      │  aa-aa-aa      │  192.168.10.10   │  10.1.1.10       │
└────────────────┴────────────────┴──────────────────┴──────────────────┘
          ▲
          └─── Default Gateway (Router) MAC adresi!
```

> **Kural:** Farklı ağda → Hedef MAC = **Default Gateway'in (Router'ın) MAC adresi**

---

## 2. Adres Çözümleme Protokolü (ARP)

### 2.1 ARP Nedir?

**ARP (Address Resolution Protocol):**
- Aynı ağda, **IP adresinden MAC adresini** öğrenmek için kullanılan protokoldür.
- Katman 2 protokolüdür (IP başlığı içermez).
- IPv4'e özgüdür; IPv6 farklı bir yöntem kullanır.

```
           ARP
  IP Adresi ──────────► MAC Adresi
  (biliniyor)           (öğreniliyor)
```

### 2.2 ARP Tablosu

Her cihaz öğrendiği IP–MAC eşleşmelerini bir tabloda tutar:

```
C:\>arp -a
  Internet Address    Physical Address    Type
  192.168.1.1         0001.c7ee.eb01      dynamic
  192.168.1.20        000c.852e.33aa      dynamic
  192.168.1.30        0060.2fbe.8bce      dynamic
```

- **dynamic:** ARP protokolüyle otomatik öğrenilmiş
- **static:** Manuel olarak eklenmiş
- Girişler belirli süre kullanılmadığında otomatik silinir (Linux: ~2 dk, Windows: ~4–5 dk)

### 2.3 ARP İşleyişi – Adım Adım

#### ARP İsteği (Request) – Broadcast

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ARP Request (Broadcast)                          │
│                                                                     │
│   PC1 (1.10)          Tüm Ağ             PC2 (1.20)               │
│       │                                      │                      │
│       │──── "Hey TÜM NETWORK! ──────────────►│                      │
│       │      192.168.1.20 IP'li cihaz,       │                      │
│       │      bana MAC adresini söyle!"        │                      │
│       │                                      │                      │
└─────────────────────────────────────────────────────────────────────┘

Frame Header:
  Dest MAC  : FF:FF:FF:FF:FF:FF  ← Broadcast (herkese gider)
  Source MAC: aa:bb:cc:dd:ee:ff  ← PC1'in MAC adresi

ARP Data (Opcode 1 = Request):
  Sender MAC: aa:bb:cc:dd:ee:ff
  Sender IP : 192.168.1.10
  Target MAC: 00:00:00:00:00:00  ← Bilinmiyor (boş)
  Target IP : 192.168.1.20
```

#### ARP Yanıtı (Reply) – Unicast

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ARP Reply (Unicast)                              │
│                                                                     │
│   PC1 (1.10)                            PC2 (1.20)                 │
│       │                                      │                      │
│       │◄─── "Dostum, ben 1.20'yim.  ─────────│                      │
│       │      Benim MAC adresim MAC2!"         │                      │
│       │                                      │                      │
└─────────────────────────────────────────────────────────────────────┘

Frame Header:
  Dest MAC  : aa:bb:cc:dd:ee:ff  ← Sadece PC1'e (Unicast)
  Source MAC: 11:22:33:44:55:66  ← PC2'nin MAC adresi

ARP Data (Opcode 2 = Reply):
  Sender MAC: 11:22:33:44:55:66
  Sender IP : 192.168.1.20
  Target MAC: aa:bb:cc:dd:ee:ff
  Target IP : 192.168.1.10
```

### 2.4 ARP Akış Diyagramı – Aynı Ağda İletişim

```
                    PC1, PC2'ye ping atmak istiyor
                              │
                              ▼
              ┌───────────────────────────────┐
              │  ARP tablosuna bak:           │
              │  192.168.1.20 var mı?         │
              └───────────────────────────────┘
                       │           │
                      Evet        Hayır
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ Broadcast ARP Request   │
                       │  │ (FF:FF:FF:FF:FF:FF)     │
                       │  │ → Tüm ağa gönder        │
                       │  └─────────────────────────┘
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ PC2 Unicast ARP Reply   │
                       │  │ gönderir → PC1'e        │
                       │  └─────────────────────────┘
                       │           │
                       │           ▼
                       │  ┌─────────────────────────┐
                       │  │ ARP tablosuna yaz:      │
                       │  │ 192.168.1.20 → MAC2     │
                       │  └─────────────────────────┘
                       │           │
                       └─────┬─────┘
                             │
                             ▼
              ┌───────────────────────────────┐
              │  Paketi hedef MAC ile gönder  │
              │  Dest MAC: MAC2               │
              └───────────────────────────────┘
```

### 2.5 ARP Akış Diyagramı – Farklı Ağda İletişim

```
              PC1, farklı ağdaki sunucuya paket göndermek istiyor
                              │
                              ▼
              ┌───────────────────────────────┐
              │  Subnet Mask kontrolü:        │
              │  Hedef aynı ağda mı?          │
              └───────────────────────────────┘
                       │           │
                      Evet        Hayır → Farklı ağ!
                       │           │
                       │           ▼
                       │  ┌─────────────────────────────┐
                       │  │ Default Gateway MAC'ini     │
                       │  │ ARP tablosunda ara          │
                       │  └─────────────────────────────┘
                       │           │           │
                       │          Var         Yok
                       │           │           │
                       │           │           ▼
                       │           │  ┌──────────────────────┐
                       │           │  │ Router'a ARP Request │
                       │           │  │ gönder (Broadcast)   │
                       │           │  └──────────────────────┘
                       │           │           │
                       │           │           ▼
                       │           │  ┌──────────────────────┐
                       │           │  │ Router ARP Reply     │
                       │           │  │ → Tabloya yaz        │
                       │           │  └──────────────────────┘
                       │           │           │
                       │           └─────┬─────┘
                       └───────────┬─────┘
                                   │
                                   ▼
              ┌────────────────────────────────────────┐
              │  Dest MAC = Router (Gateway) MAC       │
              │  Dest IP  = Sunucunun IP'si            │
              │  Paketi gönder!                        │
              └────────────────────────────────────────┘
```

### 2.6 Router'larda ARP

Router'lar da ARP kullanır:

- Kendisine bağlı **tüm networklerdeki** cihazların MAC adreslerini öğrenir.
- Her cihaza paket iletmek için o cihazın MAC adresini bilmesi gerekir.
- Router'ın ARP tablosunu görme komutu:

```
Router1> show ip arp

Protocol  Address         Age(min)  Hardware Addr    Type    Interface
Internet  192.168.1.1     -         0001.C7EE.EB01   ARPA    GigabitEthernet0/0
Internet  192.168.1.10    19        0003.E4E7.0B72   ARPA    GigabitEthernet0/0
Internet  192.168.2.1     -         0001.C7EE.EB02   ARPA    GigabitEthernet0/1
Internet  192.168.2.20    19        00E0.A3A7.CE87   ARPA    GigabitEthernet0/1
```

---

## 3. ARP Sorunları ve Güvenlik

### 3.1 ARP Broadcasting Sorunu

**Problem:** Büyük ağlarda aşırı broadcast trafiği

```
1000 kullanıcılı bir ağda:
  - Her cihaz Gateway MAC'ini öğrenmek için ARP sorgusu gönderir
  - Bu 1000 broadcast paketi aynı anda ağa gider
  - Ağ performansı düşer
  - Network kartları gereksiz yükle meşgul olur
```

**Çözüm:** Ağları VLAN'larla küçük segmentlere bölmek

### 3.2 ARP Poisoning (ARP Zehirleme) Saldırısı

**Nasıl çalışır?**

```
Normal Durum:
  PC-A ──── ARP Request ────────────────► Router
  PC-A ◄─── ARP Reply (Router'dan) ───── Router

Saldırı Durumu (ARP Spoofing):
  PC-A ──── ARP Request ────────────────► Router + Saldırgan
  PC-A ◄─── Sahte ARP Reply ──────────── Saldırgan
             "Ben 192.168.1.1'im,
              MAC adresim 00-0C'dir"
              (aslında saldırganın MAC'i!)
```

**Saldırı akışı:**

```
  ┌─────────────────────────────────────────────────────────┐
  │                  ARP Poisoning Atağı                    │
  │                                                         │
  │   PC-A              Saldırgan (C)         Router (R1)   │
  │    │                     │                    │         │
  │    │── ARP Request ─────►│────────────────────►│        │
  │    │                     │                    │         │
  │    │◄─ Sahte ARP Reply ──│ "Ben Router'ım!"   │         │
  │    │   (saldırganın MAC) │                    │         │
  │    │                     │                    │         │
  │    │── Paket ───────────►│ (saldırgana gitti!)│         │
  │    │                     │──── Paketi ilet ──►│         │
  │    │                     │◄─── Dönüş paketi ──│         │
  │    │◄─ Dönüş paketi ─────│                    │         │
  │                                                         │
  │   = Man in the Middle (Aradaki Adam) Saldırısı         │
  └─────────────────────────────────────────────────────────┘
```

### 3.3 Koruma Yöntemleri

| Yöntem | Açıklama |
|---|---|
| **Dynamic ARP Inspection (DAI)** | Kurumsal switch özelliği; sahte ARP paketlerini tespit edip engeller |
| **Statik ARP girişleri** | Kritik cihazlar (gateway) için manuel MAC eşleşmesi |
| **802.1X Kimlik doğrulama** | Ağa bağlanmak için kullanıcı adı/parola gerektirir |
| **Port Security** | Switch portlarına yetkisiz cihaz bağlantısını engeller |
| **HTTPS/Şifreleme** | Trafik ele geçirilse bile içerik okunamaz |

> ⚠️ **Not:** Dynamic ARP Inspection (DAI) konfigürasyonu CCNA 2 – LAN Security konusunda anlatılacaktır.

---

## 4. IPv6 Komşu Saptama (Neighbor Discovery)

### 4.1 IPv6'da ARP'ın Yerini Ne Alır?

IPv6'da ARP **kullanılmaz**. Bunun yerine:

```
IPv4  →  ARP (Address Resolution Protocol)
IPv6  →  ICMPv6 Neighbor Discovery (ND) Protokolü
```

IPv6'da ayrıca **broadcast yoktur** → Bunun yerine **Multicast** kullanılır.

### 4.2 Neighbor Discovery (ND) Mesaj Tipleri

ICMPv6 ND protokolünün **5 mesaj tipi** vardır:

| Mesaj | Kısaltma | IPv4 Karşılığı | Amaç |
|---|---|---|---|
| Neighbor Solicitation | NS | ARP Request | Komşunun MAC adresini öğrenme isteği |
| Neighbor Advertisement | NA | ARP Reply | MAC adresi yanıtı |
| Router Solicitation | RS | - | Router keşfi isteği |
| Router Advertisement | RA | - | Router'dan IP bilgisi yanıtı |
| Redirect | - | ICMP Redirect | Daha iyi yol yönlendirmesi |

### 4.3 Adres Çözümleme – NS ve NA

#### Neighbor Solicitation (NS) – ARP Request'e karşılık

```
PC1 (2001:db8:acad:1::10)         PC2 (2001:db8:acad:1::11)
        │                                   │
        │── NS Mesajı (Multicast) ─────────►│
        │   "Hey 2001:db8:acad:1::11        │
        │    bana MAC adresini söyle!"       │
        │                                   │
```

#### Neighbor Advertisement (NA) – ARP Reply'a karşılık

```
        │                                   │
        │◄── NA Mesajı (Unicast) ───────────│
        │   "Ben 2001:db8:acad:1::11'im,    │
        │    MAC adresim f8-94-c2-e4-c5-0A" │
        │                                   │
```

### 4.4 Router Keşfi – RS ve RA

**PC ilk açıldığında statik IP yoksa:**

```
        PC (yeni bağlandı)                    Router
              │                                  │
              │── RS Mesajı ───────────────────► │
              │   "Ağda router var mı?            │
              │    IP bilgilerimi nasıl alırım?"  │
              │                                  │
              │◄── RA Mesajı ────────────────────│
              │   "Ben router'ım! Seçeneklerin:  │
              │    1) IP'ni kendin oluştur        │
              │    2) IP'ni kendin oluştur +      │
              │       DNS için DHCP'ye sor        │
              │    3) Her şeyi DHCP'den öğren"   │
```

#### RA Mesajındaki 3 Seçenek (SLAAC)

```
┌──────────────────────────────────────────────────────────────┐
│              Router Advertisement (RA) Seçenekleri           │
├──────┬───────────────────────────────────────────────────────┤
│  1   │ SLAAC (Stateless): IP'ni kendin oluştur              │
│      │ • Network prefix → Router'dan öğren                  │
│      │ • Host kısmı → Kendin uydur (/64)                    │
│      │ • Default Gateway → Router'dan öğren                 │
│      │ → DHCP sunucusu gerekmez                             │
├──────┼───────────────────────────────────────────────────────┤
│  2   │ SLAAC + DHCP: IP'ni kendin oluştur, DNS'i sor        │
│      │ • IP adresi → SLAAC ile                              │
│      │ • DNS adresi → DHCPv6'dan                            │
├──────┼───────────────────────────────────────────────────────┤
│  3   │ Stateful DHCPv6: Her şeyi DHCP'den öğren            │
│      │ • Kurumsal ağlarda tercih edilir                     │
│      │ • Kim hangi IP'yi aldı → DHCP kaydeder              │
└──────┴───────────────────────────────────────────────────────┘
```

### 4.5 Route Redirection (Yol Yönlendirme)

```
PC-A                    Router A              Router B
  │                         │                     │
  │── Paket ───────────────►│                     │
  │                         │                     │
  │◄─ Redirect Mesajı ──────│                     │
  │   "Bu hedefe gitmek için│                     │
  │    Router B'yi kullan!" │                     │
  │                         │                     │
  │── Paket ────────────────────────────────────►│
```

> IPv6 router'ları, host'ları daha uygun bir router'a yönlendirmek için Redirect mesajı kullanır.

### 4.6 ARP vs Neighbor Discovery Karşılaştırması

| Özellik | IPv4 – ARP | IPv6 – ICMPv6 ND |
|---|---|---|
| İstek paketi | ARP Request (Broadcast) | NS – Neighbor Solicitation (Multicast) |
| Yanıt paketi | ARP Reply (Unicast) | NA – Neighbor Advertisement (Unicast) |
| İletim türü | Broadcast | Multicast |
| IP başlığı | Yok (L2 protokolü) | Var (ICMPv6 içinde) |
| Router keşfi | Yok (ayrı DHCP/static) | RS/RA mesajları |
| Yol yönlendirme | ICMP Redirect | ICMPv6 Redirect |

---

## 5. Komut Özeti

### Windows / PC Komutları

```bash
# ARP tablosunu görüntüle
arp -a

# ARP tablosunu sil (Yönetici olarak çalıştır)
arp -d

# IP ve MAC bilgilerini görüntüle
ipconfig /all
```

### Cisco IOS Komutları (Router)

```bash
# ARP tablosunu görüntüle
Router> show ip arp

# Routing tablosunu görüntüle
Router> show ip route
```

---

## 6. Modül Özeti

```
┌───────────────────────────────────────────────────────────────────┐
│                    MODÜL 9 – ÖZET                                 │
├───────────────────────────────────────────────────────────────────┤
│                                                                   │
│  MAC Adresi      → Fiziksel adres, Katman 2, NIC'e sabit gelir   │
│  IP Adresi       → Mantıksal adres, Katman 3, ağa göre değişir   │
│                                                                   │
│  Aynı ağ         → Hedef MAC = Hedef cihazın MAC adresi          │
│  Farklı ağ       → Hedef MAC = Default Gateway'in MAC adresi     │
│                                                                   │
│  ARP             → IPv4'te IP → MAC çözümleme protokolü          │
│  ARP Request     → Broadcast (FF:FF:FF:FF:FF:FF)                 │
│  ARP Reply       → Unicast (sadece isteyene)                     │
│  ARP Tablosu     → arp -a (görüntüle), arp -d (sil)              │
│  Router ARP      → show ip arp                                    │
│                                                                   │
│  ARP Sorunları   → Aşırı broadcast / ARP Poisoning saldırısı     │
│  DAI             → Dynamic ARP Inspection (switch koruması)      │
│  MITM            → Man in the Middle saldırısı                   │
│                                                                   │
│  IPv6 ND         → ARP'ın IPv6'daki karşılığı (ICMPv6)          │
│  NS              → Neighbor Solicitation (ARP Request gibi)      │
│  NA              → Neighbor Advertisement (ARP Reply gibi)       │
│  RS              → Router Solicitation (Router keşfi)            │
│  RA              → Router Advertisement (IP bilgisi yanıtı)      │
│  IPv6'da         → Broadcast yok, Multicast kullanılır           │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

### Temel Kurallar

> 1. **Ethernet iletişimi MAC adresleriyle gerçekleşir** — IP adresi sadece yönlendirme için kullanılır.
> 2. **ARP olmadan Ethernet çalışmaz** — Kapatılamaz; statik MAC girişi yapılabilir.
> 3. **ARP Request broadcast, ARP Reply unicast'tir.**
> 4. **ARP tablosu geçicidir** — Girişler belirli süre sonra otomatik silinir.
> 5. **IPv6'da ARP yoktur** — ICMPv6 Neighbor Discovery kullanılır.
> 6. **IPv6'da broadcast yoktur** — Multicast kullanılır.

---

*CCNA1 – Introduction to Networks v7.0 | Modül 9: Adres Çözümleme*
