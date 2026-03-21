# CCNA1 - Modül 14: Taşıma Katmanı (Transport Layer – L4)
> **Kaynak:** Cisco Networking Academy — Introduction to Networks v7.0 (ITN)  


---

## 📋 Modül Hedefleri

| Konu | Hedef |
|------|-------|
| Verilerin Taşınması | Taşıma katmanının uçtan uca iletişimdeki rolünü açıklamak |
| TCP'ye Genel Bakış | TCP özelliklerini açıklamak |
| UDP'ye Genel Bakış | UDP özelliklerini açıklamak |
| Port Numaraları | TCP ve UDP'nin port numaralarını nasıl kullandığını açıklamak |
| TCP İletişim Süreci | TCP oturum kurma ve sonlandırma süreçlerini açıklamak |
| Güvenilirlik ve Akış Kontrolü | TCP'nin güvenilir iletim ve akış kontrolünü nasıl sağladığını açıklamak |
| UDP İletişimi | UDP haberleşme sürecini açıklamak |

---

## Hatırlatma: Katmanlı Paket Yapısı

```
┌─────────────────────────────────────────────────────────────────────┐
│                       UYGULAMA KATMANI DATAsı                      │
├──────────────┬──────────────────────────────────────────────────────┤
│  UDP (8 B)   │               UDP Datası                             │
├──────────────┼──────────────────────────────────────────────────────┤
│  TCP (20 B)  │               TCP Datası                             │
├──────────────┼──────────────────────────────────────────────────────┤
│  IP  (20 B)  │               IP Datası                              │
├──────────────┼─────────────────────────────────────────────┬────────┤
│ Ethernet(18B)│          46 – 1500 Byte Ethernet Datası     │  CRC   │
└──────────────┴─────────────────────────────────────────────┴────────┘
```

---

## 14.1 Verilerin Taşınması

### Taşıma Katmanının Rolü

Taşıma katmanı (Layer 4), farklı host'larda çalışan **uygulamalar arasındaki mantıksal iletişimi** sağlar. Uygulama katmanı ile ağ iletiminden sorumlu alt katmanlar arasında köprü görevi görür.

```
┌──────────────────────────────────────────────────────────────┐
│                  NEDEN TAŞIMA KATMANI GEREKİR?               │
│                                                              │
│  PC'de aynı anda çalışan birden fazla uygulama var:          │
│                                                              │
│   WhatsApp → port 50001                                      │
│   Spotify  → port 50002     Paket geldi!                     │
│   Firefox  → port 50003     Hangi uygulamaya gidecek?        │
│   Netflix  → port 50004     ──────────────────────────────   │
│                             CEVAP: Port numarasına bakılır!  │
└──────────────────────────────────────────────────────────────┘
```

### Taşıma Katmanının Sorumlulukları

**Segmentlere Bölme (Segmentation):** Datayı küçük parçalara böler, her parçaya başlık bilgisi ekler.

**Yeniden Birleştirme (Reassembly):** Hedefe ulaşan segmentler sıra numaralarına göre birleştirilerek orijinal data oluşturulur.

**Uygulama Tanımlama:** Port numaraları sayesinde paketin hangi uygulamaya iletileceğini belirler.

**Çoğullama (Multiplexing):** Birden fazla uygulamanın aynı ağ üzerinden eş zamanlı haberleşmesini sağlar.

```
Multiplexing örneği:
PC'den aynı anda çıkan trafik:
  Port 50001 → WhatsApp sunucusu
  Port 50002 → Spotify sunucusu
  Port 50003 → Google (HTTPS 443)
  Port 50004 → Netflix sunucusu
  ──────────────────────────────
  Hepsi aynı internet hattından geçiyor!
```

### TCP ve UDP: Doğru Protokolü Seçmek

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PROTOKOL SEÇİMİ                            │
│                                                                     │
│          UDP                             TCP                        │
│  ┌──────────────────┐          ┌──────────────────────┐            │
│  │ Hızlı            │          │ Güvenilir            │            │
│  │ Az ek yük        │          │ Sıralı iletim        │            │
│  │ Onay yok         │          │ Kayıp paketi tekrar  │            │
│  │ Sıra numarası yok│          │ gönderir             │            │
│  │ Bağlantısız      │          │ Bağlantı tabanlı     │            │
│  └──────────────────┘          └──────────────────────┘            │
│  VoIP, DNS, DHCP,              HTTP/HTTPS, FTP, SMTP,              │
│  SNMP, TFTP, video konf.       SSH, e-posta                        │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 14.2 TCP'ye Genel Bakış

