# ds-work

A Claude Code plugin that adds 17 lightweight project-management slash commands — vision, roadmap, milestones, session lifecycle, parking lot, backlog, and review — for managing software projects end-to-end.

All commands are prefixed with `/ds-work-`.

## Two modes: full or lite

`/ds-work-scaffold` asks which mode you want before doing anything:

- **full** — the complete stack: `vision.md`, `roadmap.md`, `now.md`, per-milestone PRDs + PLANs, design reviews, market research. Best when there's a real product story, multiple milestones with design decisions to capture, or external stakeholders.
- **lite** — a stripped-down stack: just `milestones.md` (all milestones in one file with a flat task checklist — *this file is the plan*), `backlog.md`, `parking-lot.md`, and `reports/`. No vision, no roadmap, no now.md, no PRDs. Best for simple projects with a clear execution path.

The chosen mode is recorded in `product/ds-work-mode.txt`. Every other command reads that file and branches its behavior. Commands that target full-only artifacts (`/ds-work-vision`, `/ds-work-roadmap`, `/ds-work-plan`, `/ds-work-one-pager`, `/ds-work-elevator-pitch`, `/ds-work-challenge`) detect lite mode and offer to graduate before doing anything.

Pass `--lite` or `--full` to `/ds-work-scaffold` to skip the prompt. Promote a lite project to full at any time with `/ds-work-graduate` — it's one-way and additive (existing files preserved, full-mode artifacts added alongside).

## Install

In Claude Code:

```
/plugin marketplace add shyamsfo/ds-work-plugin
/plugin install ds-work@ds-work-plugin
```

That's it. The slash commands become available immediately, in every project you open.

## Learn the system

Two ways to read the guide:

- **In Claude Code:** run `/ds-work-user-guide` — prints the full guide inline and optionally saves a copy into your current project.
- **In this repo:** see [USER-GUIDE.md](./USER-GUIDE.md) — a condensed summary of the full guide.

The rest of this README is the short version.

## What you get

### Setup
- `/ds-work-scaffold [--lite|--full] [description]` — bootstrap `product/` directory with placeholder docs (asks which mode if no flag)
- `/ds-work-graduate [dir]` — promote a lite-mode project to full mode (adds vision, roadmap, now.md, design/, reviews/)
- `/ds-work-vision [dir]` — interactively create/review `vision.md` *(full mode only)*
- `/ds-work-roadmap [dir]` — interactively create/review `roadmap.md` *(full mode only)*

### Milestone planning
- `/ds-work-plan [dir]` — write/refresh `M{N}-PRD.md` and `M{N}-PLAN.md` for the active milestone *(full mode only — lite mode tracks tasks directly in `milestones.md`)*

### Session lifecycle
- `/ds-work-continue [dir]` — start of session: resume brief + next PLAN item
- `/ds-work-status [dir]` — mid-session: what to do next
- `/ds-work-update [dir]` — mid-session checkpoint snapshot
- `/ds-work-halt [dir]` — end of session: update docs, commit, push, shut down infra
- `/ds-work-parking-lot [add <text> | pick | <tag>]` — manage unscheduled work items
- `/ds-work-backlog [add <text> | sweep]` — low-commitment mind-dump (one rung below the parking lot)

### Review
- `/ds-work-challenge <target> [milestone-id]` — adversarial pressure-test of a planning artifact *(full mode only)*

### Exploration
- `/ds-work-research [dir]` — structured research documents (market, tech deep-dives)
- `/ds-work-spike [dir]` — time-boxed technical spikes

### Communication
- `/ds-work-one-pager [dir]` — generate `one-pager.md` *(full mode only)*
- `/ds-work-elevator-pitch [dir]` — generate pitch variants *(full mode only)*

### Reference
- `/ds-work-user-guide` — full inline user guide

## The document stack

Every project using this system has a `product/ds-work-mode.txt` marker plus one of two shapes under `product/`:

