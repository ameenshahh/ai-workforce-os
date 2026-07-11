# Testing Strategy Specification

## Purpose

This document defines the testing strategy for AI Workforce OS — the test levels, the non-negotiable test requirements (tenant isolation, authorization, runtime, skills, webhooks, idempotency), test-data practices, coverage expectations, and how testing ties into the quality gates that guard every merge. It is the implementation contract for the testing concerns in `docs/00-foundation/MASTER_ARCHITECTURE.md` §25, backend §25, and `docs/00-foundation/QUALITY_GATES.md`. It consolidates the per-spec `Testing` sections into one strategy.

## Scope

This spec covers test levels and their responsibilities, the critical test cases every implementation must include, fixtures and test-data strategy, coverage and CI gates, and per-domain testing focus. It defines what must be tested and to what standard, not the test code itself.

It does not cover: individual test implementations, the CI pipeline scripts, or load-testing tooling selection. No application code.

## Goals

- Prove the invariants that matter most: tenant isolation, authorization, bounded/observable runtime, safe skills, and idempotent async processing.
- Keep the test suite fast and reliable by weighting toward unit and repository tests.
- Make every spec's `Acceptance Criteria` verifiable by concrete tests.
- Gate merges on the tests that protect correctness and multi-tenancy.

## Non Goals

- No exhaustive end-to-end coverage of every UI path (weight toward unit/integration).
- No performance/load SLAs in the MVP beyond basic latency checks (see Future Work).
- No testing of third-party providers' internals (mock at the boundary).
- No prescription of a specific test runner here (chosen at implementation time; must support the levels below).

## Test Levels

A pyramid: many fast unit tests, a solid layer of repository and API/integration tests, and a thin layer of end-to-end flows.

- **Unit tests** — services and pure logic (business rules, conflict resolution, planners, validators). Fast, no I/O, dependencies mocked.
- **Repository tests** — persistence and, critically, tenant scoping, against a real PostgreSQL (with pgvector where relevant). Prove `organization_id` filtering.
- **API / integration tests** — controllers with DTO validation, guards, and envelopes; module-to-module interactions through public services.
- **Queue / worker tests** — job processors for idempotency, bounded retries, and fresh-state loading.
- **End-to-end flows** — a thin set of the highest-value paths (inbound message → response; publish Agent version; connect integration; upload knowledge).

## Critical Test Requirements

These must exist and pass; they are gate conditions, not optional.

**Tenant isolation.**
- A user from org A cannot read or mutate org B's data (per resource: workers, conversations, customers, knowledge, memory, secrets, integrations, operators).
- Every repository method scoped by `organization_id` is tested to reject cross-org access.
- Retrieval (knowledge, memory) never returns another tenant's content.

**Authorization.**
- Protected endpoints reject unauthenticated and unauthorized requests before service execution.
- Sensitive actions require their specific permission (e.g., `workers.publish`, `secrets.manage`, `operators.manage`, `integrations.connect`).
- Client-supplied role/org claims are never trusted.

**AI Runtime.**
- The tool loop terminates at the iteration limit and never exceeds it.
- No response is produced while a conversation is in human handoff.
- A blocking policy routes to handoff instead of sending.
- A run is reconstructable from `runtime_runs` + `runtime_steps` + `llm_calls` + `skill_executions`.
- A run executes against a pinned `worker_version_id` (reproducibility).

**Skills.**
- A skill not pinned to the Worker Version cannot be exposed or executed.
- Execution without the required permission is rejected before side effects.
- Input and output are schema-validated; invalid input/output fails.
- Side-effecting skills are idempotent under retry.
- External-system skills obtain connections via Integrations and never see raw credentials.

**Webhooks & idempotency.**
- Webhook signature verification rejects invalid signatures.
- A duplicate inbound event does not create duplicate messages or duplicate runs.
- Reprocessing any queued job (same idempotency key) produces no duplicate side effects.
- A failed outbound message is recorded.

