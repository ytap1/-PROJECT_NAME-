# Conventions: {{PROJECT_NAME}}

Rules the AI (and humans) must follow when writing code in this repo.
When a rule conflicts with a framework default, the rule wins — document the
exception in `.ai/decisions.md`.

## File Naming

- **Source files:** `snake_case.py` / `kebab-case.ts` — match the language norm.
- **Test files:** mirror the source path with a `test_` prefix or `.test.ts`
  suffix. `src/services/billing.py` → `tests/services/test_billing.py`.
- **One public export per file** when practical. Group related helpers as
  private (`_name`) in the same file.
- **No "utils", "helpers", "common", "misc" modules.** If it doesn't have a
  home, it probably doesn't have a purpose yet.
- **Scripts go in `scripts/`** and are named by verb: `scripts/seed-db.py`,
  `scripts/reset-cache.sh`.

## Code Style

- **Formatter is the source of truth.** Run `make fmt` before committing.
  (Python: ruff format. JS/TS: prettier. Go: gofmt. Rust: rustfmt.)
- **Lint is not optional.** `make lint` must pass before push. Do not add
  `# noqa` / `// eslint-disable` without a comment explaining why.
- **Types are required at module boundaries** (public functions, HTTP handlers,
  DB models). Internal helpers may use inference.
- **Naming:**
  - Functions and variables: `lower_snake_case` (Python) / `camelCase` (JS/TS).
  - Classes and types: `PascalCase`.
  - Constants: `UPPER_SNAKE_CASE`.
  - Booleans read as predicates: `is_active`, `has_access`, `should_retry`.
- **Errors are values, not control flow.** Raise/throw for truly exceptional
  states. Return a `Result`/tuple for expected failure modes (validation,
  not-found, rate-limit).
- **No single-letter names** except loop indices (`i`, `j`) and standard math
  (`x`, `y`, `n`).
- **Imports: stdlib, third-party, local** — three groups, alphabetized.

## Architecture Patterns

- **Layered, one-way dependency:** `api → services → repositories → db`.
  A service never imports an api module. A repository never imports a service.
- **Pure functions where you can.** Push side effects (I/O, time, randomness)
  to the edges; keep the middle testable.
- **Dependency injection via constructor or function argument.** Do not reach
  for global singletons. The only globals allowed are the config object and
  the logger.
- **One migration per PR.** Never edit a migration after it lands on main.
- **Feature work lives under `src/features/<name>/`** when it spans multiple
  layers. Cross-feature imports only go through `src/shared/`.
- **Tests live next to their layer:** `tests/api/`, `tests/services/`,
  `tests/repositories/`. Integration tests that cross layers go in
  `tests/integration/`.

## Forbidden

Do not do any of the following without updating `.ai/decisions.md` first.

- `TODO` / `FIXME` comments without a linked issue number.
- `print()` / `console.log()` in checked-in code. Use the logger.
- Catching a bare `Exception` / `catch (e)` without re-raising or handling a
  specific type.
- `sleep()` in request handlers.
- Accessing `os.environ` / `process.env` outside of `src/config.py`.
- Inline SQL outside the repository layer.
- Mutating function arguments.
- Adding a new top-level dependency.
- Disabling a test. If a test is flaky, fix it or delete it — never skip it
  silently.
- Committing generated files (other than lockfiles).

## When Adding New Code

Walk this checklist before calling the change done.

1. **Does this belong in an existing module?** Prefer extending over creating.
2. **Is there a test?** New logic needs at least one test that would fail
   without the change.
3. **Did I update types / schemas?** OpenAPI, DB models, TS types, etc.
4. **Did I update docs?** If a public-facing behavior changed, update
   `README.md`, `.ai/context.md`, or `docs/`.
5. **Did I run `make fmt lint test`?** All three must pass.
6. **Is the diff minimal?** No drive-by renames, no unrelated formatting.
   Separate commits for separate concerns.
7. **Did I remove what I replaced?** No dead code, no commented-out blocks,
   no "kept for reference" files.
