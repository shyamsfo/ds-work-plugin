---
name: ds-work-ideal-customer-profile
description: Interactively develop an Ideal Customer Profile (ICP) for the project. Walks the user through firmographics, technographics, AI-journey stage, buyer/user roles, trigger events, PMF signals, disqualifiers, and where to find them. Iterates until sharp, then saves.
---

You are helping the user develop a rigorous Ideal Customer Profile (ICP) for their project. This is an interactive, iterative process — start with a scaffold, elicit specifics dimension by dimension, sharpen, then save. Prefer concrete examples over abstractions ("Series A Document AI startup running vLLM on 2× L40S" beats "AI companies").

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

The ICP output lands in `<dir>/gtm/ideal-customer-profile.md`.

## 0. Lite-mode gate

Read `<dir>/ds-work-mode.txt` if it exists.

- If the file contains `lite`: **stop here.** An ICP synthesises problem framing, target customer, and market context — content that lives in `vision.md`. A lite project has none of that. Tell the user:

  > This project is in lite mode — it doesn't have a vision document to draw an ICP from. Run `/ds-work-graduate` first to promote the project to full mode and create `vision.md`, then re-run `/ds-work-ideal-customer-profile`.

  Then stop.

- If the file contains `full` or does not exist: continue with Step 1 below.

## 1. Gather source material

