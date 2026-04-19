# {{PROJECT_NAME}}

> {{PROJECT_DESCRIPTION}}

## Status

- **Phase:** pre-alpha
- **Version:** 0.1.0 (see [CHANGELOG.md](./CHANGELOG.md))
- **Last updated:** {{CURRENT_DATE}}
- **Deployed?** No — local development only.
- **Stable?** No. Expect breaking changes without notice until 1.0.

## Quick Start

```bash
# 1. Clone
git clone https://github.com/<org>/{{PROJECT_NAME}}.git
cd {{PROJECT_NAME}}

# 2. Install dependencies
make install           # or: uv sync / npm install / cargo build

# 3. Configure environment
cp .env.example .env
# edit .env with your local values

# 4. Run
make dev               # or: uvicorn src.main:app --reload / npm run dev

# 5. Verify
curl http://localhost:8000/health
# -> {"status":"ok"}
```

Full instructions, including prerequisites and troubleshooting, are in
[docs/setup.md](./docs/setup.md).

## What Works

- Repository scaffolding with AI-first docs in [`.ai/`](./.ai/).
- Base `.gitignore`, `CHANGELOG.md`, and `docs/` structure.
- Template prompts for common AI coding sessions.

Replace this list with actual working features as you ship them. Example:

- `GET /health` — liveness probe.
- `POST /imports` — accepts a CSV up to 5 MB and returns a job id.
- `GET /imports/{id}` — returns job status and any parse errors.

## What's Next

The short list. If it isn't here, it isn't being worked on.

- [ ] Pick and document the stack in `.ai/context.md` (`{{TECH_STACK}}`).
- [ ] Stand up the app entry point (`src/main.py` or equivalent) and a
      `/health` endpoint.
- [ ] Wire CI: format, lint, test on every PR.
- [ ] Write the first ADR for the chosen datastore.
- [ ] First end-to-end feature slice behind a feature flag.

## Documentation

- [.ai/context.md](./.ai/context.md) — what this is, current state, constraints
- [.ai/conventions.md](./.ai/conventions.md) — coding rules
- [.ai/decisions.md](./.ai/decisions.md) — architectural decision records
- [.ai/prompts.md](./.ai/prompts.md) — prompt templates for AI sessions
- [.ai/metrics.md](./.ai/metrics.md) — AI-assist usage log
- [docs/setup.md](./docs/setup.md) — local development setup
- [docs/architecture.md](./docs/architecture.md) — directory structure, data flow
- [CHANGELOG.md](./CHANGELOG.md) — release history
