# File-Tree Audit — Context

This workspace holds the methodology and results for evaluating project folder architecture. Audits score projects against the three-layer routing system (Map, Rooms, Tools) and produce graded reports with prioritized fixes.

## What Happens Here

1. Pick a target project to audit
2. Run the `folder-audit` skill (`.claude/skills/folder-audit/SKILL.md`)
3. The skill generates a file tree snapshot, scores 3 layers, checks 7 anti-patterns, measures 5 structural metrics
4. Output: a graded audit report saved to `audits/`
5. **Imprint:** The skill offers to write structure rules into the audited project's CLAUDE.md — so future Claude sessions follow the structure automatically
6. **Escalate:** If repeated friction is found, the skill recommends creating a new skill via the skill-creator workflow
7. **MWP Pipeline Audit:** If numbered stage folders are detected, runs an additional assessment of stage contracts, reference/working separation, and pipeline architecture (scored separately, X/18)

## What Good Looks Like

- A complete report with all sections filled (scores, anti-patterns, metrics, top 3 fixes)
- Fixes ordered by the 60/30/10 rule (structure first, routing second, AI tuning last)
- Specific, actionable recommendations — not generic advice
- Honest scoring — don't inflate grades to be polite

## What to Avoid

- Skipping sections of the report template
- Scoring Layer 3 as a penalty on new projects (use N/A)
- Recommending more tooling when the structure itself is broken
- Auditing without first generating the file tree snapshot

## Key Files

- `Playbook - Folder Architecture Evaluation.md` — Full methodology, scoring rubrics, report template
- `audits/` — Completed audit reports (`audit-{project-name}-YYYY-MM-DD.md`)
