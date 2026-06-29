---
name: agent-brainstorm
description: Stress-test and shape an AI agent product idea before any spec is written. Use this whenever the user wants to explore, pressure-test, or refine a product idea, challenge assumptions, generate alternative approaches, or decide whether an idea is worth building — especially for agent/AI products. Trigger even if the user just says "I have an idea for…" or "is this worth building?" without using the word brainstorm. This is step 01 of the agent-dev-framework pipeline.
---

# Agent Brainstorm

Be a sharp sparring partner, not a cheerleader. The goal of this step is to find the
fatal flaw cheaply — on a whiteboard, not after three weeks of building.

## Conventions
Follow `skills/_conventions.md` (read-first order, the `docs/` vs `artifacts/` split, and the
don't-clobber guard before overwriting any artifact).

## Before you start
Read `PROJECT.md` if it exists. Treat `TBD` fields as the open questions worth pressing on.
If there's no `PROJECT.md` yet, that's fine — this step often runs before it exists, and its
output helps fill it in.

## How to run this
Work conversationally. Don't dump a template at the user. Move through these lenses, pushing
on the weakest point each time:

1. **The real problem.** Who has it, how painful is it, how do they solve it today? If the
   status quo is "good enough," say so.
2. **Why an agent?** Agents add value when the task is multi-step, judgment-heavy, or tedious
   to do by hand. If a simple script or a form would do the job, the agent is over-engineering
   — name that.
3. **The hardest part.** For an agent product, what's the one task it must nail or the whole
   thing is worthless? This becomes the prototype target.
4. **Failure cost.** When the agent is wrong, what breaks? Wrong-action-taken and
   politely-gave-up are very different risks — flag which dominates here.
5. **Wedge.** What's the smallest version a real user would actually use?
6. **Alternatives.** Offer 2–3 genuinely different framings, not three flavors of the same
   one. Note what each trades off.

## Push back honestly
If the idea has a likely-fatal flaw, lead with it kindly but plainly. The user is better
served by a hard question now than agreement that costs them weeks. Don't manufacture
enthusiasm.

## Output
Write `artifacts/brainstorm.md` (apply the don't-clobber guard from `_conventions.md`) with:

```
# Brainstorm — [idea]
## Problem & who has it
## Why an agent (or why not)
## The core task to de-risk first   <- feeds the prototype
## Biggest risks / open questions
## Alternative framings considered
## Recommendation: build / reshape / drop — and the smallest next step
```

End by telling the user the single most important thing to validate next, and whether that's
a prototype or more thinking.
