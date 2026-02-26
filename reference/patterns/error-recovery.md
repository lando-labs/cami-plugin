<!--
AI-Generated Error Recovery Patterns
Created by: plugin-architect
Date: 2026-02-25
Purpose: Error handling and recovery guidelines for CAMI skills
-->

# CAMI Error Recovery Patterns

This document defines how CAMI skills should handle errors, communicate failures to users, and offer recovery options. All error handling follows the Scout persona - friendly, helpful, and never alarming.

## Core Recovery Principles

Every error recovery flow MUST follow these 5 principles:

### 1. Explain What Happened
State the error clearly in user-friendly terms. Avoid technical jargon when possible.

**Good:** "I couldn't find that agent in your sources."
**Bad:** "Error: Agent lookup failed with code 404."

### 2. Explain Why
Help the user understand the cause without making them feel at fault.

**Good:** "The repo URL seems to be private or doesn't exist."
**Bad:** "You provided an invalid URL."

### 3. Offer Options
Present 2-3 clear paths forward. Never leave users stuck.

**Good:** "I can try again, skip this source, or you can check the URL."
**Bad:** "Operation failed. Please fix and retry."

### 4. Allow Skip
Users should always be able to continue even if one operation fails.

**Good:** "Want to skip this one and continue with the others?"
**Bad:** [Operation just stops with no way forward]

### 5. Remember State
Track what succeeded and what failed so users don't lose progress.

**Good:** "I deployed 3 agents successfully. The 4th failed. Want to retry just that one?"
**Bad:** [User has to start entire deployment over]

---

## Error Categories

### Git Operations

#### Clone Failed

**Detection:**
```
git clone command exits with non-zero status
OR stderr contains "fatal: repository not found"
OR network timeout
```

**User Message:**
```
I couldn't clone [repo-name]:

[Brief error reason]

This could mean:
• The repo is private and needs authentication
• The URL might be incorrect
• Network issue

What would you like to do?
1. Create the agent yourself instead
2. Try again (if it was a network glitch)
3. Skip this source and continue
4. Check the URL and I'll wait
```

**Options Offered:**
- Create agent yourself (primary alternative - empowers user)
- Retry (useful for transient network issues)
- Skip (continue with other sources)
- Manual intervention (user fixes URL/auth)

**Recovery Flow:**
1. Display friendly error with context
2. Present numbered options (creation first)
3. If create: guide to agent-architect skill
4. If retry: attempt clone again (max 2 retries)
5. If skip: mark source as failed, continue to next
6. If manual: pause and wait for user action

**Skip Behavior:**
- Mark source as "failed to clone" in state
- Continue processing remaining sources
- Report skipped sources in final summary
- Don't deploy agents from skipped sources

---

#### Pull Failed

**Detection:**
```
git pull exits with non-zero
OR merge conflicts detected
OR "divergent branches" message
```

**User Message:**
```
I couldn't update [source-name]:

[Brief error reason]

Your local changes might conflict with the remote updates.

What would you like to do?
1. Use your local version (skip pull)
2. Let me try to reset and pull fresh
3. I'll wait while you resolve this manually
```

