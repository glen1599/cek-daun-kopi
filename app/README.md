# app/: aplikasi Flutter

Gambaran, fitur, arsitektur, dan unduhan APK ada di [README utama](../README.md).
Halaman ini catatan kerja untuk pengembang.

## Perintah sehari-hari

Semua dari folder `app/`:

```bash
flutter pub get
```

```bash
flutter test
```

```bash
flutter run
```

Melihat tampilan setiap layar tanpa HP (gambar ditulis ke `build/previews`):

```bash
PREVIEW_DIR=build/previews flutter test test/ui/screens
```

APK release per jenis prosesor:

```bash
flutter build apk --release --split-per-abi --target-platform android-arm,android-arm64
```

## Data yang dibuat dari server

Dua jenis file di aplikasi ini dibuat oleh skrip di `server/`, jangan diedit
langsung. Jalankan dari folder `server/`:

- `assets/content/diseases.json`, teks penyakit dan penanganan:

  ```bash
  python scripts/export_mobile_content.py
  ```

- `test/fixtures/`, keluaran model asli untuk tes deteksi Dart, sekaligus
  memeriksa bahwa alur acuan sama dengan server Ultralytics:

  ```bash
  python scripts/export_mobile_fixtures.py
  ```

Memeriksa foto yang disiapkan kode Dart pada model asli:

```bash
dart run tool/dump_tensors.dart build/parity
```

lalu dari `server/`:

```bash
python scripts/export_mobile_fixtures.py --tensors ../app/build/parity
```

## Ikon aplikasi

Digambar dari ikon daun yang dipakai aplikasi:

```bash
flutter test tool/render_app_icon_test.dart
```

```bash
dart run flutter_launcher_icons
```
