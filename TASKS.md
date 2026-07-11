# Engineering Roadmap — AI Workforce OS

This is the top-level task tracker for the Architecture Phase. Each section corresponds to a documentation area under `docs/`. Tasks describe **documentation and design work**, not code. Do not check an item until its specification is complete, internally consistent, and passes the rules in `docs/CONTRIBUTING.md`.

Work top to bottom: foundation and architecture must be stable before subsystem specs are finalized.

## Foundation

- [ ] Complete `docs/00-foundation/PROJECT_VISION.md` review and sign-off
- [ ] Write `docs/00-foundation/MASTER_ARCHITECTURE.md` (the anchor document)
- [ ] Complete `docs/00-foundation/ENGINEERING_PRINCIPLES.md`
- [ ] Complete `docs/00-foundation/QUALITY_GATES.md`
- [ ] Finalize `docs/00-foundation/GLOSSARY.md` with all core terms
- [ ] Keep `docs/00-foundation/DECISIONS.md` current with each major decision
- [ ] Review `docs/00-foundation/CLAUDE.md` for phase-appropriate constraints

## Architecture

- [ ] Define module boundaries and responsibilities in `docs/02-architecture/`
- [ ] Document the modular-monolith structure and folder conventions
- [ ] Specify cross-module communication and public interfaces
- [ ] Specify the domain event model and event catalog
- [ ] Document the request lifecycle and error-handling strategy
- [ ] Author ADRs `adr/0001`–`adr/0006`

## Database

- [ ] Complete the domain model in `docs/01-domain/DOMAIN_MAP.md`
- [ ] Design the core schema (organizations, users, workers, customers, conversations, messages)
- [ ] Define multi-tenancy and `organization_id` scoping rules
- [ ] Define ID strategy, timestamps, and soft-delete conventions
- [ ] Specify indexing strategy for common query patterns
- [ ] Specify pgvector usage for knowledge embeddings
- [ ] Define the migration and versioning process

## Backend

- [ ] Specify the controller/service/repository layering rules
- [ ] Define DTO, validation, and response-envelope conventions
- [ ] Define the REST API versioning and pagination standards
- [ ] Specify authentication and authorization design
- [ ] Specify the BullMQ job and queue design (idempotency, retries)
- [ ] Define webhook ingestion and deduplication design

## AI Runtime

- [ ] Specify the Worker Runtime lifecycle (event → context → LLM → response)
- [ ] Define prompt construction and context-window management
- [ ] Specify LLM provider access via LiteLLM
- [ ] Specify OpenAI tool-calling integration and bounded tool loops
- [ ] Define runtime observability (runs, steps, token/cost tracking)
- [ ] Specify human-handoff behavior within the runtime

## Skills

- [ ] Define the skill contract (schemas, permissions, timeout, retry, idempotency)
- [ ] Specify the skill registry design
- [ ] Specify the skill execution flow and authorization checks
- [ ] Define the initial set of MVP skills
- [ ] Define skill versioning and metadata

## Knowledge

- [ ] Specify knowledge source ingestion and storage (S3 originals)
- [ ] Define text extraction and deterministic chunking
- [ ] Specify asynchronous embedding generation
- [ ] Specify organization-scoped retrieval and ranking
- [ ] Define citation and source-reference behavior in responses

## Memory

- [ ] Define the customer memory model (facts, source, confidence, timestamps)
- [ ] Specify asynchronous memory extraction
- [ ] Define conflict-resolution rules for competing memories
- [ ] Specify privacy constraints on what may be stored

## Workflow

- [ ] Define the deterministic workflow model (triggers, conditions, actions)
- [ ] Specify separation between workflows and the AI runtime
- [ ] Define workflow run auditing
- [ ] Specify explicit LLM actions within workflows

## Frontend

- [ ] Define the Next.js App Router structure in `docs/06-frontend/`
- [ ] Specify Tailwind and shadcn/ui conventions
- [ ] Design the worker configuration experience
- [ ] Design the unified conversation inbox and human-takeover UI
- [ ] Specify realtime update patterns (inbox, runtime status)

## Deployment

- [ ] Define environments and configuration strategy
- [ ] Specify infrastructure topology (Postgres, Redis, S3) at a design level
- [ ] Define secrets handling and rotation
- [ ] Specify observability, logging, and alerting design
- [ ] Define the release and rollback process

## Testing

- [ ] Define test levels and coverage expectations in `docs/08-testing/`
- [ ] Specify tenant-isolation test requirements
- [ ] Specify authorization and permission test requirements
- [ ] Define runtime and skill testing approach
- [ ] Define fixtures, factories, and test-data strategy
- [ ] Tie testing requirements to `docs/00-foundation/QUALITY_GATES.md`
