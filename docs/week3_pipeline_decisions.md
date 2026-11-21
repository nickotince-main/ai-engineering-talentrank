# Week 3 Pipeline Decisions — TalentRank

**Project:** TalentRank — Explainable Machine Learning for Employee Promotion Prediction and Ranking
**Version:** 1.0 · Diagram: `docs/week3_pipeline_diagram.png` · Notebook: `notebooks/week3_pipeline.ipynb`

## 1. Requirements Supported

| Requirement | How the pipeline supports it |
|---|---|
| FR1 | Loads, inspects and reports records, columns, types, missing values and target distribution. |
| FR2, FR3 | Produces a clean, complete table for feature analysis and model training. |
| NFR3 | Deterministic output: identical SHA-256 on repeat runs (Cell 9) and across operating systems. |
| RAI1 | Keeps every employee record, so no group is removed by preparation. |
| RAI2 | Records source identity and rights status in both manifests. |

## 2. Selected Source and Pathway

Source S01 `data/raw/employee_promotion.csv` (54,808 × 13). Pathway B — CSV, because the data is a single structured table.

## 3. Part B Comparison and Selected Configuration

| Measurement | Configuration A — keep missing (selected) | Configuration B — drop rows with missing values |
|---|---:|---:|
| Operations | Remove exact duplicates, trim text | Configuration A + drop rows with any missing value |
| Records | 54,808 | 46,380 (−8,428, −15.38%) |
| Missing values | 9,093 | 0 |
| Promotion rate | 8.52% | 8.78% |
| Employees with 1 year of service | 4,547 | 362 (−92.04%) |
| Legal department records | 1,039 | 765 |
| Operations department records | 11,348 | 9,259 |

**Selected: Configuration A.** Configuration B removes almost all employees with one year of service (their `previous_year_rating` is missing because there is no previous year to rate) and about a quarter of Legal records. The model would then know almost nothing about new employees — pipeline-induced bias that conflicts with RAI1.

**Accepted trade-off:** missing values stay in the prepared table, so the training pipeline must handle them (imputation or missing-value indicators fitted on the training split only, to avoid leakage). In this dataset, duplicate removal and text trimming changed nothing (0 duplicates, no extra spaces), but the checks stay in the pipeline to protect future data versions.

## 4. ETL / ELT / Hybrid and Batch / Streaming

- **Hybrid:** the raw CSV is landed and kept unchanged; transformations run in Python and the prepared output is stored separately. Keeping raw and prepared files supports reproducibility and reprocessing.
- **Batch:** the source is a static historical file, so streaming adds no value.
- **Not applicable:** document parsing, chunking and embeddings are for text data. Here the unit of evidence is one employee record, identified by `employee_id`.

## 5. Stage Contracts

| Stage | Contract / evidence | Failure type | Failure response | V1 result |
|---|---|---|---|---|
| Ingest | Selected source exists (`select_source`, `resolve_source`) | Explicit — file missing | Stop; `FileNotFoundError` | PASS |
| Inspect | 13 expected columns; table not empty | Explicit — schema changed | Stop; `ValueError` | PASS |
| Prepare | Configuration A runs; record count stays 54,808; missing values kept | Silent — rows lost without error | Print a warning; review before continuing | PASS |
| Validate | Output exists and is non-empty (B3); exported rows/columns match | Silent — empty or wrong output | Stop | PASS |
| Record lineage | Source and output SHA-256 recorded (Cell 6A, 6B, 8B); Cell 9 repeat run matches | Silent — hash mismatch | Stop and report | PASS |

Partial failure (some inputs succeed, others fail) cannot happen in V1 because there is a single source file.

## 6. Lineage Records

| File | Content |
|---|---|
| `data/week3_manifest.json` | Part B: source ID, filename, URL, source SHA-256, prepared output path and SHA-256, processing note, record ID field, version and rights status |
| `data/pipeline_v1/tabular/pipeline_manifest.json` | Part C: run time, Python/pandas versions, configuration, inspection results, output SHA-256, source-to-output mapping, rights status |

Adjustments to the lab template: paths are stored relative to the project folder (so manifests match on any computer), and CSV files are written with Unix line endings (so the SHA-256 is identical on Windows, macOS and Linux). Manifests prove file traceability only, not data quality, fairness or usage rights.

## 7. Provenance and Access Caveats

- Licence listed as "Unknown" on Kaggle — not verified.
- Collection period, dataset version and original collecting organisation are not documented.
- The `KPIs_met >80%` column of the original 2018 release is absent from this copy.

## 8. Left for Later Topics

| Item | When |
|---|---|
| Data quality, coverage and bias audit | Week 4 |
| Train/validation/test split; missing-value handling fitted on training data; gender and age held out for audit | Model development |
| Model, ranking, explanation and fairness evaluation | Evaluation topics |
| Interface, deployment and monitoring | Later topics |
