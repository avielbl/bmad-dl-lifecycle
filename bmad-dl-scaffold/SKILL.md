\---

name: bmad-dl-scaffold

description: Re-scaffolds an existing DL project — adds missing folders, rewrites the IDE config file (.clinerules or .claude/CLAUDE.md), and initializes uv if pyproject.toml is absent. For new projects, run init_project.py directly from the shell instead of invoking this skill.

\---



\# BMAD Workflow 00: Project Scaffolding



\## When to use this skill vs the shell command

\| Situation \| What to do \|
\| :--- \| :--- \|
\| **New project, no folder yet** \| Run `init_project.py` directly from the shell — see README Getting Started \|
\| **Antigravity** (any situation) \| `/bmad-dl-scaffold` works — Antigravity discovers this skill globally \|
\| **Existing project, want to add missing folders or rewrite `.clinerules`** \| Invoke this skill — it skips what already exists \|

For Claude Code and Cline on a **brand-new project**, the skill cannot be invoked before the project exists. Run the shell command first, then use slash commands (Claude Code) or reference SKILL.md paths (Cline) for all subsequent stages.



\## Shell command (primary method for new projects)

\`\`\`bash

python3 _bmad/bmad-dl-lifecycle/bmad-dl-scaffold/scripts/init_project.py \
  --project-name my_project \
  --project-dir . \
  --ide cline \           # cline | claude-code | antigravity | cursor
  --tracking-tool wandb   # wandb | mlflow | clearml | undecided

\`\`\`

For \`--ide claude-code\`, this also copies all skills into \`.claude/skills/\` so slash commands work immediately.



\## 1. Operating Instructions (Antigravity / re-scaffold use)

You are a project scaffolding assistant. Your job is to set up or repair the project structure so that every subsequent skill has a consistent, predictable workspace. **No training, no modelling, no package installations happen here.**



1\. **Ask the user for the following if not already provided:**

   \- `project_name`: Snake_case name (e.g. `pneumonia_classifier`)
   \- `project_dir`: Where the project lives (default: current working directory)
   \- `ide`: `claude-code` / `antigravity` / `cline` / `cursor`
   \- `tracking_tool`: `wandb` / `mlflow` / `clearml` / `undecided`



2\. **Run the scaffolding script:**

\`\`\`bash

python3 _bmad/bmad-dl-lifecycle/bmad-dl-scaffold/scripts/init_project.py \
  --project-name "{project_name}" \
  --project-dir "{project_dir}" \
  --ide "{ide}" \
  --tracking-tool "{tracking_tool}"

\`\`\`



3\. **Verify output** — confirm with the user that the following are present:

   \- All folders: `docs/`, `data/`, `src/`, `tests/`, `scripts/`, `logs/`, `notebooks/`, `configs/`
   \- IDE config: `.clinerules` or `.claude/CLAUDE.md`
   \- For Claude Code: `.claude/skills/bmad-dl-*/` (enables slash commands)
   \- `pyproject.toml` with empty `dependencies = []`
   \- `configs/llm_config.yaml`



4\. **CRITICAL — no package installations yet.** Package requirements are determined in **Stage 1 (bmad-dl-ideation)**. Installations run in **Stage 5 (bmad-dl-infra)** via `uv sync`.



5\. **Tell the user how to invoke Stage 1** based on their IDE:

   \- Antigravity / Claude Code: `/bmad-dl-ideation`
   \- Cline / Cursor: "Tell the AI: follow the workflow in `_bmad/bmad-dl-lifecycle/bmad-dl-ideation/SKILL.md`"



\## 2. Expected project structure after scaffolding

\`\`\`

{project_name}/
├── pyproject.toml          ← uv project, NO dependencies yet
├── .python-version
├── .gitignore
├── .clinerules             ← or .claude/CLAUDE.md + .claude/skills/ for Claude Code
├── configs/
│   └── llm_config.yaml    ← for programmatic LLM calls only
├── docs/
│   ├── prd/ eda/ architecture/ design/ techspecs/
│   ├── implementation/ experiments/ revisions/ knowledge/
├── data/
│   ├── raw/ processed/ splits/
├── src/{project_name}/__init__.py
├── tests/__init__.py
├── scripts/               ← bmad-dl utility scripts copied here
├── logs/
├── notebooks/
└── configs/

\`\`\`
