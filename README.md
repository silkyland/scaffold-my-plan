# scaffold-my-plan

**From a plan to a structure that holds.** A skill for AI coding agents that
turns the plan's first milestone into a running slice, writes down where new
code goes, enforces only the boundaries that matter, and tests the whole thing
with an agent that has never seen the repo.

> **No folder without a caller. No boundary worth declaring without a check.**

Most "scaffolding" produces a beautiful empty tree that proves nothing and
must be maintained forever. This skill produces the opposite: one path that
runs end to end, a fifteen-line placement answer filed where agents already
look, and a checker that goes red when someone crosses a seam.

## When to use it

- right after a plan, before the first feature scatters files
- when a repo is turning into a junk drawer and the fix is a shape, not a
  rewrite
- when several agents or people add files and each invents a new folder
- when nobody can answer "where does this go?" without asking

Not for: planning (`deep-plan`), a one-file change, a brand-new project with
an obvious shape and no plan, or visual design.

## The workflow

| Phase | What happens | Gate |
|-------|--------------|------|
| 0 Read | first slice, committed file names, vocabulary, framework idiom, your expectation of where files land | the slice is one sentence and has acceptance criteria |
| 1 Seams | modules per axis of change, public API per module, one-way dependencies, deferred boundaries declared | no cycles, no module reachable only through internals |
| 2 Slice | the thinnest path end to end; every folder has a caller | seam test passes with nothing running; the path satisfies the plan |
| 3 Placement | amend the existing agent instructions with ≤15 lines: work → home → exemplar → must-not, plus an escape hatch | a stranger answers "where does X go?" without the source |
| 4 Enforce | the ladder: existing checks, then a tool worth its cost, then a small script, then an honest gap | every rule has been seen failing, on purpose |
| 5 Probe | a fresh agent adds the next milestone's feature using only the repo's instructions | CLEAN or EXTENDED; DRIFT means the contract failed |
| 6 Handoff | architecture section, report, no session journal | two documents, one working slice |

Effort scales: a single module with no contested boundary gets a slice and no
probe; a contested boundary or a retrofit gets the full run.

## What it produces

| Artifact | Why it matters |
|----------|----------------|
| `AGENTS.md` (or `CLAUDE.md`) — "Where code goes" | the file every future session loads; the delta, not a second manual |
| architecture doc, "Structure" section | modules, public APIs, direction, deferred boundaries |
| one boundary checker + one command in CI | the seams as failures, not prose |
| the first slice + its seam test | proof the shape works |
| placement for the next feature | so the plan's next milestone lands in the right place |

## Relationship to the family

| Skill | Moment |
|-------|--------|
| `deep-plan` | plan the change, evidence-gated |
| **scaffold-my-plan** | turn milestone 0 into a running, placed, checked slice |
| `deep-plan-ingest` | distill the plan into `AGENTS.md`, `docs/ARCHITECTURE.md`, `ROADMAP.md` |
| `clean-code` | the deeper rulebook for SOLID, patterns, general review |

This skill works with **none of them installed**. It consumes a plan if one
exists, and treats `clean-code` as a supplement to consult when a gap appears —
not a dependency, and not a reason to duplicate a whole manual.

## Validation

Tested once, A/B, on a real private Tauri + React desktop repository by
replaying a shipped feature on the commit before it:

| | with a placement contract | without |
|---|---|---|
| typecheck / tests | pass, 84 tests | pass, 88 tests |
| files created | 7 | 5 |
| roles placed where the real commit put them | 3 of 5 | 2 of 5 |

The single difference between the two arms was the stateful logic: with the
contract it went to `src/hooks/`, where the repo's convention and the real
commit both put it; without it, it was buried in a component. Both arms also
chose a frontend `fetch` over the real commit's Rust command — a defensible
deviation, and the reason the probe scores ownership and rationale rather than
path equality. The cost was two extra files.

That is a modest, honest result: the contract matters most where the repo's
own documentation is thin, which is the case this pilot did not cover. The
Phase 5 probe itself was not exercised in that run.

## Install

```bash
npx skills add silkyland/scaffold-my-plan
```

Or copy `SKILL.md` and `references/` into your agent's skills folder
(`~/.claude/skills/scaffold-my-plan/`).

## Structure

```
scaffold-my-plan/
├── SKILL.md                        # law, modes, tiers, 7 phases, artifacts, anti-patterns
└── references/
    ├── seams.md                    # finding boundaries and naming modules
    ├── placement.md                # writing the placement answer (amend first)
    ├── enforcement.md              # the checker ladder, per-stack configs
    ├── stranger-test.md            # running and scoring the probe
    ├── retrofit.md                 # moving an existing repo in green steps
    └── handoff.md                  # architecture section, report, ingest compatibility
```

## License

[MIT](LICENSE)
