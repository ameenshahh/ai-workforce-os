# Frontend Architecture Specification

## Purpose

This document specifies the frontend for AI Workforce OS — the Next.js dashboard agencies use to build Agents, run a unified inbox, manage knowledge and integrations, and monitor performance. It is the implementation contract for the frontend concerns in `docs/00-foundation/MASTER_ARCHITECTURE.md` §20 and the product surfaces implied by the domain model. It uses the customer-facing term **Agent** for what the backend models as a **Worker**.

## Scope

This spec covers the frontend application structure (Next.js App Router), state and data-fetching strategy, the product surfaces, component/design conventions, API integration against the versioned REST API, realtime updates, and security/accessibility/performance expectations.

It does not cover: backend modules or the API contract itself (`docs/04-backend/`), the design system's visual tokens beyond conventions, or marketing/website pages. No application code.

## Goals

- Give agency users a fast, operational dashboard and inbox as the first screen after login.
- Present the backend's domain model clearly, using product-facing terms (Agent) while staying consistent with the API.
- Keep the frontend a thin, well-typed client over the REST API — no business logic duplicated client-side.
- Support realtime inbox and runtime updates where it matters.
- Be accessible, responsive, and consistent via a shared component library.

## Non Goals

- No business logic, authorization decisions, or tenant scoping enforced client-side (the server is authoritative).
- No direct database or LLM access from the browser.
- No custom design system built from scratch (use Tailwind + shadcn/ui primitives).
- No GraphQL client (the API is REST; see ADR 0005).
- No storage of secrets/tokens in insecure client storage.

## Business Rules (Frontend Principles)

1. The server is the source of truth; the client never enforces authorization or tenant boundaries, only reflects them.
2. Use product terminology (Agent, Inbox) in UI copy; map to API resources (workers, conversations) at the data layer.
3. All server state flows through a typed data-fetching layer; component state is for UI only.
4. Every mutating action reflects the standard API response/error envelopes and surfaces safe error messages.
5. Sensitive values (tokens, secrets) are never rendered or stored in the browser; credential entry defers to the backend/Secrets flow.
6. The first authenticated screen is an operational surface (inbox or dashboard), never a marketing page.

## Architecture

A Next.js App Router application in TypeScript. Server Components render shells and do server-side data loading where beneficial; Client Components handle interactivity. Server state is managed with TanStack Query against the REST API; forms use React Hook Form with Zod schemas that mirror the API DTOs. Styling uses Tailwind CSS with shadcn/ui component primitives.

```text
frontend/
  app/
    (auth)/                  sign-in, invitations
    (dashboard)/
      layout.tsx             authed shell: org switcher, nav, presence
      page.tsx               operational dashboard
      inbox/                 unified inbox + conversation view
      agents/                Agent (Worker) list + builder + version timeline
      knowledge/             knowledge sources + ingestion status
      customers/             customer profiles + memory view
      channels/              channel connections
      integrations/          external system connections (OAuth)
      skills/                skill catalog + attachment
      workflows/             workflow list + builder
      operators/             operator presence + queues
      analytics/             usage, cost, outcomes
      settings/              organization, members, roles, API keys
  components/                shared UI (shadcn/ui-based)
  lib/
    api/                     typed REST client, query/mutation hooks
    auth/                    session handling
    realtime/                socket client
    validation/              Zod schemas mirroring API DTOs
```

### State & Data

- **Server state**: TanStack Query — queries for reads, mutations for writes, with query-key conventions per resource and organization.
- **UI state**: local component state / lightweight context (e.g., current organization, theme).
- **Forms**: React Hook Form + Zod; schemas mirror backend DTOs so client validation matches server rules (server remains authoritative).
- **Auth/session**: JWT-based session handled server-side where possible; the client attaches auth to API calls and handles refresh/expiry gracefully.

### Realtime

Socket.IO client for surfaces that need live updates: the unified inbox (new messages, handoff state, assignment), the AI Runtime execution timeline (run/step status), and operator presence/queues. Realtime augments, and never replaces, the authoritative REST reads.

## Product Surfaces

