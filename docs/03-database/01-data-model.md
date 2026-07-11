# Database Specification: Initial Data Model

## 1. Purpose

This document defines the initial PostgreSQL data model for AI Workforce OS. It is the starting point for Prisma schema generation and backend repository implementation.

The schema is designed for a modular monolith, multi-tenant SaaS, server-side skills, asynchronous worker runtime, RAG, memory, and channel-based conversations.

## 2. Design Principles

- PostgreSQL is the primary source of truth.
- Use UUID primary keys.
- Use `organization_id` on all tenant-owned records.
- Use explicit foreign keys for strong ownership relationships.
- Use soft deletes for business entities that users may expect to restore or audit.
- Use audit logs for sensitive operations.
- Use pgvector for knowledge embeddings.
- Avoid premature partitioning.
- Avoid storing secrets in plaintext.
- Optimize for correctness and debuggability before raw scale.

## 3. Naming Conventions

- Tables use snake_case plural names.
- Columns use snake_case.
- Primary key column is `id`.
- Foreign keys use `<entity>_id`.
- Timestamps use `created_at`, `updated_at`, `deleted_at`.
- Status fields use text enums in Prisma or PostgreSQL enums if stable.
- JSON columns use `jsonb`.

## 4. Common Columns

Most persistent tables should include:

```text
id uuid primary key
created_at timestamptz not null default now()
updated_at timestamptz not null default now()
deleted_at timestamptz null
```

Tenant-owned tables should include:

```text
organization_id uuid not null references organizations(id)
```

## 5. Core Entity Relationship Overview

```mermaid
erDiagram
    organizations ||--o{ organization_memberships : has
    users ||--o{ organization_memberships : belongs_to
    organizations ||--o{ workers : owns
    workers ||--o{ worker_versions : versions
    workers ||--o{ worker_skills : has
    skills ||--o{ worker_skills : attached_to
    organizations ||--o{ customers : owns
    customers ||--o{ customer_channels : has
    customers ||--o{ conversations : participates
    workers ||--o{ conversations : handles
    conversations ||--o{ messages : contains
    conversations ||--o{ runtime_runs : processed_by
    runtime_runs ||--o{ runtime_steps : contains
    runtime_runs ||--o{ llm_calls : makes
    runtime_runs ||--o{ skill_executions : executes
    organizations ||--o{ knowledge_sources : owns
    knowledge_sources ||--o{ knowledge_chunks : chunks
    customers ||--o{ customer_memories : has
    organizations ||--o{ workflows : owns
```

## 6. Identity and Tenancy Tables

### 6.1 organizations

Stores platform tenants.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| name | text | yes | Organization display name |
| slug | text | yes | Unique URL-safe slug |
| status | text | yes | active, suspended, deleted |
| plan | text | yes | starter, growth, enterprise |
| settings | jsonb | yes | Default `{}` |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique `organizations_slug_key` on `slug`
- index on `status`

### 6.2 users

Stores dashboard users.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| email | citext | yes | Unique |
| password_hash | text | no | Nullable for future OAuth users |
| full_name | text | yes |  |
| avatar_url | text | no |  |
| status | text | yes | active, invited, disabled |
| email_verified_at | timestamptz | no |  |
| last_login_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `email`
- index on `status`

### 6.3 organization_memberships

Connects users to organizations.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| user_id | uuid | yes | FK users |
| role | text | yes | owner, admin, manager, operator, viewer |
| status | text | yes | active, invited, disabled |
| invited_by_user_id | uuid | no | FK users |
| joined_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `(organization_id, user_id)`
- index on `(organization_id, role)`
- index on `(user_id)`

### 6.4 sessions

Tracks refresh-token-backed sessions.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| user_id | uuid | yes | FK users |
| refresh_token_hash | text | yes | Store hash only |
| user_agent | text | no |  |
| ip_address | inet | no |  |
| expires_at | timestamptz | yes |  |
| revoked_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- index on `(user_id)`
- index on `(expires_at)`
- index on `(revoked_at)`

## 7. Worker Tables

### 7.1 workers

