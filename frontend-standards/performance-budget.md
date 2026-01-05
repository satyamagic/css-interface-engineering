# Performance Budget

## Problem

Performance degrades incrementally. Each feature adds a bit more JavaScript. Each image slightly larger. Each dependency introduces more code.

Without explicit limits, applications become slow. Users abandon interfaces that don't respond quickly.

**Need:** Quantified performance targets enforced throughout development.

## Core Principle

**Define maximum acceptable costs before building, then enforce them.**

Not "optimize later." Constraints during development prevent performance debt.

## Budget Categories

### 1. Network Transfer

**Critical resources (initial page load):**
- HTML: < 14KB (initial document)
- Critical CSS: < 20KB
- Critical JavaScript: < 50KB
- Total: < 100KB gzipped

**Why 100KB?** Loads in ~1 second on slow 3G (conservative baseline).

**Secondary resources (lazy loaded):**
- Images: < 200KB per image
- Fonts: < 100KB per family
- JavaScript bundles: < 250KB per bundle

### 2. Time Metrics

**Initial load:**
- First Contentful Paint (FCP): < 1.8s
- Largest Contentful Paint (LCP): < 2.5s
- Time to Interactive (TTI): < 3.8s

**Interaction:**
- First Input Delay (FID): < 100ms
- Cumulative Layout Shift (CLS): < 0.1

**Navigation:**
- Page transitions: < 300ms
- Route changes: < 500ms

