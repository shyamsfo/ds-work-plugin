---
name: ds-work-backlog
description: Manage the project's backlog — a low-commitment mind-dump of thoughts, ideas, and "we should look at this someday" notes. Show items, add new ones, or sweep periodically to promote, fold into a PRD's Out-of-scope, or delete. Reads and writes product/backlog.md.
---

Manage the project's backlog — pure mind-dump, no commitment, no priority.

The target directory defaults to `product/`. `backlog.md` lives at `<dir>/backlog.md`.

The backlog sits one rung below `parking-lot.md` on the capture ladder:

- **`backlog.md`** — "don't lose this thought." Might be a good idea, might be junk, nobody has decided yet.
- **`parking-lot.md`** — recognized as worth tackling eventually. Has tags. Gets suggested by `/ds-work-parking-lot`.
- **`design/M{N}-PLAN.md`** — scheduled within a milestone.

Items graduate UPWARDS during a `sweep`. They do not need to.

---

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — show the items + count, prompt next action |
| `add` | **add** — append a new item; the rest of the args is the item text |
| `sweep` | **sweep** — walk items one at a time; for each, decide promote / fold / delete / skip |

If `backlog.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

There is intentionally **no filter mode and no pick/suggest mode**. The whole point of the backlog is to NOT rank — the user gets to items when they're ready.

---

## Mode: default (no args)

1. Read `<dir>/backlog.md`. Parse items under `## Items`.
2. Output:

```
## Backlog — <N> items

- <item line, as written>
- ...
```

   Preserve the line exactly as it appears in the file. Do not reorder, do not regroup, do not normalize.

3. If `N == 0`: say *"Backlog is empty. Add a thought with `/ds-work-backlog add <text>`."* and exit.
4. If `N >= 1`: end with: *"Want to (a) add a new thought, (b) sweep through these (promote / fold / delete), or (c) just browse?"*
5. If `N >= 15` (configurable threshold), gently flag: *"Backlog is getting long — a sweep would be a good idea."* Do not nag beyond one sentence.

Do not rank, prioritize, or suggest "what to tackle." That's the parking-lot's job, not the backlog's.

---

## Mode: add

The item text is everything after `add` in `$ARGUMENTS`. If empty, ask the user for the item text.

1. Read `<dir>/backlog.md` (create from template if missing).
2. Build the new line:
   - If the user's text already starts with a date pattern (`YYYY-MM-DD` or similar), preserve as written: `- <text-as-typed>`.
   - Otherwise, prepend today's date: `- YYYY-MM-DD — <text>`.
3. **Append to the BOTTOM of the `## Items` section.** Backlog is append-only chronological. Never insert at top.
4. Confirm: *"Added to backlog. <N> items."*

Do not modify anything else. Do not auto-infer or add tags — the backlog is tag-free by design.

---

## Mode: sweep

Periodic cleanup. Walk items one at a time; for each, the user chooses one of:

- **(p)romote to parking-lot** — the item is real and worth tackling someday
- **(f)old into PRD out-of-scope** — the item belongs as an explicit non-goal of a specific milestone
- **(d)elete** — the thought has lost relevance (or was always junk)
- **(s)kip** — leave in backlog, move on
- **(q)uit sweep** — stop walking; remaining items stay in place

Process:

1. Read `<dir>/backlog.md` items under `## Items`.
2. If empty: *"Backlog is empty — nothing to sweep."* Exit.
3. For each item, in document order:
   - Show: `[i/N]  <item line>`
   - Ask: *"(p)romote, (f)old, (d)elete, (s)kip, or (q)uit sweep?"*
   - Branch on the answer (see sub-flows below).
4. After all items processed (or user quits), summarize: *"Sweep done. Promoted: X. Folded: Y. Deleted: Z. Skipped: S. Remaining in backlog: W."*

---

## Sub-flow: promote to parking-lot

1. Open `<dir>/parking-lot.md`. If missing, create from the empty template that `/ds-work-parking-lot` uses (or invite the user to run `/ds-work-parking-lot` once to seed it).
2. Ask the user (optional, short): *"Any tags? (e.g. `[bug] [S] →M3`, or skip)"*. Carry tags through verbatim; never auto-infer.
3. Append a new line to the **top** of parking-lot.md's `## Open` section (parking-lot is newest-first, opposite of backlog):
   ```
   - [ ] YYYY-MM-DD — <item text>  <tags if provided>  *(from backlog, <original-date-if-present>)*
   ```
   Today's date for `YYYY-MM-DD` (the promotion date). The backlog's original date goes into the provenance suffix.
4. Remove the item from `backlog.md`.
5. Confirm: *"Promoted to parking-lot."*

---

## Sub-flow: fold into PRD out-of-scope

1. Ask: *"Which milestone PRD? (M1, M2, ...)"*
2. Open `<dir>/design/M{N}-PRD.md`. If missing, warn: *"No PRD for M{N} yet. Skip, pick a different milestone, or run `/ds-work-plan M{N}` first?"*
3. Find the `## Out of scope` section. If it doesn't exist, append it immediately after `## Scope` (or, if there's no Scope section either, before the Decision Log / at the end of the body).
4. Append a new bullet under `## Out of scope`:
   ```
   - <item text>  *(from backlog, YYYY-MM-DD)*
   ```
5. Remove the item from `backlog.md`.
6. Confirm: *"Folded into M{N}-PRD Out-of-scope."*

---

## Sub-flow: delete

1. Remove the item from `backlog.md`. **No reason needed and no record kept** — the explicit purpose of the backlog is to surface things you might later realize are junk. The backlog is intentionally lossy.
2. Confirm: *"Deleted."*

If the user wants a record of what got deleted, they can promote-to-parking-lot first then drop in the parking-lot (which captures a reason). But by default, backlog deletes are silent.

---

## Sub-flow: skip

Leave the item in place and move to the next one. No file changes.

---

## Empty-file template

When creating `backlog.md` for the first time, write exactly:

```markdown
# Backlog

Mind-dump. Captured so we don't forget. No commitment, no priority. Sweep periodically: promote to `parking-lot.md`, fold into a PRD's Out-of-scope, or delete.

One item per line. Date prefix optional but useful: `- YYYY-MM-DD — <thought>`. Append at the bottom.

## Items

<!-- append at bottom; format: - YYYY-MM-DD — <thought> -->
```

---

## Notes

- One backlog per project, not per milestone.
- **Never reorder items.** They live in append order forever, by design.
- **No tags, no checkboxes, no priority.** The backlog is intentionally formless. The moment you start tagging or ranking, you have a parking-lot — graduate it.
- **The backlog is intentionally lossy.** Deleted items leave no trace. If you need a record, promote to parking-lot first then drop with a reason there.
- If the same thought keeps showing up in the backlog, that's a signal: promote it to parking-lot or fold it into a PRD's out-of-scope, then delete the duplicates.
- `/ds-work-halt` does not sweep the backlog automatically. Sweeping is an explicit user-initiated act.
