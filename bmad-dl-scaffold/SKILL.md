\---

name: bmad-dl-scaffold

description: Scaffolds a new DL project — creates folder structure, .clinerules, and initializes a uv project with a skeleton pyproject.toml. Run once before any other bmad-dl skill. No packages are installed; package selection happens in bmad-dl-ideation (Stage 1) and installation happens in bmad-dl-infra (Stage 5).

\---



\# BMAD Workflow 00: Project Scaffolding



\## 1. Operating Instructions

You are a project scaffolding assistant. Your job is to set up the empty project structure so that every subsequent skill has a consistent, predictable workspace to write into. **No training, no modelling, no package installations happen here.** You are laying the foundation.



1\. **Ask the user for the following if not already provided:**

   \- `project_name`: Short snake_case name for the project directory (e.g. `pneumonia_classifier`)
   \- `project_dir`: Absolute path where the project should be created (default: current working directory)
   \- `ide`: Which IDE will be used? (`claude-code` / `antigravity` / `cline` / `cursor`) — determines which config file to write
   \- `tracking_tool`: Which experiment tracker? (`wandb` / `mlflow` / `clearml` / `undecided`) — added to `.clinerules` note; can be changed in Architecture stage



   \- `llm_script_provider`: If utility scripts in this project will call an LLM programmatically (e.g., auto-summarize logs, auto-interpret results), which provider should they use? (`anthropic` / `openai-compatible`) — default: `anthropic`. Note: this is **not** the model running these skill prompts; that is set in your IDE.



2\. **Run the scaffolding script:**

\`\`\`bash

python3 scripts/init_project.py \
  --project-name "{project_name}" \
  --project-dir "{project_dir}" \
  --ide "{ide}" \
  --tracking-tool "{tracking_tool}"

\`\`\`

This script creates all folders, writes `.clinerules` (or `.claude/CLAUDE.md` for Claude Code), and runs `uv init` to create a `pyproject.toml` with no dependencies yet.



3\. **Verify output** — confirm with the user that the following were created:

   \- All folders under `docs/`, `data/`, `src/`, `tests/`, `scripts/`, `logs/`, `notebooks/`
   \- `.clinerules` (or `.claude/CLAUDE.md`) pointing to all bmad-dl skill files
   \- `pyproject.toml` with project name and Python version, **no dependencies**
   \- `.python-version` file pinning Python (default: 3.11)
   \- `.gitignore` covering common DL artifacts



4\. **CRITICAL — no package installations yet.** The `pyproject.toml` is intentionally empty. Package requirements are determined in **Stage 1 (bmad-dl-ideation)** based on the project's domain and architecture choices. Installations run in **Stage 5 (bmad-dl-infra)** via `uv sync`. Inform the user of this flow.



5\. **Instruct the user on next steps:**

\`\`\`

Project scaffold complete. Next:

  1. /bmad-dl-ideation  ← Domain Expert frames the problem + determines package requirements
  2. /bmad-dl-eda         ← Data Scientist explores the data
  3. /bmad-dl-architecture ← AI Architect picks the model stack
  4. /bmad-dl-detailed-design ← Tech Lead breaks work into INF-* and EXP-* tasks
  4.5 /bmad-dl-techspec   ← Pre-experiment contract with Domain Expert sign-off
  5. /bmad-dl-infra       ← Developer builds infra + runs: uv sync
  ...
\`\`\`



\## 2. Expected Output

After running this skill the project root contains:

\`\`\`

{project_name}/
├── pyproject.toml          ← uv project, NO dependencies yet
├── .python-version         ← e.g. "3.11"
├── .gitignore
├── .clinerules             ← (or .claude/CLAUDE.md for Claude Code)
├── docs/
│   ├── prd/
│   ├── eda/
│   ├── architecture/
│   ├── design/
│   ├── techspecs/
│   ├── implementation/
│   ├── experiments/
│   ├── revisions/
│   └── knowledge/
├── data/
│   ├── raw/
│   ├── processed/
│   └── splits/
├── src/
│   └── {project_name}/
│       └── __init__.py
├── tests/
│   └── __init__.py
├── scripts/
│   └── [bmad-dl utility scripts copied here]
├── logs/
├── notebooks/
└── configs/

\`\`\`
