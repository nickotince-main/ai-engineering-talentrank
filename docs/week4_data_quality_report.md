# Week 4 — Data Quality & Bias Report: TalentRank

**Project:** TalentRank — Explainable Machine Learning for Employee Promotion Prediction and Ranking  
**Evidence:** `notebooks/week4_data_quality_checks.ipynb` (all figures below), `notebooks/week3_pipeline.ipynb`  
**Labels:** **Observed** = measured in the data · **Inferred risk** = plausible consequence, not proven · **N.V.** = Not Verified

---

## 1. Project and Data Overview

| Field | Response / evidence |
|---|---|
| Project title and purpose | TalentRank predicts historical promotion outcomes, ranks employees by predicted probability and explains each ranking for HR Talent Management Specialists and top management. It does not make promotion decisions. |
| Two Topic 2 requirements | **FR3 / NFR1** — prediction needs a valid target (`is_promoted`) and usable input features. **RAI1** — the ranked list must not select any gender or age group at a substantially lower rate. |
| Topic 3 pathway and data type | Pathway B — structured CSV (tabular). |
| Audited output and manifest | Topic 3 **Part C** output `data/pipeline_v1/tabular/prepared_table.csv`, traced through `data/pipeline_v1/tabular/pipeline_manifest.json` (SHA-256 `1dd337e5…` re-verified). The raw file `data/raw/employee_promotion.csv` is used for before/after-cleaning comparison. |
| Source and access status | S01 Kaggle "Employees Evaluation for Promotion" (original release: WNS Analytics Wizard 2018). Version, collection period and licence: **N.V.** |
| Audit population and sample | Population: 54,808 employee records across 9 departments (unit = one employee record). Full-population checks for counts, missing values, rules and coverage. Sample: **20 records** — 14 random, stratified by outcome (7 promoted, 7 not; seed 42) + 6 known problem cases (2 each with missing education, rating, training score). Two departments selected for detailed review (Section 4). |

## 2. Five-Dimension Quality Scorecard

| Dimension | Finding | Evidence | Impact on task | Decision |
|---|---|---|---|---|
| Completeness | **Observed:** 9,093 missing cells in 3 columns — `previous_year_rating` 4,124 (7.52%), `avg_training_score` 2,560 (4.67%), `education` 2,409 (4.40%). `is_promoted` has 0 missing values. Counts are identical before and after cleaning. | Notebook §4 | Models must handle missing values; dropping rows would remove most new employees. | **Warning** |
| Consistency | **Observed:** categorical values are consistent (9 departments, 3 education levels, 2 gender codes). `avg_training_score` is not on a common scale: department means range from 50.2 (HR) to 84.6 (Analytics, R&D). | Notebook §7 | Raw training scores are not comparable across departments, which can distort ranking. | **Review** |
| Accuracy | **Observed:** 20 / 20 sampled prepared records match the raw file, so preparation is faithful. Whether the raw file matches the real organisation's records: **N.V.** (no authoritative reference). | Notebook §5 | Pipeline added no errors; source truth cannot be confirmed. | **Pass** (pipeline) / **N.V.** (source) |
| Timeliness | **N.V. (Not Verified).** The dataset has no date field (no evaluation or promotion date). The documentation defines `previous_year_rating` as "Employee Rating for the previous year", but the reference year and data collection period are not specified, and the dataset version could not be confirmed. As a result, it cannot be determined whether the promotion records are recent enough for the proposed employee ranking system. | `data/README.md`; S01 and S02 pages | Patterns may reflect an older promotion cycle. | **N.V.** |
| Uniqueness | **Observed:** 0 exact duplicate rows; 0 duplicate `employee_id` across 54,808 records. | Week 3 Step A3 | No double counting in training or evaluation. | **Pass** |

**How missing values were handled (Topic 3) and why:**

| Column | Reason for missing values | Handling |
|---|---|---|
| `is_promoted` | None missing. If any were missing, they would be left as missing, because the outcome is unknown. | — |
| `previous_year_rating` | **Observed:** all 4,124 missing ratings belong to employees with 1 year of service — they have no completed previous year to rate. 423 one-year employees do have a rating; the dataset does not explain why. | Kept. Dropping rows would remove 92% of one-year employees (Topic 3 comparison). Imputation or a missing-value indicator will be fitted on the training split only. |
| `avg_training_score` | Not documented. **Observed:** missing only in some departments (Finance 12.4%, Operations 12.3%, Legal 11.8%, Sales & Marketing 3.8%, HR 3.7%; 0% elsewhere). | Kept; handled in training as above. |
| `education` | Not documented. **Observed:** promotion rate is lower when education is missing (5.06% vs 8.68%), so missingness carries information. | Kept; treated as a separate "missing" category in training. |

