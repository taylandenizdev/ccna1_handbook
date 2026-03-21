# CCNA1 - Modül 13: ICMP (Internet Control Message Protocol)
> **Kaynak:** Cisco Networking Academy — Introduction to Networks v7.0 (ITN)  
> **Eğitmenler:** Gökhan AKIN - CCIE | Ozan BÜK - CCIE

---

## 📋 Modül Hedefleri

| Konu | Hedef |
|------|-------|
| ICMP Mesajları | ICMP'nin ağ bağlantısını test etmek için nasıl kullanıldığını açıklamak |
| Ping ve Traceroute Testleri | Ping ve traceroute araçlarını kullanarak ağ bağlantısını test etmek |

---

## 13.1 ICMP Mesajları

### ICMP Nedir?

**ICMP (Internet Control Message Protocol)**, belirli koşullar altında IP paketlerinin işlenmesiyle ilgili sorunlar hakkında geri bildirim sağlayan bir **3. Katman (Network Layer) protokolüdür.**

- **ICMPv4** → IPv4 için mesajlaşma protokolü
- **ICMPv6** → IPv6 için mesajlaşma protokolü (ek işlevler içerir)

> ⚠️ **Önemli:** ICMPv4 mesajları **zorunlu değildir** ve güvenlik nedeniyle genellikle ağ içinde engellenir.

---

### ICMP Paket Yapısı

ICMP, doğrudan IP paketinin içine kapsüllenir — 4. Katman başlığı (TCP/UDP) **yoktur**.

```
┌─────────────────────────────────────────────────────────────┐
│  Ethernet Başlığı │  IP Başlığı  │  ICMP Başlığı  │  FCS   │
│    (2. Katman)    │  (3. Katman) │  (3. Katman)   │        │
└─────────────────────────────────────────────────────────────┘

ICMP Başlık Yapısı:
┌──────────┬──────────┬──────────────────────┐
│   TIP    │   KOD    │      CHECKSUM        │
│  (Type)  │  (Code)  │                      │
├──────────┴──────────┴──────────────────────┤
│                  UNUSED                    │
├────────────────────────────────────────────┤
│                   DATA                     │
└────────────────────────────────────────────┘
```

> IP başlığındaki **Protokol alanı = 1** → ICMP paketi  
> IP başlığındaki **Protokol alanı = 6** → TCP paketi  
> IP başlığındaki **Protokol alanı = 17** → UDP paketi  
> IPv6 başlığındaki **Next Header = 58** → ICMPv6 paketi

---

### Ortak ICMP Mesaj Tipleri (ICMPv4 ve ICMPv6)

| Mesaj Tipi | Açıklama |
|------------|----------|
| **Host Reachability** | Hedef cihaza erişilebilirlik testi (ping) |
| **Destination/Service Unreachable** | Hedefe veya servise ulaşılamıyor |
| **Time Exceeded** | TTL (veya Hop Limit) sıfırlandı |

---

### ICMP Tip ve Kod Değerleri (ICMPv4)

