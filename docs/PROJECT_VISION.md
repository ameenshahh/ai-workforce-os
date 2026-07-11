# Project Vision

## Mission

AI Workforce OS helps agencies deploy AI workers that communicate with customers, qualify leads, answer support questions, book appointments, execute business tasks, and hand off to humans across channels such as WhatsApp, Instagram, Facebook Messenger, website chat, and future communication channels.

The product is not a generic chatbot builder. It is an operating system for AI employees used by agencies to automate customer-facing and operations-heavy work.

## Product Positioning

External positioning:

> AI agents for agencies that work across WhatsApp, Instagram, Facebook, and web chat.

Internal architecture positioning:

> A multi-tenant AI workforce platform where each customer-facing "agent" is implemented as a Worker with brain, memory, knowledge, skills, policies, channels, metrics, and versions.

The UI may use the term "Agent" because customers understand it. The backend domain model should use "Worker" because it is more precise and extensible.

## Primary Customers

The first customers are service agencies that manage communication, sales, marketing, bookings, and support for multiple clients.

Examples:

- Marketing agencies
- Social media agencies
- Real estate agencies
- Lead generation agencies
- Customer support outsourcing agencies
- Local business automation agencies
- Appointment-heavy service agencies

## End Users

The people who directly use the product are:

- Agency owners
- Agency operations managers
- Client success managers
- Sales managers
- Support managers
- Human agents who take over conversations
- Developers or technical operators configuring integrations

The people who interact with AI workers are the agency clients' customers.

## Core Problem

Agencies manage many repetitive communication workflows:

- Responding to inbound WhatsApp messages
- Replying to Instagram DMs
- Answering Facebook Messenger inquiries
- Qualifying leads
- Booking appointments
- Following up with prospects
- Handling FAQs
- Collecting customer information
- Creating CRM leads
- Sending proposals or quotations
- Escalating complex cases to humans

These workflows are often manual, inconsistent, slow, and hard to scale across many client accounts. Agencies need a way to deploy reliable AI workers that operate inside existing customer channels while respecting permissions, brand voice, business rules, and human oversight.

## Product Goals

1. Let agencies create AI workers without custom engineering for every client.
2. Let each worker use reusable server-side skills such as CRM lookup, calendar booking, knowledge search, email sending, and lead creation.
3. Provide a unified inbox for human review, handoff, and debugging.
4. Support channel integrations beginning with WhatsApp, Instagram, and Facebook Messenger.
5. Keep the MVP architecture simple enough for a small team to build and operate.
6. Preserve strong module boundaries so the platform can scale without a microservice rewrite.
7. Make runtime behavior observable through execution timelines, tool logs, prompt versions, message history, and cost metrics.

## Non-Goals

For the MVP, the platform will not:

- Become a general-purpose no-code automation platform like n8n.
- Support arbitrary third-party plugin execution from untrusted developers.
- Run skills in customer browsers.
- Require Kubernetes or microservices.
- Build a public marketplace.
- Support voice agents.
- Support complex multi-agent collaboration.
- Support every communication channel.
- Expose every internal capability through public APIs.
- Build advanced enterprise SSO before there is enterprise demand.

These may become later roadmap items after the core product is validated.

## MVP Scope

The MVP must support:

- Multi-tenant organizations.
- Users, roles, and organization membership.
- Worker creation and editing.
- Worker prompt, model, tone, and policy configuration.
- Server-side skill attachment.
- WhatsApp channel integration first.
- Instagram and Facebook Messenger integration after the channel abstraction is stable.
- Unified conversation inbox.
- Customer profiles.
- Conversation history.
- Worker runtime with LLM tool calling.
- Basic knowledge base using uploaded documents and pgvector search.
- Basic customer memory extraction.
- Human handoff.
- Audit logging for sensitive operations.
- Usage and cost tracking.
- Basic analytics dashboard.

## V1 Scope

V1 should add:

- Workflow automation.
- More built-in skills.
- Prompt versioning.
- Worker versioning.
- Conversation replay.
- Runtime execution timeline.
- Better analytics.
- Client-level workspaces under agencies.
- White-label basics.
- Billing integration.
- API keys for controlled external access.
- Stronger evaluation and testing tools for workers.

## Business Model

Likely pricing dimensions:

- Monthly subscription per agency.
- Active workers.
- Message volume.
- AI token usage.
- Premium channel integrations.
- Premium skills.
- White-label plan.
- Client accounts under agency.
- Dedicated infrastructure for larger customers.

Avoid overly complex billing in the MVP. Track usage early even if not charged immediately.

## Success Criteria

Early product success means:

- Agencies can onboard a client without engineering support.
- A worker can answer common customer questions from knowledge base content.
- A worker can qualify a lead and create a structured lead record.
- A worker can book or request an appointment through a skill.
- Human operators can review and take over conversations.
- Runtime behavior can be debugged by inspecting the conversation, prompt inputs, tool calls, and final response.
- The system can process inbound messages asynchronously without blocking webhooks.

## Product Principles

- Agencies buy outcomes, not agent infrastructure.
- Workers should be useful before they are configurable.
- Human takeover is a product feature, not a failure mode.
- Runtime behavior must be observable.
- Every automation must be explainable after the fact.
- Configuration must be constrained enough to avoid chaos.
- Skills must be permissioned and auditable.
- The simplest reliable architecture wins.

