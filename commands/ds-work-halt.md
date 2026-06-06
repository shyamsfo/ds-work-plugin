---
name: ds-work-halt
description: Wrap up the current work session cleanly. Updates docs, writes a session report, commits and pushes. Reads product/how-to/ds-work-halt.md for project-specific shutdown steps (infrastructure hibernation, etc.).
---

Wrap up the current work session cleanly.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

## 0. Detect the project mode

Read `<dir>/ds-work-mode.txt` if it exists. Its single line is either `lite` or `full`. Missing → default to `full` (backward compatible).

Branch the steps below by mode. The short version: lite mode skips PRD/PLAN ticking and `now.md` updates entirely; everything else (parking-lot sweep, milestones.md ticking, session report, commit, push, project-specific shutdown) runs the same way.

Work through these steps in order:

## 1. Gather current state

**Full mode** — read all of:
- `<dir>/now.md` — what was just done, what's next
- `<dir>/milestones.md` — task checklist
- `<dir>/design/M{N}-PRD.md` and `<dir>/design/M{N}-PLAN.md` for the active milestone (if they exist)
- `git log --oneline -10` — recent commits
- `git status` — uncommitted changes

Note the active milestone's **sub-state** (`defined` / `drafting` / `planned` / `executing` / `complete`) inferred from PRD/PLAN file presence and PLAN checkbox progress. This will be reported in the session summary.

**Lite mode** — read only:
- `<dir>/milestones.md` — task checklist (this *is* the plan in lite mode)
- `git log --oneline -10` — recent commits
- `git status` — uncommitted changes

There is no sub-state in lite mode. Just note how many tasks in the active milestone are now ticked vs total.

## 2. Assess checkpoint readiness

A good checkpoint is one where:
- No task is mid-execution (no benchmark half-run, no deploy half-applied)
- All completed work is reflected in docs and committed, OR you are about to do that now
- The next person (or future session) can resume cleanly from now.md

If NOT at a good checkpoint, warn the user: explain what's in-flight, and ask if they want to wait, skip, or force-halt anyway.

## 2a. Sweep for parking-lot items

Ask the user a single, skippable question: *"Anything that came up this session worth parking? (one line per item, or skip)"*

If the user lists items, for each line append it to `<dir>/parking-lot.md` under `## Open` (newest at top) following the format used by `/ds-work-parking-lot add`. If `parking-lot.md` does not exist, create it from the template (see `/ds-work-parking-lot`).

Also: if the user reports that a parking-lot item was completed or dropped this session, update `<dir>/parking-lot.md` accordingly (move from `## Open` to `## Done / Dropped (last 10)`, trim tail to 10).

If the user skips, move on silently.

## 3. Update milestones.md and the active PLAN

Go through the active milestone task list in `milestones.md`. For each task completed this session, change `[ ]` to `[x]`. For any task in progress but not done, use `[~]`. Be conservative — only check off tasks that are verifiably complete.

**Full mode**: if the active milestone has a PLAN file (`<dir>/design/M{N}-PLAN.md`), also walk the unchecked items there. For each one finished this session, tick it. Ask the user to confirm before ticking — do not silently mark items based on guesswork. The PLAN's checkboxes are the canonical record of what got done within the milestone. Do not modify the PRD.

**Lite mode**: there is no PLAN file — `milestones.md` is the only checklist to update. Skip the PLAN/PRD steps.

If all tasks in the active milestone are now ticked, ask the user whether to flip the milestone status to `✅ done` and mark the next pending milestone `🔄 in progress`.

## 4. Update now.md

**Lite mode**: there is no `now.md`. Skip this step entirely.

**Full mode**:
- Rewrite "What Was Just Done" with 3–5 bullets from this session
- Update "Next action" to the next unchecked PLAN item if a PLAN exists, otherwise the first unchecked task in milestones.md
- If a PLAN exists, surface the milestone's sub-state and progress (e.g. "M2 `executing`, 4 of 7 PLAN items done")
- Update any infra state section if infrastructure changed
- Update the "Recent Commits" block

## 5. Write session report

Create or append to `<dir>/reports/YYYY-MM-DD.md` (today's date).

If the file already exists, append a new section. **Full mode** uses the sub-state line; **lite mode** uses the simpler progress line instead.
```
## Session End — HH:MM UTC

**Duration**: <start to now if known, otherwise omit>
**Active milestone**: M{N} — sub-state `<defined | drafting | planned | executing | complete>` (<X> of <Y> PLAN items done, when applicable)   <!-- full mode -->
**Active milestone**: M{N} (<X> of <Y> tasks done)                                                                                              <!-- lite mode — use this line instead -->
**Git commits this session**: <list new commits>

### Completed
- <bullet list of what was accomplished>

### Key numbers (if any benchmarks or significant runs happened)
| Metric | Value |
|--------|-------|

### Bugs / Surprises
- <anything unexpected>

### Left in progress
- <anything started but not finished>

### Parked
<!-- Include this section ONLY if any items were added/completed/dropped in parking-lot.md this session. Omit otherwise. -->
- <item title> — <added | completed | dropped> *(reason if dropped)*

### Next session starts at
<next unchecked PLAN item, or first unchecked task from milestones.md if no PLAN exists>
```

If the file does not exist, create it with a header first:
```markdown
# Session Report — YYYY-MM-DD
```
Then append the Session End section.

## 6. Commit and push

Stage all modified files:
- `<dir>/now.md` *(full mode only — file doesn't exist in lite mode)*
- `<dir>/milestones.md`
- `<dir>/design/M{N}-PLAN.md` *(full mode only, if PLAN checkboxes were ticked this session)*
- `<dir>/parking-lot.md` (if parking-lot was modified this session)
- `<dir>/reports/YYYY-MM-DD.md`
- Any other modified tracked files surfaced by git status
- New untracked files that are clearly intentional (reports, design docs, config files)

Write a commit message summarising what was accomplished:
```
session: <one-line summary of what was done>

- <bullet 1>
- <bullet 2>
```

Push to origin main.

## 7. Project-specific shutdown

Check if `<dir>/how-to/ds-work-halt.md` exists. If it does, read it and execute all steps described there. These are project-specific teardown steps such as infrastructure hibernation, scaling down services, or other environment cleanup.

If the file does not exist, skip this step silently.

## 8. Report back

Confirm:
- ✅ Docs updated (milestones.md, plus `now.md` and PLAN in full mode if applicable)
- ✅ Session report written to `<dir>/reports/YYYY-MM-DD.md`
- ✅ Committed and pushed (show `git log --oneline -3`)
- Active milestone status at session end
  - **Full mode**: sub-state (e.g. "M2 `executing`, 4 of 7 PLAN items done")
  - **Lite mode**: tasks ticked (e.g. "M2: 4 of 7 tasks done")
- Any project-specific shutdown steps completed (from how-to file, if present)

**Full mode**: if the milestone just transitioned to `complete` (all PLAN boxes ticked), suggest: *"Run `/ds-work-plan` next to confirm closure and flip the green tick in milestones.md."*

**Lite mode**: if the active milestone's tasks are all ticked, suggest: *"All M{N} tasks are done — flip its status to `✅ done` and mark the next milestone `🔄 in progress` in milestones.md."* (Offer to do it now.)
