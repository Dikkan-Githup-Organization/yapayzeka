# SQL JOIN Rehberi — `okul.db`

Bu dosya, `okul.db` veritabanı üzerinde **JOIN** işlemlerini basitten zora öğrenmen için hazırlandı.
Altı bölüm × 3 örnek = **18 sorgu**. Hepsi gerçekten çalıştırılıp sonuçları buraya yazıldı.

> Veriler tamamen **sahte (üretilmiş)** verilerdir.

## Nasıl çalıştırırım?

- **Navicat:** `okul.db` dosyasını SQLite bağlantısı olarak aç → Yeni Sorgu → sorguyu yapıştır → Çalıştır.
- **Terminal:** `sqlite3 okul.db` yazıp sorguyu yapıştır. (SQLite **3.39 veya üzeri** gerekir; RIGHT/FULL JOIN için.)

## Veritabanı Haritası

| Tablo | Satır | Ne tutar? | Önemli sütunlar |
|---|---|---|---|
| `ogrenciler` | 204 | Öğrenciler | `id`, `okul_no`, `ad`, `soyad`, `sinif`, `sube`, `boy_cm`, `kilo_kg` |
| `ogretmenler` | 19 | Öğretmenler | `id`, `ad`, `soyad`, `brans` |
| `siniflar` | 18 | Sınıf/şube | `id`, `sinif_adi` (örn. `5/A`), `derslik`, `rehber_ogretmen_id` → `ogretmenler.id` |
| `dersler` | 15 | Ders listesi | `id`, `ders_kodu`, `ders_adi`, `haftalik_saat` |
| `yoklama` | 18000 | Günlük devam | `ogrenci_id` → `ogrenciler.id`, `tarih`, `durum` |
| `haftalik_ders_programi` | 560 | Ders programı | `sinif_id`, `ders_id`, `ogretmen_id` (hepsi yabancı anahtar), `gun`, `ders_saati` |

```
ogretmenler ──< siniflar (rehber_ogretmen_id)
ogretmenler ──< haftalik_ders_programi >── dersler
siniflar    ──< haftalik_ders_programi
ogrenciler  ──< yoklama
ogrenciler ···· siniflar   (sinif || '/' || sube = sinif_adi)
```

**Not:** `ogrenciler` ile `siniflar` arasında numara (yabancı anahtar) yoktur; bağlantı `sinif || '/' || sube = sinif_adi` ifadesiyle kurulur.

### Eşleşmeyen veriler (bilerek eklendi)

LEFT/RIGHT/FULL JOIN farkı ancak eşleşmeyen satır varsa görülür. Bu yüzden şunlar bilerek var:

- 3 öğretmenin (Almanca, Satranç, Robotik Kodlama) sınıfı ve ders programı yok
- 3 ders (Almanca, Satranç, Robotik Kodlama) programa konulmamış
- 2 sınıfın (5/E, 6/E) öğrencisi ve rehber öğretmeni yok
- 4 yeni kayıtlı öğrencinin yoklaması yok; 2 tanesi (5/F, 6/F) tanımsız şubede

## JOIN türlerine kısa bakış

| Tür | Soru | Eşleşmeyen satır |
|---|---|---|
| INNER | Sadece ikisinde de olanlar | Atılır |
| LEFT | Soldaki hepsi + eşleşen sağ | Sağ NULL |
| RIGHT | Sağdaki hepsi + eşleşen sol | Sol NULL |
| FULL OUTER | İkisi de hepsi | Karşı taraf NULL |
| CROSS | Tüm kombinasyonlar | (ON yok) |
| SELF | Tablo kendisiyle | (teknik) |

**Temel sözdizimi:**

```sql
SELECT ...
FROM   tablo_a  a
<TÜR> JOIN tablo_b b  ON a.sutun = b.sutun
WHERE  ...;
```

`a`, `b` birer **takma ad (alias)**dır; sorguyu kısaltır.

---

## 1. INNER JOIN — Sadece eşleşenler

İki tabloda **de karşılığı olan** satırları getirir. Karşılığı olmayan satır sonuca girmez. `JOIN` yazıp başına bir şey koymazsanız varsayılan budur.

