# Week 2 Requirements Specification — TalentRank (Explainable Machine Learning for Employee Promotion Prediction and Ranking)

**Version:** 1.0

---

## Section 0 — Project Framing & Application Scope Checkpoint

| | |
|---|---|
| **Problem Evidence** | Evidence 1: Employee promotion prediction has been investigated using machine learning techniques and employee-related data, showing that historical employee records can be used to investigate patterns associated with promotion outcomes. - https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion).<br><br>Evidence 2: Research has explored explainable AI methods to understand the features influencing employee promotion predictions, highlighting the importance of interpreting model outputs. - https://ieeexplore-ieee-org.ezproxy.utm.my/document/10393141.<br><br>These sources support studying historical promotion outcomes; they do not establish that the data measures leadership potential or identifies employees suitable for a specific role such as Department Manager, Director, or CEO. |
| **Existing Approaches** | 1) Traditional Machine Learning: Classification models can learn patterns from employee records and predict historical promotion outcomes. However, prediction results alone may not provide sufficient explanations for HR users.<br><br>2) Explainable AI (XAI): Methods such as SHAP can explain how input features contribute to individual model predictions. However, feature contributions do not prove the actual reasons behind organisational promotion decisions.<br><br>3) Employee Promotion Analytics: Existing datasets and research provide opportunities to analyse historical promotion outcomes. However, an integrated workflow for prediction, employee ranking and explanation is the focus of this project. |
| **Project / Engineering Gap** | Existing research has demonstrated the use of machine learning for employee promotion prediction and explainable AI for interpreting model outputs. However, TalentRank will focus on developing an integrated workflow that combines employee feature investigation, promotion prediction, ranking based on predicted outcomes, and feature-level explanations.<br><br>The engineering focus is to develop a system that presents prediction and ranking results in an understandable way to support HR Talent Management Specialists and top management. The system will be developed as a decision-support tool rather than an automated promotion decision-maker. |
| **Objective O1** | To investigate employee features associated with historical promotion outcomes. |
| **Objective O2** | To develop machine learning models for promotion prediction and ranking. |
| **Objective O3** | To evaluate the predictive performance and interpretability of the developed machine learning models. |
| **Intended Users** | HR Talent Management Specialists: Review employee prediction results and understand the features influencing model predictions.<br><br>Top Management: Obtain additional information when reviewing employees for promotion consideration. |
| **Supported Tasks** | - Load and inspect the selected employee dataset.<br>- Investigate employee features associated with historical promotion outcomes.<br>- Prepare employee data for machine learning development.<br>- Develop machine learning models for promotion prediction.<br>- Generate employee rankings based on predicted promotion probabilities.<br>- Display explanations of features influencing model predictions.<br>- Evaluate predictive performance and interpretability. |
| **Information / Knowledge Domain** | Structured employee-related data containing approximately 54,808 employee records and historical promotion outcomes.<br><br>The dataset includes information such as:<br>- Department<br>- Education<br>- Gender<br>- Recruitment channel<br>- Number of trainings<br>- Previous year rating<br>- Length of service<br>- Awards won<br>- Average training score<br>- Historical promotion outcome |
| **Operating Context** | A small academic AI Engineering prototype developed for one semester using a publicly available HR dataset. The system is intended for experimentation, evaluation and decision-support demonstration. It is not a production HR decision system. |
| **Important Constraints** | - Use a publicly available employee dataset and document its source and usage rights.<br>- Inspect and understand the dataset before finalising preprocessing methods.<br>- Handle missing values, duplicate records and class imbalance appropriately.<br>- Separate training, validation and testing data to avoid data leakage.<br>- Generate rankings based on model prediction outputs.<br>- Use explainability methods to interpret model predictions.<br>- Evaluate predictive performance using suitable metrics, considering the imbalanced promotion labels.<br>- Clearly communicate the limitations of historical promotion data.<br>- Keep implementation manageable within one semester. |
| **Explicit Out-of-Scope Tasks** | - Automatically approving or rejecting employee promotions.<br>- Making final promotion decisions on behalf of HR personnel or management.<br>- Claiming to measure an employee's actual leadership potential.<br>- Predicting suitability for a specific higher position not represented in the dataset.<br>- Treating feature contributions as proof of actual promotion decision reasons.<br>- Guaranteeing future employee promotion outcomes.<br>- Using confidential employee records without proper permission. |
| **Evaluation Focus** | The evaluation will determine whether the developed machine learning models can predict historical employee promotion outcomes, generate meaningful employee rankings, and provide understandable explanations of their predictions.<br><br>Predictive performance will be evaluated using suitable classification metrics such as precision, recall, F1-score, ROC-AUC and PR-AUC. Interpretability will be evaluated by examining feature contributions and individual prediction explanations. Responsible AI checks will also consider fairness, privacy and human oversight. |

