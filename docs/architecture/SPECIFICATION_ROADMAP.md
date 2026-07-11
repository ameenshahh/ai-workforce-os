# Specification Roadmap

This roadmap defines the order in which engineering specifications should be written before application code is generated.

## Phase 1 - Foundation

Status: In Progress

- `docs/PROJECT_VISION.md`
- `docs/MASTER_ARCHITECTURE.md`
- `docs/CLAUDE.md`
- `docs/DECISIONS.md`
- `docs/GLOSSARY.md`
- `docs/ENGINEERING_PRINCIPLES.md`
- `docs/QUALITY_GATES.md`

## Phase 2 - Core Platform Specifications

Write these next:

1. `docs/database/01-data-model.md` - drafted
2. `docs/backend/01-backend-architecture.md` - drafted
3. `docs/backend/02-auth-and-organizations.md`
4. `docs/backend/03-workers.md`
5. `docs/backend/04-conversations.md`
6. `docs/backend/05-channels.md`

## Phase 3 - AI System Specifications

1. `docs/ai/01-worker-runtime.md`
2. `docs/ai/02-prompt-builder.md`
3. `docs/ai/03-tool-loop.md`
4. `docs/ai/04-skills-framework.md`
5. `docs/ai/05-knowledge-rag.md`
6. `docs/ai/06-memory-engine.md`
7. `docs/ai/07-evaluation-and-debugging.md`

## Phase 4 - Product Surface Specifications

1. `docs/frontend/01-frontend-architecture.md`
2. `docs/frontend/02-dashboard.md`
3. `docs/frontend/03-unified-inbox.md`
4. `docs/frontend/04-worker-builder.md`
5. `docs/frontend/05-knowledge-base.md`
6. `docs/frontend/06-settings-and-integrations.md`

## Phase 5 - Operations Specifications

1. `docs/deployment/01-local-development.md`
2. `docs/deployment/02-docker-compose.md`
3. `docs/deployment/03-production-deployment.md`
4. `docs/deployment/04-observability.md`
5. `docs/deployment/05-backups-and-recovery.md`

## Phase 6 - Implementation Prompts

Create implementation prompts after each spec is written:

- `docs/prompts/build-foundation.md`
- `docs/prompts/build-auth-and-organizations.md`
- `docs/prompts/build-workers.md`
- `docs/prompts/build-conversations.md`
- `docs/prompts/build-worker-runtime.md`
- `docs/prompts/build-skills-framework.md`
- `docs/prompts/build-knowledge-rag.md`
- `docs/prompts/build-memory-engine.md`
- `docs/prompts/build-frontend-shell.md`

## Rule

Do not ask Claude Code to implement a subsystem until the corresponding specification and prompt exist.
