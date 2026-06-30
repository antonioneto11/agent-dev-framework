---
name: agent-prompt-and-contract
description: Pin down the literal source code of an AI agent — the system prompt, the tool/function schemas, and the structured input/output contracts. Use whenever the user is ready to turn an architecture into something an engineer can type against: writing or refining the agent's system prompt, defining the JSON schemas for each tool the agent calls, specifying the request/response contract, or locking the structured-output format. Trigger on "system prompt", "tool schema", "function calling", "I/O contract", "output schema", "what does the agent actually receive and return", even without those exact words. This is the code-ready keystone of the agent-dev-framework pipeline — slot it right after the tech spec (step 05) and before the build plan (step 12). Use it even if the user only asks for one of these three artifacts.
---

# Agent Prompt & Contract

The tech spec describes the system in prose; this step writes the parts an engineer
copies verbatim into code. For an LLM agent, three artifacts *are* the source code:
the **system prompt**, the **tool schemas**, and the **I/O contract**. If these are
vague, two engineers build incompatible halves and the agent's behavior is undefined.
Everything here must be concrete enough to paste into a codebase without a follow-up
question.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard, tone).

## Before you start
Read `PROJECT.md`, `artifacts/system-design.md`, and `artifacts/tech-spec.md`. The agent
loop, the tool list (read-only vs. state-changing), the model choice, and the escape-hatch
decision all come from there. This step makes them literal — it doesn't re-decide them.

## Bias toward the simplest contract that works
A solo founder ships faster with one well-shaped tool than five speculative ones. Don't
invent tools the loop doesn't call or fields the caller never reads. If a schema field
exists "just in case," cut it and name the cut. Over-specified contracts rot.

## What to produce

### 1. The system prompt (write it in full, not a description of it)
This is the single highest-leverage artifact. Write the actual prompt, not a summary.
It should establish, at minimum:
- **Role & task** — what the agent is, in one or two sentences.
- **The core behavioral contract** — the non-negotiable rules. For most agents the
  load-bearing one is *what to do under uncertainty*: ask/escalate vs. proceed. This must
  match the escape-hatch decision from system design. If the product's thesis is
  "escalate rather than fabricate," the prompt must say so explicitly and unambiguously —
  this is exactly where that behavior is won or lost.
- **Tool-use instructions** — when to call which tool, and when NOT to.
- **Output format instructions** — point at the output schema below and require it.
- **Refusal / out-of-scope behavior** — what the agent declines and how.
Keep it tight. State *why* a rule matters where it helps the model comply, rather than
stacking bare imperatives.

### 2. Tool / function schemas (one per tool, fully typed)
For every tool the agent can call, write the actual schema the model will see:
- name, one-line description (the model reads this to decide when to call it),
- typed input parameters (name · type · required? · what it means · constraints),
- the return shape the tool produces.
Mark each tool **read-only or state-changing** (carry this from system design). For
state-changing tools, the schema is also where you encode safety affordances the guardrails
step will rely on: a `dry_run` flag, an idempotency key, an explicit confirmation parameter.
Use whatever schema dialect the stack uses (JSON Schema / the model provider's tool format);
be consistent across all tools.

### 3. The I/O contract (the agent's outer boundary)
What a caller hands the agent and what it gets back, typed end to end:
- **Request schema** — the structured input to a run (not the prose prompt; the envelope:
  user/session identifiers, the task payload, any context the caller supplies).
- **Response schema** — the structured output of a run. Critically, the response must be
  able to represent *every* terminal state, not just success: a normal answer, an
  escalation/clarification request, and an error. A response shape that can only express
  success forces the escalation behavior to leak out as malformed text — design it in.
- **Structured-output schema** — if the agent emits structured data (the actual deliverable:
  a portfolio, a classification, a plan), give its exact shape, with required vs. optional
  fields and types. If you ask the model for JSON, state in the prompt that it must return
  *only* that JSON, and define the schema here so parsing is deterministic.

### 4. Worked example (at least one full round-trip)
Show one concrete request → (tool calls, if any) → response, with real-looking values, for
a normal case. Then show one for the escalation/uncertainty case, so the contract is proven
to carry the agent's most important behavior, not just the happy path. Examples catch
schema gaps that prose hides.

## Output
Write `artifacts/prompt-and-contract.md` (apply the don't-clobber guard):

```
# Prompt & contract — [product]
## System prompt (the full, literal prompt — fenced, copy-ready)
## Tool schemas (one block per tool: purpose · read-only/state-changing · typed I/O)
## I/O contract
### Request schema
### Response schema (success · escalation · error — all representable)
### Structured-output schema (the deliverable's exact shape)
## Worked examples (one normal round-trip · one escalation round-trip)
## Deliberately NOT included (cut tools/fields and why)
## Open questions
```

End by naming the one place where prompt and contract could disagree at runtime — the
behavior the prompt promises but the response schema can't yet express, or vice versa — and
the cheapest way to close that gap before the build plan turns this into tasks.
