# Panda Farkı Bul! – Android (Capacitor 7)

Panda Games Studio · 2-5 yaş · 50 seviyeli fark bulma oyunu
Paket kimliği: `com.pandagamesstudio.farkibul` · Yatay + tam ekran

## Klasör yapısı

```
panda-farki-bul/
├── www/index.html              ← oyunun tamamı (50 seviye, SVG + Web Audio)
├── assets/                     ← ikon ve açılış görselleri (@capacitor/assets bunları kullanır)
│   ├── icon.png / icon-only.png         1024×1024 uygulama ikonu
│   ├── icon-foreground.png / icon-background.png   Android 8+ uyarlanabilir ikon katmanları
│   └── splash.png / splash-dark.png     2732×2732 açılış ekranı
├── scripts/patch-android.js    ← yatay ekran, tam ekran, uygulama adı, sürüm numarası
├── capacitor.config.json
├── package.json
├── privacy-policy.html         ← Play Console'a link olarak verilecek gizlilik politikası
└── .github/workflows/build.yml ← GitHub Actions: APK + AAB üretir
```

## GitHub ile derleme (önerilen)

1. Bu klasörün içeriğini yeni bir GitHub deposuna yükleyin (`main` dalı).
2. **Actions** sekmesinde “Android APK ve AAB Derle” iş akışı kendiliğinden başlar (elle başlatmak için *Run workflow*).
3. Bittiğinde **Artifacts → panda-farki-bul-android** zip'ini indirin:
   - `panda-farki-bul-debug.apk` → telefona/tablete doğrudan kurup test edin
   - `panda-farki-bul-release.aab` → Play Console'a yüklenir (imza anahtarı eklendiyse)
   - `panda-farki-bul-IMZASIZ.aab` → imza anahtarı eklenmediyse oluşur

## İmzalama anahtarı (Play Store için bir kez)

```bash
keytool -genkeypair -v -keystore upload.jks -alias panda -keyalg RSA -keysize 2048 -validity 10000
base64 -w0 upload.jks > upload.jks.b64      # macOS: base64 -i upload.jks -o upload.jks.b64
```

GitHub → **Settings → Secrets and variables → Actions → New repository secret**:

| Secret | Değer |
|---|---|
| `ANDROID_KEYSTORE_BASE64` | `upload.jks.b64` dosyasının içeriği |
| `ANDROID_KEYSTORE_PASSWORD` | keystore şifresi |
| `ANDROID_KEY_ALIAS` | `panda` |
| `ANDROID_KEY_PASSWORD` | anahtar şifresi |

`upload.jks` dosyasını ve şifreleri güvenli bir yerde saklayın; depoya **yüklemeyin**.

## Bilgisayarda derleme (isteğe bağlı)

```bash
npm install
npm run android:prepare      # cap add android + ikonlar + cap-patch + cap sync
npm run android:open         # Android Studio'da açar
```

## Sürüm numarası

`versionName` → `package.json` içindeki `version`.
`versionCode` → GitHub Actions çalıştırma numarası (her derlemede artar). Elle vermek için `VERSION_CODE=12 npm run cap-patch`.

## İkonu değiştirmek

`assets/` içindeki PNG'leri aynı ad ve boyutta yenileriyle değiştirip depoya gönderin; iş akışı ikonları yeniden üretir.

## Yayın öncesi kontrol listesi

- [ ] `privacy-policy.html` içindeki `ILETISIM_EPOSTANIZ` yazılarını gerçek e-posta adresinizle değiştirin, sayfayı GitHub Pages ile yayımlayıp linkini Play Console'a girin.
- [ ] **Gerçek ödeme bağlantısı:** Oyun, `window.PandaBilling.buy("unlock_all_levels")` / `restore(...)` köprüsü varsa onu kullanır; yoksa satın almayı **simüle eder** (test sürümü). Play Store'a çıkmadan önce Google Play Billing eklentisi bağlanmalı ve Play Console'da `unlock_all_levels` ürün kimliğiyle 24,99 TL'lik tek seferlik ürün oluşturulmalıdır.
- [ ] Play Console → Hedef kitle: 5 yaş ve altı · Reklam içermiyor · Aileler Politikası beyanı.
