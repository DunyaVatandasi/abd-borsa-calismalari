# Karar Defteri 📒

Bu dosya, çalışmalarımızda aldığımız kararları ve kuralları tek yerde tutar. Yeni bir sohbete başlarken Claude'a "KARARLAR.md'yi oku" demek yeterli.

Son güncelleme: 15/11/2026

## Temel ilkeler
- Tüm analizler **öğrenme amaçlıdır**; kendim ya da başkaları için **yatırım tavsiyesi içermez**.
- Paylaşım hedefi: önce öğrenmek, sonra X'te paylaşmak.
- Hazır, uçtan uca çalışan repolar tercih edilir; sıfırdan yazmak son çaredir.
- Kabul edilen lisanslar: **MIT, Apache-2.0, BSD**. GPL ve AGPL lisanslı repolar kullanılmaz.
- Teknik analizde 6 yıllık deneyim var; göstergelerin temel anlatımına gerek yok. Opsiyonlara hâkimim (çok bacaklı yapılar dahil).
- Çalışma ortamı: Windows, Google Colab, GitHub (`DunyaVatandasi/abd-borsa-calismalari`).

## Çalışma çerçevesi
Sıra: **kalabalığın sesi → teknik analiz → temel analiz → opsiyonlar**.

## Not defterleri
| # | Konu | Not |
|---|------|-----|
| 01 | Hareketli ortalamalar ve fiyatın ortalamaya uzaklığı | Uzaklık arttıkça risk artar ("mıknatıs" fikri) |
| 02 | Haber ve StockTwits duyarlılığı | Sosyal medya için **FinTwitBERT** seçildi (FinBERT çoğu yorumu nötr okuyordu). Tarama süresi `saat` parametresiyle ayarlanır. Yahoo haberleri engellenirse Google News yedeği devreye girer. |
| 03 | Temel analiz ve rakip karşılaştırması | yfinance verisi |
| 05 | TradingAgents ile tek tıkla ajan analizi | Claude API, anahtar Colab Gizli Anahtarlar'da (`ANTHROPIC_API_KEY`) |
| 06 | Opsiyon karar hunisi | API anahtarı ve maliyet yok |
| 07 | Gün içi stratejiler: ORB ve alternatifleri | Ayrı parkur. Bölüm 1: 15 dk ORB, yfinance 5 dk veri (son ~60 gün) |
| 07b | ORB Bölüm 2: 5 dk mı 15 dk mı, hangi teyit? | Sabit evren, hisse bazında sonuç, filtreler permütasyon + q testiyle |
| 07c | Önceden kayıtlı test: vadeli yön uyumu | 1 dk veri 2016–2026; Alpaca + Databento anahtarı gerekir (Colab Gizli anahtarlar) |

## Karar kartı ve X gönderisi biçimi (05)
- Kart hisse kodu ve **GG/AA/YYYY** tarihle başlar; "Hisse / Tarih" etiketi kullanılmaz.
- Hareketli ortalama değerleri parantez içinde yazılmaz.
- Analist hedef fiyatları ve F/K oranı yer alır.
- X gönderisi her zaman **"KOPYALAMAK İÇİN DÜZ METİN"** bölümünden kopyalanır. Ekrandaki kartta ＄ işareti görünür ve X'te cashtag olarak çalışmaz.
- Gönderiler "Öğrenme amaçlıdır, yatırım tavsiyesi değildir." ile biter.

## TradingAgents ayarları (05)
- **Tarama:** Sonnet ve 1 tartışma turu, hisse başına yaklaşık 0,15–0,20 $.
- **Derin inceleme:** Opus ve 2 tur, hisse başına yaklaşık 0,50 $.
- Ucuz ayarlar "Tut" kararına yatkın. CRDO'da Opus ve 2 tur, kararı "Tut"tan "Ağırlığı azalt"a çevirdi.
- Ajan raporlarında sorulacak soru: argüman gerçek bir bulguya mı, yoksa veri eksikliğine mi dayanıyor?

