# GlucoWatch — CGM Real-World Evidence Analytics Pipeline
![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-F7931E?style=flat-square&logo=scikit-learn&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?style=flat-square&logo=powerbi&logoColor=black)
![Pandas](https://img.shields.io/badge/Pandas-Data-150458?style=flat-square&logo=pandas&logoColor=white)
![Healthcare](https://img.shields.io/badge/Domain-Healthcare%20Analytics-0C6B3C?style=flat-square)
![RWE](https://img.shields.io/badge/Type-Real--World%20Evidence-8A2BE2?style=flat-square)

# An end-to-end clinical analytics pipeline for CGM-based diabetes cohort analysis, predictive modelling, and automated ML drift monitoring — grounded in published RWE literature.
## About
GlucoWatch mirrors the real-world evidence (RWE) workflows used in clinical data science teams. Starting from a literature review of published CGM studies, the pipeline moves through systematic data cleaning, patient cohort profiling, predictive modelling, and automated drift detection — culminating in a 4-page interactive Power BI dashboard built for non-technical clinical stakeholders.

**Why this matters:** Abbott's FreeStyle Libre CGM is the world's leading continuous glucose monitor. This project directly replicates population-level diabetes analytics, post-market surveillance thinking, and ML model monitoring work performed by GDSA teams supporting real-world device and therapeutic studies.

## Pipeline Overview
```
  Data Cleaning → Cohort EDA → Baseline Model → Drift Monitor → Power BI Dashboard
     (Phase 1)     (Phase 2)      (Phase 3)       (Phase 4)          (Phase 5)
```
## Model Performance

| Metric | Score |
| AUC (ROC) | **~0.82** |
| F1 Score | ~0.76 |
| Precision | ~0.79 |
| Recall | ~0.74 |

> Logistic Regression baseline · 5-fold stratified CV · `class_weight=balanced` · n=768

## Pipeline Phases

### Phase 1 — Data Cleaning Pipeline
- Clinical zero-value resolution across 5 columns (`Glucose`, `BloodPressure`, `SkinThickness`, `Insulin`, `BMI`)
- Cohort-stratified median imputation by Outcome group — not naive mean imputation
- IQR-based outlier flagging only — not dropping (clinical data best practice)
- 4 literature-justified engineered features:
  - `InsulinResistance_Proxy` — HOMA-IR approximation
  - `BMI_Category` — WHO classification thresholds
  - `Glycemic_Risk` — Normal / Pre-diabetic / Diabetic-range
  - `Age_Group` — cohort bands for population-level analysis

### Phase 2 — Exploratory Cohort Analysis
- 4-tier risk cohort segmentation (High / Moderate / Borderline / Low Risk)
- Glycemic distribution analysis across age bands via violin plots
- Annotated correlation heatmap with clinically significant pair callouts
- Stakeholder-readable EDA key findings summary (dynamically generated from data)

### Phase 3 — Baseline Predictive Model
- Logistic Regression with literature-justified feature selection only
- 5-fold stratified cross-validation — no data leakage
- Full evaluation suite: AUC, F1, Precision, Recall, Confusion Matrix, ROC Curve
- Feature coefficient importance chart
- Baseline metrics seeded to `monitoring_log.csv` for Phase 5 reference

### Phase 4 — ML Monitoring & Drift Detection
- 6 simulated monthly data windows via stratified sampling
- Per-window model retraining and metric logging
- PSI-based feature drift detection on Glucose, BMI, Age:

| PSI Range | Status | Action |
|---|---|---|
| < 0.10 | 🟢 Stable | No action needed |
| 0.10 – 0.20 | 🟡 Watch | Monitor closely |
| > 0.20 | 🔴 Alert | Trigger model review |

- AUC drop alerting — flags if degradation exceeds 5% from baseline
- Auto-generated one-page drift alert report per monitoring cycle
- 4-panel monitoring chart: AUC trend, F1 trend, PSI heatmap, PSI trend lines

### Phase 5 — Power BI Dashboard
- **Page 1 · Cohort Overview** — KPI cards, diabetic split donut, risk cohort breakdown
- **Page 2 · Clinical EDA** — Glucose vs BMI scatter, glycemic age profiles, BMI category table
- **Page 3 · Model Performance** — AUC/F1 trend lines, baseline vs latest KPI, all-metrics chart
- **Page 4 · Drift Monitor** — PSI heatmap with conditional formatting, drift trends, alert log
- Risk Cohort slicer synced across all 4 pages for self-serve stakeholder access

---

## 📁 Repository Structure
```
glucowatch/
├── notebooks/
│   ├── GlucoWatch.ipynb
├── data/
│   ├── glucowatch_cleaned.csv
│   ├── glucowatch_phase3_final.csv
│   ├── monitoring_log.csv
│   └── glucowatch_drift_report.csv
├── visuals/
│   ├── phase1_cleaning_qa.png
│   ├── phase2_cohort_segmentation.png
│   ├── phase2_glycemic_age_analysis.png
│   ├── phase2_correlation_heatmap.png
│   ├── phase3_model_evaluation.png
│   └── phase4_monitoring_dashboard.png
├── GlucoWatch_Dashboard_AaryanJoshi.pbix
├── requirements.txt
└── README.md
```

---
