---
type: playbook
date: 2026-03-24
status: active
summary: "How to evaluate, build, and run folder-based AI workflows using the five skills in this project"
---

# Playbook: Folder Architecture Evaluation

> **Purpose:** Audit any project's folder structure, build staged AI pipelines, and run them with human review gates.
> **Skills covered:** `/folder-audit`, `/pipeline-scaffold`, `/run-stage`, `/stage-review`, `/validate-pipeline`
> **Source:** ICM specification (Van Clief & McDermott, 2026), Clief Notes Module 3 (Map/Rooms/Tools metaphors), multi-project implementations
> **When to use:** Before onboarding Claude Code, when building staged workflows, during quarterly reviews, or when AI output quality degrades.

---

## Part 1: The Evaluation Framework

Every project is evaluated against the **ICM five-layer context hierarchy** (Van Clief & McDermott, 2026). The base audit scores Layers 0–1 and Tools; if pipeline stages are detected, an extended assessment covers Layers 2–4. The framework also checks **eight anti-patterns** and **five structural metrics**. The audit skill automates this; the framework below explains what it measures and why.

> **Terminology note:** The "Map," "Rooms," and "Tools" metaphors originate from Clief Notes Module 3. The underlying layer numbering (0–4) follows the ICM specification. The scoring rubric and grading scale are FSP-authored practical tools, not part of the ICM paper.

### The 60/30/10 Rule

- **60%** — Traditional structure (folders, naming, files)
- **30%** — Routing and rules (CLAUDE.md, CONTEXT.md, conventions)
- **10%** — The AI interactions themselves

If the structure is bad, no amount of prompt engineering fixes it. Fix the 60% first.

### Layer 0: The Map (CLAUDE.md)

The root file that tells any agent — human or AI — where things are.

| Criterion | Pass | Fail |
|-----------|------|------|
| Root config file exists | CLAUDE.md at project root | No root navigation file |
| Under 50 non-blank lines | Concise routing hub | Context hiding inside it |
| Routing table present | Task / Go-to / Read columns | No routing table |
| Naming conventions defined | Explicit patterns per file type | "Follow existing" or nothing |
| Identity section | 2–3 sentences: what, who, why | Personality description |
| Read-only folders marked | Clearly labeled | Everything implicitly read-write |

**Score: X/6.** Below 4 = agent will guess and waste tokens.

### Layer 1: The Rooms (CONTEXT.md)

Each workspace folder gets its own context file scoping the work that happens there.

| Criterion | Pass | Fail |
|-----------|------|------|
| CONTEXT.md exists per workspace | One per major folder | Missing or only at root |
| Under 80 lines | Concise | Move details into child files |
| Describes the work (80%+) | Project, audience, constraints | Describes Claude's personality |
| Includes "What to Avoid" | Explicit anti-patterns | Only positive instructions |
| Fresh | Updated within project's active period | Stale (>6 weeks) |
| Lists key files | Points to important files | Agent must discover everything |

**Score: X/6.** Average across workspaces, rounded.

### Tools (Skills / Playbooks)

Reusable processes wired into workspaces. Should only exist after real friction. Tools are scored separately from the five-layer hierarchy — they complement the layers but are not a numbered layer in ICM.

| Criterion | Pass | Fail |
|-----------|------|------|
| Born from friction | Created after repeated manual steps | Pre-built "just in case" |
| Scoped to workspaces | Loaded only where needed | Available everywhere |
| Instructions are markdown | Prompt files | Python orchestration |
| Anti-fragile | Model updates simplify the skill | Model updates break it |

**Score: X/4.** New projects with no tools score N/A (not a penalty).

### Eight Anti-Patterns

| # | Anti-Pattern | Signal | Fix |
|---|-------------|--------|-----|
| 1 | Oversized CLAUDE.md | >50 lines | Pull content into CONTEXT.md files |
| 2 | Missing routing table | No task-to-folder mapping | Add Task / Go-to / Read table |
| 3 | Too many workspaces | 5+ from day one | Merge to 2–3. Split when mental modes diverge |
| 4 | Personality over context | Describes Claude, not the work | Flip to 80% work, 20% behavior |
| 5 | Stale context | Not updated since project evolved | Review and refresh |
| 6 | Flat dump | 10+ files at one level | Group by workspace/type |
| 7 | Built before used | Elaborate system, no work product | Tear down, start working, grow from use |
| 8 | Monolithic context loading | All instructions in one file/prompt | Scope context per workspace using CONTEXT.md |

