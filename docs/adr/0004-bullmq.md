# ADR 0004: BullMQ

## Status

Accepted — 2026-07-11

## Context

Inbound channel webhooks must be acknowledged quickly (external platforms enforce short timeouts and retry aggressively), but the work they trigger — AI Runtime execution, knowledge ingestion, memory extraction, workflow execution, scheduled jobs — can be slow and must be retried safely. Processing this work synchronously inside the webhook request would block acknowledgement and risk duplicate delivery. The stack is already Node/TypeScript and uses Redis.

## Decision

Use BullMQ (backed by Redis) as the background job and queue system. Webhooks enqueue jobs and return immediately; workers process jobs asynchronously. Every job carries an idempotency key and, when tenant-owned, an `organization_id`; retries are bounded, and job failures are logged with enough context to debug.

## Consequences

Webhook paths stay fast and resilient, and slow or failure-prone work is retried safely and observably. Redis becomes required infrastructure for the platform to function. Because external events can be delivered more than once and jobs can retry, every job must be idempotent — this is a firm rule, not a preference. Queue depth, latency, and failure rates become operational signals that Analytics and observability must track.

## Alternatives Considered

- **Processing synchronously in the webhook** — rejected: blocks acknowledgement, causes timeouts and duplicate deliveries.
- **Kafka** — rejected for MVP: a powerful event-streaming platform but heavy operational overhead unjustified at current scale; BullMQ covers the job-queue need.
- **Cloud-native queues (SQS, Cloud Tasks)** — viable but add provider coupling and lose the tight Redis/Node integration and local-dev simplicity BullMQ provides today.
