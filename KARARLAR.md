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
| 07d | Önceden kayıtlı test: seviye ve EMA teyitleri | PDH/PDL, EMA 13/48/200, PMH/PML, gövde kapanışı; Alpaca anahtarı yeterli |

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
- Ana aralık **5 dk ORB** (16/11/2026; 07c'de vadeli yön filtresi yalnızca 5 dk'da doğrulandı). 15 dk karşılaştırma olarak her raporda yan yana gösterilir; 10 yıllık veride ikisinin mutlak sonucu da sıfıra yakın (5 dk −0,021 R, 15 dk +0,007 R, filtresiz). Yeni kurallar iki aralıkta birden ve önceden kayıtla test edilir. Temel kurallar Zarattini, Barbon ve Aziz 2024: aralık yönü, aralık ucuna stop emri, gün sonunda çıkış (başlangıçta 15 dk ve ATR %10 stop ile incelendi).
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

## Açık işler ve fikirler
- [x] 07c: önceden kayıtlı test çalıştırıldı (16/11/2026) — 5 dk doğrulandı (zayıf), 15 dk doğrulanmadı
- [x] 07d: seviye ve EMA teyitleri test edildi (16/11/2026) — sekiz testin hiçbiri doğrulanmadı
- [ ] 07c sonrası: skor 4'ü ayrı bir kural olarak test etmek **yeni bir önceden kayıt** gerektirir (bu veride bulundu); 2026 sonrası veriyle sınanmalı
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