### Five Structural Metrics

| Metric | Formula | Ideal |
|--------|---------|-------|
| M1 Depth Ratio | Max folder depth / top-level folders | 2–4 |
| M2 File Density | Avg files per folder | 3–10 |
| M3 Naming Consistency | % files following consistent pattern per folder | >80% |
| M4 Convention Coverage | % file types with declared convention | 100% |
| M5 Context-to-Content | Context files / total content files | 5–15% |

### Grading Scale

| Total | Grade | Meaning |
|-------|-------|---------|
| 15–16 | A | Production-ready. Agent works autonomously. |
| 10–14 | B | Functional. Occasional re-steering needed. |
| 6–9 | C | Fragile. Agent frequently guesses wrong. |
| 0–5 | F | Broken. Start with minimum viable structure. |

### Debugging AI Failures

Trace from the symptom backward:

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Wrong format | Missing output spec | Fix naming conventions |
| Off-topic content | Wrong/stale context loaded | Fix routing or update CONTEXT.md |
| Ignoring instructions | Reading wrong file | Check routing table |
| Fundamentally confused | Identity/framing broken | Rewrite CLAUDE.md identity |
| Hallucinating | Model-layer issue | Add reference files or verification |

Most problems are rows 1–3.

---

## Part 2: The Five Skills

This project provides five skills that cover the full lifecycle: audit structure, build pipelines, run stages, review output, and validate connections.

```
Audit ──→ Scaffold ──→ Execute ──→ Review ──→ Validate
  ↑                                              |
  └──────────── fix and iterate ─────────────────┘
```

### Skill 1: `/folder-audit`

**What it does:** Scores any project's folder architecture against the ICM five-layer hierarchy (base audit covers Layers 0–1 + Tools). Produces a graded report with prioritized fixes.

**When to use:**
- Before onboarding Claude Code to a project
- When AI output quality degrades
- Quarterly structure reviews
- After major project reorganization

**How to run:**
```
/folder-audit /path/to/project
```

**What it produces:** An audit report saved to `File-tree audit/audits/audit-{project-name}-YYYY-MM-DD.md` with:
- Layer scores (X/16 total)
- Anti-patterns found
- Structural metrics
- Top 3 prioritized fixes
- File tree snapshot

**Post-audit actions:**
- **Imprint:** The skill offers to write structure rules into the audited project's CLAUDE.md so future sessions follow the structure automatically
- **Escalate:** If repeated friction is found, recommends creating a skill
- **ICM Pipeline Audit:** If numbered stage folders are detected, runs an additional 18-point assessment of stage contracts, reference/working separation, and pipeline architecture

### Skill 2: `/pipeline-scaffold`

**What it does:** Creates a new ICM pipeline — numbered stage folders with CONTEXT.md contracts, reference and output directories, and a root CONTEXT.md.

**When to use:**
- Converting a repeatable process into a staged workflow
- Setting up a new content production pipeline
- Any multi-step AI task that benefits from human review gates

**How to run:**
```
/pipeline-scaffold
```
The skill will ask for:
1. Domain/purpose (what does this pipeline produce?)
2. Stage names (natural language: "research, then script, then production")
3. Target directory

**What it produces:**
```
pipeline-root/
├── CONTEXT.md              ← Pipeline overview and stage routing
├── _config/                ← Workspace-wide settings (voice, style, brand)
│   └── README.md
├── 01_stage_name/
│   ├── CONTEXT.md          ← Stage contract (Inputs/Process/Outputs/Review)
│   ├── references/         ← Layer 3: stable reference material
│   └── output/             ← Layer 4: working artifacts
├── 02_stage_name/
│   ├── CONTEXT.md
│   ├── references/
│   └── output/
└── ...
```

**Key concepts:**
- **Stage contracts** — Each CONTEXT.md declares exactly what the stage reads (Inputs), does (Process), produces (Outputs), and how to verify it (Review Checkpoint)
- **Layer 3 vs Layer 4** — Inputs are labeled as reference (internalize as constraints) or working (process as input material). This scoping prevents context window bloat.
- **File-based handoffs** — Output of stage N becomes input to stage N+1. No hidden state.

**After scaffolding:**
1. Add reference material to `_config/` and stage `references/` folders
2. Review each stage's CONTEXT.md contract
3. Run `/validate-pipeline` to verify the chain
4. Start execution with `/run-stage 01`

