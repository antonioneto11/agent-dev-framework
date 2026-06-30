---
name: agent-monitoring
description: Design production monitoring and alerting for an AI agent product — online quality signals, cost/latency tracking, bad-run review, and what to alert on. Use whenever the user wants to plan how to monitor an agent in production, decide what metrics or dashboards to track, set up alerting, or build a process for reviewing failed runs. Trigger on "monitoring", "observability in prod", "what should we track", "dashboards", "alerting", "how do we know if it breaks", even without those exact words. This is step 11 of the agent-dev-framework pipeline.
---

# Agent Monitoring

Offline evals (step 10) catch regressions before you ship. Monitoring catches what production
does that you didn't anticipate. For a non-deterministic agent in front of real users, this
isn't optional polish — it's how you find the failure cases that become your next eval cases.

## Conventions
Follow `skills/_conventions.md` (read-first order, `docs/` vs `artifacts/`, don't-clobber guard).

## Before you start
Read `PROJECT.md`, `artifacts/prd.md` (monitoring requirements section),
`artifacts/system-design.md` (observability layer), and `artifacts/data-model.md` (the trace
record + events). Monitoring is built on the traces and events already designed — this step
turns that raw capture into signals, not new plumbing.

## What to design

### 1. Online quality signals
The handful of numbers that tell you if the agent is healthy:
- Task success / failure rate (the headline metric from the PRD success definition).
- Tool error rate.
- Fallback / escape-hatch frequency (spiking = the agent is confused or the world changed).
- Cost per task and latency percentiles (p50/p95) — both from the trace record.
Keep it to the few that actually drive action. A dashboard nobody reads is wasted work.

### 2. Alerting
What conditions wake you up vs. what you review weekly. For a solo founder, bias toward few,
high-signal alerts: a sharp drop in success rate, a cost spike, or any occurrence of an
unacceptable failure mode (from the PRD). Everything else is a periodic review, not a page.

### 3. Bad-run review loop
The most valuable habit. A way to flag, surface, and read the full trace of failed or
low-quality runs. Each bad run reviewed should either become a new eval case (feed it back to
step 10) or get dismissed. This closes the loop: production failure → eval case → can't regress.

### 4. State-changing-action monitoring (if applicable)
Per `PROJECT.md`: if the agent takes real-world actions, monitor those specifically — what it
did, whether confirmation was sought, spend totals against limits. These deserve their own
view because they're where being wrong is most expensive.

## Output
Write `artifacts/monitoring.md` (apply the don't-clobber guard):

```
# Monitoring — [product]
## Online signals (each: what it measures · why it matters · healthy range)
## Alerts (condition → severity → action)
## Bad-run review loop (how runs get flagged, reviewed, fed back to evals)
## State-changing-action monitoring (if applicable)
## What we are deliberately NOT monitoring yet
```

End by recommending the single most important signal to wire up first — usually task success
rate plus a way to read failed traces — so monitoring exists on day one of the MVP, not later.
