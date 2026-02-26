# Confirmation Patterns

**Version:** 1.0.0
**Last Updated:** 2026-02-25

This document defines when and how CAMI agents request user confirmation before executing operations. The goal is to protect users from unintended destructive actions while keeping common read-only operations frictionless.

---

## Core Principles

1. **Protect User Work** - Always confirm before destroying or overwriting data
2. **Minimize Friction** - Never confirm read-only operations
3. **Be Explicit** - Clearly state what WILL happen and all side effects
4. **Batch Intelligence** - Offer "yes/no to all" for multi-item operations
5. **Detect Conflicts** - Only confirm conditional operations when conflicts exist

---

## Confirmation Categories

### 1. Always Confirm

These operations are potentially destructive and ALWAYS require explicit user confirmation.

#### Overwrite Existing Files

**Trigger:** Writing to a file path that already exists with different content.

**Confirmation Message:**
```
I found an existing file at:
  agents/scout.md

This will overwrite the current file with a new version.

Continue? (y/n)
```

**Detection Logic:**
```typescript
// Check if file exists and content differs
const existingContent = await readFile(targetPath)
if (existingContent && existingContent !== newContent) {
  await confirm("overwrite")
}
```

**On "yes":** Write the new file, overwriting the old one
**On "no":** Abort operation, leave existing file unchanged

---

#### Delete Files/Capabilities

**Trigger:** Removing agent files, skills, or source directories.

**Confirmation Message:**
```
This will delete:
  agents/game-dev-specialist.md
  agents/phaser-expert.md

These agents will no longer be available.

Continue? (y/n)
```

**Detection Logic:**
```typescript
// Always confirm deletions
if (operation === "delete" || operation === "remove") {
  await confirm("delete", targetPaths)
}
```

**On "yes":** Delete the specified files
**On "no":** Abort operation, preserve all files

---

#### Normalize (Bulk Rename)

**Trigger:** Using `normalize` command to rename multiple agents/skills to match standards.

**Confirmation Message:**
```
This will rename 3 agents to match naming conventions:

  game-dev-agent.md → game-dev-specialist.md
  PhaserExpert.md → phaser-expert.md
  UI_Designer.md → ui-designer.md

Continue? (y/n)
```

**Detection Logic:**
```typescript
// Always confirm normalize operations
if (operation === "normalize") {
  const changes = detectNamingViolations()
  if (changes.length > 0) {
    await confirm("normalize", changes)
  }
}
```

**On "yes":** Rename all files to normalized names
**On "no":** Abort operation, leave filenames unchanged

---

#### Deploy Many (Batch Operations)

**Trigger:** Deploying multiple agents/skills in a single operation.

**Confirmation Message:**
```
This will deploy 5 agents to ~/my-project/.claude/agents:

  scout.md
  architect.md
  game-dev-specialist.md
  phaser-expert.md
  ui-designer.md

2 of these will overwrite existing files:
  scout.md (exists, will be updated)
  architect.md (exists, will be updated)

Continue? (y/n/all)

Options:
  y     - Confirm this batch
  n     - Skip this batch
  all   - Confirm this and all remaining batches
```

**Detection Logic:**
```typescript
// For batch operations, check all targets
if (targets.length > 1) {
  const conflicts = targets.filter(hasConflict)
  await confirm("batch-deploy", {
    total: targets.length,
    conflicts: conflicts
  })
}
```

**On "y":** Deploy this batch only
**On "n":** Skip this batch, continue to next
**On "all":** Deploy this and all subsequent batches without further confirmation

---

#### Destructive Operations

**Trigger:** Any operation that modifies state irreversibly (force push, hard reset, purge cache).

**Confirmation Message:**
```
This will remove all cached data from ~/cami-workspace/.cache

This cannot be undone. Cached metadata will be rebuilt on next scan.

Continue? (y/n)
```

**Detection Logic:**
```typescript
// Flag operations as destructive in metadata
if (operation.metadata?.destructive === true) {
  await confirm("destructive", operation.description)
}
```

**On "yes":** Execute the destructive operation
**On "no":** Abort operation

---

### 2. Never Confirm

These operations are read-only and NEVER require confirmation.

#### List Operations

```bash
cami list agents
cami list sources
cami list deployments
```

**Why:** Pure read operation, no state changes.

---

#### Scan Operations

```bash
cami scan agents
cami scan sources
cami scan deployed ~/project
```

**Why:** Reads filesystem, updates internal cache, but doesn't modify user files.

---

#### Status Checks

```bash
cami status
cami version
cami which agent-name
```

**Why:** Reports current state, no modifications.

---

#### Recommendations (Read-Only)

```bash
cami recommend agents
cami suggest improvements
cami validate naming
```

**Why:** Provides guidance but takes no action. User must explicitly execute suggestions.

---

### 3. Conditional Confirm

These operations ONLY require confirmation when conflicts or risks are detected.

#### Deploy Single (Conflict Detection)

**Trigger:** Deploying a single agent/skill where a different version already exists at the target.

