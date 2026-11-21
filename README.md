# TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

> TalentRank predicts historical employee promotion outcomes, ranks employees by predicted probability and explains each ranking, giving HR Talent Management Specialists and top management additional information for human review. It does not make promotion decisions.

**Course:** SAIA 3113 AI Engineering, Faculty of Artificial Intelligence, Universiti Teknologi Malaysia

## Problem Statement

In many organisations, promotion decisions depend mainly on supervisors' judgements, and finding the employees who qualify takes more time and effort as the organisation grows. Machine learning can learn patterns from historical HR records and apply them consistently to every employee. However, a prediction alone does not show HR which recorded factors drove it, and an unchecked model may repeat past bias. TalentRank combines prediction, ranking, explanation and a fairness check in one decision-support workflow.

## Evidence of the Problem

- The current promotion process in most organisations depends on supervisors' judgements, and the larger the company, the more time and effort it takes to identify qualified employees manually. Source: Alqahtani & Almaleh (2022), DSIT 2022, IEEE. https://doi.org/10.1109/DSIT55514.2022.9943959
- AI used for decisions that affect promotion is high-risk under the EU AI Act (Annex III, point 4), which requires transparency, human oversight and bias control. Source: https://ai-act-service-desk.ec.europa.eu/en/node/464

## Initial Related Work / Existing Approaches

- **Alqahtani & Almaleh (2022):** seven classifiers with SMOTE; Gradient Boosting performs best (AUC 79.28%). https://doi.org/10.1109/DSIT55514.2022.9943959 *Limitation:* classification only, with no ranked list or individual explanations. Gender is among the top features, yet no fairness test is done.
- **Bhattacharya et al. (2023):** SMOTE-ENN, XGBoost (95% accuracy), SHAP and LIME. https://doi.org/10.1109/ICACTA58201.2023.10393141 *Limitation:* judged by accuracy; resampling is reported on the whole dataset; SHAP features are presented as the organisation's real reasons; no fairness check; no ranking.
- **Benabou & Touhami (2026):** eight models, SMOTE only inside training folds; Random Forest performs best (96.21% accuracy). https://doi.org/10.4995/ijpme.2026.23364 *Limitation:* global importance only, with no ranking evaluation and no fairness audit (both listed as future work).
- **Bitkuri et al. (2025):** review of ML ranking systems (pointwise, pairwise and listwise; ranking metrics; fairness in hiring). https://doi.org/10.47991/2996-4954/JCETAI-119 *Limitation:* not applied to HR promotion data.

Full list: [docs/references.md](docs/references.md)

## Engineering Gap / Limitation

Studies on this dataset report 94–96% accuracy, only slightly better than the 91.5% from always predicting "not promoted". None evaluates the ranked list HR would review, checks whether explanations are stable, or audits fairness across employee groups. This is an engineering gap, not a claim of scientific novelty.

## Project Objectives

- **O1:** To investigate employee features associated with historical promotion outcomes in a public HR dataset.
- **O2:** To develop explainable machine learning models that predict historical promotion outcomes, rank employees by predicted probability and explain each ranking.
- **O3:** To evaluate the developed models in terms of predictive performance, ranking quality, explanation stability and fairness across employee groups.

## Target Users

- **HR Talent Management Specialists:** review ranked employees and the features influencing each prediction.
- **Top Management:** obtain additional, explained information when reviewing employees for promotion.

Final promotion decisions remain with HR and top management.

## Data Sources

- **S01: Employees Evaluation for Promotion (Kaggle).** 54,808 records × 13 columns, nine departments. https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion
- **S02: WNS Analytics Wizard 2018 HR Analytics challenge (Analytics Vidhya).** Original problem statement and variable definitions. https://datahack.analyticsvidhya.com/contest/wns-analytics-hackathon-2018-1/

Data dictionary, checksums and licence status: [data/README.md](data/README.md). The target `is_promoted` means "recommended for promotion" in a past cycle; it does not measure merit or leadership potential.

