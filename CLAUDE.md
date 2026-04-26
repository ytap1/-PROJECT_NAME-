# CLAUDE.md

Read this first. Then `.ai/context.md`, then `.ai/conventions.md`.

## Working Model (non-negotiable)

- **Branch-per-feature.** All work lives on a short-lived feature branch. No
  direct commits to `main` except emergency hotfixes (still need a follow-up PR).
- **PR → review → merge.** Every PR gets at least a self-review — run
  `/review` before merging.
- **Full CLI access.** Git, package managers, test runners, and build tools
  are all available and expected.
- **Local development first.** Verify changes locally before pushing. CI
  catches what local misses — don't rely on CI as a first-pass linter.
- **Deployment:** <fill in per-project — e.g., merged PRs auto-deploy via
  GitHub Actions, or deploy manually with `<command>`>.

## Token Discipline

- Short, accurate prose beats long, hedged prose.
- No restating obvious context back to the user.
- Trim every doc you touch.

## Read in Order

1. [.ai/context.md](./.ai/context.md) — what this is, current state, stack
2. [.ai/conventions.md](./.ai/conventions.md) — coding rules
3. [.ai/decisions.md](./.ai/decisions.md) — ADRs
4. [docs/workflow.md](./docs/workflow.md) — full working model
5. [docs/architecture.md](./docs/architecture.md) — structure + data flow
6. [.ai/prompts.md](./.ai/prompts.md) — session templates
