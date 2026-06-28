# Client Brief: Folder Structure Protocol

**Client:** Me. **Built for:** Me.

## The problem

I run ~50 projects across Claude Code (in VS Code), Claude.ai, and Obsidian. My folders drift into chaos faster than I can clean them. Two costs compound:

1. **Human cost.** I lose minutes every session re-finding where things live, and onboarding any collaborator into a project means a tour I have to give in person.
2. **AI cost.** When Claude opens a messy project, it guesses where files are, loads the wrong context, and produces inconsistent output. I used to blame the model. The real problem was structural: the context *before* the model runs.

## What I'd already tried

Ad-hoc `CLAUDE.md` files, a naming convention I never followed, and "I'll clean it later" (I never did). None survived a busy week, because none of them *checked* whether a folder was well-structured. They relied on my discipline.

## What I needed

Not another template to maintain by hand. I needed a **tool that audits any project against a real methodology and tells me exactly what to fix, in priority order**, and one that scaffolds new projects correctly so the mess never forms.

## What I built

Five chained Claude skills built on the ICM five-layer context hierarchy:

- `/folder-audit` scores any project (X/16, or X/18 with pipelines), flags anti-patterns, and returns the top 3 fixes.
- `/pipeline-scaffold`, `/run-stage`, `/stage-review`, `/validate-pipeline` build and run staged workflows with contracts between every stage.

The structure now stays clean without me having to think about it. That's the whole point.
