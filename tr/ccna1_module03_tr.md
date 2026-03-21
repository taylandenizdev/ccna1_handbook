# CCNA1 Modül 3: Protokoller ve Modeller


---

## İÇİNDEKİLER

1. [Temel Kavramlar: MAC ve IP Adresleri](#1-temel-kavramlar)
2. [İletişim Kuralları (Protokoller)](#2-iletişim-kuralları)
3. [Protokol Kümeleri](#3-protokol-kümeleri)
4. [Standart Organizasyonları](#4-standart-organizasyonları)
5. [Referans Modelleri: OSI ve TCP/IP](#5-referans-modelleri)
6. [Veri Kapsülleme ve De-Kapsülleme](#6-veri-kapsülleme)
7. [Veri Erişimi: Aynı ve Farklı Ağlar](#7-veri-erişimi)
8. [Özet ve Sınav Notları](#8-özet-ve-sınav-notları)

---

## 1. Temel Kavramlar

### 1.1 MAC Adresi (Fiziksel Adres)

MAC (Media Access Control) adresi, ağ kartı (NIC – Network Interface Card) üzerine donanımsal olarak işlenmiş eşsiz bir adrestir.

**Özellikleri:**
- 48 bit uzunluğunda
- 12 adet hexadecimal (onaltılık) karakterle gösterilir
- Örnek: `9C-35-5B-5F-AA-BB`
- "Burned-in Address" (Donanıma kazılmış adres) olarak da bilinir
- Fiziksel adres olarak adlandırılır; değiştirilemez (yazılımsal olarak maskelenebilir)
- Dünya üzerinde aynı MAC adresine sahip iki NIC bulunmaz
- Kablolu (Ethernet) ve kablosuz (Wi-Fi) kartların her birinin ayrı MAC adresi vardır

```
MAC Adresi Yapısı (48 bit = 6 bayt):
┌─────────────────┬─────────────────┐
│   OUI (24 bit)  │  Cihaz ID(24bit)│
│  Üretici Kodu   │  Seri Numarası  │
└─────────────────┴─────────────────┘
 9C-35-5B    -    5F-AA-BB
```

**MAC Adresi Ne Zaman Kullanılır?**
- Yalnızca **aynı yerel ağ (LAN)** içindeki iletişimde kullanılır
- Switch'ler, paketi doğru porta yönlendirmek için MAC tablosuna bakar
- Farklı ağlara çıkılırken MAC adresi değişir; IP adresi değişmez

---

### 1.2 IP Adresi (Mantıksal Adres)

IP adresi, ağ kartına yazılımsal olarak atanan ve hangi ağda bulunulduğunu gösteren mantıksal adrestir.

**Özellikleri:**
- IPv4: 32 bit, 4 oktet (örnek: `192.168.1.5`)
- Her oktet 0-255 arasında değer alır
- Mantıksal adres: bulunulan ağa göre değişir
- İki kısımdan oluşur:

```
IP Adresi Yapısı:
┌────────────────────┬───────────┐
│   Network Kısmı    │ Host Kısmı│
│  (Ağ Numarası)     │(Cihaz No) │
└────────────────────┴───────────┘
  192.168.1      .      5
  (Hangi ağ?)        (Hangi cihaz?)
```

**Bilgisayarın İnternete Bağlanması için Gereken 4 Ayar:**

| Ayar | Açıklama | Örnek |
|------|----------|-------|
| IP Adresi | Cihazın ağdaki adresi | 192.168.1.5 |
| Subnet Mask | Ağ/host kısmını belirler | 255.255.255.0 |
| Default Gateway | Router'ın IP adresi | 192.168.1.1 |
| DNS Sunucusu | İsim → IP çözümler | 8.8.8.8 |

---

### 1.3 Switch ve Router'ın Rolü

```
Ağ Topolojisi (4 Network Örneği):
─────────────────────────────────────────────────────────────────
 [PC1]──┐                                          ┌──[WebSrv]
 .1.5   │   [Switch1]──[Router1]──[Router2]──[Router3]──[Switch2]
 [PC2]──┘                                          └──[DNSSrv]
 .1.6      192.168.1.0  .2.0/.3.0    192.168.4.0

 Switch → MAC adresine göre yönlendirir (Katman 2)
 Router → IP adresine göre yönlendirir (Katman 3)
─────────────────────────────────────────────────────────────────
```

| Cihaz | Çalıştığı Katman | Tablo Türü | IP Adresi |
|-------|-----------------|-----------|-----------|
| Switch | Katman 2 | MAC Tablosu | Yok* |
| Router | Katman 3 | Routing Tablosu | Var (her arayüz için) |
| PC/Sunucu | Tüm katmanlar | — | Var |

> *Yönetimli (managed) switch'lerin yönetim IP'si olabilir, ancak paket yönlendirme için IP kullanmazlar.

---

## 2. İletişim Kuralları

### 2.1 Protokol Nedir?

**Protokol**, iletişimin gerçekleşmesi için önceden belirlenmiş kurallar dizisidir.

Mektup haberleşmesindeki kurallar gibi (alıcı adresi ortaya, gönderen üst sola), bilgisayar haberleşmesinde de her paketin belirli bir yapıya uyması gerekir.

### 2.2 Ağ Protokolü Gereksinimleri

```
Protokol Gereksinimleri:
        ┌──────────────────────────┐
        │        PROTOKOL          │
        └────────────┬─────────────┘
          ┌──────────┼──────────┐
    ┌─────┴────┐ ┌───┴───┐ ┌───┴────┐
    │  Mesaj   │ │Biçim/ │ │Zamanlama│
    │ Kodlama  │ │Kapsül.│ │& Boyut  │
    └──────────┘ └───────┘ └────────┘
          │           │         │
    Elektrik     Başlık &   Akış Kont.
    Dalgası      Zarflama   Boyut Sınırı
```

**a) Mesaj Kodlaması (Encoding)**
- Verinin iletim ortamına uygun formata dönüştürülmesi
- Bakır kablo → elektrik voltaj seviyeleri
- Fiber → ışık darbeleri
- Kablosuz → elektromanyetik dalgalar
- Alıcı tarafta tersine çevrilir (decoding)

**b) Mesaj Biçimlendirme ve Kapsülleme**
- Her mesajın belirli bir yapıya (format) uyması gerekir
- Datanın başına/sonuna başlık bilgileri eklenir
- Mektup mantığı: veri = mektup, başlık = zarf üzerindeki adres

**c) Mesaj Boyutu**
- Büyük veriler küçük parçalara (segmentlere) bölünür
- Neden? Tek parça gönderimde 1 bit bozulursa tüm veri yeniden gönderilmeli
- Parçalı gönderimde yalnızca bozulan parça yeniden istenir

**d) Mesaj Zamanlaması**

| Kavram | Açıklama |
|--------|----------|
| Akış Kontrolü (Flow Control) | İletim hızını düzenler; alıcı kapasitesine göre hızlanır/yavaşlar |
| Yanıt Zaman Aşımı | Cevap gelmezse paketi yeniden gönderir |
| Erişim Yöntemi | Kim ne zaman gönderecek? (CSMA/CA algoritması) |

**e) Mesaj Teslim Seçenekleri**

```
Üç İletim Tipi:
┌──────────┐    ┌────────────┐    ┌───────────┐
│ UNICAST  │    │ MULTICAST  │    │ BROADCAST │
│          │    │            │    │           │
│  A → B   │    │  A → Grup  │    │ A → Tümü  │
│  (1'e 1) │    │  (1'e çok) │    │ (1'e hep) │
└──────────┘    └────────────┘    └───────────┘
  Tek alıcı     Üye olan grup    Tüm ağ cihazları
  
  Not: IPv6'da Broadcast YOK → Anycast kullanılır
```

---

### 2.3 Protokol Tipleri

| Protokol Tipi | Görevi | Örnekler |
|--------------|--------|---------|
| Ağ İletişim | Cihazlar arası veri taşıma | Ethernet, IP, TCP, HTTP |
| Ağ Güvenliği | Kimlik doğrulama, şifreleme | SSH, SSL, TLS |
| Yönlendirme | Router'lar arası rota paylaşımı | OSPF, BGP, EIGRP |
| Servis Bulma | Otomatik IP/servis keşfi | DHCP, DNS |

**Web Sayfası Açarken Kullanılan 4 Protokol:**
```
[Tarayıcı] www.google.com
     │
     ├─ HTTP/HTTPS  → Uygulama Katmanı  (web sayfası verisi)
     ├─ TCP         → Transport Katmanı (güvenilir iletim)
     ├─ IP          → Network Katmanı   (adres & yönlendirme)
     └─ Ethernet    → Veri Bağlantı K.  (fiziksel çerçeve)
```

---

## 3. Protokol Kümeleri

### 3.1 Tarihsel Süreç

```
Protokol Kümeleri Evrimi:
──────────────────────────────────────────
1985  AppleTalk     → Apple cihazları için
1983  SPX/IPX       → Novell NetWare için
1969  TCP/IP        → ARPA/Savunma Bakanlığı
──────────────────────────────────────────
        ↓
TCP/IP tüm platformları kapsadı ve
internet standardı haline geldi (de facto)
──────────────────────────────────────────
```

### 3.2 TCP/IP Protokol Paketi

TCP/IP, günümüz internetinin temelini oluşturan protokol kümesidir. Açık standarttır, ücretsizdir, tüm üreticiler tarafından kullanılabilir.

```
TCP/IP Katmanları ve Protokolleri:
┌─────────────────────────────────────────────────────┐
│                APPLICATION LAYER                    │
│  DNS │ DHCP │ SMTP │ POP3 │ IMAP │ FTP │ HTTP │HTTPS│
├─────────────────────────────────────────────────────┤
│                TRANSPORT LAYER                      │
│         TCP (güvenilir)  │  UDP (hızlı)             │
├─────────────────────────────────────────────────────┤
│                INTERNET LAYER                       │
│    IPv4 │ IPv6 │ ICMPv4 │ ICMPv6 │ OSPF │ BGP      │
├─────────────────────────────────────────────────────┤
│              NETWORK ACCESS LAYER                   │
│         ARP │ Ethernet │ WLAN (802.11)              │
└─────────────────────────────────────────────────────┘
```

---

## 4. Standart Organizasyonları

### 4.1 İnternet Standartları Hiyerarşisi

```
İnternet Yönetim Hiyerarşisi:
┌─────────────────────────────┐
│    ISOC (Internet Society)  │  ← İnternetin genel gelişimi
└──────────────┬──────────────┘
               │
┌──────────────▼──────────────┐
│    IAB (Internet Arch. Bd.) │  ← Mimari yönetim
└─────────┬────────┬──────────┘
          │        │
┌─────────▼──┐ ┌───▼────────┐
│    IETF    │ │    IRTF    │
│(Protokol   │ │(Araştırma) │
│geliştirme) │ │            │
└────────────┘ └────────────┘
      │
  RFC Dokümanları
  (HTTP, TCP, IP için teknik
   belirtimler – herkese açık)
```

### 4.2 Organizasyonlar ve Görevleri

| Organizasyon | Açıklama | Önemli Standartlar |
|-------------|----------|-------------------|
| **ISOC** | İnternetin açık gelişimini destekler | — |
| **IAB** | İnternet mimarisini yönetir | — |
| **IETF** | TCP/IP protokollerini geliştirir | RFC dokümanları |
| **IRTF** | Uzun vadeli internet araştırması | Yapay zeka, kriptoloji |
| **ICANN** | Alan adları & IP tahsisi koordinasyonu | — |
| **IANA** | IP blokları, port numaraları, domain yönetimi | Port 80=HTTP, 443=HTTPS |
| **IEEE** | Donanım standartları | 802.3 (Ethernet), 802.11 (Wi-Fi) |
| **EIA/TIA** | Kablo & konektör standartları | TIA-568B kablolama |
| **ITU-T** | Telekomünikasyon standartları | ADSL, VDSL2 |

### 4.3 Yaygın Port Numaraları

| Protokol | Port | Açıklama |
|---------|------|---------|
| HTTP | 80 | Web sayfası (şifresiz) |
| HTTPS | 443 | Web sayfası (şifreli/TLS) |
| SMTP | 25 | Mail gönderme |
| FTP | 20/21 | Dosya transferi |
| DNS | 53 | İsim çözümleme |
| DHCP | 67/68 | Otomatik IP atama |
| SSH | 22 | Güvenli uzak bağlantı |

### 4.4 IEEE Kablosuz Standartları Evrimi

```
IEEE 802.11 (Wi-Fi) Standartları:
──────────────────────────────────────
 802.11b  →   11 Mbps
 802.11a  →   54 Mbps
 802.11g  →   54 Mbps
 802.11n  →  600 Mbps
 802.11ac → 6.9 Gbps
 802.11ax → 9.6 Gbps  (Wi-Fi 6 – güncel)
──────────────────────────────────────
Laptop kutusunda "802.11ax" görürseniz
en güncel teknolojiyi kullanıyorsunuzdur.
```

---

## 5. Referans Modelleri

### 5.1 Neden Katmanlı Model?

Karmaşık ağ haberleşmesini anlaşılır kılmak için haberleşme adımları katmanlara bölünmüştür. Faydaları:

- Her katman bağımsız geliştirilebilir (IPv4 → IPv6 geçişi diğer katmanları etkilemedi)
- Farklı üreticilerin ürünleri birlikte çalışabilir
- Sorun tespiti kolaylaşır
- Ortak terminoloji sağlar

### 5.2 OSI Referans Modeli (7 Katman)

```
OSI Referans Modeli:
┌───┬──────────────┬─────────────────────────────────────────┐
│ 7 │ APPLICATION  │ Son kullanıcı arayüzü                   │
│   │ (Uygulama)   │ HTTP, HTTPS, FTP, SMTP, DNS, DHCP       │
├───┼──────────────┼─────────────────────────────────────────┤
│ 6 │PRESENTATION  │ Veri formatı: JPEG, PDF, ASCII          │
│   │(Sunum)       │ Şifreleme (Encryption), Sıkıştırma      │
├───┼──────────────┼─────────────────────────────────────────┤
│ 5 │   SESSION    │ Oturum takibi, senkronizasyon           │
│   │  (Oturum)    │ Facebook oturumu, dosya transferi takibi│
├───┼──────────────┼─────────────────────────────────────────┤
│ 4 │  TRANSPORT   │ Port numaraları, segmentasyon           │
│   │  (Taşıma)    │ Sıra no, ACK no, akış kontrolü          │
│   │              │ TCP (güvenilir), UDP (hızlı)            │
├───┼──────────────┼─────────────────────────────────────────┤
│ 3 │   NETWORK    │ IP adresleri, yol seçimi (routing)      │
│   │    (Ağ)      │ IP, ICMP, yönlendirme protokolleri      │
├───┼──────────────┼─────────────────────────────────────────┤
│ 2 │  DATA LINK   │ MAC adresleri, hata kontrolü (CRC)      │
│   │(Veri Bağl.)  │ Ethernet, PPP, Framing                  │
├───┼──────────────┼─────────────────────────────────────────┤
│ 1 │   PHYSICAL   │ Bit iletimi, kablo, konnektör           │
│   │   (Fiziksel) │ Encoding, bakır/fiber/kablosuz          │
└───┴──────────────┴─────────────────────────────────────────┘

 ←── UYGULAMA KATMANLARI (7-6-5): Veri oluşturma
 ←── VERİ İLETİM KATMANLARI (4-3-2-1): Taşıma
```

**OSI Katman Ezber Cümlesi (aşağıdan yukarı):**
> **A**kşam **P**azarından **S**onra **T**üm **N**iğde **D**omatesleri **P**atlaktır
> (All People Seem To Need Data Processing)

### 5.3 Her Katmanda Eklenen Bilgi

```
Kapsülleme Süreci (Gönderici Tarafta):

 7-6-5  ┌──────────────────────────┐
        │          DATA            │  ← HTTP isteği oluşturulur
        └──────────────────────────┘
   4    ┌────────┬─────────────────┐
        │TCP HDR │      DATA       │  ← Kaynak/Hedef PORT eklenir
        │Src:5001│                 │    Sıra No, ACK No eklenir
        │Dst:443 │                 │
        └────────┴─────────────────┘
   3    ┌──────┬──────┬────────────┐
        │IP HDR│TCHDR │    DATA    │  ← Kaynak/Hedef IP eklenir
        │SrcIP │      │            │
        │DstIP │      │            │
        └──────┴──────┴────────────┘
   2    ┌────┬──────┬──────┬──────┬────┐
        │FHDR│IP HDR│TCHDR │ DATA │FCS │  ← MAC adresleri + Hata
        │MAC │      │      │      │CRC │    kontrol (FCS/CRC) eklenir
        └────┴──────┴──────┴──────┴────┘
   1    01001011010110101001011010001...   ← Bitler iletilir
```

### 5.4 TCP/IP Modeli (4 Katman)

```
TCP/IP Modeli – OSI Karşılaştırması:

  OSI Modeli       TCP/IP Modeli
 ┌───────────┐    ┌──────────────┐
 │Application│    │              │
 │     7     │    │  Application │  HTTP, DNS, FTP, SMTP
 │Presentation│   │      4       │
 │     6     │    │              │
 │  Session  │    └──────────────┘
 │     5     │    ┌──────────────┐
 ├───────────┤    │  Transport   │  TCP, UDP
 │ Transport │    │      3       │
 │     4     │    └──────────────┘
 ├───────────┤    ┌──────────────┐
 │  Network  │    │   Internet   │  IPv4, IPv6, ICMP
 │     3     │    │      2       │
 ├───────────┤    └──────────────┘
 │ Data Link │    ┌──────────────┐
 │     2     │    │Network Access│  Ethernet, WLAN, ARP
 ├───────────┤    │      1       │
 │ Physical  │    │              │
 │     1     │    └──────────────┘
 └───────────┘
```

### 5.5 Sınav için Kritik Eşleştirme

| OSI Katmanı | TCP/IP Katmanı | Kullanılan Protokoller | Eklenen Adres |
|------------|---------------|----------------------|--------------|
| 7-6-5 Application/Presentation/Session | Application | HTTP, HTTPS, FTP, DNS, DHCP | — |
| 4 Transport | Transport | TCP, UDP | Port numarası |
| 3 Network | Internet | IPv4, IPv6, ICMP | IP adresi |
| 2 Data Link | Network Access | Ethernet, WLAN | MAC adresi |
| 1 Physical | Network Access | — | — (bit) |

---

## 6. Veri Kapsülleme

### 6.1 PDU (Protocol Data Unit) – Katman Bazlı Veri Adları

Her katmanda veriye farklı isim verilir:

```
PDU İsimleri:
┌─────────────┬──────────────┬──────────────────────────────┐
│   Katman    │   PDU Adı    │       İçeriği                │
├─────────────┼──────────────┼──────────────────────────────┤
│ 7-6-5       │   DATA       │ Uygulama verisi              │
│ 4 Transport │  SEGMENT     │ TCP/UDP başlığı + data       │
│ 3 Network   │   PACKET     │ IP başlığı + segment         │
│ 2 Data Link │   FRAME      │ MAC başlıkları + paket + FCS │
│ 1 Physical  │    BIT       │ 0 ve 1'ler                   │
└─────────────┴──────────────┴──────────────────────────────┘
```

### 6.2 Segmentasyon ve Multiplexing

**Segmentasyon (Bölme):** Büyük verinin küçük parçalara ayrılması

```
Segmentasyon Avantajları:
─────────────────────────────────────────────────────
 500 MB dosya → TEK PARÇA gönderilirse:
   • 1 bit bozulursa → 500 MB baştan gönderilir!

 500 MB dosya → PARÇALARA bölünürse:
   • 1 parça bozulursa → yalnızca o parça yeniden istenir
   • Birden fazla uygulama eş zamanlı çalışabilir
─────────────────────────────────────────────────────
```

**Multiplexing (Çoklama):** Birden fazla uygulama verisinin aynı hat üzerinden taşınması

```
Multiplexing Örneği:
─────────────────────────────────────────────────────
[Bilgisayar]
   ├── YouTube (port 5001 → 443)    ─┐
   ├── E-posta (port 5002 → 25)     ─┼→ [İnternet Hattı]
   ├── Web (port 5003 → 80)         ─┤
   └── Netflix (port 5004 → 443)    ─┘

Küçük paketler halinde birbirlerine araya giriyor!
─────────────────────────────────────────────────────
```

### 6.3 TCP: Güvenilir İletim Mekanizması

**TCP Başlığında Önemli Alanlar:**
- **Kaynak Port:** Göndericinin uygulaması (rastgele, örn. 5001)
- **Hedef Port:** Alıcının uygulaması (sabit, örn. 443 HTTPS)
- **Sıra Numarası (Sequence Number):** Paketin sırası
- **Onay Numarası (Acknowledgement Number):** Hangi paketi bekliyorum

```
TCP Güvenilir İletim Mekanizması:
─────────────────────────────────────────────────────
Gönderici          Ağ            Alıcı
   │                              │
   ├─── Paket 1 ──────────────────►│  (aldı)
   ├─── Paket 2 ────────► ✗ KAYIP │  (kayıp)
   ├─── Paket 3 ──────────────────►│  (aldı)
   │                              │
   │              ◄─── ACK: "Paket 2'yi gönder!" ───┤
   │                              │
   ├─── Paket 2 (yeniden) ────────►│  (aldı)
   │                              │
   │              ◄─── ACK: "Paket 4'ü gönder!" ────┤
─────────────────────────────────────────────────────
```

**TCP vs UDP Karşılaştırması:**

| Özellik | TCP | UDP |
|---------|-----|-----|
| Güvenilirlik | ✅ Evet (ACK) | ❌ Hayır |
| Sıra numarası | ✅ Var | ❌ Yok |
| Hız | Daha yavaş | **Daha hızlı** |
| Kullanım alanı | Web, e-posta, FTP | DNS, VoIP, video streaming |

---

## 7. Veri Erişimi

### 7.1 Aynı Ağdaki İletişim

```
Aynı Ağ İçi İletişim (192.168.1.0/24):
────────────────────────────────────────────────
[PC1: IP=192.168.1.5, MAC=AA:AA:AA:AA:AA:AA]
    │
[Switch]
    │
[PC2: IP=192.168.1.6, MAC=BB:BB:BB:BB:BB:BB]

Paket Yapısı:
┌──────────────────┬──────────┬──────────┐
│  Frame Header    │ IP Paketi│   DATA   │
│ Dst: BB:BB...    │ SrcIP:   │          │
│ Src: AA:AA...    │ 192.168.1│          │
│                  │ DstIP:   │          │
│                  │ 192.168.1│          │
└──────────────────┴──────────┴──────────┘

Hedef MAC = Hedef cihazın MAC adresi (ARP ile öğrenilir)
────────────────────────────────────────────────
```

**ARP (Address Resolution Protocol):** IP adresi bilindiğinde MAC adresini öğrenmek için kullanılır.
- `"Hey, 192.168.1.6 kimsin? MAC adresini ver!"` → Broadcast
- Hedef cihaz yanıt verir: `"Ben 192.168.1.6, MAC adresim BB:BB..."`

### 7.2 Farklı Ağa Giden İletişim

```
Farklı Ağa Giden Paket:
──────────────────────────────────────────────────────────
[PC1]          [Router1]         [Router2]         [WebSrv]
192.168.1.5    .1.1 | .2.1      .2.2 | .3.1       172.16.1.99
MAC: AAAA      R1-MAC1 R1-MAC2  R2-MAC1 R2-MAC2    MAC: DDDD

─── Segment 1 ──────────────────────────────────────────────
 Frame Hdr: [Dst=R1-MAC1 | Src=AAAA]
 IP Hdr:    [SrcIP=192.168.1.5 | DstIP=172.16.1.99]  ← SABİT
 DATA:      ...

─── Segment 2 (Router1'den sonra) ──────────────────────────
 Frame Hdr: [Dst=R2-MAC1 | Src=R1-MAC2]  ← DEĞİŞTİ
 IP Hdr:    [SrcIP=192.168.1.5 | DstIP=172.16.1.99]  ← SABİT
 DATA:      ...

─── Segment 3 (Router2'den sonra) ──────────────────────────
 Frame Hdr: [Dst=DDDD | Src=R2-MAC2]  ← DEĞİŞTİ
 IP Hdr:    [SrcIP=192.168.1.5 | DstIP=172.16.1.99]  ← SABİT
 DATA:      ...
──────────────────────────────────────────────────────────
KURAL: L3 IP adresi uçtan uca SABİT kalır.
       L2 MAC adresi her atlama (hop) noktasında DEĞİŞİR.
```

**Temel Kural:**
- **Aynı ağ** → Hedef MAC = Hedef cihazın MAC adresi
- **Farklı ağ** → Hedef MAC = **Default Gateway (Router)'ın MAC adresi**

### 7.3 Router'ın Çalışma Prensibi

```
Router'ın Yaptığı İşlemler:
─────────────────────────────────────────────────────
1. Paketi alır (Katman 1 - fiziksel)
2. Frame'i açar (Katman 2 - MAC kontrol)
3. IP başlığına bakar (Katman 3 - yol seçimi)
4. Routing tablosuna göre çıkış arayüzü seçilir
5. Yeni Frame başlığı yazar (yeni MAC adresleri)
6. Paketi iletir

Routing Tablosu Örneği:
┌──────────────────┬──────────────┬────────────┐
│ Hedef Network    │ Çıkış Arayüz │ Sonraki Hop│
├──────────────────┼──────────────┼────────────┤
│ 192.168.1.0/24   │ Gi0/0        │ Direkt     │
│ 192.168.2.0/24   │ Gi0/1        │ Direkt     │
│ 172.16.0.0/16    │ Gi0/1        │ 192.168.2.2│
└──────────────────┴──────────────┴────────────┘
─────────────────────────────────────────────────────
```

---

## 8. Özet ve Sınav Notları

### 8.1 Kritik Ezberlenmesi Gerekenler

```
OSI Katmanları ve Görevleri – Özet:
┌─────┬──────────────┬──────────────┬────────────────┐
│ No  │  Katman      │  PDU Adı     │  Kritik Bilgi  │
├─────┼──────────────┼──────────────┼────────────────┤
│  7  │ Application  │    Data      │ HTTP, HTTPS    │
│  6  │Presentation  │    Data      │ Şifreleme/Format│
│  5  │   Session    │    Data      │ Oturum takibi  │
│  4  │  Transport   │  Segment     │ Port no, TCP   │
│  3  │   Network    │   Packet     │ IP adresi      │
│  2  │  Data Link   │   Frame      │ MAC adresi     │
│  1  │   Physical   │    Bit       │ Kablo/Sinyal   │
└─────┴──────────────┴──────────────┴────────────────┘
```

### 8.2 Sıkça Sorulan Sınav Soruları

**S: MAC adresi kaç bit?** → 48 bit (12 hex karakter)
**S: IP adresi kaç bit?** → IPv4: 32 bit, IPv6: 128 bit
**S: Port numaraları hangi katmanda?** → Katman 4 (Transport)
**S: IP adresleri hangi katmanda?** → Katman 3 (Network)
**S: MAC adresleri hangi katmanda?** → Katman 2 (Data Link)
**S: Switch hangi katmanda çalışır?** → Katman 2
**S: Router hangi katmanda çalışır?** → Katman 3
**S: TCP/IP'nin kaç katmanı var?** → 4 katman
**S: OSI'nin kaç katmanı var?** → 7 katman
**S: HTTPS kaçıncı portu kullanır?** → 443
**S: HTTP kaçıncı portu kullanır?** → 80
**S: Farklı ağa giderken hedef MAC ne olur?** → Default Gateway (Router)'ın MAC'i
**S: Paket yol boyunca ilerlerken IP adresi değişir mi?** → Hayır, sabit kalır
**S: Paket yol boyunca ilerlerken MAC adresi değişir mi?** → Evet, her hop'ta değişir

### 8.3 Wireshark ile Pratik Doğrulama

Wireshark (wireshark.org) ile gerçek trafiği analiz edebilirsiniz:
- Her paketin 2. katman (MAC), 3. katman (IP) ve 4. katman (Port) bilgilerini görebilirsiniz
- Kendi IP adresinizi `whatismyipaddress.com` adresinden görebilirsiniz
- `ping` komutu ile ICMP paketlerini test edebilirsiniz

---

---
