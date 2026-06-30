---
name: agent-security-and-constraints
description: Pin down the non-functional constraints an AI agent's architecture must respect — who may call it (authz), how data and PII flow and are logged vs. redacted, the compliance posture, and the cost/latency/token budget as enforceable targets rather than after-the-fact hopes. Use whenever the user needs to make the agent safe to ship and affordable to run: defining access control, deciding what gets logged or redacted, handling PII/regulated data, setting the per-run cost ceiling or p95 latency target, or choosing a model-tier routing policy. Trigger on "security", "auth", "permissions", "PII", "data handling", "compliance", "LGPD/GDPR/HIPAA/SOC2", "cost budget", "latency target", "token budget", "model routing", even without those exact words. Most projects skip this until it hurts — invoke it proactively. Part of the agent-dev-framework pipeline, before the build plan. Use it even if the user only asks about one of these.
---

# Agent Security & Constraints

These are the rules the architecture must obey, separate from what the agent *does*. They get
skipped because they're nobody's feature — until an auditor, a bill, or a breach makes them
everybody's problem. For a regulated or data-sensitive product they're load-bearing from day
one, and they shape code (caching, redaction, model routing), so they belong *before* the
build, not in a post-launch cleanup. Two clusters live here because engineers author and read
them together: **security/data** and **cost/latency**.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard, tone).

## Before you start
Read `PROJECT.md` (its compliance/regulatory and hosting fields), `artifacts/system-design.md`
(the observability layer — it decides *what* gets traced; this step decides what's safe to
trace), and `artifacts/prompt-and-contract.md` (the data the agent receives and the tools it
calls determine the attack surface and the cost drivers).

## Right-size to the stage and the domain
A throwaway internal tool needs a fraction of this; a regulated cross-border product needs all
of it. Match the rigor to `PROJECT.md`'s domain. Don't bolt enterprise security onto a
prototype — but don't wave away PII handling on something touching real user or financial data
because it's "just an MVP." Name which parts you're deliberately deferring and why.

## Part A — Security & data

### 1. Authn / authz
Who (or what) may invoke the agent, and how identity is established. Per-tool authorization
if some actions need higher privilege than others. The principle of least privilege applied to
the agent itself: what it can reach, and what it's walled off from.

### 2. Data flow & classification
Trace the sensitive data through a run: what enters, where it goes (model provider, tools,
logs, storage), where it leaves the trust boundary, and where it rests. Classify it (public /
internal / PII / regulated). The key question: **does sensitive data cross a boundary it
shouldn't** — e.g., into a third-party model or an external tool — and if so, what's the
control (redaction, tokenization, a provider with the right data-handling terms, on-region
hosting)?

### 3. Logging, redaction & retention
The observability layer wants to capture everything; compliance wants the opposite. Resolve it
here: what's logged in full, what's redacted or hashed before it's written, what's never
logged, and how long traces are retained. Get this right *before* logging code is written —
retrofitting redaction onto existing logs is painful and leaky.

### 4. Compliance posture
Name the regimes that apply (from `PROJECT.md`) and the concrete obligations each imposes on
*this* system — data residency, consent, right-to-deletion, audit trail, model-decision
explainability. Translate each into a system requirement, not a citation. If a regime forces a
hosting region or a logging behavior, say so and link it to the relevant control above.

### 5. Secrets
Where API keys / credentials live, how they're injected, and the rule that they never appear in
prompts, traces, or model context.

## Part B — Cost & latency (a budget, set before build, not measured after)

### 6. Per-run budgets
The numbers the architecture must respect: max tokens per run, a per-request cost ceiling, and
an acceptable p95 (and worst-case) latency. Monitoring (later) *tracks* these; here you *set*
them, because they dictate design choices — caching, batching, context trimming, max loop steps.

### 7. Model-tier routing policy
When the expensive/strong model is justified and when a cheaper/faster one suffices (e.g., a
small model for classification or a judge, the strong one for the core reasoning). This is one
of the largest cost levers; decide the rule now so the code is built around it rather than
refactored into it.

## Output
Write `artifacts/security-and-constraints.md` (apply the don't-clobber guard):

```
# Security & constraints — [product]
## Authn / authz (who may call · per-tool privilege · least-privilege for the agent)
## Data flow & classification (what crosses which boundary · the control)
## Logging, redaction & retention (logged · redacted · never-logged · retention)
## Compliance posture (regime → concrete system requirement)
## Secrets handling
## Per-run budgets (tokens · cost ceiling · p95/worst-case latency)
## Model-tier routing policy
## Deliberately deferred (what's out of scope for this stage and why)
## Open questions
```

End by naming the single constraint most likely to force a rework if discovered late — the PII
path, the compliance requirement, or the cost driver that quietly dictates the architecture —
and the cheapest way to validate it now.
