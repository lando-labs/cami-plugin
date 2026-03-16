# Research to Sprint Pipeline

This reference explains how to triage research findings into sprint candidates.

## The Research-Sprint Boundary

```
RESEARCH PHASE              |  SPRINT PHASE
----------------------------|---------------------------
Investigate options         |  Implement chosen solution
Explore unknowns            |  Execute known work
Document findings           |  Ship working code
Make recommendations        |  Deliver user value
No commits to main          |  PRs to main

Output: Findings + Decision |  Output: Merged code
```

## Research Staging Area Structure

Research work lives in a staging area until ready for sprints:

```
research/
├── [topic-name]/
│   ├── README.md              # What is being researched
│   ├── findings.md            # Detailed findings
│   ├── recommendation.md      # Decision/recommendation
│   ├── sprint-candidates/     # Ready-to-implement items
│   │   ├── issue-1.md
│   │   ├── issue-2.md
│   │   └── issue-3.md
│   └── artifacts/             # Code samples, diagrams, etc.
│       ├── prototype/
│       └── diagrams/
```

## Research Output Documents

### findings.md Template

```markdown
# Research Findings: [Topic]

## Research Question
[What were we trying to learn?]

## Approach
[How did we investigate?]

## Key Findings

### Finding 1: [Title]
[Description]
- Evidence: [Links, data, experiments]
- Confidence: High / Medium / Low
- Implication: [What this means for implementation]

### Finding 2: [Title]
[Description]
- Evidence: [Links, data, experiments]
- Confidence: High / Medium / Low
- Implication: [What this means for implementation]

## Options Evaluated

| Option | Pros | Cons | Effort | Recommendation |
|--------|------|------|--------|----------------|
| Option A | [List] | [List] | S/M/L | Recommended / Not recommended |
| Option B | [List] | [List] | S/M/L | Recommended / Not recommended |

## Open Questions
- [Question still unanswered]
- [Question needing future research]

## References
- [Link 1]
- [Link 2]
```

### recommendation.md Template

```markdown
# Recommendation: [Topic]

## Decision
[Clear statement of what we recommend]

## Rationale
[Why this is the right choice]

## Implementation Impact

### Version Impact
- Expected: MAJOR / MINOR / PATCH
- Reason: [Why this version bump]

### Effort Estimate
- Estimated sprints: [Number]
- Complexity: Low / Medium / High
- Risk: Low / Medium / High

### Dependencies
- Requires: [None / List dependencies]
- Blocks: [None / List blocked work]

## Sprint Readiness
- [ ] Findings documented
- [ ] Decision made and approved
- [ ] Sprint candidates written
- [ ] Acceptance criteria defined
- [ ] Technical approach validated

## Alternative If Rejected
[What to do if this recommendation is not approved]
```

## Sprint Candidate Format

Each sprint candidate in `sprint-candidates/` should be a mini-issue:

```markdown
# Sprint Candidate: [Title]

## Source
Research: [Topic name]
Finding: [Which finding this implements]

## Description
[What needs to be implemented]

## Acceptance Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

## Technical Approach
[High-level approach from research]

## Effort Estimate
- Size: Small / Medium / Large
- Risk: Low / Medium / High

## Dependencies
- Requires: [Other candidates / external]
- Can parallel with: [Other candidates]

## Files Likely Affected
- `src/path/to/file1`
- `src/path/to/file2`

## Version Impact
- Type: fix / feat / breaking
- Impact: PATCH / MINOR / MAJOR
```

## Triage Workflow

### Step 1: Review Research Completion

Before triaging, verify research is complete:

```
- [ ] findings.md is comprehensive
- [ ] recommendation.md has clear decision
- [ ] Decision is approved (if approval needed)
- [ ] Sprint candidates are written
- [ ] No blocking open questions
```

### Step 2: Assess Sprint Candidates

For each sprint candidate, determine:

```
1. Is this actionable? (Clear enough to implement)
   NO -> Return to research for clarification
   YES -> Continue

2. What's the version impact?
   MAJOR -> Flag for careful sprint planning
   MINOR -> Standard sprint candidate
   PATCH -> Can bundle with related work

3. Are there dependencies?
   YES -> Map dependencies, may affect sprint order
   NO -> Flexible placement

4. How does it group?
   Feature-related -> Group with feature sprint
   Discipline-related -> Group with discipline sprint
   Standalone -> Could be its own small sprint
```

### Step 3: Create Issues or Add to Backlog

Options for handling sprint candidates:

**Option A: Create GitHub Issues**
```bash
# Good for formal tracking
# Each candidate becomes an issue
# Link back to research
```

