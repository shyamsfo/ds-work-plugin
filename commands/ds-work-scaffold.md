---
name: ds-work-scaffold
description: Bootstrap a new project with the standard PM structure — product/ directory, placeholder docs, how-to extension files, and project-management-principles.md. Asks interactively whether you want full or lite mode (or pass --lite / --full to skip the prompt). Run from the project root.
---

Bootstrap a new project with the standard project management structure.

## Modes

Parse `$ARGUMENTS`. The first token may be `--lite` or `--full`. Everything after the mode token (if any) is treated as an optional project description and may be used to fill in placeholders. If no description is provided, use generic placeholder text.

There are two modes for scaffolded projects:

- **full** — complete document stack: vision, roadmap, milestones, now, design/, reviews/, market-research/, etc. Use when the project has a real product story, multiple milestones with design decisions to capture, or external stakeholders to communicate with.
- **lite** — stripped-down stack: just milestones, backlog, parking-lot, reports, and how-to extensions. No vision, no roadmap, no now.md, no per-milestone PRDs/PLANs. Use for simple projects with a clear execution path that don't need product-level framing or design records. `milestones.md` doubles as *the* plan.

### Step 0a — Pick mode (interactive)

If `$ARGUMENTS` contains `--lite` or `--full`, use that mode and proceed.

If neither flag is passed, **ask the user before doing anything else**:

```
This will scaffold a new project. Pick a mode:

  1. full — full PM stack: vision.md, roadmap.md, now.md, per-milestone PRDs + PLANs,
            design reviews, market research. Best for projects with real product
            framing, multiple milestones with design decisions, or external stakeholders.

  2. lite — stripped stack: just milestones.md (with all milestones in one file),
            backlog.md, parking-lot.md, and reports/. No vision, no roadmap, no PRDs.
            Best for projects with a clear execution path that don't need
            product-level framing. milestones.md doubles as the plan. Can be promoted
            to full later with /ds-work-graduate.

Which mode? (full / lite, default: full)
```

Wait for the user's reply. Accept `full`, `f`, `1` → full mode; `lite`, `l`, `2` → lite mode; empty / `y` / anything ambiguous → ask again (do not assume). Once you have a clear answer, proceed.

### Step 0b — Record the mode

Record the chosen mode in `product/ds-work-mode.txt` — a visible, single-line file containing exactly `lite` or `full`. All other ds-work-* commands read this file to branch their behavior. If the file is missing, mode defaults to `full` so projects scaffolded before this flag existed continue to work.

A lite project can be promoted later with `/ds-work-graduate`, which flips the mode marker and creates the missing artifacts interactively.

## What this creates

### Full mode

```
product/
├── ds-work-mode.txt                 # contains "full"
├── vision.md                     # why + who (problem, customer, north star)
├── roadmap.md                    # architecture + milestone plan (placeholder)
├── milestones.md                 # progress tracker (placeholder)
├── now.md                        # session pointer (placeholder)
├── backlog.md                    # low-commitment mind-dump (placeholder)
├── parking-lot.md                # unscheduled work items (placeholder)
├── project-management-principles.md   # canonical PM reference (copied from scaffold)
├── design/                       # PRDs and PLANs per milestone (empty, ready to use)
├── reviews/                      # challenge reports (empty, ready to use)
├── market-research/              # competitive analysis, user research (empty)
├── research/                     # pre-decision options analyses and deep dives (empty)
├── operations/                   # cluster runbooks, infra how-tos (empty)
├── learnings/                    # post-hoc reports, incident retrospectives (empty)
├── reports/                      # session reports live here (empty, ready to use)
└── how-to/
    ├── ds-work-challenge.md           # project-specific challenge lenses (template)
    ├── ds-work-continue.md            # project-specific resume additions (template)
    ├── ds-work-halt.md                # project-specific shutdown steps (template)
    ├── ds-work-plan.md                # project-specific planning hints (template)
    ├── ds-work-status.md              # project-specific prerequisite checks (template)
    └── ds-work-update.md              # project-specific update additions (template)
```

### Lite mode

```
product/
├── ds-work-mode.txt                 # contains "lite"
├── milestones.md                 # ALL milestones live here — sections per milestone, each with goal + task checklist. Doubles as the plan.
├── backlog.md                    # low-commitment mind-dump (placeholder)
├── parking-lot.md                # unscheduled work items (placeholder)
├── project-management-principles.md   # canonical PM reference (copied from scaffold, lite variant)
├── research/                     # pre-decision options analyses and deep dives (empty)
│   └── spikes/                   # time-boxed feasibility investigations
├── operations/                   # cluster runbooks, infra how-tos (empty)
├── learnings/                    # post-hoc reports, incident retrospectives (empty)
├── reports/                      # session reports live here (empty, ready to use)
└── how-to/
    ├── ds-work-continue.md            # project-specific resume additions (template)
    ├── ds-work-halt.md                # project-specific shutdown steps (template)
    ├── ds-work-status.md              # project-specific prerequisite checks (template)
    └── ds-work-update.md              # project-specific update additions (template)
```

