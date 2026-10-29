---
name: ds-work-todo
description: Manage the project's todo list — committed future work that wasn't done as a milestone closed. Show open todos, add new ones, or mark done/dropped. Reads and writes product/todos.md.
---

Manage the project's todo list — **committed deferred work**. Things we know we need to do but weren't worth keeping a milestone open for, or that we've consciously scheduled for after the current milestone.

The target directory defaults to `product/`. `todos.md` lives at `<dir>/todos.md`.

## When to log a todo (vs. not)

- **Log it**: a milestone just closed and there's known work that got scoped out — not a defect, not polish, just "we know we have to get to this, but not now." Typical origin: *"benchmark against larger cache TTLs — M2 only validated 5min"*, *"wire Prometheus alerts before public launch"*.
- **Log it**: committed future work that doesn't belong in any current milestone yet but will need to happen.
- **Don't log it**: it's broken (that's a bug — use `/ds-work-bug`).
- **Don't log it**: it's polish (that's a nitpick — use `/ds-work-nitpick`).
- **Don't log it**: it's a question or an idea you're not committed to (that's parking-lot).
- **Don't log it**: it's a half-formed thought (that's backlog).
- **Don't log it**: you're doing it this session — just do it.

Rule of thumb for the five surfaces:

| Surface | Content | Example |
|---|---|---|
| `bugs.md` | defects — "it's broken" | *"Flow didn't fire after Status update"* |
| `todos.md` | committed deferred work — "we will do this" | *"Benchmark larger cache TTLs — M2 only validated 5min"* |
| `nitpicks.md` | polish — "works but could be better" | *"Flag icon is red in Stats, blue in state LWC"* |
| `parking-lot.md` | questions/ideas — "should we…?" | *"Should we standardize the status colours?"* |
| `backlog.md` | mind-dump — "don't want to forget this thought" | *"Explore RAG for large documents someday"* |

A todo is a **commitment**; a parking-lot item is a **question**. If someone would reasonably ask "is this definitely going to happen?" and the answer is "yes, we just haven't scheduled it," it's a todo.

## Modes

Parse `$ARGUMENTS` (trim whitespace). The first token selects mode; everything after is mode-specific:

| First token | Mode |
|---|---|
| empty / missing | **default** — list open todos |
| `add` | **add** — append a new todo; the rest of the args is the todo text (title; optional " — body" after an em-dash; optional "from M{N}" trailing provenance) |
| `done` | **done** — mark a matching todo as done |
| `drop` | **drop** — drop a matching todo with a reason |

If `todos.md` does not exist, create it from the empty template at the bottom of this file and tell the user.

`<match>` for `done` and `drop` is a case-insensitive substring match against todo titles. If ambiguous (2+ matches), list candidates and prompt before acting. If no match, say so and bail — do not create a new entry.

---

## Mode: default (no args)

1. Read `<dir>/todos.md`. Parse items under `## Open`.
2. Output:

```
## Todos — <N> open

- **<title>** · <YYYY-MM-DD> [· from M{N}]
  - <body line, if present>
- ...
```

Preserve the order from the file (newest first). If zero open todos, say so and stop.

---

## Mode: add

The todo text is everything after `add` in `$ARGUMENTS`. If empty, ask the user for the todo text.

Parse the text:
- If the text contains an em-dash (`—`), the part before is the title; the part after is the body line.
- If the text ends with `from M{N}` (optionally also matching `from M1`/`from m2` case-insensitively), extract that as the provenance tag.
- Otherwise, the whole text is the title; no body; no provenance.

If no `from M{N}` was supplied and there is a milestone currently or recently marked `✅ done` in `<dir>/milestones.md`, ask once: *"Deferred from a closed milestone? (M{N} / none)"* — accept the answer, do not infer.

1. Read `<dir>/todos.md` (create from template if missing).
2. Build the new entry:
   ```
   - **<title>** · YYYY-MM-DD[· from M{N}]
     - <body line, if any>
   ```
   Use today's date. The `· from M{N}` suffix is included only if provenance was supplied.
3. **Append to the top of the `## Open` section** (newest first).
4. Confirm: *"Logged todo: <title>. <N> open todos."*

Do not modify anything else.

---

## Mode: done

The `<match>` is everything after `done` in `$ARGUMENTS`. Trim whitespace and surrounding quotes.

1. Read `<dir>/todos.md`. Case-insensitive substring match against the titles under `## Open`.
2. If ambiguous (2+ matches), list candidates numbered and ask which. If no match, say so and bail.
3. Move the matched entry from `## Open` to the top of `## Done / Dropped (last 10)` with marker:
   ```
   - **<title>** · <added-date> → YYYY-MM-DD · done [· from M{N}]
   ```
   Carry the body line(s) and the `from M{N}` suffix along verbatim.
4. Trim `## Done / Dropped` tail to **10 entries max** — older items drop off silently.
5. Confirm: *"Done: <title>. <N> open todos."*

---

## Mode: drop

Same as `done`, but marked as `dropped` and requires a one-line reason (prompt if not supplied — drops without a reason rot).

Move with marker:
```
- **<title>** · <added-date> → YYYY-MM-DD · dropped (<reason>) [· from M{N}]
```

---

## Empty-file template

When creating `todos.md` for the first time, write exactly:

```markdown
# Todos

Committed deferred work — things we know we need to do but weren't worth keeping a milestone open for. Distinct from `bugs.md` (defects), `nitpicks.md` (polish), and `parking-lot.md` (questions/ideas).

Managed by `/ds-work-todo`. `/ds-work-halt` asks each session whether anything new is worth logging; `/ds-work-continue` surfaces the open count in the resume brief above nitpicks/parking-lot (todos are committed work and rank just below bugs).

## Open

<!-- newest first; format:
- **<title>** · YYYY-MM-DD [· from M{N}]
  - <optional one-line body>
-->

## Done / Dropped (last 10)

<!-- newest first; auto-trimmed to 10 -->
```

---

## Notes

- One todos file per project.
- Never reorder Open items — append new ones at the top.
- The Done/Dropped tail is capped at 10. Session reports and git history are the long-term archive.
- The `· from M{N}` provenance is optional but useful — it tells future-you *why* this was committed and when. Prefer to include it when the todo came from a specific milestone cut.
- If a todo grows into real scoped work with its own exit criteria, promote it: add it as a task to the active milestone in `milestones.md` (or a new milestone), then `/ds-work-todo done <match>` to clear it from the todo list.
- Do not duplicate milestones.md's own `**Deferred:**` convention — that line in a closed milestone's section exists for auditors asking "did we really finish M{N}?"; `todos.md` is the actionable worklist those deferred items feed into.
