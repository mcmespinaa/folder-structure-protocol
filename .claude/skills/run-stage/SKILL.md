---
name: run-stage
description: "Executes a single ICM pipeline stage by reading its CONTEXT.md contract, loading scoped inputs (Layer 3 reference vs Layer 4 working), following the Process section, writing declared outputs, and pausing for human review. Use when: running a pipeline stage, executing a stage, processing the next stage."
---

# Run Stage Skill

Executes a single pipeline stage by following its CONTEXT.md contract. Loads only declared inputs, produces only declared outputs, and pauses for human review before advancing.

## Trigger

Use this skill when:
- The user asks to run, execute, or process a specific stage (e.g., "run stage 2", "execute 01_research")
- The user says "next stage", "run the next one", "process this stage"
- The user wants to advance through a pipeline one stage at a time

## Procedure

### Step 1: Identify Target Stage

Determine which stage to run:
- If the user specifies a stage number or name, use that
- If the user says "next stage", find the last stage with output files and advance to the next one
- If ambiguous, list all available stages and ask

```bash
# Find numbered stage folders
ls -d [0-9][0-9]_*/ 2>/dev/null || ls -d stages/[0-9][0-9]_*/ 2>/dev/null
```

If no numbered stage folders exist, halt: "No pipeline stages found. Use `/pipeline-scaffold` to create one, or ensure stage folders follow the `{NN}_{name}/` naming pattern."

### Step 2: Read the Stage Contract

Open `{stage-folder}/CONTEXT.md`. Parse three required sections:
- **Inputs table** — files to load, their source paths, and Layer designation (3 or 4)
- **Process section** — the numbered instructions to follow
- **Outputs section** — what to produce and where to write it

If any section is missing, halt and report the gap:
- Missing Inputs: "Stage {NN} has no Inputs table. Add one to its CONTEXT.md before running."
- Missing Process: "Stage {NN} has no Process section. The contract must specify what to do."
- Missing Outputs: "Stage {NN} has no Outputs section. Declare what this stage produces."

### Step 3: Load Inputs (Scoped Context)

For each file in the Inputs table:

**Layer 3 (reference):** Read from the declared source (`references/`, `_config/`, `shared/`). These are stable constraints — style guides, voice docs, design systems, conventions. **Internalize these as rules.** Do not quote them in the output; write as if you've absorbed them.

**Layer 4 (working):** Read from the declared source (usually a previous stage's `output/`). These are the material to transform — research output, drafts, data. **Process these as input.** Extract, analyze, and transform them according to the Process section.

**If an input file is missing:**
- Check if the source path exists
- If it's from a previous stage's `output/`, suggest running that stage first
- If it's a reference file, ask the user to provide it
- Do NOT proceed with missing inputs — the contract exists to prevent guessing

**Token budget awareness:** Only load the files listed in the Inputs table. Do not read other files in the stage folder, other stages' folders, or the project root beyond what's declared. This scoping is what prevents context window bloat.

### Step 4: Execute the Process

Follow the Process section instructions sequentially:
- Each instruction may involve reading, analyzing, generating, or transforming content
- Stay within the scope of what the contract declares — do not add steps
- If an instruction is ambiguous, ask the user rather than guessing
- If an instruction references a technique or format not covered by the inputs, flag it

**Layer 3 vs Layer 4 attention:**
- Layer 3 material shapes HOW you work (write in this voice, follow this structure, use these terms)
- Layer 4 material is WHAT you work with (transform this research, convert this script, analyze this data)

### Step 5: Write Outputs

Write results to the locations declared in the Outputs table:
- Use the specified format (markdown, JSON, etc.)
- Use the exact file names declared
- Write to the stage's `output/` directory (or other declared destination)

**Guardrails:**
- Never write to `references/` or `_config/` — those are read-only
- Never write to another stage's directory
- Never create files not declared in the Outputs table
- If the output is larger than expected, write it anyway — the review step handles quality

### Step 6: Pause for Human Review

After writing all outputs, display:

```
## Stage {NN} Complete

**Outputs written:**
- {file1} → {path} ({brief description})
- {file2} → {path} ({brief description})

**Review Checkpoint:**
{Copy the Review Checkpoint items from the stage's CONTEXT.md}

**Next stage:** {NN+1}_{name} — run `/run-stage {NN+1}` when ready, or `/stage-review {NN}` to verify before advancing.
```

**Do NOT auto-advance to the next stage.** The human decides when to proceed. This is a core ICM principle — every stage boundary is a review gate.

## Guardrails

- Never read files not listed in the Inputs table
- Never write to locations not declared in the Outputs table
- Never modify the stage's CONTEXT.md during execution
- Never skip the human review pause
- If Process instructions conflict with Layer 3 constraints, follow Layer 3 (the factory beats the product)
- If a previous stage's output seems wrong, flag it — do not silently fix it

## What NOT to Do

- Do not auto-advance to the next stage
- Do not read the entire workspace or project root
- Do not load other stages' CONTEXT.md files (each stage is scoped)
- Do not create helper files, logs, or metadata not in the Outputs table
- Do not modify reference material
- Do not re-run previous stages without the user asking