Stores current worker identity and high-level status.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| name | text | yes | Customer-facing name |
| internal_name | text | no | Optional operator label |
| description | text | no |  |
| status | text | yes | draft, active, paused, archived |
| default_language | text | yes | e.g. en |
| created_by_user_id | uuid | yes | FK users |
| published_version_id | uuid | no | FK worker_versions |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, status)`
- index on `(organization_id, name)`

### 7.2 worker_versions

Immutable snapshots of worker configuration.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| worker_id | uuid | yes | FK workers |
| version_number | integer | yes | Incrementing per worker |
| status | text | yes | draft, published, archived |
| model_provider | text | yes | openai, anthropic, gemini, local, etc. |
| model_name | text | yes |  |
| temperature | numeric | yes |  |
| system_prompt | text | yes |  |
| behavior_config | jsonb | yes | tone, style, constraints |
| policy_config | jsonb | yes | approvals, safety rules |
| created_by_user_id | uuid | yes | FK users |
| published_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- unique on `(worker_id, version_number)`
- index on `(organization_id, worker_id, status)`

### 7.3 skills

Registry of server-side skills available in the platform.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| name | text | yes | Stable programmatic name |
| display_name | text | yes | UI name |
| description | text | yes |  |
| version | text | yes | Skill version |
| category | text | yes | crm, calendar, messaging, etc. |
| input_schema | jsonb | yes | JSON schema |
| output_schema | jsonb | yes | JSON schema |
| required_permissions | text[] | yes |  |
| status | text | yes | active, deprecated, disabled |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- unique on `(name, version)`
- index on `(status)`
- index on `(category)`

### 7.4 worker_skills

Attaches skills to workers.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| worker_id | uuid | yes | FK workers |
| skill_id | uuid | yes | FK skills |
| enabled | boolean | yes | Default true |
| configuration | jsonb | yes | Skill-specific config |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `(worker_id, skill_id)` where `deleted_at is null`
- index on `(organization_id, worker_id)`

## 8. Channel and Conversation Tables

### 8.1 channel_connections

Stores connected communication channels.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| type | text | yes | whatsapp, instagram, facebook, web_chat |
| display_name | text | yes |  |
| external_account_id | text | no | Provider account/page/number ID |
| status | text | yes | active, disabled, error |
| credentials_ref | text | no | Reference to encrypted secret |
| settings | jsonb | yes | Default `{}` |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, type, status)`
- unique on `(type, external_account_id)` where external_account_id is not null

### 8.2 worker_channels

Maps workers to channels.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| worker_id | uuid | yes | FK workers |
| channel_connection_id | uuid | yes | FK channel_connections |
| routing_config | jsonb | yes | Rules for this worker/channel pair |
| enabled | boolean | yes | Default true |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `(worker_id, channel_connection_id)` where `deleted_at is null`
- index on `(organization_id, channel_connection_id)`

### 8.3 customers

Stores external contacts.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| display_name | text | no |  |
| email | citext | no |  |
| phone | text | no |  |
| metadata | jsonb | yes | Default `{}` |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, email)`
- index on `(organization_id, phone)`

### 8.4 customer_channels

Maps customers to channel identities.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| customer_id | uuid | yes | FK customers |
| channel_connection_id | uuid | yes | FK channel_connections |
| external_user_id | text | yes | Provider user/contact ID |
| display_name | text | no | Provider display name |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `(channel_connection_id, external_user_id)` where `deleted_at is null`
- index on `(organization_id, customer_id)`

### 8.5 conversations

Stores conversation threads.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| customer_id | uuid | yes | FK customers |
| worker_id | uuid | no | FK workers, nullable during routing |
| channel_connection_id | uuid | yes | FK channel_connections |
| status | text | yes | open, pending, resolved, closed |
| mode | text | yes | worker, human, paused |
| assigned_user_id | uuid | no | Human operator |
| last_message_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, status, last_message_at)`
- index on `(organization_id, assigned_user_id, status)`
- index on `(organization_id, customer_id)`
- index on `(organization_id, worker_id)`

### 8.6 messages

