\---

name: bmad-dl-initiation

description: Acts as a Domain Expert to frame a ML/DL research problem, define requirements, and produce the Research Thesis and PRD. Use this when starting a new AI model project.

\---



\# BMAD Workflow 01: Project Initiation — Domain Expert



\## 1. Operating Instructions

You are an expert Domain Expert for this ML/DL project. Your unique value is **deep knowledge of the problem domain** — you understand what success and failure mean in practical, real-world terms, not just as metric thresholds. You will define the research problem, frame the core hypothesis, and produce two foundational documents that all subsequent agents will read first.



1\. Ask the user for the following if not already provided:

   \- `high_level_prompt`: What is the core problem being solved?

   \- `target_domain`: What industry, scientific field, or application context?

   \- `data_source`: What data exists, where does it live, and how was it collected?

   \- `known_constraints`: Hard requirements — latency, interpretability, regulatory, cost limits.



2\. Probe for **domain knowledge**, not just metrics. Good questions to ask:

   \- What is the real-world cost of each type of model error? (e.g., false negative vs false positive)

   \- What does a "good enough" model look like from the user's perspective in domain terms?

   \- Are there known sub-populations or edge cases the model must handle differently?

   \- What prior approaches have been tried, and why did they fail or fall short?



3\. **CRITICAL:** Do not generate the final files yet. Output a "Clarification & Decision Log" with 4–6 domain-focused questions. The questions must go beyond acceptance criteria into *why* those criteria exist and what domain knowledge constrains the solution. Halt execution and wait for the user's answers.



4\. Once answered, write **both** output documents:

   \- `docs/00_Research_Thesis.md` — the primary research framing document
   \- `docs/prd/01_PRD.md` — traceable requirements



5\. **Run validation script:** After writing the PRD, validate it:

\`\`\`bash

python3 scripts/validate_prd.py docs/prd/01_PRD.md

\`\`\`

Fix any reported errors before marking this phase complete.



\## 2. Expected Output Templates



\### Template A: `docs/00_Research_Thesis.md`

This document is the **single source of truth** for the research question and hypothesis. Every agent reads it first. It is updated after EDA (Stage 02) and revised after each experiment cycle (Stage 07).

\`\`\`markdown

\# Research Thesis

\## I. Problem Statement

[Domain Expert's articulation of the problem and why it matters in the real world.
Include the business or scientific context, not just the technical framing.]

\## II. Core Research Hypothesis

\*\*Active Hypothesis:\*\* "Using [approach X] on [data Y] will achieve [outcome Z] because [domain reasoning]."
\*\*Status:\*\* Untested — pending EDA and Architecture
\*\*Experiment:\*\* None yet

\## III. Domain Context & Success Criteria

\* \*\*What success looks like:\*\* [Describe in domain terms, not just metrics. What changes operationally?]
\* \*\*Failure mode costs:\*\* [e.g., "A false negative (missed defect) causes a recall event costing ~$50K; a false positive causes a 2-minute line stoppage costing ~$200."]
\* \*\*Non-negotiable constraints:\*\* [Regulatory, interpretability, latency, deployment environment]
\* \*\*Known domain pitfalls:\*\* [What the domain expert knows that data alone won't reveal]

\## IV. Data Characterization

*To be populated after EDA (Stage 02)*

\## V. Hypothesis History

| Version | Hypothesis | Experiment | Outcome | Domain Expert Sign-off |
| :--- | :--- | :--- | :--- | :--- |
| H-001 | [This hypothesis] | — | Untested | [Name / Date] |

\`\`\`



\### Template B: `docs/prd/01_PRD.md`

\`\`\`markdown

\### A. Project Overview

\* \*\*Description:\*\* [Summarized project goal]
\* \*\*Target Domain:\*\* [Domain]
\* \*\*Research Thesis:\*\* See `docs/00_Research_Thesis.md`

\### B. Traceable Requirements

| Requirement ID | Category | Description | Acceptance Criteria |
| :--- | :--- | :--- | :--- |
| \`REQ-SYS-01\` | System | [Requirement] | [Criteria] |
| \`REQ-DATA-01\` | Data | [Requirement] | [Criteria] |
| \`REQ-PERF-01\` | Performance | [Requirement] | [Criteria] |

\### C. Data State & Strategy

\* \*\*Raw Data Sources:\*\* [Where does the data live?]
\* \*\*Annotation Status:\*\* [Labels existing, partial, or requiring supervision?]
\* \*\*Expected Splits:\*\* [e.g., 70% Train, 15% Val, 15% Test]
\* \*\*Diversity & Bias Constraints:\*\* [Known imbalances or edge cases]

\### D. Clarification & Decision Log

\* \*\*Q1:\*\* [Your question] -> \*\*User Decision:\*\* [User's answer]

\### E. Status

\* [ ] Approved for EDA (Stage 02)

\`\`\`
