---
name: ds-work-vision
description: Interactively create or review product/vision.md. Creates it from scratch if missing (e.g. after scaffold), or reviews it against current project state and proposes updates.
---

You are helping the user create or review the product vision document. Branch on whether the document exists and is filled in.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Assess what exists

Read `<dir>/vision.md`.

Classify its state:
- **Missing**: file does not exist
- **Placeholder**: file exists but contains scaffold boilerplate (`<Describe...>`, `TODO`, `> Replace this placeholder`, empty sections)
- **Filled**: file has real content

Then read these in parallel (they inform both creation and review):
- `<dir>/milestones.md` — what has been proven or completed
- `<dir>/now.md` — most recent completed work and current focus
- The most recent file in `<dir>/reports/` — last session's findings

---

## Step 2A — If Missing or Placeholder: Create Mode

Walk the user through each section interactively. Do not generate all sections at once — complete one, show it, get feedback, then move on.

Work through these sections in order:

### Problem
Ask: *"What pain are you solving, for whom, and why existing options fall short?"*

Generate a draft from whatever the user says. Aim for 3–5 sentences covering:
- Who feels the pain and in what context
- What they do today (and why that's inadequate)
- What the gap is

Show the draft. Offer to sharpen it. Move on when the user is satisfied.

### Customer / Target user
Ask: *"Who is this for — be as specific as you can about their role, team, and the moment they hit this problem."*

Draft 2–3 sentences. Offer to add a secondary user if relevant. Move on.

### Market context
Ask: *"What alternatives exist and what's wrong with each? Are there any competitors or adjacent products?"*

Draft a short paragraph. Refer to `<dir>/market-research/` if it exists and has content. Move on.

### North star
Ask: *"What does success look like in 2–3 years? What can a user do then that they can't do today?"*

Draft 2–3 sentences. This should be aspirational but specific — not "best product in category" but a concrete capability or outcome. Move on.

### Why now
Ask: *"Why is this the right moment to build this — what's changed recently that makes it viable or urgent?"*

Draft 1–2 sentences. Could be a technology shift, a market gap that just opened, a regulatory change, or simply that the infrastructure now exists to make it feasible. Move on.

### What we are not
Ask: *"What are the product-level anti-goals — things this product will deliberately never try to be?"*

Draft 3–5 bullets. These should be principled product-scope cuts, not just feature gaps. Move on.

### Finalize
Show the complete draft. List 2–3 suggestions for anything that seems vague, unsupported, or weak.

Ask: *"Anything to change before I save this?"* Iterate until the user confirms.

Write to `<dir>/vision.md`. Do not include a Decision Log yet — the document is new.

---

## Step 2B — If Filled: Review Mode

The goal is to surface drift: places where the vision no longer matches what has been learned or built.

### Load context
Read the filled `<dir>/vision.md` fully.

### Check for drift — work through each section

**Problem section**: Does recent work confirm or complicate the problem statement?
- Did any completed milestone reveal that the problem is harder, narrower, or different than stated?
- Is the "why existing options fall short" still accurate?

**Customer section**: Has the target user sharpened or shifted?
- Did actual usage (or benchmark results, or customer feedback) reveal a more specific or different user profile?

**Market context**: Is this still accurate?
- Any new competitors or reference points from the session reports or design docs?

**North star**: Does it still point the right direction?
- Did any milestone reveal that the north star is too ambitious, too narrow, or needs reframing?

**Why now**: Is the timing argument still valid, or has it strengthened?
- Did completed work prove out a key technical assumption?

**What we are not**: Are the anti-goals still holding?
- Did any scope decision in milestones or PRDs imply a different boundary?

### Propose specific edits

For each section where you detect drift or weakness, propose a specific change:

```
### [Section name] — suggested update

**Current**: "<exact quote from current doc>"

**Proposed**: "<replacement text>"

**Why**: <one sentence — what changed or what's weak>
```

If a section looks solid, say so briefly and skip it.

Aim for 2–5 targeted proposals. Do not rewrite the whole document.

### Iterate
Ask: *"Which of these would you like to apply? I can also make other changes or leave anything as-is."*

Apply changes one at a time. Show the updated section after each change.

### Decision Log
If any changes are applied, append a row to the Decision Log at the bottom of the file:

```markdown
---

## Decision Log

| Date | Section | Change | Rationale |
|------|---------|--------|-----------|
| YYYY-MM-DD | <section name> | <what changed — brief> | <why — what prompted the update> |
```

If a Decision Log already exists, append a new row. Do not rewrite existing rows.

Write the file when the user confirms.

---

## Step 3 — After saving

Tell the user:
- What was written or updated
- If this was a creation: suggest running `/ds-work-roadmap` next to align the roadmap with the vision
- If this was a review: note any vision changes that may require a roadmap review (`/ds-work-roadmap`)
