---
name: ds-work-challenge
description: Adversarially review a planning artifact (vision, roadmap, milestone PRD, or milestone PLAN) — pressure-tests for soundness, not drift. Writes a Challenge Report to product/reviews/ without modifying the source doc.
---

You are helping the user **pressure-test** a planning artifact. This is *adversarial* review — "is this actually right?" — distinct from the *drift* review modes built into `/ds-work-vision`, `/ds-work-roadmap`, and `/ds-work-plan`, which ask "does this still match reality?".

You write a Challenge Report. You do **not** modify the source doc. The user decides which concerns to fold into the source doc's Decision Log themselves.

The target directory is: `product/` (default). If the user wants a different directory, they will say so.

---

## Step 1 — Identify the target

Parse `$ARGUMENTS`. Expected form: `<target>` or `<target> <milestone-id>`.

Valid targets:
- `vision` → `<dir>/vision.md`
- `roadmap` → `<dir>/roadmap.md`
- `milestone-prd` → `<dir>/design/M{N}-PRD.md` (where N is the active milestone or the one passed as second argument)
- `milestone-plan` → `<dir>/design/M{N}-PLAN.md` (same)

If `$ARGUMENTS` is empty or ambiguous, ask: *"What do you want to challenge — `vision`, `roadmap`, `milestone-prd`, or `milestone-plan`?"*

For milestone targets without an explicit ID: read `<dir>/milestones.md` and use the active milestone (the first one marked in progress). Confirm with the user before proceeding.

If the resolved target file does not exist, say so and stop.

---

## Step 2 — Load context

Read the target file fully. Then load *supporting* context relevant to the target — these inform whether the target's claims hold up:

| Target | Also read |
|---|---|
| `vision` | `<dir>/roadmap.md`, `<dir>/milestones.md`, latest 2 files in `<dir>/reports/`, `<dir>/research/` and `<dir>/market-research/` index if present |
| `roadmap` | `<dir>/vision.md`, `<dir>/milestones.md`, latest 2 files in `<dir>/reports/` |
| `milestone-prd` | `<dir>/vision.md`, `<dir>/roadmap.md`, `<dir>/milestones.md`, the M{N}-PLAN.md if it exists, latest 2 files in `<dir>/reports/` |
| `milestone-plan` | The companion M{N}-PRD.md, `<dir>/milestones.md`, latest 2 files in `<dir>/reports/` |

If `<dir>/how-to/ds-work-challenge.md` exists, read it now and fold in any project-specific lenses or red-team angles.

---

## Step 3 — Adversarial review

Adopt a **skeptical, well-informed critic** frame. The lens depends on the target:

| Target | Lens |
|---|---|
| `vision` | A skeptical early customer + a skeptical investor. Where is the problem statement weak? Is the north star achievable? Is the "why now" actually load-bearing or just rhetorical? |
| `roadmap` | A skeptical engineering lead + a skeptical PM. Are milestones in the right order? Does completing them deliver the vision, or is there a gap? Are there hidden dependencies? Is any milestone over- or under-scoped? |
| `milestone-prd` | A critical reviewer focused on scope and soundness. Is the goal sharp? Are success criteria observable? Is the approach feasible? What's the riskiest assumption? What's missing from "Out of scope"? |
| `milestone-plan` | A critical engineer reviewing decomposition. Are sub-tasks sized right? Are verification steps real? What's missing — error handling, edge cases, integration steps, rollback? Is the order right? |

Generate a numbered list of concerns. Aim for **5–10** — quality over quantity. Each concern should be actionable: either a question the user should answer before proceeding, or a specific change to consider.

Do **not**:
- Restate the doc back to the user. Assume they wrote it.
- Be performatively harsh. The bar is "informed critic," not "contrarian for sport."
- Surface concerns the doc explicitly already addresses. Read it carefully first.

For each concern, capture:

```markdown
### N. <Short title>

**Claim**: <what's potentially wrong, in one sentence>

**Reasoning**: <why this is a concern — cite the doc, cite reality, or both. 1–3 sentences.>

**Suggested response**: <a question to answer, a change to consider, or "accept as-is and document why">
```

Group concerns thematically if natural (e.g., "Scope concerns", "Sequencing concerns", "Feasibility concerns"). Don't force grouping if there are too few to warrant it.

---

## Step 4 — Write the Challenge Report

Save to `<dir>/reviews/<target-slug>-challenge-YYYY-MM-DD.md`.

Filename convention:
- `vision-challenge-2026-04-28.md`
- `roadmap-challenge-2026-04-28.md`
- `M3-PRD-challenge-2026-04-28.md`
- `M3-PLAN-challenge-2026-04-28.md`

If `<dir>/reviews/` does not exist, create it.

Report structure:

```markdown
# Challenge: <Target> — YYYY-MM-DD

**Target file**: `<path>`
**Frame**: <one sentence describing the lens used>

---

## Summary
<2–3 sentences: what's strong, what the most important concerns are. Up front so the user can triage.>

---

## Concerns

### 1. <Short title>
**Claim**: …
**Reasoning**: …
**Suggested response**: …

### 2. …
```

Do **not** modify the source doc.

---

## Step 5 — After saving

Show the user the report (or a tight summary of it — don't spam if it's long), then:

1. Tell them where it was saved.
2. Suggest the next step: *"Walk through these one at a time, decide which to act on. For accepted concerns, the relevant `/ds-work-vision` / `/ds-work-roadmap` / `/ds-work-plan` review modes can fold them into the source doc's Decision Log."*
3. Do **not** auto-modify the source doc or its Decision Log.

If the user wants to walk the concerns interactively right now, do so — for each concern, ask "accept / reject / defer" and capture the disposition. But default to handing the report off and stopping.
