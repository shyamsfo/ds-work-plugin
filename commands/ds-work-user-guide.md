---
name: ds-work-user-guide
description: Display the full user guide for the ds-work-* command system — what each command does, when to use it, and how the document stack fits together.
---

Display the following user guide exactly as written. Do not summarize it or truncate it.

After displaying it, ask: **"Would you like me to save a copy to `product/ds-work-user-guide.md` so you can view it in your IDE?"** If yes, write the guide content (everything below the `---` separator, starting with `# ds-work User Guide`) to `product/ds-work-user-guide.md`. If the user provides a different path, use that instead. Confirm the file was written and note they can open it in their editor.

---

# ds-work User Guide

The `ds-work-*` commands are a set of Claude Code slash commands for managing software projects end-to-end — from initial setup through daily session flow, periodic review, and external communication. They work together as a system.

---

## The Command Map

### Setup commands
Run these when starting a new project or filling in empty scaffolded docs.

| Command | What it does |
|---------|-------------|
| `/ds-work-scaffold [description]` | Bootstrap a new project — creates the full `product/` directory, placeholder docs, `how-to/` extension templates, and `project-management-principles.md`. Run from the project root. |
| `/ds-work-vision [dir]` | Interactively create `vision.md` from scratch (if placeholder/missing) or review and update an existing one against current project state. |
| `/ds-work-roadmap [dir]` | Interactively create `roadmap.md` from scratch (if placeholder/missing) or review and update an existing one against what milestones have proven. |

### Milestone planning commands
Run when starting a new milestone, or when an in-progress milestone's plan needs revisiting.

| Command | What it does |
|---------|-------------|
| `/ds-work-plan [dir]` | Interactively plan the active milestone — produces `M{N}-PRD.md` (spec) and `M{N}-PLAN.md` (sub-task checklist) in `product/design/`. State-driven and re-runnable: walks PRD then PLAN if neither exists, fills in whichever is missing, or runs a drift review if both exist. |

A milestone moves through five **sub-states**, inferred from file presence + checkbox progress:

| Sub-state | Means | Next action |
|---|---|---|
| `defined` | In `milestones.md` only — no PRD/PLAN yet | `/ds-work-plan` to write them |
| `drafting` | PRD exists, PLAN does not | `/ds-work-plan` to write the PLAN |
| `planned` | Both exist, no PLAN boxes ticked | `/ds-work-continue` to start executing |
| `executing` | Some PLAN boxes ticked | `/ds-work-continue` to resume at the next unchecked item |
| `complete` | All PLAN boxes ticked | `/ds-work-plan` to close out and flip the milestone tick in `milestones.md` |

`/ds-work-continue` and `/ds-work-status` are sub-state aware — they read the active milestone's PRD/PLAN and surface the next unchecked item.

### Session lifecycle commands
Run these every session, in order.

| Command | When | What it does |
|---------|------|-------------|
| `/ds-work-continue [dir]` | Start of session | Reads `now.md`, `milestones.md`, the active milestone's PRD/PLAN, and the latest session report. Reports the milestone's sub-state and the next unchecked PLAN item. |
| `/ds-work-status [dir]` | After finishing a task | Shorter than continue. Reports the sub-state, the next unchecked PLAN item, prerequisite checks, and the concrete first action. |
| `/ds-work-update [dir]` | Mid-session checkpoint | Appends a timestamped snapshot to today's session report. Does not commit. Use before stepping away or after a significant task. |
| `/ds-work-halt [dir]` | End of every session | Full shutdown: updates `milestones.md` + `now.md`, writes the session report, commits and pushes, then runs any project-specific teardown from `how-to/ds-work-halt.md`. Also prompts to park anything that came up. |
| `/ds-work-backlog [add <text> \| sweep]` | Anytime — when a half-formed thought arrives that you might want to revisit but don't want to commit to. Or when the backlog has grown enough to need a sweep | Default: shows the items (no ranking, no suggestions — backlog is intentionally flat). `add <text>` appends to the bottom (date optional). `sweep` walks items one at a time and for each asks: promote to parking-lot, fold into a PRD's Out-of-scope, delete silently, or skip. |
| `/ds-work-parking-lot [add <text> \| pick \| <tag>]` | Anytime — when an idea/bug/chore comes up that isn't in the current milestone, or when you want to pull something off the lot | Default: shows the grouped list and suggests 1–3 picks. `add <text>` appends a new item. `pick` skips the list and only shows suggestions. A tag (`bug`, `chore`, `idea`, `research`) filters. Selected items can be tackled now, promoted into the active `M{N}-PLAN.md`, or dropped with a reason. |

