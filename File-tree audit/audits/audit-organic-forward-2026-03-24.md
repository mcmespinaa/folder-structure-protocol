# Folder Architecture Audit: Organic Forward
**Date:** 2026-03-24
**Path:** `/Users/MC/organic-forward`

## Scores (Post-Fix)

| Layer | Before | After | Max | Grade |
|-------|--------|-------|-----|-------|
| Layer 1 (Map) | 4 | 6 | 6 | A |
| Layer 2 (Rooms) | 3 | 5 | 6 | A |
| Layer 3 (Tools) | 3 | 4 | 4 | A |
| **Total** | **10** | **15** | **16** | **A (94%)** |

Grading: A = 90%+, B = 63%+, C = 38%+, F = below 38%

## Anti-Patterns Found

| # | Anti-Pattern | Found? |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md (>50 lines) | **Fixed** — trimmed from 101 to 30 lines (19 effective). Build commands and architecture moved to sub-project files. |
| 2 | Missing routing table | Clear — routing table present with naming conventions added |
| 3 | Too many workspaces (5+ from day one) | Accepted — 6 dirs justified by monorepo structure (4 independent sub-projects + docs + dashboard) |
| 4 | Personality over context | Clear — all context files describe work, not AI behavior |
| 5 | Stale context (>6 weeks without update) | Clear — project in active Phase 0 |
| 6 | Flat dump (10+ files at one level) | Remaining — `mercur-admin/src/hooks/api/` (55 files), `mercur-vendor/src/hooks/api/` (45 files). Inherited from MercurJS upstream, not worth refactoring. |
| 7 | Built before used (elaborate system, no work product) | **Fixed** — pruned from 30+ files to 5 essentials. Archived 9 agents, 3 hooks, 1 skill, 3 commands, 1 workflow to `docs/claude-tooling-archive/`. |

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | 18 / 6 = 3.0 | OK |
| M2 File Density | 3.1 files/folder | OK (low end) |
| M3 Naming Consistency | ~85% | OK — ts/tsx files follow kebab-case convention consistently |
| M4 Convention Coverage | 80% | OK — naming conventions table added to root CLAUDE.md (routes, components, modules, config) |
| M5 Context-to-Content | 1.0% | **Warning** — still below 5% target (80 context files / 8,006 total), but this is dominated by upstream MercurJS code. Among project-authored files the ratio is healthy. |

## Top 3 Fixes (Priority Order)

1. **Trim root CLAUDE.md to under 50 lines.** Move build commands to each sub-project's CLAUDE.md (they're already duplicated there). The root should only route — the per-workspace files already carry the detail. This single change clears the oversized anti-pattern and sharpens routing.

2. **Prune `.claude/` tooling to match actual friction.** Phase 0 has 9 agents, 5 hooks, a 5-stage certification workflow, and 2 skills — but no production code. Delete or archive anything not yet battle-tested. Keep only what you've actually needed. Premature tooling creates maintenance burden and confuses routing.

3. **Add CONTEXT.md to each workspace.** None of the 4 sub-projects or `docs/` have a CONTEXT.md. The sub-project CLAUDE.md files serve a similar purpose but miss key criteria: no "What to Avoid" sections, no key-file listings, no constraints. Adding a lightweight CONTEXT.md (or enriching existing CLAUDE.md) per workspace would push Layer 2 from C to B.

## Detailed Findings

### Layer 1: The Map

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Root config exists | 1 | `CLAUDE.md` at root |
| 2 | Length under 50 lines | 1 | **Fixed:** 30 lines total, 19 effective — pure routing hub |
| 3 | Routing table present | 1 | Good table with Task / Directory / Read First columns |
| 4 | Naming conventions defined | 1 | **Fixed:** Added naming conventions table (routes, components, modules, config) |
| 5 | Identity section | 1 | First paragraph: what the project is, who it's for, current phase |
| 6 | Read-only folders marked | 1 | `docs/` and `dashboard/` marked as read-only reference |

**Layer 1 Score: 6/6**

Root CLAUDE.md is now a clean routing hub. Build commands live in sub-project files. Architecture details moved to `mercur/CLAUDE.md`.

### Layer 2: The Rooms

Four sub-projects have CLAUDE.md files acting as room context:

