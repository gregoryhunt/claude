---
name: figma-to-code-research
description: Forces exhaustive Figma design analysis before any code is written. Produces a complete screen-by-screen specification with element inventory, Tailwind mappings, and component reuse analysis.
---

# Figma-to-Code Research Phase

Before implementing ANY Figma design, complete this research phase. Do not skip steps.

## When This Skill Applies

- User provides a Figma URL or references Figma designs
- User asks to implement a screen, flow, or component from Figma
- A plan references Figma designs for implementation

## Research Workflow

### Phase 1: Fetch Design Data

For each screen/frame in the design:

```
1. Call mcp__figma__get_design_context with the Figma URL/node
2. Save the returned node data — you'll need it for analysis
3. If multiple screens, fetch ALL of them before proceeding
```

### Phase 2: Spawn Analysis Agents

For each screen, spawn a `figma-analyzer` agent:

```
Task(
  subagent_type="figma-analyzer",
  prompt="Analyze this Figma design data and produce an element-level inventory with Tailwind mappings. Also search src/onboardai/templates/ for existing patterns that match.\n\nFigma data:\n[paste node data]",
  description="Analyze [screen name] design"
)
```

**For multiple screens**: Spawn agents in parallel — one per screen.

### Phase 3: Cross-Reference Existing Code

Search the codebase for:

```
1. Existing templates that handle similar views
   → Glob: src/onboardai/templates/**/*.html
   → Look for similar page layouts, form patterns, table structures

2. Existing Jinja2 macros or partials
   → Grep: {% macro, {% include, {% import in templates/

3. Existing Tailwind patterns
   → Grep for class patterns in templates/ that match the design

4. Existing routes that serve similar pages
   → Grep: @router in src/onboardai/routes/
```

### Phase 4: Run Interaction Discovery

Follow the `figma-interaction-discovery` skill to build a complete interaction spec. This MUST happen before implementation.

### Phase 5: Produce Screen Specifications

For each screen, write a specification using the `figma-screen-spec` template. For the full flow, write a flow specification using the `figma-flow-spec` template.

Save these to `thoughts/{namespace}/NNNN-description/` alongside the research and plan docs.

## Output Checklist

Before declaring research complete, verify:

- [ ] Every screen in the flow has been analyzed
- [ ] Every visible element has been inventoried with exact measurements
- [ ] Tailwind class mappings are specified for every element
- [ ] Existing template patterns have been identified for reuse
- [ ] ALL interactive elements have interaction specs (from discovery)
- [ ] Disabled states and their conditions are documented
- [ ] Empty states are documented
- [ ] Error states are documented
- [ ] Transitions between screens are mapped
- [ ] Background tasks triggered by user actions are identified
- [ ] Shared components across screens are identified
- [ ] Implementation order is proposed

## Critical Rule

**If the figma-analyzer output is missing ANY visible element from the Figma design, the research is incomplete.** Go back and fill in the gaps before proceeding to implementation.
