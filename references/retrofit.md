# Retrofit

Giving an existing repo a shape it never had. The work is mostly moving files,
so the danger is not design — it is breaking a working system while tidying it.

## Preconditions

- The build and the tests are green before you start. If they are not, fix
  that first; you cannot tell your breakage from the existing red.
- The check commands are written down and run by one command each.
- The working tree is clean. A move commit mixed with someone's half-finished
  edit is unreviewable.

## Find the shape the repo almost has

Do not impose a cathedral. Look for the seams already present:

1. **Clusters of imports.** Files that import each other heavily are one
   module whatever the folders say.
2. **The hub.** The file or folder everything imports is your wiring or your
   accidental god module. Wiring stays a hub; a god module gets split.
3. **The leaf.** Code imported by nothing and importing nothing but the
   standard library is a domain candidate — start there.
4. **The churn.** Files that change together in `git log --name-only` are one
   module; files that never change together are the boundary you want.

Then choose the **minimal** target: the two or three seams that explain most
of the current mess. A repo that has never had modules does not need eight.

## Order

Leaves first, dependents later.

```
1. extract a leaf module (nothing imports it yet, or only its callers)
2. make its public API explicit
3. point the callers at the API, one caller per commit
4. move to the next module
```

Never move a module and its dependents in the same step: when the build breaks
you cannot tell which edge is wrong.

## The move protocol

One commit per move, and inside a move commit:

- **Move only.** No reformatting, no renaming, no behavior change. A diff
  that is 100% renames is reviewable in seconds; a diff that is 95% moves and
  5% fixes is not reviewable at all.
- **Update imports and registration only.** Routes, DI, barrels, build config.
- **Tests unchanged.** A test that had to change to keep passing means the
  move changed behavior.
- **Green before the next move.** If it is red, revert the commit; do not
  debug on top of it.

Rename in its own commit, after the move commit is green.

## Compatibility shims

When a public path moves (a package, a module other repos import), leave a
re-export at the old path:

```ts
// old path, temporary
export * from "../orders/index";
```

Pair it with an owner and a removal task in the plan — a shim with no removal
date is a second permanent API. Remove it when the last caller has moved. In
an application (as opposed to a library), prefer moving the callers in the
same change and skipping the shim entirely.

## Stop rule

Stop when the next move requires a behavior change. That is not a retrofit any
more; it is a plan. Write it down and go back to planning, or the retrofit
turns into a rewrite nobody authorized.

## Progress signals

- modules with a declared public API: rising
- dependency cycles: falling to zero
- files at the repo root: falling
- `utils`/`helpers`/`common` population: falling, not rising

## Anti-patterns

| Temptation | Why it costs |
|------------|--------------|
| Moving and fixing in one commit | The build breaks and nobody knows which half did it |
| "While I'm here" renaming | Every rename is a merge conflict for whoever is working |
| Moving everything at once | One red build with fifty causes |
| Retrofitting into a reference architecture | The repo gets a shape nobody asked for and cannot maintain |
| Leaving the shims forever | Two public APIs, both maintained, neither trusted |
| Announcing the new structure before it exists | Contributors follow the announcement, not the code |