### TCP Özellikleri

TCP (Transmission Control Protocol), **güvenilir** ve **sıralı** iletim sağlayan bağlantı odaklı bir protokoldür.

**Oturum Kurar (Connection-Oriented):** Veri göndermeden önce istemci ve sunucu arasında 3-yollu el sıkışmayla oturum kurulur.

**Güvenilir Teslimat (Reliability):** Her segmente sıra numarası verilir. Kayıp segment tespit edilip yeniden iletilir.

**Sıralı Teslimat (Same-Order Delivery):** Yanlış sırada gelen segmentler sıra numarasına bakılarak doğru sıraya dizilir.

**Akış Kontrolü (Flow Control):** Alıcının işleyebileceği hız kadar veri gönderilir; bant genişliği dinamik olarak ayarlanır.

### TCP Başlık Yapısı (20 Byte)

```
┌────────────────────────┬────────────────────────────────────────┐
│   Kaynak Port (16 bit) │      Hedef Port (16 bit)               │
├────────────────────────┴────────────────────────────────────────┤
│                   Sıra Numarası / Seq. Number (32 bit)          │
├─────────────────────────────────────────────────────────────────┤
│               Onay Numarası / Ack. Number (32 bit)              │
├──────────┬──────────────┬──────────────┬────────────────────────┤
│Hdr Len(4)│ Reserved (6) │ Flags (6 bit)│   Window Size (16 bit) │
├──────────┴──────────────┴──────────────┴────────────────────────┤
│              Checksum (16 bit)          │  Urgent (16 bit)       │
├─────────────────────────────────────────────────────────────────┤
│                     Options (0 veya 32 bit)                     │
├─────────────────────────────────────────────────────────────────┤
│                  Uygulama Katmanı Datası (değişken)              │
└─────────────────────────────────────────────────────────────────┘
```

### TCP Başlık Alanları

| Alan | Boyut | Açıklama |
|------|-------|----------|
| Kaynak Port | 16 bit | Kaynak uygulamanın port numarası |
| Hedef Port | 16 bit | Hedef uygulamanın port numarası |
| Sıra Numarası | 32 bit | Segmentin byte numarası (paket takibi için) |
| Onay Numarası | 32 bit | Karşıdan beklenen bir sonraki byte numarası |
| Başlık Uzunluğu | 4 bit | TCP başlığının uzunluğu |
| Kontrol Bitleri | 6 bit | SYN, ACK, FIN, RST, PSH, URG bayrakları |
| Window Size | 16 bit | Onay beklemeden gönderilebilecek byte sayısı |
| Checksum | 16 bit | Hata kontrolü |
| Urgent | 16 bit | Acil verinin önemini belirtir |

### TCP Kullanan Uygulamalar

| Protokol | Port | Açıklama |
|----------|------|----------|
| FTP (Data) | TCP 20 | Dosya transferi (veri) |
| FTP (Control) | TCP 21 | Dosya transferi (kontrol) |
| SSH | TCP 22 | Güvenli uzaktan erişim |
| Telnet | TCP 23 | Uzaktan komut satırı erişimi |
| SMTP | TCP 25 | Mail gönderme |
| HTTP | TCP 80 | Web sayfası |
| HTTPS | TCP 443 | Şifreli web sayfası |

---

## 14.3 UDP'ye Genel Bakış

### UDP Özellikleri

UDP (User Datagram Protocol), **hızlı** ve **düşük ek yüklü** iletim için kullanılan bağlantısız protokoldür.

- Sıra numarası yok → segmentler yeniden sıraya dizilmez
- Onay mekanizması yok → kayıp paket yeniden gönderilmez
- Bağlantı kurulmaz → connectionless
- Akış kontrolü yok

> UDP bu özellikleriyle bir **"Best Effort Delivery"** (en iyi çaba teslimatı) protokolüdür.

