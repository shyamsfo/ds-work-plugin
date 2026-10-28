---
name: ds-work-bug
description: Manage the project's bug list — actual defects discovered but not yet fixed. Show open bugs, add new ones, or mark done/dropped. Reads and writes product/bugs.md.
---

Manage the project's bug list — actual defects that have been observed but not yet fixed.

The target directory defaults to `product/`. `bugs.md` lives at `<dir>/bugs.md`.

## When to log a bug (vs. not)

- **Log it**: it's broken, you've seen it, and you're deferring the fix.
- **Don't log it**: you're fixing it this session (just fix it — logs are for deferred work).
- **Don't log it**: it belongs in `milestones.md` (features and scoped work live there).
- **Don't log it**: it's unverified ("I think X might be broken"). Investigate first; log only if confirmed.
- **Don't log it**: it's a "should we…?" question or a polish item. Those go in `parking-lot.md` and `nitpicks.md` respectively.

Rule of thumb: if a stranger opened `bugs.md`, `nitpicks.md`, `parking-lot.md`, and `backlog.md`, which one would they understand this entry belongs in? If ambiguous between parking-lot and bugs, the parking-lot item is a *question*; the bug is a *defect statement* (it's broken, we know what it is).

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — list open bugs |
| `add` | **add** — append a new bug; the rest of the args is the bug text (title; optional " — body" after an em-dash) |
| `done` | **done** — mark a matching bug as fixed |
| `drop` | **drop** — drop a matching bug with a reason |

If `bugs.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

`<match>` for `done` and `drop` is a case-insensitive substring match against bug titles. If ambiguous (matches 2+), list candidates and prompt before acting. If no match, say so and bail — do not create a new entry.

---

## Mode: default (no args)

1. Read `<dir>/bugs.md`. Parse items under `## Open`.
2. Output:

```
## Bugs — <N> open

- **<title>** · <YYYY-MM-DD>
  - <body line, if present>
  - <optional context lines>
- ...
```

Preserve the order from the file (newest first). If zero open bugs, say so and stop.

---

## Mode: add

The bug text is everything after `add` in `$ARGUMENTS`. If empty, ask the user for the bug text.

Parse the text:
- If the text contains an em-dash (`—`), the part before is the title; the part after is the body line.
- Otherwise, the whole text is the title; no body.

1. Read `<dir>/bugs.md` (create from template if missing).
2. Build the new entry:
   ```
   - **<title>** · YYYY-MM-DD
     - <body line, if any>
   ```
   Use today's date. Do not infer severity or tags the user did not type.
3. **Append to the top of the `## Open` section** (newest first).
4. Confirm: *"Logged bug: <title>. <N> open bugs."*

Do not modify anything else.

---

## Mode: done

The `<match>` is everything after `done` in `$ARGUMENTS`. Trim whitespace and surrounding quotes.

1. Read `<dir>/bugs.md`. Case-insensitive substring match against the titles under `## Open`.
2. If ambiguous (2+ matches), list candidates numbered and ask which. If no match, say so and bail.
3. Move the matched entry from `## Open` to the top of `## Done / Dropped (last 10)` with marker:
   ```
   - **<title>** · <added-date> → YYYY-MM-DD · done
   ```
   Carry the body line(s) along as indented sub-bullets.
4. Trim `## Done / Dropped` tail to **10 entries max** — older items drop off silently.
5. Confirm: *"Fixed: <title>. <N> open bugs."*

---

## Mode: drop

Same as `done`, but marked as `dropped` and requires a one-line reason (prompt if not supplied — drops without a reason rot).

Move with marker:
```
- **<title>** · <added-date> → YYYY-MM-DD · dropped (<reason>)
```

---

## Empty-file template

When creating `bugs.md` for the first time, write exactly:

```markdown
# Bugs

Actual defects discovered but not yet fixed. If you fix a bug this session, move the entry to Done. Keep this list short — bugs should get prioritized, not accumulated.

Managed by `/ds-work-bug`. `/ds-work-halt` asks each session whether anything new is worth logging; `/ds-work-continue` surfaces the open count in the resume brief.

## Open

<!-- newest first; format:
- **<title>** · YYYY-MM-DD
  - <optional one-line body / severity / context>
-->

## Done / Dropped (last 10)

<!-- newest first; auto-trimmed to 10 -->
```

---

## Notes

- One bugs file per project.
- Never reorder Open items — append new ones at the top, leave the rest alone.
- The Done/Dropped tail is capped at 10. Session reports and git history are the long-term archive.
- No severity field, no "assigned to", no linked PRs — the file is small enough that reading it beats filtering it. Add structure later only if the list grows past ~15 items and the lack of structure actually hurts.
- A fixed bug's commit message should reference the bug's title. The git log is the audit trail; this file is the worklist.