**Skipped in lite mode:** `vision.md`, `roadmap.md`, `now.md`, `design/`, `reviews/`, `market-research/`, and the `how-to/ds-work-{plan,challenge}.md` extension files. Commands that target those artifacts (`/ds-work-vision`, `/ds-work-roadmap`, `/ds-work-plan`, `/ds-work-one-pager`, `/ds-work-elevator-pitch`, `/ds-work-challenge`) detect lite mode and offer to graduate before doing anything.

## Steps

### 1. Check for existing structure

If `product/` already exists, warn the user: "product/ already exists — scaffolding will add missing files but will not overwrite existing ones." Proceed file by file, skipping any that already exist.

If `product/ds-work-mode.txt` already exists and its content conflicts with the mode requested via flag, stop and tell the user — do not overwrite the marker silently. Suggest `/ds-work-graduate` if they want to move from lite to full.

### 1a. Write the mode marker

Create `product/ds-work-mode.txt` containing exactly one of:
- `lite`
- `full`

Single line, no trailing content. This is the first file to create — every subsequent step branches on it.

### 1b. Lite-mode file set

**If mode is `lite`, skip the following steps entirely and create only the files marked `[lite]` below:**

- Step 5 (milestones.md) — use the **lite variant** template described in Step 5.
- Step 5a (parking-lot.md)
- Step 5b (backlog.md)
- Step 6 (how-to/ds-work-continue.md)
- Step 7 (how-to/ds-work-halt.md)
- Step 8 (how-to/ds-work-update.md)
- Step 9 (how-to/ds-work-status.md)
- Step 10 (project-management-principles.md) — but use the **lite variant** text described in Step 10.
- Step 11 (placeholder `.gitkeep` files) — only for `research/`, `research/spikes/`, `operations/`, `learnings/`, `reports/`. Skip `design/`, `reviews/`, `market-research/`.
- Step 12 (report back) — use the **lite variant** of the next-steps message described in Step 12.

Skip Steps 2 (roadmap.md), 3 (vision.md), 4 (now.md), 9a (how-to/ds-work-plan.md), 9b (how-to/ds-work-challenge.md).

**If mode is `full`, run all steps below.**

### 2. Create product/roadmap.md

```markdown
# Roadmap

> Replace this placeholder with your project's architecture, vision, and milestone plan.
> See project-management-principles.md for guidance on what belongs here.

## Purpose

This document captures the architecture decisions, component design, and phased delivery
plan. It is a **planning artifact**, not a task tracker.

---

## Context: Where We Are Now

<Describe the current state of the project — what exists, what works, what doesn't.>

---

## Vision

See `product/vision.md`.

---

## Architecture

<Diagram or description of the main components and how they fit together.>

---

## Delivery Phases

### Milestone 1: <Name>
*Goal: <one sentence>*

Stories:
1. <Story title>
2. <Story title>

Exit criteria: <How do you know M1 is done?>

---

## Key Decisions Made

| Decision | Choice | Rationale |
|----------|--------|-----------|

---

## Open Questions

- <Question 1>
- <Question 2>
```

### 3. Create product/vision.md

```markdown
# Vision

> The why behind this project — problem, customer, and north star.
> More stable than roadmap.md. Rarely changes once the product direction is set.
> Market research and competitive context lives in `product/market-research/`.

---

## Problem

<What pain are we solving? Who feels it and when?>

---

## Customer / Target user

<Who is this for? Be specific about the role, context, and workflow.>

---

## Market context

<Where does this sit in the landscape? What alternatives exist and why aren't they enough?>
See `product/market-research/` for supporting research.

---

## North star

<What does success look like in 2–3 years? What can a user do that they couldn't before?>

---

## Why now?

<What makes this the right time to build this?>

---

## What we are not

<Product-level anti-goals — not feature-scope cuts, but fundamental things this product will never try to be.>
```

### 5. Create product/milestones.md

**Full mode** uses this template:

```markdown
# Milestones

Lightweight task tracker. Update status markers and checkboxes as work progresses.
Architecture context lives in `roadmap.md`. Per-milestone design lives in `design/M{N}-PRD.md`.

## Status Key

```
⏳ pending      — not started
🔄 in progress  — actively being worked
✅ done         — exit criteria met
🚫 blocked      — waiting on something external
```

Task checkboxes:
- `[ ]` not done
- `[x]` done
- `[~]` in progress
- `[-]` skipped / won't do

---

## M1 — <Milestone Name>
**Status**: ⏳ pending
**Goal**: <one sentence>

### Tasks

- [ ] <Task 1>
- [ ] <Task 2>
- [ ] <Task 3>

**Exit criteria**: <How do you know this milestone is done?>

---

## Notes

- Milestones are sequential by dependency, not by calendar priority
- Each milestone must pass its exit criteria before the next begins
```

