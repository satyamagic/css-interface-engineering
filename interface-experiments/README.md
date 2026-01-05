# Interface Experiments

Focused explorations of specific interface patterns and interactions.

## Purpose

Each experiment isolates a single problem space to explore technical constraints and design tradeoffs.

Not production code. Not comprehensive solutions. Intentional constraints that reveal system behavior.

## Structure

Each experiment is self-contained:

```
experiment-name/
  index.html
  styles.css
  README.md
```

No build tools. No dependencies. Pure HTML and CSS.

## Experiments

### 1. CSS-Only System Boot Animation
Simulating system initialization states using pure CSS keyframes and state transitions.

Constraints:
- No JavaScript
- Performance budget: 60fps on low-end devices
- Accessible reduced-motion fallback

[View experiment](css-boot-animation/)

### 2. HUD Grid Layout System
Information-dense heads-up display with dynamic grid zones and visual hierarchy.

Constraints:
- CSS Grid only
- Responsive without media queries
- High contrast for readability

[View experiment](hud-grid-layout/)

### 3. Interaction Easing and Motion Timing
Exploring the relationship between easing curves and perceived interface responsiveness.

Constraints:
- Standard cubic-bezier functions
- Comparison of timing effects
- Physical motion principles

[View experiment](motion-timing/)

### 4. Reduced-Motion Accessibility Fallback
Designing interfaces that adapt gracefully when users prefer reduced motion.

Constraints:
- prefers-reduced-motion media query
- Instant state transitions
- Alternative visual feedback

[View experiment](reduced-motion/)

## Running Locally

```bash
cd experiment-name
python3 -m http.server 8000
```

Visit `http://localhost:8000`

---

Constraints reveal system behavior. Every limitation teaches something.
