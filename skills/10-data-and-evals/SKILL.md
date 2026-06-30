---
name: agent-data-and-evals
description: Design the data model, event logging, and the starter eval set for an AI agent product. Use whenever the user wants to define what data to store, plan event/trace logging from day one, build an eval set or test cases for an agent, or set up how agent quality will be measured. Trigger on "data model", "what should we log", "evals", "test cases for the agent", "how do we measure quality", even without those exact words. This is step 10 of the agent-dev-framework pipeline, and the most important one for an agent product.
---

# Agent Data & Evals

Two jobs, tightly linked: define the **data** the product stores (including the traces that
make the agent observable) and build the **eval set** that defines what "good" means. For an
agent product this is the step that most determines whether you can improve the product at
all — without traces you can't debug, and without evals you can't tell if a change helped or
silently broke something.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md`, `artifacts/prd.md`, `artifacts/system-design.md`, and
`artifacts/tech-spec.md`. The PRD's success definition and eval strategy, and the system
design's observability layer, are the direct inputs here.

## Part A — Data & event logging

Define:
1. **Entities** — what the product stores, derived from the tech-spec data model. What must
   be stored vs. what can be derived (don't store what you can compute).
2. **Events to capture from day one** — this is the thing founders most regret skipping: you
   can't analyze data you never logged. List the events that later become your analytics and
   your eval material (runs started/completed, tool calls, failures, user corrections,
   escape-hatch activations).
3. **The trace record** — the concrete shape of one agent run's trace (from the system
   design's observability layer): run_id, inputs, each step's reasoning + tool call + result,
   final output, tokens, latency, cost, outcome.
4. **Retention & privacy** — per `PROJECT.md`: PII handling, how long traces live, anything
   regulated.

## Part B — The eval set

The eval set is the product's test suite. Build a real, runnable starter set — even 20–50
hand-written cases catches most regressions.

For each eval case define:
- The input (a real task the agent should handle).
- The expected outcome OR a grading rubric.
- The check type:
  - **Deterministic** — did it call the right tool? return valid output? stay under budget?
    These are cheap and should cover as much as possible.
  - **Judgment-based** — was the reasoning sound? the tone right? the action appropriate?
    These need an LLM-as-judge or human grading. Note which.
- Whether it's an **offline** eval (run before shipping a change) or an **online** one
  (sampled from real traffic).

Cover the unacceptable failure modes from the PRD explicitly — there should be an eval case
for each hard line the agent must never cross, especially for state-changing actions.

Use `assets/eval-cases.template.jsonl` as the starting format.

## The growth rule (state this to the user)
Every time production surfaces a new failure, it becomes a new eval case so the same mistake
can't silently return. The eval set is a permanent, growing asset — treat it like code.

## Output
- Write `artifacts/data-model.md` (entities, events, trace record, retention) — apply guard.
- Write `artifacts/evals/eval-cases.jsonl` (the starter set) — apply guard.
- Write `artifacts/evals/README.md` explaining how to run/grow them.

End by stating how many eval cases you created, which unacceptable failure modes are covered,
and — honestly — which important behaviors are NOT yet covered and should be added.
