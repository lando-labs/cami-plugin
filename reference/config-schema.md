# CAMI Configuration Schema

**Version**: 1.0.0
**Location**: `~/cami-workspace/config.json`
**Created**: Auto-generated on first meaningful action

---

## UX Philosophy: Config is Output, Not Input

**Core Principle**: Configuration is an **output** of using CAMI, not a **prerequisite** to getting started.

**Zero-Config Onboarding**:
```
Install CAMI → Use CAMI → Config auto-generated behind the scenes
```

Users should **never** be asked to:
- Copy/edit a config template
- Manually create directory structures
- Initialize a workspace
- Set up config files before using CAMI

Instead, CAMI creates its configuration **automatically** when users perform their first meaningful action:
- Create their first custom agent
- Deploy their first agent
- Add their first source

The user's first experience should be:
```
User: "Create a frontend specialist for React"
Claude: *Creates agent, auto-creates ~/cami-workspace/config.json behind the scenes*
Claude: "Created frontend-specialist in your custom source"
```

**NOT**:
```
User: "Create a frontend specialist"
Claude: "First, you need to initialize your workspace..."
```

---

## Auto-Creation Triggers

Config file is created when the user:

1. **Creates First Custom Agent**:
   - Trigger: Agent creation via agent-architect skill
   - Creates: `~/cami-workspace/config.json` + `~/cami-workspace/sources/custom/` directory
   - Priority: 10 (highest priority - custom agents are the intended primary)

2. **Deploys First Agent**:
   - Trigger: `deploy_agents` or "deploy frontend agent to my project"
   - Creates: Config if missing, adds deployment entry
   - Updates: Deployment tracking for the target project

3. **Adds First Source**:
   - Trigger: `add_source` or "add the X source"
   - Creates: Config if missing, adds source to `~/cami-workspace/sources/` directory
   - Initial state: One source entry, no deployments

**Implementation Detail**:
```javascript
// Pseudo-code for auto-creation logic
function ensureConfig() {
  const configPath = expandPath('~/cami-workspace/config.json');
  if (!exists(configPath)) {
    createDirectory('~/cami-workspace/sources/');
    writeFile(configPath, {
      version: "1.0.0",
      sources: [],
      deployments: []
    });
  }
}

// Called before any operation that needs config
function addSource(name, gitUrl) {
  ensureConfig(); // Silent, automatic
  // ... rest of add logic
}
```

---

## config.json Schema

### Complete Structure

```json
{
  "version": "1.0.0",
  "sources": [
    {
      "name": "fullstack-source",
      "path": "~/cami-workspace/sources/fullstack-source",
      "git": "https://github.com/lando-labs/fullstack-source.git",
      "priority": 100,
      "added": "2026-02-25T10:30:00Z"
    },
    {
      "name": "custom",
      "path": "~/cami-workspace/sources/custom",
      "git": null,
      "priority": 10,
      "added": "2026-02-25T11:00:00Z"
    }
  ],
  "deployments": [
    {
      "project": "~/projects/my-app",
      "agents": [
        "fullstack-source:frontend-methodology",
        "custom:my-team-agent"
      ],
      "lastUpdated": "2026-02-25T10:30:00Z"
    }
  ]
}
```

### Field Definitions

#### Top-Level Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | Yes | Schema version (semver), currently "1.0.0" |
| `sources` | array | Yes | Array of agent source definitions |
| `deployments` | array | Yes | Array of deployment tracking entries |

#### Source Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Source identifier (kebab-case, unique) |
| `path` | string | Yes | Absolute or tilde-expanded path to source directory |
| `git` | string \| null | Yes | Git clone URL, or `null` for local-only sources |
| `priority` | number | Yes | Priority for conflict resolution (lower wins) |
| `added` | string | Yes | ISO 8601 timestamp when source was added |

**Source Priority Defaults**:
- Custom agents: **10** (highest priority - this is the intended primary path)
- Team/organization sources: **50** (medium priority)
- Public sources: **100** (lowest priority, default)

