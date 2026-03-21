# CCNA1 – Modül 10: Temel Yönlendirici Yapılandırması


---

## 📋 Modül Hedefi
Yönlendirici ve son cihazlarda **ilk ayarları uygulamak**.

| Konu | Hedef |
|------|-------|
| Yönlendiricinin Başlangıç Yapılandırması | İlk ayarları bir Cisco IOS yönlendiricisinde yapılandırmak |
| Arayüzleri Yapılandırma | Cisco IOS yönlendiricisi üzerinde iki etkin arayüz yapılandırmak |
| Varsayılan Ağ Geçidi Yapılandırması | Aygıtları varsayılan ağ geçidini kullanacak şekilde yapılandırmak |

---

## 10.1 Yönlendirici İlk Ayarları Yapılandırma

### Switch ile Router Arasındaki Temel Fark

```
┌──────────────────────────────────────────────────────────────┐
│                  SWITCH vs ROUTER                            │
├─────────────────────────────┬────────────────────────────────┤
│           SWITCH            │            ROUTER              │
├─────────────────────────────┼────────────────────────────────┤
│ • Layer 2 (MAC adresi)      │ • Layer 3 (IP adresi)          │
│ • Aynı ağ içinde anahtarlama│ • Farklı ağlar arası yönlendirme│
│ • Fiziksel portlara IP VERI.│ • Fiziksel portlara IP VERİLİR │
│   MEZKEN SVI üzerinden veril│                                │
│ • Varsayılan gateway'i bu   │ • Bilgisayarların gatewayidir  │
│   switch değil router'dır   │                                │
│ • Portlar: Layer 2 port     │ • Portlar: Layer 3 interface   │
└─────────────────────────────┴────────────────────────────────┘
```

### Neden Router Portuna Doğrudan IP Verilir?

```
Bilgisayar (192.168.1.10)
        │
        ▼
    SWITCH (Layer 2 – sadece MAC ile çalışır)
        │
        ▼
 ROUTER G0/0/0 (192.168.1.1) ← Bu IP bilgisayarın gateway'idir!
        │
        ▼
   İnternet / Diğer ağlar
```

Router'ın her fiziksel portu ayrı bir **Layer 3 arayüzüdür** → Doğrudan IP verilebilir.

---

### Temel Yönlendirici Yapılandırma Adımları

Switch'te yapılan temel yapılandırma (hostname, parolalar, banner, kaydetme) **router'da da aynı komutlarla** yapılır:

```bash
Router(config)# hostname hostname
Router(config)# enable secret password
Router(config)# line console 0
Router(config-line)# password password
Router(config-line)# login
Router(config)# line vty 0 4
Router(config-line)# password password
Router(config-line)# login
Router(config-line)# transport input {ssh | telnet}
Router(config)# service password-encryption
Router(config)# banner motd # mesaj #
Router(config)# end
Router# copy running-config startup-config
```

> 📝 **Router'da VTY 0 4** kullanılır (switch'te 0 15). Bu 5 eşzamanlı bağlantıya izin verir.
> `transport input ssh telnet` komutu hem SSH hem Telnet ile bağlanmaya izin verir.

---

### Tam Yapılandırma Örneği – R1

```bash
Router> enable
Router# configure terminal

! İsim ver
Router(config)# hostname R1

! Enable parolası
R1(config)# enable secret class

! Console parolası
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login

! VTY (SSH/Telnet) parolası
R1(config-line)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

! Tüm parolaları şifrele
R1(config)# service password-encryption

! Uyarı mesajı
R1(config)# banner motd #
***********************************************
WARNING: Unauthorized access is prohibited!
***********************************************
#

! Kaydet
R1(config)# exit
R1# copy running-config startup-config
```

---

## 10.2 Arayüzleri Yapılandırma

### Router Arayüz Tipleri

