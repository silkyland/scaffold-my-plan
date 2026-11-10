# Enforcement

A boundary written only in prose is crossed by the next agent, which never
read the prose. Turn the seams you declared into failures — but only the ones
that would actually be violated.

## The ladder

Stop at the first rung that is honest:

1. **Something the repo already runs.** A compiler, package visibility, a
   linter with import rules, project references. Free, maintained, understood.
2. **Add a tool, if the boundary is worth its setup cost.** One config file,
   one command. Worth it when the rule protects something expensive (the
   domain, a security edge) and would be broken by a reasonable person in a
   hurry.
3. **A small script, only for an unambiguous edge.** Aliases, re-exports,
   dynamic loading, and framework magic are where home-made scanners silently
   pass. If you write one, pair it with an example that must pass and one that
   must fail.
4. **Nothing can check it: say so.** Record the limitation next to the rule
   and fall back to review. A check that passes for the wrong reason is worse
   than a stated gap — it buys confidence nobody earned.

## Choosing the rule

- One rule per seam you would actually break. A three-module project rarely
  needs ten.
- The rule must map to a line in the architecture doc, or it is decoration.
- Prefer the *edge* over the *file*: "domain may not import adapters" is
  checkable; "keep the domain clean" is a mood.
- Encode what the framework does not already guarantee. A framework that
  already forbids cross-module imports does not need you to forbid them again.

## Per stack

Configs below are shapes to adapt, not gospel: check the installed version's
docs before pasting. What you remember about a tool's schema is a hypothesis.

### TypeScript / JavaScript — `dependency-cruiser`

```js
// .dependency-cruiser.js
module.exports = {
  forbidden: [
    {
      name: "domain-stays-pure",
      severity: "error",
      from: { path: "^src/(domain|core)" },
      to: { path: ["^src/(adapters|infrastructure)", "^src/(http|ui)"] },
    },
    {
      name: "no-deep-imports",
      severity: "error",
      from: { path: "^src/(?!orders/)", pathNot: "^src/orders/" },
      to: { path: "^src/orders/(?!index\\.ts)" },
    },
  ],
  options: { doNotFollow: { path: "node_modules" }, tsPreCompilation: true },
};
```

`npx depcruise src --config .dependency-cruiser.js`. For lint-level rules
inside files, `eslint-plugin-boundaries` works the same way.

### Python — `import-linter`

```ini
# setup.cfg (or .importlinter)
[importlinter]
root_packages =
    app

[importlinter:contract:layers]
name = Layers
type = layers
layers =
    app.adapters
    app.usecases
    app.domain

[importlinter:contract:modules]
name = Modules stay independent
type = independence
modules =
    app.orders
    app.billing
```

`lint-imports`. The `layers` contract lists higher layers first: each may
import the ones below, never the reverse.

### PHP — `deptrac`

```yaml
# deptrac.yaml
deptrac:
  paths: [./src]
  layers:
    - { name: Domain, collectors: [{ type: directory, value: 'src/Domain/.*' }] }
    - { name: Application, collectors: [{ type: directory, value: 'src/Application/.*' }] }
    - { name: Infrastructure, collectors: [{ type: directory, value: 'src/Infrastructure/.*' }] }
  ruleset:
    Domain: ~
    Application: [Domain]
    Infrastructure: [Domain, Application]
```

`vendor/bin/deptrac analyse`.

### Java / Kotlin — ArchUnit

```java
@AnalyzeClasses(packages = "com.example.app")
class ArchitectureTest {
  @ArchTest
  static final ArchRule domain_is_pure =
      noClasses().that().resideInAPackage("..domain..")
          .should().dependOnClassesThat()
          .resideInAnyPackage("..web..", "..persistence..", "..vendor..");
}
```

It is a normal unit test: it fails the build where tests already run.

### Go — `go-arch-lint`, or the language itself

```yaml
# .go-arch-lint.yml
version: 3
components:
  orders: { in: internal/orders/** }
  billing: { in: internal/billing/** }
deps:
  billing:
    mayDependOn: [orders]
```

`internal/` plus package boundaries already prevent much of this for free;
reach for the tool only when cross-package edges need stating.

### Others

- **Ruby** — `packwerk` with `package.yml` per package and a dependency list.
- **C#/.NET** — `NetArchTest` as a test with `Types.InAssembly(...)`.
- **Rust** — the compiler's own privacy (`pub(crate)`, module trees) and
  workspace crate splits do most of the work; there is no standard graph
  checker, so record the limit rather than half-checking it.

## One command, in CI

```jsonc
// package.json — the shape is the same everywhere
"check": "tsc --noEmit && vitest run && depcruise src --config .dependency-cruiser.js"
```

A check that only runs when someone remembers is not enforcement. Wire the
single command into the pipeline the repo already uses; do not add a second
pipeline to hold one rule.

## Prove it fires

1. Add a deliberate violation — the exact import the rule forbids.
2. Run the command. It must fail, and the message must name the rule.
3. Revert.

A rule that has never failed has never been tested. If the failure message is
unreadable, fix the message before trusting the rule: the next person to hit
it is in a hurry.

## Maintenance

- **Delete rules that never fire.** A rule nobody trips in months is either
  already internalized or is being bypassed; both mean it is not doing work.
- **A rule everyone bypasses is worse than none** — it teaches that the rules
  are optional.
- **When the repo deliberately stays light**, a checker may lose to the
  constraint. Record the trade in the architecture doc: "no import checker;
  the 600 kB dependency budget matters more; review covers the domain edge."
  That is a decision, not a gap.
