---
name: figma-interaction-discovery
description: Systematic interrogation of the user about every interactive element in a Figma design. Builds a complete interaction specification covering buttons, links, inputs, transitions, disabled states, and background tasks.
---

# Figma Interaction Discovery

Figma designs show what the UI looks like, but interactions live in the user's head. This skill guides you to ask about EVERY interactive element until you have a complete specification.

## When This Skill Applies

- After `figma-analyzer` has produced an element inventory
- Before any implementation begins
- When the user describes a feature flow from Figma

## Discovery Process

### Step 1: Identify All Interactive Elements

From the figma-analyzer output, extract every element that could be interactive:
- Buttons (primary, secondary, icon buttons)
- Links (navigation, external)
- Form inputs (text, select, checkbox, radio, file upload)
- Tabs or navigation elements
- Toggle switches
- Expandable/collapsible sections
- Table rows (if clickable)
- Cards (if clickable)
- Pagination controls
- Search/filter inputs
- Dropdown menus

### Step 2: Ask About Each Element

For EVERY interactive element, ask the user using AskUserQuestion:

**For buttons:**
```
I found these buttons in the design. For each one, I need to know:

1. **[Button Name/Text]**
   - What happens when clicked? (navigate, submit, trigger action, open modal?)
   - Does it trigger a background task? (Temporal workflow, API call?)
   - What are the conditions for it to be enabled/disabled?
   - Should it show a loading state while processing?
   - What happens on success? (redirect, toast, update section?)
   - What happens on error? (error message, stay on page?)

2. **[Next Button]**
   ...
```

**For form inputs:**
```
I found these form fields. For each one:

1. **[Field Name]**
   - Is it required or optional?
   - What validation rules apply? (min/max length, format, etc.)
   - Should validation happen on blur, on submit, or real-time?
   - Does changing this field affect other fields? (show/hide, enable/disable)
   - Is there a default value?
   - Does it auto-save or only save on form submit?
```

**For navigation/links:**
```
I found these navigation elements:

1. **[Link/Tab Name]**
   - Where does it navigate to? (route, anchor, external URL?)
   - Should it be a full page load or HTMX partial swap?
   - Is there active/selected state styling?
   - Are there conditions where it should be hidden or disabled?
```

### Step 3: Map Screen Transitions

Ask the user to walk through the complete flow:

```
Let me map out the screen transitions. Starting from [First Screen]:

1. User lands on [Screen A]
   → What actions can they take?
   → For each action, which screen do they go to?

2. From [Screen A] → [Screen B]
   → Is this a full page navigation or a partial update (HTMX)?
   → Does the URL change?
   → Is there any data that carries over?
   → Can they go back? How?

3. [Continue for each transition]
```

### Step 4: Identify Background Tasks

Ask specifically about background processing:

```
Looking at this flow, which actions trigger background work?

For each background task:
- What triggers it? (button click, form submit, file upload?)
- What system handles it? (Temporal workflow, inline processing?)
- How long does it typically take?
- How should the UI indicate progress? (polling, loading spinner, redirect?)
- What happens when the task completes? (notification, page update, email?)
- What happens if the task fails?
```

### Step 5: Identify Conditional UI

Ask about dynamic visibility and states:

```
Are there elements that should show/hide or enable/disable based on conditions?

For example:
- Buttons that are disabled until a form is valid
- Sections that only appear for certain user roles
- Elements that change based on data state (e.g., "Pending" vs "Complete")
- Feature flags that gate visibility (LaunchDarkly)
```

### Step 6: Document the Specification

Compile all answers into a structured interaction spec:

```markdown
## Interaction Specification: [Flow Name]

### Screen: [Screen Name]

#### Buttons
| Element | Action | Trigger | Disabled When | Loading State | Success | Error |
|---------|--------|---------|---------------|---------------|---------|-------|
| Submit  | POST /api/... | Temporal workflow X | Form invalid | Spinner + text | Redirect to /next | Inline error |

#### Form Fields
| Field | Required | Validation | On Change | Default |
|-------|----------|------------|-----------|---------|
| Name  | Yes | min 2 chars | None | Empty |

#### Navigation
| Element | Destination | Method | Active State |
|---------|-------------|--------|-------------|
| Tab: Overview | /assessments/{id} | HTMX swap #content | Bold + border-b |

#### Transitions
| From | Trigger | To | Method | Data Carried |
|------|---------|----| -------|-------------|
| Screen A | Click "Continue" | Screen B | HTMX swap | form data in session |

#### Background Tasks
| Trigger | System | Duration | Progress UI | On Complete | On Fail |
|---------|--------|----------|-------------|-------------|---------|
| Upload doc | Temporal: DocumentUploadWorkflow | 5-30s | Poll every 3s | Show tags | Error text |

#### Conditional UI
| Element | Condition | Behavior |
|---------|-----------|----------|
| Submit button | All required fields filled | Enable |
| Admin section | user.role == 'admin' | Show |
```

## Rules

- **Ask, don't assume** — if the design shows a button, ask what it does
- **Be specific** — "what happens on click?" not "how does this work?"
- **Cover failure cases** — every action can fail, ask about error handling
- **Cover empty states** — what if there's no data yet?
- **Ask about HTMX vs full page** — this is critical for the implementation approach
- **Ask about feature flags** — features often start behind LaunchDarkly gates
- **Group questions efficiently** — batch related questions, don't ask one at a time
- **Present what you know** — show the user your understanding, ask them to correct/fill gaps
