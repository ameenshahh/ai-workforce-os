# Specification Roadmap

This roadmap defines the order in which engineering specifications should be written before application code is generated.

## Phase 1 - Foundation

Status: In Progress

- `docs/00-foundation/PROJECT_VISION.md`
- `docs/00-foundation/MASTER_ARCHITECTURE.md`
- `docs/00-foundation/CLAUDE.md`
- `docs/00-foundation/DECISIONS.md`
- `docs/00-foundation/GLOSSARY.md`
- `docs/00-foundation/ENGINEERING_PRINCIPLES.md`
- `docs/00-foundation/QUALITY_GATES.md`

## Phase 2 - Core Platform Specifications

Write these next:

1. `docs/03-database/01-data-model.md` - drafted
2. `docs/04-backend/01-backend-architecture.md` - drafted
3. `docs/04-backend/02-auth-and-organizations.md`
4. `docs/04-backend/03-workers.md`
5. `docs/04-backend/04-conversations.md`
6. `docs/04-backend/05-channels.md`

## Phase 3 - AI System Specifications

1. `docs/05-ai/01-worker-runtime.md`
2. `docs/05-ai/02-prompt-builder.md`
3. `docs/05-ai/03-tool-loop.md`
4. `docs/05-ai/04-skills-framework.md`
5. `docs/05-ai/05-knowledge-rag.md`
6. `docs/05-ai/06-memory-engine.md`
7. `docs/05-ai/07-evaluation-and-debugging.md`

## Phase 4 - Product Surface Specifications

1. `docs/06-frontend/01-frontend-architecture.md`
2. `docs/06-frontend/02-dashboard.md`
3. `docs/06-frontend/03-unified-inbox.md`
4. `docs/06-frontend/04-worker-builder.md`
5. `docs/06-frontend/05-knowledge-base.md`
6. `docs/06-frontend/06-settings-and-integrations.md`

## Phase 5 - Operations Specifications

1. `docs/07-deployment/01-local-development.md`
2. `docs/07-deployment/02-docker-compose.md`
3. `docs/07-deployment/03-production-deployment.md`
4. `docs/07-deployment/04-observability.md`
5. `docs/07-deployment/05-backups-and-recovery.md`

## Phase 6 - Implementation Prompts

Create implementation prompts after each spec is written:

- `docs/09-prompts/build-foundation.md`
- `docs/09-prompts/build-auth-and-organizations.md`
- `docs/09-prompts/build-workers.md`
- `docs/09-prompts/build-conversations.md`
- `docs/09-prompts/build-worker-runtime.md`
- `docs/09-prompts/build-skills-framework.md`
- `docs/09-prompts/build-knowledge-rag.md`
- `docs/09-prompts/build-memory-engine.md`
- `docs/09-prompts/build-frontend-shell.md`

## Rule

Do not ask Claude Code to implement a subsystem until the corresponding specification and prompt exist.