**Capture ladder.** Three surfaces, three commitment levels: `backlog.md` (mind-dump, no priority) → `parking-lot.md` (worth doing eventually, has tags) → `design/M{N}-PLAN.md` (scheduled). Items graduate upward via `/ds-work-backlog sweep` and `/ds-work-parking-lot promote`. They don't have to.

### Review commands
Run these at milestone boundaries, when strategy needs a sanity check, or when you want to pressure-test a planning artifact before committing to it.

| Command | What it does |
|---------|-------------|
| `/ds-work-vision [dir]` | Drift review (when vision.md is filled): checks each section for drift against recent milestone completions and session reports. Proposes targeted edits. Appends Decision Log rows. |
| `/ds-work-roadmap [dir]` | Drift review (when roadmap.md is filled): checks milestone state, architecture currency, resolved open questions, alignment with vision. Proposes targeted edits. |
| `/ds-work-plan [dir]` | Drift review (when both PRD + PLAN exist for the active milestone): proposes PRD updates if scope shifted, PLAN edits if work surfaced new tasks. |
| `/ds-work-challenge <target> [milestone-id]` | Adversarial review — pressure-tests a planning artifact for *soundness*, not drift. Target ∈ {`vision`, `roadmap`, `milestone-prd`, `milestone-plan`}. Writes a Challenge Report to `product/reviews/` without modifying the source doc. |

**Drift review vs. adversarial review.** Drift review asks "does this still match reality?" — built into the create-or-review skills (vision/roadmap/plan). Adversarial review asks "is this *right* in the first place?" — the dedicated job of `/ds-work-challenge`. Use challenge sparingly; it's high-signal but becomes noise if invoked reflexively.

### Exploration commands
Run these for investigations that don't fit neatly into a milestone — research that informs a decision, or a technical question that needs a time-boxed answer before work can continue.

| Command | What it does |
|---------|-------------|
| `/ds-work-research [dir]` | Create a structured research document for market research, competitive analysis, technology evaluation, or domain deep-dives. Guides you through framing the question, scoping the investigation, and writing findings + recommendation. Shows open research at the start. Writes to `product/research/` or `product/market-research/` depending on type. |
| `/ds-work-spike [dir]` | Run a time-boxed technical spike — a focused investigation with a single yes/no/A-vs-B question and a hard timebox. Shows open spikes at the start so nothing gets forgotten. Writes to `product/research/spikes/`. Connects the outcome back to milestones or roadmap when concluded. |

### Communication commands
Run these when you need to explain the project to someone outside.

| Command | What it does |
|---------|-------------|
| `/ds-work-one-pager [dir]` | Creates or updates `product/one-pager.md` — a ~500-word single-page project summary (problem, what we're building, how it works, where we are, what's next, key numbers). Iterates with you until you approve, then saves. |
| `/ds-work-elevator-pitch [dir]` | Generates 3 pitch variants (tweet, 30-second verbal, 60-second investor) from `vision.md` and `one-pager.md`. Iterates on tone, audience, and concreteness. Saves to `product/elevator-pitch.md` when approved. |

### Reference
| Command | What it does |
|---------|-------------|
| `/ds-work-user-guide` | This document. |

