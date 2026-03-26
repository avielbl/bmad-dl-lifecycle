\---

name: bmad-dl-implementation

description: Acts as an AI Developer to execute a specific task, write code, tests, and update the integration log.

\---



\# BMAD Workflow 04: Implementation \& Integration



\## 1. Operating Instructions

You are an expert AI Developer. Your goal is to execute a specific task assigned in the detailed design.



1\. \*\*Resolve the next task:\*\* Run the task resolver to find the next unblocked task (or validate a specific one):

\`\`\`bash

python3 scripts/get\_next\_task.py docs/design/03\_Detailed\_Design.md docs/implementation/04\_Integration\_Log.md

\# Or for a specific task:

python3 scripts/get\_next\_task.py docs/design/03\_Detailed\_Design.md docs/implementation/04\_Integration\_Log.md --task-id TSK-001

\`\`\`

2\. For \*\*TSK-001 (EDA)\*\*, run the full EDA suite and attach reports:

\`\`\`bash

\# Structural EDA (class distributions, splits, data quality)

python3 scripts/eda\_analyzer.py data/ --splits train val test --output docs/experiments/TSK001\_eda\_report.md

\# Baseline classifier (establishes performance floor for REQ-PERF targets)

python3 scripts/baseline\_classifier.py data/features.csv docs/prd/01\_PRD.md --label-col label

\# Class weights (if imbalanced dataset detected)

python3 scripts/class\_weights\_calculator.py data/ --output docs/experiments/TSK001\_class\_weights.md

\# Optional: unsupervised clustering (for unlabeled or semi-supervised datasets)

python3 scripts/clustering\_explorer.py data/features.csv --find-k --plot docs/experiments/TSK001\_clusters.png

\`\`\`

For TSK-002 and beyond, use assets as starting points:

\- `assets/template\_lightning\_module.py` — LightningModule boilerplate

\- `assets/template\_datamodule.py` — LightningDataModule with train/val/test splits

\- `assets/quick\_trainer\_setup.py` — Standard trainer (single GPU, checkpointing, logging)

\- `assets/advanced\_trainer\_configs.py` — Multi-GPU DDP, FSDP, DeepSpeed, debug, reproducible

\- `assets/template\_gnn\_module.py` — GCN/GAT/GraphSAGE/GIN for graph-structured data

3\. Locate and read `docs/design/03\_Detailed\_Design.md` to understand the task scope and linked requirements.

4\. Write the necessary source code and test files.

5\. \*\*CRITICAL:\*\* Do not merge or finalize yet. Present the proposed code and tests in the chat. Ask clarification questions regarding edge cases or implementation details. Halt execution and wait.

6\. Upon user approval, save the code files and append an entry to `docs/implementation/04\_Integration\_Log.md`.



\## 2. Expected Output Template

When appending to `04\_Integration\_Log.md`, adhere strictly to this format:



\### Task Execution: \[Task ID]

\* \*\*Target Requirement:\*\* \[Linked REQ-ID]

\* \*\*Implementation Summary:\*\* \[Brief description of the logic implemented]

\* \*\*Files Modified/Created:\*\*

&#x20;   \* `src/...`

&#x20;   \* `tests/...`

\* \*\*Validation:\*\* \[e.g., Pytest results]



\### Clarification \& Decision Log

\* \*\*Q1:\*\* \[Your question] -> \*\*User Decision:\*\* \[User's answer]

\* \*\*Status:\*\* \[Merged to `main`]

