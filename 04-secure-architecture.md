# LAB 4 · Secure Remote Access — Secure Architecture — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 02 Network Architecture**, **EP 06 Remote Access Procedure** · Durum: **Taslak**
> **[Kn]** = kaynak (bkz. [kaynaklar.md](kaynaklar.md)), **(V)** = varsayım.
> Girdi: [LAB 3 Risk Register](03-risk-register.md) → en yüksek skorlu senaryo **R-01 (18 Kritik, S3)**

**Kavram:** İki katman var ve ikisi de şart:
1. **Erişim güvenliği:** Saldırganın içeri girmesini zorlaştırmak.
2. **Emniyet bağımsızlığı:** Saldırgan içeri girse bile insanın korunması.

## Görev A · Mevcut → Hedef

### MEVCUT (savunmasız)

Arburg ARS bağlantısı şifreli ve müşteri onayı istiyor [K5]. Bu iyi bir başlangıç. Ancak sahada tipik olarak aşağıdaki durumlar görülür. Bu analizde mevcut durumun böyle olduğu **varsayıldı (V)**:

```
Arburg teknisyeni ──internet──► IIoT ağ geçidi ──► GESTICA (Z3)
                                 (sürekli bağlı)        │
Ofis ağı (Z1) ──► ALS / OPC UA ──────────────────────────┘ (okuma + yazma açık)
USB bellek / servis dizüstü ─────────────────────────────┘ (kayıtsız)
```

| Sorun | Neden riskli | İlgili risk |
|---|---|---|
| ARS onayı "kolaylık olsun" diye sürekli açık bırakılmış | Kapı internetten her an erişilebilir (E=3) | R-01 |
| IIoT ağ geçidi doğrudan makine bölgesine bağlı, arada ara nokta yok | İlk erişim doğrudan GESTICA'ya ulaşır | R-01 |
| Tek ortak "servis" hesabı | Kimin bağlandığı ve ne değiştirdiği bilinmez | R-01, LAB 6 |
| Fabrika tarafında oturum kaydı yok | Parametre değişikliğinin kanıtı yok | LAB 6 |
| OPC UA / ALS üzerinden veri seti yazma her zaman açık | Ofis ağından makine parametrelerine yol açılır | R-04 |
| USB kullanımı kayıtsız | Değiştirilmiş yazılım ya da veri seti fark edilmeden yüklenir | R-02, R-03 |

### HEDEF (güvenli)

```
Arburg teknisyeni
   │  (1) kişisel hesap + (2) MFA
   ▼
İnternet ──VPN──► [Firewall] ──► DMZ: JUMP SERVER ──(oturum kaydı)──► [Firewall C2] ──► IIoT ağ geçidi ──► GESTICA (Z3)
                                     ▲                                       ▲
                                     │ (3) varsayılan KAPALI                  │ sadece gerekli servis,
                                     │     iş emriyle açılır,                 │ sadece bu makine
                                     │     süre dolunca otomatik kapanır      │
                                     │
                         Makine başındaki sorumlunun onayı (ARS müşteri onayı [K5])

Ofis (Z1) ──► ALS (Z2) ──► OPC UA: OKUMA serbest · veri seti YAZMA sadece onaylı iş emriyle
USB ──► sadece kayıtlı ve taranmış bellek · yükleme kayda geçer

Emniyet bölgesi (Z4): hiçbir uzak yoldan YAZILAMAZ, sadece durum okunur
```

### Beş ilke (ve VPN) nasıl uygulandı

| # | İlke | Bu makinede karşılığı | Hangi puanı düşürür |
|---|---|---|---|
| 1 | Ara nokta (doğrudan açık değil) | IIoT ağ geçidi internete değil, DMZ'deki jump server'a bağlanır | E: 3 → 2 |
| 2 | Kişisel hesap (ortak yok) | Her Arburg teknisyeninin ve her ayarcının kendi hesabı olur. GESTICA kullanıcı yetkileri [K3] kişiye göre verilir. | L, LAB 6 kanıtı |
| 3 | İkinci faktör (MFA) | Jump server girişinde parola + ikinci faktör | L: 2 → 1 |
| 4 | Varsayılan kapalı, süre sınırlı | Bağlantı bir iş emriyle açılır, örneğin 2 saat sonra kendiliğinden kapanır. ARS müşteri onayı bu kurala bağlanır. | E: 3 → 2 |
| 5 | Oturum kaydı | Jump server'da kim, ne zaman bağlandı ve ne yaptı kaydedilir. GESTICA'daki değişiklik kayıtları da saklanır. | Kanıt (LAB 6) |
| + | VPN | Jump server'a sadece şifreli tünelle ulaşılır. ARS'nin kendi şifrelemesi [K5] buna ek bir katmandır. | L |

