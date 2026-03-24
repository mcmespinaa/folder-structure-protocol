# Folder Structure

Tools, playbooks, and audits for evaluating project folder architecture against the ICM five-layer context hierarchy.

## Workspaces

| Folder | Purpose | Read/Write |
|--------|---------|------------|
| `File-tree audit/` | Obsidian vault — playbook, audit reports | Read-write |
| `.claude/skills/folder-audit/` | Audit skill | Read-only |
| `.claude/skills/pipeline-scaffold/` | Pipeline creation skill | Read-only |
| `.claude/skills/run-stage/` | Stage execution skill | Read-only |
| `.claude/skills/validate-pipeline/` | Pipeline validation skill | Read-only |
| `.claude/skills/stage-review/` | Stage review skill | Read-only |

## Routing Table

| Task | Go to | Read |
|------|-------|------|
| Audit a project's folder structure | `.claude/skills/folder-audit/` | SKILL.md |
| Scaffold a new ICM pipeline | `.claude/skills/pipeline-scaffold/` | SKILL.md |
| Execute a pipeline stage | `.claude/skills/run-stage/` | SKILL.md |
| Validate pipeline contract chain | `.claude/skills/validate-pipeline/` | SKILL.md |
| Review a stage before advancing | `.claude/skills/stage-review/` | SKILL.md |
| Understand the methodology | `File-tree audit/` | CONTEXT.md, then Playbook |
| Review past audits | `File-tree audit/audits/` | Most recent report |

## Naming Conventions

- **Audit reports:** `audit-{project-name}-YYYY-MM-DD.md`
- **Playbooks:** `Playbook - {Title}.md`
- **Context files:** `CONTEXT.md` (one per workspace folder)
- **Skill files:** `SKILL.md` (one per skill in `.claude/skills/{skill-name}/`)
- **Stage folders:** `{NN}_{stage-name}/` (zero-padded, snake_case)

## Rules

- Read this file first on every new session
- Fix recommendations go in priority order: structure (60%) > routing (30%) > AI tuning (10%)
