# Naming Conventions

## Problem

Inconsistent naming creates cognitive overhead. Developers waste time deciphering abbreviations, guessing file locations, and maintaining mental maps of arbitrary patterns.

**Need:** Systematic naming that reveals purpose and location without documentation.

## Core Principles

### 1. Clarity Over Brevity

**Bad:** `usrMgr.js`, `btn.css`, `utils.js`  
**Good:** `user-manager.js`, `button.css`, `string-helpers.js`

Abbreviations save keystrokes, not comprehension time.

**Exception:** Industry-standard abbreviations (`HTTP`, `URL`, `API`, `HTML`, `CSS`)

### 2. Consistent Patterns

Same type of thing = same naming pattern.

Components: `PascalCase.js`  
Utilities: `kebab-case.js`  
Constants: `SCREAMING_SNAKE_CASE`  

No mixing `userService.js` and `product-service.js` in the same project.

### 3. Reveal Intent, Not Implementation

**Bad:** `red-button`, `fetch-data`, `loop-users`  
**Good:** `primary-button`, `load-user-profile`, `process-users`

Names describe what, not how. Implementation can change; purpose remains.

## File Naming

### Components

**Pattern:** `PascalCase.ext`

```
Button.js
UserProfile.js
NavigationMenu.js
```

**Why PascalCase?** Matches JavaScript class/component naming. Visual distinction from utilities.

### Utilities and Helpers

**Pattern:** `kebab-case.js`

```
string-helpers.js
date-formatter.js
api-client.js
```

**Why kebab-case?** URL-safe, readable, distinct from components.

### Styles

**Pattern:** Match associated component or use `kebab-case.css`

```
Button.css          // Component-specific
button.css          // Generic button styles
design-tokens.css   // Shared tokens
```

### Tests

**Pattern:** `filename.test.ext` or `filename.spec.ext`

```
Button.test.js
user-service.spec.js
```

Co-locate with tested file:
```
/components
  /Button
    Button.js
    Button.test.js
    Button.css
```

## Variable Naming

### JavaScript

**Constants (immutable):**
```javascript
const MAX_RETRY_ATTEMPTS = 3;
const API_BASE_URL = 'https://api.example.com';
const DEFAULT_TIMEOUT = 5000;
```

`SCREAMING_SNAKE_CASE` signals "don't change this."

**Variables (mutable):**
```javascript
let userCount = 0;
let isAuthenticated = false;
let selectedItems = [];
```

`camelCase` for regular variables. Start with lowercase.

**Functions:**
```javascript
function calculateTotal(items) { }
function getUserById(id) { }
function isValidEmail(email) { }
```

`camelCase`. Verbs for actions. `is/has/should` prefix for booleans.

**Classes:**
```javascript
class UserManager { }
class HttpClient { }
class ValidationError { }
```

`PascalCase`. Nouns for things.

### Boolean Variables

**Pattern:** Start with `is`, `has`, `should`, `can`, `will`

```javascript
const isLoading = true;
const hasPermission = false;
const shouldRetry = true;
const canEdit = false;
const willExpire = true;
```

**Avoid:** `loading`, `permission`, `retry` (ambiguous type)

### Arrays and Collections

**Pattern:** Plural nouns

```javascript
const users = [];
const errorMessages = [];
const selectedIds = new Set();
```

**Avoid:** `userList`, `userArray` (type in name is redundant)

## CSS Naming

### Component Classes

**Pattern:** `component-element-modifier`

```css
.button { }
.button-icon { }
.button-primary { }
.button-large { }
```

Not strict BEM. Simplified for readability.

### Utility Classes

**Pattern:** `property-value`

```css
.flex { display: flex; }
.grid { display: grid; }
.text-center { text-align: center; }
.mt-lg { margin-top: var(--space-lg); }
```

Abbreviated utilities are acceptable when pattern is clear.

### State Classes

**Pattern:** `is-state` or `has-state`

```css
.button.is-active { }
.card.is-loading { }
.input.has-error { }
```

`is-*` prefix prevents conflicts with other classes.

### Custom Properties (CSS Variables)

**Pattern:** `--category-property-variant`

```css
:root {
  --color-text-primary: #e8e8e8;
  --color-bg-secondary: #1a1a1a;
  --space-margin-lg: 1.5rem;
  --font-heading-bold: 700;
}
```

Hierarchical structure aids autocomplete and understanding.

## Function Naming

### Action Functions

**Pattern:** `verb + noun`

```javascript
function createUser(data) { }
function deleteComment(id) { }
function updateProfile(changes) { }
function fetchOrders() { }
function validateEmail(email) { }
```

### Boolean Functions

