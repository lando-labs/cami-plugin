# Issue Grouping Patterns

This reference provides concrete patterns for grouping issues into focused sprints.

## Grouping Principles

1. **Cohesion over convenience** - Issues should belong together, not just fit together
2. **Context switching is expensive** - Group to minimize mental context switches
3. **Completable units** - Each group should be achievable in 1-2 sessions
4. **Clear boundaries** - Obvious where one sprint ends and another begins

## Pattern 1: Page/Feature Focus

**When to use**: Multiple issues affect the same user-facing area

**Structure**:
```
Focus: [Page/Feature Name]
Theme: Improvements to a specific user touchpoint

Issues:
- Bug affecting this page
- Enhancement for this page
- Related UX improvement
```

**Example - User Profile Page**:
```
Focus: User Profile Page
Theme: Complete overhaul of profile experience

#12: Fix avatar upload failing on large images
#15: Add bio field to profile
#18: Improve profile loading skeleton
#22: Add profile completion indicator
```

**Example - Checkout Flow**:
```
Focus: Checkout Flow
Theme: Reduce cart abandonment

#30: Fix payment error not displaying
#31: Add Apple Pay support
#32: Improve address autocomplete
#35: Fix coupon code case sensitivity
```

**Why this works**: Developer stays in same codebase area, understands full context, can make holistic improvements.

## Pattern 2: Discipline Focus

**When to use**: Issues span features but share a technical concern

**Structure**:
```
Focus: [Discipline Name]
Theme: Cross-cutting technical improvement

Issues:
- All related to same technical concern
- Span multiple features
- Require similar expertise
```

**Example - Security Hardening**:
```
Focus: Security Hardening
Theme: Address OWASP Top 10 findings

#40: Implement CSRF tokens on all forms
#41: Add rate limiting to API endpoints
#42: Sanitize user input in search
#45: Audit and rotate API keys
#48: Add Content-Security-Policy headers
```

**Example - Accessibility Compliance**:
```
Focus: Accessibility (WCAG 2.1 AA)
Theme: Pass accessibility audit

#50: Add ARIA labels to all form inputs
#51: Fix color contrast in sidebar
#52: Enable keyboard navigation in modals
#55: Add skip navigation link
#58: Fix focus indicators on buttons
```

**Example - Performance Optimization**:
```
Focus: Performance Optimization
Theme: Improve Core Web Vitals

#60: Implement lazy loading for images
#61: Add response caching for API
#62: Reduce JavaScript bundle size
#65: Optimize database queries for dashboard
```

**Why this works**: Similar technical skills needed, changes don't conflict, clear success criteria.

## Pattern 3: User Journey Focus

**When to use**: Issues affect steps in a user workflow

**Structure**:
```
Focus: [Journey Name]
Theme: End-to-end improvement of user flow

Issues:
- Span multiple pages but single flow
- User completes a goal
- Sequential or branching path
```

**Example - Onboarding Flow**:
```
Focus: New User Onboarding
Theme: Improve activation rate

#70: Simplify signup form (reduce fields)
#71: Add welcome email sequence trigger
#72: Fix tutorial tooltip positioning
#75: Add progress indicator to setup wizard
#78: Improve first-project creation UX
```

**Example - Purchase Journey**:
```
Focus: Purchase Journey
Theme: Increase conversion rate

#80: Add product recommendations on cart
#81: Streamline guest checkout
#82: Fix shipping calculator accuracy
#85: Add order confirmation email
#88: Improve mobile payment experience
```

**Why this works**: Holistic improvement to user goal completion, metrics easily measured.

## Pattern 4: Technical Debt Focus

**When to use**: Accumulated debt needs systematic addressing

**Structure**:
```
Focus: [Debt Category]
Theme: Reduce technical debt in specific area

Issues:
- Related refactoring tasks
- Test coverage gaps
- Documentation needs
```

**Example - Test Coverage**:
```
Focus: Test Coverage - Auth Module
Theme: Increase auth test coverage to 80%

#90: Add unit tests for password validation
#91: Add integration tests for login flow
#92: Add e2e tests for password reset
#95: Mock external auth providers in tests
```

**Example - Code Refactoring**:
```
Focus: Refactor Legacy API Layer
Theme: Modernize API patterns

#100: Extract API client to separate module
#101: Add TypeScript types to API responses
#102: Implement consistent error handling
#105: Add request/response logging
```

