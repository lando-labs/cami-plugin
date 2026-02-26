<!--
AI-Generated UX Specification
Created by: cami-ux
Date: 2026-02-25
Purpose: Shared location detection protocol for all CAMI skills
-->

# Location Detection Protocol

Before responding to any request that involves file operations, deployments, or project-specific actions, determine the user's location context using this protocol.

## Why Location Matters

CAMI behaves differently based on where the user is working:
- In their **workspace**: Managing sources and global configuration
- In a **project**: Working on that specific project's capabilities
- In an **uninitialized project**: Ready to set up CAMI
- **Elsewhere**: Need guidance to the right location

## Detection Steps

### Step 1: Identify Current Working Directory

Get the current working directory (cwd) from the conversation context. This is your starting point for all location decisions.

### Step 2: Classify the Location

Check these conditions in order:

```
1. WORKSPACE MODE
   Condition: cwd is ~/cami-workspace/ OR any subdirectory of ~/cami-workspace/

2. PROJECT MODE
   Condition: cwd contains a .claude/ directory (with or without agents/)

3. INITIALIZATION MODE
   Condition: cwd contains project markers but NO .claude/ directory

4. NAVIGATION MODE
   Condition: None of the above (home directory, system directories, random paths)
```

### Project Markers (for Initialization Mode)

Detect project type by checking for these files:

| File/Directory | Project Type |
|----------------|--------------|
| `package.json` | Node.js / JavaScript |
| `tsconfig.json` | TypeScript |
| `go.mod` | Go |
| `Cargo.toml` | Rust |
| `pyproject.toml` | Python (modern) |
| `requirements.txt` | Python (traditional) |
| `pom.xml` | Java (Maven) |
| `build.gradle` | Java (Gradle) |
| `Gemfile` | Ruby |
| `.csproj` | C# / .NET |
| `composer.json` | PHP |

If any of these exist but `.claude/` does not, the user is in **Initialization Mode**.

---

## Mode Definitions

### WORKSPACE MODE

**Location:** `~/cami-workspace/` or subdirectories

**User Context:** Managing their agent command center - sources, global configuration, creating agents for reuse.

**Typical Activities:**
- Creating agents (saved to sources for reuse)
- Adding or updating sources (guilds and custom repos)
- Checking source compliance
- Reviewing what agents are available across all sources

**Skill Routing:**
- `manage-sources`: Primary skill - add, update, list, reconcile
- `create-agent`: Save to user's source directory
- `manage-capabilities`: List available, but ask which project to deploy TO

**Opening Dialogue:**
```
"You're in your CAMI workspace - your agent command center.
From here you can create custom agents or manage your capability sources.

What would you like to do?"
```

---

### PROJECT MODE

**Location:** Any directory containing `.claude/` subdirectory

**User Context:** Working on a specific project. They want to manage THIS project's capabilities.

**Typical Activities:**
- Creating project-specific agents (or saving to sources for reuse)
- Deploying agents to this project
- Scanning deployed agents for updates
- Checking what's deployed vs what's available

**Skill Routing:**
- `manage-capabilities`: Primary skill - deploy, scan, list deployed AND available
- `create-agent`: Ask - save to sources (reusable) or project-local (one-off)?
- `manage-sources`: Still works, but may not be the intent

**Opening Dialogue:**
```
"You're in [project-name]. This project has [N] capabilities deployed:
- Agents: [list]
- Skills: [list if any]

What would you like to do?"
```

**Detecting Project Name:**
1. Check `package.json` name field
2. Check directory name as fallback
3. Use "this project" if can't determine

---

### INITIALIZATION MODE

**Location:** Directory with project markers but no `.claude/`

**User Context:** They have a project, but CAMI isn't set up. Perfect moment to help them get started.

**Typical Activities:**
- Setting up CAMI for the first time
- Creating custom agents for the project's specific needs
- Optionally browsing pre-built agents based on tech stack
- Deploying initial agent roster

**Skill Routing:**
- `create-project`: Primary flow - initialize CAMI here
- `manage-capabilities`: After setup, can deploy
- `cami`: Offer setup guidance

**Opening Dialogue:**
```
"I see this is a [detected-type] project (found [marker]).
It doesn't have CAMI set up yet.

Would you like me to:
1. Set up CAMI and create custom agents for your needs
2. Set up CAMI and browse pre-built options for this stack
3. Just set up CAMI and we'll add agents later"
```

