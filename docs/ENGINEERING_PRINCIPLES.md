# Engineering Principles

These principles guide design and implementation decisions across AI Workforce OS.

## Simplicity First

Choose the simplest design that satisfies the current product and keeps future change possible. Do not introduce distributed systems, plugin sandboxes, event streaming platforms, or complex orchestration engines before the product needs them.

## Explicit Boundaries

Every module owns its data, rules, DTOs, services, repositories, tests, and documentation. A module may expose public service methods to other modules, but it must not allow other modules to reach into its internals.

## Documentation Before Code

Major features require a specification before implementation. The specification should define business rules, data model, API contracts, failure cases, permissions, logging, metrics, tests, and acceptance criteria.

## Observability Is a Product Feature

Worker behavior must be inspectable. Operators should be able to understand why a worker replied, which prompt was used, which knowledge chunks were retrieved, which skills were executed, and what each external call returned.

## Tenant Isolation Everywhere

Every query, job, cache key, event, and external side effect must be scoped to an organization. Never trust organization identifiers from clients without authorization checks.

## Deterministic Where Possible

Use deterministic workflows for business process automation. Use LLMs for language understanding, reasoning, and response generation. Do not use LLMs where a normal conditional, validation rule, or workflow step is more reliable.

## Human Control

Human handoff, review, and override are core product capabilities. The platform should help agencies trust automation by making it easy to pause, inspect, and take over.

## Fail Loudly

Do not silently swallow errors. Expected failures should become typed domain errors. Unexpected failures should be logged with request ID, organization ID when available, and enough context to debug safely.

## Secure by Default

Credentials must be encrypted. Webhooks must be verified. Skills must be permissioned. External API calls must be time-limited. Sensitive information must not be leaked into logs.

## Small Replaceable Abstractions

Create abstractions around external systems such as LLM providers, channels, storage, and email. Keep abstractions small and close to current needs.

## Tests Match Risk

High-risk areas such as runtime, permissions, tenant isolation, billing, webhooks, and skill execution need strong tests. Low-risk CRUD can use focused unit and integration tests.

## No Architecture by Accident

Claude Code and human developers must not invent major patterns during implementation. New patterns require documentation and an entry in `docs/DECISIONS.md`.

