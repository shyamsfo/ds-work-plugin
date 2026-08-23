---
name: ds-work-recurring
description: Manage the project's recurring-maintenance list — tasks that decay and need periodic attention (refresh a snapshot, sweep a directory, re-validate a pin). Not milestones (no exit criteria), not parking-lot (never move to Done). Show overdue + upcoming, add new items, mark done to update last-run, or sweep for stale entries. Reads and writes product/recurring.md.
---

Manage the recurring-maintenance list.

The target directory defaults to `product/`. `recurring.md` lives at `<dir>/recurring.md`.

## What "recurring" is (and what it isn't)

Recurring items are tasks that:
- **Decay over time** and need periodic touching (a snapshot goes stale, a directory accumulates cruft, a pin ages)
- **Have no terminal Definition of Done** — you never "finish" refreshing the pricing table; you finish *this* refresh
- **Aren't sequential** with each other or with milestone work

They differ from:
- **milestones.md** — sequential dependency-ordered work with exit criteria
- **parking-lot.md** — items that get executed once and then move to Done/Dropped
- **backlog.md** — half-formed thoughts that graduate upward or drop

If an item will complete exactly once and then be done, it's a parking-lot item, not a recurring one. If in doubt, put it in parking-lot first — you can always promote it later.

## Cadence conventions

Every recurring item carries a cadence marker. The four supported values:

| Marker | Cadence | Interval | Use when |
|---|---|---|---|
| 🕐 | weekly | 7 days | Something drifts fast (fresh API data, active-repo activity) |
| 🕐🕐 | monthly | 30 days | Snapshots that decay in weeks (pricing, model landscape) |
| 🕐🕐🕐 | quarterly | 90 days | Slower drift (dependency pins, stale-item sweeps) |
| 📡 | signal-driven | never overdue by time | Fires on some external trigger (e.g. "before any preset fire >$5/hr"); documented for discoverability |

"Overdue" is computed as: `today − last_run > cadence_days` for time-driven items. Signal-driven items are never counted as overdue — they exist in the file for discoverability, not scheduling.

An item with "Last run: never" is treated as overdue.

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — show overdue + upcoming, grouped by cadence |
| `add` | **add** — append a new item; the rest of the args is the item text |
| `done` | **done** — mark an item done today; the rest of the args identifies the item |
| `sweep` | **sweep** — walk stale items; for each, ask keep / adjust / drop |

If `recurring.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

---

## Mode: default (no args)

1. Read `<dir>/recurring.md`. Parse items under `## Open`. Each item has a cadence marker (🕐 / 🕐🕐 / 🕐🕐🕐 / 📡) and a "Last run: YYYY-MM-DD" or "Last run: never" field.
2. For each time-driven item, compute `days_since_last_run` (∞ if never). Compare against the cadence interval.
3. Group Open items into buckets:
   - **Overdue** (days_since_last_run > cadence_days, or "never") — sorted by days-past-due descending
   - **Due soon** (within 7 days of becoming overdue)
   - **On track** (further out than 7 days)
   - **Signal-driven** (📡 items, listed separately)
4. Output:

```
## Recurring — <N> open (<X> overdue, <Y> due soon)

### Overdue (<count>)
- <cadence marker> **<title>** — last run <YYYY-MM-DD or "never">, <N> days past due
  <pointer to where the how-to lives>
- ...

### Due soon (<count>)
- <cadence marker> **<title>** — last run <date>, due in <N> days

### On track (<count>)
- <cadence marker> **<title>** — last run <date>, due in <N> days

### Signal-driven (<count>)
- 📡 **<title>** — <signal condition>
  <pointer>
```

5. If there are overdue items, ask: *"Tackle the top overdue item now, or just browse?"*
6. If the user picks one to tackle, hand off. When the user reports the refresh done, run the `done` sub-flow.

---

## Mode: add

The item text is everything after `add` in `$ARGUMENTS`.

