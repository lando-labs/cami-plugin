# CAMI Plugin - App Architecture

> Created: 2026-02-26
> Last Updated: 2026-02-26

## Overview

CAMI (Claude Agent Management Interface) is a Claude Code plugin that provides agent management capabilities through a skill-based architecture. It enables users to scout, create, and deploy agents across projects.

## Feature Areas

| Area | Description | Key Files |
|------|-------------|-----------|
| **Entry Point** | Main CAMI skill, persona, routing | `skills/cami/` |
| **Capabilities** | Deploy, scan, import agents/skills | `skills/manage-capabilities/` |
| **Sources** | Add, update, reconcile agent sources | `skills/manage-sources/` |
| **Agent Creation** | Two-class agent system, roster building | `skills/create-agent/` |
| **Project Setup** | Initialize projects with capabilities | `skills/create-project/` |

## Skills Architecture

```
skills/
├── cami/                   # Entry point - persona + location routing
│   └── SKILL.md
├── manage-capabilities/    # Deploy, scan, import agents/skills
│   └── SKILL.md
├── manage-sources/         # Add, update, reconcile sources
│   └── SKILL.md
├── create-agent/           # Two-class agent creation
│   ├── SKILL.md
│   └── triggers/
└── create-project/         # Project initialization
    ├── SKILL.md
    └── triggers/
```

## Agents

```
agents/
├── agent-architect.md      # Creates methodology-specialist/strategic-planner agents
└── skill-architect.md      # Creates workflow skills
```

## Reference Documentation

```
reference/
├── config-schema.md        # Configuration format documentation
├── tech-detection.md       # Tech stack auto-detection
├── voice/
│   ├── scout-persona.md    # CAMI's scout voice
│   └── location-protocol.md # Location detection
├── ux/
│   └── skill-architecture-spec.md
├── patterns/               # Common patterns
└── sources/                # Source management patterns
```

## Core Concepts

### Capabilities
Both agents and skills are "capabilities":
- **Agents**: Methodology experts (judgment, decisions, quality standards)
- **Skills**: Implementation patterns (workflows, syntax, checklists)

### Two-Class Agent System
- **methodology-specialist**: Domain expertise + skill pairing
- **strategic-planner**: Architecture, research, decisions

### Location Modes
- **Workspace mode**: In ~/cami-workspace/ → source operations
- **Project mode**: In project with .claude/ → capability operations
- **Initialization mode**: In project without .claude/ → setup
- **Navigation mode**: Unclear location → guide user

## Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| `config.yaml` | `~/cami-workspace/` | Workspace configuration |
| `deployments.yaml` | `~/cami-workspace/` | Deployment tracking |
| `cami-manifest.yaml` | Project `.claude/` | Project capability manifest |

## Dependencies

- Claude Code plugin system
- User's CAMI workspace (`~/cami-workspace/` or `$CAMI_WORKSPACE_PATH`)

## Update Log

| Date | Sprint | Changes |
|------|--------|---------|
| 2026-02-26 | Initial | Created architecture document |
