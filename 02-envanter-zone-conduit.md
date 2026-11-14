# LAB 2 · Envanter ve Zone / Conduit — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 01 Asset Inventory**, **EP 05 Zone & Conduit** · Durum: **Taslak**
> **[Kn]** = kaynak (bkz. [kaynaklar.md](kaynaklar.md)), **(V)** = varsayım. Her kategoride üç kez "başka?" diye soruldu.

## Ekibin ilk sandığı

"3 varlığımız var": **GESTICA ekranı · süreç kontrolcüsü · servo motorlar**

## Sistematik envanter (altı kategori)

### 1 · Donanım

| ID | Varlık | Not | Kaynak |
|---|---|---|---|
| D-01 | GESTICA operatör bilgisayarı ve 15,6" dokunmatik ekran | Donanım tuşları, LED durum ışıkları | [K3] |
| D-02 | GESTICA süreç kontrol bilgisayarı | Operatör bilgisayarından ayrı | [K3] |
| D-03 | Servo sürücüler (AMKmotion) | Kapama, enjeksiyon, dozaj eksenleri | [K2] |
| D-04 | Emniyet devresi | Kapı ve meme anahtarları, acil durdurma, STO | (V) |
| D-05 | MULTILIFT SELECT robot kontrolcüsü | Sıralaması GESTICA'ya entegre | [K3] [K4] |
| D-06 | IIoT ağ geçidi | Uzaktan servis ve bağlantı için ön şart | [K5] |
| D-07 | USB portu | Veri seti ve yazılım aktarımı | [K3] |
| D-08 | Yazıcı bağlantısı | | [K3] |
| D-09 | Çevre birimleri: kalıp temperleme cihazı, kurutucu | (V) | |

### 2 · Yazılım / firmware

| ID | Varlık | Not | Kaynak |
|---|---|---|---|
| Y-01 | GESTICA kontrol yazılımı | ALS arayüzü için sürüm 3.090 veya üzeri gerekir | [K6] |
| Y-02 | Kapalı işletim sistemi | Broşüre göre güvenlik güncellemesi gerektirmez | [K3] |
| Y-03 | Gömülü tarayıcı | Makine dokümantasyonu kontrol ekranından çevrim içi açılır | [K3] |
| Y-04 | aXw Control asistanları | FillAssist, CycleAssist, MeltAssist, EnergyAssist, PressurePilot, ReferencePilot | [K3] |
| Y-05 | Servo sürücü ve robot firmware'leri | Sürümler kamuya açık değil | (V) |

### 3 · Hesaplar

| ID | Varlık | Not | Kaynak |
|---|---|---|---|
| H-01 | GESTICA kullanıcı yetki seviyeleri | Operatör / ayarcı / servis (adlandırma V) | [K3] "operating rights" |
| H-02 | Paylaşılan vardiya hesabı veya anahtarı | Tipik saha alışkanlığı | (V) |
| H-03 | Arburg servis teknisyeni erişimi (ARS) | Müşteri önceden etkinleştirip onaylar | [K5] |
| H-04 | arburgXworld portal hesapları | Müşteri tarafı kullanıcılar | [K5] |

### 4 · Sertifika / anahtar

| ID | Varlık | Not | Kaynak |
|---|---|---|---|
| S-01 | ARS şifreli bağlantı anahtarları / sertifikaları | | [K5] |
| S-02 | OPC UA sertifikaları | | [K3] |
| S-03 | Fabrika Wi-Fi / VPN anahtarları (IIoT geçidinin ağı) | (V) | |

### 5 · Dış bağlantılar

| ID | Bağlantı | Yön | Kaynak |
|---|---|---|---|
| B-01 | ARBURG Remote Service (ARS), IIoT ağ geçidi üzerinden | Arburg → makine | [K5] |
| B-02 | arburgXworld portalı (MachineCenter, ServiceCenter vb.) | Makine → bulut | [K5] |
| B-03 | ALS ana bilgisayar sistemi (üretim verisi, veri seti yönetimi) | Fabrika ağı ↔ makine | [K6] |
| B-04 | OPC UA arayüzü | MES / SCADA ↔ makine | [K3] |
| B-05 | Servis dizüstü bilgisayarı / USB bellek | Fiziksel | [K3] (V) |

### 6 · Kritik veri akışları

