# Output directory layout

Keep generated artifacts grouped by workflow:

- `training/` — checkpoints and logs for new training runs.
- `validation/` — validation metrics and reports.
- `inference/` — separated audio stems.
- `experiments/` — short trials and experiment-specific outputs.
- `checkpoints/archive_<date>/` — archived checkpoints from completed runs.
- `metadata/` — model summaries and run metadata.

The previous run's checkpoints are preserved in
`checkpoints/archive_2026-10-22/`. Its model summary is in `metadata/`.

Git ignores generated files in this directory because model checkpoints and
audio outputs can be very large. This README remains tracked to document the
layout.
