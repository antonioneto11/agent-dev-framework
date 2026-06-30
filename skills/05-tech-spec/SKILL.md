---
name: agent-tech-spec
description: Write a detailed technical specification for an AI agent product — the how behind the system design. Use whenever the user wants to spec out implementation detail, define APIs/interfaces, pin down the data model and dependencies, or turn an architecture into something buildable. Trigger on "tech spec", "technical spec", "implementation plan", "how do we build this", even without those exact words. This is step 05 of the agent-dev-framework pipeline.
---

# Agent Tech Spec

Where the system design said *what shape*, the tech spec says *how, in detail* — concrete
enough that you (or a future you, or a contractor) could build from it. The most valuable
section for a solo founder is often the **deliberately-not-building** list, because it's what
stops a one-person MVP from sprawling.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md`, `artifacts/prd.md`, and `artifacts/system-design.md`. The stack, datastore,
model, and integrations come from `PROJECT.md` — don't invent them. If a needed fact is `TBD`,
flag it as a decision to make rather than guessing a default.

## Calibrate the depth to the stack
This skill is stack-agnostic. Read the platform/language/datastore from `PROJECT.md` and write
the spec in those terms. Don't assume a web-app/Postgres default unless `PROJECT.md` says so.

## What to specify

```
# Tech spec — [product]

## Components
Each component from the system design, now with concrete responsibilities and boundaries.

## Interfaces / APIs
The contracts between components and any external-facing API. Request/response shapes,
the agent's tool-call signatures, error formats.

## Agent implementation detail
- Prompt structure / system prompt responsibilities (not the full prompt, its shape).
- How tools are defined and dispatched.
- Loop control in code terms: max steps, timeout, retry policy.
- How the escape hatch / human-in-the-loop is implemented for state-changing actions.

## Data model
The entities and their relationships at implementation level. (The deeper data + event
design lives in step 10; here, the schema the code needs.)

## Dependencies
Libraries, services, model provider, and anything with a cost or rate limit. Note version
constraints and any lock-in risk.

## Observability implementation
How the tracing from the system design is actually written: log/trace format, the run_id
plumbing, what gets recorded at each step.

## Security & secrets
Where keys live, how state-changing actions are authorized, PII handling per PROJECT.md.

## Deliberately NOT building yet
The explicit cut-list. For each: why it's safe to defer. This is the anti-sprawl section.

## Open questions / decisions needed
```

## Principles
- Concrete over clever. Favor the boring, changeable implementation.
- Treat the output as a strong first draft to react to, not a decision. The spec can't know
  constraints it wasn't told (budget quirks, a datastore limitation) — make the user the
  reviewer, not the rubber stamp. Say this explicitly when handing it over.

## Output
Write `artifacts/tech-spec.md` (apply the don't-clobber guard). End by listing the decisions
still marked `TBD` that block starting the build, so the user knows exactly what to resolve.
