# LAB 1 · Safety Function Map — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 03 Safety Functions** · Durum: **Taslak**
> Kaynak kuralı: Yalnızca herkese açık bilgiler kullanıldı. **[Kn]** = kaynak (bkz. [kaynaklar.md](kaynaklar.md)), **(V)** = açık kaynakta doğrulanamayan varsayım.

## Makine özeti

| Özellik | Değer | Kaynak |
|---|---|---|
| Tip | Servo-elektrikli, mafsallı (toggle) plastik enjeksiyon makinesi | [K2] |
| Kapanma kuvveti | 1000 kN | [K1] |
| Kolonlar arası mesafe | 470 × 470 mm | [K1] |
| Kalıp açma stroku | en fazla 350 mm | [K1] |
| En yüksek enjeksiyon basıncı | 2500 bar (en küçük vida) | [K1] |
| Kontrol sistemi | GESTICA (PREMIUM sürümde standart) | [K2] |
| Kontrol mimarisi | Operatör arayüzü ve süreç kontrolü için **iki ayrı bilgisayar** | [K3] |
| Tipik çevre birimi | MULTILIFT SELECT robot sistemi | [K4] |
| Uygulanan emniyet standardı | EN ISO 20430:2020 (enjeksiyon makineleri emniyet gereksinimleri) | [K7] |

## İki kontrol yolu

```
NORMAL KONTROL YOLU · üretim
  Sensörler (konum, basınç, sıcaklık) → GESTICA süreç kontrol bilgisayarı → servo sürücüler → kalıp / vida / itici / robot
        ↑
  GESTICA operatör bilgisayarı (dokunmatik ekran, USB, yazıcı, tarayıcı)
        ↑
  Dış dünya: ARBURG Remote Service (ARS) · IIoT ağ geçidi · ALS ana bilgisayar · OPC UA      ← DİJİTAL KESİŞİM NOKTASI

SAFETY YOLU · koruma · AYRI olmalı
  Koruyucu kapı anahtarları · meme koruyucusu · acil durdurma · robot hücre kapısı
        → emniyet devresi (V: safety PLC / emniyet rölesi)
        → servo sürücülerde STO / kontaktör → Safe State
```

**Açık soru (V):** Arburg, emniyet fonksiyonlarının GESTICA'dan bağımsız bir emniyet devresinde mi çalıştığını yoğun olarak paylaşmıyor. Broşürdeki "iki ayrı bilgisayar" ifadesi [K3] arayüz ile süreç kontrolünün ayrıldığını gösteriyor. Ancak bu, **emniyet yolunun** ayrı olduğu anlamına gelmez. Bu belge, emniyet fonksiyonlarının donanımsal bir emniyet devresinde çalıştığını, ancak **hız, konum ve sıralama parametrelerinin GESTICA'dan geldiğini** varsayar.

## Görev cevapları

### 1. Tehlike nedir? İnsan neyden zarar görür?

| # | Tehlike bölgesi | ISO 12100 tehlike türü | Neden |
|---|---|---|---|
| T1 | Kalıp alanı (hareketli ve sabit plaka arası) | Mekanik: ezilme | 1000 kN kapanma kuvveti, 350 mm strok [K1] |
| T2 | Meme (nozzle) bölgesi | Termal: yanık · Mekanik: ezilme | Yüksek sıcaklıkta eriyik plastik püskürmesi, memenin kalıba yaslanması [K8] |
| T3 | Robot hücresi (MULTILIFT SELECT) | Mekanik: çarpma, ezilme | Robotun kalıp alanına girip çıkması [K4] |
| T4 | Isıtma bantları, silindir | Termal: yanık · Yangın | Isıtılmış silindir, bozunan malzemeden gaz |
| T5 | Elektrik panosu | Elektriksel: çarpılma | 400 V besleme, 21–32 kW bağlantı [K1] |

### 2. Emniyet fonksiyonları

