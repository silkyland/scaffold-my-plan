# Handoff

The deliverable is two documents and one working slice. Write less than you
want to; the repo is the record, not a session journal.

## The architecture section

In the existing architecture doc, or `docs/ARCHITECTURE.md` if there is none.
Keep the heading stable — `deep-plan-ingest` merges into this file later and
leaves sections it doesn't own untouched, so a stable heading keeps this
section intact and un-duplicated.

```markdown
## Structure

What changes here does not change there.

| Module | Public API | May import | Never |
|--------|-----------|-----------|-------|
| `orders` | `orders/index.ts` | — | HTTP, DB, the payment vendor |
| `billing` | `billing/index.ts` | `orders` | HTTP, the payment vendor |
| `payments` | `payments/index.ts` | `billing` types | `orders` internals |
| `main` | — | everything | contain rules |

**Dependency direction**: `main → adapters → use cases → domain`; nothing
imports `main`.

**Deferred**: `payments` shares a transaction with `orders` until the DB split
(M3). Written here so the next session does not draw a different line.

**Enforced**: `npm run check` — `domain-stays-pure` (dependency-cruiser).
No rule for the `orders`/`billing` edge yet: both are stable and the checker
costs more than the risk.
```

Four rows are usually enough. The "Never" column is what makes the table
actionable; the "Enforced" paragraph is what tells the next reader whether the
table is a promise or a suggestion.

## The report

Five short lines to the person who asked:

```
Tree:      <the modules created/moved, one line each>
Contract:  <where the placement answer lives, and how many rows>
Enforced:  <rules now failing the build, or "none — recorded trade">
Probe:     <CLEAN/PATCH/EXTENDED/DRIFT/not evaluated> — <what it asked>
Next:      <the milestone a contributor should pick up>
```

No preamble, no restating the plan. If a phase was skipped or degraded, say
which and why — a silent gap is worse than an admitted one.

## Ingest compatibility

`deep-plan-ingest` writes `AGENTS.md`, `docs/ARCHITECTURE.md`, and
`ROADMAP.md`, merging section-by-section. Two habits keep the handoff clean:

- **Put placement rules under one heading** — "Where code goes" — ingest
  leaves sections it doesn't own untouched, so one stable heading keeps this
  section intact and un-duplicated.
- **Do not invent a second source of truth.** If the placement table lives in
  `AGENTS.md`, the architecture doc points at it rather than repeating it.

If ingest is not installed, the two files stand alone; nothing here depends on
it.

## What not to write

- a session summary or a changelog in the repo
- a `STATE.md` for a workflow this short
- aspirational architecture ("we will split this later") that no milestone
  carries
- the same fact in two files
