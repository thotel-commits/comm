# Steemit / Steem Comment Bot

Bu sürüm, önceki botun özelliklerini koruyarak yalnızca **Steem ana ağına** bağlanacak şekilde uyarlanmıştır. Okuma işlemleri `https://api.steemit.com` üzerinden yapılır; yorum gönderimi `beem.Steem` ile Steem ağına imzalanır. Hive RPC veya Hive anahtar değişkenleri kullanılmaz.

## Özellikler

- Son 24 saatteki gönderileri tarar.
- Gemini ile gönderiye özgü kısa yorum taslağı üretir.
- Genel/tekrarlanan yorumları ve proje/otomasyon hesaplarını filtreler.
- Minimum Steem Power (SP), günlük limit, yazar bekleme süresi ve rastgele gecikme uygular.
- Daha önce botun yorumuna oy veren gönderi yazarlarını önceliklendirir.
- Durumu `steem_state.enc` dosyasında şifreli saklar.
- GitHub Actions ile zamanlanabilir.

## GitHub ayarları

Repository → **Settings → Secrets and variables → Actions** bölümünde şu değerleri ekleyin.

### Repository secrets

- `STEEM_POSTING_KEY`: Steem hesabının yalnızca **posting** yetkisine ait private key. Owner veya active key kullanmayın.
- `GEMINI_API_KEY`: Google Gemini API anahtarı.
- `STATE_KEY`: Fernet anahtarı. Yerelde üretmek için: `python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"`
- İsteğe bağlı: `BLACKLIST`, `COMMENT_RULES`, `GENERIC_PHRASES`, `EXTRA_SKIP_HINTS`

### Repository variables

- `STEEM_ACCOUNT`: Steem kullanıcı adı (başında `@` olmadan).
- `DRY_RUN`: İlk test için `true` bırakın. Gerçek yorum göndermek için yalnızca ayarları doğruladıktan sonra `false` yapın.
- İsteğe bağlı: `MIN_HP` (varsayılan `5000`), `DAILY_LIMIT` (`20`), `MAX_PER_RUN` (`1`), `RUN_EVERY_MIN` (`30`), `MIN_GAP_MIN` (`25`), `JITTER_MAX_SEC` (`300`), `COOLDOWN_DAYS` (`1`), `MODEL` (`gemini-3.5-flash`), `LOG_TEXT` (`false`), `MAX_WORDS` (`35`), `MAX_POSTS_PER_DAY` (`4`).

`STATE_KEY` değerini kaybetmeyin; şifreli durum dosyası bu anahtar olmadan çözülemez. `steem_state.enc`, eski Hive botunun `state.enc` dosyasından ayrı tutulur; Hive geçmişi Steem geçmişi gibi kullanılmaz.

## İlk çalıştırma

1. Dosyaları kendi GitHub repository'nize yükleyin.
2. Yukarıdaki secret ve variable değerlerini ayarlayın.
3. **Actions → sync → Run workflow** ile çalıştırın.
4. `DRY_RUN=true` iken yalnızca tarama/yorum üretme denemesi yapılır; blokzincire yorum gönderilmez. Logları inceleyin.
5. Canlı moda geçecekseniz `STEEM_ACCOUNT` ve `STEEM_POSTING_KEY` değerlerini iki kez kontrol edin, sonra `DRY_RUN=false` yapın.

## Notlar

- Bu bot otomatik yorum gönderir; platform kurallarına ve topluluk beklentilerine uygun limitler kullanın. Varsayılan günlük limit 20, çalışma başına limit 1'dir.
- İlk testte `DRY_RUN=true` kullanın. API erişimi, Gemini model erişimi ve hesap yetkileri ortamınıza bağlıdır; bu arşivde canlı zincire işlem gönderildiği iddia edilmez.
