# AI Workforce OS

Engineering design and implementation repository for an AI Workforce Operating System for agencies.

Status: Architecture Phase

## What This Repository Is

This repository is the source of truth for the product and engineering specification before implementation begins.

The goal is to design the system well enough that Claude Code can implement modules consistently without inventing architecture.

## Current Rule

Do not scaffold application code yet.

Documentation comes first. Code follows the specifications.

## Start Here

Read these files in order:

1. `docs/00-foundation/PROJECT_VISION.md`
2. `docs/00-foundation/MASTER_ARCHITECTURE.md`
3. `docs/00-foundation/CLAUDE.md`
4. `docs/00-foundation/DECISIONS.md`
5. `docs/00-foundation/GLOSSARY.md`
6. `docs/00-foundation/ENGINEERING_PRINCIPLES.md`
7. `docs/00-foundation/QUALITY_GATES.md`

## Repository Structure

```text
docs/
  00-foundation/    Vision, master architecture, principles, quality gates, glossary, decisions, CLAUDE.md
  01-domain/        Domain model and ubiquitous language
  02-architecture/  System architecture and module boundaries
  03-database/      Schema, migrations, multi-tenancy, pgvector
  04-backend/       NestJS modules, layering, APIs, queues
  05-ai/            Worker Runtime, tool calling, skills, knowledge, memory
  06-frontend/      Next.js dashboard, inbox, UI conventions
  07-deployment/    Environments, operations, release process
  08-testing/       Test strategy and quality gates
  09-prompts/       Implementation prompts for AI assistants
  adr/              Architecture Decision Records
```

See `docs/README.md` for the full documentation index and recommended reading order.

## Implementation Workflow

1. Write or update the relevant engineering specification.
2. Add a Claude Code implementation prompt in `docs/09-prompts/`.
3. Ask Claude Code to read `docs/00-foundation/MASTER_ARCHITECTURE.md`, `docs/00-foundation/CLAUDE.md`, and the relevant subsystem spec.
4. Let Claude Code implement the module.
5. Review generated code against `docs/00-foundation/QUALITY_GATES.md`.
6. Commit only after tests and documentation are aligned.
