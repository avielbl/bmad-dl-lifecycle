# bmad-dl-lifecycle

> Agentic workflows for the Deep Learning development lifecycle — a custom [BMAD Method](https://github.com/bmad-code-org) module.

This package provides six structured AI workflow skills that guide a team through the full lifecycle of a machine learning project: from initial requirements, through architecture and implementation, to experiment analysis and iterative revision. Each skill adopts a specific expert role, asks clarifying questions before generating artifacts, and produces traceable, structured documents.

---

## Skills Overview

| # | Skill | Role | Output |
|---|-------|------|--------|
| 1 | `bmad-dl-initiation` | Product Manager | `docs/prd/01_PRD.md` |
| 2 | `bmad-dl-architecture` | AI Architect | `docs/architecture/02_Architecture.md` |
| 3 | `bmad-dl-detailed-design` | AI Tech Lead | `docs/design/03_Detailed_Design.md` |
| 4 | `bmad-dl-implementation` | AI Developer | `src/`, `tests/`, `docs/implementation/04_Integration_Log.md` |
| 5 | `bmad-dl-analysis` | Data Scientist | `docs/experiments/05_Analysis_EXP_[ID].md` |
| 6 | `bmad-dl-revision` | AI Tech Lead | `docs/revisions/06_Revision_Log.md` |

The lifecycle follows a sequential planning phase followed by an iterative loop:

```
[1] Initiation → [2] Architecture → [3] Detailed Design
                                            ↓
                                    [4] Implementation
                                            ↓
                                    [5] Analysis
                                            ↓
                                    [6] Revision ──→ back to [4]
```

---

## Prerequisites

- [BMAD Method](https://github.com/bmad-code-org/bmad-method) installed in your project (`_bmad/` folder present)
- An AI IDE: Claude Code, Antigravity, or VSCode + Cline
- A strong model recommended: Claude Sonnet 4.5+ or equivalent

---

## Installation

### Option A — Git Submodule (recommended for teams)

Add this package as a submodule in your ML project:

```bash
git submodule add https://github.com/avielbl/bmad-dl-lifecycle _bmad/bmad-dl-lifecycle
git submodule update --init
```

Then register the module in your project's `_bmad/_config/manifest.yaml`:

```yaml
modules:
  - name: bmad-dl-lifecycle
    version: 1.0.0
    source: external
    repoUrl: https://github.com/avielbl/bmad-dl-lifecycle
```

To pull future updates:

```bash
git submodule update --remote _bmad/bmad-dl-lifecycle
```

### Option B — Manual Copy

Clone and copy the skill folders directly into your project:

```bash
git clone https://github.com/avielbl/bmad-dl-lifecycle
cp -r bmad-dl-lifecycle/bmad-dl-* your-project/_bmad/bmad-dl-lifecycle/
```

### Option C — npm (coming soon)

Once published:

```bash
bmad install bmad-dl-lifecycle
```

---

## IDE Setup

### Claude Code

Skills are invoked via slash commands. Copy the skill folders into the Claude Code skills directory:

```bash
# From your project root
for skill in _bmad/bmad-dl-lifecycle/bmad-dl-*; do
  name=$(basename $skill)
  mkdir -p .claude/skills/$name
  cp $skill/SKILL.md .claude/skills/$name/SKILL.md
  cp $skill/bmad-manifest.json .claude/skills/$name/bmad-manifest.json 2>/dev/null || true
done
```

Invoke a skill directly in the Claude Code chat:

```
/bmad-dl-initiation
```

Claude Code loads the `SKILL.md` automatically and begins the workflow.

### Antigravity

Antigravity reads skills directly from `_bmad/` — no additional setup required once the module is installed. Skills are available immediately after installation.

To invoke, type the skill name or describe the step in the chat:

```
/bmad-dl-initiation

or

Start a new DL project — run the initiation workflow
```

### VSCode + Cline

**1. Install the Cline extension** from the VSCode marketplace.

**2. Configure your model** — open Cline settings, set provider to Anthropic, choose `claude-sonnet-4-5` or `claude-opus-4-5`.

**3. Invoke skills using `@file` mentions** in the Cline chat:

```
@_bmad/bmad-dl-lifecycle/bmad-dl-initiation/SKILL.md

I want to build an image classification model for defect detection.
```

**4. Optional: Create a `.clinerules` file** at your project root to enable natural language invocation without typing file paths:

```markdown
# BMAD DL Lifecycle Skills

This project uses the bmad-dl-lifecycle BMAD module. Available skills:

- Start a new ML project / create PRD: `_bmad/bmad-dl-lifecycle/bmad-dl-initiation/SKILL.md`
- Design architecture: `_bmad/bmad-dl-lifecycle/bmad-dl-architecture/SKILL.md`
- Create detailed task breakdown: `_bmad/bmad-dl-lifecycle/bmad-dl-detailed-design/SKILL.md`
- Implement a task: `_bmad/bmad-dl-lifecycle/bmad-dl-implementation/SKILL.md`
- Analyze experiment results: `_bmad/bmad-dl-lifecycle/bmad-dl-analysis/SKILL.md`
- Plan next revision cycle: `_bmad/bmad-dl-lifecycle/bmad-dl-revision/SKILL.md`

When the user references any of these workflow steps, load and follow the corresponding SKILL.md.
```

With `.clinerules` in place you can simply say:

```
Analyze the results from my last training run
```

---

## Using Skills in an ML Project

### Key principles

- **Always start each skill in a fresh context window.** Skills are long — mixing multiple steps in one session degrades quality.
- **Each skill halts and asks questions before writing.** Answer them before the final artifact is generated. This is by design.
- **Documents are the source of truth.** Each skill reads the docs from previous steps — keep them committed to your repo.
- **Headless mode** is available on all skills for CI/CD integration (see below).

### Headless / Autonomous Mode

All skills support headless invocation for unattended or pipeline execution. Pass inputs upfront and the skill will run without stopping:

```
@_bmad/bmad-dl-lifecycle/bmad-dl-analysis/SKILL.md --headless

Experiment ID: EXP-007
Logs: docs/experiments/run_007_metrics.csv
PRD: docs/prd/01_PRD.md
```

---

## Full Lifecycle Example: Fruit Defect Classifier

This walkthrough uses a simple image classification project — detecting defective fruit on a production line — to demonstrate the complete lifecycle.

---

### Step 1 — Initiation (`bmad-dl-initiation`)

**Invoke:**
```
/bmad-dl-initiation
```

**You provide:**
```
high_level_prompt: Build a model that classifies fruit images as "defective" or "healthy"
  for use on a factory production line camera.
target_domain: Industrial visual quality inspection
data_source: Local directory of 4,000 labeled JPEG images (2,000 per class)
```

**The skill asks:**
```
Q1: What is the minimum acceptable F1-Score on the defective class?
Q2: What is the inference latency constraint (real-time vs. batch)?
Q3: Are there regulatory requirements around false negatives (missed defects)?
```

**You answer, and the skill writes `docs/prd/01_PRD.md`:**

```markdown
### B. Traceable Requirements

| Requirement ID | Category    | Description                              | Acceptance Criteria              |
| REQ-PERF-01    | Performance | F1-Score on defective class              | ≥ 0.92                          |
| REQ-PERF-02    | Performance | Inference latency                        | < 50ms per image                 |
| REQ-DATA-01    | Data        | Balanced dataset with quality annotation | < 2% label noise verified by EDA |
| REQ-SYS-01     | System      | Model deployable as REST API             | Passes load test at 20 req/s     |
```

---

### Step 2 — Architecture (`bmad-dl-architecture`)

**Invoke** (new context window):
```
/bmad-dl-architecture
```

**The skill reads `docs/prd/01_PRD.md` and asks:**
```
Q1: Cloud provider or on-premise GPU?
Q2: Preferred framework — PyTorch or TensorFlow?
Q3: Should the EDA phase use Pandas Profiling or a custom script?
```

**Writes `docs/architecture/02_Architecture.md`:**

```markdown
### B. Component Design & Traceability

| Component       | Description                              | Tech Stack          | Satisfies       |
| Data Profiling  | EDA on class balance and annotation quality | pandas-profiling | REQ-DATA-01     |
| Data Loader     | Augmentation pipeline + train/val/test split | PyTorch DataLoader | REQ-DATA-01   |
| Training Core   | ResNet-18 fine-tuned with Focal Loss     | PyTorch + timm      | REQ-SYS-01      |
| Serving API     | FastAPI endpoint with ONNX export        | FastAPI + ONNX      | REQ-PERF-01/02  |
```

---

### Step 3 — Detailed Design (`bmad-dl-detailed-design`)

**Invoke** (new context window):
```
/bmad-dl-detailed-design
```

**The skill reads PRD + Architecture and proposes tasks:**

```
TSK-001 | Data-Agent  | EDA: class balance, annotation audit, split verification
TSK-002 | Data-Agent  | Implement DataLoader with augmentation pipeline
TSK-003 | Model-Agent | Implement ResNet-18 fine-tuning with Focal Loss
TSK-004 | MLOps-Agent | Implement FastAPI serving endpoint + ONNX export
TSK-005 | MLOps-Agent | Write integration tests and CI pipeline
```

**You confirm granularity. Writes `docs/design/03_Detailed_Design.md`.**

---

### Step 4 — Implementation (`bmad-dl-implementation`) — repeat per task

**Invoke** (new context window per task):
```
/bmad-dl-implementation
```

**You provide:**
```
task_id: TSK-003
```

**The skill reads the design doc, proposes the code, asks questions:**
```
Q1: Should we use a frozen backbone initially, or fine-tune all layers from epoch 1?
Q2: What weighted sampling strategy for class imbalance — oversampling or loss weighting?
```

**On approval, writes:**
- `src/models/classifier.py`
- `tests/test_classifier.py`
- Appends entry to `docs/implementation/04_Integration_Log.md`

Repeat for each `TSK-00X` in sequence.

---

### Step 5 — Analysis (`bmad-dl-analysis`)

After your training run completes:

**Invoke** (new context window):
```
/bmad-dl-analysis
```

**You provide:**
```
Logs: docs/experiments/run_001_metrics.csv
```

**The skill reads PRD + logs, presents findings:**
```
Preliminary findings:
- Overall accuracy: 94.1%
- F1-Score (defective): 0.87  ← FAILS REQ-PERF-01 (target: 0.92)
- Class confusion: 38 healthy images misclassified as defective

Q1: The model struggles heavily with under-ripe fruit — should we treat it as a
    third class or keep it in "healthy"?
Q2: Should we prioritize recall (catch all defects) over precision in the next run?
```

**Writes `docs/experiments/05_Analysis_EXP_001.md`.**

---

### Step 6 — Revision (`bmad-dl-revision`)

**Invoke** (new context window):
```
/bmad-dl-revision
```

**The skill reads the analysis doc and proposes:**

```
Core Hypothesis: "Applying MixUp augmentation + increasing Focal Loss gamma from 2.0
to 3.0 will improve F1-Score on the defective class from 0.87 to ≥ 0.92 by forcing
the model to learn harder examples."

New Tasks:
REV-001 | Model-Agent | Increase Focal Loss gamma to 3.0
REV-002 | Data-Agent  | Add MixUp augmentation to training pipeline
REV-003 | MLOps-Agent | Re-run training and log as EXP-002
```

**Writes `docs/revisions/06_Revision_Log.md`, updates PRD and Architecture with decisions.**

→ **Loop back to Step 4** with the new task IDs. Repeat until `REQ-PERF-01` is satisfied.

---

## Expanding This Package

### Adding a new skill

1. Create a new folder in this repo:
   ```
   bmad-dl-<skillname>/
   ├── SKILL.md
   └── bmad-manifest.json
   ```

2. `SKILL.md` structure:
   ```markdown
   ---
   name: bmad-dl-<skillname>
   description: <5-8 word summary>. Use this when <trigger phrase>.
   ---

   # BMAD Workflow N: <Title>

   ## 1. Operating Instructions
   You are an expert <role>. Your goal is to <objective>.

   1. Read <input documents>.
   2. <Core logic steps>
   3. CRITICAL: Do not generate the final file yet. Ask <N> clarifying questions. Halt and wait.
   4. Upon approval, write to <output path>.

   ## 2. Expected Output Template
   <Structured markdown template for the output document>
   ```

3. Generate `bmad-manifest.json` using the BMAD workflow builder or create manually:
   ```json
   {
     "module-code": "bmad-dl-lifecycle",
     "capabilities": [
       {
         "name": "<skillname>",
         "menu-code": "DLX",
         "description": "Produces <artifact> from <input>.",
         "supports-headless": true,
         "phase-name": "<N-phasename>",
         "after": ["bmad-dl-<previous>"],
         "before": ["bmad-dl-<next>"],
         "output-location": "{output_folder}"
       }
     ]
   }
   ```

4. Submit a PR to this repo. See contribution guidelines below.

### Editing an existing prompt

All workflow logic lives in `SKILL.md` — specifically in **Section 1 (Operating Instructions)** and **Section 2 (Expected Output Template)**.

Common edits:

| What to change | Where |
|---|---|
| Questions the skill asks | Section 1, step 3 — the "Clarification & Decision Log" bullet |
| Output document structure | Section 2 — the template |
| Which input docs to read | Section 1, step 1 |
| Role/persona of the skill | Section 1, opening "You are..." line |
| Output file path | Section 1, final step |

After editing, test on a real (even toy) ML project before committing. Verify the output doc matches the new template exactly.

---

## Team Contribution Workflow

```
main          ← stable releases only
feature/      ← new skills
fix/          ← prompt corrections, template fixes
experiment/   ← trying new approaches
```

**Versioning** (`module.yaml`):

| Change | Bump |
|--------|------|
| New skill added | Minor: `1.0.0 → 1.1.0` |
| Prompt/template fix | Patch: `1.1.0 → 1.1.1` |
| Output format breaking change | Major: `1.1.1 → 2.0.0` |

**PR requirements:**
- Describe which skill was changed and why
- Include a sample output snippet showing the improvement
- Tag a reviewer who has used the affected skill on a real project

---

## Project Structure

```
bmad-dl-lifecycle/
├── module.yaml                          # Module metadata and version
├── README.md                            # This file
├── bmad-dl-initiation/
│   ├── SKILL.md                         # Workflow instructions + output template
│   └── bmad-manifest.json               # BMAD capability registration
├── bmad-dl-architecture/
│   ├── SKILL.md
│   └── bmad-manifest.json
├── bmad-dl-detailed-design/
│   ├── SKILL.md
│   └── bmad-manifest.json
├── bmad-dl-implementation/
│   ├── SKILL.md
│   └── bmad-manifest.json
├── bmad-dl-analysis/
│   ├── SKILL.md
│   └── bmad-manifest.json
└── bmad-dl-revision/
    ├── SKILL.md
    └── bmad-manifest.json
```

---

## License

MIT
