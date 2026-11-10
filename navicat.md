# Navicat Premium Kurulum Rehberi (Windows)

Bu rehber, Navicat Premium'u Windows'a kurmanı ve `okul.db` veritabanını Navicat'e tanıtmanı anlatır.

> Menü adları sürüme göre küçük farklar gösterebilir. Ekran farklıysa aynı mantıkla ilerle.

## Navicat nedir?

Veritabanlarını **görsel arayüzle** yönetmeye yarayan bir programdır. SQLite, PostgreSQL, MySQL gibi farklı veritabanlarına tek yerden bağlanır. Eğitimde SQL yazmak ve tabloları gözle görmek için kullanıyoruz.

## 1. İndirme

1. Tarayıcıda şu adresi aç: <https://www.navicat.com/en/download/navicat-premium>
2. **Windows** sürümünü seç (Windows için `.exe` kurulum dosyası).
3. İndirmeyi başlat. Dosya genelde `İndirilenler` klasörüne iner.

**Lisans seçenekleri:**

| Seçenek | Not |
|---|---|
| **Navicat Premium Lite** | Ücretsiz sürüm; temel işler (bağlanma, tablo görme, SQL çalıştırma) için eğitime yeter. Sayfada sunuluyorsa bunu seç. |
| **Deneme sürümü (trial)** | Tam özellikli, süreli (genelde 14 gün). |
| **Lisanslı** | İşletme lisansı varsa lisans anahtarını gir. |

Eğitim için Lite veya deneme sürümü yeterlidir. Hangisini kullanacağını eğitmene danış.

## 2. Kurulum

1. İndirilen `.exe` dosyasına çift tıkla.
2. Windows "Bu uygulamanın cihazında değişiklik yapmasına izin vermek istiyor musunuz?" diye sorarsa **Evet** de.
3. Kurulum sihirbazında: **Next** → lisans sözleşmesini kabul et (**I accept**) → **Next**.
4. Kurulum klasörünü değiştirmene gerek yok (varsayılan kalsın) → **Next**.
5. İstersen **Create a desktop icon** kutusunu işaretle → **Next** → **Install**.
6. Bittiğinde **Finish**.

## 3. İlk açılış

1. Navicat'i masaüstü kısayolundan veya Başlat menüsünden aç.
2. Lisans/etkinleştirme ekranı çıkarsa:
   - Ücretsiz/deneme için **Trial** (veya Lite) seçeneğini seç.
   - Lisansın varsa lisans anahtarını gir.
3. Bir hesap girişi istenirse eğitmenin yönlendirmesine göre ilerle; **atlayabiliyorsan atla** (Skip / Later).

## 4. `okul.db` dosyasını bilgisayara al

Önce veritabanı dosyası bilgisayarında olmalı. İki yol var:

**Yol A — Git ile (önerilen):**

```
git clone <repo-adresi>
```

Dosya, klonlanan klasörün içinde `okul.db` olarak durur.

**Yol B — GitHub'dan tek dosya:**

1. GitHub'da repoda `okul.db` dosyasına tıkla.
2. **Download raw file** (indirme simgesi) ile indir.

> **Dosya yolunu not et.** Örnek: `C:\Projeler\yapayzeka\okul.db`. Navicat'te bu yolu seçeceksin.
> Navicat açıkken dosyayı **taşıma/silme**; bağlantı kopar.

## 5. `okul.db`'yi Navicat'e tanıtma (SQLite bağlantısı)

SQLite, sunucu olmayan **tek dosyalık** bir veritabanıdır. Bu yüzden kullanıcı adı, şifre, port gerekmez; sadece dosyayı göstermen yeterlidir.

1. Navicat'in sol üstünde **Connection** (Bağlantı) düğmesine tıkla.
2. Listeden **SQLite** seç.
3. Açılan pencerede alanları doldur:

   | Alan | Değer |
   |---|---|
   | **Connection Name** | `okul` (istediğin ad olabilir) |
   | **Type** | **Existing Database File** (var olan dosya) |
   | **Database File** | **...** (Browse) düğmesiyle `okul.db` dosyasını seç |

   > "New Database File" **seçme**; o, boş yeni bir veritabanı oluşturur.

4. **Test Connection** düğmesine bas. **Connection Successful** mesajı gelmeli.
5. **OK** (veya Save) de.
6. Sol listede `okul` bağlantısı görünür. Üzerine **çift tıkla** (bağlantı açılır).
7. Açılan ağaçta **main** → **Tables** (Tablolar) altında şu 6 tabloyu görmelisin:

   `dersler`, `haftalik_ders_programi`, `ogrenciler`, `ogretmenler`, `siniflar`, `yoklama`

   (`sqlite_sequence` sistem tablosu da görünebilir; dokunma.)

## 6. Tabloya bakma

- Bir tabloya **çift tıkla** → satırları (veriyi) Excel gibi gösterir.
- Tabloya sağ tık → **Design Table** (Tabloyu Tasarla) → sütunlar, tipler, anahtarlar.
- Beklenen satır sayıları: `ogrenciler` 204, `ogretmenler` 19, `siniflar` 18, `dersler` 15, `yoklama` 18000, `haftalik_ders_programi` 560.

## 7. İlk SQL sorgusunu çalıştırma

1. Üst menüden **Query** (Sorgu) → **New Query** (Yeni Sorgu) tıkla.
2. Şunu yapıştır:

   ```sql
   SELECT * FROM ogrenciler LIMIT 10;
   ```

3. **Run** (▶) düğmesine bas (kısayol: `Ctrl + R`).
4. Alt kısımda sonuç tablosu çıkar.

Sonraki adım: JOIN örnekleri için [okul.md](okul.md) dosyasındaki sorguları aynı şekilde çalıştır.

> RIGHT ve FULL OUTER JOIN için SQLite **3.39+** gerekir. Navicat'in kendi SQLite sürümü eskiyse bu iki sorgu hata verebilir. Bu durumda tabloların yerini değiştirip `LEFT JOIN` kullan veya Navicat'i güncelle.

## 8. Sık karşılaşılan sorunlar

| Sorun | Çözüm |
|---|---|
| Kurulum "yönetici izni" istiyor | **Evet** de; izin yoksa BT birimine başvur. |
| `Test Connection` hata veriyor | Dosya yolunu kontrol et; **Type**'ın *Existing Database File* olduğundan emin ol. |
| Tablolar boş / hiç yok | Yanlışlıkla *New Database File* seçmiş olabilirsin; bağlantıyı silip yeniden oluştur. |
| `database is locked` | Aynı `okul.db`'yi başka program (ör. VSCode eklentisi, `sqlite3`) açık tutuyor; onu kapat. |
| Türkçe karakterler bozuk | Dosya yolunda Türkçe karakter/boşluk varsa dosyayı `C:\Projeler\` gibi sade bir klasöre taşı. |
| `FULL OUTER JOIN` hata veriyor | SQLite sürümü eski (bkz. bölüm 7 notu). |
| Yanlışlıkla veriyi değiştirdim | `git restore okul.db` ile dosyayı eski haline döndür. |

## 9. İyi pratikler

- **Yedek al:** Deneme yaparken `okul.db`'yi kopyala (`okul_yedek.db`) ve kopya üzerinde çalış.
- `UPDATE` ve `DELETE` yazarken **mutlaka `WHERE`** koy; yoksa tüm satırlar etkilenir.
- İş bitince bağlantıya sağ tık → **Close Connection** de.
