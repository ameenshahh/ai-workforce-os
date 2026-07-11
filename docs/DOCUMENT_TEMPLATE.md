# <Document / Subsystem Title>

> Template for every engineering document in this repository. Copy this file, rename it, and fill in each section. Delete any section that is genuinely not applicable, but state why it was removed. Keep the section order.

## Purpose

_One or two sentences: why this document exists and what it covers._

## Scope

_What this document covers and, at a boundary level, what it connects to. Name the modules, systems, or concerns in and around scope._

## Goals

_What this subsystem is trying to achieve. Bullet list of concrete objectives._

## Non Goals

_What is explicitly out of scope. Prevents false assumptions and scope creep._

## Business Rules

_The domain and product rules this subsystem must enforce. State each rule so it can be tested._

## Architecture

_How this subsystem is structured: components, responsibilities, boundaries, and how it fits into the modular monolith. Include a diagram reference if helpful._

## Domain Model

_Entities, value objects, relationships, and invariants owned by this subsystem. Use the ubiquitous language from the glossary._

## APIs

_Public interfaces exposed by this subsystem: REST endpoints, internal service interfaces, events published or consumed. Include request/response shapes and error contracts._

## Database

_Tables, columns, indexes, relationships, and migrations owned by this subsystem. State multi-tenant scoping (organization_id) and soft-delete behavior where relevant._

## Sequence Diagram

_Step-by-step flow of the primary operations, showing how components and external systems interact over time._

## State Diagram

_The lifecycle and valid state transitions of the key entities or processes in this subsystem._

## Security

_Authentication, authorization, tenant isolation, input validation, secrets handling, and audit logging requirements._

## Performance

_Expected load, latency targets, hot paths, caching, pagination, and known scalability considerations._

## Logging

_What is logged, at what level, with what context (request IDs, organization IDs), and what must never be logged (secrets, sensitive data)._

## Testing

_Test levels and cases required: business rules, authorization, tenant isolation, edge cases, and failure modes. Reference the strategy in `08-testing/`._

## Future Work

_Known deferrals, roadmap items, and areas intentionally left for later phases._

## Implementation Checklist

_An ordered, checkable list of implementation steps derived from this document._

- [ ] _Step_
- [ ] _Step_

## Acceptance Criteria

_Concrete, verifiable conditions that define "done" for this document and its implementation._

- [ ] _Criterion_
- [ ] _Criterion_
