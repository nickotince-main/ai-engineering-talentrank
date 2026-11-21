# Week 3 Data Inventory — TalentRank

**Project:** TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

**Version:** 1.0 · Notebook: `notebooks/week3_pipeline.ipynb`

## 1. From Topic 2 Requirements to Data Needs (Step A1)

| Requirement ID | Needed information / feature | Candidate source | Why usable / constraint |
|---|---|---|---|
| FR3, NFR1, NFR5 | Employee features + historical promotion label (`is_promoted`) | S01 `employee_promotion.csv` | Public, labelled, 54,808 records across nine departments. Label is a past recommendation, not leadership potential. |
| FR1, FR2 | Definitions of each column and the original problem | S02 WNS 2018 challenge page | Gives the variable definitions used in `data/README.md`. |
| RAI1 | Gender and age to audit fairness (not model inputs) | S01, audit subset | Present in the data; gender has only two recorded values. |
| RAI2 | Source identity, owner, licence | S01 Kaggle page + S02 | Licence listed as "Unknown"; original collecting organisation not named. |

## 2. Data Inventory (Step A4)

| Source ID | Filename / URL | Type and purpose | Version / date / owner | Access / rights | Inspection evidence |
|---|---|---|---|---|---|
| S01 | `data/raw/employee_promotion.csv` — https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion | Structured CSV; training and evaluation data for prediction, ranking and explanation (FR1–FR5) | Kaggle upload (account `muhammadimran112233`); version and collection period **not documented (N.V.)**; SHA-256 `9e1c9c62…fdaf` | Public download; licence "Unknown" (**N.V.**); used for academic coursework only | Loaded with pandas: 54,808 × 13. Inspected two subsets (promoted vs not promoted; records with missing values) — notebook Step A3. |
| S02 | WNS Analytics Wizard 2018 HR Analytics challenge — https://datahack.analyticsvidhya.com/contest/wns-analytics-hackathon-2018-1/ | Documentation: problem statement and variable definitions (FR1, FR2, RAI2) | Analytics Vidhya, 2018 | Public web page | Confirms the 54,808-record training set and column definitions; original release also had `KPIs_met >80%`, not present in S01. |

## 3. Dataset Structure (Step A3)

| Item | Result |
|---|---|
| Format | CSV with header row |
| Records / columns | 54,808 / 13 |
| Inspection unit | One employee record (`employee_id` unique: 54,808 of 54,808) |
| Data types | 8 numeric (`employee_id`, `no_of_trainings`, `age`, `previous_year_rating`, `length_of_service`, `awards_won`, `avg_training_score`, `is_promoted`); 5 text (`department`, `region`, `education`, `gender`, `recruitment_channel`) |
| Target | `is_promoted` — recommended for promotion (1) or not (0) |
| Date fields | None — no evaluation or promotion date is recorded |
| Exact duplicate rows | 0 |
| Data dictionary | `data/README.md` |

## 4. Initial Observations

**Promotion outcome**

| Outcome | Records | Percent |
|---|---:|---:|
| Not promoted (0) | 50,140 | 91.48% |
| Promoted (1) | 4,668 | 8.52% |
| Total | 54,808 | 100% |

**Missing values**

| Column | Missing | Percent | Observation |
|---|---:|---:|---|
| `previous_year_rating` | 4,124 | 7.52% | All 4,124 belong to employees with `length_of_service` = 1 — they have no previous year to rate. |
| `avg_training_score` | 2,560 | 4.67% | Reason not documented. |
| `education` | 2,409 | 4.40% | Reason not documented. |
| `is_promoted` | 0 | 0% | — |

## 5. Storage

| Data | Location |
|---|---|
| Raw (unchanged) | `data/raw/employee_promotion.csv` |
| Part B prepared output | `data/processed/prepared_table.csv` |
| Part B lineage manifest | `data/week3_manifest.json` |
| Part C pipeline output + run record | `data/pipeline_v1/tabular/prepared_table.csv`, `pipeline_manifest.json` |

## 6. Known Limitations

1. Target is imbalanced (8.52% promoted).
2. Missing values in three columns; only the `previous_year_rating` gap has a clear explanation.
3. No date fields; collection period and dataset version are not documented.
4. Licence and original data owner are not verified.
5. The label is a historical recommendation for promotion, not a measure of leadership potential.
