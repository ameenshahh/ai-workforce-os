# Quality Gates

Every meaningful code change must satisfy these gates before merge.

## Required for All Changes

- Code follows `docs/CLAUDE.md`.
- Relevant documentation is updated.
- No unrelated refactors.
- No hardcoded secrets.
- No unscoped organization queries.
- No direct Prisma access outside repositories.
- No business logic in controllers.
- No `any` unless explicitly justified.
- No unbounded retries.
- No unhandled external API timeouts.
- No sensitive data in logs.
- Lint passes.
- Type checking passes.
- Relevant tests pass.

## Required for API Changes

- DTOs exist for request and response.
- Validation is explicit.
- Authorization guard is applied.
- Standard response envelope is used.
- Errors use the standard error format.
- OpenAPI documentation is updated.
- Pagination, filtering, and sorting follow platform conventions where applicable.

## Required for Database Changes

- Migration is included.
- Indexes are documented.
- Foreign keys and unique constraints are intentional.
- Tenant isolation is enforced.
- Soft delete and audit fields are considered.
- Backfill strategy is documented when needed.

## Required for Runtime Changes

- Runtime step is logged.
- Execution timeline is preserved.
- Tool calls are auditable.
- Prompt inputs are traceable without leaking secrets.
- Token and cost usage are recorded.
- Retries are bounded.
- Failure behavior is documented.
- Human handoff impact is considered.

## Required for Skill Changes

- Skill schema is defined.
- Skill permission is defined.
- Input validation exists.
- Output validation exists.
- External calls have timeout.
- External calls are retried only when safe.
- Side effects are idempotent where possible.
- Skill execution is logged and measured.

## Required for Channel Changes

- Webhook verification is implemented.
- Inbound events are normalized.
- Duplicate webhook delivery is handled.
- Outbound send errors are captured.
- Rate limits are respected.
- Channel-specific payloads do not leak into runtime internals.

## Required for Security-Sensitive Changes

- Threat model is considered.
- Authorization tests are added.
- Tenant isolation tests are added.
- Secrets are encrypted or delegated to secret storage.
- Audit logs are emitted.

