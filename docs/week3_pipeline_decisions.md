# Week 3 Pipeline Decisions — TalentRank

**Version:** 1.0  
**Project:** TalentRank - Explainable Machine Learning for Employee Promotion Prediction and Ranking

## 1. Requirements Addressed

The data pipeline supports the following project requirements:

| Requirement ID | Description |
|---|---|
| FR1 | Load and inspect the employee promotion dataset. |
| FR2 | Analyse employee features and their association with historical promotion outcomes. |
| FR3 | Prepare the dataset for machine learning model development. |
| RAI2 | Protect employee data and avoid unauthorised disclosure. |

## 2. Data Source Decision

**Selected approach: Pathway B — CSV/Excel**

The project uses a public employee promotion dataset in CSV format.

| Item | Details |
|---|---|
| Source file | `employee_promotion.csv` |
| File format | CSV |
| Records | 54,808 |
| Columns | 13 |
| Target column | `is_promoted` |
| Raw data location | `data/raw/employee_promotion.csv` |
| Prepared data location | `data/processed/prepared_table.csv` |

CSV was selected because the dataset contains structured employee records that can be loaded and processed using Python and pandas.

The dataset's redistribution licence and usage rights have not yet been verified.

## 3. Data Preparation Decision

Two preparation approaches were considered.

| Approach | Description | Result |
|---|---|---|
| A — Original | Use the original dataset without cleaning operations. | 54,808 records and 13 columns. |
| B — Prepared | Remove exact duplicate rows and trim leading/trailing whitespace from text columns. | 54,808 records and 13 columns. |

**Selected approach: Approach B**

Approach B was selected because it provides basic data cleaning checks before further processing.

However, the inspection found:
- No duplicate rows.
- No leading or trailing whitespace in the text columns.
- 9,093 missing values remained unchanged.

Therefore, the preparation operations did not change the dataset values.

## 4. Missing Value Decision

Missing values were preserved during this pipeline stage.

The inspection identified missing values in three columns:

| Column | Missing Values |
|---|---:|
| education | 2,409 |
| previous_year_rating | 4,124 |
| avg_training_score | 2,560 |
| **Total** | **9,093** |

All 4,124 records with missing `previous_year_rating` have a `length_of_service` value of 1.

The missing values were not removed or imputed because their handling should be considered during model development. Any imputation method should be fitted using the training data only to avoid data leakage.

## 5. Pipeline Processing Decision

**Selected approach: ETL (Extract, Transform, Load)**

The pipeline follows an ETL approach:

1. **Extract:** Load the original employee promotion CSV file.
2. **Transform:** Inspect the data, check duplicates and whitespace, and prepare the dataset.
3. **Load:** Export the prepared dataset into `data/processed/prepared_table.csv`.

Batch processing was selected because the project uses a static historical dataset. Real-time or streaming processing is not required for the current project scope.

## 6. Data Lineage Decision

A JSON manifest was created to record the source and prepared dataset information.

**Manifest location:** `data/week3_manifest.json`

The manifest records:
- Source and output file paths.
- SHA-256 file fingerprints.
- Number of records and columns.
- Missing value and duplicate row counts.
- Data preparation operations.
- Notes about the pipeline limitations.

Both source and output file fingerprints were successfully validated.

This supports file traceability but does not prove data accuracy, fairness, or legal permission to use the dataset.

## 7. Accepted Limitations

The current pipeline has the following limitations:

1. Missing values have not been handled yet.
2. The dataset contains an imbalanced promotion outcome, with approximately 8.52% promoted records and 91.48% not promoted records.
3. The dataset represents historical promotion outcomes and does not directly measure employee leadership potential.
4. Dataset redistribution and usage rights still need verification.
5. The current pipeline focuses on CSV-based batch processing and does not support real-time data updates.

## 8. Output Files

| File | Purpose |
|---|---|
| `notebooks/week3_pipeline.ipynb` | Contains the data inspection and preparation workflow. |
| `data/raw/employee_promotion.csv` | Original dataset. |
| `data/processed/prepared_table.csv` | Prepared dataset. |
| `data/week3_manifest.json` | Records data lineage and file fingerprints. |
| `docs/week3_data_inventory.md` | Records dataset information and inspection results. |

## 9. Conclusion

The first version of the TalentRank data pipeline successfully loads, inspects, prepares, exports, and validates the employee promotion dataset. The prepared dataset contains 54,808 records and 13 columns. Missing values are preserved for later model-specific preprocessing, while the source and output files are recorded in the data lineage manifest.

This pipeline provides the initial data preparation stage for the next machine learning development activities.