# Failure-Aware Interfaces

## Problem Definition

Systems fail. Networks timeout. APIs return errors. Databases become unavailable. Services degrade.

Traditional interfaces hide failure or treat it as exceptional. Users encounter:
- Infinite loading spinners
- Generic "something went wrong" messages
- Disabled features with no explanation
- Lost work with no recovery path

**Failure is not exceptional. It is inevitable.**

## Core Principle

**Design interfaces that expect failure and communicate it clearly, preserving user context and enabling recovery.**

Not hiding failure. Not pretending everything is fine. Honest communication about system state.

## Failure Classifications

### Temporary vs Permanent

**Temporary (retry-able):**
- Network timeouts
- Rate limiting
- Service overload
- Connection interruption

**Permanent (require intervention):**
- Authentication failures
- Permission errors
- Invalid data
- Resource not found

Interface should distinguish and handle differently.

### User-Caused vs System-Caused

**User-caused:**
- Invalid input
- Missing required fields
- Unauthorized action
- Quota exceeded

**System-caused:**
- Server errors
- Database failures
- Third-party service outages
- Configuration errors

User-caused: Provide clear correction path.  
System-caused: Acknowledge issue, communicate timeline.

## Failure State Design

### Information Requirements

Every failure state must communicate:

1. **What failed:** Specific component/action, not generic "error"
2. **Why it failed:** Root cause when knowable
3. **Impact:** What functionality is affected
4. **Action:** What user can/should do
5. **Status:** Is system retrying? When will it retry?

### Visual Hierarchy

```
┌─────────────────────────────────────┐
│  [!] Failed to save document        │  Clear, specific
│                                     │
│  Network connection lost            │  Root cause
│                                     │
│  Your changes are saved locally     │  Impact/mitigation
│  and will sync when connection      │
│  returns.                           │
│                                     │
│  [ Retry Now ]  [ Work Offline ]    │  Clear actions
└─────────────────────────────────────┘
```

Not: "Error: 500 Internal Server Error"

### Error Severity Levels

**Critical (blocks all work):**
- Full-screen modal
- Cannot be dismissed until resolved
- Clear escalation path

**Major (blocks specific feature):**
- Inline in affected area
- Dismissible with acknowledgment
- Feature remains visible but disabled

**Minor (doesn't block work):**
- Toast/banner notification
- Auto-dismissible or persistent until resolved
- Work continues unaffected

## Retry Strategies

### Automatic Retry

Appropriate for:
- Network timeouts
- Rate limit errors (with backoff)
- Temporary service unavailability

```
Retry 1: 1 second delay
Retry 2: 2 second delay
Retry 3: 4 second delay
Retry 4: 8 second delay
Max retries: 4
```

Exponential backoff prevents retry storms.

### User-Initiated Retry

Appropriate for:
- Ambiguous failures
- Long-duration outages
- User permission errors

Provide clear "Retry" button with feedback:
```
[Retrying...]  →  [Retry successful] / [Retry failed - try again later]
```

### Background Retry

Appropriate for:
- Non-critical operations
- Batch operations
- Offline-first architecture

Show retry queue status:
```
3 operations pending
Retrying in 30 seconds...
```

## State Preservation

### Prevent Data Loss

**Before action fails:**
- Save user input to local storage
- Preserve form state
- Cache incomplete work

**After action fails:**
- Restore previous state
- Maintain user context
- Enable continuation from failure point

### Implementation

```javascript
// Save before network call
localStorage.setItem('draft', formData);

// Restore on page load
const draft = localStorage.getItem('draft');
if (draft) {
  // Show restore prompt
}
```

## Partial Degradation

### Feature Isolation

When service X fails, features A, B, C should continue working.

Not: "Service unavailable" blocking entire application.  
Instead: "Comments currently unavailable. Document editing works normally."

### Progressive Enhancement

Design core functionality to work without optional dependencies:

```
Core: Editing, saving, basic formatting → Always works
Enhanced: Collaboration, comments, AI → Fails gracefully
```

Users can accomplish primary task even when enhancements fail.

## Communication Patterns

### Loading States

**Specific, not generic:**
- Bad: "Loading..."
- Good: "Saving document..."

**Time-bounded:**
- Under 2 seconds: Spinner, no text
- 2-10 seconds: Spinner with text explaining action
- Over 10 seconds: Progress indicator with estimate if possible

**Timeout handling:**
- After 30 seconds: "This is taking longer than expected. Still working..."
- After 60 seconds: Offer cancel or continue options

### Error Messages

**User-facing language:**
- Bad: "ERR_CONNECTION_REFUSED"
- Good: "Could not connect to server. Check your internet connection."

**Actionable:**
- Bad: "Operation failed."
- Good: "Could not upload file. Check file size (max 10MB) and try again."

**Honest:**
- Bad: "Please try again later." (when issue is permanent)
- Good: "Your account doesn't have permission for this action. Contact your administrator."

## Feedback Loops

### Success Confirmation

After retry succeeds:
```
✓ Connection restored. All changes synced.
```

Users need closure. Don't silently recover from failure.

### Persistent Issues

After multiple failures:
```
Unable to connect after 5 attempts.
This might be a larger issue. [Report Problem]
```

Escalation path when automatic recovery fails.

## Testing Failure States

### Simulation

Must be testable without breaking production:

```javascript
// Feature flag for failure injection
if (window.SIMULATE_NETWORK_ERROR) {
  throw new Error('Simulated network failure');
}
```

### Failure Scenarios

Test matrix:
- Network disconnection mid-operation
- Slow network (3G simulation)
- Timeout before response
- 500 server error
- 403 permission error
- 404 resource not found
- Rate limit exceeded
- Partial response (connection drops mid-stream)

Each should have designed response.

## Real-World Examples

**Gmail:** Offline mode, drafts saved locally, clear sync status  
**Figma:** Continues working offline, shows who's disconnected, auto-reconnect  
**Linear:** Optimistic updates, background sync, clear error states  

## Anti-Patterns

**Infinite loading spinner:** User abandoned, no recovery  
**Generic error modal:** "Something went wrong" - unhelpful  
**Silent failure:** Action appears successful but didn't complete  
**Blocking entire app:** One service failure breaks everything  
**No retry mechanism:** User stuck with no path forward  
**Lost user input:** Form data disappears on error  

## Implementation Checklist

- [ ] All async operations have timeout handling
- [ ] Retry logic with exponential backoff
- [ ] User input preserved on failure
- [ ] Specific error messages, not generic
- [ ] Clear user action for each error type
- [ ] Partial degradation for service failures
- [ ] Success confirmation after recovery
- [ ] Failure state testing in development
- [ ] Error logging for debugging
- [ ] Escalation path for persistent issues

## Monitoring

Track failure patterns:
- Failure frequency by type
- Time to recovery
- User retry attempts
- Escalation rate

High failure rates indicate system issues requiring architecture changes, not just better error messages.

---

**Key insight:** Failure is a feature, not a bug. Design it with the same care as success states.

Users judge systems by how they handle failure, not how they perform when everything works.
