# 📘 CCNA1 – Modül 12: IPv6 Adresleme

> **Kurs:** Introduction to Networks v7.0 (ITN)  
> **Modül Amacı:** Bir IPv6 adresleme şeması uygulamak.

---

## 📑 İçindekiler

1. [Neden IPv6?](#1-neden-ipv6)
2. [IPv4'den IPv6'ya Geçiş Teknikleri](#2-ipv4den-ipv6ya-geçiş-teknikleri)
3. [IPv6 Adres Formatı ve Kısaltma Kuralları](#3-ipv6-adres-formatı-ve-kısaltma-kuralları)
4. [IPv6 Adres Türleri](#4-ipv6-adres-türleri)
5. [Özel IPv6 Adresleri](#5-özel-ipv6-adresleri)
6. [GUA ve LLA Statik Yapılandırma](#6-gua-ve-lla-statik-yapılandırma)
7. [IPv6 GUA için Dinamik Adresleme (SLAAC & DHCPv6)](#7-ipv6-gua-için-dinamik-adresleme-slaac--dhcpv6)
8. [Interface ID Türetme: EUI-64 ve Rastgele](#8-interface-id-türetme-eui-64-ve-rastgele)
9. [IPv6 LLA için Dinamik Adresleme](#9-ipv6-lla-için-dinamik-adresleme)
10. [IPv6 Multicast Adresleri](#10-ipv6-multicast-adresleri)
11. [IPv6 Subnetting](#11-ipv6-subnetting)
12. [Komut Özeti](#12-komut-özeti)
13. [Modül Özeti](#13-modül-özeti)

---

## 1. Neden IPv6?

### 1.1 IPv4'ün Sınırları

| Özellik | IPv4 | IPv6 |
|---|---|---|
| Bit sayısı | 32 bit | 128 bit |
| Toplam adres | ~4,3 milyar (2³²) | 340 undecilyon (2¹²⁸) |
| Subnet Maskesi | Var | Yok → Prefix Length kullanılır |
| Broadcast | Var | **Yok** → Multicast kullanılır |
| NAT | Gerekli | **Gerekli değil** |

### 1.2 IPv4 Adres Tükenme Tarihleri

```
  ARIN    (Amerika)      →  Temmuz 2015
  RIPE    (Avrupa)       →  Eylül 2012
  APNIC   (Asya-Pasifik) →  Haziran 2014
  AFRINIC (Afrika)       →  2020 (Beklenen)
  LACNIC  (Latin Amerika)→  Nisan 2011
```

> IPv6, 2000'lerin başında bu tükeniş öngörülerek tasarlandı. Tasarımcılar 10 yıl önceden süreci gördü.

---

## 2. IPv4'den IPv6'ya Geçiş Teknikleri

Tüm internet altyapısı bir anda IPv6'ya geçemez. Bu geçişi kolaylaştırmak için 3 teknik geliştirilmiştir:

```
┌─────────────────────────────────────────────────────────────────┐
│              IPv4 → IPv6 Geçiş Teknikleri                       │
├──────────────┬──────────────────────────────────────────────────┤
│  Dual Stack  │ Cihaz aynı anda hem IPv4 hem IPv6 çalıştırır.    │
│              │ İkisi de aktif. En yaygın kullanılan yöntem.      │
├──────────────┼──────────────────────────────────────────────────┤
│  Tunneling   │ IPv6 trafiğini IPv4 ağı üzerinden taşır.          │
│  (Tünelleme) │ IPv6 paketi, IPv4 paketin içine kapsüllenir.      │
│              │ ISP sadece IPv4 desteklese bile iki IPv6 ağı       │
│              │ birbirine bağlanabilir.                            │
├──────────────┼──────────────────────────────────────────────────┤
│  Translation │ NAT64 — IPv6 cihazın IPv4 cihazla iletişimini     │
│  (Çeviri)    │ sağlar. Sadece eski cihazlar için gerekli;         │
│              │ günümüzde Dual Stack tercih edilir.                │
└──────────────┴──────────────────────────────────────────────────┘
```

---

## 3. IPv6 Adres Formatı ve Kısaltma Kuralları

### 3.1 Temel Yapı

```
2001:0DB8:AC10:FE01:0000:0000:0000:0000
│    │    │    │    │    │    │    │
└─── Her biri 16 bit (hextet) ───────┘
         Toplam 8 hextet = 128 bit

Ayraç: İki nokta üst üste ( : )  ← nokta DEĞİL
```

- **8 adet hextet** — her biri 16 bit, onaltılık sistemde yazılır
- Aralarda **nokta değil iki nokta üst üste** `:`  kullanılır (IPv4 ile karışmasın diye)
- Büyük/küçük harfe duyarsız: `2001:DB8` = `2001:db8`

### 3.2 Kural 1: Baştaki Sıfırları Atla

Her hextet içindeki **baştaki sıfırları** yazmak zorunda değilsiniz:

```
01ab  →  1ab      (baş sıfırı atla)
09f0  →  9f0
0a00  →  a00
0000  →  0        (dört sıfır → tek sıfır)

⚠️ Sondaki sıfırları ATLAMAYIN!
2000  →  2000     (200 yazarsan çarşı karışır)
```

**Örnek:**
```
Tam hali  : 2001:0db8:0000:1111:0000:0000:0000:0200
Kısaltılmış: 2001:db8:0:1111:0:0:0:200
```

### 3.3 Kural 2: Çift İki Nokta ( :: ) Kuralı

Ardışık **sıfır hextetlerini** `::` ile özetleyebilirsiniz:

```
2001:db8:0:1111:0:0:0:200
                ↑ ↑ ↑
         3 sıfır hextet → :: ile özetle

→  2001:db8:0:1111::200
```

**⚠️ ALTIN KURAL: `::` bir adreste yalnızca BİR KEZ kullanılabilir!**

```
DOĞRU:   2001:db8:0:1111::200
DOĞRU:   2001:db8::1111:0:0:0:200
YANLIŞ:  2001:db8::1111::200   ← İKİ KEZ KULLANIMI — BİLGİSAYAR KABUL ETMEZ!
```

**Neden iki kez kullanılamaz?**
```
Bilgisayar bu adresi görür:  2001:db8::1111::200
8 hextet lazım, 4 tane var, 2 tane :: var.
Kaçı birinciye, kaçı ikinciye ait? → BİLİNEMEZ!
```

### 3.4 Kısaltma Örnekleri

```
Tam hali:
  2001:0DB8:0000:0000:0008:8000:0000:417A

Adım 1 – Baştaki sıfırları sil:
  2001:DB8:0:0:8:8000:0:417A

Adım 2 – En uzun sıfır bloğunu :: ile özetle:
  2001:DB8::8:8000:0:417A

Alternatif (daha az kısaltma ama doğru):
  2001:DB8:0:0:8:8000::417A
```

### 3.5 Prefix Length (Önek Uzunluğu)

IPv6'da subnet maskesi **yoktur**. Bunun yerine prefix length kullanılır:

```
IPv4: 192.168.1.0 / 255.255.255.0  →  192.168.1.0/24
IPv6: 2001:db8:acad:1::/64

Slash 64 → İlk 64 bit Network (Prefix), son 64 bit Host (Interface ID)

128 bit toplam:
┌──────────────────────┬──────────────────────┐
│    Network (Prefix)  │   Host (Interface ID) │
│       64 bit         │        64 bit         │
└──────────────────────┴──────────────────────┘
```

> Not: IPv6'da network kısmına **"prefix"**, host kısmına **"Interface ID"** denir. Anlam aynıdır.

---

## 4. IPv6 Adres Türleri

### 4.1 Üç Ana Tür

| Tür | Açıklama | IPv4 Karşılığı |
|---|---|---|
| **Unicast** | Tek bir arabirimi benzersiz olarak tanımlar | Unicast |
| **Multicast** | Bir grup cihaza aynı anda gönderim | Multicast |
| **Anycast** | Aynı IP birden fazla cihaza verilir; en yakına yönlendirilir | Kısmi karşılık var |

> ⚠️ **IPv6'da Broadcast YOKTUR!** Bunun yerine `ff02::1` (all-nodes multicast) kullanılır.

### 4.2 Unicast Alt Türleri

```
                    IPv6 Unicast Adresleri
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   Global Unicast     Link-Local         Unique Local
      (GUA)             (LLA)               (ULA)
   2000::/3          fe80::/10          fc00::/7
   İnternet'e        Sadece aynı        Kapalı ağlar
   çıkılabilir       ağda kullanılır    için özel adres
   (Public IP        (Router           (IPv4 private
   gibi)             geçilemez)         benzeri değil!)
```

---

## 5. Özel IPv6 Adresleri

### 5.1 Global Unicast Address (GUA)

İnternete çıkmak için kullanılan adres. ISP tarafından verilir.

```
Aktif GUA aralığı: 2000:: ile 3FFF:FFFF:... arası
İlk 3 bit: 001 (yani 2xxx veya 3xxx ile başlar)

GUA Yapısı (tipik /48 atama):
┌────────────────┬──────────┬──────────────────────┐
│ Global Routing │ Subnet   │    Interface ID       │
│    Prefix      │    ID    │                       │
│    48 bit      │  16 bit  │       64 bit          │
└────────────────┴──────────┴──────────────────────┘
      ISP verir    Sen kullan   Cihaz/kullanıcı oluşturur
```

### 5.2 Link-Local Address (LLA)

IPv6 aktive edildiği anda cihaz **otomatik olarak** oluşturur. Router geçilemez.

```
Aralık: FE80:: ile FEBF:FFFF:... arası
        (fe80::/10)

Özellikler:
  ✓ IPv6 aktive olur olmaz otomatik atanır
  ✓ Hiçbir yapılandırma gerekmez
  ✓ Aynı ağ içinde haberleşmek için zorunludur
  ✗ Router geçemez (başka ağa gidemez)
  ✗ İnternete çıkamaz

Kullanım amaçları:
  • Komşu cihazlarla haberleşme (Neighbor Discovery)
  • Default Gateway adresi olarak kullanılır
  • Routing protokolü mesajları için
```

### 5.3 Unique Local Address (ULA)

```
Aralık: FC00:: ile FDFF:FFFF:... arası
        (fc00::/7)

Ne değildir: IPv4 Private adreslerinin (192.168.x.x) karşılığı DEĞİL!
Ne için: İnternete hiç bağlanmayacak kapalı ağlar için
Örnekler: Askeri ağ, demiryolu sinyalizasyon ağı, elektrik dağıtım ağı
```

### 5.4 Loopback Adresi

```
IPv4'te: 127.0.0.0/8 aralığının tamamı  →  16 milyon adres israf!
IPv6'da: ::1/128                          →  Tek bir adres!

Test komutu:
  ping ::1
  → Kendi bilgisayarından cevap gelir
```

### 5.5 Adres Türleri Özet Tablosu

| Adres | Aralık | Kullanım |
|---|---|---|
| GUA | `2000::/3` → `3FFF.../3` | Global internet |
| LLA | `fe80::/10` | Sadece aynı link |
| ULA | `fc00::/7` | Kapalı kurumsal ağlar |
| Loopback | `::1/128` | Kendine ping |
| Multicast | `ff00::/8` | Grup yayını |

---

## 6. GUA ve LLA Statik Yapılandırma

### 6.1 Router'a Statik GUA Yapılandırması (Cisco IOS)

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```

> IPv4 komutlarıyla tek fark: `ip address` yerine `ipv6 address` kullanılır.

### 6.2 Router'a Statik LLA Yapılandırması

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address fe80::1:1 link-local
R1(config-if)# no shutdown
R1(config-if)# exit
```

### 6.3 Windows'ta Statik GUA Yapılandırması

Network Adapter Properties → IPv6 Properties:

```
IPv6 address      : 2001:db8:acad:1::10
Subnet prefix len : 64
Default gateway   : 2001:db8:acad:1::1   (veya router'ın LLA'sı)
DNS server        : 2001:db8:acad:2::B
```

> **Best Practice:** Default Gateway olarak router'ın **LLA adresi** tercih edilir.  
> (DHCPv6 veya SLAAC kullanıldığında, router'ın LLA'sı otomatik gateway olur.)

---

## 7. IPv6 GUA için Dinamik Adresleme (SLAAC & DHCPv6)

### 7.1 Dinamik IP Alma Süreci

IPv4'te tek yol vardı: DHCP. IPv6'da **3 farklı yöntem** vardır:

```
       PC açıldı
           │
           ▼
   ┌───────────────────────────────┐
   │  RS Mesajı gönder             │
   │  (Router Solicitation)        │
   │  "Ağda router var mı?"        │
   │  Hedef: ff02::2 (tüm router) │
   └───────────────────────────────┘
           │
           ▼
   ┌───────────────────────────────┐
   │  Router RA Mesajı döner       │
   │  (Router Advertisement)       │
   │  "Ben varım! IP bilgin şöyle" │
   └───────────────────────────────┘
           │
    ┌──────┴──────┐
    │  RA içinde  │
    │  hangi mod  │
    └──────┬──────┘
           │
   ┌───────┼───────────────┐
   ▼       ▼               ▼
Yöntem 1  Yöntem 2     Yöntem 3
 SLAAC   SLAAC+DHCP  Stateful DHCP
```

### 7.2 Yöntem 1: SLAAC (Stateless Address Autoconfiguration)

```
RA Mesajında: Managed:"0"  Other:"0"

Router şunu söyler:
  • Prefix: 2001:db8:acad:1::/64
  • Gateway: FE80::1 (benim LLA'm)

PC ne yapar:
  • Network kısmını router'dan öğrenir: 2001:db8:acad:1::
  • Host kısmını KENDİSİ oluşturur (EUI-64 veya rastgele)
  • DNS'i elle girmek gerekir!

Özellik: Stateless — kim hangi IP aldı, hiçbir yerde kayıt yok
Kullanım: Ev kullanıcıları için
```

### 7.3 Yöntem 2: SLAAC + Stateless DHCPv6

```
RA Mesajında: Managed:"0"  Other:"1"

Router şunu söyler:
  • IP'nin network kısmını ve Gateway'i benden öğren
  • Host kısmını kendin oluştur
  • DNS gibi diğer bilgileri DHCPv6 sunucusundan öğren

PC ne yapar:
  1. Router'dan prefix ve gateway öğrenir
  2. Host kısmını kendisi oluşturur
  3. DHCPv6 sunucusundan sadece DNS öğrenir

Özellik: Stateless — yine kayıt yok
Kullanım: Ev kullanıcıları (tam otomatik, DNS dahil)
```

### 7.4 Yöntem 3: Stateful DHCPv6

```
RA Mesajında: Managed:"1"  Other:"0"

Router şunu söyler:
  • Git, tüm IP bilgilerini DHCPv6 sunucusundan öğren
  • Ben sadece Gateway'im

PC ne yapar:
  1. Router'dan sadece Gateway öğrenir
  2. DHCPv6 sunucusundan IP, prefix, DNS hepsini alır
  3. DHCPv6 sunucusu KİME hangi IP'yi verdiğini kaydeder

Özellik: Stateful — tam kayıt tutulur!
Kullanım: Kurumsal ağlar, şirketler
```

### 7.5 Üç Yöntem Karşılaştırması

```
┌─────────────────┬─────────┬──────────────┬──────────────────┐
│ Özellik         │ SLAAC   │ SLAAC+DHCP   │ Stateful DHCP    │
├─────────────────┼─────────┼──────────────┼──────────────────┤
│ IP kaynağı      │ Kendisi │ Kendisi      │ DHCPv6           │
│ DNS kaynağı     │ Elle    │ DHCPv6       │ DHCPv6           │
│ Gateway kaynağı │ Router  │ Router       │ Router           │
│ Kayıt tutulur   │ Hayır   │ Hayır        │ Evet             │
│ RA Managed bit  │ 0       │ 0            │ 1                │
│ RA Other bit    │ 0       │ 1            │ 0                │
│ Kullanım yeri   │ Ev      │ Ev (tam oto) │ Kurumsal         │
└─────────────────┴─────────┴──────────────┴──────────────────┘
```

---

## 8. Interface ID Türetme: EUI-64 ve Rastgele

### 8.1 EUI-64 Yöntemi (Artık Son Kullanıcılarda Kullanılmıyor)

48 bit MAC adresinden 64 bit Interface ID türetilir:

```
MAC adresi: FC:99:47:75:CE:E0
                │
                ▼
Adım 1: MAC'i iki 24 bitlik parçaya böl
  FC:99:47   |   75:CE:E0

Adım 2: Ortaya sabit FFFE ekle
  FC:99:47 : FF:FE : 75:CE:E0

Adım 3: İlk byte'ın 7. bitini ters çevir (0→1 veya 1→0)
  FC = 1111 1100
  7. bit = 0 → 1 yap
  FE = 1111 1110

Sonuç Interface ID: FE:99:47:FF:FE:75:CE:E0
Kısaltılmış:         fe99:47ff:fe75:cee0
```

**Neden artık kullanılmıyor?**
```
Sorun: Host kısmı her zaman aynı kalıyor!
  Eve git       → aynı host kısmı
  İşe git       → aynı host kısmı
  Kafeye git    → aynı host kısmı

Sonuç: Kişi takip edilebilir! 
  "Gökhan bugün saat 09:00'da ofiste, 13:00'da kafede..." 
→ Windows 7'den itibaren terk edildi.
```

### 8.2 Rastgele Oluşturma (Günümüzde Kullanılan)

```
İşletim sistemi host kısmını tamamen rastgele üretir.

Çakışma ihtimali: 1 / 2^64 = 1 / 18,446,744,073,709,551,616
→ Milli piyangodan büyük ödül kazanmaktan çok çok daha zor!

Yine de DAD (Duplicate Address Detection) koruması var:
  Bilgisayar, seçtiği adresi kendi kendine "sorgular"
  Cevap gelirse → çakışma var, yeni adres üret
  Cevap gelmezse → adres benzersiz, kullan!

Windows ipconfig çıktısı (rastgele):
  IPv6 Address: 2001:db8:acad:1:50a5:8a35:a5bb:66e1
  Link-local  : fe80::50a5:8a35:a5bb:66e1
  Default GW  : fe80::1
```

---

## 9. IPv6 LLA için Dinamik Adresleme

Her IPv6 arabiriminin bir LLA'sı **olması zorunludur**.

```
LLA oluşturma akışı:
  IPv6 arabirimi aktive edilir
           │
           ▼
  fe80::/10 prefixini kullan
           │
           ▼
  Interface ID → EUI-64 VEYA Rastgele oluştur
           │
           ▼
  LLA adresi hazır!
  Örnek: fe80::fc99:47ff:fe75:cee0 (EUI-64)
  Örnek: fe80::50a5:8a35:a5bb:66e1 (Rastgele)
```

Cisco router'da LLA doğrulama:
```bash
R1# show ipv6 interface brief
GigabitEthernet0/0/0 [up/up]
    FE80::7279:B3FF:FE92:3640     ← LLA (EUI-64 ile otomatik)
    2001:DB8:ACAD:1::1            ← GUA (statik verilmiş)
```

---

## 10. IPv6 Multicast Adresleri

### 10.1 Multicast Aralığı

```
ff00::/8 aralığı → Tüm IPv6 Multicast adresleri

NOT: Multicast adresler SADECE hedef adres olabilir, kaynak adres olamaz!
```

### 10.2 Well-Known (İyi Bilinen) Multicast Adresleri

```
ff02::1  →  All-Nodes Multicast   = Tüm IPv6 cihazlar
            (IPv4'teki 255.255.255.255'e en yakın karşılık)

ff02::2  →  All-Routers Multicast = Tüm IPv6 router'lar
            (RS mesajı buraya gider: "Ağda router var mı?")
```

**IPv4 Broadcast vs IPv6 Multicast farkı:**
```
IPv4 Broadcast (255.255.255.255):
  Switch filtre yapamaz, herkese gider → Kontrol yok

IPv6 ff02::1 (All-Nodes Multicast):
  Switch filtreleyebilir → Yönetici şekillendirebilir
  → IPv6'da ağ yöneticisinin gücü artmış!
```

### 10.3 Solicited-Node Multicast Adresi

ARP Sorgusu (Neighbor Solicitation) herkese gitmek yerine, ilgili cihaza özel bir multicast adrese gider:

```
Adres formatı:
  ff02::1:ff  +  [IP adresinin son 6 hex hanesi]

Örnek:
  GUA: 2001:DB8::00AB:0002/64
  Son 6 hane: AB:0002

  Solicited-Node Multicast: ff02::1:ffAB:0002

Nasıl çalışır:
  1. Bilgisayar kendi Solicited-Node adresini switch'e "kaydeder"
  2. NS (Neighbor Solicitation) bu adrese gönderilir
  3. Switch sadece ilgili bilgisayara iletir (herkese değil!)
```

```
Akış diyagramı:
  A, B'ye ping atacak, B'nin MAC'ini öğrenmek istiyor
                │
                ▼
  A, "ff02::1:ffAB:0002" adresine NS gönderir
                │
                ▼
         Switch bu paketi alır
                │
         ┌──────┴──────┐
         ▼             ▼
     B alır         C almaz
   (ff02::1:ffAB:0002  (farklı solicited-node
    B'ye ait)           adresi var)
         │
         ▼
   B, NA ile MAC adresini A'ya bildirir
```

---

## 11. IPv6 Subnetting

### 11.1 IPv6'da Subnetting Mantığı

IPv4'teki karmaşık bit hesaplamalarına gerek yok! IPv6 subnetting için tasarlandı.

```
Tipik kurumsal atama:
┌──────────────────────────────────────────────────────────────┐
│  ISP sana /48 verir:  2001:A98:8000::/48                     │
│                                                              │
│  ┌────────────────┬──────────┬──────────────────────┐        │
│  │  Global Route  │ Subnet   │    Interface ID       │        │
│  │    Prefix      │    ID    │                       │        │
│  │    48 bit      │  16 bit  │       64 bit          │        │
│  │  (ISP verir)   │(Sen kul) │  (Cihaz oluşturur)   │        │
│  └────────────────┴──────────┴──────────────────────┘        │
└──────────────────────────────────────────────────────────────┘

16 bit Subnet ID ile:  2^16 = 65.536 adet /64 subnet!
```

### 11.2 Subnet Oluşturma Örneği

```
ISP verdi: 2001:A98:8000::/48

Sen subnet ID kısmını artırırsın:
  Subnet 0  →  2001:A98:8000:0000::/64  (Fenedibiyat Fakültesi)
  Subnet 1  →  2001:A98:8000:0001::/64  (İnşaat Fakültesi)
  Subnet 2  →  2001:A98:8000:0002::/64  (Mimarlık Fakültesi)
  ...
  Subnet FFFF → 2001:A98:8000:ffff::/64

Her subnet içinde 2^64 adet host adresi var!
```

### 11.3 Cisco Router'a Birden Fazla Subnet Yapılandırma

```bash
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 address 2001:db8:acad:2::1/64
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface serial 0/1/0
R1(config-if)# ipv6 address 2001:db8:acad:3::1/64
R1(config-if)# no shutdown
```

---

## 12. Komut Özeti

### Cisco IOS Komutları

```bash
# IPv6 adres atama
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64

# LLA manuel atama
R1(config-if)# ipv6 address fe80::1:1 link-local

# IPv6 routing aktive etme
R1(config)# ipv6 unicast-routing

# IPv6 arabirim özetini görüntüle
R1# show ipv6 interface brief

# IPv6 routing tablosu
R1# show ipv6 route

# Komşu tablosu (ARP'a karşılık)
R1# show ipv6 neighbors
```

### Windows Komutları

```powershell
# IPv6 adresi görüntüle
ipconfig

# Tüm IPv6 adresleri (LLA dahil)
ipconfig /all

# IPv6 ping
ping ::1                           # loopback
ping 2001:db8:acad:1::10           # GUA ping
ping fe80::1%12                    # LLA ping (zone ID ile)
```

---

## 13. Modül Özeti

```
┌─────────────────────────────────────────────────────────────────────┐
│                      MODÜL 12 – ÖZET                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  IPv6 neden?    → IPv4 tükendi, 128 bit → 340 undecilyon adres      │
│                                                                     │
│  Geçiş          → Dual Stack / Tunneling / NAT64 Translation        │
│                                                                     │
│  Format         → 8 hextet × 16 bit = 128 bit, hex, ":" ayraç      │
│  Kural 1        → Baştaki sıfırlar atlanır                          │
│  Kural 2        → :: ile sıfır blokları özetlenir (SADECE 1 kez!)   │
│                                                                     │
│  GUA            → 2000::/3, İnternete çıkış, public IP gibi        │
│  LLA            → fe80::/10, Aynı ağda zorunlu, router geçemez     │
│  ULA            → fc00::/7, Kapalı kurumsal ağlar                   │
│  Loopback       → ::1/128, Kendine ping                             │
│  Multicast      → ff00::/8, Broadcast yok, multicast var            │
│                                                                     │
│  Prefix         → Subnet mask yerine /64 kullanılır                 │
│  Interface ID   → Host kısmı (64 bit), EUI-64 veya rastgele        │
│                                                                     │
│  SLAAC          → IP kendisi oluşturur, DNS elle — stateless        │
│  SLAAC+DHCP     → IP kendisi, DNS DHCPv6'dan — stateless           │
│  Stateful DHCP  → Her şey DHCPv6'dan, kayıt tutulur                │
│                                                                     │
│  EUI-64         → MAC'ten Interface ID türetme (artık kullanılmaz)  │
│  Rastgele       → Windows Vista+ varsayılan, gizlilik için          │
│  DAD            → Duplicate Address Detection, çakışma önleme      │
│                                                                     │
│  ff02::1        → All-nodes multicast (tüm IPv6 cihazlar)          │
│  ff02::2        → All-routers multicast (tüm router'lar)           │
│  Solicited-Node → ff02::1:ff + son 6 hex (ARP optimize versiyonu)  │
│                                                                     │
│  Subnetting     → /48 prefix + 16 bit Subnet ID = /64 → kolay!     │
│  65.536 subnet  → 16 bit Subnet ID ile mümkün                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Temel Kurallar

> 1. **IPv6'da Broadcast yoktur** — ff02::1 (all-nodes multicast) kullanılır.
> 2. **IPv6'da NAT yoktur** — Her cihazın gerçek global adresi olur.
> 3. **LLA zorunludur** — Her IPv6 arayüzünde mutlaka bir fe80:: adresi bulunur.
> 4. **`::` sadece bir kez kullanılabilir** — Aksi hâlde adres belirsizleşir.
> 5. **Baştaki sıfırlar atlanır, sondaki sıfırlar atlanamaz.**
> 6. **Önerilen prefix: /64** — İlk 64 bit network, son 64 bit host.
> 7. **Bilgisayar önce RS gönderir** — Router'ı keşfeder, sonra IP alır.
> 8. **EUI-64 artık son kullanıcılarda kullanılmaz** — Rastgele tercih edilir.

---

*CCNA1 – Introduction to Networks v7.0 | Modül 12: IPv6 Adresleme*