These are Core Web Vitals thresholds (Google's "good" ratings).

### 3. JavaScript Budget

**Parse + compile time:**
- < 50ms on mid-range device
- < 200ms on low-end device

**Main thread blocking:**
- No single task > 50ms
- Total blocking time < 300ms

**Bundle sizes:**
- Vendor bundle: < 150KB gzipped
- Application bundle: < 100KB gzipped
- Per-route lazy chunks: < 50KB gzipped

### 4. Asset Budget

**Images:**
- Hero images: < 200KB
- Thumbnails: < 50KB
- Icons: Use SVG or icon font

**Video:**
- Autoplaying video: < 1MB
- User-initiated video: < 5MB initial segment

**Fonts:**
- Max 2 font families
- Max 4 weights per family
- Subset character ranges when possible

## Measurement

### Local Development

**Lighthouse CLI:**
```bash
lighthouse https://localhost:3000 \
  --only-categories=performance \
  --throttling.cpuSlowdownMultiplier=4 \
  --output=json \
  --output-path=./lighthouse-results.json
```

Run on every significant change.

**Bundle analyzer:**
```bash
webpack-bundle-analyzer stats.json
```

Visualize what's in bundles. Identify unexpected bloat.

### CI/CD Integration

Fail builds that exceed budgets:

```json
// lighthouserc.json
{
  "ci": {
    "assert": {
      "assertions": {
        "first-contentful-paint": ["error", {"maxNumericValue": 1800}],
        "largest-contentful-paint": ["error", {"maxNumericValue": 2500}],
        "interactive": ["error", {"maxNumericValue": 3800}],
        "total-byte-weight": ["error", {"maxNumericValue": 102400}]
      }
    }
  }
}
```

### Real User Monitoring

Synthetic tests don't capture real conditions. Monitor production:

```javascript
// Performance Observer API
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    // Send to analytics
    analytics.track('performance_metric', {
      name: entry.name,
      value: entry.value,
      rating: entry.rating
    });
  }
});

observer.observe({entryTypes: ['largest-contentful-paint', 'first-input']});
```

Track P75 and P95 percentiles, not just median.

## Optimization Strategies

### Code Splitting

**Route-based:**
```javascript
const Dashboard = lazy(() => import('./Dashboard'));
const Profile = lazy(() => import('./Profile'));
```

Load only what's needed for current route.

**Component-based:**
```javascript
const HeavyChart = lazy(() => import('./HeavyChart'));

function Analytics() {
  return (
    <Suspense fallback={<Skeleton />}>
      <HeavyChart data={data} />
    </Suspense>
  );
}
```

Defer non-critical components.

### Image Optimization

**Responsive images:**
```html
<img
  srcset="small.jpg 480w, medium.jpg 768w, large.jpg 1200w"
  sizes="(max-width: 768px) 100vw, 50vw"
  src="medium.jpg"
  alt="Description"
/>
```

**Modern formats:**
```html
<picture>
  <source srcset="image.avif" type="image/avif" />
  <source srcset="image.webp" type="image/webp" />
  <img src="image.jpg" alt="Description" />
</picture>
```

AVIF > WebP > JPEG in efficiency.

**Lazy loading:**
```html
<img src="image.jpg" loading="lazy" alt="Description" />
```

Native browser lazy loading. No JavaScript needed.

### CSS Optimization

**Critical CSS extraction:**
```html
<style>
  /* Inline above-the-fold styles */
  :root { --color-bg: #0a0a0a; }
  body { background: var(--color-bg); }
  .header { /* ... */ }
</style>
<link rel="stylesheet" href="full.css" media="print" onload="this.media='all'" />
```

**Remove unused CSS:**
```javascript
// PostCSS with PurgeCSS
module.exports = {
  plugins: [
    require('@fullhuman/postcss-purgecss')({
      content: ['./src/**/*.html', './src/**/*.js']
    })
  ]
};
```

### JavaScript Optimization

**Tree shaking:**
```javascript
// Import only what's needed
import { debounce } from 'lodash-es';  // Good
import _ from 'lodash';  // Bad (imports everything)
```

**Minification:**
Use Terser or esbuild. Enable all safe transformations.

**Compression:**
Serve with Brotli (better than gzip):
```
Content-Encoding: br
```

## Budget Tracking

### Automated Reporting

Generate reports on every build:

```javascript
// webpack.config.js
module.exports = {
  performance: {
    maxAssetSize: 250000,  // 250KB
    maxEntrypointSize: 400000,  // 400KB
    hints: 'error'  // Fail build on violation
  }
};
```

### Dashboard

Track budgets over time:

```
┌────────────────────────────────────────┐
│ Performance Budget Status              │
├────────────────────────────────────────┤
│ ✓ HTML: 12KB / 14KB                   │
│ ✓ CSS: 18KB / 20KB                    │
│ ✗ JS: 65KB / 50KB (EXCEEDED)          │
│ ✓ Images: 145KB / 200KB               │
│ ✓ FCP: 1.6s / 1.8s                    │
│ ✗ LCP: 2.8s / 2.5s (EXCEEDED)         │
└────────────────────────────────────────┘
```

Visual indicator of budget health.

## When Budgets Are Exceeded

### Immediate Actions

1. **Identify culprit:**
   - Run bundle analyzer
   - Check recent changes
   - Profile runtime performance

2. **Quick wins:**
   - Remove unused dependencies
   - Lazy load non-critical features
   - Compress images

3. **Communicate:**
   - Block PR until fixed
   - Document why budget matters
   - Get team alignment

### Long-Term Solutions

**If legitimate need exceeds budget:**
- Reassess budget (is it too strict?)
- Optimize elsewhere to compensate
- Split feature across multiple loads

**If technical debt accumulated:**
- Schedule refactoring sprint
- Audit dependencies quarterly
- Review asset pipeline

## Budget Exemptions

### When to Increase Budget

**Valid reasons:**
- New critical feature with no lighter alternative
- Third-party requirement (payment, auth) with no substitute
- Accessibility enhancement (screen reader support)

**Invalid reasons:**
- "Just one more library"
- "It's only 50KB"
- "We'll optimize later"

### Process

1. Document why exemption needed
2. Get team approval
3. Update budget officially
4. Set reminder to revisit

Don't silently increase budgets.

## Device Targeting

### Low-End Device Baseline

**Test on:**
- Moto G4 or equivalent
- 4× CPU throttling (Chrome DevTools)
- Slow 3G network simulation

**Why?** Represents ~40% of global mobile users. If it works here, it works everywhere.

### Budget Tiers

**Tier 1 (critical):** Must work on low-end device  
**Tier 2 (enhanced):** Works on mid-range device  
**Tier 3 (premium):** Only on high-end device  

Core functionality always Tier 1.

## Real-World Example

### Before (No Budget)

```javascript
// Importing entire library
import _ from 'lodash';
import moment from 'moment';

// Unoptimized images
<img src="hero-5mb.jpg" />

// No code splitting
const App = () => (
  <>
    <Dashboard />
    <Analytics />
    <Reports />
  </>
);
```

**Result:**
- Bundle: 450KB gzipped
- FCP: 4.2s
- LCP: 6.8s

### After (With Budget)

```javascript
// Tree-shaking friendly imports
import debounce from 'lodash-es/debounce';
import { formatDate } from './date-utils';  // 2KB custom implementation

// Optimized images
<img
  srcset="hero-480w.webp 480w, hero-1200w.webp 1200w"
  src="hero-1200w.jpg"
  loading="lazy"
/>

// Code splitting
const Analytics = lazy(() => import('./Analytics'));
const Reports = lazy(() => import('./Reports'));

const App = () => (
  <>
    <Dashboard />
    <Suspense fallback={<Loading />}>
      <Analytics />
      <Reports />
    </Suspense>
  </>
);
```

**Result:**
- Initial bundle: 95KB gzipped
- FCP: 1.5s
- LCP: 2.2s

**Improvement:** 3× faster load time

## Checklist

- [ ] Performance budget defined and documented
- [ ] CI/CD integration blocks over-budget builds
- [ ] Lighthouse runs on every PR
- [ ] Bundle analyzer configured
- [ ] Real user monitoring in production
- [ ] Team trained on budget importance
- [ ] Regular budget reviews scheduled
- [ ] Low-end device testing routine

---

**Key insight:** Performance is a feature. Budget it like any other resource.

Users don't care about your architecture. They care that pages load fast and interactions feel instant.

Protect their experience with explicit constraints.
