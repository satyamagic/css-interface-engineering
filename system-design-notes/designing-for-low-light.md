# Designing for Low-Light

## Problem Definition

Interfaces used in dark environments face specific challenges:
- Standard contrast ratios insufficient
- Bright whites cause eye strain and glare
- Color perception changes in scotopic vision
- Screen brightness affects battery life and heat
- Surrounding darkness reduces visual context

Users in:
- Nighttime work environments
- Bedrooms
- Vehicles at night
- Dimmed offices
- Photography/video editing suites

Standard light-mode interfaces become hostile. Dark modes often fail by simply inverting colors.

## Core Principle

**Design for reduced luminance while maintaining information hierarchy and readability.**

Not just "dark background." Careful color selection, contrast management, and reduced cognitive load.

## Luminance Management

### Background Colors

**Avoid pure black (#000000):**
- Causes halation (glow) around bright elements
- Increases perceived contrast to uncomfortable levels
- Reduces depth perception

**Use dark grays (#0a0a0a to #1a1a1a):**
- Maintains depth layering
- Reduces eye strain
- Enables subtle elevation shadows

### Foreground Colors

**Avoid pure white (#ffffff):**
- Too bright in dark environments
- Causes afterimages
- Difficult to read extended text

**Use off-whites (#e8e8e8 to #f0f0f0):**
- Sufficient contrast (12:1 to 15:1 on dark backgrounds)
- Reduced glare
- Better for extended reading

### Luminance Hierarchy

```
Background:   #0a0a0a (L*:  1)
Surface:      #1a1a1a (L*:  6)
Border:       #2a2a2a (L*: 12)
Text-subtle:  #707070 (L*: 47)
Text-normal:  #a0a0a0 (L*: 65)
Text-primary: #e8e8e8 (L*: 91)
```

Consistent luminance steps maintain hierarchy in darkness.

## Color Perception in Low Light

### Purkinje Shift

Human vision shifts toward blue sensitivity in low light. Colors appear different than in bright conditions.

**Implications:**
- Blues appear brighter/more prominent
- Reds appear darker/less distinct
- Yellow-green remains relatively consistent

### Color Selection

**Accent colors for dark mode:**
- Cyan/aqua: High visibility, low strain
- Green: Good for success states, readable
- Yellow/amber: Effective for warnings
- Red: Reduce saturation (use #ff4444 not #ff0000)

**Avoid:**
- Highly saturated primaries (overwhelming)
- Pure complementaries (vibration effects)
- Low-saturation colors near background luminance (invisible)

## Contrast Ratios

### WCAG in Dark Mode

Standard ratios still apply, but implementation differs:

**Light mode:** #000000 on #ffffff = 21:1  
**Dark mode:** #e8e8e8 on #0a0a0a = 15:1  

Both exceed AAA requirements (7:1), but dark mode is more comfortable.

### Practical Targets

**Critical text:** 15:1 to 18:1  
**Body text:** 12:1 to 15:1  
**Secondary text:** 7:1 to 10:1  
**Disabled/subtle:** 4.5:1 to 6:1  

Higher than WCAG minimums. Comfortable reading requires it.

## Typography Adjustments

### Font Weight

Dark backgrounds with light text require slight weight increase:

```css
@media (prefers-color-scheme: dark) {
  body {
    font-weight: 450; /* Increase from 400 */
  }
  
  strong {
    font-weight: 650; /* Increase from 600 */
  }
}
```

Light text on dark backgrounds appears thinner due to optical effects.

### Letter Spacing

Slight increase improves readability in low light:

```css
body {
  letter-spacing: 0.01em; /* Subtle increase */
}
```

### Line Height

Increase for extended reading:

```css
body {
  line-height: 1.7; /* From 1.5 in light mode */
}
```

## Layering and Depth

### Elevation System

Use subtle background lightness increases instead of shadows:

```
Base:        #0a0a0a
Elevated 1:  #1a1a1a (+10 lightness)
Elevated 2:  #2a2a2a (+10 lightness)
Elevated 3:  #3a3a3a (+10 lightness)
```

Shadows blend into dark backgrounds. Lightness creates clear hierarchy.

### Borders

Essential in dark mode. Without them, elements merge:

```css
.card {
  background: #1a1a1a;
  border: 1px solid #2a2a2a; /* Subtle but necessary */
}
```

## Motion and Animation

### Reduced Brightness Changes

Avoid flashing or rapid brightness transitions:
- Cause discomfort in dark environments
- Can trigger photosensitivity
- Disruptive to dark-adapted vision

### Transition Timing

Slightly slower in dark mode:

```css
@media (prefers-color-scheme: dark) {
  * {
    transition-duration: 300ms; /* 250ms in light mode */
  }
}
```

Gives eyes time to adjust to changes.

## Implementation

### System Preference Detection

```css
@media (prefers-color-scheme: dark) {
  :root {
    --bg-primary: #0a0a0a;
    --text-primary: #e8e8e8;
    --accent: #00d9ff;
  }
}
```

### Manual Override

Allow users to choose regardless of system setting:

```javascript
// Store preference
localStorage.setItem('theme', 'dark');

// Apply
document.documentElement.setAttribute('data-theme', 'dark');
```

```css
[data-theme="dark"] {
  --bg-primary: #0a0a0a;
  --text-primary: #e8e8e8;
}
```

### Smooth Transitions

```css
:root {
  transition: background-color 0.3s, color 0.3s;
}
```

Prevent jarring switches between modes.

## Special Considerations

### Images and Media

**Reduce brightness:**
```css
@media (prefers-color-scheme: dark) {
  img {
    opacity: 0.85;
  }
}
```

**Code blocks:**
Use appropriate syntax theme. Light code on dark is common but verify contrast.

**Charts and graphs:**
Invert colors. Verify all data remains distinguishable.

## Testing

Test in actual low-light conditions:
- Dimmed room with screen only light source
- Various ambient light levels
- Extended reading sessions (30+ minutes)
- Different screen brightness settings

Simulator testing insufficient. Human perception in darkness differs from simulated conditions.

## Anti-Patterns

**Pure black backgrounds:** Causes halation  
**Pure white text:** Too bright, causes glare  
**Inverted light mode:** Color relationships break  
**Insufficient contrast:** Hierarchy collapses  
**Bright accent overuse:** Distracting, uncomfortable  

## Design Checklist

- [ ] Background is dark gray, not pure black
- [ ] Text is off-white, not pure white
- [ ] Contrast exceeds 12:1 for body text
- [ ] Accent colors adjusted for dark mode
- [ ] Typography weight slightly increased
- [ ] Borders present on all components
- [ ] Elevation uses lightness, not shadows
- [ ] System preference detection implemented
- [ ] Manual override available
- [ ] Tested in actual dark environment

---

**Key insight:** Dark mode isn't light mode with inverted colors. It's a different design system optimized for low-luminance environments.

Design with actual darkness, not just dark hex codes.
