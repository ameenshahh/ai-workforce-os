# Engineering Roadmap — AI Workforce OS

This is the top-level task tracker for the Architecture Phase. Each section corresponds to a documentation area under `docs/`. Tasks describe **documentation and design work**, not code. Do not check an item until its specification is complete, internally consistent, and passes the rules in `docs/CONTRIBUTING.md`.

Work top to bottom: foundation and architecture must be stable before subsystem specs are finalized.

**Status:** Phase 1 design specifications are **fully completed** across Foundation, Architecture, Database, Backend, AI Runtime, Skills, Knowledge, Memory, Workflow, Frontend, Deployment, and Testing. The only remaining item is enumerating the concrete MVP skill set, which is done at implementation time. The plan is ready to implement.

## Foundation

- [x] Complete `docs/00-foundation/PROJECT_VISION.md` review and sign-off
- [x] Write `docs/00-foundation/MASTER_ARCHITECTURE.md` (the anchor document)
- [x] Complete `docs/00-foundation/ENGINEERING_PRINCIPLES.md`
- [x] Complete `docs/00-foundation/QUALITY_GATES.md`
- [x] Finalize `docs/00-foundation/GLOSSARY.md` with all core terms
- [x] Keep `docs/00-foundation/DECISIONS.md` current with each major decision
- [x] Review `docs/00-foundation/CLAUDE.md` for phase-appropriate constraints

## Architecture

- [x] Define module boundaries and responsibilities (MASTER_ARCHITECTURE §5 + DOMAIN_MAP)
- [x] Document the modular-monolith structure and folder conventions
- [x] Specify cross-module communication and public interfaces
- [x] Specify the domain event model and event catalog (DOMAIN_MAP dependency matrix + backend §17)
- [x] Document the request lifecycle and error-handling strategy
- [x] Author ADRs `adr/0001`–`adr/0006` (plus 0007 NestJS, 0008 server-side skills)

## Database

- [x] Complete the domain model in `docs/01-domain/DOMAIN_MAP.md`
- [x] Design the core schema (organizations, users, workers, customers, conversations, messages)
- [x] Define multi-tenancy and `organization_id` scoping rules
- [x] Define ID strategy, timestamps, and soft-delete conventions
- [x] Specify indexing strategy for common query patterns
- [x] Specify pgvector usage for knowledge embeddings
- [x] Define the migration and versioning process

## Backend

- [x] Specify the controller/service/repository layering rules
- [x] Define DTO, validation, and response-envelope conventions
- [x] Define the REST API versioning and pagination standards
- [x] Specify authentication and authorization design
- [x] Specify the BullMQ job and queue design (idempotency, retries)
- [x] Define webhook ingestion and deduplication design

## AI Runtime

- [x] Specify the AI Runtime lifecycle (event → context → LLM → response)
- [x] Define prompt construction and context-window management
- [x] Specify LLM provider access via LiteLLM
- [x] Specify OpenAI tool-calling integration and bounded tool loops
- [x] Define runtime observability (runs, steps, token/cost tracking)
- [x] Specify human-handoff behavior within the runtime

## Skills

- [x] Define the skill contract (schemas, permissions, timeout, retry, idempotency)
- [x] Specify the skill registry design
- [x] Specify the skill execution flow and authorization checks
- [ ] Define the initial set of MVP skills (enumerate at implementation time)
- [x] Define skill versioning and metadata

## Knowledge

- [x] Specify knowledge source ingestion and storage (S3 originals)
- [x] Define text extraction and deterministic chunking
- [x] Specify asynchronous embedding generation
- [x] Specify organization-scoped retrieval and ranking
- [x] Define citation and source-reference behavior in responses

## Memory

- [x] Define the customer memory model (facts, source, confidence, timestamps)
- [x] Specify asynchronous memory extraction
- [x] Define conflict-resolution rules for competing memories
- [x] Specify privacy constraints on what may be stored

## Workflow

- [x] Define the deterministic workflow model (triggers, conditions, actions)
- [x] Specify separation between workflows and the AI runtime
- [x] Define workflow run auditing
- [x] Specify explicit LLM actions within workflows

## Frontend

- [x] Define the Next.js App Router structure in `docs/06-frontend/`
- [x] Specify Tailwind and shadcn/ui conventions
- [x] Design the worker (Agent) configuration experience
- [x] Design the unified conversation inbox and human-takeover UI
- [x] Specify realtime update patterns (inbox, runtime status)

## Deployment

- [x] Define environments and configuration strategy
- [x] Specify infrastructure topology (Postgres, Redis, S3) at a design level
- [x] Define secrets handling and rotation
- [x] Specify observability, logging, and alerting design
- [x] Define the release and rollback process

## Testing

- [x] Define test levels and coverage expectations in `docs/08-testing/`
- [x] Specify tenant-isolation test requirements
- [x] Specify authorization and permission test requirements
- [x] Define runtime and skill testing approach
- [x] Define fixtures, factories, and test-data strategy
- [x] Tie testing requirements to `docs/00-foundation/QUALITY_GATES.md`
