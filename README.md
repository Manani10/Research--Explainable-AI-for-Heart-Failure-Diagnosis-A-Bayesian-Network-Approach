# Explainable AI for Heart Failure Diagnosis: A Bayesian Network Approach

MSc research code — Sinethemba Nani (University of the Witwatersrand, School of Computer Science and Applied Mathematics).
Supervisor: Dr. Ritesh Ajoodha · Co-supervisor: Jonathan Kariv

This repository contains the final, unified experimental pipeline for comparing a learned Bayesian Network (BN) against five standard machine-learning classifiers for heart-failure diagnosis on MIMIC-IV, and for evaluating the BN's explainability capabilities.

## Research questions

| Notebook | Research question |
|---|---|
| `notebooks/01_RQ1_forward_diagnosis_prediction.ipynb` | **RQ1 — Forward diagnosis prediction.** Logistic Regression, Random Forest, SVM, XGBoost and MLP vs a Bayesian Network, using a patient-level 70/15/15 split. Reports precision, recall, F1, AUROC, AUPRC, Brier score and calibration (bootstrap 95% CIs), DeLong tests with Holm correction, and BN sensitivity analyses. |
| `notebooks/02_RQ2_reverse_symptom_inference.ipynb` | **RQ2 — Reverse symptom inference.** Marginal posteriors P(Sᵢ \| HF=1), comparison against held-out test frequencies (MAD), held-out log-likelihood (vs a marginals-only baseline), top-K joint profiles, Jaccard profile similarity, bootstrap CIs, multi-evidence inference and the clinical rater template. |
| `notebooks/03_RQ3_evidence_value_EIG.ipynb` | **RQ3 — Evidence-value analysis.** Expected information gain (EIG) for uncertain predictions (0.40–0.60, with 0.35–0.65 and 0.45–0.55 sensitivity bands), EIG vs SHAP rank correlation, band stability and actionability at 0.75 / 0.80 / 0.85. |

## Run order

The notebooks must be run in order — each one reads artifacts saved by the previous one:

1. **RQ1** builds the patient-level split, preprocessors, baseline models and the final BN (`models/FINAL_bayesian_network.pkl`, `data/X_*_bn.pkl`, `predictions/FINAL_BN_test_prob.npy`, `results/FINAL_RQ1_winning_grids.csv`).
2. **RQ2** loads the final BN and the BN-ready splits.
3. **RQ3** loads the final BN, BN test probabilities, classifier splits and the RQ1 winning grids.

## Environment

The notebooks are written for **Google Colab** with Google Drive mounted. All artifacts are saved to:

```
/content/drive/MyDrive/heart_failure_bn_FINAL/
├── checkpoints/   # resumable progress (grid results, BN bootstrap edges, EIG cases)
├── data/          # raw cohort + train/val/test splits
├── models/        # trained classifiers and the final BN
├── predictions/   # test-set probabilities
├── results/       # all final CSV tables
├── figures/       # calibration plots
└── templates/     # clinical rater template
```

Long-running steps (grid search, BN bootstrap structure learning, BN inference, EIG) are checkpointed to Drive, so a Colab restart resumes completed work instead of retraining.

Python dependencies are listed in `requirements.txt` (Colab provides most of them; the notebooks install `pgmpy`, `xgboost`, `statsmodels` and `shap` themselves).

## Data access

This project uses **MIMIC-IV**, which is credentialed-access data distributed by PhysioNet. **No MIMIC-IV data is included in this repository**, and none should be committed. To reproduce the results you need your own approved PhysioNet credentials and data use agreement.

The raw cohort is queried from a BigQuery table (`research-heart-failure.Research.master_dataset_fixed`) built from MIMIC-IV; on first run the RQ1 notebook caches it to `data/raw_master_dataset.pkl` on Drive.

## Outcome definition

The outcome is **heart failure** (ICD-9 428.x and ICD-10 I50.x), not specifically acute heart failure. After reviewing the positive-cohort code distribution (acute, chronic, acute-on-chronic and unspecified codes are all present), the terminology was changed to "heart failure" throughout the pipeline. See RQ1, section 2.
