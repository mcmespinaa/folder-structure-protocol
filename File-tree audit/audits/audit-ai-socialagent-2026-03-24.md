---
type: audit
date: 2026-03-24
status: superseded
summary: "Sweden Ecosystem Directory audit — strong workspace routing, oversized CLAUDE.md and root-level clutter. All 3 fixes applied same day."
---

# Folder Architecture Audit: ai-socialagent (Sweden Ecosystem Directory)

**Date:** 2026-03-24
**Path:** `/Users/MC/ai-socialagent/`

## Scores

| Layer | Score | Max | Grade |
|-------|-------|-----|-------|
| Layer 1 (Map) | 4 | 6 | B |
| Layer 2 (Rooms) | 5 | 6 | B |
| Layer 3 (Tools) | 3 | 4 | B |
| **Total** | **12** | **16** | **B** |

Grading: A = 90%+, B = 63%+, C = 38%+, F = below 38%

## Anti-Patterns Found

- **#1 Oversized CLAUDE.md** — 63 non-blank lines (limit: 50). The Tech Stack table and Folder Structure tree could move into a CONTEXT file or be trimmed, since the routing table already points to the right places.
- **#6 Flat dump at root** — 11 files at the project root, including 4 PNG screenshots, a standalone HTML prototype, a JSON dataset, and README. These aren't routed or conventioned. Group assets into a folder or move them into `app/public/`.

## Structural Metrics

| Metric | Value | Status |
|--------|-------|--------|
| M1 Depth Ratio | 3.0 | OK |
| M2 File Density | 3.8 | OK |
| M3 Naming Consistency | 95% | OK |
| M4 Convention Coverage | 75% | Warning — root-level PNGs, scripts/, and standalone files have no declared naming convention |
| M5 Context-to-Content | 4.2% | Warning — slightly below ideal (5–15%), but acceptable given the app has many source files |

## Top 3 Fixes (Priority Order)

1. **Clean the project root** — Move `cookiebot-banner*.png`, `hero-*.png` into `app/public/` or a `docs/` folder. Move `sweden-ecosystem-map.html` into `app/public/` or a `prototypes/` folder. This clears the flat dump and makes the root a clean navigation surface. (Structure fix — 60%)
2. **Trim CLAUDE.md under 50 lines** — Remove the Folder Structure tree (the routing table already serves this purpose) and the Tech Stack table (move it to CONTEXT-data.md or a README). Keep only: identity, routing table, naming conventions, rules. (Routing fix — 30%)
3. **Add "What to Avoid" sections to all three CONTEXT files** — Each workspace has clear processes but no explicit anti-patterns. Even brief ones help (e.g., CONTEXT-frontend: "Don't use purple-to-blue gradients", CONTEXT-data: "Don't modify the JSON file directly — Supabase is the source of truth"). (Routing fix — 30%)

## Detailed Findings

### Layer 1: The Map

CLAUDE.md is well-structured with an excellent routing table (8 task types mapped to context files, workspaces, and skills). Identity is clear in the first line. Naming conventions cover 6 file types. Rules section is practical.

**Issues:**
- **63 lines exceeds the 50-line limit.** The Folder Structure tree (20 lines) duplicates what the routing table already conveys. The Tech Stack table (12 lines) is useful but belongs in a context file.
- **No Read/Write markings.** `public/`, `sweden-ecosystem-database.json`, and `messages/` should be marked as reference/read-only where applicable.

### Layer 2: The Rooms

Three CONTEXT files, one per workspace (data, frontend, seo). All are concise (34–36 lines), work-focused, and list key files. This is a strong Layer 2.

**One consistent gap across all three:** No "What to Avoid" section. Each workspace has implicit constraints (e.g., "Swedish is primary", "tracking is dormant") but they live in CLAUDE.md rules, not in the workspace where they'd be loaded when working.

### Layer 3: The Tools

Three skills, each mapped to specific task types via the routing table:
- `socialdesigner` — UI/design work
- `seo-aio` — SEO, schema, tracking
- `senior-engineer` — Architecture, data, deployment

All are markdown-only with reference files. Scoping is good (routing table directs each skill to its workspace).

**One concern:** `socialdesigner` and `senior-engineer` open with personality-heavy framing ("You are a world-class UI/UX designer", "You are a senior engineer"). This style of prompting is more brittle across model updates than task-focused instructions. Current models handle it fine, but it's worth noting.

### File Tree Snapshot

```
ai-socialagent/
├── CLAUDE.md
├── CONTEXT-data.md
├── CONTEXT-frontend.md
├── CONTEXT-seo.md
├── README.md
├── cookiebot-banner-3000.png          ← should move to app/public/ or docs/
├── cookiebot-banner.png               ← should move
├── hero-desktop.png                   ← should move
├── hero-mobile.png                    ← should move
├── sweden-ecosystem-database.json     ← seed data (referenced in CONTEXT-data)
├── sweden-ecosystem-map.html          ← prototype (referenced in CLAUDE.md)
├── scripts/
│   ├── add-long-descriptions.py
│   └── migrate-to-jsonb.sql
├── .claude/
│   ├── settings.json
│   └── skills/
│       ├── senior-engineer/
│       │   ├── SKILL.md
│       │   └── references/
│       ├── seo-aio/
│       │   ├── SKILL.md
│       │   └── references/
│       └── socialdesigner/
│           ├── SKILL.md
│           └── references/
└── app/                               ← Next.js application
    ├── src/
    │   ├── app/
    │   │   ├── [locale]/              ← Pages (home, initiative, map, list, cookies)
    │   │   ├── api/                   ← 4 API routes
    │   │   ├── layout.tsx
    │   │   ├── robots.ts
    │   │   └── sitemap.ts
    │   ├── components/                ← 16 React components
    │   ├── lib/                       ← Utilities (data, schema, seo, pixels, supabase, utm)
    │   ├── i18n/                      ← next-intl config
    │   ├── types/                     ← TypeScript types
    │   └── middleware.ts
    ├── messages/                      ← Translation files (en, sv)
    ├── supabase/                      ← Migrations and seeds
    ├── public/                        ← Static assets
    └── scripts/                       ← App-level scripts
```
