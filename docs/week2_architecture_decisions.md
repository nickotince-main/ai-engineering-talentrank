# Week 2 Architecture Decisions — TalentRank (Explainable Machine Learning for Employee Promotion Prediction and Ranking)

Version: 1.0

## 1. Identify Architecture Needs from Requirements

| Requirement ID | What does the requirement need? | Architecture Response |
|---|---|---|
| FR1 | The system needs to load and inspect employee records, features, data types, missing values and historical promotion outcome distribution. | **Employee Data Loading and Inspection:** A component to load the employee dataset and inspect its structure, data quality and promotion outcome distribution. |
| FR2 | The system needs to analyse employee features and present statistical information about their association with historical promotion outcomes. | **Feature Analysis:** A component to perform exploratory data analysis and present statistical summaries and visualisations of employee features. |
| FR3 / NFR1 | The system needs to develop machine learning models to predict historical promotion outcomes and evaluate their performance using unseen employee records. | **Machine Learning Capability:** A component to prepare data, train prediction models, generate predictions and evaluate predictive performance using a held-out test dataset. |
| FR4 | The system needs to generate an ordered employee list based on predicted promotion probabilities. | **Employee Ranking:** A component to sort employee records according to their predicted promotion probabilities and generate an ordered ranking. |
| FR5 / NFR2 | The system needs to explain which employee features influence model predictions, including individual predictions and overall feature importance. | **Explainability Capability:** A component to generate global and local feature contribution explanations for model predictions. |
| NFR3 | The model development process needs to produce consistent results using the same dataset and configuration. | **Reproducibility Support:** Record dataset information, preprocessing settings, model configuration, random seed and software environment so that the development process can be repeated. |
| NFR4 | The system needs to generate prediction and ranking results within a practical response time. | **Performance Measurement:** Include a measurement point to record the time required to generate predictions and employee rankings. |
| RAI1 | The system needs to inspect differences in predictive performance across relevant employee groups and prevent sensitive attributes from being used directly as automatic ranking criteria. | **Fairness Inspection:** A component to compare selected performance metrics across relevant employee groups and inspect the features used by the prediction and ranking process. |
| RAI2 | The system needs to use approved data and avoid exposing confidential employee information. | **Privacy and Data Protection Control:** Use an approved dataset, document its source and usage rights, and check displayed results and shared project files for unauthorised information. |
| RAI3 | The system needs to communicate that predictions and rankings support human review and are not final promotion decisions. | **Transparency and Human Oversight:** Display clear disclaimers alongside prediction, ranking and explanation results to communicate the system's limitations. |

## 2. Architecture Decision Records (ADR)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| Use a Structured Employee Dataset as the Main Data Source | FR1, FR2, FR3 — the system needs employee records and historical promotion outcomes for data inspection, feature analysis and machine learning development. | 1. Use a structured CSV dataset. 2. Use a database containing employee records. 3. Use manually entered employee information. | Use a publicly available structured CSV employee dataset as the main data source. | CSV is simple to access, inspect and process for an academic prototype. However, it does not provide automatic data updates or the management capabilities of a database. |
| Use a Machine Learning Prediction and Ranking Workflow | FR3, FR4, NFR1 — the system needs to predict historical promotion outcomes, evaluate predictive performance and generate employee rankings. | 1. Use manually defined rules to rank employees. 2. Use a machine learning prediction model to generate promotion probabilities. 3. Use a manual employee ranking process without prediction. | Develop a machine learning workflow that predicts historical promotion outcomes and uses predicted probabilities to generate employee rankings. | Machine learning can identify patterns in historical employee data and generate consistent predictions. However, the results depend on the quality and limitations of historical data and may reproduce existing organisational biases. |
| Include an Explainability Capability | FR5, NFR2, RAI3 — HR users need to understand which employee features influence model predictions. | 1. Display prediction probabilities only. 2. Display general model feature importance. 3. Provide global and individual prediction explanations. | Include an explainability capability that provides overall feature importance and explanations for individual predictions. | Explanations improve transparency and help users understand model behaviour. However, they add implementation complexity and do not prove the actual reasons behind organisational promotion decisions. |
| Keep Human Oversight in the Promotion Review Process | RAI1, RAI2, RAI3 — the system must consider fairness, privacy and transparency and must not make final employee promotion decisions. | 1. Automatically approve or reject employees based on model predictions. 2. Generate predictions and rankings for human review. | Use TalentRank as a decision-support system. HR personnel and management remain responsible for final promotion decisions. | Human oversight reduces the risk of relying entirely on model predictions. However, users still need to understand the system's limitations and avoid over-relying on its rankings. |

## 3. Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 — Investigate employee features associated with historical promotion outcomes. | FR1, FR2 | Dataset inspection and feature investigation. | Employee Data Loading and Inspection; Feature Analysis. | Dataset inspection results, missing value summary, feature analysis tables and visualisations. |
| O2 — Develop machine learning models for promotion prediction and ranking. | FR3, FR4, NFR1 | Prediction performance and employee ranking usefulness. | Machine Learning Capability; Employee Ranking. | Model evaluation results, PR-AUC, precision, recall, F1-score, ROC-AUC, Precision@K and Lift@K. |
| O3 — Evaluate predictive performance and interpretability of the developed machine learning models. | FR5, NFR2 | Prediction explainability and explanation coverage. | Explainability Capability. | Global feature importance results, individual prediction explanations and explanation coverage results. |
| O3 — Evaluate predictive performance and interpretability of the developed machine learning models. | NFR3, NFR4 | Reproducibility and processing performance. | Reproducibility Support; Performance Measurement. | Repeated training results, recorded configurations and prediction/ranking processing time. |
| O2 — Develop machine learning models for promotion prediction and ranking. | RAI1, RAI2, RAI3 | Fairness inspection, privacy and human oversight. | Fairness Inspection; Privacy and Data Protection Control; Transparency and Human Oversight. | Group-level performance analysis, data handling checks and screenshots of system disclaimers. |

## 4. Architecture Decision Reflection

**Which architecture decision involved the most important trade-off? Which requirement drove the decision, and what trade-off did you accept?**

The most important architecture decision is using machine learning to generate promotion predictions and employee rankings instead of relying on manually defined ranking rules.

This decision is driven by FR3 and FR4 because TalentRank aims to investigate historical promotion outcomes and generate rankings based on predicted probabilities.

The main trade-off is that machine learning can identify patterns from historical employee data, but the predictions may also reflect existing biases and limitations in the dataset. Therefore, the system needs explainability, fairness inspection and human oversight to help users understand the results.

TalentRank will remain a decision-support prototype rather than an automated employee promotion decision system.
