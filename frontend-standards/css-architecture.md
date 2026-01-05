# CSS Architecture

## Problem

CSS grows chaotically. Styles cascade unpredictably. Specificity wars emerge. File organization becomes arbitrary. Changes cause unintended side effects.

**Need:** Systematic architecture for predictable, maintainable CSS at scale.

## Core Principles

### 1. Composition Over Inheritance

**Bad:**
```css
.button { /* base styles */ }
.button-primary { /* inherits + overrides */ }
.button-primary-large { /* inherits + overrides */ }
```

Brittle. Fragile inheritance chain.

**Good:**
```css
.button { /* base */ }
.button-primary { /* variant */ }
.button-large { /* size */ }
```

```html
<button class="button button-primary button-large">
```

Composable. Each class does one thing.

### 2. Locality of Behavior

Styles should live near the components they affect.

**File structure:**
```
/components
  /button
    Button.html
    button.css
  /card
    Card.html
    card.css
```

Not a single monolithic `styles.css`.

### 3. Naming That Reveals Intent

**Bad:** `.btn-1`, `.blue-box`, `.container-2`  
**Good:** `.button-primary`, `.card-header`, `.layout-sidebar`

Names communicate purpose, not appearance.

## File Organization

### Layered Architecture

```
/styles
  /00-reset
    reset.css         # Baseline normalization
  /01-tokens
    colors.css        # Design tokens
    spacing.css
    typography.css
  /02-layout
    grid.css          # Layout systems
    utilities.css
  /03-components
    button.css        # Reusable components
    card.css
    input.css
  /04-pages
    dashboard.css     # Page-specific overrides
```

**Import order matters:**
```css
@import './00-reset/reset.css';
@import './01-tokens/colors.css';
@import './01-tokens/spacing.css';
@import './02-layout/grid.css';
/* ... */
```

Lower layers don't know about higher layers. Dependency flows one direction.

## Specificity Management

### Rule: Minimize Specificity

**Target specificity range:** 0-1-0 to 0-2-0  
(0 IDs, 1-2 classes, 0 elements)

**Avoid:**
```css
div#content .sidebar ul li a { }  /* Specificity: 1-1-4 */
```

**Prefer:**
```css
.nav-link { }  /* Specificity: 0-1-0 */
```

### Handling Specificity Conflicts

**Option 1: Add context class**
```css
.modal .button { }
```

**Option 2: Variant class**
```css
.button-modal { }
```

**Option 3: Data attribute**
```css
.button[data-context="modal"] { }
```

Never use `!important` except for utilities that must always apply.

## Design Tokens

### Custom Properties for Values

```css
:root {
  /* Color palette */
  --color-gray-900: #0a0a0a;
  --color-gray-800: #1a1a1a;
  --color-blue-500: #00d9ff;
  
  /* Semantic tokens */
  --color-bg-primary: var(--color-gray-900);
  --color-text-primary: var(--color-gray-100);
  --color-accent: var(--color-blue-500);
  
  /* Spacing scale */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
  
  /* Typography scale */
  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
}
```

**Why custom properties?**
- Runtime changes (theme switching)
- Cascading when needed
- Inheritance across shadow DOM
- Browser DevTools inspection

### Token Naming

`--[category]-[property]-[variant]`

Examples:
- `--color-text-primary`
- `--space-margin-lg`
- `--font-heading-bold`

## Utility Classes

### When to Use

**Good use cases:**
- Spacing adjustments
- Display properties
- Text alignment
- Visibility states

**Bad use cases:**
- Complex component styles
- Multiple properties bundled
- Replacing component classes entirely

### Implementation

```css
/* Spacing utilities */
.mt-sm { margin-top: var(--space-sm); }
.mt-md { margin-top: var(--space-md); }
.mt-lg { margin-top: var(--space-lg); }

/* Display utilities */
.flex { display: flex; }
.grid { display: grid; }
.hidden { display: none; }

/* Text utilities */
.text-center { text-align: center; }
.text-bold { font-weight: var(--weight-bold); }
```

Limit scope. Don't recreate Tailwind.

## Component Patterns

### BEM-like Naming

```css
/* Block */
.card { }

/* Element */
.card-header { }
.card-body { }
.card-footer { }

/* Modifier */
.card-elevated { }
.card-interactive { }
```

