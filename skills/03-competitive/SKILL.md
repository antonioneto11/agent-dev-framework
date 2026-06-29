---
name: agent-competitive
description: Produce a competitive / market analysis brief for an AI agent product. Use whenever the user wants to size up competitors, validate positioning, decide where to differentiate vs. reach parity, or check whether a space is already crowded. Trigger on "competitive analysis", "who else does this", "market check", "how do we position", even without those exact words. This is step 03 of the agent-dev-framework pipeline.
---

# Agent Competitive Analysis

The output is a decision, not a catalog: **where do we differentiate, and where do we just
need parity?** Everything else serves that.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md` and `artifacts/prd.md`. Anchor on the core task and the user's
job-to-be-done — compare on *that*, not on feature checklists.

## Gathering information
If web search is available, use it to find current competitors and recent moves — agent/AI
tooling shifts fast, so prefer recent sources and note the date. If web search is not
available, work from what the user knows and clearly mark gaps as "needs verification" rather
than guessing. Don't invent competitor facts.

## What to compare on
For each competitor: how they solve the core job, their autonomy level (do they take actions
or just suggest?), where they're strong, where they're weak/annoying, and pricing/positioning
if known. For an agent product specifically, note **trust and reliability posture** — how they
handle being wrong — since that's often the real battleground, not features.

## Output
Write `artifacts/competitive.md` (apply the don't-clobber guard):

```
# Competitive brief — [product]
## The job we're competing on
## Landscape (2–6 competitors)
For each: approach · autonomy · strengths · weaknesses · trust posture · pricing
## Gaps in the market
Where is everyone weak? That's the opening.
## Differentiate vs. parity
- Differentiate on: (the 1–2 things we'll be clearly best at)
- Parity on: (table stakes we just can't be bad at)
- Ignore for now: (things that look important but aren't, for our wedge)
## Positioning one-liner
## Sources & confidence
```

End with the single sharpest strategic implication for the build — usually "differentiate on
X, don't waste effort matching Y."
