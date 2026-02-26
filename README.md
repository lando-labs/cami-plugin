# CAMI Plugin

**Claude Agent Management Interface**

Your agent scout for Claude Code. Build championship teams of specialized AI agents for any project.

```bash
claude plugin install lando-labs/cami
```

---

## What is CAMI?

CAMI is your agent scout and orchestrator. Instead of manually copying agent files between projects, CAMI helps you discover, deploy, and manage teams of specialized Claude Code agents.

Think of it as talent management for AI specialists:
- Browse official guilds and custom sources
- Get recommendations based on your tech stack
- Deploy agents to projects with one command
- Keep your roster updated across all projects

**Skills-only architecture**: No binary installation, no MCP server configuration. Just install the plugin and start talking.

**Zero-config**: Install and go. CAMI creates its workspace automatically when you add your first guild or deploy your first agent.

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

### Minute 2: Get recommendations

```
You: "Yes. Scout this project."

CAMI: "Done! Created your workspace at ~/cami-workspace/

      Looking at your React/TypeScript project... found package.json with React 19.

      For this stack, I'd recommend starting with:
      - frontend-methodology (guides UI architecture decisions)
      - react-specialist (React patterns and best practices)

      These are in the fullstack-guild. Want me to add it and deploy these agents?"
```

### Minute 3: Agents deployed, you're working

```
You: "Yes"

CAMI: "Adding fullstack-guild to your sources... Done!
      Deploying frontend-methodology and react-specialist to my-app... Done!

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
| **manage-sources** | Add and update guilds | "Add fullstack-guild", "Update my sources", "List guilds" |
| **create-agent** | Create custom agents | "Create an agent for code review", "Build me a testing specialist" |
| **create-project** | Set up new projects | "Set up CAMI for this project", "Recommend agents for this stack" |

---

## Official Guilds

These are Lando Labs' official agent collections, ready to add to your workspace:

| Guild | Focus | Add Command | Agents |
|-------|-------|-------------|--------|
| **fullstack-guild** | Web development | `"Add fullstack-guild"` | frontend-methodology, backend-methodology, database-specialist, react-specialist, node-specialist |
| **content-guild** | Writing & marketing | `"Add content-guild"` | content-strategist, technical-writer, marketing-specialist, editor |
| **game-dev-guild** | Game development | `"Add game-dev-guild"` | phaser-specialist, game-design-methodologist, game-systems-architect |

More guilds coming soon. You can also add custom sources from any Git repo.

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
"I'd recommend frontend-methodology because it guides UI architecture
decisions - helpful for a React app with complex state management."
```

You make the call. CAMI executes.

### 4. Workspace Auto-Created

The first time you add a guild or deploy an agent, CAMI creates:

```
~/cami-workspace/
├── config.json      # Auto-generated tracking
└── sources/         # Cloned guild repos
    └── fullstack-guild/
        ├── CLAUDE.md
        ├── frontend-methodology.md
        └── ...
```

You never touch this directly unless you want to. It just works.

---

## Common Workflows

### Add Your First Guild

```
You: "Add the fullstack guild"
CAMI: "Adding fullstack-guild from GitHub... Done!
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

      I'd recommend:
      - backend-methodology (REST API design and architecture)
      - database-specialist (schema design and query optimization)

      Deploy these?"
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

### Create a Custom Agent

```
You: "Create an agent for API versioning strategy"
CAMI: "Let me help you create that agent.

      What domain is this for?
      1. Backend/API design
      2. Frontend integration
      3. DevOps/infrastructure

      Or describe the domain yourself."
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
├── config.json              # Tracks sources and deployments
└── sources/
    ├── fullstack-guild/     # Official guild (git cloned)
    │   ├── .git/
    │   ├── CLAUDE.md        # Guild documentation
    │   ├── STRATEGIES.yaml  # Tech stack guidance
    │   └── *.md             # Agent files
    ├── game-dev-guild/      # Another official guild
    └── custom/              # Your custom agents
        └── my-agent.md
```

### Custom Workspace Location

Override the default location:

```bash
export CAMI_WORKSPACE_PATH=~/my-custom-workspace
```

CAMI will use this path instead of `~/cami-workspace/`.

### config.json Schema

Auto-generated, but you can edit manually if needed:

```json
{
  "version": "1.0.0",
  "sources": [
    {
      "name": "fullstack-guild",
      "path": "~/cami-workspace/sources/fullstack-guild",
      "git": "https://github.com/lando-labs/fullstack-guild.git",
      "priority": 100,
      "added": "2026-02-25T10:30:00Z"
    }
  ],
  "deployments": [
    {
      "project": "~/projects/my-app",
      "agents": [
        "fullstack-guild:frontend-methodology",
        "fullstack-guild:backend-methodology"
      ],
      "lastUpdated": "2026-02-25T10:45:00Z"
    }
  ]
}
```

**Priority System**: Lower numbers win. Custom agents default to priority 10 (highest), guilds default to 100 (lowest). Customize when adding sources:

```
You: "Add my team's guild with priority 20"
CAMI: "Adding team-guild with priority 20... Done!"
```

### Adding Custom Sources

Add any Git repo as a source:

```
You: "Add https://github.com/my-org/agents.git as my-team-agents"
CAMI: "Cloning my-team-agents from GitHub... Done!
      Found 3 agents available.
      Set priority to 50 (medium). Want to change it?"
```

### Manual Deployment Tracking

CAMI tracks what's deployed where automatically, but you can check the state:

```json
// ~/cami-workspace/config.json
{
  "deployments": [
    {
      "project": "~/projects/web-app",
      "agents": [
        "fullstack-guild:frontend-methodology",
        "custom:team-conventions"
      ],
      "lastUpdated": "2026-02-25T12:30:00Z"
    }
  ]
}
```

Edit this manually if needed (e.g., after manually copying agents).

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
You: "Add the fullstack guild"
```

### "An agent isn't showing up"

Check your sources:

```
You: "List my sources"
CAMI: "You have 2 sources configured:
      - fullstack-guild (5 agents)
      - custom (2 agents)"
```

If the guild you need isn't listed, add it:

```
You: "Add game-dev-guild"
```

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
- **Contribute code**: [Contribution Guidelines](https://github.com/lando-labs/cami-plugin/blob/main/CONTRIBUTING.md)

### Creating Your Own Guilds

Want to create an official guild or share your custom agents? See the [Guild Creation Guide](https://github.com/lando-labs/cami-plugin/blob/main/docs/creating-guilds.md).

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
