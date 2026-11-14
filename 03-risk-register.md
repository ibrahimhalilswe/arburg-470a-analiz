# LAB 3 · Threat & Risk — Risk Register — ARBURG ALLROUNDER 470 A

> Evidence Pack: **EP 04 Cyber Risk Assessment** · Durum: **Taslak**
> Makine: ALLROUNDER 470 A, GESTICA kontrollü (PREMIUM) · Hazırlayan: İbrahim Halil Yılmaz
> Formül: **LAB 2'deki kapı × LAB 1'deki korunan fonksiyon = tehdit senaryosu**
> Skor = L × S × E (her biri 1–3) · Kritik 18–27 · Yüksek 9–17 · Orta 4–8 · Düşük 1–3 · **S = 3 → S3 bayrağı**

## 1 · Seçilen kapılar (en az 5)

Hepsi [LAB 2 envanterinde](02-envanter-zone-conduit.md) kayıtlı:

| Kapı | Envanter ID | Kategori |
|---|---|---|
| ARS uzaktan servis, IIoT ağ geçidi üzerinden | B-01, D-06 | Dış bağlantı |
| Paylaşılan GESTICA ayarcı hesabı | H-01, H-02 | Hesap |
| USB / servis dizüstü ile yazılım güncellemesi | D-07, B-05, V-04 | Yazılım / firmware |
| ALS / OPC UA veri seti aktarımı | B-03, B-04, V-01 | Kritik veri akışı |
| Ofis ağı (oltalama girişi) → C1 conduit | Z1 → Z2 | Dış bağlantı |

## 2 · Senaryolar

Kalıp: *[PROFİL], [GİRİŞ] üzerinden içeri girer ve [EYLEM] yapar; bu, [ZİNCİR] yoluyla [EMNİYET FONKSİYONU]'nu bozar ve sonuçta [FİZİKSEL ETKİ] oluşur.*

**R-01 · Fırsatçı dış**
Fırsatçı dış saldırgan, sürekli açık bırakılmış ARS / IIoT ağ geçidi bağlantısı üzerinden içeri girer ve GESTICA'daki kalıp kapama hızını artırır. Bu, hareketli plakanın durma süresini uzatarak **SF-01 (koruyucu kapı kilitlemesi)**'nin güvenlik payını bozar. Sonuçta kapıyı açıp elini uzatan operatörün eli **plakalar arasında ezilir** (mekanik, ezilme).

**R-02 · Meraklı içeriden**
Çevrimi hızlandırmak isteyen bir ayarcı, paylaşılan GESTICA hesabıyla ısıtma bölgesi sıcaklıklarını ve sınırlarını yükseltir. Bu, malzemenin bozunup silindirde gaz basıncı oluşturması yoluyla **SF-02 (meme koruyucusu)** ve **SF-05 (aşırı sıcaklık sınırı)**'nın koruma varsayımını aşar. Sonuçta boşaltma (purging) sırasında meme bölgesinden püsküren eriyik operatörde **yanık** oluşturur (termal).

**R-03 · Tedarik zinciri**
Ele geçirilmiş bir servis dizüstü bilgisayarı veya değiştirilmiş bir güncelleme paketi, USB üzerinden GESTICA'ya yüklenir ve robot / kalıp sıralama mantığını değiştirir. Bu, robotun hücre kapısı sinyalini beklemeden hareket etmesine yol açarak **SF-04 (robot hücresi koruması)**'nü bozar. Sonuçta hücreye giren bakım personeline robot **çarpar veya onu ezer** (mekanik).

**R-04 · Hedefli**
Hedefli saldırgan oltalama e-postasıyla ofis ağına girer, ALS sunucusuna yanal hareket eder ve OPC UA / ALS üzerinden makineye değiştirilmiş bir **veri seti** gönderir. Isıtma sınırları yükseltilir ve ekrana normal değerler gösterilir (sahte veri). Bu, operatörün aşırı ısınmayı fark etmemesi yoluyla **SF-05**'i bozar. Sonuçta silindir ve hazne çevresinde **yangın** çıkar.

**R-05 · Fırsatçı dış (emniyet bulgusu değil, karşılaştırma için)**
Fırsatçı dış saldırgan, internete açık bir ofis sunucusundan girip ALS sunucusunu fidye yazılımıyla şifreler. Makineye veri seti gönderilemez, üretim durur. *"Peki sonra ne olur?"* Operatörler veri setini elle girer ve parametre hatası riski artar, ama hiçbir emniyet fonksiyonu doğrudan bozulmaz. **Fiziksel sonuç yok: S = 1.**

## 3 · Puanlama (bugünkü hâl)