#### Deployment Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `project` | string | Yes | Absolute or tilde-expanded path to project directory |
| `agents` | array | Yes | Array of deployed agent identifiers (`source:agent-name`) |
| `lastUpdated` | string | Yes | ISO 8601 timestamp of last deployment change |

**Agent Identifier Format**: `source-name:agent-name`
- Example: `fullstack-source:frontend-methodology`
- Example: `custom:my-agent`

---

## Priority System

### How Priority Works

When the **same agent name** exists in multiple sources, the source with the **lower priority number wins**.

**Priority Tiers**:
```
10  = Custom agents (user-created - the intended primary path)
50  = Team/organization sources (medium priority)
100 = Public sources (lowest priority, default)
```

### Example Scenario

**Sources**:
```json
{
  "sources": [
    {
      "name": "custom",
      "priority": 10,
      "path": "~/cami-workspace/sources/custom"
    },
    {
      "name": "my-team",
      "priority": 50,
      "path": "~/cami-workspace/sources/my-team"
    },
    {
      "name": "fullstack-source",
      "priority": 100,
      "path": "~/cami-workspace/sources/fullstack-source"
    }
  ]
}
```

**Agent Resolution**:

If `frontend.md` exists in all three sources:
1. CAMI uses `custom/frontend.md` (priority 10 - user's own agents win)
2. If custom doesn't have it, uses `my-team/frontend.md` (priority 50)
3. If neither have it, uses `fullstack-source/frontend.md` (priority 100)

**User Control**:

Users can override priority when adding sources:
```
User: "Add the fullstack source with priority 20"
Claude: *Adds source with priority 20 instead of default 100*
```

---

## Workspace Structure

CAMI auto-creates this structure when needed:

```
~/cami-workspace/
├── config.json              ← Auto-created on first create/deploy/source-add
└── sources/                 ← Auto-created with config.json
    ├── custom/              ← Auto-created when first custom agent created (primary path)
    │   ├── my-agent.md      ← User-created agents
    │   └── ...
    ├── fullstack-source/    ← Git cloned when source added
    │   ├── .git/
    │   ├── CLAUDE.md
    │   ├── STRATEGIES.yaml
    │   ├── frontend.md
    │   ├── backend.md
    │   └── ...
    └── game-dev-source/     ← Git cloned when source added
        └── ...
```

**Directory Creation Rules**:
- `~/cami-workspace/` created when config first needed
- `sources/` created alongside config.json
- `sources/custom/` created when first custom agent created (most common first action)
- `sources/[source-name]/` created when source added (git clone)

**No Manual Setup Required**: Users never create directories manually.

---

## Environment Variables

### CAMI_WORKSPACE_PATH

**Purpose**: Override default workspace location

**Default**: `~/cami-workspace`

**Usage**:
```bash
# Use a different workspace location
export CAMI_WORKSPACE_PATH=~/my-custom-workspace

# CAMI will use this location instead
cami create agent frontend-specialist
# Creates: ~/my-custom-workspace/config.json
```

**Use Cases**:
- Multi-workspace setups (work vs personal)
- Shared team workspaces (network drives)
- Testing/development (temporary workspaces)

**Config Path Resolution**:
```javascript
const workspacePath = process.env.CAMI_WORKSPACE_PATH || '~/cami-workspace';
const configPath = path.join(workspacePath, 'config.json');
```

---

## Key Design Decisions

### 1. JSON, Not YAML

**Decision**: Use JSON for config file

**Rationale**:
- **Claude Code Convention**: Claude Code uses JSON for plugin.json, marketplace.json
- **Consistency**: Matches ecosystem standards
- **Tooling**: Better IDE support, validation, parsing
- **Simplicity**: Less ambiguity than YAML (no indentation issues)

**Trade-off**: Less human-friendly than YAML (no comments, strict syntax)

**Mitigation**: Config is mostly auto-generated, users rarely edit manually

### 2. Versioned Schema

**Decision**: Include `"version": "1.0.0"` in config

**Rationale**:
- **Future Migrations**: Can detect old config format and auto-migrate
- **Breaking Changes**: Can introduce v2.0.0 with different structure
- **Tooling**: Can validate against specific schema version

**Example Migration**:
```javascript
function loadConfig(configPath) {
  const config = JSON.parse(readFile(configPath));

  if (config.version === "1.0.0") {
    return config; // Current version
  } else if (config.version === "0.9.0") {
    return migrateFrom090(config); // Auto-upgrade
  } else {
    throw new Error(`Unknown config version: ${config.version}`);
  }
}
```

### 3. Flat Structure

**Decision**: Keep config flat (sources + deployments arrays)

**Rationale**:
- **Easy Editing**: Users can manually edit if needed
- **Simple Queries**: No deep nesting to traverse
- **Clear Ownership**: Each array has single responsibility

**Alternative Considered**: Nested structure (sources contain deployments)
```json
{
  "sources": [
    {
      "name": "fullstack-source",
      "deployments": [...]  // ❌ Rejected - couples sources to deployments
    }
  ]
}
```

**Why Rejected**: A deployment uses agents from **multiple sources**. Nesting couples them incorrectly.

### 4. ISO 8601 Timestamps

**Decision**: Use `"2026-02-25T10:30:00Z"` format for all timestamps

**Rationale**:
- **Unambiguous**: No timezone confusion
- **Sortable**: Lexicographic sort works correctly
- **Standard**: Widely supported, JSON-friendly

**Fields Using Timestamps**:
- `source.added` - When source was added
- `deployment.lastUpdated` - When deployment last changed

### 5. Agent Identifier Format

**Decision**: Use `source:agent-name` format

**Rationale**:
- **Explicit Source**: Clear which source provides the agent
- **Conflict Resolution**: Priority system can resolve duplicates
- **Namespacing**: Prevents collisions between sources

**Examples**:
- `fullstack-source:frontend-methodology`
- `game-dev-source:phaser-specialist`
- `custom:my-custom-agent`

**Deployment Tracking**:
```json
{
  "deployments": [
    {
      "project": "~/projects/my-app",
      "agents": [
        "fullstack-source:frontend-methodology",
        "fullstack-source:backend-methodology",
        "custom:my-team-conventions"
      ]
    }
  ]
}
```

### 6. Tilde Expansion

**Decision**: Support `~` in paths (`~/cami-workspace`, `~/projects/my-app`)

**Rationale**:
- **User-Friendly**: Shorter, more readable paths
- **Portability**: Works across different usernames/systems
- **Convention**: Standard in Unix/Linux tooling

**Implementation**: Must expand tilde before filesystem operations
```javascript
function expandPath(p) {
  return p.replace(/^~/, os.homedir());
}
```

---

## Validation Rules

### Schema Validation

When loading `config.json`, validate:

1. **Required Fields**:
   - `version` is present and valid semver
   - `sources` is an array
   - `deployments` is an array

2. **Source Validation**:
   - Each source has `name`, `path`, `git`, `priority`, `added`
   - `name` is unique across all sources
   - `priority` is a positive integer
   - `added` is valid ISO 8601 timestamp

3. **Deployment Validation**:
   - Each deployment has `project`, `agents`, `lastUpdated`
   - `project` is a valid path
   - `agents` is an array of strings
   - Agent identifiers follow `source:agent-name` format
   - Referenced sources exist in `sources` array

### Example Validation Errors

**Invalid JSON**:
```
Error: Config file is not valid JSON
Location: ~/cami-workspace/config.json:12
Details: Unexpected token '}' in JSON at position 245
```

**Missing Required Field**:
```
Error: Invalid config schema
Missing required field: sources[0].priority
Source: fullstack-source
```

**Invalid Source Reference**:
```
Error: Deployment references unknown source
Agent: unknown-source:some-agent
Project: ~/projects/my-app
Available sources: fullstack-source, custom
```

---

## Migration & Upgrades

### From MCP Server config.yaml to Plugin config.json

The original CAMI MCP server used `config.yaml`. This plugin uses `config.json`.

**Migration Strategy**:

1. **Detection**: Check for `~/cami-workspace/config.yaml`
2. **Parse**: Read YAML config
3. **Transform**: Convert to JSON schema
4. **Write**: Write `config.json`
5. **Backup**: Rename `config.yaml` to `config.yaml.backup`

**Field Mapping**:

| YAML (MCP) | JSON (Plugin) | Notes |
|------------|---------------|-------|
| `workspace_path` | _implicit_ | Always `~/cami-workspace` |
| `agent_sources` | `sources` | Array of source objects |
| `agent_sources[].path` | `sources[].path` | Direct mapping |
| `agent_sources[].git_url` | `sources[].git` | Renamed field |
| `deploy_locations` | _omitted_ | Location management deferred |

**Example Migration**:

```yaml
# config.yaml (old)
workspace_path: ~/cami-workspace
agent_sources:
  - path: ~/cami-workspace/sources/fullstack-source
    git_url: https://github.com/lando-labs/fullstack-source.git
```

```json
// config.json (new)
{
  "version": "1.0.0",
  "sources": [
    {
      "name": "fullstack-source",
      "path": "~/cami-workspace/sources/fullstack-source",
      "git": "https://github.com/lando-labs/fullstack-source.git",
      "priority": 100,
      "added": "2026-02-25T10:30:00Z"
    }
  ],
  "deployments": []
}
```

### Future Schema Versions

When introducing breaking changes, increment version:

**v1.0.0 → v2.0.0**:
- Detect old version in config
- Auto-migrate to new structure
- Preserve user data
- Backup old config

**Example**:
```javascript
if (config.version === "1.0.0") {
  // Backup old config
  fs.copyFileSync('config.json', 'config.json.v1.backup');

  // Migrate to v2
  const v2Config = {
    version: "2.0.0",
    // New structure
  };

  fs.writeFileSync('config.json', JSON.stringify(v2Config, null, 2));
}
```

---

## Implementation Checklist

### Phase 1: Auto-Creation (MVP)

- [ ] Detect if config exists before any operation
- [ ] Auto-create `~/cami-workspace/config.json` if missing
- [ ] Auto-create `~/cami-workspace/sources/` directory
- [ ] Initialize with empty sources and deployments arrays
- [ ] Silent operation (no user prompt, no confirmation)

### Phase 2: Source Management

- [ ] Add source to config when source added
- [ ] Set priority based on source type (custom=10, team=50, source=100)
- [ ] Record ISO 8601 timestamp in `added` field
- [ ] Support custom priority via user override

### Phase 3: Deployment Tracking

- [ ] Record deployment when agents deployed
- [ ] Track project path and agent list
- [ ] Update `lastUpdated` timestamp on changes
- [ ] Support multiple agents per project

### Phase 4: Priority Resolution

- [ ] Implement priority-based agent resolution
- [ ] Handle agent name conflicts (lowest priority wins)
- [ ] Warn user when priority override happens
- [ ] Allow priority customization

### Phase 5: Environment Variables

- [ ] Support `CAMI_WORKSPACE_PATH` override
- [ ] Expand tilde in all path operations
- [ ] Validate custom workspace paths

### Phase 6: Validation

- [ ] Validate JSON schema on load
- [ ] Check required fields
- [ ] Validate source uniqueness
- [ ] Validate deployment references
- [ ] Provide helpful error messages

### Phase 7: Migration

- [ ] Detect old `config.yaml` format
- [ ] Auto-migrate to `config.json`
- [ ] Backup old config
- [ ] Preserve all user data

---

## Testing Scenarios

### Auto-Creation Tests

1. **Fresh Install**:
   - No `~/cami-workspace/` exists
   - User creates first custom agent
   - Config auto-created with custom source
   - No errors, no prompts

2. **Config Exists, Directory Missing**:
   - `~/cami-workspace/config.json` exists but corrupted
   - `sources/` directory deleted
   - CAMI recreates missing directories
   - Preserves valid config data

3. **Custom Workspace Path**:
   - `CAMI_WORKSPACE_PATH=~/custom-workspace`
   - User creates first agent
   - Config created at `~/custom-workspace/config.json`

### Priority Resolution Tests

1. **Single Source (Custom)**:
   - Only `custom` source exists (user created their own agent)
   - `frontend` agent requested
   - Uses `custom:frontend`

2. **Multiple Sources, No Conflict**:
   - `fullstack-source` has `frontend`
   - `game-dev-source` has `phaser-specialist`
   - Both available, no conflict

3. **Multiple Sources, With Conflict**:
   - `fullstack-source` (priority 100) has `frontend`
   - `custom` (priority 10) has `frontend`
   - CAMI uses `custom:frontend` (lower priority wins)

4. **Custom Priority Override**:
   - User sets `fullstack-source` to priority 20
   - User has `custom` at priority 10
   - Custom still wins (10 < 20)

### Deployment Tracking Tests

1. **First Deployment**:
   - User deploys `frontend` to `~/projects/my-app`
   - Config records deployment
   - `lastUpdated` timestamp set

2. **Multiple Deployments**:
   - Deploy to `~/projects/app1`
   - Deploy to `~/projects/app2`
   - Both tracked separately

3. **Update Existing Deployment**:
   - Deploy `backend` to existing project
   - `agents` array updated
   - `lastUpdated` timestamp refreshed

### Validation Tests

1. **Corrupted JSON**:
   - Config has syntax error
   - CAMI shows helpful error
   - Suggests manual fix or re-init

2. **Missing Required Field**:
   - Source missing `priority`
   - CAMI shows validation error
   - Points to specific source

3. **Invalid Source Reference**:
   - Deployment references `unknown-source:agent`
   - CAMI warns about invalid reference
   - Lists available sources

---

## Related Documentation

- **UX Specification**: `/reference/ux/skill-architecture-spec.md`
- **Scout Persona**: `/reference/voice/scout-persona.md`
- **Plugin Structure**: `/.claude-plugin/plugin.json`
- **Source Guilds**: See marketplace for available sources

---

## Appendix: Example Configs

### Minimal Config (Fresh Install)

```json
{
  "version": "1.0.0",
  "sources": [],
  "deployments": []
}
```

### Single Guild Config

```json
{
  "version": "1.0.0",
  "sources": [
    {
      "name": "fullstack-source",
      "path": "~/cami-workspace/sources/fullstack-source",
      "git": "https://github.com/lando-labs/fullstack-source.git",
      "priority": 100,
      "added": "2026-02-25T10:30:00Z"
    }
  ],
  "deployments": [
    {
      "project": "~/projects/my-web-app",
      "agents": [
        "fullstack-source:frontend-methodology",
        "fullstack-source:backend-methodology"
      ],
      "lastUpdated": "2026-02-25T10:45:00Z"
    }
  ]
}
```

### Multi-Guild + Custom Config

```json
{
  "version": "1.0.0",
  "sources": [
    {
      "name": "fullstack-source",
      "path": "~/cami-workspace/sources/fullstack-source",
      "git": "https://github.com/lando-labs/fullstack-source.git",
      "priority": 100,
      "added": "2026-02-25T10:30:00Z"
    },
    {
      "name": "game-dev-source",
      "path": "~/cami-workspace/sources/game-dev-source",
      "git": "https://github.com/lando-labs/game-dev-source.git",
      "priority": 100,
      "added": "2026-02-25T11:00:00Z"
    },
    {
      "name": "custom",
      "path": "~/cami-workspace/sources/custom",
      "git": null,
      "priority": 10,
      "added": "2026-02-25T12:00:00Z"
    }
  ],
  "deployments": [
    {
      "project": "~/projects/web-app",
      "agents": [
        "fullstack-source:frontend-methodology",
        "custom:team-conventions"
      ],
      "lastUpdated": "2026-02-25T12:30:00Z"
    },
    {
      "project": "~/projects/game",
      "agents": [
        "game-dev-source:phaser-specialist"
      ],
      "lastUpdated": "2026-02-25T13:00:00Z"
    }
  ]
}
```

---

**Document Version**: 1.0.0
**Last Updated**: 2026-02-25
**Maintainer**: Plugin Architect
