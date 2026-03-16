# Claude Code Plugin Data Storage Research

**Date**: 2026-02-26
**Researched by**: Plugin Architect
**Purpose**: Determine best practices for CAMI plugin workspace data storage

---

## Executive Summary

**Current State**: CAMI stores its workspace at `~/cami-workspace/` (user's home directory)

**Key Finding**: Claude Code does NOT define a standard plugin data directory. Plugins are expected to manage their own persistent data storage based on the type of data and scope needed.

**Recommendation**: Keep `~/cami-workspace/` but add support for `~/.config/cami/` as an alternative for users who prefer XDG conventions.

---

## Research Findings

### 1. Claude Code's Own Data Storage

Claude Code itself uses `~/.claude/` as its primary data directory:

```
~/.claude/
├── agents/              # User-level subagents
├── cache/               # Various caches
├── commands/            # Custom commands
├── debug/               # Debug logs
├── file-history/        # File change tracking
├── history.jsonl        # Command history
├── plugins/             # Plugin registry and installations
│   ├── config.json
│   ├── installed_plugins.json
│   ├── known_marketplaces.json
│   ├── marketplaces/    # Installed marketplace clones
│   └── repos/           # Plugin repositories
├── projects/            # Project-specific data
├── settings.json        # User settings
├── settings.local.json  # Machine-local settings
├── skills/              # User-level skills
├── tasks/               # Task tracking
├── todos/               # Todo lists by session
└── [various other runtime data]
```

**Key Observations**:
- Claude Code consolidates all its data under `~/.claude/`
- Plugin installations go in `~/.claude/plugins/marketplaces/`
- BUT: No dedicated directory for plugin runtime data

### 2. Plugin Storage Conventions (or Lack Thereof)

**From Official Documentation**:
- Plugins have NO specified convention for storing persistent workspace data
- Plugin manifests (`.claude-plugin/plugin.json`) define plugin structure, NOT data storage
- Settings can be stored in `settings.json` files (user/project/local scope)
- MCP servers can be configured in `.mcp.json` (user or project scope)

**Plugin Components Storage**:
```
~/.claude/agents/              # User-level agents
.claude/agents/                # Project-level agents
~/.claude/skills/              # User-level skills
.claude/skills/                # Project-level skills
~/.claude.json                 # User-level MCP servers
.mcp.json                      # Project-level MCP servers
```

**What's Missing**: No convention for plugin-specific persistent data like:
- Configuration files beyond settings
- Workspace data (like CAMI's sources/)
- Cache directories
- User data collections

### 3. Cross-Platform Storage Patterns

**XDG Base Directory Specification** (Linux/Unix standard):
```
~/.config/                     # Configuration files
~/.local/share/                # Data files
~/.cache/                      # Cache files
~/.local/state/                # State data
```

**macOS Native Conventions**:
```
~/Library/Application Support/ # App data
~/Library/Preferences/         # Preferences
~/Library/Caches/              # Caches
```

**Current Industry Practice** (from examining popular CLI tools):
- Many cross-platform CLI tools use `~/.config/` even on macOS
- Some tools use `~/.<app-name>/` for simplicity (e.g., `~/.docker/`, `~/.npm/`)
- Modern tools often support XDG_CONFIG_HOME and XDG_DATA_HOME env vars

### 4. Comparable Tool Examples

**Observed patterns in CLI tools**:
- `~/.docker/` - Docker Desktop
- `~/.npm/` - npm
- `~/.nvm/` - Node Version Manager
- `~/.cargo/` - Rust package manager
- `~/.config/gh/` - GitHub CLI (XDG-aware)
- `~/.local/share/claude/` - Found in local system (XDG pattern)

**Mixed approach is common**: Simple tools use `~/.<name>/`, while modern tools support XDG.

---

## Analysis: CAMI's Storage Needs

### Data Types CAMI Manages

1. **Configuration** (`config.yaml`):
   - Agent sources (git repos, local directories)
   - Deploy locations (project paths)
   - Installation metadata
   - **Scope**: User-level, persists across all projects

2. **Agent Sources** (`sources/`):
   - Git repositories (cloned)
   - Local agent collections
   - CLAUDE.md, STRATEGIES.yaml, agent files
   - **Scope**: User-level, shared across projects

3. **Deployment Tracking** (`deployments.yaml`):
   - Which agents are deployed where
   - Deployment timestamps
   - Version tracking
   - **Scope**: User-level, global deployment state

4. **Backups** (`.cami-backup-*`):
   - Automatic backups before operations
   - Timestamped snapshots
   - **Scope**: User-level, safety net

### Why `~/cami-workspace/` Makes Sense

**Advantages**:
1. **Visibility**: Users can easily find and browse their workspace
2. **Direct access**: Can cd into sources, edit agents directly
3. **Familiar pattern**: Similar to `~/workspace/`, `~/projects/`
4. **Git-friendly**: Sources are git repos users may want to work with directly
5. **Not hidden**: User data, not just config - should be visible
6. **Platform-agnostic**: Works identically on all platforms

**Disadvantages**:
1. **Clutters home directory**: Adds another top-level directory
2. **Not "standard"**: Doesn't follow XDG or macOS conventions
3. **Backup concerns**: Users may not back up home directory contents as reliably

### Alternative: `~/.config/cami/`

**Advantages**:
1. **XDG compliance**: Follows Linux/Unix standard
2. **Cleaner home**: Hidden by default
3. **Standard backup**: Tools that respect XDG will back it up
4. **Professional**: Matches modern CLI tool conventions

**Disadvantages**:
1. **Less discoverable**: Hidden from users
2. **macOS mismatch**: Not native to macOS conventions
3. **Git workflow**: Harder to browse and edit sources directly
4. **Multiple locations**: Would need to split config vs data (XDG distinguishes these)

### Alternative: `~/Library/Application Support/cami/` (macOS)

**Advantages**:
1. **macOS native**: Respects platform conventions
2. **Backup-friendly**: Time Machine backs this up by default
3. **Professional**: Matches Mac app expectations

**Disadvantages**:
1. **Platform-specific**: Requires different paths per OS
2. **Deeply nested**: Harder to access
3. **Complexity**: Need platform detection and multiple paths
4. **Not for workspace-like data**: Better for preferences than user-editable repos

---

## Recommendation

### Primary Recommendation: Keep `~/cami-workspace/` with Enhanced Flexibility

**Rationale**:
1. CAMI's data is **user-facing workspace data**, not just configuration
2. Users need to **browse, edit, and manage** agent sources directly
3. The workspace contains **git repositories** users may want to work with
4. **Visibility** is a feature, not a bug - users should see their agent collections
5. **Simplicity** across platforms reduces complexity

**Enhancement**: Support configurable workspace location via environment variable:
```bash
export CAMI_WORKSPACE_DIR=~/.config/cami  # XDG users can override
export CAMI_WORKSPACE_DIR=~/my-agents     # Custom location
```

### Implementation Plan

**Phase 1: Document Current Behavior**
```yaml
# config.yaml includes workspace metadata
workspace_path: ~/cami-workspace  # Absolute path (~ expands to user's home)
```

**Phase 2: Add Environment Variable Support**
```go
// Determine workspace location
func getWorkspaceDir() string {
    if dir := os.Getenv("CAMI_WORKSPACE_DIR"); dir != "" {
        return expandPath(dir)
    }
    return filepath.Join(os.Getenv("HOME"), "cami-workspace")
}
```

**Phase 3: Add CLI Flag**
```bash
cami setup --workspace ~/my-agents
```

**Phase 4: Support XDG (Optional)**
```go
// For users who want XDG compliance
if os.Getenv("CAMI_USE_XDG") == "true" {
    configHome := os.Getenv("XDG_DATA_HOME")
    if configHome == "" {
        configHome = filepath.Join(os.Getenv("HOME"), ".local/share")
    }
    return filepath.Join(configHome, "cami")
}
```

### File Organization Pattern

**Recommended structure** (regardless of location):
```
${CAMI_WORKSPACE_DIR}/
├── config.yaml          # Configuration
├── deployments.yaml     # Deployment tracking
├── sources/             # Agent sources (git repos, local)
│   ├── my-agents/
│   ├── game-dev-guild/
│   └── fullstack-guild/
├── .backups/            # Hidden backup directory
│   ├── 20260226-120000/
│   └── 20260226-130000/
└── .cache/              # Hidden cache directory (optional)
```

**Benefits**:
- Config files at root for easy access
- Sources organized in subdirectory
- Backups hidden but accessible
- Cache isolated from user data

---

## Migration Considerations

If we decide to change from `~/cami-workspace/` in the future:

### Migration Strategy

1. **Detect existing workspace**: Check for `~/cami-workspace/`
2. **Prompt user**: Ask to migrate or keep current location
3. **Migrate data**: Copy all files to new location
4. **Update config**: Update `config.yaml` with new path
5. **Symlink option**: Create symlink at old location pointing to new location
6. **Deprecation period**: Support both locations during transition

### Backwards Compatibility

```go
func findWorkspace() string {
    // Priority order:
    // 1. Environment variable
    if dir := os.Getenv("CAMI_WORKSPACE_DIR"); dir != "" {
        return dir
    }

    // 2. Config file workspace_path field
    if cfg, err := loadConfig(); err == nil && cfg.WorkspacePath != "" {
        return cfg.WorkspacePath
    }

    // 3. New default (if we change it)
    newDefault := filepath.Join(getXDGDataHome(), "cami")
    if exists(newDefault) {
        return newDefault
    }

    // 4. Legacy default
    return filepath.Join(os.Getenv("HOME"), "cami-workspace")
}
```

---

## Comparison Table

| Location | Visibility | XDG | macOS Native | Discoverability | Complexity | Recommendation |
|----------|-----------|-----|--------------|-----------------|------------|----------------|
| `~/cami-workspace/` | ✅ High | ❌ No | ❌ No | ✅ Easy | ✅ Simple | **Best for CAMI** |
| `~/.config/cami/` | ❌ Hidden | ✅ Yes | ❌ No | ⚠️ Medium | ✅ Simple | Good for config-only |
| `~/.local/share/cami/` | ❌ Hidden | ✅ Yes | ❌ No | ⚠️ Medium | ✅ Simple | Good for data-only |
| `~/Library/Application Support/cami/` | ❌ Hidden | ❌ No | ✅ Yes | ❌ Hard | ❌ Complex | Not for workspace data |
| Configurable | ⚠️ Varies | ⚠️ Optional | ⚠️ Optional | ⚠️ Varies | ⚠️ More complex | **Best overall** |

---

## Conclusion

**Final Recommendation**:

1. **Keep `~/cami-workspace/` as the default** - It's appropriate for the type of data CAMI manages
2. **Add configurability** via `CAMI_WORKSPACE_DIR` environment variable - Respects user preferences
3. **Document the choice clearly** - Explain why this location makes sense
4. **Support migration** if users want to move it - Provide tooling for easy relocation

This approach balances:
- **User experience**: Easy discovery and direct access
- **Flexibility**: Users can choose their preferred location
- **Simplicity**: Single location works across all platforms
- **Standards**: Can adopt XDG if user wants it

---

## Sources

- [Create plugins - Claude Code Docs](https://code.claude.com/docs/en/plugins)
- [Claude Code settings - Claude Code Docs](https://code.claude.com/docs/en/settings)
- [How Claude Code Manages Local Storage for AI Agents - Milvus Blog](https://milvus.io/blog/why-claude-code-feels-so-stable-a-developers-deep-dive-into-its-local-storage-design.md)
- Local system inspection: `~/.claude/` directory structure
- CAMI current implementation: `~/cami-workspace/` examination
