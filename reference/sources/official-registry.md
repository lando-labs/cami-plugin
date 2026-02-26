# Official Sources Registry

> Reference for official Lando Labs sources. Use these as templates or starting points when creating your own agents.

**Purpose**: Official sources provide pre-built agents you can use as-is or as inspiration for custom agents. Most users create custom agents tailored to their workflow, using official sources as reference.

---

## Available Sources

| Source | Repository | Focus | Agents |
|--------|------------|-------|--------|
| fullstack-guild | `github.com/lando-labs/fullstack-guild` | Web development (MERN stack) | frontend, backend, database, api |
| content-guild | `github.com/lando-labs/content-guild` | Writing & marketing | content-strategist, copywriter, seo |
| game-dev-guild | `github.com/lando-labs/game-dev-guild` | Game development (Phaser 3) | game-core, ui, audio |

---

## Adding an Official Source

```
"Add the fullstack-guild as a source"
```

This clones the repository to your workspace's `sources/` directory.

---

## Using as Reference

You don't need to add a source to learn from it. Browse the repositories directly:
- [fullstack-guild](https://github.com/lando-labs/fullstack-guild)
- [content-guild](https://github.com/lando-labs/content-guild)
- [game-dev-guild](https://github.com/lando-labs/game-dev-guild)

Then create your own agents inspired by their patterns.

---

## Creating Custom Sources

Any git repository can be a source:

```
"Add https://github.com/myorg/my-agents as a source"
```

Custom sources work exactly like official ones.

---

## When to Use Official vs Custom

| Scenario | Recommendation |
|----------|----------------|
| Learning CAMI patterns | Browse official sources as examples |
| Standard web/game/content project | Start with official, customize later |
| Unique workflow or domain | Create custom agents from scratch |
| Team-specific conventions | Create custom source for your org |

**Most users**: Create 2-3 custom agents tailored to their project, optionally adding an official source for reference.

---

## Related

- `skills/create-agent/` - Create custom agents
- `skills/manage-sources/` - Add and manage sources
- `reference/voice/scout-persona.md` - CAMI communication style