### UDP Başlık Yapısı (8 Byte)

```
┌──────────────────────────┬──────────────────────────┐
│   Kaynak Port (16 bit)   │   Hedef Port (16 bit)    │  8
├──────────────────────────┼──────────────────────────┤ Byte
│    Uzunluk (16 bit)      │   Checksum (16 bit)      │
├──────────────────────────┴──────────────────────────┤
│          Uygulama Katmanı Datası (değişken)          │
└─────────────────────────────────────────────────────┘
```

### UDP Başlık Alanları

| Alan | Boyut | Açıklama |
|------|-------|----------|
| Kaynak Port | 16 bit | Kaynak uygulamanın port numarası |
| Hedef Port | 16 bit | Hedef uygulamanın port numarası |
| Uzunluk | 16 bit | Datagram'ın toplam uzunluğu |
| Checksum | 16 bit | Hata kontrolü |

### UDP Kullanan Uygulamalar

| Protokol | Port | Kullanım Nedeni |
|----------|------|-----------------|
| DNS | UDP 53 | Tek soru–tek cevap (hızlı yeterli) |
| DHCP | UDP 67/68 | Tek soru–tek cevap |
| TFTP | UDP 69 | Basit dosya transferi |
| SNMP | UDP 161 | Ağ cihazı yönetim sorguları |
| VoIP / Video Konf. | Dinamik | Gerçek zamanlı iletim |

> **Gerçek zamanlı trafik neden UDP kullanır?**  
> Ses veya video konuşmasında kaybolan bir paket için "yeniden gönder" demek anlamsızdır. O an geçmiştir. Hız önceliklidir.

---

## TCP vs UDP Karşılaştırması

```
┌──────────────────────────────┬──────────────────────────────────┐
│             TCP              │              UDP                 │
├──────────────────────────────┼──────────────────────────────────┤
│ Bağlantı odaklı              │ Bağlantısız (connectionless)     │
│ 3-yollu el sıkışma           │ El sıkışma yok                   │
│ Sıra numarası var            │ Sıra numarası yok                │
│ Onay (ACK) mekanizması var   │ Onay yok                         │
│ Kayıp paket yeniden iletilir │ Kayıp paket yeniden iletilmez    │
│ Akış kontrolü var            │ Akış kontrolü yok                │
│ 20 byte başlık               │ 8 byte başlık                    │
│ Daha yavaş, daha güvenilir   │ Daha hızlı, daha az güvenilir    │
│ HTTP, FTP, SMTP, SSH         │ DNS, DHCP, VoIP, SNMP, TFTP      │
└──────────────────────────────┴──────────────────────────────────┘
```

---

## 14.4 Port Numaraları

### Port Numarası Nedir?

Port numarası 16 bittir ve **0 ile 65.535** arasında değer alır. IP adresi cihazı, port numarası ise o cihazdaki **uygulamayı** tanımlar.

```
Soket (Socket) = IP Adresi + Port Numarası
Örnek: 192.168.1.5:49152

Socket Pair (Soket Çifti):
  Kaynak: 192.168.1.5:49152   ──►  Hedef: 216.58.213.46:443
```

### Port Numarası Grupları (IANA)

```
┌─────────────────────────────────────────────────────────────────────┐
│                     PORT NUMARASI HİYERARŞİSİ                      │
│                                                                     │
│  0 – 1023       Well-Known Ports (İyi Bilinen Portlar)             │
│                 Herkes tarafından tanınan uygulamalar               │
│                 Örnek: HTTP=80, HTTPS=443, FTP=21, SSH=22           │
│                                                                     │
│  1024 – 49151   Registered Ports (Kayıtlı Portlar)                 │
│                 IANA'ya kayıtlı ticari uygulamalar                  │
│                 Örnek: RDP=3389, MySQL=3306, RADIUS=1812            │
│                                                                     │
│  49152 – 65535  Dynamic / Private Ports (Dinamik Portlar)          │
│                 İstemci uygulamaları tarafından geçici olarak       │
│                 kullanılır (kaynak port numarası)                   │
└─────────────────────────────────────────────────────────────────────┘
```

### Yaygın Port Numaraları (Ezberlenecek!)