## 3. Validation Findings

| | V1 | V2 |
|---|---|---|
| Quality expectation | Every record has a valid outcome label — **FR3 / NFR1** | Values lie in a valid range so analysis and explanations are not distorted — **FR2 / NFR1** |
| Observable property | `is_promoted` | `previous_year_rating`, `avg_training_score`, `length_of_service`, `age` |
| Rule | Present and ∈ {0, 1} | Rating ∈ {1..5}, or missing only when service = 1; score ∈ [0, 100]; service ≤ age − 18 |
| Items tested | 54,808 (all) | 54,808 (all) |
| Result | 54,808 pass / 0 fail | 54,807 pass / **1 fail** — Operations employee, age 30, 13 years of service (would have started at 17) |
| Action | Hard fail / quarantine if any row fails; none did | **Manual review, keep.** Starting work at 17 is possible, so this is an exception, not a defect. Kept with a flag; ID not shown. |

## 4. Duplicate, Anomaly and Coverage Findings

**Overall promotion outcome (target imbalance):**

| Outcome | Employees | Percent |
|---|---:|---:|
| Not promoted (0) | 50,140 | 91.48% |
| Promoted (1) | 4,668 | 8.52% |
| Total | 54,808 | 100% |

**Observed:** the target is highly imbalanced. A model that always predicts "not promoted" would already be 91.48% accurate, so accuracy will not be used to judge models. Evaluation will use PR-AUC and Precision@10% (Topic 2 NFR1, NFR5), with a stratified train/test split.

**Department overview (all nine departments):**

| Department | Total employees | Promoted | Not promoted | Promoted (%) | Share of all employees (%) |
|---|---:|---:|---:|---:|---:|
| Sales & Marketing | 16,840 | 1,213 | 15,627 | 7.20 | 30.73 |
| Operations | 11,348 | 1,023 | 10,325 | 9.01 | 20.71 |
| Procurement | 7,138 | 688 | 6,450 | 9.64 | 13.02 |
| Technology | 7,138 | 768 | 6,370 | 10.76 | 13.02 |
| Analytics | 5,352 | 512 | 4,840 | 9.57 | 9.76 |
| Finance | 2,536 | 206 | 2,330 | 8.12 | 4.63 |
| HR | 2,418 | 136 | 2,282 | 5.62 | 4.41 |
| Legal | 1,039 | 53 | 986 | 5.10 | 1.90 |
| R&D | 999 | 69 | 930 | 6.91 | 1.82 |
| **All** | **54,808** | **4,668** | **50,140** | **8.52** | **100** |

**Selected for detailed review:** **Technology** (highest promotion rate, 10.76%) and **Legal** (lowest promotion rate, 5.10%, and one of the two smallest departments). TalentRank covers all departments, so the two were chosen to compare the extremes.

| Measure | Technology | Legal |
|---|---:|---:|
| Total / Promoted / Not promoted | 7,138 / 768 / 6,370 | 1,039 / 53 / 986 |
| Missing `previous_year_rating` | 546 (7.65%) | 168 (16.17%) |
| Missing `avg_training_score` | 0 (0.00%) | 123 (11.84%) |
| Missing `education` | 99 (1.39%) | 4 (0.38%) |
| Mean `avg_training_score` | 79.9 | 59.7 |
| Female share | 39.06% | 14.34% |

**Observed:** Legal has fewer promoted examples (53), more missing ratings and training scores, and a much lower average training score than Technology. **Inferred risk:** predictions and explanations for Legal employees will be less reliable, and department-level results will need wide uncertainty. These differences alone do not prove that either department was treated unfairly.

**Duplicates and anomalies:** no duplicates; one service-versus-age exception (V2). Value ranges are plausible (trainings 1–10, service 1–37 years, training score 39–99). **Limits:** coverage is measured on this dataset only and says nothing about roles or employees outside it.

## 5. Bias Assessment

