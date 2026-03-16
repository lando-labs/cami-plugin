# CAMI Plugin

**Claude Capability Management Interface**

Your agent scout for Claude Code. Build championship teams of specialized AI agents for any project.

```bash
claude plugin install lando-labs/cami
```

---

## What is CAMI?

CAMI is your agent scout and orchestrator. Instead of manually copying agent files between projects, CAMI helps you discover, deploy, and manage teams of specialized Claude Code agents.

Think of it as talent management for AI specialists:
- Create custom agents tailored to your projects
- Browse community sources when you need them
- Get recommendations based on your tech stack
- Deploy agents to projects with one command
- Keep your roster updated across all projects

**Skills-only architecture**: No binary installation, no MCP server configuration. Just install the plugin and start talking.

**Zero-config**: Install and go. CAMI creates its workspace automatically when you create your first agent, add your first source, or deploy your first agent.

---

## Quick Start (Zero-Config)

### Minute 1: Install and say hi

```bash
claude plugin install lando-labs/cami
```

Then in any Claude Code session:

```
You: "Hey CAMI"

CAMI: "Hey! I'm CAMI - your agent scout. I help you build championship teams
      of AI agents for your projects.

      I notice you haven't set up your workspace yet. This is your command center
      for managing agent sources - think of it as your talent network.

      Ready to set it up? [y/n]"
```

### Minute 2: What are you building?

**Option A: Create a custom agent (Recommended)**

```
You: "I'm building a React dashboard with complex state management"

CAMI: "Perfect! Let me create a custom agent for you.

      I'll make a state-management-specialist that understands:
      - React Context vs Redux vs Zustand trade-offs
      - Performance optimization patterns
      - Testing stateful components

      Sound good?"

You: "Yes"

CAMI: "Creating your workspace at ~/cami-workspace/
      Creating state-management-specialist in sources/my-agents/... Done!

      Ready to deploy it to this project?"
```

**Option B: Browse community sources**

```
You: "Yes. Scout this project."

CAMI: "Done! Created your workspace at ~/cami-workspace/

      Looking at your React/TypeScript project... found package.json with React 19.

      For this stack, I'd recommend creating:
      - A frontend-methodology agent (guides UI architecture decisions)
      - A react-patterns skill (React best practices)

      Want me to create these for you? Or browse community sources first?"
```

### Minute 3: Agents deployed, you're working

```
You: "Yes, create them"

CAMI: "Creating frontend-methodology in my-agents...
      Creating react-patterns skill...
      Deploying to my-app... Done!

      Your roster is set - ready to build.
      Try @frontend-methodology to start planning your architecture."
```

No config files. No setup commands. Just conversation.

---

## Installation

### From Marketplace (Recommended)

```bash
claude plugin install lando-labs/cami
```

### Local Development

```bash
git clone https://github.com/lando-labs/cami-plugin.git
claude --plugin-dir /path/to/cami-plugin
```

---

## Available Skills

CAMI operates through conversational skills. You don't need to invoke them explicitly - just talk naturally.

| Skill | Purpose | Example Phrases |
|-------|---------|-----------------|
| **cami** | Entry point - greetings, help, routing | "Hey CAMI", "What can you do?", "Help me with agents" |
| **manage-capabilities** | Deploy and scan agents | "Deploy frontend agent", "Check for updates", "List my roster" |
| **manage-sources** | Add sources or create custom agents | "Add a source", "Create a custom agent", "Update my sources", "List sources" |
| **create-agent** | Create custom agents | "Create an agent for code review", "Build me a testing specialist" |
| **create-project** | Set up new projects | "Set up CAMI for this project", "Recommend agents for this stack" |

---

## Community Sources

