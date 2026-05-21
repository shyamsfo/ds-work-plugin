---
name: ds-work-elevator-pitch
description: Generate a short elevator pitch for the project. Produces 3 variants at different lengths, then iterates with the user until it's sharp.
---

You are helping the user craft a tight elevator pitch for their project. This is a collaborative, iterative process — generate options, get feedback, refine.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

## 1. Gather source material

Read all of these in parallel:
- `<dir>/vision.md` — problem, customer, north star (primary source)
- `<dir>/one-pager.md` — if it exists, use it as a condensed starting point
- `<dir>/now.md` — current milestone state (for credibility signals: "we've already proven X")

If neither `vision.md` nor `one-pager.md` exist, read `roadmap.md` as a fallback.

## 2. Generate 3 pitch variants

Produce three pitches targeting different contexts. Label them clearly.

**Tweet (≤ 280 chars)**
One sentence that captures the problem and the product. No jargon. Must make a skeptic want to know more.

**30-second verbal (≤ 75 words)**
The version you'd say out loud to someone you just met. Covers: who has the problem, what the pain costs them, what you've built, and one proof point. Conversational tone, no bullet points.

**60-second investor / stakeholder (≤ 150 words)**
Adds: market context, what makes this defensible or timely, and current traction (what's been proven, not just planned). Still a single paragraph — no headers, no lists.

## 3. Present and prompt

Show all three variants. Then ask:

**"Which of these is closest to what you need? I can adjust the tone, sharpen the hook, add a specific number, make it more technical or less, or write a version targeting a specific audience (investor, customer, recruiter, partner)."**

## 4. Iterate

Work through changes one at a time. After each round, show the updated variant only (not all three again) unless the user asks for a full reset.

Common refinement passes:
- **Sharpen the hook** — lead with the pain or the surprise, not the product name
- **Add a number** — a concrete metric beats any adjective ("93% cache hit rate" > "fast prefix caching")
- **Audience swap** — rewrite for a specific reader (a CTO, a VC, a potential customer)
- **Cut jargon** — flag any term that requires insider knowledge; offer a plain-language swap
- **Strengthen the "why us / why now"** — add one sentence about what makes this timely or defensible

Keep iterating until the user is satisfied or asks to save.

## 5. Save when ready

When the user confirms a variant is ready, write it to `<dir>/elevator-pitch.md` with all three final variants (or just the one they chose — ask if unclear). Tell them the file was written.

Do not write the file until the user explicitly says to save or approves it.
