---
name: scaffold-my-plan
description: >-
  Takes the plan's first milestone and turns it into a running first slice:
  the slice itself, a short written answer to "where does new code go", the
  few boundary rules that actually matter enforced by a tool, and a probe in
  which a no-context agent extends the repo using only those instructions.
  Use after a plan exists and before feature work spreads — when the user
  wants to start implementing the plan, lay the project's structure, fix a
  repo that is turning into a junk drawer, or asks where a new feature
  should live. Triggers: "scaffold", "skeleton", "walking skeleton",
  "structure this properly", "the codebase is getting messy", "where should
  this file go". It does not invent an architecture, it works with no other
  skill installed, and it is not worth running for a one-file change.
license: MIT
argument-hint: "[new|retrofit] [plan: docs/PLAN.md] [root: .]"
---

# Scaffold My Plan

> "Scaffold" here means a skeleton that runs, not a folder tree that waits.

A pretty empty structure proves nothing, must be maintained, and makes every
later session guess which folders are real. What survives contact with the
next fifty features is smaller: one slice that runs, one page that says where
code goes, and only the boundaries that would actually be violated — checked.

This is a handoff skill. A plan names the files its tasks touch, yet says
little about the shape they form and nothing about where the next feature's
files go.

## Division of labor

This skill must work alone. A repo whose agent has no `deep-plan`, `clean-code`,
or `deep-plan-ingest` installed must still get a running slice, a placement
answer, enforced boundaries, and a probe. Those are the deliverable, not a
delegation.

| Question | Owner |
|----------|-------|
| What to build, in what order, and why | `deep-plan`, if present |
| Where code goes, what a boundary is here, one checker, one command | this skill — the floor, always |
| Deeper standards: SOLID, full review checklists, repo-wide refactors | a code-quality skill, if present |
| Durable knowledge once the plan is settled | `deep-plan-ingest`, if present |

Rules for the optional ones:

- **Consult, do not depend.** Start from the plan and the evidence in the
  repo; read an external skill only when this session has hit the specific
  gap it fills, never as a preamble — and once consulted, read the relevant
  part before applying it. Reading a long rulebook to answer one question
  costs more than the question.
- **Verify from the repo, not from the skill list.** "The skill is installed"
  proves nothing about whether the linter is wired. Look at the files: is
  there a check command, a CI job, a config?
- **Floor, not ceiling.** This skill's version of a step is the minimum that
  must exist; an external skill may raise it. Overlapping principles are
  fine — duplicating a whole manual, or keeping two contradictory rule sets,
  is not.

## The law

**No folder without a caller. No boundary worth declaring without a check.**

The first half applies to what *this* skill creates: a directory nothing
references is a wish, and wishes accumulate maintenance. The second half is
why enforcement is not optional — a boundary written only in prose will be
crossed by the next agent, which never read the prose.

A future contributor may legitimately add folders. That is not a violation;
the probe (Phase 5) asks whether they could justify the new folder, not
whether it appeared.

## When it applies

| Situation | Do instead |
|-----------|-------------|
| No plan, and the design is not obvious | `deep-plan` first — this skill consumes a plan, it does not invent one |
| New project, no plan, shape already obvious | Use the framework's own generator, or `clean-code new-project` if installed; say so and stop |
| One reader can hold every placement in their head and nobody else adds files | Skip the ceremony; the contract would be a restatement of the obvious |
| Boundaries are contested, several agents or people add files, or the framework's default layout is not obvious | Run this |
| The system is finished and wants documentation | `deep-plan-ingest` (it works from the plan; the code need not be done) |
| The question is visual, not structural | the UI skills |

The plan may be informal — a milestone list in the user's message is enough.
What it cannot be is absent.

## Modes

- **`new`** — greenfield, or a new module inside an existing project.
- **`retrofit`** — the repo already sprawls. Choose a target shape, move code
  into it in green steps, then write the placement answer for what comes
  next. Retrofit never changes behavior: a move commit contains moves only.

Infer the mode from the repo. Say which one you are running in and why.

## Roles

| Role | Who | Knows | Job |
|------|-----|-------|-----|
| Mason | You, this session | Plan, repo, framework source | Build the slice, write the placement answer, wire the checks |
| Stranger | Fresh subagent, no history | The repo and whatever instructions are checked in | Extend the repo and show whether those instructions route code |

