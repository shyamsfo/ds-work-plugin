---
name: ds-work-parking-lot
description: Manage the project's parking-lot — work items captured but not yet scheduled into a milestone. Show items, suggest 1–3 to tackle, add new items, promote to the active PLAN, or drop. Reads and writes product/parking-lot.md.
---

Manage the parking-lot of unscheduled work items.

The target directory defaults to `product/`. `parking-lot.md` lives at `<dir>/parking-lot.md`.

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — show grouped list + suggest 1–3 |
| `add` | **add** — append a new item; the rest of the args is the item text |
| `pick` | **pick** — just the recommendations, no full list |
| any other token | **filter** — treat the token as a tag filter (e.g. `bug`, `chore`, `idea`, `research`) |

If `parking-lot.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

---

## Mode: default (no args)

1. Read `<dir>/parking-lot.md`. Parse items under `## Open`.
2. Read `<dir>/milestones.md` and identify the active milestone ID (`M{N}`) — needed for the relevance heuristic.
3. Group Open items by type tag (`bug`, `chore`, `idea`, `research`, or `other` if no type tag).
4. Pick **1–3 suggestions** with rationale, ranked by this heuristic (in order):
   - **Linked to active milestone** (`→M{N}` tag) — bumps to the top.
   - **Quick wins** (`[S]` size tag).
   - **Stale** (added more than 21 days ago) — surface so they don't rot silently.

   Prefer variety: don't suggest three bugs if there's also an idea worth surfacing.

5. Output:

```
## Parking Lot — <N> open

### bugs (<count>)
- <date> — <title>  <tags>
- ...

### chores (<count>)
...

(repeat per type that has items)

---

### Suggested next pick(s)

1. **<title>** — <why this one: e.g. "quick win, linked to active milestone M2">
2. **<title>** — <why>
3. **<title>** — <why>
```

6. Ask: *"Want to (a) tackle one now, (b) promote one into the active M{N}-PLAN, (c) drop one, or (d) just browse?"*
7. If the user picks an item to tackle, promote, or drop, follow the relevant sub-flow below.

---

## Mode: add

The item text is everything after `add` in `$ARGUMENTS`. If empty, ask the user for the item text.

1. Read `<dir>/parking-lot.md` (create from template if missing).
2. Build the new line:
   ```
   - [ ] YYYY-MM-DD — <item text>  <tags carried over verbatim if the user included them>
   ```
   Use today's date. Preserve any `[bug]`, `[S]`, `→M2` tags the user typed — do not infer tags they did not write.
3. **Append to the top of the `## Open` section** (newest first).
4. Confirm: *"Parked: <title>. Parking lot has <N> open items."*

Do not modify anything else.

---

## Mode: pick

Same as default mode, but skip the full grouped list — output only the "Suggested next pick(s)" section and the follow-up question.

---

## Mode: filter (tag)

Same as default, but in step 3 show only items whose tags include the filter token (case-insensitive). If no items match, say so and offer to fall back to the full list.

---

## Sub-flow: tackle an item now

1. In `parking-lot.md`, mark the item `[~]` and prepend today's date as a start marker: `- [~] <added-date> (started YYYY-MM-DD) — <title>  <tags>`.
2. Hand off as a normal task — the user works it. At session halt, `/ds-work-halt` will surface completed/in-flight parking items.
3. When the user reports done, change `[~]` to `[x]` and move the item from `## Open` to the top of `## Done / Dropped (last 10)` with end date appended: `- [x] <added-date> → YYYY-MM-DD — <title>  <tags>`. Trim the tail to 10 items; older items drop off silently.

---

## Sub-flow: promote to PLAN

1. Identify active milestone `M{N}` from `<dir>/milestones.md`.
2. Open `<dir>/design/M{N}-PLAN.md`. If it does not exist, abort: *"No PLAN exists for M{N}. Run /ds-work-plan first, or tackle this directly without promoting."*
3. Append the item under a heading `## Parked items promoted` at the bottom of the PLAN (create the heading if it does not exist). Format:
   ```
   - [ ] <title>  *(from parking-lot, parked YYYY-MM-DD)*
   ```
4. In `parking-lot.md`, move the item from `## Open` to `## Done / Dropped (last 10)` with marker `[→]`:
   ```
   - [→] <added-date> → YYYY-MM-DD — <title>  *(promoted to M{N}-PLAN)*
   ```
5. Trim Done/Dropped tail to 10.
6. Confirm both edits to the user.

---

## Sub-flow: drop an item

1. Ask the user for a one-line reason (required — drops without a reason rot).
2. Move the item from `## Open` to `## Done / Dropped (last 10)` with marker `[-]`:
   ```
   - [-] <added-date> → YYYY-MM-DD dropped — <title>  *(<reason>)*
   ```
3. Trim tail to 10.
4. Confirm.

---

## Empty-file template

When creating `parking-lot.md` for the first time, write exactly:

```markdown
# Parking Lot

Unscheduled work items — bugs, chores, ideas, research notes — captured so they don't get lost. Add items as you go; `/ds-work-parking-lot` surfaces them and suggests what to tackle.

One item per line. Tags are freeform but the conventions are:
- type: `[bug]` `[chore]` `[idea]` `[research]`
- size: `[S]` `[M]` `[L]`
- milestone link: `→M{N}` (e.g. `→M2`)

## Open

<!-- newest first; format: - [ ] YYYY-MM-DD — title  [tags] -->

## Done / Dropped (last 10)

<!-- newest first; auto-trimmed to 10 -->
```

---

## Notes

- One parking-lot per project, not per milestone.
- Never reorder Open items — append new ones at the top, leave the rest alone. The suggestion heuristic does the ranking at read time.
- Never auto-infer tags. Carry them through verbatim from what the user wrote.
- The Done/Dropped tail is capped at 10. Session reports are the long-term record.
