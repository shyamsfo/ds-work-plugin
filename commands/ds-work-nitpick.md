---
name: ds-work-nitpick
description: Manage the project's nitpick list — polish items that work but could be better. Show open nitpicks, add new ones, or mark done/dropped. Reads and writes product/nitpicks.md.
---

Manage the project's nitpick list — minor polish items. The thing works, but it could be better.

The target directory defaults to `product/`. `nitpicks.md` lives at `<dir>/nitpicks.md`.

## When to log a nitpick (vs. not)

- **Log it**: it works, but something is inconsistent, imprecise, or just a little annoying. You want to clean it up but not now.
- **Don't log it**: it's broken (that's a bug — use `/ds-work-bug`).
- **Don't log it**: it's a "should we…?" question (that's parking-lot).
- **Don't log it**: you're fixing it this session. Just fix it.
- **Don't log it**: it belongs in `milestones.md` — if it's part of scoped feature work, track it there.

Rule of thumb: a nitpick is a **statement** ("flag icon is red in Stats, blue in state LWC"); a parking-lot item is a **question** ("should we standardize the status colors?"); a bug is a **defect** ("Status update doesn't trigger the flow").

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — list open nitpicks |
| `add` | **add** — append a new nitpick; the rest of the args is the nitpick text (title; optional " — body" after an em-dash) |
| `done` | **done** — mark a matching nitpick as cleaned up |
| `drop` | **drop** — drop a matching nitpick with a reason |

If `nitpicks.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

`<match>` for `done` and `drop` is a case-insensitive substring match against nitpick titles. If ambiguous (2+ matches), list candidates and prompt before acting. If no match, say so and bail — do not create a new entry.

---

## Mode: default (no args)

1. Read `<dir>/nitpicks.md`. Parse items under `## Open`.
2. Output:

```
## Nitpicks — <N> open

- **<title>** · <YYYY-MM-DD>
  - <body line, if present>
- ...
```

Preserve the order from the file (newest first). If zero open nitpicks, say so and stop.

---

## Mode: add

The nitpick text is everything after `add` in `$ARGUMENTS`. If empty, ask the user for the nitpick text.

Parse the text:
- If the text contains an em-dash (`—`), the part before is the title; the part after is the body line.
- Otherwise, the whole text is the title; no body.

1. Read `<dir>/nitpicks.md` (create from template if missing).
2. Build the new entry:
   ```
   - **<title>** · YYYY-MM-DD
     - <body line, if any>
   ```
   Use today's date.
3. **Append to the top of the `## Open` section** (newest first).
4. Confirm: *"Logged nitpick: <title>. <N> open nitpicks."*

Do not modify anything else.

---

## Mode: done

The `<match>` is everything after `done` in `$ARGUMENTS`. Trim whitespace and surrounding quotes.

1. Read `<dir>/nitpicks.md`. Case-insensitive substring match against the titles under `## Open`.
2. If ambiguous, list candidates numbered and ask which. If no match, say so and bail.
3. Move the matched entry from `## Open` to the top of `## Done / Dropped (last 10)` with marker:
   ```
   - **<title>** · <added-date> → YYYY-MM-DD · done
   ```
   Carry the body line(s) along as indented sub-bullets.
4. Trim `## Done / Dropped` tail to **10 entries max**.
5. Confirm: *"Cleaned up: <title>. <N> open nitpicks."*

---

## Mode: drop

Same as `done`, but marked as `dropped` and requires a one-line reason (prompt if not supplied).

Move with marker:
```
- **<title>** · <added-date> → YYYY-MM-DD · dropped (<reason>)
```

---

## Empty-file template

When creating `nitpicks.md` for the first time, write exactly:

```markdown
# Nitpicks

Minor polish items — things that work but could be better. Logged ad hoc so they don't rot in a Slack DM; sweep at session end via `/ds-work-halt`.

Managed by `/ds-work-nitpick`. `/ds-work-halt` asks each session whether anything new is worth logging; `/ds-work-continue` surfaces the open count in the resume brief.

## Open

<!-- newest first; format:
- **<title>** · YYYY-MM-DD
  - <optional one-line body / context>
-->

## Done / Dropped (last 10)

<!-- newest first; auto-trimmed to 10 -->
```

---

## Notes

- One nitpicks file per project.
- Never reorder Open items — append new ones at the top.
- The Done/Dropped tail is capped at 10. Session reports and git history are the long-term archive.
- No severity, no tags, no owner — nitpicks are small by definition. If you're reaching for structure, the item probably belongs in `parking-lot.md` or `milestones.md` instead.
