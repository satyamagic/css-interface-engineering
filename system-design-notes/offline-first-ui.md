# Offline-First UI

## Problem Definition

Network connectivity is unreliable. Interfaces designed assuming constant connectivity fail when that assumption breaks.

Users in:
- Tunnels and subways
- Rural areas
- Congested networks
- Bandwidth-constrained environments
- International travel

Traditional client-server architecture becomes client-wait-for-server architecture.

## Core Principle

**Design interfaces that work without a network, then add sync as an enhancement.**

Not "graceful degradation" where features disappear. Primary functionality remains available offline.

## State Management

### Local-First Data
```
User action → Local database → Background sync
```

Not:
```
User action → HTTP request → Wait → Update UI
```

### Sync Strategy

**Optimistic updates:**
- Write to local storage immediately
- Update UI instantly
- Queue sync operation
- Handle conflicts on reconciliation

**Conflict resolution:**
- Last-write-wins (simple, lossy)
- Operational transforms (complex, correct)
- Manual resolution (user-driven)

Choose based on data criticality and conflict likelihood.

## Interface Patterns

### Sync Status Indicator
```
┌─────────────────────────┐
│ [●] Synced             │  Clear state
│ [○] Syncing...         │  In progress
│ [!] Sync pending       │  Queued changes
│ [✕] Sync failed        │  User action needed
└─────────────────────────┘
```

Position: Persistent but unobtrusive. Top bar or bottom corner.

### Action Feedback

Immediate: "Saved locally"  
Delayed: "Synced to server"

Users need confirmation their action succeeded. Don't wait for network confirmation to provide feedback.

### Queue Visualization

Show pending operations when queue is non-empty:
```
3 changes pending sync
- Updated document title
- Added comment
- Changed status
```

Transparency builds trust. Users understand system state.

## Technical Implementation

### Service Workers

```javascript
// Cache-first strategy for static assets
// Network-first with cache fallback for API
```

### IndexedDB

Local database for application state. Survives page refresh and browser restart.

### Background Sync API

Retry failed requests when connectivity returns. Handle sync in background even if tab closed.

## Failure Modes

### Permanent Offline
Accept it. Interface remains functional. Clearly communicate sync will happen when connection available.

### Sync Conflicts
Surface to user when automatic resolution impossible. Provide clear options and context.

### Storage Quota
Monitor usage. Warn before limits. Provide clear data management options.

## Design Constraints

**Storage limits:** ~50MB typical, varies by browser  
**Sync timing:** Unpredictable, depends on network  
**Conflict probability:** Increases with offline duration  

## Real-World Examples

**Google Docs:** Local edits, background sync, conflict resolution  
**Linear:** Offline-first, optimistic updates, clear sync status  
**Figma:** Operational transforms, real-time or offline  

## Anti-Patterns

**"Network error" modal blocking all interaction**  
Better: Continue working, show sync status in corner

**Disabled buttons when offline**  
Better: Enable with "saved locally" feedback

**Infinite spinners waiting for network**  
Better: Instant local response, background sync

## Implementation Checklist

- [ ] Local storage for application state
- [ ] Service worker for asset caching
- [ ] Optimistic UI updates
- [ ] Sync queue with retry logic
- [ ] Clear sync status indicator
- [ ] Conflict resolution strategy
- [ ] Storage quota monitoring
- [ ] Offline capability testing

## Testing

Test with:
- Chrome DevTools offline mode
- Network throttling (slow 3G)
- Airplane mode on mobile devices
- Interrupted connections mid-sync

Offline isn't just "no connection." It's intermittent, slow, and unreliable connections.

---

**Key insight:** Users don't care about your backend architecture. They care that their work doesn't disappear when the network fails.

Design for that reality.