## Görev B · Functional safety köprüsü

### 1. Saldırgan tüm önlemlere rağmen içeri girerse emniyet fonksiyonu etkilenir mi? Neden?

**Bugünkü mimaride: evet, dolaylı olarak.**

- Saldırganın emniyet devresine (Z4) doğrudan yazamadığını varsayıyoruz (V).
- Ama GESTICA'ya ulaşan biri **kalıp kapama hızını** değiştirebilir. Hız artarsa, kapı açıldıktan sonra hareketli plakanın durma süresi uzar. SF-01, tasarlandığı güvenlik payını kaybeder (bkz. [LAB 1, soru 8](01-safety-function-map.md)).
- Yani emniyet fonksiyonu "çalışır" görünür ama artık yeterli değildir. Kapıyı açan operatörün eli plaka durmadan tehlikeye ulaşabilir.

Erişim önlemleri (Görev A) içeri girmeyi zorlaştırır, ama **içeri girildikten sonra** insanı korumaz. Bu yüzden ikinci katman gerekli.

### 2. Gereksinimler (çözüm değil)

> **Tarafsızlık notu:** Bu föy çözüm tasarlatmaz, gereksinimi tanımlatır. Gereksinimin nasıl karşılanacağı (hangi donanım, hangi yazılım) üreticinin ve işletmenin kararıdır.

| ID | Gereksinim |
|---|---|
| **GR-01** | Koruyucu kapı açıldığında kalıp hareketinin durma süresi ve mesafesi, GESTICA üzerinden yapılabilecek **hiçbir parametre değişikliğinden etkilenmeden**, tasarımda hesaplanan güvenlik payı içinde kalmalıdır. |
| **GR-02** | Emniyet fonksiyonlarını etkileyen parametrelerin sınırları (örneğin kapama hızının üst sınırı), emniyetle ilgili kontrol sisteminde tutulmalı ve **uzak erişim yolundan değiştirilememelidir**. |
| **GR-03** | Emniyet bölgesine (Z4) uzak erişim, ALS veya OPC UA yollarından **yalnızca durum okunabilmeli**, hiçbir değer yazılamamalıdır. |
| **GR-04** | Uzak bir oturum açıkken makine hareketleri **uzaktan başlatılamamalıdır**. Hareket başlatmak makine başında bir kişi gerektirmelidir. |
| **GR-05** | Emniyetle ilgili bir parametre ya da sıralama değişikliği **makine başında fiziksel onay** gerektirmeli ve değişiklik kaydı (kim, ne zaman, eski ve yeni değer) saklanmalıdır. |
| **GR-06** | Yazılım güncellemesi veya veri seti değişikliğinden sonra, makine üretime dönmeden önce emniyet fonksiyonlarının hâlâ doğru çalıştığı **doğrulanmalıdır** (safety validation, bkz. LAB 5). |

## Sonuç · R-01'in artık riski

| Durum | L | S | E | Skor | Ne değişti? |
|---|---|---|---|---|---|
| Bugün (mevcut) | 2 | 3 | 3 | **18 Kritik** 🚩 | — |
| + Görev A (jump server, VPN, MFA, kişisel hesap, varsayılan kapalı, kayıt) | 1 | 3 | 2 | **6 Orta** 🚩 | Girmek zorlaştı ve kapı daha az açık. Ama girilirse sonuç hâlâ ağır. |
| + Görev B (GR-01…GR-06 karşılanırsa) | 1 | 1 | 2 | **2 Düşük** | Siber yol artık fiziksel sonuca ulaşamıyor |

**Erişim önlemleri L ve E'yi düşürür. S'yi yalnızca emniyet tasarımı düşürür.** En güçlü tek yatırım GR-01 ve GR-02'dir: GESTICA ele geçirilse bile makine güvenli tarafta kalır.

### Diğer senaryolara etkisi

| Risk | Önce | Hedef mimariyle | Neden |
|---|---|---|---|
| R-04 (hedefli, ALS / OPC UA) | 6 Orta 🚩 | L1 · S3 · E1 = **3 Düşük** 🚩 | OPC UA'da veri seti yazma artık makine başında onaylanan bir iş emrine bağlı, bu yüzden yol fiilen makine başına indi (E=1). S3 bayrağı sürüyor: GR-02 ısıtma sınırlarına da uygulanmalı. |
| R-03 (tedarik zinciri, USB) | 3 Düşük 🚩 | **3 Düşük** 🚩 | Uzak erişim önlemleri USB yolunu etkilemez. GR-05 ve GR-06 ile ele alınmalı. |
| R-02 (meraklı içeriden) | 6 Orta | L2 · S2 · E1 = **4 Orta** | Kişisel hesap paylaşımı bitirir. Isıtma sınırı için GR-02 gerekir. |