Stores conversation messages.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| conversation_id | uuid | yes | FK conversations |
| sender_type | text | yes | customer, worker, user, system |
| sender_id | uuid | no | Worker/user/customer depending on sender_type |
| channel_message_id | text | no | External provider ID |
| direction | text | yes | inbound, outbound, internal |
| content_type | text | yes | text, image, audio, file, system |
| text | text | no |  |
| payload | jsonb | yes | Normalized extra content |
| status | text | yes | received, queued, sent, delivered, failed |
| sent_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, conversation_id, created_at)`
- index on `(organization_id, channel_message_id)`
- index on `(organization_id, status)`

### 8.7 inbound_events

Stores raw and normalized inbound webhook events for idempotency and debugging.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | no | May be unknown before resolution |
| channel_connection_id | uuid | no | FK channel_connections |
| provider | text | yes | whatsapp, instagram, facebook |
| external_event_id | text | yes | Provider event ID or computed hash |
| raw_payload | jsonb | yes | Original webhook payload |
| normalized_payload | jsonb | no | Normalized event |
| processing_status | text | yes | received, queued, processed, failed, duplicate |
| received_at | timestamptz | yes |  |
| processed_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- unique on `(provider, external_event_id)`
- index on `(organization_id, processing_status)`
- index on `(received_at)`

## 9. Runtime Tables

### 9.1 runtime_runs

Stores one worker runtime execution.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| conversation_id | uuid | yes | FK conversations |
| worker_id | uuid | yes | FK workers |
| worker_version_id | uuid | no | FK worker_versions |
| trigger_message_id | uuid | no | FK messages |
| status | text | yes | running, completed, failed, cancelled |
| failure_code | text | no |  |
| failure_message | text | no | Safe message |
| started_at | timestamptz | yes |  |
| completed_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, conversation_id, created_at)`
- index on `(organization_id, worker_id, created_at)`
- index on `(organization_id, status)`

### 9.2 runtime_steps

Stores runtime execution timeline.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| runtime_run_id | uuid | yes | FK runtime_runs |
| step_name | text | yes | e.g. RETRIEVING_KNOWLEDGE |
| status | text | yes | started, completed, failed, skipped |
| input_summary | jsonb | no | Redacted |
| output_summary | jsonb | no | Redacted |
| error | jsonb | no | Safe error |
| started_at | timestamptz | yes |  |
| completed_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, runtime_run_id, created_at)`

### 9.3 llm_calls

Tracks LLM calls for debugging and cost.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| runtime_run_id | uuid | no | FK runtime_runs |
| provider | text | yes |  |
| model | text | yes |  |
| purpose | text | yes | runtime, memory_extraction, embedding, etc. |
| prompt_tokens | integer | no |  |
| completion_tokens | integer | no |  |
| total_tokens | integer | no |  |
| estimated_cost_usd | numeric | no |  |
| latency_ms | integer | no |  |
| status | text | yes | success, failed |
| error | jsonb | no | Safe error |
| created_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, created_at)`
- index on `(organization_id, provider, model)`
- index on `(runtime_run_id)`

### 9.4 skill_executions

Tracks server-side skill execution.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| runtime_run_id | uuid | no | FK runtime_runs |
| worker_id | uuid | no | FK workers |
| skill_id | uuid | yes | FK skills |
| skill_name | text | yes | Denormalized stable name |
| input | jsonb | yes | Redacted if needed |
| output | jsonb | no | Redacted if needed |
| status | text | yes | success, failed, skipped |
| error | jsonb | no | Safe error |
| latency_ms | integer | no |  |
| idempotency_key | text | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, runtime_run_id)`
- index on `(organization_id, skill_name, created_at)`
- unique on `(organization_id, idempotency_key)` where `idempotency_key is not null`

## 10. Knowledge Tables

### 10.1 knowledge_sources

Stores uploaded or connected knowledge sources.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| name | text | yes |  |
| type | text | yes | file, website, faq, text |
| status | text | yes | pending, processing, indexed, failed |
| storage_key | text | no | Original file reference |
| metadata | jsonb | yes | Default `{}` |
| created_by_user_id | uuid | yes | FK users |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, status)`
- index on `(organization_id, type)`

### 10.2 knowledge_chunks

Stores searchable chunks and embeddings.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| knowledge_source_id | uuid | yes | FK knowledge_sources |
| chunk_index | integer | yes | Order within source |
| content | text | yes |  |
| content_hash | text | yes | Used to avoid duplicate embeddings |
| embedding | vector | yes | pgvector |
| metadata | jsonb | yes | Default `{}` |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- unique on `(knowledge_source_id, chunk_index)`
- index on `(organization_id, knowledge_source_id)`
- vector index on `embedding`

