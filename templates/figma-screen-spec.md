# Screen Specification: {SCREEN_NAME}

## Design Metadata
- **Figma URL**: {FIGMA_URL}
- **Figma Frame**: {FRAME_NAME}
- **Dimensions**: {WIDTH} x {HEIGHT}
- **Background**: {BACKGROUND_COLOR}

## Element Inventory

### {SECTION_1_NAME} (container)
- **Layout**: {FLEX_GRID_DETAILS}
- **Dimensions**: {WIDTH} x {HEIGHT}
- **Padding**: {PADDING_PX} → `{TAILWIND_PADDING}`
- **Background**: {HEX} → `{TAILWIND_BG}`
- **Border**: {BORDER_DETAILS} → `{TAILWIND_BORDER}`

#### {ELEMENT_1_NAME} ({ELEMENT_TYPE})
- **Content**: "{TEXT_CONTENT}"
- **Font**: {FONT_FAMILY} {SIZE}px/{LINE_HEIGHT}px {WEIGHT} → `{TAILWIND_TEXT_CLASSES}`
- **Color**: {HEX} → `{TAILWIND_COLOR}`
- **States**:
  - Default: {DEFAULT_STYLES}
  - Hover: {HOVER_STYLES}
  - Disabled: {DISABLED_STYLES}
  - Loading: {LOADING_STYLES}

<!-- Repeat for every element -->

## Proposed HTML Structure

```html
<!-- Complete HTML with Tailwind classes for this screen -->
```

## Existing Component Matches

| Design Element | Existing Template | Match Quality |
|---------------|-------------------|---------------|
| {ELEMENT} | {FILE_PATH}:{LINE} | Exact / Partial / None |

## State Variations

### Default State
{DESCRIPTION}

### Empty State
{DESCRIPTION_OR_NOT_DESIGNED}

### Loading State
{DESCRIPTION}

### Error State
{DESCRIPTION}

## Interactions (from discovery)

| Element | Action | Method | Target | Disabled When |
|---------|--------|--------|--------|---------------|
| {BUTTON} | {ACTION} | {HTMX_OR_NAVIGATE} | {TARGET_ID} | {CONDITION} |

## Gaps and Ambiguities

- [ ] {ITEM_NEEDING_CLARIFICATION}

## Custom CSS Required

| Element | Why Tailwind Insufficient | Custom CSS |
|---------|--------------------------|------------|
| {ELEMENT} | {REASON} | {CSS} |