The Stranger is a probe, not a proof. It tells you whether a competent
newcomer can place code without asking; it does not prove the design is
right, and one clean run does not guarantee the next one. Score it, then keep
the written contract as the durable artifact.

## Tiers

| Tier | Signal | Slice | Probe |
|------|--------|-------|-------|
| Light | One module, no contested boundary, solo | thinnest path through it | optional |
| Standard | Several modules, a boundary someone could get wrong | one use case end to end | one run, pre-registered expectation |
| Retrofit | Existing repo, cross-cutting moves | the most self-contained module first | one run per moved module |

Between two tiers take the lower one, and say so.

---

## Phase 0 — Read

Extract from the plan, not from imagination:

1. **The first slice.** The earliest milestone's walking skeleton, or the
   thinnest end-to-end path if the plan names none. If the milestone is a
   list of layers ("the API, then the UI"), rewrite it into something that
   touches each layer once — a finished layer proves nothing.
2. **The file names the plan commits to.** Plans name files in their tasks;
   use those names or explain the rename.
3. **The domain vocabulary.** Nouns the plan uses; they become folder and
   type names. One word per concept, one concept per word.
4. **The stack's real idiom.** Read the installed framework's own structure
   (`vendor/`, `node_modules/`, site-packages, its generator output) before
   inventing a layout it will fight. What the framework generates is
   evidence; what you remember is a hypothesis.
5. **Your own expectation of where things will land**, written down now,
   before anything is built: which files the first slice should produce, and
   where a small follow-up feature should go. This is the evaluator's
   hypothesis, not an answer key. Keep it out of the repo and out of the
   Stranger's brief; it exists so that Phase 5 can surprise you. Written
   after the fact, it proves nothing.

Exit gate: you can name the slice in one sentence, list the modules it
crosses, and name the acceptance criteria the slice must satisfy.

## Phase 1 — Seams

Decide the few boundaries everything else will follow.

- **One module per axis of change**, not per technical layer. Orders and
  Billing change for different reasons and different people; Models and
  Services change for no reason at all.
- **A public API declared with the mechanism the stack already has**:
  package visibility, `module-info`, `__all__`, `@internal`, an entry file,
  or a written convention where the language offers none. Outsiders use that
  API; reaching into internals needs a stated reason. The API may expose
  several operations — a boundary is what outsiders may reach, never a count
  of exports.
- **Dependencies point one way.** The domain knows nothing of HTTP, the
  database, the framework, or the delivery mechanism. Adapters know the
  domain. Wiring knows everything and is imported by nothing.
- **The framework wins ties.** If its convention and your preference
  disagree, follow the convention and record the exception — fighting a
  framework's layout costs more than any tidiness it buys.
- **Name for the domain where you are free to choose**, and leave the names
  the framework dictates exactly as they are.
- **Declare what you are deliberately not deciding.** Deferred is fine;
  undeclared is how a structure rots.

Write the seam map into the project's existing architecture doc, or into
`docs/ARCHITECTURE.md` if there is none: each module, its public API, what it
may import, one line of rationale. `deep-plan-ingest` merges into this file
later, so keep the section shape simple.

Exit gate: no dependency cycles, each module reachable through its declared
API, and the deferred boundaries listed.

## Phase 2 — Slice

Build the thinnest thing that runs end to end across the seams, and nothing
else.

- The slice crosses each seam it needs, once: an input, a domain call, an
  output. Add persistence or an external call only when the plan's acceptance
  criteria need one — forcing a database into a job that has no data proves
  nothing about the design.
- **Every folder you create gets a real file something references.** No
  placeholder README in an empty folder, no barrel file re-exporting nothing.
- Stubs are explicit: a body that throws `NotImplemented` with a
  `TODO(M2-T3)` marker, never a file that silently does nothing.

Two things need proving, and they are not the same claim:

1. **The seam holds** — the business rule runs in a test with no database,
   server, or network. If that test needs infrastructure, the seam is in the
   wrong place: fix the seam, not the test.
