---
name: agent-prd
description: Write a PRD / product spec for an AI agent product. Use whenever the user wants to turn an idea into a structured product requirements doc, define what to build and why, set success metrics, or scope a feature — especially for agent/AI products where evals and monitoring must be designed in from the start. Trigger on "write a PRD", "product spec", "scope this feature", or "what should we build". Supports a thin mode (just enough to prototype) and a full mode. This is step 02 of the agent-dev-framework pipeline.
---

# Agent PRD

A PRD says **what** and **why**, never **how** (that's the tech spec). For an agent product,
two sections that are optional elsewhere are mandatory here: the **agent success definition**
and the **eval + monitoring strategy** — because an agent's output is non-deterministic, so
"is it working?" is itself a measurement problem you must define before building.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md` and `artifacts/brainstorm.md` if they exist. Pull the core task, autonomy
level, and failure costs from there rather than re-asking. Fill gaps from `PROJECT.md`; only
ask the user about things neither file answers.

## Two modes — pick based on PROJECT.md stage
- **Thin** (stage = prototype): write only sections 1–4 below. Just enough to know the core
  task so a prototype can de-risk it. Don't over-invest before feasibility is proven.
- **Full** (stage = building MVP): all sections.

State which mode you're using in one line at the top of the output.

## Sections

ALWAYS use this template:

```
# PRD — [product] (mode: thin|full)

## 1. Problem & users
Who, what pain, how they solve it today.

## 2. Goals & non-goals
Bullets. Non-goals are as important as goals — they prevent scope creep.

## 3. The core agent task
The one or two tasks the agent must perform. Concrete input → expected outcome.

## 4. Agent success definition        <- MANDATORY
- What "works" means concretely (not "users are happy").
- Target task success rate.
- Autonomy: when unsure, does it stop-and-ask or proceed? (pull from PROJECT.md)
- Unacceptable failures — hard lines it must never cross.

## 5. Eval strategy                    <- MANDATORY (full mode)
- What you'll measure quality against.
- Which checks are deterministic (right tool called? valid output?) vs.
  judgment-based (sound reasoning? right tone? — needs LLM-judge or human).
- Offline (before shipping a change) vs. online (sampled from real traffic).
- Note: the actual eval cases get built in step 10; here you define the strategy.

## 6. Monitoring requirements          <- MANDATORY (full mode)
- What must be observable in production: task success/failure, tool error rate,
  fallback/escape-hatch frequency, cost per task, latency.
- State plainly: every agent run must be fully traced (this becomes a design
  constraint in steps 04 and 06).

## 7. Scope & milestones
MVP boundary. What's explicitly deferred past v1.

## 8. Open questions
```

## Principles
- Keep it tight. A solo founder's PRD is a thinking tool, not a deliverable for a committee.
- If the agent takes real-world actions (per PROJECT.md), section 4's unacceptable-failures
  and section 6's monitoring must be heavier — high-autonomy agents need guardrails specified
  here, not discovered later.
- Make the user the decision-maker. Where two scopings are defensible, present both and ask.

## Output
Write `artifacts/prd.md` (apply the don't-clobber guard). End by flagging the riskiest assumption in the PRD and whether the
next step is a prototype (thin mode) or system design (full mode).
