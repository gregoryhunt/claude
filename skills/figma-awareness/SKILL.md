---
name: figma-awareness
description: ALWAYS check before implementing any UI from Figma designs. Ensures exhaustive design analysis before coding. Routes to figma-analyzer agent and figma interaction discovery.
---

# Figma Design Implementation Guide

You have specialized Figma analysis tools. **NEVER jump straight to coding from a Figma design.** Always analyze first.

## Prerequisites

**Required MCP**: `mcp__figma__get_design_context`

If the Figma MCP is not available:
1. Tell the user: "Figma MCP is not configured. Run `/workflows:setup onboardai` to set up."
2. Do NOT attempt to implement designs without Figma node data
3. Do NOT ask the user to describe the design manually — the MCP provides structured data

## Decision Tree

### Have a Figma URL or node reference?

**Step 1: ALWAYS analyze first**
→ Fetch design data with `mcp__figma__get_design_context`
→ Spawn `figma-analyzer` agent to decompose every element
→ Review the analysis output — is anything missing?

**Step 2: ALWAYS discover interactions**
→ Follow the `figma-interaction-discovery` skill
→ Ask the user about EVERY interactive element
→ Build complete interaction spec before coding

**Step 3: ONLY THEN implement**
→ Use the analysis + interaction spec as your implementation guide
→ Cross-reference with existing templates in `src/onboardai/templates/`
→ Follow `htmx-jinja-patterns` skill for code generation

### Multiple screens in a flow?

**Analyze ALL screens before implementing ANY of them:**
1. Fetch design data for each screen
2. Spawn `figma-analyzer` agent for each screen (can be parallel)
3. Run `figma-interaction-discovery` for the full flow
4. Identify shared components across screens
5. Plan implementation order (shared components first, then screens)

### Implementing a single component?

Even for a single component:
1. Analyze with `figma-analyzer`
2. Check for existing similar components: `grep -r "class=" src/onboardai/templates/`
3. Ask about states: default, hover, disabled, loading, error
4. Implement

## Available Figma Tools

| Type | Name | Purpose |
|------|------|---------|
| MCP | `mcp__figma__get_design_context` | Fetch Figma node data (styles, layout, content) |
| Agent | `figma-analyzer` | Decompose designs element-by-element with Tailwind mappings |
| Skill | `figma-to-code-research` | Full research phase for Figma implementation |
| Skill | `figma-interaction-discovery` | Discover all interactions, transitions, states |
| Skill | `htmx-jinja-patterns` | Guide for generating HTMX/Jinja2/Tailwind code |

## Common Mistakes to Avoid

- ❌ Glancing at Figma and starting to code immediately
- ❌ Approximating spacing, colors, or typography
- ❌ Skipping disabled/error/empty states
- ❌ Forgetting to check existing templates for reusable patterns
- ❌ Implementing screens in isolation without understanding the flow
- ❌ Assuming button behavior without asking the user

## When NOT to Use Figma Analysis

- Fixing a bug in existing UI (just read the template)
- Changing copy/text content only
- Backend-only changes that don't affect templates
