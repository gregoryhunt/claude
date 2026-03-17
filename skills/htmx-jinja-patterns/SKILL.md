---
name: htmx-jinja-patterns
description: Guide for generating HTMX + Jinja2 + Tailwind CSS code following the project's established patterns. ALWAYS reference when implementing UI templates.
---

# HTMX + Jinja2 + Tailwind Patterns

Follow these patterns when implementing UI templates for the OnboardAI platform.

## When This Skill Applies

- Implementing any new template in `src/onboardai/templates/`
- Modifying existing templates
- Creating HTMX interactions
- Building forms, tables, or any UI component

## Project Architecture

```
src/onboardai/
├── templates/           # Jinja2 templates (139+ files)
│   ├── admin/          # Admin portal
│   ├── developer/      # Developer portal
│   │   ├── onboarding/
│   │   ├── product_assessments/
│   │   └── products/
│   ├── implementer/    # Implementer portal
│   │   ├── assessments/
│   │   ├── onboarding/
│   │   └── teams/
│   └── shared/         # Shared partials (if any)
├── static/
│   ├── css/tailwind.css  # Compiled Tailwind
│   └── js/               # JavaScript files
└── routes/              # FastAPI route handlers
```

## HTMX Patterns

### Partial Page Updates (most common)

Use `hx-get` / `hx-post` with `hx-target` to swap sections without full page reload:

```html
<!-- Trigger: button that loads content into a target -->
<button hx-get="/developer/products/{{ product.id }}/artifacts"
        hx-target="#content-area"
        hx-swap="innerHTML"
        class="px-4 py-2 bg-indigo-600 text-white rounded-md">
  View Artifacts
</button>

<!-- Target: container that receives the partial -->
<div id="content-area">
  <!-- Content swapped here -->
</div>
```

### Form Submissions

```html
<form hx-post="/developer/products/{{ product.id }}/update"
      hx-target="#form-result"
      hx-swap="innerHTML"
      hx-indicator="#submit-spinner">

  <input type="text" name="name" value="{{ product.name }}"
         class="block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 sm:text-sm">

  <button type="submit"
          class="px-4 py-2 bg-indigo-600 text-white rounded-md hover:bg-indigo-700 disabled:opacity-50 disabled:cursor-not-allowed">
    Save
    <span id="submit-spinner" class="htmx-indicator">
      <!-- loading spinner SVG -->
    </span>
  </button>
</form>
```

### Polling for Status Updates

```html
<!-- Poll every 3 seconds until processing is complete -->
<div hx-get="/developer/products/{{ product.id }}/artifacts/status"
     hx-trigger="every 3s [!document.querySelector('[data-processing-complete]')]"
     hx-swap="innerHTML">
  Processing...
</div>
```

### Out-of-Band Swaps (update multiple sections at once)

```html
<!-- In the response template, swap additional elements -->
<div id="main-content">
  <!-- Primary content -->
</div>

<div id="sidebar-stats" hx-swap-oob="innerHTML">
  <!-- This replaces the sidebar stats section too -->
</div>
```

## Jinja2 Patterns

### Template Inheritance

```html
<!-- Base layout -->
{% extends "layouts/developer.html" %}

{% block title %}Page Title{% endblock %}

{% block content %}
  <!-- Page content -->
{% endblock %}
```

### Partials / Includes

```html
<!-- Include a partial template -->
{% include "developer/products/_product_card.html" %}

<!-- Partial naming convention: prefix with underscore -->
<!-- _partial_name.html -->
```

### Conditionals for State

```html
<!-- Show different UI based on state -->
{% if assessment.status == "complete" %}
  <span class="text-green-600 font-medium">Complete</span>
{% elif assessment.status == "processing" %}
  <span class="text-yellow-600 font-medium">Processing...</span>
{% else %}
  <span class="text-gray-500">Pending</span>
{% endif %}

<!-- Disabled button based on condition -->
<button class="px-4 py-2 bg-indigo-600 text-white rounded-md
               {% if not can_submit %}opacity-50 cursor-not-allowed{% endif %}"
        {% if not can_submit %}disabled{% endif %}>
  Submit
</button>
```

### Loops with Index

```html
{% for item in items %}
  <div class="{% if not loop.last %}border-b border-gray-200{% endif %} py-4">
    {{ item.name }}
  </div>
{% endfor %}

<!-- Empty state -->
{% if not items %}
  <div class="text-center py-12 text-gray-500">
    <p>No items yet.</p>
  </div>
{% endif %}
```

## Tailwind Patterns

### Consistent Spacing

Follow the project's spacing conventions — check existing templates for the established scale:
- Page padding: typically `px-4 sm:px-6 lg:px-8`
- Section spacing: typically `py-6` or `space-y-6`
- Card padding: typically `p-4` or `p-6`
- Form field spacing: typically `space-y-4`

### Color Usage

Check existing templates for the project's color palette. Common patterns:
- Primary action: `bg-indigo-600 hover:bg-indigo-700 text-white`
- Secondary action: `bg-white border border-gray-300 text-gray-700 hover:bg-gray-50`
- Danger action: `bg-red-600 hover:bg-red-700 text-white`
- Status colors: green (complete), yellow (processing), gray (pending), red (error)

### Before Implementing

1. **Search existing templates** for similar patterns:
   ```
   Grep: class=".*bg-indigo in src/onboardai/templates/
   ```
2. **Match existing conventions** — don't introduce new colors or spacing that diverge
3. **Check for shared partials** that handle common elements (headers, footers, modals)

## FastAPI Route Patterns

Routes that serve templates follow this pattern:

```python
@router.get("/products/{product_id}")
async def show_product(
    request: Request,
    product_id: int,
    session: AsyncSession = Depends(get_session),
    current_user: User = Depends(get_current_user),
):
    product = await session.get(Product, product_id)
    return templates.TemplateResponse(
        "developer/products/show.html",
        {"request": request, "product": product, "user": current_user},
    )
```

For HTMX partial responses, return just the partial template (no layout):

```python
@router.get("/products/{product_id}/artifacts")
async def list_artifacts(request: Request, product_id: int, ...):
    # Return partial template for HTMX swap
    return templates.TemplateResponse(
        "developer/products/artifacts/_artifacts_content.html",
        {"request": request, "artifacts": artifacts},
    )
```

## Key Rules

- **Partials use underscore prefix**: `_partial_name.html`
- **HTMX for dynamic updates**: Prefer HTMX partials over full page reloads
- **Always handle empty states**: Every list/table should have an empty state
- **Always handle loading states**: Use `hx-indicator` for async operations
- **Match existing templates**: Search before creating new patterns
- **Tailwind only**: No custom CSS unless absolutely necessary
