---
type: playbook
date: 2026-03-24
status: active
summary: "How to evaluate any project's folder architecture against the three-layer routing system"
---

# Playbook: Folder Architecture Evaluation

> **Purpose:** Systematic audit of any project folder to assess how well it supports AI-assisted work (Claude Code, Cursor, etc.) and human navigability.
> **Time:** 10–20 minutes per project
> **Source:** Clief Notes three-layer routing (Module 3), file-tree-as-architecture research, multi-project implementations
> **When to use:** Before onboarding Claude Code to a project, during quarterly reviews, or when AI output quality degrades.

---

## The Evaluation Framework

Every project folder is evaluated against **three layers**, **seven anti-patterns**, and **five structural metrics**. The result is a score sheet with specific, actionable fixes.

---

## Layer 1: The Map (CLAUDE.md / Root Config)

The root file that tells any agent — human or AI — where things are and how to navigate.

### What to check

| Criterion | Pass | Fail |
|-----------|------|------|
| Root config file exists | CLAUDE.md or equivalent at project root | No root navigation file |
| Length | Under 50 lines | Over 50 lines (context hiding inside it) |
| Routing table present | Task / Go-to / Read columns (minimum 3) | No routing table, or just a folder listing |
| Naming conventions defined | Explicit patterns per file type | "Follow existing patterns" or nothing |
| Identity section | 2–3 sentences: what, who, why | Personality description or multi-paragraph brief |
| Read-only folders marked | Clearly labeled (assets, references, vendor) | Everything implicitly read-write |

### Scoring

- **6/6** — Excellent. The map routes cleanly.
- **4–5** — Functional. Minor gaps won't block work but will cause drift.
- **0–3** — Broken. Agent will guess, waste tokens, or produce inconsistent output.

---

## Layer 2: The Rooms (Workspace Context Files)

Each workspace (major folder representing a distinct mode of work) should have its own context file.

### What to check

| Criterion | Pass | Fail |
|-----------|------|------|
| CONTEXT.md exists per workspace | One per major folder | Missing or only at root |
| Length | Under 80 lines | Over 80 (move details into child files) |
| Describes the work (80%+) | Project, audience, constraints, what good looks like | Describes Claude's personality instead |
| Includes "What to Avoid" | Explicit anti-patterns for this workspace | Only positive instructions |
| Freshness | Updated within project's active period | Stale (>6 weeks with no review on active project) |
| Key files listed | Points to the important files in the workspace | Agent must discover everything by scanning |

### Scoring

- **6/6** — Rooms are well-lit. Agent knows exactly what to do in each space.
- **4–5** — Usable. Some guessing required.
- **0–3** — Dark rooms. Agent will read wrong files or produce off-target output.

---

## Layer 3: The Tools (Skills / Playbooks / Automation)

Reusable processes wired into specific workspaces. This layer should only exist after real friction has been identified — never on day one.

### What to check

| Criterion | Pass | Fail |
|-----------|------|------|
| Skills exist only where friction was real | Born from repeated manual steps | Pre-built "just in case" skills with no usage |
| Skills are scoped to workspaces | Loaded only where needed | Every skill available everywhere |
| Instructions are markdown, not code | Prompt files, not Python orchestration | Coded routing logic that breaks on model updates |
| Anti-fragile to model updates | A new model feature simplifies the skill | A new model feature breaks the skill |

### Scoring

- **4/4** — Tools are sharp and well-placed.
- **2–3** — Some premature tooling or over-broad loading.
- **0–1** — Either no tools when needed, or tools everywhere creating noise.

---

## Seven Anti-Patterns (Quick Scan)

Run through these fast. Each one found is a deduction and a specific fix.

