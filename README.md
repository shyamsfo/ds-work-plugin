# ds-work

A Claude Code plugin that adds 15 lightweight project-management slash commands — vision, roadmap, milestones, session lifecycle, parking lot, and review — for managing software projects end-to-end.

All commands are prefixed with `/ds-work-`.

## Install

In Claude Code:

```
/plugin marketplace add shyamsfo/ds-work-plugin
/plugin install ds-work@ds-work-plugin
```

That's it. The slash commands become available immediately.

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

Run `/ds-work-user-guide` after installing for the full picture.

## Typical workflows

**Starting a new project**
```
/ds-work-scaffold [description]
/ds-work-vision
/ds-work-roadmap
/ds-work-plan          # writes M1-PRD.md and M1-PLAN.md
/ds-work-continue      # start first session
```

**Daily session**
```
/ds-work-continue      # resume brief
# ... do the work ...
/ds-work-halt          # update docs, commit, push
```

## License

MIT — see [LICENSE](./LICENSE).
