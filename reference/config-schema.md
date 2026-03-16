# CAMI Configuration Schema

**Version**: 1.0.0
**Format**: YAML
**Location**: `~/cami-workspace/config.yaml`
**Created**: Auto-generated on first meaningful action

---

## CRITICAL: Format Alignment

**This plugin uses the EXISTING CAMI workspace format.** Users already have `config.yaml` files from the CAMI MCP server. This plugin MUST be compatible with that format.

| Aspect | Correct | Wrong |
|--------|---------|-------|
| File format | YAML | ~~JSON~~ |
| File name | `config.yaml` | ~~config.json~~ |
| Sources field | `agent_sources` | ~~sources~~ |
| Locations field | `deploy_locations` | ~~deployments~~ |

---

## Workspace Resolution

CAMI stores configuration and sources in a user-configurable workspace.

### Bootstrap File

The workspace location is stored in `~/.claude/cami.yaml`:

```yaml
# ~/.claude/cami.yaml
workspace_path: ~/.claude/cami-workspace
```

### Resolution Logic

**To resolve `$WORKSPACE`:**
1. Check `~/.claude/cami.yaml` for `workspace_path` setting
2. If not found, default to `~/.claude/cami-workspace`

```bash
# Read workspace path
WORKSPACE=$(cat ~/.claude/cami.yaml 2>/dev/null | grep workspace_path | cut -d: -f2 | tr -d ' ' || echo "$HOME/.claude/cami-workspace")
```

### Workspace Locations (Set During Onboarding)

Users choose their workspace location during first-run onboarding:

| Option | Path | Best For |
|--------|------|----------|
| 1 (default) | `~/.claude/cami-workspace` | Most users - keeps things tidy |
| 2 | `~/cami-workspace` | Easy access in home directory |
| 3 | `~/projects/cami-workspace` | Users who organize by projects |
| 4 | Custom | Specific requirements |

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
Claude: *Creates agent, auto-creates config.yaml behind the scenes*
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
   - Creates: `config.yaml` + `sources/my-agents/` directory
   - Priority: 10 (highest priority - custom agents are the intended primary)

2. **Deploys First Agent**:
   - Trigger: "deploy frontend agent to my project"
   - Creates: Config if missing, updates deployment tracking
   - Updates: `deployments.yaml` for the target project

3. **Adds First Source**:
   - Trigger: "add the my-source"
   - Creates: Config if missing, clones source to `sources/` directory
   - Initial state: One source entry

---

## config.yaml Schema (Workspace Configuration)

### Complete Structure

This is the **existing format** that CAMI MCP server users already have:

```yaml
# ~/cami-workspace/config.yaml
version: "1"
install_timestamp: 2025-11-20T17:37:36.626242873-06:00
setup_complete: true

agent_sources:
  - name: my-agents
    type: local
    path: ~/cami-workspace/sources/my-agents
    priority: 10
    git:
      enabled: false

  - name: my-source
    type: local
    path: ~/cami-workspace/sources/my-source
    priority: 40
    git:
      enabled: true
      url: https://github.com/lando-labs/my-source.git

deploy_locations:
  - name: my-app
    path: ~/projects/my-app
```

### Field Definitions

#### Top-Level Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | Yes | Schema version, currently "1" |
| `install_timestamp` | string | No | ISO 8601 timestamp of workspace creation |
| `setup_complete` | boolean | No | Whether initial setup is done |
| `agent_sources` | array | Yes | Array of agent source definitions |
| `deploy_locations` | array | Yes | Array of tracked project locations |

#### Source Object (`agent_sources[]`)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Source identifier (kebab-case, unique) |
| `type` | string | Yes | Source type: `local`, `git`, `remote` |
| `path` | string | Yes | Absolute path to source directory |
| `priority` | number | Yes | Priority for conflict resolution (lower wins) |
| `git.enabled` | boolean | Yes | Whether git sync is enabled |
| `git.url` | string | No | Git clone URL (required if git.enabled) |

