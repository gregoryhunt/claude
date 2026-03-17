---
name: alembic-awareness
description: ALWAYS check before creating or modifying Alembic migrations. Prevents multiple-head conflicts and provides migration best practices.
---

# Alembic Migration Guide

You have a specialized Alembic advisor agent. Use it for conflict resolution and migration planning.

## Prerequisites

**Required CLI**: `uv run alembic` (via uv package manager)

## Decision Tree

### Creating a new migration?

**Before generating:**
1. Check for existing heads: `uv run alembic heads`
2. If multiple heads exist → resolve FIRST using `alembic-advisor` agent
3. Only then generate: `uv run alembic revision --autogenerate -m "description"`
4. **Always review** the generated migration file for unexpected changes

**Naming convention:**
```bash
uv run alembic revision --autogenerate -m "$(date +%Y%m%d_%H%M%S)_description_of_change"
```

### Multiple heads detected?
→ Use `alembic-advisor` agent
- It will analyze the conflict, identify which branches diverged, and recommend resolution
- Most common fix: `uv run alembic merge heads -m "merge_description"`

### Migration fails to apply?
→ Use `alembic-advisor` agent to diagnose
- Check: `uv run alembic current` vs `uv run alembic heads`
- Common cause: migration references column/table that doesn't exist yet (ordering issue)

### Modifying a model?
1. Make changes to SQLModel class in `src/onboardai/models/`
2. Generate migration: `uv run alembic revision --autogenerate -m "description"`
3. Review generated migration
4. Apply: `uv run alembic upgrade head`
5. Check: `uv run alembic current`

## Available Tools

| Type | Name | Purpose |
|------|------|---------|
| Agent | `alembic-advisor` | Conflict resolution, merge strategies, best practices |
| CLI | `uv run alembic` | Direct Alembic commands |
| Hook | `scripts/check_alembic_heads.py` | Pre-commit check for multiple heads |

## Key Rules

- **Always use `uv run alembic`** — never bare `alembic`
- **Always use `--autogenerate`** — let SQLModel drive migrations, don't write raw SQL
- **Always review generated migrations** — autogenerate can include unintended changes
- **Never delete applied migrations** — only merge forward
- **Staging has separate DB** — migrations may diverge from main when multiple features are in flight
- **Production runs on deploy** — `release: alembic upgrade head` in Procfile

## Staging Branch Strategy

When multiple developers are working:
1. Each feature branch has its own migration(s)
2. Pushing to staging may create multiple heads
3. This is **expected** on staging — resolve before merging to main
4. Use `alembic merge heads` on the branch being merged to main
5. The pre-commit hook (`prek`) will catch multiple heads before commit
