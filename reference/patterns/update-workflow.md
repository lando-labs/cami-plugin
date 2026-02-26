# Update Workflow Pattern

**The Six-Step Update Cycle for Keeping Deployed Capabilities Current**

This document describes the complete workflow for discovering, evaluating, and applying updates to deployed agents and skills in Claude Code projects.

---

## Overview

The update workflow is a coordinated sequence that:
1. Checks source repos for new versions
2. Scans deployed capabilities
3. Identifies update candidates
4. Prompts user for selection
5. Redeploys chosen capabilities
6. Updates project documentation

This is typically orchestrated by the **Scout** agent, who guides the user through each step.

---

## The Six Steps

### Step 1: Update Sources

**Purpose**: Ensure local source repos have the latest content.

**Skill**: `manage-sources` (update mode)

**Actions**:
- Pull latest commits from git repos in `~/cami-workspace/sources/`
- Check for new tags or version bumps
- Detect new agents/skills added to sources
- Report what changed since last update

**Output**:
```
Updated sources:
- fullstack-guild: 3 new commits, agent-architect updated to v2.1.0
- game-dev-guild: No changes
- content-guild: New agent added (seo-optimizer v1.0.0)
```

**Error Handling**:
- Source repo unavailable → Skip and warn user
- Merge conflicts → Report and skip, ask user to resolve manually
- No git remote → Report as local-only source

---

### Step 2: Scan Deployed Capabilities

**Purpose**: Build inventory of what's currently deployed and their versions.

**Skill**: `manage-capabilities` (scan mode)

**Actions**:
- Scan `.claude/agents/` for agent files
- Scan `.claude/skills/` for skill directories
- Extract version from YAML frontmatter
- Build capability manifest with:
  - Name
  - Type (agent/skill)
  - Current version
  - Source guild
  - File path

**Output**:
```json
{
  "agents": [
    {
      "name": "agent-architect",
      "version": "2.0.0",
      "source": "fullstack-guild",
      "path": ".claude/agents/agent-architect.md"
    }
  ],
  "skills": [
    {
      "name": "manage-capabilities",
      "version": "1.2.0",
      "source": "cami",
      "path": ".claude/skills/manage-capabilities/"
    }
  ]
}
```

**Edge Cases**:
- Missing version field → Report as "unknown", suggest manual check
- Unrecognized source → Mark as "custom" or "unknown"
- Invalid YAML → Flag for user review

---

### Step 3: Show Updates Available

**Purpose**: Compare deployed versions with source versions to find update candidates.

**Process**:
1. For each deployed capability:
   - Find matching capability in sources
   - Compare versions (deployed vs source)
   - Detect semantic version changes (major/minor/patch)
   - Flag breaking changes (major version bump)

2. Present findings to user in clear format

**Output Format**:
```
Updates available:

AGENTS:
  agent-architect: 2.0.0 → 2.1.0 (minor)
    - Enhanced multi-agent orchestration
    - New template system

SKILLS:
  manage-capabilities: 1.2.0 → 1.3.0 (minor)
    - Improved version detection

BREAKING CHANGES:
  (none)

No updates found for:
  - scout (already on latest)
  - manage-sources (already on latest)
```

**Version Comparison Logic**:
- Major bump (1.x.x → 2.x.x) → Breaking change warning
- Minor bump (x.1.x → x.2.x) → New features
- Patch bump (x.x.1 → x.x.2) → Bug fixes
- Same version → No update needed

**Edge Cases**:
- Version downgrade (2.1.0 → 2.0.0) → Flag as unusual, confirm intent
- Local modifications → Warn about potential overwrite
- Source version unparseable → Report as "unable to determine"

---

### Step 4: Prompt for Redeploy

**Purpose**: Let user choose which updates to apply.

**Scout's Role**: Present clear options and capture user decision.

**Prompt Format**:
```
Which capabilities would you like to update?

Options:
1. Update all (3 capabilities)
2. Select individually
3. Skip for now

Enter your choice (1-3):
```

**If Individual Selection**:
```
Select capabilities to update (space-separated numbers or names):

1. agent-architect (2.0.0 → 2.1.0)
2. manage-capabilities (1.2.0 → 1.3.0)

Enter selections: 1 manage-capabilities
```

**User Decision Handling**:
- "all" / "1" → Update everything
- Individual names/numbers → Update only selected
- "skip" / "3" → Abort workflow
- Invalid input → Re-prompt with clarification

