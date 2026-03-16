# Sources Overview

> How to add and manage capability sources in CAMI.

**Purpose**: Sources are repositories of agents and skills. You can create your own custom sources or add sources from the community.

---

## Creating Your Own Source

The recommended approach is to create custom agents tailored to your workflow:

```
"Create a custom agent for React development"
```

CAMI will guide you through creating an agent specific to your needs.

### Local Sources

Create a local source directory in your workspace:

```
~/cami-workspace/sources/my-agents/
├── agents/
│   └── my-frontend.md
└── skills/
    └── my-patterns/
        └── SKILL.md
```

### Git-Based Sources

Any git repository can be a source:

```
"Add https://github.com/myorg/my-agents as a source"
```

This clones the repository to your workspace's `sources/` directory.

---

## Adding a Source

```
"Add https://github.com/example/agents as a source"
```

Or for local directories:

```
"Add ~/path/to/my-agents as a source"
```

---

## Source Structure

A valid source follows this structure:

```
my-source/
├── agents/           # Agent definitions
│   ├── agent-one.md
│   └── agent-two.md
├── skills/           # Skill definitions
│   └── skill-name/
│       └── SKILL.md
└── README.md         # Optional: source documentation
```

---

## Community Sources

Community sources and official Lando Labs sources are available in the [CAMI Marketplace](https://cami.lando.dev/marketplace).

Browse the marketplace to discover pre-built agents for:
- Web development
- Game development
- Content creation
- DevOps
- And more

---

## When to Use Custom vs Community Sources

| Scenario | Recommendation |
|----------|----------------|
| Learning CAMI patterns | Browse community sources as examples |
| Standard projects | Browse marketplace, customize as needed |
| Unique workflow or domain | Create custom agents from scratch |
| Team-specific conventions | Create custom source for your org |

**Most users**: Create 2-3 custom agents tailored to their project, optionally adding community sources for reference.

---

## Related

- `skills/create-agent/` - Create custom agents
- `skills/manage-sources/` - Add and manage sources
- `reference/voice/scout-persona.md` - CAMI communication style