```
┌─────────────────────────────────────────────────────────┐
│           R1 - Cisco Router Arayüzleri                  │
│                                                         │
│  [GE0/0/0] ──────── LAN'a bağlı (192.168.10.0/24)      │
│   (Gigabit │                                            │
│   Ethernet)│                                            │
│            │                                            │
│  [GE0/0/1] ──────── WAN/İnternet'e bağlı               │
│            │        (209.165.200.224/30)                │
│            │                                            │
│  [Serial0] ──────── Seri bağlantı (WAN)                 │
└─────────────────────────────────────────────────────────┘
```

### Router Arayüz Yapılandırma Komutları

```bash
Router(config)# interface type-and-number
Router(config-if)# description açıklama-metni
Router(config-if)# ip address ipv4-adresi subnet-maskesi
Router(config-if)# ipv6 address ipv6-adresi/prefix-uzunluğu
Router(config-if)# no shutdown
```

**Önemli Notlar:**
- `description` → Opsiyoneldir ama çok önerilir (kablo nereye gidiyor?)
- `no shutdown` → **Zorunludur!** Router arayüzleri varsayılan olarak kapalı gelir
- Layer 3 arayüzler (router) varsayılan olarak shutdown durumundadır
- Layer 2 portlar (switch) varsayılan olarak açık gelir

---

### Yapılandırma Örneği – Topoloji

```
                    İnternet
                       │
        ┌──────────────┤
        │         G0/0/1│.225
   PC1  │               │              PC2
.10 │   │          [R1] │  .226 [R2] .1
────┤[SW]├──G0/0/0──────┤─────────────────── .10
    │   │  .1      .1   │
    │   │               │
 192.168.10.0/24   209.165.200.224/30   10.1.1.0/24

IPv6:
2001:db8:acad:10::/64  |  2001:db8:feed:224::/64  |  2001:db8:cafe:1::/64
```

### G0/0/0 Yapılandırması (LAN Tarafı)

```bash
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

**Çıktı (bağlantı geldiğinde):**
```
*Aug 1 01:43:53.435: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to down
*Aug 1 01:43:56.447: %LINK-3-UPDOWN: Interface GigabitEthernet0/0/0, changed state to up
*Aug 1 01:43:57.447: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/0, changed state to up
```

### G0/0/1 Yapılandırması (WAN/İnternet Tarafı)

```bash
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description Link to R2
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

---

### Arayüz Durumu Mesajlarını Anlama

```
Arayüz durumu için iki katman var:

Katman 1 (Fiziksel):  up / down / administratively down
Katman 2 (Protokol):  up / down

┌─────────────────┬────────────────┬──────────────────────────────────┐
│ Status (L1)     │ Protocol (L2)  │ Anlam                            │
├─────────────────┼────────────────┼──────────────────────────────────┤
│ up              │ up             │ ✅ Her şey normal, çalışıyor     │
│ up              │ down           │ ⚠️ Enkapsülasyon veya L2 sorunu  │
│ down            │ down           │ 🔴 Fiziksel bağlantı yok         │
│ admin down      │ down           │ 🔴 shutdown komutu verilmiş      │
└─────────────────┴────────────────┴──────────────────────────────────┘
```

---

### Arayüz Doğrulama Komutları

#### show ip interface brief

```bash
R1# show ip interface brief
```

```
Interface         IP-Address       OK? Method  Status              Protocol
GigabitEthernet0/0/0  192.168.10.1 YES manual  up                 up
GigabitEthernet0/0/1  209.165.200.225 YES manual up               up
Vlan1             unassigned       YES unset   administratively down  down
```

#### show ipv6 interface brief

```bash
R1# show ipv6 interface brief
```

```
GigabitEthernet0/0/0 [up/up]
  FE80::201:C9FF:FE89:4501
  2001:DB8:ACAD:10::1
GigabitEthernet0/0/1 [up/up]
  FE80::201:C9FF:FE89:4502
  2001:DB8:FEED:224::1
```

#### show ip route (Routing Tablosu)

```bash
R1# show ip route
```

