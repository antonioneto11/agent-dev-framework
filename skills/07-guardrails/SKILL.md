---
name: agent-guardrails
description: Define what an AI agent does when things go wrong — the failure taxonomy and the handling policy for each failure mode (retry, fallback, escalate to human, hard refuse). Use whenever the user wants to make the agent safe and predictable under failure: listing the ways a run can go wrong, deciding the response to each, specifying when the agent must stop and ask a human, or hardening against bad/malicious input. Trigger on "guardrails", "failure modes", "what happens when it breaks", "fallback", "refusal", "escalation policy", "prompt injection", even without those exact words. Distinct from evals: evals *measure* quality, guardrails *govern behavior under failure*. Part of the agent-dev-framework pipeline — runs alongside prompt-and-contract, before the build plan. Use it even if the user only asks about one failure mode.
---

# Agent Guardrails

Evals tell you how often the agent is right. Guardrails decide what happens the rest of the
time. These are different jobs, and conflating them is why agents that "passed evals" still
do dangerous things in production: the eval set never contained the timeout, the malformed
model output, or the adversarial input. This step writes the policy that governs failure —
deterministic rules that run regardless of what the model decides.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard, tone).

## Before you start
Read `PROJECT.md`, `artifacts/system-design.md` (its failure-modes section is the seed),
and `artifacts/prompt-and-contract.md` (the tools, the I/O contract, and the response shape
that must be able to *carry* an escalation or error — guardrails depend on it existing).

## The core distinction to hold onto
A guardrail is a rule the *system* enforces, not a hope about how the *model* behaves.
"The prompt asks the agent to escalate when unsure" is a behavior; "if confidence < threshold
OR a rule fires, the run returns an escalation and does not act" is a guardrail. Prefer
guardrails that don't depend on the model cooperating, especially for anything that spends,
sends, or writes.

## What to define

### 1. The failure taxonomy
Enumerate the ways a run can go wrong. Cover at least these families (cut any that don't
apply, add domain-specific ones):
- **Model failures** — malformed/unparseable output, schema violation, refusal, low
  confidence, hallucinated content presented confidently.
- **Tool failures** — timeout, error response, empty result, rate limit, partial success.
- **Input failures** — missing required fields, out-of-scope request, ambiguous request,
  adversarial input / prompt injection.
- **Loop failures** — exceeds max steps, stuck/oscillating, runs over a cost or latency budget.
Give each a short stable name; the data/eval and monitoring steps will reference these names.

### 2. The handling policy (one decision per failure mode)
For each failure in the taxonomy, specify the response. The vocabulary is small on purpose:
- **Retry** — and the bound (how many times, with what backoff). Unbounded retries burn money.
- **Fallback** — a cheaper/simpler path or a secondary model acting as a check. Say what's lost.
- **Escalate** — stop and return control to a human, via the escalation shape in the I/O
  contract. Define *what the human sees* — enough context to act, not a raw stack trace.
- **Refuse** — return a safe, explicit decline. Define the user-facing message shape.
- **Degrade** — return a partial/best-effort result *clearly marked as such*, never silently.
The cardinal rule: **never fabricate to cover a failure.** A confident-looking wrong answer
is worse than an honest escalation. If the product's whole thesis is escalation-over-
fabrication, this table is where it becomes enforceable rather than aspirational.

### 3. Guardrails on state-changing actions
For every state-changing tool from the contract, specify the gate: confirmation step,
dry-run, spend/rate ceiling, idempotency, and an allow/deny boundary on what the agent may
never do unprompted. These are hard stops, not prompt suggestions.

### 4. Adversarial / abuse hardening
Name the realistic attempts to make the agent misbehave (injection via tool results or user
input, jailbreak of the refusal rules, data-exfil prompts) and the concrete mitigation for
each. Tie a few of these to seed cases the eval step should carry, so hardening is verifiable.

## Output
Write `artifacts/guardrails.md` (apply the don't-clobber guard):

```
# Guardrails — [product]
## Failure taxonomy (named failure modes, grouped)
## Handling policy (a table: failure mode → response → bound/detail → what the human/user sees)
## State-changing action gates (per tool: the hard stop)
## Adversarial hardening (attempt → mitigation → seed case to verify)
## The never-do list (actions forbidden regardless of model output)
## Open questions
```

End by naming the single failure mode most likely to slip through silently in production —
the one with no detection and no defined response yet — and the cheapest guardrail or trace
signal that would catch it.