---

## The Document Stack

Every project using this system has the same structure under `product/`:

```
product/
├── vision.md                    ← why + who (problem, customer, north star)
├── roadmap.md                   ← what + how (architecture, milestones, key decisions)
├── milestones.md                ← progress tracker (story checkboxes, milestone status)
├── now.md                       ← session pointer (current focus, what was just done)
├── backlog.md                   ← low-commitment mind-dump (no priority, no tags)
├── parking-lot.md               ← unscheduled work items (bugs, chores, ideas captured for later)
├── project-management-principles.md   ← how this system works (per-project copy)
├── one-pager.md                 ← external-facing summary (generated by /ds-work-one-pager)
├── elevator-pitch.md            ← pitch variants (generated by /ds-work-elevator-pitch)
├── design/                      ← PRD + PLAN per milestone (M1-PRD.md, M1-PLAN.md, etc., written by ds-work-plan)
├── reviews/                     ← challenge reports (written by ds-work-challenge)
├── market-research/             ← competitive analysis, user research
├── research/                    ← technology deep-dives and domain research (ds-work-research)
│   └── spikes/                  ← time-boxed feasibility investigations (ds-work-spike)
├── operations/                  ← cluster runbooks, infra how-tos
├── learnings/                   ← post-hoc reports, incident retrospectives
├── reports/                     ← session history (YYYY-MM-DD.md, written by update/halt)
└── how-to/                      ← project-specific skill extensions (see below)
    ├── ds-work-challenge.md
    ├── ds-work-continue.md
    ├── ds-work-halt.md
    ├── ds-work-plan.md
    ├── ds-work-status.md
    └── ds-work-update.md
```

**One question, one document.** The moment the same content lives in two places, they drift.

| Question | Answer lives in |
|----------|----------------|
| Why are we building this? Who is it for? | `vision.md` |
| What are we building and how? | `roadmap.md` |
| Which milestone am I in? Which stories are done? | `milestones.md` |
| What am I working on right now? | `now.md` |
| What half-formed thought do I want to remember? | `backlog.md` |
| What's parked for later (not yet scheduled)? | `parking-lot.md` |
| What exactly does milestone N require? | `design/M{N}-PRD.md` |
| What's the execution checklist for milestone N? | `design/M{N}-PLAN.md` |
| Which concerns were raised against a planning artifact? | `reviews/<target>-challenge-YYYY-MM-DD.md` |
| What happened last Tuesday? | `reports/YYYY-MM-DD.md` |
| How do I explain this project in one page? | `one-pager.md` |

---

## Change Tracking

Different documents track changes differently:

| Document | Tracking mechanism | Why |
|---|---|---|
| `vision.md` | Decision Log at the bottom (append-only) | Changes are rare and high-signal — losing the old direction costs context |
| `roadmap.md` | Decision Log + resolved Open Questions move to Key Decisions | Architecture pivots need "why we ended up here", not silent rewrites |
| `milestones.md` | None — git history + session reports are the trail | Changes too frequently; a log would duplicate reports |
| `backlog.md` | None — items disappear (deleted), graduate (promoted), or fold (into PRD). No archive. | Backlog is intentionally lossy; if you need a record, promote to parking-lot first then drop there |
| `parking-lot.md` | Items move from `Open` → `Done / Dropped (last 10)` tail; older items drop off silently | The session reports are the long-term record — the parking lot is a working surface, not an archive |
| PRDs in `design/` | Decision Log at the bottom | Milestone-level design decisions are what future-you will question |
| PLANs in `design/` | None — checkboxes are the change record | Ticked items are the trail of what got done; a log would duplicate them |
| Reports in `reviews/` | None — each report is timestamped and append-only as a directory | A challenge report is a snapshot in time; folding accepted points goes into the source doc's Decision Log |

