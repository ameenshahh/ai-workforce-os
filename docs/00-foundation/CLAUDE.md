# CLAUDE.md

This file contains standing instructions for Claude Code and any AI assistant working in this repository.

## 1. Role

You are acting as a senior software engineer implementing AI Workforce OS from the engineering specifications in `docs/`.

Your job is not to invent the architecture. Your job is to implement the documented architecture carefully, consistently, and with tests.

## 2. Current Project Phase

The project is in the Architecture Phase until application code is intentionally introduced.

During the Architecture Phase:

- Do not scaffold NestJS or Next.js unless explicitly asked.
- Do not install packages unless explicitly asked.
- Do not create application source folders unless the implementation phase has started.
- Focus on complete, internally consistent documentation.

## 3. Source of Truth Order

When instructions conflict, follow this order:

1. User's current explicit request.
2. `docs/00-foundation/MASTER_ARCHITECTURE.md`
3. `docs/00-foundation/DECISIONS.md`
4. Detailed subsystem spec in `docs/`
5. `docs/00-foundation/ENGINEERING_PRINCIPLES.md`
6. `docs/00-foundation/QUALITY_GATES.md`
7. Existing code conventions

If a conflict remains, stop and ask for clarification.

## 4. Product Terminology

- Use "Agent" for customer-facing UI copy when appropriate.
- Use `Worker` in backend architecture, entities, services, repositories, and internal documentation.
- A platform `User` is a logged-in dashboard user.
- A `Customer` is an external person messaging through a channel.
- An `Organization` is a tenant.
- A `Skill` is a server-side executable capability.
- A `Channel` is a communication transport.

See `docs/00-foundation/GLOSSARY.md` before introducing new terms.

## 5. Backend Standards

Use:

- NestJS
- TypeScript strict mode
- Prisma
- PostgreSQL
- BullMQ
- Redis
- Socket.IO only where realtime is required

Do not use:

- Microservices for MVP
- Kafka for MVP
- Kubernetes for MVP
- GraphQL for MVP
- LangChain or AutoGen as the core runtime
- Dynamic untrusted plugin execution

## 6. Module Structure

Use feature-first modules:

```text
src/modules/<feature>/
  <feature>.module.ts
  <feature>.controller.ts
  <feature>.service.ts
  <feature>.repository.ts
  dto/
  entities/
  events/
  jobs/
  tests/
```

Do not organize the backend by global technical folders such as all controllers in one folder and all services in another.

## 7. Controller Rules

Controllers:

- Handle HTTP transport only.
- Validate DTOs.
- Read authenticated request context.
- Call application services.
- Return standard response envelopes.

Controllers must not:

- Contain business logic.
- Call Prisma.
- Call repositories directly.
- Execute skills.
- Call external APIs.
- Build prompts.

## 8. Service Rules

Services:

- Own business workflows.
- Enforce module business rules.
- Call repositories.
- Call public services from other modules only through documented interfaces.
- Publish events when meaningful domain changes occur.

Services must not:

- Use raw SQL unless documented and isolated.
- Bypass repositories.
- Leak Prisma models to controllers.

## 9. Repository Rules

Repositories:

- Are the only normal layer that accesses Prisma.
- Scope every tenant-owned query by `organizationId`.
- Return domain entities or explicit persistence models, not arbitrary Prisma payloads.
- Own transactions where the transaction is purely persistence-focused.

Repositories must not:

- Contain cross-module business logic.
- Call external APIs.
- Call LLM providers.
- Publish domain events directly unless explicitly documented.

## 10. Database Rules

- Use UUID primary keys.
- Use `organization_id` on tenant-owned tables.
- Use `created_at` and `updated_at` on persistent tables.
- Use `deleted_at` for soft-deletable business records.
- Use foreign keys where relationships are strong.
- Add indexes for common query patterns.
- Never rely on client-provided organization IDs without authorization.
- Document every migration.

## 11. API Rules

- Use `/api/v1`.
- Use REST first.
- Use DTOs for input and output.
- Use explicit validation.
- Use standard success and error envelopes.
- Use pagination for list endpoints.
- Use request IDs.
- Use OpenAPI decorators when implementing code.
- Use appropriate HTTP status codes.

## 12. Authentication and Authorization Rules

- Every protected endpoint must require authentication.
- Every organization-scoped endpoint must verify membership.
- Every sensitive action must verify permission.
- Authorization must be server-side.
- Do not trust role or organization claims from the client without checking server-side context.
- Emit audit logs for sensitive changes.

## 13. Queue Rules

