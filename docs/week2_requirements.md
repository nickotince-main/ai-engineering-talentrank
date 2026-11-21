# Week 2 Requirements Specification — TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

**Project:** TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

**Version:** 1.0

---

## Section 0 — Project Framing & Application Scope Checkpoint

| | |
|---|---|
| **Problem Evidence** | **Evidence 1:** The current promotion process in most organisations depends on supervisors' judgements, and the larger the company, the more time and effort it takes to identify employees qualified for promotion manually (Alqahtani & Almaleh, 2022, https://doi.org/10.1109/DSIT55514.2022.9943959).<br><br>**Evidence 2:** The EU AI Act (Annex III, point 4) classifies AI used for decisions that affect promotion as high-risk, requiring transparency, human oversight and bias control (https://ai-act-service-desk.ec.europa.eu/en/node/464).<br><br>These sources support studying historical promotion outcomes. They do not show that the data measures leadership potential or suitability for a specific role. |
| **Existing Approaches** | 1) **ML promotion classification** — Alqahtani & Almaleh (2022): seven classifiers with SMOTE; Gradient Boosting best (AUC 79.28%). *Already does:* promotion prediction and global feature importance.<br>2) **Explainable promotion prediction** — Bhattacharya et al. (2023): SMOTE-ENN, five classifiers, XGBoost best (95% accuracy), explained with SHAP and LIME. *Already does:* global and individual explanations.<br>3) **Leakage-aware model comparison** — Benabou & Touhami (2026): eight models, stratified 5-fold cross-validation with SMOTE only inside training folds; Random Forest best (96.21% accuracy). *Already does:* reliable model comparison under class imbalance.<br>4) **ML ranking systems** — Bitkuri et al. (2025): pointwise, pairwise and listwise learning-to-rank, ranking metrics such as precision, fairness in hiring. *Already does:* general methods to build and evaluate rankings.<br>Full references: `docs/references.md`. |
| **Project / Engineering Gap** | Studies 1–3 report 94–96% accuracy, only slightly better than the 91.5% from always predicting "not promoted". None evaluates the **ranked list** HR would review (Study 4 shows rankings need ranking metrics), none checks whether explanations are **stable** or states their limits (Study 2 presents them as real decision reasons), and none **audits fairness** across employee groups. TalentRank addresses this practical engineering gap in one decision-support workflow. This is not a claim of scientific novelty. |
| **Objective O1** | To investigate employee features associated with historical promotion outcomes in a public HR dataset. |
| **Objective O2** | To develop explainable machine learning models that predict historical promotion outcomes, rank employees by predicted probability and explain each ranking. |
| **Objective O3** | To evaluate the developed models in terms of predictive performance, ranking quality, explanation stability and fairness across employee groups. |
| **Intended Users** | HR Talent Management Specialists; Top Management. |
| **Supported Tasks** | Inspect the employee dataset · analyse features linked to promotion · predict promotion probability · rank employees by probability · show global and individual explanations · report performance, ranking and fairness results. |
| **Information / Knowledge Domain** | Structured HR records — 54,808 employees, 13 columns, all nine departments.<br>**Model inputs:** department, region, education, recruitment_channel, no_of_trainings, previous_year_rating, length_of_service, awards_won, avg_training_score.<br>**Fairness audit only (not model inputs):** gender, age.<br>**Identifier:** employee_id. **Target:** is_promoted (recommended for promotion). Definitions: `data/README.md`. |
| **Operating Context** | One-semester academic prototype run in Python (local or Google Colab) on a public dataset, used to demonstrate decision support. Not a production HR system. |
| **Important Constraints** | Public dataset only (Kaggle lists the licence as "Unknown"; recorded as N.V.) · class imbalance (8.52% promoted) · missing values in three columns · no date information in the data · split data before model-specific preprocessing to avoid leakage · gender and age excluded from model inputs · one-semester timeline. |
| **Explicit Out-of-Scope Tasks** | Approving or rejecting promotions · making final decisions for HR or management · measuring leadership potential · predicting suitability for a specific position · treating explanations as proof of real decision reasons · guaranteeing future outcomes · using confidential employee records. |
| **Evaluation Focus** | Can the models (1) predict historical promotion recommendations better than the base rate, (2) place promoted employees near the top of the ranked list, (3) give stable explanations, and (4) do so without large differences between gender and age groups? |

---

## Section 1 — Functional Requirements (FR)

| ID | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
|---|---|---|
| FR1 | Load and inspect the employee dataset, reporting records, columns, data types, missing values and the promotion outcome distribution. | Modify or overwrite the original raw dataset. |
| FR2 | Analyse each model input feature and present its association with historical promotion outcomes. | Claim that an association proves causation or the real reason for a promotion decision. |
| FR3 | Predict the probability that an employee was recommended for promotion, using a model trained on prepared historical records. | Use gender or age as model inputs, or claim the prediction guarantees a future outcome. |
| FR4 | Generate and display a list of employees ranked by predicted promotion probability. | Present the ranking as a promotion decision or an automatic employment action. |
| FR5 | Generate and display global feature importance and an individual explanation for each ranked employee. | Present feature contributions as causal explanations or proof of organisational decision reasons. |