---

## Section 1 — Functional Requirements (FR)

| ID | Requirement - The system SHALL | Scope Boundary - SHALL NOT (where relevant) |
|---|---|---|
| FR1 | Allow the user to load and inspect the selected employee dataset, including its records, features, data types, missing values and historical promotion outcome distribution. | Allow the user to load and inspect the selected employee dataset, including its records, features, data types, missing values and historical promotion outcome distribution. |
| FR2 | Analyse employee features and present relevant statistical information about their association with historical promotion outcomes. | Claim that observed feature associations prove causation or represent the actual reasons behind promotion decisions. |
| FR3 | Develop and train machine learning models using the prepared employee dataset to predict historical promotion outcomes. | Claim that model predictions guarantee future promotion outcomes. |
| FR4 | Generate and display employee rankings based on their predicted promotion probabilities from the developed machine learning models. | Present rankings as definitive promotion recommendations or automatic employment decisions. |
| FR5 | Generate and display explanations showing the employee features that influence model predictions. | Present feature contributions as causal explanations or proof of actual organisational promotion decisions. |

## Section 2 — Non-Functional Requirements (NFR)

| ID | Quality Dimension | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Predictive performance | The developed models shall provide measurable predictive performance on unseen employee records. | Evaluate the final model using a held-out test dataset and compare its PR-AUC with the positive-class prevalence baseline. | Evaluate the final model using a held-out test dataset and compare its PR-AUC with the positive-class prevalence baseline. | PR-AUC | Proposed target: PR-AUC greater than the positive-class prevalence baseline. Report precision, recall, F1-score and ROC-AUC as supporting metrics. |
| NFR2 | Interpretability | The system shall provide feature contribution information for its generated predictions. | Inspect the explanation outputs for selected individual predictions and the overall model. | Percentage of evaluated predictions with available explanations | Proposed target: 100% of selected test predictions have corresponding explanations. |
| NFR3 | Reproducibility | The model development process shall produce reproducible results when using the same dataset, preprocessing configuration, model settings and random seed. | Run the training pipeline again using the same configuration and compare the resulting predictions and evaluation metrics. | Difference in evaluation metrics and prediction outputs | Proposed target: Identical results when using a deterministic configuration and the same software environment. |
| NFR4 | Performance | The system shall generate prediction and ranking results within a practical response time for the defined evaluation dataset. | Measure the end-to-end prediction and ranking time using the documented test environment. | Processing time in seconds | Proposed target: ≤ 10 seconds for the selected evaluation workload. Validate this target during implementation. |

## Section 3 — Responsible AI Requirements (RAI)

| ID | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Bias and Fairness | The system shall inspect model performance across relevant employee groups where the dataset contains suitable attributes and the analysis is permitted. Sensitive attributes shall not be used as automatic promotion-ranking criteria. | Compare relevant predictive performance metrics across groups and inspect the model input features. | Difference in group-level recall and other selected performance metrics; sensitive attributes included as ranking features | Report all observed group differences. Sensitive attributes used directly as ranking criteria: 0. |
| RAI2 | Privacy and Data Protection | The system shall use only approved data and shall not expose confidential employee information in displayed results or shared project artefacts. | Inspect dataset access, output files and shared artefacts for unauthorised personal or confidential information. | Number of unauthorised personal or confidential data disclosures | 0 |
| RAI3 | Transparency and Human Oversight | The system shall clearly communicate that prediction probabilities and employee rankings are model-generated results intended to support human review, not final promotion decisions. | Inspect the interface and prediction results for the required disclaimer and test representative result screens. | Percentage of relevant views containing the disclaimer | 100% of relevant views display the disclaimer. |

---

### Reflection

**Which Responsible AI requirement in your project will be the most difficult to measure or enforce technically? Why?**

RAI1 may be difficult to evaluate because historical promotion outcomes may already contain organisational biases. Differences in model performance between employee groups should be reported rather than assumed to be fair simply because the model achieves good overall accuracy.<br><br>RAI3 is also important because users may over-rely on model predictions and rankings. TalentRank must communicate its limitations clearly and ensure that final promotion decisions remain the responsibility of HR personnel and management.
