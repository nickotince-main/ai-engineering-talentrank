# TalentRank - Explainable Machine Learning for Employee Promotion Prediction and Ranking

> TalentRank develops explainable machine learning models to predict and rank historical employee promotion outcomes, supporting HR Talent Management Specialists and top management in reviewing employee information.

## Problem Statement

Organisations need to review employee information when considering promotion decisions. However, investigating employee features associated with historical promotion outcomes and understanding prediction results can be challenging, especially when dealing with many employee records. Machine learning can help identify patterns in historical employee data, predict promotion outcomes, and provide explanations of the features influencing the predictions.

## Evidence of the Problem

- Employee promotion prediction is an existing HR analytics research area, showing the use of employee-related data to study historical promotion outcomes.  
  Source: [Employee Promotion Prediction Dataset - Kaggle](https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion)

- Historical promotion outcomes may be imbalanced, making predictive performance more difficult to evaluate using accuracy alone.  
  Source: [Employee Promotion Prediction Dataset - Kaggle](https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion)

## Initial Related Work / Existing Approaches

- **Traditional Machine Learning for Employee Promotion Prediction** — Uses employee-related features to predict promotion outcomes.  
  Source: [Employee Promotion Prediction Dataset - Kaggle](https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion)  
  Key limitation: The dataset provides historical promotion labels but does not explain the reasons behind actual promotion decisions.

- **Explainable AI (XAI)** — Provides methods to interpret machine learning predictions and understand the influence of input features.  
  Source: [SHAP Documentation](https://shap.readthedocs.io/en/latest/)  
  Key limitation: Feature contribution explanations do not establish causal relationships or prove why an organisation made a promotion decision.

- **Machine Learning Model Evaluation** — Uses evaluation metrics to assess predictive performance, including precision, recall, F1-score and ROC-AUC.  
  Source: [Scikit-learn Model Evaluation Documentation](https://scikit-learn.org/stable/modules/model_evaluation.html)  
  Key limitation: Predictive performance metrics alone do not explain the factors influencing individual predictions.

## Project Objectives

- **O1:** Investigate employee features associated with historical promotion outcomes.
- **O2:** Develop machine learning models for promotion prediction and ranking.
- **O3:** Evaluate the predictive performance and interpretability of the developed machine learning models.

## Target Users

- **HR Talent Management Specialists:** To review employee prediction results and understand the features influencing model predictions.
- **Top Management:** To obtain additional information when reviewing employees for promotion consideration.

## Data Sources

- **Employee Promotion Dataset** — Public HR dataset containing approximately 54,808 employee records and historical promotion outcomes.  
  URL: [Kaggle - Employee Promotion Dataset](https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion)

- **SHAP Documentation** — Reference for machine learning explainability methods.  
  URL: [SHAP Documentation](https://shap.readthedocs.io/en/latest/)

- **Scikit-learn Documentation** — Reference for machine learning development and model evaluation.  
  URL: [Scikit-learn Documentation](https://scikit-learn.org/stable/)

## Weekly Progress

| Week | Focus | Deliverable | Status |
|------|-------|-------------|--------|
| W1 | System map + project scope | README + project scope | ✅ Completed |
| W2 | Requirements + system architecture | Requirements + architecture documents | ✅ Completed |
| W3 | Data engineering and pipeline | Pipeline notebook + data inventory + pipeline diagram | In progress |
| W4 onwards | Model development, evaluation and system implementation | To be updated | Pending |

## Repository Structure (Latest)

```text
ai-engineering-talentrank/
├── README.md
├── docs/
│   ├── week1_observation_sheet.md
│   ├── week1_project_scope.md
│   ├── week2_requirements.md
│   ├── week2_evaluation_criteria.md
│   ├── week2_architecture_v1.png
│   ├── week2_architecture_decisions.md
│   ├── week3_data_inventory.md
│   ├── week3_pipeline_diagram.png
│   └── week3_pipeline_decisions.md
├── data/
│   ├── raw/
│   │   └── employee_promotion.csv
│   ├── processed/
│   │   └── prepared_table.csv
│   ├── pipeline_v1/
│   │   └── tabular/
│   │       ├── prepared_table.csv
│   │       └── pipeline_manifest.json
│   └── week3_manifest.json
├── src/
├── notebooks/
│   └── week3_pipeline.ipynb
├── evaluation/
└── prompts/
```
