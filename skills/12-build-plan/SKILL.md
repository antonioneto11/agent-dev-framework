---
name: agent-build-plan
description: Turn the specs into a sequenced, scoped build plan for an AI agent product — milestones, the smallest shippable MVP, and what to build in what order. Use whenever the user wants to plan the actual build, sequence the work, decide what goes in the MVP vs. later, break a spec into tasks, or figure out where to start coding. Trigger on "build plan", "what do I build first", "sequence the work", "MVP scope", "break this into tasks", "roadmap", even without those exact words. This is step 08 of the agent-dev-framework pipeline.
---

# Agent Build Plan

The final step: turn everything upstream into an ordered plan a solo founder can actually
execute. The discipline here is ruthless scoping — the plan's job is to get to the *smallest
thing a real user can use*, with the agent's eval/trace backbone in place, and nothing more.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md` and all prior artifacts: `prd.md`, `system-design.md`, `tech-spec.md`,
`data-model.md`, `monitoring.md`, and the eval set. The deliberately-not-building lists from
the tech spec and system design are your scoping guide — respect them.

## The MVP gate (apply this hard)
An agent prototype becomes an MVP only when it has **traces and a starter eval set**. So the
build plan must sequence those early, not last. A plan that ships agent features to users
before observability and evals exist is sequenced wrong — fix it.

## How to sequence
Order by **risk and dependency**, not by what's easy:
1. **De-risk first.** If feasibility of the core agent task isn't already proven (prototype),
   that's task one — prove it before building scaffolding around it.
2. **Backbone early.** Tracing + a minimal eval harness come before feature breadth, because
   everything after is safer when you can measure it.
3. **Thin vertical slice.** One complete path through the product end to end, for one real
   use case, before widening.
4. **Then widen** to the rest of the MVP scope.
5. **Defer** everything on the not-building lists explicitly.

## Output
Write `artifacts/build-plan.md` (apply the don't-clobber guard):

```
# Build plan — [product]
## MVP definition (the smallest thing a real user can use)
## Sequenced milestones
For each: goal · what's in · what's explicitly deferred · how you'll know it's done
(tie "done" to eval pass-rate or a concrete check, not vibes)
## Prototype status
Is core-task feasibility proven? If not, that's milestone 0.
## Observability & eval backbone
Where in the sequence traces + starter evals land (must be early).
## Explicitly deferred past MVP
## Biggest execution risk & how the plan mitigates it
```

## Principles
- Each milestone should be independently shippable or at least demoable.
- Tie "done" to the eval set where possible — for an agent, "done" means "passes these cases,"
  which is more honest than "looks finished."
- Keep it realistic for one person. If the plan needs a team, say so and suggest what to cut.

End by stating the single first task the user should start on Monday morning, concretely.
