---
name: stage-review
description: "Reviews a completed pipeline stage before advancing to the next one. Verifies all declared outputs exist, runs the stage's Review Checkpoint criteria, checks quality against the Process intent, and confirms downstream readiness. Use when: reviewing stage output, checking if ready to advance, verifying stage completion."
---

# Stage Review Skill

Reviews a completed pipeline stage to determine whether it's safe to advance. Checks output existence, runs checkpoint criteria, and verifies the next stage's inputs are satisfied.

## Trigger

Use this skill when:
- The user asks to review a stage before moving on ("review stage 2", "can I advance?", "check stage output")
- The user says "stage review", "stage checkpoint", "is this stage done?"
- The user wants a quality gate between `/run-stage` and the next stage
- The user is unsure whether a stage's output is good enough

## Procedure

### Step 1: Identify Target Stage

Determine which stage to review:
- If the user specifies a stage number or name, use that
- If the user says "review this stage" or "can I advance?", find the most recent stage with output files
- If ambiguous, list stages that have output and ask

```bash
# Find stages with output
for dir in [0-9][0-9]_*/; do
  if [ -d "$dir/output" ] && [ "$(ls -A "$dir/output" 2>/dev/null)" ]; then
    echo "$dir — has output"
  else
    echo "$dir — no output yet"
  fi
done
```

### Step 2: Read the Stage Contract

Load `{stage-folder}/CONTEXT.md`. Extract:
- **Outputs table** — what files should exist and where
- **Review Checkpoint** — the criteria the human (and this skill) should verify
- **Process section** — what the stage was supposed to do (for quality assessment)
- **Inputs table** — what the stage had to work with (for traceability)

If the stage has no CONTEXT.md, report: "Stage {NN} has no contract. Cannot verify outputs against intent."

### Step 3: Verify Output Existence

For each file declared in the Outputs table:
- Check the declared destination path
- Record: **exists** or **missing**
- If a file exists, note its size (empty files are a warning)

```markdown
| Declared Output | Path | Status |
|----------------|------|--------|
| {file1} | {stage}/output/{file1} | [Exists/Missing/Empty] |
| {file2} | {stage}/output/{file2} | [Exists/Missing/Empty] |
```

If any declared output is missing, mark the stage as **Not Ready** immediately. The remaining checks still run to give a complete picture.

### Step 4: Run Review Checkpoint

Read the Review Checkpoint section from the stage's CONTEXT.md. For each checkpoint criterion:

1. Read the relevant output file(s)
2. Assess whether the criterion is met
3. Score: **Pass**, **Partial**, or **Fail**
4. Provide brief evidence (one sentence)

```markdown
| Checkpoint | Status | Evidence |
|-----------|--------|----------|
| {criterion 1} | [Pass/Partial/Fail] | {one-line evidence} |
| {criterion 2} | [Pass/Partial/Fail] | {one-line evidence} |
```

If the stage's CONTEXT.md has no Review Checkpoint section, run a basic quality check instead:
- Does the output appear complete (not truncated)?
- Does the output format match the declared format in the Outputs table?
- Does the output relate to the Process section's intent?

### Step 5: Quality Assessment

Compare the stage's outputs against its Process section:
- Does the output reflect what the Process instructions asked for?
- Are there gaps between what was requested and what was produced?
- Are Layer 3 constraints (from the Inputs table's reference files) reflected in the output?

Score: **Good** (output fully matches intent), **Acceptable** (minor gaps), or **Needs Revision** (significant gaps or missing elements).

If quality is poor, identify specific issues: "The Process section asks for scene-by-scene breakdown, but the output is a single continuous narrative."

### Step 6: Check Downstream Readiness

Identify the next stage (N+1). If it exists:
1. Read its CONTEXT.md Inputs table
2. For each input that sources from the current stage's `output/`:
   - Verify the file exists
   - Verify the file name matches exactly
   - Verify the format is compatible

```markdown
| Next Stage Input | Expected From | Status |
|-----------------|---------------|--------|
| {file} | {current stage}/output/ | [Ready/Missing/Name mismatch] |
```

If no next stage exists (this is the final stage), note: "This is the final stage — no downstream dependencies."

### Step 7: Generate Review Report

```markdown
## Stage Review: {NN}_{name}

**Status:** [Ready / Needs Revision / Not Ready]

### Output Completeness
{Table from Step 3}

### Checkpoint Results
{Table from Step 4}

### Quality Assessment
**Score:** [Good / Acceptable / Needs Revision]
{One paragraph explaining the assessment}

### Downstream Readiness
{Table from Step 6, or "Final stage" note}

### Recommendation
{One of:}
- **Advance:** Stage is complete. Run `/run-stage {NN+1}` to continue.
- **Revise:** Address these items before advancing: {list}
- **Re-run:** Stage output is incomplete or missing. Run `/run-stage {NN}` again.
```

### Step 8: Offer Actions

Based on the recommendation:
- **If Ready:** "Run `/run-stage {NN+1}` to proceed to {next stage name}."
- **If Needs Revision:** Offer to re-run the current stage with specific guidance on what to fix, or let the user manually edit the output files.
- **If Not Ready:** Identify whether the problem is missing output (re-run needed) or missing inputs (previous stage issue).

## What NOT to Do

- Do not modify output files during review — this skill reads, it does not write
- Do not auto-advance to the next stage — the user decides
- Do not re-run the stage automatically — flag the issue and let the user choose
- Do not score outputs on subjective quality beyond what the checkpoint criteria specify
- Do not read files outside the current stage and the next stage's contract
