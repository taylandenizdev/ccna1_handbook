# CCNA1 – Modül 15: Uygulama Katmanı
> **Application Layer**  
> CCNA1 · Introduction to Networks v7.0 (ITN)  
> Gökhan AKIN – CCIE | Ozan BÜK – CCIE

---

## 📋 Modül Hedefleri

| Konu | Amaç |
|------|-------|
| Uygulama, Sunum ve Oturum Katmanları | Bu üç katmanın birlikte nasıl çalıştığını açıklamak |
| Eşler Arası (Peer-to-Peer) | P2P ağlarda uç cihaz uygulamalarının nasıl çalıştığını açıklamak |
| Web ve E-Posta Protokolleri | HTTP, HTTPS, SMTP, POP, IMAP'in nasıl çalıştığını açıklamak |
| IP Adres Servisleri | DNS ve DHCP'nin nasıl çalıştığını açıklamak |
| Dosya Paylaşım Servisleri | FTP, TFTP ve SMB protokollerini açıklamak |

---

## 15.1 Uygulama, Sunum ve Oturum Katmanları

### OSI Katman Özeti

```
OSI Modeli (7 Katman)                TCP/IP Modeli
─────────────────────                ─────────────
7. APPLICATION  ┐                    ┐
6. PRESENTATION ├── Uygulama         │ Application
5. SESSION      ┘   Katmanları       ┘
─────────────────────────────────────────────────
4. TRANSPORT    ────────────────────── Transport
─────────────────────────────────────────────────
3. NETWORK      ┐                    ┐
2. DATA LINK    ├── Veri Akış        │ Internet /
1. PHYSICAL     ┘   Katmanları       ┘ Network Access
```

> 💡 Ağ mühendislerinin temel ilgi alanı **alt 4 katmandır** (Transport, Network, Data Link, Physical). Uygulama katmanları yazılım geliştiricileri ve sistem yöneticileri tarafından da ele alınır.

---

### 7. Katman: Uygulama Katmanı (Application Layer)

**Görevi:** Son kullanıcıya ağ haberleşmesini başlatacağı arayüzü sağlamak.

```
Kullanıcı
    │  (tarayıcıya www.google.com yazar)
    ▼
[ Uygulama Katmanı – L7 ]
    │  Chrome, Firefox, Outlook, WhatsApp...
    │  → Datayı oluşturur ve alt katmanlara iletir
    ▼
[ Transport – L4 ] → [ Network – L3 ] → [ Data Link – L2 ] → [ Physical – L1 ]
```

En yaygın uygulama katmanı protokolleri: **HTTP, FTP, TFTP, IMAP, DNS, DHCP, SMTP, Telnet, SSH**

---

### 6. Katman: Sunum Katmanı (Presentation Layer)

**Görevi:** Datanın iletim biçimini belirlemek.

**3 temel işlev:**

```
1. DATA FORMATTING (Biçimlendirme)
   Veriyi hedef cihazın anlayacağı formata çevirir.
   Örnekler: text/html, JPEG, MP4, GIF, PNG, MOV, MKV

2. COMPRESSION (Sıkıştırma)
   Veriyi sıkıştırarak daha az bant genişliği kullanır.
   Örnek: gzip formatında web sayfası gönderimi

3. ENCRYPTION (Şifreleme)
   Veriyi şifreleyerek güvenli iletim sağlar.
   Örnek: HTTPS/TLS şifrelemesi
   Not: Pratikte TLS, transport katmanının üstünde çalışır.
```

---

### 5. Katman: Oturum Katmanı (Session Layer)

**Görevi:** İstemci ile sunucu arasındaki oturumu takip etmek ve yönetmek.

```
Oturum Katmanının yaptıkları:
  ✅ Diyalogları başlatır ve sürdürür
  ✅ Oturum süresince veri iletimini takip eder
  ✅ Zaman aşımına uğrayan oturumları sonlandırır
  ✅ Kesintiye uğrayan oturumları yeniden başlatır

Gerçek hayat örnekleri:
  • Facebook login → uzun süre hareketsizlik → oturum zaman aşımı
  • Twitter'da yeni sekme → aynı oturumun devam etmesi
  • WhatsApp'ta "...yazıyor" göstergesi → oturum takibi
  • Dosya transferinde ilerleme takibi
```

---

### Katmanların Birlikte Çalışması