## Section 2 — Non-Functional Requirements (NFR)

| ID | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Correctness — prediction | The selected model shall predict historical promotion outcomes on unseen records clearly better than chance. | Evaluate once on a stratified held-out test set not used for training or model selection. | PR-AUC (supporting: ROC-AUC, precision, recall, F1) | PR-AUC ≥ 0.35 (≈ 4× the 0.085 base rate); ROC-AUC ≥ 0.75 |
| NFR2 | Interpretability | Every ranked prediction shall have an explanation, and the main explanations shall be stable. | (a) Count ranked test predictions with an explanation. (b) Retrain on 3 different stratified bootstrap resamples of the training set; compare the global top-5 features. (Resamples are used instead of random seeds alone, because some models give identical results for every seed, which would make the check meaningless.) | (a) Coverage (%) (b) Top-5 feature overlap (%) | (a) 100% (b) ≥ 80% |
| NFR3 | Reliability — reproducibility | The pipeline shall give identical results with the same data, configuration, seed and environment. | Re-run preparation and training; compare SHA-256 of outputs and the metrics. | Hash / metric difference | Identical |
| NFR4 | Performance — latency | Scoring and ranking the evaluation set shall finish in a practical time. | Time end-to-end scoring and ranking of the full test set in the documented environment. | Seconds | ≤ 10 s |
| NFR5 | Correctness — ranking | The top of the ranked list shall contain a clearly higher share of promoted employees than random selection. | Rank the test set by predicted probability; take the top K = 10%. | Precision@10%, Lift@10% | Precision@10% ≥ 0.25; Lift@10% ≥ 3.0 |

**Why these thresholds:**

- **NFR1:** a random ranking gives PR-AUC equal to the base rate (0.085), so 0.35 means about 4× better than chance. ROC-AUC ≥ 0.75 sits just below the 0.79 that Alqahtani & Almaleh (2022) report with gender and age included, because TalentRank removes those inputs.
- **NFR5:** picking 10% of employees at random would give Precision@10% ≈ 0.085. A threshold of 0.25 (Lift ≥ 3.0) means at least one in four employees at the top of the list was actually recommended, which is worth an HR reviewer's time.
- **NFR4:** scoring and ranking the whole held-out test set within 10 seconds means HR gets the list without waiting, even on a free Colab runtime.

## Section 3 — Responsible AI Requirements (RAI)

| ID | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Bias and fairness | Gender and age shall not be model inputs, and the top of the ranked list shall not select any gender or age group at a substantially lower rate. | (a) Inspect the final input feature list. (b) Compute top-10% selection rate and recall per gender and per age band (20–29, 30–39, 40–49, 50–60). (c) Compare a model with vs without gender and age. | (a) Protected attributes used as inputs (b) Selection-rate ratio (four-fifths rule); recall gap | (a) 0 (b) Ratio ≥ 0.80; recall gap ≤ 10 percentage points. Any breach is reported and flagged for human review. |
| RAI2 | Privacy and data protection | Results and shared files shall not expose employee identifiers together with personal attributes beyond what the public dataset already contains, and no data shall be sent to external services. | Inspect output files, result screens and the repository; check the code for external calls. | Unauthorised disclosures; external transfers | 0; 0 |
| RAI3 | Transparency and human oversight | Every ranking and explanation view shall state that results are model-generated decision support, not promotion decisions, and that explanations are not proof of real decision reasons. | Inspect every result view and exported report. | Views with both statements (%) | 100% |

**Risk → Control → Evidence**

| Risk | Engineered control | Evidence |
|---|---|---|
| Biased ranking | Gender and age excluded from inputs; fairness audit of the top-10% list | Feature list; four-fifths ratios and recall gaps |
| Privacy exposure | No external services; no extra personal data in outputs | Code and output inspection |
| Over-reliance | Disclaimer and explanation-limit statement on every view | Screenshots of all views |

**Why gender and age are excluded from model inputs:** both are protected characteristics, and the historical promotion rate falls with age (about 9.1% at 30–39 vs 6.6% at 50–60), which a model would learn as an age penalty. Both stay in the data **only for auditing**, because removing them does not remove bias carried by related features such as length of service. Region stays as an input but is included in the fairness audit as a possible proxy.

---

### Reflection

**Which Responsible AI requirement will be the most difficult to measure or enforce technically? Why?**

RAI1 is the hardest. Removing gender and age from the inputs is easy to check, but bias can still enter through related features such as length of service, department or region, and the historical labels may already contain organisational bias. Passing the four-fifths check on the test set only shows that the ranked list does not select groups at very different rates on this dataset; it does not prove that past decisions were fair. RAI1 therefore uses several metrics plus a with/without comparison, and any breach is sent for human review instead of being corrected automatically.
