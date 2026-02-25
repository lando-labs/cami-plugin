# CAMI Plugin - Technical Architecture

> Created: 2026-02-25
> Last Updated: 2026-02-25

## Overview

CAMI is a **skills-only** Claude Code plugin - no binary, no MCP server, just markdown files that Claude Code interprets.

## Tech Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Plugin Format | Claude Code Plugin Spec | Plugin manifest, skill definitions |
| Content | Markdown | SKILL.md files, CLAUDE.md, references |
| Config | YAML | config.yaml for workspace settings |
| VCS | Git | Source management, version tracking |
| Hosting | GitHub | Source repositories, plugin distribution |

## Plugin Specification

### plugin.json
```json
{
  "name": "cami",
  "version": "1.0.0",
  "description": "Agent management for Claude Code",
  "skills": ["manage-capabilities", "manage-sources", "create-agent", "create-project"],
  "agents": ["agent-architect", "skill-architect"]
}
```

### SKILL.md Format
Each skill follows Claude Code's skill specification:
- Trigger phrases
- Workflow steps
- Reference files
- Error handling

## External Dependencies

### Git Operations
Skills use Git for source management:
- Clone repositories (add source)
- Pull updates (update sources)
- Check status (source compliance)

### GitHub API
For official guild discovery:
- List available guilds
- Fetch guild metadata
- Version checking

## Auto-Discovery (Replaces STRATEGIES.yaml)

Instead of requiring user-maintained config, detect tech stack at runtime:

```
Scan project for:
├── package.json    → Node.js, dependencies
├── tsconfig.json   → TypeScript
├── next.config.*   → Next.js
├── tailwind.config.* → Tailwind
├── .firebase/      → Firebase
├── prisma/         → Prisma
└── etc.
```

Save as optional project profile for future sessions.

## File Boundaries

### Core Plugin (this repo)
- `plugin.json` - Plugin manifest
- `CLAUDE.md` - Scout persona
- `skills/*/SKILL.md` - Skill definitions
- `agents/*.md` - Architect agents

### User Workspace (~/cami-workspace)
- `config.yaml` - User configuration
- `sources/` - Cloned capability repos
- Custom agents/skills

### Target Projects
- `.claude/agents/` - Deployed agents
- `.claude/skills/` - Deployed skills
- `CLAUDE.md` - Project instructions (updated by deploy)

## Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                     CAPABILITY SOURCES                       │
│  (GitHub repos: guilds, user repos, local directories)      │
└─────────────────────────┬───────────────────────────────────┘
                          │ git clone/pull
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                   ~/cami-workspace/sources/                  │
│  (Local copies of capability sources)                        │
└─────────────────────────┬───────────────────────────────────┘
                          │ manage-capabilities deploy
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    TARGET PROJECT                            │
│  .claude/agents/*.md   .claude/skills/*/   CLAUDE.md        │
└─────────────────────────────────────────────────────────────┘
```

## Version Tracking

Deployed capabilities include version metadata:
```markdown
<!-- cami:version=1.2.0 source=fullstack-guild deployed=2026-02-25 -->
```

This enables:
- Scan for outdated deployments
- Update available notifications
- Source reconciliation

## Update Log

| Date | Sprint | Changes |
|------|--------|---------|
| 2026-02-25 | Initial | Created tech architecture document |