1. If the arg text doesn't include a cadence marker (🕐 / 🕐🕐 / 🕐🕐🕐 / 📡) or a `[weekly]` / `[monthly]` / `[quarterly]` / `[signal]` tag, ask the user which cadence applies. Do not infer.
2. If no pointer is provided (a file path or section reference telling future-you where the instructions live), ask for one. Recurring items without pointers rot into vague reminders.
3. Read `<dir>/recurring.md` (create from template if missing).
4. Build the new line:
   ```
   - <cadence marker> **<title>** — <pointer sentence>. Last run: never.
   ```
5. **Append to the top of the `## Open` section** (newest first, like parking-lot).
6. Confirm: *"Added recurring: <title> (<cadence>). Recurring list has <N> open items, <X> overdue."*

Do not modify anything else.

---

## Mode: done

The item identifier is everything after `done` in `$ARGUMENTS`. Match it fuzzily against titles (case-insensitive substring match). If ambiguous, show the candidates and ask which.

1. Read `<dir>/recurring.md`.
2. In the matched Open item, update the "Last run:" field to today's date.
3. Append a one-liner to `## Done / History (last 10)` at the top:
   ```
   - YYYY-MM-DD — <title>: <one-line summary of what was done, if the user provided one, else just "refreshed">
   ```
4. Trim the tail to 10 items; older items drop off silently.
5. Confirm: *"Marked done: <title>. Next due: <YYYY-MM-DD> (in <N> days)."*

If the user has NOT already done the refresh (i.e. `done` was invoked without the refresh having happened), stop and tell them — `done` only *records* completion, it doesn't do the work.

---

## Mode: sweep

Walk items one at a time. For each item, present:

```
<cadence marker> **<title>** — added <YYYY-MM-DD>, last run <date>, currently <overdue / on track>
Pointer: <where the how-to lives>
```

Ask: *"Keep as-is / adjust cadence / drop?"*

- **Keep** — no change; move to next item
- **Adjust cadence** — ask for new cadence marker; update the item's marker in place
- **Drop** — ask for a one-line reason (required); move item from `## Open` to `## Done / History (last 10)` with marker `[-]` and the reason
- Skip continues to the next item

After the walk, confirm what changed.

---

## Empty-file template

When creating `recurring.md` for the first time, write exactly:

```markdown
# Recurring Maintenance

Tasks that need periodic attention. Not milestones (no exit criteria), not parking-lot (never move to Done), not backlog (don't graduate). Just things that decay.

Managed by `/ds-work-recurring`. `/ds-work-status` and `/ds-work-continue` surface overdue counts; `/ds-work-halt` asks whether any recurring items were completed this session.

## Cadence key

- 🕐 weekly (7 days)
- 🕐🕐 monthly (30 days)
- 🕐🕐🕐 quarterly (90 days)
- 📡 signal-driven (triggered by an external event; never "overdue" by time)

Every item must have a cadence marker, a title, a pointer (where the how-to lives), and a "Last run" field. Items without pointers rot; the pointer is what future-you consults when the item comes due.

## Open

<!-- newest first; format: - <cadence marker> **<title>** — <pointer sentence>. Last run: <YYYY-MM-DD or never>. -->

## Done / History (last 10)

<!-- newest first; auto-trimmed to 10; format: - YYYY-MM-DD — <title>: <summary> -->
```

---

## Notes

- One recurring-list per project, not per milestone.
- Never reorder Open items — sort at display time, keep the file order stable.
- Never auto-infer a cadence. Ask the user if they didn't specify one.
- Signal-driven items live in the same file as time-driven ones because they share the same category ("attention that doesn't come from a milestone") — but they are not counted in overdue math.
- The Done/History tail is capped at 10. Session reports are the long-term record.
- If a recurring item consistently gets ignored past its due date across multiple sweeps, that is signal: either the cadence is wrong (too aggressive) or the item isn't actually worth doing (drop it).