**Source Priority Defaults**:
- Custom agents (my-agents): **10** (highest priority - user's own agents win)
- Team/organization sources: **40-50** (medium priority)
- Public/official sources: **100** (lowest priority, default)

#### Deploy Location Object (`deploy_locations[]`)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Project display name |
| `path` | string | Yes | Absolute path to project directory |

---

## Priority System

### How Priority Works

When the **same agent name** exists in multiple sources, the source with the **lower priority number wins**.

**Priority Tiers**:
```
10  = Custom agents (my-agents - user's own work wins)
40  = Team/organization sources (medium priority)
100 = Public/official sources (lowest priority, default)
```

### Example Scenario

**Sources**:
```yaml
agent_sources:
  - name: my-agents
    priority: 10
    path: ~/cami-workspace/sources/my-agents

  - name: team-guild
    priority: 40
    path: ~/cami-workspace/sources/team-guild

  - name: my-source
    priority: 100
    path: ~/cami-workspace/sources/my-source
```

**Agent Resolution**:

If `frontend-methodology.md` exists in all three sources:
1. CAMI uses `my-agents/frontend-methodology.md` (priority 10 - user's own agents win)
2. If my-agents doesn't have it, uses `team-guild/frontend-methodology.md` (priority 40)
3. If neither have it, uses `my-source/frontend-methodology.md` (priority 100)

**User Control**:

Users can override priority when adding sources:
```
User: "Add the my-source with priority 20"
Claude: *Adds source with priority 20 instead of default 100*
```

---

## Workspace Structure

CAMI auto-creates this structure when needed:

```
$WORKSPACE/                          # User-configured location (see Workspace Resolution)
├── config.yaml                      # Workspace configuration
├── deployments.yaml                 # Detailed deployment tracking (optional)
└── sources/                         # Agent source directories
    ├── my-agents/                   # User's custom agents (priority 10)
    │   ├── CLAUDE.md                # Source description
    │   ├── my-agent.md              # User-created agents
    │   └── ...
    ├── my-source/                   # Git cloned source
    │   ├── .git/
    │   ├── CLAUDE.md
    │   ├── agents/
    │   │   ├── frontend-methodology.md
    │   │   └── backend-methodology.md
    │   └── skills/
    │       └── react-tailwind/
    └── team-source/                 # Another git source
        └── ...
```

**Directory Creation Rules**:
- Workspace directory created when config first needed
- `sources/` created alongside config.yaml
- `sources/my-agents/` created when first custom agent created
- `sources/[source-name]/` created when source added (git clone)

**No Manual Setup Required**: Users never create directories manually.

---

---

## Key Design Decisions

### 1. YAML Format (Compatibility with Existing CAMI)

**Decision**: Use YAML for config file to match existing CAMI MCP server format

**Rationale**:
- **Existing Users**: CAMI MCP server users already have `config.yaml` files
- **No Migration Required**: Plugin works with existing workspaces immediately
- **Human-Readable**: YAML is easier to read and edit if needed
- **Comments**: YAML supports comments for documentation

**Note**: While Claude Code plugins use JSON for `plugin.json`, user-facing config files use YAML for compatibility with the existing CAMI ecosystem.

### 2. Versioned Schema

**Decision**: Include `version: "1"` in config

**Rationale**:
- **Future Migrations**: Can detect old config format and auto-migrate
- **Breaking Changes**: Can introduce v2 with different structure
- **Tooling**: Can validate against specific schema version

### 3. Separate Config and Deployments

**Decision**: Keep `config.yaml` (sources) separate from `deployments.yaml` (what's deployed where)

**Rationale**:
- **Single Responsibility**: Each file has one purpose
- **Scalability**: Deployments can grow without cluttering config
- **Optional**: `deployments.yaml` is optional for simple setups

### 4. ISO 8601 Timestamps

**Decision**: Use `2026-02-25T10:30:00Z` format for all timestamps

**Rationale**:
- **Unambiguous**: No timezone confusion
- **Sortable**: Lexicographic sort works correctly
- **Standard**: Widely supported

**Fields Using Timestamps**:
- `install_timestamp` - When workspace was created
- `deployed_at` - When capability was deployed to a project

### 5. Absolute Paths in Config

**Decision**: Store absolute paths in config (e.g., `~/cami-workspace/sources/...`)

**Rationale**:
- **Unambiguous**: No resolution needed
- **Portability**: Tilde expansion happens once at write time
- **Consistency**: Same path format everywhere

### 6. Priority-Based Resolution

**Decision**: Lower priority number wins when same agent exists in multiple sources

**Rationale**:
- **User Control**: User's custom agents (priority 10) always win
- **Explicit**: Priority is visible in config
- **Flexible**: Can be overridden per-source

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

## Deployment Tracking Architecture

CAMI uses a two-tier deployment tracking system:

1. **Centralized**: `~/cami-workspace/deployments.yaml` - Source of truth for ALL deployments
2. **Per-Project**: `<project>/.claude/cami-manifest.yaml` - Cache of that project's deployment data

Both files use the **same schema** for consistency.

---

## Centralized Deployments (deployments.yaml)

**Location**: `$WORKSPACE/deployments.yaml`

This is the master record of all deployments across all projects.

```yaml
# ~/cami-workspace/deployments.yaml
version: "2"
last_updated: 2026-02-05T14:07:59.387077-06:00
manifest_format_version: 2

deployments:
  ~/projects/my-app:
    state: cami-native
    normalized_at: 2026-01-28T22:17:31.633506-06:00
    last_scanned: 2026-01-29T22:44:47.131615-06:00
    agents:
      - name: frontend-methodology
        version: 1.2.0
        source: my-source
        source_path: ~/cami-workspace/sources/my-source/agents/frontend-methodology.md
        priority: 40
        deployed_at: 2026-01-28T22:17:31.633506-06:00
        content_hash: sha256:abc123def456...
        metadata_hash: sha256:def789ghi012...
        custom_override: false
        origin: cami

  ~/projects/other-app:
    state: cami-native
    # ... same structure
```

---

## Project Manifest Schema (cami-manifest.yaml)

Each project has a manifest at `.claude/cami-manifest.yaml` that mirrors the centralized data for that project.

### Standardized Format

```yaml
# .claude/cami-manifest.yaml
version: "2"
manifest_format_version: 2
state: cami-native
normalized_at: 2026-01-28T22:17:31.633506-06:00
last_scanned: 2026-01-29T22:44:47.131615-06:00

agents:
  - name: frontend-methodology
    version: 1.2.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/agents/frontend-methodology.md
    priority: 40
    deployed_at: 2026-01-28T22:17:31.633506-06:00
    content_hash: sha256:abc123def456...
    metadata_hash: sha256:def789ghi012...
    custom_override: false
    origin: cami

  - name: backend-methodology
    version: 2.0.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/agents/backend-methodology.md
    priority: 40
    deployed_at: 2026-01-28T22:20:00.000000-06:00
    content_hash: sha256:ghi789jkl012...
    metadata_hash: sha256:jkl345mno678...
    custom_override: false
    origin: cami

skills:
  - name: react-tailwind
    version: 2.1.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/skills/react-tailwind/SKILL.md
    priority: 40
    deployed_at: 2026-01-28T22:25:00.000000-06:00
    content_hash: sha256:mno456pqr789...
    metadata_hash: sha256:pqr012stu345...
    custom_override: false
    origin: cami
```

### Top-Level Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | Yes | Schema version, currently `"2"` |
| `manifest_format_version` | number | Yes | Format version, currently `2` |
| `state` | string | Yes | Project state: `cami-native`, `imported`, `legacy` |
| `normalized_at` | string | Yes | ISO 8601 timestamp of last normalization |
| `last_scanned` | string | Yes | ISO 8601 timestamp of last scan |
| `agents` | array | Yes | Array of deployed agents |
| `skills` | array | Yes | Array of deployed skills |

### Agent/Skill Entry Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Capability name (matches filename without .md) |
| `version` | string | Yes | Semantic version from source frontmatter |
| `source` | string | Yes | Source name it came from |
| `source_path` | string | Yes | Absolute path to source file |
| `priority` | number | Yes | Source priority (from config.yaml) |
| `deployed_at` | string | Yes | ISO 8601 timestamp of deployment |
| `content_hash` | string | Yes | SHA-256 of file content (for update detection) |
| `metadata_hash` | string | Yes | SHA-256 of frontmatter (for metadata changes) |
| `custom_override` | boolean | Yes | True if user modified the deployed file |
| `origin` | string | Yes | How it was deployed: `cami`, `manual`, `imported` |

### State Values

| State | Description |
|-------|-------------|
| `cami-native` | Project initialized and managed by CAMI |
| `imported` | Agents imported from another project |
| `legacy` | Pre-CAMI project with manually added agents |

### Origin Values

| Origin | Description |
|--------|-------------|
| `cami` | Deployed via CAMI skill/command |
| `manual` | User manually copied the file |
| `imported` | Imported from another project |

---

## Migration: Old Manifest Formats

Skills may encounter old manifest formats. Here's how to handle them:

### Format Detection

**Version 1 / Plugin-Created Format**:
```yaml
version: 1
project:
  name: my-app
capabilities:
  agents:
    - name: frontend-methodology
      deployed_at: 2026-02-25T10:30:00Z
```

**Very Old Format** (`deployed` root key):
```yaml
deployed:
  agents:
    - name: frontend-methodology
      added_at: 2026-02-25T10:30:00Z
```

### Migration Logic

When reading a manifest, detect and auto-migrate:

1. **Check `version` field**:
   - If `version: 1` or `version` is a number → needs migration
   - If `version: "2"` → current format

2. **Migrate structure**:
   - `capabilities.agents` → `agents` (move to root)
   - `capabilities.skills` → `skills` (move to root)
   - `project.initialized_at` → `normalized_at`
   - Add `manifest_format_version: 2`
   - Add `state: "cami-native"`
   - Add `last_scanned: <current_time>`

3. **Migrate agent fields**:
   - `added_at` → `deployed_at`
   - Add missing fields with defaults:
     - `source_path`: Resolve from source + name
     - `priority`: Read from config.yaml for that source
     - `metadata_hash`: Compute from frontmatter
     - `custom_override: false`
     - `origin: "cami"`

4. **Write updated manifest** (silent, no user prompt)

### Migration Example

**Before (v1)**:
```yaml
version: 1
project:
  name: my-app
  initialized_at: 2026-02-25T10:30:00Z
capabilities:
  agents:
    - name: frontend-methodology
      version: 1.2.0
      source: my-source
      deployed_at: 2026-02-25T10:30:00Z
      content_hash: sha256:abc123...
```

**After (v2)**:
```yaml
version: "2"
manifest_format_version: 2
state: cami-native
normalized_at: 2026-02-25T10:30:00Z
last_scanned: 2026-02-26T15:00:00Z
agents:
  - name: frontend-methodology
    version: 1.2.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/agents/frontend-methodology.md
    priority: 40
    deployed_at: 2026-02-25T10:30:00Z
    content_hash: sha256:abc123...
    metadata_hash: sha256:def456...
    custom_override: false
    origin: cami
skills: []
```

---

## Future Schema Versions

When introducing breaking changes, increment version:

**Version Detection**:
```yaml
# Check version field
version: 1  # Current version
```

**Migration Path**:
1. Read config/manifest
2. Check `version` field
3. If old version, auto-migrate
4. Write updated file

---

## Implementation Checklist

### Workspace Config (config.yaml)

- [x] Use YAML format (not JSON)
- [x] Use `agent_sources` field name (not `sources`)
- [x] Use `deploy_locations` field name (not `deployments`)
- [x] Check `~/.claude/cami.yaml` bootstrap file before using default
- [ ] Auto-create workspace directory on first operation
- [ ] Auto-create config.yaml with correct schema
- [ ] Set priority based on source type (custom=10, team=40, official=100)

### Project Manifest (cami-manifest.yaml)

- [x] Use `agents` at root level (not under `capabilities`)
- [x] Use `version: "2"` (string format)
- [x] Include `manifest_format_version: 2`
- [x] Include `state`, `normalized_at`, `last_scanned` at top level
- [x] Use `deployed_at` for timestamp (not `added_at`)
- [x] Include `content_hash` and `metadata_hash` for update detection
- [x] Include `source_path`, `priority`, `custom_override`, `origin`
- [ ] Auto-migrate old manifest formats on read
- [ ] Compute content_hash on deployment

### Bootstrap Configuration

- [x] Document `~/.claude/cami.yaml` bootstrap file
- [x] Skills use `$WORKSPACE` variable (resolved at runtime)
- [x] Default workspace: `~/.claude/cami-workspace`
- [x] First-run onboarding sets workspace path

---

## Testing Scenarios

### Workspace Path Resolution Tests

1. **Default Path (No Bootstrap File)**:
   - `~/.claude/cami.yaml` does not exist
   - Uses default `~/.claude/cami-workspace`

2. **Custom Path (Bootstrap Configured)**:
   - `~/.claude/cami.yaml` contains `workspace_path: ~/my-workspace`
   - Uses `~/my-workspace/config.yaml`

3. **Missing workspace_path Key**:
   - `~/.claude/cami.yaml` exists but no `workspace_path` key
   - Falls back to `~/.claude/cami-workspace`

### Priority Resolution Tests

1. **User Agents Win**:
   - `my-agents` (priority 10) has `frontend-methodology`
   - `my-source` (priority 100) has `frontend-methodology`
   - CAMI uses `my-agents` version (lower priority wins)

2. **Source Not Found**:
   - User requests agent not in any source
   - Offer to create custom agent

### Manifest Migration Tests

1. **Old `deployed` Format**:
   - Manifest has `deployed:` root key
   - Auto-migrates to `capabilities:` on read

2. **Old `added_at` Field**:
   - Entry has `added_at` instead of `deployed_at`
   - Auto-migrates field name

3. **Missing `content_hash`**:
   - Old manifest lacks hash
   - Computes and adds on next operation

### Validation Tests

1. **Corrupted YAML**:
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

### Minimal Workspace Config (Fresh Install)

```yaml
# ~/cami-workspace/config.yaml
version: "1"
setup_complete: true
agent_sources: []
deploy_locations: []
```

### Single Source Config

```yaml
# ~/cami-workspace/config.yaml
version: "1"
install_timestamp: 2026-02-25T10:30:00Z
setup_complete: true

agent_sources:
  - name: my-source
    type: local
    path: ~/cami-workspace/sources/my-source
    priority: 100
    git:
      enabled: true
      url: https://github.com/lando-labs/my-source.git

deploy_locations:
  - name: my-web-app
    path: ~/projects/my-web-app
```

### Multi-Source + Custom Agents Config

```yaml
# ~/cami-workspace/config.yaml
version: "1"
install_timestamp: 2026-02-25T10:30:00Z
setup_complete: true

agent_sources:
  - name: my-agents
    type: local
    path: ~/cami-workspace/sources/my-agents
    priority: 10
    git:
      enabled: false

  - name: my-source
    type: local
    path: ~/cami-workspace/sources/my-source
    priority: 100
    git:
      enabled: true
      url: https://github.com/lando-labs/my-source.git

  - name: team-source
    type: local
    path: ~/cami-workspace/sources/team-source
    priority: 100
    git:
      enabled: true
      url: https://github.com/lando-labs/team-source.git

deploy_locations:
  - name: web-app
    path: ~/projects/web-app
  - name: phaser-game
    path: ~/projects/phaser-game
```

### Example Project Manifest (v2 Format)

```yaml
# ~/projects/my-app/.claude/cami-manifest.yaml
version: "2"
manifest_format_version: 2
state: cami-native
normalized_at: 2026-02-25T10:30:00Z
last_scanned: 2026-02-26T15:00:00Z

agents:
  - name: frontend-methodology
    version: 1.2.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/agents/frontend-methodology.md
    priority: 40
    deployed_at: 2026-02-25T10:30:00Z
    content_hash: sha256:abc123def456789...
    metadata_hash: sha256:def456ghi789...
    custom_override: false
    origin: cami

  - name: backend-methodology
    version: 2.0.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/agents/backend-methodology.md
    priority: 40
    deployed_at: 2026-02-25T10:32:00Z
    content_hash: sha256:def789ghi012345...
    metadata_hash: sha256:ghi012jkl345...
    custom_override: false
    origin: cami

skills:
  - name: react-tailwind
    version: 2.1.0
    source: my-source
    source_path: ~/cami-workspace/sources/my-source/skills/react-tailwind/SKILL.md
    priority: 40
    deployed_at: 2026-02-25T10:35:00Z
    content_hash: sha256:xyz789abc123456...
    metadata_hash: sha256:abc123xyz789...
    custom_override: false
    origin: cami
```

---

**Document Version**: 2.0.0
**Last Updated**: 2026-02-26
**Maintainer**: Plugin Architect

**Changelog**:
- v2.0.0: Aligned with existing CAMI workspace format (YAML, correct field names)
- v1.0.0: Initial version (incorrectly documented JSON format)
