# Metrics: {{PROJECT_NAME}}

Track AI-assisted work so you can tell — later — what actually helped.
Fill in a row per meaningful session. "Meaningful" = you shipped code,
not "I asked a quick question."

## How to use this log

- **Date:** YYYY-MM-DD the session ended.
- **Feature:** short tag. Match a commit, PR, or ADR when possible.
- **AI Sessions:** how many distinct conversations it took to finish.
- **Total Tokens:** approximate, from your provider's usage dashboard.
- **Bugs Found:** bugs discovered *during or because of* the AI session,
  whether you fixed them or not.
- **Notes:** one line. What worked, what didn't, what you'd do differently.

Keep rows terse. If you need a paragraph, link to a PR or an ADR.

## Log

| Date       | Feature                  | AI Sessions | Total Tokens | Bugs Found | Notes                                                            |
|------------|--------------------------|-------------|--------------|------------|------------------------------------------------------------------|
| {{CURRENT_DATE}} | repo scaffolding (.ai/*) | 1           | ~12k         | 0          | Initial template. No app code yet.                                |
| 2026-04-20 | csv import v1            | 3           | ~85k         | 2          | Caught an off-by-one in row indexing. Worth the round trips.     |
| 2026-04-22 | auth: refresh tokens     | 2           | ~40k         | 1          | Claude missed the middleware ordering on first try; fixed on 2nd. |
| 2026-04-25 | refactor repositories    | 1           | ~30k         | 0          | Straight mechanical refactor. Fast with a tight prompt.           |

## Rollups (update weekly)

- **This week's sessions:** <n>
- **Avg sessions per feature:** <n>
- **Features shipped with zero follow-up bugs:** <n>
- **Single biggest time sink:** <one line>

## Prompts worth saving

When a prompt works unusually well, lift it into `.ai/prompts.md`. When
one fails repeatedly, note it here so future-you doesn't repeat the
mistake.

- 2026-04-22 — "write the tests first" consistently produces better diffs
  than "write the code then the tests." Promoted to `prompts.md`.