**Lite mode** uses this template instead. In lite mode this file *is* the plan — there are no PRD/PLAN files. Each milestone is a single section with its goal and a flat task checklist; mark the active one `🔄 in progress`, the rest `⏳ pending`.

```markdown
# Milestones

All milestones live in this file. Each section is one milestone with its goal, status, task checklist, and exit criteria. There are no separate PRD/PLAN files in lite mode — this file is the plan.

## Status Key

```
⏳ pending      — not started
🔄 in progress  — actively being worked
✅ done         — exit criteria met
🚫 blocked      — waiting on something external
```

Task checkboxes:
- `[ ]` not done
- `[x]` done
- `[~]` in progress
- `[-]` skipped / won't do

The active milestone is the first one marked `🔄 in progress`. `/ds-work-continue` and `/ds-work-status` find the next `[~]` or `[ ]` task within it.

---

## M1 — <Milestone Name>
**Status**: 🔄 in progress
**Goal**: <one sentence>

- [ ] <Task 1>
- [ ] <Task 2>
- [ ] <Task 3>

**Exit criteria**: <How do you know this milestone is done?>

---

## M2 — <Milestone Name>
**Status**: ⏳ pending
**Goal**: <one sentence>

- [ ] <Task 1>
- [ ] <Task 2>

**Exit criteria**: <How do you know this milestone is done?>

---

## Notes

- Milestones are sequential by dependency, not by calendar priority
- Each milestone must pass its exit criteria before the next begins
- If a milestone grows enough to need a real spec, run `/ds-work-graduate` to promote the project to full mode
```

### 4. Create product/now.md

```markdown
# Where We Are Now

> Read this first when resuming work. Update it whenever a milestone or major task changes.

---

## Current Focus

**Milestone**: M1 — <Milestone Name>
**Status**: ⏳ not started
**Next action**: <First task from milestones.md>

---

## What Was Just Done

- Project scaffolded — replace this with session notes after first work session

---

## What's Coming Next

1. [ ] <Task 1>
2. [ ] <Task 2>
3. [ ] <Task 3>

---

## Key Files

| File | Purpose |
|------|---------|
| `product/milestones.md` | Task checklist for all milestones |
| `product/roadmap.md` | Architecture and vision — stable reference |
| `product/now.md` | This file — current focus and resume context |

---

## How to Resume After a Break

1. Read this file
2. Check `milestones.md` for the first unchecked task in the active milestone
3. Run `/ds-work-continue` for a full context brief

---

## Recent Commits

```
<git log --oneline -5 will appear here after first commits>
```
```

### 5a. Create product/parking-lot.md

```markdown
# Parking Lot

Unscheduled work items — bugs, chores, ideas, research notes — captured so they don't get lost. Add items as you go; `/ds-work-parking-lot` surfaces them and suggests what to tackle.

One item per line. Tags are freeform but the conventions are:
- type: `[bug]` `[chore]` `[idea]` `[research]`
- size: `[S]` `[M]` `[L]`
- milestone link: `→M{N}` (e.g. `→M2`)

## Open

<!-- newest first; format: - [ ] YYYY-MM-DD — title  [tags] -->

## Done / Dropped (last 10)

<!-- newest first; auto-trimmed to 10 -->
```

### 5b. Create product/backlog.md

```markdown
# Backlog

Mind-dump. Captured so we don't forget. No commitment, no priority. Sweep periodically: promote to `parking-lot.md`, fold into a PRD's Out-of-scope, or delete.

One item per line. Date prefix optional but useful: `- YYYY-MM-DD — <thought>`. Append at the bottom.

## Items

<!-- append at bottom; format: - YYYY-MM-DD — <thought> -->
```

### 6. Create product/how-to/ds-work-continue.md

```markdown
# Project-specific ds-work-continue additions

> Fill in any project-specific context sources that /ds-work-continue should gather.
> Examples: a CURRENT_STATE.md file, a cluster status check, an infra state API call.
> Delete this file if there are no project-specific additions needed.

## Additional context to gather

TODO: list any project-specific files or commands to read during resume.

## Additional fields in resume brief

TODO: list any fields to add to the resume brief output (e.g. infrastructure state).
```

### 7. Create product/how-to/ds-work-halt.md

```markdown
# Project-specific halt steps

> These steps run AFTER the generic ds-work-halt steps (docs updated, committed, pushed).
> Add any project-specific teardown here — infrastructure hibernation, service shutdown, etc.
> Delete this file if there are no project-specific shutdown steps.

## TODO: Add project-specific shutdown steps

Examples of what goes here:
- Scale down cloud infrastructure (Kubernetes nodes, VMs, etc.)
- Stop background processes
- Update an external status page
- Send a session-end notification

Replace this with your actual shutdown steps and remove this comment.
```

### 8. Create product/how-to/ds-work-update.md

