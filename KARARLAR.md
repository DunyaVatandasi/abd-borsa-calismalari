# Karar Defteri 📒

Bu dosya, çalışmalarımızda aldığımız kararları ve kuralları tek yerde tutar. Yeni bir sohbete başlarken Claude'a "KARARLAR.md'yi oku" demek yeterli.

Son güncelleme: 17/11/2026

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
| 07d | Önceden kayıtlı test: seviye ve EMA teyitleri | PDH/PDL, EMA 13/48/200, PMH/PML, gövde kapanışı; Alpaca anahtarı yeterli |
| 07e | Önceden kayıtlı test: seviye + EMA geri çekilme stratejisi | Kırılım ve ret kolları; 07c/07d Drive önbelleğini kullanır |
| 08 | Teknik analiz sistemi: The Strat (sade), önceden kayıtlı test | yfinance günlük veri 2010–13/11/2026, günlük ve haftalık barlar; API anahtarı gerekmez. Karıştırılmış bar kıyası Colab'da birkaç dakika sürer. **Sonuç: doğrulanmadı (17/11/2026)** |

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
- **PARKUR KAPATILDI (16/11/2026).** Önceden kayıtla test edilen 12 gün içi kural/filtreden yalnızca biri sınırda doğrulandı (07c, 5 dk vadeli yön filtresi, skor ≥ 3 işlemleri +0,017 R). Özet, 2016–2026, 1 dk veri, maliyet sonrası işlem başı:
  - 5 dk ORB −0,021 R; 15 dk ORB +0,007 R (filtresiz).
  - 07d: PDH/PDL, EMA 13/48/200, PMH/PML filtreleri ve gövde kapanışı girişi — 8 testin 8'i doğrulanmadı.
  - 07e: seviye + EMA geri çekilme stratejisi — kırılım −0,323 R, ret −0,251 R; 11 yılın hiçbirinde pozitif değil.
  - **Ders:** bu evrende 1 dk gün içi kırılım tarzı kurallar maliyetten sonra kenar bırakmıyor; 60 günlük örneklemde görülen etkiler 10 yılda ~10 kat küçüldü. Sonuca bakarak yeni filtre/stop denemek veri madenciliğine dönüşeceği için parkur burada bırakıldı.
  - Notebook'lar (07–07e) ve Drive önbelleği (Alpaca seans + piyasa öncesi, Databento ES/NQ/RTY) referans olarak kalır. Yeniden açılırsa yalnızca yeni ve önceden kayıtlı bir hipotezle, tercihen 21/08/2026 sonrası veriyle.
- **Ayrı parkur:** 01–06'dan bağımsızdır; opsiyon kuralları (21–45 gün vade vb.) burada geçerli değildir, yalnızca dayanak varlıkla (ETF / hisse) çalışılır. Pozisyonlar aynı gün kapanır.
- **ORB rafa kaldırıldı (16/11/2026):** 07c ve 07d'de hiçbir teyit mutlak kenar üretmedi (5 dk ORB maliyet sonrası −0,021 R). Notebook'lar ve sonuçlar referans olarak kalır; yeni gün içi stratejiler bu çıtayla kıyaslanır. Sıradaki strateji: 07e seviye + EMA geri çekilme.
- (Rafa kaldırılmadan önceki karar) Ana aralık **5 dk ORB** (16/11/2026; 07c'de vadeli yön filtresi yalnızca 5 dk'da doğrulandı). 15 dk karşılaştırma olarak her raporda yan yana gösterilir; 10 yıllık veride ikisinin mutlak sonucu da sıfıra yakın (5 dk −0,021 R, 15 dk +0,007 R, filtresiz). Yeni kurallar iki aralıkta birden ve önceden kayıtla test edilir. Temel kurallar Zarattini, Barbon ve Aziz 2024: aralık yönü, aralık ucuna stop emri, gün sonunda çıkış (başlangıçta 15 dk ve ATR %10 stop ile incelendi).
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