```
Örnek: www.google.com açmak

L7 Uygulama:   "Google'ı aç" talebi oluştur
                        │
L6 Sunum:      Veriyi HTML/gzip formatında hazırla
                        │
L5 Oturum:     Oturum başlat, takip et
                        │
L4 Transport:  TCP → 3-way handshake → veriyi segmentlere böl
                        │
L3 Network:    IP adresi ekle, yönlendir
                        │
L2 Data Link:  MAC adresi ekle, Ethernet frame oluştur
                        │
L1 Physical:   Bitleri kabloya/havaya gönder
```

---

### Önemli Uygulama Katmanı Protokolleri (Port Tablosu)

| Protokol | Port | Transport | Açıklama |
|----------|------|-----------|----------|
| DNS | UDP/TCP 53 | UDP (sorgu), TCP (zone transfer) | Alan adı → IP çözümlemesi |
| DHCP | UDP 67 (sunucu), 68 (istemci) | UDP | Otomatik IP dağıtımı |
| HTTP | TCP 80, 8080 | TCP | Web gezintisi |
| HTTPS | TCP 443 | TCP | Şifreli web gezintisi |
| SMTP | TCP 25, 587, 465 | TCP | Mail gönderme |
| POP3 | TCP 110, 995 (güvenli) | TCP | Mail indirme (sunucudan siler) |
| IMAP | TCP 143, 993 (güvenli) | TCP | Mail senkronizasyonu |
| FTP | TCP 21 (kontrol), 20 (data) | TCP | Dosya transferi |
| TFTP | UDP 69 | UDP | Basit dosya transferi |
| Telnet | TCP 23 | TCP | Uzak komut satırı (şifresiz) |
| SSH | TCP 22 | TCP | Uzak komut satırı (şifreli) |
| SMB | TCP 445 | TCP | Dosya/yazıcı paylaşımı |

---

## 15.2 Eşler Arası (Peer-to-Peer)

### İstemci-Sunucu Modeli (Client/Server Model)

```
      İNTERNET
          │
    ┌─────────────┐
    │   SUNUCU    │  ← Kaynakları depolar ve hizmet verir
    │  (Server)   │
    └─────────────┘
          │  Network
    ┌─────────────┐
    │  İSTEMCİ   │  ← Hizmet talep eder
    │  (Client)   │     Download ↓ / Upload ↑
    └─────────────┘
```

**Temel kurallar:**
- Bilgi talep eden → **İstemci (Client)**
- Talebe cevap veren → **Sunucu (Server)**
- Sunucuda kaynaklar depolanır
- Download hızı genelde upload'dan yüksektir (son kullanıcı daha çok tüketir)

---

### Eşler Arası Ağlar (Peer-to-Peer / P2P)

```
P2P AĞINDA:
  Her cihaz hem istemci hem sunucu rolünü alabilir

  [PC-A] ←──────────────→ [PC-B]
  Dosya Sunucusu           Print İstemcisi
  Print İstemcisi          Dosya İstemcisi
                      │
                  [Yazıcı]
                (USB ile B'ye bağlı)
                  Print Sunucusu

Avantajlar:
  ✅ Özel sunucu gerektirmez
  ✅ Küçük ofisler için uygun
  ✅ Kurulumu kolay

Dezavantajlar:
  ❌ Merkezi yönetim yok
  ❌ Kullanıcı sayısı arttıkça güvenlik sorunları artar
  ❌ Kimin neye eriştiğini takip etmek zor
```

---

### Yaygın P2P Uygulamaları

```
P2P UYGULAMA MİMARİSİ:

[İndeksleme Sunucusu]
  │  Hangi dosya kimde? bilgisi
  ▼
[Peer 1] ←──────────────→ [Peer 2]
  │    \                 /    │
  │     \     Ağ        /     │
  ▼      \             /      ▼
[Peer 3]──────────────────[Peer 4]
         Doğrudan P2P bağlantı

Yaygın P2P ağları: BitTorrent, Direct Connect, eDonkey, Freenet
```

> ⚠️ **Not:** WhatsApp P2P değildir! Mesajlar önce WhatsApp sunucusuna gider. Gerçek P2P'de sunucu aracısı yoktur.

---

## 15.3 Web ve E-Posta Protokolleri

### HTTP ve HTML

**HTTP** (Hypertext Transfer Protocol) → Web'de gezinmeyi sağlar  
**HTML** (Hypertext Markup Language) → Web sayfalarının yazıldığı dil  
**URL** (Uniform Resource Locator) → Web kaynaklarının adresi

