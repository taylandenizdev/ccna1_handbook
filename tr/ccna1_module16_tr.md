# CCNA1 – Modül 16: Ağ Güvenliği Temelleri
> **Network Security Fundamentals**  
> CCNA1 · Introduction to Networks v7.0 (ITN)  


---

## 📋 Modül Hedefleri

| Konu | Amaç |
|------|-------|
| Güvenlik Tehditleri ve Açıkları | Ağ cihazlarında neden temel güvenlik önlemlerinin gerekli olduğunu açıklamak |
| Ağ Saldırıları | Güvenlik açıklarını ve saldırı türlerini belirlemek |
| Ağ Saldırılarını Azaltma | Genel azaltma tekniklerini tanımlamak |
| Cihaz Güvenliği | Güvenlik tehditlerini azaltmak için ağ aygıtlarını yapılandırmak |

---

## 16.1 Güvenlik Tehditleri ve Açıkları

### Tehdit Türleri

Bir ağa yapılan saldırılar yıkıcı olabilir; bilgi/varlık hasarı, zaman ve para kaybına yol açar. Saldırganlar ağa çeşitli yollarla erişebilir:

- Yazılım güvenlik açıkları
- Donanım saldırıları
- Kullanıcı adı/şifre tahmini

> 💡 **Tehdit Aktörü:** Yazılımı değiştirerek veya güvenlik açıklarını kullanarak yetkisiz erişim sağlayan kişi/gruplara denir.

### Tehdit aktörü ağa girdikten sonra ortaya çıkabilecek 4 tehdit türü:

```
┌─────────────────────────────────────────────────────┐
│                  4 TEMEL TEHDİT TÜRÜ                │
├───────────────────┬─────────────────────────────────┤
│ 🔓 Bilgi Hırsızlığı │ Kurumsal/kişisel verilerin çalınması │
│ 💾 Veri Kaybı/      │ Verilerin silinmesi, değiştirilmesi  │
│    Manipülasyon    │ veya şifrelenerek fidye istenmesi    │
│ 👤 Kimlik Hırsızlığı│ Kişisel verilerin ele geçirilmesi   │
│ 🚫 Hizmet Kesintisi │ Ağ/sistemlerin devre dışı bırakılması│
└───────────────────┴─────────────────────────────────┘
```

---

### Açık (Vulnerability) Türleri

**Güvenlik açığı:** Bir ağ veya cihazdaki zayıflık derecesidir. Yönlendiriciler, anahtarlar, sunucular ve güvenlik cihazları dahil her cihazda belirli düzeyde güvenlik açığı mevcuttur.

#### 3 Temel Güvenlik Açığı Kaynağı:

```
                    GÜVENLİK AÇIĞI KAYNAKLARı
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
   TEKNOLOJİK         YAPILANDIRMA       GÜVENLİK
   AÇIKLAR            AÇIKLARI           POLİTİKASI
                                         AÇIKLARI
  • TCP/IP proto.    • Zayıf parolalar   • Yazılı politika
    zayıflıkları    • Tahmin edilebilir   eksikliği
  • İşletim sistemi   sistem şifreleri  • Kimlik doğrulama
    açıkları        • Yanlış yapılandır-  sürekliliği yok
  • Ağ ekipmanı       ılmış servisler   • Mantıksal erişim
    açıkları        • Güvenli olmayan     kontrolleri yok
                      varsayılan        • Felaket kurtarma
                      ayarlar            planı yok
```

> ⚠️ **Not:** Protokol zayıflıkları (IP spoofing, ARP zehirlenmesi gibi) güncellemeyle kapatılamaz; bunlar için ek kontrol mekanizmaları gerekir.

---

### Fiziksel Güvenlik Tehditleri

Ağ kaynakları fiziksel olarak tehlikeye girerse, tehdit aktörü bu kaynakların kullanımını engelleyebilir.

| Tehdit Sınıfı | Örnekler |
|---------------|----------|
| **Donanım Tehditleri** | Sunucu, router, switch, kablolama tesisine fiziksel hasar |
| **Çevresel Tehditler** | Aşırı sıcaklık, aşırı nem (çok ıslak/kuru) |
| **Elektriksel Tehditler** | Voltaj yükselmeleri, elektrik kesintisi, koşulsuz güç (gürültü) |
| **Bakım Tehditleri** | Kötü kablolama, kritik yedek parça eksikliği, kötü etiketleme |