**Full mode**
```
product/
├── ds-work-mode.txt          ← contains "full"
├── vision.md                 ← why + who
├── roadmap.md                ← what + how
├── milestones.md             ← progress tracker
├── now.md                    ← session pointer
├── parking-lot.md            ← unscheduled work
├── backlog.md                ← low-commitment mind-dump
├── design/                   ← M{N}-PRD.md + M{N}-PLAN.md per milestone
├── reviews/                  ← challenge reports
├── research/                 ← deep-dives + spikes/
├── reports/                  ← session history
└── how-to/                   ← project-specific extensions
```

**Lite mode**
```
product/
├── ds-work-mode.txt          ← contains "lite"
├── milestones.md             ← all milestones in one file — THIS FILE IS THE PLAN
├── parking-lot.md            ← unscheduled work
├── backlog.md                ← low-commitment mind-dump
├── research/                 ← deep-dives + spikes/
├── reports/                  ← session history
└── how-to/                   ← project-specific extensions (fewer files than full mode)
```

## Customizing commands per project

The commands ship as a general-purpose template. Each project can extend specific commands with steps that only make sense for that project — without forking the plugin.

This works through **how-to extension files** that live inside each project at `product/how-to/`. When a command runs, it checks for its corresponding extension file and folds those project-specific steps into its behavior.

**Concrete example.** A project running a Kubernetes cluster and a Postgres database in the cloud burns money while idle. You can extend `/ds-work-halt` to shut that infra down at the end of every session by adding:

> **Pedantic but worth saying:** you don't need to hand-write these files. Just ask Claude. Something like *"customize `/ds-work-halt` for this project — at the end of each session we should scale our GKE node pool to 0 and stop the Cloud SQL instance"* is enough. Claude will create the right file under `product/how-to/` and the next halt will pick it up. Same for any other command you want to customize.

```markdown
# product/how-to/ds-work-halt.md

After committing and pushing, also do the following before reporting halt complete:

1. Scale the GPU node pool to 0:
   `kubectl scale --replicas=0 deployment/inference -n prod`
2. Stop the Postgres instance:
   `gcloud sql instances patch my-db --activation-policy=NEVER`
3. Stop any local docker containers:
   `docker compose -f infra/local/docker-compose.yml stop`
4. Confirm everything is down, then report.
```

Next time you run `/ds-work-halt`, Claude reads that file and runs those steps as part of the shutdown.

The extension files Claude looks for:

| File | Extends | Common use |
|------|---------|------------|
| `how-to/ds-work-continue.md` | `/ds-work-continue` | Extra context to read at session start (cluster state, CURRENT_STATE.md) |
| `how-to/ds-work-halt.md`     | `/ds-work-halt`     | Infra shutdown — scale down nodes, stop databases, stop containers |
| `how-to/ds-work-update.md`   | `/ds-work-update`   | Extra state to capture in mid-session snapshots |
| `how-to/ds-work-status.md`   | `/ds-work-status`   | Prerequisite checks (is the cluster up? is the DB reachable?) before suggesting the next task |
| `how-to/ds-work-plan.md`     | `/ds-work-plan`     | Project-specific verification commands or sub-task templates to fold into PLAN items |
| `how-to/ds-work-challenge.md`| `/ds-work-challenge`| Project-specific red-team lenses (e.g. "always question latency assumptions") |

How-to files contain **only** the project-specific additions — the generic steps stay in the plugin commands. If a project doesn't need an extension, just don't create the file.

`/ds-work-scaffold` writes empty placeholder how-to files for you to fill in.

## Typical workflows

**Starting a new full-mode project**
```
/ds-work-scaffold --full [description]
/ds-work-vision
/ds-work-roadmap
# edit product/how-to/ds-work-halt.md — add your infra shutdown steps
/ds-work-plan          # writes M1-PRD.md and M1-PLAN.md
/ds-work-continue      # start first session
```

**Starting a new lite-mode project**
```
/ds-work-scaffold --lite [description]
# edit product/milestones.md — replace placeholder milestones + tasks
/ds-work-continue      # start first session
# ... later, if the project outgrows a flat task list:
/ds-work-graduate      # promote to full mode (one-way, additive)
```

**Daily session** (same in both modes)
```
/ds-work-continue      # resume brief
# ... do the work ...
/ds-work-halt          # update docs, commit, push, run project-specific shutdown
```

## License

MIT — see [LICENSE](./LICENSE).
