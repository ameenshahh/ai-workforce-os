# AI Workforce OS — Engineering Documentation

This is the documentation root and single entry point for the Engineering Design System (EDS) of AI Workforce OS. The project is currently in the **Architecture Phase**: documentation is written before application code. Every subsystem is specified here before it is implemented.

## Documentation Hierarchy

Documentation is organized into numbered folders. The numbering encodes the recommended reading order — lower numbers are more foundational and should be read first.

```
docs/
├── 00-foundation/     Vision, master architecture, principles, quality gates, glossary, decisions, CLAUDE.md
├── 01-domain/         Domain model, core concepts, ubiquitous language
├── 02-architecture/   System architecture, module boundaries, runtime flow
├── 03-database/       Schema, migrations, multi-tenancy, pgvector
├── 04-backend/        NestJS modules, layering, APIs, queues, authorization
├── 05-ai/             Worker Runtime, LLM access, tool calling, skills, knowledge, memory
├── 06-frontend/       Next.js dashboard, inbox, Tailwind + shadcn/ui conventions
├── 07-deployment/     Environments, configuration, operations, release process
├── 08-testing/        Test strategy, coverage, tenant-isolation and authorization tests
├── 09-prompts/        Implementation prompts for AI assistants (e.g. Claude Code)
└── adr/               Architecture Decision Records (numbered, immutable once accepted)
```

## Purpose of Every Folder

The `00-foundation` folder holds the project-wide documents that everything else depends on: the product vision, the master architecture overview, engineering principles, quality gates, the glossary of shared terms, the running decision log, and the standing instructions for AI assistants working in the repository. The `01-domain` folder defines the business domain — the concepts, entities, and relationships that make up AI Workforce OS — and establishes the ubiquitous language used consistently across all other documents. The `02-architecture` folder describes the system as a whole: the modular monolith, how modules are bounded, how requests and events flow, and how modules contract with one another.

The `03-database` folder is the persistence contract, covering schema design, indexing, migrations, multi-tenant scoping, and vector storage with pgvector. The `04-backend` folder specifies backend engineering: module structure, the controller/service/repository layering, DTOs and validation, REST API conventions, authorization, and background jobs. The `05-ai` folder specifies the Worker Runtime and the AI subsystem — prompt construction, LLM access through LiteLLM, tool calling, skills, knowledge retrieval, and memory. The `06-frontend` folder specifies the dashboard and unified inbox built on Next.js, Tailwind, and shadcn/ui. The `07-deployment` folder captures deployment and operations design (not infrastructure code). The `08-testing` folder defines the testing strategy and quality gates. The `09-prompts` folder holds reusable implementation prompts for AI assistants. The `adr` folder records individual, referenceable architectural decisions.

## Recommended Reading Order

Read the foundation first, then the domain, then architecture, and then the subsystem specifications in numeric order:

1. `00-foundation/PROJECT_VISION.md`
2. `00-foundation/MASTER_ARCHITECTURE.md`
3. `00-foundation/ENGINEERING_PRINCIPLES.md`
4. `00-foundation/DECISIONS.md` and `adr/`
5. `00-foundation/GLOSSARY.md`
6. `01-domain/`
7. `02-architecture/`
8. `03-database/` through `08-testing/` in order
9. `00-foundation/QUALITY_GATES.md` before opening any pull request

## Engineering Workflow

The documentation is the implementation contract. Work follows a documentation-first loop:

1. Pick a subsystem or task from the root `TASKS.md`.
2. Write or update its specification using `DOCUMENT_TEMPLATE.md`, following the rules in `CONTRIBUTING.md`.
3. If the work involves a significant architectural choice, add an ADR in `adr/` and note it in `00-foundation/DECISIONS.md`.
4. Only implement code once the relevant specification is complete and internally consistent.
5. If implementation reveals a gap, update the documentation first, then the code.
6. Verify against the document's Acceptance Criteria and the project `QUALITY_GATES.md` before merging.
