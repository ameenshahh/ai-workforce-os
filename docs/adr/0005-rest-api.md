# ADR 0005: REST API

## Status

Accepted — 2026-07-11

## Context

The platform exposes an API for its Next.js dashboard and for integrations. The team needs an interface that is simple to build, easy to debug, well understood by tooling and new engineers, and sufficient for the MVP's needs. Some surfaces (the unified inbox, runtime status) require realtime updates rather than request/response.

## Decision

Use versioned REST APIs (`/api/v1/...`) as the primary interface, with DTOs for input and output validation, standard success and error envelopes, pagination for list endpoints, and request IDs. Use WebSockets (Socket.IO) only where realtime is genuinely required — inbox updates and runtime status streaming. Do not adopt GraphQL for the MVP.

## Consequences

REST keeps the API simple, cacheable, debuggable with ordinary tools, and approachable for new engineers, with OpenAPI documentation generated from decorators. The trade-off is that clients may over- or under-fetch on complex screens and that some aggregated views require purpose-built endpoints. If future product needs (rich, client-driven querying across many resources) justify it, GraphQL can be introduced for specific surfaces without discarding REST.

## Alternatives Considered

- **GraphQL** — deferred: flexible client-driven queries, but adds schema, resolver, caching, and authorization complexity not warranted for the MVP.
- **gRPC** — rejected for the public/dashboard API: excellent for internal service-to-service calls, poor fit for a browser dashboard and third-party integrations.
- **tRPC** — considered: strong TypeScript ergonomics, but couples client and server tightly and is a weaker fit for external integrations than versioned REST.
