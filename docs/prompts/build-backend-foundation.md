# Build Backend Foundation

Read these files first:

- `docs/MASTER_ARCHITECTURE.md`
- `docs/CLAUDE.md`
- `docs/database/01-data-model.md`
- `docs/backend/01-backend-architecture.md`

Scaffold the backend foundation exactly as specified.

## Implement

- NestJS backend project
- Configuration validation
- Prisma module
- Request context model
- Standard success response envelope
- Standard error response envelope
- Global exception filter
- Validation pipe
- Common decorators
- Guard placeholders for auth, organization membership, and permissions
- Base app error classes
- Queue module wiring placeholders

## Do Not Implement Yet

- Full auth flows
- Worker CRUD
- Conversation runtime
- Channel webhooks
- Skills framework
- RAG
- Memory extraction
- Frontend

## Constraints

- Use TypeScript strict mode.
- Do not use Prisma outside repository classes.
- Do not put business logic in controllers.
- Do not introduce microservices.
- Do not introduce GraphQL.
- Keep module boundaries consistent with the docs.

## Acceptance Criteria

- Backend starts locally.
- Config validation fails fast on missing required env vars.
- Standard response and error envelopes are available.
- Prisma module is wired.
- Foundation tests pass.
- No undocumented architecture is introduced.

