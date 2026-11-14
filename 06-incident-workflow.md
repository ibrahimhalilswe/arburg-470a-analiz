# LAB 6 · CRA Incident Simulation — Incident Workflow — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 13 Incident Response** · Durum: **Taslak**
> **[Kn]** = kaynak (bkz. [kaynaklar.md](kaynaklar.md)), **(V)** = varsayım.

## Senaryo (bu makineye uyarlandı)

> Saat **10:15**. Jump server kayıtlarında, Arburg servis hesabıyla **gece 03:00'te tanınmayan bir konumdan** açılmış bir uzak oturum görüldü. O saatte açık bir iş emri yoktu. Oturumda GESTICA veri setinin, yani **kalıp kapama hızı ve sıralama parametrelerinin değişmiş olabileceği** düşünülüyor. Koruyucu kapının (SF-01) durma davranışı henüz doğrulanmadı. **Makine şu an üretimde.** Vardiya sorumlusu sizsiniz.

Bu, LAB 3'teki **R-01** senaryosunun gerçekleşmiş olabileceği anlamına geliyor (18 Kritik, S3).

**Kavram:** Bir olayda en değerli iki şey **zaman** ve **kanıttır**. Sıralama hep aynı: **önce emniyet, hemen sonra kanıt.**

## Zaman çizgisi

### 1 · 10:15 – 10:30 · İlk 15 dakika

**Kimi ararım?**

| Kişi | Neden |
|---|---|
| OT sorumlusu | Makine ve ağ tarafını o bilir (RACI: R) |
| Üretim müdürü | Nihai karar onda (RACI: A) |
| İş güvenliği / emniyet sorumlusu | Konu bir emniyet fonksiyonu |
| IT güvenlik sorumlusu | Hesap ve ağ kayıtları onda |

**İlk üç eylem:**

1. **Makineyi güvenli şekilde durdur.** Normal durdurma kullanılır: çevrim tamamlanır, kalıp açık konumda bekler. Acil durdurma ancak tehlike varsa basılır. Gerekçe: Kapama hızı değişmiş olabilir. SF-01'in hâlâ yeterli olduğunu bilmiyoruz. **Bilmediğimiz bir emniyet fonksiyonuyla operatörü makinenin önünde çalıştırmayız.**
2. **Uzak bağlantıyı kes, ama kanıtı silme.** IIoT ağ geçidinin bağlantısı ağdan ayrılır (kablo veya firewall kuralı). Cihaz kapatılmaz, sıfırlanmaz, yeniden başlatılmaz.
3. **Makineyi olduğu gibi bırak ve etiketle.** Kontrol paneline "Olay incelemesi, dokunmayın, ayar değiştirmeyin" etiketi asılır. GESTICA'da hiçbir parametre "düzeltilmez".

> ❌ **En sık hata:** "Veri setini eski hâline getirip devam edelim." O tek işlem, saldırganın ne değiştirdiğinin kanıtını sonsuza dek siler.

### 2 · İlk 2 saat

**Emniyet güvende mi? Nasıl doğrularım?**

| Adım | Ne yapılır |
|---|---|
| 1 | Mevcut veri seti, **değiştirilmeden** USB'ye kopyalanır ve kanıt olarak saklanır |
| 2 | Bu kopya, en son bilinen iyi veri seti yedeğiyle (EP 12) **karşılaştırılır**: kapama hızı, ısıtma sınırları, robot sıralaması |
| 3 | Fark varsa: Ne değişti, hangi emniyet fonksiyonunu etkiliyor? (LAB 1, soru 7) |
| 4 | Bilinen iyi veri seti **yeni** bir kopya olarak yüklenir. Kanıt kopyasının üzerine yazılmaz. |
| 5 | LAB 5'teki emniyet testleri yapılır: SF-01 kapı açma ve durma süresi, SF-02, SF-03, SF-04, SF-05 |
| 6 | Testler geçmeden makine üretime dönmez. Kararı üretim müdürü verir (A). |

**Hangi kanıtı korurum?**

| Kanıt | Nerede | Neden |
|---|---|---|
| Jump server oturum kaydı | DMZ | Kim, ne zaman, nereden bağlandı |
| IIoT ağ geçidi ve firewall kayıtları | DMZ / C5 | Bağlantının yolu ve süresi |
| GESTICA değişiklik kayıtları ve son **2500 çevrimin** kaydı [K3] | Makine | Parametreler ne zaman değişti, çevrimler nasıl etkilendi |
| Değiştirilmiş veri setinin kopyası | USB, kilitli dolapta | Saldırganın tam olarak ne yaptığı |
| Arburg tarafındaki ARS kayıtları | Arburg | Gerçek bir teknisyen mi bağlandı, hesap mı çalındı? |
| Vardiya defteri ve operatör ifadeleri | Üretim | Gece makinede olağan dışı bir şey görüldü mü? |

Her kanıtın kim tarafından, ne zaman alındığı yazılır (delil zinciri).

**Hangi hesap kapatılır?**

