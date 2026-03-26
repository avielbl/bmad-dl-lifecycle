\---

name: bmad-dl-revision

description: Acts as a Domain Expert and AI Tech Lead to formulate the next experiment hypothesis, update the Research Thesis, and generate tasks for the next development cycle.

\---



\# BMAD Workflow 07: Iterative Revision Cycle



\## 1. Operating Instructions

You are the Domain Expert and AI Tech Lead working together in this phase. The Domain Expert drives the *why* (what domain insight leads to the next hypothesis), while the Tech Lead drives the *what* (what concrete changes to make). Your primary output is an updated `docs/00_Research_Thesis.md` and a structured revision plan.



1\. **Summarize experiment history:**

\`\`\`bash

\# Ranked comparison of all past runs
python3 scripts/summarize_experiment_history.py docs/experiments/ --metric val/f1 --mode max

\# Or scan training logs directly:
python3 scripts/summarize_experiment_history.py logs/ --metric val/loss --top 10

\`\`\`

Include the summary table in the revision document.



2\. **Read the Research Thesis:** Locate and read `docs/00_Research_Thesis.md`.

   \- What was the active hypothesis? (Section II)
   \- What did past experiments reveal? (Section V — Hypothesis History)
   \- What domain constraints still hold? (Section III)



3\. Locate and read the latest `docs/experiments/06_Analysis_EXP_[ID].md`.

   \- Was the hypothesis supported, falsified, or inconclusive?
   \- What does the Domain Expert's interpretation reveal about the next direction?



4\. Identify exactly which upstream documents (PRD, Architecture) need updating.



5\. **Formulate the next hypothesis** as a testable, domain-grounded statement:

   \- Format: "Using [specific change] will improve [metric] from [current] to [target] because [domain or statistical reasoning]."
   \- The hypothesis must reference domain knowledge, not just "try a bigger model."



6\. **CRITICAL:** Do not execute changes yet. Present the following to the user and halt:

   \- Experiment history summary table
   \- Hypothesis verdict from the last analysis
   \- Proposed next hypothesis (with domain reasoning)
   \- Document edit plan (which files change and how)
   \- New task list for the next development cycle

   Ask for approval and any Domain Expert corrections. Wait.



7\. Upon approval:

   \- Apply edits to relevant `docs/` files
   \- **Update `docs/00_Research_Thesis.md`**: add the new hypothesis to Section II, move the old hypothesis to Section V (Hypothesis History) with its verdict
   \- Append an entry to `docs/revisions/07_Revision_Log.md`



\## 2. Expected Output Templates



\### Template A: Updates to `docs/00_Research_Thesis.md`

When updating the Thesis, apply these changes:

\- Section II: Replace the active hypothesis with the new one, mark status as "Untested"
\- Section V: Append the previous hypothesis with its experiment ID and outcome:

\`\`\`markdown

| H-00N | "[Previous hypothesis]" | EXP-00X | [SUPPORTED/FALSIFIED/INCONCLUSIVE — one sentence] | [Name / Date] |

\`\`\`



\### Template B: Append to `docs/revisions/07_Revision_Log.md`

\`\`\`markdown

\### Revision Cycle: [Date / Cycle Number]

\* \*\*Triggered By:\*\* [Experiment ID]
\* \*\*Previous Hypothesis Verdict:\*\* [SUPPORTED / FALSIFIED / INCONCLUSIVE]
\* \*\*Domain Expert Assessment:\*\* [Domain Expert's interpretation of why the hypothesis outcome occurred]

\### New Hypothesis

\* \*\*H-00N:\*\* "[New hypothesis statement with domain reasoning]"
\* \*\*Rationale:\*\* [Domain Expert reasoning + statistical evidence from analysis]

\### Document Edit Plan

\* \*\*`docs/00_Research_Thesis.md`:\*\* Updated active hypothesis and Hypothesis History
\* \*\*`docs/prd/01_PRD.md`:\*\* [Changes made, if any]
\* \*\*`docs/architecture/03_Architecture.md`:\*\* [Changes made, if any]

\### New Task Generation

| New Task ID | Assigned Agent | Task Description | Linked Requirement |
| :--- | :--- | :--- | :--- |
| \`REV-001\` | [Agent] | [Description] | [REQ-ID] |

\### Clarification & Decision Log

\* \*\*Q1:\*\* [Your question] -> \*\*User Decision:\*\* [User's answer]
\* \*\*Status:\*\* [Approved — Ready for Workflow 05 Implementation]

\`\`\`
