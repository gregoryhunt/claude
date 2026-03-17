---
name: temporal-awareness
description: ALWAYS check before debugging Temporal workflow issues. Routes to temporal-debugger agent and provides guidance for Temporal-related development.
---

# Temporal Workflow Guide

You have a specialized Temporal debugging agent. Use it instead of manually querying Temporal.

## Prerequisites

**Required CLI**: `temporal` (Temporal CLI)

If `temporal` is not available:
1. Tell the user: "Temporal CLI not found. Install via `brew install temporal`"
2. For local development, Temporal server should be running: `temporal server start-dev`
3. Temporal UI is at http://localhost:8233

## Decision Tree

### Workflow failed or stuck?
→ Use `temporal-debugger` agent
- Better than: Manually running temporal CLI commands
- Better than: Only checking the Temporal UI (which hides nested failures)
- Example: "Debug why the document upload workflow failed"

### Writing a new workflow?
→ Read existing patterns first:
- Workflows: `src/onboardai/temporal/workflows/`
- Activities: `src/onboardai/temporal/activities/`
- Trigger point: `src/onboardai/services/workflow_router.py`
- Worker registration: `src/onboardai/temporal/worker.py`

### Key patterns to follow:
- Activities use `worker_session()` for DB access (NOT the web app session)
- Workflow inputs/outputs must be JSON-serializable (use dataclasses or Pydantic models)
- Activities are registered in `worker.py` — new activities must be added there
- Workflow router in `services/workflow_router.py` is the entry point for triggering workflows

### Quick status check?
→ Use Temporal CLI directly:
```bash
temporal workflow list --limit 10
temporal workflow describe --workflow-id "ID"
```

### Need to understand workflow execution?
→ Use `temporal-debugger` agent for full trace

## Available Temporal Tools

| Type | Name | Purpose |
|------|------|---------|
| Agent | `temporal-debugger` | Full workflow debugging with source code correlation |
| CLI | `temporal` | Direct Temporal CLI commands |
| UI | http://localhost:8233 | Temporal Web UI (local dev) |

## Common Pitfalls

- **DB sessions in activities**: Must use `worker_session()`, not `get_session()`
- **Non-serializable inputs**: Workflow I/O must be JSON-serializable
- **Missing activity registration**: New activities must be added to `worker.py`
- **Temporal hides failures**: Always check event history JSON, not just the UI status
- **Local vs Cloud**: Dev uses local server, staging/prod use Temporal Cloud with separate namespaces