| ID | Emniyet fonksiyonu | Koruduğu tehlike | Safe state |
|---|---|---|---|
| **SF-01** | Kalıp alanı koruyucu kapısı açılınca kalıp hareketleri durur, kapı açıkken kapanma başlamaz | T1 | Kapama ekseni durur, servo torku kesilir (STO) |
| **SF-02** | Meme koruyucusu açılınca enjeksiyon ve meme hareketi durur | T2 | Vida ve meme ekseni durur, enjeksiyon engellenir |
| **SF-03** | Acil durdurma: tüm hareketler durur | T1–T4 | Tüm eksenler durur, enerji kesilir |
| **SF-04** | Robot hücresi kapısı açılınca robot ve kalıp hareketleri durur | T3 | Robot ve kalıp ekseni durur |
| **SF-05** (V) | Isıtma aşırı sıcaklık sınırlayıcısı: sınır aşılırsa ısıtma kesilir | T4 | Isıtma enerjisi kesilir |

**Dikkat (emniyet fonksiyonu olmayan bir koruma):** "Kalıp koruma" (kolon gerinim ölçümüyle aktif kalıp koruması [K2]) **makineyi** ve kalıbı korur, **insanı değil**. Bozulursa kalıp hasar görür (S=1). Bu yüzden emniyet fonksiyonu listesine alınmadı.

### 3. Safety-related control system nerede?

Kapı anahtarları, meme koruyucu anahtarı, acil durdurma butonu ve robot hücre kapısı anahtarından gelen sinyallerin işlendiği emniyet devresi ile servo sürücülerdeki STO girişleri. GESTICA dokunmatik ekranı ve süreç kontrol bilgisayarı bu yolun **dışında** olmalıdır (V).

### 4. Normal PLC ile Safety PLC farkı

GESTICA süreç kontrolü üretimi yönetir: hız, konum, sıcaklık, sıralama. Emniyet kontrolcüsü ise sertifikalı, kendini denetleyen ve çift kanallı çalışır. Tek görevi tehlike anında makineyi güvenli duruma almaktır. Üretim kontrolcüsü çökse bile emniyet kontrolcüsü çalışmaya devam etmelidir.

### 5. PLr / SIL neden var?

SF-01 için: ezilme **ağır ve kalıcı** yaralanmadır (ISO 13849-1 S2). Operatör kalıp alanına her çevrimde veya her parça alımında yaklaşabilir (sık maruz kalma). Hareket hızlı olduğu için kaçınma zordur. Bu yüzden yüksek bir gerekli performans seviyesi beklenir (V: tipik olarak PL d). Bu seviye emniyet devresinin **konfigürasyonuna** bağlıdır. Konfigürasyon değişirse koruma da değişir.

### 6. Safe state nedir?

Hareket eksenleri için **"dur ve enerjiyi kes"** (STO). Isıtma için **"ısıtmayı kes"**. Bir istisna da var: kalıp kapama kuvveti altındayken enerji kesilirse kalıp hızlı açılmamalıdır. Güvenli durum bağlama göre tanımlanır.

### 7. Hangi dijital bağımlılıklar var?

| Bağımlılık | Neden önemli | Kaynak |
|---|---|---|
| Kalıp kapama hızı ve konum parametreleri GESTICA'da ayarlanır | Hız artarsa kapı açıldıktan sonraki **durma süresi** uzar | [K2] (V) |
| Grafik sıralama programlama (robot dahil, tek veri seti) | Sıra değişirse robot ve kalıp hareketlerinin zamanlaması değişir | [K3] |
| Isıtma bölgesi sıcaklık ayarları ve sınırları | Sınır yükselirse malzeme bozunur, basınç ve yangın riski oluşur | (V) |
| Veri seti aktarımı (USB, ALS, OPC UA) | Dışarıdan gelen veri seti, makinenin tüm hareket parametrelerini değiştirebilir | [K3] [K6] |
| Uzaktan servis (ARS) | Arburg teknisyeni dışarıdan kontrol sistemine bağlanabilir | [K5] |

### 8. Siber müdahale hangi noktadan bozar?

Emniyet devresinin kendisine dokunmadan: **parametreler üzerinden**. LAB 3'teki ISO 13855 mantığı burada da geçerli:

- Koruyucu kapı tasarlanırken, kapı açıldıktan sonra elin tehlikeye ulaşma süresi ile makinenin durma süresi karşılaştırılır.
- Kapama hızı GESTICA'dan yükseltilirse hareketli plakanın durma süresi ve mesafesi artar.
- Vida sökülmez, tel kesilmez. Sadece bir sayı değişir ve SF-01'in güvenlik payı biter.

Bu nedenle en kritik dijital kesişim noktası: **GESTICA'ya parametre yazabilen her yol** (dokunmatik ekran ve hesaplar, USB, ARS, ALS / OPC UA).