| # | Anti-Pattern | Signal | Fix |
|---|-------------|--------|-----|
| 1 | **Oversized CLAUDE.md** | Root file > 50 lines | Pull content into workspace CONTEXT.md files |
| 2 | **Missing routing table** | No task-to-folder mapping | Add 3-column table: Task / Go to / Read |
| 3 | **Too many workspaces** | 5+ workspaces from day one | Merge to 2–3. Split only when mental modes diverge |
| 4 | **Personality over context** | Context files describe Claude's behavior, not the work | Flip to 80% work description, 20% behavior |
| 5 | **Stale context** | CONTEXT.md hasn't been updated since project evolved | Add "Last updated" line, review now |
| 6 | **Flat dump** | 10+ files at one level, no subfolders | Group by workspace, then by stage/type |
| 7 | **Built before used** | Elaborate system with no real work product yet | Tear down to minimum, start working, grow from use |

---

## Five Structural Metrics

These measure the raw folder quality independent of AI tooling.

### M1: Depth Ratio

**Formula:** Max folder depth / Number of top-level folders

- **Ideal:** 2–4 (enough structure, not over-nested)
- **Too shallow (< 2):** Flat dump problem
- **Too deep (> 5):** Over-engineering, unclear boundaries

### M2: File Density

**Formula:** Average files per folder (excluding config/hidden)

- **Ideal:** 3–10 files per folder
- **Too sparse (< 3):** Premature splitting — merge folders
- **Too dense (> 10):** Needs subfolders

### M3: Naming Consistency

**Check:** Do files within the same folder follow the same naming pattern?

- `kebab-case.md` throughout? Pass.
- Mix of `camelCase`, `snake_case`, spaces, and dates? Fail.
- Are file types distinguishable by name? (e.g., `*_draft.md` vs `*_final.md`)

### M4: Convention Coverage

**Check:** What percentage of file types have an explicit naming convention in CLAUDE.md?

- **100%** — Every file type has a declared pattern
- **50–99%** — Some file types are undocumented (agent will improvise)
- **< 50%** — Naming is ad-hoc (agent has no pattern to follow)

### M5: Context-to-Content Ratio

**Formula:** Number of context/config files / Total content files

- **Ideal:** 5–15% (one context file per workspace, not per subfolder)
- **Too low (< 5%):** No guidance — agent flies blind
- **Too high (> 20%):** More bureaucracy than work product

---

## The Evaluation Process

### Step 1: Snapshot the Tree

```bash
# Generate a clean tree (exclude hidden dirs except .claude/, node_modules, etc.)
find . -not -path '*/node_modules/*' -not -path '*/dist/*' -not -path '*/build/*' -not -name '.DS_Store' | grep -v '/\.' | sort | head -150
# Then separately capture .claude/ contents:
find ./.claude -type f 2>/dev/null | sort
```

Or in Claude Code: ask for a file tree of the project root.

### Step 2: Score Layer 1 (The Map)

Open the root CLAUDE.md (or equivalent). Run through the 6-point checklist. Record score.

### Step 3: Score Layer 2 (The Rooms)

For each major workspace folder, check for CONTEXT.md. Run through the 6-point checklist per workspace. Average the scores.

### Step 4: Score Layer 3 (The Tools)

Inventory any skills, playbooks, or automation. Run through the 4-point checklist. Record score.

### Step 5: Anti-Pattern Scan

Walk through all 7 anti-patterns. Flag each one found.

### Step 6: Measure Structural Metrics

Calculate M1–M5. Note any out-of-range values.

### Step 7: Verify Scores

Cross-check scores against evidence, confirm anti-pattern flags align with layer scores, and check that metrics support anti-pattern findings. Fix inconsistencies before generating the report.

### Step 8: Generate the Report

Use the report template defined in SKILL.md (`.claude/skills/folder-audit/SKILL.md`, Step 8). The skill is the single source of truth for the output format.

### Grading Scale

| Total Score | Grade | Meaning |
|-------------|-------|---------|
| 15–16 | A | Production-ready. Agent works autonomously with high accuracy. |
| 10–14 | B | Functional. Agent works but needs occasional re-steering. |
| 6–9 | C | Fragile. Agent frequently guesses wrong or wastes tokens. |
| 0–5 | F | Broken. Start over with minimum viable structure. |

