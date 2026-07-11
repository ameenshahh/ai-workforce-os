# ADR 0002: PostgreSQL

## Status

Accepted — 2026-07-11

## Context

The platform is strongly relational and multi-tenant: organizations, users, workers, customers, conversations, messages, skills, and their relationships all require consistency and reliable tenant scoping by `organization_id`. It also needs vector similarity search for retrieval-augmented generation. Operating multiple specialized datastores in the MVP would add cost and operational complexity for a small team.

## Decision

Use PostgreSQL as the single primary database for all transactional, tenant-owned data, and use its pgvector extension (see ADR 0006) for embeddings, so one database serves both relational and vector needs during the MVP.

## Consequences

The platform gets strong transactional guarantees, foreign keys, mature indexing, and a well-understood operational profile, with tenant isolation enforced by consistent `organization_id` scoping. Vector search quality and indexing must be tuned inside PostgreSQL rather than delegated to a dedicated engine. If retrieval scale eventually outgrows pgvector, a separate vector store can be introduced later without changing the relational model.

## Alternatives Considered

- **MySQL** — viable relationally but weaker native vector support and ecosystem fit for this use case.
- **A separate vector database (e.g., Pinecone, Weaviate, Qdrave-class engines)** — rejected for MVP: adds another system to operate and sync; pgvector is sufficient at current scale (see ADR 0006).
- **A document database (e.g., MongoDB)** — rejected: the domain is highly relational and benefits from constraints and joins that a relational store provides.
