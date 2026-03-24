---
name: validate-pipeline
description: "Validates an ICM pipeline's contract chain — checks that each stage's outputs match the next stage's expected inputs, verifies factory/product separation, and flags structural anti-patterns. Use when: checking pipeline integrity, verifying handoffs, debugging broken stage connections."
---

# Validate Pipeline Skill

Walks the full contract chain of an ICM pipeline and validates that stages connect correctly. Finds broken handoffs, mixed reference/working files, and structural anti-patterns.

## Trigger

Use this skill when:
- The user asks to validate, verify, or check a pipeline's structure
- The user says "validate pipeline", "check contracts", "verify handoffs", "are my stages connected?"
- The user just ran `/pipeline-scaffold` and wants to verify the result
- A `/run-stage` execution failed because of missing inputs

## Procedure

### Step 1: Discover Stages

Find all numbered stage folders:

```bash
ls -d [0-9][0-9]_*/ 2>/dev/null || ls -d stages/[0-9][0-9]_*/ 2>/dev/null
```

Sort by numeric prefix. If no stages found, halt: "No pipeline stages found. This skill validates ICM pipelines with numbered stage folders (`01_name/`, `02_name/`, etc.)."

Record:
- Total stage count
- Stage names and paths
- Whether `_config/` or `shared/` directories exist

### Step 2: Read All Contracts

For each stage, read its `CONTEXT.md`. Parse and record:
- **Inputs table** — file names, source paths, Layer designations
- **Process section** — present/absent, instruction count
- **Outputs table** — file names, destination paths, formats
- **Review Checkpoint** — present/absent

Flag any stage missing a CONTEXT.md entirely.

### Step 3: Validate Contract Chain

For each consecutive pair of stages (N, N+1):

**3a: Output-to-Input matching**
- For every file in stage N+1's Inputs that sources from stage N's `output/`: verify that stage N's Outputs table declares that file.
- Flag mismatches as **broken handoffs**: "Stage {N+1} expects `{file}` from stage {N}, but stage {N} doesn't declare it as an output."

**3b: Name consistency**
- Check that file names match exactly between the upstream Output and downstream Input declarations.
- Flag near-misses: "Stage {N} outputs `research_notes.md` but stage {N+1} expects `research-notes.md`."

**3c: Format compatibility**
- If stage N declares an output format (e.g., "JSON") and stage N+1's Process expects a different format (e.g., "markdown"), flag it.

Record results per handoff: **Pass** (all inputs satisfied), **Warning** (near-misses), or **Fail** (missing outputs).

### Step 4: Check Factory/Product Separation

**4a: Directory structure**
- Does each stage have a `references/` directory (or equivalent for Layer 3)?
- Does each stage have an `output/` directory (for Layer 4)?
- Does `_config/` exist at the pipeline root for shared reference material?

**4b: Layer labeling**
- Do Inputs tables distinguish Layer 3 (reference) from Layer 4 (working)?
- Flag any Inputs table that has no Layer column.

**4c: No cross-contamination**
- Are reference files (in `references/`, `_config/`) free of per-run output?
- Are output files (in `output/`) free of stable configuration?
- Does any stage write to another stage's `references/` folder? (This should never happen.)

### Step 5: Check Pipeline Anti-Patterns

| # | Anti-Pattern | How to detect |
|---|-------------|---------------|
| 1 | Monolithic stage | One stage has 8+ Process instructions or produces 5+ output files |
| 2 | Missing output folders | Stage folder has no `output/` directory |
| 3 | Mixed reference and working | `references/` contains dated/versioned files, or `output/` contains style guides |
| 4 | No Inputs table | Stage CONTEXT.md has Process and Outputs but no Inputs table |
| 5 | Circular dependencies | Stage N reads from stage N+2 or later (forward reference) |
| 6 | Over-staged | 3+ consecutive stages with only 1-2 Process instructions each |

### Step 6: Generate Validation Report

```markdown
# Pipeline Validation: {Pipeline Name}
**Date:** {today}
**Path:** {pipeline root}
**Stages:** {count}

## Chain Validation

| Handoff | From | To | Status |
|---------|------|----|--------|
| 1 | 01_{name} | 02_{name} | [Pass/Warning/Fail] |
| 2 | 02_{name} | 03_{name} | [Pass/Warning/Fail] |
| ... | ... | ... | ... |

### Broken Handoffs
{List each with: what's missing, which file, suggested fix}

### Near-Misses
{List each with: the name mismatch and suggested correction}

## Factory/Product Separation

| Check | Status |
|-------|--------|
| Stage `references/` directories | [Present/Missing] |
| Stage `output/` directories | [Present/Missing] |
| Root `_config/` directory | [Present/Missing] |
| Inputs tables have Layer column | [Yes/Partial/No] |
| No cross-contamination | [Clean/Issues found] |

## Anti-Patterns Found

{List each found with a one-line fix, or "None found" if clean}

## Contract Completeness

| Stage | CONTEXT.md | Inputs | Process | Outputs | Checkpoint |
|-------|-----------|--------|---------|---------|------------|
| 01_{name} | [Yes/No] | [Yes/No] | [Yes/No] | [Yes/No] | [Yes/No] |
| ... | ... | ... | ... | ... | ... |

## Suggested Fixes

1. {Most critical fix — what to change and in which file}
2. {Second fix}
3. {Third fix}
```

### Step 7: Offer to Fix

For each broken handoff or missing contract section, offer to apply the fix directly:
- Missing Output declaration → add the file to stage N's Outputs table
- Name mismatch → rename in the downstream Inputs table to match upstream
- Missing Layer column → add Layer designations to the Inputs table
- Missing CONTEXT.md section → generate a template section

Always ask before modifying any CONTEXT.md file.