```
URL Anatomisi:
http://www.cisco.com/index.html
 │        │    │        │
 │        │    │        └── Dosya adı
 │        │    └─────────── Domain (alan adı)
 │        └──────────────── Host adı (makine adı)
 └───────────────────────── Protokol
```

---

### Web Sayfası Açılma Adımları

```
ADIM 1: Tarayıcı URL'yi ayrıştırır
  http  →  www.cisco.com  →  index.html
  (protokol)  (sunucu adı)  (dosya adı)

ADIM 2: DNS sorgusu → IP çözümlemesi
  www.cisco.com → DNS → 198.133.219.25

ADIM 3: İstemci, GET isteği gönderir
  GET /index.html HTTP/1.1
  Host: www.cisco.com
  User-Agent: Firefox/39.0
  Accept-Encoding: gzip

ADIM 4: Sunucu, 200 OK + HTML kodu döner
  HTTP/1.1 200 OK
  Server: Apache/1.3.41
  Content-Type: text/html
  <html>...içerik...</html>

ADIM 5: Tarayıcı HTML'yi işler ve sayfayı gösterir
```

---

### HTTP Mesaj Tipleri

| Mesaj | Kullanım |
|-------|---------|
| **GET** | Web sayfası/kaynak isteme |
| **POST** | Form verisi gönderme (kayıt formu, login) |
| **PUT** | Sunucuya dosya yükleme (Google Drive vb.) |
| **DELETE** | Sunucudaki kaynağı silme |
| **HEAD** | Sadece başlık bilgisi isteme |

**HTTP Durum Kodları:**

```
2xx → Başarılı
  200 OK           → İstek başarılı, içerik geliyor

4xx → İstemci Hatası
  404 Not Found    → Sayfa bulunamadı

5xx → Sunucu Hatası
  503 Unavailable  → Servis şu an çalışmıyor
  500 Server Error → Sunucu iç hatası
```

---

### HTTP vs HTTPS

```
HTTP (TCP 80)                HTTPS (TCP 443)
─────────────                ───────────────
❌ Şifresiz (plain text)     ✅ TLS ile şifreli
❌ Wireshark ile okunabilir  ✅ Dinlenemez
❌ Güvensiz                  ✅ Günümüzde standart
❌ Artık neredeyse yok       ✅ Tüm modern siteler
```

> 💡 Google, HTTPS kullanmayan siteleri arama sonuçlarında daha düşükte gösterir. Bu yüzden tüm modern siteler HTTPS'e geçmiştir.

> 📄 RFC dokümantasyonu: Protokollerin nasıl çalıştığını tanımlayan resmi belgelerdir. IETF tarafından yayınlanır. Örn: RFC 2616 → HTTP protokolü.

---

### E-Posta Protokolleri

#### E-Posta Akış Diyagramı:

```
GÖNDEREN                                 ALICI
(ozan@gmail.com)                   (gokhan@yahoo.com)

[Mail Uygulaması]                   [Mail Uygulaması]
     │                                       ▲
     │ SMTP (gönderme)                       │ IMAP/POP3 (alma)
     ▼                                       │
[Gmail                    SMTP          Yahoo
 Mail     ────────────────────────────▶ Mail
 Sunucusu]                              Sunucusu]
                  İNTERNET
```

---

### SMTP (Simple Mail Transfer Protocol)

**Görevi:** Mail göndermek için kullanılır.

```
Portlar:
  TCP 25   → Sunucular arası (ama ISP'ler genellikle engeller)
  TCP 587  → İstemci → Sunucu (TLS ile güvenli, önerilen)
  TCP 465  → İstemci → Sunucu (SSL ile güvenli)
```

> ⚠️ Türkiye'de Türk Telekom, spam nedeniyle TCP 25'i yasaklamıştır. Mail gönderimi için TCP 587 veya 465 kullanılmalıdır.

---

### POP3 vs IMAP

| Özellik | POP3 (TCP 110/995) | IMAP (TCP 143/993) |
|---------|--------------------|--------------------|
| Temel davranış | Maili indir, sunucudan sil | Maili senkronize et, sunucuda tut |
| Çoklu cihaz | ❌ Uygun değil | ✅ Mükemmel |
| Offline kullanım | ✅ Mailler lokalde | ✅ Cache ile mümkün |
| Sunucu kapasitesi | ✅ Az kullanır | Daha fazla kullanır |
| Günümüzde tercih | ❌ Eskidi | ✅ Standart |

