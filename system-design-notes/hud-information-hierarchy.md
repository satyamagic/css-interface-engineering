# HUD Information Hierarchy

## Problem Definition

Heads-up displays present multiple data streams simultaneously. Users need to:
- Scan rapidly for critical information
- Maintain spatial awareness of data locations
- Process updates without cognitive overload
- Respond to anomalies immediately

Traditional web layout patterns fail. HUDs require spatial consistency and visual priority encoding.

## Core Principle

**Information location should be predictable. Visual weight should reflect data criticality.**

Not scrolling. Not modal dialogs. Not hidden menus. Everything visible, appropriately weighted.

## Visual Hierarchy

### Priority Levels

**P1 - Critical data requiring immediate attention:**
- Largest text size (36-48px)
- Highest contrast (7:1 minimum)
- Central or primary position
- Color encoding for state (error red, warning yellow, ok green)

**P2 - Important contextual information:**
- Medium text size (18-24px)
- Good contrast (4.5:1 minimum)
- Secondary zones near primary
- Neutral colors unless state requires attention

**P3 - Supporting metadata:**
- Small text size (12-14px)
- Lower contrast (3:1 minimum)
- Peripheral positions
- Muted colors

### Visual Weight Formula

```
Weight = Size × Contrast × Position × Motion
```

Most critical data should dominate all dimensions.

## Spatial Layout

### Grid Zones

```
┌─────────────┬─────────────┬─────────────┐
│   Status    │   Primary   │   Alerts    │
│             │   Metrics   │             │
├─────────────┴─────────────┴─────────────┤
│                                         │
│          Main Data Visualization        │
│                                         │
├─────────────┬─────────────┬─────────────┤
│   System    │  Secondary  │   Controls  │
│   Health    │   Metrics   │             │
└─────────────┴─────────────┴─────────────┘
```

**Fixed positions:** Critical data always in same location. Muscle memory for scanning.

**Consistent sizing:** Zones don't resize unless data requires it. Layout stability reduces cognitive load.

## Information Density

### Maximum Useful Density

Not "how much can we fit?" but "how much can users process in 2-3 seconds?"

Rule: Users should comprehend primary state in single glance (< 2 seconds).

### Density Calculation

```
Items per zone = Screen area / (Min readable size × Attention span)

Practical: 4-7 items per zone maximum
```

Beyond this, add hierarchy within zone or split zones.

## Motion and Updates

### Update Strategies

**Real-time critical data:**
- Smooth value transitions (avoid jumpy numbers)
- Change highlighting (brief color flash)
- Threshold crossing indicators

**Background data:**
- Batched updates (every 1-5 seconds)
- Subtle transitions
- No interruption of foreground attention

### Animation Constraints

Duration: 150-300ms for value changes  
Easing: ease-out for data arriving, ease-in for data leaving  
Frequency: Limit to reduce distraction

## Color Encoding

### Semantic Color Usage

**Red:** Error, critical threshold, immediate action required  
**Yellow/Orange:** Warning, approaching threshold, attention needed  
**Green:** Normal operation, within bounds, confirmation  
**Blue:** Neutral information, system state, passive data  
**White/Gray:** Non-semantic data, labels, static text  

**Critical:** Color alone is not enough. Use size, position, and shape redundantly.

## Typography

### Font Selection

Monospace for:
- Numeric data (alignment, digit width consistency)
- System logs
- Fixed-width tables

Sans-serif for:
- Labels and headings
- Body text
- Navigation

### Size Relationships

Maintain consistent ratio between levels:
```
P1: 48px
P2: 24px (0.5×)
P3: 12px (0.25×)
```

Clear differentiation. No ambiguous intermediate sizes.

## Failure State Display

### Error Hierarchy

**System-critical errors:** Full-screen modal (rare, blocks operation)  
**Feature errors:** Inline in affected zone, clear indication  
**Background errors:** Status bar indicator, details on demand  

### Error Information

Required:
- What failed (clear description)
- Impact (what's affected)
- Action (what user can do)
- Status (is it being retried?)

## Real-World Examples

**Aircraft cockpit displays:** Spatial consistency, color-coded alerts, hierarchical info  
**Terminal dashboards:** Fixed zones, real-time updates, high density  
**Medical monitors:** Critical values prominent, supporting data peripheral  

## Implementation Patterns

### CSS Grid for Layout

```css
.hud-container {
  display: grid;
  grid-template-areas:
    "status primary alerts"
    "main main main"
    "health metrics controls";
  grid-template-columns: 1fr 2fr 1fr;
  gap: 1px;
}
```

Fixed areas maintain spatial consistency.

### Custom Properties for Hierarchy

```css
:root {
  --text-critical: 48px;
  --text-important: 24px;
  --text-supporting: 12px;
  
  --color-critical: hsl(0, 100%, 60%);
  --color-normal: hsl(200, 100%, 60%);
}
```

Consistent application across components.

## Design Checklist

- [ ] Critical data < 2 second scan time
- [ ] Spatial positions fixed and predictable
- [ ] Visual hierarchy matches data priority
- [ ] Color used redundantly with size/position
- [ ] Updates don't disrupt user attention
- [ ] Failure states clearly communicated
- [ ] Typography optimized for scannability
- [ ] Contrast meets accessibility minimums
- [ ] Layout tested at target viewing distance

## Testing

Test with:
- Simulated data spikes (stress information density)
- Rapid update frequencies (verify readability)
- Error injection (confirm failure visibility)
- Various screen sizes (maintain hierarchy)
- Distance viewing (readability at 2-3 feet)

---

**Key insight:** HUDs are not websites. Users don't scroll, search, or explore. They scan, process, and act. Design accordingly.
