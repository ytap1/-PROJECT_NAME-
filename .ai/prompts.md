# Prompts: {{PROJECT_NAME}}

Copy-paste templates for common AI coding sessions. Fill in the `<...>`
slots before pasting. These are starting points — edit them as the project
matures.

## Bootstrap a New Repo

Run this once, in a fresh empty repo, to generate the same `.ai/` + `docs/`
scaffold this project uses. Replace the four `{{...}}` placeholders with
your project's values before pasting.

````
Create a complete AI-optimized GitHub repository setup using placeholders for customization.

PROJECT INPUT PLACEHOLDERS:
Use these exact placeholders in all generated files:
- {{PROJECT_NAME}}
- {{PROJECT_DESCRIPTION}}
- {{TECH_STACK}}
- {{CURRENT_DATE}}

GENERATE THESE FILES WITH FULL CONTENTS:

1. .ai/context.md
2. .ai/conventions.md
3. .ai/decisions.md
4. .ai/prompts.md
5. .ai/metrics.md
6. README.md
7. docs/setup.md
8. docs/architecture.md
9. CHANGELOG.md
10. .gitignore (language-agnostic base: .env, .DS_Store, node_modules/, __pycache__/, *.pyc, .vscode/, .idea/, dist/, build/)

REQUIREMENTS:
- .ai/context.md: Include "What This Is", "Current State", "Stack", "Key Constraints", "Entry Points" sections with placeholders
- .ai/conventions.md: Include "File Naming", "Code Style", "Architecture Patterns", "Forbidden", "When Adding New Code" sections with language-agnostic defaults
- .ai/decisions.md: Include template structure with one example decision
- .ai/prompts.md: Include templates for: "Starting a Session", "Adding a Feature", "Debugging", "Refactoring", "Code Review", "Update Context"
- .ai/metrics.md: Include tracking table with columns: Date | Feature | AI Sessions | Total Tokens | Bugs Found | Notes
- README.md: Include "Status", "Quick Start", "What Works", "What's Next", "Documentation" sections
- docs/setup.md: Include "Prerequisites", "First Time Setup", "Environment Variables", "Verify Installation", "Troubleshooting" sections
- docs/architecture.md: Include "Directory Structure", "Data Flow", "Key Design Principles", "External Dependencies", "Testing Strategy" sections
- CHANGELOG.md: Include version 0.1.0 template with "Added/Changed/Fixed" sections

OUTPUT FORMAT:
For each file:
1. Show full file path (e.g., `.ai/context.md`)
2. Provide complete file contents in a markdown code block
3. Add a brief note: "Customize now: [what]" or "Customize later: [what]"

After all files, provide:
1. Git initialization commands (init, add, commit, remote add, push)
2. Exact prompt to start first AI coding session (should reference .ai/ files)

Make all content copy-paste-ready. Use realistic examples in templates, not abstract placeholders.
````

## Starting a Session

```
You are working on {{PROJECT_NAME}}: {{PROJECT_DESCRIPTION}}.

Before touching any code, read these in order:
1. .ai/context.md — what this is, current state, stack, constraints
2. .ai/conventions.md — coding rules (naming, layout, forbidden patterns)
3. .ai/decisions.md — why things are the way they are

Then confirm back to me, in 3 bullets:
- the current phase and what is known-broken
- the one-way dependency rule for layers
- two things the "Forbidden" list prohibits

Do not write code until I approve your summary. Today's goal: <goal>.
```

## Adding a Feature

```
Feature: <one-sentence description>

User story:
  As a <role>, I want to <capability>, so that <outcome>.

Acceptance criteria:
  - <concrete, testable statement>
  - <concrete, testable statement>
  - <error/edge case behavior>

Constraints (from .ai/context.md and .ai/conventions.md):
  - Must live under src/features/<name>/ (or justify why not).
  - Needs integration test in tests/integration/.
  - No new top-level dependencies without an ADR.

Deliver in this order, pausing for review after each:
  1. A short plan: files you will create/modify, and the public API.
  2. The test(s) — failing first.
  3. The implementation that makes the tests pass.
  4. Doc updates (README / context.md / architecture.md as needed).
```

## Debugging

```
Bug: <one-line symptom>

Repro steps:
  1. <step>
  2. <step>
  3. <observed behavior>

Expected: <what should happen>
Environment: <OS, runtime version, commit sha>

Relevant files (start here, then expand as needed):
  - <path>
  - <path>

Logs / stack trace:
```
<paste>
```

Do this:
  1. Form a hypothesis about the root cause. State it before reading more code.
  2. Identify the smallest change that would confirm or reject the hypothesis.
  3. If the hypothesis survives, propose a fix AND a regression test.
  4. Do not "fix" by widening a try/except or by silencing the symptom.
```

## Refactoring

```
Refactor target: <module or pattern>

Why now: <specific pain point — not "it feels messy">
Out of scope: <what you must NOT change in this pass>

Rules:
  - Behavior must not change. Tests must pass before and after with no edits.
  - If a test needs to change to keep passing, stop and ask.
  - One concern per commit. No drive-by renames in the same diff.
  - If you find a bug mid-refactor, note it and keep going — fix in a
    separate PR.

Deliver:
  1. A "before" sketch: current shape, in 5–10 lines of prose or pseudocode.
  2. An "after" sketch: target shape, same form.
  3. The diff.
  4. Evidence tests still pass: paste the test runner output.
```

## Code Review

```
Review the diff below against .ai/conventions.md and .ai/context.md.

Give me, in order:
  1. Blocking issues (correctness, security, broken invariants).
  2. Convention violations with file:line references.
  3. Suggestions (non-blocking, marked "nit:").
  4. What's good — at least one thing.

Do not rewrite the code. Point at the line and describe the change in one
sentence. If something is ambiguous in the conventions, say so and
recommend an ADR.

Diff:
```
<paste diff or PR link>
```
```

## Update Context

```
We just finished: <what shipped or changed>.

Update .ai/context.md so that a fresh AI session starting tomorrow has
accurate ground truth. Specifically:

  - "Current State" — phase, version, what's deployed, what's broken.
  - "Entry Points" — add/remove any new entry files, scripts, or commands.
  - "Key Constraints" — add any new hard rules this change introduced.
  - "Last updated" date at the top.

If a significant choice was made (new dep, new pattern, reversed an earlier
call), also add an entry to .ai/decisions.md using the template there.

Show me the diff before saving.
```