```
IMAP senkronizasyonu:
  Yahoo Sunucusu
       │
       ├──────▶ Laptop (kopya)
       ├──────▶ Cep Telefonu (kopya)
       └──────▶ Tablet (kopya)

  Herhangi bir cihazda mail silinirse → tüm cihazlardan silinir
  Herhangi bir cihazda okunursa → tüm cihazlarda okunmuş görünür
```

---

### Mail Uygulaması Yapılandırması

```
Outlook/Thunderbird konfigürasyonu için gerekli bilgiler:

  Ad:                 İstediğiniz isim (doğrulanmaz)
  Mail adresi:        kullanici@gmail.com

  Gelen posta sunucusu (Incoming):
    Protokol:         IMAP (önerilir) veya POP3
    Sunucu:           imap.gmail.com
    Port:             993 (SSL/TLS)

  Giden posta sunucusu (Outgoing):
    Protokol:         SMTP
    Sunucu:           smtp.gmail.com
    Port:             587 (TLS) veya 465 (SSL)

  Kullanıcı adı:      kullanici@gmail.com
  Şifre:              ••••••••
```

---

## 15.4 IP Adres Servisleri

### DNS (Domain Name System)

**Görevi:** İnsan tarafından okunabilen alan adlarını (www.google.com) sayısal IP adreslerine çevirmek.

```
www.cisco.com  ──DNS──▶  198.133.219.25
     │                          │
  Alan adı                  IP adresi
(insanlar için)          (makineler için)
```

#### DNS Kayıt Tipleri:

| Kayıt | Açıklama | Örnek |
|-------|----------|-------|
| **A** | IPv4 adresi kaydı | www.google.com → 8.1.1.1 |
| **AAAA** | IPv6 adresi kaydı | www.google.com → 2001:db8::1 |
| **NS** | DNS sunucusu kaydı | google.com → ns1.google.com |
| **MX** | Mail sunucusu kaydı | google.com → mx1.google.com |
| **CNAME** | Takma ad kaydı | example.com → www.example.com |

---

### DNS Hiyerarşisi

```
              [ROOT DNS]
              (13 kök sunucu)
                    │
         ┌──────────┼──────────┐
         ▼          ▼          ▼
      [.com]     [.org]      [.tr]
    TLD Sunucu  TLD Sunucu  TLD Sunucu
         │
         ▼
    [cisco.com]
    Yetkili DNS Sunucusu
    (Authoritative Name Server)
         │
    ┌────┼────┐
    ▼    ▼    ▼
  www  ftp  mail
```

#### Nasıl Çalışır?

```
Senaryo: www.cisco.com için IP öğrenmek

1. İstemci → Yerel DNS Sunucusu: "www.cisco.com nerede?"
2. Yerel DNS → Kök Sunucu: ".com'u kim tutuyor?"
   Kök → "6.1.1.1 tutuyor"
3. Yerel DNS → .com Sunucusu: "cisco.com'u kim tutuyor?"
   .com → "7.1.1.1 tutuyor"
4. Yerel DNS → cisco.com Sunucusu: "www kaydı nedir?"
   cisco.com → "198.133.219.25"
5. Yerel DNS → İstemci: "www.cisco.com = 198.133.219.25"
6. İstemci → 198.133.219.25'e TCP bağlantısı başlatır

NOT: Yerel DNS bu bilgiyi CACHE'e alır.
     Aynı sorgu tekrar gelirse doğrudan cache'den cevap verir.
```

---

### DNS Mesaj Formatı

```
DNS İki Tip Mesaj Kullanır:

QUERY (Sorgu):            RESPONSE (Cevap):
┌─────────────┐           ┌─────────────────┐
│   Header    │           │     Header      │
├─────────────┤           ├─────────────────┤
│  Question   │           │    Question     │
│  Section    │           ├─────────────────┤
└─────────────┘           │  Answer Section │
                          ├─────────────────┤
                          │ Auth. Section   │
                          ├─────────────────┤
                          │  Additional     │
                          └─────────────────┘
```

---

### nslookup Komutu

```bash
# Temel DNS sorgusu
nslookup www.cisco.com

# Alt mod sorguları
nslookup
> set type=A      → IPv4 kaydı sorgula
> cisco.com

> set type=AAAA   → IPv6 kaydı sorgula
> cisco.com

> set type=MX     → Mail sunucusu sorgula
> cisco.com

> set type=NS     → DNS sunucusu sorgula
> cisco.com

> set type=CNAME  → Takma ad sorgula
> www.hotmail.com
```

---

### DHCP (Dynamic Host Configuration Protocol)

**Görevi:** Ağa bağlanan cihazlara otomatik IP yapılandırması dağıtmak.

