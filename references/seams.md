# Seams

The boundaries everything else follows. Get these wrong and every later file
lands in the wrong place, so this is the only phase worth slowing down for.

## What a seam is

A line where one kind of change stops and another begins. Not a folder, not a
layer, not a package — those are consequences. If you cannot say *what changes
behind this line that does not change outside it*, there is no seam there.

## Four tests, any one justifies a seam

| Test | Question | Example |
|------|----------|---------|
| Actor | Could two different people demand changes here? | finance wants invoicing rules; operations wants shipping rules |
| Rate | Do these change at different speeds? | pricing rules weekly; the payment vendor yearly |
| Failure | Do they fail differently? | arithmetic is total; the network is not |
| Release | Would you ever ship one without the other? | CLI tool without the web UI |

If two things always change together, they are one module even when they look
different. Splitting them buys a boundary you will cross on every change.

## How many

Two to five for most applications, one for a library, and more than six is a
smell unless the domain genuinely has that many actors. A seam you cannot name
with a domain noun is usually a technical layer wearing a module's clothes.

## Naming

- **Modules are domain nouns**: `orders`, `billing`, `search` — not `models`,
  `services`, `handlers`, `core`.
- **Inside a module, name the concept**, not the pattern: `order.ts`, not
  `OrderServiceImpl.ts` or `orders-manager.ts`.
- **The vocabulary in the placement answer is this same list.** One word per
  concept, everywhere: types, folders, tests, the doc.
- **Singular vs plural**: plural when the module is a collection
  (`notifications`), singular when it is a concept (`billing`).

## Dependency direction

```
domain (rules)  ←  use cases (orchestration)  ←  adapters (vendor, DB, HTTP)
                              ↑
                        wiring / main (knows everything, imported by nothing)
```

Why this direction: the domain is the part you can test with nothing running;
adapters change with vendors; wiring is the only place allowed to know the
whole app. Every "hexagonal", "clean", "vertical slice", or "modular monolith"
layout is this rule under a different name — pick the one the framework
already implies instead of importing a second vocabulary.

## The seam test

A seam is real when all three hold:

1. delete the module — only its callers break
2. its tests run with no database, server, or network
3. swap its adapter without editing the domain

If (2) fails, the seam is not where you drew it: infrastructure leaked into
the rules.

## Deferred boundaries

Write down what you are **not** separating yet, and why. "Payments is a module
but shares a transaction with orders until we split the DB" is architecture.
Undeclared deferral is how a structure rots: the next session sees no line and
draws a different one.

## Worked example

| Module | Owns | May import | Never |
|--------|------|-----------|-------|
| `orders` | order rules, totals | nothing | HTTP, DB, the payment vendor |
| `billing` | invoices, tax | `orders` (read) | HTTP, the payment vendor |
| `payments` | vendor adapter | `billing` types | `orders` internals |
| `main` | config, DI, routes | everything | contain rules |

`orders` and `billing` are separate because different actors change them.
`payments` is separate because it fails differently (network) and changes on a
vendor's schedule. `main` is the wiring dump everything is allowed to know.

## Mistakes

- **Folder per layer.** `controllers/`, `services/`, `models/` describe the
  framework, not the business; a change to pricing touches all three.
- **`core` or `shared` as a module.** It becomes the place design goes to die,
  because nothing owns it and everything may change it.
- **Splitting by noun while sharing a transaction.** Two modules that always
  commit together are one module with two names.
- **A module that needs another's internals.** The seam is in the wrong place;
  merge them or publish the operation the first one needs.
- **Drawing seams from the org chart alone.** Ownership is one input, not the
  only one — the rate and failure tests still apply.
