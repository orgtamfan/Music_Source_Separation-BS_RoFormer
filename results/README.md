# Struktur output

Gunakan folder berikut untuk memisahkan artefak tiap alur kerja:

- `training/` — checkpoint dan log untuk run baru.
- `validation/` — ringkasan metrik dan hasil validasi.
- `inference/` — audio stem hasil pemisahan.
- `experiments/` — output uji coba singkat atau konfigurasi eksperimental.
- `runs/` — arsip run yang sudah selesai serta metadata model.

Checkpoint lama dipindahkan tanpa dihapus ke `runs/archive_2026-10-22/`.
`runs/model_info.json` menyimpan informasi model dari run tersebut. Folder
output dan checkpoint diabaikan Git karena ukurannya besar; file README ini
tetap dilacak.
