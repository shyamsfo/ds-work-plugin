---
name: ds-work-plan
description: Interactively plan a milestone — produces both M{N}-PRD.md (spec) and M{N}-PLAN.md (sub-task checklist) in product/design/. State-driven and re-runnable; behaves differently based on what already exists for the active milestone.
---

You are helping the user plan a milestone. Produce two artifacts in `<dir>/design/`:
- `M{N}-PRD.md` — milestone spec (what to build, success criteria, scope)
- `M{N}-PLAN.md` — sub-task checklist (each item: file path(s), specific change, verification step)

The command is state-driven and re-runnable. Branch on what already exists.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Identify the active milestone and assess state

Read these in parallel:
- `<dir>/milestones.md` — to find the active milestone (the one in progress, no green tick)
- `<dir>/now.md` — confirms current focus
- `<dir>/roadmap.md` — context for what the milestone serves
- `<dir>/vision.md` — the why (informs scope decisions)
- The most recent file in `<dir>/reports/` — recent context
- `<dir>/how-to/ds-work-plan.md` if it exists — project-specific planning guidance

Identify the active milestone (call it `M{N}`). If multiple are in progress or none is clearly active, ask the user which milestone to plan.

Then read both:
- `<dir>/design/M{N}-PRD.md` (may not exist)
- `<dir>/design/M{N}-PLAN.md` (may not exist)

Classify the milestone's planning state:

| PRD | PLAN | Boxes | Sub-state |
|---|---|---|---|
| missing | missing | — | `defined` |
| exists | missing | — | `drafting` |
| exists | exists | none ticked | `planned` |
| exists | exists | some ticked | `executing` |
| exists | exists | all ticked | `complete` |

Tell the user: "M{N} is in `<sub-state>`. Here's what `/ds-work-plan` will do next: …" — set expectations before doing work.

---

## Step 2A — `defined`: Create PRD, then PLAN

### PRD phase

Walk the user through PRD sections interactively. One at a time — draft, show, get feedback, move on.

Use this structure for `M{N}-PRD.md`:

```markdown
# M{N} — <Milestone Name>

## Goal
<1–2 sentences: what this milestone delivers and why it matters now>

## Scope
<bullets: what's explicitly in this milestone>

## Out of scope
<bullets: what's deliberately not — guards against creep>

## Success criteria
<bullets: how we know it's done — observable, ideally testable>

## Approach
<2–4 paragraphs or bullets: high-level shape of the implementation, key components affected, any sequencing constraints>

## Open questions
<bullets: what's unresolved — to be answered before/during execution>

## References
<links to relevant research/, spikes/, or prior milestones if applicable>
```

For each section:

