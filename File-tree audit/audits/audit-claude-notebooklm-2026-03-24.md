# Folder Architecture Audit: claude-notebooklm
**Date:** 2026-03-24
**Path:** /Users/MC/claude-notebooklm

## Scores

| Layer | Score | Max | Grade |
|-------|-------|-----|-------|
| Layer 1 (Map) | 3 | 6 | C |
| Layer 2 (Rooms) | 0 | 6 | F |
| Layer 3 (Tools) | 3 | 4 | B |
| **Total** | **6** | **16** | **F (38%)** |

Grading: A = 90%+, B = 63%+, C = 38%+, F = below 38%

## Anti-Patterns Found

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md (>50 lines) | **FOUND** — 125 lines. Contains full folder tree, routing table, naming conventions, config docs, and rules all in one file. |
| 2 | Missing routing table | Clear — routing table exists and is detailed |
| 3 | Too many workspaces (5+ from day one) | **FOUND** — 5 top-level folders (brands, content-engine, docs, output, session-docs) plus 14 skills, 2 workflows, hooks, and scripts inside `.claude/` |
| 4 | Personality over context (context files describe behavior not work) | Clear — CLAUDE.md describes the work, not personality |
| 5 | Stale context (>6 weeks without update on active project) | **FOUND** — No CONTEXT.md files exist at all. Last session-doc is March 7, suggesting possible inactivity, but the project has content from March 17-18. |
| 6 | Flat dump (10+ files at one level, no subfolders) | **FOUND** — `brands/ces/` has 13 files at one level with no subfolders. Root has 10 non-hidden items. |
| 7 | Built before used (elaborate system, no work product) | **BORDERLINE** — 14 skills and 2 workflows is substantial infrastructure. Output folder has limited work product (7 files). Session-docs show 10 sessions. The system is elaborate relative to visible output. |

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | 10 / 5 = 2.0 | OK |
| M2 File Density | 2.7 files/folder | Warning — below ideal range of 3–10 |
| M3 Naming Consistency | 90% | OK — session-docs 100%, brands consistent, docs inconsistent (mixed naming) |
| M4 Convention Coverage | 80% | Warning — conventions declared for 8 content types, but docs/, output/, and brands/ files aren't covered |
| M5 Context-to-Content | 0% (0 CONTEXT.md / 271 files) | Warning — no context files exist outside CLAUDE.md |

## Top 3 Fixes (Priority Order)

1. **Split the CLAUDE.md (structure — 60%).** At 125 lines, it's doing the job of CLAUDE.md + 4 CONTEXT.md files. Extract workspace-specific context into CONTEXT.md files for `brands/ces/`, `content-engine/`, `output/`, and `docs/`. The root CLAUDE.md should be ≤50 lines: identity, folder map (one line per folder), routing table, and rules. Move the detailed folder tree, config file docs, and naming conventions into the appropriate CONTEXT.md files.

2. **Add CONTEXT.md to each workspace folder (routing — 30%).** The project has zero CONTEXT.md files. Each major folder needs one:
   - `brands/ces/CONTEXT.md` — what the brand assets are, how they relate, what to avoid when writing for this brand
   - `content-engine/CONTEXT.md` — that this is the portable plugin package, read-only vs. canonical source distinction
   - `output/CONTEXT.md` — what goes here, naming rules, what "done" looks like
   - `session-docs/CONTEXT.md` — what gets logged and when

3. **Fix the ig-reels skill (AI tuning — 10%).** The `ig-reels` skill is missing its SKILL.md file, and the `web-design` skill has an empty `references/` directory. Either complete these skills or remove the empty scaffolding to avoid confusing the model.

## Detailed Findings

### Layer 1: The Map

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Root config exists | 1 | CLAUDE.md present at root |
| 2 | Length under 50 lines | 0 | 125 lines — 2.5x the limit |
| 3 | Routing table present | 1 | Detailed human routing table + registry.json reference |
| 4 | Naming conventions defined | 1 | Explicit patterns for 8 content types |
| 5 | Identity section | 0 | Opens with "Content Engine" and a description, but no clear "who it's for" audience statement |
| 6 | Read-only folders marked | 0 | No folders marked read-only. `brands/`, `docs/` should likely be read-only references |