| ID | Veri | Nereden → nereye | Neden kritik |
|---|---|---|---|
| V-01 | **Veri seti** (kalıp, hız, sıcaklık, robot sıralaması) | USB / ALS → GESTICA | Tüm hareket parametrelerini belirler |
| V-02 | Üretim ve süreç verisi | GESTICA → ALS / OPC UA / bulut | Fikri mülkiyet, kalite kaydı |
| V-03 | Döngü kaydı (son 2500 çevrim) | GESTICA içinde | Olay sonrası kanıt (LAB 6) |
| V-04 | Yazılım güncellemeleri | Arburg → GESTICA | Tedarik zinciri kapısı |

**Sonuç:** Ekip 3 varlık sandı, sistematik sayımda **25 varlık ve 5 dış bağlantı** çıktı.

## Görev A · Bölgeler (zone)

```
┌─────────────────────────────┐
│ Z1 · Kurumsal IT            │  ERP, e-posta, ofis PC'leri
└──────────────┬──────────────┘
               │ C1 (firewall)
┌──────────────┴──────────────┐
│ Z2 · DMZ / Üretim IT        │  ALS sunucusu, OPC UA istemcisi (MES), IIoT ağ geçidi
└──────────────┬──────────────┘         ↑ C5: ARS / arburgXworld (internet)
               │ C2
┌──────────────┴──────────────┐
│ Z3 · Makine bölgesi         │  GESTICA operatör PC, GESTICA süreç kontrolcüsü,
│                             │  servo sürücüler, robot kontrolcüsü, çevre birimleri
└──────────────┬──────────────┘         ↑ C4: USB / servis dizüstü (fiziksel)
               │ C3 (yalnızca durum bilgisi, tek yön olmalı)
┌──────────────┴──────────────┐
│ Z4 · Emniyet bölgesi        │  Emniyet devresi, kapı anahtarları, acil durdurma, STO
└─────────────────────────────┘
```

1. **Aynı bölgedekiler:** GESTICA'nın iki bilgisayarı, servo sürücüler ve robot kontrolcüsü aynı kritiklikte. Hepsi Z3'te.
2. **Emniyet devresi neden Z4'te, ayrı?** En yüksek kritiklik onda. Z3 ele geçirilse bile Z4'ün kararı değişmemeli. Kilit soru: GESTICA'nın emniyet devresine **yazabildiği** bir yol var mı? Açık kaynakta bilgi yok (V). Varsa bu, LAB 3'te E puanını artırır.
3. **Geçişler (conduit) ve kontrolleri:**

| Conduit | Arası | Ne kontrol edilmeli? |
|---|---|---|
| C1 | Z1 ↔ Z2 | Sadece tanımlı servisler (ALS, OPC UA). Ofis ağından makineye doğrudan erişim olmamalı. |
| C2 | Z2 ↔ Z3 | OPC UA'da **okuma ve yazma ayrımı**. Veri seti yazımı sadece yetkili kişi ve onayla yapılmalı. |
| C3 | Z3 → Z4 | Emniyet devresine sadece **durum okuma**. Parametre yazılmamalı. |
| C4 | USB / servis dizüstü → Z3 | USB kullanımı kayıtlı ve yetkili olmalı. Dizüstü taranmış olmalı. |
| C5 | İnternet → IIoT ağ geçidi → Z3 | **En riskli conduit.** ARS varsayılan kapalı olmalı, müşteri onayı ve oturum kaydı tutulmalı. |

## Görev B · Uzak erişim kararı

Arburg tedarikçisi GESTICA'ya uzaktan bağlanmak istiyor:

| Karar | Seçenekler | Seçim | Neden |
|---|---|---|---|
| Aracı? | Doğrudan / Jump server | **Jump server** | IIoT ağ geçidi doğrudan Z3'e değil, DMZ'deki ara noktaya bağlanmalı |
| Tünel? | Açık / VPN | **VPN** | ARS zaten şifreli bağlantı kullanıyor [K5]. Fabrika tarafında da tünel zorunlu tutulmalı. |
| Kimlik? | Parola / MFA | **MFA** | Tek parola çalınırsa tedarikçi kapısı tamamen açılır |
| Hesap? | Paylaşılan / Kişisel | **Kişisel** | "Arburg servis" ortak hesabı, kimin ne yaptığını gizler |
| Kayıt? | Yok / Tam oturum kaydı | **Tam oturum kaydı** | Parametre değişikliği LAB 6'da kanıt olacak |
| Zamanlama? | Sürekli açık / Varsayılan kapalı | **Varsayılan kapalı** | ARS müşteri onayı ister [K5]. Bu onay iş bitince **otomatik kapanmalı**. |
