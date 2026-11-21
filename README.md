# TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

> TalentRank predicts historical employee promotion outcomes, ranks employees by predicted probability and explains each ranking, giving HR Talent Management Specialists and top management additional information for human review. It does not make promotion decisions.

**Course:** SAIA 3113 AI Engineering — Faculty of Artificial Intelligence, Universiti Teknologi Malaysia

## Problem Statement

Promotion decisions in many organisations depend mainly on supervisors' judgements, and identifying employees who qualify for promotion becomes slower as an organisation grows. Machine learning can learn patterns from historical HR records, but a prediction alone does not show which recorded factors drove it, and an unchecked model may repeat past bias. TalentRank combines prediction, ranking, explanation and a fairness check in one decision-support workflow.

## Evidence of the Problem

- Promotion processes depend on supervisors' judgements, and manual identification of qualified employees takes more time as companies grow — Alqahtani & Almaleh (2022), DSIT 2022, IEEE. https://doi.org/10.1109/DSIT55514.2022.9943959
- AI that affects promotion decisions is high-risk under the EU AI Act (Annex III, point 4), requiring transparency, human oversight and bias control. https://ai-act-service-desk.ec.europa.eu/en/node/464

## Initial Related Work / Existing Approaches

- **Alqahtani & Almaleh (2022)** — seven classifiers with SMOTE; Gradient Boosting best (AUC 0.79). https://doi.org/10.1109/DSIT55514.2022.9943959 — *Limitation:* classification only; no ranking or individual explanation; no fairness analysis.
- **Bhattacharya et al. (2023)** — XGBoost with LIME and SHAP explanations. https://doi.org/10.1109/ICACTA58201.2023.10393141 — *Limitation:* accuracy-based; treats explanations as decision reasons; no ranking or fairness check.
- **Benabou & Touhami (2026)** — eight models, SMOTE inside cross-validation. https://doi.org/10.4995/ijpme.2026.23364 — *Limitation:* global importance only; fairness left to future work.
- **Bitkuri et al. (2025)** — review of ML ranking systems and ranking metrics. https://doi.org/10.47991/2996-4954/JCETAI-119 — *Limitation:* not applied to HR promotion.

Full list: [docs/references.md](docs/references.md)

## Engineering Gap

Published studies on this dataset report 94–96% accuracy, close to the 91.5% from always predicting "not promoted". None evaluates the ranked list HR would review, checks explanation stability, or audits fairness across employee groups.

## Project Objectives

- **O1:** To investigate employee features associated with historical promotion outcomes in a public HR dataset.
- **O2:** To develop explainable machine learning models that predict historical promotion outcomes, rank employees by predicted probability and explain each ranking.
- **O3:** To evaluate the developed models in terms of predictive performance, ranking quality, explanation stability and fairness across employee groups.

## Target Users

- **HR Talent Management Specialists** — review ranked employees and the features influencing each prediction.
- **Top Management** — additional, explained information when reviewing employees for promotion.

## Data Sources

- **S01 — Employees Evaluation for Promotion (Kaggle):** 54,808 records × 13 columns, all nine departments. https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion
- **S02 — WNS Analytics Wizard 2018 HR Analytics challenge (Analytics Vidhya):** original problem statement and variable definitions. https://datahack.analyticsvidhya.com/contest/wns-analytics-hackathon-2018-1/

Data dictionary, checksums and licence status: [data/README.md](data/README.md)

## Key Design Decisions

- **Ranking:** pointwise — a classifier predicts each employee's promotion probability and the list is sorted by it; evaluated at the top 10% (Precision@10% ≥ 0.25).
- **Evaluation:** PR-AUC and Precision@10%, not accuracy (the target is 8.52% positive).
- **Fairness:** gender and age are not model inputs; they are used only to audit the ranked list (four-fifths ratio ≥ 0.80).
- **Data pipeline:** batch hybrid — the raw CSV is kept unchanged and the prepared table is stored separately; missing values are kept and handled inside model training.

## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | System map + project scope | [Observation sheet](docs/week1_observation_sheet.md) · [System map](docs/week1_system_map.jpeg) · [Project scope](docs/week1_project_scope.md) | ✅ |
| W2 | Requirements, evaluation criteria, V1 architecture | [Requirements](docs/week2_requirements.md) · [Evaluation criteria](docs/week2_evaluation_criteria.md) · [Architecture](docs/week2_architecture_v1.png) · [Decisions + traceability](docs/week2_architecture_decisions.md) | ✅ |
| W3 | V1 data pipeline | [Notebook](notebooks/week3_pipeline.ipynb) · [Data inventory](docs/week3_data_inventory.md) · [Pipeline diagram](docs/week3_pipeline_diagram.png) · [Pipeline decisions](docs/week3_pipeline_decisions.md) | ✅ |
| W4 | Data quality and bias audit | [Report](docs/week4_data_quality_report.md) ([PDF](docs/week4_data_quality_report.pdf)) · [Checks notebook](notebooks/week4_data_quality_checks.ipynb) | ✅ **GO WITH CONDITIONS** |
| W5+ | Model development, evaluation, implementation | — | Pending |

## Week 4 Quality Gate — GO WITH CONDITIONS

- 54,808 records; 4,668 promoted (8.52%) vs 50,140 not promoted (91.48%) — strong class imbalance.
- Promotion rate by department ranges from 5.10% (Legal) to 10.76% (Technology).
- Missing values in `previous_year_rating` (7.52%), `avg_training_score` (4.67%) and `education` (4.40%); kept and handled during training.
- Timeliness, licence and consent are N.V. (no dates or collection period documented).
- Historical age pattern (ratio 0.72) and department-specific training-score scales require fairness controls.

## Repository Structure

```text
ai-engineering-talentrank/
├── README.md
├── docs/
│   ├── week1_observation_sheet.md      # group activity
│   ├── week1_system_map.jpeg           # group activity
│   ├── week1_project_scope.md
│   ├── week2_requirements.md
│   ├── week2_evaluation_criteria.md
│   ├── week2_architecture_v1.png
│   ├── week2_architecture_decisions.md
│   ├── week3_data_inventory.md
│   ├── week3_pipeline_diagram.png
│   ├── week3_pipeline_decisions.md
│   ├── week4_data_quality_report.md
│   ├── week4_data_quality_report.pdf
│   └── references.md
├── data/
│   ├── README.md
│   ├── raw/employee_promotion.csv
│   ├── processed/prepared_table.csv
│   ├── week3_manifest.json
│   └── pipeline_v1/tabular/
│       ├── prepared_table.csv
│       └── pipeline_manifest.json
├── notebooks/
│   ├── week3_pipeline.ipynb
│   └── week4_data_quality_checks.ipynb
├── src/
├── evaluation/
└── prompts/
```
