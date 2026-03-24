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

Grading: A = 90%+, B = 63%+, C = 38%+, F = below 38%

## Anti-Patterns Found

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md (>50 lines) | Clear — 30 non-blank lines |
| 2 | Missing routing table | Clear — 7-row routing table present |
| 3 | Too many workspaces (5+ from day one) | Clear — 1 content workspace + 5 read-only skill folders |
| 4 | Personality over context | Clear — all context describes work |
| 5 | Stale context (>6 weeks) | Clear — project is same-day current |
| 6 | Flat dump (10+ files at one level) | Clear — max 3 files per folder |
| 7 | Built before used (elaborate system, no work product) | Clear — 3 audit reports exist as work product; 5 skills match real workflows |

No anti-patterns found.

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | 9 / 1 = 9.0 | Warning — high depth ratio due to nested `.claude/skills/` with only 1 top-level content folder |
| M2 File Density | 1.9 files/folder | Warning — sparse (ideal 3–10), natural for a project with many single-file skill folders |
| M3 Naming Consistency | 100% | OK — all audit files follow `audit-{name}-YYYY-MM-DD.md`, all skills follow `SKILL.md` |
| M4 Convention Coverage | 100% | OK — 5 naming patterns declared, covering all file types present |
| M5 Context-to-Content | 39% (7 context/config files / 18 total) | Warning — high ratio; more guidance than content |

## Top 3 Fixes (Priority Order)

1. **Let the project grow — metrics will self-correct (structure).** M1, M2, and M5 are all out of ideal range, but this is structural reality for a tooling project: most "files" are SKILL.md definitions in their own folders. As more audits accumulate in `File-tree audit/audits/`, file density rises and context-to-content ratio drops. No structural change needed.

2. **Consider whether 5 read-only skill workspaces need listing in CLAUDE.md (routing).** The workspaces table grew from 2 to 6 entries with the pipeline skills. This is fine today, but if more skills are added the table could become noisy. A possible future pattern: list only content workspaces in the table, and let the routing table handle skill discovery. Not urgent — the current size is manageable.

3. **Add stage folder naming convention examples to the Playbook (AI tuning).** CLAUDE.md declares the pattern `{NN}_{stage-name}/` for stage folders, but the Playbook methodology doesn't reference this convention. Adding a cross-reference ensures consistency when auditing projects that use ICM pipelines.

## Detailed Findings

### Layer 1: The Map

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Root config exists | 1 | CLAUDE.md at project root |
| 2 | Length under 50 lines | 1 | 30 non-blank lines (39 total) |
| 3 | Routing table present | 1 | 7-row table with Task / Go-to / Read columns |
| 4 | Naming conventions defined | 1 | 5 explicit patterns (audit reports, playbooks, context files, skill files, stage folders) |
| 5 | Identity section | 1 | Line 3: clear statement of purpose and scope |
| 6 | Read-only folders marked | 1 | All 5 skill folders marked "Read-only" in workspaces table |

**Layer 1 Score: 6/6**

The CLAUDE.md grew from 20 to 30 non-blank lines with the addition of 4 pipeline skills (workspace entries + routing rows + stage folder naming convention). Still well under the 50-line ceiling. The routing table now covers 7 tasks — comprehensive without being bloated.

### Layer 2: The Rooms

One content workspace evaluated: `File-tree audit/`

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | CONTEXT.md exists | 1 | Present at `File-tree audit/CONTEXT.md` |
| 2 | Under 80 lines | 1 | 33 lines |
| 3 | Describes the work (80%+) | 1 | 8-step process, quality criteria, anti-patterns |
| 4 | Includes "What to Avoid" | 1 | 4 explicit anti-patterns listed |
| 5 | Fresh | 1 | Updated today (pipeline execution step added) |
| 6 | Lists key files | 1 | Playbook and audits/ folder referenced |

**Layer 2 Score: 6/6**

The CONTEXT.md was updated to reference the 4 new pipeline skills (line 14). It correctly points users to the Playbook for methodology details rather than duplicating content. The `.claude/skills/` folders don't need CONTEXT.md — each SKILL.md serves the equivalent function within the skills convention.

### Layer 3: The Tools

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Skills born from friction | 1 | `folder-audit` was created from repeated manual audits. Pipeline skills (`pipeline-scaffold`, `run-stage`, `stage-review`, `validate-pipeline`) were created to support the ICM methodology — a real workflow need. |
| 2 | Scoped to workspaces | 1 | Each skill in its own `.claude/skills/{name}/` directory |
| 3 | Instructions are markdown | 1 | All 5 SKILL.md files are pure markdown with step-by-step procedures (86–131 non-blank lines each) |
| 4 | Anti-fragile | 1 | No coded orchestration. Procedures are model-agnostic instructions. A better model executes them more accurately. |

**Layer 3 Score: 4/4**

The project grew from 1 to 5 skills since the previous audit. All 4 new pipeline skills follow the established pattern:
- `pipeline-scaffold` (191 lines) — creates ICM pipeline structure
- `run-stage` (121 lines) — executes individual stages
- `stage-review` (153 lines) — reviews stage output before advancing
- `validate-pipeline` (147 lines) — validates contract chain integrity

Each has frontmatter with name/description, and substantive procedure content. The skill-to-playbook separation remains clean.

### File Tree Snapshot

```
/Users/MC/Projects/Folder Structure
├── CLAUDE.md                                          ← 39 lines, routing hub
├── README.md
├── File-tree audit/
│   ├── CONTEXT.md                                     ← 33 lines, workspace context
│   ├── Playbook - Folder Architecture Evaluation.md   ← Full methodology
│   └── audits/
│       ├── audit-ai-socialagent-2026-03-24.md
│       ├── audit-claude-notebooklm-2026-03-24.md
│       └── audit-folder-structure-2026-03-24.md
└── .claude/
    └── skills/
        ├── folder-audit/
        │   └── SKILL.md                               ← 131 non-blank lines
        ├── pipeline-scaffold/
        │   └── SKILL.md                               ← 131 non-blank lines
        ├── run-stage/
        │   └── SKILL.md                               ← 86 non-blank lines
        ├── stage-review/
        │   └── SKILL.md                               ← 113 non-blank lines
        └── validate-pipeline/
            └── SKILL.md                               ← 105 non-blank lines
```