## Key Design Decisions

- **Ranking:** pointwise. A classifier predicts each employee's promotion probability and the list is sorted by it. It is evaluated at the top 10% (Precision@10% ≥ 0.25, Lift@10% ≥ 3.0).
- **Evaluation:** PR-AUC (≥ 0.35) and Precision@10%, not accuracy, because only 8.52% of employees were promoted.
- **Explanations:** global and individual explanations, with stability checked across 3 bootstrap resamples (top-5 overlap ≥ 80%).
- **Fairness:** gender and age are not model inputs. They are used only to audit the ranked list (four-fifths ratio ≥ 0.80, recall gap ≤ 10 percentage points).
- **Data pipeline:** batch hybrid. The raw CSV is kept unchanged and the prepared table is stored separately. Missing values are kept and handled inside model training.

## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | System map + project scope | [Observation sheet](docs/week1_observation_sheet.md) · [System map](docs/week1_system_map.jpeg) · [Project scope](docs/week1_project_scope.md) | ✅ |
| W2 | Requirements, evaluation criteria, V1 architecture | [Requirements](docs/week2_requirements.md) · [Evaluation criteria](docs/week2_evaluation_criteria.md) · [Architecture](docs/week2_architecture_v1.png) · [Decisions + traceability](docs/week2_architecture_decisions.md) | ✅ |
| W3 | V1 data pipeline | [Notebook](notebooks/week3_pipeline.ipynb) · [Data inventory](docs/week3_data_inventory.md) · [Pipeline diagram](docs/week3_pipeline_diagram.png) · [Pipeline decisions](docs/week3_pipeline_decisions.md) | ✅ |
| W4 | Data quality and bias audit | [Report](docs/week4_data_quality_report.md) ([PDF](docs/week4_data_quality_report.pdf)) · [Checks notebook](notebooks/week4_data_quality_checks.ipynb) | ✅ **GO WITH CONDITIONS** |
| W5+ | Model development, evaluation, implementation | — | Pending |

## Week 4 Quality Gate: GO WITH CONDITIONS

**Scope:** controlled academic model development on this dataset only.

**Evidence:**
- 54,808 records; 4,668 promoted (8.52%) vs 50,140 not promoted (91.48%), a strong class imbalance.
- V1 (valid label) passed on all records; V2 (valid ranges) found one exception, which was reviewed and kept. There are no duplicates, and 20/20 sampled records match the source.
- Promotion rate by department ranges from 5.10% (Legal) to 10.76% (Technology).
- Missing values in `previous_year_rating` (7.52%, all employees with one year of service), `avg_training_score` (4.67%) and `education` (4.40%) are kept and handled during training.

**Conditions:** (1) gender and age are excluded from model inputs and used only for auditing; (2) missing values are handled inside the training pipeline (fitted on the training split only); (3) a fairness audit is reported with every model result; (4) evaluation uses PR-AUC and Precision@10%, not accuracy; (5) rankings remain decision support.

## How to Run (VS Code or Google Colab)

**VS Code (local):**

1. Install Python 3.10 or later and the VS Code **Python** and **Jupyter** extensions.
2. Open this folder in VS Code, then create and activate a virtual environment:
   ```bash
   python -m venv .venv
   # Windows: .venv\Scripts\activate    macOS/Linux: source .venv/bin/activate
   pip install -r requirements.txt
   ```
3. Open `notebooks/week3_pipeline.ipynb`, select the `.venv` kernel and choose **Run All**. The notebook uses the repository root as its base folder.
4. Check that Cell 9 prints **PASS** and that the prepared table's SHA-256 starts with `1dd337e5`.
5. Open `notebooks/week4_data_quality_checks.ipynb` and choose **Run All**.

**Google Colab:** upload `employee_promotion.csv` to `/content/week3_project/data/raw/` and run all cells. The notebook detects Colab automatically.

## Repository Structure

```text
ai-engineering-talentrank/
├── README.md
├── requirements.txt
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
