# Fantasia Database

| Field | Value |
|-------|-------|
| Id | `fantasia` |
| Category | `physio` |
| Access | `public` |
| Homepage | https://physionet.org/content/fantasia/1.0.0/ |
| PhysioNet slug | `fantasia` |
| WFDB name | `fantasia` |
| Paper alias | Fantasia (Abdeldayem & Bourlai, TBIOM 2019 spectral-correlation ECG ID) |

20 young + 20 elderly; ECG (+ respiration; some BP) @ 250 Hz. **40** subjects.

## Download

```bash
# from repo root (Git Bash / WSL / Linux / macOS)
bash projects/datasets/scripts/download_fantasia.sh
```

Or:

```bash
cd projects/datasets/scripts
./download_fantasia.sh
```

Preferred (after `pip install wfdb`):

```bash
python -c "import wfdb; wfdb.dl_database('fantasia', r'projects/datasets/fantasia')"
```

Place PhysioNet files under `projects/datasets/fantasia/`.
