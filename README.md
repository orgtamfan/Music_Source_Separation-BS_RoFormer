# BS-RoFormer Music Source Separation Training

Proyek ini berisi kode untuk melatih, memvalidasi, dan menjalankan inferensi
model pemisahan sumber musik **Band-Split RoFormer (BS-RoFormer)**. Kode model
berasal dari [Music-Source-Separation-Training](https://github.com/ZFTurbo/Music-Source-Separation-Training),
dengan konfigurasi dan perubahan training yang disesuaikan untuk eksperimen
BS-RoFormer pada dataset MUSDB18.

## Isi repository

- `configs/config_musdb18_bs_roformer.yaml` — konfigurasi BS-RoFormer untuk
  eksperimen MUSDB18.
- `models/bs_roformer/` — implementasi arsitektur BS-RoFormer.
- `train.py` — training pada satu GPU atau beberapa GPU melalui DDP.
- `train_accelerate.py` — alternatif training menggunakan Hugging Face
  Accelerate.
- `valid.py` dan `inference.py` — validasi metrik dan pemisahan audio.
- `utils/` — dataset, audio, loss, metrik, dan konfigurasi.
- `scripts/`, `tests/`, `docs/` — alat bantu, pengujian, dan dokumentasi.
- `results/` — direktori lokal untuk checkpoint dan output; artefaknya
  diabaikan Git.

## Kebutuhan

- Python 3.10–3.13.
- PyTorch yang sesuai dengan perangkat. Training GPU memerlukan build CUDA
  serta GPU NVIDIA dengan VRAM memadai.
- Dataset berisi campuran dan stem target yang didukung format dataset pada
  [docs/dataset_types.md](docs/dataset_types.md).
- Dependensi Python dari `requirements.txt`.

## Instalasi

Di Windows PowerShell:

```powershell
py -3.13 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

Pasang build PyTorch yang cocok untuk driver/CUDA pada mesin, lalu pasang
dependensi proyek:

```powershell
python -m pip install torch torchaudio
python -m pip install -r requirements.txt
```

Jika memakai Linux, macOS, atau versi CUDA tertentu, ikuti petunjuk instalasi
PyTorch untuk platform tersebut sebelum memasang dependensi lainnya.

## Dataset dan konfigurasi

Sesuaikan `configs/config_musdb18_bs_roformer.yaml` dengan dataset dan GPU.
Konfigurasi saat ini memakai `batch_size: 1` dan
`gradient_accumulation_steps: 16` sebagai titik awal untuk GPU dengan VRAM
terbatas. Path dataset diberikan ke perintah training; data audio tidak
disimpan di repository.

Contoh di bawah mengasumsikan dataset training dan validasi sudah disiapkan
sesuai [format dataset](docs/dataset_types.md). Ganti path contoh dengan lokasi
dataset lokal Anda.

## Training

```powershell
python train.py `
  --model_type bs_roformer `
  --config_path configs/config_musdb18_bs_roformer.yaml `
  --data_path datasets/train `
  --valid_path datasets/valid `
  --results_path results/training `
  --num_workers 4 `
  --device_ids 0
```

Untuk melanjutkan training dari checkpoint yang kompatibel, tambahkan:

```text
--start_check_point results/runs/archive_2026-10-22/last_bs_roformer.ckpt
```

Validasi training berjalan setiap epoch. Pastikan jenis checkpoint cocok
dengan model dan konfigurasi.

## Validasi

```powershell
python valid.py `
  --model_type bs_roformer `
  --config_path configs/config_musdb18_bs_roformer.yaml `
  --start_check_point results/runs/archive_2026-10-22/model_bs_roformer_ep_204_sdr_3.9355.ckpt `
  --valid_path datasets/valid `
  --store_dir results/validation `
  --device_ids 0
```

## Inferensi audio

Letakkan audio campuran di satu folder, lalu jalankan:

```powershell
python inference.py `
  --model_type bs_roformer `
  --config_path configs/config_musdb18_bs_roformer.yaml `
  --start_check_point results/runs/archive_2026-10-22/model_bs_roformer_ep_204_sdr_3.9355.ckpt `
  --input_folder audio/input `
  --store_dir results/inference `
  --device_ids 0
```

Checkpoint lokal saat ini berada di `results/runs/archive_2026-10-22/` dan
tidak disertakan saat push ke GitHub karena ukuran file. Gunakan checkpoint
Anda sendiri atau unduh pasangan konfigurasi/checkpoint yang sesuai.

## Tata letak output

```text
results/
├── training/       # checkpoint dan log run baru
├── validation/     # metrik validasi
├── inference/      # stem audio hasil pemisahan
├── experiments/    # percobaan singkat
└── runs/           # arsip run lama lokal
```

Git mengabaikan isi `results/` agar checkpoint, audio, dan output eksperimen
tidak ikut masuk ke repository. Lihat [results/README.md](results/README.md).

## Dokumentasi

- [Panduan mulai](docs/getting_started.md)
- [Format dataset](docs/dataset_types.md)
- [CLI](docs/cli.md)
- [Panduan testing/validasi](docs/test.md)
- [Eksperimen](docs/experiments.md)
- [LoRA](docs/LoRA.md)

## Atribusi dan lisensi

Proyek ini diturunkan dari
[ZFTurbo/Music-Source-Separation-Training](https://github.com/ZFTurbo/Music-Source-Separation-Training)
dan mengikuti lisensi yang tercantum di [LICENSE](LICENSE). BS-RoFormer
diperkenalkan dalam [paper Band-Split RoFormer](https://arxiv.org/abs/2309.02612).
