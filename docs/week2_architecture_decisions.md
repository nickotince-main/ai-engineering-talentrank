# Week 2 Architecture Decisions — TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

**Project:** TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking
**Version:** 1.0 · Diagram: `docs/week2_architecture_v1.png`

## 1. Start Simple (Step C1)

**Simplest architecture:** HR User → TalentRank Interface → Promotion Prediction Capability → Probability for each employee.

| Requirement | Can the simple flow satisfy it? | What is missing |
|---|---|---|
| FR1, FR2 | No | Inspected and prepared employee data |
| FR4, NFR5 | No | An ordered list and a way to evaluate it |
| FR5, NFR2 | No | Explanations of each prediction |
| RAI1 | No | A control that keeps gender/age out and checks the list |
| RAI3 | No | A boundary on how results are used |

## 2. Architecture Needs from Requirements (Step C2)

| Requirement ID | What does the requirement need? | Architecture Response |
|---|---|---|
| FR1, FR2 | Load, inspect and analyse employee records | **Employee Data Processing** — loading, inspection, feature analysis, preparation |
| FR3, NFR1 | Predict promotion probability and measure it on unseen records | **Machine Learning Model** + **Model Evaluation** (held-out test set) |
| FR4, NFR5 | An ordered list whose top part is evaluated | **Employee Ranking** + ranking metrics in **Model Evaluation** |
| FR5, NFR2 | Global and individual explanations that are stable | **Prediction Explainability** + stability check in **Model Evaluation** |
| NFR3 | Repeatable results | Run records with data fingerprints, configuration and seed |
| NFR4 | Practical processing time | Timing point in **Model Evaluation** |
| RAI1 | Gender/age not used as inputs; fair top-of-list selection | **Responsible AI Controls** — protected-attribute filter + fairness audit of the ranked list |
| RAI2 | No exposure of employee data beyond the public dataset; no external transfers | **Responsible AI Controls** — privacy and data protection |
| RAI3 | Results presented as decision support only | **Responsible AI Controls** — transparency and human oversight on **TalentRank Results** |

## 3. Architecture Decision Records (ADR)

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| Use a structured employee dataset as the data source | FR1–FR3 need labelled historical records | 1. Public structured CSV 2. Organisation HR database 3. Manually entered records | Public structured CSV | Easy to inspect and reproduce, but static, no dates, and licence not verified. |
| Rank employees with a pointwise approach | FR3, FR4, NFR5 | 1. Manual ranking rules 2. Pointwise: a classifier predicts each employee's probability and the list is sorted by it 3. Pairwise/listwise learning-to-rank (Bitkuri et al., 2025) | Pointwise ranking | Simple, gives a probability per employee that can be explained, and is evaluated with Precision@K. Does not optimise list order directly; pairwise/listwise methods need grouped queries this single-list dataset does not have. |
| Include an explainability capability | FR5, NFR2, RAI3 | 1. Probabilities only 2. Global importance only 3. Global + individual explanations | Global + individual explanations | More transparency for HR, but more complexity, and explanations show model behaviour, not real decision reasons. |
| Exclude protected attributes from model inputs | RAI1, FR3 | 1. Use all features 2. Drop gender/age completely 3. Exclude from inputs but keep for auditing | Option 3 | Possible small loss of predictive signal; bias can still enter through related features, so a fairness audit is still required. |
| Keep human oversight | RAI1–RAI3; promotion is a high-risk use | 1. Automatic approve/reject 2. Results for human review | Results for human review only | Slower than automation, but final decisions stay with accountable people. |

## 4. Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | FR1, FR2 | Inspection correctness; feature investigation | Employee Data Processing | Week 3 notebook, data inventory, feature analysis tables |
| O2 | FR3, NFR1 | Prediction performance | Machine Learning Model; Model Evaluation | PR-AUC, ROC-AUC, F1 on the test set |
| O2 | FR4, NFR5 | Ranking quality | Employee Ranking; Model Evaluation | Precision@10%, Lift@10% |
| O2, O3 | FR5, NFR2 | Explanation coverage and stability | Prediction Explainability; Model Evaluation | Explanation coverage; top-5 overlap across 3 bootstrap resamples |
| O3 | NFR4 | Processing time | Model Evaluation (timing point) | Timing results |
| O3 | NFR3 | Reproducibility check (engineering control) | Run records (data fingerprints, configuration, seed) | Hash and metric comparison of two identical runs |
| O3 | RAI1 | Fairness of the ranked list | Responsible AI Controls | Four-fifths ratios, recall gaps, with/without comparison |
| O2 | RAI2, RAI3 | Privacy; disclaimer coverage | Responsible AI Controls; TalentRank Results | Output inspection; screenshots |

## 5. Place in the AI Engineering Stack

| Layer | TalentRank components |
|---|---|
| Application development | Interface, ranking, explanation views, disclaimers, fairness audit, evaluation |
| Model development | Data preparation, promotion classifier, explainability method, model selection |
| Infrastructure | Local/Colab compute, file storage, manifests and run records (monitoring in later topics) |

The model is only one component. The engineering work is the system around it — measurable requirements, data lineage, quality gates, ranking and fairness evaluation, and human-oversight controls.

## 6. Reflection

**Which architecture decision involved the most important trade-off? Which requirement drove it, and what trade-off was accepted?**

Excluding gender and age from the model inputs while keeping them for auditing (driven by RAI1). It gives up a little predictive signal and does not remove bias by itself, because related features can still carry it. The accepted trade-off is extra evaluation work: every model result must include a fairness audit of the ranked list and a comparison with a model that uses all features.