```markdown
# Project-specific ds-work-update additions

> These additions run during /ds-work-update state-gathering, before writing the report.
> Add any project-specific state to capture in mid-session snapshots.
> Delete this file if there are no project-specific additions needed.

## Additional state to gather

TODO: list any project-specific state to include in update snapshots.

## Additional fields in the report snapshot

TODO: describe any extra fields to add to the "In progress" section.
```

### 9. Create product/how-to/ds-work-status.md

```markdown
# Project-specific ds-work-status additions

> These additions run during /ds-work-status prerequisite checking.
> Add any project-specific infrastructure checks needed before starting the next task.
> Delete this file if there are no project-specific prerequisite checks.

## Additional context to gather

TODO: list any project-specific state files or commands to check.

## Infrastructure prerequisite check

TODO: describe what "infrastructure ready" means for this project and how to check it.
```

### 9a. Create product/how-to/ds-work-plan.md

```markdown
# Project-specific ds-work-plan additions

> These additions run during /ds-work-plan, when generating PLAN sub-tasks.
> Add project-specific verification commands, file conventions, or sub-task templates.
> Delete this file if there are no project-specific additions needed.

## Verification commands to fold into PLAN items by default

TODO: list the canonical commands the project uses to verify a change works
(e.g. `pytest tests/`, `make typecheck`, `kubectl rollout status …`).

## File conventions

TODO: any naming or directory conventions to enforce when planning
(e.g. tests live next to source, migrations are numbered, etc.).
```

### 9b. Create product/how-to/ds-work-challenge.md

```markdown
# Project-specific ds-work-challenge lenses

> These additions run during /ds-work-challenge to add project-specific red-team angles.
> Add lenses or recurring concerns the challenger should always check.
> Delete this file if there are no project-specific lenses needed.

## Recurring concerns to always raise

TODO: list things this project has been burned by before, or constraints
the challenger should always pressure-test against
(e.g. "always question latency under load", "always check k8s quota implications",
"always ask whether this respects the data-residency policy").
```

### 10. Create product/project-management-principles.md

**Full mode**: write the content below (from `# Project Management Principles` down to the *"To update the principles for all future projects…"* footer) verbatim into `product/project-management-principles.md`.

**Lite mode**: write the *lite variant* shown at the end of this step instead — a much shorter principles doc tailored to the lite stack.

---

# Project Management Principles

This document explains how we work on this project: what our documents are, what our skills do, and how they fit together. Read this before your first session.

For the full command reference — what every `ds-work-*` command does, when to run it, and how they fit together — run **`/ds-work-user-guide`**.

---

## The Guiding Principle

**Each question has exactly one document to answer it. Reference, don't duplicate.**

The moment the same content lives in two places, they drift. When documents drift, sessions start with confusion instead of clarity. We keep the document count small and the responsibility of each document strict.

---

## The Document Stack

```
product/vision.md            ← why + who (problem, customer, north star)
product/roadmap.md           ← what + how (architecture, milestones, key decisions)
product/milestones.md        ← progress tracker (story checkboxes, milestone status)
product/now.md               ← session pointer (what I'm doing right now)
product/backlog.md           ← low-commitment mind-dump (no priority, no tags)
product/parking-lot.md       ← unscheduled work items (bugs, chores, ideas captured for later)
product/design/M{N}-PRD.md   ← milestone spec (what to build, scope, success criteria)
product/design/M{N}-PLAN.md  ← milestone execution checklist (sub-task checkboxes)
product/reviews/             ← challenge reports (adversarial reviews of planning artifacts)
product/market-research/     ← competitive analysis, user research (feeds vision.md)
product/research/            ← pre-decision options analyses and deep dives
product/operations/          ← cluster runbooks, infra how-tos
product/learnings/           ← post-hoc reports, incident retrospectives
product/reports/             ← session history (timestamped logs of what happened)
product/how-to/              ← project-specific skill extensions (infrastructure, etc.)
```

### `product/vision.md` — The Product Vision

The why. Read it to understand the problem, the customer, and the north star. It contains:
- Problem statement and who feels it
- Target customer / user profile
- Market context (references `market-research/` for depth)
- North star — what success looks like in 2–3 years
- Why now
- What this product is not (product-level anti-goals)

**More stable than roadmap.md.** Update it when product direction changes, not when milestones shift.

### `product/roadmap.md` — The Architecture Document

The technical delivery plan. Read it to understand how we're building it. It contains:
- Architecture stack and component design
- All milestones in sequence, with their goals and exit criteria
- Key decisions already made (and the rationale behind them)
- Open questions with proposed resolutions

**Do not edit roadmap.md unless architecture changes.** It is not a task tracker.

### `product/milestones.md` — The Progress Tracker

The live checklist. Read it to understand where we are across all milestones. It contains:
- One section per milestone with status (`⏳ pending`, `🔄 in progress`, `✅ done`, `🚫 blocked`)
- Story-level checkboxes: `[ ]` not done, `[x]` done, `[~]` in progress, `[-]` skipped
- For milestones with a PRD: a `Design:` reference to the design doc
- Exit criteria per milestone

