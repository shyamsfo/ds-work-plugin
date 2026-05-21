# ds-work User Guide

A condensed reference for the `/ds-work-*` slash command system. The authoritative copy is the `/ds-work-user-guide` command itself — run it in Claude Code to get the full version (with extra examples) printed inline and optionally saved into your project.

---

## Mental model

The plugin treats a software project as a set of stable questions, each answered in exactly one document under `product/`. The slash commands read and write those documents in a predictable lifecycle: **setup → plan → work → review**.

> **One question, one document.** The moment the same content lives in two places, they drift.

| Question | Answer lives in |
|----------|----------------|
| Why are we building this? Who is it for? | `vision.md` |
| What are we building and how? | `roadmap.md` |
| Which milestone am I in? Which stories are done? | `milestones.md` |
| What am I working on right now? | `now.md` |
| What's parked for later (not yet scheduled)? | `parking-lot.md` |
| What exactly does milestone N require? | `design/M{N}-PRD.md` |
| What's the execution checklist for milestone N? | `design/M{N}-PLAN.md` |
| Which concerns were raised against a planning artifact? | `reviews/<target>-challenge-YYYY-MM-DD.md` |
| What happened last Tuesday? | `reports/YYYY-MM-DD.md` |
| How do I explain this project in one page? | `one-pager.md` |

---

## The command map

### Setup
Run when starting a project or filling in empty scaffolded docs.

| Command | Purpose |
|---------|---------|
| `/ds-work-scaffold [description]` | Bootstrap `product/` with placeholder docs and `how-to/` extension templates. |
| `/ds-work-vision [dir]` | Interactively write or review `vision.md`. |
| `/ds-work-roadmap [dir]` | Interactively write or review `roadmap.md`. |

### Milestone planning
| Command | Purpose |
|---------|---------|
| `/ds-work-plan [dir]` | State-driven: writes `M{N}-PRD.md` and/or `M{N}-PLAN.md` for the active milestone, or runs a drift review if both exist. |

A milestone moves through five **sub-states**, inferred from file presence + checkbox progress:

| Sub-state | Means | Next action |
|---|---|---|
| `defined` | In `milestones.md` only — no PRD/PLAN yet | `/ds-work-plan` to write them |
| `drafting` | PRD exists, PLAN does not | `/ds-work-plan` to write the PLAN |
| `planned` | Both exist, no PLAN boxes ticked | `/ds-work-continue` to start executing |
| `executing` | Some PLAN boxes ticked | `/ds-work-continue` to resume at the next unchecked item |
| `complete` | All PLAN boxes ticked | `/ds-work-plan` to close out and flip the milestone tick |

`/ds-work-continue` and `/ds-work-status` are sub-state aware.

### Session lifecycle
Run every session, in order.

| Command | When | Purpose |
|---------|------|---------|
| `/ds-work-continue [dir]` | Start of session | Resume brief: active milestone, sub-state, next PLAN item. |
| `/ds-work-status [dir]` | After finishing a task | Shorter than continue — reports the next concrete action. |
| `/ds-work-update [dir]` | Mid-session checkpoint | Appends a timestamped snapshot to today's session report. |
| `/ds-work-halt [dir]` | End of session | Updates `milestones.md` + `now.md`, writes the session report, commits, pushes, runs project-specific teardown. |
| `/ds-work-parking-lot [add <text> \| pick \| <tag>]` | Anytime | Manage unscheduled items. `add <text>` appends. `pick` shows suggestions. A tag (`bug`, `chore`, `idea`, `research`) filters. |

### Review
| Command | Purpose |
|---------|---------|
| `/ds-work-vision [dir]` | Drift review of `vision.md` against recent milestones. |
| `/ds-work-roadmap [dir]` | Drift review of `roadmap.md` against milestone state. |
| `/ds-work-plan [dir]` | Drift review of the active milestone's PRD + PLAN. |
| `/ds-work-challenge <target> [milestone-id]` | Adversarial pressure-test of a planning artifact. Target ∈ `vision`, `roadmap`, `milestone-prd`, `milestone-plan`. Writes a Challenge Report without modifying the source doc. |

> **Drift review vs. adversarial review.** Drift review asks "does this still match reality?" — built into vision/roadmap/plan. Adversarial review asks "is this *right* in the first place?" — the dedicated job of `/ds-work-challenge`. Use challenge sparingly.

### Exploration
| Command | Purpose |
|---------|---------|
| `/ds-work-research [dir]` | Structured market research, competitive analysis, or technology deep-dives. Writes to `product/research/` or `product/market-research/`. |
| `/ds-work-spike [dir]` | Time-boxed technical spike with a single yes/no/A-vs-B question. Writes to `product/research/spikes/`. |

