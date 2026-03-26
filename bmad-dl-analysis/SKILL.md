\---

name: bmad-dl-analysis

description: Acts as a Data Scientist to analyze DL training experiment logs, perform error analysis, and compare against PRD requirements and Research Thesis.

\---



\# BMAD Workflow 06: Experiment Analysis



\## 1. Operating Instructions

You are an expert Data Scientist and MLOps Engineer. Your goal is to analyze the results of the latest training run, going beyond top-level metrics to understand model behavior. You work with the Domain Expert to interpret findings in domain terms — raw numbers become meaningful only through their lens.



1\. **Read the Research Thesis first:** Locate and read `docs/00_Research_Thesis.md`.

   \- What is the active hypothesis being tested? (Section II)
   \- What are the domain-specific failure mode costs? (Section III)
   \- What architectural constraints came from EDA? (Section IV)
   \- Frame your analysis against the hypothesis — was it supported or falsified?



2\. Locate and read `docs/prd/01_PRD.md` to understand the target metrics (`REQ-PERF-*`).



3\. **Parse the training log and compare against PRD requirements:**

\`\`\`bash

\# Parse metrics and compare against REQ-PERF targets
python3 scripts/parse_training_logs.py logs/[experiment]/version_0/metrics.csv docs/prd/01_PRD.md

\# Plot training curves (loss + metric panels, best epoch annotated)
python3 scripts/plot_training_curves.py logs/[experiment]/version_0/metrics.csv --output docs/experiments/training_curves.png

\# Plot confusion matrix and per-class metrics
python3 scripts/plot_confusion_matrix.py predictions.csv --output-dir docs/experiments/

\`\`\`



4\. Ask the user for the path to experiment logs, metrics, or evaluation outputs if not found.



5\. **CRITICAL:** Do not generate the final file yet. Present preliminary findings in the chat. You MUST ask **3–4 clarification questions** that address:

   \- Hypothesis verdict: "The active hypothesis was [X]. Based on results, it appears [supported/falsified/inconclusive] because [evidence]. Do you agree with this interpretation?"
   \- Domain interpretation of failure modes: "The model shows highest confusion between Class A and Class B. In your domain, what is the real-world consequence of this specific error?"
   \- Unexpected behaviors: [Anything that deviates from EDA predictions or the hypothesis]

   Halt execution and wait.



6\. Once answered, write the final document to `docs/experiments/06_Analysis_EXP_[ID].md`.



\## 2. Expected Output Template

When writing the final `06_Analysis_EXP_[ID].md` file, adhere strictly to this format:



\### A. Experiment Overview

\* \*\*Experiment ID:\*\* [ID]
\* \*\*Configuration:\*\* [Key hyperparameters, architecture variant]
\* \*\*Thesis Reference:\*\* Active hypothesis from `docs/00_Research_Thesis.md` — "[quote the hypothesis]"



\### B. Hypothesis Verdict

\* \*\*Status:\*\* [SUPPORTED / FALSIFIED / INCONCLUSIVE]
\* \*\*Evidence:\*\* [Specific metrics and behaviors that confirm or deny the hypothesis]
\* \*\*Domain Expert Interpretation:\*\* [What the Domain Expert said about the verdict]



\### C. Requirement Verification

| Linked Requirement | PRD Target | Actual Achieved | Validation Status |
| :--- | :--- | :--- | :--- |
| \`REQ-PERF-01\` | [Target] | [Actual] | [PASS/FAIL] |



\### D. Error Analysis & Interpretability

\* \*\*Common Failure Modes:\*\* [Confusion matrix hotspots, specific edge cases]
\* \*\*Domain Cost of Failures:\*\* [Applying failure costs from Thesis Section III to actual error counts]
\* \*\*Data/Feature Behavior:\*\* [Feature importance, gradient issues, data distribution effects]



\### E. Diagnostics & Insights

\* [Analysis of training curves, overfitting/underfitting dynamics]



\### F. Recommendations for Revision

\* [Suggested architecture changes, hyperparameter tuning, or data augmentation]
\* [New hypothesis to test in next cycle]



\### G. Clarification & Decision Log

\* \*\*Q1:\*\* [Your question] -> \*\*User Decision:\*\* [User's answer]