**The milestone section owns the completion state of stories. The PRD owns the content of stories.**

**Milestone status markers:**
```
⏳ pending      — not started
🔄 in progress  — actively being worked
✅ done         — exit criteria met
🚫 blocked      — waiting on something external
```

### `product/parking-lot.md` — The Parking Lot

Unscheduled work items — bugs, chores, ideas, research notes — captured so they don't get lost when they surface mid-session. Distinct from `milestones.md` (which is scheduled, milestone-bound work) and `roadmap.md`'s Open Questions (strategic, not actionable).

Each item is one line: `- [ ] YYYY-MM-DD — title  [tags]`. Tags are freeform; conventions are `[bug] [chore] [idea] [research]`, sizes `[S] [M] [L]`, and milestone links `→M{N}`.

Managed by `/ds-work-parking-lot` — show the list with suggestions, add items (`/ds-work-parking-lot add <text>`), promote items into the active PLAN, or drop with a reason. `/ds-work-halt` asks each session whether anything is worth parking.

### `product/backlog.md` — The Backlog

Mind-dump that sits one rung *below* the parking lot in commitment level. The parking lot says "this is worth doing eventually"; the backlog says "I don't want to forget this thought." No priority, no tags, no checkboxes — just dated bullets, append-only at the bottom.

Each item is one line: `- YYYY-MM-DD — <thought>` (date optional but useful).

Managed by `/ds-work-backlog` — show the list (no ranking, no suggestions), add items (`/ds-work-backlog add <text>`), or `sweep` periodically to walk each item and decide: **promote** to parking-lot, **fold** into a PRD's Out-of-scope, or **delete** (silently — the backlog is intentionally lossy).

The capture ladder: `backlog.md` → `parking-lot.md` → `design/M{N}-PLAN.md`. Items graduate upward; they don't have to.

### `product/now.md` — The Session Pointer

