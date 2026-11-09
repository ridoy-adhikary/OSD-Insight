# OSD-Insight - Project Guide

Single reference for setup, layout, data, protocol, and sealed results. Detailed
architecture investigation is documented in [FINAL_REPORT.md](FINAL_REPORT.md).

**Remote:** `git@github.com:ridoy-adhikary/OSD-Insight.git`  
**Status:** closed at P7 - complete with documented limitations; P8 was not authorized

## 1. Environment

Use the project environment only:

```bash
source /home/sda/Anaconda/etc/profile.d/conda.sh
conda activate ridoy
which python
export PYTHONPATH=src:$PYTHONPATH
# or: pip install -r requirements.txt
```

Do not use `base`, `dl`, or other shared environments.

## 2. Repository layout

```text
OSD-Insight/
|-- README.md, requirements.txt, .gitignore
|-- docs/
|   |-- PROJECT.md
|   |-- FINAL_REPORT.md
|   `-- assets/                 # README figures
|-- src/                        # models, datasets, training, evaluation
|-- configs/
|-- dataset/                    # OI, LVIS, metadata, and splits
|-- experiments/                # FREEZE, OI, and phase result seals
|-- reports/                    # phase, closure, and OI reports
|-- checkpoints/               # JSON metadata; model weights are ignored
|-- results/
|-- scripts/
`-- tools/gated_auto/           # controller code only
```

Large images, model weights, and historical archives are not committed.

## 3. Problem and pipeline

**Task:** open-set object detection - detect known objects and localize or flag
unknown objects.

**Pipeline:** Faster R-CNN R50-FPN -> semantic and reliability signals ->
condition-aware calibration (E5/C2_nor) -> comparison with Energy -> FREEZE ->
OI final test (P5) -> efficiency (P6) -> LVIS transfer (P7).

The novelty boundary is condition-aware evidence calibration, not CA-RPN,
prototypes, FPN, or naive score fusion alone. The method bar was not met:
Energy was stronger on A-OSE.

## 4. Datasets

| Role | Path | Notes |
|---|---|---|
| Primary | `dataset/open_images_v7/` | 20 known, 5 unknown-validation, and 5 unknown-test classes |
| Additional OI unknowns | `dataset/open_images_v7_p1/` | Used in later phases |
| Transfer | `dataset/lvis/` | P7 evaluation set contains 19,809 images |

Do not use COCO or expand the Open Images class caps without an explicit
protocol unlock. Full local datasets are not included in Git.

## 5. Protocol (R2.2.1)

- Stage A (unknown development) sets provisional thresholds and gates.
- Stage B (unknown validation) sets final thresholds, followed by FREEZE.
- Gates G1-G3 passed; G4 failed, so C2-MLP (P3b) was not required.
- `c2_method = c2_nor`; perturbation consistency (RPC) was dropped.
- P5 is a one-shot evaluation after FREEZE.
- P7 uses frozen transfer without LVIS recalibration.
- Door is excluded from LVIS unknown metrics (D2).

Full historical protocol text is under the consolidated archive.

## 6. Sealed results

| Artifact | Path |
|---|---|
| FREEZE | `experiments/FREEZE.json` |
| P7 transfer metrics | `experiments/phases/p7/P7_TRANSFER_METRICS_INNER.json` |
| P7 execution summary | `experiments/phases/p7/P7_EXECUTION_SUMMARY.json` |
| P7 reporting seal | `reports/phases/P7_REPORTING_SEAL.json` |
| P5 final-test metrics | `experiments/phases/p5/P5_FINAL_TEST_METRICS.json` |
| P6 efficiency | `experiments/phases/p6/P6_EFFICIENCY_METRICS.json` |
| Architecture verdict | `docs/FINAL_REPORT.md` |

**Headline (P7 raw A-OSE at the same recall):** Energy 781 < E1 854 < C2_nor 964.

## 7. Compute

- GPU: 1 x RTX A4000 (`cuda:0`)
- Documented sealed GPU time: approximately 1.9 hours
- Approximate disk usage with images: 25 GB

## 8. Path migration

| Old path | New path |
|---|---|
| `data/` | `dataset/` |
| `dataset/open_images_v7` alias | `dataset/open_images_v7/` |
| `research_tier_b/datasets/lvis_p7/` | `dataset/lvis/` |
| `research_tier_b/FREEZE.json` | `experiments/FREEZE.json` |
| `research_tier_b/experiments/p*` | `experiments/phases/p*` |
| `research_tier_b/implementation/` | `src/implementation/` |
| `outputs/checkpoints/` | `checkpoints/` |
| `ref_paper/` | `papers/` |

All project datasets use the single `dataset/` root.

## 9. Contributors

| Person | Role | Planned commits |
|---|---|---:|
| Rafi | System and training | 10 |
| Ridoy | Open-set method and metrics | 10 |
| Fahim | Transfer, efficiency and delivery | 10 |