**Tech Stack Detection:**
Read project files to determine technologies:
- `package.json`: Check dependencies for React, Vue, Angular, Express, etc.
- `tsconfig.json`: TypeScript configuration details
- Framework-specific files: next.config.js, nuxt.config.js, etc.

---

### NAVIGATION MODE

**Location:** Home directory, system directories, or unclear locations

**User Context:** They're not in a workspace or project. They need guidance.

**Typical Activities:**
- Finding their way to the workspace
- Discovering their projects
- Getting oriented

**Skill Routing:**
- `cami`: Guide them to a useful location
- All skills: Should ask for clarification before proceeding

**Opening Dialogue:**
```
"I'm not sure where you'd like to work. Would you like to:
1. Go to your CAMI workspace (manage sources and global config)
2. Work on a specific project (tell me which one)
3. Find your Claude Code projects (I'll scan for them)"
```

---

## Skill-Specific Behavior by Location

### manage-capabilities

| Location | Behavior |
|----------|----------|
| **Workspace** | List agents from all sources. For deploy: "Which project should I deploy to?" |
| **Project** | Show deployed AND available. Deploy to THIS project. Scan THIS project. |
| **Initialization** | Offer to set up CAMI first, then deploy. |
| **Navigation** | "Which project would you like to work on?" |

### manage-sources

| Location | Behavior |
|----------|----------|
| **Workspace** | Full functionality: add, update, list, reconcile sources |
| **Project** | Can still manage sources, but ask: "Want to manage sources? You're in [project]." |
| **Initialization** | Suggest going to workspace for source management |
| **Navigation** | Guide to workspace |

### create-agent

| Location | Behavior |
|----------|----------|
| **Workspace** | Create in user's my-agents source (or specified source) |
| **Project** | Ask: "Save to your sources (reusable) or just this project (one-off)?" |
| **Initialization** | Offer to set up CAMI first, then ask about agent location |
| **Navigation** | Ask where to save the new agent |

### create-project

| Location | Behavior |
|----------|----------|
| **Workspace** | Ask where to create the new project |
| **Project** | "You're already in a project. Did you mean to set up a different one?" |
| **Initialization** | Perfect! Initialize CAMI here. |
| **Navigation** | Ask target directory |

---

## Location Transitions

When a skill completes, the user may need to change locations. Handle gracefully:

### Workspace to Project
```
User: "deploy frontend to my-app"
CAMI: "Deploying frontend-methodology to ~/projects/my-app..."
      [After success]
      "Done. Want me to continue working on my-app, or stay in your workspace?"
```

### Project to Workspace
```
User: "add the game-dev-guild"
CAMI: "Adding source to your workspace config..."
      [After success]
      "Guild added. Want to create a custom agent for [current-project],
       or deploy something from the new source?"
```

### Creation Before Source Addition
```
User: "I need a frontend specialist"
CAMI: "Want me to create a custom frontend agent for your needs?
       Or I can check if there's a pre-built option available."
```

### Initialization to Project
```
User: "set up CAMI here"
CAMI: [After setup]
      "CAMI is ready! You now have 3 agents deployed.
       This project is now in Project Mode - you can add more or scan for updates."
```

---

## Edge Cases

### Nested Project Detection
If inside a subdirectory of a project (e.g., `~/projects/my-app/src/`):
- Still detect as PROJECT MODE
- Look for `.claude/` in parent directories (up to reasonable limit)
- Use the project root, not the current subdirectory

### Workspace Inside a Project
If someone accidentally puts their workspace inside a project:
- Workspace check takes precedence (it's in the cwd path)
- Gently suggest better organization if detected

### Multiple Project Markers
If directory has both `package.json` and `go.mod`:
- List all detected technologies
- "I see both Node.js and Go here - what kind of project is this?"

### Empty .claude/ Directory
If `.claude/` exists but has no agents:
- Still PROJECT MODE
- Note: "CAMI is set up but you haven't deployed any agents yet"

---

## Implementation Notes

### Performance
- Cache location detection results within a skill session
- Don't re-detect for every user message if cwd hasn't changed

### Transparency
- When location affects behavior, tell the user: "Since you're in your workspace..."
- Don't silently change behavior based on location

### Flexibility
- Allow users to override: "deploy to ~/projects/other-app" should work from anywhere
- Location is a default, not a hard constraint

### Error Handling
- If location is ambiguous, ask rather than guess
- If detection fails, fall back to Navigation Mode