| Tip | Kod | Açıklama |
|-----|-----|----------|
| **0** | 0 | Echo Reply (Ping yanıtı) |
| **3** | 0 | Destination Network Unreachable |
| **3** | 1 | Destination Host Unreachable |
| **3** | 2 | Destination Protocol Unreachable |
| **3** | 3 | Destination Port Unreachable |
| **3** | 4 | Fragmentation Needed, DF flag set |
| **3** | 5 | Source Route Failed |
| **5** | 0 | Redirect Datagram (Network) |
| **5** | 1 | Redirect Datagram (Host) |
| **5** | 2 | Redirect Datagram (ToS + Network) |
| **5** | 3 | Redirect Datagram (ToS + Host) |
| **8** | 0 | Echo Request (Ping isteği) |
| **9** | 0 | Router Advertisement |
| **10** | 0 | Router Solicitation |
| **11** | 0 | Time Exceeded (Transit'te TTL sıfırlandı) |
| **11** | 1 | Time Exceeded (Fragment yeniden birleşme) |
| **12** | 0 | Parameter Problem (Pointer hatası) |
| **13** | 0 | Timestamp |
| **14** | 0 | Timestamp Reply |

---

### 1. Host Erişilebilirliği (Echo Request / Echo Reply)

**Ping** komutu ICMP Echo mesajlarını kullanarak hedef cihazın erişilebilir olup olmadığını test eder.

```
┌──────────┐    Echo Request (Type 8, Code 0)    ┌──────────┐
│          │  ──────────────────────────────────► │          │
│  Host A  │                                      │  Host B  │
│          │  ◄──────────────────────────────────  │          │
└──────────┘    Echo Reply   (Type 0, Code 0)    └──────────┘
```

**Ping çıktısı örneği:**
```
C:\>ping 208.67.220.220

Pinging 208.67.220.220 with 32 bytes of data:
Reply from 208.67.220.220: bytes=32 time=14ms TTL=56
Reply from 208.67.220.220: bytes=32 time=17ms TTL=56
Reply from 208.67.220.220: bytes=32 time=13ms TTL=56
Reply from 208.67.220.220: bytes=32 time=11ms TTL=56

Ping statistics for 208.67.220.220:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Approximate round trip times in milli-seconds:
    Minimum = 11ms, Maximum = 17ms, Average = 13ms
```

**Windows Ping Parametreleri:**

| Parametre | Açıklama |
|-----------|----------|
| `-t` | Sürekli ping at (Ctrl+C ile durdur) |
| `-l <boyut>` | Paket boyutunu belirle (varsayılan 32 byte) |
| `-w <ms>` | Bekleme süresini belirle (varsayılan 2000ms) |
| `-n <sayı>` | Gönderilecek paket sayısını belirle |

---

### 2. Destination Unreachable (Type 3)

Bir router veya hedef sunucu paketi iletmekte başarısız olursa ICMP Type 3 mesajı gönderir.

```
┌──────────┐   Paket ──►  ┌──────────┐        ┌──────────┐
│          │              │          │   ✗     │          │
│  Host A  │              │  Router  │ ──────► │  Server  │
│          │              │          │         │          │
└──────────┘              └──────────┘         └──────────┘
      ▲                        │
      │   Type 3 (Destination  │
      └── Unreachable)  ◄──────┘
```

**ICMPv4 — Destination Unreachable Kodları (Type 3):**

| Kod | Açıklama | Kaynağı |
|-----|----------|---------|
| 0 | Net Unreachable | Router (routing tablosunda yok) |
| 1 | Host Unreachable | Router (ARP yanıtı gelmiyor) |
| 2 | Protocol Unreachable | Sunucu (bilinmeyen protokol) |
| 3 | Port Unreachable | Sunucu (port kapalı) |

**ICMPv6 — Destination Unreachable Kodları (Type 1):**

| Kod | Açıklama |
|-----|----------|
| 0 | No Route to Destination |
| 1 | Administratively Prohibited (Firewall) |
| 2 | Beyond Scope of Source Address |
| 3 | Address Unreachable |
| 4 | Port Unreachable |

---

### 3. Time Exceeded (Type 11)

TTL (Time To Live) değeri 0'a düştüğünde router paketi düşürür ve kaynağa **Type 11** mesajı gönderir.

```
PC ──(TTL=64)──► R1 ──(TTL=63)──► R2 ──(TTL=62)──► ...
                                         │
                              TTL=0 olduğunda:
                                         │
                              Type 11 mesajı ◄─────────
```

**Döngü (Loop) Senaryosu:**
```
        ┌─────────────────────────────────┐
        │                                 │
PC ──► R1 ──(TTL=64)──► R2 ──► R1 ──► R2 ──► ...
                               (Yanlış routing)
                                         │
                              TTL=0, Type 11 mesajı
                              "TTL expired in transit"
```

> 📝 **Not:** Time Exceeded mesajları **traceroute** aracının çalışma prensibi için kritik öneme sahiptir.

---

### 4. Redirect Message (Type 5)

Router, paketi daha iyi bir yola yönlendirmek için kullanır.

```
         ┌──── Daha iyi yol var! ────┐
         │    (Type 5 Redirect)      │
         ▼                           │
┌──────────┐          ┌──────────┐   │   ┌──────────┐
│          │ ─paket─► │Default   │───┘   │  GW2     │
│   PC     │          │   GW1   │ ──────►│          │
└──────────┘          └──────────┘       └──────────┘
```

---

## ICMPv6 — Neighbor Discovery Protocol (NDP)

ICMPv6, IPv4'te ARP protokolünün yaptığı işlevi de üstlenir. IPv6'da **broadcast yoktur**, bunun yerine **multicast** kullanılır.

### ICMPv6 NDP Mesaj Tipleri

| Tip | İsim | Kısaltma | İşlev |
|-----|------|----------|-------|
| 133 | Router Solicitation | RS | Cihaz router'a "Ben IP bilgimi nasıl alacağım?" diye sorar |
| 134 | Router Advertisement | RA | Router cihaza IP prefix, gateway bilgilerini yollar |
| 135 | Neighbor Solicitation | NS | "Bu IPv6 adresinin MAC adresi nedir?" (ARP Request karşılığı) |
| 136 | Neighbor Advertisement | NA | "Benim MAC adresim şu." (ARP Reply karşılığı) |

---

### ICMPv6 — IPv6 Dinamik Adres Atama Akışı

```
┌────────────────────────────────────────────────────────────────────┐
│            IPv6 Otomatik Adres Yapılandırma Süreci                 │
│                                                                    │
│  PC Açılır                                                         │
│      │                                                             │
│      ▼                                                             │
│  [RS Mesajı Gönder] ──Type 133──► Router                          │
│  "IP bilgimi nasıl alacağım?"                                      │
│      │                                                             │
│      ▼                                                             │
│  [RA Mesajı Al]     ◄──Type 134── Router                          │
│  PREFIX + GW bilgisi gelir                                         │
│      │                                                             │
│      ▼                                                             │
│  RA'daki Managed/Other bitlerine bak:                              │
│      │                                                             │
│      ├── M=0, O=0 ──► SLAAC                                       │
│      │               (IP'yi kendin türet, DNS'i statik gir)       │
│      │                                                             │
│      ├── M=0, O=1 ──► SLAAC + DHCPv6                             │
│      │               (IP'yi kendin türet, DNS'i DHCPv6'dan al)   │
│      │                                                             │
│      └── M=1, O=0 ──► Stateful DHCPv6                            │
│                        (Her şeyi DHCPv6'dan al)                   │
└────────────────────────────────────────────────────────────────────┘
```

---

### SLAAC (Stateless Address AutoConfiguration)

Router her **200 saniyede bir** RA mesajı yayınlar. PC bu beklemeyi kısaltmak için RS mesajı gönderebilir.

**RA Mesajı İçeriği:**
```
RA Mesajı (Type 134):
├── PREFIX:  2001:db8:acad:1::/64
├── Gateway: FE80::1  (Link-Local adresi)
├── Managed: 0 veya 1
└── Other:   0 veya 1
```

**SLAAC ile IP Oluşturma:**
```
Network kısmı (Router'dan):  2001:db8:acad:1::
                                       +
Interface ID (Cihazın kendisi türetir): xxxx:xxxx:xxxx:xxxx
                                       =
Tam IPv6 Adresi:             2001:db8:acad:1:xxxx:xxxx:xxxx:xxxx/64
```

---

### Neighbor Solicitation / Advertisement (ARP'ın IPv6 Karşılığı)

```
                NS (Type 135)
R1 ─────────────────────────────────────────► PC1
"2001:db8:acad:1::10'un MAC adresi nedir?"

                NA (Type 136)
R1 ◄─────────────────────────────────────────  PC1
"Benim MAC adresim: 00:aa:bb:cc:dd:ee"
```

**Solicited-Node Multicast Adresi:**
```
FF02:0000:0000:0000:0000:0001:FF??:???? 
                                   └── IPv6 adresinin son 24 biti
```

---

### Duplicate Address Detection (DAD)

Yeni IP atanmadan önce aynı IP'nin ağda kullanılıp kullanılmadığı kontrol edilir.

**IPv4'te:**
```
PC ──[ARP Request]──► "192.168.1.10'u kullanan var mı?"
   ◄──[ARP Reply]────  Varsa: IP Conflict uyarısı!
   ──[Sessizlik]────   Yoksa: IP kullanılmaya başlanır
```

**IPv6'da (DAD):**
```
PC ──[NS, Type 135]──► "2001:db8::10'u kullanan var mı?"
   ◄──[NA, Type 136]──  Varsa: IP kullanılamaz!
   ──[Sessizlik]─────   Yoksa: IP kullanılmaya başlanır
```

---

## 13.2 Ping ve Traceroute Testleri

### Ping — Bağlantı Testi

**Adım Adım Bağlantı Testi Metodolojisi:**

```
┌────────────────────────────────────────────────────────────────┐
│              Sistematik Ağ Sorun Giderme Adımları              │
│                                                                │
│  ADIM 1: Loopback Testi                                        │
│  ─────────────────────                                         │
│  ping 127.0.0.1 (IPv4) veya ping ::1 (IPv6)                  │
│  ✓ Başarılı → TCP/IP stack çalışıyor                          │
│  ✗ Başarısız → TCP/IP kurulumunda sorun var                   │
│                    │                                           │
│                    ▼                                           │
│  ADIM 2: Kendi IP Adresi                                       │
│  ─────────────────────                                         │
│  ping <kendi_ip_adresin>                                       │
│  ✓ Başarılı → NIC çalışıyor, IP yapılandırması doğru          │
│                    │                                           │
│                    ▼                                           │
│  ADIM 3: Default Gateway                                       │
│  ──────────────────────                                        │
│  ping <default_gateway_ip>                                     │
│  ✓ Başarılı → Yerel ağ bağlantısı sağlam                      │
│  ✗ Başarısız → Kablo/switch/router sorunu                     │
│                    │                                           │
│                    ▼                                           │
│  ADIM 4: Uzak Host                                             │
│  ─────────────────                                             │
│  ping 8.8.8.8 (veya ping'e cevap veren bir IP)                │
│  ✓ Başarılı → İnternet bağlantısı var                         │
│  ✗ Başarısız → ISP bağlantısında sorun var                    │
└────────────────────────────────────────────────────────────────┘
```

---

### Ping Çıktısı Yorumlama

**Windows/Linux PC:**
```
Cevap Türleri:
  Reply from x.x.x.x: bytes=32 time=14ms → ✅ Başarılı
  Request timed out.                       → ❌ Cevap yok (2 sn beklendi)
  Destination host unreachable.            → ❌ ICMP Type 3 mesajı alındı
  TTL expired in transit.                  → ❌ ICMP Type 11 mesajı alındı
```

**Cisco Router/Switch:**
```
S1#ping 192.168.20.2
Sending 5, 100-byte ICMP Echos to 192.168.20.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/1 ms

Semboller:
  !  → Echo reply alındı (başarılı)
  .  → Zaman aşımı (timeout)
  U  → Destination Unreachable
```

> ⚠️ **İlk paketin timeout alması normaldir!** Bunun nedeni: echo request gönderilmeden önce ARP (IPv4) veya ND (IPv6) çözümlemesi yapılması gerekir. Bu işlem ilk paketin 2 saniyelik süresini aşabilir.

---

### Loopback Ping

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│   C:\>ping 127.0.0.1    →  IPv4 Loopback testi      │
│   C:\>ping ::1          →  IPv6 Loopback testi      │
│                                                     │
│   Başarılı  → TCP/IP protokol yığını çalışıyor      │
│   Başarısız → TCP/IP kurulumunu kontrol et          │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

### Traceroute — Yol Testi

**Traceroute Nasıl Çalışır?**

```
┌────────────────────────────────────────────────────────────────────┐
│                    Traceroute Çalışma Prensibi                     │
│                                                                    │
│  Tur 1: TTL=1 gönder                                               │
│  PC ──(TTL=1)──► R1                                               │
│                  R1: TTL=0, "Time Exceeded" (Type 11) gönder      │
│                  PC: R1'in IP adresini öğrendi ✓                  │
│                                                                    │
│  Tur 2: TTL=2 gönder                                               │
│  PC ──(TTL=2)──► R1 ──(TTL=1)──► R2                              │
│                                   R2: TTL=0, Type 11 gönder       │
│                                   PC: R2'nin IP adresini öğrendi ✓│
│                                                                    │
│  Tur 3: TTL=3 gönder                                               │
│  PC ──(TTL=3)──► R1 ──(TTL=2)──► R2 ──(TTL=1)──► R3             │
│                                                    Type 11 gönder │
│                                                                    │
│  Son Tur: Hedef cevap verir                                        │
│  Hedef: "Port Unreachable" (Type 3, Code 3) → Trace tamamlandı   │
└────────────────────────────────────────────────────────────────────┘
```

**Her tur için 3 paket gönderilir**, round-trip time ölçülür:
```
tracert -d 8.8.8.8

  1     7 ms    5 ms    2 ms  192.168.0.1
  2       *       *       *   Request timed out.
  3    12 ms   10 ms   10 ms  172.25.34.29
  4    14 ms   15 ms   14 ms  10.59.10.109
  ...
 13    64 ms   69 ms   68 ms  8.8.8.8
```

| Sembol | Anlam |
|--------|-------|
| `ms` değerleri | Round-Trip Time (gidiş-dönüş süresi) |
| `*` | Yanıt yok (router ICMP engelliyor olabilir) |
| `Request timed out` | 3 paketten hiçbirisi cevap vermedi |

**Traceroute Komutları:**

| Platform | Komut |
|----------|-------|
| Windows | `tracert <ip_adresi>` |
| Windows (DNS çözümlemesiz) | `tracert -d <ip_adresi>` |
| Cisco IOS (Router) | `traceroute <ip_adresi>` |
| Linux/macOS | `traceroute <ip_adresi>` |

---

### Döngü (Routing Loop) Tespiti

```
Traceroute çıktısında döngü görünümü:

  1    1 ms   192.168.1.1
  2    2 ms   10.0.0.1
  3    3 ms   10.0.0.2
  4    4 ms   10.0.0.1    ← Tekrar!
  5    5 ms   10.0.0.2    ← Döngü var!
  6    6 ms   10.0.0.1
  ...
 30    *    *    *         (TTL=30'a ulaştı, paket öldü)
```

---

## 📊 Hızlı Referans Tablosu

### ICMP vs ICMPv6 Karşılaştırması

| Özellik | ICMPv4 | ICMPv6 |
|---------|--------|--------|
| Echo Request | Type 8, Code 0 | Type 128, Code 0 |
| Echo Reply | Type 0, Code 0 | Type 129, Code 0 |
| Dest. Unreachable | Type 3 | Type 1 |
| Time Exceeded | Type 11 | Type 3 |
| Router Solicitation | ✗ (yok) | Type 133 |
| Router Advertisement | ✗ (yok) | Type 134 |
| Neighbor Solicitation | ✗ (ARP kullanılır) | Type 135 |
| Neighbor Advertisement | ✗ (ARP kullanılır) | Type 136 |
| Loopback Adresi | 127.0.0.1 | ::1 |

---

### Ağ Sorun Giderme Araçları Özeti

| Araç | Komut | İşlev |
|------|-------|-------|
| **ipconfig** | `ipconfig /all` | IP yapılandırmasını göster |
| **Ping (loopback)** | `ping 127.0.0.1` | TCP/IP stack çalışıyor mu? |
| **Ping (gateway)** | `ping <gateway_ip>` | Yerel ağ bağlantısı var mı? |
| **Ping (uzak)** | `ping 8.8.8.8` | İnternet bağlantısı var mı? |
| **Traceroute** | `tracert <ip>` | Paket hangi yoldan gidiyor? |
| **NSLookup** | `nslookup <domain>` | DNS çalışıyor mu? |
| **Netstat** | `netstat` | Açık portlar ve bağlantılar |

---

## 🧠 Aklında Kalsın

1. **ICMP, 3. Katman protokolüdür** — Port numarası yoktur, TCP/UDP başlığı yoktur.
2. **Ping cevap gelmemesi = cihaz kapalı değil!** Firewall veya güvenlik politikası ICMP'yi engelliyor olabilir.
3. **İlk ping paketi timeout alabilir** — ARP/ND çözümlemesi süresi nedeniyle bu normaldir.
4. **Traceroute TTL'i 1'den başlatarak artırır** — Her adımda router'ın IP adresini öğrenir.
5. **IPv6'da ARP yoktur** — Onun yerine ICMPv6 Neighbor Solicitation/Advertisement kullanılır.
6. **SLAAC ile statik IP gerekmez** — Router RA mesajıyla prefix yollar, cihaz kendi IP'sini türetir.
7. **DAD (Duplicate Address Detection)** — Yeni IP almadan önce ağda aynı IP kullanılıyor mu diye kontrol eder.

---

## 📝 Modül Özeti

```
ICMP
├── ICMPv4
│   ├── Echo Request/Reply     (Ping → Type 8/0)
│   ├── Destination Unreachable (Type 3)
│   │   ├── Code 0: Net Unreachable
│   │   ├── Code 1: Host Unreachable
│   │   ├── Code 2: Protocol Unreachable
│   │   └── Code 3: Port Unreachable
│   ├── Time Exceeded          (Type 11 → Traceroute)
│   └── Redirect               (Type 5)
│
└── ICMPv6
    ├── Echo Request/Reply     (Ping → Type 128/129)
    ├── Destination Unreachable (Type 1)
    ├── Time Exceeded          (Type 3)
    └── Neighbor Discovery Protocol (NDP)
        ├── Router Solicitation   (Type 133) ─── PC → Router
        ├── Router Advertisement  (Type 134) ─── Router → PC
        ├── Neighbor Solicitation (Type 135) ─── ARP Request yerine
        └── Neighbor Advertisement(Type 136) ─── ARP Reply yerine
```

---

*CCNA1 Module 13 Handbook — agyoneticileri.org*
