---
name: ds-work-continue
description: Resume work on a project by reading now.md, milestones.md, and roadmap.md. Default directory is product/. Pass a different path as argument if needed.
---

You are helping the user resume work on a software project after a break or context reset. Work through the following steps in order.

## 0. Detect the project mode

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

Read `<dir>/ds-work-mode.txt` if it exists. Its single line is either `lite` or `full`. If the file is missing, default to `full` (backward compatible with existing projects).

- **lite mode** → follow the **lite path** described inline below for each step. Skip anything that references `now.md`, `roadmap.md`, PRD/PLAN files, or sub-state.
- **full mode** → follow the steps as written.

## 1. Locate the product docs

Look for these files in the target directory:
- `now.md` — current focus, next action, what was recently completed *(full mode only — skip in lite)*
- `milestones.md` — task checklist across all milestones *(both modes)*
- `roadmap.md` — architecture and design decisions, skim only *(full mode only — skip in lite)*

If none of these files exist in the given directory, search the repo for files with these names and ask the user to confirm which project they want to resume.

## 2. Gather supporting context

- If `<dir>/how-to/ds-work-continue.md` exists, read it now and follow any project-specific context-gathering steps listed there (e.g. reading a cluster state file, checking additional infra docs) before proceeding.
- Run `git log --oneline -8` to see recent commits
- Run `git status` to surface any uncommitted or untracked changes
- If `<dir>/parking-lot.md` exists, count items under `## Open` (used in the resume brief if > 0)

## 3. Find the active task

In `milestones.md`:
- Find the first milestone marked `🔄 in progress`
- Within that milestone, find the first task marked `[~]` (in progress) or `[ ]` (not yet done)
- That is the task to resume

## 4. Load milestone planning state

**Lite mode**: there is no PRD/PLAN. The "next item" is the unchecked task identified in Step 3. Skip the rest of this step and jump to Step 5.

**Full mode**: from the active milestone in `milestones.md`, identify its ID (e.g. `M2`). Then look for its planning artifacts in `<dir>/design/`:

- `M{N}-PRD.md` — milestone spec
- `M{N}-PLAN.md` — sub-task checklist

Classify the milestone's **sub-state** from file existence + checkbox progress:

| PRD | PLAN | Boxes | Sub-state |
|---|---|---|---|
| missing | missing | — | `defined` |
| exists | missing | — | `drafting` |
| exists | exists | none ticked | `planned` |
| exists | exists | some ticked | `executing` |
| exists | exists | all ticked | `complete` |

Then load the appropriate working context:

- **`defined` / `drafting`**: the next action is to run `/ds-work-plan` to fill in the missing artifact. No checkbox to resume.
- **`planned`**: read the PLAN; the first item is the next step. PRD provides scope context.
- **`executing`**: read the PLAN; the **first unchecked item** is where to resume. Read the PRD section relevant to that item for scope/validation criteria.
- **`complete`**: all items ticked — the next action is to run `/ds-work-plan` to confirm closure and flip the milestone tick in `milestones.md`.

**Backward compatibility:** If `now.md` has a `Design doc:` field pointing elsewhere, prefer that path. Otherwise default to `<dir>/design/M{N}-PRD.md` based on the active milestone.

## 5. Present the resume brief

**Full mode** — output a short, scannable summary (aim for something the user can read in 30 seconds):

```
## Resume Brief

**Milestone**: <milestone name and status>
**Sub-state**: `<defined | drafting | planned | executing | complete>` (<X> of <Y> plan items done)
**Next item**: <first unchecked PLAN item if executing/planned, else action implied by sub-state>
**Scope context**: <1–2 sentence summary from the PRD section relevant to the next item, when applicable>

**Recently completed**:
- <2–3 bullet points from now.md "What Was Just Done">

**Infrastructure state**: <up / hibernated / unknown — from how-to/ds-work-continue.md context if available>
**Uncommitted changes**: <none / list files if any>
**Recent commits**: <last 3–4 one-liners from git log>

<!-- Include the next line ONLY if parking-lot.md has open items. Omit it entirely otherwise. -->
**Parking lot**: <N> open — run `/ds-work-parking-lot` to review.
```

**Lite mode** — shorter brief, no sub-state, no scope context, no now.md:

```
## Resume Brief (lite)

**Milestone**: <milestone name and status> (<X/Y tasks done>)
**Next task**: <first [~] or [ ] task verbatim>

**Recently completed**: <2–3 bullets — derive from the most recent file in <dir>/reports/ if available, else from the most recent commits>

**Infrastructure state**: <up / hibernated / unknown — from how-to/ds-work-continue.md context if available>
**Uncommitted changes**: <none / list files if any>
**Recent commits**: <last 3–4 one-liners from git log>

<!-- Include the next line ONLY if parking-lot.md has open items. Omit it entirely otherwise. -->
**Parking lot**: <N> open — run `/ds-work-parking-lot` to review.
```

## 6. Propose the next action

**Full mode** — state clearly what to do next based on the sub-state:

- `defined` / `drafting` → run `/ds-work-plan` to write/finish the PRD and PLAN.
- `planned` / `executing` → quote the next unchecked PLAN item verbatim (with its file path and verification step) and ask the user to confirm before diving in.
- `complete` → run `/ds-work-plan` to close out and update `milestones.md`.

**Lite mode** — quote the next unchecked task verbatim and ask the user to confirm before starting. If all tasks in the active milestone are ticked, say so and ask whether to flip the milestone to `✅ done` and start the next one.

Interactive, not auto-resume: ask the user to confirm before starting work, or whether something has changed since the docs were last updated.

## 7. Offer to update now.md / milestones.md

**Full mode** — if the user confirms the current state is accurate, offer to update `now.md` with today's date and any corrections. If the state has changed (e.g. a task was completed offline), update `milestones.md` checkboxes and `now.md` before proceeding.

**Lite mode** — there is no `now.md`. If the state has drifted (a task was completed offline, the active milestone shifted), offer to update `milestones.md` checkboxes before proceeding.
