# Placement answer

The shortest document that stops a future contributor from guessing. Its home
is the file agents already load — `AGENTS.md`, `CLAUDE.md`, or whichever exists
— not a new manual.

Budget: **fifteen lines**. If it does not fit, the seams are unclear, not the
budget too small.

## Amend first, write second

1. Find every instruction file: `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/`,
   `CONTRIBUTING.md`, the architecture section of `README.md`.
2. Grep them for placement sentences already there — "put X in Y", "never add
   Z", "business logic lives in". A mature repo usually has some.
3. Write **only the delta**. Two rows that are missing beat a table that
   repeats what the file already says in different words.
4. Reuse the file's own vocabulary. A second name for the same concept is how
   two documents start disagreeing.

If the repo's docs already answer the placement question, say so and stop:
that is a successful Phase 3, not a skipped one.

## Row anatomy

| Work | Home | Exemplar | Must not |
|------|------|----------|----------|

- **Work** — a kind of change in the user's language: "a rule that prices
  something", "a background job", "a screen that lists records". Not a folder.
- **Home** — a path pattern with placeholders: `src/<module>/use-cases/`.
- **Exemplar** — one real file that demonstrates the row. The *smallest good*
  example, not the biggest: a newcomer copies it.
- **Must not** — the violations you would actually reject in review. This is
  the half that changes behavior.

A row is good when a newcomer can apply it to a case you did not anticipate,
and wrong only when they misread the domain — not when they guess the folder.

## The four things that must be answered

Beyond the table, the section answers:

1. **Where do tests go?** (same folder, or a parallel tree — pick one)
2. **How do I add a folder?** The escape hatch. "If nothing fits: propose the
   module, justify it against the vocabulary and the dependency direction, add
   a row." Without this line every new concept either squeezes into `utils` or
   gets invented silently.
3. **What are the anti-homes?** `utils`, `helpers`, `common`, `misc`, `_v2`,
   `core` — each with the replacement: "name the concept, then place it".
4. **The vocabulary** — five to ten domain nouns, one line each.

## What does not belong

- the full component map and data flow — that is the architecture doc
- code style, naming conventions a formatter or linter owns
- process ("open a PR", "ask the lead")
- anything that restates the folder tree without adding a decision

## By application shape

| Shape | Advice |
|-------|--------|
| Framework-shaped (Next, Rails, Django, Laravel, Spring) | Do not fight the generator. Add only where business rules live, and what may not leak into controllers/handlers. |
| Modular monolith | One module table (module, owns, may import); the row set is the dependency graph. |
| Frontend + backend in one repo (Tauri, Electron, SPA + API) | Say which side owns state and which owns persistence, and what crosses the IPC/HTTP boundary. Follow the existing split even if you would choose differently. |
| Library or package | Public surface (what `exports`/`__init__` publishes) versus internal, and where a new capability goes. |
| Plugin | Extension points and hooks; what a plugin may import and what it must never reach into. |

## Example, end to end

```markdown
## Where code goes

- Pure logic, no React and no Tauri: `src/utils/<concept>.ts`, test at
  `src/utils/__tests__/<concept>.test.ts`. Exemplar: `src/utils/junkDetector.ts`.
- React behavior with state or effects: `src/hooks/use<Thing>.ts`.
  Exemplar: `src/hooks/useJunkNotifier.ts`.
- State shared across views: `src/store/<name>Slice.ts`, exported through
  `src/store/index.ts`. Exemplar: `src/store/uiSlice.ts`.
- Visible UI: `src/components/<Thing>.tsx`; dialogs in `components/dialogs/`.
  Exemplar: `src/components/AccountsPanel.tsx`.
- A Rust command: `src-tauri/src/<domain>/<module>.rs`, registered in
  `lib.rs`. Exemplar: `src-tauri/src/git/branch.rs`. Call it with `invoke()`.
- Never: `misc`, `helpers`, `common`, a `fetch()` inside a component, a new
  top-level folder.
- Nothing fits? Propose a module, justify it against the vocabulary, add a row.
```

Ten lines, six exemplars, one escape hatch.

## Failure modes

| Symptom | What went wrong |
|---------|-----------------|
| The table mirrors the folder tree | You described the layout instead of deciding for new work |
| Rows with no exemplar | Written from imagination; it will drift by week two |
| Contradicts the framework's own docs | The framework wins; rewrite the row as an exception |
| Twenty rows | Nobody holds twenty rules in their head; keep the ones that prevent real mistakes |
| No escape hatch | New concepts either get buried in `utils` or invent a folder silently |
| Duplicates `ARCHITECTURE.md` | Two documents, two truths, one will be stale |
