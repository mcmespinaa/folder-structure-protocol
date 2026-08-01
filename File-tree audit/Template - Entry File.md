# Template — Entry File

<!-- provenance -->
*Generated 2026-08-01 by Claude (Opus 5).*
*Session: 2026-08-01-0230-webportal · id 50dce16b-aaa8-4d01-b3be-60cd67b64f67*
*Source: ad-hoc*

The canonical shape for a Layer 0 entry file (`CLAUDE.md` / `AGENTS.md`). FSP scores entry
files but has never shipped one to copy — which is how a repo governed by this protocol ends
up with a 133-line root file and a 402-line `AGENTS.md`. This closes that gap.

Copy the fenced block below, fill every `{}`, delete what does not apply.

## The template

```markdown
---
updated: YYYY-MM-DD
---

# {folder-name}

> {One sentence: what this folder IS and why it exists — the one thing an agent
> cannot derive by looking at the file list.}

## Workspaces

| Folder | Purpose | Read/Write |
|---|---|---|
| `{subfolder}/` | {what lives here} | Read-write / **Read-only** |

## Routing table

| Task | Go to | Read |
|---|---|---|
| {the situation that sends an agent there} | `{folder}/` | {file} |

## Naming conventions

- **{File type}:** `{explicit-pattern}.md`

## Rules

- {Deltas only — state what differs from the level above, never restate it.}

## What to avoid

- {Traps, wrong-folder risks, things that look correct and are not.}

---
Cascade: parent ↑ `{../CLAUDE.md}` · children ↓ `{child entry files, or "none"}`
Maintained by: {owner} · updated {YYYY-MM-DD}
```

That skeleton is ~33 lines filled and scores 6/6 on the Layer 0 rubric.

## The six sections, and why each earns its place

| Section | Layer 0 criterion | The test it has to pass |
|---|---|---|
| Identity blockquote | #5 Identity | Could an agent derive this from `ls`? If yes, cut it. |
| Workspaces table | #6 Read-only marked | The Read/Write column *is* the criterion. Don't drop it. |
| Routing table | #3 Routing table | Routes by **task**, not by folder. A `Folder \| Purpose` table does not satisfy this. |
| Naming conventions | #4 Conventions | Explicit patterns. "Follow existing conventions" scores 0. |
| Rules | — | Deltas only. |
| What to avoid | — | Naming the noise beats naming the signal. |

## The three gates

**1. The brevity gate — apply per line.**
> *Would removing this line cause the AI to make a mistake? If no, cut it.*

This is the instrument, not the line count. It resolves cases a target never will: "50 skills
installed" fails (the agent already has the list); "plugin X always fails, use the CLI instead"
passes (without it, a session burns calls on a dead path).

**2. The delta rule — never restate a rule from the level above.**
Root → workspace → folder. Each level states only its own deltas and links up and down. The
moment a rule lives in two files, they disagree — and the copy an agent reads first wins,
which will not reliably be the correct one. The `Cascade:` line makes the chain navigable in
both directions without a central index.

**3. Curated, not exhaustive.**
Never list every file; the agent can see the files. List only rows that answer *"open X when
Y."* Four to seven rows is the working range. A routing table longer than the folder listing
has inverted its own purpose.

## Sizing

| | Target | Hard cap |
|---|---|---|
| Entry file | **≤ 45 lines** | **60** |

Reconciled from three sources that broadly agree: the FSP rubric (<50), the ICM course
material (40–50), and practitioner field notes (aim <60, hard cap 100). Non-blank lines.
Past the cap, the file has context files hiding inside it — pull them out into `CONTEXT.md`.

## One file or two?

Default to **one**. `CLAUDE.md` carries the routing table — Layer 0 criterion #3 requires it
there, so moving routing into `CONTEXT.md` fails the audit it was meant to pass.

Add a `CONTEXT.md` beside it only when a workspace needs orientation *beyond* routing: what is
stale, what is disposable, which folders will waste an agent's time. If `CLAUDE.md` already
routes and names the ignore list, the second file fails the brevity gate — do not create it.

## `AGENTS.md` vs `CLAUDE.md`

`AGENTS.md` is vendor-neutral; `CLAUDE.md` is not. Where both exist, make `AGENTS.md` the
substantive file and have `CLAUDE.md` point at it. Renaming an established tree is rarely
worth the churn — adopt it at new roots and where `AGENTS.md` is already present.

Either way the shape above is identical. Size is the failure mode to watch: an `AGENTS.md`
that has grown into a contributor guide, CI spec, and style guide is three `CONTEXT.md` files
wearing one filename.

## Attribution

The five-section skeleton is FSP's own reference implementation (this repo's `CLAUDE.md`).
The layer model is [ICM](https://github.com/RinDig/Interpreted-Context-Methdology) (Van Clief &
McDermott, 2026). The seven common mistakes behind the gates are from the ICM course material
(Quantum Quill Lyceum, Module 3.3). The brevity gate, the delta rule, the cascade line and the
`updated:` field come from practitioner field notes (private correspondence, July 2026).

Not an official standard — the sizing numbers are practical shortcuts, not measurements.
