# bmad-dl-lifecycle

> Agentic workflows for the Deep Learning development lifecycle — a custom [BMAD Method](https://github.com/bmad-code-org) module.

This package provides seven structured AI workflow skills that guide a team through the full lifecycle of a machine learning project: from initial requirements and data exploration, through architecture and implementation, to experiment analysis and iterative revision. Each skill adopts a specific expert role, halts to ask clarifying questions before generating artifacts, and produces traceable, structured documents.

---

## Skills Overview

| # | Skill | Role | Output |
|---|-------|------|--------|
| 1 | `bmad-dl-initiation` | Domain Expert | `docs/00_Research_Thesis.md` + `docs/prd/01_PRD.md` |
| 2 | `bmad-dl-eda` | Data Scientist | `docs/eda/02_EDA_Report.md` |
| 3 | `bmad-dl-architecture` | AI Architect | `docs/architecture/03_Architecture.md` |
| 4 | `bmad-dl-detailed-design` | AI Tech Lead | `docs/design/04_Detailed_Design.md` |
| 5 | `bmad-dl-implementation` | AI Developer | `src/`, `tests/`, `docs/implementation/05_Integration_Log.md` |
| 6 | `bmad-dl-analysis` | Data Scientist | `docs/experiments/06_Analysis_EXP_[ID].md` |
| 7 | `bmad-dl-revision` | Domain Expert + Tech Lead | `docs/revisions/07_Revision_Log.md` + updates `docs/00_Research_Thesis.md` |

### The Research Thesis

`docs/00_Research_Thesis.md` is the **central document** for the entire lifecycle. The `00_` prefix ensures it is always the first file visible in the docs folder. It contains:

- The research question and core hypothesis (updated each revision cycle)
- Domain context and failure mode costs (set by the Domain Expert)
- Data characterization (populated after EDA)
- Hypothesis history (full record of tested hypotheses and their outcomes)

Every agent reads the Thesis as its first step. The Domain Expert maintains it.

### The Domain Expert Role

The initiation skill adopts the **Domain Expert** role — not a generic Product Manager. The Domain Expert brings deep knowledge of the problem domain: what the data represents scientifically or operationally, what failure modes cost in real terms, what prior approaches have been tried, and what "good enough" means beyond metric thresholds. This domain knowledge constrains every subsequent decision.

---

## Lifecycle Flow

The lifecycle follows a sequential planning phase (Stages 1–4) followed by an iterative experiment loop (Stages 5–7):

```
[1] Initiation (Domain Expert)
    ↓  docs/00_Research_Thesis.md + docs/prd/01_PRD.md
[2] EDA (Data Scientist) ← interprets findings with Domain Expert
    ↓  docs/eda/02_EDA_Report.md + updates Thesis Section IV
[3] Architecture (AI Architect) ← reads Thesis + EDA
    ↓  docs/architecture/03_Architecture.md
[4] Detailed Design (AI Tech Lead)
    ↓  docs/design/04_Detailed_Design.md
    ↓
[5] Implementation (AI Developer) ← reads EDA constraints
    ↓
[6] Analysis (Data Scientist) ← reads Thesis hypothesis
    ↓
[7] Revision (Domain Expert + Tech Lead) ← updates Thesis hypothesis
    ↓
    └──→ back to [5] with new hypothesis
```

---

## Prerequisites

- [BMAD Method](https://github.com/bmad-code-org/bmad-method) installed in your project (`_bmad/` folder present)
- An AI IDE: Claude Code, Antigravity, or VSCode + Cline
- A strong model recommended: Claude Sonnet 4.5+ or equivalent

---

## Installation

### Option A — Git Submodule (recommended for teams)

```bash
git submodule add https://github.com/avielbl/bmad-dl-lifecycle _bmad/bmad-dl-lifecycle
git submodule update --init
```

Then register the module in your project's `_bmad/_config/manifest.yaml`:

```yaml
modules:
  - name: bmad-dl-lifecycle
    version: 1.1.0
    source: external
    repoUrl: https://github.com/avielbl/bmad-dl-lifecycle
```

To pull future updates:

```bash
git submodule update --remote _bmad/bmad-dl-lifecycle
```

### Option B — Manual Copy

```bash
git clone https://github.com/avielbl/bmad-dl-lifecycle
cp -r bmad-dl-lifecycle/bmad-dl-* your-project/_bmad/bmad-dl-lifecycle/
```

### Option C — npm (coming soon)

```bash
bmad install bmad-dl-lifecycle
```

---

## IDE Setup

### Claude Code

```bash
# From your project root
for skill in _bmad/bmad-dl-lifecycle/bmad-dl-*; do
  name=$(basename $skill)
  mkdir -p .claude/skills/$name
  cp $skill/SKILL.md .claude/skills/$name/SKILL.md
  cp $skill/bmad-manifest.json .claude/skills/$name/bmad-manifest.json 2>/dev/null || true
done
```

Invoke in chat:
```
/bmad-dl-initiation
```

### Antigravity

Skills are auto-discovered from `_bmad/` — no additional setup needed. Invoke:
```
/bmad-dl-initiation
```

### VSCode + Cline

**1.** Install the Cline extension.

**2.** Configure model — Anthropic provider, `claude-sonnet-4-5` or later.

**3.** Reference skills with `@file`:
```
@_bmad/bmad-dl-lifecycle/bmad-dl-initiation/SKILL.md
I want to build an image classification model for defect detection.
```

**4.** Optional `.clinerules` for natural-language invocation:

```markdown
# BMAD DL Lifecycle Skills

This project uses the bmad-dl-lifecycle module. Available skills:

- Frame the research question / create PRD: `_bmad/bmad-dl-lifecycle/bmad-dl-initiation/SKILL.md`
- Exploratory data analysis: `_bmad/bmad-dl-lifecycle/bmad-dl-eda/SKILL.md`
- Design architecture: `_bmad/bmad-dl-lifecycle/bmad-dl-architecture/SKILL.md`
- Create task breakdown: `_bmad/bmad-dl-lifecycle/bmad-dl-detailed-design/SKILL.md`
- Implement a task: `_bmad/bmad-dl-lifecycle/bmad-dl-implementation/SKILL.md`
- Analyze experiment results: `_bmad/bmad-dl-lifecycle/bmad-dl-analysis/SKILL.md`
- Plan next revision cycle: `_bmad/bmad-dl-lifecycle/bmad-dl-revision/SKILL.md`
```

---

## Key Principles

- **Always start each skill in a fresh context window.** Skills are long — mixing steps in one session degrades quality.
- **The Research Thesis is the source of truth.** All agents read it first; the Domain Expert maintains it.
- **EDA informs Architecture.** Never design the model before understanding the data.
- **Hypotheses must be domain-grounded.** Not "try a bigger model" — but "X will improve Y because of Z domain reasoning."
- **Headless mode** is available on all skills for CI/CD integration.

---

## Expanding This Package

### Adding a new skill

1. Create a new folder: `bmad-dl-<skillname>/`
2. `SKILL.md` structure:
   ```markdown
   ---
   name: bmad-dl-<skillname>
   description: <5-8 word summary>. Use this when <trigger phrase>.
   ---

   # BMAD Workflow N: <Title>

   ## 1. Operating Instructions
   You are an expert <role>. Your goal is to <objective>.

   1. Read `docs/00_Research_Thesis.md` first.
   2. Read <other input documents>.
   3. <Core logic steps>
   4. CRITICAL: Do not generate the final file yet. Ask <N> clarifying questions. Halt and wait.
   5. Upon approval, write to <output path>.

   ## 2. Expected Output Template
   <Structured markdown template>
   ```

3. `bmad-manifest.json`:
   ```json
   {
     "module-code": "bmad-dl-lifecycle",
     "capabilities": [{
       "name": "<skillname>",
       "menu-code": "DLX",
       "description": "...",
       "supports-headless": true,
       "phase-name": "<N-phasename>",
       "after": ["bmad-dl-<previous>"],
       "before": ["bmad-dl-<next>"],
       "output-location": "{output_folder}"
     }]
   }
   ```

---

## Project Structure

```
bmad-dl-lifecycle/
├── module.yaml
├── README.md
├── bmad-dl-initiation/        # Stage 1 — Domain Expert
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/validate_prd.py
├── bmad-dl-eda/               # Stage 2 — Data Scientist (NEW in v1.1)
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/
│       ├── eda_analyzer.py
│       ├── baseline_classifier.py
│       ├── class_weights_calculator.py
│       └── clustering_explorer.py
├── bmad-dl-architecture/      # Stage 3 — AI Architect
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/check_req_coverage.py
├── bmad-dl-detailed-design/   # Stage 4 — AI Tech Lead
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/validate_design.py
├── bmad-dl-implementation/    # Stage 5 — AI Developer
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   ├── scripts/
│   │   ├── get_next_task.py
│   │   ├── baseline_classifier.py
│   │   └── class_weights_calculator.py
│   └── assets/
│       ├── template_lightning_module.py
│       ├── template_datamodule.py
│       ├── quick_trainer_setup.py
│       ├── advanced_trainer_configs.py
│       └── template_gnn_module.py
├── bmad-dl-analysis/          # Stage 6 — Data Scientist
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/
│       ├── parse_training_logs.py
│       ├── plot_training_curves.py
│       └── plot_confusion_matrix.py
└── bmad-dl-revision/          # Stage 7 — Domain Expert + Tech Lead
    ├── SKILL.md
    ├── bmad-manifest.json
    └── scripts/summarize_experiment_history.py
```

---

## Versioning

| Change | Bump |
|--------|------|
| New skill added | Minor: `1.0.0 → 1.1.0` |
| Prompt/template fix | Patch: `1.1.0 → 1.1.1` |
| Output format breaking change | Major: `1.1.1 → 2.0.0` |

**Changelog:**
- `v1.1.0` — Added dedicated EDA stage (Stage 2); PM role redesigned as Domain Expert; Research Thesis document introduced as central living document; all stage numbers shifted accordingly.
- `v1.0.0` — Initial release with 6 stages.

---

## License

MIT
