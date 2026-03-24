# Folder Architecture Audit: Folder Structure
**Date:** 2026-03-24
**Path:** /Users/MC/Projects/Folder Structure

## Scores

| Layer | Score | Max | Grade |
|-------|-------|-----|-------|
| Layer 1 (Map) | 6 | 6 | A |
| Layer 2 (Rooms) | 6 | 6 | A |
| Layer 3 (Tools) | 4 | 4 | A |
| **Total** | **16** | **16** | **A** |

Grading: A = 15-16, B = 10-14, C = 6-9, F = 0-5

## Anti-Patterns Found

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md (>50 lines) | Clear — 20 non-blank lines |
| 2 | Missing routing table | Clear — 3-column table present |
| 3 | Too many workspaces (5+ from day one) | Clear — 2 workspaces |
| 4 | Personality over context | Clear — all context describes work |
| 5 | Stale context (>6 weeks) | Clear — project is same-day current |
| 6 | Flat dump (10+ files at one level) | Clear — max 3 files per folder |
| 7 | Built before used (elaborate system, no work product) | Clear — 3 audit reports exist as work product |

No anti-patterns found.

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | 1.5 | Warning — slightly shallow (ideal 2–4) |
| M2 File Density | 1.75 | Warning — sparse (ideal 3–10) |
| M3 Naming Consistency | 86% | OK — 1 of 7 files breaks convention (`-v2` suffix) |
| M4 Convention Coverage | 100% | OK — all file types have declared patterns |
| M5 Context-to-Content | 29% | Warning — high (ideal 5–15%), more guidance than content |

## Top 3 Fixes (Priority Order)

1. **Rename or remove the `-v2` audit file.** The file `audit-folder-structure-2026-03-24-v2.md` breaks the `audit-{project-name}-YYYY-MM-DD.md` convention. Either rename it with a distinguishing project name (e.g., `audit-folder-structure-revised-2026-03-24.md`) or delete it if the v3 supersedes it. This protects naming consistency (M3) as the audits folder grows.

2. **Let the project grow before worrying about metrics.** M2 (file density) and M5 (context-to-content ratio) are both out of range, but this is natural for a young, focused project with only 2 workspaces. As more audits accumulate, both metrics will self-correct. No structural change needed — just awareness.

3. **Consider adding depth only when a third workspace emerges.** M1 (depth ratio) is slightly low at 1.5. This isn't a problem today with 2 workspaces, but if a third workspace is added (e.g., templates, references), the ratio will improve naturally. Don't add structure preemptively.

## Detailed Findings

### Layer 1: The Map

CLAUDE.md is exemplary. At 20 non-blank lines it's well under the 50-line ceiling. It contains:

- **Identity:** Clear 1-sentence description of purpose (line 3)
- **Workspaces table:** Lists both folders with purpose and read/write permissions — the read-only marking on `.claude/skills/folder-audit/` satisfies criterion 6
- **Routing table:** 3-column Task/Go-to/Read format with 3 routes
- **Naming conventions:** 4 explicit patterns covering all file types in the project
- **Rules:** Two concise operational rules including the 60/30/10 priority

No issues found. This is a reference-quality CLAUDE.md.

### Layer 2: The Rooms

One workspace evaluated: `File-tree audit/`

The CONTEXT.md is 30 lines — well under the 80-line limit. It covers:

- What happens in this workspace (4-step process)
- What good looks like (4 criteria)
- What to avoid (4 anti-patterns)
- Key files with descriptions

The `.claude/skills/folder-audit/` workspace is correctly treated as read-only tooling rather than a workspace needing its own CONTEXT.md — the SKILL.md serves the equivalent purpose within the skills convention.

### Layer 3: The Tools

One skill (`folder-audit`) and one playbook present.

- **folder-audit skill:** Born from the need to repeatedly evaluate folder structures. Scoped to its own directory. Pure markdown instructions (SKILL.md). No coded orchestration. Model-agnostic — a better model would execute it more accurately, not break it.
- **Playbook:** Comprehensive methodology document. Serves as reference material, not runtime instructions. Correctly separated from the skill's executable procedure.

The skill-to-playbook separation is clean: SKILL.md is the executable procedure, the Playbook is the reference methodology. Neither duplicates the other.

### File Tree Snapshot

```
/Users/MC/Projects/Folder Structure
├── CLAUDE.md
├── File-tree audit/
│   ├── CONTEXT.md
│   ├── Playbook - Folder Architecture Evaluation.md
│   └── audits/
│       ├── audit-ai-socialagent-2026-03-24.md
│       ├── audit-folder-structure-2026-03-24.md
│       └── audit-folder-structure-2026-03-24-v2.md
└── .claude/
    └── skills/
        └── folder-audit/
            └── SKILL.md
```
