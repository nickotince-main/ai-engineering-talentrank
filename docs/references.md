# References — TalentRank

APA 7th style.

## Research papers (main ideas for TalentRank)

| # | Reference | Idea used in TalentRank |
|---|---|---|
| R1 | Alqahtani, F. A., & Almaleh, A. (2022). Analysis and prediction of employee promotions using machine learning. *2022 5th International Conference on Data Science and Information Technology (DSIT)*, IEEE. https://doi.org/10.1109/DSIT55514.2022.9943959 | Problem evidence; promotion prediction with classical classifiers (Gradient Boosting best, AUC 79.28%); previous-year rating as a key feature |
| R2 | Bhattacharya, A., Choudhary, P., Mukhopadhyay, S., Misra, B., Chakraborty, S., & Dey, N. (2023). Explainable AI for predictive analytics on employee promotion. *2023 International Conference on Advanced Computing Technologies and Applications (ICACTA)*, IEEE. https://doi.org/10.1109/ICACTA58201.2023.10393141 | Explaining promotion predictions with SHAP and LIME (global and individual explanations) |
| R3 | Benabou, A., & Touhami, F. (2026). Optimizing employee promotion predictions using machine learning. *International Journal of Production Management and Engineering, 14*(1), 16–33. https://doi.org/10.4995/ijpme.2026.23364 | Comparing several models; handling class imbalance only inside training folds to avoid leakage |
| R4 | Bitkuri, V., Kendyala, R., Madhavaram, C. R., Gollangi, H. K., & Bauskar, S. R. (2025). A review of machine learning ranking systems: Methods, applications, and challenges. *Journal of Contemporary Education Theory & Artificial Intelligence, 4*(1), 100119. https://doi.org/10.47991/2996-4954/JCETAI-119 | Pointwise ranking; ranking metrics; fairness of rankings in hiring |

**TalentRank's position:** R1–R3 predict and explain but do not rank or audit fairness, and report accuracy (94–96%) on data where always predicting "not promoted" gives 91.5%. R4 explains how rankings should be evaluated but not for HR promotion. TalentRank combines prediction (R1, R3), explanation (R2) and ranking (R4) with a fairness audit.

## Evaluation and fairness

| # | Reference | Used in |
|---|---|---|
| E1 | Murrell, T. (2025, April 25). *Precision@K for ranking systems*. Tullie. https://tullie.ai/blog/precision-at-k-ranking | NFR5 — Precision@10% |
| E2 | U.S. Equal Employment Opportunity Commission. (1978). *Uniform guidelines on employee selection procedures*, 29 C.F.R. § 1607.4(D). https://www.govinfo.gov/content/pkg/CFR-2024-title29-vol4/pdf/CFR-2024-title29-vol4-sec1607-4.pdf | RAI1 — four-fifths rule (ratio ≥ 0.80) |
| E3 | European Commission. (n.d.). *AI Act Service Desk — Employment (Annex III, point 4)*. https://ai-act-service-desk.ec.europa.eu/en/node/464 | Evidence 2; decision-support boundary |
| E4 | *Personal Data Protection Act 2010* (Act 709), Malaysia, as amended by the *Personal Data Protection (Amendment) Act 2024*. | RAI2; Week 4 privacy review |

## Data sources

| # | Reference |
|---|---|
| D1 | Zaman, M. I. (n.d.). *Employees evaluation for promotion* [Data set]. Kaggle. https://www.kaggle.com/datasets/muhammadimran112233/employees-evaluation-for-promotion |
| D2 | Analytics Vidhya. (2018). *WNS Analytics Wizard 2018 — HR analytics* [Competition]. https://datahack.analyticsvidhya.com/contest/wns-analytics-hackathon-2018-1/ |

## Course texts

- Huyen, C. (2025). *AI engineering: Building applications with foundation models*. O'Reilly Media.
- Kleppmann, M., & Riccomini, C. (2026). *Designing data-intensive applications* (2nd ed.). O'Reilly Media.
