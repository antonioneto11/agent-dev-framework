---
name: agent-ux-spec
description: Specify the interface of an AI agent product — the screens, the primary user flows, the empty/loading/error/escalation states, and above all how the agent's uncertainty and its requests for human input surface to the user. Use whenever the user needs to turn an architecture into something with an actual interface: defining the prototype's screens, mapping the core flow, deciding how an escalation or clarification is shown, designing how a human takes over, or specifying the states an engineer must build. Trigger on "UX", "UI", "screens", "user flow", "wireframe", "what does the user see", "interaction design", "how does escalation show up", even without those exact words. For an agent the interaction *is* the product — invoke this even when the user is focused on backend work. Slot between the tech spec (step 05) and the build plan (step 12) of the agent-dev-framework pipeline.
---

# Agent UX Spec

For an agent product the interaction is not a skin over the system — it *is* the product.
Whether users trust the agent, catch its mistakes, and let it act is decided entirely at the
interface. This step defines what the user sees and does, so an engineer can build a screen
instead of inventing one. It is *not* visual design (colors, typography — that's a separate
concern); it's the contract of screens, flows, and states.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard, tone).

## Before you start
Read `PROJECT.md`, `artifacts/prd.md` (the core task and the autonomy decision drive the
interaction), and `artifacts/prompt-and-contract.md` if it exists — the response schema's
terminal states (success / escalation / error) map directly onto the states you must design.
The interface has to render every state the contract can produce.

## Scope to the prototype's job
For an MVP, the UX is whatever is needed to validate the core thesis — usually one or two
screens, not a product. Resist designing settings pages, dashboards, and onboarding the
prototype doesn't need to learn what it's trying to learn. Name what you're leaving out.

## What to specify

### 1. The core flow (one primary path, start to finish)
Walk the single most important user journey step by step: what the user does, what the agent
does in response, what the user sees at each beat, and how the journey ends. This is the spine;
get it concrete before anything else. One flow done fully beats five sketched.

### 2. Screens / surfaces
List the surfaces the core flow needs (a surface might be a screen, a panel, a message in a
thread). For each: its purpose, the key elements on it, and the user actions available. Keep
the inventory to what the core flow actually touches.

### 3. The full set of states (this is where agent UX is won or lost)
Every surface that shows agent output must handle more than the happy path. Specify each:
- **Loading / working** — agents are slow; what does waiting look like, and is progress or
  intermediate reasoning shown?
- **Empty** — before the first run, or no result.
- **Success** — the normal result, and how the agent's output is presented (and, where it
  matters, how its confidence or sources are conveyed).
- **Escalation / clarification** — *the most important state.* When the agent stops and asks
  rather than guessing, how does that surface? It must be visibly different from a normal
  answer, make the question obvious, and give the user an easy way to respond. If the product's
  thesis is escalation-over-fabrication, **this is the screen that has to be right** — the whole
  value is making "the agent asked me instead of making something up" legible and reassuring
  rather than confusing or like a failure.
- **Error / failure** — when something breaks (mapped from the guardrails handling policy),
  what the user sees and what they can do next.
- **Human takeover** — if a human can correct, approve, or take over a run, how that hand-off
  looks from the user's side.

### 4. Trust & legibility affordances
The cross-cutting elements that make an agent trustworthy: showing what the agent is about to
do before it does it (especially for state-changing actions — the confirmation UI lives here),
making sources/reasoning inspectable, and giving the user a way to undo or correct. Tie these to
the state-changing tools from the contract.

## Output
Write `artifacts/ux-spec.md` (apply the don't-clobber guard):

```
# UX spec — [product]
## Core flow (numbered walk-through: user action ↔ agent response ↔ what's on screen)
## Surfaces (each: purpose · key elements · user actions)
## States (per output surface: loading · empty · success · escalation · error · takeover)
## Trust & legibility affordances (preview/confirm · inspect · undo)
## Deliberately NOT designing yet (out-of-scope surfaces)
## Open questions
```

A picture helps here — if a wireframe or flow sketch would make the interaction clearer than
prose, offer to render one (this spec defines structure and behavior; the rendering is just an
aid, not visual design).

End by naming the single interaction the prototype most depends on getting right — almost always
the escalation state for an agent whose thesis rests on it — and the cheapest way to put that one
screen in front of a real user before the build plan turns this into tasks.
