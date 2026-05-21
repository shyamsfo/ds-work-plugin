# ds-work

A Claude Code plugin that adds 15 lightweight project-management slash commands — vision, roadmap, milestones, session lifecycle, parking lot, and review — for managing software projects end-to-end.

All commands are prefixed with `/ds-work-`.

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
- `/ds-work-scaffold [description]` — bootstrap `product/` directory with placeholder docs
- `/ds-work-vision [dir]` — interactively create/review `vision.md`
- `/ds-work-roadmap [dir]` — interactively create/review `roadmap.md`

### Milestone planning
- `/ds-work-plan [dir]` — write/refresh `M{N}-PRD.md` and `M{N}-PLAN.md` for the active milestone

### Session lifecycle
- `/ds-work-continue [dir]` — start of session: resume brief + next PLAN item
- `/ds-work-status [dir]` — mid-session: what to do next
- `/ds-work-update [dir]` — mid-session checkpoint snapshot
- `/ds-work-halt [dir]` — end of session: update docs, commit, push, shut down infra
- `/ds-work-parking-lot [add <text> | pick | <tag>]` — manage unscheduled work items

### Review
- `/ds-work-challenge <target> [milestone-id]` — adversarial pressure-test of a planning artifact

### Exploration
- `/ds-work-research [dir]` — structured research documents (market, tech deep-dives)
- `/ds-work-spike [dir]` — time-boxed technical spikes

### Communication
- `/ds-work-one-pager [dir]` — generate `one-pager.md`
- `/ds-work-elevator-pitch [dir]` — generate pitch variants

### Reference
- `/ds-work-user-guide` — full inline user guide

## The document stack

Every project using this system has the same shape under `product/`:

```
product/
├── vision.md                 ← why + who
├── roadmap.md                ← what + how
├── milestones.md             ← progress tracker
├── now.md                    ← session pointer
├── parking-lot.md            ← unscheduled work
├── design/                   ← M{N}-PRD.md + M{N}-PLAN.md per milestone
├── reviews/                  ← challenge reports
├── research/                 ← deep-dives + spikes/
├── reports/                  ← session history
└── how-to/                   ← project-specific extensions
```

## Customizing commands per project

The commands ship as a general-purpose template. Each project can extend specific commands with steps that only make sense for that project — without forking the plugin.

This works through **how-to extension files** that live inside each project at `product/how-to/`. When a command runs, it checks for its corresponding extension file and folds those project-specific steps into its behavior.

**Concrete example.** A project running a Kubernetes cluster and a Postgres database in the cloud burns money while idle. You can extend `/ds-work-halt` to shut that infra down at the end of every session by adding:

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

**Starting a new project**
```
/ds-work-scaffold [description]
/ds-work-vision
/ds-work-roadmap
# edit product/how-to/ds-work-halt.md — add your infra shutdown steps
/ds-work-plan          # writes M1-PRD.md and M1-PLAN.md
/ds-work-continue      # start first session
```

**Daily session**
```
/ds-work-continue      # resume brief
# ... do the work ...
/ds-work-halt          # update docs, commit, push, run project-specific shutdown
```

## License

MIT — see [LICENSE](./LICENSE).
