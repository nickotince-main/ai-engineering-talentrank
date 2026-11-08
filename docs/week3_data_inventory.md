# Data Inventory — TalentRank

## 1. Data Source Overview

TalentRank uses a publicly available employee promotion dataset in CSV format. The dataset contains employee-related information and historical promotion outcomes.

The dataset is used to investigate employee features associated with historical promotion outcomes and prepare the data for machine learning development.

## 2. Data Inventory

| Source ID | Filename / URL | Type and Purpose | Version / Date / Owner | Access / Rights | Inspection Evidence |
|---|---|---|---|---|---|
| S01 | `employee_promotion.csv` / [Kaggle dataset](https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion) | Structured CSV dataset for employee feature investigation and promotion prediction. | Downloaded dataset; exact download date and original owner to be verified. | Publicly downloaded. Licence and redistribution rights not yet verified. | Successfully loaded using Pandas. Contains 54,808 records and 13 columns. First and last five records inspected. |

## 3. Dataset Structure

| Item | Inspection Result |
|---|---|
| Filename | `employee_promotion.csv` |
| Format | CSV |
| Total Records | 54,808 |
| Total Columns | 13 |
| Input Attributes | 11 employee-related attributes, excluding employee_id and is_promoted |
| Target Variable | `is_promoted` |
| Duplicate Rows | 0 |
| Missing Value Columns | education, previous_year_rating, avg_training_score |
| Python Version | 3.12.3 |
| Main Processing Library | Pandas |

## 4. Missing Value Inspection

| Column | Missing Records | Missing Percentage |
|---|---:|---:|
| education | 2,409 | 4.40% |
| previous_year_rating | 4,124 | 7.52% |
| avg_training_score | 2,560 | 4.67% |

The dataset contains missing values in three columns. These missing values will be investigated further before deciding on suitable data preparation methods.

## 5. Historical Promotion Outcome Distribution

| Promotion Outcome | Records | Percentage |
|---|---:|---:|
| Not Promoted (0) | 50,140 | 91.48% |
| Promoted (1) | 4,668 | 8.52% |
| Total | 54,808 | 100% |

The target variable is imbalanced because the number of employees who were promoted is much lower than the number who were not promoted.

This will be considered during machine learning development and model evaluation.

## 6. Data Storage

| Data Category | Storage Location | Description |
|---|---|---|
| Raw Data | `data/raw/employee_promotion.csv` | Original downloaded dataset. Kept unchanged. |
| Prepared Data | `data/processed/prepared_table.csv` | Prepared dataset to be generated during Part B. |

## 7. Data Processing Pathway

- Selected pathway: Pathway B - CSV/Excel.
- Data type: Structured data.
- File format: CSV.
- Main processing library: Pandas.
- Development environment: Visual Studio Code.
- Preparation purpose: Inspect, validate and prepare employee data for machine learning development.

## 8. Related Requirements

- FR1: Load and inspect employee records, features and promotion outcomes.
- FR2: Investigate employee features associated with historical promotion outcomes.
- FR3: Prepare data for machine learning model development.
- RAI2: Document the data source and usage rights and handle data responsibly.

## 9. Initial Data Limitations

1. The dataset contains missing values in education, previous_year_rating and avg_training_score.
2. The historical promotion outcome is imbalanced, with only 8.52% promoted records.
3. The dataset contains gender information, which requires careful consideration during fairness inspection.
4. The dataset records historical promotion outcomes and does not directly measure leadership potential.
5. Dataset licence and redistribution rights have not yet been verified.

### Additional Data Quality Checks

**Employee ID Uniqueness**

| Check | Result |
|---|---:|
| Total employee IDs | 54,808 |
| Unique employee IDs | 54,808 |
| Duplicate employee IDs | 0 |

All employee records have unique identifiers. The `employee_id` column will be retained for identification purposes but excluded from model input features.

**Missing Previous-Year Rating Investigation**

| Length of Service | Missing Previous-Year Rating Records |
|---|---:|
| 1 year | 4,124 |
| Other service periods | 0 |
| Total | 4,124 |

All records with missing `previous_year_rating` values belong to employees with one year of service. This indicates an association between missing ratings and length of service. The missing values will be considered during data preparation rather than removed automatically.