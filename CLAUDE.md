# Folder Structure

Tools, playbooks, and audits for evaluating project folder architecture against the three-layer routing system.

## Workspaces

| Folder | Purpose | Read/Write |
|--------|---------|------------|
| `File-tree audit/` | Obsidian vault — playbook, audit reports | Read-write |
| `.claude/skills/folder-audit/` | Executable audit skill | Read-only |

## Routing Table

| Task | Go to | Read |
|------|-------|------|
| Run an audit on a project | `.claude/skills/folder-audit/` | SKILL.md |
| Understand the methodology | `File-tree audit/` | CONTEXT.md, then Playbook |
| Review past audits | `File-tree audit/audits/` | Most recent report |

## Naming Conventions

- **Audit reports:** `audit-{project-name}-YYYY-MM-DD.md`
- **Playbooks:** `Playbook - {Title}.md`
- **Context files:** `CONTEXT.md` (one per workspace folder)
- **Skill files:** `SKILL.md` (one per skill in `.claude/skills/{skill-name}/`)

## Rules

- Read this file first on every new session
- Fix recommendations go in priority order: structure (60%) > routing (30%) > AI tuning (10%)
