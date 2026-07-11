# Claude Code Prompts

This folder contains implementation prompts designed for Claude Code.

Each prompt should instruct Claude Code to:

1. Read `docs/MASTER_ARCHITECTURE.md`.
2. Read `docs/CLAUDE.md`.
3. Read the relevant subsystem specification.
4. Inspect existing code before editing.
5. Implement exactly what is specified.
6. Add tests.
7. Run validation commands.
8. Report changes and verification results.

## Prompt Template

```markdown
# Implement <Module Name>

Read these files first:

- `docs/MASTER_ARCHITECTURE.md`
- `docs/CLAUDE.md`
- `<relevant spec file>`

Implement `<module>` exactly as specified.

Constraints:

- Do not introduce architecture not documented.
- Do not access Prisma outside repositories.
- Scope tenant-owned data by organization ID.
- Add tests for business rules, authorization, and tenant isolation.
- Update documentation if implementation reveals a missing detail.

Deliverables:

- Module code
- DTOs
- Repository
- Service
- Controller if applicable
- Queue processors if applicable
- Tests
- OpenAPI decorators if applicable
- Summary of validation commands run
```