```
Gateway of last resort is not set

      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L        192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
      209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
C        209.165.200.224/30 is directly connected, GigabitEthernet0/0/1
L        209.165.200.225/32 is directly connected, GigabitEthernet0/0/1
```

**Routing tablosu kodu açıklamaları:**

| Kod | Anlamı |
|-----|--------|
| `C` | Connected – Doğrudan bağlı ağ |
| `L` | Local – Arayüzün kendi IP'si (/32) |
| `S` | Static – Elle girilmiş statik rota |
| `O` | OSPF – OSPF protokolüyle öğrenilmiş |
| `R` | RIP – RIP protokolüyle öğrenilmiş |

#### show interfaces (Detaylı İstatistik)

```bash
R1# show interfaces gigabitEthernet 0/0/0
```

```
GigabitEthernet0/0/0 is up, line protocol is up
  Hardware is ISR4321-2x1GE, address is a0e0.af0d.e140
  Description: Link to LAN
  Internet address is 192.168.10.1/24
  MTU 1500 bytes, BW 100000 Kbit/sec
  ...
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
  1180 packets input, 109486 bytes, 0 no buffer
  0 runts, 0 giants, 0 throttles
  0 input errors, 0 CRC, 0 frame
  ...
```

**İzlenmesi gereken önemli sayaçlar:**

| Sayaç | Anlam | Yüksekse Sorun |
|-------|-------|----------------|
| `CRC` | Hatalı frame (kablo veya donanım sorunu) | ✅ |
| `runts` | Çok kısa frame | ✅ |
| `giants` | Çok uzun frame | ✅ |
| `input errors` | Toplam giriş hataları | ✅ |
| `collisions` | Çarpışma (eski hub ağları) | ✅ |

---

### Doğrulama Komutları Özeti

| Komut | İşlev |
|-------|-------|
| `show ip interface brief` | Tüm arayüzlerin IPv4 özeti (IP + durum) |
| `show ipv6 interface brief` | Tüm arayüzlerin IPv6 özeti |
| `show ip route` | IPv4 routing tablosu |
| `show ipv6 route` | IPv6 routing tablosu |
| `show interfaces` | Tüm arayüzlerin detaylı istatistiği (L1/L2) |
| `show ip interface` | Router arayüzlerinin IPv4 istatistikleri |
| `show ipv6 interface` | Router arayüzlerinin IPv6 istatistikleri |

---

## 10.3 Varsayılan Ağ Geçidi Yapılandırması

### Default Gateway (Varsayılan Ağ Geçidi) Nedir?

```
192.168.10.0/24 ağı                   192.168.11.0/24 ağı
       │                                       │
 ┌─────┴──────┐    ┌──────────────┐    ┌───────┴─────┐
 │    PC1     │    │     R1       │    │    PC3      │
 │ 192.168.   │───►│ G0/0/0: .1  │───►│ 192.168.    │
 │ 10.10      │    │ G0/0/1: .1  │    │ 11.10       │
 └────────────┘    └──────────────┘    └─────────────┘
        │
   PC1 başka ağa paket göndermek istiyor:
   1. Hedef IP: 192.168.11.10 (farklı ağ!)
   2. Paketi kendi ağ geçidine (192.168.10.1) gönder
   3. Router paketi doğru ağa iletir
```

**Kural:** Ana bilgisayar ile router arayüzünün IP adresleri **aynı ağda** olmalıdır!

### Host (Bilgisayar) Üzerinde Default Gateway

| Yapılandırma | Yöntem |
|-------------|--------|
| **Manuel** | IP ayarlarında "Varsayılan Ağ Geçidi" alanına router'ın IP'sini yaz |
| **Otomatik (DHCP)** | DHCP sunucusu otomatik olarak gönderir |

### Switch Üzerinde Default Gateway

Switch'in uzaktan yönetilebilmesi için (SVI IP'si üzerinden) default gateway tanımlanmalıdır:

