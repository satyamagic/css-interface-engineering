# Accessibility Basics

## Problem

Inaccessible interfaces exclude users. People with disabilities, assistive technology users, keyboard-only users, and users in constrained environments cannot use products built without accessibility consideration.

**Legal reality:** Many jurisdictions require digital accessibility (ADA, Section 508, WCAG).  
**Practical reality:** Accessible design benefits everyone, not just disabled users.

**Need:** Fundamental accessibility practices integrated from the start, not retrofitted.

## Core Principle

**Accessible by default. Inclusive design is not optional.**

Not "add accessibility later." Built into every interface decision.

## WCAG Conformance Levels

**Level A:** Minimum. If you fail this, interface is unusable for many.  
**Level AA:** Target for production. Industry standard, legally defensible.  
**Level AAA:** Nice to have. Not always achievable, but pursue where possible.

**This guide targets WCAG 2.1 Level AA minimum.**

## Semantic HTML

### Use Correct Elements

**Bad:**
```html
<div onclick="submit()">Submit</div>
<span class="heading">Title</span>
<div class="button">Click me</div>
```

**Good:**
```html
<button type="submit">Submit</button>
<h1>Title</h1>
<button>Click me</button>
```

**Why:** Semantic elements provide:
- Keyboard navigation
- Screen reader context
- Browser default behaviors
- Focus management

### Document Structure

```html
<header>
  <nav aria-label="Main navigation">
    <!-- navigation links -->
  </nav>
</header>

<main>
  <article>
    <h1>Page Title</h1>
    <section>
      <h2>Section Title</h2>
      <!-- content -->
    </section>
  </article>
</main>

<footer>
  <!-- footer content -->
</footer>
```