```
 A ∩ B   (kesişim)
```

### Örnek 1: Seviye 1 — Sınıfların rehber öğretmeni

`siniflar.rehber_ogretmen_id`, `ogretmenler.id` ile eşleşir. Rehberi olmayan sınıflar (5/E, 6/E) listede **yoktur**.

```sql
SELECT s.sinif_adi, s.derslik, o.ad || ' ' || o.soyad AS rehber
FROM siniflar s
INNER JOIN ogretmenler o ON o.id = s.rehber_ogretmen_id
ORDER BY s.sinif_adi;
```

**Sonuç:**

| sinif_adi | derslik | rehber |
|---|---|---|
| 5/A | Kat 1 - No: 501 | Kemal Yıldız |
| 5/B | Kat 1 - No: 502 | Ayşe Demir |
| 5/C | Kat 1 - No: 503 | Murat Kaya |
| 5/D | Kat 1 - No: 504 | Fatma Öztürk |
| 6/A | Kat 2 - No: 601 | Selim Çelik |
| 6/B | Kat 2 - No: 602 | Zeynep Aydın |
| 6/C | Kat 2 - No: 603 | Hakan Şahin |
| 6/D | Kat 2 - No: 604 | Ebru Arslan |
| 7/A | Kat 3 - No: 701 | Cem Koç |
| 7/B | Kat 3 - No: 702 | Derya Polat |
| 7/C | Kat 3 - No: 703 | Burak Korkmaz |
| 7/D | Kat 3 - No: 704 | Merve Doğan |
| 8/A | Kat 4 - No: 801 | Serkan Aslan |
| 8/B | Kat 4 - No: 802 | Gülşen Erdoğan |
| 8/C | Kat 4 - No: 803 | Tolga Güler |
| 8/D | Kat 4 - No: 804 | Seda Kurt |

*(16 satır)*


### Örnek 2: Seviye 2 — 5/A sınıfının öğrencileri ve dersliği

`ogrenciler` tablosunda sınıf numarası yok; `sinif` ve `sube` sütunlarını birleştirip (`||`) `siniflar.sinif_adi` ile eşleştiriyoruz.

```sql
SELECT o.okul_no, o.ad, o.soyad, s.sinif_adi, s.derslik
FROM ogrenciler o
INNER JOIN siniflar s ON s.sinif_adi = o.sinif || '/' || o.sube
WHERE s.sinif_adi = '5/A'
ORDER BY o.soyad;
```

**Sonuç:**

| okul_no | ad | soyad | sinif_adi | derslik |
|---|---|---|---|---|
| 227 | Hazal | Acar | 5/A | Kat 1 - No: 501 |
| 297 | Miray | Bulut | 5/A | Kat 1 - No: 501 |
| 237 | Zeynep | Güler | 5/A | Kat 1 - No: 501 |
| 263 | İrem | Güler | 5/A | Kat 1 - No: 501 |
| 154 | Onur | Korkmaz | 5/A | Kat 1 - No: 501 |
| 158 | Yağız | Koç | 5/A | Kat 1 - No: 501 |
| 217 | Defne | Koç | 5/A | Kat 1 - No: 501 |
| 280 | Berna | Yılmaz | 5/A | Kat 1 - No: 501 |

*(toplam 12 satır, ilk 8 tanesi gösterildi)*


### Örnek 3: Seviye 3 — 5/A Pazartesi ders programı (4 tablo)

Birden fazla `JOIN` art arda yazılabilir. Program tablosu sadece numara tutar; sınıf, ders ve öğretmen adlarını diğer tablolardan alırız.

```sql
SELECT p.ders_saati, d.ders_adi, t.ad || ' ' || t.soyad AS ogretmen, p.derslik
FROM haftalik_ders_programi p
INNER JOIN siniflar s ON s.id = p.sinif_id
INNER JOIN dersler d ON d.id = p.ders_id
INNER JOIN ogretmenler t ON t.id = p.ogretmen_id
WHERE s.sinif_adi = '5/A' AND p.gun = 'Pazartesi'
ORDER BY p.ders_saati;
```

