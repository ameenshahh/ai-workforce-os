# ADR 0008: Server-Side Skills Only

## Status

Accepted — 2026-07-11

## Context

Skills are the capabilities a Worker invokes through LLM tool calling (create a lead, book a calendar slot, search a CRM). Allowing customers or third parties to upload and run arbitrary skill code would introduce serious security (sandboxing, isolation), reliability (untrusted failures), versioning, and support complexity — before the product has validated demand. The runtime is the core differentiator and must stay transparent and debuggable (ADR 0001).

## Decision

For the MVP, skills are implemented as registered server-side TypeScript executors that satisfy a uniform contract (see `docs/05-ai/02-skills-framework.md`). Workers may only use registered, pinned skills. There is no dynamic third-party or customer-uploaded code execution. Skills reach external systems only through the Integrations domain, with credentials held by Secrets — a skill never handles OAuth or raw credentials.

## Consequences

The MVP is far safer and easier to reason about: every capability is reviewed, versioned, permission-checked, schema-validated, and idempotent where side-effecting. Execution is fully observable via `skill_executions`. The cost is that adding a new capability requires a code change and deploy rather than a runtime upload, and a third-party marketplace is not possible yet. Marketplace-style external skills with sandboxed execution are deliberately deferred to a future Marketplace domain, behind the same skill contract so the boundary is stable.

## Alternatives Considered

- **Customer-uploaded code / plugin sandboxes** — rejected for MVP: heavy security and operational burden (isolation, resource limits, abuse) unjustified before product validation.
- **Webhook-based skills (call out to customer endpoints)** — deferred: viable later as a controlled Integration/skill type, but adds latency, reliability, and validation concerns not needed now.
- **A general workflow/automation engine as the capability layer** — rejected as the primary model: Workflow exists for deterministic automation (ADR-adjacent), but AI tool calling needs typed, permissioned, idempotent server-side skills.
