# Fantasia Database

[![License: ODC-By 1.0](https://img.shields.io/badge/License-ODC--By%201.0-green)](https://opendatacommons.org/licenses/by/1-0/)
[![Access: public](https://img.shields.io/badge/access-public-0e8a16.svg)](https://physionet.org/content/fantasia/1.0.0/)

**Fantasia Database** — resting ECG (+ respiration; half with BP) from healthy young and elderly adults watching *Fantasia*; PhysioNet open access.

- **Dataset repository**: https://github.com/biometric-community/MIT-BIH-Fantasia-Database
- **Upstream source**: https://physionet.org/content/fantasia/1.0.0/
- **DOI**: https://doi.org/10.13026/C2RG61
- **Original format**: PhysioNet WFDB (`.dat` / `.hea` / `.ecg`) under `data/`
- **License (data)**: [Open Data Commons Attribution License v1.0](https://opendatacommons.org/licenses/by/1-0/) (PhysioNet)
- **License (helpers / docs)**: CC BY 4.0 (see [`LICENSE`](LICENSE))

| Field | Value |
|-------|-------|
| Catalog id (tbiom) | `fantasia` |
| Category | `physio` |
| Access | `public` |
| PhysioNet / WFDB slug | `fantasia` |
| Upstream homepage | https://physionet.org/content/fantasia/1.0.0/ |
| Paper alias | Fantasia (e.g. Abdeldayem & Bourlai, TBIOM 2019 spectral-correlation ECG ID) |

## TL;DR

- **Task**: heart-rate variability / fractal RR dynamics / age-group ECG analysis; also used for ECG identity benchmarks
- **Modality**: ECG + respiration; half of records also include uncalibrated continuous BP (WFDB `.dat` / `.hea` / `.ecg`)
- **Platform**: supine resting recordings while watching Disney’s *Fantasia* (1940)
- **Real/Synthetic**: real
- **Subjects / records**: **40** (20 young ages 21–34; 20 elderly ages 68–85); equal men/women overall and within each `f1*` / `f2*` × cohort cell
- **Sampling**: **250 Hz** for 39/40 records; record `f2y02` headers report **333 Hz**
- **Duration**: ~2 h supine rest (local sample counts ≈ 1.56M–2.34M; ~104–156 min at the stated rate)
- **Signals**: `f1*` → RESP + ECG (2 ch, format 16); `f2*` → RESP + ECG + BP (3 ch, format 212)
- **Annotations**: beat labels in `.ecg` (automated detector + visual verification upstream)
- **Size**: ~292.5 MiB uncompressed (PhysioNet); ~291.5 MiB under `data/` locally
- **Citation**: Iyengar et al., Am J Physiol 1996 (+ PhysioNet citation)

## Table of contents

- [Download](#download)
- [Dataset structure](#dataset-structure)
- [Annotation schema](#annotation-schema)
- [Stats and splits](#stats-and-splits)
- [Quick start](#quick-start)
- [Evaluation and baselines](#evaluation-and-baselines)
- [Datasheet (data card)](#datasheet-data-card)
- [Known issues and caveats](#known-issues-and-caveats)
- [License](#license)
- [Citation](#citation)
- [Contact](#contact)

## Download

- **This repository**: WFDB records under [`data/`](data/) (40 × `.hea` / `.dat` / `.ecg`).
- **Upstream**: https://physionet.org/content/fantasia/1.0.0/ (ZIP ≈ 292.6 MiB).
- **Helper script** (from tbiom monorepo root):

```bash
bash projects/datasets/scripts/download_fantasia.sh
```

Preferred (after `pip install wfdb`):

```bash
python -c "import wfdb; wfdb.dl_database('fantasia', r'projects/datasets/fantasia/data')"
```

Or with wget:

```bash
wget -r -N -c -np https://physionet.org/files/fantasia/1.0.0/ -P projects/datasets/fantasia/data
```

## Dataset structure

```text
fantasia/
├── README.md
├── LICENSE
└── data/
    ├── f1y01.hea / f1y01.dat / f1y01.ecg
    ├── f1o01.hea / f1o01.dat / f1o01.ecg
    ├── ...
    ├── f2y01.hea / f2y01.dat / f2y01.ecg
    └── f2o10.hea / f2o10.dat / f2o10.ecg
```

**Record IDs** (40):

| Cohort | Without BP (`f1*`, 2 ch) | With BP (`f2*`, 3 ch) |
|--------|--------------------------|----------------------|
| Young (`y`) | `f1y01`–`f1y10` | `f2y01`–`f2y10` |
| Elderly (`o`) | `f1o01`–`f1o10` | `f2o01`–`f2o10` |

- **Splits**: no official ML train/val/test partition.
- **Layout notes**: flat WFDB naming; beat annotations use extension `.ecg` (not `.atr`).

## Annotation schema

### WFDB records (`data/*.hea`, `.dat`, `.ecg`)

- **`.hea`**: channel count, sampling rate, sample count, ADC/gain fields, plus comment lines with `Age` and `Sex`
- **`.dat`**: binary multi-channel samples (`f1*` format **16**; `f2*` format **212** in local headers)
- **`.ecg`**: beat annotations (WFDB annotation file; read with extension `"ecg"`)
- **Time base**: sample index at the record’s stated `fs` (usually 250 Hz)
- **Example** (record `f1y01` header):

```text
f1y01 2 250 1806271
f1y01.dat 16 2000 16 0 16000 -22048 0 RESP
f1y01.dat 16 2000 16 0 15904 168 0 ECG
# Age: 23 Sex: F
```

**Example** (record `f2y01` with BP):

```text
f2y01 3 250 1768624
f2y01.dat 212 409.6 12 0 -81 15004 0 RESP
f2y01.dat 212 409.6 12 0 4 20763 0 ECG
f2y01.dat 212 409.6 12 0 322 10580 0 BP
# Age: 23 Sex: F
```

Use [WFDB](https://physionet.org/content/wfdb/) / `wfdb` (Python) to read signals and annotations.

### Signal subsets

| Subset | Channels | Typical format | Notes |
|--------|----------|----------------|-------|
| `f1*` | RESP, ECG | 16 | no BP |
| `f2*` | RESP, ECG, BP | 212 | BP is continuous, **uncalibrated** (upstream) |

## Stats and splits

Counts verified from local `data/`:

| Measure | Count |
|---------|------:|
| Records (`.hea` / `.dat` / `.ecg`) | 40 |
| Young / elderly | 20 / 20 |
| Female / male | 20 / 20 |
| Without BP (`f1*`) / with BP (`f2*`) | 20 / 20 |
| Sample rate | 250 Hz (39 records); 333 Hz (`f2y02`) |
| Samples per record | 1 558 559 – 2 342 528 |
| Approx. duration | ~104 – 156 min |
| Local `data/` size | ~291.5 MiB |

No official subject-disjoint train/test split — keep age/sex balance in mind when defining folds.

## Quick start

```bash
cd projects/datasets/fantasia
pip install wfdb
```

```python
from pathlib import Path
import wfdb

root = Path("data")
records = sorted(p.stem for p in root.glob("*.hea"))
print(len(records), "records:", records[:5], "...")

rec = root / "f1y01"
sig, fields = wfdb.rdsamp(str(rec))
ann = wfdb.rdann(str(rec), "ecg")
print(sig.shape, fields["fs"], "Hz;", fields["sig_name"])
print(len(ann.sample), "beat annotations; symbols:", ann.symbol[:8])
```

**Dependencies**: optional `wfdb` for loading; bash + network for re-download helpers.

## Evaluation and baselines

- **Primary metrics**: RR / HRV fractal scaling and age-group separation (Iyengar et al.); ECG biometric metrics when used for identity
- **Suggested baselines**: classical HRV / fractal RR methods on Fantasia; modern ECG deep-learning papers citing this corpus
- **Baseline numbers**: not reproduced here — see citing literature

## Datasheet (data card)

### Motivation

Study age-related alterations in the fractal scaling of cardiac interbeat-interval dynamics in rigorously screened healthy subjects.

### Composition

40 ~2-hour supine resting recordings. All include ECG and respiration; half (`f2*`) also include an uncalibrated continuous noninvasive blood pressure waveform. Headers record age and sex. Beat annotations are provided in `.ecg` files.

### Collection process

Subjects watched Disney’s *Fantasia* to help maintain wakefulness while remaining in sinus rhythm at rest. Digitized and released via PhysioNet as `fantasia` 1.0.0 (Iyengar et al., 1996).

### Preprocessing

Distributed in PhysioNet WFDB format. Beat labels from an automated arrhythmia detector with visual verification (upstream description). Local headers use format 16 (`f1*`) or 212 (`f2*`).

### Distribution

- **Signal / annotation files**: ODC-By 1.0 via PhysioNet
- **Helpers / docs in this folder**: CC BY 4.0 (`LICENSE`)
- **GitHub mirror**: https://github.com/biometric-community/MIT-BIH-Fantasia-Database

### Maintenance

Re-download with `wfdb.dl_database('fantasia', ...)` or `download_fantasia.sh` if needed. Keep record IDs and WFDB triples (including `.ecg`) intact.

## Known issues and caveats

- Beat annotations use extension **`.ecg`**, not `.atr` — pass `"ecg"` to `wfdb.rdann`
- **`f2y02` reports 333 Hz** in its header while other records are 250 Hz; do not hard-code `fs=250` for all records
- BP channels on `f2*` are **uncalibrated** continuous noninvasive waveforms
- Sample counts / durations vary slightly around the nominal 120 minutes
- No official ML split — avoid leakage when segmenting from the same subject
- Catalog id `fantasia` is the PhysioNet / WFDB database name

## License

**Data files** are licensed under **[ODC-By 1.0](https://opendatacommons.org/licenses/by/1-0/)** as published by PhysioNet.

**Packaging helpers / docs** in this folder are **[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)**. See [`LICENSE`](LICENSE).

## Citation

When using this resource, cite the original publication and PhysioNet:

```bibtex
@article{Iyengar1996Fantasia,
  title   = {Age-related alterations in the fractal scaling of cardiac interbeat interval dynamics},
  author  = {Iyengar, N. and Peng, C.-K. and Morin, R. and Goldberger, A. L. and Lipsitz, L. A.},
  journal = {American Journal of Physiology},
  volume  = {271},
  pages   = {1078--1084},
  year    = {1996}
}

@misc{fantasia100,
  title        = {Fantasia Database},
  author       = {{PhysioNet}},
  howpublished = {PhysioNet},
  year         = {2003},
  note         = {Version 1.0.0},
  doi          = {10.13026/C2RG61},
  url          = {https://physionet.org/content/fantasia/1.0.0/}
}
```

Also include the current PhysioNet platform citation required on the project page.

## Contact

- **Upstream**: https://physionet.org/content/fantasia/1.0.0/
- **Dataset repository**: https://github.com/biometric-community/MIT-BIH-Fantasia-Database
- **tbiom catalog**: `projects/datasets/fantasia/`