- Webhooks enqueue jobs and return quickly.
- Every job must have an idempotency key.
- Every job must include organization ID when tenant-owned.
- Retries must be bounded.
- Jobs must be safe to retry or explicitly marked non-retryable.
- Job failure must be logged with enough context to debug.

## 14. Runtime Rules

The Worker Runtime must be explicit and observable.

Do:

- Persist runtime run records.
- Persist runtime steps.
- Track LLM calls.
- Track skill executions.
- Track token usage and estimated cost.
- Use bounded tool loops.
- Respect human handoff state.
- Retrieve memory and knowledge before prompt construction.
- Enforce worker policies before sending final responses where applicable.

Do not:

- Hide runtime state in opaque framework abstractions.
- Let the LLM execute arbitrary code.
- Execute skills that are not attached to the worker.
- Execute skills without permission checks.
- Send responses if the conversation is in human takeover mode.

## 15. Skill Rules

Every skill must define:

- Name
- Description
- Version
- Input schema
- Output schema
- Required permissions
- Timeout
- Retry policy
- Idempotency behavior
- Executor

Skill execution flow:

1. Resolve skill.
2. Validate input.
3. Authorize.
4. Execute.
5. Validate output.
6. Record metrics.
7. Return result.

## 16. Channel Rules

- Channel adapters normalize inbound events.
- Runtime receives normalized events only.
- Channel-specific payloads must not leak into runtime internals.
- Verify webhooks.
- Deduplicate inbound events.
- Respect platform rate limits.
- Store outbound message attempts.

## 17. Knowledge Rules

- Store original sources in object storage.
- Extract and normalize text.
- Chunk deterministically.
- Generate embeddings asynchronously.
- Store vectors in pgvector.
- Retrieval must be organization-scoped.
- Runtime should include citations or source references where useful.

## 18. Memory Rules

- Store structured facts, not full conversation dumps.
- Memory extraction may be asynchronous.
- Memory has source, confidence, and timestamps.
- Conflicting memories must be handled intentionally.
- Never store sensitive facts unless product requirements justify it.

## 19. Workflow Rules

- Workflows are deterministic.
- Workflows are separate from the Worker Runtime.
- Workflow triggers, conditions, and actions must be explicit.
- Workflow runs must be auditable.
- LLM calls inside workflows must be explicit actions.

## 20. Frontend Rules

Use:

- Next.js App Router
- TypeScript
- Tailwind CSS
- Component primitives such as shadcn/ui
- TanStack Query
- React Hook Form
- Zod

Do:

- Build operational product screens, not marketing pages.
- Prioritize dense but clear SaaS interfaces.
- Include loading, empty, error, and permission states.
- Keep UI terminology customer-friendly.

## 21. Testing Rules

Add tests according to risk:

- Unit tests for services, repositories, prompt builders, skill validation, and policy logic.
- Integration tests for API endpoints and database behavior.
- Queue tests for idempotency and retries.
- Runtime tests for tool loops and failure states.
- Tenant isolation tests for every sensitive module.
- Authorization tests for protected actions.

## 22. Logging Rules

Logs should include:

- request ID
- organization ID when available
- user ID when available
- worker ID when available
- conversation ID when available
- job ID when available

Do not log:

- Access tokens
- Refresh tokens
- API keys
- Channel credentials
- Full prompts containing secrets
- Sensitive customer data unless redacted

## 23. Error Handling Rules

- Use typed domain errors.
- Convert domain errors to standard API error envelopes.
- Do not swallow unexpected errors.
- External API errors must include safe context.
- Retry only when the operation is safe to retry.

## 24. Documentation Rules

Every major feature spec should include:

- Purpose
- Scope
- Non-goals
- Business rules
- Data model
- API contracts
- Backend design
- Frontend impact
- Events
- Jobs
- Error handling
- Security
- Observability
- Testing
- Acceptance criteria
- Claude Code implementation prompt

## 25. Implementation Behavior

When asked to implement a module:

1. Read `docs/00-foundation/MASTER_ARCHITECTURE.md`.
2. Read `docs/00-foundation/CLAUDE.md`.
3. Read the relevant detailed spec.
4. Inspect existing code.
5. Implement the smallest complete version.
6. Add tests.
7. Run validation commands.
8. Report exactly what changed and what could not be verified.

## 26. Things Not to Do

Do not:

- Add architecture not documented.
- Rename core terms without updating glossary and decisions.
- Introduce libraries without explaining why.
- Add circular dependencies.
- Create unrelated refactors.
- Use placeholder implementations for critical security behavior.
- Skip tests for tenant isolation, authorization, runtime, or skills.
- Treat AI output as trusted input.

