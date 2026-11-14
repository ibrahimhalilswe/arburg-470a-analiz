# LAB 7 · AI Safety Challenge — AI Fail-Safe Decision — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 03 Safety Functions**, **EP 15 Verification/Test** · Durum: **Taslak**
> **[Kn]** = kaynak (bkz. [kaynaklar.md](kaynaklar.md)), **(V)** = varsayım.

**Kavram · Prediction ≠ Safety:** Yapay zekâ bir **tahmin** üretir. Güvenliği garanti eden mimari **YZ'nin dışında** tanımlanır. Kullanılan araçlar: fail-safe · human override · monitoring · confidence threshold · fallback.

## Arburg'da bugün hangi YZ var?

| Ürün | Ne yapar? | Makineye doğrudan müdahale eder mi? | Kaynak |
|---|---|---|---|
| **Ask ARBURG** | arburgXworld müşteri portalındaki YZ destekli uygulama | Basın bülteninde ayrıntı yok. Parametre değiştirdiğine dair bilgi yok. | [K10] |
| **aXw Control RecyclatePilot** | Geri dönüştürülmüş malzemenin işlenmesi için "YZ destekli" pilot | Ayrıntı yok | [K10] |
| **GESTICA lite** YZ fonksiyonları | Menüde gezinme ve arama | Hayır | [K10] |
| **Virtual Twin** (2026'dan itibaren) | GESTICA'nın sanal ikizi. Çevrimi PC'de simüle eder. | Hayır, sadece simülasyon | [K10] |

Açık kaynakta Arburg'un **makine sürecine kapalı döngüyle doğrudan müdahale eden** bir YZ fonksiyonu tanımlanmıyor. Bu yüzden föydeki PaketPro sürümünde olduğu gibi bu lab bir **proje aşaması değerlendirmesi** olarak yapıldı.

## Senaryo: planlanan YZ'li hücre (V)

İşletme, 470 A hücresine iki YZ fonksiyonu eklemeyi planlıyor:

```
MULTILIFT SELECT robotu parçayı kameranın önüne getirir
   → Kamera → YZ görsel kontrol modeli → OK / RED
        ├─ OK  → robot parçayı OK bandına koyar
        └─ RED → robot parçayı hurda kutusuna atar
   → YZ süreç pilotu (RecyclatePilot benzeri): RED oranına ve malzemeye bakarak
     enjeksiyon hızı · ütüleme basıncı · sıcaklık için düzeltme ÖNERİR / UYGULAR → GESTICA
```

**Kritik nokta:** Görsel kontrol modeli tek başına bir **kalite** fonksiyonudur ve yanılırsa sonuç kusurlu parçadır (S=1). Ama süreç pilotu GESTICA'ya **parametre yazıyorsa**, YZ artık LAB 1'deki **dijital kesişim noktasına** dokunuyor demektir. Enjeksiyon hızı, kapama hızı ve ısıtma sınırları emniyet paylarını etkiler (SF-01, SF-02, SF-05).

## Görev · Üç başarısızlıkta güvenli davranış

### 1 · Model yanlış sınıflandırdı: kusurlu parçaya OK verdi

**Sistem ne yapmalı?**

- **İkinci kontrol (monitoring):** YZ'nin OK dediği parçalardan **örneklem** alınır ve insan muayenesinden geçer (V: örneğin her 100 parçadan biri). Örneklemde kusur bulunursa son partinin tamamı karantinaya alınır.
- **Trend izleme:** OK oranı veya parça ağırlığı gibi bağımsız bir süreç ölçüsü aniden değişirse alarm verilir. Model yanılsa bile süreç verisi ve model kararı birbirini tutmaz.
- **Süreç pilotu yanlış veriye göre ayar yapmamalı:** Pilotun yapabileceği düzeltmeler, **YZ'nin dışında** tanımlanan sınırlar içinde kalır (LAB 4, GR-02). Model yanılıp pilotu yanlış yöne itse bile hız ve sıcaklık bu sınırları aşamaz.
- **İnsan onayı (human override):** Pilotun önerdiği her parametre değişikliği ayarcının onayıyla uygulanır. Ayarcı pilotu GESTICA'dan tek tuşla kapatabilir.

### 2 · Girdi manipüle edildi: kamera verisi bozuk veya sahte

**Nasıl fark edilir?**

| Kontrol | Neyi yakalar |
|---|---|
| **Referans parça (golden sample):** Robot belirli aralıklarla bilinen kusurlu ve kusursuz bir parçayı kameraya gösterir | Model veya kamera artık doğru görmüyorsa |
| **Görüntü kalitesi kontrolü:** parlaklık, odak, kontrast | Kirli lens, bozulan ışık |
| **Görüntü ile çevrim eşleşmesi:** Her görüntüde çevrim numarası ve zaman damgası | Eski veya tekrar oynatılan (sahte) görüntü |
| **Kamera ağı ayrı ve kapalı:** Kamera Z3 içinde, dışarıdan erişimi yok (LAB 2) | Ağ üzerinden görüntü enjeksiyonu |

**Şüpheli girdide ne yapılır?**

- **Fail-safe:** Tüm parçalar **karantina kutusuna** gider. Hiçbir parça "OK" sayılmaz. Kalite fonksiyonunda güvenli durum budur.
- **Fallback:** Süreç pilotu **dondurulur**. GESTICA son doğrulanmış veri setiyle çalışmaya devam eder.
- Operatöre alarm verilir ve olay kaydedilir. Manipülasyon şüphesi varsa LAB 6 akışı başlar.
- **Makinenin durması gerekmez.** Emniyet fonksiyonları YZ'den bağımsızdır, insan hâlâ korunuyor.

### 3 · Confidence düştü: model "emin değilim" diyor

**Eşik ne olmalı?** Eşik tahminle değil, **doğrulama verisiyle** belirlenir. Modelin yanlış OK verme oranının kabul edilebilir kaldığı en düşük güven değeri seçilir (V: örneğin 0,90). Eşik ve gerekçesi EP 15'e yazılır.

**Eşiğin altında:**

| Durum | Davranış | Kavram |
|---|---|---|
| Tek parça eşiğin altında | Parça karantinaya gider, insan muayene eder | Human override |
| Art arda N parça eşiğin altında (V: örneğin 5) | Süreç pilotu devre dışı kalır. Son doğrulanmış veri setine dönülür, ayarcı çağrılır. | Fallback |
| Düşük güven sürüyor | Hücre "insan kontrolünde üretim" moduna geçer: Tüm parçalar elle kontrol edilir veya üretim planlı olarak durdurulur. | Fail-safe |

## Karar tablosu · AI Fail-Safe Decision

| Başarısızlık | Tanımlanan güvenli davranış | Kavram |
|---|---|---|
| **Model yanlış sınıflandırdı** | OK parçalardan örneklemle insan muayenesi. Trend alarmı. Pilotun düzeltmeleri YZ dışında tanımlanan sınırlar içinde (GR-02). Parametre değişikliği ayarcı onayıyla. | Monitoring · human override |
| **Girdi manipüle edildi** | Referans parça, görüntü kalitesi ve çevrim eşleşmesi kontrolleri. Şüphede tüm parçalar karantinaya, pilot dondurulur, son doğrulanmış veri seti kullanılır. | Fail-safe · fallback · monitoring |
| **Confidence düştü** | Doğrulama verisiyle belirlenmiş eşik. Altında parça karantinaya ve insana, art arda düşükse pilot kapanır ve son iyi veri setine dönülür. | Confidence threshold · fallback · human override |

## Emniyet mimarisi kuralları

| Kural | Neden |
|---|---|
| YZ **emniyet bölgesine (Z4) hiçbir şey yazamaz** | Emniyet fonksiyonu tahmine bağlı olamaz |
| YZ'nin değiştirebileceği her parametrenin sınırı **YZ'nin dışında**, emniyetle ilgili kontrol sisteminde tutulur (GR-02) | Model yanılsa veya manipüle edilse bile emniyet payı korunur |
| Ayarcı YZ'yi her an **tek tuşla kapatabilir** | Human override |
| YZ'nin her kararı ve önerisi kaydedilir | Olay sonrası kanıt (LAB 6) ve doğrulama (EP 15) |
| Model güncellemesi, bir yazılım güncellemesi gibi test edilir ve imzalı olarak gelir | Tedarik zinciri dersi (R-03, LAB 5) |

**"Ask ARBURG" gibi soru-cevap asistanları için not:** YZ asistanının önerisi, emniyet prosedürünün önüne geçemez. Örneğin asistan bir arıza için "kapı anahtarını köprüleyin" benzeri bir öneri üretse bile **emniyet fonksiyonunu devre dışı bırakan hiçbir öneri uygulanmaz.** YZ cevapları yanlış olabilir; emniyet kararı insanda ve emniyet prosedüründedir.

## Risk Register'a ek

| ID | Senaryo | L | S | E | Skor | Önlemle |
|---|---|---|---|---|---|---|
| **R-06** | **Tedarik zinciri:** Değiştirilmiş bir model güncellemesi süreç pilotuna yüklenir. Bu, pilotun enjeksiyon ve kapama hızlarını yükseltmesi yoluyla **SF-01**'in güvenlik payını bozar. Sonuçta operatörün eli **plakalar arasında ezilir**. | 1 | 3 | 2 | **6 Orta** 🚩 | GR-02 sınırları ile L1 · **S1** · E2 = **2 Düşük** |

Ders, LAB 4 ile aynı: YZ'ye ne kadar güvenilirse güvenilsin, **S'yi düşüren YZ'nin doğruluğu değil, YZ'nin emniyet sınırlarını aşamamasıdır.**
