# Framework conventions (read before writing any artifact)

Every skill in this pipeline follows these rules. They live here so each SKILL.md can
reference them instead of repeating them.

## Read first
1. Read `PROJECT.md` at the project root. It holds all project-specific facts so the skills
   stay generic. Treat `TBD` fields as open questions, not settled facts.
2. Read the prior step's artifact(s) from `artifacts/` (each skill names which ones).

## Two folders, never mixed
- `docs/` — the user's hand-written documents. **Never write here.**
- `artifacts/` — pipeline output. Yours to write, but the user edits these freely, so they
  must be protected (see guard below).

## Don't-clobber guard (MANDATORY before overwriting any artifact)
Before writing an artifact that already exists:
1. Check whether it was hand-edited after it was generated. Quick ways: look for an
   `<!-- generated: <timestamp> -->` marker at the top vs. the file's modification time, or
   check `git status`/`git log` if the project is a git repo, or simply diff against what the
   skill would expect to have written.
2. If it looks hand-edited, STOP and ask the user: "`artifacts/<file>` has changes since it
   was generated — overwrite, merge, or write to `artifacts/<file>.new`?" Default to NOT
   overwriting.
3. If it's unchanged generated output (or doesn't exist yet), write freely.
4. When you do write, stamp the top of the file with `<!-- generated: <ISO timestamp> -->`
   so the next run can tell.

## Chaining
Each step reads the previous artifact and writes its own. This is what makes the pipeline
work identically in Claude Code and Cowork — it's just files. Never rely on conversation
memory to carry state between steps; rely on the artifacts.

## Tone
Be a direct, honest thinking partner. Lead with the most important caveat or risk. Don't
manufacture enthusiasm. The user is a solo founder who needs a truth-teller, not a cheerleader.