### Skill 3: `/run-stage`

**What it does:** Executes a single pipeline stage by following its CONTEXT.md contract. Loads only declared inputs, produces only declared outputs, pauses for human review.

**When to use:**
- Running a pipeline one stage at a time
- Advancing to the next stage after review
- Re-running a stage after revisions

**How to run:**
```
/run-stage 01          ← Run stage 01
/run-stage 03          ← Run a specific stage
/run-stage next        ← Run the next unfinished stage
```

**What it does internally:**
1. Reads the stage's CONTEXT.md contract
2. Loads Layer 3 inputs (reference — internalized as constraints)
3. Loads Layer 4 inputs (working — processed as material)
4. Follows the Process instructions sequentially
5. Writes outputs to declared locations
6. Pauses and displays the Review Checkpoint

**Guardrails:**
- Never reads files not listed in the Inputs table
- Never writes to locations not declared in the Outputs table
- Never modifies the stage's CONTEXT.md
- Never auto-advances to the next stage
- If an input file is missing, halts and suggests running the prior stage

**After execution:** The skill suggests `/stage-review` to verify, or `/run-stage` for the next stage.

### Skill 4: `/stage-review`

**What it does:** Reviews a completed stage before advancing. Verifies outputs exist, runs checkpoint criteria, checks quality, and confirms the next stage's inputs are satisfied.

**When to use:**
- After `/run-stage` completes, before moving on
- When unsure whether a stage's output is good enough
- As a formal quality gate in important pipelines

**How to run:**
```
/stage-review 02       ← Review stage 02
/stage-review          ← Review the most recent stage with output
```

**What it checks:**
1. **Output existence** — Are all declared output files present? (Missing = Not Ready)
2. **Review Checkpoint** — Runs each criterion from the stage's CONTEXT.md, scores Pass/Partial/Fail
3. **Quality assessment** — Does the output match what the Process section requested?
4. **Downstream readiness** — Does the next stage's Inputs table match what was produced?

**What it produces:** A review report with a recommendation:
- **Advance:** Stage is complete. Proceed to the next stage.
- **Revise:** Address specific items before advancing.
- **Re-run:** Output is incomplete or missing.

**Important:** This skill is read-only. It never modifies output files — only reads and reports.

### Skill 5: `/validate-pipeline`

**What it does:** Walks the full contract chain and validates that stages connect correctly. Finds broken handoffs, mixed reference/working files, and structural anti-patterns.

**When to use:**
- After `/pipeline-scaffold` to verify the generated structure
- When `/run-stage` fails because of missing inputs
- Before starting a pipeline run to catch issues early
- After editing stage contracts

**How to run:**
```
/validate-pipeline     ← Validates the pipeline in the current directory
```

**What it checks:**
1. **Contract chain** — Does each stage's Outputs match the next stage's Inputs? Flags broken handoffs and name mismatches.
2. **Factory/Product separation** — Are `references/` and `output/` directories present? Do Inputs tables label Layer 3 vs Layer 4? Is there cross-contamination?
3. **Anti-patterns** — Monolithic stages, missing output folders, circular dependencies, over-staging.
4. **Contract completeness** — Does every stage have Inputs, Process, Outputs, and Review Checkpoint sections?

**What it produces:** A validation report with:
- Handoff-by-handoff status (Pass/Warning/Fail)
- Factory/Product separation check
- Anti-patterns found
- Contract completeness table
- Suggested fixes (with offers to apply them)

---

## Part 3: Typical Workflows

### Workflow A: Audit an Existing Project

```
1. /folder-audit /path/to/project
2. Review the report — focus on Top 3 Fixes
3. Apply structural fixes (folders, naming, CLAUDE.md)
4. Apply routing fixes (CONTEXT.md files)
5. Re-audit to confirm improvement
```

### Workflow B: Build and Run a New Pipeline

```
1. /pipeline-scaffold
   → Provide domain + stages → generates folder structure
2. Add reference material to _config/ and references/ folders
3. /validate-pipeline
   → Verify contracts connect correctly before running
4. /run-stage 01
   → Execute first stage
5. /stage-review 01
   → Verify output before advancing
6. /run-stage 02
   → Continue through the pipeline
7. Repeat steps 5-6 for each stage
```

### Workflow C: Debug a Broken Pipeline

