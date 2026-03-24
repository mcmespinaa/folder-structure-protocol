---
name: folder-audit
description: "Evaluates any project's folder architecture against the ICM five-layer context hierarchy. Scores Layer 0 (Map/CLAUDE.md), Layer 1 (Rooms/CONTEXT.md), Layer 2 (Stage Contracts), Layer 3 (Reference Material), Layer 4 (Working Artifacts), plus Tools assessment. Checks 14 anti-patterns (8 base + 6 ICM), measures 5 structural metrics. Outputs a graded audit report with prioritized fixes. Use when: setting up Claude Code on a project, reviewing workspace quality, debugging degraded AI output, onboarding a new codebase, scoring folder structure, checking folder quality, evaluating project organization."
---

# Folder Architecture Audit Skill

Evaluates a project folder against the ICM five-layer context hierarchy and produces a scored audit report. The base audit scores Layers 0–1 and Tools (X/16). If ICM pipeline stages are detected, an extended assessment covers Layers 2–4 (X/18).

## Trigger

Use this skill when:
- The user asks to audit, evaluate, review, or score a project's folder structure
- The user is onboarding Claude Code to an existing project
- The user reports degraded AI output quality and the cause may be structural
- The user says "folder audit", "evaluate structure", "score this project", "check folder quality", or similar

## Procedure

### Step 1: Snapshot the File Tree

Generate a file tree of the target project. Exclude hidden directories (except `.claude/`), `node_modules/`, `dist/`, `build/`, `__pycache__/`, and other generated directories.

```
Use two Bash commands:
1. find [project-path] -not -path '*/node_modules/*' -not -path '*/dist/*' -not -path '*/build/*' -not -path '*/__pycache__/*' -not -name '.DS_Store' | grep -v '/\.' | sort | head -150
2. find [project-path]/.claude -type f 2>/dev/null | sort
```

The second command captures the `.claude/` directory (skills, settings) which the first command's `grep -v '/\.'` excludes.

### Step 2: Score Layer 0 -- The Map (CLAUDE.md)

Read the root CLAUDE.md (or equivalent root config). Score each criterion 0 or 1:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Root config exists | CLAUDE.md or equivalent at project root |
| 2 | Length under 50 lines | Count non-blank lines |
| 3 | Routing table present | Table with Task / Go-to / Read columns (minimum) |
| 4 | Naming conventions defined | Explicit patterns per file type, not "follow existing" |
| 5 | Identity section | 2-3 sentences: what the project is, who it's for |
| 6 | Read-only folders marked | Assets, references, vendor clearly labeled |

**Layer 0 Score: X/6**

### Step 3: Score Layer 1 -- The Rooms (CONTEXT.md)

For each major workspace folder, check for a CONTEXT.md. Score each criterion 0 or 1, then average across workspaces:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | CONTEXT.md exists | One per workspace |
| 2 | Under 80 lines | Concise, not exhaustive |
| 3 | Describes the work (80%+) | Project/audience/constraints, not Claude personality |
| 4 | Includes "What to Avoid" | Explicit anti-patterns |
| 5 | Fresh | Updated within project's active period |
| 6 | Lists key files | Points to important files in the workspace |

**Layer 1 Score: X/6** (average across workspaces, rounded)

### Step 4: Score Tools

Inventory skills, playbooks, or automation files. Score each criterion 0 or 1:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Skills born from friction | Created after repeated manual steps, not pre-built |
| 2 | Scoped to workspaces | Loaded only where needed, not globally |
| 3 | Instructions are markdown | Not coded orchestration logic |
| 4 | Anti-fragile | Model updates simplify, not break |

**Tools Score: X/4**

If the project is new and has no tools yet, score N/A (not a penalty). Tools should not exist until friction is identified.

### Step 5: Anti-Pattern Scan

Check for each anti-pattern. Mark found/clear:

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md (>50 lines) | |
| 2 | Missing routing table | |
| 3 | Too many workspaces (5+ from day one) | |
| 4 | Personality over context (context files describe behavior not work) | |
| 5 | Stale context (>6 weeks without update on active project) | |
| 6 | Flat dump (10+ files at one level, no subfolders) | |
| 7 | Built before used (elaborate system, no work product) | |
| 8 | Monolithic context loading (all instructions in one file/prompt instead of scoped per workspace) | |

### Step 6: Calculate Structural Metrics

| Metric | Formula | Ideal | Value |
|--------|---------|-------|-------|
| M1 Depth Ratio | Max folder depth / top-level folders | 2-4 | |
| M2 File Density | Avg files per folder | 3-10 | |
| M3 Naming Consistency | % of files following consistent pattern per folder | >80% | |
| M4 Convention Coverage | % of file types with declared naming convention | 100% | |
| M5 Context-to-Content | Context files / total content files | 5-15% | |

