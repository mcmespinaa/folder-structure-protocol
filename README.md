# Folder Audit

A Claude Code skill that evaluates any project's folder architecture against a three-layer routing system. It scores your project structure, flags anti-patterns, and outputs a graded report with prioritized fixes.

Built for developers using [Claude Code](https://docs.anthropic.com/en/docs/claude-code) who want their AI assistant to navigate their codebase effectively -- but the methodology works for any AI coding tool or even manual project organization.

## What It Does

When you run `/folder-audit` on a project, it:

1. **Snapshots the file tree** -- maps your project structure
2. **Scores three layers** (0-16 total):
   - **Layer 1: The Map** (CLAUDE.md) -- does the root config route to the right place? (0-6)
   - **Layer 2: The Rooms** (CONTEXT.md) -- do workspaces have clear context files? (0-6)
   - **Layer 3: The Tools** (Skills/Playbooks) -- are automation tools well-scoped? (0-4)
3. **Checks 7 anti-patterns** -- oversized configs, missing routing, flat dumps, stale context, etc.
4. **Measures 5 structural metrics** -- depth ratio, file density, naming consistency, convention coverage, context-to-content ratio
5. **Generates a graded report** with a letter grade (A/B/C/F) and top 3 prioritized fixes

## Quick Start

### Option A: Copy the skill into your project

```bash
# From your project root
mkdir -p .claude/skills/folder-audit
curl -o .claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-audit/main/.claude/skills/folder-audit/SKILL.md
```

Then in Claude Code:

```
/folder-audit
```

Claude will ask which project to audit (or audit the current one).

### Option B: Clone the full repo

```bash
git clone https://github.com/mcmespinaa/folder-audit.git
cd folder-audit
```

This gives you the skill, the methodology playbook, example audit reports, and the CLAUDE.md routing setup.

### Option C: Install as a global skill

```bash
# Copy to your global Claude Code skills directory
mkdir -p ~/.claude/skills/folder-audit
curl -o ~/.claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-audit/main/.claude/skills/folder-audit/SKILL.md
```

This makes `/folder-audit` available in every project.

## Usage

### Run an audit

In Claude Code, use any of these:

```
/folder-audit
folder audit on this project
evaluate the folder structure of ~/Projects/my-app
score this project's organization
check folder quality
```

### What you get

A markdown report saved to `File-tree audit/audits/audit-{project-name}-YYYY-MM-DD.md` (if using the full repo) or printed to your conversation:

```
# Folder Architecture Audit: my-project
Date: 2026-03-24
Path: /Users/you/Projects/my-project

## Scores
| Layer              | Score | Max | Grade |
|--------------------|-------|-----|-------|
| Layer 1 (Map)      | 4     | 6   | B     |
| Layer 2 (Rooms)    | 5     | 6   | B     |
| Layer 3 (Tools)    | 3     | 4   | B     |
| **Total**          | **12**| **16** | **B** |

## Anti-Patterns Found
- #1 Oversized CLAUDE.md -- trim to under 50 lines
- #6 Flat dump at root -- move assets into subfolders

## Top 3 Fixes (Priority Order)
1. Clean the project root ...
2. Trim CLAUDE.md under 50 lines ...
3. Add "What to Avoid" sections to CONTEXT files ...
```

### Grading scale

| Score | Grade | Meaning |
|-------|-------|---------|
| 15-16 | A | Production-ready. Agent works autonomously with high accuracy. |
| 10-14 | B | Functional. Agent works but needs occasional re-steering. |
| 6-9   | C | Fragile. Agent frequently guesses wrong or wastes tokens. |
| 0-5   | F | Broken. Start over with minimum viable structure. |

## The Three-Layer Routing System

The audit evaluates projects against this architecture:

```
Layer 1: CLAUDE.md (The Map)      --> Routes to the right workspace
Layer 2: CONTEXT.md (The Room)    --> Routes to the right files
Layer 3: Playbooks/Skills (Tools) --> Solves specific, recurring friction
```

### Layer 1: The Map (CLAUDE.md)

A root config file under 50 lines that contains:
- Project identity (2-3 sentences)
- Routing table (Task / Go-to / Read)
- Naming conventions per file type
- Read-only folder markings

### Layer 2: The Rooms (CONTEXT.md)

One context file per workspace folder, under 80 lines, covering:
- What happens in this workspace
- What good output looks like
- What to avoid
- Key files listing

### Layer 3: The Tools (Skills/Playbooks)

Reusable procedures that emerge from friction -- not pre-built scaffolding. Each should be:
- Born from repeated manual steps
- Scoped to specific workspaces
- Written in markdown (not code)
- Anti-fragile to model updates

## The 60/30/10 Rule

When fixing audit findings, prioritize:

- **60%** of value comes from traditional structure (folders, naming, files)
- **30%** from routing and rules (CLAUDE.md, CONTEXT.md, conventions)
- **10%** from the AI interactions themselves

Fix the 60% first. No amount of prompt engineering compensates for bad structure.

## Project Structure

```
folder-audit/
├── CLAUDE.md                          # Layer 1 -- routes to the right workspace
├── README.md                          # This file
├── .claude/
│   └── skills/
│       └── folder-audit/
│           └── SKILL.md               # The audit skill (copy this to use)
└── File-tree audit/                   # Methodology workspace
    ├── CONTEXT.md                     # Layer 2 -- workspace context
    ├── Playbook - Folder Architecture Evaluation.md  # Full methodology
    └── audits/                        # Example audit reports
        ├── audit-folder-structure-2026-03-24.md
        └── audit-ai-socialagent-2026-03-24.md
```

### What each file does

| File | Purpose | You need it? |
|------|---------|-------------|
| `.claude/skills/folder-audit/SKILL.md` | The executable audit skill -- 8-step procedure with scoring rubrics, anti-pattern checklist, metrics, and report template | **Yes** -- this is the skill |
| `CLAUDE.md` | Root routing config for this project | Only if cloning the full repo |
| `File-tree audit/CONTEXT.md` | Workspace context for the audit methodology | Only if cloning the full repo |
| `File-tree audit/Playbook - *.md` | Deep methodology reference -- scoring rationale, anti-pattern details, structural metric formulas | Optional -- for understanding the "why" |
| `File-tree audit/audits/*.md` | Example audit reports from real projects | Optional -- to see what output looks like |

## Scoring Criteria

### Layer 1: The Map (6 points)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | Root config exists | CLAUDE.md at project root |
| 2 | Under 50 lines | Non-blank line count |
| 3 | Routing table | Task / Go-to / Read columns |
| 4 | Naming conventions | Explicit patterns per file type |
| 5 | Identity section | 2-3 sentences: what, who |
| 6 | Read-only folders marked | Assets, references labeled |

### Layer 2: The Rooms (6 points, averaged across workspaces)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | CONTEXT.md exists | One per workspace |
| 2 | Under 80 lines | Concise |
| 3 | Describes the work (80%+) | Not Claude's personality |
| 4 | "What to Avoid" section | Explicit anti-patterns |
| 5 | Fresh | Updated in active period |
| 6 | Key files listed | Points to important files |

### Layer 3: The Tools (4 points)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | Born from friction | Not pre-built |
| 2 | Scoped to workspaces | Not loaded globally |
| 3 | Markdown instructions | Not coded orchestration |
| 4 | Anti-fragile | Model updates help, not break |

### 7 Anti-Patterns

| # | Anti-Pattern | Signal |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md | > 50 lines |
| 2 | Missing routing table | No task-to-folder mapping |
| 3 | Too many workspaces | 5+ from day one |
| 4 | Personality over context | Context describes behavior, not work |
| 5 | Stale context | > 6 weeks without update |
| 6 | Flat dump | 10+ files at one level |
| 7 | Built before used | Elaborate system, no work product |

### 5 Structural Metrics

| Metric | Formula | Ideal |
|--------|---------|-------|
| M1 Depth Ratio | Max depth / top-level folders | 2-4 |
| M2 File Density | Avg files per folder | 3-10 |
| M3 Naming Consistency | % following pattern per folder | > 80% |
| M4 Convention Coverage | % file types with declared convention | 100% |
| M5 Context-to-Content | Context files / content files | 5-15% |

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and authenticated
- A project folder to audit

No dependencies, no build step, no configuration. The skill is a single markdown file.

## Sources

- Clief Notes Module 3: Folder Architecture (Quantum Quill Lyceum)
- File-Tree-as-Architecture pattern
- Anthropic [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

## License

MIT
