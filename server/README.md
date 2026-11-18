# server/: API dan demo web

Server Flask untuk aplikasi Cek Daun Kopi. Aplikasi HP **tidak** butuh server
ini untuk mendeteksi; server dipakai untuk demo web dan untuk memperbarui teks
Info Penyakit saat ada internet.

Berjalan di <https://glendevit.pythonanywhere.com>.

Kode ini berawal dari proyek web
[coffee-leaf-disease-yolov8](https://github.com/glen1599/coffee-leaf-disease-yolov8)
(halaman web, detektor YOLOv8, log pemindaian anonim). Yang ditambahkan di sini:

| Bagian | File |
|---|---|
| API berversi `/api/v1` (`health`, `diseases`, `predict`), JSON ringkas, Bahasa Indonesia | `coffee_leaf/api_v1.py`, `coffee_leaf/content.py` |
| Mode ringan `DETECTOR_BACKEND=onnx`: ONNX Runtime saja, tanpa PyTorch, hasil sama | `coffee_leaf/onnx_detector.py`, `coffee_leaf/onnx_pipeline.py` |
| Alamat pengunjung di balik proxy (`TRUSTED_PROXIES`) untuk batas permintaan | `coffee_leaf/__init__.py` |
| Ekspor konten dan data uji untuk aplikasi | `scripts/export_mobile_content.py`, `scripts/export_mobile_fixtures.py` |
| Deploy: PythonAnywhere, Render, Hugging Face Spaces | `deploy/`, `docs/DEPLOY_*.md` |

## API

| Endpoint | Isi |
|---|---|
| `GET /api/v1/health` | Status, mesin model, kelas, batas unggahan |
| `GET /api/v1/diseases?lang=id\|en` | Basis pengetahuan: nama, gejala, penanganan, metrik model |
| `POST /api/v1/predict` | Foto (`file`, multipart) → penyakit, keyakinan, kotak temuan |

Galat selalu berbentuk `{"error": {"code": "...", "message": "..."}}`, dengan
pesan berbahasa Indonesia secara bawaan (`lang=en` untuk Inggris).

```bash
curl -F "file=@static/img/samples/coffee_leaf_rust.jpg" https://glendevit.pythonanywhere.com/api/v1/predict
```

## Menjalankan

Python 3.11. Dari folder `server/`:

```bash
uv venv --python 3.11 && uv pip install -r requirements.txt
```

```bash
PORT=5050 .venv/bin/python app.py
```

Mode ringan (cukup `onnxruntime`, `opencv-python-headless`, Flask):

```bash
DETECTOR_BACKEND=onnx ONNX_MODEL_PATH=../app/assets/models/best.onnx PORT=5050 .venv/bin/python app.py
```

## Tes

```bash
.venv/bin/pytest -q
```

```bash
.venv/bin/ruff check . && .venv/bin/mypy
```

75 tes, termasuk kontrak API, batas permintaan di balik proxy, dan
pembandingan mode ringan dengan keluaran server Ultralytics yang direkam.

## Deploy

- [PythonAnywhere](docs/DEPLOY_PYTHONANYWHERE.md): gratis, tanpa kartu kredit (yang dipakai sekarang)
- [Render](docs/DEPLOY_RENDER.md): Docker, meminta kartu untuk verifikasi
- [Hugging Face Spaces](docs/DEPLOY_HUGGINGFACE.md): server lengkap, butuh akun PRO

Kartu model: [docs/MODEL_CARD.md](docs/MODEL_CARD.md).