**When to Confirm:**
```typescript
const targetExists = await fileExists(targetPath)
const targetContent = await readFile(targetPath)
const sourceContent = await readFile(sourcePath)

if (targetExists && targetContent !== sourceContent) {
  // Content differs - confirm
  await confirm("deploy-conflict", {
    agent: agentName,
    targetPath: targetPath,
    diff: computeDiff(targetContent, sourceContent)
  })
}
```

**Confirmation Message:**
```
The agent 'scout' already exists at:
  ~/my-project/.claude/agents/scout.md

The deployed version differs from the source.

Changes:
  - Version: 2.1.0 → 2.2.0
  - Modified: 3 sections updated

Continue? (y/n)
```

**On "yes":** Deploy the new version, overwriting existing
**On "no":** Skip deployment, keep existing version

**When to Skip Confirmation:**
- Target doesn't exist (new deployment)
- Target content matches source exactly (no change)
- Target is identical to source (idempotent operation)

---

#### Update Source (Local Changes Detection)

**Trigger:** Updating a source (git pull, refresh) when local uncommitted changes exist.

**When to Confirm:**
```typescript
const hasLocalChanges = await gitStatus(sourcePath)
  .then(status => status.modified.length > 0 || status.staged.length > 0)

if (hasLocalChanges) {
  await confirm("update-source-with-local-changes", {
    source: sourceName,
    modifiedFiles: status.modified,
    stagedFiles: status.staged
  })
}
```

**Confirmation Message:**
```
The source 'game-dev-guild' has uncommitted local changes:

Modified:
  agents/phaser-expert.md
  STRATEGIES.yaml

Updating will pull remote changes and may cause conflicts.

Continue? (y/n)
```

**On "yes":** Attempt update (git pull), handle merge conflicts if they occur
**On "no":** Skip update, preserve local changes

**When to Skip Confirmation:**
- No local changes (clean working tree)
- Source is not a git repo (simple directory copy)

---

#### Add Source (Similar Source Detection)

**Trigger:** Adding a new source when a source with a similar name or URL already exists.

**When to Confirm:**
```typescript
const existingSources = await listSources()
const similar = existingSources.find(s =>
  similarity(s.name, newSource.name) > 0.8 ||
  s.url === newSource.url
)

if (similar) {
  await confirm("add-similar-source", {
    newSource: newSource,
    existingSource: similar
  })
}
```

**Confirmation Message:**
```
A similar source already exists:

Existing: game-dev-guild
  URL: https://github.com/lando-labs/game-dev-guild

New: game-dev-agents
  URL: https://github.com/lando-labs/game-dev-guild

These may be duplicates. Continue? (y/n)
```

**On "yes":** Add the new source anyway
**On "no":** Abort add operation

**When to Skip Confirmation:**
- No similar sources found
- User explicitly forces with `--force` flag

---

## Conflict Detection Details

### What Constitutes a Conflict?

| Scenario | Conflict? | Action |
|----------|-----------|--------|
| Target doesn't exist | No | Deploy without confirmation |
| Target exists, identical content | No | Skip (no-op) or deploy without confirmation |
| Target exists, different content | **Yes** | Confirm before overwriting |
| Target exists, missing metadata | **Yes** | Confirm (assume conflict) |

### Version Comparison Logic

When comparing versions, CAMI uses frontmatter metadata:

```typescript
function hasConflict(sourcePath: string, targetPath: string): boolean {
  const source = parseAgent(sourcePath)
  const target = parseAgent(targetPath)

  // No target = no conflict
  if (!target) return false

  // Compare versions if available
  if (source.version && target.version) {
    return source.version !== target.version
  }

  // Fallback to content hash comparison
  return hashContent(source.content) !== hashContent(target.content)
}
```

**Version Precedence:**
1. Semantic version from frontmatter (`version: "2.1.0"`)
2. Git commit hash (if source is git repo)
3. File content hash (SHA-256)

---

## Batch Operation Handling

### Individual vs Batch Confirmation

**Individual Confirmation** (1 item):
```
This will overwrite:
  agents/scout.md

Continue? (y/n)
```

**Batch Confirmation** (2+ items):
```
This will deploy 5 agents. 2 will overwrite existing files.

Continue? (y/n/all/skip-conflicts)

Options:
  y               - Confirm this batch
  n               - Skip entire batch
  all             - Confirm all without further prompts
  skip-conflicts  - Only deploy new files, skip overwrites
```

### "Yes to All" / "No to All" Options

For batch operations, CAMI offers extended options:

| Option | Behavior |
|--------|----------|
| `y` | Confirm this batch only, prompt again for next batch |
| `n` | Skip this entire batch, prompt for next batch |
| `all` | Confirm this and all subsequent operations (no more prompts) |
| `skip-conflicts` | Only perform non-conflicting operations |

**Implementation:**
```typescript
let confirmationMode = "prompt" // "prompt" | "all" | "skip-conflicts"

for (const batch of batches) {
  if (confirmationMode === "all") {
    await executeBatch(batch)
    continue
  }

  if (confirmationMode === "skip-conflicts") {
    const safe = batch.filter(item => !hasConflict(item))
    await executeBatch(safe)
    continue
  }

  // Prompt for this batch
  const response = await confirm("batch", batch)

  if (response === "all") {
    confirmationMode = "all"
    await executeBatch(batch)
  } else if (response === "skip-conflicts") {
    confirmationMode = "skip-conflicts"
    const safe = batch.filter(item => !hasConflict(item))
    await executeBatch(safe)
  } else if (response === "y") {
    await executeBatch(batch)
  }
  // "n" skips batch, continues to next
}
```