2. **The path works** — the slice satisfies the acceptance criteria from the
   plan through routing, wiring, and persistence, exercised end to end with
   real services or explicitly named substitutes. A passing unit test says
   nothing about whether the pieces are connected.

Do not scaffold later milestones. The next module gets built when its
milestone starts; until then a folder for it is a promise nobody kept.

Exit gate: one command runs the app, one command runs the tests; the seam
test passes with nothing running, and the end-to-end check demonstrates the
acceptance criteria.

## Phase 3 — Placement answer

Write the answer to "where does new code go" — short, loadable, and next to
the instructions an agent already reads.

- **Default home: the project's agent instructions.** Add a "Where code
  goes" section to `AGENTS.md` (or `CLAUDE.md`, or whichever exists) of at
  most fifteen lines, linking to one exemplar file per row. Agents load that
  file at session start; a separate document nobody loads is decoration.
  Create `docs/PLACEMENT.md` only when the instructions file is already at
  its line budget or the repo has no such file and does not want one.
- **Amend, do not author.** If the instructions already state some
  conventions, the deliverable is the delta — often two rows. A repo with a
  documented layout needs a sentence added, not a second manual that
  contradicts the first.
- **Rows come from the seams you actually built**, not from a canonical
  layering. A project with no adapters gets no adapters row. Presenting a
  full domain/use-cases/adapters/main table to a framework-shaped app is how
  placement advice turns into dogma.
- **Every row cites a real file that demonstrates it.** The exemplar is what
  a contributor copies; a rule with no exemplar drifts into fiction.
- **Name the anti-homes** — `utils`, `helpers`, `common`, `misc`, `_v2` — and
  say what to do instead: name the concept, then place it.
- **Include the vocabulary**, one line per domain noun, so names stay stable.
- **Give the escape hatch**: what to do when nothing fits. "Propose the module,
  justify it against the vocabulary and the dependency direction, then add a
  row." Without it, new concepts squeeze into `utils` or invent a folder
  silently — and the probe's EXTENDED outcome becomes impossible.

Exit gate: a stranger can answer "where does X go?" for any plausible X
without opening the source.

## Phase 4 — Enforce

Turn the seams into failures — but only the ones that would actually be
violated.

- One rule per declared seam. A three-module project rarely needs ten.
- Use the ecosystem's checker: `dependency-cruiser` (JS/TS), `import-linter`
  (Python), `deptrac` (PHP), `ArchUnit` (Java/Kotlin), `go-arch-lint` (Go).
  Wire it yourself; do not assume another skill did. If the repo already has
  a checker, read its config first and extend it rather than adding a second
  — two rule sources that disagree are worse than none.
- **When nothing can check it, say so.** The ladder: a compiler, a visibility
  rule, or a linter the repo already has; then a tool worth its setup cost;
  then a small script, and only for an edge that is unambiguous — with one
  example that must pass and one that must fail. If none of that is honest,
  record the limitation and fall back to review. A script that misses
  aliases, re-exports, or dynamic loading passes for the wrong reason, which
  is worse than a stated gap.
- One command per check, one command for all of them, wired into CI. A check
  that runs when someone remembers is not enforcement.
- **Prove each check fires.** Add a forbidden import on purpose, watch it go
  red, revert. A check that passes for the wrong reason is worse than none.

Exit gate: you have seen every rule fail at least once, deliberately.

## Phase 5 — Probe

Prove the instructions, not the code.

Run it in a separate worktree or a copy of the repo — switching branches in
the working directory isolates nothing, and the probe leaves build artifacts,
migrations, or an edited config behind. Attach a behavior check: the feature
must work, or the run says nothing about placement.

Decide before you start what happens to the diff. It is next milestone's
work, not this skill's: score it, then discard it, or hand it to that
milestone's owner as a reference. Never merge it silently because it looked
right.

Brief the Stranger with no conversation history:

> You are new to this repository. Read the agent instructions and the files
> they point to. Add this feature: `<one small feature from the next
> milestone, 3–5 files>`. Run the tests. Do not ask me questions — if the
> instructions do not answer something, write down what you would have
> asked, choose the best option, and continue.

