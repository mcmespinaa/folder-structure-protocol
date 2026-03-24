# Folder Audit

A Claude skill that evaluates any project's folder architecture against a three-layer routing system. It scores your project structure, flags anti-patterns, and outputs a graded report with prioritized fixes.

Works with [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI), [Claude.ai](https://claude.ai) (Cowork), the [Claude API](https://platform.claude.com/docs/en/build-with-claude/skills-guide), and the [Agent SDK](https://platform.claude.com/docs/en/agent-sdk/overview). The methodology also works for any AI coding tool or manual project organization.

## Why This Matters

AI coding tools are only as good as the context they receive. When Claude Code opens your project, it needs to figure out where things are, what conventions to follow, and which files matter for the current task. If your folder structure is disorganized, Claude wastes tokens guessing, reads the wrong files, produces inconsistent output, or ignores your instructions entirely.

Most developers blame the model when output quality drops. But the real problem is usually structural:

- **Wrong format?** Missing naming conventions -- Claude improvises a pattern.
- **Off-topic output?** No routing table -- Claude loaded the wrong context file (or none at all).
- **Ignoring instructions?** Your CLAUDE.md is 200 lines and the real rules are buried at line 180.
- **Inconsistent across sessions?** No CONTEXT.md files -- every session starts from scratch.

**60% of AI output quality comes from traditional folder structure** (naming, organization, file grouping). Another 30% comes from routing (CLAUDE.md, CONTEXT.md, conventions). Only 10% comes from the AI interaction itself. This skill audits the 90% you can actually control.

It also matters for humans. A well-structured project is easier to navigate, onboard into, and maintain -- with or without AI. The three-layer system this audit checks isn't just for Claude; it's good project hygiene that happens to make AI tools dramatically more effective.

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
6. **Imprints structure rules** into the audited project's CLAUDE.md -- so every future Claude session follows the folder conventions automatically
7. **Escalates to skill creation** when repeated friction is found -- recommends building a skill via the [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) workflow
8. **ICM pipeline audit** (conditional) -- if numbered stage folders are detected, runs an extended assessment of stage contracts, reference/working separation, and pipeline architecture (scored separately, X/18)

## Quick Start

### Claude Code (CLI)

There are four ways to install, from lightest to most complete:

**Option A: One-command install (project-level)**

```bash
# From your project root
mkdir -p .claude/skills/folder-audit
curl -o .claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-audit/main/.claude/skills/folder-audit/SKILL.md
```

Then run `/folder-audit` in Claude Code. The skill is scoped to this project only.

**Option B: Global install (available in every project)**

```bash
mkdir -p ~/.claude/skills/folder-audit
curl -o ~/.claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-audit/main/.claude/skills/folder-audit/SKILL.md
```

Now `/folder-audit` works in any project you open with Claude Code.

**Option C: Use as a plugin (shareable across teams)**

Claude Code supports [plugins](https://code.claude.com/docs/en/plugins) -- reusable packages that can include skills. To use this repo as a plugin:

```bash
# Add as a plugin from the GitHub URL
claude plugin add https://github.com/mcmespinaa/folder-audit.git
```

Or reference it in your project's `.claude/plugins.json`:

```json
{
  "plugins": [
    "https://github.com/mcmespinaa/folder-audit.git"
  ]
}
```

Plugin skills use a namespaced command: `/folder-audit:folder-audit`.

**Option D: Clone the full repo**

```bash
git clone https://github.com/mcmespinaa/folder-audit.git
cd folder-audit
```

This gives you the skill, the methodology playbook, example audit reports, and the CLAUDE.md routing setup. Best if you want to customize the methodology or contribute.

### Claude.ai (Web / Cowork)

Custom skills work on Claude.ai on Pro, Max, Team, and Enterprise plans with code execution enabled.

1. **Download and zip** the skill:
   ```bash
   git clone https://github.com/mcmespinaa/folder-audit.git
   cd folder-audit/.claude/skills
   zip -r folder-audit.zip folder-audit/
   ```
   Or download just the `SKILL.md` file, place it in a `folder-audit/` directory, and zip that.

2. **Upload** in Claude.ai: go to **Settings > Features > Custom Skills** and upload `folder-audit.zip`

3. **Use it** in any conversation. Upload your project folder (or zip it and attach it) so Claude can read the file tree, then ask:
   ```
   Audit the folder structure of this project
   ```

**Notes for Claude.ai:**
- Claude needs access to your project files to audit them. Attach your project as a zip or share the relevant files in the conversation.
- The skill only reads files -- no network access needed.
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
# Place the skill in your project
mkdir -p .claude/skills/folder-audit
curl -o .claude/skills/folder-audit/SKILL.md \
  https://raw.githubusercontent.com/mcmespinaa/folder-audit/main/.claude/skills/folder-audit/SKILL.md
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

## Run Once, Remember Forever

The audit doesn't just produce a report -- it can **imprint** structural rules directly into the audited project's CLAUDE.md. This means:

1. You run `/folder-audit` once on a project
2. The skill generates the report and scores
3. It offers to write a `## Structure Rules` section into that project's CLAUDE.md
4. Every future Claude session in that project reads those rules and follows them automatically

The imprinted rules are generated from your actual audit findings -- not a generic template. They include naming conventions, folder boundaries, file placement mappings, and folder creation guidelines. They're kept under 30 lines and dated so you can see when they go stale.

Projects that score A (15-16) skip imprinting -- they're already self-documenting.

### When friction repeats

If the audit finds the same manual process showing up multiple times (e.g., the same file type created by hand repeatedly, or a multi-step procedure described in multiple CONTEXT.md files), it recommends creating a dedicated skill using the [skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) workflow. The audit identifies *what* to automate; skill-creator handles *how* to build it.

The audit will never auto-create skills. Layer 3 should be a conscious decision, not an audit side-effect -- otherwise you'd trigger anti-pattern #7 (built before used).

## ICM Pipeline Projects

If your project uses numbered stage folders (`01_research/`, `02_script/`, `03_production/`) or follows the [Interpretable Context Methodology](https://github.com/RinDig/Model-Workspace-Protocol-MWP-), the audit automatically detects this and runs an extended assessment.

ICM extends the three-layer system into a five-layer context hierarchy that handles sequential, human-reviewed AI workflows. The base audit covers Layers 0-1 well but can't evaluate the stage-level architecture that makes ICM work. The extended assessment fills that gap:

| Component | What it checks | Max score |
|-----------|---------------|-----------|
| Stage Contracts | Each stage has a CONTEXT.md with Inputs/Process/Outputs, single responsibility, scoped context | 6 |
| Reference/Working Separation | Stable reference material (`references/`, `_config/`) is separated from per-run working artifacts (`output/`) | 6 |
| Pipeline Architecture | Numbered stages, file-based handoffs, review gates, incremental re-run support, self-documenting flow | 6 |
| **ICM Total** | | **18** |

The ICM score is reported **separately** from the base score. A project can score A on structure (16/16) and F on pipeline architecture (3/18), or vice versa. Both matter for their respective contexts.

### ICM anti-patterns

The extended audit checks for six pipeline-specific anti-patterns:
- Monolithic stage (one stage does everything)
- Missing output folders (stages write to random locations)
- Mixed reference and working files in the same folder
- No Inputs table in stage contracts
- Circular dependencies between stages
- Over-staged (trivial steps split unnecessarily)

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
| `.claude/skills/folder-audit/SKILL.md` | The executable audit skill -- 11-step procedure with scoring rubrics, anti-pattern checklist, metrics, report template, structure imprinting, skill escalation, and ICM pipeline assessment | **Yes** -- this is the skill |
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

- Any Claude surface that supports custom skills:
  - **Claude Code** -- CLI installed and authenticated
  - **Claude.ai** -- Pro, Max, Team, or Enterprise plan with code execution enabled
  - **Claude API** -- with code execution + skills beta headers
  - **Agent SDK** -- with `"Skill"` in `allowed_tools`
- A project folder to audit

No dependencies, no build step, no configuration. The skill is a single markdown file.

## Sources

- Clief Notes Module 3: Folder Architecture (Quantum Quill Lyceum)
- File-Tree-as-Architecture pattern
- Van Clief & McDermott, "Interpretable Context Methodology: Folder Structure as Agent Architecture" (2026) -- [ICM/MWP repo](https://github.com/RinDig/Model-Workspace-Protocol-MWP-)
- Anthropic [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

## License

MIT
