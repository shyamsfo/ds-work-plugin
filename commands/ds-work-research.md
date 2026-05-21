---
name: ds-work-research
description: Create a structured research document for market research, competitive analysis, or technology deep-dives. Guides the investigation interactively and writes findings to product/research/ or product/market-research/.
---

You are helping the user conduct and document a structured research investigation. This produces a permanent artifact in the project — not a one-time answer, but a document that informs a decision.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Check for existing research

List any files in `<dir>/research/` and `<dir>/market-research/` so the user knows what's already been done. If either directory doesn't exist, note it but continue.

Ask: **"What are you researching?"** Wait for the answer before proceeding.

---

## Step 2 — Classify the research type

Based on the user's answer, determine the type (or ask if unclear):

| Type | Description | Output directory |
|------|-------------|-----------------|
| `market` | Market size, customer segments, buying behavior, pricing | `<dir>/market-research/` |
| `competitive` | Specific competitors, their positioning, features, pricing, weaknesses | `<dir>/market-research/` |
| `technology` | Evaluating a technology, framework, protocol, or approach for use in the project | `<dir>/research/` |
| `user` | User interviews, surveys, behavioral patterns, workflow analysis | `<dir>/market-research/` |
| `domain` | Understanding a domain, industry, or regulatory context relevant to the product | `<dir>/research/` |

Confirm with the user: **"I'll file this as [type] research in [directory] — does that sound right?"**

---

## Step 3 — Frame the research

Ask these questions one at a time. Don't ask all at once.

1. **"What specific question does this research need to answer?"**
   Help them sharpen it if it's too broad. "How big is the market?" is weak. "Is there a segment of platform engineering teams managing >10 GPU workloads who are currently unserved by managed options?" is strong.

2. **"What decision does this inform — and by when?"**
   This prevents research from becoming an end in itself. Acceptable answers: "whether to price by seat or by GPU-hour (before M5)", "whether to build our own routing layer or use LiteLLM (this week)".

3. **"What do we already know or believe?"**
   Capture existing assumptions so the research can confirm or challenge them, not just collect neutral facts.

4. **"What would change if the answer is X vs Y?"**
   Forces the research to be decision-relevant. If both answers lead to the same action, the research isn't needed.

---

## Step 4 — Draft the research document

Generate a filename: `YYYY-MM-DD-<slug>.md` where slug is a 3-5 word kebab-case title derived from the question. Use today's date.

Write the document to the appropriate directory with this structure:

```markdown
# <Research Title>

**Type**: <market / competitive / technology / user / domain>
**Status**: 🔄 in progress
**Question**: <the sharpened question from Step 3>
**Decision it informs**: <what changes based on the answer, and by when>
**Started**: YYYY-MM-DD

---

## What We Already Know

<Assumptions and prior knowledge going into this research. These are the things this research will confirm, challenge, or add nuance to.>

---

## Approach

<How this will be investigated — sources, methods, scope limits. Be honest about what won't be covered.>

---

## Findings

> Fill this in as research progresses. Structure by sub-question or theme, not by source.

---

## Recommendation

> Complete this last. State the answer to the research question directly, then explain.

**Answer**: <direct answer to the research question>

**Rationale**: <2–3 sentences — what the findings show and why they support this answer>

**Confidence**: <high / medium / low — and why>

**What this changes**: <specific impact on vision, roadmap, or a milestone decision>

---

## Sources

| Source | Type | Key takeaway |
|--------|------|-------------|
| | | |

---

## Open Questions

<Things this research raised but didn't answer. These may become their own research docs or spike investigations.>
```

Show the user the created document path and tell them the structure is ready to fill in.

---

## Step 5 — Begin the investigation collaboratively

Ask: **"Do you want to work through this together now, or do you have findings to add?"**

**If working together now**: help the user think through the research systematically. For competitive research, work through competitors one by one. For technology research, evaluate against the project's specific requirements. For market research, help estimate from first principles rather than citing vague statistics. Push back on weak evidence. Fill in Findings and Recommendation collaboratively.

**If they have findings to add**: take what they give you and help structure it into the document. Ask clarifying questions to get to the Recommendation section — don't let the document end with findings but no answer.

---

## Step 6 — Complete the document

When findings are sufficient to answer the research question:

1. Fill in the **Recommendation** section — answer the question directly
2. Change **Status** from `🔄 in progress` to `✅ concluded`
3. Fill in **What this changes** — this is the most important field: what specifically in the project should change as a result?

If the research reveals something significant that affects `vision.md` or `roadmap.md`, flag it:
> "This finding suggests [X] should change in your roadmap/vision. Want to run `/ds-work-roadmap` or `/ds-work-vision` to review?"

---

## Step 7 — If research can't be concluded today

If the user needs to stop before the research is complete, leave **Status** as `🔄 in progress` and add a note under Findings:

```markdown
> **Paused YYYY-MM-DD**: <what was found so far, what remains to investigate>
```

Tell the user: "This research doc is in `<path>`. Resume it any time by running `/ds-work-research` — it will show you open research at the start."
