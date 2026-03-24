---
name: pipeline-scaffold
description: "Creates a new ICM pipeline from a domain description. Generates numbered stage folders with CONTEXT.md contracts (Inputs/Process/Outputs), reference and output directories, and a root CONTEXT.md. Use when: setting up a new staged workflow, creating a production pipeline, scaffolding an ICM workspace."
---

# Pipeline Scaffold Skill

Creates a new ICM (Interpretable Context Methodology) pipeline — a sequential, human-reviewed AI workflow built from numbered stage folders and file-based handoffs.

## Trigger

Use this skill when:
- The user asks to create, scaffold, or set up a new pipeline or staged workflow
- The user says "new pipeline", "scaffold stages", "create workflow", "set up ICM", "build a pipeline for X"
- The user wants to convert an existing process into a staged folder structure

## Procedure

### Step 1: Gather Domain Description

Ask the user for:
- **Domain/purpose** — What does this pipeline produce? (e.g., "podcast production", "course deck", "content research")
- **Stages** — What are the major steps? Accept natural language: "research, then script, then production" is enough.

If the user gives a vague request like "set up a pipeline for video production", infer reasonable stages and confirm before proceeding. Default to 3-5 stages — fewer is better.

Do NOT proceed without at least a domain name and stage list.

### Step 2: Determine Pipeline Root

Confirm the target directory. Options:
- Current project root (default)
- A subdirectory (e.g., `workspaces/podcast-pipeline/`)
- A new directory (create it)

If the target already contains numbered stage folders, warn the user and ask whether to overwrite or create alongside.

### Step 3: Generate Stage Folder Structure

For each stage, create:

```
{NN}_{stage-name}/
  CONTEXT.md        # Stage contract (Step 4)
  references/       # Layer 3: stable reference material (empty)
  output/           # Layer 4: working artifacts (empty)
```

Stage numbering: `01_`, `02_`, etc. Use snake_case for stage names. Zero-pad to two digits.

Also create:
- `_config/` at the pipeline root — for workspace-wide settings (voice, style, brand). Include an empty placeholder `README.md` explaining its purpose.
- Root `CONTEXT.md` — pipeline-level routing (Step 5)

### Step 4: Write Stage Contracts

Each stage's `CONTEXT.md` must follow this format exactly:

```markdown
# Stage {NN}: {Stage Name}

{One sentence: what this stage does and why it exists.}

## Inputs

| File | Source | Layer | Purpose |
|------|--------|-------|---------|
| {file} | {path or previous stage output/} | 3=reference / 4=working | {why this file is needed} |

## Process

1. {First instruction}
2. {Second instruction}
3. {Third instruction}

## Outputs

| File | Destination | Format |
|------|-------------|--------|
| {file} | output/ | {markdown, JSON, etc.} |

## Review Checkpoint

Before advancing to the next stage, verify:
- [ ] {First check}
- [ ] {Second check}
```

**Rules for writing contracts:**
- Inputs table must label every file as Layer 3 (reference — internalize as constraints) or Layer 4 (working — process as input)
- Process section should have 3-7 instructions. If it needs more, the stage is too broad — split it.
- Outputs section names exact files, not "whatever was produced"
- Review Checkpoint gives the human concrete things to verify before advancing
- First stage's Inputs will mostly be Layer 3 (references, config) plus the user's raw input
- Subsequent stages' Inputs should reference the previous stage's `output/` directory

### Step 5: Generate Root CONTEXT.md

The pipeline-level context file routes across stages:

```markdown
# {Pipeline Name}

{2-3 sentences: what this pipeline produces, who it's for, what makes it different from doing this manually.}

## Stage Overview

| Stage | Name | Reads From | Writes To |
|-------|------|-----------|-----------|
| 01 | {name} | _config/, user input | 01_{name}/output/ |
| 02 | {name} | 01_{name}/output/ | 02_{name}/output/ |
| ... | ... | ... | ... |

## How to Run

1. Place your input material in `01_{first-stage}/references/` or as specified in its contract
2. Run each stage sequentially using `/run-stage`
3. Review output at each stage boundary before advancing
4. Use `/stage-review` to verify readiness before moving on
5. Use `/validate-pipeline` to check the full contract chain

## Conventions

- Stage folders use `{NN}_{name}/` format
- Each stage has `references/` (stable) and `output/` (per-run)
- Files in `_config/` apply to all stages
- Output of stage N is input to stage N+1 — never skip stages
```

### Step 6: Verify Structure

After generating all files, run a file tree of the pipeline root and display it to the user:

```bash
find {pipeline-root} -not -name '.DS_Store' | sort
```

Confirm the structure looks correct before finishing.

### Step 7: Suggest Next Steps

Tell the user:
1. Add reference material to `_config/` and stage-specific `references/` folders
2. Review and customize each stage's CONTEXT.md contract
3. Run `/validate-pipeline` to verify the contract chain is consistent
4. Start execution with `/run-stage 01`

## What NOT to Generate

- Do not create output files (those come from execution)
- Do not pre-populate references with placeholder content
- Do not create more than 8 stages without explicit user confirmation
- Do not create skills or playbooks — the pipeline is the structure, skills come from friction
- Do not add a CLAUDE.md — that's the project's responsibility, not the pipeline's

## Stage Contract Template

For reference, the canonical stage contract format:

```markdown
# Stage {NN}: {Stage Name}

{Purpose sentence.}

## Inputs

| File | Source | Layer | Purpose |
|------|--------|-------|---------|
| voice-guide.md | _config/ | 3 | Writing style and tone constraints |
| research-output.md | 01_research/output/ | 4 | Raw research to transform into script |

## Process

1. Read all Layer 3 inputs — internalize as constraints (do not copy, embody)
2. Read all Layer 4 inputs — these are the material to process
3. {Domain-specific instruction}
4. Write output to the Outputs location

## Outputs

| File | Destination | Format |
|------|-------------|--------|
| script-draft.md | output/ | Markdown with scene headers |

## Review Checkpoint

Before advancing to the next stage, verify:
- [ ] Output follows voice guide constraints
- [ ] All source material from research was addressed
- [ ] No hallucinated facts — everything traces to an input
```