```bash
Switch# configure terminal
Switch(config)# ip default-gateway 192.168.1.1
Switch(config)# end
```

```
┌─────────────────────────────────────────────────────────┐
│  Neden Switch'e Default Gateway Gerekir?                │
│                                                         │
│  Sen başka ağdan (örn. 10.0.0.0/8) switch'e SSH atmak  │
│  istiyorsun. Switch paketin nereye gideceğini bilmek    │
│  için default gateway'e ihtiyaç duyar.                  │
│                                                         │
│  [Yönetim PC] ──► [Router] ──► [Switch SVI 192.168.1.20]│
│  10.0.0.5          192.168.1.1    ↑                     │
│                    Gateway        |                     │
│                                Switch bu gateway'i      │
│                                bilmek zorunda           │
└─────────────────────────────────────────────────────────┘
```

---

## 📝 Tam Router Yapılandırma Özeti (Cheat Sheet)

```bash
! ═══════════════════════════════════════════
! TEMEL ROUTER YAPILANDIRMASI (R1 Örneği)
! ═══════════════════════════════════════════

! 1. Privileged moda geç
Router> enable

! 2. Yapılandırma moduna gir
Router# configure terminal

! ── BAŞLANGIÇ AYARLARI ──────────────────────

! 3. İsim ver
Router(config)# hostname R1

! 4. Enable parolası
R1(config)# enable secret GuvenliParola1!

! 5. Console parolası
R1(config)# line console 0
R1(config-line)# password ConsoleP1!
R1(config-line)# login
R1(config-line)# exit

! 6. VTY parolası (SSH/Telnet)
R1(config)# line vty 0 4
R1(config-line)# password VTYParola1!
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit

! 7. Tüm parolaları şifrele
R1(config)# service password-encryption

! 8. Banner
R1(config)# banner motd #
Yetkisiz erisim yasaktir!
#

! ── ARAYÜZ YAPILANDIRMASI ───────────────────

! 9. LAN arayüzü (G0/0/0)
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description LAN Baglantisi
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

! 10. WAN arayüzü (G0/0/1)
R1(config)# interface gigabitEthernet 0/0/1
R1(config-if)# description R2 Baglantisi
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:feed:224::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

! ── KAYDETME ─────────────────────────────────

! 11. Kaydet
R1(config)# end
R1# copy running-config startup-config
```

---

## 🔍 Hızlı Doğrulama Akışı

```
Router yapılandırıldı mı?
        │
        ▼
show ip interface brief
        │
    ┌───┴───┐
    │ up/up │─── Evet ──► Devam et
    └───────┘
        │
      down
        │
        ▼
   no shutdown
   uyguladın mı?
        │
   ─── Hayır ──► interface X → no shutdown
        │
      Evet
        │
        ▼
  Kablo bağlı mı?
  Karşı cihaz açık mı?
        │
   Sorunları çöz
```

---

## 🔑 Önemli Hatırlatmalar

- **Router portları varsayılan kapalı gelir** → Her zaman `no shutdown` gerekir
- **Switch portları varsayılan açık gelir** → `no shutdown` nadiren gerekir
- **Switch SVI** → `interface vlan 1` üzerinden IP (sanal)
- **Router fiziksel port** → `interface gigabitEthernet 0/0/X` üzerinden IP (fiziksel)
- **`description`** → Opsiyoneldir ama bakım açısından çok değerlidir
- **Default Gateway** → Son kullanıcı cihazları ve switch için gerekli; router'da routing ile yapılır
- **`C`** routing tablosunda = Directly Connected (doğrudan bağlı ağ)
- **`L`** routing tablosunda = Local address (/32 host route)
- **show interfaces** → Layer 1/2 istatistiklerini gösterir (hata sayaçları dahil)
- **`transport input ssh`** → Sadece SSH'a izin ver (Telnet'i engeller, güvenli)

---

*CCNA1 ITN v7.0 – Modül 10 | agyoneticileri.org*