> 🏢 **Gerçek Dünya:** Sistem odası klimasının bozulması, sunucuların aşırı ısınarak kapanmasına yol açar. Fiziksel müdahale (kablo kesmek, cihaza zarar vermek) de bir siber tehdit sayılır!

---

## 16.2 Ağ Saldırıları

### Zararlı Yazılım Türleri (Malware)

**Malware** (Malicious Software – Kötü Amaçlı Yazılım): Verilere, ana bilgisayarlara veya ağlara zarar vermek için tasarlanmış kod/yazılımdır.

```
                        MALWARE TÜRLERİ
                              │
           ┌──────────────────┼──────────────────┐
           ▼                  ▼                  ▼
        VİRÜS              SOLUCAN           TRUVA ATI
       (Virus)              (Worm)         (Trojan Horse)
           │                  │                  │
  Kendini başka          Bağımsız çalışır,  Meşru görünür,
  programa ekler,        ana bilgisayar    diğer dosyalara
  dosyalara bulaşır      gerekmez,         bulaşmaz, uzaktan
  ve yayılır             ağ üzerinden      erişim sağlar
                         yayılır
```

| Özellik | Virüs | Solucan (Worm) | Truva Atı |
|---------|-------|----------------|-----------|
| Yayılma yöntemi | Dosyaya eklenir | Bağımsız, ağdan yayılır | Kullanıcı etkileşimi gerekir |
| Ana bilgisayar gereksinimi | Evet | Hayır | Hayır |
| Çoğalma | Diğer dosyalara bulaşır | Kendi kopyasını üretir | Üremez |
| Temel amacı | Bulaşmak/zarar vermek | Yayılmak/zarar vermek | Uzaktan erişim sağlamak |

> 💡 **Fidye Yazılımı (Ransomware):** Worm formatında çalışır; dosyaları şifreleyip fidye ister. Günümüzdeki zararlı yazılımların büyük çoğunluğu hem worm hem de trojan özelliklerini bir arada taşır.

---

### Ağ Saldırılarının 3 Ana Kategorisi

```
                    AĞ SALDIRI KATEGORİLERİ
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
      KEŞİF               ERİŞİM            HİZMET REDDİ
   SALDIRISI            SALDIRISI              (DoS)
  (Reconnaissance)      (Access)
         │                   │                   │
  Hedef hakkında        Yetkisiz             Ağ/sistem/
  bilgi toplama,        veri/sistem          servisi devre
  haritalama            erişimi              dışı bırakma
```

---

### 1) Keşif Saldırıları (Reconnaissance Attacks)

Sistemlerin, hizmetlerin veya güvenlik açıklarının keşfedilmesi ve haritalanması.

#### Pasif Keşif vs. Aktif Keşif:

```
PASİF KEŞİF                          AKTİF KEŞİF
──────────────                        ─────────────
Hedef sistemle doğrudan               Hedef sisteme trafik
trafik oluşturmadan bilgi             yaratarak bilgi toplama
toplama (fark edilme riski düşük)     (IDS tarafından algılanabilir)

Örnek:                                Örnek:
• DNS sorguları (nslookup)            • Port taraması (Nmap)
• WHOIS/RIPE sorguları                • Açık port tespiti
• Web sitesi analizi                  • Servis versiyon tespiti
• Sosyal medya araştırması            • Güvenlik açığı taraması
• Meta veri analizi                   • Banner grabbing
```

> 🎯 **Saldırı Akışı:** Keşif → Güvenlik açığı tespiti → Exploit ile içeri sızma

---

### 2) Erişim Saldırıları (Access Attacks)

Kimlik doğrulama, FTP ve web servislerindeki güvenlik açıklarından yararlanarak yetkisiz erişim sağlama.

| Tür | Açıklama |
|-----|----------|
| **Parola Saldırıları** | Brute force, trojan, paket dinleyici kullanımı |
| **Güven İstismarı (Trust Exploitation)** | Yetkili bir sistemin güvenini kullanarak başka sistemlere sızmak |
| **Port Yönlendirme (Port Redirection)** | Ele geçirilen sistemi diğer hedeflere saldırmak için köprü olarak kullanmak |
| **Ortadaki Adam (Man-in-the-Middle)** | İki taraf arasına girerek veri okuma/değiştirme |

