# ADR 0003: Prisma

## Status

Accepted — 2026-07-11

## Context

The backend is TypeScript on NestJS and benefits from typed database access, first-class migrations, and a strong developer experience. The architecture mandates that data access is confined to a repository layer (see ADR 0001), so the chosen ORM must support that discipline cleanly rather than encouraging data access scattered across services and controllers.

## Decision

Use Prisma as the ORM and migration tool. Prisma is accessed only from repositories; controllers and application services never call Prisma directly. Repositories return domain entities or explicit persistence models, not arbitrary Prisma payloads, and every tenant-owned query is scoped by `organization_id`.

## Consequences

The team gets end-to-end type safety, readable schema definitions, and reliable migrations, which speeds development and reduces a class of runtime data errors. The repository boundary keeps Prisma an implementation detail that could be replaced without touching business logic. The trade-off is that some complex or performance-critical queries may need raw SQL, which must be documented and isolated within repositories. Prisma's schema becomes an input to the database design in `docs/03-database/`, which remains the source of truth for the data model.

## Alternatives Considered

- **TypeORM** — rejected: heavier, historically less predictable migrations and typing.
- **Knex or raw SQL** — rejected as the default: maximal control but poor developer ergonomics and no type safety; raw SQL is still allowed inside repositories where justified.
- **Drizzle** — considered; promising, but Prisma's maturity, migration tooling, and ecosystem fit better for the team today.
