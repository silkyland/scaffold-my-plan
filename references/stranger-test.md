# The probe

The only test of the instructions rather than the code. A competent newcomer
with no history reads what the repo publishes and adds a feature; where the
files land tells you whether the contract routes work or merely describes it.

It is a probe, not a proof. One clean run is a signal; a design can pass one
task and fail the next, because the task it passed happened to match the
contract's vocabulary.

## Isolation

Use a separate worktree or a copy. **A branch in the working directory is not
isolation**: switching branches leaves build artifacts, migrations, an edited
config, and an installed dependency behind, and the probe's leftovers will
show up in the next diff.

Remove the history that contains the answer if you are replaying a real
feature — a fresh `git init` over an exported tree, or a worktree cut *before*
the commit. An agent that runs `git log` and finds the finished feature has
tested nothing.

Decide before starting what happens to the diff: it is the next milestone's
work, not this skill's. Score it, then discard it, or hand it to that
milestone's owner as a reference. Never merge it silently because it looked
right.

## Pre-registration

Before the probe, write down — for yourself, not for the Stranger:

- the module that should own each part
- the dependency direction each part must respect
- one acceptance criterion the feature must satisfy
- the two or three paths you expect

Keep it out of the repo and out of the brief. Shown to the Stranger, it
measures mind-reading. Written after the fact, it measures nothing.

## The brief

Verbatim, no conversation history, no hints about where anything lives:

> You are new to this repository. Read the agent instructions and the files
> they point to. Add this feature: `<3–5 files' worth, from the next
> milestone>`. Run the tests. Do not ask me questions — if the instructions do
> not answer something, write down what you would have asked, choose the best
> option, and continue.

Rules for the task:

- **Small but real**: one acceptance criterion, three to five files. A
  one-file change tests nothing; a milestone tests the agent's stamina.
- **Not the feature you just built.** It must be new work, chosen from the
  plan's next milestone.
- **Not a refactor.** Moving code tests a different skill.
- **Vary the type across runs.** A contract can route a backend feature and
  fail a UI one; one run of one kind is not coverage.

## Scoring

| Outcome | Meaning | Action |
|---------|---------|--------|
| Owned by the right module, dependencies point the right way, no questions, tests green | CLEAN | Ship |
| A new folder justified from the vocabulary and the dependency direction, tests green | EXTENDED | Accept and add the row — the contract worked |
| One file owned by the wrong module and movable, or 1–2 questions | PATCH | Add the missing row, rerun once |
| Tests failing, or the feature works only by ignoring the instructions | INCONCLUSIVE | Fix the slice or the brief; the probe tested nothing |
| Cannot say which module owns it, duplicates an existing folder, or bypasses the dependency direction | DRIFT | The contract failed, not the agent |

Read the questions the Stranger wrote. They are the most direct evidence of
what the contract fails to say. Split them:

- **Missing information** — the contract should answer it. Add the row.
- **A preference** — two placements are both defensible. Either accept the
  choice or say which one is house style, once.

A file in an unexpected place that the Stranger justifies from the written
rules is a better result than a file in the expected place found by guessing.

## Interpretation

- **DRIFT** is always a contract finding. Rewrite the affected rows; do not
  lecture the agent.
- **INCONCLUSIVE** is always a slice or brief finding. The feature did not
  work, so placement cannot be judged.
- **EXTENDED** is success, not tolerance: the escape hatch worked. Add the
  row so the next agent does not have to invent it again.

## Repetition

- One run is a signal. Two runs of different task types is evidence.
- Repeat only when the contract is contested or the first run was
  INCONCLUSIVE; do not polish a contract until a probe passes, or you will
  overfit it to one task.
- Record the runs — task, outcome, questions — in the handoff. The next
  session needs to know what was tested and what was not.

## No subagent platform

Hand the brief to a person, or run it in a new session with only the repo.
Do not play the Stranger yourself: you know what you meant, and that
knowledge is exactly what the probe is meant to lack. If neither is possible,
report the probe as **not evaluated** and continue — an honest gap beats a
fabricated pass.