```
1. /validate-pipeline
   → Find broken handoffs and contract gaps
2. Fix the CONTEXT.md files flagged in the report
3. /validate-pipeline again
   → Confirm the chain is clean
4. /run-stage {broken-stage}
   → Re-run the stage that failed
5. /stage-review {broken-stage}
   → Verify the fix worked
```

### Workflow D: Periodic Health Check

```
1. /folder-audit {project}
   → Catch structural drift
2. If ICM pipeline detected:
   /validate-pipeline
   → Catch contract drift
3. Apply fixes from both reports
4. Update CONTEXT.md freshness dates
```

---

## Part 4: ICM Pipeline Concepts

### What is ICM?

Interpretable Context Methodology (ICM) uses folder structure as agent orchestration. Instead of coding a pipeline in Python, you define it in markdown files and folders. The agent reads contracts and follows instructions — no runtime orchestration needed.

### The Five-Layer Context Hierarchy

```
Layer 0: CLAUDE.md     → Routes to the right workspace
Layer 1: CONTEXT.md    → Routes to the right files within a workspace
Layer 2: Stage Contracts → Declares inputs, process, outputs per stage
Layer 3: References    → Stable material (voice, style, conventions) — the factory
Layer 4: Working       → Per-run artifacts (drafts, data, output) — the product
```

### Factory vs Product

| Type | Location | Model Behavior | Example |
|------|----------|---------------|---------|
| Factory (Layer 3) | `references/`, `_config/` | Internalize as constraints | Voice guide, design system |
| Product (Layer 4) | `output/`, prior stage output | Process as input material | Research notes, draft |

This distinction matters because the model should *embody* factory material (write in this voice) but *transform* product material (convert this research into a script).

### Stage Contract Specification

Every stage's CONTEXT.md is a contract with four sections:

**Inputs** — Declares which files to read, where they come from, and whether they're Layer 3 (reference) or Layer 4 (working).

**Process** — 3–7 numbered instructions. If it needs more, the stage is too broad — split it.

**Outputs** — Names exact files, their destination, and format. No "whatever was produced."

**Review Checkpoint** — Concrete criteria the human verifies before advancing. This is the gate between stages.

### Design Principles

- **Human review at every boundary.** No auto-advance between stages.
- **Scoped context loading.** Each stage reads only its declared inputs — nothing else.
- **Incremental re-run.** Can re-run stage N without restarting from stage 1.
- **File-based handoffs.** Output of stage N becomes input to stage N+1. Everything is inspectable.
- **3–5 stages default.** Fewer well-defined stages beat many thin ones.

---

## Part 5: Quick Reference

### What Goes Where

| Content | Location |
|---------|----------|
| Project identity, routing table | CLAUDE.md (root) |
| Workspace process, constraints | CONTEXT.md (per workspace) |
| Stage-level contracts | CONTEXT.md (per stage folder) |
| Stable reference material | `references/` or `_config/` |
| Per-run working artifacts | `output/` |
| Reusable procedures | Skills (`.claude/skills/`) |
| File naming patterns | CLAUDE.md conventions section |
| "What to avoid" rules | CONTEXT.md per workspace |

### Skill Quick Reference

| Task | Skill | Input | Output |
|------|-------|-------|--------|
| Audit folder structure | `/folder-audit` | Project path | Graded report (X/16) |
| Create a pipeline | `/pipeline-scaffold` | Domain + stages | Stage folders + contracts |
| Run one stage | `/run-stage` | Stage number | Stage output files |
| Review before advancing | `/stage-review` | Stage number | Review report + recommendation |
| Validate contract chain | `/validate-pipeline` | Pipeline root | Validation report + fixes |

### What NOT to Penalize in Audits

- No tools on a new project
- Simple projects with 1–2 workspaces
- Missing "What to Avoid" in early exploration
- No ICM structure on a non-pipeline project
- Only 2–3 stages in a pipeline

---

## Sources

- Van Clief & McDermott, "Interpretable Context Methodology: Folder Structure as Agent Architecture" (2026) — five-layer hierarchy, stage contracts, factory/product distinction, design principles
- Clief Notes Module 3: Folder Architecture (Quantum Quill Lyceum) — "Map/Rooms/Tools" metaphors
- Real-world implementations across multiple Claude Code projects

**FSP-authored (not from the above sources):** Scoring rubric (X/16, X/18), letter grading scale (A/B/C/F), 60/30/10 prioritization heuristic, structural metrics (M1–M5), base anti-patterns #1–8