### Progress Reporting During Batch

When executing batch operations, CAMI provides real-time progress:

```
Deploying 5 agents...

✓ scout.md (deployed)
✓ architect.md (deployed)
⊘ game-dev-specialist.md (skipped - conflict)
✓ phaser-expert.md (deployed)
✓ ui-designer.md (deployed)

Completed: 4/5 (1 skipped)
```

**Progress Indicators:**
- `✓` Success
- `⊘` Skipped (conflict or user choice)
- `✗` Failed (error occurred)
- `→` In progress

---

## Message Clarity Standards

### Always State What WILL Happen

**Good:**
```
This will overwrite 3 existing files:
  agents/scout.md
  agents/architect.md
  agents/specialist.md

Continue? (y/n)
```

**Bad:**
```
Files may be modified. Continue? (y/n)
```

### List All Side Effects

**Good:**
```
This will:
- Delete ~/cami-workspace/sources/old-guild
- Remove 12 agents from the registry
- Update deployment records in 3 projects

Continue? (y/n)
```

**Bad:**
```
This will delete the source. Continue? (y/n)
```

### Never Hide Destructive Actions

**Good:**
```
This will PERMANENTLY DELETE the source 'old-guild'.

All agents from this source will be removed from the registry.
Deployed agents will remain but will no longer receive updates.

This cannot be undone.

Continue? (y/n)
```

**Bad:**
```
Remove 'old-guild'? (y/n)
```

---

## Scout Voice Examples

All confirmation messages use Scout's voice: direct, protective, matter-of-fact.

### Example 1: Overwrite Conflict

```
Found an existing agent at:
  agents/scout.md (version 2.1.0)

The source has a newer version:
  version 2.2.0

This will overwrite your deployed version.

Continue? (y/n)
```

**Scout's Tone:** Direct, states facts, no drama, protective without being alarmist.

---

### Example 2: Batch Deploy with Conflicts

```
Ready to deploy 5 agents to ~/my-project/.claude/agents

New deployments (3):
  ✓ phaser-expert.md
  ✓ game-dev-specialist.md
  ✓ ui-designer.md

Will overwrite (2):
  ⚠ scout.md (v2.1.0 → v2.2.0)
  ⚠ architect.md (v1.5.0 → v1.5.1)

Continue? (y/n/all/skip-conflicts)
```

**Scout's Tone:** Organized, clear categorization, uses visual markers, offers options.

---

### Example 3: Destructive Operation

```
This will delete the source 'old-guild'.

Impact:
- 12 agents removed from registry
- 3 projects have deployments from this source
- Deployed agents will remain but won't update

This cannot be undone.

Continue? (y/n)
```

**Scout's Tone:** Serious but not dramatic, clearly states impact, emphasizes permanence.

---

### Example 4: Local Changes Detection

```
The source 'game-dev-guild' has uncommitted changes:

Modified:
  agents/phaser-expert.md
  STRATEGIES.yaml

Updating will pull remote changes and may cause conflicts.

Continue? (y/n)
```

**Scout's Tone:** Informative, warns of potential outcome, lets user decide.

---

## User Response Handling

### Valid Responses

| Response | Aliases | Meaning |
|----------|---------|---------|
| `y` | `yes`, `Y`, `YES` | Confirm and proceed |
| `n` | `no`, `N`, `NO` | Cancel operation |
| `all` | `a`, `yes-all` | Confirm all (batch only) |
| `skip-conflicts` | `skip`, `safe` | Only non-conflicting (batch only) |

### Invalid Response Handling

**User Input:** `maybe`

**Scout's Response:**
```
I need a clear answer.

Options:
  y - Yes, proceed
  n - No, cancel

Continue? (y/n)
```

**User Input:** `<blank>` or `<Enter>`

**Scout's Response:**
```
No response received. Treating as 'no' (operation canceled).
```

**Default Behavior:** When in doubt, ALWAYS default to "no" (safe option).

---

## Implementation Checklist

For each new CAMI operation, verify:

- [ ] Categorized correctly (Always/Never/Conditional)
- [ ] Confirmation message written in Scout voice
- [ ] All side effects listed explicitly
- [ ] Conflict detection logic implemented
- [ ] Batch handling if applicable
- [ ] Valid response handling
- [ ] Default-to-safe behavior
- [ ] Progress reporting for long operations
- [ ] "Yes to all" option for batch operations
- [ ] Documentation updated

---

## Related Documentation

- [Scout Agent Definition](../../agents/scout.md)
- [CAMI Commands Reference](../cami-commands.md)
- [Error Handling Patterns](./error-handling.md)
- [Voice & Tone Guidelines](./voice-tone.md)

---

*This documentation ensures CAMI operations are both safe and user-friendly. When in doubt, confirm. When confirming, be clear.*