| Workspace | Context file | Under 80 lines | Describes work | What to Avoid | Fresh | Key files |
|-----------|-------------|-----------------|----------------|---------------|-------|-----------|
| mercur/ | CLAUDE.md | Yes (68 lines) | Yes | **Yes** | Yes | **Yes** |
| b2b-storefront/ | CLAUDE.md | Yes (37 lines) | Yes | **Yes** | Yes | **Yes** |
| mercur-admin/ | CLAUDE.md | Yes (28 lines) | Yes | **Yes** | Yes | **Yes** |
| mercur-vendor/ | CLAUDE.md | Yes (30 lines) | Yes | **Yes** | Yes | **Yes** |
| docs/ | **CONTEXT.md** | Yes (15 lines) | Yes | **Yes** | Yes | **Yes** |
| dashboard/ | **CONTEXT.md** | Yes (13 lines) | Yes | **Yes** | Yes | **Yes** |

Averaged scores across 6 workspaces:
| # | Criterion | Score |
|---|-----------|-------|
| 1 | Context file exists | 1 |
| 2 | Under 80 lines | 1 |
| 3 | Describes the work | 1 |
| 4 | What to Avoid | 1 |
| 5 | Fresh | 1 |
| 6 | Lists key files | 0 (mercur-admin and mercur-vendor key files are approximate — need verification against actual active routes) |

**Layer 2 Score: 5/6**

All 6 workspaces now have context files with "What to Avoid" guardrails and key file listings. The one missing point is that key files in the dashboard frontends haven't been verified against actual development patterns yet.

### Layer 3: The Tools

**After pruning** (archived 25+ files to `docs/claude-tooling-archive/`):
- **2 hooks:** block-destructive, env-file-guard — defensive guards
- **1 skill:** frontend-design — used during Nordic Terroir storefront redesign
- **2 commands:** context-load, status — session management essentials

**Archived** (restorable from `docs/claude-tooling-archive/`):
- 9 agents, 3 hooks, 1 skill (skill-builder), 3 commands, 1 workflow (certification-verification)

| # | Criterion | Score | Notes |
|---|-----------|-------|-------|
| 1 | Born from friction | 1 | **Fixed:** remaining tools are all friction-born (hooks from security needs, frontend-design from redesign work, commands from session management) |
| 2 | Scoped to workspaces | 1 | frontend-design skill scoped to storefront work |
| 3 | Instructions are markdown | 1 | All remaining tools are .md or .sh files |
| 4 | Anti-fragile | 1 | Markdown-based, no brittle orchestration logic |

**Layer 3 Score: 4/4**

Lean tooling that matches the project phase. Archived items are available in `docs/claude-tooling-archive/` and can be restored when friction demands them.

### File Tree Snapshot

```
organic-forward/
├── CLAUDE.md                          ← Root map (30 lines, clean routing hub)
├── firebase.json
├── .claude/
│   ├── commands/                      ← 2 commands (context-load, status)
│   ├── hooks/                         ← 2 hooks (block-destructive, env-file-guard)
│   └── skills/
│       └── frontend-design/           ← Storefront design skill
├── b2b-storefront/
│   ├── CLAUDE.md                      ← With Key Files + What to Avoid
│   ├── backend/                       ← Starter kit demo (read-only)
│   └── storefront/                    ← Next.js 15 buyer frontend
├── mercur/
│   ├── CLAUDE.md                      ← With Architecture + Key Files + What to Avoid
│   ├── apps/backend/                  ← Medusa marketplace API
│   └── packages/modules/              ← Feature modules
├── mercur-admin/
│   ├── CLAUDE.md                      ← With Key Files + What to Avoid
│   └── src/                           ← React + Vite admin dashboard
├── mercur-vendor/
│   ├── CLAUDE.md                      ← With Key Files + What to Avoid
│   └── src/                           ← React + Vite vendor dashboard
├── dashboard/
│   ├── CONTEXT.md                     ← NEW
│   ├── index.html                     ← Firebase-hosted status page
│   └── status.json
└── docs/                              ← Architecture, plans, research (read-only)
    ├── CONTEXT.md                     ← NEW
    ├── claude-tooling-archive/        ← NEW — archived pre-built tooling
    ├── organic-forward-platform-plan.md
    ├── design-system-reference.md
    ├── commerce-protocols-landscape.md
    ├── architecture.drawio / .html
    └── printables/
```
