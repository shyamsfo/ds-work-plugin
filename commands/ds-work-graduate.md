---
name: ds-work-graduate
description: Promote a lite-mode project to full mode. Adds vision.md, roadmap.md, now.md, design/, reviews/, market-research/ alongside existing milestones.md / backlog.md / parking-lot.md, walks the user through creating vision + roadmap interactively, and (optionally) writes a PRD/PLAN for the active milestone.
---

You are helping the user **graduate** a lite-mode project to full mode. Graduation is one-way and additive: existing files (milestones.md, backlog.md, parking-lot.md, reports/, how-to/) are preserved as-is; new full-mode artifacts are added alongside.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Verify the project is in lite mode

Read `<dir>/ds-work-mode.txt`.

- If the file is missing or contains `full`: the project is already in full mode (or treated as such by default). Tell the user: *"This project is already in full mode — there's nothing to graduate. If you want to fill in any missing full-mode artifacts (vision.md, roadmap.md, now.md), run /ds-work-vision, /ds-work-roadmap, or /ds-work-plan directly."* Then stop.

- If the file contains `lite`: proceed.

## Step 2 — Confirm the user wants to graduate

Explain what graduation does and what stays the same. Show this summary:

```
Graduating from lite mode to full mode will:

ADD:
  product/vision.md             ← created interactively in this command
  product/roadmap.md            ← created interactively in this command
  product/now.md                ← created from milestones.md state (in this command)
  product/design/               ← new directory for per-milestone PRDs and PLANs (empty initially)
  product/reviews/              ← new directory for challenge reports (empty initially)
  product/market-research/      ← new directory (empty initially)
  product/how-to/ds-work-plan.md       ← project-specific planning hints (template)
  product/how-to/ds-work-challenge.md  ← project-specific red-team lenses (template)

UPDATE:
  product/ds-work-mode.txt         ← "lite" → "full"
  product/project-management-principles.md   ← rewritten to the full-mode variant

PRESERVE (no changes):
  product/milestones.md         ← all existing milestones and checkbox state kept verbatim
  product/backlog.md            ← unchanged
  product/parking-lot.md        ← unchanged
  product/reports/              ← unchanged
  product/how-to/ds-work-{continue,halt,status,update}.md   ← unchanged
  product/research/, operations/, learnings/                ← unchanged
```

Ask: *"Ready to graduate? (y/n)"* — wait for an explicit yes before any writes.

## Step 3 — Walk vision creation

Hand off to the vision-creation flow. Use the same interactive sequence as `/ds-work-vision`'s "Step 2A — Create Mode": walk Problem → Customer → Market context → North star → Why now → What we are not, one section at a time, drafting and confirming each before moving on. Write to `<dir>/vision.md` once approved.

Skip the lite-mode gate in `/ds-work-vision` — we know this is a lite project being graduated.

## Step 4 — Walk roadmap creation

Hand off to the roadmap-creation flow. Use the same interactive sequence as `/ds-work-roadmap`'s create mode: Context → Vision pointer (references `vision.md` you just wrote) → Architecture → Delivery phases → Key Decisions → Open Questions.

Import the milestone list from the existing `<dir>/milestones.md` so the user does not re-enter them — for each milestone in milestones.md, surface its name, goal, and exit criteria as a starting point for the roadmap's Delivery Phases section. Let the user edit before saving.

Write to `<dir>/roadmap.md` once approved.

## Step 5 — Create now.md from milestones.md state

Derive `now.md` from current `milestones.md` state — do not ask the user, just synthesize. Find the active milestone (first marked `🔄 in progress`) and its first `[~]` or `[ ]` task. Populate:

- **Current focus**: active milestone name + status
- **Next action**: that first unchecked task
- **What Was Just Done**: leave a placeholder bullet (`- Project graduated to full mode on YYYY-MM-DD`) — the user will update on the next halt
- **What's Coming Next**: the next 3–5 unchecked tasks in the active milestone
- **Key Files**: the standard table (milestones.md, roadmap.md, vision.md, now.md, design/M{N}-PRD.md once it exists)
- **Recent Commits**: `git log --oneline -5` output

Use the structure shown in `/ds-work-scaffold` Step 4 (full-mode now.md template). Write to `<dir>/now.md`.

## Step 6 — Create the new directories

Create empty directories with `.gitkeep` files:
- `<dir>/design/.gitkeep`
- `<dir>/reviews/.gitkeep`
- `<dir>/market-research/.gitkeep`

If any of these directories already exist (unlikely in a lite project but possible if the user created them manually), leave their contents alone and just make sure a `.gitkeep` exists if the directory is empty.

## Step 7 — Add the two extra how-to extension files

Create these only if they do not already exist:
- `<dir>/how-to/ds-work-plan.md` — use the template from `/ds-work-scaffold` Step 9a
- `<dir>/how-to/ds-work-challenge.md` — use the template from `/ds-work-scaffold` Step 9b

## Step 8 — Rewrite project-management-principles.md to the full variant

Overwrite `<dir>/project-management-principles.md` with the full-mode principles content from `/ds-work-scaffold` Step 10 (the long version, starting at `# Project Management Principles`, ending at the *"To update the principles for all future projects…"* footer).

Confirm to the user before overwriting if the file shows local edits (compare against the lite-variant template). If the user has customised the principles doc, ask which sections to preserve before clobbering.

## Step 9 — Flip the mode marker

Overwrite `<dir>/ds-work-mode.txt` with the single line `full`.

This is the last write so that if any earlier step fails or the user aborts, the project stays in lite mode and the partial state is recoverable.

## Step 10 — Offer to plan the active milestone

The active milestone in `milestones.md` is now a candidate for its first PRD + PLAN.

Show the user:

```
Graduation complete. Mode is now: full

Next suggested step:
- The active milestone (M{N} — <name>) currently has only a flat task checklist.
  Run /ds-work-plan to write its M{N}-PRD.md and M{N}-PLAN.md interactively.

Other commands you can now use:
- /ds-work-vision        review / update the vision (drift review)
- /ds-work-roadmap       review / update the roadmap (drift review)
- /ds-work-plan          plan any milestone — writes PRD + PLAN
- /ds-work-challenge     pressure-test any planning artifact
- /ds-work-one-pager     generate an external-facing summary
- /ds-work-elevator-pitch generate pitch variants

The lite-only files (milestones.md, backlog.md, parking-lot.md, reports/) work exactly as before.
```

Offer to run `/ds-work-plan` for the active milestone now. If the user accepts, hand off to that command. If not, wrap up.

## Step 11 — Final report

List every file created, every file updated, and the new mode. Suggest `/ds-work-continue` if the user wants to start a fresh session in the now-full project.