**Confirmation for Breaking Changes**:
If major version bumps are selected:
```
WARNING: agent-architect 2.0.0 → 3.0.0 includes breaking changes.
Review changelog before updating.

Proceed? (yes/no):
```

---

### Step 5: Add with Overwrite

**Purpose**: Deploy updated capabilities, replacing existing files.

**Skill**: `manage-capabilities` (add mode with overwrite)

**Actions**:
For each selected capability:

1. **Backup (Optional)**:
   - If backup configured, copy current version to `.claude/backups/`
   - Timestamp: `agent-architect-2.0.0-20260225.md`

2. **Copy from Source**:
   - Copy agent file or skill directory from source
   - Preserve frontmatter and structure
   - Apply any project-specific configurations

3. **Verify Deployment**:
   - Check file exists at target path
   - Validate YAML frontmatter
   - Confirm version field updated

4. **Report Result**:
   ```
   ✓ agent-architect updated (2.0.0 → 2.1.0)
   ✓ manage-capabilities updated (1.2.0 → 1.3.0)
   ```

**Error Handling** (see error-recovery.md):
- File write fails → Restore from backup if available
- Invalid capability in source → Skip and report error
- Partial failure → Continue with remaining updates, report failures
- Source file missing → Alert user, skip update

**Partial Update Failure Example**:
```
Results:
✓ agent-architect updated successfully
✗ manage-capabilities failed (source file not found)

1 of 2 updates completed. Review errors above.
```

---

### Step 6: Update Project CLAUDE.md

**Purpose**: Regenerate capability tables to reflect new versions.

**Skill**: `manage-capabilities` (document mode)

**Actions**:
1. Scan newly deployed capabilities
2. Regenerate "Agents" and "Skills" tables
3. Update version numbers in table
4. Add timestamp: "Last updated: 2026-02-25"
5. Write updated sections back to CLAUDE.md

**Output Format in CLAUDE.md**:
```markdown
## Agents

Deployed to `.claude/agents/`.

| Agent | Version | Purpose |
|-------|---------|---------|
| **agent-architect** | 2.1.0 | Create and refine Claude Code agents |
| **scout** | 1.5.0 | Coordinate CAMI workflows |

Last updated: 2026-02-25
```

**Edge Cases**:
- CLAUDE.md missing → Create new file with tables
- Custom sections in CLAUDE.md → Preserve them, only update tables
- Write fails → Report error, don't abort (manual update needed)

---

## Scout Orchestration

**Scout's Role**: The conductor of the update symphony.

### Workflow Sequence

```
User: "Check for updates"

Scout:
├─1. Invoke manage-sources (update mode)
│   └─> Present: "Updated 2 sources, changes detected"
│
├─2. Invoke manage-capabilities (scan mode)
│   └─> Build: Deployed capability inventory
│
├─3. Compare versions
│   └─> Present: "3 updates available"
│
├─4. Prompt user
│   ├─> "Update all?"
│   └─> User: "Yes, update all"
│
├─5. Invoke manage-capabilities (add with overwrite)
│   └─> Present: "Updated 3 capabilities"
│
└─6. Invoke manage-capabilities (document mode)
    └─> Present: "CLAUDE.md updated"

Scout: "Update complete! All capabilities are now current."
```

### Handoff Protocol

**Between Steps**:
- Scout presents findings clearly
- Waits for user acknowledgment or decision
- Proceeds only with explicit or implicit permission
- Reports progress after each major action

**Skill Invocation Pattern**:
```
Scout: [Explains what will happen]
Scout: [Invokes skill via Skill tool]
Scout: [Presents skill output to user]
Scout: [Asks for next decision if needed]
```

**When to Stop and Ask**:
- Before updating anything (Step 4)
- When breaking changes detected
- On any error or unexpected result
- When no updates are available

**When to Proceed Automatically**:
- Updating sources (Step 1) - safe operation
- Scanning deployed (Step 2) - read-only
- Comparing versions (Step 3) - read-only
- After user says "update all" - explicit permission

---

## Example Conversation Flow

