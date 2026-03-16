# Sprint Manifest: [Focus Name]

> Generated: [Date]
> Status: Planning | Active | Completed

## Overview

| Field | Value |
|-------|-------|
| **Branch** | `sprint/vX.Y.Z-focus-slug` |
| **Current Version** | vX.Y.Z |
| **Target Version** | vX.Y.Z |
| **Bump Type** | MAJOR / MINOR / PATCH |
| **Focus Area** | [Feature/Page/Discipline] |
| **Estimated Scope** | Small / Medium / Large |
| **Estimated Sessions** | 1-2 sessions |

## Issues Addressed

### Must Complete (Sprint Goals)

- [ ] #XX: [Issue title]
  - Acceptance: [Specific criteria]
  - Files: `src/path/to/affected/files`

- [ ] #XX: [Issue title]
  - Acceptance: [Specific criteria]
  - Files: `src/path/to/affected/files`

- [ ] #XX: [Issue title]
  - Acceptance: [Specific criteria]
  - Files: `src/path/to/affected/files`

### Nice to Have (If Time Permits)

- [ ] #XX: [Issue title]
- [ ] #XX: [Issue title]

## Acceptance Criteria

Sprint is complete when:

- [ ] All "Must Complete" issues are resolved
- [ ] All changes have appropriate test coverage
- [ ] No regressions in existing functionality
- [ ] PR passes all CI checks
- [ ] Documentation updated (if applicable)

## Version Impact Assessment

### Highest Impact Change
- **Issue**: #XX
- **Type**: [Feature / Breaking Change / Fix]
- **Why this determines version**: [Explanation]

### Breaking Changes
- [ ] None
- [ ] [List any breaking changes]

### Migration Required
- [ ] None
- [ ] [List any migrations]

## Dependencies

### Blocked By (Cannot Start Until)
- [ ] [None / List blockers]

### Blocks (Other Work Waiting)
- [ ] [None / List dependent work]

### Sprint Dependencies
- [ ] Sprint N must complete [specific item] first
- [ ] [Or: Self-contained, no sprint dependencies]

## Parallel Work Considerations

### Sprint Scope (Implementation Agents Work Here)

Files/areas that implementation agents will modify during this sprint:

```
[List specific files and directories]
- src/[area]/*
- tests/[area]/*
- etc.
```

### Protected Areas (Research Agents Avoid)

Research agents should NOT modify these during the sprint:

```
[Same as sprint scope above - copy the list]
```

### Active Research

| Topic | Agent | Staging Area | Impact on This Sprint |
|-------|-------|--------------|----------------------|
| [Topic] | research-agent | `research/[topic]/` | [None / May inform future work] |

### Other Sprints in Progress

- [ ] No parallel sprints
- [ ] Parallel sprint: [Name]
  - Their scope: [Files/areas]
  - Boundary: [How to avoid conflicts]

## Git Workflow

### Branch Details
```
Base branch: main
Sprint branch: sprint/vX.Y.Z-focus-slug
```

### Commit Conventions
```
Prefix: [scope]
Format: [scope] type: description (Refs: #XX)

Example commits for this sprint:
- [auth] fix: correct password validation (Refs: #12)
- [auth] feat: add remember-me option (Refs: #15)
- [auth] test: add login flow tests (Refs: #12, #15)
```

### Commit Types for This Sprint
- `fix`: Bug fixes
- `feat`: New features
- `refactor`: Code improvements
- `test`: Test additions
- `docs`: Documentation
- `chore`: Maintenance

## Testing Checklist

### Before PR
- [ ] All unit tests pass locally
- [ ] New code has test coverage
- [ ] Integration tests pass (if applicable)
- [ ] Manual testing of affected flows

### Test Commands
```bash
# Run all tests
npm test

# Run tests for affected files
npm test -- --coverage src/path/to/module

# Run specific test file
npm test -- src/path/to/file.test.ts
```

## Rollback Plan

### Individual Issue Rollback
Each issue has atomic commits. To revert a specific issue:
```bash
git revert <commit-sha>
```

### Full Sprint Rollback
If entire sprint needs reverting:
```bash
# Option 1: Revert merge commit
git revert -m 1 <merge-commit-sha>

# Option 2: Revert range (if squash merged)
git revert <first-sha>..<last-sha>
```

### Data Rollback (If Applicable)
- [ ] No data migrations in this sprint
- [ ] Migration is reversible: [Command/steps]
- [ ] Migration is NOT reversible: [Backup strategy]

## PR Template

When ready to create PR, use this structure:

```markdown
## Sprint: [Focus Name]

**Target Version**: vX.Y.Z -> vX.Y.Z ([BUMP TYPE])
**Branch**: sprint/vX.Y.Z-focus-slug

### Summary
[1-2 sentence summary of what this sprint accomplishes]

### Issues Resolved
- Fixes #XX: [Title]
- Fixes #XX: [Title]
- Fixes #XX: [Title]

### Changes
- [Bullet point list of key changes]
- [Organized by component/area]

### Testing
- [x] Unit tests added/updated
- [x] Integration tests pass
- [x] Manual testing completed

### Screenshots (if UI changes)
[Before/after screenshots]

### Checklist
- [ ] Code follows project conventions
- [ ] Tests cover new functionality
- [ ] Documentation updated
- [ ] No console errors/warnings
- [ ] Accessibility reviewed (if UI)

### Rollback
Individual issues can be reverted independently via atomic commits.
```

## Session Notes

### Session 1 - [Date]
- Started: [Time]
- Completed:
  - [ ] Issue #XX
  - [ ] Issue #XX
- Blockers encountered:
  - [None / Description]
- Notes:
  - [Any relevant observations]

### Session 2 - [Date] (if needed)
- Started: [Time]
- Completed:
  - [ ] Issue #XX
- Blockers encountered:
  - [None / Description]
- Notes:
  - [Any relevant observations]

## Post-Sprint

### Completed
- [ ] All issues closed
- [ ] PR merged
- [ ] Version bumped
- [ ] Changelog updated
- [ ] Deploy verified

### Lessons Learned
- [What went well]
- [What could improve]
- [Carry forward to next sprint]

### Spawned Work
Issues discovered during sprint that belong in future sprints:
- [ ] #XX: [New issue created]
- [ ] #XX: [New issue created]

---

*Manifest generated by vibecoding-sprint skill*
