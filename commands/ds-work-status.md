---
name: ds-work-status
description: Give a concise, actionable summary of what to work on next. Reads product/how-to/ds-work-status.md for project-specific context (cluster state, infra prerequisites, etc.).
---

Give a concise, actionable summary of what to work on next.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

## 0. Detect the project mode

Read `<dir>/ds-work-mode.txt` if it exists. Its single line is either `lite` or `full`. Missing → default to `full` (backward compatible).

Branch the steps below by mode.

## Steps

1. Read what's available:
   - **Full mode**: `<dir>/now.md`, `<dir>/milestones.md`, `<dir>/roadmap.md` (skim only), and the most recent file in `<dir>/reports/`.
   - **Lite mode**: `<dir>/milestones.md` and the most recent file in `<dir>/reports/`. There is no `now.md` and no `roadmap.md`.

2. If `<dir>/how-to/ds-work-status.md` exists, read it now and incorporate any project-specific context gathering (e.g. reading a cluster state file, checking infra status) before assembling the output.

3. From milestones.md: find the active milestone (first one marked `🔄 in progress`) and note its ID (e.g. `M2`). Within it, also note the first `[~]` task (in progress) or `[ ]` task (next to start).

4. **Lite mode**: the "next item" is the unchecked task identified in Step 3. Skip the rest of this step and jump to Step 5.

   **Full mode**: look for the milestone's planning artifacts in `<dir>/design/`:
   - `M{N}-PRD.md` — milestone spec
   - `M{N}-PLAN.md` — sub-task checklist

   Classify the **sub-state** from file existence + checkbox progress:

   | PRD | PLAN | Boxes | Sub-state |
   |---|---|---|---|
   | missing | missing | — | `defined` |
   | exists | missing | — | `drafting` |
   | exists | exists | none ticked | `planned` |
   | exists | exists | some ticked | `executing` |
   | exists | exists | all ticked | `complete` |

   The "concrete first action" depends on the sub-state:
   - `defined` / `drafting` → run `/ds-work-plan` to fill in the missing artifact.
   - `planned` → start the first PLAN item (quote it with its file path + verification).
   - `executing` → resume at the first unchecked PLAN item (quote it with its file path + verification).
   - `complete` → run `/ds-work-plan` to close out and flip the milestone tick in `milestones.md`.

   **Backward compatibility:** If `now.md` has a `Design doc:` field pointing elsewhere, prefer that path.

5. Check for blockers or prerequisites:
   - Does the next task need infrastructure running? (note it clearly)
   - Is there an unresolved bug or investigation from the last session report?
   - Is there a "next session starts at" note in the last report?

6. If `<dir>/parking-lot.md` exists, count items under `## Open`. Note the count (only surfaced in output if > 0).

7. Output a short, scannable answer.

**Full mode**:

```
## What's Next

**Milestone**: <milestone name> (<X/Y stories done>)
**Sub-state**: `<defined | drafting | planned | executing | complete>` (<X> of <Y> plan items done)
**Next item**: <first unchecked PLAN item if executing/planned, else action implied by sub-state>

**Why this matters**: <1–2 sentences — what it unblocks or proves, drawn from the PRD section if available>

**Prerequisite check**:
- Infrastructure: <up / hibernated / unknown>
- Any blocker: <yes/no — describe if yes>

**Concrete first action**:
<The exact command to run, file to edit, or `/ds-work-plan` invocation — whichever the sub-state implies.>

**After that**:
<The next 2–3 unchecked PLAN items in sequence, when applicable.>

<!-- Include the next line ONLY if parking-lot.md has open items. Omit it entirely otherwise. -->
**Parking lot**: <N> open — run `/ds-work-parking-lot` to review.
```

**Lite mode** — even tighter, no sub-state, no PRD context:

```
## What's Next (lite)

**Milestone**: <milestone name> (<X/Y tasks done>)
**Next task**: <first [~] or [ ] task verbatim>

**Prerequisite check**:
- Infrastructure: <up / hibernated / unknown>
- Any blocker: <yes/no — describe if yes>

**Concrete first action**:
<The exact command to run or file to edit for the next task.>

**After that**:
<The next 2–3 unchecked tasks in sequence, if any.>

<!-- Include the next line ONLY if parking-lot.md has open items. Omit it entirely otherwise. -->
**Parking lot**: <N> open — run `/ds-work-parking-lot` to review.
```

Keep it tight. No need to summarise every milestone — just tell the user what to do right now and why.