Pre-built agents are available through the [CAMI Marketplace](https://cami.lando.dev/marketplace).

**Most users create custom agents** tailored to their specific projects. Community sources are helpful when you need a starting point or want to explore what's possible.

Add any Git repository as a source:

```
You: "Add https://github.com/example/agents as a source"

CAMI: "Adding example-agents from GitHub... Done!
      Found 5 agents available."
```

---

## How It Works

### 1. You Talk to CAMI

Describe what you need, where you are, what you're building:

```
"I'm building a Next.js app and need help with architecture."
"Scout this project and recommend agents."
"What agents do I have deployed here?"
"Create a custom agent for API design."
```

### 2. CAMI Scouts

CAMI analyzes your project structure, tech stack, and needs:
- Detects frameworks and languages from your project files
- Understands your current roster of agents
- Knows what specialists are available from your sources
- Recommends the right composition for your project

### 3. You Approve

CAMI always explains what it's doing and why:

```
"I'd recommend a frontend-methodology agent because it guides UI architecture
decisions - helpful for a React app with complex state management."
```

You make the call. CAMI executes.

### 4. Workspace Auto-Created

The first time you create an agent, add a source, or deploy an agent, CAMI creates:

```
~/cami-workspace/
├── config.yaml      # Auto-generated tracking
└── sources/
    └── my-agents/               # Your custom agents (priority 10)
        ├── agents/
        │   ├── state-management-specialist.md
        │   └── api-design-specialist.md
        └── skills/
            └── react-patterns/
                └── SKILL.md
```

You never touch this directly unless you want to. It just works.

---

## Common Workflows

### Create Your First Custom Agent

```
You: "Create an agent for API design"
CAMI: "Let me help you create an API design specialist.

      What aspects should this agent focus on?
      - RESTful design principles
      - Authentication/authorization patterns
      - Versioning strategies
      - Documentation standards

      Or describe the focus yourself."

You: "All of those, plus GraphQL vs REST trade-offs"

CAMI: "Creating api-design-specialist in your my-agents source... Done!

      The agent is ready to help with API architecture decisions,
      design patterns, and best practices.

      Deploy it to your current project?"
```

### Add a Community Source

```
You: "Add https://github.com/example/web-agents as a source"
CAMI: "Adding web-agents from GitHub... Done!
      Found 5 agents available:
      - frontend-methodology
      - backend-methodology
      - database-specialist
      - react-specialist
      - node-specialist

      Want me to recommend agents for your current project?"
```

### Scout a New Project

```
You: "Scout this project"
CAMI: "Looking at your Go + PostgreSQL project...

      I'd recommend creating:
      - backend-methodology (REST API design and architecture)
      - database-specialist (schema design and query optimization)

      Want me to create these for you?"
```

### Check Your Roster

```
You: "What agents do I have?"
CAMI: "You're in my-app. 3 agents deployed:
      - frontend-methodology (v1.2.0, current)
      - react-specialist (v1.1.0, current)
      - database-specialist (v1.0.5, update available)

      Want me to update database-specialist?"
```

### Update All Agents

```
You: "Update my agents"
CAMI: "Checking all deployed agents...

      2 updates available:
      - database-specialist: 1.0.5 → 1.1.0 (schema migration patterns added)
      - backend-methodology: 2.0.1 → 2.1.0 (GraphQL guidance added)

      Update both?"
```

---

## For Power Users

### Workspace Structure

CAMI stores everything in `~/cami-workspace/`:

```
~/cami-workspace/
├── config.yaml              # Tracks sources and deployments
├── deployments.yaml         # Per-project deployment tracking
└── sources/
    ├── my-agents/           # Your custom agents (priority 10)
    │   ├── agents/
    │   │   ├── api-design-specialist.md
    │   │   └── state-management-specialist.md
    │   └── skills/
    │       └── my-patterns/
    │           └── SKILL.md
    └── team-agents/         # Team source (git cloned, custom priority)
        ├── .git/
        ├── CLAUDE.md        # Source documentation
        └── agents/
            └── *.md
```

### Custom Workspace Location

Override the default location:

```bash
export CAMI_WORKSPACE_PATH=~/my-custom-workspace
```

CAMI will use this path instead of `~/cami-workspace/`.

### config.yaml Schema

Auto-generated, but you can edit manually if needed:

```yaml
version: "2"
created_at: 2026-02-25T10:30:00Z
updated_at: 2026-02-25T10:45:00Z

sources:
  - name: my-agents
    path: ~/cami-workspace/sources/my-agents
    priority: 10
    added_at: 2026-02-25T10:30:00Z

  - name: team-agents
    path: ~/cami-workspace/sources/team-agents
    priority: 50
    added_at: 2026-02-25T10:35:00Z
    git:
      url: https://github.com/my-org/team-agents.git
      branch: main
```

**Priority System**: Lower numbers win. Custom agents default to priority 10 (highest), community sources default to 100 (lowest). This ensures your custom agents are the primary choice. Customize when adding sources:

```
You: "Add my team's source with priority 20"
CAMI: "Adding team-agents with priority 20... Done!"
```

### Adding Custom Sources

Add any Git repo as a source:

```
You: "Add https://github.com/my-org/agents.git as my-team-agents"
CAMI: "Cloning my-team-agents from GitHub... Done!
      Found 3 agents available.
      Set priority to 50 (medium). Want to change it?"
```

---

## Troubleshooting

### "I don't see CAMI responding"

Make sure the plugin is installed:

```bash
claude plugin list
```

Should show `cami` in the list. If not:

```bash
claude plugin install lando-labs/cami
```

### "CAMI can't find my workspace"

Check if the workspace exists:

```bash
ls -la ~/cami-workspace/
```

If it doesn't exist, CAMI will create it on your first action. Try:

```
You: "Create an agent for testing"
```

### "An agent isn't showing up"

Check your sources:

```
You: "List my sources"
CAMI: "You have 2 sources configured:
      - my-agents (2 agents) - priority 10
      - team-agents (5 agents) - priority 50"
```

If the source you need isn't listed, add it.

### "Custom workspace path not working"

Make sure the environment variable is set in your shell config:

```bash
# Add to ~/.bashrc or ~/.zshrc
export CAMI_WORKSPACE_PATH=~/my-custom-workspace
```

Restart your shell or source the config:

```bash
source ~/.zshrc
```

---

## Contributing

CAMI is open source and welcomes contributions:

- **Report bugs**: [GitHub Issues](https://github.com/lando-labs/cami-plugin/issues)
- **Suggest features**: [GitHub Discussions](https://github.com/lando-labs/cami-plugin/discussions)
- **Contribute code**: See [CONTRIBUTING.md](CONTRIBUTING.md)

---

## License

MIT License - see [LICENSE](LICENSE) for details.

---

## Credits

Built with championship-level standards by [Lando Labs](https://lando-labs.com).

**Powered by Claude Code** - the AI assistant for developers.

---

**Ready to build your team?**

```bash
claude plugin install lando-labs/cami
```

Then start any Claude Code session and say:

```
"Hey CAMI"
```

Your scout is waiting.
