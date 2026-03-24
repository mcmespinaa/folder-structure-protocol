# Folder Structure Protocol

> **Disclaimer:** This is not an official scoring tool or industry standard. It's an opinionated scaffolding tool — a structured way to think about folder architecture for AI-assisted projects, guided by principles from [Interpretable Context Methodology](https://github.com/RinDig/Interpreted-Context-Methdology) (Van Clief & McDermott, 2026). The scores, grades, and heuristics (including the 60/30/10 rule) are practical shortcuts for personal and team use, not empirical measurements. Use what's useful, adapt what isn't, ignore what doesn't fit your project.

A set of Claude skills for evaluating folder architecture and running staged AI workflows. Built on the [ICM five-layer context hierarchy](https://doi.org/10.48550/arXiv.2603.16021) — the methodology that uses folder structure as agent orchestration.

Works with [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI), [Claude.ai](https://claude.ai) (Cowork), the [Claude API](https://platform.claude.com/docs/en/build-with-claude/skills-guide), and the [Agent SDK](https://platform.claude.com/docs/en/agent-sdk/overview). The methodology also works for any AI coding tool or manual project organization.

## Why This Matters

AI coding tools are only as good as the context they receive. When Claude Code opens your project, it needs to figure out where things are, what conventions to follow, and which files matter for the current task. If your folder structure is disorganized, Claude wastes tokens guessing, reads the wrong files, produces inconsistent output, or ignores your instructions entirely.

Most developers blame the model when output quality drops. But the real problem is usually structural:

- **Wrong format?** Missing naming conventions — Claude improvises a pattern.
- **Off-topic output?** No routing table — Claude loaded the wrong context file (or none at all).
- **Ignoring instructions?** Your CLAUDE.md is 200 lines and the real rules are buried at line 180.
- **Inconsistent across sessions?** No CONTEXT.md files — every session starts from scratch.

**Most AI output quality comes from what happens before the AI runs.** Industry data backs this up: 44–65% of developers blame missing context — not bad prompts — for poor AI-generated code (Qodo, 2025). Adding routing files like AGENTS.md cuts agent runtime by 29% (Sherwood, 2025). Anthropic's own guidance frames the core challenge as "curating what enters the model's attention budget," not crafting the perfect prompt (Schluntz et al., 2025).

We use a **60/30/10 heuristic** to prioritize fixes: **60%** traditional structure (naming, organization, file grouping) → **30%** routing (CLAUDE.md, CONTEXT.md, conventions) → **10%** the AI interaction itself. The exact ratio is a teaching shorthand, not an empirical measurement — but the direction is well-established. Fix the 90% you can actually control.

It also matters for humans. A well-structured project is easier to navigate, onboard into, and maintain — with or without AI.

## The Five-Layer Context Hierarchy

The project implements the ICM five-layer architecture (Van Clief & McDermott, 2026). The base audit scores Layers 0–1 and Tools (X/16). If pipeline stages are detected, an extended assessment covers Layers 2–4 (X/18).

```
Layer 0: CLAUDE.md (The Map)       → Routes to the right workspace
Layer 1: CONTEXT.md (The Rooms)    → Routes to the right files within a workspace
Layer 2: Stage Contracts           → Declares inputs, process, outputs per stage
Layer 3: References (The Factory)  → Stable material: voice, style, conventions
Layer 4: Working (The Product)     → Per-run artifacts: drafts, data, output
```

> **Terminology:** "Map," "Rooms," and "Tools" are metaphors from Clief Notes Module 3. Layer numbering (0–4) follows the ICM specification. The scoring rubric and grading scale are FSP-authored practical tools — not part of the ICM paper.

### Layer 0: The Map (CLAUDE.md)

A root config file under 50 lines that contains:
- Project identity (2–3 sentences)
- Routing table (Task / Go-to / Read)
- Naming conventions per file type
- Read-only folder markings

### Layer 1: The Rooms (CONTEXT.md)

One context file per workspace folder, under 80 lines, covering:
- What happens in this workspace
- What good output looks like
- What to avoid
- Key files listing

### Tools (Skills / Playbooks)

Reusable procedures that emerge from friction — not pre-built scaffolding. Tools are scored alongside the layers but are not a numbered ICM layer. Each should be:
- Born from repeated manual steps
- Scoped to specific workspaces
- Written in markdown (not code)
- Anti-fragile to model updates

### Layers 2–4: ICM Pipelines

For projects with sequential, human-reviewed workflows (numbered stage folders like `01_research/`, `02_script/`), the hierarchy extends:

- **Layer 2 — Stage Contracts:** Each stage's CONTEXT.md declares Inputs, Process, Outputs, and a Review Checkpoint
- **Layer 3 — References (Factory):** Stable material (`references/`, `_config/`) internalized as constraints
- **Layer 4 — Working (Product):** Per-run artifacts (`output/`) produced and transformed between stages

The model should *embody* factory material (write in this voice) but *transform* product material (convert this research into a script).

## Skills

This project provides five skills that cover the full lifecycle:

```
Audit ──→ Scaffold ──→ Execute ──→ Review ──→ Validate
  ↑                                              |
  └──────────── fix and iterate ─────────────────┘
```

### `/folder-audit` — Score any project's structure

Snapshots the file tree, scores Layers 0–1 + Tools (X/16), checks 8 anti-patterns, measures 5 structural metrics, and generates a graded report with the top 3 prioritized fixes. Optionally imprints structure rules into the audited project's CLAUDE.md so future sessions follow the conventions automatically.

If numbered stage folders are detected, runs an extended ICM assessment (X/18) covering stage contracts, reference/working separation, and pipeline architecture.

### `/pipeline-scaffold` — Create a staged workflow

Generates numbered stage folders with CONTEXT.md contracts (Inputs/Process/Outputs/Review Checkpoint), `references/` and `output/` directories per stage, a shared `_config/` folder, and a root CONTEXT.md with stage routing.

### `/run-stage` — Execute one stage at a time

Reads the stage's CONTEXT.md contract, loads only declared inputs (Layer 3 as constraints, Layer 4 as material), follows the Process instructions, writes declared outputs, and pauses for mandatory human review. Never auto-advances.

### `/stage-review` — Verify before advancing

Checks that all declared outputs exist, runs the Review Checkpoint criteria, assesses quality against the Process intent, and confirms the next stage's inputs are satisfied. Read-only — never modifies output files.

### `/validate-pipeline` — Check the contract chain

Walks the full pipeline to find broken handoffs (output/input mismatches), factory/product cross-contamination, missing contract sections, and structural anti-patterns. Offers to fix issues directly.

## Quick Start

### Claude Code (CLI)

Four ways to install, from lightest to most complete:

**Option A: One-command install (project-level)**

```bash
# From your project root
mkdir -p .claude/skills/folder-audit
curl -o .claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-structure-protocol/main/.claude/skills/folder-audit/SKILL.md
```

Then run `/folder-audit` in Claude Code. Scoped to this project only.

**Option B: Global install (available in every project)**

```bash
mkdir -p ~/.claude/skills/folder-audit
curl -o ~/.claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-structure-protocol/main/.claude/skills/folder-audit/SKILL.md
```

Now `/folder-audit` works in any project you open with Claude Code.

**Option C: Use as a plugin (shareable across teams)**

Claude Code supports [plugins](https://code.claude.com/docs/en/plugins) — reusable packages that can include skills:

```bash
claude plugin add https://github.com/mcmespinaa/folder-structure-protocol.git
```

Or reference it in your project's `.claude/plugins.json`:

```json
{
  "plugins": [
    "https://github.com/mcmespinaa/folder-structure-protocol.git"
  ]
}
```

Plugin skills use a namespaced command: `/folder-audit:folder-audit`.

**Option D: Clone the full repo**

```bash
git clone https://github.com/mcmespinaa/folder-structure-protocol.git
cd folder-structure-protocol
```

This gives you all five skills, the methodology playbook, example audit reports, and the CLAUDE.md routing setup.

### Claude.ai (Web / Cowork)

Custom skills work on Claude.ai on Pro, Max, Team, and Enterprise plans with code execution enabled.

1. **Download and zip** the skill:
   ```bash
   git clone https://github.com/mcmespinaa/folder-structure-protocol.git
   cd folder-structure-protocol/.claude/skills
   zip -r folder-audit.zip folder-audit/
   ```
   Or download just the `SKILL.md` file, place it in a `folder-audit/` directory, and zip that.

2. **Upload** in Claude.ai: go to **Settings > Features > Custom Skills** and upload `folder-audit.zip`

3. **Use it** in any conversation. Upload your project folder (or zip it and attach it), then ask:
   ```
   Audit the folder structure of this project
   ```

**Notes for Claude.ai:**
- Claude needs access to your project files. Attach your project as a zip or share the relevant files.
- The skill only reads files — no network access needed.
- Custom skills are per-user. Each team member uploads separately.
- Skills don't sync across surfaces (uploading to Claude.ai doesn't make it available in Claude Code or the API).

### Claude API

Skills work via the API with the code execution container. Three beta headers are required:

```
anthropic-beta: code-execution-2025-08-25,skills-2025-10-02,files-api-2025-04-14
```

1. Upload the skill using the `/v1/skills` endpoint
2. Reference it in your requests via the `container.skills` parameter

See the [API Skills Guide](https://platform.claude.com/docs/en/build-with-claude/skills-guide) for full setup.

### Claude Agent SDK

```bash
mkdir -p .claude/skills/folder-audit
curl -o .claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-structure-protocol/main/.claude/skills/folder-audit/SKILL.md
```

Include `"Skill"` in your `allowed_tools` configuration. The SDK auto-discovers skills from `.claude/skills/`. See the [Agent SDK Skills docs](https://platform.claude.com/docs/en/agent-sdk/skills) for details.

## Usage

### Run an audit

In Claude Code, use any of these:

```
/folder-audit
folder audit on this project
evaluate the folder structure of ~/Projects/my-app
score this project's organization
```

### What you get

A markdown report with scores, anti-patterns, metrics, and prioritized fixes:

```
# Folder Architecture Audit: my-project
Date: 2026-03-24

## Scores
| Layer              | Score | Max | Grade |
|--------------------|-------|-----|-------|
| Layer 0 (Map)      | 4     | 6   | B     |
| Layer 1 (Rooms)    | 5     | 6   | B     |
| Tools              | 3     | 4   | B     |
| **Total**          | **12**| **16** | **B** |

## Top 3 Fixes (Priority Order)
1. Clean the project root — group files into workspaces
2. Trim CLAUDE.md under 50 lines — move details to CONTEXT.md
3. Add "What to Avoid" sections to workspace CONTEXT files
```

### Grading scale

| Score | Grade | Meaning |
|-------|-------|---------|
| 15–16 | A | Production-ready. Agent works autonomously. |
| 10–14 | B | Functional. Occasional re-steering needed. |
| 6–9 | C | Fragile. Agent frequently guesses wrong. |
| 0–5 | F | Broken. Start with minimum viable structure. |

## Scoring Criteria

### Layer 0: The Map (6 points)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | Root config exists | CLAUDE.md at project root |
| 2 | Under 50 lines | Non-blank line count |
| 3 | Routing table | Task / Go-to / Read columns |
| 4 | Naming conventions | Explicit patterns per file type |
| 5 | Identity section | 2–3 sentences: what, who |
| 6 | Read-only folders marked | Assets, references labeled |

### Layer 1: The Rooms (6 points, averaged across workspaces)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | CONTEXT.md exists | One per workspace |
| 2 | Under 80 lines | Concise |
| 3 | Describes the work (80%+) | Not Claude's personality |
| 4 | "What to Avoid" section | Explicit anti-patterns |
| 5 | Fresh | Updated in active period |
| 6 | Key files listed | Points to important files |

### Tools (4 points)

| # | Criterion | What passes |
|---|-----------|------------|
| 1 | Born from friction | Not pre-built |
| 2 | Scoped to workspaces | Not loaded globally |
| 3 | Markdown instructions | Not coded orchestration |
| 4 | Anti-fragile | Model updates help, not break |

### 8 Base Anti-Patterns

| # | Anti-Pattern | Signal |
|---|-------------|--------|
| 1 | Oversized CLAUDE.md | > 50 lines |
| 2 | Missing routing table | No task-to-folder mapping |
| 3 | Too many workspaces | 5+ from day one |
| 4 | Personality over context | Context describes behavior, not work |
| 5 | Stale context | > 6 weeks without update |
| 6 | Flat dump | 10+ files at one level |
| 7 | Built before used | Elaborate system, no work product |
| 8 | Monolithic context loading | All instructions in one file instead of scoped per workspace |

### 6 ICM Anti-Patterns (pipeline projects only)

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
| M1 Depth Ratio | Max depth / top-level folders | 2–4 |
| M2 File Density | Avg files per folder | 3–10 |
| M3 Naming Consistency | % following pattern per folder | > 80% |
| M4 Convention Coverage | % file types with declared convention | 100% |
| M5 Context-to-Content | Context files / content files | 5–15% |

## Run Once, Remember Forever

The audit can **imprint** structural rules directly into the audited project's CLAUDE.md:

1. Run `/folder-audit` on a project
2. The skill generates the report and scores
3. It offers to write a `## Structure Rules` section into that project's CLAUDE.md
4. Every future Claude session reads those rules and follows them automatically

Imprinted rules are generated from actual findings — not a generic template. They include naming conventions, folder boundaries, file placement mappings, and folder creation guidelines. Kept under 30 lines and dated so you can see when they go stale. Projects that score A skip imprinting — they're already self-documenting.

If repeated friction is found, the audit recommends creating a dedicated skill via the [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) workflow. The audit identifies *what* to automate; skill-creator handles *how*.

## Project Structure

```
folder-structure-protocol/
├── CLAUDE.md                          # Layer 0 — routes to the right workspace
├── README.md                          # This file
├── .claude/
│   └── skills/
│       ├── folder-audit/
│       │   └── SKILL.md               # Audit skill (copy this to use)
│       ├── pipeline-scaffold/
│       │   └── SKILL.md               # Pipeline creation skill
│       ├── run-stage/
│       │   └── SKILL.md               # Stage execution skill
│       ├── stage-review/
│       │   └── SKILL.md               # Stage review skill
│       └── validate-pipeline/
│           └── SKILL.md               # Pipeline validation skill
└── File-tree audit/                   # Methodology workspace
    ├── CONTEXT.md                     # Layer 1 — workspace context
    ├── Playbook - Folder Architecture Evaluation.md
    └── audits/                        # Example audit reports
        ├── audit-folder-structure-2026-03-24.md
        ├── audit-ai-socialagent-2026-03-24.md
        ├── audit-claude-notebooklm-2026-03-24.md
        └── audit-organic-forward-2026-03-24.md
```

### What each file does

| File | Purpose | You need it? |
|------|---------|-------------|
| `.claude/skills/folder-audit/SKILL.md` | Audit skill — 11-step procedure with scoring rubrics, 14 anti-patterns (8 base + 6 ICM), 5 metrics, report template, structure imprinting, and ICM pipeline assessment | **Yes** — the core skill |
| `.claude/skills/pipeline-scaffold/SKILL.md` | Generates numbered stage folders with contracts, reference/output directories | For ICM pipelines |
| `.claude/skills/run-stage/SKILL.md` | Executes one stage — reads contract, loads scoped inputs, writes outputs, pauses for review | For ICM pipelines |
| `.claude/skills/stage-review/SKILL.md` | Verifies outputs, runs checkpoints, checks downstream readiness | For ICM pipelines |
| `.claude/skills/validate-pipeline/SKILL.md` | Walks contract chain, flags broken handoffs and anti-patterns | For ICM pipelines |
| `CLAUDE.md` | Root routing config for this project | Only if cloning the full repo |
| `File-tree audit/CONTEXT.md` | Workspace context for the audit methodology | Only if cloning the full repo |
| `File-tree audit/Playbook - *.md` | Deep methodology reference — scoring rationale, pipeline execution, design principles | Optional — for the "why" |
| `File-tree audit/audits/*.md` | Example audit reports from real projects | Optional — to see output |

## Requirements

- Any Claude surface that supports custom skills:
  - **Claude Code** — CLI installed and authenticated
  - **Claude.ai** — Pro, Max, Team, or Enterprise plan with code execution enabled
  - **Claude API** — with code execution + skills beta headers
  - **Agent SDK** — with `"Skill"` in `allowed_tools`
- A project folder to audit

No dependencies, no build step, no configuration. Each skill is a single markdown file.

## References

Van Clief, J., & McDermott, D. (2026). Interpretable context methodology: Folder structure as agentic architecture. *arXiv*. https://doi.org/10.48550/arXiv.2603.16021

Schluntz, B., Sanchez, B., Morar, D., & Schluntz, E. (2025, November 18). Effective context engineering for AI agents. Anthropic. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

Sherwood, P. (2025, June 24). Context engineering for coding agents. *martinfowler.com*. https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html

Qodo. (2025). *State of AI code quality 2025*. https://www.qodo.ai/reports/state-of-ai-code-quality/

Nussbaum, A. (2024). *Context rot*. Chroma Research. https://research.trychroma.com/context-rot

### Additional sources

- Clief Notes Module 3: Folder Architecture (Quantum Quill Lyceum) — origin of "Map/Rooms/Tools" metaphors
- Anthropic. (2025). *Skill authoring best practices*. https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- [ICM Principles](https://github.com/RinDig/Interpreted-Context-Methdology) — full specification and examples

## License

MIT
