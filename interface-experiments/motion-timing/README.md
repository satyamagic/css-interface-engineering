# Interaction Easing and Motion Timing

## Problem

Explore how easing curves affect perceived interface responsiveness and the relationship between timing functions and physical motion principles.

Demonstrate:
- Standard cubic-bezier curves
- Visual comparison of easing effects
- Timing sensitivity in user interactions
- Performance considerations

## Constraints

- Standard CSS easing functions only
- No custom cubic-bezier beyond standard set
- 60fps animation target
- GPU-accelerated properties (transform, opacity)

## Technical Approach

**Standard easing functions:**
```css
ease-in: cubic-bezier(0.42, 0, 1, 1)
ease-out: cubic-bezier(0, 0, 0.58, 1)
ease-in-out: cubic-bezier(0.42, 0, 0.58, 1)
linear: cubic-bezier(0, 0, 1, 1)
```

**Animation test cases:**
- Modal entry/exit
- Dropdown expansion
- Button feedback
- Scroll indicators

**Performance strategy:**
```css
.animated {
  transform: translateX(0);
  will-change: transform;
}
```

## Design Decisions

**Why standard easings?**  
They represent tested, physical motion curves. Custom easings often deviate from natural motion perception.

**Why compare side-by-side?**  
Relative comparison reveals subtle differences that absolute observation misses.

**Why transform over position?**  
Transform operations use GPU compositing. Position changes trigger layout recalculation.

## Physical Motion Principles

**Ease-in:** Acceleration from rest (falling object)  
**Ease-out:** Deceleration to rest (friction stop)  
**Ease-in-out:** Natural motion cycle (pendulum)  
**Linear:** Mechanical, non-physical (useful for opacity)

## Performance

- Transform animations: GPU-accelerated, ~16ms budget
- Opacity changes: GPU-accelerated, minimal cost
- Layout properties: CPU-bound, avoid in animations

## Timing Recommendations

| Interaction | Duration | Easing | Rationale |
|------------|----------|--------|-----------|
| Micro-interaction | 100-200ms | ease-out | Quick feedback |
| Modal/Dialog | 200-300ms | ease-out | Entering view |
| Exit animation | 150-250ms | ease-in | Leaving view |
| Loading indicator | 400-600ms | ease-in-out | Continuous loop |

## Limitations

Timing perception is subjective. These recommendations assume typical user expectations and should be adjusted based on context and user testing.

High-frequency animations may cause motion sensitivity. Always provide reduced-motion alternatives.