Not strict BEM (`card__header--primary`). Simplified for readability.

### State Classes

```css
.button { }
.button.is-loading { }
.button.is-disabled { }
.button.is-active { }
```

`is-*` prefix clearly indicates state.

Alternative: data attributes
```css
.button[data-state="loading"] { }
```

## Performance Considerations

### Selector Performance

**Fast:**
```css
.class { }              /* Single class */
.class1.class2 { }      /* Multiple classes */
```

**Slow:**
```css
* { }                   /* Universal selector */
[type="text"] { }       /* Attribute without tag */
div > * { }             /* Universal with combinator */
```

**Rule:** Avoid universal selectors except in reset. Prefer classes.

### Reducing CSS Size

**Techniques:**
- Remove unused styles (PurgeCSS, manual audit)
- Combine similar rules
- Use shorthand properties
- Avoid redundant vendor prefixes

**Target:** < 50KB uncompressed for critical CSS

### Critical CSS

Extract above-the-fold styles:
```html
<style>
  /* Inline critical CSS */
  :root { /* tokens */ }
  body { /* layout */ }
  .header { /* visible components */ }
</style>
<link rel="stylesheet" href="full.css">
```

## Responsive Design

### Mobile-First

```css
.card {
  padding: var(--space-md);
}

@media (min-width: 768px) {
  .card {
    padding: var(--space-lg);
  }
}

@media (min-width: 1024px) {
  .card {
    padding: var(--space-xl);
  }
}
```

Base styles for mobile. Enhance for larger screens.

### Container Queries (When Supported)

```css
@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}
```

Component adapts to container, not viewport.

## Maintenance Practices

### Code Review Checklist

- [ ] Specificity under 0-3-0
- [ ] No inline styles (except dynamic JS-controlled)
- [ ] Design tokens used for colors/spacing/typography
- [ ] Mobile-first responsive design
- [ ] No `!important` except utilities
- [ ] Component files co-located with markup
- [ ] Naming follows conventions
- [ ] Browser compatibility verified

### Refactoring Triggers

When to refactor:
- Same styles repeated 3+ times → Extract component
- Specificity wars → Redesign selectors
- Unclear names → Rename for clarity
- Dead code accumulating → Audit and remove

## Tooling

### Recommended

**Linting:** Stylelint with agreed rules  
**Formatting:** Prettier for consistency  
**Autoprefixer:** Add vendor prefixes automatically  
**PostCSS:** For custom transformations if needed  

### Avoid Over-Tooling

Don't need:
- CSS-in-JS for static sites
- Complex build pipeline for simple projects
- Framework-specific solutions for generic problems

## Anti-Patterns

**Inline styles (HTML):**
```html
<div style="margin-top: 20px; color: blue;">
```

**Deep nesting:**
```css
.header .nav .menu .item .link { }
```

**Overly generic names:**
```css
.container { }
.wrapper { }
.box { }
```

**Styling by tag:**
```css
div { }
span { }
```

**!important overuse:**
```css
.button {
  color: blue !important;
  padding: 10px !important;
}
```

## Real-World Example

### Before (Problematic)

```css
/* styles.css */
div.container div.box {
  background: #fff;
  padding: 20px;
}

div.container div.box h3 {
  color: #333;
  font-size: 24px;
}

div.container div.box.featured {
  background: #f0f0f0 !important;
}
```

**Issues:** High specificity, tag selectors, !important, no tokens

### After (Improved)

```css
/* tokens.css */
:root {
  --color-bg-card: #ffffff;
  --color-bg-featured: #f0f0f0;
  --color-text-heading: #333333;
  --text-heading: 1.5rem;
  --space-md: 1.25rem;
}

/* card.css */
.card {
  background-color: var(--color-bg-card);
  padding: var(--space-md);
  border-radius: 0.5rem;
}

.card-title {
  color: var(--color-text-heading);
  font-size: var(--text-heading);
  margin-bottom: var(--space-sm);
}

.card-featured {
  background-color: var(--color-bg-featured);
}
```

```html
<article class="card card-featured">
  <h3 class="card-title">Title</h3>
</article>
```

**Improvements:** Low specificity, design tokens, semantic naming, no tag selectors

---

**Key insight:** CSS architecture isn't about tools or methodologies. It's about making the next engineer's job easier.

Write CSS you'd want to inherit.
