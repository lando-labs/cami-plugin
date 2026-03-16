# Semantic Versioning Decision Guide

This reference helps determine the correct version bump for a sprint.

## Quick Decision Tree

```
For each issue in the sprint, ask:

1. Does this change break existing API contracts?
   YES -> MAJOR
   NO  -> Continue

2. Does this remove a feature users depend on?
   YES -> MAJOR
   NO  -> Continue

3. Does this require users to migrate data/config?
   YES -> MAJOR (usually)
   NO  -> Continue

4. Does this add new user-facing functionality?
   YES -> MINOR
   NO  -> Continue

5. Does this add new API endpoints?
   YES -> MINOR
   NO  -> Continue

6. Does this significantly enhance existing features?
   YES -> MINOR (probably)
   NO  -> PATCH
```

## Version Impact by Change Type

### Definitely MAJOR (X.0.0)

| Change | Example | Why MAJOR |
|--------|---------|-----------|
| API endpoint removal | DELETE /api/v1/users/legacy | Breaking for clients |
| Request/response schema change | user.name -> user.fullName | Breaking for clients |
| Database migration (breaking) | Rename column, change type | Data compatibility |
| Auth flow change | Session -> JWT | All clients must update |
| Minimum Node/Python version | Node 16 -> Node 20 | Deployment breaking |
| Feature removal | Remove export to CSV | Users lose capability |

### Definitely MINOR (0.X.0)

| Change | Example | Why MINOR |
|--------|---------|-----------|
| New feature | Add dark mode | New capability |
| New API endpoint | POST /api/v1/reports | Extends API |
| New integration | Add Slack notifications | New capability |
| New page/view | Add analytics dashboard | New capability |
| Significant enhancement | Add bulk operations | Major UX improvement |
| New configuration options | Add webhook URL setting | New capability |

### Definitely PATCH (0.0.X)

| Change | Example | Why PATCH |
|--------|---------|-----------|
| Bug fix | Fix null pointer on empty list | Corrects behavior |
| Security fix | Patch XSS vulnerability | Corrects behavior |
| Performance improvement | Add caching, optimize query | Same behavior, faster |
| Typo fix | "Sucessful" -> "Successful" | Cosmetic |
| Dependency update (minor) | lodash 4.17.20 -> 4.17.21 | No behavior change |
| Documentation | Update README | No code change |
| Test additions | Add missing unit tests | No behavior change |

## Gray Area Decisions

### "Significant Enhancement" - MINOR or PATCH?

Ask: **Does this change how users think about the feature?**

```
MINOR examples (changes mental model):
- Search can now filter by date range (new capability)
- List view now supports sorting (new capability)
- Export now includes all fields (significantly more useful)

PATCH examples (same mental model):
- Search is now faster (same capability)
- List view loads more smoothly (same capability)
- Export file is smaller (same output)
```

### "UI Redesign" - MAJOR or MINOR?

Ask: **Do users need to re-learn how to use it?**

```
MAJOR (re-learning required):
- Navigation structure completely changed
- Core workflows reorganized
- Key features moved or renamed

MINOR (fresher look, same usage):
- New color scheme
- Updated typography
- Modernized buttons/inputs
- Same layout, better styling
```

### "API Changes" - MAJOR or MINOR?

Ask: **Will existing clients break?**

```
MAJOR (clients break):
- Endpoint path changed
- Required field added
- Response structure changed
- Authentication method changed

MINOR (clients work fine):
- New optional field added
- New endpoint added
- New optional parameter
- New response fields (additive)
```

### "Database Changes" - MAJOR or PATCH?

Ask: **Is the migration reversible without data loss?**

```
MAJOR (risky migration):
- Column renamed
- Column type changed (int -> string)
- Table split or merged
- Required column added without default

PATCH (safe migration):
- New nullable column
- New table
- Index added
- Default added to existing column
```

## Sprint Version Aggregation

When a sprint has mixed changes, use the HIGHEST impact:

```
Sprint contains:
- #12: Fix login bug (PATCH)
- #15: Add remember me (MINOR)
- #18: Fix error messages (PATCH)

Sprint target: MINOR (v1.2.0 -> v1.3.0)
Reason: #15 adds new functionality
```

```
Sprint contains:
- #20: Add OAuth support (MINOR)
- #22: Remove legacy auth (MAJOR)
- #24: Fix token refresh (PATCH)

Sprint target: MAJOR (v1.3.0 -> v2.0.0)
Reason: #22 removes existing functionality
```

## Pre-1.0.0 Considerations

For projects in initial development (v0.x.x):

```
v0.x.y rules are relaxed:
- MINOR (0.X.0) can include breaking changes
- PATCH (0.0.X) for non-breaking changes
- Once you hit v1.0.0, full semver rules apply

When to go to v1.0.0:
- API is stable
- Production users exist
- Breaking changes become costly
```

## Version Declaration Template

Include in sprint manifest:

```markdown
## Version Impact

**Current Version**: v1.2.0
**Target Version**: v1.3.0
**Bump Type**: MINOR

**Reasoning**:
- #15 adds "remember me" functionality (new feature)
- Other issues are bug fixes (PATCH-level)
- Highest impact is MINOR, so target is v1.3.0

**Breaking Changes**: None
**Migration Required**: None
```

## Common Mistakes

### Mistake 1: Calling Everything a PATCH

```
Wrong: "It's just a small feature, call it PATCH"
Right: New features are MINOR, regardless of size

Small feature = MINOR
Large bug fix = PATCH
Size doesn't determine version type
```

### Mistake 2: Forgetting Dependency Updates

```
Dependency update types:
- Major dep bump (lodash 4 -> 5) = evaluate your MAJOR
- Minor dep bump (lodash 4.17 -> 4.18) = your MINOR or PATCH
- Patch dep bump (lodash 4.17.20 -> 4.17.21) = your PATCH
```

### Mistake 3: Underestimating "Internal" Changes

```
"Internal" changes that might be MAJOR:
- Changing how data is stored (even if API same)
- Changing default behaviors
- Changing error codes/messages (if clients parse them)
```

### Mistake 4: Scope Creep Mid-Sprint

```
Started as PATCH sprint, but:
- Found opportunity for enhancement -> Now MINOR
- Needed to break API for proper fix -> Now MAJOR

Action: Stop. Re-plan. Don't quietly change version impact.
```

## Reference Links

- [Semantic Versioning Spec](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)
- [Conventional Commits](https://www.conventionalcommits.org/)
