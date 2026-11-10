# Yapay Zeka ile Kodlama Eğitimi

Bu repo, işletme içinde verilen **yapay zeka ile kodlama eğitimi** için hazırlanmıştır.
Eğitim 5 gün sürer; 4 gün temel bilgiler, son gün uçtan uca uygulamalı projedir.

## Eğitim Programı

### 1. Gün: Temel Altyapı, Ağ, İşletim Sistemleri & Sanallaştırma

| Saat | Konu | İçerik |
|---|---|---|
| 09:00 - 10:00 | Ağ (Network) Temelleri | LAN / WAN, Wi-Fi / Wired; Modem, Router, Switch, VLAN, Firewall, VPN; Port nedir? IP yapısı; TCP, UDP ve Stream farkı |
| 10:00 - 10:15 | Mola | |
| 10:15 - 11:30 | İşletim Sistemleri, Sanallaştırma & Web Mimarisi | Windows vs Linux sunucu mimarileri; Docker, Container ve Docker Manager mantığı; Client-Side vs Server-Side; Browser teknolojileri (Chromium, GUI, Frontend, Backend, DOM) |

### 2. Gün: Veri Yapıları, Veritabanı Yönetimi & API

| Saat | Konu | İçerik |
|---|---|---|
| 09:00 - 10:00 | Veri Biçimleri & Veritabanı Temelleri | Structured, Unstructured ve Raw Data; JSON, XML, CSV ve Plain Text; SQL nedir? RDBMS mimarisi; SQLite vs PostgreSQL |
| 10:00 - 10:15 | Mola | |
| 10:15 - 11:30 | Navicat ile Veritabanı Yönetimi & API | Navicat arayüzü ve sunucu bağlantısı; SELECT, INSERT, UPDATE, DELETE, INDEX, JOIN; API mantığı (GET / POST); Postman ile API testleri |

### 3. Gün: Git/GitHub, Geliştirme Ortamı (VSCode) & Yazılım Temelleri

| Saat | Konu | İçerik |
|---|---|---|
| 09:00 - 10:00 | Versiyon Kontrol (Git & GitHub) | Repo, Branch, Commit, Push, Pull; GitHub kullanımı ve proje eşitleme |
| 10:00 - 10:15 | Mola | |
| 10:15 - 11:30 | VSCode & Programlama Dillerine Giriş | VSCode arayüzü, eklentiler, entegre terminal, Git entegrasyonu; Senkron vs Asenkron çalışma; HTML / CSS / JavaScript temelleri; Python ve Node.js runtime ortamları |

### 4. Gün: Yapay Zekâ ile Kodlama (Claude/Ollama) & Yayınlama (Deploy)

| Saat | Konu | İçerik |
|---|---|---|
| 09:00 - 10:15 | AI Destekli Yazılım Geliştirme (Vibe Coding) | LLM mantığı: model, token, context window; Local AI: Ollama; VSCode içinde Claude eklentisi; etkili prompt, şartname (Spec) ve adım adım kod üretimi; terminal çıktılarıyla hata ayıklama (troubleshooting); kurumsal yazılım standartları ve Clean Code |
| 10:15 - 10:30 | Mola | |
| 10:30 - 11:30 | Canlıya Alma (Deployment) & Güvenlik | `.env` vs `config.json` ve güvenliği (`.gitignore`); projeyi Dockerize etme ve sunucuya yükleme; DNS ve domain yönlendirme; NGINX Reverse Proxy ve SSL sertifikası |

### 5. Gün: Uçtan Uca Uygulamalı Proje Geliştirme

| Saat | Aşama | Pratik Uygulama Adımları |
|---|---|---|
| 09:00 - 09:40 | 1. Mimari & Prompt Hazırlığı | Gereksinimlerin belirlenmesi; Claude'a verilecek şartname (Spec) metninin hazırlanması; proje klasörü, `git init` ve `.gitignore` yapılandırması |
| 09:40 - 10:20 | 2. AI ile Kodlama & Veritabanı | Claude ile veritabanı şeması (SQLite/PostgreSQL); Python (FastAPI/Flask) veya Node.js ile REST API (GET, POST, UPDATE, DELETE); basit web arayüzünün (HTML/JS) API'ye bağlanması |
| 10:20 - 10:35 | Mola | |
| 10:35 - 11:00 | 3. Hata Ayıklama & Yapılandırma | Uygulamayı yerelde çalıştırma, terminal hatalarını Claude'a okutarak çözme; `.env` ve `config.json` entegrasyonu; commit ve GitHub'a push |
| 11:00 - 11:30 | 4. Konteynerizasyon & Canlıya Alma | Claude ile `Dockerfile` ve `docker-compose.yml`; Docker Manager/CLI ile konteynerleştirme; NGINX Reverse Proxy arkasında yayına alma |

## Eğitim Sonunda Kazanılacaklar

- Ağ, sunucu ve web mimarisinin temel kavramlarını anlamak
- Veritabanı ve API mantığını kavrayıp SQL / Postman ile çalışmak
- Git, GitHub ve VSCode ile düzenli geliştirme yapmak
- Claude (ve yerel olarak Ollama) ile şartname temelli kod ürettirmek
- Uygulamayı Docker + NGINX ile güvenli şekilde canlıya almak

## Repo Notları

- Gizli bilgiler (`.env`, `config.json` vb.) repoya **commit edilmez**; `.gitignore` ile dışarıda tutulur.
- `okul.db` (sahte verili SQLite) eğitim materyali olarak repoda durur; SQL/JOIN çalışması için bkz. [okul.md](okul.md).
- Navicat deneme süresi dolarsa, [navicat.bat](navicat.bat) dosyasını normal kullanıcı yetkileri ile çalıştırıp, deneme süresi tekrar başlatılabilir.
