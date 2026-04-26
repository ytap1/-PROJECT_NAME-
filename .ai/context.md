# Context: {{PROJECT_NAME}}

> Single source of truth Claude reads at the start of every session.
> Keep under ~150 lines. Link out instead of inlining.

Last updated: {{CURRENT_DATE}}

## What This Is

{{PROJECT_NAME}} is {{PROJECT_DESCRIPTION}}.

Answer in one sentence each:

- **Problem:** <what specific pain this solves>
- **User:** <who uses it>
- **Success:** <observable outcome that means it works>

## Current State

- **Phase:** pre-alpha / alpha / beta / production (pick one)
- **Version:** 0.1.0
- **Deployed?** No / URL
- **Users?** None / count
- **Known broken:** <list load-bearing things that fail>

## Stack

{{TECH_STACK}}

Fill in once chosen. Do not assume a stack — this template is intentionally
neutral. List only what is actually picked: language, framework, storage,
deploy target.

## Key Constraints

Project-specific rules. Edit per project.

- <e.g. "Single HTML file — no build step.">
- <e.g. "No new top-level dependencies without an ADR.">
- <e.g. "Secrets only via env vars, never committed.">

## Entry Points

Where Claude should start reading when picking up cold. Links, not commands.

- **Main file(s):** <path(s)>
- **Config:** <path>
- **Tests (if any):** <path>
- **Deploy config:** <path, e.g. `.github/workflows/deploy.yml`>

## See Also

- `.ai/conventions.md` — coding rules
- `.ai/decisions.md` — ADRs
- `docs/workflow.md` — full working model
- `docs/architecture.md` — structure + data flow
