# Sprint PR Template

Use this template when creating pull requests for sprint branches.

---

## Sprint: [Focus Name]

**Target Version**: vX.Y.Z -> vX.Y.Z (MAJOR/MINOR/PATCH)
**Branch**: `sprint/vX.Y.Z-focus-slug`
**Sprint Manifest**: [Link to manifest if tracked]

### Summary

[1-2 sentence summary of what this sprint accomplishes for the user/product]

### Issues Resolved

- Fixes #XX: [Issue title]
- Fixes #XX: [Issue title]
- Fixes #XX: [Issue title]

### Changes by Area

#### [Area 1, e.g., "Authentication"]
- [Change description]
- [Change description]

#### [Area 2, e.g., "API"]
- [Change description]
- [Change description]

#### [Area 3, e.g., "Tests"]
- [Change description]

### Version Impact

| Aspect | Details |
|--------|---------|
| **Bump Type** | MAJOR / MINOR / PATCH |
| **Breaking Changes** | None / [List] |
| **Migration Required** | None / [Steps] |
| **New Dependencies** | None / [List with versions] |

### Testing

#### Automated
- [x] Unit tests pass
- [x] Integration tests pass
- [x] E2E tests pass (if applicable)
- [x] Test coverage maintained/improved

#### Manual Testing Performed
- [x] [Scenario 1]: [Expected behavior verified]
- [x] [Scenario 2]: [Expected behavior verified]
- [x] [Edge case]: [Handled correctly]

### Screenshots / Recordings

> If this sprint includes UI changes, include before/after screenshots or a screen recording

**Before:**
[Screenshot or "N/A"]

**After:**
[Screenshot or "N/A"]

### Checklist

#### Code Quality
- [ ] Code follows project style guide
- [ ] No unnecessary console.log or debug statements
- [ ] Complex logic has comments
- [ ] No hardcoded values that should be config

#### Testing
- [ ] New code has test coverage
- [ ] Edge cases are tested
- [ ] Error paths are tested

#### Documentation
- [ ] README updated (if applicable)
- [ ] API docs updated (if applicable)
- [ ] CHANGELOG updated
- [ ] Code comments for complex logic

#### Accessibility (if UI changes)
- [ ] Keyboard navigation works
- [ ] Screen reader tested
- [ ] Color contrast verified
- [ ] ARIA labels present

#### Security
- [ ] No secrets in code
- [ ] Input validation present
- [ ] Auth/permissions verified

### Deployment Notes

#### Pre-Deploy
- [ ] No special pre-deploy steps
- [ ] [Or: List required steps]

#### Post-Deploy
- [ ] No special post-deploy steps
- [ ] [Or: List required steps, e.g., "Run migration X"]

#### Feature Flags
- [ ] No feature flags
- [ ] Feature flag: `FLAG_NAME` (default: off/on)

### Rollback Instructions

This sprint uses atomic commits per issue. Rollback options:

**Revert Specific Issue:**
```bash
# Find commit for issue #XX
git log --oneline --grep="#XX"

# Revert that commit
git revert <commit-sha>
```

**Revert Entire Sprint:**
```bash
# If merge commit
git revert -m 1 <merge-commit-sha>

# If squash merged, revert the squash commit
git revert <squash-commit-sha>
```

### Related PRs / Work

- [ ] No related PRs
- Related PR: #XXX - [Description]
- Parallel research: `research/[topic]/` - [Status]

### Questions for Reviewers

- [ ] No specific questions
- [Question 1]
- [Question 2]

---

**Review Focus Areas:**
> Help reviewers know where to pay extra attention

1. [Area/file] - [Why it needs attention]
2. [Area/file] - [Why it needs attention]

---

*Sprint PR generated following vibecoding-sprint conventions*
