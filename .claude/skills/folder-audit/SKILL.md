---
name: folder-audit
description: "Evaluates any project's folder architecture against the three-layer routing system. Scores Layer 1 (Map/CLAUDE.md), Layer 2 (Rooms/CONTEXT.md), Layer 3 (Tools/Skills), checks 7 anti-patterns, measures 5 structural metrics. Outputs a graded audit report with prioritized fixes. Use when: setting up Claude Code on a project, reviewing workspace quality, debugging degraded AI output, onboarding a new codebase, scoring folder structure, checking folder quality, evaluating project organization."
---

# Folder Architecture Audit Skill

Evaluates a project folder against the three-layer routing system and produces a scored audit report.

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

### Step 2: Score Layer 1 -- The Map (CLAUDE.md)

Read the root CLAUDE.md (or equivalent root config). Score each criterion 0 or 1:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Root config exists | CLAUDE.md or equivalent at project root |
| 2 | Length under 50 lines | Count non-blank lines |
| 3 | Routing table present | Table with Task / Go-to / Read columns (minimum) |
| 4 | Naming conventions defined | Explicit patterns per file type, not "follow existing" |
| 5 | Identity section | 2-3 sentences: what the project is, who it's for |
| 6 | Read-only folders marked | Assets, references, vendor clearly labeled |

**Layer 1 Score: X/6**

### Step 3: Score Layer 2 -- The Rooms (CONTEXT.md)

For each major workspace folder, check for a CONTEXT.md. Score each criterion 0 or 1, then average across workspaces:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | CONTEXT.md exists | One per workspace |
| 2 | Under 80 lines | Concise, not exhaustive |
| 3 | Describes the work (80%+) | Project/audience/constraints, not Claude personality |
| 4 | Includes "What to Avoid" | Explicit anti-patterns |
| 5 | Fresh | Updated within project's active period |
| 6 | Lists key files | Points to important files in the workspace |

**Layer 2 Score: X/6** (average across workspaces, rounded)

### Step 4: Score Layer 3 -- The Tools

Inventory skills, playbooks, or automation files. Score each criterion 0 or 1:

| # | Criterion | Check |
|---|-----------|-------|
| 1 | Skills born from friction | Created after repeated manual steps, not pre-built |
| 2 | Scoped to workspaces | Loaded only where needed, not globally |
| 3 | Instructions are markdown | Not coded orchestration logic |
| 4 | Anti-fragile | Model updates simplify, not break |

**Layer 3 Score: X/4**

If the project is new and has no Layer 3 yet, score N/A (not a penalty). Layer 3 should not exist until friction is identified.

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
- Do the anti-pattern flags match the layer scores? (e.g., if #1 "Oversized CLAUDE.md" is found, Layer 1 criterion #2 should score 0)
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
| Layer 1 (Map) | X | 6 | [A/B/C/F] |
| Layer 2 (Rooms) | X | 6 | [A/B/C/F] |
| Layer 3 (Tools) | X | 4 | [A/B/C/F or N/A] |
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

### Layer 1: The Map
[What exists, what's missing, specific issues]

### Layer 2: The Rooms
[Per-workspace assessment]

### Layer 3: The Tools
[Inventory and assessment, or note if premature]

### File Tree Snapshot
[The tree from Step 1]
```

### Step 9: Imprint Structure Rules (Persistent Memory)

After the audit, offer to write structural guardrails into the **audited project's** CLAUDE.md. This is how the audit "sticks" — future Claude sessions in that project will follow the structure automatically.

**What to write:** Append a `## Structure Rules` section to the target project's CLAUDE.md (create one if it doesn't exist). Generate the rules from the audit findings — not a generic template. Only include rules that address actual gaps found.

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
- Keep the section under 30 lines — these are guardrails, not documentation
- Include the audit date so staleness is visible
- If the project scored A (15-16), skip imprinting — the structure is already self-documenting

### Step 10: Escalate to Skill Creation (Integration with skill-creator)

If the audit reveals **repeated friction** (the same type of file is created manually more than twice, or the same process is described in multiple CONTEXT.md files), recommend creating a skill for it.

**When to escalate:**
- Anti-pattern #7 is clear (no premature tooling) AND repeated manual steps are visible
- A process described in a CONTEXT.md is complex enough (5+ steps) to warrant a SKILL.md
- The user has done the same structural fix across multiple projects

**How to escalate:**
Tell the user: "This project has friction worth automating: [describe]. Use the skill-creator workflow to build a skill for it." If the skill-creator skill is available in the project, offer to invoke it directly.

Do NOT auto-create skills. Layer 3 should always be a conscious decision, not an audit side-effect.

---

## What NOT to Penalize

- No Layer 3 on a new project (premature tooling is worse than none)
- Simple projects with only 1-2 workspaces (not every project needs four)
- Missing "What to Avoid" if the project is in early exploration
- Lack of frontmatter if the project doesn't use Obsidian

For full methodology background, scoring rationale, and the 60/30/10 rule, see the Playbook: `File-tree audit/Playbook - Folder Architecture Evaluation.md`