Read all of these in parallel:
- `<dir>/vision.md` — problem, ICP hints, disqualifiers, north star (primary source)
- `<dir>/overview.md` — if it exists, coverage table and "doesn't work" section (rich disqualifier source)
- `<dir>/gtm/one-pager.md` (or `<dir>/one-pager.md`) — if either exists, condensed starting point
- `<dir>/gtm/elevator-pitch.md` — if it exists, its investor variant often names the segment cleanly
- `<dir>/market-research/` — skim any files here for prospect interviews, competitive analysis, sizing
- `<dir>/milestones.md` — for credibility signals (which segments we've proven against)

If `<dir>/gtm/ideal-customer-profile.md` already exists, read it too — you'll be updating, not replacing.

## 2. Check for an existing ICP

- If an ICP already exists: load it, note what's stale, missing, or under-specified, and propose targeted updates rather than a full rewrite.
- If it doesn't exist: generate a scaffolded draft from source material using the structure below, marking anything you inferred with `[inferred — confirm]` so the user can push back before it hardens.

## 3. Draft structure

An ICP is a working document — 1–3 pages, denser than a pitch, sparser than a full market study. Use this structure. **If the project targets multiple distinct segments (e.g. primary + secondary), produce one ICP block per segment.**

```
# Ideal Customer Profile

> Working document. Update whenever new customer conversations, sales pattern, or product scope shift the picture.
> Last reviewed: <date>

## Segment: <short name — 3-6 words>

**One-line summary**: <who they are and what they do that makes them buy. Should map cleanly to the elevator pitch's audience.>

### Firmographics
- **Company stage / size**: <seed / Series A / Series B / mid-market / enterprise — with headcount or revenue anchors>
- **Industry / vertical**: <specific verticals, not "tech">
- **Geography**: <if it matters — e.g. US + EU only; APAC deferred>
- **Ownership / procurement style**: <bottoms-up product-led / top-down enterprise sales / channel / consulting-led>

### Technographics (the load-bearing PMF signals)
- **Infrastructure they run**: <specific stack — cloud, model runtime, hardware, storage>
- **Deployment shape that qualifies**: <the exact configuration where the product wins>
- **Adjacent tools already in place**: <what they've already bought / adopted that makes us a natural extension>
- **Workload shape**: <request pattern, corpus size, prompt length, QPS — whatever the product is sensitive to>

### AI journey stage
- **Where they are**: <exploring / prototyping / first production / scaling / optimising cost + latency / mature ops>
- **Why they're there** (not just what stage): <what got them to this stage — hire, product launch, cost pressure, latency SLA>
- **What they're feeling**: <the specific pain the product removes at this stage>

### Buyer & user
- **Economic buyer**: <role + seniority — CTO, VP Eng, Head of AI, Founding Eng>
- **Technical champion**: <the engineer who'll evaluate — Staff/Principal ML, Platform Lead>
- **End user of the product**: <who runs it day-to-day — SRE, ML Eng, Platform Eng>
- **Buying committee**: <who else needs to nod — Finance, Security, Legal>

### Trigger events (what puts them in market)
- <specific event 1 — e.g. "hit cost ceiling on inference bill"; "cold-start latency broke an SLA"; "hired their first ML platform engineer">
- <event 2>
- <event 3>

### PMF signals (things they say or measure that mean we can help)
- <specific — e.g. "asked about prefix cache hit rate"; "runs a scheduled batch job to warm caches manually"; "corpus size >500 GB and slow-changing">
- <signal 2>
- <signal 3>

### Disqualifiers (hard nos)
- <specific — e.g. "uses only managed APIs (OpenAI/Anthropic/Gemini)"; "corpus churns weekly"; "workload is decode-heavy generation, not prefill-heavy">
- <disqualifier 2>
- <disqualifier 3>

### Soft nos / adjacent-but-not-us (who might look right but isn't)
- <who's close enough to confuse — worth naming so we don't waste calibration time>

### Where to find them
- <communities, conferences, publications, keyword searches, GitHub stars, job postings, Slack/Discord channels>

### Objections we should expect (and our short answer)
- **"<objection>"** — <our answer in one line>
- **"<objection>"** — <our answer>

### Reference customers we've already earned (if any)
- <name / anonymized description + what they proved>

## Segment: <second segment name>
<repeat block if there's a distinct secondary ICP>
```

Adapt section names if the project's vocabulary suggests better ones. Cut sections that don't apply — but flag the cut so the user can push back.

## 4. Present the scaffold and elicit specifics

Show the scaffold populated from source material. Everything you inferred is tagged `[inferred — confirm]`; everything a source doc left blank is tagged `[gap — please fill]`.

Then walk the user through dimensions **one group at a time**, not all at once. Suggested groupings:

1. **Firmographics + AI journey stage** (who they are, where they are)
2. **Technographics + workload shape** (the load-bearing PMF signals)
3. **Buyer, user, and trigger events** (how the deal starts)
4. **Disqualifiers + soft nos** (who not to chase)
5. **Where to find them + expected objections** (go-to-market instrumentation)

For each group, ask **1–3 sharp elicitation questions**. Push for specificity:

- **Instead of** "what size company?" → "Name one real prospect who fits, and one who's just outside — what's the difference?"
- **Instead of** "who's the buyer?" → "Whose budget line does this hit? What's their KPI?"
- **Instead of** "what triggers a purchase?" → "What did the last three prospects Google right before finding us?"
- **Instead of** "who doesn't qualify?" → "If a prospect asks about X, what's the fastest 'this isn't for you' response?"

Common sharpening passes:
- **Replace adjectives with numbers** — "large corpus" → "≥100 GB, growing <5% per month"
- **Replace roles with named people** — abstract "CTO" → "a Series-A founding CTO with 30-50 person eng org and $1-3M inference bill"
- **Replace segments with concrete accounts** — "Document AI teams" → "the shape of Harvey, EvenUp, or Anterior"
- **Attach evidence** — if a claim comes from a prospect conversation, note it inline (e.g. "per 2026-08 conversation with X")

## 5. Iterate

Ask: **"Which dimensions would you like to sharpen? I can push on a specific segment, name concrete example accounts, add a section (e.g. pricing sensitivity, competitive alternatives they'd compare us to), or split/merge segments."**

After each round, show only the updated section(s) unless the user asks to see the full doc again.

Common late-stage refinements:
- **Split a segment** — if the elicitation surfaced two distinct buying motions, promote each to its own ICP block.
- **Add a "pricing sensitivity" line** — what they'll pay per month, what makes them balk.
- **Add a "competitive alternatives" line** — what they'd buy or build if not us; helps sharpen positioning.
- **Cross-check against elevator pitch and one-pager** — if the ICP names a segment the pitches don't address, one of them is drifting.

Keep iterating until the user says they're happy or asks to save.

## 6. Save when ready

When the user confirms the draft is ready, write it to `<dir>/gtm/ideal-customer-profile.md`. Create the `gtm/` directory if it doesn't exist. Tell them the file was written and that it can be regenerated any time by running `/ds-work-ideal-customer-profile`.

If the project already has an `elevator-pitch.md` or `one-pager.md` whose framing conflicts with the new ICP (e.g. names a segment the ICP disqualifies), flag the drift after saving and offer to update the pitch/one-pager next.

Do not write the file until the user explicitly says to save or approves it.
