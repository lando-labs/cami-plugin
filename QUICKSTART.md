# CAMI Plugin - Quickstart Guide

CAMI helps you create and manage AI specialists (called **agents**) that assist with specific tasks in your coding projects. Instead of manually setting up Claude for each project, CAMI scouts your codebase, recommends relevant specialists, and deploys them with a single command.

**This guide walks through installation and verification** on a fresh machine.

---

## Key Terms

Before diving in, here's what the terminology means:

- **Agent**: A markdown file giving Claude specialized expertise (like a "React expert")
- **Skill**: A workflow automation (like "create PR with tests")
- **Source**: A folder or Git repo containing agents/skills - your talent pool
- **Workspace**: CAMI's home base where sources and config live (`~/.claude/cami-workspace/`)
- **Deploy**: Copy an agent to a project so it's available there
- **Roster**: The agents deployed to a specific project - your team lineup

See the [README glossary](README.md#glossary) for detailed definitions.

---

## Prerequisites

1. **Claude Code CLI** installed and authenticated
   ```bash
   # Verify installation
   claude --version

   # Should show version 1.x or higher
   ```

2. **Git** installed (for source management)
   ```bash
   git --version
   ```

3. **A test project** with some code (or create one)
   ```bash
   mkdir ~/test-project && cd ~/test-project
   npm init -y  # or any project setup
   ```

---

## Installation Options

### Option A: From GitHub (Recommended for Testing)

```bash
# Clone the plugin
git clone https://github.com/lando-labs/cami-plugin.git ~/cami-plugin

# Run Claude with the plugin loaded
claude --plugin-dir ~/cami-plugin
```

### Option B: From Marketplace (When Published)

```bash
claude plugin install lando-labs/cami
```

---

## Verification Checklist

Run these tests to verify full functionality:

### Test 1: Plugin Loaded

```bash
claude --plugin-dir ~/cami-plugin
```

In the session:
```
You: "Hey CAMI"
```

**Expected**: CAMI responds with greeting and workspace setup prompt.

**Pass criteria**: Scout persona voice, location awareness shown.

---

### Test 2: Workspace Creation

```
You: "Yes, set up my workspace"
```

**Expected**: Creates `~/.claude/cami-workspace/` with:
- `config.json`
- `sources/` directory

**Verify**:
```bash
ls -la ~/.claude/cami-workspace/
```

---

### Test 3: Custom Agent Creation

```
You: "Create an agent for testing React components"
```

**Expected**:
1. CAMI asks clarifying questions about focus areas
2. Creates agent in `~/.claude/cami-workspace/sources/custom/`
3. Offers to deploy

**Verify**:
```bash
ls ~/.claude/cami-workspace/sources/custom/
# Should show: testing-react-components.md (or similar)
```

---

### Test 4: Agent Deployment

Navigate to your test project:
```bash
cd ~/test-project
claude --plugin-dir ~/cami-plugin
```

```
You: "Deploy my testing agent to this project"
```

**Expected**:
1. Agent copied to `.claude/agents/`
2. Manifest created/updated
3. Confirmation message

**Verify**:
```bash
ls ~/test-project/.claude/agents/
# Should show your deployed agent
```

---

### Test 5: Source Addition

```
You: "Add https://github.com/example/web-agents as a source"
```

**Expected**:
1. Clones from GitHub
2. Shows available agents
3. Offers recommendations

**Verify**:
```bash
ls ~/.claude/cami-workspace/sources/
# Should show: my-agents/, web-agents/
```

---

### Test 6: Roster Check

```
You: "What agents do I have deployed?"
```

**Expected**: Lists deployed agents with versions and update status.

---

### Test 7: Project Scouting

```
You: "Scout this project and recommend agents"
```

**Expected**:
1. Analyzes tech stack (detects package.json, etc.)
2. Recommends relevant agents from sources
3. Offers to deploy

---

## Common Issues

### "CAMI not responding"

1. Verify plugin loaded:
   ```bash
   # Check Claude started with plugin flag
   claude --plugin-dir ~/cami-plugin --debug
   ```

2. Check skill files exist:
   ```bash
   ls ~/cami-plugin/skills/
   # Should show: cami/, create-agent/, create-project/, etc.
   ```

### "Workspace not found"

CAMI creates workspace on first action. Try:
```
You: "Create an agent for testing"
```

### "Agent not deploying"

Check project has `.claude/` directory:
```bash
mkdir -p ~/test-project/.claude/agents
```

Then retry deployment.

### "Source clone failing"

Verify git access:
```bash
git clone https://github.com/example/test-repo.git /tmp/test-clone
```

If auth required, configure git credentials.

---

## Cleanup (Optional)

To start fresh:

```bash
# Remove workspace
rm -rf ~/.claude/cami-workspace

# Remove deployed agents from test project
rm -rf ~/test-project/.claude/agents/*

# Remove plugin clone
rm -rf ~/cami-plugin
```

---

## Full Test Scenario

**5-minute end-to-end test**:

```bash
# 1. Clone plugin
git clone https://github.com/lando-labs/cami-plugin.git ~/cami-plugin

# 2. Create test project
mkdir -p ~/test-project && cd ~/test-project
echo '{"name": "test", "dependencies": {"react": "^19.0.0"}}' > package.json

# 3. Start Claude with plugin
claude --plugin-dir ~/cami-plugin
```

In session:
```
You: "Hey CAMI"
# → Sets up workspace

You: "Create an agent for React state management"
# → Creates custom agent

You: "Deploy it here"
# → Deploys to test-project

You: "List my sources"
# → Shows available sources

You: "What's my roster?"
# → Shows all deployed agents

You: "Scout this project"
# → Recommends additional agents based on React stack
```

---

## Success Criteria

All tests pass when:

- [ ] CAMI responds with scout persona
- [ ] Workspace created at `~/.claude/cami-workspace/`
- [ ] Custom agent created in `sources/custom/`
- [ ] Agent deployed to project `.claude/agents/`
- [ ] Source can be added from Git URL
- [ ] Roster shows deployed agents
- [ ] Tech detection works on project

---

## Reporting Issues

If tests fail, collect:

1. Claude Code version: `claude --version`
2. Plugin structure: `ls -la ~/cami-plugin/`
3. Error messages from session
4. Debug output: `claude --plugin-dir ~/cami-plugin --debug`

Report at: https://github.com/lando-labs/cami-plugin/issues