**Why this works**: Focused debt reduction, measurable progress, doesn't conflict with feature work.

## Pattern 5: Integration Focus

**When to use**: Adding or improving external service connections

**Structure**:
```
Focus: [Integration Name]
Theme: Complete integration implementation

Issues:
- Connection setup
- Feature implementation
- Error handling
- Testing
```

**Example - Stripe Integration**:
```
Focus: Stripe Payment Integration
Theme: Enable payment processing

#110: Set up Stripe SDK and credentials
#111: Implement checkout session creation
#112: Add webhook handler for events
#115: Handle payment failures gracefully
#118: Add Stripe test mode for development
```

**Example - Slack Integration**:
```
Focus: Slack Notification Integration
Theme: Enable team notifications

#120: Set up Slack app and OAuth
#121: Implement notification sending
#122: Add notification preferences UI
#125: Handle rate limits and retries
```

**Why this works**: Self-contained integration work, clear completion criteria, doesn't fragment across sprints.

## Anti-Patterns to Avoid

### Anti-Pattern 1: The Everything Sprint

```
BAD - "Sprint 5: Various Improvements"
#12: Fix login bug
#45: Add dark mode
#78: Improve database performance
#92: Update dependencies
#105: Redesign header

Problem: No cohesion, constant context switching
```

### Anti-Pattern 2: The Endless Sprint

```
BAD - "Sprint 6: Complete Feature X"
#200-#250: All issues for massive feature

Problem: Too large, will never feel complete, scope creep guaranteed
```

### Anti-Pattern 3: The Leftover Sprint

```
BAD - "Sprint 7: Remaining Issues"
#300: Old bug from 6 months ago
#302: Nice-to-have someone mentioned
#305: Something we meant to do
#308: Random idea from backlog

Problem: No theme, likely not actually important
```

### Anti-Pattern 4: The Micro Sprint

```
BAD - "Sprint 8: Fix Typo"
#400: Fix typo in button

Problem: Overhead exceeds value, batch these into larger sprints
```

## Sizing Guidelines

### Small Sprint (1 session, ~4 hours)
- 2-4 issues
- Single page/feature focus
- PATCH-level changes
- Low risk

```
Example: Fix bugs on Settings page
- #500: Fix timezone selector
- #501: Fix password change validation
- #502: Fix notification toggle state
```

### Medium Sprint (1-2 sessions, ~8 hours)
- 4-8 issues
- Feature or discipline focus
- MINOR-level changes
- Moderate risk

```
Example: Implement User Preferences
- #600: Add preferences data model
- #601: Create preferences API endpoints
- #602: Build preferences UI
- #605: Add preference sync across devices
- #608: Add preference export/import
```

### Large Sprint (2-3 sessions, ~12+ hours)
- 8+ issues
- Major feature or system
- MINOR or MAJOR level
- Higher risk, needs breakdown consideration

```
Example: OAuth Integration (consider splitting)
- #700: Research OAuth providers
- #701: Set up OAuth credentials
- #702: Implement OAuth login flow
- #705: Add OAuth account linking
- #708: Handle OAuth token refresh
- #710: Add OAuth disconnect feature
- #712: Update tests for OAuth
- #715: Documentation for OAuth setup
```

**Note**: Large sprints often benefit from being split into 2 medium sprints with a clear boundary.

## Grouping Checklist

Before finalizing a sprint's issue group:

- [ ] All issues share a clear theme
- [ ] Developer can stay in similar code areas
- [ ] Changes don't conflict with each other
- [ ] Sprint is completable in 1-2 sessions
- [ ] Success criteria is measurable
- [ ] Dependencies are documented
- [ ] Version impact is consistent (or highest is declared)

## Emergency Grouping

Sometimes you need to address urgent issues. Pattern:

```
Focus: Critical Hotfix - [Problem]
Theme: Emergency response

Rules:
- Maximum 2-3 issues
- All directly address the emergency
- No scope creep allowed
- PATCH version only
- Immediate PR and deploy
```

**Example**:
```
Focus: Critical Hotfix - Payment Processing Down
Theme: Restore payment capability

#999: Fix Stripe webhook signature validation
#1000: Add fallback payment error handling

Branch: hotfix/v1.5.1-payment-restore
Target: Immediate merge after tests pass
```
