---
name: ds-work-roadmap
description: Interactively create or review product/roadmap.md. Creates it from scratch if missing (e.g. after scaffold), or reviews it against current milestone state and proposes updates.
---

You are helping the user create or review the roadmap document. Branch on whether the document exists and is filled in.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Assess what exists

Read `<dir>/roadmap.md`.

Classify its state:
- **Missing**: file does not exist
- **Placeholder**: file exists but contains scaffold boilerplate (`<Describe...>`, `TODO`, `> Replace this placeholder`, empty sections with no real content)
- **Filled**: file has substantive content

Then read these in parallel (they inform both creation and review):
- `<dir>/vision.md` — the why and north star (roadmap must serve this)
- `<dir>/milestones.md` — what's done, in progress, and pending
- `<dir>/now.md` — most recent completed work and current milestone state
- The most recent file in `<dir>/reports/` — last session's findings and decisions

---

## Step 2A — If Missing or Placeholder: Create Mode

Walk the user through each section interactively. One section at a time — draft it, show it, get feedback, then move on.

### Architecture / "What We're Building"
Ask: *"How does this system work? Walk me through the main components and how they fit together."*

Generate a component table or short architecture description from what the user says:
```
Component → responsibility → connects to
```
Show it. Offer to add a simple ASCII diagram if useful. Move on.

### Context: Where We Are Now
Pull from `now.md` and `milestones.md`. Describe:
- What the current system state is (what's running, what's proven)
- What has been completed so far

Show the draft. Let the user correct any inaccuracies. Move on.

### Delivery Phases / Milestones
Ask: *"What are the major milestones? For each, what's the goal and how will you know it's done?"*

For each milestone the user describes, draft:
```markdown
### Milestone N: <Name>
*Goal: <one sentence>*

Stories:
1. <story>
2. <story>

Exit criteria: <how you know it's done>
```

Walk through milestones one at a time. After each, ask if there are more or if the ordering is right.

Import any existing milestones from `milestones.md` directly — don't make the user re-describe ones already tracked there.

### Key Decisions Made
Ask: *"Have you already made any significant architecture or product decisions that should be locked down?"*

For each decision: populate a row in the table:
```markdown
| Decision | Choice | Rationale |
```

If no decisions yet, create an empty table as a placeholder.

### Open Questions
Ask: *"What are the biggest unresolved questions — technical, product, or architectural?"*

List them as bullets. Add a proposal for each if the user has one.

### Finalize
Show the complete roadmap. List 2–3 suggestions for anything missing, vague, or inconsistent with vision.md.

Ask: *"Anything to change before I save?"* Iterate until confirmed.

Write to `<dir>/roadmap.md`. No Decision Log on creation.

---

## Step 2B — If Filled: Review Mode

The goal is to find places where the roadmap has drifted from reality: completed work that changes the plan, new decisions that aren't captured, milestones that should be reordered or split.

### Load context
Read the filled `<dir>/roadmap.md` fully.

### Check for drift — systematic review

Work through each area:

**Architecture section**:
- Does the component list still match the system as built?
- Did any milestone add or remove a component that isn't reflected here?

**"Where We Are Now" section**:
- Is this current? It should describe what's running and proven today.
- Propose an update if it describes a past state.

**Milestones / Delivery Phases**:
- Are all completed milestones marked or described as done?
- Did any milestone get split, merged, or reordered in `milestones.md` but not reflected here?
- Are there milestones in `milestones.md` that aren't in the roadmap at all?
- Did completing a milestone reveal that a future milestone needs to change scope?
- Are any milestones now obsolete (the problem they solve has been superseded)?

**Key Decisions Made**:
- Did recent work make any new significant decisions that should be locked here?
- Did any existing decision get reversed or qualified in a session report or PRD?

**Open Questions**:
- Which open questions have been resolved? Propose moving them to Key Decisions Made (with the answer).
- Have new open questions emerged from recent sessions?

### Propose specific edits

For each area where drift is detected, propose a targeted change:

```
### [Area] — suggested update

**Current**: "<exact quote or section description>"

**Proposed change**: <description of the edit, or replacement text>

**Why**: <one sentence — what changed>
```

Aim for 3–6 proposals. Prioritize: resolved open questions → stale "where we are now" → milestone state → architecture drift.

### Check alignment with vision.md

If `<dir>/vision.md` exists and has been recently updated (check the Decision Log in vision.md), flag any roadmap sections that may need to align:
- Does the north star in vision.md still match the final milestone's goal?
- Do the milestones collectively deliver the vision, or is there a gap?

### Iterate
Ask: *"Which of these would you like to apply? I can also make other changes, reorder milestones, or leave anything as-is."*

Apply changes one at a time. Show the updated section after each change.

For resolved open questions: move the question + its answer into Key Decisions Made, and remove it from Open Questions. Show the user the move before applying.

### Decision Log
If any changes are applied, append rows to the Decision Log at the bottom of the file:

```markdown
---

## Decision Log

| Date | Area | Change | Rationale |
|------|------|--------|-----------|
| YYYY-MM-DD | <Milestones / Architecture / Key Decisions / etc.> | <what changed — brief> | <why — what prompted the update> |
```

If a Decision Log already exists, append new rows only. Do not rewrite existing rows.

Write the file when the user confirms.

---

## Step 3 — After saving

Tell the user:
- What was written or updated
- If any milestones changed in the roadmap: remind them to check `milestones.md` is still in sync (or offer to review it now)
- If this was a creation: suggest `/ds-work-vision` next if vision.md doesn't exist yet, then `/ds-work-one-pager` to generate external-facing material
- If open questions were resolved: note that the PRD(s) for the relevant milestones may also need updating