### Communication
| Command | Purpose |
|---------|---------|
| `/ds-work-one-pager [dir]` | Create or refresh `one-pager.md` (~500 words). |
| `/ds-work-elevator-pitch [dir]` | Generate 3 pitch variants (tweet, 30-second verbal, 60-second investor). |

### Reference
| Command | Purpose |
|---------|---------|
| `/ds-work-user-guide` | Print the full guide inline. |

---

## The document stack

```
product/
├── vision.md                    ← why + who
├── roadmap.md                   ← what + how
├── milestones.md                ← progress tracker
├── now.md                       ← session pointer
├── parking-lot.md               ← unscheduled work
├── project-management-principles.md   ← how this system works
├── one-pager.md
├── elevator-pitch.md
├── design/                      ← M{N}-PRD.md + M{N}-PLAN.md per milestone
├── reviews/                     ← challenge reports
├── market-research/
├── research/
│   └── spikes/
├── operations/                  ← cluster runbooks, infra how-tos
├── learnings/                   ← retrospectives
├── reports/                     ← YYYY-MM-DD.md session reports
└── how-to/                      ← project-specific extensions (see below)
```

---

## Customizing commands per project

Each session-style command can be extended for the project it runs in, without forking the plugin. Drop a file under `product/how-to/` with the same name as the command, and the command will read it and fold those steps in.

| File | Extends | Typical contents |
|------|---------|------------------|
| `how-to/ds-work-continue.md` | `/ds-work-continue` | Extra context to read at session start |
| `how-to/ds-work-halt.md`     | `/ds-work-halt`     | Infra shutdown — kubectl scale, gcloud sql stop, docker stop |
| `how-to/ds-work-update.md`   | `/ds-work-update`   | Extra state to capture in checkpoints |
| `how-to/ds-work-status.md`   | `/ds-work-status`   | Prereq checks before the next task |
| `how-to/ds-work-plan.md`     | `/ds-work-plan`     | Project-specific verification commands or PLAN sub-task templates |
| `how-to/ds-work-challenge.md`| `/ds-work-challenge`| Project-specific red-team angles |

How-to files contain **only** the project-specific additions — the generic steps stay in the plugin. If a project doesn't need an extension, just don't create the file. `/ds-work-scaffold` lays down empty placeholders for you.

### Example: shut down cloud infra on halt

```markdown
# product/how-to/ds-work-halt.md

After committing and pushing, also:

1. Scale GPU pool to 0:
   `kubectl scale --replicas=0 deployment/inference -n prod`
2. Stop Postgres:
   `gcloud sql instances patch my-db --activation-policy=NEVER`
3. Stop local docker:
   `docker compose -f infra/local/docker-compose.yml stop`
4. Confirm everything is down, then report.
```

---

## Change tracking

| Document | Tracking | Why |
|----------|----------|-----|
| `vision.md` | Decision Log at bottom (append-only) | Rare, high-signal changes |
| `roadmap.md` | Decision Log + resolved Open Questions → Key Decisions | Architecture pivots need "why we ended up here" |
| `milestones.md` | None — git history + session reports | Changes too often |
| `parking-lot.md` | `Open` → `Done / Dropped (last 10)` tail | A working surface, not an archive |
| PRDs | Decision Log at bottom | Milestone-level decisions future-you will question |
| PLANs | Checkboxes are the change record | A log would duplicate them |
| Challenge reports | Append-only files, timestamped | Snapshots in time |

---

## Typical workflows

**Starting a new project**
```
/ds-work-scaffold [description]
/ds-work-vision
/ds-work-roadmap
# edit product/how-to/ds-work-halt.md — add infra shutdown steps
# edit milestones.md — replace placeholder tasks with real ones
/ds-work-plan
/ds-work-continue
```

**Starting a new milestone**
```
/ds-work-plan
/ds-work-challenge milestone-prd   # optional
/ds-work-continue
```

**Daily session**
```
/ds-work-continue
# ... work ...
/ds-work-update           # optional checkpoint
/ds-work-halt             # commit, push, shut down infra
```

**End of milestone — sanity-check strategy**
```
/ds-work-vision           # drift review
/ds-work-roadmap          # drift review
/ds-work-challenge roadmap   # optional pressure-test
```

**Before an external conversation**
```
/ds-work-one-pager
/ds-work-elevator-pitch
```

---

*This document is a summary. For the authoritative version, run `/ds-work-user-guide` in Claude Code.*
