# OSD-Insight — Final Report  
### Architecture-based investigation of the whole project

**Date:** 2026-11-08  
**Status:** COMPLETE WITH DOCUMENTED LIMITATIONS (closed at P7; P8 not authorized)  
**Framework (historical):** VSC-OSOD — Visibility- and Similarity-Calibrated Open-Set Object Detection  
**Public name:** OSD-Insight  

**Unified guide:** [`PROJECT.md`](PROJECT.md) · **Archived architecture/history markdown:** `archive/markdown_consolidated_20261109/`  
**Related seals:** `experiments/FREEZE.json` · `experiments/phases/p7/P7_TRANSFER_METRICS_INNER.json`

---

## 1. Executive verdict

| Architecture success bar | Required? | Result |
|--------------------------|-----------|--------|
| **Analysis** (baselines + eval + failure modes; honest outcome) | Yes | **REACHED** |
| **Method win** (condition-aware calibrator beats semantic / naive peers without large known-AP loss) | Desired | **NOT REACHED** |
| Optional LVIS transfer (execution) | Optional | **REACHED** (executed; did not reverse the method conclusion) |

**One-line conclusion:** The project delivered what the architecture required for analysis and answered its core hypothesis **negatively**. It did **not** reach a method-claim outcome. Later protocol work (FREEZE → P7) confirmed that result under stronger controls and on LVIS transfer: **Energy** remains the stronger A-OSE operating point; **C2_nor** does not beat E1/Energy.

---

## 2. What the architecture set out to do

### 2.1 Problem and task

Everyday detectors fail by (1) misclassifying unknowns as known, (2) suppressing unknowns, (3) weak boxes on hard instances.  

**Task:** open-set object detection (OSOD) — detect knowns; localize/flag unknowns.  
**Not:** full OWOD, pure image-level OOD, UAV-only systems, COCO-centric pipelines.

### 2.2 Hypothesis (architecture §1)

Unknown decisions improve if they combine:

1. **Semantic compatibility** (prototypes / known-likeness)  
2. **Visual reliability** (scale / occlusion / consistency)  
3. via **condition-aware calibration** (E5)

### 2.3 Success tiers (architecture §1)

| Tier | Meaning |
|------|---------|
| **A — Analysis** | Baseline + metrics + near/far & difficulty analysis; allow honest negative/mixed result |
| **B — Method** | E5 > E2 and E4 on unknowns **without** large known-AP drop |

### 2.4 Contribution boundary (locked)

Claim novelty **only** for condition-aware evidence calibration.  
Do **not** claim novelty for CA-RPN, prototypes, FPN, or naive fusion alone.

### 2.5 Locked design choices

| Choice | Decision |
|--------|----------|
| Primary data | Open Images V7 locked subset (20 known / 5 unk-val / 5 unk-test) |
| Transfer | LVIS optional |
| Detector | torchvision Faster R-CNN ResNet-50-FPN |
| Env | conda `ridoy` only; single GPU `cuda:0` |
| Out of scope | COCO, Detectron2/MMDet switch, SAM-as-core, OWOD incremental |

### 2.6 Research questions

1. Near vs far → localization / confusion?  
2. Scale & occlusion → reliability?  
3. Does condition-aware calibration help without hurting known AP?  
4. Cross-dataset transfer? (optional)

---

## 3. What was built (mapped to architecture)

| Architecture block | Implementation | Location |
|--------------------|----------------|----------|
| Backbone + FRCNN | E1 baseline train | `src/models/`, `checkpoints/e1_baseline.pt` |
| Semantic branch (E2) | ROI prototypes | `experiments/oi/e2_semantic/` |
| Reliability (E3) | scale / consistency signals | `src/models/reliability_branch.py` |
| Naive fuse (E4) | combine baselines | `experiments/oi/e4_combined/` |
| Calibrator (E5) | condition-aware head | `experiments/oi/e5_calibrated/` |
| Classical OSOD baseline | Energy score | `experiments/oi/baseline_energy/` + audit corrections |
| Protocol FREEZE (later) | Stage-B thresholds, gates | `experiments/FREEZE.json` |
| Final-test (OI) | P5 oneshot | `experiments/phases/p5/` |
| Efficiency | P6 | `experiments/phases/p6/` |
| LVIS transfer | P7 federated eval | `experiments/phases/p7/`, `dataset/lvis/` |

