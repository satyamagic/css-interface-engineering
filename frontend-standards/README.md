# Frontend Standards

Opinionated engineering guidelines for production interfaces.

## Purpose

Document technical decisions and architectural patterns for building maintainable, performant interfaces.

Not universal truths. Context-specific recommendations based on production constraints and real-world tradeoffs.

## Content

### CSS Architecture
Organizing styles for maintainability, performance, and team collaboration.

[Read guideline](css-architecture.md)

### Naming Conventions
Systematic approach to naming files, functions, variables, and classes for clarity.

[Read guideline](naming-conventions.md)

### Performance Budget
Defining and enforcing performance constraints throughout development.

[Read guideline](performance-budget.md)

### Accessibility Basics
Fundamental accessibility practices for inclusive interfaces.

[Read guideline](accessibility-basics.md)

## Approach

Each guideline explains:
- The problem being solved
- Why this approach over alternatives
- How to implement
- Exceptions and edge cases

Framework-agnostic. Principles apply regardless of tooling.

## Philosophy

**Consistency over cleverness**  
Predictable code beats optimized code that only one person understands.

**Constraints enable quality**  
Clear rules reduce decision fatigue and prevent architectural drift.

**Measure what matters**  
Metrics should reflect user experience, not arbitrary targets.

**Accessibility is not optional**  
If it's not accessible, it's not done.

---

Standards without reasoning are cargo cult. Understand the "why" before enforcing the "what".