**Heading hierarchy:** h1 → h2 → h3 (don't skip levels)  
**Landmark regions:** header, nav, main, aside, footer

## Keyboard Navigation

### All Interactive Elements Must Be Keyboard Accessible

**Test:** Can you navigate entire interface with only Tab, Enter, Space, and Arrow keys?

**Requirements:**
- Tab moves focus forward
- Shift+Tab moves focus backward
- Enter activates buttons/links
- Space toggles checkboxes, activates buttons
- Arrow keys navigate within components (menus, tabs, etc.)

### Focus Indicators

**Don't do this:**
```css
*:focus {
  outline: none; /* NEVER */
}
```

**Do this:**
```css
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
```

`:focus-visible` shows focus for keyboard users, hides for mouse users.

### Focus Management

**After modal opens:**
```javascript
function openModal() {
  const modal = document.querySelector('.modal');
  modal.showModal();
  modal.querySelector('.modal-close').focus();
}
```

**After element is deleted:**
```javascript
function deleteItem(id) {
  const item = document.querySelector(`[data-id="${id}"]`);
  const nextItem = item.nextElementSibling || item.previousElementSibling;
  item.remove();
  nextItem?.focus();
}
```

Focus should never be lost. Always move to logical next element.

## Color and Contrast

### Contrast Ratios

**Normal text (< 18pt):** 4.5:1 minimum (AA), 7:1 ideal (AAA)  
**Large text (≥ 18pt):** 3:1 minimum (AA), 4.5:1 ideal (AAA)  
**UI components:** 3:1 minimum (AA)

**Testing:**
- Chrome DevTools: Inspect element → Check contrast ratio
- WAVE browser extension
- Contrast checker tools

### Don't Rely on Color Alone

**Bad:**
```html
<span style="color: red;">Error</span>
<span style="color: green;">Success</span>
```

**Good:**
```html
<span class="error">
  <svg aria-hidden="true"><use href="#icon-error"/></svg>
  Error: Invalid input
</span>
<span class="success">
  <svg aria-hidden="true"><use href="#icon-success"/></svg>
  Success: Saved
</span>
```

Use icons, text, or patterns in addition to color.

## ARIA (Accessible Rich Internet Applications)

### When to Use ARIA

**First rule of ARIA:** Don't use ARIA if HTML element exists.

**Use ARIA when:**
- HTML doesn't provide needed semantics
- Creating custom interactive widgets
- Providing additional context

**Don't use ARIA:**
- To recreate existing HTML elements
- When semantic HTML works
- Without understanding what it does

### Common ARIA Attributes

**`aria-label`** - Provides accessible name:
```html
<button aria-label="Close dialog">
  <svg><use href="#icon-x"/></svg>
</button>
```

**`aria-labelledby`** - References element providing label:
```html
<div role="dialog" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm Action</h2>
</div>
```

**`aria-describedby`** - References element providing description:
```html
<input
  type="password"
  aria-describedby="password-requirements"
/>
<p id="password-requirements">
  Must be at least 8 characters
</p>
```

**`aria-live`** - Announces dynamic content:
```html
<div aria-live="polite" aria-atomic="true">
  Item added to cart
</div>
```

**`aria-expanded`** - Indicates expandable element state:
```html
<button aria-expanded="false" aria-controls="menu">
  Menu
</button>
<ul id="menu" hidden>...</ul>
```

**`aria-current`** - Indicates current item:
```html
<nav>
  <a href="/" aria-current="page">Home</a>
  <a href="/about">About</a>
</nav>
```

### ARIA States

Update dynamically as state changes:

```javascript
function toggleMenu(button) {
  const isExpanded = button.getAttribute('aria-expanded') === 'true';
  button.setAttribute('aria-expanded', !isExpanded);
  
  const menu = document.getElementById(button.getAttribute('aria-controls'));
  menu.hidden = isExpanded;
}
```

## Form Accessibility

### Label Every Input

**Bad:**
```html
<input type="text" placeholder="Email" />
```

**Good:**
```html
<label for="email">Email</label>
<input type="text" id="email" name="email" />
```

Placeholder is not a label. Screen readers may not announce it.

### Error Handling

**Requirements:**
- Identify error clearly
- Explain what's wrong
- Suggest how to fix
- Move focus to first error

**Implementation:**
```html
<label for="email">Email</label>
<input
  type="email"
  id="email"
  aria-invalid="true"
  aria-describedby="email-error"
/>
<span id="email-error" role="alert">
  Invalid email format. Example: user@example.com
</span>
```

```javascript
// Move focus to first error
const firstError = form.querySelector('[aria-invalid="true"]');
firstError?.focus();
```

### Required Fields

```html
<label for="username">
  Username
  <span aria-label="required">*</span>
</label>
<input
  type="text"
  id="username"
  required
  aria-required="true"
/>
```

Both `required` attribute and `aria-required` for maximum compatibility.

## Images and Media

### Alt Text

**Decorative images:**
```html
<img src="decorative-border.png" alt="" role="presentation" />
```

Empty alt and role="presentation" tells screen readers to ignore.

**Informative images:**
```html
<img src="chart.png" alt="Sales increased 25% in Q4 2025" />
```

Describe the information, not the image.

**Functional images (links/buttons):**
```html
<a href="/settings">
  <img src="gear-icon.png" alt="Settings" />
</a>
```

Alt describes function, not appearance.

### Video and Audio

**Captions (for deaf users):**
```html
<video controls>
  <source src="video.mp4" type="video/mp4" />
  <track kind="captions" src="captions.vtt" srclang="en" label="English" />
</video>
```

**Transcripts (for all users):**
```html
<audio controls src="podcast.mp3"></audio>
<details>
  <summary>Transcript</summary>
  <p>Full text transcript...</p>
</details>
```

## Motion and Animation

### Respect User Preferences

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Users with vestibular disorders need this. Not optional.

### Avoid Flashing Content

**WCAG 2.3.1:** No more than 3 flashes per second.

Flashing content can trigger seizures. Avoid entirely when possible.

## Screen Reader Testing

### Test With Actual Screen Readers

**Free options:**
- NVDA (Windows)
- JAWS (Windows, trial)
- VoiceOver (macOS, iOS)
- TalkBack (Android)

**Basic test script:**
1. Navigate page with Tab key only
2. Use screen reader to read all content
3. Complete key user flow (form submission, checkout, etc.)
4. Verify all interactive elements are announced
5. Verify all images have appropriate alt text

### Common Issues

**Missing labels:**
```html
<!-- Screen reader announces: "Edit, button" -->
<button><svg><use href="#icon-edit"/></svg></button>

<!-- Screen reader announces: "Edit user profile, button" -->
<button aria-label="Edit user profile">
  <svg><use href="#icon-edit"/></svg>
</button>
```

**Unclear link text:**
```html
<!-- Bad: "Click here" without context -->
<a href="/docs">Click here</a> for documentation

<!-- Good: Link text is self-explanatory -->
<a href="/docs">Read the documentation</a>
```

**Inaccessible custom widgets:**
```html
<!-- Bad: Div with onClick, no keyboard support -->
<div onclick="openMenu()">Menu</div>

<!-- Good: Button with proper semantics -->
<button type="button" aria-expanded="false" aria-controls="menu">
  Menu
</button>
```

## Quick Wins

### Low-Effort, High-Impact

1. **Use semantic HTML** (5 minutes per component)
2. **Add alt text to images** (2 minutes per image)
3. **Ensure 4.5:1 contrast** (10 minutes to audit)
4. **Don't remove focus indicators** (0 minutes - don't do it)
5. **Label all form inputs** (2 minutes per form)
6. **Add skip link** (5 minutes once):

```html
<a href="#main" class="skip-link">Skip to main content</a>
<nav>...</nav>
<main id="main">...</main>
```

```css
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

## Automated Testing

### Tools

**Linters:**
- eslint-plugin-jsx-a11y (React)
- axe-linter
- lighthouse --only-categories=accessibility

**Runtime testing:**
```javascript
import { axe } from 'jest-axe';

test('Component should be accessible', async () => {
  const { container } = render(<MyComponent />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

**CI/CD integration:**
```yaml
# .github/workflows/a11y.yml
- name: Run accessibility tests
  run: npm run test:a11y
```

### Limitations

Automated testing catches ~30-40% of accessibility issues. Manual testing required for:
- Keyboard navigation flow
- Screen reader compatibility
- Focus management
- Context and clarity

## Accessibility Checklist

- [ ] Semantic HTML used throughout
- [ ] All interactive elements keyboard accessible
- [ ] Focus indicators visible and clear
- [ ] Color contrast meets 4.5:1 minimum
- [ ] Information not conveyed by color alone
- [ ] All images have appropriate alt text
- [ ] Forms have labels and error handling
- [ ] ARIA used correctly (not overused)
- [ ] Reduced motion preferences respected
- [ ] Page structure uses landmarks
- [ ] Heading hierarchy is logical
- [ ] Tested with screen reader
- [ ] Tested with keyboard only
- [ ] Automated tests passing

---

**Key insight:** Accessibility is not a feature to add. It's a constraint to design within.

Start accessible. Stay accessible. Test with real users.

If it's not accessible, it's not finished.