**Layer 1 Score: 3/6**

The CLAUDE.md is well-written and comprehensive — the problem is that it's doing too much. It contains a full folder tree with descriptions, a routing table, naming conventions, config file documentation, and operational rules. This information is valuable but should be distributed across CONTEXT.md files in the relevant workspaces.

### Layer 2: The Rooms

**Layer 2 Score: 0/6**

No CONTEXT.md files exist anywhere in the project. This is the single biggest structural gap. The CLAUDE.md is compensating by being oversized, but this means the model loads everything on every task instead of loading only the context relevant to the current workspace.

Workspace folders that need CONTEXT.md:
- `brands/ces/` — 13 brand asset files with no context about their relationships or usage rules
- `content-engine/` — complex plugin package with its own skills/, hooks/, and config
- `output/` — generated content with no guidance on what belongs here
- `docs/` — 3 unrelated files (PDF, MD, DOCX) with no explanation
- `session-docs/` — 10 session logs with no context about purpose or conventions

### Layer 3: The Tools

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Skills born from friction | 1 | Session-docs show skills being built in response to real tasks (YouTube search, Shopwired sync, vault-save) |
| 2 | Scoped to workspaces | 1 | Skills are under `.claude/skills/` with per-skill references and scripts |
| 3 | Instructions are markdown | 1 | All SKILL.md files are markdown with step-by-step procedures |
| 4 | Anti-fragile | 0 | Heavy reliance on shell scripts calling specific APIs (GHL, ShopWired, YouTube). The `content-engine/` duplication creates a sync burden. |

**Layer 3 Score: 3/4**

The skills layer is the strongest part of this project. 14 skills with consistent structure (SKILL.md + references/ + scripts/), a template for creating new ones, validation hooks, and a machine-readable registry. The main concerns:
- `ig-reels` skill is missing its SKILL.md
- `web-design/references/` is empty
- The dual-location problem (`.claude/skills/` vs `content-engine/skills/`) creates maintenance friction
- `.fuse_hidden0000002c00000001` file in distribute/scripts/ is debris from a mounted filesystem

### File Tree Snapshot

```
claude-notebooklm/
├── CLAUDE.md                          ← 125 lines (oversized)
├── README.md
├── .gitignore
├── .mcp.json
├── .env                               ← API keys (gitignored)
├── locations.json
├── ghl_accounts_map.json
├── ghl_post_log.md
├── newsletter_send_log.md
├── .claude/
│   ├── settings.json
│   ├── settings.local.json
│   ├── registry.json
│   ├── resolve_location.sh
│   ├── hooks/                         ← 2 validators
│   ├── scripts/                       ← 3 shared utilities
│   ├── skills/                        ← 14 skills (+ _template)
│   │   ├── _template/
│   │   ├── blog/                      ← 3 refs, 7 scripts
│   │   ├── distribute/                ← 11 refs, 11 scripts, 10 steps
│   │   ├── ig-reels/                  ← MISSING SKILL.md
│   │   ├── linkedin/                  ← 1 ref
│   │   ├── newsletter/                ← 1 ref, 4 scripts
│   │   ├── presentation/              ← 4 refs
│   │   ├── render/                    ← 5 scripts, 6 templates
│   │   ├── save-to-drive/
│   │   ├── shopwired-sync/            ← 1 ref, 6 scripts
│   │   ├── skill-checker/             ← 1 ref, 1 script
│   │   ├── vault-save/
│   │   ├── web-build/                 ← 2 scripts
│   │   ├── web-design/                ← EMPTY references/
│   │   └── youtube-search/            ← 1 script
│   └── workflows/                     ← 2 workflows
│       ├── blog-and-promote/
│       └── content-to-launch/
├── brands/
│   └── ces/                           ← 13 brand files, no CONTEXT.md
├── content-engine/                    ← Portable plugin package (duplicate of skills)
├── docs/                              ← 3 unrelated files
├── output/                            ← 7 files + 1 subfolder
└── session-docs/                      ← 10 session logs
```
