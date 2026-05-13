# Folder Structure Protocol (FSP) — Playbook & Comparison with ICM

**Author:** MC Espina
**Date:** 2026-03-24
**Version:** 1.0

---

## What This Document Covers

1. What ICM is and what FSP is
2. How they relate — methodology vs tooling
3. Side-by-side comparison
4. The full FSP playbook — evaluation framework, five skills, workflows, and ICM pipeline concepts

---

## Part 1: ICM vs FSP — What They Are

### Interpretable Context Methodology (ICM)

**Repo:** [RinDig/Interpreted-Context-Methdology](https://github.com/RinDig/Interpreted-Context-Methdology)
**Authors:** Jake Van Clief & D. McDermott
**Paper:** "Interpretable Context Methodology: Folder Structure as Agent Architecture" (arXiv:2603.16021, 2026)

ICM is a **system architecture** that replaces multi-agent framework orchestration (CrewAI, LangChain, AutoGen) with filesystem structure. The core thesis: if context for each stage already exists as files in a well-organized folder hierarchy, you don't need multiple agents. A single AI agent walks through numbered folders sequentially, with humans reviewing intermediate outputs between stages.

**ICM defines:**

- A five-layer context hierarchy (Layers 0–4)
- Stage contracts with Inputs/Process/Outputs tables
- The factory/product distinction (reference material vs working artifacts)
- Five design principles (one stage one job, plain text interface, layered context, every output is an edit surface, configure the factory not the product)
- 15 conventions across architectural, quality, and onboarding categories

**ICM ships:**

- The specification and design principles
- Three pre-built workspaces (script-to-animation, course-deck-production, workspace-builder)
- A meta-workspace that generates new ICM workspaces

**ICM does not ship:**

- Diagnostic tooling (no scoring, no audit)
- Execution tooling (no stage runner, no validator)
- Anti-pattern detection
- Structural metrics

### Folder Structure Protocol (FSP)

**Repo:** [mcmespinaa/folder-structure-protocol](https://github.com/mcmespinaa/folder-structure-protocol)
**Author:** MC Espina

FSP is a **tooling layer** built on ICM. It operationalizes the ICM architecture into five runnable Claude skills that audit, build, execute, review, and validate folder-based AI workflows.

**FSP provides:**

- `/folder-audit` — Scores any project against the ICM hierarchy (16-point base + 18-point pipeline assessment)
- `/pipeline-scaffold` — Creates ICM pipelines from a domain description
- `/run-stage` — Executes a single stage with contract-enforced scoped context loading
- `/stage-review` — Verifies stage output and checkpoint criteria before advancing
- `/validate-pipeline` — Walks the contract chain to find broken handoffs

**FSP adds on top of ICM:**

- A scoring rubric (X/16 base, X/18 ICM)
- Letter grading (A/B/C/F)
- The 60/30/10 prioritization heuristic
- 14 anti-patterns (8 base + 6 ICM-specific)
- 5 structural metrics (depth ratio, file density, naming consistency, convention coverage, context-to-content ratio)
- Structure imprinting (writing rules into a project's CLAUDE.md)
- Pipeline execution with guardrails

---

## Part 2: Side-by-Side Comparison

### Scope

| Dimension | ICM | FSP |
|-----------|-----|-----|
| **Type** | Methodology / specification | Tooling / implementation |
| **Core idea** | Folder structure replaces agent frameworks | Score, build, and run folder-based workflows |
| **Output** | Design principles + example workspaces | Runnable Claude skills + scored audit reports |
| **Audience** | Anyone building AI pipelines | Claude Code / Claude.ai users |

### Architecture Coverage

| Concept | ICM | FSP |
|---------|-----|-----|
| Five-layer hierarchy (Layers 0–4) | Defined | Evaluated and enforced |
| Stage contracts (Inputs/Process/Outputs) | Defined | Created by `/pipeline-scaffold`, executed by `/run-stage` |
| Factory/Product separation | Defined as a principle | Checked by `/validate-pipeline`, enforced by `/run-stage` |
| Human review gates | Core principle | Implemented as mandatory pause in `/run-stage`, verified by `/stage-review` |
| 15 conventions | Defined | Not directly scored (some overlap with anti-patterns) |

### Tooling

| Capability | ICM | FSP |
|------------|-----|-----|
| Folder structure scoring | Not provided | `/folder-audit` — 16pt base + 18pt ICM |
| Pipeline scaffolding | 3 pre-built workspaces + workspace-builder | `/pipeline-scaffold` — generates for any domain |
| Stage execution with scoped loading | Described in principles | `/run-stage` — contract-enforced, reads only declared inputs |
| Stage review / quality gates | Described in principles | `/stage-review` — checkpoint verification + downstream readiness |
| Contract chain validation | Not provided | `/validate-pipeline` — handoff-by-handoff validation |
| Anti-pattern detection | Not provided | 14 patterns (8 structural + 6 ICM-specific) |
| Structural metrics | Not provided | 5 quantitative metrics |
| Structure imprinting | Not provided | Writes guardrails into project's CLAUDE.md |

### Philosophy

| Principle | ICM | FSP |
|-----------|-----|-----|
| **Plain text** | All communication via markdown | All skills are markdown files |
| **Human-in-the-loop** | Humans review at every stage boundary | `/run-stage` never auto-advances |
| **Single agent** | One agent walks through folders | Skills guide one Claude session |
| **Scoped context** | Agents load only what they need | `/run-stage` reads only Inputs table files |
| **Anti-fragile** | Model updates should simplify, not break | Skills are markdown instructions, not coded orchestration |

### The Relationship

```
ICM (the building code)
  ↓ defines
FSP (the inspector + contractor)
  ├── /folder-audit    = building inspector (scores compliance)
  ├── /pipeline-scaffold = contractor (builds to spec)
  ├── /run-stage       = operator (runs with guardrails)
  ├── /stage-review    = quality inspector (verifies output)
  └── /validate-pipeline = structural engineer (checks connections)
```

ICM tells you **what** good pipeline architecture looks like.
FSP tells you **whether** yours is good, **builds** one if you don't have one, and **runs** it with guardrails.

In ICM's own terms, FSP is a **Layer 3 skill** — a bundled, reusable procedure scoped to a specific task, written in markdown, invoked on demand. But ICM itself is the architecture that defines what skills are and where they live.

---

## Part 3: The Evaluation Framework

### The 60/30/10 Rule

When fixing folder architecture, prioritize in this order:

- **60%** — Traditional structure (folders, naming, file grouping)
- **30%** — Routing and rules (CLAUDE.md, CONTEXT.md, conventions)
- **10%** — The AI interactions themselves

The ratio is a prioritization heuristic, not an empirical measurement. The directional claim is supported by industry data:

- 44–65% of developers blame missing context — not bad prompts — for poor AI-generated code (Qodo, 2025)
- Routing files like AGENTS.md cut agent runtime by 29% (Sherwood, 2025)
- Anthropic frames the core challenge as "curating what enters the model's attention budget" (Schluntz et al., 2025)

If the structure is bad, no amount of prompt engineering fixes it. Fix the 60% first.

### The Five-Layer Context Hierarchy

```
Layer 0: CLAUDE.md (The Map)       → Routes to the right workspace
Layer 1: CONTEXT.md (The Rooms)    → Routes to the right files within a workspace
Layer 2: Stage Contracts           → Declares inputs, process, outputs per stage
Layer 3: References (The Factory)  → Stable material: voice, style, conventions
Layer 4: Working (The Product)     → Per-run artifacts: drafts, data, output
```

**Layer 0: The Map (CLAUDE.md)** — A root config under 50 lines containing project identity, routing table, naming conventions, and read-only markings. Scored 0–6.

**Layer 1: The Rooms (CONTEXT.md)** — One per workspace folder, under 80 lines. Describes what happens there, what good output looks like, what to avoid, and lists key files. Scored 0–6.

**Tools (Skills/Playbooks)** — Reusable procedures born from friction. Scoped to workspaces, written in markdown. Scored 0–4. Not a numbered ICM layer.

**Layers 2–4 (ICM Pipelines)** — Only assessed if stage folders are detected. Stage contracts declare Inputs/Process/Outputs. References are internalized as constraints. Working artifacts are transformed as material. Scored 0–18 separately.

### Scoring

**Base Score: X/16**

| Layer | Max | What Passes |
|-------|-----|-------------|
| Layer 0 (Map) | 6 | CLAUDE.md exists, under 50 lines, has routing table, naming conventions, identity, read-only markings |
| Layer 1 (Rooms) | 6 | CONTEXT.md per workspace, under 80 lines, describes work, includes anti-patterns, fresh, lists key files |
| Tools | 4 | Born from friction, scoped, markdown, anti-fragile |

**ICM Score: X/18** (pipeline projects only)

| Component | Max | What Passes |
|-----------|-----|-------------|
| Stage Contracts | 6 | CONTEXT.md per stage with Inputs/Process/Outputs, single responsibility, scoped context |
| Reference/Working Separation | 6 | Separate directories, stable references, per-run output, labeled inputs, factory configured once |
| Pipeline Architecture | 6 | Numbered stages, file-based handoffs, scoped loading, review gates, incremental re-run, self-documenting |

**Grading:**

| Score | Grade | Meaning |
|-------|-------|---------|
| 15–16 | A | Production-ready. Agent works autonomously. |
| 10–14 | B | Functional. Occasional re-steering needed. |
| 6–9 | C | Fragile. Agent frequently guesses wrong. |
| 0–5 | F | Broken. Start with minimum viable structure. |

### 14 Anti-Patterns

**8 Base Anti-Patterns** (any project):

| # | Anti-Pattern | Signal |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md | > 50 lines |
| 2 | Missing routing table | No task-to-folder mapping |
| 3 | Too many workspaces | 5+ from day one |
| 4 | Personality over context | Context describes behavior, not work |
| 5 | Stale context | > 6 weeks without update |
| 6 | Flat dump | 10+ files at one level |
| 7 | Built before used | Elaborate system, no work product |
| 8 | Monolithic context loading | All instructions in one file |

**6 ICM Anti-Patterns** (pipeline projects):

| # | Anti-Pattern | Signal |
|---|-------------|--------|
| 1 | Monolithic stage | One stage does everything |
| 2 | Missing output folders | Stages write to random locations |
| 3 | Mixed reference and working | Config and output in same folder |
| 4 | No Inputs table | Stage contract doesn't specify what to load |
| 5 | Circular dependencies | Stage N reads from stage N+2 |
| 6 | Over-staged | Trivial steps split unnecessarily |

### 5 Structural Metrics

| Metric | Formula | Ideal |
|--------|---------|-------|
| M1 Depth Ratio | Max folder depth / top-level folders | 2–4 |
| M2 File Density | Avg files per folder | 3–10 |
| M3 Naming Consistency | % files following pattern per folder | > 80% |
| M4 Convention Coverage | % file types with declared convention | 100% |
| M5 Context-to-Content | Context files / content files | 5–15% |

---

## Part 4: The Five Skills

### Lifecycle

```
Audit ──→ Scaffold ──→ Execute ──→ Review ──→ Validate
  ↑                                              |
  └──────────── fix and iterate ─────────────────┘
```

### Skill 1: `/folder-audit` — Score any project

Snapshots the file tree, scores Layers 0–1 + Tools (X/16), checks anti-patterns, measures structural metrics, generates a graded report with Top 3 fixes.

If numbered stage folders are detected, runs an extended ICM assessment (X/18).

Post-audit: offers to imprint structure rules into the audited project's CLAUDE.md. Recommends skill creation if repeated friction is found.

### Skill 2: `/pipeline-scaffold` — Create a pipeline

Generates numbered stage folders with CONTEXT.md contracts (Inputs/Process/Outputs/Review Checkpoint), `references/` and `output/` directories per stage, a `_config/` folder, and a root CONTEXT.md.

After scaffolding: add reference material, review contracts, run `/validate-pipeline`, then start with `/run-stage 01`.

### Skill 3: `/run-stage` — Execute one stage

Reads the stage's contract, loads only declared inputs (Layer 3 as constraints, Layer 4 as material), follows the Process instructions, writes declared outputs, pauses for human review.

**Guardrails:** Never reads undeclared files. Never writes to undeclared locations. Never auto-advances.

### Skill 4: `/stage-review` — Verify before advancing

Checks output existence, runs Review Checkpoint criteria, assesses quality, confirms downstream readiness. Read-only — never modifies files.

**Outputs a recommendation:** Advance, Revise, or Re-run.

### Skill 5: `/validate-pipeline` — Check the chain

Walks the full contract chain: Stage 1 Outputs → Stage 2 Inputs → Stage 2 Outputs → ... Flags broken handoffs, factory/product cross-contamination, missing contract sections, and structural anti-patterns.

---

## Part 5: Typical Workflows

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
   → Verify contracts connect correctly
4. /run-stage 01
   → Execute first stage
5. /stage-review 01
   → Verify output before advancing
6. /run-stage 02 → /stage-review 02 → repeat...
```

### Workflow C: Debug a Broken Pipeline

```
1. /validate-pipeline → find broken handoffs
2. Fix the CONTEXT.md files flagged
3. /validate-pipeline → confirm chain is clean
4. /run-stage {broken-stage} → re-run
5. /stage-review {broken-stage} → verify fix
```

### Workflow D: Periodic Health Check

```
1. /folder-audit → catch structural drift
2. /validate-pipeline → catch contract drift
3. Apply fixes from both reports
4. Update CONTEXT.md freshness dates
```

---

## Part 6: Factory vs Product — The Key Distinction

ICM's most important architectural insight is that not all context is equal. The model must handle two types differently:

| Type | Layer | Location | Model Behavior | Example |
|------|-------|----------|---------------|---------|
| Factory | 3 | `references/`, `_config/` | **Internalize** as constraints | Voice guide, design system, conventions |
| Product | 4 | `output/`, prior stage output | **Transform** as material | Research notes, script draft, data |

The model should *embody* factory material (write in this voice, follow these conventions) but *transform* product material (convert this research into a script, edit this draft).

The folder structure makes this visible. `/run-stage` loads each type with the correct instruction. `/validate-pipeline` checks that the separation is maintained.

---

## Part 7: Debugging AI Failures

When AI output is wrong, trace from the symptom backward:

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Wrong format | Missing output spec | Fix naming conventions |
| Off-topic content | Wrong/stale context loaded | Fix routing or update CONTEXT.md |
| Ignoring instructions | Reading wrong file | Check routing table |
| Fundamentally confused | Identity/framing broken | Rewrite CLAUDE.md identity |
| Hallucinating | Model-layer issue | Add reference files or verification |

Most problems are rows 1–3. Almost nobody's problems are row 5.

---

## References

**ICM Specification:**
Van Clief, J., & McDermott, D. (2026). Interpretable context methodology: Folder structure as agentic architecture. *arXiv*. https://doi.org/10.48550/arXiv.2603.16021

**Supporting Research:**
- Schluntz, B., et al. (2025). Effective context engineering for AI agents. Anthropic. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Sherwood, P. (2025). Context engineering for coding agents. *martinfowler.com*. https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html
- Qodo. (2025). State of AI code quality 2025. https://www.qodo.ai/reports/state-of-ai-code-quality/
- Nussbaum, A. (2024). Context rot. Chroma Research. https://research.trychroma.com/context-rot

**FSP-Authored (not from the above):**
Scoring rubric (X/16, X/18), letter grading (A/B/C/F), 60/30/10 heuristic, structural metrics (M1–M5), base anti-patterns #1–8, skill implementations.

---

*This document combines the FSP playbook with a comparison to the ICM methodology it's built on. For the live tools, see [mcmespinaa/folder-structure-protocol](https://github.com/mcmespinaa/folder-structure-protocol). For the full ICM specification, see [RinDig/Interpreted-Context-Methdology](https://github.com/RinDig/Interpreted-Context-Methdology).*
