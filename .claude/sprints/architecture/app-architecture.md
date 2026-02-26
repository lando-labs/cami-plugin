# CAMI Plugin - Application Architecture

> Created: 2026-02-25
> Last Updated: 2026-02-25

## Overview

CAMI (Claude Agent Management Interface) is a skills-only Claude Code plugin that helps users discover, manage, and deploy AI agents to their projects.

## Core Concept: Capabilities

The plugin treats both agents and skills as "capabilities":
- **Agents** = Methodology experts (judgment, decisions, quality standards)
- **Skills** = Implementation patterns (workflows, syntax)

## Feature Areas

### 1. Scout Persona (CLAUDE.md)
The main entry point that establishes CAMI's identity and routes to skills.
- **Files**: `CLAUDE.md`
- **Purpose**: Guide users to build the right foundation for their projects
- **Voice**: Elite agent scout and orchestrator

### 2. Capability Management (manage-capabilities)
List, deploy, scan, and update agents and skills.
- **Files**: `skills/manage-capabilities/SKILL.md`, `references/`
- **Operations**: list, deploy, scan, update, remove
- **Scope**: Both agents and skills uniformly

### 3. Source Management (manage-sources)
Add, update, and manage capability sources (guilds).
- **Files**: `skills/manage-sources/SKILL.md`, `references/`
- **Operations**: add, update, list, reconcile, check compliance

### 4. Agent Creation (create-agent)
Guide users through agent creation using agent-architect.
- **Files**: `skills/create-agent/SKILL.md`, `references/`
- **Dependencies**: Invokes `agent-architect` agent
- **Decision**: Agent vs skill guidance

### 5. Project Setup (create-project)
Set up new projects with recommended agent teams.
- **Files**: `skills/create-project/SKILL.md`, `references/`
- **Operations**: Initialize project, recommend teams, deploy starters

### 6. Architects (External Agents)
- **agent-architect**: Creates methodology-focused agents (v5.0.0)
- **skill-architect**: Creates implementation skills (v2.0.0)

## Skill Routing

```
User Input
    │
    ▼
CLAUDE.md (Scout Persona)
    │
    ├─ "list agents" ──────────► manage-capabilities
    ├─ "deploy X" ─────────────► manage-capabilities
    ├─ "add source" ───────────► manage-sources
    ├─ "create agent" ─────────► create-agent ──► agent-architect
    ├─ "new project" ──────────► create-project
    └─ "update agents" ────────► manage-capabilities + manage-sources
```

## Plugin Structure

```
cami-plugin/
├── plugin.json              # Plugin manifest
├── CLAUDE.md                # Scout persona + skill routing
├── README.md                # User documentation
├── templates/
│   └── config.yaml          # Workspace config template
├── skills/
│   ├── manage-capabilities/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── manage-sources/
│   │   ├── SKILL.md
│   │   └── references/
│   ├── create-agent/
│   │   ├── SKILL.md
│   │   └── references/
│   └── create-project/
│       ├── SKILL.md
│       └── references/
└── agents/
    ├── agent-architect.md   # v5.0.0
    └── skill-architect.md   # v2.0.0
```

## Workspace Model

Users maintain a workspace at `~/cami-workspace/`:

```
~/cami-workspace/
├── config.yaml              # Sources and settings
├── sources/                 # Cloned capability sources
│   ├── fullstack-guild/
│   ├── game-dev-guild/
│   └── my-agents/
└── projects.yaml            # (optional) Project registry
```

## Update Log

| Date | Sprint | Changes |
|------|--------|---------|
| 2026-02-25 | Initial | Created architecture document |
