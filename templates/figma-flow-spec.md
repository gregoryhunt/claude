# Flow Specification: {FLOW_NAME}

## Flow Metadata
- **Date**: {DATE}
- **Figma File**: {FIGMA_URL}
- **Screens**: {SCREEN_COUNT}
- **Primary Actor**: {USER_ROLE} (e.g., Developer, Implementer, Admin)

## Flow Overview

{1-2 SENTENCE DESCRIPTION OF THE COMPLETE FLOW}

## Screen Sequence

```
[Screen A] → {trigger} → [Screen B] → {trigger} → [Screen C]
                                     ↘ {alt trigger} → [Screen D]
```

## Screens

### 1. {SCREEN_A_NAME}
- **Route**: `{HTTP_METHOD} {URL_PATTERN}`
- **Template**: `src/onboardai/templates/{PATH}`
- **Entry Point**: {HOW_USER_ARRIVES}
- **Screen Spec**: See `figma-screen-spec-{SCREEN_A}.md`

### 2. {SCREEN_B_NAME}
- **Route**: `{HTTP_METHOD} {URL_PATTERN}`
- **Template**: `src/onboardai/templates/{PATH}`
- **Entry Point**: {TRANSITION_FROM_SCREEN_A}
- **Screen Spec**: See `figma-screen-spec-{SCREEN_B}.md`

## Transitions

| # | From | Trigger | To | Method | Data Carried | URL Change |
|---|------|---------|----| -------|-------------|------------|
| 1 | {SCREEN_A} | {CLICK_BUTTON_X} | {SCREEN_B} | {HTMX_SWAP/NAVIGATE} | {DATA} | {YES/NO} |
| 2 | {SCREEN_B} | {SUBMIT_FORM} | {SCREEN_C} | {HTMX_SWAP/NAVIGATE} | {DATA} | {YES/NO} |

## Background Tasks

| Trigger | Screen | Temporal Workflow | Duration | Progress UI | On Complete | On Fail |
|---------|--------|-------------------|----------|-------------|-------------|---------|
| {ACTION} | {SCREEN} | {WORKFLOW_CLASS} | {ESTIMATE} | {POLLING/SPINNER} | {BEHAVIOR} | {BEHAVIOR} |

## Shared Components

| Component | Used In | Template Path |
|-----------|---------|---------------|
| {COMPONENT} | {SCREEN_LIST} | {PATH_OR_NEW} |

## Feature Flags

| Flag | Scope | Default | Purpose |
|------|-------|---------|---------|
| {FLAG_NAME} | {SCREENS_AFFECTED} | {ON/OFF} | {WHY} |

## Implementation Order

1. **Shared components first**: {LIST}
2. **Routes and handlers**: {LIST}
3. **Templates in flow order**: {LIST}
4. **Background tasks**: {LIST}
5. **Polish and states**: Empty states, error handling, loading states

## Data Requirements

### Models Involved
| Model | Role | Key Fields |
|-------|------|-----------|
| {MODEL} | {ROLE_IN_FLOW} | {FIELDS_USED_IN_UI} |

### New Models or Fields Needed
| Change | Model | Field | Type | Migration Required |
|--------|-------|-------|------|--------------------|
| {ADD/MODIFY} | {MODEL} | {FIELD} | {TYPE} | {YES/NO} |

## Routes to Create/Modify

| Method | Path | Handler | Template | Purpose |
|--------|------|---------|----------|---------|
| {GET/POST} | {URL} | {FUNCTION} | {TEMPLATE_PATH} | {PURPOSE} |