> ⚠️ **Man-in-the-Middle:** Aynı ağda olmayı gerektirir (kafe, havalimanı, otel wifi'ları risk taşır!). ARP zehirlenmesi, sahte AP, sahte DNS sunucusu ile gerçekleştirilebilir.

---

### 3) Hizmet Reddi Saldırıları (DoS / DDoS)

```
DoS (Denial of Service)              DDoS (Distributed DoS)
────────────────────────              ──────────────────────
Tek kaynaktan hedef sistemi           Botnet'teki binlerce/
aşırı yükleyerek hizmet              milyonlarca zombi
veremez hale getirme                  makineden eş zamanlı saldırı

        [Saldırgan]                   [C&C Sunucusu]
             │                              │
             ▼                    ┌─────────┼─────────┐
         [Hedef]                  ▼         ▼         ▼
                              [Zombi]  [Zombi]   [Zombi]
                                  └─────────┼─────────┘
                                            ▼
                                         [Hedef]
```

**DoS Saldırı Türleri:**

| Tür | Açıklama |
|-----|----------|
| **Uygulama Tabanlı (Application-centric)** | HTTP flood, SYN flood – session'ları doldurma |
| **Hacimsel (Volumetric)** | Büyük bant genişliği trafiği ile hedefi ezip geçme; %100 çözümü hâlâ zor |

**Botnet Yapısı:**
```
Saldırgan
    │
    ▼
C&C Sunucusu (Command & Control)
    │
    ├──▶ Ara C&C 1 ──▶ [Zombi x1000]
    ├──▶ Ara C&C 2 ──▶ [Zombi x1000]
    └──▶ Ara C&C 3 ──▶ [Zombi x1000]
                              │
                              ▼
                           [Hedef]
```

> 💡 **Zombi:** Zararlı yazılım bulaştırılmış, uzaktan komut bekleyen bilgisayar.  
> 💡 **Botnet:** Zombilerden oluşan ağ.  
> 💡 **C&C (Command & Control):** Zombilere saldırı emri veren sunucu.

---

## 16.3 Ağ Saldırısının Azaltılması

### Derinlemesine Savunma Yaklaşımı (Defense-in-Depth)

Katmanlı güvenlik: Tek bir savunma katmanı yeterli değildir; birden fazla güvenlik katmanı birlikte çalışmalıdır.

```
İNTERNET
    │
    ▼
 [VPN]  ◄── Uzak erişimi güvenli kılar
    │
    ▼
[ASA FIREWALL]  ◄── Yetkisiz girişleri engeller
    │
    ▼
  [IPS]  ◄── İzin verilen trafiği analiz eder, saldırıları tespit eder
    │
    ▼
[ESA/WSA]  ◄── E-posta ve web trafiğini filtreler
    │
    ▼
[Layer 3 Switch]
    │
    ▼
[Layer 2 Switch]
    │
    ├──▶ [AAA Sunucusu]  ◄── Kimlik doğrulama/yetkilendirme
    ├──▶ [E-posta Sunucusu]
    ├──▶ [Web Sunucusu]
    ├──▶ [DHCP Sunucusu]
    └──▶ [İstemciler/Hostlar]
```

---

### Güvenlik Cihazları ve Servisleri

#### 1. Firewall (Güvenlik Duvarı)

> 🛡️ **Firewall = Bodyguard:** Belirlenen kurallara göre trafiği geçirir ya da engeller.

```
INSIDE (Kurum İçi)          OUTSIDE (İnternet)
─────────────────            ─────────────────
                  [FIREWALL]
   PC ──────────▶    │    ──────────▶ Google
   PC ◀──────────    │    ◀────X──── Saldırgan
                     │
              [DMZ Bölgesi]
                  Web Sunucusu
                  (Dışarıdan erişilebilir,
                   içeriden izole)
```

**Firewall Türleri:**

| Tür | Çalışma Prensibi |
|-----|-----------------|
| **Paket Filtreleme** | IP/MAC adresine göre izin ver/engelle |
| **Uygulama Filtreleme** | Port numarasına göre filtrele (80=HTTP, 22=SSH) |
| **URL Filtreleme** | Belirli web adresi/anahtar kelimeye göre filtrele |
| **Stateful Packet Inspection (SPI)** | Oturum tablosu tutar; yalnızca içeriden başlatılan bağlantıların cevaplarını geçirir |

**DMZ (Demilitarized Zone – Silahsızlandırılmış Bölge):**
- İnternetten erişilmesi gereken sunucular (web, mail) buraya konur
- İç ağdan izole edilmiştir; DMZ'deki bir sunucu hacklenirse iç ağa kolayca sızılamaz

---

#### 2. IPS (Intrusion Prevention System – Saldırı Önleme Sistemi)

Firewall'ın izin verdiği trafiği daha derin analiz ederek saldırıları tespit edip bloklar.

```
İnternet ──▶ Firewall ──▶ [IPS] ──▶ Web Sunucusu
                            │
                     SQL injection?
                     Buffer overflow?
                         │
                    Saldırıysa: ENGELLE
                    Normalse:   GEÇİR
```

> ⚠️ **False Positive:** IPS'in normal trafiği saldırı zannedip engellemesi. Kritik servisleri kesebileceğinden dikkatli yapılandırma şarttır.

---

#### 3. ESA / WSA (E-posta ve Web Güvenliği)

```
E-posta Güvenliği (ESA):          Web Güvenliği (WSA):
─────────────────────              ──────────────────
İnternet                           Kullanıcı
    │                                  │
    ▼                                  ▼ (web isteği)
  [ESA]  ◄── Spam, phishing,       [WSA]  ◄── Zararlı site?
    │         zararlı ek analizi       │         URL filtresi?
    ▼                                  ▼
E-posta                            Web Sitesi
Sunucusu                           (temizse kullanıcıya ilet)
    │
    ▼
Kullanıcı
```

> 💡 Son kullanıcının dış tehditle temas ettiği iki ana kanal: **web** ve **e-posta** – her ikisi de korunmalıdır.

---

#### 4. AAA Sunucusu (Authentication, Authorization, Accounting)

```
AAA = KİMLİK DOĞRULAMA + YETKİLENDİRME + AKTİVİTE İZLEME

Authentication  →  "Sen kimsin?"          (Kullanıcı adı/şifre)
Authorization   →  "Ne yapabilirsin?"      (Yetki seviyeleri)
Accounting      →  "Ne yaptın?"            (Log kayıtları)

Kredi kartı analojisi:
  Kimlik doğrulama  = Kartı kimin kullanabileceği
  Yetkilendirme     = Ne kadar harcayabileceği (kredi limiti)
  Aktivite izleme   = Harcama dökümleri
```

**802.1X ile Ağ Erişim Kontrolü:**
```
Kullanıcı kabloyu taktığında:
    Switch ──▶ "Kullanıcı adı/şifren nedir?"
    Kullanıcı ──▶ Bilgi girer
    Switch ──▶ AAA Sunucusuna sorular
    AAA ──▶ "Onaylı! Yetkileri: sadece internet"
    Switch ──▶ Kuralları uygular
```

---

#### 5. VPN (Virtual Private Network)

Uzak kullanıcıların şirket ağına güvenli, şifreli tünel üzerinden bağlanmasını sağlar.

```
Ev/Uzak Ofis ──[Şifreli Tünel]──▶ Şirket Firewall ──▶ İç Ağ
                (Internet üzerinden
                 güvenli iletişim)
```

> 💡 **Kurumsal VPN ≠ NordVPN/ExpressVPN.** Şirketler kendi VPN sunucularını kurar; çalışanlar şirketin firewall'una VPN ile bağlanır.

---

### Yedekleme (Backup)

> 🏆 **En büyük güvenlik önlemi: BACKUP ALMAK!**

Fidye yazılımı, veri silme, manipülasyon gibi tehditlere karşı en etkili önlem.

| Konu | Açıklama |
|------|----------|
| **Sıklık** | Düzenli aralıklarla (haftalık full + günlük incremental) |
| **Depolama** | Ağdan bağımsız, offsite/offline konumda saklanmalı |
| **Güvenlik** | Güçlü şifreyle korunmalı; mümkünse şifreli olarak saklanmalı |
| **Doğrulama** | Yedekten geri yükleme prosedürü mutlaka test edilmeli |

> ⚠️ **Dikkat:** Sürekli bilgisayara takılı USB, ağ sürücüsü veya otomatik senkronize cloud → gerçek yedek sayılmaz! Saldırgan bu yedekleri de şifreleyebilir/silebilir.

---

### Güncelleme ve Yamalar (Upgrade, Update, Patch)

- Worm saldırısına karşı en etkili yöntem: işletim sistemi satıcısından güvenlik güncellemelerini indirip tüm sistemleri yamamak
- Uç sistemlerin güncellemeleri otomatik indirmesi sağlanmalı
- Cep telefonu, bilgisayar, ağ cihazları, IoT cihazları – **hepsi güncel tutulmalı**

---

### Uç Nokta Güvenliği (Endpoint Security)

```
Uç Nokta = Laptop, masaüstü, sunucu, akıllı telefon, tablet

Temel önlemler:
  ✅ Güvenlik politikası oluştur ve personele imzalat
  ✅ Personeli siber güvenlik konusunda eğit
  ✅ Antivirüs yazılımı zorunlu olsun
  ✅ Host tabanlı IPS kullanılsın
  ✅ Güvenlik yamalarını düzenli uygula
  ✅ Crack yazılım/oyun kullanımını engelle
  ✅ Kamuya açık WiFi'da bankacılık işlemi yapma
  ✅ Kişisel güvenlik duvarını (Windows Defender vb.) açık tut
```

---

## 16.4 Cihaz Güvenliği

### Cisco AutoSecure

Yeni IOS yüklendiğinde güvenlik ayarları varsayılan (yetersiz) değerlere gelir. `auto secure` komutu temel güvenlik yapılandırmasını otomatik uygular.

**Genel ilkeler (tüm işletim sistemleri için):**
- Varsayılan kullanıcı adı/şifreler derhal değiştirilmeli
- Sistem kaynaklarına erişim yalnızca yetkili kişilerle sınırlandırılmalı
- Gereksiz servisler ve uygulamalar kapatılıp kaldırılmalı
- Yazılım güncellemeleri ve güvenlik yamaları uygulanmadan önce yapılmalı

---

### Güçlü Parola Oluşturma

| Kural | Açıklama |
|-------|----------|
| **Uzunluk** | En az 8 karakter, tercihen 10+ karakter |
| **Karmaşıklık** | Büyük/küçük harf, rakam, sembol ve boşluk karışımı |
| **Kaçınılacaklar** | Sözlük kelimeleri, doğum tarihleri, isimler, TC kimlik no |
| **Değiştirme** | Düzenli aralıklarla şifre değiştirilmeli |
| **Saklama** | Görünür yerlere (monitör altı, masa) yazılmamalı |

**Parola Cümlesi (Passphrase) Tekniği:**

```
Cümle: "Gökhan saat 17:00'de sinemaya gitti."

Parola oluşturma:
  G  (Gökhan)
  s  (saat)
  17 (17:00)
  s  (sinemaya)
  g  (gitti)
  .  (nokta)

Sonuç: "Gs17sg." → Veya tam cümle: "Gokhan17sinemayagitti."

✅ 10+ karakter
✅ Büyük/küçük harf
✅ Rakam var
✅ Noktalama var
✅ Akılda kalıcı
```

---

### Ek Şifre Güvenliği Komutları

```
! Tüm düz metin parolaları şifrele
Router(config)# service password-encryption

! Minimum parola uzunluğu belirle
Router(config)# security passwords min-length 8

! Brute-force saldırılarını önle
! (60 saniyede 3 başarısız deneme = 120 saniye kilitle)
Router(config)# login block-for 120 attempts 3 within 60

! VTY hatlarını yapılandır
Router(config)# line vty 0 4
Router(config-line)# password cisco
Router(config-line)# exec-timeout 5 30      ! 5 dk 30 sn hareketsizse kes
Router(config-line)# transport input ssh     ! Yalnızca SSH izinli
Router(config-line)# end
```

---

### SSH Etkinleştirme

**Telnet vs SSH:**

```
TELNET (Port 23)               SSH (Port 22)
──────────────────              ──────────────
❌ Şifresiz (plain text)        ✅ Şifreli (encrypted)
❌ Wireshark ile okunabilir     ✅ Wireshark ile okunamaz
❌ Güvensiz                     ✅ Güvenli
```

**SSH Yapılandırma Adımları:**

```
! 1. Benzersiz hostname tanımla
Switch(config)# hostname CAT1-SW

! 2. IP domain adı yapılandır
CAT1-SW(config)# ip domain-name agyoneticileri.org

! 3. RSA kripto anahtarı oluştur (min 1024 bit, önerilen 2048 bit)
CAT1-SW(config)# crypto key generate rsa general-keys modulus 2048
! → "SSH has been enabled" mesajı görünür

! 4. Yerel kullanıcı oluştur
CAT1-SW(config)# username gokhan password Cisco123
CAT1-SW(config)# username ozan password Cisco456

! 5. VTY hatlarını yapılandır
CAT1-SW(config)# line vty 0 15
CAT1-SW(config-line)# login local          ! Yerel DB ile kimlik doğrulama
CAT1-SW(config-line)# transport input ssh  ! Yalnızca SSH
CAT1-SW(config-line)# exec-timeout 5 0    ! 5 dakika zaman aşımı
CAT1-SW(config-line)# end
```

> 💡 **Önemli:** SSH kullanılırken `login` yerine `login local` komutu kullanılmalı; SSH kullanıcı adı zorunlu kılar.

---

### Kullanılmayan Servisleri Devre Dışı Bırakma

```
! Açık portları listele (IOS-XE)
Router# show ip ports all

! Açık portları listele (eski IOS)
Router# show control-plane host open-ports

! Web sunucusu servisini kapat
Switch(config)# no ip http server
Switch(config)# no ip http secure-server
```

**Neden önemli?**
- Kullanılmayan servisler = gereksiz saldırı yüzeyi
- Her açık port, potansiyel bir giriş noktasıdır
- Gereksiz CPU/RAM tüketimini engeller

---

## 📊 Özet: Güvenlik Katmanları

```
    DIŞ SALDIRGANLAR (İnternet)
              │
              ▼
         ┌─────────┐
         │   VPN   │  ← Uzak erişimi güvenli kılar
         └─────────┘
              │
              ▼
      ┌──────────────┐
      │   FIREWALL   │  ← Yetkisiz trafiği engeller (%70 saldırıyı keser)
      └──────────────┘
              │
              ▼
          ┌───────┐
          │  IPS  │  ← İzin verilen trafiği derin analiz eder
          └───────┘
              │
              ▼
        ┌──────────┐
        │ ESA/WSA  │  ← E-posta ve web trafiğini temizler
        └──────────┘
              │
              ▼
        ┌──────────┐
        │ AAA/802.1X│  ← Kim bağlanıyor? Ne yapabilir? Ne yaptı?
        └──────────┘
              │
              ▼
    ┌─────────────────┐
    │  UYGULAMA/HOST  │  ← Antivirüs, host firewall, güncellemeler
    └─────────────────┘
              │
              ▼
          VERILER
    (Backup ile korunuyor)
```

---

## 📝 Bu Modülde Öğrendiklerimiz

1. **4 tehdit türü:** Bilgi hırsızlığı, veri kaybı/manipülasyon, kimlik hırsızlığı, hizmet kesintisi
2. **3 güvenlik açığı kaynağı:** Teknolojik, yapılandırma, güvenlik politikası
3. **4 fiziksel tehdit sınıfı:** Donanım, çevre, elektrik, bakım
4. **Malware türleri:** Virüs (dosyaya bulaşır), Worm (bağımsız yayılır), Truva atı (uzaktan erişim)
5. **3 saldırı kategorisi:** Keşif, erişim, hizmet reddi (DoS/DDoS)
6. **Derinlemesine savunma:** VPN + Firewall + IPS + ESA/WSA + AAA
7. **Backup stratejisi:** Sıklık, offsite depolama, güvenlik, doğrulama
8. **Cihaz güvenliği:** AutoSecure, güçlü parola, SSH, gereksiz servisleri kapatma
9. **SSH yapılandırması:** hostname → domain → crypto key → username → login local → transport input ssh

---

*Cisco CCNA1 · Introduction to Networks v7.0 · Modül 16*  
*Gökhan AKIN – CCIE · gokhan@agyoneticileri.org*  
*Ozan BÜK – CCIE · ozan@agyoneticileri.org*