Repo layout after closure cleanup: `src/`, `dataset/`, `experiments/`, `reports/` (see `STRUCTURE.md`).

---

## 4. Architecture checklist — expected vs achieved

### 4.1 Analysis requirements (Tier A / N1–N5)

| ID | Architecture expectation | Achieved? | Notes |
|----|--------------------------|-----------|-------|
| N1 | Metric suite (AP, unknown, A-OSE, etc.) | **Yes (with proxies)** | Full ideal suite (AP75, WI, AUROC/OSCR) was partial; A-OSE + known mAP50 used as primary OSOD scores |
| N2 | Scale / occlusion stratified analysis | **Partial → Done under audit** | Architecture noted reliability `vis≡1` limitation; corrected analysis does **not** claim occlusion-aware reliability |
| N3 | Incomplete-annotation matching policy | **Yes** | Documented; federated rules later enforced on LVIS |
| N4 | Classical energy / open-set baseline | **Yes** | Corrected pool under `experiments/oi/audit_corrections/` |
| N5 | Report + limitations | **Yes** | OI final report package + this closure investigation |

**Architecture freeze decision (2026-11-01):** Tier A **FINAL-FROZEN**; method claim **not** supported.

### 4.2 Method go/no-go (architecture §7)

**Rule:** E5 > E2 & E4 on unknowns, known AP retained.

| Evidence | Result |
|----------|--------|
| Unk-test (n=239): E1/E2/E4 recall 0.525 / A-OSE rate 0.525; E5 recall 0.525 / A-OSE 0.515 | Negligible gain |
| Known val mAP50: E1 0.5047 → E5 0.4978 (drop 0.0069) | AP retained at weak thr, but A-OSE barely moves |
| Stronger thr cuts A-OSE but known mAP drops >5% | **Pareto wall** |

**Go/no-go:** **FAILED** (recorded in architecture and `archive (see markdown_consolidated_20261109)`).

### 4.3 Research questions — answers from evidence

| RQ | Architecture intent | Answer from project |
|----|---------------------|---------------------|
| 1 Near vs far | Analyze confusion | **Near unknowns dominate failures** (high semantic overlap) |
| 2 Scale / occlusion | Reliability analysis | **Limited:** locked path did not provide true occlusion-aware `vis`; do not over-claim |
| 3 Calibration helps w/o hurting AP? | Core hypothesis | **No** under current signals |
| 4 LVIS transfer | Optional | **Executed (P7)**; ranking unchanged (Energy best A-OSE) |

### 4.4 Scope discipline

| Rule | Honored? |
|------|----------|
| OI subset lock / no class expansion | Yes |
| No COCO | Yes |
| torchvision only | Yes |
| `unknown_test` not used for tuning | Yes (P5 oneshot after FREEZE) |
| Novelty boundary | Yes — no unjustified method claim at closure |

---

## 5. Later protocol track (R2.2.1) — still architecture-consistent

After Tier A freeze, the project continued under protocol **R2.2.1** with a controlled C2 path (`c2_nor`), FREEZE, and P3a–P7. This did **not** rewrite the architecture’s method bar; it stress-tested related signals with sealed thresholds.

### 5.1 FREEZE Stage-B (unk-val operating points)

Same recall **0.5517** across methods; lower A-OSE is better:

| Method | A-OSE raw | Known mAP50 |
|--------|----------:|------------:|
| **Energy** | **93** | 0.485 |
| E1 | 165 | 0.492 |
| C2_nor | 190 | 0.505 |

Gates: **G1–G3 true, G4 false** → C2-MLP (P3b) correctly **NOT_REQUIRED**.

### 5.2 P5 OI final-test (C2_nor primary)

- Images: **2212**  
- Unk GT: **3260** · localized **912** · recall **0.280**  
- A-OSE raw **487** (A-OSE/GT **0.149**)

### 5.3 P7 LVIS frozen transfer (architecture RQ4)

