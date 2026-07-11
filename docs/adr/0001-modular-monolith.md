# ADR 0001: Modular Monolith

## Status

Accepted — 2026-07-11

## Context

AI Workforce OS is being built by a small team that needs to validate product demand quickly. The system spans many domains (workers, runtime, conversations, channels, skills, knowledge, memory, and more; see `docs/01-domain/DOMAIN_MAP.md`). A microservices architecture would introduce network boundaries, independent deployments, distributed transactions, cross-service observability, and coordination overhead before the business has proven the product. That overhead would slow the team and add operational risk without a corresponding benefit at this stage.

## Decision

Build the backend as a single deployable NestJS application — a modular monolith — with strict, per-domain module boundaries. Each domain in the Domain Map maps to one module that owns its data and rules. Modules communicate through public services and domain events, never by reading each other's tables. Prisma access is confined to repositories.

## Consequences

Development, local setup, deployment, and debugging stay simple, and transactions remain in-process. The discipline of strict module boundaries is essential: if boundaries erode into cross-module table access or shared business logic, the monolith degrades into a "big ball of mud." Because boundaries follow the Domain Map, any module can later be extracted into its own service if scale demands it, without re-cutting domain lines. The team must actively enforce the boundary rules in code review.

## Alternatives Considered

- **Microservices from day one** — rejected: premature operational and cognitive overhead for an unvalidated product with a small team.
- **Unstructured monolith (layered by technical concern)** — rejected: organizing by controllers/services/repositories globally blurs domain ownership and produces tight coupling; feature-first modules are required instead.
- **Serverless functions** — rejected for the core runtime: the AI Runtime needs long-lived, observable, stateful execution that does not fit short-lived function invocations well at this stage.
