---
name: ds-work-one-pager
description: Create or update a one-pager.md for the project. Synthesises vision, roadmap, and milestone state into a crisp single-page summary, then iterates with the user.
---

You are helping the user create or refine a one-pager for their project. This is an interactive, iterative process — generate a draft, present it, then work through changes together.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

## 1. Gather source material

Read all of these in parallel:
- `<dir>/vision.md` — problem, customer, north star (primary source)
- `<dir>/roadmap.md` — architecture, delivery phases, key decisions (skim)
- `<dir>/milestones.md` — what's done vs in-progress vs pending (current state)
- `<dir>/now.md` — most recent completed work (for "where we are" section)

If `<dir>/one-pager.md` already exists, read it too — you'll be updating it, not replacing it from scratch.

## 2. Check for an existing one-pager

- If `<dir>/one-pager.md` exists: load it, note what's stale or missing, and propose targeted updates rather than a full rewrite.
- If it doesn't exist: generate a fresh draft using the structure below.

## 3. Draft structure

Write a one-pager that fits on a single printed page (~400–600 words). Use this structure:

```
# <Project Name> — One-Pager

## The Problem
<2–3 sentences. What pain exists, for whom, and why it matters now.>

## What We're Building
<2–3 sentences. The product in plain language — what it does, who uses it, how it's different.>

## How It Works
<3–5 bullets. Key technical or product mechanisms — enough to be credible, not a deep dive.>

## Where We Are
<Current milestone + 1–2 sentences on what's proven so far.>

## What's Next
<Next 1–2 milestones in plain language — what they unlock.>

## Why Now
<1–2 sentences. The timing argument — market window, infrastructure readiness, or strategic moment.>

## Key Numbers
<3–5 concrete metrics — benchmark results, cost figures, customer counts, or performance numbers pulled from source docs.>
```

Adapt section names if the project's vocabulary suggests better ones. Keep every section tight — cut anything that doesn't help a reader decide whether to care.

## 4. Present the draft

Show the full draft. Then immediately below it, list:

```
### Suggested improvements
- <specific thing that could be sharper, more concrete, or better evidenced>
- <section that is weak or missing a number>
- <anything that sounds vague or that a skeptical reader would question>
```

Limit suggestions to 3–5 — don't overwhelm. Focus on the highest-leverage improvements.

## 5. Iterate

Ask the user: **"What would you like to change? I can also expand any section, add a section, sharpen the language, or update numbers."**

Make changes as requested. After each round, show only the updated section(s) unless the user asks to see the full doc again.

Keep iterating until the user says they're happy with it or asks to save.

## 6. Save when ready

When the user confirms the draft is ready, write it to `<dir>/one-pager.md`. Tell them the file was written and that it can be regenerated any time by running `/ds-work-one-pager`.

Do not write the file until the user explicitly says to save or approves it.