- Kullanılan **Arburg servis hesabı** jump server'da hemen devre dışı bırakılır. Hesap silinmez, çünkü kayıtları kanıttır.
- Hesabın parolası ve MFA eşleşmesi sıfırlanır.
- Aynı parolanın kullanıldığı başka hesap var mı diye kontrol edilir.
- Arburg'a "bu hesapla gece 03:00'te sizden biri bağlandı mı?" diye sorulur.

### 3 · 24 saat · Erken uyarı

**Hangi bilgi hazır olmalı?**

- Ne oldu: Yetkisiz uzak erişim şüphesi, GESTICA parametrelerinin değiştirilmiş olma ihtimali
- Ne zaman fark edildi: 10:15. Olay ne zaman başladı: 03:00.
- Hangi ürün: ALLROUNDER 470 A, GESTICA sürümü, IIoT ağ geçidi
- Bir **açık istismar edildi mi**, yoksa hesap mı çalındı? (Bilinmiyorsa "bilinmiyor" yazılır.)
- Başka makineler etkilenmiş olabilir mi? Aynı servis hesabı başka makinelerde de kullanılıyor mu?
- Emniyet durumu: Makine durduruldu, emniyet testleri devam ediyor

**Kim bildirir?** CRA'da bildirim yükümlülüğü **üreticinindir**, yani Arburg'un. Bu yüzden işletmenin ilk görevi bilgiyi **hemen Arburg'a iletmektir**; ancak o zaman Arburg kendi 24 saatlik süresini tutturabilir. İşletme NIS2 kapsamındaysa kendi bildirim yükümlülüğü de vardır.

### 4 · 72 saat · Bildirim

| Konu | İçerik | Kim yapıyor? |
|---|---|---|
| Kök neden | Servis hesabı nasıl ele geçirildi? (Parola sızıntısı mı, ürün açığı mı?) | IT güvenlik (R) + Arburg |
| Etki | Hangi parametreler değişti? Kaç çevrim bu parametrelerle üretildi? Kimse yaralandı mı? | OT sorumlusu (R) |
| Emniyet sonucu | SF-01…SF-05 testlerinin sonuçları | İş güvenliği (R) |
| Alınan önlemler | Hesap kapatıldı, ARS varsayılan kapalıya alındı, veri seti geri yüklendi | OT sorumlusu (R) |
| Ürün kalitesi | Değişen parametrelerle üretilen parçalar ayrılıp incelendi mi? | Kalite (R) |
| Onay | Bildirimin içeriği | Üretim müdürü (A) |

### 5 · 14 gün · Son rapor

**Hangi kayıtlar korunmuş olmalı?**

- Jump server oturum kaydının **tamamı**: kayıt, ekran ve komutlar
- Firewall ve IIoT ağ geçidi kayıtları (olaydan en az birkaç gün öncesi dahil)
- GESTICA değişiklik ve çevrim kayıtları
- Değiştirilmiş veri seti ile bilinen iyi veri setinin karşılaştırma raporu
- Emniyet doğrulama testlerinin sonuçları ve imzası
- Arburg ile yazışmalar
- Bu zaman çizgisinin kendisi: kim ne zaman ne yaptı

**Makine Tüzüğü bağlantısı:** Ek III 1.1.9, makineye meşru veya meşru olmayan müdahalenin **kanıtının toplanmasını** istiyor. Bu kayıtlar o kanıttır.

## Akış özeti

```
10:15 Tespit
  │
  ├─► 1. Makineyi güvenli durdur        ← ÖNCE EMNİYET
  ├─► 2. Uzak bağlantıyı ayır (silme)
  ├─► 3. Etiketle, dokunma
  │
 2 sa ├─► Kanıtı kopyala → karşılaştır → bilinen iyi veri seti → emniyet testleri  ← HEMEN SONRA KANIT
  │    └─► Hesabı kapat (silme)
  │
24 sa ├─► Arburg'a bildir (CRA erken uyarısı üreticinin)
72 sa ├─► Kök neden, etki, önlem
14 gün└─► Son rapor + Risk Register'ı yeniden puanla
```

## Olaydan sonra Risk Register

LAB 3 köprüsü: "Bir olay sonrası Register yeniden değerlendirilir."

| Risk | Önce | Olaydan sonra | Neden |
|---|---|---|---|
| R-01 | L2 · S3 · E3 = 18 | **L3** · S3 · E3 = **27 Kritik** 🚩 | Kapı gerçekten istismar edildi. L artık bir tahmin değil, gözlem. |

Yeni önlemler (LAB 4 hedef mimarisi ve GR-01/GR-02) uygulandıktan sonra R-01 yeniden puanlanır. Bir olay, "bizde olmaz" varsayımını kanıtla çürütür.

## Bu belgedeki varsayımlar

- GESTICA'nın parametre **değişiklik kaydı** tuttuğu varsayıldı (V). Broşür sadece 2500 çevrimlik süreç kaydından söz ediyor [K3].
- Jump server ve oturum kaydı, LAB 4'teki hedef mimariden geliyor. Mevcut mimaride bunlar yoksa **kanıtın büyük kısmı hiç oluşmaz**. Bu da LAB 4'ün önemini gösteriyor.
