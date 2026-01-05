# Reduced-Motion Accessibility Fallback

## Problem

Design interfaces that adapt gracefully when users enable `prefers-reduced-motion`, maintaining functionality while respecting accessibility needs.

Demonstrate:
- Motion sensitivity awareness
- Alternative visual feedback
- Instant state transitions
- Progressive enhancement

## Constraints

- Must work identically with or without motion
- No functionality loss when motion disabled
- Instant transitions (≤10ms) for reduced motion
- Visual feedback through opacity/color instead of movement

## Technical Approach

**Media query:**
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

**Alternative feedback patterns:**
- Animation → Opacity change
- Transform movement → Border/background color
- Rotation → Scale or highlight
- Parallax → Static layering

## Design Decisions

**Why 0.01ms instead of 0ms?**  
Some browsers treat 0ms as "no transition defined" and apply defaults. 0.01ms forces instant transition while maintaining the transition property.

**Why global wildcard selector?**  
Ensures no animation escapes detection. Specificity issues can be handled with `!important`.

**Why provide alternatives instead of just removing?**  
Animations convey state changes. Removing them entirely loses information. Alternative visual feedback preserves communication.

## Implementation Examples

**Standard approach:**
```css
.button {
  transition: transform 0.2s ease-out;
}

.button:hover {
  transform: translateY(-2px);
}
```

**Reduced-motion alternative:**
```css
@media (prefers-reduced-motion: reduce) {
  .button {
    transition: background-color 0.01ms;
  }
  
  .button:hover {
    transform: none;
    background-color: var(--hover-color);
  }
}
```

## Testing

Enable reduced motion in OS settings:
- macOS: System Preferences → Accessibility → Display → Reduce motion
- Windows: Settings → Ease of Access → Display → Show animations
- Linux: Varies by desktop environment

Verify all interactive elements provide clear feedback without motion.

## Accessibility Impact

Benefits users with:
- Vestibular disorders
- Motion sensitivity
- Cognitive processing differences
- Low-performance devices

WCAG 2.1 Success Criterion 2.3.3 (AAA): Animation from Interactions

## Limitations

Complete motion removal may reduce perceived polish. Balance accessibility with design intent by choosing appropriate static alternatives.

Some users may want selective motion. No browser currently supports granular motion preferences beyond binary on/off.
