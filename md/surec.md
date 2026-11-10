# Süreç (surec.md)

## Proje
İşletme içi **yapay zeka ile kodlama eğitimi** reposu (5 gün). Program: `README.md`.

## Oturum protokolü
1. Oturum başında bu dosyayı oku.
2. Kod/dosya değişikliği bitince `git commit` + `git push` (mesaj: `x.y Başlık` + madde madde ayrıntı).
3. Secret içerebilecek dosyalar (`.env`, `config.json`) commit edilmeden önce kontrol edilir.
4. Son kullanıcıyı ilgilendiren değişiklik varsa `release_notes.json` güncellenir (şu an projede yok, atlanır).
5. Yanıtlar kısa olur: ilk satırda sonuç, tek ana konu, sıradaki adım tek yerde.

## Dosyalar
| Dosya | Amaç |
|---|---|
| `README.md` | Eğitim programı (5 gün) |
| `okul.db` | SQLite, **sahte** okul verisi — SQL/JOIN öğrenmek için |
| `okul.md` | Öğrenciler için JOIN rehberi (6 tür × 3 örnek = 18 sorgu, sonuçlarıyla) |
| `navicat.md` | Navicat Premium Windows kurulumu + okul.db'yi Navicat'e tanıtma |
| `md/surec.md` | Bu dosya |

## Veritabanı notları
- Tablolar: `ogrenciler` (204), `ogretmenler` (19), `siniflar` (18), `dersler` (15), `yoklama` (18000), `haftalik_ders_programi` (560).
- `ogrenciler` ↔ `siniflar` bağlantısı numarayla değil: `sinif || '/' || sube = sinif_adi`.
- LEFT/RIGHT/FULL JOIN örnekleri için **bilerek eşleşmeyen veri** eklendi (rehbersiz sınıf, programsız ders/öğretmen, yoklamasız öğrenci, tanımsız 5/F ve 6/F şubesi).
- RIGHT/FULL JOIN için SQLite 3.39+ gerekir.
- SQLite `VALUES ... AS t(kolon)` söz dizimini desteklemez; `SELECT ... UNION ALL` kullanıldı.
- `okul.md` içindeki sonuç tabloları sorgular çalıştırılarak üretildi; veri değişirse yeniden üretilmeli.

## Yapıldı
- [x] README (eğitim programı)
- [x] surec.md
- [x] okul.db genişletildi (eşleşmeyen veriler)
- [x] okul.md JOIN rehberi
- [x] navicat.md kurulum rehberi

## Sıradaki adım
- `.gitignore` oluştur (`.env`, `config.json`, `__pycache__/`).
- 2. gün eğitimi için Postman/API örnek uygulaması (5. gün projesinin altyapısı) planla.