### Sonuç (16/11/2026 · Alpaca + Databento 1 dk, 2016–2026)
Notebook çıktısı, değiştirilmeden:
- 5 dk: DOĞRULANDI · fark 0,057 R, p=0,0234, 2016–2020 0,107 / 2021–2026 0,016, iyimser 0,058, 22.039 işlem
- 15 dk: DOĞRULANMADI · fark 0,034 R, p=0,0964, 2016–2020 0,026 / 2021–2026 0,042, iyimser 0,034, 20.748 işlem

Karar vermeyen ama okunması gereken notlar:
- 5 dk'da "skor ≥ 3" işlemlerinin mutlak sonucu yalnızca +0,017 R (filtresiz −0,021 R). Kural kötüyü ayıklıyor ama tek başına anlamlı bir kâr kenarı üretmiyor.
- Etki tekdüze değil: skor 3 → −0,057 R, skor 4 → +0,109 R. Asıl katkı dört şartın hepsinin tuttuğu günlerden geliyor.
- Etki son dönemde zayıf: 2021–2026 farkı 0,016 R; 2023 (−0,166) ve 2025 (−0,090) negatif. 11 yılın 7'sinde pozitif.
- Sembolde IWM (−0,088) ve NVDA (−0,011) negatif; diğer sekizi pozitif.
- 07b'deki 60 günlük tahmin (+0,4–0,65 R) uzun veride ~10 kat küçüldü; kısa örneklem etkiyi büyük göstermişti.