### Step 7: Verify Scores

Before generating the report, cross-check:
- Does each score have specific evidence in the findings? If a criterion scored 1, confirm the evidence exists. If 0, confirm the gap is real.
- Do the anti-pattern flags match the layer scores? (e.g., if #1 "Oversized CLAUDE.md" is found, Layer 0 criterion #2 should score 0)
- Do the metrics align with anti-patterns? (e.g., if M2 File Density is >10, #6 "Flat dump" should be flagged)

Fix any inconsistencies before proceeding.

### Step 8: Generate Report

Output the audit report using this structure:

```markdown
# Folder Architecture Audit: [Project Name]
**Date:** [today]
**Path:** [project path]

## Scores

| Layer | Score | Max | Grade |
|-------|-------|-----|-------|
| Layer 0 (Map) | X | 6 | [A/B/C/F] |
| Layer 1 (Rooms) | X | 6 | [A/B/C/F] |
| Tools | X | 4 | [A/B/C/F or N/A] |
| **Total** | **X** | **16** | **[Grade]** |

Grading: A = 15-16, B = 10-14, C = 6-9, F = 0-5

## Anti-Patterns Found

[List each found anti-pattern with a one-line fix]

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | X | [OK/Warning] |
| M2 File Density | X | [OK/Warning] |
| M3 Naming Consistency | X% | [OK/Warning] |
| M4 Convention Coverage | X% | [OK/Warning] |
| M5 Context-to-Content | X% | [OK/Warning] |

## Top 3 Fixes (Priority Order)

1. [Most impactful fix -- what to do and why]
2. [Second fix]
3. [Third fix]

## Detailed Findings

### Layer 0: The Map
[What exists, what's missing, specific issues]

### Layer 1: The Rooms
[Per-workspace assessment]

### Tools
[Inventory and assessment, or note if premature]

### File Tree Snapshot
[The tree from Step 1]
```

### Step 9: Imprint Structure Rules (Persistent Memory)

After the audit, offer to write structural guardrails into the **audited project's** CLAUDE.md. This is how the audit "sticks" -- future Claude sessions in that project will follow the structure automatically.

**What to write:** Append a `## Structure Rules` section to the target project's CLAUDE.md (create one if it doesn't exist). Generate the rules from the audit findings -- not a generic template. Only include rules that address actual gaps found.

```markdown
## Structure Rules
<!-- Generated by folder-audit on [date]. Re-audit to update. -->

### Naming Conventions
[Extracted from audit: the conventions the project SHOULD follow, stated as rules]

### Folder Boundaries
[Which folders exist, what goes in each, what NOT to put where]

### File Placement
- New [type] files go in: [path]
- New [type] files go in: [path]
- Never create files in: [paths] (read-only)

### When Creating New Folders
- Only add a workspace folder when an existing one exceeds 10 files or mixes two unrelated concerns
- Every new workspace folder must include a CONTEXT.md before adding content
```

**Rules for imprinting:**
- Always ask the user before writing to their project's CLAUDE.md
- If CLAUDE.md already has a Structure Rules section, replace it (not duplicate)
- Keep the section under 30 lines -- these are guardrails, not documentation
- Include the audit date so staleness is visible
- If the project scored A (15-16), skip imprinting -- the structure is already self-documenting

### Step 10: Escalate to Skill Creation (Integration with skill-creator)

If the audit reveals **repeated friction** (the same type of file is created manually more than twice, or the same process is described in multiple CONTEXT.md files), recommend creating a skill for it.

**When to escalate:**
- Anti-pattern #7 is clear (no premature tooling) AND repeated manual steps are visible
- A process described in a CONTEXT.md is complex enough (5+ steps) to warrant a SKILL.md
- The user has done the same structural fix across multiple projects

**How to escalate:**
First, check if a skill-creator skill exists: look for `.claude/skills/skill-creator/SKILL.md` or `~/.claude/skills/skill-creator/SKILL.md`. Then tell the user: "This project has friction worth automating: [describe]. Use the skill-creator workflow to build a skill for it." If the skill-creator skill is available, offer to invoke it directly. If not, suggest installing it from `anthropics/skills`.

Do NOT auto-create skills. Tools should always be a conscious decision, not an audit side-effect.

---

### Step 11: ICM Pipeline Audit (Conditional)

**When to run:** Only if the file tree from Step 1 shows numbered stage folders (e.g., `01_research/`, `02_script/`, `03_production/`) or a `stages/` directory. These indicate the project follows Interpretable Context Methodology (ICM) -- a method that uses folder structure as agent orchestration. If no stage folders are found, skip this step entirely and note "ICM: Not detected" in the report.

The base audit (Steps 2-4) covers Layers 0-1 and Tools. This step evaluates Layers 2-4, which are where ICM's pipeline value lives.

#### 11a: Score Stage Contracts (ICM Layer 2)

For each numbered stage folder, check for a CONTEXT.md that functions as a stage contract. Score each criterion 0 or 1, then average across stages:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Stage CONTEXT.md exists | One per numbered stage folder |
| 2 | Inputs table present | Lists specific files with Layer 3 (reference) vs Layer 4 (working) labels |
| 3 | Process section defined | Clear instructions for what the stage does |
| 4 | Outputs section defined | Names the output files and their destination (e.g., `-> output/`) |
| 5 | Single responsibility | Stage does one job -- not research AND scripting in the same stage |
| 6 | Scoped context | Stage only references files it needs, not everything in the workspace |

**Stage Contracts Score: X/6**

#### 11b: Score Reference/Working Separation (ICM Layers 3-4)

Check whether the project distinguishes stable reference material from per-run working artifacts:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Reference folders exist | `references/`, `_config/`, or `shared/` directories present |
| 2 | Output folders exist | `output/` directory per stage for working artifacts |
| 3 | Reference files are stable | Files in reference folders don't change between runs (no dated output mixed in) |
| 4 | Output files are per-run | Output folders contain artifacts from pipeline execution, not configuration |
| 5 | Stage inputs distinguish types | CONTEXT.md Inputs table labels which files are reference vs working |
| 6 | Factory configured once | `_config/` or `setup/` folder holds workspace-wide settings (voice, style, conventions) |

**Reference/Working Score: X/6**

#### 11c: Score Pipeline Architecture

Check the structural properties of the stage pipeline:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Stages are numbered | Folders use numeric prefixes that encode execution order |
| 2 | Handoffs are file-based | Output of stage N is readable as input to stage N+1 |
| 3 | No stage reads everything | Each stage loads scoped context, not the entire workspace |
| 4 | Review gates are possible | Human can inspect and edit output/ before the next stage runs |
| 5 | Incremental re-run supported | Can re-run stage N without re-running stages 1 through N-1 |
| 6 | Pipeline is self-documenting | Reading CONTEXT.md files top to bottom explains the entire workflow |

**Pipeline Score: X/6**

#### 11d: ICM Anti-Patterns

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Monolithic stage (one stage does everything) | |
| 2 | Missing output folders (stages write to random locations) | |
| 3 | Mixed reference and working files (config and output in same folder) | |
| 4 | No Inputs table (stage CONTEXT.md doesn't specify what to load) | |
| 5 | Circular dependencies (stage N reads from stage N+2) | |
| 6 | Over-staged (trivial steps split into separate stages unnecessarily) | |

#### 11e: Calculate ICM Score

| Component | Score | Max |
|-----------|-------|-----|
| Stage Contracts (11a) | X | 6 |
| Reference/Working (11b) | X | 6 |
| Pipeline Architecture (11c) | X | 6 |
| **ICM Total** | **X** | **18** |

ICM Grading: A = 16-18, B = 11-15, C = 6-10, F = 0-5

#### 11f: Incorporate into Report

Add an `## ICM Pipeline Assessment` section to the report after Structural Metrics:

```markdown
## ICM Pipeline Assessment

**Detected:** Yes -- [N] stages found
**ICM Score:** X/18 ([Grade])

| Component | Score | Max | Grade |
|-----------|-------|-----|-------|
| Stage Contracts | X | 6 | [A/B/C/F] |
| Reference/Working Separation | X | 6 | [A/B/C/F] |
| Pipeline Architecture | X | 6 | [A/B/C/F] |

### ICM Anti-Patterns Found
[List each found with a one-line fix]

### Stage-by-Stage Assessment
[For each stage: what its contract says, whether inputs/outputs are clean, any issues]
```

The ICM score is reported **separately** from the base score (X/16). The two are complementary:
- Base score tells you if the project is well-organized for any AI tool
- ICM score tells you if the pipeline architecture is well-structured for staged workflows

A project can score A on the base audit and F on ICM (good structure, bad pipeline), or vice versa. Both matter.

---

## What NOT to Penalize

- No tools on a new project (premature tooling is worse than none)
- Simple projects with only 1-2 workspaces (not every project needs four)
- Missing "What to Avoid" if the project is in early exploration
- Lack of frontmatter if the project doesn't use Obsidian
- No ICM structure on a non-pipeline project (ICM is for sequential staged workflows, not every project)
- Only 2-3 stages in a pipeline (fewer well-defined stages beat many thin ones)
- Missing `_config/` folder if reference material lives in stage-local `references/` directories (both are valid)

For full methodology background, scoring rationale, and the 60/30/10 rule, see the Playbook: `File-tree audit/Playbook - Folder Architecture Evaluation.md`
