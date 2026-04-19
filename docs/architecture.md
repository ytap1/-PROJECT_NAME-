# Architecture: {{PROJECT_NAME}}

How the pieces fit together. Keep this in sync with the code — stale
architecture docs are worse than none.

## Directory Structure

```
{{PROJECT_NAME}}/
├── .ai/                      # AI-first context (read these first)
│   ├── context.md
│   ├── conventions.md
│   ├── decisions.md
│   ├── prompts.md
│   └── metrics.md
├── docs/
│   ├── setup.md              # local dev setup
│   └── architecture.md       # this file
├── src/                      # application code
│   ├── main.py               # entry point: wires app, middleware, routers
│   ├── config.py             # env -> typed config (only file that reads env)
│   ├── api/
│   │   └── v1/               # HTTP routes, one file per resource
│   ├── services/             # business logic, framework-agnostic
│   ├── repositories/         # the only layer that touches the database
│   ├── features/             # cross-layer feature slices
│   │   └── <feature>/
│   └── shared/               # utilities shared across features
├── tests/
│   ├── api/
│   ├── services/
│   ├── repositories/
│   └── integration/          # end-to-end, hits real DB
├── migrations/               # Alembic / Prisma / Flyway
├── scripts/                  # one-off operator scripts
├── .env.example
├── .gitignore
├── CHANGELOG.md
├── Makefile
└── README.md
```

If you add a new top-level directory, update this tree in the same PR.

## Data Flow

A typical request traces one path, and one only.

```
┌────────────┐   HTTP    ┌────────────┐   call   ┌──────────────┐   SQL   ┌──────────┐
│   Client   │ ────────▶ │  src/api/  │ ───────▶ │src/services/ │ ──────▶ │ src/     │
│  (browser, │           │  (router,  │          │  (business   │         │repositor-│
│  curl, SDK)│ ◀──────── │  I/O only) │ ◀─────── │   logic)     │ ◀────── │  ies/    │
└────────────┘ JSON/err  └────────────┘  value   └──────────────┘  rows   └──────────┘
                                                                              │
                                                                              ▼
                                                                        ┌──────────┐
                                                                        │ Postgres │
                                                                        └──────────┘
```

- **api → services → repositories → db.** One-way. No arrow goes backwards.
- **Errors** bubble up as typed exceptions or `Result` values. The api
  layer is the only place that turns them into HTTP responses.
- **Side effects** (network calls, file I/O, time, randomness) live at the
  edges. Services accept clocks/clients as parameters.
- **Background jobs** follow the same spine: a worker process calls a
  service function, same as a request handler would.

## Key Design Principles

1. **Small core, thin edges.** The api layer translates; the services
   decide; the repositories persist. Each should be boring.
2. **Explicit over implicit.** No "magic" dependency injection, no
   metaprogramming, no decorators that mutate state. A reader should be
   able to trace a request with grep.
3. **Make the right thing easy.** If a convention is constantly getting
   violated, the tooling is wrong — fix the tooling, not the people.
4. **Irreversible decisions get an ADR.** Database choice, auth model,
   deployment target. See `.ai/decisions.md`.
5. **Types at the boundaries.** Public functions, HTTP handlers, DB
   models, and background job payloads have explicit types. Inside a
   function, inference is fine.
6. **Tests describe behavior, not implementation.** If refactoring a
   private helper breaks a test, the test is wrong.
7. **Delete aggressively.** Dead code is a tax on every reader. When a
   feature is killed, kill its tests, migrations, and docs too.

## External Dependencies

Every outbound call the app makes. Each one is a failure mode.

| Dependency      | Purpose                   | Timeout | Retry?         | Failure mode                          |
|-----------------|---------------------------|---------|----------------|---------------------------------------|
| PostgreSQL      | primary datastore         | 5s      | no             | hard-fail the request                 |
| Redis (planned) | cache + rate limits       | 200ms   | no             | degrade to no-cache, log warning      |
| Stripe          | payments                  | 10s     | 3x exponential | bubble error to caller; no partial state |
| SendGrid        | transactional email       | 5s      | 3x exponential | enqueue for later; never block a request |
| OpenAI API      | LLM calls (feature-flagged) | 30s   | 2x exponential | feature returns "unavailable" message |

Rules:

- **Every outbound call has a timeout.** No exceptions.
- **Idempotent retries only.** If the call isn't idempotent, don't retry
  it — return the error and let the caller decide.
- **Circuit-break expensive deps.** If a dep is down for N seconds, stop
  calling it for M seconds. TBD which library.

## Testing Strategy

The pyramid, keep it weighted toward the base.

- **Unit tests** (`tests/services/`, `tests/repositories/`): fast, no I/O,
  no DB. Most tests live here. Run in <5 seconds total.
- **Integration tests** (`tests/integration/`): spin up a real DB via
  `testcontainers` or a scratch schema. Cover the happy path of every
  endpoint and any cross-layer invariant.
- **Contract tests** (future): if and when external consumers land, pin
  the OpenAPI schema and fail CI on breaking changes.
- **End-to-end tests** (future): one smoke test per critical user flow,
  run against a deployed preview environment.
- **What we don't test:** the framework itself, the ORM itself, generated
  code, one-line passthroughs.

Coverage is a diagnostic, not a target. Aim for "every branch I'd be
nervous to change has a test," not 100%.
