# CCNA1 – Modül 2: Temel Switch ve Son Kullanıcı Yapılandırması
> CCNA Introduction to Networks v7.0 (ITN) 

---

## 📋 Modül Hedefi
Network switch ve son aygıtlarda **parolalar, IP adresleme ve varsayılan ağ geçidi** parametreleri dahil olmak üzere ilk ayarları uygulamak.

| Konu | Hedef |
|------|-------|
| Cisco IOS Erişimi | Yapılandırma amacıyla bir Cisco IOS cihazına nasıl erişileceğini açıklamak |
| IOS Navigasyon | Ağ aygıtlarını yapılandırmak için Cisco IOS'u nasıl navige edeceklerini açıklamak |
| Komut Yapısı | Cisco IOS yazılımının komut yapısını açıklamak |
| Temel Aygıt Yapılandırması | CLI kullanarak bir Cisco IOS cihazını yapılandırmak |
| Yapılandırmaları Kaydet | Çalışan yapılandırmayı kaydetmek için IOS komutlarını kullanmak |
| Portlar ve Adresler | Aygıtların ağ ortamları arasında nasıl iletişim kurduğunu açıklamak |
| IP Adresleme Yapılandırma | IP adresi olan bir ana bilgisayar aygıtını yapılandırmak |
| Bağlantı Doğrulama | İki son aygıt arasındaki bağlantıyı doğrulamak |

---

## 2.1 Cisco IOS Erişimi

### İşletim Sistemi Katmanları

Her bilişim cihazı (bilgisayar, telefon, router, switch) üç katmanlı bir yapıya sahiptir:

```
┌─────────────────────────────────┐
│           S H E L L             │  ← Kullanıcı Arayüzü (CLI / GUI)
├─────────────────────────────────┤
│           K E R N E L           │  ← Donanım ↔ Yazılım arası köprü
├─────────────────────────────────┤
│         H A R D W A R E         │  ← Fiziksel bileşenler
└─────────────────────────────────┘
```

| Katman | Türkçe | Görev |
|--------|--------|-------|
| **Shell** | Kabuk | Kullanıcıya CLI veya GUI arayüzü sağlar |
| **Kernel** | Çekirdek | Donanım kaynaklarını yönetir, donanım-yazılım arası iletişimi sağlar |
| **Hardware** | Donanım | CPU, RAM, portlar gibi fiziksel parçalar |

> 💡 **Neden Kernel önemli?** Kernel çok "low-level" (düşük seviyeli) çalışır; insan etkileşimi için uygun değildir. Bu yüzden üzerine Shell yazılmıştır.

---

### GUI vs CLI

| Özellik | GUI | CLI |
|---------|-----|-----|
| **Açılımı** | Graphical User Interface | Command Line Interface |
| **Kullanım** | Mouse + ikon + pencere | Klavye + komut satırı |
| **Öğrenme kolaylığı** | Kolay | Zor (başta) |
| **Ağ cihazlarında tercih** | ❌ Nadir | ✅ Standart |
| **Örnekler** | Windows, macOS, Android | Terminal, PuTTY, Tera Term |

> ⚠️ GUI'ler crash edebilir veya beklenmedik şekilde davranabilir. Bu nedenle kurumsal ağ cihazları **CLI** üzerinden yönetilir.

---

### Erişim Yöntemleri

```
┌──────────────────────────────────────────────────────────┐
│                   Cisco Cihaza Erişim                    │
├────────────────┬─────────────────────┬───────────────────┤
│    KONSOL      │        SSH          │      TELNET       │
│  (Fiziksel)    │   (Güvenli/Uzak)    │  (Güvensiz/Uzak)  │
├────────────────┼─────────────────────┼───────────────────┤
│ Konsol kablosu │ Şifrelenmiş         │ Düz metin         │
│ RJ-45 → DB9    │ Önerilen yöntem     │ Kullanımdan kalktı│
│ Fiziksel erişim│ IP gerektirir       │ IP gerektirir     │
│ gerektirir     │ Port 22             │ Port 23           │
└────────────────┴─────────────────────┴───────────────────┘
```