Score against ownership and dependency, and treat paths as supporting
evidence. A file in an unexpected place that the Stranger justifies from the
written rules is a better result than a file in the expected place found by
guessing. Never show the Stranger your expected list — that measures
mind-reading, not clarity. Read the questions they wrote: they are the most
direct evidence of what the contract fails to say.

| Outcome | Meaning | Action |
|---------|---------|--------|
| Owned by the right module, dependencies point the right way, no questions, tests green | CLEAN | Ship |
| A new folder the Stranger justifies from the existing vocabulary and dependency direction, tests green | EXTENDED | Accept and add the row — the contract worked |
| One file owned by the wrong module and movable, or 1–2 questions | PATCH | Add the missing row, rerun once |
| Tests failing, or the feature works only by ignoring the instructions | INCONCLUSIVE | Fix the slice or the brief; the probe tested nothing |
| Cannot say which module owns it, duplicates an existing folder, or bypasses the dependency direction | DRIFT | The contract failed, not the agent. Rewrite the affected rows, rerun |

Two rounds maximum. After that, name the unresolved gap in the handoff
instead of polishing forever.

If the platform cannot spawn a fresh agent, hand the brief to a person or run
it in a new session; if neither is possible, report the handoff probe as
**not evaluated** and continue with the rest of the handoff. Do not play the
Stranger yourself: you know what you meant, and that is exactly the knowledge
the probe is meant to lack.

## Phase 6 — Handoff

- Update the placement section and the architecture doc with what the probe
  taught you; those are the deliverable.
- Report: the tree you built, the rules now enforced, the probe's score and
  what it would have asked, and the next milestone to implement.
- Do not write a session summary into the repo. The repo is the record.

---

## Artifacts

| Path | Content | Read by |
|------|---------|---------|
| agent instructions (`AGENTS.md` / `CLAUDE.md`) — "Where code goes" | ≤15-line routing table, exemplars, anti-homes, vocabulary | every future contributor, human or agent |
| existing architecture doc, or `docs/ARCHITECTURE.md` | module map, public APIs, dependency direction, deferred boundaries | `deep-plan-ingest`, reviewers |
| boundary-check config + CI job | the seams as executable rules | CI |
| the first slice + its seam test | proof the shape works | the implementer of M1 |

## Anti-patterns

| Temptation | Why it costs |
|------------|--------------|
| Scaffolding all milestones at once | Folders that prove nothing and have no caller |
| A README in an empty folder | Makes an empty promise look finished |
| Copying a reference architecture whole | Structure without its reasoning cannot be adapted or defended |
| `utils`, `helpers`, `common`, `core` | The drawer where design goes to die; name the concept |
| One interface per class "for later" | A second implementation that does not exist is pure cost |
| A placement table written from imagination | Rows with no exemplar drift into fiction by week two |
| Ten boundary rules in a three-module repo | Rules nobody can hold in their head are rules nobody follows |
| Refactoring behavior during a retrofit move | Two changes in one commit; when it breaks nobody knows which |

## Signals, not scores

These are worth reporting once, as review questions — never as gates, and
never as a reason to reshape a framework's layout:

- orphan files (reachable from no entry point)
- directories with nothing in them
- dependency cycles
- depth that exceeds what the domain needs

A single-file module is normal in Go, Python, and most plugin architectures.
`app/`, `pages/`, `public/`, `migrations/`, `tests/` are correct top-level
names in many frameworks even though no domain noun appears among them. When
a signal argues with the framework, the framework wins.

## What this skill does not do

- It does not plan. If the plan is wrong, this makes the wrong shape faster.
- It does not implement features beyond the first slice.
- It does not teach SOLID or run a general code review — that is a
  code-quality skill's job, if one is installed.
- It does not document the finished system — `deep-plan-ingest` does.
- It does not choose the framework, database, or hosting.
- It does not reformat the repo as a side effect.

## Load plan

| Task | Read |
|------|------|
| Picking boundaries, naming modules | `references/seams.md` |
| Writing the placement section, per app shape | `references/placement.md` |
| Checker config for a specific stack | `references/enforcement.md` |
| Running and scoring the probe | `references/stranger-test.md` |
| Moving an existing repo in green steps | `references/retrofit.md` |
| Handoff and architecture-section shape | `references/handoff.md` |
