# Decisions: {{PROJECT_NAME}}

An append-only log of architectural and technical decisions. Each entry
answers: what did we pick, what did we reject, and why?

- **Never rewrite history.** If a decision is reversed, add a new entry that
  supersedes the old one. Cross-link them.
- **Keep entries short.** If it needs more than a page, link to a doc.
- **Number entries sequentially** (ADR-0001, ADR-0002, ...).

## Template

```markdown
## ADR-NNNN: <short title>

- **Date:** YYYY-MM-DD
- **Status:** Proposed | Accepted | Superseded by ADR-XXXX | Deprecated
- **Deciders:** <names or roles>

### Context

What is the problem? What forces are at play? 2–5 sentences.

### Decision

The choice we made, in one or two sentences.

### Alternatives Considered

- **Option A:** why rejected.
- **Option B:** why rejected.

### Consequences

- **Positive:** what gets easier.
- **Negative:** what gets harder or what we give up.
- **Follow-ups:** anything this unlocks or requires next.
```

---

## ADR-0001: Use PostgreSQL for primary storage

- **Date:** {{CURRENT_DATE}}
- **Status:** Accepted
- **Deciders:** founding team

### Context

We need a primary datastore for user accounts, ledger entries, and import
jobs. Reads are ~10x writes. We need transactional integrity across the
ledger and a clear path to JSON columns for semi-structured import payloads.

### Decision

Use PostgreSQL 16 as the single primary store. Access it through SQLAlchemy
2.x with Alembic migrations. No ORM calls outside `src/repositories/`.

### Alternatives Considered

- **SQLite:** great for local dev, but we want a single prod-parity target
  and expect concurrent writes from background jobs.
- **MongoDB:** our core data is relational (users → accounts → entries).
  Mapping it to documents adds friction without a clear upside.
- **DynamoDB:** lock-in concerns and we don't yet have access patterns
  stable enough to design keys.

### Consequences

- **Positive:** strong consistency, rich query language, `jsonb` escape
  hatch for semi-structured data, mature ecosystem.
- **Negative:** ops cost of running Postgres in prod (managed host needed);
  schema migrations require discipline.
- **Follow-ups:** pick a managed host (candidate: Supabase or Neon); add a
  CI step that runs migrations against a scratch DB.