**Manuel (Statik) vs Otomatik (Dinamik):**

```
Statik IP:                    DHCP (Dinamik IP):
────────────                  ──────────────────
Elle yazılır:                 Otomatik alınır:
  IP: 192.168.1.10             IP: 192.168.1.201
  Subnet: 255.255.255.0        Subnet: 255.255.255.0
  Gateway: 192.168.1.1         Gateway: 192.168.1.1
  DNS: 8.8.8.8                 DNS: 8.8.8.8

Genelde: Sunucular,           Genelde: Kullanıcı PC'leri,
routerlar, yazıcılar          laptoplar, telefonlar
```

---

### DHCP DORA Süreci

```
İstemci                              DHCP Sunucusu
    │                                      │
    │──DHCPDISCOVER (Broadcast)───────────▶│
    │  "Bana IP verebilecek kimse var mı?"  │
    │  Kaynak IP: 0.0.0.0                  │
    │  Hedef IP: 255.255.255.255           │
    │  Kaynak Port: 68, Hedef Port: 67     │
    │                                      │
    │◀─DHCPOFFER (Unicast)─────────────────│
    │  "192.168.1.56 IP'sini kullanmak     │
    │   ister misin?"                      │
    │                                      │
    │──DHCPREQUEST (Broadcast)────────────▶│
    │  "Evet, o IP'yi kullanmak istiyorum! │
    │   Herkese duyuruyorum."              │
    │                                      │
    │◀─DHCPACK (Unicast)───────────────────│
    │  "IP sana 48 saatliğine rezerve      │
    │   edildi. Kullanabilirsin!"           │
    │                                      │
    ↓  IP kullanımı başlar                 │

DORA = Discover → Offer → Request → Acknowledge

Kiralama süresi dolmadan (yarı zamanda = 24. saatte):
İstemci yeni DHCPREQUEST gönderir → DHCPACK ile süre uzar
```

**ipconfig /all çıktısında DHCP bilgileri:**

```
DHCP Enabled......: Yes
IPv4 Address......: 10.64.98.119
Subnet Mask.......: 255.255.240.0
Default Gateway...: 10.64.0.1
DHCP Server.......: 10.11.0.20
DNS Servers.......: 8.8.8.8
Lease Obtained....: 10 Ağustos 2015
Lease Expires.....: 12 Ağustos 2015   ← 48 saatlik kiralama
```

> 💡 **DHCPv6 farkı:** DHCPv6, varsayılan gateway adresini vermez! Bu bilgi Router Advertisement mesajından alınır.

---

## 15.5 Dosya Paylaşım Servisleri

### FTP (File Transfer Protocol)

**Görevi:** İstemci-sunucu arasında çift yönlü dosya transferi.

```
FTP İKİ BAĞLANTI KULLANIR:

İstemci                    Sunucu
   │                          │
   │──TCP 21 (Kontrol)───────▶│  ← Komutlar, dosya isimleri
   │                          │
   │──TCP 20 (Data)──────────▶│  ← Asıl dosya transferi
   │◀─────────────────────────│    (her transfer için açılır)

FTP İstemci yazılımları: FileZilla, WinSCP, Qt FTP
FTP Sunucu yazılımları:  vsftpd (Linux), FileZilla Server (Windows)
```

**FTP Kullanım Alanları:**
- Web sunucusuna web sayfası yüklemek
- Web sayfası yedeğini indirmek
- Network cihazlarına işletim sistemi yüklemek (genellikle TFTP tercih edilir)

> ⚠️ FTP şifresizdir! Güvenli alternatif: **SFTP (TCP 22, SSH üzerinden)**

---

### TFTP (Trivial File Transfer Protocol)

**Görevi:** Basit, hafif dosya transferi.

```
TFTP Özellikleri:
  Port: UDP 69
  Protokol: UDP (bağlantısız)
  Onay mekanizması: Var (uygulama seviyesinde)
  Şifreleme: Yok
  Kimlik doğrulama: Yok
  Kullanım: Cisco cihazlara IOS yükleme

TFTP Transfer Akışı:
  Sunucu → [Blok 1] → İstemci → [ACK 1] → Sunucu
  Sunucu → [Blok 2] → İstemci → [ACK 2] → Sunucu
  ...
```

> 💡 TFTP UDP kullanmasına rağmen kendi onay mekanizması vardır. Yavaş ama basittir.

---

### SMB / Samba (Server Message Block)

**Görevi:** Ağ üzerinden dosya ve yazıcı paylaşımı.