**Options Offered:**
- Use local (skip pull, use what's there)
- Hard reset (discard local changes, pull clean)
- Manual resolution (user handles git conflicts)

**Recovery Flow:**
1. Detect pull failure type (conflict vs network vs auth)
2. Tailor message to failure type
3. If user chooses local: use existing files, log warning
4. If user chooses reset: `git reset --hard origin/main && git pull`
5. If manual: provide instructions, wait for confirmation

**Skip Behavior:**
- Use currently checked out version
- Log which sources weren't updated
- Warn if local version might be stale
- Continue with deployment using local state

---

#### Repository Not Found

**Detection:**
```
Clone or fetch returns "repository not found"
OR 404 HTTP status
```

**User Message:**
```
I couldn't find the repository at [url].

This usually means:
• The URL has a typo
• The repo is private and I need access
• The repo was moved or deleted

What would you like to do?
1. Create the agent yourself instead
2. Check the URL and try again
3. Skip this source for now
4. Remove this source from your config
```

**Options Offered:**
- Create agent yourself (primary alternative)
- Correct and retry
- Skip temporarily
- Remove permanently

**Recovery Flow:**
1. Confirm URL is unreachable
2. Suggest common fixes (check spelling, verify access)
3. If create: offer agent-architect to build custom version
4. If retry: accept new URL, attempt clone
5. If skip: continue, note missing source
6. If remove: update sources config, confirm removal

**Skip Behavior:**
- Don't add to available agents
- Log missing source in summary
- Don't fail entire operation
- Report at end which sources were skipped

---

#### Authentication Errors

**Detection:**
```
git operation returns "authentication failed"
OR "permission denied"
OR 401/403 HTTP status
```

**User Message:**
```
I need authentication to access [repo-name].

This is a private repository. I can use your Git credentials, but you might need to:
• Set up a personal access token
• Configure SSH keys
• Grant me repository access

What would you like to do?
1. Create the agent yourself instead
2. I'll set up auth and you retry
3. Skip this source for now
4. Switch to a public mirror (if available)
```

**Options Offered:**
- Create agent yourself (empowers user, no auth needed)
- Manual auth setup + retry
- Skip for now
- Alternative source (if applicable)

**Recovery Flow:**
1. Detect auth failure
2. Offer creation as primary path (no auth issues)
3. Check if Git credential helper is configured
4. Provide setup instructions based on Git version
5. Wait for user to configure
6. Retry with new credentials

**Skip Behavior:**
- Mark as "authentication required"
- Skip for this session
- Don't remove from config (might work later)
- Note in summary which sources need auth

---

#### Network Failures

**Detection:**
```
Connection timeout
OR "could not resolve host"
OR network unreachable errors
```

**User Message:**
```
I couldn't reach [host] - looks like a network issue.

This could be:
• Temporary internet connectivity problem
• Corporate firewall blocking Git
• DNS resolution issue

What would you like to do?
1. Create the agent yourself instead (work offline)
2. Try again (might be temporary)
3. Skip and continue offline
4. Wait while you check your connection
```

**Options Offered:**
- Create agent yourself (work offline, no network needed)
- Retry (auto-retry with backoff)
- Skip (work offline with existing sources)
- Wait (user fixes network)

**Recovery Flow:**
1. Detect network error type
2. Implement exponential backoff: 2s, 5s, 10s
3. Max 3 automatic retries
4. If all fail, offer manual options
5. Track which operations were network-blocked

**Skip Behavior:**
- Use cached/local data if available
- Mark sources as "offline"
- Continue with what's accessible
- Summarize what couldn't be reached

---

### File Operations

#### File Not Found

**Detection:**
```
Read operation fails with ENOENT
OR file path doesn't exist
```

**User Message:**
```
I couldn't find [file-path].

Expected it to be there because:
[Why CAMI expected this file]

What would you like to do?
1. Let me create it with defaults
2. Point me to where it actually is
3. Skip this operation
```

**Options Offered:**
- Create with defaults
- Provide actual path
- Skip operation

**Recovery Flow:**
1. Verify file truly doesn't exist
2. Check if parent directory exists
3. Offer to create parent dirs if needed
4. If creating: use sensible defaults
5. If manual path: validate new path exists

**Skip Behavior:**
- Skip just this file operation
- Continue with other files
- Note what was skipped
- Warn if skipped file affects functionality

---

#### Permission Denied

**Detection:**
```
File operation fails with EACCES
OR write fails with permission error
```

**User Message:**
```
I don't have permission to write to [path].

This might be because:
• The directory is system-protected
• File permissions are too restrictive
• Another process has the file locked

What would you like to do?
1. I'll wait while you fix permissions
2. Try a different location
3. Skip this write operation
```

**Options Offered:**
- Wait for permission fix
- Alternative location
- Skip write

**Recovery Flow:**
1. Check if running with sufficient privileges
2. Verify file isn't locked by another process
3. Suggest chmod/ownership fixes
4. If alternative: validate new location is writable
5. Test write before proceeding

**Skip Behavior:**
- Don't write this file
- Continue with other operations
- Log what wasn't written
- Warn if critical file was skipped

---

#### Disk Full

**Detection:**
```
Write fails with ENOSPC
OR "no space left on device"
```

**User Message:**
```
I ran out of disk space while writing to [path].

You need about [size] more space to complete this operation.

What would you like to do?
1. I'll wait while you free up space
2. Use a different location with more space
3. Cancel this operation
```

**Options Offered:**
- Wait for cleanup
- Different location
- Cancel gracefully

**Recovery Flow:**
1. Calculate space needed
2. Check space on alternative volumes
3. Suggest temp file cleanup
4. Wait for user to free space
5. Verify space before retrying

**Skip Behavior:**
- Roll back partial writes
- Clean up temp files
- Don't leave corrupted state
- Report what was cancelled

---

#### Path Too Long

**Detection:**
```
File operation fails with ENAMETOOLONG
OR path exceeds OS limits (260 on Windows, 4096 on Unix)
```

**User Message:**
```
The file path is too long for your system:

[truncated-path]... ([length] characters)

What would you like to do?
1. Let me use a shorter path structure
2. You pick a shorter base directory
3. Skip this file
```

**Options Offered:**
- Auto-shorten paths
- Manual base directory
- Skip file

**Recovery Flow:**
1. Detect path length limit
2. Calculate how much to shorten
3. Offer abbreviated structure
4. Preserve essential path info
5. Verify shortened path is unique

**Skip Behavior:**
- Skip just the long-path file
- Note what was skipped
- Continue with other files
- Warn if file was important

---

### Parsing Errors

#### Invalid Frontmatter

**Detection:**
```
YAML parse fails on agent/skill file
OR frontmatter delimiters missing/malformed
```

**User Message:**
```
I couldn't parse the frontmatter in [file-name]:

[Specific YAML error]

The frontmatter should look like:
---
name: agent-name
version: "1.0.0"
---

What would you like to do?
1. Skip this agent and continue
2. I'll try to fix it automatically
3. Let me show you the issue to fix manually
```

**Options Offered:**
- Skip malformed file
- Auto-fix if possible
- Manual correction

**Recovery Flow:**
1. Identify specific YAML syntax error
2. Check if it's auto-fixable (common issues)
3. If auto-fix: fix and validate
4. If manual: show exact line and error
5. Re-parse after fix

**Skip Behavior:**
- Don't load this agent/skill
- Continue parsing other files
- Report which files had errors
- Summarize successful parses

---

#### Missing Required Fields

**Detection:**
```
Frontmatter parsed but missing: name, version, description
OR required plugin.json fields missing
```

**User Message:**
```
[file-name] is missing required field: [field-name]

Every agent needs:
• name (unique identifier)
• version (semver format)
• description (when to use it)

What would you like to do?
1. Skip this agent
2. Let me add a default [field-name]
3. I'll wait while you add it
```

**Options Offered:**
- Skip incomplete agent
- Use defaults where safe
- Manual completion

**Recovery Flow:**
1. Identify which required fields are missing
2. Determine if safe defaults exist
3. If defaults: suggest and confirm
4. If manual: wait for user edit
5. Re-validate after completion

**Skip Behavior:**
- Don't deploy incomplete agents
- Note which agents were skipped
- Continue with valid agents
- Report incomplete agents at end

---

#### Duplicate Names

**Detection:**
```
Two agents/skills with same name in same namespace
OR conflicting plugin names
```

**User Message:**
```
Found duplicate agent name "[name]" in:
• [source-1/path]
• [source-2/path]

I can only use one of these. Which would you prefer?
1. Use the one from [source-1] (more recent)
2. Use the one from [source-2]
3. Skip both to be safe
```

**Options Offered:**
- Choose first source
- Choose second source
- Skip both

**Recovery Flow:**
1. Detect duplicate
2. Show metadata for both (version, source, date)
3. Suggest most recent or most authoritative
4. User selects preference
5. Load only selected version

**Skip Behavior:**
- If "skip both": don't load either
- If chose one: load that one, ignore other
- Track which duplicates were encountered
- Report conflicts in summary

---

#### Invalid YAML/JSON

**Detection:**
```
JSON.parse() throws
OR YAML parse error
OR malformed data structures
```

**User Message:**
```
I couldn't read [file-name] - the format seems corrupted:

[Specific error: line X, column Y]

What would you like to do?
1. Skip this file
2. Show me the problematic section
3. Let me try to salvage what I can
```

**Options Offered:**
- Skip corrupted file
- Manual inspection
- Partial salvage (if possible)

**Recovery Flow:**
1. Identify exact parse error location
2. Check if partial data is usable
3. If salvageable: extract valid sections
4. If manual: show error context
5. Validate salvaged data

**Skip Behavior:**
- Don't use corrupted data
- Continue with other files
- Log parse errors
- Report data integrity issues

---

### Deployment Errors

#### Target Doesn't Exist

**Detection:**
```
Deployment target path doesn't exist
OR .claude directory not found
```

**User Message:**
```
I couldn't find the deployment target:

[target-path]

This might not be a Claude Code project, or the project structure is different.

What would you like to do?
1. Let me create the .claude directory structure
2. Point me to the correct project directory
3. Cancel this deployment
```

**Options Offered:**
- Create structure
- Correct path
- Cancel

**Recovery Flow:**
1. Verify target is actually a directory
2. Check for any .claude-related files
3. If create: build .claude/agents and .claude/skills
4. If manual: validate new path
5. Retry deployment

**Skip Behavior:**
- Don't deploy to invalid target
- Preserve source files
- Don't create partial structures
- Report deployment failure

---

#### Agent/Skill Already Exists

**Detection:**
```
Target file already exists
OR conflicting version detected
```

**User Message:**
```
[agent-name] is already deployed to this project.

Current: v[old-version]
New:     v[new-version]

What would you like to do?
1. Update to the new version
2. Keep the current version
3. Show me the differences first
```

**Options Offered:**
- Update (overwrite)
- Keep existing
- Diff first

**Recovery Flow:**
1. Read existing version
2. Compare versions (semver)
3. If diff requested: show changes
4. If update: backup old, write new
5. If keep: skip deployment, log decision

**Skip Behavior:**
- Keep existing file unchanged
- Note skipped deployment
- Don't fail entire operation
- Report skipped updates in summary

---

#### Write Failed

**Detection:**
```
Write operation fails after multiple retries
OR filesystem error during write
```

**User Message:**
```
I couldn't write [file-name] to [path].

Attempted [retry-count] times but failed due to:
[Error reason]

What would you like to do?
1. Try a different location
2. Skip this deployment
3. I'll wait while you check disk/permissions
```

**Options Offered:**
- Alternative path
- Skip this deployment
- Manual fix + retry

**Recovery Flow:**
1. Attempt write with retry (3 attempts)
2. If all fail, diagnose (permissions, space, lock)
3. Offer specific fix based on diagnosis
4. If alternative: validate new path
5. Clean up any partial writes

**Skip Behavior:**
- Don't leave partial files
- Remove any temp files created
- Note which deployments failed
- Continue with other deployments

---

#### Version Conflicts

**Detection:**
```
Deployed version is newer than source version
OR incompatible major version
```

**User Message:**
```
Version conflict for [agent-name]:

Deployed: v[deployed-version] (newer)
Source:   v[source-version] (older)

You're trying to deploy an older version. This might be intentional if you're rolling back.

What would you like to do?
1. Downgrade to the older version
2. Keep the newer version
3. Skip this deployment
```

**Options Offered:**
- Downgrade (intentional rollback)
- Keep newer
- Skip

**Recovery Flow:**
1. Compare versions semantically
2. Warn if major version differs
3. If downgrade: confirm intentional
4. If keep: skip deployment, log decision
5. Track version decisions

**Skip Behavior:**
- Don't modify deployed version
- Log version conflict
- Note in summary which versions conflicted
- Continue with other deployments

---

## Recovery Flow Examples

### Example 1: Network Failure During Multi-Source Scout

**Scenario:** User runs scout, 3 sources configured, 2nd source has network timeout.

**Flow:**
```
Scout: "Let me check your sources..."

[Checking custom... ✓]
[Checking fullstack-source... ✗]

Scout: "I couldn't reach fullstack-source - looks like a network issue.

This could be:
• Temporary internet connectivity problem
• Corporate firewall blocking Git
• DNS resolution issue

What would you like to do?
1. Create any agents you need yourself (work offline)
2. Try again (might be temporary)
3. Skip and continue with other sources
4. Wait while you check your connection"

User: 3

Scout: "Skipping fullstack-source for now.

[Checking game-dev-source... ✓]

Found 47 agents across 2 sources:
• custom: 28 agents (your custom agents)
• game-dev-source: 19 agents
• fullstack-source: skipped (network error)

Want to see them organized by category?"
```

**Key Points:**
- Didn't fail entire operation
- Clearly explained issue
- Offered 3 clear options
- Continued with successful sources
- Summarized what was skipped

---

### Example 2: Source Not Found - Create Alternative Path

**Scenario:** User tries to add a source but the repo doesn't exist, chooses to create their own agent instead.

**Flow:**
```
Scout: "Let me add that source for you..."

[Checking repository URL... ✗]

Scout: "I couldn't find the repository at https://github.com/org/special-agents.git.

This usually means:
• The URL has a typo
• The repo is private and I need access
• The repo was moved or deleted

What would you like to do?
1. Create the agent yourself instead
2. Check the URL and try again
3. Skip this source for now
4. Remove this source from your config"

User: 1

Scout: "Great! Let me help you create your own agent.

What kind of agent do you need? I can guide you through creating:
• A specialist for a specific tech stack
• A methodology agent for your workflow
• A custom agent for your team's conventions

Tell me what you're building and I'll create an agent that fits your needs."

User: "A React specialist with TypeScript and Tailwind"

Scout: "Creating a React + TypeScript + Tailwind specialist...

[Invoking agent-architect... ✓]
[Writing to ~/cami-workspace/sources/custom/react-specialist.md... ✓]

Created react-specialist v1.0.0 in your custom source!

This agent specializes in:
• React 18+ with hooks and modern patterns
• TypeScript for type safety
• Tailwind CSS for styling
• Best practices and accessibility

Want to deploy it to a project?"
```

**Key Points:**
- Source failure didn't block user's goal
- Creation offered as primary alternative
- Smooth transition to agent-architect
- User got what they needed without the source
- Empowering, not frustrating experience

---

### Example 3: Deployment With Permission Error

**Scenario:** User deploys agent, target directory not writable.

**Flow:**
```
Scout: "Deploying frontend-methodology to your project..."

[Reading agent... ✓]
[Validating... ✓]
[Writing to .claude/agents... ✗]

Scout: "I don't have permission to write to:

/path/to/project/.claude/agents/

This might be because:
• The directory is system-protected
• File permissions are too restrictive
• Another process has the file locked

What would you like to do?
1. I'll wait while you fix permissions (try: chmod u+w .claude)
2. Try a different location
3. Skip this deployment"

User: 1

[User runs: chmod -R u+w .claude]

User: "Done, try again"

Scout: "Deploying frontend-methodology to your project..."

[Writing to .claude/agents... ✓]

Deployment complete! frontend-methodology v2.1.0 is ready to use.
```

**Key Points:**
- Specific path shown
- Suggested exact fix command
- Waited for user action
- Retried successfully
- Confirmed completion

---

### Example 4: Multiple Parse Errors

**Scenario:** Source has 5 agents, 2 have invalid frontmatter.

**Flow:**
```
Scout: "Loading agents from team-source..."

[Parsing frontend-methodology... ✓]
[Parsing backend-specialist... ✗]

Scout: "I couldn't parse the frontmatter in backend-specialist.md:

  Expected a closing ']' in mapping (line 4)

What would you like to do?
1. Skip this agent and continue
2. Let me show you the issue to fix manually
3. Stop and fix all issues first"

User: 1

Scout: "Skipping backend-specialist.

[Parsing database-expert... ✓]
[Parsing devops-engineer... ✗]

I couldn't parse the frontmatter in devops-engineer.md:

  Missing required field: version

What would you like to do?
1. Skip this agent and continue
2. Let me add a default version
3. I'll wait while you add it"

User: 1

Scout: "Skipping devops-engineer.

Loaded 3 agents from team-source:
✓ frontend-methodology v2.1.0
✓ database-expert v1.3.0
✗ 2 skipped due to errors

Would you like to:
1. Create your own versions of the skipped agents
2. Fix the errors and reload
3. Continue with the agents that loaded"
```

**Key Points:**
- Continued despite errors
- Specific error for each file
- Offered options per error
- Summarized successes and failures
- Offered creation as alternative path
- Empowered user to work around broken source

---

## Graceful Degradation Patterns

### Partial Success Is Success

When some operations succeed and others fail:

1. **Complete What You Can**
   - Don't abort entire operation for one failure
   - Process all items, track failures
   - Report both successes and failures

2. **Clear Final Summary**
   ```
   Deployed 4 of 5 agents:
   ✓ frontend-methodology
   ✓ backend-specialist
   ✓ database-expert
   ✓ devops-engineer
   ✗ security-specialist (permission denied)
   ```

3. **Offer Retry for Failures Only**
   - Don't make user redo successes
   - "Want to retry just security-specialist?"

### Fallback Strategies

#### Missing Metadata
- Use filename as name if `name` field missing
- Default version to "0.0.0" with warning
- Generate basic description from file content

#### Incomplete Data
- Load what's parseable
- Mark incomplete sections
- Warn about missing functionality

#### Offline Operations
- Use cached data when available
- Clearly mark as "last synced [time]"
- Offer to sync when network returns

---

## Voice Consistency in Errors

### Tone Guidelines

**Friendly, Not Alarming:**
- "I couldn't find that" not "ERROR: File not found"
- "Let's try something else" not "Operation failed"
- "That didn't work because..." not "Invalid operation"

**Helpful, Not Patronizing:**
- Explain without over-explaining
- Assume user competence
- Offer help, don't demand compliance

**Confident, Not Arrogant:**
- "I'd recommend" not "You must"
- "This usually means" not "Obviously this is"
- "Let me help" not "I'll fix your mistake"

### Error Message Template

```
[What happened - one clear sentence]

[Why it happened - brief context]

[What this means for the user - impact]

What would you like to do?
1. [Option that fixes the issue]
2. [Option that works around the issue]
3. [Option that skips the issue]
```

### Example Transformations

| Technical Error | Scout Voice |
|----------------|-------------|
| `ENOENT: no such file or directory` | "I couldn't find that file" |
| `git clone failed with exit code 128` | "I couldn't clone that repository" |
| `YAML parse error at line 23` | "The frontmatter has a syntax issue on line 23" |
| `Permission denied (publickey)` | "I need SSH authentication to access this repo" |
| `ENOSPC: no space left on device` | "I ran out of disk space" |

---

## Implementation Checklist

For each error handler, ensure:

- [ ] Explains what happened clearly
- [ ] Explains why it happened
- [ ] Offers 2-3 concrete options
- [ ] Includes a "skip" option
- [ ] Tracks state for recovery
- [ ] Uses Scout voice consistently
- [ ] Provides specific next steps
- [ ] Doesn't lose progress on skip
- [ ] Summarizes skipped items
- [ ] Offers retry for transient errors

---

## Testing Error Recovery

### Manual Test Scenarios

1. **Network Failures**
   - Disconnect network during clone
   - Use invalid repo URL
   - Use unreachable hostname

2. **File System Errors**
   - Remove write permissions on target
   - Fill disk to capacity
   - Use paths exceeding OS limits

3. **Parse Errors**
   - Corrupt YAML frontmatter
   - Remove required fields
   - Create duplicate names

4. **Deployment Errors**
   - Deploy to non-existent path
   - Deploy conflicting versions
   - Lock files being deployed

### Validation Questions

For each error scenario:
- Does the user understand what went wrong?
- Does the user understand why?
- Are the options clear and actionable?
- Can the user skip and continue?
- Is progress preserved?
- Is the voice friendly and helpful?
- Would a junior developer understand the error?
- Is there a clear path to resolution?

---

*Remember: Errors are opportunities to be helpful. A well-handled error builds trust; a poorly handled error erodes confidence. Always prioritize user understanding over technical precision.*
