# bmad-dl-lifecycle

> Agentic workflows for the Deep Learning development lifecycle — a custom [BMAD Method](https://github.com/bmad-code-org) module.

This package provides ten structured AI workflow skills that guide a team through the full lifecycle of a machine learning project: from research framing and data exploration, through architecture and implementation, to experiment analysis, iterative revision, and growing team knowledge. Each skill adopts a specific expert role, halts to ask clarifying questions before generating artifacts, and produces traceable, structured documents.

---

## Skills Overview

### Sequential Lifecycle Skills

| # | Skill | Role | Output |
|---|-------|------|--------|
| 1 | `bmad-dl-initiation` | Domain Expert | `docs/00_Research_Thesis.md` + `docs/prd/01_PRD.md` |
| 2 | `bmad-dl-eda` | Data Scientist | `docs/eda/02_EDA_Report.md` |
| 3 | `bmad-dl-architecture` | AI Architect | `docs/architecture/03_Architecture.md` |
| 4 | `bmad-dl-detailed-design` | AI Tech Lead | `docs/design/04_Detailed_Design.md` |
| 4.5 | `bmad-dl-techspec` | Domain Expert + Tech Lead | `docs/techspecs/TECHSPEC_EXP_[ID].md` |
| 5 | `bmad-dl-implementation` | AI Developer | `src/`, `tests/`, `docs/implementation/05_Integration_Log.md` |
| 6 | `bmad-dl-analysis` | Data Scientist | `docs/experiments/06_Analysis_EXP_[ID].md` |
| 7 | `bmad-dl-revision` | Domain Expert + Tech Lead | `docs/revisions/07_Revision_Log.md` + updates `docs/00_Research_Thesis.md` |

### Universal Tools (run anytime)

| Skill | Purpose | Output |
|-------|---------|--------|
| `bmad-dl-advise` | Surface past experiment findings, failure warnings, and validated parameters before starting new work | Advisory report in chat |
| `bmad-dl-retrospective` | Capture session learnings into the team knowledge base at end of any session | `docs/knowledge/RETRO_EXP_[ID].md` |

---

## Central Documents

### `docs/00_Research_Thesis.md`

The **single source of truth** for the entire lifecycle. The `00_` prefix ensures it is always the first file visible in the docs folder. All agents read it as their first step. It contains:

- **Active hypothesis** — what the current experiment is trying to prove (updated each Revision cycle)
- **Domain context** — failure mode costs, real-world success criteria (set by Domain Expert)
- **Data characterization** — key EDA findings that constrain the approach (populated after Stage 2)
- **Hypothesis history** — full audit trail of every hypothesis tested, with experiment ID and verdict

### `docs/techspecs/TECHSPEC_EXP_[ID].md`

A **pre-experiment contract** written before any training begins. Locks in:
- Exact parameter search space (copy-paste tables, not prose)
- Compute budget hard cap (max runs, GPU hours, wall-clock deadline)
- Tiered success criteria signed off by the Domain Expert: *Best case → Realistic → Worst case → Failure definition*

Results are evaluated against this document as-is. It is never modified after training starts.

### `docs/knowledge/RETRO_EXP_[ID].md`

Team knowledge base entries written by `/bmad-dl-retrospective`. Each entry contains a mandatory **Failed Attempts** table — the most-referenced section in any knowledge base. Future experiments consult these via `/bmad-dl-advise`.

---

## The Knowledge Flywheel

```
Before any experiment:   /bmad-dl-advise  → surfaces what team already knows
                                ↓
                        bmad-dl-techspec  → locks success criteria before training
                                ↓
                        bmad-dl-implementation / training run
                                ↓
                        bmad-dl-analysis  → evaluates against pre-committed TECHSPEC
                                ↓
After any session:   /bmad-dl-retrospective → captures failures + validated params
                                ↓
                        docs/knowledge/   → searchable by future /advise calls
```

Once the knowledge base reaches critical mass, the flywheel accelerates: every new experiment benefits from all previous failures. A researcher joining mid-project runs `/bmad-dl-advise` and immediately inherits weeks of team learning.

---

## Role Definitions

### Domain Expert (Stage 1, 4.5, 7)
Brings deep knowledge of the problem domain — what the data represents scientifically or operationally, what failure modes cost in real terms, what prior approaches have been tried, and what "good enough" means beyond metric thresholds. The Domain Expert maintains the Research Thesis, signs off on TECHSPEC success criteria, and co-authors revision decisions.

### Data Scientist (Stage 2, 6)
Owns EDA and experiment analysis. In Stage 2, interprets data characteristics with the Domain Expert before any architecture decisions. In Stage 6, evaluates results against the pre-committed hypothesis, applies domain-grounded failure mode costs, and contributes the Failed Attempts table.

### AI Architect (Stage 3)
Designs the system architecture grounded in EDA findings — not for a hypothetical dataset but for the specific data characteristics established in Stage 2. Every architectural decision cites an EDA finding or PRD requirement.

### AI Tech Lead (Stage 4, 4.5, 7)
Breaks down architecture into tasks (Stage 4), co-produces the pre-experiment contract with the Domain Expert (Stage 4.5), and structures the next iteration hypothesis with the Domain Expert (Stage 7).

### AI Developer (Stage 5)
Implements tasks against the Detailed Design, applies EDA constraints (class weights, augmentation strategy), and documents every failed approach with root cause in the Integration Log.

---

## Lifecycle Flow

```
[1] Initiation (Domain Expert)
    ↓  docs/00_Research_Thesis.md + docs/prd/01_PRD.md
[2] EDA (Data Scientist + Domain Expert interpretation)
    ↓  docs/eda/02_EDA_Report.md + Thesis Section IV updated
[3] Architecture (reads Thesis + EDA)
    ↓  docs/architecture/03_Architecture.md
[4] Detailed Design
    ↓  docs/design/04_Detailed_Design.md
[4.5] TECHSPEC (pre-experiment contract, Domain Expert sign-off)
    ↓  docs/techspecs/TECHSPEC_EXP_[ID].md
[5] Implementation ← /advise first, /retrospective after
    ↓  src/, tests/, docs/implementation/05_Integration_Log.md
[6] Analysis (vs. TECHSPEC + Thesis) ← /advise first, /retrospective after
    ↓  docs/experiments/06_Analysis_EXP_[ID].md
[7] Revision (Domain Expert + Tech Lead, updates Thesis hypothesis)
    ↓  docs/revisions/07_Revision_Log.md + docs/00_Research_Thesis.md
    ↓
    └──→ back to [4.5] with new hypothesis
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

Register the module in your project's `_bmad/_config/manifest.yaml`:

```yaml
modules:
  - name: bmad-dl-lifecycle
    version: 1.2.0
    source: external
    repoUrl: https://github.com/avielbl/bmad-dl-lifecycle
```

Pull future updates:

```bash
git submodule update --remote _bmad/bmad-dl-lifecycle
```

### Option B — Manual Copy

```bash
git clone https://github.com/avielbl/bmad-dl-lifecycle
cp -r bmad-dl-lifecycle/bmad-dl-* your-project/_bmad/bmad-dl-lifecycle/
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
/bmad-dl-advise         ← run before any new experiment
/bmad-dl-retrospective  ← run at the end of any session
```

### Antigravity

Auto-discovered from `_bmad/` — no additional setup needed.

### VSCode + Cline

Optional `.clinerules` for natural-language invocation:

```markdown
# BMAD DL Lifecycle Skills

This project uses the bmad-dl-lifecycle module. Available skills:

- Frame the research question / create PRD: `_bmad/bmad-dl-lifecycle/bmad-dl-initiation/SKILL.md`
- Exploratory data analysis: `_bmad/bmad-dl-lifecycle/bmad-dl-eda/SKILL.md`
- Design architecture: `_bmad/bmad-dl-lifecycle/bmad-dl-architecture/SKILL.md`
- Create task breakdown: `_bmad/bmad-dl-lifecycle/bmad-dl-detailed-design/SKILL.md`
- Pre-experiment contract: `_bmad/bmad-dl-lifecycle/bmad-dl-techspec/SKILL.md`
- Implement a task: `_bmad/bmad-dl-lifecycle/bmad-dl-implementation/SKILL.md`
- Analyze experiment results: `_bmad/bmad-dl-lifecycle/bmad-dl-analysis/SKILL.md`
- Plan next revision cycle: `_bmad/bmad-dl-lifecycle/bmad-dl-revision/SKILL.md`
- Search past experiments before starting: `_bmad/bmad-dl-lifecycle/bmad-dl-advise/SKILL.md`
- Capture session learnings: `_bmad/bmad-dl-lifecycle/bmad-dl-retrospective/SKILL.md`
```

---

## Key Principles

- **Always start each skill in a fresh context window.** Skills are long — mixing steps in one session degrades quality.
- **Run `/bmad-dl-advise` before every new experiment.** Skip the 3 days of trial-and-error your teammate already completed.
- **Run `/bmad-dl-retrospective` at the end of every session.** Claude does the writing. You review. Takes 30 seconds. Prevents team knowledge from disappearing into chat history.
- **The TECHSPEC is a contract, not a plan.** It is never modified after training starts. Results are evaluated against it as-is.
- **Failed Attempts sections are mandatory.** A log with no failure documentation is incomplete. The most valuable sentence in any knowledge base is "I tried X and it broke because Y."
- **The Research Thesis is the source of truth.** Every agent reads it first. The Domain Expert maintains it.

---

## Project Structure

```
bmad-dl-lifecycle/
├── module.yaml
├── README.md
├── marketplace.json                  # Auto-generated skill index
│
├── bmad-dl-initiation/               # Stage 1 — Domain Expert
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/validate_prd.py
│
├── bmad-dl-eda/                      # Stage 2 — Data Scientist
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/
│       ├── eda_analyzer.py
│       ├── baseline_classifier.py
│       ├── class_weights_calculator.py
│       └── clustering_explorer.py
│
├── bmad-dl-architecture/             # Stage 3 — AI Architect
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/check_req_coverage.py
│
├── bmad-dl-detailed-design/          # Stage 4 — AI Tech Lead
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/validate_design.py
│
├── bmad-dl-techspec/                 # Stage 4.5 — Domain Expert + Tech Lead
│   ├── SKILL.md
│   └── bmad-manifest.json
│
├── bmad-dl-implementation/           # Stage 5 — AI Developer
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
│
├── bmad-dl-analysis/                 # Stage 6 — Data Scientist
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/
│       ├── parse_training_logs.py
│       ├── plot_training_curves.py
│       └── plot_confusion_matrix.py
│
├── bmad-dl-revision/                 # Stage 7 — Domain Expert + Tech Lead
│   ├── SKILL.md
│   ├── bmad-manifest.json
│   └── scripts/summarize_experiment_history.py
│
├── bmad-dl-advise/                   # Universal tool
│   ├── SKILL.md
│   └── bmad-manifest.json
│
├── bmad-dl-retrospective/            # Universal tool
│   ├── SKILL.md
│   └── bmad-manifest.json
│
└── .github/
    └── workflows/
        ├── validate_skills.yml       # PR gate: structure + Failed Attempts checks
        └── update_marketplace.yml    # Auto-generate marketplace.json on merge
```

---

## Output Directory Layout (in your ML project)

```
docs/
├── 00_Research_Thesis.md             ← Read first. Always. Updated every revision cycle.
├── prd/
│   └── 01_PRD.md
├── eda/
│   ├── 02_EDA_Report.md
│   └── 02_class_weights.md
├── architecture/
│   └── 03_Architecture.md
├── design/
│   └── 04_Detailed_Design.md
├── techspecs/
│   ├── TECHSPEC_EXP_001.md           ← Pre-experiment contract. Never modified after training.
│   └── TECHSPEC_EXP_002.md
├── implementation/
│   └── 05_Integration_Log.md         ← Includes mandatory Failed Attempts per task.
├── experiments/
│   ├── 06_Analysis_EXP_001.md
│   └── 06_Analysis_EXP_002.md
├── revisions/
│   └── 07_Revision_Log.md
└── knowledge/                        ← Growing team knowledge base.
    ├── RETRO_EXP_001_dataloader.md
    ├── RETRO_EXP_001_focal_loss.md
    └── RETRO_EXP_002_augmentation.md
```

---

## Versioning & Changelog

| Change | Version Bump |
|--------|------|
| New skill added | Minor: `1.1.0 → 1.2.0` |
| Prompt/template fix | Patch: `1.2.0 → 1.2.1` |
| Output format breaking change | Major: `1.2.0 → 2.0.0` |

**Changelog:**
- `v1.2.0` — Added knowledge flywheel: `bmad-dl-advise`, `bmad-dl-retrospective`, `bmad-dl-techspec`. Added mandatory Failed Attempts sections to Analysis and Implementation templates. Added GitHub Actions CI validation and marketplace auto-update.
- `v1.1.0` — Added dedicated EDA stage; PM role redesigned as Domain Expert; Research Thesis document introduced as central living document; all stage numbers shifted.
- `v1.0.0` — Initial release with 6 stages.

---

## License

MIT
