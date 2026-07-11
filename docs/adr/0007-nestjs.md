# ADR 0007: NestJS

## Status

Accepted — 2026-07-11

## Context

The backend is a TypeScript modular monolith (ADR 0001) with strict per-domain module boundaries, a controller/service/repository layering, request-scoped context, guards for authentication and authorization, and queue workers. The team needs a framework that makes these patterns the default rather than something bolted on, works cleanly with Prisma (ADR 0003) and BullMQ (ADR 0004), and is approachable for new engineers.

## Decision

Use NestJS with TypeScript strict mode as the backend framework. Organize code feature-first (one module per domain, per `docs/04-backend/01-backend-architecture.md`), use NestJS dependency injection for services and repositories, and use guards/interceptors/pipes for auth, permissions, request context, validation, and response/error envelopes.

## Consequences

NestJS's module system and DI map directly onto the modular-monolith and repository patterns, so the architecture's rules are enforced by structure rather than convention alone. Its ecosystem covers validation, configuration, OpenAPI generation, and queue integration. The trade-off is NestJS's opinionated structure and decorator-heavy style, which the team accepts as a reasonable cost for consistency. TypeScript strict mode is mandatory to catch a class of errors at compile time; `any` in core logic is forbidden.

## Alternatives Considered

- **Express (bare)** — maximum flexibility but no built-in structure for modules, DI, or guards; the team would rebuild what NestJS provides.
- **Fastify (bare)** — faster raw throughput but the same lack of application structure; performance is not the current bottleneck.
- **tRPC-only backend** — strong TS ergonomics but couples the API tightly to a TS client and is a weaker fit for external integrations and the REST decision (ADR 0005).
- **Hono / Nest-alternatives** — promising but less mature ecosystems for the DI, queue, and OpenAPI needs at this stage.
