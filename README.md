# BS-RoFormer Music Source Separation Training

This project contains code for training, validating, and running inference
with the **Band-Split RoFormer (BS-RoFormer)** music source separation model.
The model code is based on
[Music-Source-Separation-Training](https://github.com/ZFTurbo/Music-Source-Separation-Training),
with a configuration and training changes for BS-RoFormer experiments on the
MUSDB18 dataset.

## Repository contents

- `configs/config_musdb18_bs_roformer.yaml` — BS-RoFormer configuration for
  MUSDB18 experiments.
- `models/bs_roformer/` — BS-RoFormer architecture implementation.
- `train.py` — training on one GPU or multiple GPUs with DDP.
- `train_accelerate.py` — an alternative training entry point using Hugging
  Face Accelerate.
- `valid.py` and `inference.py` — model validation and audio separation.
- `utils/` — dataset, audio, loss, metrics, and configuration utilities.
- `scripts/`, `tests/`, and `docs/` — helper scripts, tests, and documentation.
- `results/` — local output directory; generated artifacts are ignored by Git.

## Requirements

- Python 3.10–3.13.
- PyTorch appropriate for your device. GPU training requires a CUDA build and
  an NVIDIA GPU with sufficient VRAM.
- A dataset containing mixtures and target stems in a supported layout. See
  [docs/dataset_types.md](docs/dataset_types.md).
- Python dependencies listed in `requirements.txt`.

## Installation

In Windows PowerShell:

```powershell
py -3.13 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

Install the PyTorch build that matches your driver and CUDA setup, then install
the project dependencies:

```powershell
python -m pip install torch torchaudio
python -m pip install -r requirements.txt
```

For Linux, macOS, or a specific CUDA version, follow the PyTorch installation
instructions for your platform before installing the remaining dependencies.

## Dataset and configuration

Adjust `configs/config_musdb18_bs_roformer.yaml` for your dataset and GPU. The
current configuration uses `batch_size: 1` and
`gradient_accumulation_steps: 16` as a starting point for GPUs with limited
VRAM. Dataset paths are passed to the training command; audio data is not
stored in this repository.

The examples below assume that your training and validation datasets follow a
supported layout from [docs/dataset_types.md](docs/dataset_types.md). Replace
the example paths with the paths to your local datasets.

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

To resume training from a compatible checkpoint, add:

```text
--start_check_point path/to/checkpoint.ckpt
```

Training validation runs every epoch. Make sure the checkpoint architecture
matches the selected model and configuration.

## Validation

```powershell
python valid.py `
  --model_type bs_roformer `
  --config_path configs/config_musdb18_bs_roformer.yaml `
  --start_check_point path/to/checkpoint.ckpt `
  --valid_path datasets/valid `
  --store_dir results/validation `
  --device_ids 0
```

## Audio inference

Put mixed audio files in one folder, then run:

```powershell
python inference.py `
  --model_type bs_roformer `
  --config_path configs/config_musdb18_bs_roformer.yaml `
  --start_check_point path/to/checkpoint.ckpt `
  --input_folder audio/input `
  --store_dir results/inference `
  --device_ids 0
```

Training checkpoints are kept locally under `results/` and are not included in
GitHub because of their size. Use your own checkpoint or download a compatible
configuration/checkpoint pair.

## Output layout

```text
results/
├── training/       # checkpoints and logs for new runs
├── validation/     # validation metrics
├── inference/      # separated audio stems
├── experiments/    # short experiments
└── runs/           # local archived runs
```

Git ignores generated files in `results/` so checkpoints, audio, and
experiment outputs are not added to the repository. See
[results/README.md](results/README.md).

## Documentation

- [Getting started](docs/getting_started.md)
- [Dataset formats](docs/dataset_types.md)
- [Command-line interface](docs/cli.md)
- [Testing and validation](docs/test.md)
- [Experiments](docs/experiments.md)
- [LoRA](docs/LoRA.md)

## Attribution and license

This project is derived from
[ZFTurbo/Music-Source-Separation-Training](https://github.com/ZFTurbo/Music-Source-Separation-Training)
and follows the license in [LICENSE](LICENSE). BS-RoFormer was introduced in
the [Band-Split RoFormer paper](https://arxiv.org/abs/2309.02612).