1. **Goal** — Ask: *"In one or two sentences, what does M{N} deliver and why does it matter?"* Pull existing context from `milestones.md` (the milestone's existing description) so the user isn't re-explaining.
2. **Scope** — Ask: *"What stories or work items are in this milestone?"* Import any stories already listed under M{N} in `milestones.md`. Confirm with the user before moving on.
3. **Out of scope** — Ask: *"What's tempting but deliberately out of scope for this milestone?"* If user can't think of any, prompt with adjacent areas they might be tempted to expand into.
4. **Success criteria** — Ask: *"How will you know M{N} is done? What can you point to or run?"* Push for observable/testable criteria, not aspirations.
5. **Approach** — Ask: *"At a high level, how do you plan to build this? What components are touched, in what order?"* Keep this at the architectural level — implementation detail belongs in the PLAN.
6. **Open questions** — Ask: *"What's unresolved? Anything blocking, anything you need to decide as you go?"*
7. **References** — Auto-populate from `<dir>/research/`, `<dir>/spikes/`, completed milestones if any are obviously relevant. Confirm with the user.

### Finalize PRD

Show the complete PRD. List 2–3 suggestions for anything that seems vague, missing, or inconsistent with `vision.md` / `roadmap.md`.

Ask: *"Anything to change before I save the PRD?"* Iterate until confirmed.

Write to `<dir>/design/M{N}-PRD.md`. No Decision Log on creation.

### PLAN phase

Tell the user: *"PRD saved. Now I'll walk you through the execution checklist."*

Use this structure for `M{N}-PLAN.md`:

```markdown
# M{N} — Plan

> Companion to `M{N}-PRD.md`. Sub-task checklist for execution. Each item should be completable in roughly one session.

## <Story or work area 1>

- [ ] <task> — `<file path(s)>` — <specific change> — verify: <test command, manual check, etc.>
- [ ] <task> — …

## <Story or work area 2>

- [ ] …

---

## Notes
<optional: sequencing constraints, gotchas surfaced during planning>
```

Walk through each story or work area in the PRD's Scope section. For each:

Ask: *"For `<story>`, what are the concrete sub-tasks? I'll ask about file paths and verification for each."*

For each sub-task elicit:
- **What** — the specific change (one short phrase)
- **Where** — file path(s), or "new file at X"
- **Verify** — how to confirm it works (test command, manual check, build passes, etc.)

If `how-to/ds-work-plan.md` defines project-specific verification commands or file conventions, fold them in by default rather than asking each time.

Don't over-decompose. Aim for sub-tasks that take roughly one focused session each. If a sub-task is too big, ask the user to break it down; if too small, group it.

### Finalize PLAN

Show the complete PLAN. Sanity-check:
- Every Scope item from the PRD has at least one sub-task.
- No sub-task addresses something Out of scope.
- Verification is specified for every item.

Ask: *"Anything to change before I save the plan?"* Iterate until confirmed.

Write to `<dir>/design/M{N}-PLAN.md`. No Decision Log on creation.

---

## Step 2B — `drafting`: Quick PRD check, then PLAN

PRD exists, PLAN does not.

### Quick PRD check

Show the user the existing PRD's Goal, Scope, and Success criteria. Ask: *"Is the PRD still right, or has anything changed since you wrote it?"*

- If yes → proceed to PLAN phase (use Step 2A's PLAN phase).
- If no → run a targeted drift review (Step 2C-style) on just the PRD, apply changes, then proceed to PLAN.

---

## Step 2C — `planned` or `executing`: Drift review

Both files exist; PLAN may have some ticked boxes.

The goal: surface drift between the PRD/PLAN and what has actually happened.

### Load context

Read in parallel:
- `<dir>/design/M{N}-PRD.md`
- `<dir>/design/M{N}-PLAN.md`
- The 2–3 most recent files in `<dir>/reports/`
- `git log --oneline -20` from the project root (if a git repo)

### Check for drift

Work through both documents:

**PRD drift**:
- Has the Goal or Scope shifted based on recent sessions or decisions?
- Have any items moved from Open questions to settled answers? Propose moving them into Approach or a Decision Log row.
- Are Success criteria still accurate, or have they sharpened?

**PLAN drift**:
- Are there ticked items that turned out to be wrong or partial? Should any be re-opened?
- Are there unticked items that are now obsolete?
- Did the work surface new sub-tasks that aren't in the PLAN? Propose adding them.
- Is the ordering still right, given what's been learned?

### Propose specific edits

For each drift detected, propose a targeted change:

```
### [PRD or PLAN] — [Section / area] — suggested update

**Current**: "<exact quote or item>"

**Proposed**: <replacement, addition, or removal>

**Why**: <one sentence — what changed>
```

Aim for 3–6 proposals total. Prioritize: scope shifts > resolved open questions > stale sub-tasks > re-ordering.

### Iterate

Ask: *"Which of these would you like to apply? I can also make other changes or leave anything as-is."*

Apply changes one at a time. Show the updated section after each change.

### Decision Log

If PRD changes are applied, append rows to a Decision Log at the bottom of the PRD:

```markdown
---

## Decision Log

| Date | Area | Change | Rationale |
|------|------|--------|-----------|
| YYYY-MM-DD | <Goal / Scope / Approach / etc.> | <what changed — brief> | <why — what prompted the update> |
```

PLAN changes do not need a Decision Log — the checklist is itself the record. Just write the updated PLAN.

---

## Step 2D — `complete`: Confirm and offer to close out

All checkboxes ticked.

1. Show a summary: "M{N} has all PLAN items ticked. Here's what was completed:" — list the ticked items grouped by story.
2. Cross-check: read `<dir>/milestones.md` — is M{N} marked complete (green tick) there? If not, offer to update it.
3. Suggest next steps:
   - If there's a next milestone in `milestones.md`: *"Want to run `/ds-work-plan` for M{N+1} now, or wrap with `/ds-work-halt`?"*
   - If M{N} was the last planned milestone: suggest `/ds-work-roadmap` to plan beyond.

Do not modify the PRD or PLAN in this state — they are the historical record.

---

## Step 3 — After saving

Tell the user:
- What was written or updated (PRD, PLAN, both)
- The new sub-state of M{N} (drafting / planned / executing / complete)
- Suggest `/ds-work-continue` to begin executing (if just transitioned to `planned`)
- If `milestones.md` was updated (tick flipped), suggest reviewing the roadmap with `/ds-work-roadmap`