| ID | L | Gerekçe | S | Gerekçe | E | Gerekçe | Skor | Bant |
|---|---|---|---|---|---|---|---|---|
| R-01 | 2 | ARS şifreli ve müşteri onayı istiyor [K5], ama onay sürekli açık bırakılmış (V). Kimlik bilgisi gerekiyor. | 3 | 1000 kN altında ezilme: kalıcı yaralanma, ölüm (ISO 13849-1 S2) | 3 | Bağlantı internetten sürekli erişilebilir | **18** | **Kritik** 🚩S3 |
| R-02 | 3 | Paylaşılan hesap, kimlik doğrulama yok | 2 | Genellikle el ve kolda geri dönüşlü yanık. Yüz veya göz teması S3'e çıkarabilir (**tartışmalı puan**). | 1 | Sadece makine başında, fiziksel erişimle | **6** | Orta |
| R-03 | 1 | Arburg kendi yazılımını üretiyor, kapalı işletim sistemi var [K3]. Birden çok adımın zincirlenmesi gerekiyor. | 3 | Robot çarpması veya ezilme: ağır yaralanma | 1 | Fiziksel servis bağlantısı, makine başında | **3** | Düşük 🚩S3 |
| R-04 | 1 | Uzman ve sabırlı saldırgan, oltalama, yanal hareket ve sahte veri zinciri | 3 | Yangın (ISO 12100: yangın/patlama) | 2 | Fabrika ağından, OPC UA / ALS ile erişilebilir | **6** | Orta 🚩S3 |
| R-05 | 3 | İnternete açık ofis sunucusu, hazır fidye araçları | 1 | Yaralanma yok, üretim kaybı | 3 | Doğrudan internetten | **9** | Yüksek |

## 4 · Risk Register (sıralı)

| Sıra | ID | Profil | Etkilenen SF | Zone / conduit | Skor | S3 | Önerilen önlem | Sahibi (RACI A) | Hedef |
|---|---|---|---|---|---|---|---|---|---|
| 1 | R-01 | Fırsatçı dış | SF-01 | C5 | 18 Kritik | 🚩 | ARS varsayılan kapalı ve süre sınırlı, jump server, MFA, oturum kaydı. Kapama hızına emniyet devresinden bağımsız üst sınır. | Üretim müdürü | 30 gün |
| 2 | R-05 | Fırsatçı dış | — | C1 | 9 Yüksek | | Ofis sunucusunu internetten kaldır, ALS yedeği ve geri yükleme testi | IT müdürü | 90 gün |
| 3 | R-04 | Hedefli | SF-05 | C1 → C2 | 6 Orta | 🚩 | OPC UA'da yazma yetkisini kapat, veri seti değişikliğine onay şartı, donanımsal aşırı sıcaklık sınırlayıcısı | OT sorumlusu | 90 gün |
| 4 | R-02 | Meraklı içeriden | SF-02, SF-05 | Z3 (HMI) | 6 Orta | | Kişisel GESTICA hesapları, ısıtma sınırlarına yetki seviyesi | Bakım şefi | Sonraki bakım |
| 5 | R-03 | Tedarik zinciri | SF-04 | C4 | 3 Düşük | 🚩 | Sadece imzalı güncelleme kabul et, servis dizüstüsünü tara, USB kayıt altında | OT sorumlusu | Sonraki bakım |

> **Ek:** LAB 7'de YZ süreç pilotuna ait **R-06** senaryosu eklendi (6 Orta 🚩). Ayrıntılar [07-ai-fail-safe.md](07-ai-fail-safe.md) dosyasında.

### S3 bayraklı liste (yönetime ayrıca raporlanır)

R-01 (18) · R-04 (6) · R-06 (6) · **R-03 (3)**

R-03'ün skoru 3, yani "Düşük". Ama sonunda yine bir insan ağır yaralanıyor. **S3 kuralı** gereği skordan bağımsız olarak görünür kalır ve en az bir önlem ya da yazılı kabul kararı olmadan kapatılmaz.

## 5 · Föy soruları

**1. En yüksek öncelikli senaryo hangisi, neden?**
R-01. İnternetten sürekli erişilebilen bir kapı ile 1000 kN'luk bir ezilme tehlikesini aynı zincirde birleştiriyor. Hem en olası hem en ağır sonuçlu senaryo bu.

**2. S = 3 olup skoru düşük kalan senaryo var mı? Nasıl görünür kılınır?**
Var: R-03 (skor 3). S3 bayrağıyla ayrı listede tutulur, yönetime raporlanır ve "imzalı güncelleme" önlemi alınana kadar kapatılmaz.

**Grubun en çok tartıştığı puan:** R-02'nin S değeri (2 mi, 3 mü?). Eriyik yüze veya göze gelirse sonuç kalıcıdır. Meme koruyucusunun konumu bilinmeden kesin karar verilemez.

## 6 · Doğal ve artık risk (R-01)

| Durum | L | S | E | Skor | Not |
|---|---|---|---|---|---|
| Bugün | 2 | 3 | 3 | **18 Kritik** | S3 bayrağı |
| + Erişim önlemleri (jump server, MFA, varsayılan kapalı) | 1 | 3 | 2 | **6 Orta** | S3 bayrağı sürer |
| + Emniyet bağımsızlığı (kapama hızı ve durma süresi emniyet devresince izlenir, GESTICA'dan değiştirilemez) | 1 | 1 | 2 | **2 Düşük** | Siber yol fiziksel sonuca ulaşamaz |

Erişim önlemleri L ve E'yi düşürür. **S'yi yalnızca emniyet tasarımı düşürür.**

## 7 · Kalite kontrol listesi

- [x] Her senaryonun profili dört tipten biri (dördü de kullanıldı)
- [x] Her giriş kapısı LAB 2 envanterinde kayıtlı
- [x] Etkilenen emniyet fonksiyonu adıyla yazılı (SF-01, 02, 04, 05)
- [x] Sonuç fiziksel ve ISO 12100 türünde. R-05 bilerek S=1 bırakıldı.
- [x] Her puanın yanında tek cümlelik gerekçe var
- [ ] Puanlar OT, IT, emniyet ve servis tarafından **birlikte** gözden geçirilmeli. Bu taslak tek kişi tarafından hazırlandı.