**Option B: Add to Sprint Plan Directly**
```markdown
# Good for immediate implementation
# Reference candidate file in sprint manifest
# Keep research link for context
```

**Option C: Add to Backlog Document**
```markdown
# Good for future planning
# Document in "What Should Be Next"
# Re-evaluate in next planning session
```

### Step 4: Update Research Status

After triage, update research README:

```markdown
## Status: Triaged

Research complete. Findings have been triaged into:
- Sprint [N]: Issues #XX, #XX
- Backlog: Issues #XX, #XX
- Deferred: [Items not implementing, with reason]
```

## Triage Decision Matrix

| Candidate Quality | Implementation Clarity | Action |
|-------------------|------------------------|--------|
| High value, clear | Ready to implement | Add to next sprint |
| High value, unclear | Needs more research | Return to research |
| Medium value, clear | Can implement when ready | Add to backlog |
| Medium value, unclear | May not be worth it | Defer or drop |
| Low value, any | Probably not worth it | Drop or park |

## Common Triage Patterns

### Pattern 1: Single Large Feature

Research produced one big implementation:

```
Research: OAuth Integration
Candidates:
  1. OAuth provider setup (pre-req)
  2. Login flow implementation
  3. Account linking
  4. Token refresh handling
  5. Logout/disconnect

Triage: Create dedicated MINOR sprint
Branch: sprint/v1.5.0-oauth-integration
All candidates in one sprint (logical unit)
```

### Pattern 2: Multiple Small Improvements

Research produced several independent improvements:

```
Research: Performance Audit
Candidates:
  1. Add image lazy loading
  2. Implement query caching
  3. Reduce bundle size
  4. Optimize CSS delivery

Triage: Create discipline sprint OR distribute
Option A: sprint/v1.4.1-performance (all together)
Option B: Add individuals to feature sprints touching same areas
```

### Pattern 3: Breaking Change Required

Research concluded breaking change is needed:

```
Research: API Versioning Strategy
Candidates:
  1. Create v2 API endpoints
  2. Migrate clients to v2
  3. Deprecate v1 endpoints
  4. Remove v1 (breaking)

Triage: Multi-sprint plan
Sprint N: Create v2 (MINOR)
Sprint N+1: Migration support (MINOR)
Sprint N+2: Deprecate v1 (MINOR)
Sprint N+3: Remove v1 (MAJOR)
```

### Pattern 4: Research Recommends Against

Research concluded "don't do this":

```
Research: GraphQL Migration
Recommendation: Do not migrate
- Current REST API is sufficient
- Migration cost exceeds benefit
- Team lacks GraphQL expertise

Triage: Document decision, no sprint candidates
- Update architecture decision records
- Close related issues as "won't do"
- Archive research for future reference
```

## Parallel Work Boundaries

When research is ongoing alongside sprints:

### Safe Zones (Sprint Can Touch)
- Files not being researched
- Features with clear implementations
- Bug fixes unrelated to research area

### Danger Zones (Avoid in Sprint)
- Files research might change
- Patterns research might replace
- Dependencies research might swap

### Communication Protocol

```
Research Agent: "I'm investigating caching strategies"
Sprint Planner: "Sprint 3 needs to touch caching"

Options:
A. Sprint waits for research
B. Research prioritizes caching findings
C. Sprint proceeds with current approach, research continues
D. Coordinate specific files (research: Redis, sprint: local cache)
```

## Research Lifecycle

```
1. INITIATED
   Research question defined
   Staging area created

2. IN PROGRESS
   Active investigation
   findings.md being updated
   DO NOT create sprint candidates yet

3. FINDINGS COMPLETE
   findings.md comprehensive
   Ready for recommendation

4. RECOMMENDATION READY
   recommendation.md written
   Decision proposed

5. APPROVED
   Decision confirmed
   Sprint candidates can be written

6. TRIAGED
   Candidates moved to sprints/backlog
   Research archived

7. ARCHIVED
   Implementation complete
   Research folder moved to archive/
```

## Checklist: Research Ready for Triage

Before triaging research into sprints:

- [ ] Research question fully answered
- [ ] Findings documented with evidence
- [ ] Recommendation is clear and justified
- [ ] Decision approved (if approval required)
- [ ] Sprint candidates written with:
  - [ ] Clear descriptions
  - [ ] Acceptance criteria
  - [ ] Effort estimates
  - [ ] Version impact assessed
- [ ] Dependencies mapped
- [ ] No blocking open questions
- [ ] Implementation approach validated (prototype if complex)
