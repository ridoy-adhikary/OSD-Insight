# OSD-Insight: Open-Set Detection - Experimental Insight and Analysis

**Deep Learning Course Project**

OSD-Insight investigates the challenges of Open-Set Object Detection (OSOD), focusing on how object detectors can distinguish known objects from previously unseen categories.

The project explores adaptive unknown-object rejection by combining detection confidence and semantic uncertainty. It also investigates whether perturbation consistency can provide additional evidence for identifying unknown objects.

> **Research status:** Preliminary experiments (P1–P3a) completed. Sensitivity analysis, final-test evaluation, efficiency evaluation, and LVIS transfer evaluation are planned or pending.

---

## 1. Research Objectives

Conventional object detectors are primarily designed to recognize predefined categories. In real-world environments, however, they may encounter objects belonging to categories that were not included during training.

This project investigates:

- How effectively existing detection signals distinguish known and unknown objects.
- Whether semantic uncertainty improves unknown-object rejection.
- Whether perturbation consistency provides useful complementary information.
- How rejection performance changes when detection quality and computational cost are considered together.

## 2. Methodology

The project uses a frozen Faster R-CNN detector with a ResNet-50 Feature Pyramid Network (FPN) backbone as the underlying detection architecture.

The experimental framework investigates several rejection strategies:

| Method | Description |
|---|---|
| E1 | Detection-confidence-based rejection |
| Energy | Energy-based unknown-object scoring |
| C1 | Semantic uncertainty-based rejection |
| C2 / AUOR | Adaptive rejection using detection confidence and semantic uncertainty |
| RPC | Per-detection perturbation consistency, evaluated as an additional signal |

### Adaptive Unknown-Object Rejection

The proposed C2 approach investigates combining detection confidence with semantic uncertainty.

Its general decision rule is:

\[
\text{Reject} =
(d < \tau_d)
\lor
(u_{\text{sem}} < \tau_s \land rpc < \tau_r)
\]

Where:

- \(d\): Detection confidence.
- \(u_{\text{sem}}\): Semantic uncertainty.
- \(rpc\): Perturbation consistency score.
- \(\tau_d,\tau_s,\tau_r\): Rejection thresholds.

**Important experimental finding:** Preliminary experiments did not establish a benefit from adding RPC. The primary C2 configuration was therefore simplified to a no-RPC variant. This is an empirical design decision, not a claim that perturbation consistency is universally ineffective.

## 3. Experimental Setup

| Component | Configuration |
|---|---|
| Detection architecture | Faster R-CNN |
| Backbone | ResNet-50 + FPN |
| Development dataset | Open Images V7 |
| Transfer evaluation | LVIS (planned) |
| Detection framework | PyTorch / Torchvision |
| Evaluation focus | Unknown-object rejection and detection quality |
| Experimental design | Controlled comparisons and ablation studies |

The research follows a staged experimental protocol, separating development data, validation evaluation, final-test evaluation, and cross-dataset transfer.

## 4. Preliminary Experimental Findings

The initial P3a experiments compared detection-confidence, energy-based, and adaptive rejection strategies.

The following are preliminary results from the Open Images V7 unknown-validation evaluation:

| Method | A-OSE ↓ | AP ↑ |
|---|---:|---:|
| E1 | 165 | 0.492 |
| Energy | **93** | 0.485 |
| C2 (no RPC) | 190 | **0.505** |

*Lower A-OSE indicates fewer unknown objects incorrectly classified as known. Higher AP indicates better detection quality.*

### Initial Observations

1. **Energy-based rejection:** Achieved the lowest A-OSE among the three evaluated configurations.
2. **Adaptive rejection:** The C2 no-RPC configuration achieved the highest AP but did not outperform the Energy baseline in A-OSE.
3. **Perturbation consistency:** Its additional computational cost and preliminary performance trade-off did not justify retaining it in the primary C2 configuration.
4. **Further validation:** These findings are preliminary and require the remaining protocol-governed experiments before broader conclusions can be drawn.

These results do not establish that the proposed adaptive method outperforms existing baselines.

## 5. Research Workflow

The project is organized into the following phases:

| Phase | Description | Status |
|---|---|---|
| P1 | Open Images V7 data preparation | Complete |
| P2 | Experimental framework implementation | Complete |
| P3a | Preliminary experiments and ablations | Complete |
| P3b | Conditional MLP extension | Not required under current protocol |
| P4 | Sensitivity analysis and FREEZE assembly | Pending |
| P5 | One-shot final-test evaluation | Pending |
| P6 | Computational efficiency evaluation | Pending |
| P7 | Frozen transfer evaluation on LVIS | Pending |

The project uses separate authorization and validation gates for final-test and LVIS evaluations to preserve experimental integrity.

## 6. Evaluation Metrics

The experimental analysis considers:

- **A-OSE:** Measures unknown-object classification errors.
- **Average Precision (AP):** Evaluates detection quality.
- **AUROC:** Evaluates the ability of scores to distinguish known and unknown detections where applicable.
- **Computational efficiency:** Measures inference overhead and runtime.
- **Ablation analysis:** Examines the contribution of individual rejection components.

## 7. Technologies

- Python
- PyTorch
- Torchvision
- Faster R-CNN
- Open Images V7
- LVIS
- NumPy
- Computer Vision
- Deep Learning
- Open-Set Recognition

## 8. Current Limitations

- Preliminary results are limited to the completed Open Images V7 experiments.
- The proposed C2 approach has not demonstrated superior A-OSE performance over the Energy baseline.
- Perturbation consistency was excluded from the primary configuration following preliminary evaluation.
- Final-test evaluation has not yet been performed.
- Cross-dataset transfer to LVIS has not yet been completed.
- Generalization to other detection architectures and datasets remains to be investigated.

## 9. Future Work

The next stages of this research will focus on:

1. Completing sensitivity analysis and freezing the experimental configuration.
2. Conducting the authorized one-shot final-test evaluation.
3. Measuring computational efficiency.
4. Evaluating frozen transfer performance on LVIS.
5. Consolidating the findings into a reproducible research report.

## 10. Research Status

OSD-Insight is an ongoing empirical research project. Its goal is to understand the strengths and limitations of different unknown-object rejection strategies under controlled experimental conditions, rather than to claim an improvement without sufficient evidence.

---

## 👥 Team Members

This research project is conducted by:

- **Ridoy Kumar Adhikary**
- **Iftekhar Alam Fahim**
- **Rafi Ahmed**
**Institution:** Sichuan University  
**Research Area:** Open-Set Object Detection · Computer Vision · Deep Learning · Trustworthy AI

*This repository and its conclusions are subject to ongoing experimental validation.*
