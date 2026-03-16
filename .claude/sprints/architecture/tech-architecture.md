# CAMI Plugin - Tech Architecture

> Created: 2026-02-26
> Last Updated: 2026-02-26

## Tech Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| **Runtime** | Claude Code Plugin | Skills + agents architecture |
| **Config Format** | YAML | Matches existing CAMI workspace |
| **Manifest Format** | YAML | Project capability tracking |
| **Distribution** | GitHub | Plugin marketplace |

## Plugin Structure

```
cami-plugin/
├── .claude-plugin/
│   └── plugin.json         # Plugin manifest (v0.1.0)
├── skills/                 # SKILL.md files (5 skills)
├── agents/                 # Agent .md files (2 agents)
├── reference/              # Shared documentation
├── README.md               # User documentation
└── QUICKSTART.md           # Getting started guide
```

## Configuration Architecture

### Current State (Issues #37, #34)

The plugin has config format inconsistencies that need alignment:

**Existing CAMI Workspace Format** (what users have):
```yaml
# ~/cami-workspace/config.yaml
version: "1"
agent_sources:
  - name: my-agents
    type: local
    path: /path/to/sources/my-agents
    priority: 10
    git:
      enabled: false
deploy_locations:
  - name: project-name
    path: /path/to/project
```

**Plugin Documentation** (needs alignment):
- Some skills reference JSON instead of YAML
- Field names inconsistent (`sources` vs `agent_sources`)
- Manifest format varies between skills

## External Dependencies

| Dependency | Purpose | Required |
|------------|---------|----------|
| CAMI Workspace | Agent sources, config | Yes |
| Git | Source synchronization | Optional |
| GitHub CLI (gh) | Source publishing | Optional |

## Bootstrap Configuration

| File | Purpose | Default |
|------|---------|---------|
| `~/.claude/cami.yaml` | Stores workspace path setting | N/A (created during onboarding) |

**Workspace Resolution:**
1. Check `~/.claude/cami.yaml` for `workspace_path` key
2. If not found, use default `~/.claude/cami-workspace`

Skills use `$WORKSPACE` variable which is resolved at runtime using the above logic.

## Integration Points

```
┌─────────────────────────────────────────────────┐
│                 Claude Code                      │
│    ┌─────────────────────────────────────────┐  │
│    │           CAMI Plugin                    │  │
│    │  ┌─────────┐  ┌─────────┐  ┌─────────┐ │  │
│    │  │  Skills │  │ Agents  │  │Reference│ │  │
│    │  └────┬────┘  └────┬────┘  └────┬────┘ │  │
│    └───────┼────────────┼────────────┼──────┘  │
└────────────┼────────────┼────────────┼──────────┘
             │            │            │
    ┌────────▼────────────▼────────────▼────────┐
    │            CAMI Workspace                  │
    │  ~/cami-workspace/                         │
    │  ├── config.yaml                          │
    │  ├── deployments.yaml                     │
    │  └── sources/                             │
    │      ├── my-agents/                       │
    │      └── my-source/                 │
    └───────────────────────────────────────────┘
```

## Version History

| Version | Tag | Notes |
|---------|-----|-------|
| 0.1.0 | v1.0.0-rc.4 | Pre-release, config format issues |

## Update Log

| Date | Sprint | Changes |
|------|--------|---------|
| 2026-02-26 | Initial | Created tech architecture document |