```
SMB Bağlantısı:
\\192.168.1.6 → kullanıcı adı/şifre → dosyalar

Uzaktaki dosyalar yerel disk gibi görünür:
  ┌─────────────────────────────┐
  │  Shared Resources           │
  │  ├── Desktop                │
  │  ├── My Documents           │
  │  └── Local Disk (C:)        │
  │       ├── _Cisco            │
  │       └── _Contracts        │
  └─────────────────────────────┘
  
SMB Mesajlarının 3 işlevi:
  1. Oturum başlatma, kimlik doğrulama, sonlandırma
  2. Dosya ve yazıcı erişimini kontrol etme
  3. Uygulamalar arası mesajlaşma

Port: TCP 445
```

> ⚠️ **Güvenlik Notu:** SMB v1 sürümünde ciddi güvenlik açıkları bulundu ve WannaCry fidye yazılımı bu açığı kullandı. Windows'ta **SMB v3** kullanılmalı, SMB v1 devre dışı bırakılmalıdır.

---

### Telnet vs SSH

| Parametre | SSH (TCP 22) | Telnet (TCP 23) |
|-----------|-------------|-----------------|
| **Güvenlik** | Yüksek güvenlik | Düşük güvenlik |
| **Veri formatı** | Şifreli | Düz metin (plain text) |
| **Kimlik doğrulama** | Public key şifrelemesi | Mekanizma yok |
| **Ağ tipi** | Genel ağlar için uygun | Sadece özel ağlar |
| **Güvenlik açıkları** | Telnet'in sorunlarını giderir | Çok sayıda açık |
| **RFC** | RFC 4253 | RFC 854 |
| **Wireshark** | Okunamaz | Her şey görünür |

> ❌ **Telnet'i ASLA internet üzerinden kullanmayın!** Kullanıcı adı, şifre ve tüm komutlar açık metin olarak iletilir.  
> ✅ **Her zaman SSH kullanın** – Cisco cihazları için de geçerli.

---

## 📊 Protokol Özet Diyagramı

```
                    UYGULAMA KATMANI PROTOKOLLERİ
                               │
          ┌────────────────────┼────────────────────┐
          ▼                    ▼                    ▼
      WEB              E-POSTA              DOSYA/DİĞER
   ─────────          ─────────           ────────────
   HTTP (80)          SMTP (25)           FTP (21/20)
   HTTPS (443)        POP3 (110)          TFTP (UDP 69)
                      IMAP (143)          SMB (445)
                                          SSH (22)
                                          Telnet (23)
          ▼                    ▼                    ▼
      ─────────          ─────────           ────────────
   IP ADRESLEMESİ
   ─────────────
   DNS (UDP 53)
   DHCP (UDP 67/68)
```

---

## 📝 Bu Modülde Öğrendiklerimiz

1. **Uygulama katmanı** (L7) son kullanıcı arayüzü sağlar; **sunum katmanı** (L6) veri biçimlendirme/sıkıştırma/şifreleme yapar; **oturum katmanı** (L5) oturum takibi yapar.
2. **İstemci-Sunucu modeli:** Hizmet talep eden istemci, hizmet veren sunucu — en yaygın kullanılan model.
3. **P2P ağları:** Her cihaz hem istemci hem sunucu olabilir; BitTorrent en yaygın örnek.
4. **HTTP mesajları:** GET (sayfa iste), POST (form gönder), PUT (dosya yükle).
5. **E-posta üçlüsü:** Göndermek için SMTP, indirmek için POP3 (siler) veya IMAP (senkronize eder).
6. **DNS:** Alan adını IP'ye çevirir; A, AAAA, MX, NS, CNAME kayıt tipleri; hiyerarşik yapı.
7. **DHCP DORA:** Discover → Offer → Request → Acknowledge; kiralama süresi kavramı.
8. **FTP:** TCP 21 (kontrol) + TCP 20 (data) — çift bağlantı; güvensiz → SFTP tercih edilmeli.
9. **SMB:** Dosya/yazıcı paylaşımı, TCP 445; SMB v3 güvenli, SMB v1 tehlikeli.
10. **SSH vs Telnet:** Telnet şifresiz, SSH şifreli — her zaman SSH kullanın.

---

*Cisco CCNA1 · Introduction to Networks v7.0 · Modül 15*  
*Gökhan AKIN – CCIE · gokhan@agyoneticileri.org*  
*Ozan BÜK – CCIE · ozan@agyoneticileri.org*
