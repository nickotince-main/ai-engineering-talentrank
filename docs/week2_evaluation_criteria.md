# Week 2 Evaluation Criteria — TalentRank

**Project:** TalentRank: Explainable Machine Learning for Employee Promotion Prediction and Ranking

**Version:** 1.0

Thresholds are proposed engineering targets. They must not be reported as achieved until testing is complete.

Capability buckets follow the Topic 2 lecture:

- **Domain-specific:** can the system handle HR promotion data and domain rules?
- **Generation:** are its outputs correct and faithful? For TalentRank, the generated outputs are explanations, not text.
- **Instruction-following:** does it obey scope and format rules?
- **Cost and latency:** is the processing time acceptable?

NFR3 (reproducibility) is checked as an engineering control (see `week2_architecture_decisions.md`) and is not listed here, because it is not a capability of the application. The lab allows evaluation to focus on a small set of important requirements.

| Objective / Requirement ID | Evaluation Criterion | Capability Bucket | Measurement Method | Metric | Threshold | User / Business Outcome |
|---|---|---|---|---|---|---|
| O1 / FR1 | Dataset inspection correctness | Domain-specific | Run the inspection pipeline; check reported counts against the source CSV. | Inspection checks matching source (%) | 100% | Data can be trusted before modelling. |
| O1 / FR2 | Feature investigation completeness | Domain-specific | Promotion rate and summary statistics for every model input feature. | Features analysed / features in scope | 9 / 9 | Users see which recorded factors are linked to promotion. |
| O2, O3 / FR3 / NFR1 | Prediction performance | Domain-specific | Evaluate once on a stratified held-out test set. | PR-AUC (supporting: ROC-AUC, precision, recall, F1) | PR-AUC ≥ 0.35; ROC-AUC ≥ 0.75 | Predictions add value above the 8.52% base rate. |
| O2, O3 / FR4 / NFR5 | Ranking quality | Domain-specific | Sort test employees by predicted probability; evaluate the top K = 10%. | Precision@10% = promoted employees in top K ÷ K; Lift@10% = Precision@10% ÷ base rate | Precision@10% ≥ 0.25; Lift@10% ≥ 3.0 | HR reviewers see more relevant employees first. |
| O2 / FR5 / NFR2(a) | Explanation coverage | Instruction-following | Check that every ranked employee shown has an individual explanation. | Coverage (%) | 100% | No employee appears in the list without a reason HR can check. |
| O3 / NFR2(b) | Explanation stability | Generation | Retrain on 3 stratified bootstrap resamples of the training set; compare the global top-5 features. | Top-5 feature overlap (%) | ≥ 80% | Explanations do not change randomly when the training data changes slightly, so users can trust them. |
| O3 / NFR4 | Processing time | Cost and latency | Time scoring and ranking of the full test set. | Seconds | ≤ 10 s | Results are available without waiting. |
| O3 / RAI1 | Fairness of the ranked list (four-fifths rule from employee-selection guidelines) | Domain-specific | Top-10% selection rate and recall per gender and age band; with vs without gender/age. | Four-fifths ratio; recall gap | ≥ 0.80; ≤ 10 pp | Lower risk of ranking a group systematically lower. |
| O2 / RAI1 | Protected attributes excluded | Instruction-following | Inspect the final feature list. | Protected attributes used as inputs | 0 | Gender and age cannot directly drive rankings. |
| O1 / RAI2 | Privacy | Instruction-following | Inspect outputs, screens, repository and code. | Disclosures; external transfers | 0; 0 | Employee information is not exposed further. |
| O2 / RAI3 | Human-oversight disclaimer | Instruction-following | Inspect every ranking and explanation view. | Views with disclaimer (%) | 100% | Users understand the system does not decide promotions. |

**About Precision@K:** Precision@K is the share of the top K items that are relevant — here, the share of the top 10% ranked employees who were actually recommended for promotion. It ignores order inside the top K, gives no credit to relevant items ranked below K, and depends on the chosen K (Murrell, 2025). TalentRank therefore fixes K = 10% before testing and reports Lift@10% and PR-AUC alongside it.

---

### Reflection

**Which evaluation criterion is most important for determining whether the application will be useful to its intended users? Why?**

Ranking quality (Precision@10%) matters most, because HR users review the top of the list rather than every individual probability. Accuracy is not useful here: published studies on this dataset report 94–96% accuracy, but predicting "not promoted" for everyone already scores 91.5%. A useful list is still not enough on its own — the fairness of the same top-10% list (RAI1) must be checked alongside it, and explanation stability decides whether users can trust why an employee appears there.