## Önceden kayıtlı test: Seviye ve EMA teyitleri (07d)
**Kayıt tarihi:** 16/11/2026, piyasa öncesi veri indirilmeden ve aşağıdaki filtreler 2016–2026 verisinde hiç hesaplanmadan önce. Kaynak: Gökhan'ın bulduğu "önceki güne göre işlem" belgesi (PDH/PDL, PMH/PML, 13/48/200 EMA, 15 dk gövde kapanışı). Belgenin ret senaryoları ve 13 EMA'ya geri çekilmede giriş bu teste dahil değildir (ayrı strateji).
- **Veri:** Alpaca 1 dk, 01/01/2016–21/08/2026, aynı evren (SPY, QQQ, IWM + Mag7). Seans mumları 07c önbelleğinden; piyasa öncesi (04:00–09:30 ET) mumları yeni indirilir. Vadeli veri kullanılmaz.
- **Temel ORB (07c ile aynı, sabit):** aralık yönü, aralık ucuna anlık stop emri, stop aralığın karşı ucu, gün sonunda çıkış, son giriş 15:30; kötümser varsayım esas, iyimser raporlanır; komisyon 0,0035 $/hisse, kayma ETF 1 bps, hisse 3 bps. Aralıklar: 5 dk (ana) ve 15 dk.
- **F1 · PDH/PDL "yol açık":** uzun işlemde giriş fiyatı önceki seansın en yükseğinin (PDH) üstünde, kısa işlemde önceki seansın en düşüğünün (PDL) altında.
- **F2 · EMA hizalaması:** seans içi 5 dk kapanışlarından (günler arası kesintisiz) 13, 48 ve 200 EMA. Girişten önce tamamlanmış son 5 dk mumunda uzun için EMA13 > EMA48 > EMA200, kısa için tersi. 200 mumdan az geçmişi olan günler bu testten çıkar.
- **F3 · PMH/PML:** uzun işlemde giriş fiyatı aynı günün piyasa öncesi en yükseğinin (PMH, 04:00–09:29) üstünde, kısada piyasa öncesi en düşüğünün (PML) altında. 10'dan az piyasa öncesi mumu olan günler bu testten çıkar.
- **V · Gövde kapanışı ile giriş (varyant):** aralık uzunluğundaki mumlarla (5 dk ORB'de 5 dk, 15 dk ORB'de 15 dk), kapanışı aralığın dışında olan ilk mumun kapanışında giriş (15:30'a kadar); stop ve çıkış aynı. Temel ORB'nin işlem açtığı her sembol-gün için fark = varyant net R (işlem yoksa 0) − temel net R.
- **Filtre testi (F1–F3):** fark = filtre sağlanan işlemlerin ortalama net R'ı − sağlanmayanların (kötümser). Gün bazında permütasyon (07c ile aynı yöntem), 5.000 tekrar, tek yönlü.
- **Varyant testi (V):** sembol-gün farklarının ortalaması; gün bazında işaret çevirme permütasyonu, 5.000 tekrar, tek yönlü.
- **Doğrulandı sayılmak için (her aralıkta ayrı, dört testin her biri için):** (1) p < **0,00625** (0,025 ÷ 4, aynı aralıktaki dört test için Bonferroni), (2) fark 2016–2020 ve 2021–2026'da ayrı ayrı pozitif, (3) fark iyimser varsayımda da pozitif, (4) filtrelerde iki grubun her birinde en az 200 işlem.
- **Raporlanır ama karar vermez:** filtre sağlanan işlemlerin mutlak net R'ı, üç filtrenin birlikte sağlandığı işlemler, ETF / Mag7 ve sembol kırılımı, yıllara göre sonuç, filtrelerin 07c skoru ile örtüşmesi.
- Eşikler ve tanımlar veri görüldükten sonra değiştirilmez; değiştirilirse test geçersiz sayılır ve yeni kayıt gerekir.

### Sonuç (16/11/2026 · Alpaca 1 dk seans + piyasa öncesi, 2016–2026)
Notebook çıktısı, değiştirilmeden:
- 5 dk · F1 · PDH/PDL yol açık: DOĞRULANMADI · fark 0,033 R, p=0,1040, 2016–2020 0,051 / 2021–2026 0,016, iyimser 0,028, işlem 6.968 / 16.127
- 5 dk · F2 · EMA 13/48/200: DOĞRULANMADI · fark -0,011 R, p=0,6621, 2016–2020 0,030 / 2021–2026 -0,046, iyimser -0,015, işlem 8.564 / 14.514
- 5 dk · F3 · PMH/PML: DOĞRULANMADI · fark 0,046 R, p=0,0348, 2016–2020 0,058 / 2021–2026 0,036, iyimser 0,044, işlem 10.769 / 11.508
- 5 dk · V · gövde kapanışı: DOĞRULANMADI · fark 0,003 R, p=0,3507, 2016–2020 -0,012 / 2021–2026 0,016, iyimser -0,000, işlem 20.688 / 23.095
- 15 dk · F1 · PDH/PDL yol açık: DOĞRULANMADI · fark -0,013 R, p=0,7317, 2016–2020 -0,026 / 2021–2026 -0,003, iyimser -0,014, işlem 8.067 / 13.652
- 15 dk · F2 · EMA 13/48/200: DOĞRULANMADI · fark 0,009 R, p=0,3303, 2016–2020 0,040 / 2021–2026 -0,019, iyimser 0,007, işlem 8.704 / 12.997
- 15 dk · F3 · PMH/PML: DOĞRULANMADI · fark 0,016 R, p=0,2112, 2016–2020 0,004 / 2021–2026 0,027, iyimser 0,014, işlem 12.955 / 7.996
- 15 dk · V · gövde kapanışı: DOĞRULANMADI · fark -0,003 R, p=0,6437, 2016–2020 -0,001 / 2021–2026 -0,004, iyimser -0,004, işlem 18.262 / 21.719

Karar vermeyen ama okunması gereken notlar:
- Sekiz testin hiçbiri doğrulanmadı. En yakını 5 dk PMH/PML (p=0,035, iki dönem ve iyimser pozitif); kayıtlı eşik 0,00625 olduğu için geçmedi. Tek test olsaydı bile 0,025'i geçmezdi.
- Mutlak sonuçlar sıfıra yakın: 5 dk filtresiz −0,021 R, PMH/PML sağlanan +0,006 R, üç filtre birlikte +0,017 R (3.268 işlem). 15 dk filtresiz +0,007 R, üç filtre birlikte +0,029 R (4.601 işlem).
- EMA hizalaması 2021–2026'da iki aralıkta da negatif. Gövde kapanışı ile giriş, anlık stop emrine göre fark yaratmıyor (±0,003 R).
- PMH/PML ile 07c'nin 4. şartı (vadeli gece aralığı aşıldı mı) aynı fikrin iki ölçümü; ikisi de küçük ve pozitif. Bağımsız iki kanıt sayılmaz.

## Önceden kayıtlı test: Seviye + EMA geri çekilme stratejisi (07e)
**Kayıt tarihi:** 16/11/2026, strateji bu veride hiç çalıştırılmadan önce. Kaynak: Gökhan'ın "önceki güne göre işlem" belgesi. Belirsiz kısımlar aşağıdaki gibi mekanik hale getirildi; bunlar sonradan değiştirilmez.
- **Veri ve evren:** 07c/07d önbelleği (Alpaca 1 dk seans + piyasa öncesi), 01/01/2016–21/08/2026, SPY, QQQ, IWM + Mag7. Maliyet: 0,0035 $/hisse komisyon, kayma ETF 1 bps, hisse 3 bps (her yön).
- **Seviyeler:** PDH/PDL = önceki seansın en yükseği/düşüğü; PMH/PML = aynı günün 04:00–09:29 en yükseği/düşüğü (10'dan az piyasa öncesi mumu varsa PMH/PML yok). Çizgi olarak.
- **Sinyal mumu:** seans içi 15 dk mumlar (09:30, 09:45, …). İlk 30 dakika beklenir: sinyal mumunun başlangıcı en erken 10:00.
- **Trend:** sinyal mumunun kapanışında, tamamlanmış son 5 dk mumunda EMA 13/48/200 (seans içi 5 dk kapanışları, günler arası kesintisiz, `adjust=False`). Uzun için 13 > 48 > 200, kısa için tersi; değilse sinyal yok.
- **Kol A · Kırılım:** uzun = 15 dk kapanış PDH ya da PMH'nin üstünde ve bir önceki 15 dk kapanış o seviyenin altında/eşit; kısa = PDL ya da PML için tersi. Stop = kırılan seviye.
- **Kol B · Ret:** kısa = 15 dk mumun açılışı ve kapanışı PDH (ya da PMH) altında, en yükseği seviyeye değmiş/aşmış; uzun = PDL (ya da PML) için tersi. Stop = sinyal mumunun en yükseği (kısa) / en düşüğü (uzun).
- **Giriş (iki kolda aynı):** sinyal mumu kapandıktan sonraki 60 dakika içinde, bir 5 dk mumun en düşüğü (uzun) bir önceki tamamlanmış mumun 13 EMA'sına değer ve kapanışı o değerin üstündeyse kapanışta giriş (kısada tersi). Son giriş 15:30. Giriş fiyatı stopun yanlış tarafındaysa işlem yok. Sembol başına günde kol başına tek işlem (ilk geçerli giriş).
- **Stop düzeltmesi (16/11/2026, gerçek veri görülmeden):** sahte veri testinde giriş seviyenin hemen yanında olunca risk 0,03 bps'e düşüp maliyet R'ı −200'e çıkardı. Belgedeki "seviyenin / 13 EMA'nın gerisi" ifadesine uygun olarak stop = kırılan seviye (retde sinyal mumunun ucu) ile giriş mumunun en düşüğü (kısada en yükseği) arasından girişe **uzak olanı**; risk girişin %0,10'undan azsa stop girişten %0,10 uzağa konur. Test eşikleri değişmedi.
- **Hedef:** işlem yönünde girişten sonraki ilk kilit seviye (PDH, PDL, PMH, PML arasından); yoksa gün sonu kapanışı. Çıkış 1 dk mumlarla: stop ya da hedef; aynı dakikada ikisi birden mümkünse kötümser = stop, iyimser = hedef. Hiçbiri olmazsa gün sonu kapanışı.
- **Test (kol başına, iki test):** maliyet sonrası ortalama net R (kötümser) > 0. Gün bazında işaret çevirme permütasyonu (günlük toplam R'lar rastgele ±1), 5.000 tekrar, tek yönlü.
- **Doğrulandı sayılmak için (her kol ayrı):** (1) p < **0,0125** (0,025 ÷ 2, Bonferroni), (2) ortalama 2016–2020 ve 2021–2026'da ayrı ayrı pozitif, (3) iyimser varsayımda da pozitif, (4) en az 200 işlem.
- **Raporlanır ama karar vermez:** isabet oranı, hedefe ulaşma payı, seviye türüne göre (PDH/PDL ile PMH/PML), ETF / Mag7 ve sembol kırılımı, yıllara göre sonuç, 5 dk ORB (−0,021 R) ile kıyas, EMA filtresi olmadan sonuç.

### Sonuç (16/11/2026 · Alpaca 1 dk seans + piyasa öncesi, 2016–2026)
Notebook çıktısı, değiştirilmeden:
- Kol A · kırılım: DOĞRULANMADI · net R -0,323, p=1,0000, 2016–2020 -0,348 / 2021–2026 -0,301, iyimser -0,320, isabet %31,2, 10.896 işlem
- Kol B · ret: DOĞRULANMADI · net R -0,251, p=1,0000, 2016–2020 -0,266 / 2021–2026 -0,235, iyimser -0,251, isabet %42,0, 3.169 işlem

Karar vermeyen ama okunması gereken notlar:
- İki kol da 2016–2026'nın her yılında negatif; seviye türü, yön, ETF/Mag7 ayrımı fark etmiyor. Tek tek sembollerin hiçbiri pozitif değil.
- Kırılımda ortalama risk 23 bps, retde 41 bps. Gidiş-dönüş kayma (ETF 2, hisse 6 bps) bu risklere göre yaklaşık 0,1–0,25 R ediyor (tahmin); maliyet öncesi sonuç da sıfırın altında ya da sıfıra yakın. Kenar yok, maliyet kaybı büyütüyor.
- EMA filtresi olmadan kırılım −0,311 R (16.613 işlem): filtre işlem sayısını üçte bir azaltıyor ama sonucu değiştirmiyor.
- ETF'ler Mag7'den daha az kötü (A: −0,180 / −0,393; B: −0,152 / −0,301), çünkü kayma ETF'de düşük.
- 5 dk ORB (−0,021 R) çıtasının belirgin altında.

## Teknik analiz sistemi: The Strat (08)
- **SONUÇ (17/11/2026): 08 testi günlükte ve haftalıkta doğrulanmadı.** Önceden kayıttaki karar gereği Strat **tek başına sinyal olarak kullanılmaz**; yalnızca bar okuma dili olarak (bar tipi, kurulum, FTFC durumu bilgi amaçlı) kullanılır. Ayrıntılar aşağıda, 08 sonuç bölümünde.
- **Karar (17/11/2026):** Teknik analizde tek bir sistem benimsendi: **The Strat**. Seçilme nedeni tamamen kurala dayalı olması: bar tipleri ve kurulumlar yoruma yer bırakmadan kodla tanımlanıyor, analiz her seferinde aynı çıkıyor ve ölçülebiliyor. Price action (Brooks, Volman) yoruma çok dayandığı için seçilmedi.
- **Sade başlangıç:** broadening formation ve magnitude seviyeleri şimdilik **bekletiliyor**. Yalnızca bar kombinasyonları ve FTFC.
- **Bar tipleri:** her bar bir öncekine göre sınıflanır. **1** inside (iki ucu da aşmadı), **2u** yalnız yükseği aştı, **2d** yalnız düşüğü aştı, **3** outside (ikisini de aştı). Eşit uç "aşmadı" sayılır.
- **Kurulumlar:** 2-1-2 devam (2u-1-2u, 2d-1-2d), 2-1-2 dönüş (2d-1-2u, 2u-1-2d), 3-1-2, 2-2 dönüş (2d-2u, 2u-2d). RevStrat (1-2-2) sonraya bırakıldı.
- **Giriş, stop, hedef:** giriş, kurulumun son barının (inside bar ya da 2-2'deki ilk 2) ucunun kırılması; boşlukla açılırsa açılış fiyatı. Stop o barın öbür ucu. Hedef, ondan bir önceki barın ucu.
- **FTFC:** giriş fiyatı, tetik anındaki üst zaman dilimi açılışlarının hepsinin üstünde (long) ya da altında (short). Günlük kurulumda ay ve hafta açılışı; haftalık kurulumda çeyrek ve ay açılışı (haftanın ilk işlem gününün ayı ve çeyreği). Eşitlik uyumsuz sayılır.
- **Zaman dilimleri:** günlük ve haftalık. Gün içi yok (07 parkuru kapalı); opsiyon kurallarıyla (21–45 gün) uyum için.
- **Ölçüt:** hedefe ulaşma oranı değil, **maliyet sonrası ortalama R**. Inside bar kurulumlarında ilk hedef çoğu zaman 1R'nin altında; sahte veri denemesinde isabet %85–100 iken ortalama R negatifti. Hedefi 1R'nin altındaki kurulumlar elenmez, test edilir; "hedef ≥ 1R" olanlar ayrıca raporlanır.

## Önceden kayıtlı test: The Strat, sade (08)
**Kayıt tarihi:** 17/11/2026, strateji gerçek veride hiç çalıştırılmadan önce (yalnızca sahte veriyle denendi). Bu bölüm test yapıldıktan sonra değiştirilmez; sonuçlar altına eklenir.
- **Hipotez (Gökhan onayı, 17/11/2026):** FTFC uyumlu kurulumların maliyet sonrası ortalama R'si sıfırdan büyüktür ve FTFC uyumsuz kurulumlardan yüksektir; bu hem örneklem içinde hem 2023 sonrası örneklem dışı dönemde geçerlidir. Tutmazsa Strat tek başına sinyal olarak değil, yalnızca bar okuma dili olarak kullanılır.
- **Veri:** yfinance günlük, bölünme ve temettü düzeltmeli, 01/01/2010–13/11/2026. Haftalık barlar günlükten üretilir (cuma kapanışlı hafta). Evren: SPY, QQQ, IWM + Mag7 (07 ile aynı; genişletilmez).
- **Dönemler:** örneklem içi 2010–2022, örneklem dışı 01/01/2023–13/11/2026 (giriş tarihine göre).
- **Kurallar:** "Teknik analiz sistemi: The Strat (08)" bölümü. Ek simülasyon kuralları:
  - Inside bar kurulumunda tetik barı iki ucu da kırdıysa (sıra bilinmez) işlem yok. Boşlukla stopun ya da hedefin ötesinde açılırsa işlem yok.
  - Zaman stopu: günlükte 10, haftalıkta 4 bar; hedef ya da stop görülmezse son barın kapanışında çıkış.
  - Sonraki barlarda boşlukla stop ya da hedef aşılırsa açılış fiyatından çıkış.
  - Aynı barda hedef ve stop: **kötümser = stop (esas)**, iyimser = hedef. İyimserde giriş barında stop dikkate alınmaz.
  - Maliyet: 0,0035 $/hisse komisyon, kayma ETF 1 bps, hisse 3 bps (her yön; 07 ile aynı).
- **Testler:** günlük ve haftalık ayrı, iki test, α = 0,025 ÷ 2 = **0,0125**. Doğrulandı sayılmanın şartları:
  1. FTFC uyumlu işlemlerin ortalama net R'ı (kötümser) > 0; gün bazında işaret çevirme permütasyonu, 5.000 tekrar, tek yönlü, p < 0,0125.
  2. Fark (uyumlu − uyumsuz) > 0 ve **karıştırılmış bar** kıyasına göre p < 0,0125. Karıştırılmış bar: her sembolün günlük bar oranları (gap, yüksek, düşük, kapanış) rastgele sıralanıp fiyat yolu yeniden kurulur ve aynı kurallar uygulanır; 400 tekrar; p = (1 + gerçek farka eşit ya da büyük tekrar sayısı) ÷ 401.
  3. Ortalama ve fark örneklem içi ve dışında ayrı ayrı > 0.
  4. Ortalama ve fark iyimser varsayımda da > 0.
  5. Her grupta en az 200 (günlük) / 100 (haftalık) işlem.
  Biri bile sağlanmazsa o zaman dilimi **doğrulanmadı** sayılır.
- **Neden karıştırılmış bar (sahte veri denemesinde görüldü):** FTFC uyumlu kurulumların hedefi mekanik olarak daha yakın (medyan ~0,4R'ye karşı ~0,75R) ve yükselen piyasada uyumlu long'lar eğilimden pay alıyor; sırası rastgele piyasada bile FTFC farkı ~+0,03 R çıktı. İşlemler arasında etiket karıştıran permütasyon, çakışan işlemleri bağımsız saydığı için rastgele veride p=0,003 verdi; bu yüzden kullanılmadı.
- **Raporlanır ama karar vermez:** kurulum ve yön kırılımı, yalnızca hedef ≥ 1R kurulumlar, hedefe ulaşma oranı, ETF / Mag7 ve sembol, yıllara göre sonuç, gerçek ile karıştırılmış sonuç farkı (sıra katkısı).
- Eşikler ve tanımlar veri görüldükten sonra değiştirilmez; değiştirilirse test geçersiz sayılır ve yeni kayıt gerekir.
- **Kodlama düzeltmesi (17/11/2026, ilk çalıştırmadan sonra):** ilk sürüm hisse başı komisyonu (0,0035 $) bölünmeye göre düzeltilmiş fiyata uyguluyordu; çok bölünen hisselerde (AAPL 28, NVDA 40, AMZN 20, GOOGL 40, TSLA 15 kat) eski yıllarda maliyet o oranda şişiyordu. Düzeltilmiş sürüm komisyonu bölünme çarpanıyla gerçek hisse fiyatına göre alır (commit `69cb982`). Kural, eşik ve tanımlar değişmedi; bu, kayıtlı maliyet kuralının doğru uygulanmasıdır. Esas sonuç düzeltilmiş sürümdür; ilk çıktı şeffaflık için aşağıda ayrıca durur.

### Sonuç (17/11/2026 · yfinance günlük, 04/01/2010–13/11/2026)
Notebook çıktısı (düzeltilmiş sürüm, esas), değiştirilmeden:
- D: DOĞRULANMADI · FTFC uyumlu net R -0,087, p=1,0000 · fark 0,044 R, p=0,0998 · örneklem içi -0,091 / 0,042, dışı -0,072 / 0,052 · iyimser 0,036 / 0,040 · karıştırılmış fark ort. 0,020 · işlem 5.503 / 9.635
- W: DOĞRULANMADI · FTFC uyumlu net R -0,106, p=1,0000 · fark 0,094 R, p=0,0224 · örneklem içi -0,094 / 0,119, dışı -0,143 / 0,010 · iyimser 0,058 / 0,067 · karıştırılmış fark ort. 0,029 · işlem 1.471 / 1.851

İlk çalıştırma (komisyon hatalı, geçersiz; yalnızca kayıt için):
- D: DOĞRULANMADI · FTFC uyumlu net R -0,130, p=1,0000 · fark 0,039 R, p=0,1471 · örneklem içi -0,148 / 0,035, dışı -0,072 / 0,052 · iyimser -0,008 / 0,034 · karıştırılmış fark ort. 0,020 · işlem 5.503 / 9.635
- W: DOĞRULANMADI · FTFC uyumlu net R -0,123, p=1,0000 · fark 0,095 R, p=0,0200 · örneklem içi -0,116 / 0,121, dışı -0,143 / 0,010 · iyimser 0,041 / 0,068 · karıştırılmış fark ort. 0,029 · işlem 1.471 / 1.851

Karar vermeyen ama okunması gereken notlar:
- Esas (kötümser) varsayımda kenar yok: FTFC uyumlu işlemler maliyet sonrası günlükte −0,087 R, haftalıkta −0,106 R; örneklem içi ve dışı negatif. Hatadan etkilenmeyen ETF'ler (SPY, QQQ, IWM) de ilk çalıştırmada negatifti.
- İyimser varsayımda ortalama pozitif (günlük +0,036, haftalık +0,058 R). Sonuç, aynı barda hedef ve stop görüldüğünde hangisinin önce geldiğine bağlı; olası bir kenar bu belirsizlik bandının içinde kalıyor ve günlük barla çözülemiyor.
- Günlük FTFC farkı (+0,044 R), sırası karıştırılmış piyasadaki mekanik farkın (+0,020 R) üstünde ama anlamlı değil (p=0,100).
- Haftalık FTFC farkı (+0,094 R) karıştırılmış farkın (+0,029 R) belirgin üstünde, p=0,022; 0,0125 eşiğini geçmedi. Fark örneklem içinde 0,119 R, 2023 sonrasında 0,010 R: son dönemde neredeyse kayboluyor (07c'deki desen).
- İlk çalıştırmanın tablolarına göre kaybın ağırlığı 2-2 dönüşte (günlük işlemlerin ~%80'i); kötümser–iyimser farkının neredeyse tamamı da burada, çünkü 2-2'de giriş barı stopa da değebiliyor. Inside bar kurulumları (2-1-2, 3-1-2) küçük örneklemde hafif pozitif, hedefleri ~0,2R. Bunlardan sonradan kural seçmek veri madenciliği olur; yeniden sınanacaksa yalnızca yeni önceden kayıtla ve 13/11/2026 sonrası veriyle.

## Açık işler ve fikirler
- [x] 07c: önceden kayıtlı test çalıştırıldı (16/11/2026) — 5 dk doğrulandı (zayıf), 15 dk doğrulanmadı
- [x] 07e: seviye + EMA geri çekilme stratejisi test edildi (16/11/2026) — iki kol da doğrulanmadı, her yıl negatif
- [x] 07d: seviye ve EMA teyitleri test edildi (16/11/2026) — sekiz testin hiçbiri doğrulanmadı
- [x] 08: önceden kayıtlı Strat testi çalıştırıldı (17/11/2026) — günlük ve haftalık doğrulanmadı; Strat yalnızca bar okuma dili
- [ ] Strat isabet kaydı: 08'in 6. bölümündeki güncel kurulumları kaydedip 5 / 10 / 20 işlem günü sonra gerçekleşenle karşılaştırmak. Haftalık FTFC ya da inside bar kurulumları yeniden sınanacaksa bu kayıtla ve önce yeni önceden kayıt yazılarak (karar bekliyor)
- [ ] Strat: broadening formation ve magnitude seviyeleri (bekletiliyor)
- [ ] (Parkur kapalı; yeniden açılırsa) 07c skor 4'ü ayrı kural olarak, yeni önceden kayıtla ve 21/08/2026 sonrası veriyle sınamak
- [ ] Gün içi parkur sonuçlarını (07–07e) bir X dizisine dönüştürmek
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
