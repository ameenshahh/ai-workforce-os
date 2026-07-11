# Build Initial Database

Read these files first:

- `docs/MASTER_ARCHITECTURE.md`
- `docs/CLAUDE.md`
- `docs/database/01-data-model.md`

Implement the initial database layer exactly as specified.

## Constraints

- Use Prisma with PostgreSQL.
- Use UUID primary keys.
- Use `organization_id` on tenant-owned tables.
- Do not access Prisma from controllers or services.
- Do not add application modules beyond what is required for database setup unless explicitly requested.
- Include pgvector support for knowledge chunks.
- Do not store secrets in plaintext.

## Deliverables

- Prisma schema
- Initial migration
- Database setup documentation
- Seed script only if explicitly useful for local development
- Validation command output summary

## Acceptance Criteria

- Prisma schema validates.
- Migration includes all tables from `docs/database/01-data-model.md`.
- Indexes and unique constraints match the spec.
- pgvector extension is handled.
- No application business logic is added during this step.

