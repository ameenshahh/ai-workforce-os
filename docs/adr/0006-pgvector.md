# ADR 0006: pgvector

## Status

Accepted — 2026-07-11

## Context

The Knowledge domain provides retrieval-augmented generation: uploaded and connected content is chunked, embedded, and retrieved by similarity at runtime. This requires vector storage and nearest-neighbor search. The platform already standardizes on PostgreSQL (ADR 0002), and operating a separate vector database would add another system to deploy, secure, scope by tenant, and keep in sync during the MVP.

## Decision

Store embeddings and perform vector similarity search inside PostgreSQL using the pgvector extension. Knowledge chunks and their vectors live alongside the relational data, and retrieval is organization-scoped like every other query.

## Consequences

The MVP avoids operating and synchronizing a separate vector database; embeddings inherit PostgreSQL's backup, tenancy, and transactional guarantees, and retrieval can join against relational metadata for filtering and citations. The trade-off is that retrieval quality and performance must be tuned within PostgreSQL (index type, dimensions, and query parameters), and at very large scale pgvector may become a bottleneck. If that happens, a dedicated vector store can be introduced behind the Knowledge domain's retrieval interface without changing its consumers.

## Alternatives Considered

- **Dedicated vector databases (Pinecone, Weaviate, Qdrant, Milvus)** — deferred: strong at large scale but add an extra system to operate and keep consistent with the relational store; unnecessary for MVP scale.
- **In-memory / library-based search (FAISS)** — rejected: not durable, not multi-tenant-safe, and not suited to a persistent multi-tenant SaaS.
- **External managed embedding+search APIs** — rejected for MVP: added latency, cost, and data-residency considerations versus keeping vectors in the primary database.
