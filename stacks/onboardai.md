---
name: onboardai
description: OnboardAI Platform stack — FastAPI, SQLModel, Temporal, HTMX/Jinja2/Tailwind, Alembic, PydanticAI
---

# OnboardAI Platform Stack

This stack provides agents and skills for the OnboardAI platform development workflow.

## Agents

| Agent | Purpose | Required Tools |
|-------|---------|---------------|
| figma-analyzer | Decompose Figma designs element-by-element | Figma MCP |
| temporal-debugger | Debug Temporal workflow failures | `temporal` CLI |
| alembic-advisor | Migration conflict resolution | `uv run alembic` |
| codebase-locator | Find files and components | (built-in) |
| codebase-analyzer | Understand code implementation | (built-in) |
| codebase-pattern-finder | Find similar patterns | (built-in) |
| error-analyzer | Analyze errors and stack traces | (built-in) |
| test-writer | Generate tests following patterns | (built-in) |

## Skills

| Skill | Purpose | Required Tools |
|-------|---------|---------------|
| figma-awareness | Route to Figma analysis tools | Figma MCP |
| figma-to-code-research | Force exhaustive design analysis | Figma MCP |
| figma-interaction-discovery | Interrogate user about interactions | (none) |
| temporal-awareness | Route to Temporal debugging | `temporal` CLI |
| alembic-awareness | Migration guidance | `uv run alembic` |
| htmx-jinja-patterns | Frontend code generation patterns | (none) |
| agent-awareness | Route to right agent | (none) |
| debug-systematically | Structured debugging | (none) |
| discover-project-commands | Auto-discover make/uv commands | (none) |
| discover-test-patterns | Auto-discover pytest patterns | (none) |
| follow-test-patterns | Apply test patterns | (none) |

## NOT Included (not relevant to this stack)

- gcp-awareness, gcp-locator, gcp-analyzer, gcp-pattern-finder (uses Heroku, not GCP)
- k8s-awareness, k8s-locator, k8s-analyzer, k8s-pattern-finder (no Kubernetes)
- linear-locator, linear-analyzer, linear-pattern-finder (uses Linear MCP directly)

## Required MCP Servers

### Figma MCP
- **Tools used**: `mcp__figma__get_design_context`
- **Purpose**: Fetch structured design data from Figma files
- **Auth**: Figma API token required
- **Setup**: Add to project's `.claude/settings.json`:
  ```json
  {
    "permissions": {
      "allow": ["mcp__figma__*"]
    }
  }
  ```

### Linear MCP
- **Tools used**: `mcp__linear-server__*` or `mcp__claude_ai_Linear__*`
- **Purpose**: Issue tracking, ticket management
- **Auth**: Linear API key required
- **Setup**: Add to project's `.claude/settings.json`:
  ```json
  {
    "permissions": {
      "allow": ["mcp__linear-server__*"]
    }
  }
  ```

## Required CLI Tools

### temporal
- **Install**: `brew install temporal`
- **Local dev**: `temporal server start-dev --port 7233 --ui-port 8233`
- **Auth check**: `temporal operator cluster health`
- **Used by**: `temporal-debugger` agent

### uv
- **Install**: `brew install uv` or `curl -LsSf https://astral.sh/uv/install.sh | sh`
- **Auth check**: `uv --version`
- **Used by**: All Python commands (`uv run alembic`, `uv run pytest`, etc.)

### alembic (via uv)
- **Install**: Included in project dependencies via `uv sync`
- **Auth check**: `uv run alembic --version`
- **Used by**: `alembic-advisor` agent

## Required Bash Permissions

Add to project's `.claude/settings.json` `permissions.allow`:
```json
[
  "Bash(temporal *)",
  "Bash(uv run alembic *)",
  "Bash(uv run pytest *)",
  "Bash(make *)"
]
```

## Required Environment Variables

| Variable | Purpose | Where |
|----------|---------|-------|
| `DATABASE_URL` | PostgreSQL connection for Alembic | `.env` |
| `TEMPORAL_ADDRESS` | Temporal server address (Cloud) | `.env` |
| `TEMPORAL_NAMESPACE` | Temporal namespace | `.env` |
| `ANTHROPIC_API_KEY` | Claude API for PydanticAI | `.env` |
| `OPENAI_API_KEY` | GPT API for PydanticAI | `.env` |
| `LAUNCHDARKLY_SDK_KEY` | Feature flags | `.env` |
