---
name: agent-system-design
description: Design the high-level system architecture for an AI agent product — the agent loop, its tools, control flow, where state lives, and the observability/tracing layer. Use whenever the user wants to architect or design the shape of an agent system, decide how components fit together, plan the agent's control flow, or figure out how to make agent runs observable. Trigger on "system design", "architecture", "how should this be structured", "design the agent", even without those exact words. This is step 04 of the agent-dev-framework pipeline.
---

# Agent System Design

This step defines the *shape* of the system — components and how they talk — not
implementation detail (that's the tech spec). For an agent product, the design has one
non-negotiable that ordinary apps lack: **the tracing/observability layer is part of the
architecture, not an add-on.** You cannot debug or eval an agent run you didn't capture.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md`, `artifacts/prd.md`, and `artifacts/competitive.md`. Pull the core task,
autonomy level, tools, model, and constraints from there. The PRD's monitoring requirements
section directly drives the tracing design below.

## Bias toward simplicity
A solo founder is almost always better served by "works and is changeable" than "scales to
millions." Design for the current stage. If you find yourself adding a queue, a cache, or a
microservice the MVP doesn't need, name it as gold-plating and cut it. Flag over-engineering
out loud.

## What to design

### 1. The agent loop
The heart of it. Spell out:
- Trigger → how a run starts (user message, event, schedule).
- The control flow: single LLM call, or a loop of reason → call tool → observe → repeat?
  Planner/executor? Keep it the simplest thing that does the job.
- Stop conditions and max steps (agents that don't bound their loops burn money and hang).
- Where the **escape hatch** is: when the agent is unsure, does it stop and ask, or proceed?
  (This must match the autonomy decision in the PRD.)

### 2. Tools / actions
For each tool the agent can call: what it does, its inputs/outputs, and — critically —
whether it's **read-only or state-changing**. Per `PROJECT.md`, if the agent takes real-world
actions (sends, writes, spends), those tools need guardrails called out here: confirmation
steps, dry-run modes, spend limits, idempotency.

### 3. State & memory
What the agent needs to remember within a run vs. across runs. Where conversation/session
state lives. Keep persistent state minimal at this stage.

### 4. The observability layer (MANDATORY)
Design how every run is traced. A run's trace must capture: inputs, each tool call + its
result, intermediate reasoning/decisions, the final output, and tokens/latency/cost per run.
Decide where traces are written (even a simple append-only log or a table is fine to start)
and how a single run is identified (a run_id threaded through everything). This feeds steps 06
(data) and 07 (monitoring) directly — design it now so it isn't bolted on later.

### 5. Failure modes
What happens when a tool errors, the model returns garbage, or a run exceeds its step budget?
Define fallbacks and the safe-default behavior.

## Output
Write `artifacts/system-design.md` (apply the don't-clobber guard):

```
# System design — [product]
## Overview (one paragraph + a simple component sketch in text/ASCII)
## The agent loop (control flow, stop conditions, escape hatch)
## Tools / actions (each: purpose · read-only or state-changing · guardrails)
## State & memory
## Observability layer (what a trace captures, where it's stored, run_id)
## Failure modes & fallbacks
## Deliberately NOT building yet (anti-gold-plating list)
## Open questions
```

A diagram helps here. If a visual would make the architecture clearer, offer to render one.

End by naming the single riskiest part of the architecture and the cheapest way to validate
it before writing the full tech spec.
