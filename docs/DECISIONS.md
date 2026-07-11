# Decision Log

This file records significant product and technical decisions as lightweight Architecture Decision Records.

Each decision should include:

- Date
- Status
- Context
- Decision
- Consequences

## 2026-07-11 - Use a Modular Monolith

**Status:** Accepted

**Context:** The product needs to move quickly with a small team. Microservices would add deployment, networking, observability, transaction, and coordination complexity before the business has validated demand.

**Decision:** Build the backend as one NestJS modular monolith with strict feature boundaries.

**Consequences:** Development and deployment stay simple. Modules must remain cleanly separated so future extraction is possible if needed.

## 2026-07-11 - Use Worker Internally and Agent Externally

**Status:** Accepted

**Context:** Customers understand the term "AI Agent", but internally the platform is closer to an AI workforce operating system.

**Decision:** Use "Agent" in marketing and UI where appropriate. Use `Worker` as the backend domain model and internal architecture term.

**Consequences:** Documentation must be explicit about the difference. API paths may use customer-facing terminology when useful, but backend services and entities should prefer Worker.

## 2026-07-11 - Keep Skills Server-Side Only

**Status:** Accepted

**Context:** Dynamic third-party skill execution increases security, reliability, sandboxing, versioning, and support complexity.

**Decision:** MVP skills are implemented as TypeScript classes inside the backend. Workers can only use registered server-side skills.

**Consequences:** The MVP is safer and easier to debug. Marketplace-style external skills are postponed.

## 2026-07-11 - Use BullMQ for Background Jobs

**Status:** Accepted

**Context:** Webhooks must return quickly, runtime work can be slow, and message processing must be retried safely.

**Decision:** Use Redis-backed BullMQ for inbound message processing, knowledge ingestion, memory extraction, workflow execution, and scheduled tasks.

**Consequences:** Redis becomes required infrastructure. Every job must be idempotent.

## 2026-07-11 - Use PostgreSQL and pgvector

**Status:** Accepted

**Context:** The product needs relational consistency and vector search for RAG.

**Decision:** Use PostgreSQL as the primary database and pgvector for embeddings.

**Consequences:** The platform avoids operating a separate vector database during MVP. Retrieval quality and indexing must be tuned inside PostgreSQL.

## 2026-07-11 - Use Prisma

**Status:** Accepted

**Context:** The project benefits from typed database access, migrations, and a strong developer experience.

**Decision:** Use Prisma as the ORM, but never access Prisma directly from controllers or business services.

**Consequences:** Repositories own Prisma access. Domain services depend on repositories, not Prisma clients.

## 2026-07-11 - Use REST First

**Status:** Accepted

**Context:** REST is simple, debuggable, well understood, and sufficient for the MVP.

**Decision:** Build versioned REST APIs for the dashboard and integrations. Use WebSockets only for realtime inbox updates and runtime status streaming.

**Consequences:** Avoid GraphQL complexity until product needs justify it.

## 2026-07-11 - Avoid LangChain and AutoGen as Core Runtime Foundations

**Status:** Accepted

**Context:** The runtime is the core product differentiator. Heavy frameworks can obscure control over state, logs, retries, tool execution, and tenant isolation.

**Decision:** Build a custom Worker Runtime with direct LLM provider integration and tool-calling support.

**Consequences:** More internal code must be written, but behavior remains transparent, testable, and product-specific.

## 2026-07-11 - Use Documentation as Implementation Contract

**Status:** Accepted

**Context:** Claude Code will implement large parts of the system. It needs precise architecture to avoid inconsistent patterns.

**Decision:** Every major subsystem gets an implementation-ready specification before code is written.

**Consequences:** Documentation must stay ahead of code. PRs that introduce behavior not described in docs should update the docs first.