The first thing you read when resuming. It tells you exactly where you are and where to go next. It contains:
- Current milestone + status
- Design doc reference + current story (if the milestone has a PRD)
- What was just done (last session's output, 3–5 bullets)
- Current infra state (if project has infrastructure)
- What's coming next (ordered task list, first unchecked item is the target)
- Key files you'll need
- Recent commits

**The skills keep `now.md` current.** You should rarely need to edit it by hand.

### `product/design/` — PRDs and PLANs (one pair per milestone)

Each milestone has two artifacts in `design/`, both produced and maintained by `/ds-work-plan`:

- **`M{N}-PRD.md`** — the milestone **spec**: goal, scope, out-of-scope, success criteria, approach, open questions. Answers "what are we building and why?"
- **`M{N}-PLAN.md`** — the milestone **execution checklist**: sub-tasks grouped by story, each with a file path and verification step. Answers "what's the next concrete step?"

**Sub-state of an in-progress milestone** is inferred from these files plus checkbox progress (no explicit state field anywhere):

| PRD | PLAN | Boxes | Sub-state |
|---|---|---|---|
| missing | missing | — | `defined` |
| exists | missing | — | `drafting` |
| exists | exists | none ticked | `planned` |
| exists | exists | some ticked | `executing` |
| exists | exists | all ticked | `complete` |

The PRD is the design record (Decision Log at the bottom for significant changes). The PLAN is the execution record — its checkboxes *are* the change history; no Decision Log needed.

### `product/reviews/` — Challenge Reports

Adversarial reviews of planning artifacts, written by `/ds-work-challenge`. Pressure-tests for *soundness* (is this right?), separate from the drift-review modes built into vision/roadmap/plan (does this still match reality?).

Each challenge report is timestamped and append-only as a directory. The user decides which concerns to fold back into the source doc's Decision Log.

### `product/reports/` — Session History

One file per day: `product/reports/YYYY-MM-DD.md`. Append-only. Written by `/ds-work-update` (mid-session) and `/ds-work-halt` (session end). Historical record only — not a planning document.

### `product/how-to/` — Skill Extension Files

Project-specific additions to the generic global skills. Each file extends one skill:

| File | Extends |
|------|---------|
| `how-to/ds-work-continue.md` | `/ds-work-continue` — extra context sources, extra resume brief fields |
| `how-to/ds-work-halt.md` | `/ds-work-halt` — infrastructure shutdown, teardown steps |
| `how-to/ds-work-update.md` | `/ds-work-update` — extra state to capture in snapshots |
| `how-to/ds-work-status.md` | `/ds-work-status` — infrastructure prerequisites, extra context |
| `how-to/ds-work-plan.md` | `/ds-work-plan` — project-specific verification commands, file conventions |
| `how-to/ds-work-challenge.md` | `/ds-work-challenge` — project-specific red-team angles or recurring concerns |

**How-to files contain only the project-specific additions.** The generic steps live in the global skills. If there's nothing project-specific to add, the file can be deleted.

---

## The Skills

Skills are Claude Code slash commands. Global skills live in `~/.claude/commands/` and work in any project. Invoke them with `/skill-name [arguments]`.

### Session Skills (global)

#### `/ds-work-continue [dir]`
**When**: Start of every session.

Reads `now.md`, `milestones.md`, `roadmap.md`, the latest session report, and `how-to/ds-work-continue.md` (if present). Outputs a resume brief with active story context from the PRD if applicable.

#### `/ds-work-status [dir]`
**When**: After finishing a task, to orient to the next one.

Shorter than `/ds-work-continue`. Finds the active task, checks prerequisites (including project-specific infra state from `how-to/ds-work-status.md`), and gives the concrete first action.

#### `/ds-work-update [dir]`
**When**: Mid-session checkpoint — after a significant task, before stepping away.

Appends a timestamped snapshot to today's session report. Does **not** commit. Incorporates project-specific state from `how-to/ds-work-update.md` if present.

#### `/ds-work-halt [dir]`
**When**: End of every session.

The full shutdown sequence: updates milestones + now.md, writes session report, commits and pushes, then executes `how-to/ds-work-halt.md` for project-specific teardown (infrastructure hibernation, etc.).

**Always end sessions with `/ds-work-halt`**, not manual commits.

#### `/ds-work-backlog [add <text> | sweep]`
**When**: A half-formed thought arrives that you might want to revisit but don't want to commit to — or when the backlog has grown enough to need a sweep.

Reads/writes `backlog.md`. Default mode shows the items (no ranking, no suggestions — backlog is intentionally flat). `add <text>` appends to the bottom. `sweep` walks items one at a time and for each asks: **promote** to parking-lot, **fold** into a PRD's Out-of-scope, **delete** silently, or skip. The backlog sits one rung below the parking lot in commitment level.

#### `/ds-work-parking-lot [add <text> | pick | <tag>]`
**When**: An idea, bug, or chore surfaces that does not belong to the active milestone — or when you want to pull something off the parking lot.

Reads/writes `parking-lot.md`. Default mode shows the grouped list and suggests 1–3 picks (linked to active milestone, quick wins, or stale items). `add <text>` appends a new item; `pick` skips the list and only shows suggestions; passing a tag (e.g. `bug`) filters. Selected items can be tackled now, promoted into the active `M{N}-PLAN.md`, or dropped with a reason.

#### `/ds-work-scaffold [description]`
**When**: Starting a new project.

Creates the full `product/` structure with placeholder docs, `how-to/` templates, and this principles document. Run from the project root.

#### `/ds-work-plan [dir]`
**When**: Starting a new milestone, or revisiting an in-progress milestone's plan.

Interactively produces (or updates) the active milestone's `M{N}-PRD.md` and `M{N}-PLAN.md` in `design/`. State-driven and re-runnable: walks PRD then PLAN if neither exists, fills in whichever is missing, or runs a drift review if both exist. Honors `how-to/ds-work-plan.md` for project-specific verification commands and conventions.

#### `/ds-work-challenge <target> [milestone-id]`
**When**: You want a pressure-test of a planning artifact before committing to it.

Adversarial review — produces a Challenge Report in `reviews/` listing 5–10 concerns from a skeptical-critic frame. Does not modify the source doc. Targets: `vision`, `roadmap`, `milestone-prd`, `milestone-plan`. Honors `how-to/ds-work-challenge.md` for project-specific lenses.

---

## How a Typical Session Flows

```
Start session
  → /ds-work-continue
  → (project-specific) wake infrastructure if needed

Do the work
  → edit files, run commands, iterate
  → /ds-work-update  (optional mid-session checkpoint)

End session
  → /ds-work-halt  (updates docs, commits, runs project-specific teardown)
```

---

## How Milestones, PRDs, and PLANs Work Together

Each in-progress milestone has four documents working together. Each answers one question.

**`milestones.md`** — top-level completion state:
```markdown
## M1 — Walking Skeleton
**Status**: 🔄 in progress

- [ ] Story 1 — Profile extraction
- [ ] Story 2 — Hand-author Helmfile
```

**`product/design/M1-PRD.md`** — the spec: goal, scope, success criteria, approach.

**`product/design/M1-PLAN.md`** — the execution checklist: sub-tasks under each story with file paths and verification steps.

**`now.md`** — session pointer that surfaces current sub-state and the next unchecked PLAN item.

The sub-state of M1 is *inferred* from which of these files exist and how many PLAN boxes are ticked — there's no explicit state field anywhere, so nothing can drift. `/ds-work-continue` and `/ds-work-status` derive the sub-state on each invocation.

Nothing is duplicated. Each document answers exactly one question.

---

## What Goes Where — Quick Reference

| Question | Document |
|----------|----------|
| Why are we building this? Who is it for? | `vision.md` |
| What are we building and how? | `roadmap.md` |
| Which milestone am I in? | `milestones.md` |
| Which stories are done? | `milestones.md` |
| What am I doing right now? | `now.md` |
| What half-formed thought do I want to remember? | `backlog.md` |
| What's parked for later (not yet scheduled)? | `parking-lot.md` |
| What exactly does milestone N require? | `product/design/M{N}-PRD.md` |
| What's the execution checklist for milestone N? | `product/design/M{N}-PLAN.md` |
| Which concerns were raised against a planning artifact? | `product/reviews/<target>-challenge-YYYY-MM-DD.md` |
| What happened last Tuesday? | `product/reports/YYYY-MM-DD.md` |
| How do I shut down after a session? | `/ds-work-halt` (reads `how-to/ds-work-halt.md`) |

---

## Conventions

**Commits** — written by `/ds-work-halt` in this format:
```
session: <one-line summary>

- bullet 1
- bullet 2
```
No WIP commits. Sessions end cleanly with `/ds-work-halt`.

**Story naming** — titles in PRDs match checkboxes in `milestones.md`. Rename in sync.

**PRDs are design records, not task lists** — don't add checkboxes to PRDs. Progress lives in `milestones.md`. If a design decision changes, update the PRD in place with the new decision and rationale.

**Open questions get resolved, not deleted** — the question + answer pair is more useful than either alone.

**Decision Log at the bottom of every PRD** — when a significant design decision changes (an API shape, a storage strategy, a scope cut), append a row to the Decision Log table rather than silently rewriting the doc. This answers "why did we end up here?" without requiring the reader to dig through git history. The format:

```markdown
## Decision Log

| Date | Decision | Rationale | Changed from |
|------|----------|-----------|--------------|
| YYYY-MM-DD | <what was decided> | <why> | <what it replaced, or "new decision"> |
```

Log significant direction changes, contested calls, and anything a future reader would wonder about. Do NOT log every small edit — those belong to git. The question + answer pairs in "Open questions" feed naturally into the Decision Log once resolved.

**Not every milestone needs a PRD** — if the work is straightforward operational tasks (infrastructure changes, benchmarking), track it directly in `milestones.md`. Create a PRD only when there are real design decisions to document.

**How-to files contain only additions** — never duplicate the generic skill steps. If a how-to file would be empty, delete it.

---

*This document is the canonical copy distributed by `/ds-work-scaffold`. To update the principles for all future projects, update the scaffold skill at `~/.claude/commands/ds-work-scaffold.md`.*

---

#### Lite-variant principles doc

In lite mode, write this shorter version instead. It explains the smaller stack and the graduation path; the full version above is unnecessarily heavy for a project that doesn't need vision/roadmap/PRDs.

```markdown
# Project Management Principles — Lite Mode

This is a **lite-mode** project. It uses a stripped-down version of the `ds-work-*` system: just milestones, backlog, parking-lot, and session reports. Use it when the work has a clear execution path and doesn't need product-level framing or per-milestone design records.

For the full command reference, run **`/ds-work-user-guide`**.

To promote this project to full mode (adds vision.md, roadmap.md, now.md, per-milestone PRDs/PLANs, design reviews), run **`/ds-work-graduate`**.

---

## The Document Stack (lite)

```
product/
├── ds-work-mode.txt               ← contains "lite"
├── milestones.md               ← all milestones in one file. Sections per milestone with goal + task checklist. **This file IS the plan.**
├── backlog.md                  ← low-commitment mind-dump (no priority, no tags)
├── parking-lot.md              ← unscheduled work items (bugs, chores, ideas captured for later)
├── project-management-principles.md   ← this file
├── research/                   ← pre-decision options analyses (ds-work-research)
│   └── spikes/                 ← time-boxed feasibility investigations (ds-work-spike)
├── operations/                 ← cluster runbooks, infra how-tos
├── learnings/                  ← post-hoc reports, incident retrospectives
├── reports/                    ← session history (YYYY-MM-DD.md, written by update/halt)
└── how-to/                     ← project-specific skill extensions
    ├── ds-work-continue.md
    ├── ds-work-halt.md
    ├── ds-work-status.md
    └── ds-work-update.md
```

**Skipped vs full mode:** `vision.md`, `roadmap.md`, `now.md`, `design/` (PRDs + PLANs), `reviews/` (challenge reports), `market-research/`, `one-pager.md`, `elevator-pitch.md`, and the `how-to/ds-work-{plan,challenge}.md` extensions.

### `milestones.md` — *the plan*

All milestones live here, one section each. The active milestone is the first one marked `🔄 in progress`. `/ds-work-continue` and `/ds-work-status` find the next `[~]` or `[ ]` task within it.

Task checkboxes:
- `[ ]` not done · `[x]` done · `[~]` in progress · `[-]` skipped / won't do

Milestone status markers:
- `⏳ pending` · `🔄 in progress` · `✅ done` · `🚫 blocked`

There is no separate "sub-state" (no PRD/PLAN files), so `/ds-work-continue` and `/ds-work-status` simply surface the next unchecked task and call it done. If a milestone outgrows a flat checklist, run `/ds-work-graduate`.

### `backlog.md` — mind-dump (unchanged from full mode)

Capture half-formed thoughts. No priority. Append-only. Managed by `/ds-work-backlog`. Sweep periodically to promote items to the parking-lot or drop them silently.

### `parking-lot.md` — unscheduled work (unchanged from full mode)

Items that are worth tackling eventually but aren't in the active milestone. Tagged. Managed by `/ds-work-parking-lot`. In lite mode, `promote` adds the item to the active milestone's task list in `milestones.md` (not to a PLAN file, since none exists).

### `reports/` — session history (unchanged from full mode)

One file per day, written by `/ds-work-update` (mid-session) and `/ds-work-halt` (session end).

---

## Commands Available in Lite Mode

| Command | Notes |
|---|---|
| `/ds-work-continue` | Reads `milestones.md`, finds active milestone + next unchecked task |
| `/ds-work-status` | Same as continue, but tighter output |
| `/ds-work-update` | Mid-session snapshot to today's report — same as full mode but skips `now.md` |
| `/ds-work-halt` | Tick `milestones.md`, write report, commit, push, run project-specific teardown. Does not touch a PLAN file because there isn't one. |
| `/ds-work-backlog` | Unchanged from full mode |
| `/ds-work-parking-lot` | Unchanged from full mode |
| `/ds-work-spike` | Unchanged from full mode |
| `/ds-work-research` | Unchanged from full mode |
| `/ds-work-graduate` | Promote this project to full mode |

Commands that target full-mode-only artifacts (`/ds-work-vision`, `/ds-work-roadmap`, `/ds-work-plan`, `/ds-work-one-pager`, `/ds-work-elevator-pitch`, `/ds-work-challenge`) detect lite mode and offer to run `/ds-work-graduate` first. They do not create vision/roadmap/PRDs in a lite project.

---

## Typical Session Flow

```
Start session
  → /ds-work-continue           (reads milestones.md, surfaces next task)

Do the work
  → edit files, run commands
  → /ds-work-update             (optional mid-session checkpoint)

End session
  → /ds-work-halt               (ticks milestones.md, writes report, commits, pushes)
```

---

## When to Graduate

Run `/ds-work-graduate` when:
- The project picks up a real product story (problem framing, customer, north star) worth writing down
- A milestone has enough internal design decisions to need its own spec (PRD) and execution plan (PLAN) — flat checklists in `milestones.md` stop being enough
- You want adversarial reviews of planning artifacts (`/ds-work-challenge`)
- You need external-facing summaries (`/ds-work-one-pager`, `/ds-work-elevator-pitch`)

Graduation is one-way and additive — existing milestones and their checkboxes are preserved as-is; new full-mode artifacts (vision.md, roadmap.md, now.md, design/) are added alongside.

---

*This is the lite-variant principles doc. The canonical copy is distributed by `/ds-work-scaffold` when invoked with `--lite`. To update for all future lite projects, edit `~/.claude/commands/ds-work-scaffold.md`.*
```

---

### 11. Create empty placeholder files for the empty subdirectories

**Full mode** — create `.gitkeep` files in:
- `product/design/.gitkeep`
- `product/reviews/.gitkeep`
- `product/market-research/.gitkeep`
- `product/research/.gitkeep`
- `product/research/spikes/.gitkeep`
- `product/operations/.gitkeep`
- `product/learnings/.gitkeep`
- `product/reports/.gitkeep`

**Lite mode** — create `.gitkeep` files only in:
- `product/research/.gitkeep`
- `product/research/spikes/.gitkeep`
- `product/operations/.gitkeep`
- `product/learnings/.gitkeep`
- `product/reports/.gitkeep`

### 12. Report back

List every file created (or skipped because it already existed).

**Full mode** — next-steps message:

```
Project scaffolded (full mode). Next steps:
1. Edit product/vision.md — fill in problem, customer, north star
2. Edit product/roadmap.md — fill in your architecture and milestone plan
3. Edit product/milestones.md — replace placeholder tasks with real ones
4. Edit product/now.md — set your first milestone and next action
5. Edit product/how-to/ds-work-halt.md — add your project's shutdown steps (or delete if none)
6. Run /ds-work-plan to write M1's PRD and PLAN
7. Run /ds-work-continue when you're ready to start the first session
```

**Lite mode** — next-steps message:

```
Project scaffolded (lite mode). Next steps:
1. Edit product/milestones.md — replace placeholder milestones + tasks with real ones (mark the first one 🔄 in progress)
2. Edit product/how-to/ds-work-halt.md — add your project's shutdown steps (or delete if none)
3. Run /ds-work-continue when you're ready to start the first session

When the project outgrows a flat task list (real design decisions to capture, external stakeholders, vision worth writing down), run /ds-work-graduate to promote to full mode.
```