**Sonuç:**

| ders_saati | ders_adi | ogretmen | derslik |
|---|---|---|---|
| 1 | Din Kültürü ve Ahlak Bilgisi | Zeynep Aydın | Kat 1 - No: 501 |
| 2 | Matematik | Kemal Yıldız | Kat 1 - No: 501 |
| 3 | Matematik | Kemal Yıldız | Kat 1 - No: 501 |
| 4 | Matematik | Kemal Yıldız | Kat 1 - No: 501 |
| 5 | Sosyal Bilgiler | Fatma Öztürk | Kat 1 - No: 501 |
| 6 | Beden Eğitimi ve Spor | Hakan Şahin | Kat 1 - No: 501 |
| 7 | Fen Bilimleri | Murat Kaya | Kat 1 - No: 501 |

*(7 satır)*


---

## 2. LEFT JOIN — Soldaki tablo eksiksiz

**Soldaki** (FROM'daki) tablonun tüm satırlarını getirir. Sağda karşılığı yoksa sağ tablonun sütunları **NULL** gelir.

```
 A tamamı + (A ∩ B)
```

İpucu: `WHERE sağ.id IS NULL` eklersen **"karşılığı olmayanları"** bulursun.

### Örnek 4: Seviye 1 — Tüm öğretmenler ve rehber oldukları sınıf

Rehberlik yapmayan öğretmenlerin `sinif_adi` değeri NULL gelir.

```sql
SELECT t.ad, t.soyad, t.brans, s.sinif_adi AS rehber_sinif
FROM ogretmenler t
LEFT JOIN siniflar s ON s.rehber_ogretmen_id = t.id
ORDER BY s.sinif_adi DESC, t.soyad;
```

**Sonuç:**

| ad | soyad | brans | rehber_sinif |
|---|---|---|---|
| Seda | Kurt | Teknoloji ve Tasarım | 8/D |
| Tolga | Güler | Rehberlik / Psikolojik Danışman | 8/C |
| Gülşen | Erdoğan | İngilizce | 8/B |
| Serkan | Aslan | Fen Bilimleri | 8/A |
| Merve | Doğan | Türkçe | 7/D |
| Burak | Korkmaz | Matematik | 7/C |
| Derya | Polat | Bilişim Teknolojileri | 7/B |
| Cem | Koç | Müzik | 7/A |
| Ebru | Arslan | Görsel Sanatlar | 6/D |
| Hakan | Şahin | Beden Eğitimi | 6/C |
| Zeynep | Aydın | Din Kültürü | 6/B |
| Selim | Çelik | İngilizce | 6/A |
| Fatma | Öztürk | Sosyal Bilgiler | 5/D |
| Murat | Kaya | Fen Bilimleri | 5/C |
| Ayşe | Demir | Türkçe | 5/B |
| Kemal | Yıldız | Matematik | 5/A |
| Selin | Aydın | Almanca | *NULL* |
| Derya | Koç | Robotik Kodlama | *NULL* |
| Burak | Çelik | Satranç | *NULL* |

*(19 satır)*


### Örnek 5: Seviye 2 — Hiç yoklaması alınmamış öğrenciler

LEFT JOIN + `IS NULL`: yoklamada karşılığı olmayan öğrenciler. (Yeni kayıt olan öğrenciler.)

```sql
SELECT o.id, o.okul_no, o.ad, o.soyad, o.sinif, o.sube
FROM ogrenciler o
LEFT JOIN yoklama y ON y.ogrenci_id = o.id
WHERE y.id IS NULL;
```

**Sonuç:**

| id | okul_no | ad | soyad | sinif | sube |
|---|---|---|---|---|---|
| 201 | 301 | Ela | Güneş | 5 | F |
| 202 | 302 | Kaan | Arslan | 6 | F |
| 203 | 303 | Zeynep | Polat | 7 | A |
| 204 | 304 | Emre | Tunç | 8 | B |

*(4 satır)*


### Örnek 6: Seviye 3 — Her sınıfta kaç öğrenci var? (0 olanlar dahil)

`GROUP BY` ile birlikte. `COUNT(o.id)` NULL'ları saymaz, bu yüzden boş sınıflar **0** görünür. (`COUNT(*)` yazsaydık 1 görünürdü!)

```sql
SELECT s.sinif_adi, COUNT(o.id) AS ogrenci_sayisi, ROUND(AVG(o.boy_cm), 1) AS ort_boy
FROM siniflar s
LEFT JOIN ogrenciler o ON s.sinif_adi = o.sinif || '/' || o.sube
GROUP BY s.sinif_adi
ORDER BY ogrenci_sayisi, s.sinif_adi
LIMIT 6;
```

**Sonuç:**

| sinif_adi | ogrenci_sayisi | ort_boy |
|---|---|---|
| 5/E | 0 | *NULL* |
| 6/E | 0 | *NULL* |
| 6/C | 7 | 154.3 |
| 7/A | 8 | 155.5 |
| 6/D | 9 | 154.8 |
| 5/D | 11 | 146.2 |

*(6 satır)*


---

## 3. RIGHT JOIN — Sağdaki tablo eksiksiz

LEFT JOIN'in aynadaki hali: **Sağdaki** tablonun tüm satırlarını getirir, solda karşılığı yoksa NULL gelir.

```
 B tamamı + (A ∩ B)
```

> SQLite 3.39+ sürümü RIGHT JOIN'i destekler. Pratikte çoğu kişi tabloların yerini değiştirip LEFT JOIN yazar; ikisi aynı sonucu verir.

### Örnek 7: Seviye 1 — Tüm sınıflar ve öğrenci sayıları

Önceki LEFT JOIN örneğinin tabloları ters yazılmış hali — sonuç aynı.

```sql
SELECT s.sinif_adi, COUNT(o.id) AS ogrenci_sayisi
FROM ogrenciler o
RIGHT JOIN siniflar s ON s.sinif_adi = o.sinif || '/' || o.sube
GROUP BY s.sinif_adi
ORDER BY ogrenci_sayisi, s.sinif_adi
LIMIT 4;
```

**Sonuç:**

| sinif_adi | ogrenci_sayisi |
|---|---|
| 5/E | 0 |
| 6/E | 0 |
| 6/C | 7 |
| 7/A | 8 |

*(4 satır)*


### Örnek 8: Seviye 2 — Programa hiç konulmamış dersler

Soldaki tablo programdır; sağdaki `dersler` eksiksiz gelir. Programda yeri olmayan dersler `IS NULL` ile bulunur.

```sql
SELECT d.ders_kodu, d.ders_adi, d.haftalik_saat
FROM haftalik_ders_programi p
RIGHT JOIN dersler d ON d.id = p.ders_id
WHERE p.id IS NULL;
```

**Sonuç:**

| ders_kodu | ders_adi | haftalik_saat |
|---|---|---|
| ALM | Almanca | 2 |
| SAT | Satranç | 1 |
| ROB | Robotik Kodlama | 2 |

*(3 satır)*


### Örnek 9: Seviye 3 — Öğretmenlerin haftalık toplam ders saati

Hiç dersi olmayan öğretmenler 0 saat ile listelenir. Sıralamada en az yüklü olanlar en üstte.

```sql
SELECT t.ad, t.soyad, t.brans, COUNT(p.id) AS haftalik_ders_saati
FROM haftalik_ders_programi p
RIGHT JOIN ogretmenler t ON t.id = p.ogretmen_id
GROUP BY t.id
ORDER BY haftalik_ders_saati, t.soyad
LIMIT 6;
```

**Sonuç:**

| ad | soyad | brans | haftalik_ders_saati |
|---|---|---|---|
| Selin | Aydın | Almanca | 0 |
| Derya | Koç | Robotik Kodlama | 0 |
| Burak | Çelik | Satranç | 0 |
| Ebru | Arslan | Görsel Sanatlar | 16 |
| Cem | Koç | Müzik | 16 |
| Serkan | Aslan | Fen Bilimleri | 32 |

*(6 satır)*


---

## 4. FULL OUTER JOIN — İki taraf da eksiksiz

Her iki tablonun **tüm satırlarını** getirir. Eşleşmeyen taraf NULL olur. Veri tutarlılığı kontrolünde ("iki liste birbirini tutuyor mu?") çok kullanılır.

```
 A ∪ B   (birleşim)
```

İpucu: `WHERE a.id IS NULL OR b.id IS NULL` → sadece **uyuşmayanları** gösterir.

### Örnek 10: Seviye 1 — Rehber öğretmen ↔ sınıf uyuşmazlıkları

Hem rehberi olmayan sınıflar hem sınıfı olmayan öğretmenler tek listede.

```sql
SELECT t.ad || ' ' || t.soyad AS ogretmen, s.sinif_adi
FROM ogretmenler t
FULL OUTER JOIN siniflar s ON s.rehber_ogretmen_id = t.id
WHERE t.id IS NULL OR s.id IS NULL;
```

**Sonuç:**

| ogretmen | sinif_adi |
|---|---|
| Selin Aydın | *NULL* |
| Burak Çelik | *NULL* |
| Derya Koç | *NULL* |
| *NULL* | 5/E |
| *NULL* | 6/E |

*(5 satır)*


### Örnek 11: Seviye 2 — Öğrenci ↔ sınıf kaydı uyuşmazlıkları

Hangi sınıfın öğrencisi yok? Hangi öğrencinin sınıf kaydı yok (5/F, 6/F gibi tanımsız şubeler)?

```sql
SELECT o.okul_no, o.ad, o.soyad, o.sinif || '/' || o.sube AS ogrenci_sinifi, s.sinif_adi AS tanimli_sinif
FROM ogrenciler o
FULL OUTER JOIN siniflar s ON s.sinif_adi = o.sinif || '/' || o.sube
WHERE o.id IS NULL OR s.id IS NULL;
```

**Sonuç:**

| okul_no | ad | soyad | ogrenci_sinifi | tanimli_sinif |
|---|---|---|---|---|
| 301 | Ela | Güneş | 5/F | *NULL* |
| 302 | Kaan | Arslan | 6/F | *NULL* |
| *NULL* | *NULL* | *NULL* | *NULL* | 5/E |
| *NULL* | *NULL* | *NULL* | *NULL* | 6/E |

*(4 satır)*


### Örnek 12: Seviye 3 — Ders ↔ öğretmen branş kontrolü

Hangi ders adı hiçbir öğretmenin branşıyla eşleşmiyor, hangi branşın dersi yok? (Ad farklılıkları da burada görünür — gerçek hayatta veri temizliğine işaret eder.)

```sql
SELECT d.ders_adi, t.brans, t.ad || ' ' || t.soyad AS ogretmen
FROM dersler d
FULL OUTER JOIN ogretmenler t ON t.brans = d.ders_adi
WHERE d.id IS NULL OR t.id IS NULL
ORDER BY d.ders_adi, t.brans;
```

**Sonuç:**

| ders_adi | brans | ogretmen |
|---|---|---|
| *NULL* | Beden Eğitimi | Hakan Şahin |
| *NULL* | Bilişim Teknolojileri | Derya Polat |
| *NULL* | Din Kültürü | Zeynep Aydın |
| *NULL* | Rehberlik / Psikolojik Danışman | Tolga Güler |
| Beden Eğitimi ve Spor | *NULL* | *NULL* |
| Bilişim Teknolojileri ve Yazılım | *NULL* | *NULL* |
| Din Kültürü ve Ahlak Bilgisi | *NULL* | *NULL* |
| Rehberlik ve Kariyer Planlama | *NULL* | *NULL* |

*(8 satır)*


---

## 5. CROSS JOIN — Her satır her satırla

**ON koşulu yoktur.** A'nın her satırı B'nin her satırıyla eşleştirilir (kartezyen çarpım). Sonuç satır sayısı = `A satırı × B satırı`.

```
 A × B
```

Kullanım alanı: kombinasyon / iskelet liste üretmek. Dikkat: büyük tablolarda patlar!

### Örnek 13: Seviye 1 — Sınıf seviyeleri × durum türleri

4 seviye × 3 durum = 12 satır. `SELECT ... UNION ALL SELECT ...` ile geçici bir liste yazabiliriz.

```sql
SELECT sv.sinif_seviye, d.durum
FROM (SELECT DISTINCT sinif_seviye FROM siniflar) sv
CROSS JOIN (SELECT 'Geldi' AS durum UNION ALL SELECT 'Gelmedi' UNION ALL SELECT 'Izinli') d
ORDER BY sv.sinif_seviye, d.durum;
```

**Sonuç:**

| sinif_seviye | durum |
|---|---|
| 5 | Geldi |
| 5 | Gelmedi |
| 5 | Izinli |
| 6 | Geldi |
| 6 | Gelmedi |
| 6 | Izinli |
| 7 | Geldi |
| 7 | Gelmedi |
| 7 | Izinli |
| 8 | Geldi |
| 8 | Gelmedi |
| 8 | Izinli |

*(12 satır)*


### Örnek 14: Seviye 2 — Sınıf × gün (5. sınıf şubeleri için haftalık iskelet)

5 şube × 5 gün = 25 kombinasyon. İlk satırları görelim.

```sql
SELECT s.sinif_adi, g.gun
FROM siniflar s
CROSS JOIN (SELECT 'Pazartesi' AS gun UNION ALL SELECT 'Sali' UNION ALL SELECT 'Carsamba' UNION ALL SELECT 'Persembe' UNION ALL SELECT 'Cuma') g
WHERE s.sinif_seviye = 5
ORDER BY s.sinif_adi, g.gun
LIMIT 8;
```

**Sonuç:**

| sinif_adi | gun |
|---|---|
| 5/A | Carsamba |
| 5/A | Cuma |
| 5/A | Pazartesi |
| 5/A | Persembe |
| 5/A | Sali |
| 5/B | Carsamba |
| 5/B | Cuma |
| 5/B | Pazartesi |

*(8 satır)*


### Örnek 15: Seviye 3 — CROSS JOIN + LEFT JOIN: 0 olan satırları da gösteren rapor

Önce tüm olasılıkları üret (sınıf × durum), sonra gerçek veriyi LEFT JOIN ile bağla. Hiç öğrencisi olmayan sınıf bile 0 ile görünür.

```sql
SELECT s.sinif_adi, d.durum, COUNT(y.id) AS adet
FROM siniflar s
CROSS JOIN (SELECT 'Geldi' AS durum UNION ALL SELECT 'Gelmedi' UNION ALL SELECT 'Izinli') d
LEFT JOIN ogrenciler o ON s.sinif_adi = o.sinif || '/' || o.sube
LEFT JOIN yoklama y ON y.ogrenci_id = o.id AND y.durum = d.durum
WHERE s.sinif_adi IN ('5/A', '5/E')
GROUP BY s.sinif_adi, d.durum
ORDER BY s.sinif_adi, d.durum;
```

**Sonuç:**

| sinif_adi | durum | adet |
|---|---|---|
| 5/A | Geldi | 1009 |
| 5/A | Gelmedi | 43 |
| 5/A | Izinli | 28 |
| 5/E | Geldi | 0 |
| 5/E | Gelmedi | 0 |
| 5/E | Izinli | 0 |

*(6 satır)*


---

## 6. SELF JOIN — Tablo kendisiyle

Ayrı bir JOIN türü değil, bir **tekniktir**: aynı tabloyu iki farklı takma adla (`a`, `b`) bağlarsınız. Aynı tablodaki satırları birbiriyle karşılaştırmak için kullanılır.

İpucu: `a.id < b.id` yazarak (1,2) ve (2,1) gibi tekrarları ve kişinin kendisiyle eşleşmesini engelleriz.

### Örnek 16: Seviye 1 — Aynı branşta çalışan öğretmen çiftleri

Matematik, Türkçe gibi branşlarda 2 öğretmen var.

```sql
SELECT a.ad || ' ' || a.soyad AS ogretmen_1, b.ad || ' ' || b.soyad AS ogretmen_2, a.brans
FROM ogretmenler a
INNER JOIN ogretmenler b ON a.brans = b.brans AND a.id < b.id
ORDER BY a.brans;
```

**Sonuç:**

| ogretmen_1 | ogretmen_2 | brans |
|---|---|---|
| Murat Kaya | Serkan Aslan | Fen Bilimleri |
| Kemal Yıldız | Burak Korkmaz | Matematik |
| Ayşe Demir | Merve Doğan | Türkçe |
| Selim Çelik | Gülşen Erdoğan | İngilizce |

*(4 satır)*


### Örnek 17: Seviye 2 — Soyadı aynı olan öğrenciler (kardeş olabilir)

Aynı soyadı taşıyan öğrenci çiftleri. İlk 8 satır.

```sql
SELECT a.ad || ' ' || a.soyad AS ogrenci_1, a.sinif || '/' || a.sube AS sinif_1,
       b.ad || ' ' || b.soyad AS ogrenci_2, b.sinif || '/' || b.sube AS sinif_2
FROM ogrenciler a
INNER JOIN ogrenciler b ON a.soyad = b.soyad AND a.id < b.id
ORDER BY a.soyad, a.id
LIMIT 8;
```

**Sonuç:**

| ogrenci_1 | sinif_1 | ogrenci_2 | sinif_2 |
|---|---|---|---|
| Yağız Acar | 6/C | Ali Acar | 7/B |
| Yağız Acar | 6/C | Alper Acar | 8/B |
| Yağız Acar | 6/C | Beste Acar | 7/B |
| Yağız Acar | 6/C | Güneş Acar | 6/B |
| Yağız Acar | 6/C | Hazal Acar | 5/A |
| Ali Acar | 7/B | Alper Acar | 8/B |
| Ali Acar | 7/B | Beste Acar | 7/B |
| Ali Acar | 7/B | Güneş Acar | 6/B |

*(8 satır)*


### Örnek 18: Seviye 3 — Her sınıfın en uzun boylu öğrencisi (8/A, 8/B)

Zor olan: `LEFT JOIN` + self. 'Benden uzun biri var mı?' diye sorarız; yoksa (`b.id IS NULL`) en uzun benim.

```sql
SELECT a.sinif || '/' || a.sube AS sinif, a.ad, a.soyad, a.boy_cm
FROM ogrenciler a
LEFT JOIN ogrenciler b
       ON b.sinif = a.sinif AND b.sube = a.sube AND b.boy_cm > a.boy_cm
WHERE b.id IS NULL AND a.sinif = '8' AND a.sube IN ('A', 'B')
ORDER BY sinif;
```

**Sonuç:**

| sinif | ad | soyad | boy_cm |
|---|---|---|---|
| 8/A | Alper | Aslan | 176.3 |
| 8/B | Kerem | Çelik | 177.2 |

*(2 satır)*


---

## Alıştırmalar (kendin dene)

1. 7/B sınıfının Çarşamba günü ders programını öğretmen adlarıyla listele. (INNER JOIN)
2. Hiç `Gelmedi` kaydı olmayan öğrencileri bul. (LEFT JOIN + IS NULL)
3. Her ders için haftalık programdaki toplam ders saatini ver; 0 olanlar da görünsün. (LEFT/RIGHT JOIN)
4. Her sınıfın en kısa boylu öğrencisini bul. (SELF JOIN)
5. Her öğretmen için ders verdiği sınıf sayısını (`COUNT(DISTINCT ...)`) bul.

## Sık yapılan hatalar

- **ON yazmayı unutmak:** Satır sayısı patlar (CROSS JOIN gibi davranır).
- **LEFT JOIN'de sağ tablo filtresini `WHERE`'e yazmak:** NULL satırlar elenir ve LEFT JOIN, INNER JOIN'e dönüşür. Sağ tablo filtresini `ON` içine yaz.
- **`COUNT(*)` ile `COUNT(sütun)` farkı:** LEFT JOIN sonrası eşleşmeyen satırı saymak istemiyorsan `COUNT(sag.id)` kullan.
- **Aynı sütun adı:** İki tabloda `id` varsa `a.id`, `b.id` diye tablo takma adını yaz.