| Port | Protokol | Açıklama |
|------|----------|----------|
| 20 | TCP | FTP – Veri |
| 21 | TCP | FTP – Kontrol |
| 22 | TCP | SSH – Güvenli uzaktan erişim |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP – Mail gönderme |
| 53 | UDP/TCP | DNS |
| 67 | UDP | DHCP – Sunucu |
| 68 | UDP | DHCP – İstemci |
| 69 | UDP | TFTP |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 – Mail alma |
| 143 | TCP | IMAP – Mail alma |
| 161 | UDP | SNMP |
| 443 | TCP | HTTPS – Güvenli web |
| 1812 | UDP | RADIUS – Kimlik doğrulama |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP – Uzak masaüstü |

### Birden Fazla Sekme Sorunu: Kaynak Port Neden Önemli?

```
┌──────────────────────────────────────────────────────────────────┐
│  Tarayıcıda 2 sekme aynı web sitesine bağlı:                    │
│                                                                  │
│  Sekme 1: Kaynak port 50001 → Hedef 443 (google.com)           │
│  Sekme 2: Kaynak port 50002 → Hedef 443 (google.com)           │
│                                                                  │
│  Sunucudan cevap geldi:                                          │
│    ├── Hedef port 50001 → Sekme 1'e iletilir                   │
│    └── Hedef port 50002 → Sekme 2'ye iletilir                  │
│                                                                  │
│  Kaynak port = Dönüş adresi                                     │
└──────────────────────────────────────────────────────────────────┘
```

### netstat Komutu

Bilgisayardaki aktif bağlantıları görüntülemek için kullanılır.

```
C:\> netstat -n        → Bütün bağlantıları sayısal göster
C:\> netstat -a        → TCP + UDP bağlantılarını göster
C:\> netstat -b        → Hangi uygulama hangi portu kullanıyor (Admin gerekli)
C:\> netstat -o        → Proses ID'si ile birlikte göster
C:\> netstat -ano      → Hepsini birlikte göster (en kullanışlı)

Örnek çıktı:
Proto  Local Address          Foreign Address        State
TCP    192.168.1.5:50573       216.58.213.46:443      ESTABLISHED
TCP    192.168.1.5:50574       52.114.132.46:443      ESTABLISHED
UDP    0.0.0.0:5353            *:*

netstat durumları:
  ESTABLISHED  → Aktif bağlantı (data transfer)
  SYN_SENT     → 3-yollu el sıkışma başlatıldı
  CLOSE_WAIT   → FIN paketi gönderildi, sonlandırma bekleniyor
  LISTEN       → Sunucu bu porttan istek kabul ediyor
```

---

## 14.5 TCP İletişim Süreci

### TCP Haberleşmesinin 3 Aşaması

```
┌─────────────────────────────────────────────────────────────────┐
│                    TCP HABERLEŞME SÜRECİ                        │
│                                                                 │
│  1. BAĞLANTI KURULUM AŞAMASI  (3-Yollu El Sıkışma)            │
│  2. DATA TRANSFER AŞAMASI                                       │
│  3. BAĞLANTI SONLANDIRMA AŞAMASI (4 Paket)                     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 3-Yollu El Sıkışma (Three-Way Handshake)

```
İstemci (Client)                              Sunucu (Server)
      │                                              │
      │  1  SYN  (SYN=1, ACK=0)                    │
      │  Seq=100, Src Port=50000, Dst Port=80       │
      │ ──────────────────────────────────────────► │  LISTEN
      │                                              │
      │  2  SYN + ACK  (SYN=1, ACK=1)             │
      │  Seq=300, Ack=101                           │
      │  Src Port=80, Dst Port=50000                │
      │ ◄──────────────────────────────────────────  │
      │                                              │
      │  3  ACK  (SYN=0, ACK=1)                    │
      │  Seq=101, Ack=301                           │
      │  Src Port=50000, Dst Port=80                │
      │ ──────────────────────────────────────────► │
      │                                              │
      │         *** ESTABLISHED ***                  │
      │  ──────── DATA TRANSFER ────────►            │