```
User: "Check if there are updates available"

Scout: "I'll check for updates. First, let me pull the latest from your sources."

[Invokes: manage-sources update]

Scout: "Sources updated:
- fullstack-guild: agent-architect updated to v2.1.0
- cami: No changes

Now scanning your deployed capabilities..."

[Invokes: manage-capabilities scan]

Scout: "Found 5 deployed capabilities. Comparing versions..."

Scout: "Updates available:

AGENTS:
  agent-architect: 2.0.0 → 2.1.0 (minor update)
    - Enhanced orchestration
    - New template system

Would you like to:
1. Update all (1 capability)
2. Skip for now"

User: "Update all"

Scout: "Updating agent-architect..."

[Invokes: manage-capabilities add --overwrite]

Scout: "✓ agent-architect updated successfully (2.0.0 → 2.1.0)

Updating your CLAUDE.md..."

[Invokes: manage-capabilities document]

Scout: "✓ CLAUDE.md updated

Update complete! Your capabilities are now current."
```

---

## Edge Cases

### Source Repo Unavailable

**Scenario**: Git pull fails for a source.

**Handling**:
```
Scout: "Unable to update fullstack-guild (network error).
Proceeding with other sources...

Updates based on available sources:
[shows updates from accessible sources only]"
```

**User Options**:
- Proceed with available updates
- Skip until connectivity restored
- Check manually with `gh repo view`

---

### Local Modifications to Deployed Files

**Scenario**: Deployed agent has local edits not in source.

**Detection**: Compare file hash with source (future enhancement).

**Handling**:
```
Scout: "Warning: agent-architect appears to have local modifications.
Updating will overwrite these changes.

Options:
1. Backup and update
2. Skip this capability
3. View diff first"
```

**Best Practice**: Always use backups if making local modifications.

---

### Version Downgrade Requested

**Scenario**: Source version is older than deployed version.

**Detection**: Semver comparison shows deployed > source.

**Handling**:
```
Scout: "Unusual: agent-architect deployed version (2.1.0) is newer than source (2.0.0).

This typically means:
- Source was rolled back
- You're on a dev/beta version
- Wrong source configured

Proceed with downgrade? (yes/no)"
```

**User Decision Required**: Never auto-downgrade.

---

### Partial Update Failure

**Scenario**: Some updates succeed, others fail.

**Handling**:
```
Scout: "Update results:

✓ agent-architect (2.0.0 → 2.1.0)
✗ manage-capabilities (source file missing)
✓ scout (1.4.0 → 1.5.0)

2 of 3 updates completed.

Failed updates require manual attention:
- Check fullstack-guild/skills/manage-capabilities/ exists
- Run 'manage-sources update' to refresh sources"
```

**Recovery**: See error-recovery.md for detailed strategies.

---

### No Updates Available

**Scenario**: All deployed capabilities are current.

**Handling**:
```
Scout: "All capabilities are up to date!

Deployed:
- agent-architect v2.1.0 (latest)
- scout v1.5.0 (latest)
- manage-capabilities v1.3.0 (latest)

No action needed."
```

---

## State Management

**Between Steps**:
Scout maintains context about:
- What sources were updated
- What capabilities are deployed
- What updates are available
- What user selected
- What was successfully updated

**Persistence**: No persistent state required - each step queries current state.

**Error State**: If workflow interrupted:
- User can restart from Step 1
- Idempotent operations allow safe re-run
- No partial state to clean up

---

## Testing the Workflow

**Manual Test Scenario**:

1. Deploy agent-architect v2.0.0 to a test project
2. Update source to v2.1.0
3. Run update workflow
4. Verify:
   - Scout detects update
   - Prompts for confirmation
   - Deploys v2.1.0
   - Updates CLAUDE.md
   - Version confirmed via scan

**Edge Case Testing**:
- Disconnect network before Step 1 (source unavailable)
- Modify deployed agent before Step 2 (local mods)
- Request update to older version (downgrade)
- Simulate file write error in Step 5 (partial failure)

---

## Related Documentation

- **error-recovery.md**: Detailed error handling strategies
- **source-management.md**: Managing agent sources
- **capability-deployment.md**: Initial deployment workflow
- **version-management.md**: Semantic versioning practices

---

## Future Enhancements

- **Automatic Update Checks**: Periodic background scans
- **Changelog Display**: Show detailed changes between versions
- **Dependency Resolution**: Handle capability dependencies
- **Rollback Command**: Quick revert to previous version
- **Update Notifications**: Alert when updates available
- **Diff Preview**: Show file changes before updating
- **Selective File Update**: Update only specific files in skills

---

*This workflow enables zero-friction capability updates while maintaining user control and project stability.*
