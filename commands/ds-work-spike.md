---
name: ds-work-spike
description: Create and work through a time-boxed technical spike — a focused investigation to answer a specific feasibility or design question. Writes to product/research/spikes/.
---

You are helping the user run a spike: a time-boxed investigation with a single question and a concrete outcome (yes / no / not yet / use X instead of Y). Spikes are not milestones — they feed into milestones by resolving uncertainty before it becomes a blocker.

The target directory is: `$ARGUMENTS` (use `product/` if empty or not provided).

---

## Step 1 — Show open spikes

Check if `<dir>/research/spikes/` exists. If it does, list any files where status is `🔄 open`. Show them as:

```
Open spikes:
- product/research/spikes/2026-04-01-lmcache-redis-compat.md — "Can LMCache share KV blocks across pods via Redis without pod restart?"
```

If there are open spikes, ask: **"Do you want to continue one of these, or start a new one?"**

If continuing an existing spike, read it and skip to Step 4.

---

## Step 2 — Frame the spike

Ask: **"What's the question you're trying to answer?"**

A good spike question is:
- Answerable in the stated timebox (not open-ended research)
- Binary or close to it — yes/no, A or B, feasible or not
- Specific enough that you'd know when you have the answer

Help them sharpen the question if needed. Examples of weak → strong:
- "How does LMCache work?" → "Does LMCache support cross-pod KV sharing via Redis without requiring pod restart?"
- "Should we use Helm or Kustomize?" → "Can Kustomize handle our per-customer value overrides without a full template per customer?"

Then ask: **"What's your timebox?"** (e.g. 2 hours, half a day, one session). This is a commitment, not a suggestion.

Then ask: **"What does a 'yes' answer unblock? What does a 'no' mean for the project?"** This prevents spikes that don't connect to a real decision.

---

## Step 3 — Create the spike document

Generate a filename: `YYYY-MM-DD-<slug>.md` where slug is 3-5 kebab-case words. Create `<dir>/research/spikes/` if it doesn't exist.

Write the document:

```markdown
# Spike: <Question as title>

**Status**: 🔄 open
**Question**: <exact question — the spike is done when this is answered>
**Timebox**: <duration — treat this as a hard stop>
**Started**: YYYY-MM-DD

**If yes**: <what this unblocks>
**If no**: <what this means for the project — pivot, descope, or accept the constraint>

---

## Approach

<How you'll investigate this. List the specific things to try or check, in order. Keep it short — a spike is not a research project.>

1. <First thing to try>
2. <Second thing to try>
3. <Fallback / alternative to test if #1 fails>

---

## Log

> Add entries as you go. Timestamp optional but helpful for longer spikes.

- 

---

## Outcome

> Complete this when the spike concludes. Do not leave it blank.

**Answer**: <yes / no / partial — state it directly>

**Evidence**: <what you found that supports the answer — command output, a working example, a specific failure mode>

**Recommendation**: <what the project should do as a result>

**Status**: ✅ concluded / ⚠️ inconclusive / 🚫 abandoned
```

Tell the user the file is created and the timebox has started.

---

## Step 4 — Work through the spike

Ask: **"Do you want to work through this together, or do you have findings to add?"**

**If working together**: follow the approach list. Help the user interpret results. When something works, capture it in the Log with enough detail to reproduce. When something fails, note the failure mode specifically — "it failed" is not useful; "it fails with `AssertionError` in `wait_for_save` when block size > 3 chunks" is.

Push toward the answer — don't let the spike turn into open-ended exploration. If the timebox is approaching and the answer isn't clear, that is itself a finding.

**If adding findings**: take what they give you, help structure it into Log entries, and push toward the Outcome section.

---

## Step 5 — Conclude the spike

When the question is answered (or the timebox is up):

Fill in the **Outcome** section:
- State the answer directly — don't hedge if the evidence is clear
- Include concrete evidence (a working command, a measured number, a specific error)
- Set status to `✅ concluded`, `⚠️ inconclusive`, or `🚫 abandoned`

**On inconclusive**: if the timebox ran out without a clear answer, write what was learned and what a follow-up spike would need to investigate. Don't extend the timebox — open a new spike with a narrower question.

**On 'no' answers**: a 'no' is a valid and valuable outcome. Help the user think through what it means:
- Does this block a milestone? → flag it in `milestones.md` as a blocker
- Does it invalidate a roadmap assumption? → suggest `/ds-work-roadmap` to review
- Is there an alternative approach to spike? → suggest a follow-up spike with a new angle

Change **Status** at the top from `🔄 open` to match the outcome status.

---

## Step 6 — Connect back to the project

After concluding, ask: **"Does this outcome change anything in your milestones or roadmap?"**

If yes, help them identify the specific change:
- A task that's now unblocked → offer to note it in `milestones.md`
- A task that's now blocked → offer to add a blocker note
- An assumption in `roadmap.md` that needs updating → suggest `/ds-work-roadmap`
- A technology decision that should be recorded → suggest adding it to the roadmap's Key Decisions table