```

**Adım 1 – SYN:** İstemci bağlantı isteği gönderir. SYN=1, rastgele başlangıç sıra numarası (ISN) belirlenir.

**Adım 2 – SYN+ACK:** Sunucu hem istemcinin SYN'ini onaylar (ACK=istemcinin Seq+1), hem kendi SYN'ini gönderir.

**Adım 3 – ACK:** İstemci sunucunun SYN'ini onaylar. Oturum kuruldu.

> Bu 3 pakette hiç data taşınmaz. Sadece oturum parametreleri belirlenir.

### TCP Kontrol Bitleri (Bayraklar / Flags)

| Flag | Anlamı | Kullanım |
|------|--------|----------|
| **SYN** | Synchronize — bağlantı isteği | 3-yollu el sıkışmada |
| **ACK** | Acknowledgement — onay | Her paketin onaylanması |
| **FIN** | Finish — bitiş | Oturum sonlandırma |
| **RST** | Reset — sıfırla | Hata veya zorla bağlantı kesme |
| **PSH** | Push — hemen ilet | Datayı bekletmeden uygulamaya gönder |
| **URG** | Urgent — acil | Acil veri işaretleme |

### TCP Bağlantı Sonlandırma (4 Paket)

```
İstemci (A)                                    Sunucu (B)
     │                                               │
     │  1  FIN  ──────────────────────────────────► │
     │                                               │
     │  2  ACK  ◄────────────────────────────────── │
     │                                               │
     │  3  FIN  ◄────────────────────────────────── │
     │                                               │
     │  4  ACK  ──────────────────────────────────► │
     │                                               │
     │           *** OTURUM SONA ERDİ ***            │
```

---

## 14.6 Güvenilirlik ve Akış Kontrolü

### Sıra Numaraları ve Byte Takibi

TCP'de sıra numarası aslında **byte numarasıdır**, paket numarası değildir.

```
Örnek — 3000 byte'lık dosya gönderimi:

Paket 1: Seq=1,    1–1460 byte arası    gönderildi  →
Cevap:   Ack=1461  "1460 byte'a kadar aldım"        ←

Paket 2: Seq=1461, 1461–2920 byte arası gönderildi  →
Cevap:   Ack=2921  "2920 byte'a kadar aldım"        ←

