# Decisions: {{PROJECT_NAME}}

Append-only log of architectural and technical decisions. Each entry
answers: what did we pick, what did we reject, and why?

- **Never rewrite history.** Reversed decisions get a new entry that
  supersedes the old one. Cross-link them.
- **Keep entries short.** Link out if it needs more than a page.
- **Number sequentially** (ADR-0001, ADR-0002, ...).

## Template

```markdown
## ADR-NNNN: <short title>

- **Date:** YYYY-MM-DD
- **Status:** Proposed | Accepted | Superseded by ADR-XXXX | Deprecated

### Context
<problem and forces, 2-5 sentences>

### Decision
<the choice, 1-2 sentences>

### Alternatives Considered
- **Option A:** why rejected.
- **Option B:** why rejected.

### Consequences
- **Positive:** <what gets easier>
- **Negative:** <what gets harder>
- **Follow-ups:** <what this unlocks or requires next>
```

---

## ADR-0001: Branch-per-feature; merge to `main` via PR

- **Date:** {{CURRENT_DATE}}
- **Status:** Accepted

### Context

Laptop + CLI workflow with full git tooling available. This template is
intended for use by a small team (up to 5-10 people), so habits formed here
need to scale beyond solo work. Direct pushes to `main` eliminate the review
gate that catches regressions before they land.

### Decision

All changes live on a short-lived feature branch. Merge to `main` only via
PR after at least a self-review (`/review`). Emergency hotfixes may push
directly but require a follow-up PR.

### Alternatives Considered

- **Push directly to `main`:** removes friction but eliminates the pre-merge
  review gate. Appropriate for solo/no-CI projects; not appropriate here.
- **Trunk-based with feature flags:** future option once the project has
  enough surface area to need it.

### Consequences

- **Positive:** every change has a review artifact; `main` stays deployable;
  CI runs on PRs before merge.
- **Negative:** slight overhead vs. direct push. Mitigation: keep branches
  short-lived and PRs small.
- **Follow-ups:** add branch protection rules on `main` once the team grows
  past two contributors.