**Konsol Bağlantısı Kurulumu:**
1. Mavi konsol kablosunu cihazın **Console portuna** tak (RJ-45 uç)
2. Diğer ucu (DB9/USB) bilgisayara bağla
3. Günümüzde USB → Seri dönüştürücü gerekebilir
4. **PuTTY** veya **Tera Term** gibi terminal yazılımını aç
5. **Serial** bağlantı tipini seç, COM numarasını gir (Device Manager'dan öğren)
6. Enter'a bas → cihaz CLI'si açılır

---

### Terminal Emülatör Programları

| Program | Lisans | SSH | Telnet | Serial | Notlar |
|---------|--------|-----|--------|--------|--------|
| **PuTTY** | Ücretsiz/Açık | ✅ | ✅ | ✅ | En yaygın kullanılan |
| **Tera Term** | Ücretsiz/Açık | ✅ | ✅ | ✅ | PuTTY alternatifi |
| **SecureCRT** | Ücretli | ✅ | ✅ | ✅ | Profesyonel çözüm |

> ⚠️ **Güvenlik Uyarısı:** Crackli terminal yazılımı KULLANMAYIN! Kritik cihaz parolalarını girdiğiniz programın güvenilir olması şarttır.

---

## 2.2 IOS Navigasyon

### Birincil Komut Modları

```
Cihaza bağlandın
        │
        ▼
┌───────────────────┐
│   USER EXEC MOD   │  Switch>   veya  Router>
│  (Kullanıcı Modu) │  → Sınırlı izleme komutları
│     Sembol: >     │  → Yapılandırma yapılamaz
└────────┬──────────┘
         │  enable
         ▼
┌───────────────────┐
│ PRIVILEGED EXEC   │  Switch#   veya  Router#
│  (Ayrıcalıklı Mod)│  → Tüm komutlara erişim
│     Sembol: #     │  → show, debug, copy vs.
└────────┬──────────┘
         │  configure terminal (conf t)
         ▼
┌───────────────────┐
│  GLOBAL CONFIG    │  Switch(config)#
│   MOD             │  → Genel yapılandırma
│  (config)#        │  → hostname, password vs.
└────────┬──────────┘
         │
    ┌────┴─────┐
    ▼          ▼
┌────────┐  ┌──────────┐
│ LINE   │  │INTERFACE │
│CONFIG  │  │ CONFIG   │
│(config │  │(config   │
│-line)# │  │-if)#     │
└────────┘  └──────────┘
```

### Modlar Arası Geçiş Komutları

| Geçiş | Komut |
|-------|-------|
| User EXEC → Privileged EXEC | `enable` |
| Privileged EXEC → User EXEC | `disable` |
| Privileged EXEC → Global Config | `configure terminal` (kısaca `conf t`) |
| Global Config → Privileged EXEC | `exit` |
| Alt mod → Global Config | `exit` |
| Herhangi mod → Privileged EXEC (hızlı) | `end` veya `Ctrl+Z` |
| Cihazdan çıkış | `exit` (User EXEC modunda) |

### Prompt (Komut İstemi) Anlamı

```
Switch(config-if)#
│      │          │
│      │          └── Mod göstergesi (# = Privileged ya da alt mod)
│      └─────────── Hangi alt modda olduğun
└──────────────── Cihazın adı (hostname)
```

---

## 2.3 Komut Yapısı

### Temel IOS Komut Yapısı

```
Switch> show    ip    protocols
│       │       │     │
│       │       │     └── Keyword (Anahtar kelime) – OS'de tanımlı
│       │       └──────── Space (Boşluk)
│       └────────────── Command (Komut)
└────────────────────── Prompt
```

**Keyword vs Argument:**
- **Keyword** → İşletim sisteminde önceden tanımlı parametre (örn: `ip protocols`)
- **Argument** → Kullanıcının belirlediği değişken (örn: `192.168.10.5`)

### Komut Sözdizimi Kuralları

| Gösterim | Anlamı |
|----------|--------|
| **boldface** | Komutlar ve keyword'ler – aynen girilecek |
| *italics* | Argument – kullanıcı değer girecek |
| `[x]` | Köşeli parantez → İsteğe bağlı (opsiyonel) |
| `{x}` | Süslü parantez → Zorunlu, mutlaka girilmeli |
| `[x {y \| z}]` | İsteğe bağlı içinde zorunlu seçim |
| `x \| y` | Pipe → "veya" anlamında |

**Örnek:**
```
switchport port-security aging { static | time time | type {absolute | inactivity}}
                                 └─────┘   └────────┘        └──────────────────┘
                                 Seçenek1  Seçenek2            Seçenek3 (iç içe)
```

---

### IOS Yardım Özellikleri

#### Bağlam Duyarlı Yardım (Context-Sensitive Help)

```bash
Switch> ?                    # Mevcut moddaki tüm komutları listeler
Switch> c?                   # 'c' ile başlayan komutları gösterir
Switch> clock ?              # clock komutunun parametrelerini gösterir
Switch> clock set ?          # set parametresinin alt parametrelerini gösterir
```

#### Hata Mesajları

| Hata | Anlamı | Çözüm |
|------|--------|-------|
| `% Ambiguous command` | Birden fazla komut eşleşiyor | Daha fazla karakter yaz |
| `% Incomplete command` | Komut eksik, parametre gerekiyor | `?` ile alt parametre bak |
| `% Invalid input detected at '^' marker` | `^` işaretinden itibaren hatalı | O kısmı düzelt |

---

### Kısayol Tuşları

| Tuş | İşlev |
|-----|-------|
| `Tab` | Komutu tamamlar (benzersizse) |
| `Backspace` | Sol karakteri siler |
| `↑` veya `Ctrl+P` | Önceki komutu getirir |
| `↓` veya `Ctrl+N` | Sonraki komutu getirir |
| `←` veya `Ctrl+B` | İmleci bir karakter sola taşır |
| `→` veya `Ctrl+F` | İmleci bir karakter sağa taşır |
| `Ctrl+C` | Yapılandırma modundan çıkar / işlemi durdurur |
| `Ctrl+Z` | Yapılandırma modundan Privileged EXEC'e döner |
| `Ctrl+Shift+6` | DNS arama, ping, traceroute gibi işlemleri durdurur |

**`--More--` görüntülendiğinde:**
- `Space` → Bir sayfa daha göster
- `Enter` → Bir satır daha göster
- Başka herhangi tuş → Çıkış

---

## 2.4 Temel Aygıt Yapılandırması

### Cihaz Adı (Hostname) Verme

```bash
Switch# configure terminal
Switch(config)# hostname Sw-Kat1-1
Sw-Kat1-1(config)#
```

**Hostname Kuralları:**
- Harf ile başlamalı
- Boşluk içermemeli
- Harf veya rakam ile bitmeli
- Sadece harf, rakam ve tire kullanılabilir
- Maksimum 64 karakter (pratikte çok kısa tutun!)
- Varsayılana dönmek için: `no hostname`

> 💡 **İyi Pratik:** Cihazın konumunu ve işlevini belirten isimler kullanın. Örnek: `Core-SW-Floor1`, `Access-SW-B2-K3`

---

### Parola Yapılandırması

Cisco cihazlarda **iki farklı noktada** parola sorulur:

```
Cihaza ilk bağlantı (Console/Telnet/SSH)
              │
              ▼
    [ Console / VTY Parolası ]   ← line console 0 / line vty 0 15
              │
              ▼
         User EXEC >
              │
          enable komutu
              │
              ▼
    [ Enable Secret Parolası ]   ← enable secret
              │
              ▼
       Privileged EXEC #
```

#### Console Parolası

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line console 0
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

#### Privileged EXEC Parolası

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# enable secret class
Sw-Floor-1(config)# exit
```

#### VTY (Sanal Terminal – Telnet/SSH) Parolası

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# line vty 0 15
Sw-Floor-1(config-line)# password cisco
Sw-Floor-1(config-line)# login
Sw-Floor-1(config-line)# end
```

> 📝 **VTY 0 15** → 16 eşzamanlı uzak bağlantıya izin verir (0'dan 15'e = 16 kanal)

#### Parola Güvenlik Kuralları

| Kural | Öneri |
|-------|-------|
| Uzunluk | Minimum 8 karakter |
| Karmaşıklık | Büyük/küçük harf + sayı + özel karakter |
| Benzersizlik | Her cihaza farklı parola |
| Yaygın kelimeler | Kullanmayın (sözlük saldırısı) |
| Lab ortamında | `cisco`, `class` gibi basit parolalar kullanılabilir (gerçek ortamda asla!) |

---

### Parola Şifreleme

Varsayılan olarak `show running-config` çalıştırıldığında parolalar **düz metin** görünür. Bunu engellemek için:

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# service password-encryption
Sw-Floor-1(config)# exit
```

**Doğrulama:**
```
line con 0
 password 7 094F471A1A0A    ← Artık şifreli görünüyor
 login
Line vty 0 4
 Password 7 03095A0F034F38435B49150A1819
 Login
```

> ⚠️ `enable secret` zaten MD5 ile şifreli saklanır. `service password-encryption` diğer parolaları (console, vty) şifreler.

---

### Banner (Giriş Uyarı Mesajı)

```bash
Sw-Floor-1# configure terminal
Sw-Floor-1(config)# banner motd #
Bu cihaza yetkisiz erişim yasaktır.
Tüm erişimler kayıt altındadır.
#
```

**Nasıl çalışır:**
- `banner motd` → Message Of The Day
- `#` → Sınırlayıcı karakter (delimiter) – metnin başında ve sonunda kullanılır
- Metnin içinde `#` kullanmayın! Başka bir karakter seçin (örn: `$`, `%`)

> ⚖️ **Hukuki Uyarı:**
> - ❌ "Hoş Geldiniz" yazmayın → Davetsiz misafiri davet etmiş sayılabilirsiniz
> - ❌ "Girersen döverim" yazmayın → Tehdit sayılabilir
> - ✅ "Bu cihaza yetkisiz erişim yasal işlem gerektirir" gibi nötr uyarılar yazın

---

## 2.5 Yapılandırmaları Kaydetme

### Yapılandırma Dosyaları

```
┌──────────────────────────────────────────────────┐
│                 CİHAZ BELLEĞİ                    │
│                                                  │
│  ┌─────────────────┐      ┌──────────────────┐   │
│  │  running-config │      │  startup-config  │   │
│  │    (RAM'de)     │      │   (NVRAM'da)     │   │
│  │                 │      │                  │   │
│  │ • Aktif config  │ ───► │ • Kalıcı config  │   │
│  │ • Anlık değişir │copy  │ • Yeniden başl.  │   │
│  │ • Kapatınca     │      │   yüklenir       │   │
│  │   silinir!      │      │ • Kalıcı hafıza  │   │
│  └─────────────────┘      └──────────────────┘   │
└──────────────────────────────────────────────────┘
```

| Dosya | Depolama | Özellik |
|-------|----------|---------|
| **running-config** | RAM | Anlık yapılandırma, cihaz kapanınca silinir |
| **startup-config** | NVRAM (Flash) | Kalıcı yapılandırma, her açılışta yüklenir |

### Yapılandırmayı Kaydetme

```bash
# Yöntem 1: Standart (Önerilen)
Router# copy running-config startup-config

# Yöntem 2: Kısa yol (wr = write)
Router# write

# Yöntem 3: Tam yazım
Router# write memory
```

### Yapılandırmayı Görüntüleme

```bash
Router# show running-config     # Aktif yapılandırmayı göster
Router# show startup-config     # Kayıtlı yapılandırmayı göster
```

### Yapılandırmayı Sıfırlama

```bash
Router# erase startup-config    # Startup config'i sil
Router# reload                  # Cihazı yeniden başlat (onay gerekir)
```

> ⚠️ **reload** komutu kritik cihazlarda dikkatli kullanılmalıdır! Yeniden başlatma 10 dakikaya kadar sürebilir ve tüm ağ bağlantısı kesilir.

### PuTTY ile Oturum Kaydetme

1. PuTTY → **Change Settings** → **Session** → **Logging**
2. **All session output** seçeneğini seç
3. Dosya adı ve konumu belirle (örn: `SwitchLog.txt`)
4. **Apply** → Artık tüm terminal çıktısı dosyaya yazılır
5. İşlem bitince → **None** seçerek kaydı durdur

---

## 2.6 Portlar ve Adresler

### IP Adresleri

#### IPv4

```
192  .  168  .  1   .  10
 │       │       │      │
 └───────┴───────┴──────┘
         32 bit
    4 oktet × 8 bit
   Her oktet: 0-255 arası

Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.1  ← Router IP'si
```

#### IPv6

```
2001:0db8:acad:0010:0000:0000:0000:0001
└───┘└───┘└───┘└───┘└───────────────┘
 128 bit = 32 hexadecimal karakter
 8 grup × 16 bit, iki nokta üst üste ile ayrılır
```

### Ağ Ortamı (Media) Türleri

```
┌──────────────────────────────────────────────────────┐
│                   AĞ ORTAMLARI                       │
├──────────────┬──────────────┬───────────────────────┤
│  BAKIR KABLO │  FİBER OPTİK │      KABLOSUZ          │
│  (Copper)    │              │      (Wireless)        │
├──────────────┼──────────────┼───────────────────────┤
│ • UTP/STP    │ • Tek modlu  │ • Wi-Fi (802.11)      │
│ • RJ-45      │ • Çok modlu  │ • Bluetooth            │
│ • 100m limit │ • km'lerce   │ • Cellular             │
│ • Ucuz       │ • Pahalı     │ • Esnek                │
│ • EMI etkisi │ • EMI yok    │ • Güvenlik riski       │
└──────────────┴──────────────┴───────────────────────┘
```

---

## 2.7 IP Adresleme Yapılandırması

### Son Aygıtlara IP Adresi Verme

#### Manuel (Statik) – Windows PC

**Kontrol Paneli > Ağ ve Paylaşım Merkezi > Bağdaştırıcı Ayarlarını Değiştir > Sağ Tık > Özellikler > IPv4**

| Alan | Örnek Değer |
|------|-------------|
| IP Adresi | 192.168.1.10 |
| Alt Ağ Maskesi | 255.255.255.0 |
| Varsayılan Ağ Geçidi | 192.168.1.1 |
| DNS Sunucusu | 8.8.8.8 |

#### Otomatik (DHCP)

- **Otomatik olarak IP adresi al** seçeneğini işaretle
- DHCP sunucusu IP, maske, gateway ve DNS'i otomatik atar
- IPv6 için: **DHCPv6** veya **SLAAC** (Stateless Address Autoconfiguration)

---

### Switch'e IP Adresi Verme (SVI – Sanal Arayüz)

> 📌 **Neden SVI?** Switch'in fiziksel portları Layer 2 (MAC adresi bazlı) çalışır. IP adresi Layer 3 gerektirir. Bu yüzden **VLAN 1** sanal arayüzüne IP verilir.

```
Fiziksel Port (Layer 2 – MAC ile çalışır)
       │
       ▼
  SVI - interface VLAN 1  (Layer 3 – IP alabilir)
       │
       ▼
  Uzaktan yönetim için IP adresi
```

**Yapılandırma:**

```bash
Switch# configure terminal
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.20 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit
Switch(config)# ip default-gateway 192.168.1.1
Switch(config)# end
Switch# copy running-config startup-config
```

> ⚠️ Layer 3 arayüzler varsayılan olarak **kapalı (shutdown)** gelir. `no shutdown` komutu ile açılmalıdır.

---

## 2.8 Bağlantı Doğrulama

### Temel Kontrol Komutları

```bash
# Tüm arayüzlerin özet durumunu göster
Switch# show ip interface brief

# Çalışan yapılandırmayı göster
Switch# show running-config

# Sistem saatini göster
Switch# show clock

# Cihaz versiyonunu göster
Switch# show version
```

### `show ip interface brief` Çıktısı Yorumlama

```
Interface     IP-Address    OK? Method  Status   Protocol
Vlan1         192.168.1.20  YES manual  up       up        ← Normal çalışıyor
Vlan1         unassigned    YES unset   up       down      ← IP yok veya sorun var
Vlan1         192.168.1.20  YES manual  admin    down      ← shutdown uygulandı
```

| Status | Protocol | Anlam |
|--------|----------|-------|
| up | up | ✅ Çalışıyor |
| up | down | ⚠️ Layer 2 sorun |
| administratively down | down | 🔴 shutdown komutu verilmiş |

### Ping ile Bağlantı Testi

```bash
Switch# ping 192.168.1.1
!!!!!                    ← 5/5 başarılı
.....                    ← 5/5 başarısız (zaman aşımı)
```

---

## 📝 Temel Switch Yapılandırma Özeti (Cheat Sheet)

```bash
! 1. Cihaza gir ve Privileged moda geç
Switch> enable

! 2. Config moduna gir
Switch# configure terminal

! 3. İsim ver
Switch(config)# hostname Sw-Kat1-1

! 4. Enable parolası
Sw-Kat1-1(config)# enable secret SifreliParola123!

! 5. Console parolası
Sw-Kat1-1(config)# line console 0
Sw-Kat1-1(config-line)# password ConsoleParola1!
Sw-Kat1-1(config-line)# login
Sw-Kat1-1(config-line)# exit

! 6. VTY (uzak erişim) parolası
Sw-Kat1-1(config)# line vty 0 15
Sw-Kat1-1(config-line)# password VtyParola1!
Sw-Kat1-1(config-line)# login
Sw-Kat1-1(config-line)# exit

! 7. Tüm parolaları şifrele
Sw-Kat1-1(config)# service password-encryption

! 8. Uyarı banner'ı
Sw-Kat1-1(config)# banner motd #
Bu sisteme yetkisiz erisim yasaktir!
#

! 9. IP adresi ver (SVI üzerinden)
Sw-Kat1-1(config)# interface vlan 1
Sw-Kat1-1(config-if)# ip address 192.168.1.20 255.255.255.0
Sw-Kat1-1(config-if)# no shutdown
Sw-Kat1-1(config-if)# exit

! 10. Default gateway
Sw-Kat1-1(config)# ip default-gateway 192.168.1.1

! 11. Privileged moda dön ve kaydet
Sw-Kat1-1(config)# end
Sw-Kat1-1# copy running-config startup-config
```

---

## 🔑 Önemli Hatırlatmalar

- **`enable secret`** → `enable password`'den daha güvenlidir (MD5 şifreleme)
- **`no` ön eki** → Herhangi bir komutu kaldırır / varsayılana döndürür
- **RAM** (running-config) → Hızlı ama kalıcı değil; cihaz kapanınca silinir
- **NVRAM** (startup-config) → Kalıcı; her açılışta yüklenir
- **SVI** → Switch'e uzaktan erişim için kullanılan sanal Layer 3 arayüzü
- **`Ctrl+Shift+6`** → Takılan komutları (DNS lookup, uzun ping) durdurmak için
- **`conf t`** = `configure terminal` → Cisco kısa yazım kabul eder (benzersizse)

---

*CCNA1 ITN v7.0 – Modül 2 | agyoneticileri.org*
