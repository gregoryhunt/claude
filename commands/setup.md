---
name: setup
description: Configure the current project to use workflow agents and skills. Checks for required MCP servers, CLI tools, and permissions, then guides setup of missing dependencies.
allowed-tools: Read, Grep, Glob, Bash, AskUserQuestion
---

# Project Setup for Workflows Plugin

Set up the current project to use the workflows plugin's agents and skills.

## Usage

```
/workflows:setup [stack-name]
```

If no stack name provided, auto-detect from the project.

## Process

### Step 1: Detect Project Stack

Check the current project to determine which stack applies:

1. Read `pyproject.toml`, `package.json`, `go.mod`, `Cargo.toml` to identify language/framework
2. Check for framework markers:
   - `src/*/temporal/` → Temporal workflows present
   - `alembic/` or `alembic.ini` → Alembic migrations present
   - `*.html` templates with `hx-` attributes → HTMX frontend
   - `.claude/settings.json` with `mcp__figma__` → Figma MCP configured
   - `Procfile` with temporal → Temporal deployment
3. If a stack name was provided (e.g., "onboardai"), read `stacks/{name}.md`
4. If no stack name, suggest the best match

### Step 2: Check Prerequisites

For each requirement in the stack manifest, check availability:

**MCP Servers:**
```bash
# Check if Figma MCP is configured
# Look in .claude/settings.json and .claude/settings.local.json for mcp__figma__
```

**CLI Tools:**
```bash
# Check temporal
temporal --version 2>/dev/null || echo "NOT INSTALLED"

# Check uv
uv --version 2>/dev/null || echo "NOT INSTALLED"

# Check alembic (via uv)
uv run alembic --version 2>/dev/null || echo "NOT INSTALLED"
```

**Permissions:**
```bash
# Read .claude/settings.json and check if required permissions are in allow list
```

### Step 3: Report Status

Present a status report:

```
## Workflows Plugin Setup: [stack-name]

### ✅ Available
- [tool/MCP] — [status]

### ❌ Missing
- [tool/MCP] — [how to install/configure]

### ⚠️ Configured but not verified
- [tool/MCP] — [how to verify]
```

### Step 4: Guide Setup

For each missing dependency, provide exact setup instructions:

**For missing MCP servers:**
Ask the user if they want to configure it, then provide the exact JSON to add to their `.claude/settings.json`.

**For missing CLI tools:**
Provide the exact install command (e.g., `brew install temporal`).

**For missing permissions:**
Show the exact permissions to add to `.claude/settings.json`.

**For missing auth:**
Guide the user through authentication:
- Figma: "Generate a Figma API token at https://www.figma.com/developers/api#access-tokens"
- Linear: "Generate a Linear API key at Settings → API"
- Temporal Cloud: "Get namespace credentials from Temporal Cloud console"

### Step 5: Verify Setup

After the user completes setup, verify everything works:

```bash
# Verify temporal connection
temporal operator cluster health

# Verify alembic
uv run alembic heads

# Verify MCP by checking if tools are accessible
```

Report final status.

## Auto-Detection Heuristics

| Indicator | Stack |
|-----------|-------|
| `src/onboardai/` exists | onboardai |
| `temporal/workflows/` exists | includes temporal tools |
| `alembic/` exists | includes alembic tools |
| Figma MCP in settings | includes figma tools |
| GCP credentials configured | gcp (not onboardai) |
| Kubernetes context available | k8s (not onboardai) |

## Notes

- This command does NOT modify `.claude/settings.json` directly — it shows the user what to add
- The user must manually add permissions (security principle: explicit consent)
- Stack manifests in `stacks/` define what each stack needs
- Missing tools degrade gracefully — agents will report what's missing when invoked
