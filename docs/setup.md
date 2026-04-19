# Setup: {{PROJECT_NAME}}

How to get {{PROJECT_NAME}} running on your machine. If something in this
doc is wrong, fix the doc in the same PR as the code change that broke it.

## Prerequisites

Install these before anything else. Versions are tested minimums — newer
usually works, older probably doesn't.

- **OS:** macOS 13+ or Linux (Ubuntu 22.04+ / Debian 12+). Windows via WSL2 only.
- **Git:** 2.40+ (`git --version`)
- **Make:** any GNU make 4.x (`make --version`)
- **Runtime:** the language runtime from `{{TECH_STACK}}`. Concrete example:
  - Python 3.12+ (`python --version`) — install via `uv python install 3.12`
    or `pyenv install 3.12`.
  - Node 20 LTS (`node --version`) — install via `nvm` or Volta.
  - Go 1.22+ / Rust 1.78+ / etc. as applicable.
- **Package manager:** the one listed in `{{TECH_STACK}}`. Example: `uv`,
  `pnpm`, `cargo`.
- **Database:** PostgreSQL 16 running locally. Docker is the easy path:
  `docker run -d --name {{PROJECT_NAME}}-db -p 5432:5432 -e POSTGRES_PASSWORD=dev postgres:16`.

## First Time Setup

```bash
# 1. Clone and enter the repo
git clone https://github.com/<org>/{{PROJECT_NAME}}.git
cd {{PROJECT_NAME}}

# 2. Install dependencies
make install
# Behind the scenes this runs (example): uv sync --all-extras

# 3. Create your local env file
cp .env.example .env
# Open .env in your editor and fill in any blanks.

# 4. Create the local database and apply migrations
make db-create         # creates the `{{PROJECT_NAME}}_dev` database
make migrate           # applies Alembic / Prisma / whatever migrations

# 5. Seed with fixtures (optional but recommended)
make seed

# 6. Run the app
make dev
```

The app should now be listening on `http://localhost:8000`.

## Environment Variables

Every variable the app reads. Authoritative list lives in
`src/config.py` (or equivalent). `.env.example` mirrors this table.

| Variable            | Required | Default                                | What it does                              |
|---------------------|----------|----------------------------------------|-------------------------------------------|
| `APP_ENV`           | yes      | `development`                          | Switches config profile. Values: `development`, `test`, `production`. |
| `DATABASE_URL`      | yes      | `postgresql://postgres:dev@localhost:5432/{{PROJECT_NAME}}_dev` | DSN for the primary database.             |
| `LOG_LEVEL`         | no       | `info`                                 | One of `debug`, `info`, `warn`, `error`.  |
| `SECRET_KEY`        | yes      | *(none)*                               | Signing key for sessions/JWTs. Generate with `openssl rand -hex 32`. |
| `OPENAI_API_KEY`    | no       | *(none)*                               | Only needed if the AI features are on.    |
| `PORT`              | no       | `8000`                                 | HTTP port the app binds to.               |

Rules:

- **Never commit `.env`**. It is in `.gitignore` for a reason.
- **Never read env vars outside `src/config.py`**. See
  `.ai/conventions.md` → "Forbidden."
- **Rotate `SECRET_KEY` on any suspected leak.** All sessions invalidate.

## Verify Installation

A green install passes all three checks.

```bash
# 1. The app responds
curl -sf http://localhost:8000/health
# expected: {"status":"ok"}

# 2. The test suite passes
make test

# 3. The linter is clean
make lint
```

If any of these fails on a fresh clone, treat it as a P0 and fix the doc
or the code.

## Troubleshooting

### `make install` fails on a native extension

Usually a missing system library. On Debian/Ubuntu:

```bash
sudo apt-get install -y build-essential libpq-dev libffi-dev
```

On macOS:

```bash
xcode-select --install
brew install libpq && brew link --force libpq
```

### `make migrate` says "database does not exist"

You skipped `make db-create`, or your `DATABASE_URL` points somewhere that
has no database created yet. Confirm with:

```bash
psql "$DATABASE_URL" -c '\conninfo'
```

### Port 8000 already in use

Either kill the other process or set `PORT=8001` in `.env` and rerun.

```bash
lsof -ti :8000 | xargs kill
```

### Tests pass locally, fail in CI

The most common cause is environment drift: an env var set on your shell
but not in `.env.example`, or a tool version that's newer locally than in
CI. Compare `python --version` / `node --version` against
`.github/workflows/*.yml`.

### Still stuck

Open an issue with: OS + version, runtime version, the exact command you
ran, and the full output. Redact `.env` values before pasting.