**Historical bias (age).** Source labels are past promotion recommendations → **Observed:** the promotion rate falls with age (30–39: 9.12%; 40–49: 7.42%; 50–60: 6.58%); the 50–60 band's rate is **0.72** of the highest band, below the four-fifths (0.80) guideline → **Inferred risk:** a model can learn this age pattern through related features such as length of service, even without the age column → **Control:** exclude age and gender from model inputs, audit the top-10% list with the four-fifths rule, compare with a model that uses them, and flag any breach for human review.

**Measurement bias (training score).** Training is run separately for each department, and mean `avg_training_score` ranges from 50.2 (HR) to 84.6 (Analytics, R&D), with missing scores only in some departments → **Inferred risk:** rankings may reward a department's scoring scale rather than the employee; all three promotion studies rank training score among the top predictors → **Control:** test a within-department standardised score and report results per department.

**Pipeline-induced bias — checked.** Dropping rows with missing values (Topic 3 Configuration B) would have removed 92% of one-year employees and 26% of Legal records. Configuration A kept all records, so preparation did not change coverage.

Gender: historical promotion rates are similar (female 8.99%, male 8.32%; ratio 0.93). This does not prove past decisions were fair; it will be re-checked on model outputs.

## 6. Privacy, Rights and Governance

| Question | Finding |
|---|---|
| Provenance and version | S01 Kaggle upload; original WNS 2018 release (S02). SHA-256: raw `9e1c9c62…`, audited output `1dd337e5…` (manifests). Version and original collecting organisation: **N.V.** |
| Rights and permissions | Kaggle licence listed as "Unknown" — **N.V.** Used for academic coursework only. |
| Personal / sensitive data | Pseudonymous IDs plus personal attributes (age, gender, education, ratings). No names or contact details. Age and gender are used only for fairness auditing. |
| Will data leave the system? | No. Processing is local or in Colab; no external service receives the data. |
| Consent / collection basis | No information on employee consent or the basis for secondary use — **N.V.** Released originally for a public hackathon. |
| Ongoing maintenance | Owner: project student. Re-validate when the file hash changes, the schema changes, or licence/provenance information is found. |

**Safeguards:** no outputs combine employee IDs with additional personal data; reports show item IDs instead of employee IDs; any use of real HR data would require permission and compliance with Malaysia's Personal Data Protection Act 2010.

## 7. Mitigation and Impact on Requirements

| Priority improvement | Who / when | Requirements affected | Residual risk |
|---|---|---|---|
| 1. Fairness controls — exclude gender and age from inputs; audit the top-10% list by gender and age band; compare with vs without | Student, model development (Week 5–6) | RAI1, NFR5, O3 | Bias through related features; historical labels stay biased. |
| 2. Missing values and training-score scale — handle missing values inside the training pipeline (fit on training split only); test within-department standardised training score | Student, preprocessing (Week 5) | FR3, NFR1, NFR2 | Imputed ratings for new employees are estimates. |

| Finding | Topic 2 requirement / criterion affected | Effect if not handled |
|---|---|---|
| Target imbalance (8.52%) | NFR1, NFR5 | Misleading accuracy; poor recall for promoted employees |
| Historical age pattern (ratio 0.72) | RAI1 | Four-fifths check likely to fail for older employees |
| Training-score scale differs by department | FR4, FR5, NFR5 | Ranking and explanations driven by department scale |
| Missing ratings for one-year staff | FR3, RAI1 | Less reliable predictions for new employees |
| Few promoted examples in small departments | NFR1, NFR5 (by department) | Wide uncertainty for Legal, R&D, HR |
| Timeliness and licence N.V. | RAI2; interpretation of all results | Results describe a past, undated promotion cycle only |

## 8. Quality-Gate Decision

**Decision: GO WITH CONDITIONS** — for controlled academic model development on this dataset only.

- **Evidence:** V1 passed on all 54,808 records; V2 found one reviewable exception; no duplicates; 20 / 20 sampled records match the source; missing values are explained or documented.
- **Why not GO:** historical age skew (ratio 0.72), department-specific training-score scale, and timeliness, licence and consent are **N.V.**
- **Conditions:** (1) gender and age excluded from model inputs and used for auditing; (2) missing values handled inside the training pipeline; (3) a fairness audit reported with every model result; (4) evaluation uses PR-AUC and Precision@10%, not accuracy; (5) rankings remain decision support.
- **Re-review triggers:** a new data version (hash change), licence or provenance information found, a four-fifths ratio below 0.80 on model outputs, or any plan to use real employee data.