- **Dashboard** — operational overview: active Agents, inbox load, recent runs, cost/usage at a glance.
- **Unified Inbox** — all conversations across channels; view a thread, reply as an operator, take over (human handoff), see Agent vs human authorship and delivery status.
- **Agent (Worker) Builder** — create/edit an Agent's Brain, Prompt Profile, Personality, Goals, Capabilities, Policies, Runtime Configuration; attach Skills, Knowledge, Channels; publish and roll back versions.
- **Agent Runtime Timeline** — per-run execution trace (context, prompt, tool calls, LLM calls, cost) for debugging and trust.
- **Knowledge Base** — upload/connect sources, watch ingestion status, manage what an Agent can retrieve.
- **Customer Profiles** — external contacts, their channel identities, and their extracted Memory (read/manage).
- **Channels** — connect and manage WhatsApp/Instagram/Facebook/web-chat connections (credential entry deferred to the secure backend flow).
- **Integrations** — connect external systems (HubSpot, Salesforce, Gmail, etc.) via OAuth; view connection health.
- **Skills** — browse the skill catalog and attach skills to Agents.
- **Workflows** — list and build deterministic automations.
- **Operators** — operator presence, availability, and queues.
- **Analytics** — message volume, worker/skill usage, token usage, cost, conversation outcomes.
- **Settings** — organization profile, members and roles, API keys, notification preferences.

## API Integration

- All data access goes through a typed REST client in `lib/api` targeting `/api/v1`.
- Requests carry auth and organization context; responses use the standard `{ data, meta }` envelope and errors the `{ error }` envelope.
- List views use the API's pagination (`page`, `pageSize`) and surface `meta.pagination`.
- Mutations invalidate the relevant TanStack Query keys to keep views fresh.
- The client never assumes permissions; it hides/disables actions based on the user's permission set returned by the API and relies on the server to enforce.

## Security

- No authorization or tenant enforcement in the client; it reflects server-provided permissions only.
- Never store or display secrets, tokens, or channel/integration credentials; credential and OAuth entry happens through backend-initiated secure flows.
- Sanitize and safely render all user- and customer-generated content (no injection).
- Auth tokens handled per the backend's session strategy; avoid insecure browser storage.
- Follow least privilege in the UI: gate actions by permission, but treat the server as the gate.

## Performance

- Prefer Server Components and server-side data loading for initial shells; hydrate interactivity as Client Components.
- Paginate and virtualize long lists (inbox, messages, runs); never load full history by default.
- Cache reads via TanStack Query with sensible staleness; use realtime to invalidate.
- Code-split by route surface; lazy-load heavy builders (Agent/workflow) and charts.
- Optimistic UI for low-risk mutations; reconcile with server responses.

## Accessibility

- shadcn/ui primitives with keyboard navigation and ARIA where applicable.
- Sufficient color contrast; do not rely on color alone for status.
- Focus management in dialogs and the inbox; screen-reader-friendly labels.

## Testing

- Component tests for shared UI primitives and forms (validation parity with API DTOs).
- Integration tests for key flows: reply/takeover in inbox, publish an Agent version, connect an integration, upload knowledge.
- Contract tests/mocks against the REST envelopes so UI handles success/error/pagination shapes.
- Realtime tests for inbox and runtime-timeline updates.
- Accessibility checks on primary surfaces.

## Future Work

- Visual workflow builder (drag-and-drop).
- Live/streaming Agent responses in the inbox.
- Richer analytics dashboards and saved views.
- White-label theming per agency.
- In-app notification center backed by the Notification domain.

## Implementation Checklist

- [ ] Next.js App Router scaffold (TypeScript, Tailwind, shadcn/ui).
- [ ] Authed dashboard shell with org switcher and nav; operational first screen.
- [ ] Typed REST client + TanStack Query hooks honoring the API envelopes and pagination.
- [ ] Zod validation schemas mirroring API DTOs; React Hook Form integration.
- [ ] Unified inbox with reply, takeover, authorship, and delivery status.
- [ ] Agent builder (all facets) with version publish/rollback and runtime timeline.
- [ ] Knowledge, Customers/Memory, Channels, Integrations, Skills, Workflows, Operators, Analytics, Settings surfaces.
- [ ] Socket.IO realtime for inbox, runtime timeline, and operator presence.
- [ ] Permission-aware UI gating (server-authoritative).
- [ ] Accessibility and performance passes.

## Acceptance Criteria

- [ ] The app is a thin, typed client over `/api/v1`; no business logic or authorization lives client-side.
- [ ] Product terminology (Agent) is used in UI while mapping cleanly to API resources.
- [ ] All primary surfaces exist and reflect the domain model and standard envelopes.
- [ ] Realtime updates work for inbox, runtime timeline, and operator presence.
- [ ] Secrets/credentials are never rendered or stored in the browser.
- [ ] The frontend matches §20 of `MASTER_ARCHITECTURE.md` and the domain model in `DOMAIN_MAP.md`.
