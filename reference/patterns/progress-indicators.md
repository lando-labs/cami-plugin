# Progress Indicators Pattern

## Purpose

Multi-step operations need progress feedback to:
- **Build confidence** - User knows something is happening
- **Manage expectations** - User understands the scope
- **Enable interruption** - User can see where we are if they need to stop
- **Celebrate completion** - User feels accomplishment at the end

Without progress indicators, users feel uncertainty. With too much detail, they feel overwhelmed. The sweet spot is scannable, confident feedback that shows meaningful progress without explaining every detail.

---

## When to Show Progress

| Operation | Show Progress? | Why |
|-----------|----------------|-----|
| Deploy 1 agent | No | Too fast, would feel noisy |
| Deploy 2+ agents | Yes | User needs feedback on batch operation |
| Git pull 1 source | Brief | Quick confirmation is enough |
| Git pull 2+ sources | Yes | Each source takes time, show which is current |
| Scan projects | Yes | Discovery takes time, show what's found |
| Create agent | Yes | Multi-step process with clear stages |
| List agents | No | Immediate display, no progress needed |
| Read single agent | No | Instant operation |
| Compare versions | Brief | Quick summary is enough |

**Rule of Thumb**: If it touches 2+ things or takes >2 seconds, show progress.

---

## Pattern Examples

### Batch Deployment

**Good:**
```
Deploying 5 agents to my-app...
  ✓ frontend-methodology
  ✓ backend-methodology
  ✓ testing-methodology
  ✓ database-methodology
  ✓ devops-methodology

All 5 deployed. Your roster is set.
```

**Why It Works:**
- One line per agent keeps it scannable
- Checkmarks show completion
- Final summary confirms success
- Voice is confident, not anxious

**Avoid:**
```
Deploying agents...
Now deploying frontend-methodology...
Copying file from source...
Writing to target...
Frontend-methodology deployed successfully!
Now deploying backend-methodology...
```
Too verbose. Each step doesn't need narration.

---

### Source Update

**Good:**
```
Updating your sources...

fullstack...
  3 new commits pulled
  +1 agent updated (frontend-methodology v1.3.0)

game-dev...
  Already up to date

All sources current.
```

**Why It Works:**
- Source name shows which is being processed
- Meaningful changes are highlighted
- "Already up to date" is brief but reassuring
- Summary confirms all sources checked

**Avoid:**
```
Updating sources...
Running git pull on fullstack...
Checking for changes...
Found 3 commits...
Parsing commit messages...
Detected agent update...
Moving to next source...
```
Too much detail about the mechanics.

---

### Project Scan

**Good:**
```
Scanning for Claude Code projects...

Found 4 projects:
  ~/projects/my-app (3 agents deployed)
  ~/projects/game (5 agents deployed)
  ~/projects/website (no agents)
  ~/projects/api (2 agents deployed)

Scan complete.
```

**Why It Works:**
- Shows what was found as it's discovered
- Agent count gives context
- Clear completion signal
- Paths are absolute and useful

**Avoid:**
```
Scanning...
Checking directory 1...
Checking directory 2...
Checking directory 3...
Found project in directory 1!
```
Progress on mechanics, not on meaningful findings.

---

### Agent Creation

**Good:**
```
Creating frontend-methodology agent...

Analyzing requirements... ✓
Drafting system prompt... ✓
Generating methodology section... ✓
Adding examples... ✓
Writing agent file... ✓

Agent created at ~/cami-workspace/sources/fullstack/agents/frontend-methodology.md
```

**Why It Works:**
- Stages are meaningful to the user
- Checkmarks show completion
- Final path is actionable
- Feels like progress, not just steps

**Avoid:**
```
Starting agent creation...
Step 1 of 5: Analyzing...
Please wait...
Step 2 of 5: Drafting...
Processing...
```
"Step X of Y" feels mechanical. Name what's happening instead.

---

## Format Guidelines

### Structure
```
[Opening statement of what's happening]

[Progressive updates as work happens]
  [Item or stage being processed]
  [Meaningful details if any]

[Summary confirming completion]
```

### Visual Elements
- **Checkmarks (✓)** - For completed items in a list
- **Item names** - Show what's currently being processed
- **Indentation** - Use 2 spaces to show details under an item
- **Blank lines** - Separate logical groups (sources, stages, etc.)
- **Counts** - "All 5 deployed", "Found 4 projects"

### What to Show
- **DO show**: Item names, meaningful changes, final counts
- **DON'T show**: Internal mechanics, file operations, waiting states

### Timing
- Update when something **meaningful** changes
- Don't update for every internal step
- Always provide a completion summary

---

## Voice Notes

### Confident, Not Anxious
```
✓ Good: "Deploying 5 agents..."
✗ Avoid: "Attempting to deploy 5 agents..."
```

### Accomplishment, Not Relief
```
✓ Good: "All 5 deployed. Your roster is set."
✗ Avoid: "Whew! Finally got all 5 deployed."
```

### Scannable, Not Verbose
```
✓ Good: "fullstack... 3 new commits pulled"
✗ Avoid: "Now pulling from fullstack source repository..."
```

### Factual, Not Explanatory
```
✓ Good: "Agent created at ~/path/to/agent.md"
✗ Avoid: "The agent file has been successfully written to the filesystem at ~/path/to/agent.md"
```

---

## Anti-Patterns

### Too Much Detail
```
Deploying agents...
Reading source file...
Checking if target exists...
Creating target directory...
Copying content...
Setting permissions...
```
Users don't care about file operations. Show what matters.

### Too Little Context
```
Processing...
Working...
Done.
```
What's being processed? What was accomplished?

### Premature Celebration
```
Almost done!
Just a bit more...
Nearly there...
```
Anxious energy. Just show progress and complete confidently.

### Mechanical Counting
```
Processing item 1 of 12...
Processing item 2 of 12...
Processing item 3 of 12...
```
Show item names, not positions. The list itself shows progress.

---

## Implementation Notes

When implementing progress indicators in skills/commands:

1. **Open with scope** - "Deploying 5 agents...", "Scanning projects..."
2. **Update on meaningful events** - Agent deployed, source updated, project found
3. **Use consistent formatting** - Checkmarks for completion, indents for details
4. **Close with summary** - Confirm what was accomplished, provide counts
5. **Keep voice confident** - State facts, don't narrate anxiety

The goal is for users to glance at progress and immediately understand:
- What's happening
- How far along we are
- What's been accomplished

Not to understand every internal step of the process.