The Decision Log format used in `vision.md` and `roadmap.md`:
```markdown
## Decision Log

| Date | Section | Change | Rationale |
|------|---------|--------|-----------|
| YYYY-MM-DD | <section name> | <what changed — brief> | <why> |
```

---

## How Project-Specific Extensions Work

Each session skill can be extended with a project-specific file in `product/how-to/`:

| File | Extends | Common use |
|------|---------|------------|
| `how-to/ds-work-continue.md` | `/ds-work-continue` | Extra context sources to read at session start (e.g. a CURRENT_STATE.md, cluster status) |
| `how-to/ds-work-halt.md` | `/ds-work-halt` | Infrastructure shutdown steps (scale down GPU nodes, hibernate cluster, etc.) |
| `how-to/ds-work-update.md` | `/ds-work-update` | Extra state to capture in mid-session snapshots |
| `how-to/ds-work-status.md` | `/ds-work-status` | Infrastructure prerequisite checks before suggesting the next task |
| `how-to/ds-work-plan.md` | `/ds-work-plan` | Project-specific verification commands, file conventions, sub-task templates to fold into PLAN items |
| `how-to/ds-work-challenge.md` | `/ds-work-challenge` | Project-specific red-team angles or lenses (e.g. "always question latency assumptions", "always check k8s quota") |

**How-to files contain only the project-specific additions.** The generic steps live in the global skills. If nothing is project-specific, delete the file.

---

## Typical Workflows

### Starting a new project
```
/ds-work-scaffold [description]   # create product/ structure
/ds-work-vision                   # fill in vision.md interactively
/ds-work-roadmap                  # fill in roadmap.md interactively
# edit product/how-to/ds-work-halt.md — add your infra shutdown steps
# edit milestones.md — replace placeholder tasks with real ones
/ds-work-plan                     # plan M1 — writes M1-PRD.md and M1-PLAN.md
/ds-work-continue                 # start first session
```

### Starting a new milestone
```
/ds-work-plan         # walks PRD, then PLAN, for the active milestone
/ds-work-challenge milestone-prd   # optional: pressure-test the PRD before executing
/ds-work-continue     # start executing at the first PLAN item
```

### Daily session
```
/ds-work-continue     # read resume brief, see sub-state and next PLAN item
# ... do the work ...
/ds-work-update       # optional mid-session checkpoint
# ... more work ...
/ds-work-halt         # update docs, commit, push, shut down infra
```

### After finishing a task mid-session
```
/ds-work-status       # report sub-state, find the next unchecked PLAN item
```

### Closing out a milestone
```
# Once all PLAN boxes are ticked:
/ds-work-plan         # confirms complete, prompts to flip the green tick in milestones.md
```

### End of milestone — check if strategy still holds
```
/ds-work-vision       # drift review: did this milestone change our understanding?
/ds-work-roadmap      # drift review: is the plan still right? resolve open questions
/ds-work-challenge roadmap   # optional: pressure-test the roadmap if a pivot is on the table
```

### Before a meeting or investor conversation
```
/ds-work-one-pager      # generate or refresh the one-pager
/ds-work-elevator-pitch # generate or refine the pitch
```

---

## Where the Skills Live

All `ds-work-*` commands are global — they work in any project. They live in:
```
~/.claude/commands/ds-work-*.md
```

Each skill file is the complete prompt for that command. To modify a skill's behavior, edit the file there. Changes take effect immediately in the next invocation.

Project-specific additions go in `product/how-to/` inside each project — not in the global skill files.

---

## All Arguments Default to `product/`

Every command that takes a directory argument (`[dir]`) defaults to `product/` if no argument is given. Pass a different path when your project uses a non-standard layout:
```
/ds-work-status               # uses product/
/ds-work-status src/product   # uses src/product/
```

---

*The per-project copy of operational conventions lives in `product/project-management-principles.md`. That document covers how milestones, PRDs, and commits work within a project. This user guide covers the tooling itself.*
