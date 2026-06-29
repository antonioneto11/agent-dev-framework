# Agent Dev Framework

A reusable, project-agnostic pipeline of Claude Skills for taking an AI **agent product**
from idea to shippable MVP. Built for a solo founder who reuses the same workflow across
multiple projects, runnable from **Claude Code** and **Claude Cowork** equally.

## The core idea

Two design rules make this portable:

1. **Skills stay generic.** No SKILL.md hardcodes your stack, domain, or product. All
   project-specific facts live in one file — `PROJECT.md` — that each skill reads.
2. **Steps chain by files, not memory.** Each step reads the previous step's artifact from
   `artifacts/` and writes its own. That's what makes it a *workflow* instead of eight
   disconnected prompts, and it behaves identically in Claude Code and Cowork because it's
   just files on disk.

So: the same `skills/` folder serves every project. `PROJECT.md` is the only thing that
changes per project.

## The pipeline

```
Brainstorm ──► PRD(thin) ──────────────► PROTOTYPE  (de-risk the core task; throwaway)
                  │
                  ▼
PRD(full) ─► Competitive ─► System design ─► Tech spec ─► Data+Evals ─► Monitoring ─► Build plan ──► MVP
```

| # | Skill | Produces | Why it matters for an agent |
|---|-------|----------|------------------------------|
| 01 | brainstorm | `artifacts/brainstorm.md` | Stress-test the idea before committing |
| 02 | prd | `artifacts/prd.md` | What + why; **includes agent success definition, eval strategy, monitoring needs** |
| 03 | competitive | `artifacts/competitive.md` | Where to differentiate vs. reach parity |
| 04 | system-design | `artifacts/system-design.md` | The agent loop, tools, **and the tracing layer** |
| 05 | tech-spec | `artifacts/tech-spec.md` | How, in detail; deliberately-not-building list |
| 06 | data-and-evals | `artifacts/data-model.md`, `artifacts/evals/` | Traces, event logging, the starter eval set |
| 07 | monitoring | `artifacts/monitoring.md` | Online signals, alerting, bad-run review |
| 08 | build-plan | `artifacts/build-plan.md` | Scoped to the smallest shippable MVP |

## Prototype vs. MVP

These aren't steps — they're depths.

- **Prototype** forks off after a *thin* PRD (01→02). Throwaway: hardcoded inputs, a couple
  real tasks, no monitoring infra. Its only job is to answer "can the agent do the core task
  at acceptable quality?" You eyeball the runs — that informal grading seeds your eval set.
- **MVP** is the output of the full pipeline (02→08), scoped tight. The gate between the two:
  **a prototype becomes an MVP only once it has traces and a starter eval set.** A
  non-deterministic agent in front of real users without those is flying blind.

## How to use it on a new project

1. Copy `PROJECT.template.md` to your project as `PROJECT.md` and fill it in.
2. Make the skills available to your tool (paths verified against Anthropic docs, mid-2026 —
   re-check if conventions have moved):
   - **Claude Code** reads skills from two places: `~/.claude/skills/` (personal, available
     across *all* your projects) and `<project>/.claude/skills/` (project-scoped, committed to
     the repo). When a name collides, personal overrides project. Each skill is its own folder
     containing `SKILL.md`. Best home for steps 04–08 — they're in your codebase and can run
     scripts.
   - **Cowork / Claude.ai** does **not** use a filesystem folder. You upload/enable skills
     through the UI: Customize → Skills in the sidebar (requires code execution enabled). Best
     for steps 01–03 and 06's eval design — the multi-document planning work. The *same*
     `SKILL.md` files work in both surfaces unchanged.
3. Walk the pipeline. Each skill reads `PROJECT.md` + the prior artifact, writes the next.
4. Each time production surfaces a new failure, add it as an eval case. The eval set is the
   product's test suite — grow it forever.

### Recommended setup: one repo, linked into many projects

Treat this framework like a dotfiles repo — small, opinionated, version-controlled.

```
~/dev/agent-dev-framework/        <- source of truth; the repo you edit and improve
  skills/01-…  …  08-…

# Claude Code, skills you want everywhere → symlink into personal dir:
~/.claude/skills/01-brainstorm  ->  ~/dev/agent-dev-framework/skills/01-brainstorm   (etc.)

# Claude Code, build-heavy steps for one project → symlink into the project:
<project>/.claude/skills/04-system-design  ->  ~/dev/agent-dev-framework/skills/04-…  (etc.)

# Cowork → upload the skill folders via Customize → Skills.
```

Symlinking (rather than copying) keeps one source of truth, so fixing a skill once updates
every project that links it. For Cowork you re-upload when a skill changes, since it's
installed through the UI.

> Paths and discovery mechanics change between releases. Verify current Claude Code / Cowork
> skill locations in Anthropic's docs before committing to a layout.

## Layout

```
agent-dev-framework/
  README.md
  PROJECT.template.md          <- copy to PROJECT.md per project
  skills/
    _conventions.md            <- shared rules (read-first, artifacts/, clobber guard)
    01-brainstorm/SKILL.md
    02-prd/SKILL.md
    03-competitive/SKILL.md
    04-system-design/SKILL.md
    05-tech-spec/SKILL.md
    06-data-and-evals/
      SKILL.md
      assets/eval-cases.template.jsonl
    07-monitoring/SKILL.md
    08-build-plan/SKILL.md
```