- Images: **19809** · annotations: **244707** · device: RTX A4000 `cuda:0` · ~24 min  
- Door policy: excluded from unk metrics (D2); flag-semantics limitation documented, not a metric pollution finding  

| Method | A-OSE raw | Recall | Unk GT |
|--------|----------:|-------:|-------:|
| **Energy** | **781** | 0.152 | 10115 |
| E1 | 854 | 0.152 | 10115 |
| C2_nor | 964 | 0.152 | 10115 |

**Transfer does not rescue the method claim.** Energy remains best on A-OSE; C2_nor is worst of the three sealed peers.

### 5.4 Closure state

- P1–P7 complete; P8 **not** authorized  
- Post-execution audit **PASS**  
- Project status: **COMPLETE WITH DOCUMENTED LIMITATIONS**  
- Details: `experiments/ JSON seals + archive/OSD_INSIGHT_P7_PROJECT_CLOSURE_REPORT.md`

---

## 6. Computing used (context, not a success metric)

| Resource | Amount |
|----------|--------|
| GPU | 1× NVIDIA RTX A4000 (`cuda:0`) |
| Documented GPU-hours | ~**1.9 h** sealed (E1+P3a+P5+P6+P7); ~**2–3 h** including earlier OI work |
| Peak model VRAM (logged) | ~0.65–0.93 GB allocated; ~1.4 GB reserved in P6 |
| Disk | ~**25 GB** (mostly LVIS + OI images) |

---

## 7. Limitations (architecture-honest)

1. **Method negative result** — condition-aware calibration as implemented does not clear the architecture’s Tier-B bar.  
2. **Reliability / occlusion** — do not claim occlusion-aware reliability from the locked `vis≡1` path.  
3. **Metric proxies** — some architecture-listed report metrics (full Unknown AP, WI, AUROC/OSCR, AP75) were incomplete vs the ideal table; A-OSE family + known mAP50 carried decisions.  
4. **LVIS federated GT** — non-exhaustive absents imply residual unmeasured FN risk (stated in P7 metrics).  
5. **Door flag semantics** — INNER vs outer `door_excluded` meanings documented; scientific Door exclusion still held.  
6. **RPC cost** — full cutout path ~2× latency vs `c2_nor`; FREEZE dropped RPC (`rpc_decision=drop`).

---

## 8. Final judgment against architecture

### Reached (required / in-scope)

- Built the planned FRCNN + E1–E5 + energy system on the locked OI subset.  
- Completed the **analysis** success tier with an honest decision lock.  
- Answered the core hypothesis: calibration did **not** help enough without trading known AP.  
- Kept scope locks (no COCO, ridoy, contribution boundary).  
- Optionally completed LVIS transfer under FREEZE without breaking seals.

### Not reached (desired method outcome)

- E5 / C2 condition-aware path never became the best unknown operating point.  
- Architecture Tier-B go/no-go remains **failed**; P7 reinforces peer ranking (**Energy < E1 < C2** on A-OSE raw at matched recall).

### Overall

**The project reached its architecture-defined analysis outcome and failed its architecture-defined method outcome — by evidence, not by incomplete work.**  
That is a successful research close relative to the architecture’s Tier A mandate, and an unsuccessful method claim relative to Tier B.

---

## 9. Canonical pointers

| Artifact | Path |
|----------|------|
| Architecture (archived) | `archive/legacy_tier_workspace_20261108/project_architecture.md` |
| Tier A decision | `archive (see markdown_consolidated_20261109)` |
| FREEZE | `experiments/FREEZE.json` |
| P7 metrics | `experiments/phases/p7/P7_TRANSFER_METRICS_INNER.json` |
| P7 consolidated | `experiments/ JSON seals + archive/P7_CONSOLIDATED_RESEARCH_REPORT.md` |
| Project closure | `experiments/ JSON seals + archive/OSD_INSIGHT_P7_PROJECT_CLOSURE_REPORT.md` |
| Door addendum | `experiments/ JSON seals + archive/P7_DOOR_EXCLUDED_FLAG_ADDENDUM.md` |
| Layout | `STRUCTURE.md` · `README.md` |

---

*End of final report.*
