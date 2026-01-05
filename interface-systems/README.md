# Interface Systems

System-first portfolio demonstrating interface architecture using pure HTML and CSS.

## Philosophy

This is not a project showcase. It is a demonstration of system thinking applied to interface design.

Interfaces should emerge from clear system constraints:
- Performance budget
- Information hierarchy
- User capability and context
- Failure modes and degradation paths

## Approach

**No frameworks**  
Dependencies are liabilities. HTML and CSS express interface logic directly.

**Performance-first**  
Every byte matters. Optimize for initial render and perceived performance.

**Accessibility by design**  
Semantic HTML, proper contrast, keyboard navigation, reduced motion support.

**GitHub Pages compatible**  
Static files. No build step. Works everywhere.

## Architecture

```
/
  index.html          # Entry point and navigation
  styles/
    reset.css         # Baseline normalization
    system.css        # Design tokens and variables
    layout.css        # Grid and spatial systems
    components.css    # Reusable interface elements
  examples/
    hud-interface/
    data-table/
    command-palette/
```

## Design Tokens

Color, spacing, typography, and timing defined as CSS custom properties.  
Consistency through constraint, not convention.

## Running Locally

```bash
python3 -m http.server 8000
```

Visit `http://localhost:8000`

## Deployment

Push to GitHub. Enable GitHub Pages. Done.

---

Interface architecture is about making system constraints visible and working within them.