---

## The 60/30/10 Rule

When evaluating, remember where the real value lives:

- **60%** — Traditional structure (folders, naming, files)
- **30%** — Routing and rules (CLAUDE.md, CONTEXT.md, conventions)
- **10%** — The AI interactions themselves

If the structure is bad, no amount of prompt engineering fixes it. Fix the 60% first.

---

## Debugging AI Failures (Trace Downward)

When AI output is wrong, trace the cause from output backward:

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Output format wrong | Missing or unclear output spec | Fix naming conventions or add format rules |
| Content is off-topic | Wrong context loaded, or stale context | Fix routing table or update CONTEXT.md |
| Ignoring instructions | Reading wrong file | Check routing — agent entered wrong workspace |
| Fundamentally confused | Identity/task framing broken | Rewrite CLAUDE.md identity section |
| Hallucinating facts | Model-layer issue | Add retrieval, reference files, or verification steps |

Most problems are rows 1–3. Almost nobody's problems are row 5.

---

## Quick Reference: What Goes Where

| Content | Location | Why |
|---------|----------|-----|
| Project identity, folder map, routing table | CLAUDE.md (root) | Loaded every session, routes everything |
| Workspace process, audience, constraints | CONTEXT.md (per workspace) | Loaded only when working in that space |
| Reusable multi-step procedures | Playbooks (Layer 3) | Loaded only when that process is triggered |
| File naming patterns | CLAUDE.md conventions section | Agent needs this before creating any file |
| "What to avoid" rules | CONTEXT.md per workspace | Constraints are workspace-specific |
| Style guides, brand voice, references | Reference files in workspace | Keeps context files short, details accessible |

---

## MWP Pipeline Projects (Extended Evaluation)

Some projects use numbered stage folders (`01_research/`, `02_script/`, etc.) to implement sequential, human-reviewed AI workflows. This pattern is formalized as Model Workspace Protocol (MWP), which extends the three-layer system into a five-layer context hierarchy.

When stage folders are detected, the audit runs an additional assessment covering three areas the base audit cannot evaluate:

### Stage Contracts (MWP Layer 2)

Each numbered stage should have a CONTEXT.md that acts as a contract: what the stage reads (Inputs), what it does (Process), and what it writes (Outputs). The Inputs table should explicitly label files as Layer 3 (reference — stable across runs) or Layer 4 (working — per-run artifacts). This scoping is what prevents context window bloat and the "lost in the middle" degradation documented in Liu et al. (2024).

### Reference vs Working Separation (MWP Layers 3-4)

MWP distinguishes between reference material (voice guides, design systems, conventions — the factory) and working artifacts (research output, script drafts, specifications — the product). These require different model attention: reference material should be internalized as constraints, working artifacts should be processed as input. The folder structure should make this distinction visible through separate directories (`references/` vs `output/`).

### Pipeline Architecture

Stages should be numbered, each doing one job. Output of stage N becomes input to stage N+1 via file handoff. Humans can review and edit at every boundary. Individual stages can be re-run without restarting the entire pipeline (incremental recompilation).

### Relationship to Base Score

The MWP score (X/18) is reported separately from the base score (X/16). They measure different things:
- **Base score:** Is this project well-organized for any AI tool?
- **MWP score:** Is this pipeline well-structured for staged sequential workflows?

A project can score well on one and poorly on the other. Both matter for their respective contexts.

For the full MWP specification, see: Van Clief & McDermott, "Interpretable Context Methodology: Folder Structure as Agent Architecture" (2026).

---

## Sources

- Clief Notes Module 3: Folder Architecture (Quantum Quill Lyceum)
- File-Tree-as-Architecture research (2026-03-16)
- Van Clief & McDermott, "Interpretable Context Methodology: Folder Structure as Agent Architecture" (2026)
- Playbook: Folder Structure Setup for Claude Code Projects
- Playbook: File-Tree Agent Scaffold
- Real-world implementations across multiple Claude Code projects