## 11. Memory Tables

### 11.1 customer_memories

Stores structured facts about customers.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| customer_id | uuid | yes | FK customers |
| key | text | yes | Stable memory key |
| value | jsonb | yes | Structured value |
| confidence | numeric | yes | 0 to 1 |
| source_conversation_id | uuid | no | FK conversations |
| source_message_id | uuid | no | FK messages |
| status | text | yes | active, superseded, deleted |
| expires_at | timestamptz | no |  |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, customer_id, key)`
- index on `(organization_id, status)`

## 12. Workflow Tables

### 12.1 workflows

Stores deterministic automations.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| name | text | yes |  |
| description | text | no |  |
| status | text | yes | draft, active, paused, archived |
| trigger_type | text | yes | e.g. lead_created |
| definition | jsonb | yes | Nodes and edges |
| created_by_user_id | uuid | yes | FK users |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |
| deleted_at | timestamptz | no |  |

Indexes:

- index on `(organization_id, status)`
- index on `(organization_id, trigger_type)`

### 12.2 workflow_runs

Tracks workflow executions.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | yes | FK organizations |
| workflow_id | uuid | yes | FK workflows |
| trigger_payload | jsonb | yes |  |
| status | text | yes | running, completed, failed, cancelled |
| started_at | timestamptz | yes |  |
| completed_at | timestamptz | no |  |
| error | jsonb | no | Safe error |
| created_at | timestamptz | yes |  |
| updated_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, workflow_id, created_at)`
- index on `(organization_id, status)`

## 13. Audit and Operations Tables

### 13.1 audit_logs

Stores security and compliance-relevant events.

Columns:

| Column | Type | Required | Notes |
| --- | --- | --- | --- |
| id | uuid | yes | Primary key |
| organization_id | uuid | no | Nullable for platform-level events |
| actor_user_id | uuid | no | FK users |
| action | text | yes |  |
| resource_type | text | yes |  |
| resource_id | uuid | no |  |
| metadata | jsonb | yes | Redacted |
| ip_address | inet | no |  |
| user_agent | text | no |  |
| created_at | timestamptz | yes |  |

Indexes:

- index on `(organization_id, created_at)`
- index on `(organization_id, action)`
- index on `(resource_type, resource_id)`

## 14. Initial Prisma Implementation Notes

Claude Code should generate Prisma models from this specification, but it must preserve these rules:

- Use `String @id @default(uuid()) @db.Uuid` for UUID IDs.
- Map Prisma model names to PascalCase singular names.
- Map table names with `@@map`.
- Use `DateTime @default(now())` for created fields.
- Use `DateTime @updatedAt` for updated fields.
- Use nullable `deletedAt` for soft deletes.
- Use `Json` for jsonb fields.
- Use pgvector according to the selected Prisma vector support strategy. If Prisma cannot model vector cleanly, use a documented raw migration for the vector column and keep repository access isolated.

## 15. Acceptance Criteria

The database implementation is acceptable when:

- Migrations create all MVP tables.
- Tenant-owned tables include `organization_id`.
- Common query indexes exist.
- Uniqueness constraints prevent duplicate memberships, duplicate channel identities, duplicate worker skills, and duplicate inbound events.
- Runtime, LLM, and skill execution traces are persisted.
- Knowledge chunks support vector search.
- Credentials are not stored in plaintext.
- Repository tests prove organization-scoped access for critical entities.

## 16. Claude Code Implementation Prompt

Use this prompt after the backend project has been scaffolded:

```markdown
Read:

- `docs/00-foundation/MASTER_ARCHITECTURE.md`
- `docs/00-foundation/CLAUDE.md`
- `docs/03-database/01-data-model.md`

Implement the initial Prisma data model and migrations exactly as specified.

Requirements:

- Use UUID primary keys.
- Use PostgreSQL.
- Include pgvector support for `knowledge_chunks.embedding`.
- Preserve tenant scoping.
- Add indexes and unique constraints from the spec.
- Do not create backend services yet.
- Add a short database README explaining how to run migrations.
- Run Prisma formatting and validation.
```

