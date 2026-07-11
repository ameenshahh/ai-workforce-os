# Glossary

This file defines shared product and engineering terminology. Use these terms consistently in documentation and code.

| Term | Definition |
| --- | --- |
| Agency | The platform customer. An agency uses AI Workforce OS to serve its own clients. |
| Agent | Customer-facing product term for an AI employee. In code, prefer Worker. |
| Attachment | File, image, audio, video, or other media associated with a message or knowledge source. |
| Audit Log | Immutable record of important actions, especially permission changes, external calls, configuration changes, and human handoff. |
| Brain | The worker configuration that controls model, prompt, response style, policies, tool availability, and reasoning behavior. |
| Channel | A communication transport such as WhatsApp, Instagram, Facebook Messenger, website chat, or email. |
| Channel Adapter | Server-side implementation that normalizes inbound events and sends outbound messages for one channel. |
| Client | A business served by an agency. Clients may be introduced after the MVP if agency-client hierarchy is needed. |
| Conversation | A thread of messages between a customer, a worker, and optionally human operators. |
| Customer | The end customer who messages the agency or client through a channel. |
| Customer Memory | Structured facts about a customer extracted from conversations, such as location, preferences, budget, and prior purchases. |
| DTO | Data Transfer Object used for API input and output validation. |
| Event | A domain-significant occurrence such as MessageReceived, WorkerResponded, SkillExecuted, or HumanHandoffStarted. |
| External User | A customer or contact interacting through a channel. Not the same as a platform User. |
| Human Handoff | Transition where a human operator takes control of a conversation from a worker. |
| Idempotency | Guarantee that repeated processing of the same external event does not create duplicate side effects. |
| Knowledge Chunk | Searchable fragment of a knowledge source, usually embedded and stored in pgvector. |
| Knowledge Source | Uploaded or connected content used by workers, such as PDF, markdown, website pages, FAQs, or documents. |
| Memory | Persisted structured context. Customer Memory belongs to a customer; Worker Memory may later belong to a worker. |
| Module | A feature boundary in the NestJS modular monolith. Each module owns its services, repositories, DTOs, and rules. |
| Organization | A tenant in the platform. Every platform user, worker, customer, conversation, and configuration belongs to an organization. |
| Permission | A fine-grained authorization capability such as workers.create or conversations.takeover. |
| Policy | A rule that constrains worker behavior, such as requiring human review before sending discounts. |
| Repository | Data access abstraction. Repositories are the only application layer allowed to talk directly to Prisma. |
| Runtime | The system that processes inbound events, builds context, calls the LLM, executes skills, generates replies, saves state, and dispatches responses. |
| Skill | Server-side executable capability that a worker can use through LLM tool calling. Examples: create lead, check calendar, send email, search CRM. |
| Skill Registry | Server-side registry of available skills, metadata, schemas, permissions, and executors. |
| Tenant | Synonym for Organization. Prefer Organization in product docs and code. |
| Tool Calling | LLM capability where the model requests execution of predefined tools. In this system, tools map to server-side skills. |
| User | A person with a login to the AI Workforce OS dashboard. Not the same as Customer. |
| Webhook | HTTP callback from an external platform such as Meta, WhatsApp, Stripe, or a CRM. |
| Worker | Internal domain term for an AI employee. A Worker has brain, skills, memory, knowledge, channels, policies, goals, metrics, and versions. |
| Worker Version | Immutable snapshot of worker configuration used for auditability, rollback, and reproducible runtime behavior. |
| Workflow | Deterministic automation made of triggers, conditions, and actions. Workflows are separate from the AI runtime. |

