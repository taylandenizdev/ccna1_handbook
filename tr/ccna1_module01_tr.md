# 🌐 Network Temelleri Handbook



---

## İçindekiler

- [1. Network Nedir?](#1-network-nedir)
- [2. Client-Server ve Peer-to-Peer Modelleri](#2-client-server-ve-peer-to-peer-modelleri)
- [3. Network Bileşenleri](#3-network-bileşenleri)
- [4. Network Tipleri](#4-network-tipleri)
- [5. Topoloji Diyagramları](#5-topoloji-diyagramları)
- [6. ISP, Intranet, Extranet](#6-isp-intranet-extranet)
- [7. Güvenilir Ağ Kriterleri](#7-güvenilir-ağ-kriterleri)
- [8. Modern Trendler](#8-modern-trendler)
- [9. İnternet Erişim Teknolojileri](#9-i̇nternet-erişim-teknolojileri)
- [10. Ağ Güvenliği Temelleri](#10-ağ-güvenliği-temelleri)
- [11. CIA Üçgeni](#11-cia-üçgeni)
- [12. CCNA Sertifikasyon Yolu](#12-ccna-sertifikasyon-yolu)
- [13. Terimler Sözlüğü](#13-terimler-sözlüğü)

---

## 1. Network Nedir?

> **İki ya da daha fazla cihazın haberleştiği ortama NETWORK (Ağ) denir.**

Ağ büyüklüğü fark etmez — çalışma prensipleri tümüyle aynıdır:

| Ağ Türü | Örnek |
|---------|-------|
| Ev ağı (Small Home Network) | 2–5 cihaz |
| Küçük ofis / ev ofisi (SOHO) | 10–50 cihaz |
| Kampüs | Binalar arası |
| Kurumsal | Dünya geneli şubeler |

**İnternet büyüme milestoneları:**

| Dönem | Etken | Bağlı Cihaz |
|-------|-------|-------------|
| Sabit bilgisayar | Masaüstü PC | 200 milyon |
| Mobilite / BYOD | Laptop, akıllı telefon | 10 milyar |
| IoT — Cihazlar Çağı | Akıllı cihazlar, sensörler | 30 milyar (2020) |
| Internet of Everything | İnsan, süreç, veri, nesne | ~50 milyar (2026 tahmini) |

---

## 2. Client-Server ve Peer-to-Peer Modelleri

### 2.1 Client-Server

| Taraf | Tanım | Örnek |
|-------|-------|-------|
| **Server** | Sunucu yazılımıyla hizmet veren | Web sunucusu, dosya sunucusu, e-posta sunucusu |
| **Client** | Hizmet alan | Tarayıcı, masaüstü uygulaması |

Her hizmet ayrı sunucu yazılımı gerektirir. Web hizmeti sunmak için web sunucusu yazılımı şarttır.

### 2.2 Peer-to-Peer (P2P)

Her cihaz aynı anda hem server hem client olabilir.

| | Avantajlar | Dezavantajlar |
|-|-----------|--------------|
| | Kurulumu kolay | Merkezi yönetim yok |
| | Daha az karmaşık | O kadar güvenli değil |
| | Daha düşük maliyet | Ölçeklenebilir değil |
| | Basit görevler için uygun (dosya/yazıcı paylaşımı) | Daha yavaş performans |

> ⚠️ Sunucu olmak = Dışarıdan trafik kabul etmek = Güvenlik riski + CPU/RAM/Disk tüketimi. Büyük kurumlarda P2P servisleri **kapatılır**.

---

## 3. Network Bileşenleri

Tüm ağlar üç temel kategoriden oluşur:

```
┌─────────────────┐   ┌──────────────────────┐   ┌──────────────────┐
│   Uç Cihazlar   │   │     Ara Cihazlar      │   │      Medya       │
│  (End Devices)  │   │ (Intermediate Devices)│   │                  │
│                 │   │                      │   │                  │
│ Masaüstü/Laptop │   │ Switch (kablolu LAN)  │   │ Bakır (UTP)      │
│ Sunucu          │   │ Access Point (WiFi)   │   │ Fiber Optik      │
│ IP Telefon      │   │ Router (WAN/internet) │   │ Kablosuz         │
│ IP Kamera       │   │ Firewall              │   │                  │
│ Yazıcı          │   │ Multilayer switch     │   │                  │
│ IoT cihazı      │   │ Modem                 │   │                  │
│ (her birinde NIC│   │                      │   │                  │
└─────────────────┘   └──────────────────────┘   └──────────────────┘
```

---

### 3.1 End Device (Uç Cihaz / Host)

> Paketin oluştuğu veya sonlandığı cihazdır.

**Örnekler:**
- Masaüstü / Laptop / Sunucu
- IP Telefon & IP Kamera
- Yazıcı, wireless tablet, TelePresence uç noktası
- El terminali (fabrika/depo barkod okuyucusu, mobil ödeme terminali)
- IoT cihazları (akıllı ampul, termostat, buzdolabı...)

| Terim | Anlam |
|-------|-------|
| **Server** | Hizmet veren end device |
| **Client** | Hizmet alan end device |
| **Host** | Genel "uç cihaz" karşılığı |

---

### 3.2 NIC Kart (Network Interface Card)

> Bilgisayarın ağa veri giriş/çıkışını sağlayan karttır.

| Tür | Açıklama |
|-----|----------|
| **Kablolu NIC** | Ethernet portu; genellikle anakarta entegre |
| **Kablosuz NIC** | WiFi; cep telefonlarına gömülü |
| **USB NIC** | Harici, USB üzerinden takılır |

- Her NIC'in dünyada eşsiz bir **MAC adresi** vardır.
- Her NIC arayüzüne ayrı bir **IP adresi** atanır.
- Hem kablolu hem kablosuz varsa → 2 NIC = 2 MAC = 2 IP.

> Wireshark ilk açıldığında sorar: *"Hangi NIC'i dinlemek istiyorsunuz?"*

---

### 3.3 Ara Ağ Cihazları — Görevleri

Ara ağ cihazları şu görevlerin bir kısmını veya tamamını yerine getirir:

- Veri sinyallerini yeniden oluşturur ve iletir
- Ağ ve internetwork üzerindeki yollar hakkında bilgi tutar
- Diğer cihazlara hata ve iletişim başarısızlıklarını bildirir
- Bir bağlantı başarısız olduğunda veriyi alternatif yollardan yönlendirir
- Önceliklere göre mesajları sınıflandırır ve yönlendirir (QoS)
- Güvenlik ayarlarına göre veri akışına izin verir veya reddeder

#### Switch (Anahtar)

> Uç cihazları **kablolu** olarak yerel ağa bağlayan ara cihazdır.

| Özellik | Detay |
|---------|-------|
| **Portlar** | 5 / 8 / 16 / 24 / 48 portlu modeller yaygın |
| **Port = Interface** | Her bağlantı noktası numaralıdır |
| **Şematik sembol** | 3D: tarama çizgili silindir | 2D: kare + oklar |

#### Access Point (Kablosuz Erişim Noktası)

> Uç cihazları **kablosuz** olarak yerel ağa bağlayan ara cihazdır.

| Kullanım | Detay |
|----------|-------|
| **Ev** | ADSL/Fiber modemlerin içine gömülü |
| **Kurumsal** | Ayrı cihaz; switch'e kablo ile bağlanır |
| **Mimari** | Kablosuz cihaz → AP → Kablo → Switch → Ağ |

> 📌 WiFi mobilite sağlar ama kapasite ve güvenlik açısından **kablo her zaman üstündür**.

#### Router (Yönlendirici)

> Farklı ağları birbirine bağlayan ara cihazdır. İnternetin temel yapı taşıdır.

- **"İnternet"** = *Inter-network* → Router olmadan internet yok.

| Kural | Açıklama |
|-------|----------|
| **Kural 1** | Her interface farklı bir ağa bağlanmak zorundadır |
| **Kural 2** | Aynı ağı aynı router'a iki kez bağlayamazsınız |
| **Çıkarım** | Router'ın her interface'i ayrı bir network'tür |
| **Şematik sembol** | Silindirik ok ikonu; basit çizimde daire |

**Ev modemi = Router + Switch + Access Point + Modem = 4 cihaz tek kutuda**

#### Firewall

> Ağ geçişlerinde güvenlik denetimi yapan cihazdır.

- WAN ile LAN arasına konumlanır.
- Dışarıdan gelen trafik önce firewall'a çarpar.
- İç tehdide karşı zayıftır (bkz. [Bölüm 10](#10-ağ-güvenliği-temelleri)).

#### Modem

> ISP'den gelen bağlantı sinyalini router'ın anlayacağı formata dönüştüren cihazdır.

---

### 3.4 Medya (Bağlantı Ortamı)

| Tür | İletim Yöntemi | Notlar |
|-----|---------------|--------|
| **Bakır Kablo (UTP)** | Elektrik sinyali | Ethernet kablosu; Cat5e, Cat6, Cat6a |
| **Fiber Optik** | Işık sinyali (lazer) | Uzun mesafe, yüksek hız; single-mode / multi-mode |
| **Kablosuz** | Elektromanyetik dalga | WiFi (hava), uydu (boşluk) |

---

## 4. Network Tipleri

### 4.1 LAN — Local Area Network

> Aynı lokasyondaki cihazların oluşturduğu ağdır.

| Özellik | Detay |
|---------|-------|
| **Temel cihaz** | Switch (kablolu) veya Access Point (kablosuz) |
| **Standartlar** | Ethernet IEEE 802.3 (kablolu), IEEE 802.11 (kablosuz) |
| **Yönetim** | Tek bir kuruluş veya kişi tarafından yönetilir |
| **Hız** | LAN içinde yüksek bant genişliği |

### 4.2 WAN — Wide Area Network

> Bir ISP altyapısıyla birden fazla LAN'ı bağlayan ağdır.

| Özellik | Detay |
|---------|-------|
| **Neden?** | Farklı lokasyonlardaki LAN'ları birbirine bağlamak |
| **Altyapı sahibi** | ISP — bir veya daha fazla servis sağlayıcı tarafından yönetilir |
| **Hız** | Genellikle LAN'lar arasında daha düşük hız |
| **WAN teknolojileri** | ADSL, kablonet, fiber (Metro Ethernet), ATM, MPLS |

**Örnek yapı:**
```
Şube A LAN → Router → [ISP / WAN] → Router → Şube B LAN
                     (toplam 3 ayrı ağ)
```

**MAN (Metropolitan Area Network):** Aynı şehir içi WAN. WAN altında değerlendirilebilir.

### 4.3 İnternet Nasıl Oluşur?

```
Müşteri LAN
     ↓
ISP Router  ←→  ISP-A  ←→  ISP-B  ←→  ISP-C
                  ↕             ↕
            ISP-D (AB)      ISP-E (ABD)
                  ↕
            Daha fazla ISP...
                  ↕
  Bağla → Bağla → Bağla → 1 milyon+ ağ = İNTERNET
```

> İnternet herhangi bir kişi veya gruba ait değildir.  
> Yönetim kuruluşları: **IETF** (standartlar), **ICANN** (adresleme), **IAB** (mimari).

---

## 5. Topoloji Diyagramları

**Topoloji**, ağın haritasıdır — hangi cihaz kime nasıl bağlı.

| Tür | Gösterir |
|-----|---------|
| **Fiziksel topoloji** | Ara cihazların ve kablo kurulumunun fiziksel konumunu |
| **Mantıksal topoloji** | Cihazları, bağlantı noktalarını ve ağın adresleme düzenini |

**Standart şematik semboller:**

| Cihaz | Sembol |
|-------|--------|
| Router | Oklu silindir (3D) veya daire (2D) |
| LAN Switch | Yassı taralı silindir veya oklu kare |
| Kablosuz router / AP | Router sembolü + kablosuz dalgalar |
| Multilayer switch | Güneş / yıldız ikonu |
| Firewall | Tuğla duvar ikonu |
| Uç cihaz (PC) | Monitör ikonu |
| Sunucu | Uzun kutu / rack birimi ikonu |

> Herhangi bir ağ diyagramında: **daireler = router**, **oklu kareler = switch**. Bu ikisini ezberleyin.

---

## 6. ISP, Intranet, Extranet

```
┌──────────────────────────────────────────────────────────────┐
│               İNTERNET (Tüm Dünya — herkese açık)             │
│  ┌────────────────────────────────────────────────────────┐  │
│  │        EXTRANET (Tedarikçiler, Müşteriler, Ortaklar)   │  │
│  │  ┌──────────────────────────────────────────────────┐  │  │
│  │  │          INTRANET (Yalnızca Şirket içi)          │  │  │
│  │  └──────────────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

| Kavram | Tanım | Örnek |
|--------|-------|-------|
| **ISP** | Telekom lisanslı; internet ve özel devre sağlar | Türk Telekom, Vodafone |
| **İnternet** | Herkese açık (public) — 1 milyon+ ağ | Web, e-posta, bulut |
| **Intranet** | Kuruma ait, dışarıya kapalı ağ topluluğu | Bankanın tüm şube ağı |
| **Extranet** | İş ortaklarını bağlayan inter-kurumsal özel ağ | Havayolu + bilet acentası |

- **Intranet sunucuları** internetten erişilemez (ARGE, İK sistemleri vb.).
- **Extranet** bağlantıları genellikle **VPN** kullanır.

---

## 7. Güvenilir Ağ Kriterleri

> **Güvenilir ağ** = 7/24 istenen kalitede hizmet veren ağdır.

Ağ mimarisi, kullanıcı beklentilerini karşılamak için dört temel özelliği sağlamalıdır:

### 7.1 Fault Tolerance (Hataya Tolerans)

> Tek bir noktadaki arıza tüm sistemi çökertmemelidir.

- Güvenilir ağlar **paket anahtarlamalı ağ** uygulayarak yedeklilik sağlar.
- Paket anahtarlama: trafik paketlere bölünür ve ağ üzerinden yönlendirilir — her paket teorik olarak farklı bir yol izleyebilir.
- Bu, adanmış devre kuran devre anahtarlamalı ağlarda mümkün değildir.

**Single Point of Failure:** Tek bir arızanın tüm servisi durdurması → tasarımda önlenmeli.

### 7.2 Scalability (Ölçeklendirilebilirlik)

> Ağ, mevcut kullanıcıları etkilemeden yeni kullanıcı ve uygulamaları desteklemek için hızla büyüyebilmelidir.

- Ağ tasarımcıları ölçeklenebilirliği sağlamak için kabul görmüş **standartlar ve protokolleri** izler.
- Cloud altyapısı anlık büyüme ihtiyaçlarını karşılar.

### 7.3 QoS — Quality of Service (Servis Kalitesi)

> Hizmetin ulaşması yetmez; **zamanında ve kaliteli** ulaşması şarttır.

- QoS, tüm kullanıcılar için içeriğin güvenilir teslimini sağlamak için kullanılan birincil mekanizmadır.
- QoS politikasıyla router, veri ve ses trafiğinin akışını daha kolay yönetir.

| Metrik | Standart | Kaynak |
|--------|----------|--------|
| **Ses gecikmesi** | Maks. **150 ms** | ITU standardı |
| **Aşılırsa** | Konuşma karışır; doğal akış bozulur | — |
| **Web sayfaları** | Daha yüksek gecikmeye toleranslı → düşük QoS önceliği | — |
| **IP telefon** | Düşük gecikme gerektirir → yüksek QoS önceliği | — |

### 7.4 Network Security (Ağ Güvenliği)

Ağ güvenli değilse yukarıdaki üç kriter anlamsız hale gelir. (bkz. [Bölüm 10](#10-ağ-güvenliği-temelleri))

---

## 8. Modern Trendler

### 8.1 BYOD — Kendi Cihazını Getir

> Çalışanların kişisel cihazlarını kuruma getirip ağa bağlaması.

BYOD, son kullanıcılara bilgilere erişmek ve iletişim kurmak için kişisel araçları kullanma özgürlüğü sağlar: laptoplar, netbooklar, tabletler, akıllı telefonlar, e-okuyucular.

| | Detay |
|-|-------|
| **Avantaj** | Daha fazla fırsat ve esneklik; firma cihaz maliyeti düşer |
| **Risk** | Kişisel cihazdaki zararlı yazılım kurumsal ağa sıçrayabilir |
| **Çözüm** | Misafir WiFi (izole) veya yalnızca şirket cihazlarına izin |

BYOD: cihazın sahibinin, tipinin ve kullanıldığı yerin **sınırsız** olduğu senaryodur.

### 8.2 Online İşbirliği (Collaboration)

- Zoom, Microsoft Teams, Cisco Webex, Google Meet, Cisco TelePresence
- Video, etkili işbirliği için **kritik bir gereklilik** haline geliyor
- Gerçek zamanlı ses/video → **yüksek bant genişliği + düşük gecikme** şart (QoS kritik)

### 8.3 Bulut Bilişim (Cloud Computing)

> Sunucuların kendi lokasyonunuzda değil, başka bir data center'da barındırılması ve internet üzerinden erişilmesi.

| Model | Detay |
|-------|-------|
| **On-Premise (Eski)** | Şirket içi sunucu; klima, UPS, jeneratör, yangın sistemi gerektirir |
| **Cloud (Yeni)** | Kapasite kiralama; bakım hizmet sağlayıcıya ait |
| **Network etkisi** | İnternet kesintisizliği operasyonun **kritik bağımlılığı** haline gelir |
| **Ölçekleme** | Sanal sunucular ihtiyaç anında büyür/küçülür |

**Dört tür bulut:**

| Tür | Açıklama |
|-----|----------|
| **Public Cloud** | Genel kullanıma sunulmuş; kullanım başına ödeme veya ücretsiz |
| **Private Cloud** | Belirli bir kurum için tasarlanmış (örn. devlet) |
| **Hybrid Cloud** | İki veya daha fazla bulut türünden oluşur; her parça ayrı kalır |
| **Custom Cloud** | Belirli bir endüstri (sağlık, medya) için oluşturulmuş; public veya private olabilir |

### 8.4 Akıllı Ev Teknolojisi

> Her gün kullanılan cihazların birbiriyle konuşmasını sağlayan, giderek büyüyen teknoloji trendi.

- Fırınlar takvim uygulamanızla iletişerek akşam kaçta evde olacağınızı bilebilir
- Akıllı ev teknolojisi şu anda bir ev içindeki tüm odalar için geliştirilmektedir

### 8.5 Powerline Networking

> Mevcut elektrik tesisatı üzerinden LAN'a bağlantı sağlar.

- Kablosuz erişim noktaları evdeki tüm cihazlara ulaşamadığında kullanışlıdır
- Cihazlar, standart bir powerline adaptörü aracılığıyla bir elektrik prizine takılarak LAN'a bağlanır
- Veri, belirli frekanslarda elektrik hattı üzerinden gönderilir

### 8.6 Kablosuz Geniş Bant (Wireless Broadband)

> DSL ve kabloya ek olarak ev ve küçük işletmeleri internete bağlamak için kullanılan alternatif.

- **WISP** (Kablosuz İnternet Servis Sağlayıcısı) — aboneleri erişim noktalarına bağlar; kırsal bölgelerde yaygın
- Akıllı telefonların kullandığı hücresel teknolojiyi kullanır
- Evin dışına kurulan anten, içerideki cihazlar için kablosuz veya kablolu bağlantı sağlar

---

## 9. İnternet Erişim Teknolojileri

### Ev / Küçük Ofis

| Teknoloji | Açıklama |
|-----------|----------|
| **ADSL / VDSL** | Telefon hattı üzerinden; yaygın ev teknolojisi |
| **Broadband cable (Kablonet)** | Kablo TV altyapısı üzerinden |
| **Fiber (GPON)** | Gigabit Pasif Optik Ağ; yüksek hız ve stabilite |
| **4G / 5G (hücresel)** | Mobil; taşınabilir internet |
| **Dial-up** | 50 Kbps; tarihsel; günümüzde kullanılmıyor |
| **Uydu (GEO)** | ~40.000 km yükseklik; yüksek gecikme; şantiye/acil |
| **Starlink (LEO)** | ~400 km yükseklik; düşük gecikme; global yaygınlaşıyor |
| **Power Line / HomePlug** | Elektrik prizi üzerinden veri |
| **Kablosuz geniş bant (WISP)** | Hücresel anten üzerinden sabit kablosuz |

### Kurumsal / İşletme

| Teknoloji | Açıklama |
|-----------|----------|
| **Metro Ethernet** | En yaygın WAN çözümü; fiber üzerinden Ethernet |
| **IP/MPLS** | Şube bağlantısı için ISP servisi |
| **Business DSL** | Son kullanıcıdan farklı altyapı; SLA garantili |
| **Dedicated leased lines** | Özel noktan noktaya devre; garantili bant genişliği |
| **Uydu** | Karasal seçeneğin olmadığı uzak/denizaşırı lokasyonlar |

### IPv4 → IPv6 Geçişi

```
IPv4 adres uzayı      :  ~4 milyar
İnternete bağlı cihaz:  ~50 milyar
                      ─────────────────
                      ADRES TÜKENDİ
```

| Özellik | IPv4 | IPv6 |
|---------|------|------|
| Adres uzayı | ~4 milyar | 340 undesiyon |
| NAT zorunluluğu | Evet | Hayır |
| IoT direkt erişim | Zor | Kolay |
| Güvenlik mimarisi | Protokolden ayrı | Protokole entegre |

> ⚠️ IPv6 güvenlik mimarisi IPv4'ten farklıdır. Kurumların geçiş öncesinde test ortamı kurması gerekir.

---

## 10. Ağ Güvenliği Temelleri

### 10.1 Güvenlik Tehditleri

**Dış tehditler:**

| Tehdit | Açıklama |
|--------|----------|
| Virüsler, solucanlar (worm), Truva atları | Sistemlere zarar veren veya kontrol eden zararlı yazılımlar |
| Casus yazılım (spyware) ve reklam yazılımı (adware) | Kullanıcı verisini izinsiz toplar |
| Sıfır gün saldırıları (zero-day) | Yama çıkmadan önce bilinmeyen açıkları istismar eder |
| Tehdit aktörü saldırıları | Yetenekli saldırganların hedefli atakları |
| Hizmet reddi (DoS) | Ağ kaynaklarını aşırı yükleyerek hizmet dışı bırakır |
| Veri önleme (data interception) ve hırsızlık | İletim sırasında veri ele geçirme |
| Kimlik çalma (identity theft) | Kimlik bilgilerini çalarak kullanıcı taklidi yapma |

**İç tehditler:**

| Tehdit | Açıklama |
|--------|----------|
| Kaybolan veya çalınan cihazlar | Fiziksel erişim veri sızıntısına yol açar |
| Çalışanların kazara kötüye kullanımı | Yanlış yapılandırma, phishing tıklamaları |
| Kötü niyetli çalışanlar | Kasıtlı veri hırsızlığı veya sabotaj |

### 10.2 İç Tehdit mi? Dış Tehdit mi?

**Sınav cevabı:** İç tehdit daha tehlikeli kabul edilir.  
**Gerçekte en tehlikelisi:** İçeride malware konumlandırılmış, dışarıdan yönetilen ataktır.

#### İç Tehdit — Saldırgan Avantajları

| Avantaj | Açıklama |
|---------|----------|
| Firewall yok | Direkt iç ağda |
| Yüksek bant genişliği | 1 Gbps+ LAN hızları |
| Düşük gecikme | Milisaniyenin altı |
| Kimlik bilgileri | Username / password / IP bilinebilir |
| Yetki | Sunuculara yetkili erişim mümkün |

#### İç Tehdit — Dezavantajlar

- Log kaydı — her hareket izlenebilir
- Fiziksel tespit mümkün
- Hukuki yaptırım hızlı: işten çıkarılma, dava, hapis

### 10.3 Güvenlik Çözümleri

**Ev / küçük ofis:**
- Son cihazlara virüsten koruma ve casus yazılım önleme yazılımı yüklenmelidir
- Ağa yetkisiz erişimi engellemek için firewall filtresi

**Büyük ağlar — ek gereksinimler:**

| Araç | Görevi |
|------|--------|
| **Özel firewall sistemleri** | Donanım tabanlı çevre güvenliği |
| **ACL** | Access Control List — router/switch üzerinde erişim kuralları |
| **IPS** | Intrusion Prevention System — saldırı tespit ve engelleme; mavi takımın temel cihazı |
| **VPN** | Şifreli güvenli tünel; uzak erişimde zorunludur |

> Ağ güvenliği çalışması, temel **anahtarlama (switching)** ve **yönlendirme (routing)** altyapısının net anlaşılmasıyla başlar.

> **VPN hakkında:** VPN ≠ "yasak site açma aracı". VPN = Güvenli şifreli ağ tüneli. Orta ve büyük ölçekli hiçbir kurum VPN'siz çalışamaz.

### 10.4 Güvenlik Dengesi

```
Güvenlik ↑  (çok sıkı)  →  Operasyonel kolaylık ↓
Güvenlik ↓  (çok gevşek)→  Saldırı yüzeyi ↑
              ──────────────────────────────────
              Denge kurumun risk iştahına göre belirlenir
```

### 10.5 Bütünleşen Ağlar (Converged Networks)

Eskiden bağımsız olan ağlar artık tek bir IP altyapısında birleşmiştir:

| Sistem | Eski Altyapı | Bugün |
|--------|-------------|-------|
| Telefon | Ayrı santral ve kablo | IP Telefon → Bilgisayar ağı |
| Güvenlik kameraları | Koaksiyel kablo, ayrı kayıt sistemi | IP Kamera → Bilgisayar ağı |
| Bina yönetimi | Ayrı kontrol sistemleri | IP üzerinden |
| Yayıncılık | Ayrı yayın ağı | IP üzerinden |

> **Sonuç:** Ağ çöktüğünde **tek noktadan tüm sistemler durur**. Bu, ağ güvenilirliğini kritik önemde kılar.

---

## 11. CIA Üçgeni

> Bir dijital altyapının **güvenli** sayılabilmesi için üç koşulun birlikte sağlanması gerekir.

```
              Confidentiality (C) — Gizlilik
                      /\
                     /  \
                    /    \
                   /  CIA \
                  / Üçgeni \
                 /──────────\
  Integrity (I)            Availability (A)
     Bütünlük               Erişilebilirlik
```

| Harf | Kriter | Tanım | İhlal Örneği |
|------|--------|-------|-------------|
| **C** | Confidentiality (Gizlilik) | Veriye yalnızca yetkili erişebilir | ARGE dosyasına yetkisiz erişim |
| **I** | Integrity (Bütünlük) | Veri izinsiz değiştirilemez | Havale tutarının manipülasyonu |
| **A** | Availability (Erişilebilirlik) | İhtiyaç anında veriye erişilebilir | Ransomware — dosyalar şifrelendi |

**Kısaltma:** **C**onfidentiality · **I**ntegrity · **A**vailability → **CIA**

---

## 12. CCNA Sertifikasyon Yolu

**CCNA (Cisco Certified Network Associate)**, temel teknolojiler hakkında bilgi sahibi olduğunuzu gösterir ve yeni nesil teknolojiler için güncel kalmanızı sağlar.

**Yeni CCNA odak alanları:**
- IP temeli ve güvenlik konuları
- Kablosuz, sanallaştırma, otomasyon ve ağ programlanabilirliği

**Cisco sertifikasyon seviyeleri:**

```
Associate → Specialist → Professional (CCNP) → Expert (CCIE)
```

**DevNet sertifikasyonları** (Associate, Specialist, Professional seviyelerinde):
- Programlama yetkinliklerini valide eder

**Specialist sertifikasyonu:**
- İşinizdeki rol ve ilginize yönelik yetkinliklerinizi valide eder

> Kariyer kaynakları: [netacad.com](https://www.netacad.com) → Kariyer menüsü → Talent Bridge Eşleştirme Motoru

---

## 13. Terimler Sözlüğü

| Terim | Açıklama |
|-------|----------|
| **Network / Ağ** | İki veya daha fazla cihazın haberleştiği ortam |
| **Host / End Device** | Paketin kaynağı veya hedefi olan uç cihaz |
| **NIC** | Network Interface Card — ağ arayüz kartı |
| **MAC Adresi** | NIC'in donanımsal, dünyada eşsiz kimlik numarası |
| **IP Adresi** | Ağ üzerindeki mantıksal adres; yönlendirmede kullanılır |
| **Switch** | End device'ları kablolu yerel ağa bağlayan ara cihaz |
| **Access Point** | End device'ları kablosuz ağa bağlayan ara cihaz |
| **Router** | Farklı ağları birbirine bağlayan ara cihaz |
| **Firewall** | Ağ geçişinde güvenlik denetimi yapan cihaz |
| **Modem** | ISP sinyalini router'ın anlayacağı formata çeviren cihaz |
| **LAN** | Local Area Network — yerel alan ağı |
| **WAN** | Wide Area Network — geniş alan ağı |
| **MAN** | Metropolitan Area Network — şehir alan ağı |
| **SOHO** | Small Office / Home Office |
| **ISP** | Internet Service Provider — internet servis sağlayıcı |
| **İnternet** | 1 milyon+ ağdan oluşan herkese açık global ağ |
| **Intranet** | Kuruma ait özel networkler topluluğu |
| **Extranet** | İş ortaklarını bağlayan inter-kurumsal özel ağ |
| **Topoloji** | Ağın haritası — hangi cihaz kime nasıl bağlı |
| **Fiziksel topoloji** | Cihazların ve kabloların fiziksel konumunu gösterir |
| **Mantıksal topoloji** | Cihazları, portları ve adresleme düzenini gösterir |
| **Paket** | Ağ üzerinde taşınan veri birimi |
| **Paket anahtarlama** | Trafik paketlere bölünür; her paket farklı yol izleyebilir |
| **Devre anahtarlama** | İletişim süresince adanmış devre kurulur |
| **Bant Genişliği** | Ağın veri taşıma kapasitesi (Mbps, Gbps) |
| **Gecikme / Delay** | Paketin kaynaktan hedefe ulaşma süresi (ms) |
| **Port (Fiziksel)** | Switch/router üzerindeki bağlantı noktası |
| **Port (Yazılımsal)** | Uygulama katmanı haberleşme kanalı numarası |
| **QoS** | Quality of Service — servis kalitesi |
| **SLA** | Service Level Agreement — servis kalite sözleşmesi |
| **Fault Tolerance** | Tek nokta arızası sistemi çöküşe sürüklemez |
| **Scalability** | Ölçeklendirilebilirlik |
| **Redundancy** | Yedeklilik — kritik bileşenlerin yedeklenmesi |
| **Single Point of Failure** | Tek noktadaki arızanın tüm sistemi durdurması |
| **BYOD** | Bring Your Own Device — kendi cihazını getir |
| **Cloud** | Sunucuların uzak data center'da barındırılması |
| **Public Cloud** | Genel kullanıma açık; ücretli veya ücretsiz |
| **Private Cloud** | Belirli bir kuruma özel |
| **Hybrid Cloud** | İki veya daha fazla bulut türünün kombinasyonu |
| **VPN** | Virtual Private Network — şifreli güvenli tünel |
| **IDS** | Intrusion Detection System — saldırı tespit |
| **IPS** | Intrusion Prevention System — saldırı engelleme |
| **ACL** | Access Control List — erişim kontrol listesi |
| **DoS** | Denial of Service — hizmet reddi saldırısı |
| **Zero-day** | Bilinmeyen, yamlanmamış açığı hedef alan saldırı |
| **CIA Üçgeni** | Confidentiality + Integrity + Availability |
| **IPv4** | İnternet Protokolü v4 — ~4 milyar adres; tükenmiş |
| **IPv6** | İnternet Protokolü v6 — 340 undesiyon adres |
| **UTP** | Unshielded Twisted Pair — bakır ethernet kablosu |
| **Ethernet** | IEEE 802.3 — dünya standardı yerel ağ protokolü |
| **WLAN** | Wireless LAN — IEEE 802.11 |
| **IoT** | Internet of Things — Nesnelerin İnterneti |
| **CCNA** | Cisco Certified Network Associate |
| **CCIE** | Cisco Certified Internetwork Expert |
| **IETF** | Internet Engineering Task Force — internet standartları kuruluşu |
| **ICANN** | İnternet Alan Adları ve Numaraları Tahsis Kurumu |
| **IAB** | Internet Architecture Board — internet mimarisi kurulu |
| **Kabinet / Rack** | Network ve sunucu cihazlarının monte edildiği metal dolap |
| **On-Premise** | Sunucuların kurum lokasyonunda barındırılması |
| **Bütünleşen Ağ** | Telefon, kamera ve BT sistemlerinin tek IP altyapısında birleşmesi |
| **WISP** | Wireless Internet Service Provider — kablosuz internet sağlayıcı |
| **Powerline networking** | Mevcut elektrik tesisatı üzerinden veri iletimi |

---

*CCNA Introduction to Networks | Siber Güvenlik Operasyonları Hazırlık Programı*  
*Cisco CCNA1 ITN v7.0 — Modül 1 temel alınmıştır*