## Opsiyon kuralları (06)
- Vade: **21–45 gün** (15/11/2026'da alt sınır 7'den 21'e çıkarıldı: son haftaların gamma riski). Açık pozisyonlar vadeye **7 gün** kala kapatılır. Otomatik seçim, 30 güne en yakın ve bilançoyu kapsamayan vadedir.
- Kısa bacak: **0,20–0,30 delta**.
- Spread genişliği: **hisse fiyatının en fazla %4'ü**.
- Likidite: kontratta **en az 500 açık pozisyon**, ya da kendisi ve iki yanındaki ikişer strike'ın hepsinde en az 100; alış-satış makası orta fiyatın en fazla %10'u.
- Pahalı/ucuz kararı: **IV rank** (MarketChameleon'dan elle girilir) **50 ve üstü pahalı, 25 ve altı ucuz**. IV rank girilmezse IV/HV20 oranı kullanılır: 1,25 ve üstü pahalı, 0,9 ve altı ucuz.
- **Boyut çarpanı:** opsiyonlar ucuzsa normal boyutun 0,5'i, normalse 1, pahalıysa 0,6'sı ve tanımlı riskli yapı.
- **Kâr alma:** kredi yapılarında primin %50'si gelince kapat; satış dolar dolmaz GTC geri alım emri gir. Strateji tablosunda `kâr_al_geri_alım` sütunu bu fiyatı gösterir.
- **VIX filtresi:** VIX 35'in üstünde ya da 12'nin altındaysa yeni kısa prim pozisyonu açılmaz; notebook uyarı verir ve karta yazar.
- Vade alt sınırı, likidite, boyut çarpanı, kâr alma ve VIX kuralları 15/11/2026'da Dr. Arda Züber notlarından alındı.
- MarketChameleon'daki "IV30 % Rank" bir yüzdeliktir; klasik IV Rank'e karşılık gelen ölçü "52-Week Position"dır.
- Yön sinyalleri: söz (StockTwits), para (put/call oranı, çarpıklık), fiyat (SMA20 ve SMA50).
- **Belirli bir yapıya bağlılık yok:** debit, kredi, tek bacak ya da çok bacaklı; analiz neyi gösteriyorsa ona bakılır.
- Hisse adayları ApeWisdom (Reddit) ve StockTwits trend listelerinden gelir.
- En sağlıklı sonuç ABD seansı içinde, TSİ 17:30–24:00 arasında alınır. Hafta sonu son işlem fiyatları kullanılır.
- **Bilanço:** Seçilen vade bilançoyu kapsıyorsa bu durum kartta belirtilir. Bilançodan sonraki ilk vadenin beklenen hareketi, 7 gün kuralından bağımsız olarak bilgi amaçlı gösterilir. Strateji tablosundaki IV_düşüşü sütunu, bilanço sonrası IV düşüşünün (IV crush) her yapıya etkisini gösterir: lehine, aleyhine ya da nötre yakın.

## Gün içi stratejiler (07)
- **Ayrı parkur:** 01–06'dan bağımsızdır; opsiyon kuralları (21–45 gün vade vb.) burada geçerli değildir, yalnızca dayanak varlıkla (ETF / hisse) çalışılır. Pozisyonlar aynı gün kapanır.
- Başlangıç stratejisi **15 dk ORB** (Zarattini, Barbon ve Aziz 2024 kuralları: aralık yönü, aralık ucuna stop emri, 14 günlük ATR'nin %10'u stop, gün sonunda çıkış).
- Sonuçlar **R** cinsinden, **maliyet sonrası** ve **rastgele yönlü kıyasla** birlikte değerlendirilir. Kâr iki parçaya ayrılır: yapı katkısı (rastgele yönün ortalaması) ve yön katkısı (strateji − rastgele).
- 5 dk veri kullanıldığında sonuç iki mum içi varsayımla (kötümser / iyimser) **aralık** olarak okunur; aralık işaret değiştiriyorsa karar verilmez.
- Varsayılan maliyet: hisse başına 0,0035 $ komisyon (her yön), 1 bps kayma (likit ETF). Tek hisselerde kayma 2–5 bps.
- **Evren (15/11/2026):** SPY, QQQ, IWM ve Mag7 (AAPL, MSFT, NVDA, AMZN, GOOGL, META, TSLA). Liste **genişletilmez**: küçük ve manipülasyona açık hisseler yanıltıcı sonuç verir. Sonuçlar hisse hisse raporlanır; ETF'ler ve Mag7 ayrı gruptur. Tekil hisselerde kayma 3 bps.
- **Bölüm 1 ilk sonuç (24/08–13/11/2026):** makale ayarı (ATR %10 stop) 5 dk veride belirsiz; 5 dk aralık 15 dk'dan iyi; göreli hacmi en yüksek üçte birlik dilim +0,73 R. Hipotezdir, kural değil.
- **Bölüm 2 (07b):** 5 dk ve 15 dk yan yana. Teyitler tek tek test edilir: göreli hacim, VIX, vadeli gece aralığı, gece getirisi, ES–NQ uyumu; Mag7 bilanço günleri ayrıca gösterilir.
- **Vadeliler** sinyal değil gün bağlamıdır: SPY → ES, QQQ ve Mag7 → NQ, IWM → RTY. ES–NQ uyumsuzluğu (SMT) kamuya açık kanıtı olmadığı için test edilmeden kullanılmaz.
- **VWAP giriş filtresi olarak kullanılmaz** (15 dk sonunda aralığın içindedir, kırılım zaten VWAP'ın ötesindedir); yalnızca iz süren çıkış olarak test edilir.
- **Filtre kabulü:** gün bazında permütasyon testi, Benjamini–Hochberg q < 0,10, fark iki mum içi varsayımda ve örneklemin iki yarısında pozitif, en az 20 işlem. Bunu sağlayan filtre **aday**dır; daha uzun veride doğrulanmadan kural olmaz.
- Yol haritası: Stocks in Play (göreli hacim) → Noise Area (SPY) → VWAP trend → daha uzun 1 dk veri kaynağı.

## Önceden kayıtlı test: Vadeli yön uyumu (07c)
**Kayıt tarihi: 15/11/2026 — test verisi indirilmeden önce.** Bu bölüm test yapıldıktan sonra değiştirilmez; sonuçlar altına eklenir.

- **Keşif dönemi:** 24/08–13/11/2026 (07b, yfinance 5 dk). **Test dönemi:** 01/01/2016–21/08/2026, keşif dönemi hariç, **1 dk veri**. Bu dönem filtreler için hiç görülmemiş veridir.
- **Veri:** ETF ve hisseler için Alpaca (ücretsiz plan, tüm borsalar, 1 dk). Vadeliler için Databento CME Globex (GLBX.MDP3), 1 dk OHLCV, sürekli kontrat (ES, NQ, RTY). Notebook indirmeden önce maliyeti hesaplar; tahmini maliyet **40 $'ı aşarsa durur** (16/11/2026 güncellemesi: ilk sınır 10 $'dı; gerçek tahmin 37,70 $ çıkınca veri indirilmeden önce 40 $'a çıkarıldı. Yalnızca harcama koruması; test eşikleri değişmedi). API anahtarları yalnızca Colab "Gizli anahtarlar" bölümünde tutulur; sohbete ya da repoya yazılmaz.
- **Evren ve eşleştirme:** 07 ile aynı (SPY → ES, QQQ ve Mag7 → NQ, IWM → RTY). Sembol değişiklikleri (ör. FB → META, 2022) veri adımında kontrol edilir.
- **ORB kuralları (sabit):** aralık yönü (makale), aralık ucuna anlık stop emri, stop aralığın karşı ucu, gün sonunda çıkış, son giriş 15:30 ET. Simülasyon 1 dk mumlarla; mum içi belirsizlikte kötümser varsayım esastır, iyimser de raporlanır. Maliyet: hisse başına 0,0035 $ komisyon; kayma ETF'lerde 1 bps, hisselerde 3 bps (her yön).
- **Vadeli yön uyumu skoru (0–4):** her işlem için aşağıdakilerden kaçının işlem yönünde olduğu sayılır. Gece aralığı = önceki normal seans günü 18:00 ET ile 09:30 ET arası.
  1. Eşleşen vadelinin önceki seans kapanışından 09:30'a getirisi işlem yönünde.
  2. Vadelinin 09:30 fiyatı gece aralığının işlem yönündeki yarısında.
  3. ES ve NQ'nun aynı süreli açılış aralığı mumunun (kapanış − açılış) ikisi de işlem yönünde.
  4. Girişten **önce tamamlanmış** 1 dk mumlarda eşleşen vadeli gece yükseğini (uzun) / düşüğünü (kısa) aşmış.
  Vadeli verisi eksik günler testten çıkarılır.
- **Kural:** skor ≥ 3 ise işlem al, değilse alma.
- **Testler:** iki test (5 dk ve 15 dk aralık). Her biri için **doğrulandı** sayılmanın üç şartı:
  1. Tüm evrende "skor ≥ 3" işlemlerinin ortalama net R'ı (kötümser) − "skor ≤ 2" işlemlerinin ortalaması > 0; gün bazında permütasyon (en az 5.000 tekrar), tek yönlü **p < 0,025** (%5'in iki teste bölünmesi).
  2. Fark 2016–2020 ve 2021–2026 alt dönemlerinin **ikisinde de** pozitif.
  3. Fark iyimser mum içi varsayımda da pozitif.
  Biri bile sağlanmazsa sonuç **doğrulanmadı** olarak kaydedilir.
- **Raporlanır ama karar vermez:** skor 0–4 boyunca sonuç eğrisi, ETF / Mag7 ve sembol kırılımı, "skor ≥ 3" işlemlerinin mutlak net R'ı ve rastgele yönle kıyası, yıllara göre sonuç.

## Açık işler ve fikirler
- [ ] 07c: Alpaca ve Databento hesapları, Colab gizli anahtarları (Gökhan) → önceden kayıtlı testi çalıştırmak
- [ ] 06'yı ilk kez gerçek veriyle, seans içinde çalıştırmak
- [ ] IV kaydını açıp kendi IV geçmişimizi biriktirmek (Google Drive)
- [ ] Haftalık X içeriği için hisse karşılaştırma tablosu
- [ ] Kendi StockTwits ve teknik araçlarımızı TradingAgents'a bağlamak
- [ ] Makro veri için ücretsiz FRED anahtarı
- [ ] Zamanlanmış günlük çalıştırma
- [x] 07'yi gerçek veriyle çalıştırıp ilk sonuçları birlikte okumak (15/11/2026)
- [ ] 07b'nin aday filtrelerini daha uzun, 1 dakikalık veride doğrulamak (veri kaynağı araştırması)
- [ ] Opsiyon stratejilerinin geriye dönük testi (geçmiş opsiyon verisi gerektirir)
- [ ] Opsiyon rehberini okuyup notları Claude ile değerlendirmek (konu, yeniden açılana kadar beklemede)
- [ ] NVDA bilanço testi: 16/11 seans içinde bilanço vadesi beklenen hareketini not et, 19/11 sabahı gerçekleşenle karşılaştır (hatırlatmalar kurulu)

## İlgili belgeler
- Dr. Arda Züber'in X gönderileri, sitesi ve blogundan opsiyon notları: https://claude.ai/artifact/5hYW8KowAYihhrcRBUSNAo
- Opsiyon öğrenme rehberi (orta-ileri seviye, örnek hesaplar ve grafiklerle): https://claude.ai/artifact/QeLwWDqvH1eZWSJVGqqnpT
- Repo araştırma raporu (MIT, Apache-2.0 ve BSD lisanslı repolar): https://claude.ai/artifact/NaMrKi1hWgs54BuUDkofs7