Paket 3: Seq=2921, 2921–3000 byte arası gönderildi  →
Cevap:   Ack=3001  "Tamamdır!"                      ←
```

### Kayıp Paket ve Yeniden İletim

```
Standart yeniden iletim (eski yöntem):
  Seg 1  -->  geldi
  Seg 2  -->  geldi
  Seg 3  -->  KAYIP!
  Seg 4  -->  geldi
  Seg 5  -->  geldi
               ACK=3 (bana 3'ten itibaren yolla!)
  Seg 3,4,5 yeniden gönderilir (4 ve 5 gereksiz tekrar)
```

```
SACK ile gelişmiş yöntem (günümüzde kullanılır):
  Seg 1  -->  geldi
  Seg 2  -->  geldi
  Seg 3  -->  KAYIP!
  Seg 4  -->  geldi
  Seg 5  -->  geldi
               ACK=3, SACK=4-5 (Sadece 3'ü gönder, 4-5 tamam)
  Seg 3 yeniden gönderilir (sadece eksik olan)
               ACK=6 (devam et)
```

> **SACK (Selective Acknowledgement):** Alınan aralıkları seçerek onaylayan gelişmiş mekanizma. Sadece gerçekten kayıp olan paketler yeniden gönderilir.

### Akış Kontrolü — Window Size

**Window Size** (Pencere Boyutu): Onay beklemeden gönderilecek maksimum byte sayısını belirler.

```
┌─────────────────────────────────────────────────────────────────────┐
│                     WINDOW SIZE MEKANİZMASI                        │
│                                                                     │
│  Sunucu buffer boş    → Window Size büyük  → Hızlı gönder         │
│  Sunucu buffer dolmaya başladı → Window Size küçük → Yavaş gönder │
│  Sunucu buffer tam    → Window Size = 0    → Dur, bekle!           │
│                                                                     │
│  Örnek akış:                                                        │
│    Ack: 10.001, Window: 10.000   → 10 paket gönder                 │
│    Ack: 20.001, Window:  5.000   → 5 paket gönder (tıkanıklık)    │
│    Ack: 25.001, Window:  1.000   → 1 paket gönder (buffer %90)    │
│    Ack: 26.001, Window:      0   → Dur! Buffer doldu              │
│    Ack: 26.001, Window:  5.000   → Buffer boşaldı, devam et       │
└─────────────────────────────────────────────────────────────────────┘
```

### Sliding Window (Kayan Pencere)

Sunucu onayı gönderirken bir sonraki pencereyi de bildirir. Bu sayede gönderici hiç durmadan devam edebilir.

```
Pencere=10.000, MSS=1460:

Paket 1 (byte 1-1460)      ──────────────────────────────►
Paket 2 (byte 1461-2920)   ──────────────────────────►
          ◄── ACK=2921, Window=10.000 (araya girdi!)
Paket 3 (byte 2921-4380)   ──────────────────────►
Paket 4 (byte 4381-5840)   ────────────────────►
          ◄── ACK=5841, Window=10.000 (araya girdi!)
  Hiç durmadan devam...
```

### Maksimum Segment Boyutu (MSS)

```
Ethernet MTU    = 1500 byte
  – IP Başlığı  =   20 byte
  – TCP Başlığı =   20 byte
  ──────────────────────────
  MSS           = 1460 byte   ← TCP'nin taşıyabileceği max. data
```

> VPN kullandığınızda 2. bir IP başlığı eklendiği için MSS = 1440 byte olabilir.

### Tıkanıklıktan Kaçınma (Congestion Avoidance)

Onay gelmezse gönderici: paket akışını yavaşlatır, daha küçük pencere kullanır, daha sonra yeniden artırır.

---

## EK: SYN Attack (DoS Saldırısı)

```
┌─────────────────────────────────────────────────────────────────────┐
│                       SYN ATTACK NASIL ÇALIŞIR?                    │
│                                                                     │
│  Saldırgan → SYN  ─────────────────────────────► Sunucu           │
│  Saldırgan → SYN  ─────────────────────────────► Sunucu           │
│  (1000 kez tekrar)                                                  │
│                                                                     │
│  Sunucu her SYN için: Port aç, SYN+ACK gönder, ACK bekle          │
│  Ama saldırgan 3. ACK'ı hiç göndermez!                             │
│                                                                     │
│  Sonuç: 1000 yarım oturum açık → Tüm sessionlar dolu               │
│         Gerçek kullanıcı bağlanamıyor → DoS!                        │
│                                                                     │
│  Bu saldırıya SYN FLOOD veya SYN ATTACK denir.                     │
│  Birden fazla IP'den yapılırsa → DDoS (Distributed DoS)            │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 14.7 UDP İletişimi

### UDP Haberleşme Akışı

```
          İstemci (DNS Sorgusu)              Sunucu (DNS)
                │                                │
                │   IP Hdr + UDP Hdr             │
                │   + "www.google.com IP?"       │
                │ ─────────────────────────────► │
                │                                │
                │   IP Hdr + UDP Hdr             │
                │   + "google.com = 1.2.3.4"     │
                │ ◄─────────────────────────────  │

TCP ile yapılsaydı: 3+2+4 = 9+ paket
UDP ile yapıldı:    2 paket — çok daha hızlı!
```

### UDP'de Sıra Sorunu

```
Gönderilen: Datagram 1, 2, 3, 4, 5, 6
Gelen:      Datagram 1, 2, 6, 5, 4     (yanlış sıra, 3 kayıp)

UDP'nin tepkisi: Geldiği gibi iletilir.
Ses uygulamasında bu "cızırtı" veya "boşluk" olarak duyulur.
```

---

## EK: NAT ve Port Yönlendirme

### Birden Fazla Cihazın Tek IP'den Çıkması (NAT + PAT)

```
┌──────────────────────────────────────────────────────────────────┐
│                       NAT TABLOSU                               │
│                                                                  │
│  İç IP:Port            Dış IP:Port         Hedef                │
│  192.168.1.2:49152  → 200.100.10.1:1024  → Google:443           │
│  192.168.1.3:49152  → 200.100.10.1:1025  → Yahoo:443            │
│  192.168.1.4:49153  → 200.100.10.1:1026  → YouTube:443          │
│                                                                  │
│  Gelen cevap 200.100.10.1:1025 → 192.168.1.3:49152'ye yönlendir│
└──────────────────────────────────────────────────────────────────┘
```

### Port Yönlendirme (Port Forwarding)

Dışarıdan içerideki sunucuya erişim sağlamak için:

```
İnternetten gelen istek:
  166.124.130.26:10443
        ↓  (modem port yönlendirme kuralı)
  192.168.0.186:443  (iç ağdaki web sunucusu)
```

---

## 🧠 Aklında Kalsın

1. **TCP = güvenilir, UDP = hızlı** — Eksiksiz iletim için TCP, hız için UDP kullanılır.
2. **Segment** = TCP'nin veri birimi. **Datagram** = UDP'nin veri birimi.
3. **Kaynak port** = Gelen cevabın doğru uygulamaya gitmesini sağlar (dönüş adresi).
4. **3-yollu el sıkışma** = SYN → SYN+ACK → ACK (3 paket, 0 data).
5. **Oturum sonlandırma** = FIN → ACK → FIN → ACK (4 paket).
6. **Window Size = 0** demek "dur, bekle" demektir.
7. **MSS = 1460 byte** (Ethernet MTU 1500 – IP 20 – TCP 20).
8. **SYN Attack** = Sunucunun tüm oturumlarını yarım bırakarak doldurma saldırısı.
9. **Port numarası = 0–65535** (16 bit). Dinamik portlar: 49152–65535.
10. **netstat -ano** komutu ile hangi uygulamanın hangi portu kullandığını görebilirsiniz.

---

## 📝 Modül Özeti

```
TAŞIMA KATMANI (L4)
├── TCP (Transmission Control Protocol)
│   ├── Connection-Oriented (Bağlantı tabanlı)
│   ├── 3-Yollu El Sıkışma: SYN → SYN+ACK → ACK
│   ├── Sıra Numarası (Sequence Number) — byte takibi
│   ├── Onay Numarası (Acknowledgement Number)
│   ├── Yeniden İletim (Retransmission)
│   ├── SACK (Selective Acknowledgement)
│   ├── Window Size — akış kontrolü
│   ├── Sliding Window — kesintisiz iletim
│   ├── MSS = 1460 byte
│   └── Bağlantı Sonlandırma: FIN → ACK → FIN → ACK
│
├── UDP (User Datagram Protocol)
│   ├── Connectionless (Bağlantısız)
│   ├── 8 byte başlık (port, uzunluk, checksum)
│   ├── Sıra numarası yok
│   ├── Onay yok
│   └── Kullanım: DNS, DHCP, VoIP, SNMP, TFTP
│
└── PORT NUMARALARI
    ├── 0–1023:       Well-Known (SSH=22, HTTP=80, HTTPS=443)
    ├── 1024–49151:   Registered (RDP=3389, MySQL=3306)
    └── 49152–65535:  Dynamic / Client ports
```

---

## 📖 Yeni Terimler Sözlüğü

| Terim | Açıklama |
|-------|----------|
| Segment | TCP'nin veri birimi |
| Datagram | UDP'nin veri birimi |
| Multiplexing | Birden fazla uygulamanın aynı anda çalışması |
| Three-Way Handshake | 3-yollu el sıkışma |
| SYN | Synchronize — bağlantı başlatma bayrağı |
| ACK | Acknowledgement — onay bayrağı |
| FIN | Finish — bağlantı sonlandırma bayrağı |
| RST | Reset — bağlantı sıfırlama |
| ISN | Initial Sequence Number — başlangıç sıra numarası |
| SACK | Selective Acknowledgement — seçici onay |
| Sliding Window | Kayan pencere — kesintisiz iletim |
| MSS | Maximum Segment Size = 1460 byte |
| MTU | Maximum Transmission Unit = 1500 byte (Ethernet) |
| Socket | IP Adresi + Port Numarası kombinasyonu |
| Socket Pair | Kaynak socket + Hedef socket çifti |
| Flow Control | Akış kontrolü |
| Congestion Avoidance | Tıkanıklıktan kaçınma |
| netstat | Aktif ağ bağlantılarını gösteren komut |

---

*CCNA1 Module 14 Handbook — agyoneticileri.org*
