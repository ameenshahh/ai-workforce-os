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

1. `docs/PROJECT_VISION.md`
2. `docs/MASTER_ARCHITECTURE.md`
3. `docs/CLAUDE.md`
4. `docs/DECISIONS.md`
5. `docs/GLOSSARY.md`
6. `docs/ENGINEERING_PRINCIPLES.md`
7. `docs/QUALITY_GATES.md`

## Repository Structure

```text
docs/
  architecture/
  backend/
  frontend/
  database/
  ai/
  deployment/
  product/
  prompts/
```

## Implementation Workflow

1. Write or update the relevant engineering specification.
2. Add a Claude Code implementation prompt in `docs/prompts/`.
3. Ask Claude Code to read `docs/MASTER_ARCHITECTURE.md`, `docs/CLAUDE.md`, and the relevant subsystem spec.
4. Let Claude Code implement the module.
5. Review generated code against `docs/QUALITY_GATES.md`.
6. Commit only after tests and documentation are aligned.
