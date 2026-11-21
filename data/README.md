# Data — TalentRank

## Source

| Item | Detail |
|---|---|
| Dataset | Employees Evaluation for Promotion (Kaggle) |
| URL | https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion |
| Original release | WNS Analytics Wizard 2018 HR Analytics challenge (Analytics Vidhya) |
| File in this repository | `data/raw/employee_promotion.csv` (kept unchanged) |
| Size | 54,808 records × 13 columns |
| SHA-256 | `9e1c9c62f8ebbeabd4062448df8a8983898f8925c8417df5e204386b7879fdaf` |
| Licence | Listed as "Unknown" on Kaggle — **N.V. (Not Verified)**. Included for academic coursework only. |
| Collection period / version | Not documented — **N.V.** |

## Data dictionary

Definitions follow the dataset documentation.

| Column | Definition | Type | Role in TalentRank |
|---|---|---|---|
| `employee_id` | Unique ID for employee | Integer | Record identifier (not a model input) |
| `department` | Department of employee | Categorical (9 values) | Model input |
| `region` | Region of employment (unordered) | Categorical (34 values) | Model input; audited as a possible proxy |
| `education` | Education level | Categorical (3 values + missing) | Model input |
| `gender` | Gender of employee | Categorical (f, m) | **Fairness audit only** (not a model input) |
| `recruitment_channel` | Channel of recruitment for employee | Categorical (3 values) | Model input |
| `no_of_trainings` | Number of other trainings completed in the previous year on soft skills, technical skills, etc. | Integer | Model input |
| `age` | Age of employee | Integer | **Fairness audit only** (not a model input) |
| `previous_year_rating` | Employee rating for the previous year | 1–5 (missing allowed) | Model input |
| `length_of_service` | Length of service in years | Integer | Model input |
| `awards_won` | 1 if awards were won during the previous year, else 0 (documented as `awards_won?`) | Binary | Model input |
| `avg_training_score` | Average score in current training evaluations | Number (missing allowed) | Model input |
| `is_promoted` | **Target** — recommended for promotion (1) or not (0) | Binary | Prediction target |

## Notes

- `is_promoted` records a historical **recommendation for promotion**. It does not measure leadership potential or suitability for a specific role.
- No column records a date (e.g., evaluation or promotion date). The reference year of "previous year" is not specified.
- The original 2018 release also had a `KPIs_met >80%` column, which is not in this copy.

## Prepared files

| File | Created by | Content |
|---|---|---|
| `data/processed/prepared_table.csv` | Week 3 notebook, Part B (Cell 5B) | Prepared table (Configuration A) |
| `data/week3_manifest.json` | Week 3 notebook, Cell 6A | Part B lineage manifest |
| `data/pipeline_v1/tabular/prepared_table.csv` | Week 3 notebook, Cell 8B | Executed V1 pipeline output |
| `data/pipeline_v1/tabular/pipeline_manifest.json` | Week 3 notebook, Cell 8B | Pipeline run record |