**Pattern:** `is/has/should/can + adjective/noun`

```javascript
function isAuthenticated() { }
function hasPermission(user, resource) { }
function shouldRetry(attempt) { }
function canEdit(user, document) { }
```

Return value obvious from name.

### Event Handlers

**Pattern:** `handle + EventName` or `on + EventName`

```javascript
function handleClick(event) { }
function handleSubmit(event) { }
function onUserLogin() { }
function onDataReceived(data) { }
```

Consistent across codebase. Pick one pattern.

### Transformations

**Pattern:** Input type `to` output type

```javascript
function userToDTO(user) { }
function jsonToObject(json) { }
function stringToDate(str) { }
```

Clear transformation direction.

## Directory Structure

### Grouping Strategy

**By feature (preferred for large projects):**
```
/features
  /auth
    Login.js
    Signup.js
    auth-service.js
    auth.test.js
  /dashboard
    Dashboard.js
    dashboard-api.js
```

**By type (acceptable for small projects):**
```
/components
  Button.js
  Card.js
/services
  auth-service.js
  user-service.js
/utils
  date-helpers.js
```

### Special Directories

```
/components     # Reusable UI components
/features       # Feature modules
/utils          # Pure functions, helpers
/services       # API clients, external integrations
/hooks          # React hooks (if applicable)
/styles         # Global styles, tokens
/types          # TypeScript definitions
/config         # Configuration files
/tests          # Integration/E2E tests
```

## Naming Anti-Patterns

### Avoid

**Single-letter variables (except loops):**
```javascript
const u = getUser();  // Bad
const user = getUser();  // Good

for (let i = 0; i < items.length; i++) { }  // OK for loop counter
```

**Meaningless names:**
```javascript
const data = fetchData();  // What data?
const result = calculate();  // What result?
```

**Type in name (redundant):**
```javascript
const userArray = [];  // Just `users`
const isLoadingBool = false;  // Just `isLoading`
```

**Negatives (confusing):**
```javascript
const isNotDisabled = true;  // Use `isEnabled`
if (!isNotValid) { }  // Use `isValid` and flip logic
```

**Overly generic:**
```javascript
function handle() { }  // Handle what?
const manager = new Manager();  // Manage what?
```

## Consistency Enforcement

### Linting

**ESLint naming rules:**
```json
{
  "rules": {
    "camelcase": ["error", { "properties": "never" }],
    "new-cap": ["error", { "capIsNew": false }]
  }
}
```

**Stylelint for CSS:**
```json
{
  "rules": {
    "selector-class-pattern": "^[a-z][a-z0-9-]*$"
  }
}
```

### Code Review

Naming should be explicit review criterion:
- "This variable name doesn't reveal intent"
- "Function name doesn't match what it does"
- "File should be in different directory based on purpose"

## Language-Specific Conventions

### JavaScript/TypeScript

```javascript
// Variables: camelCase
const userName = 'John';

// Constants: SCREAMING_SNAKE_CASE
const MAX_USERS = 100;

// Functions: camelCase
function getUserName() { }

// Classes: PascalCase
class UserManager { }

// Private (convention): _prefix
class User {
  _privateMethod() { }
}
```

### CSS

```css
/* Classes: kebab-case */
.user-profile { }

/* IDs: kebab-case (avoid when possible) */
#main-header { }

/* Custom properties: --kebab-case */
--primary-color: blue;
```

### HTML

```html
<!-- Elements: lowercase -->
<div class="user-profile">

<!-- Attributes: kebab-case -->
<button data-user-id="123" aria-label="Close">

<!-- Custom elements: kebab-case with hyphen required -->
<user-profile-card>
```

## Real-World Example

### Before (Inconsistent)

```
/src
  Btn.js
  user_profile.jsx
  dataService.js
  utils.js
  CSS/
    btn.css
    UserProfile.css
```

```javascript
// Btn.js
function Btn({ txt, clk }) {
  const btnState = true;
  return <button onClick={clk}>{txt}</button>;
}
```

**Issues:** Mixed case styles, unclear abbreviations, inconsistent structure

### After (Consistent)

```
/src
  /components
    /Button
      Button.js
      Button.css
      Button.test.js
    /UserProfile
      UserProfile.js
      UserProfile.css
  /services
    user-service.js
  /utils
    string-helpers.js
```

```javascript
// Button.js
function Button({ text, onClick }) {
  const isEnabled = true;
  return <button onClick={onClick}>{text}</button>;
}
```

**Improvements:** Consistent casing, clear names, organized structure

---

**Key insight:** Naming is documentation that never goes out of date.

Good names make code self-explanatory. Bad names require constant reference.

Choose names future maintainers will thank you for.