**Secrets.**
- No table other than `secrets` stores plaintext credentials (asserted structurally / in review and by tests where feasible).
- Secret values are never returned in bulk or logged.

## Test Data & Fixtures

- Use factories/builders to create tenant-scoped entities (organization → membership → worker/customer/conversation) so tests start from a valid, isolated tenant.
- Each test seeds its own data and cleans up; tests never depend on shared mutable state or ordering.
- Mock external providers (LLM, channels, integrations) at the boundary; assert the interaction, not the provider.
- Deterministic seeds for anything involving ordering, chunking, or IDs where reproducibility is asserted.

## Coverage & Gates

- Coverage is meaningful, not just numeric: authorization-sensitive and tenant-scoped code paths must be covered; the critical test requirements above are mandatory.
- CI runs lint, type-check (strict, no `any` in core logic), unit, repository, API, and queue tests on every PR; the build fails on any failure.
- A PR that adds behavior not described in the specs updates the docs first (per `CONTRIBUTING.md`), and adds tests for new business rules, authorization, and tenant scoping.
- Merge is blocked unless the critical tests pass and the quality gates in `QUALITY_GATES.md` are met.

## Per-Domain Testing Focus

- **Organization / Identity & Access** — membership uniqueness, role gating, session/token handling, permission evaluation.
- **Worker** — version immutability, pinned skill/knowledge snapshots, coherent-config validation.
- **AI Runtime** — the runtime critical tests above.
- **Skill** — the skill critical tests above.
- **Knowledge** — deterministic chunking, dedup by `content_hash`, org+Worker-scoped retrieval, async ingestion.
- **Memory** — supersede-not-duplicate conflict resolution, privacy filtering, idempotent extraction, scoped retrieval.
- **Conversation / Operators** — handoff status vs. operator ownership, one active assignment per conversation, delivery status.
- **Channel** — webhook verification and dedup, adapter normalization, outbound attempt recording.
- **Integrations / Secrets** — connection lifecycle, tokens only in Secrets, revocation propagation.
- **Workflow** — determinism, no-op on unmet conditions, reuse of the Skill executor, run auditing.

## Security Testing

- Authorization and tenant-isolation tests (above) are the core security tests.
- Input validation and injection safety on all external inputs (API, webhooks, tool arguments).
- Redaction: assert secrets/sensitive data are not present in logs or persisted summaries.
- Rate-limiting behavior where applicable.

## Performance Testing

- Basic latency checks on hot paths (webhook ack, inbox reads, retrieval) in the MVP.
- Assert boundedness rather than raw throughput: tool loop caps, retry caps, pagination on lists.
- Load/soak testing is Future Work.

## Future Work

- Contract testing against a generated OpenAPI spec.
- Load and soak testing with defined SLAs.
- Evaluation harness for Agent behavior quality (AI Evaluations domain).
- Mutation testing on critical modules.

## Implementation Checklist

- [ ] Test runner and factories/fixtures supporting tenant-scoped setup.
- [ ] Repository tests proving `organization_id` scoping on every tenant-owned entity.
- [ ] Authorization tests for protected endpoints and sensitive permissions.
- [ ] AI Runtime tests: bounded loop, handoff gating, reproducibility, trace completeness.
- [ ] Skill tests: pinning, permissions, schema validation, idempotency, Integrations boundary.
- [ ] Webhook tests: signature verification and deduplication.
- [ ] Queue tests: idempotency and bounded retries.
- [ ] CI gate wiring: lint, type-check, tests block merge; tie to QUALITY_GATES.

## Acceptance Criteria

- [ ] The critical test requirements (tenant isolation, authorization, runtime, skills, webhooks, idempotency, secrets) exist and pass.
- [ ] Repository tests prove organization scoping for all tenant-owned entities.
- [ ] Each spec's Acceptance Criteria are covered by concrete tests.
- [ ] CI blocks merge on lint, type-check, or test failure, and enforces the quality gates.
- [ ] The strategy matches §25 of `MASTER_ARCHITECTURE.md`, backend §25, and `QUALITY_GATES.md`.
