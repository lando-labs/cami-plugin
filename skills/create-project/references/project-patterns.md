# Project Patterns Reference

> **For create-project skill**: Common project patterns, typical agent combinations, and when to include strategic-planner vs methodology-specialist agents.

**Key Principle**: Team composition should match project complexity and domain requirements. Not every project needs a full roster - some need focused specialists, others need strategic architects.

---

## Agent Class Reference

### methodology-specialist (Feature Builder)
**Role**: Domain expert who guides feature development with specific technical knowledge.

**Characteristics**:
- Deep domain expertise (frontend, backend, database, etc.)
- Guides implementation decisions
- Provides patterns and best practices
- Works at feature/component level

**When to use**:
- Building features in a specific domain
- Need technical guidance for implementation
- Domain requires specialized knowledge
- Working within established architecture

### strategic-planner (System Architect)
**Role**: System-level thinker who guides strategic decisions and cross-cutting concerns.

**Characteristics**:
- Big-picture thinking
- Cross-domain concerns
- Strategic trade-offs
- System-wide impact

**When to use**:
- Complex, multi-domain projects
- Security, performance, scalability concerns
- Architectural decisions
- System design and planning

**See**: `references/agent-classes.md` for detailed class definitions.

---

## Project Type Patterns

### E-commerce Projects

**Characteristics**:
- Customer-facing transactions
- Payment processing
- Product catalogs and inventory
- Shopping cart and checkout flows
- User accounts and order history

**Complexity**: HIGH (multiple domains, security critical, performance critical)

**Recommended Core Team**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| frontend-methodology | methodology-specialist | Product pages, cart, checkout UI architecture |
| backend-methodology | methodology-specialist | Order processing, inventory management APIs |
| database-methodology | methodology-specialist | Product catalog, order schema design |
| security-specialist | strategic-planner | Payment security, PCI compliance, auth flows |

**Supporting Specialists** (add when ready):
| Agent | Class | When to Add |
|-------|-------|-------------|
| performance-specialist | strategic-planner | Product page speed critical for conversion |
| qa-methodology | methodology-specialist | Before launch - testing strategy |

**Implementation Skills**:
- react-tailwind (or framework-specific component generation)
- express-api-patterns (or framework-specific API patterns)
- postgres-schema-design (or database-specific patterns)
- payment-integration-patterns (Stripe, PayPal, etc.)

**Why this composition**:
- Three domain specialists cover main areas (UI, API, data)
- Security specialist essential for payment handling
- Performance specialist critical for conversion optimization
- E-commerce requires both implementation experts and strategic oversight

---

### SaaS Applications

**Characteristics**:
- User accounts and authentication
- Multi-tenancy (often)
- Subscription/billing
- Dashboard and admin interfaces
- API for integrations

**Complexity**: MEDIUM-HIGH (multiple features, auth critical, scalability important)

**Recommended Core Team**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| fullstack-methodology | methodology-specialist | If using meta-framework (Next.js, etc.) |
| backend-methodology | methodology-specialist | API design, business logic |
| database-methodology | methodology-specialist | Multi-tenant schema, user data |
| auth-specialist | strategic-planner | Auth strategy, session management, security |

**If NOT using meta-framework**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| frontend-methodology | methodology-specialist | UI architecture, state management |
| backend-methodology | methodology-specialist | API design, business logic |
| database-methodology | methodology-specialist | Multi-tenant schema |
| auth-specialist | strategic-planner | Auth strategy |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| performance-specialist | strategic-planner | Optimizing dashboard load times |
| qa-methodology | methodology-specialist | Testing multi-tenancy, user flows |
| integration-specialist | methodology-specialist | Building third-party integrations |

**Implementation Skills**:
- nextjs-app-router / framework-specific
- auth-patterns (Auth0, Clerk, custom)
- api-patterns
- database-schema-design
- multi-tenant-patterns

**Why this composition**:
- Domain specialists for main tech layers
- Auth specialist for security architecture
- SaaS complexity requires both implementation and strategic guidance
- Multi-tenancy adds architectural complexity

---

### CLI Tools

**Characteristics**:
- Command-line interface
- Single-purpose or focused functionality
- Often scripting or automation
- Usually one language/runtime

**Complexity**: LOW (focused scope, single domain)

**Recommended Specialist**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| backend-methodology | methodology-specialist | CLI architecture, error handling, language idioms |

**Supporting Specialists** (rare):
| Agent | Class | When to Add |
|-------|-------|-------------|
| qa-methodology | methodology-specialist | Only if complex testing needs |

**Implementation Skills**:
- go-cli-patterns (or language-specific CLI patterns)
- testing-patterns (if needed)

**Why this composition**:
- CLI tools are focused and single-domain
- One specialist is sufficient for guidance
- No need for strategic-planner (not complex enough)
- Keep it simple - over-staffing wastes resources

**Examples**:
- Git-like tools → backend-methodology for Go/Rust/etc.
- Build tools → backend-methodology + testing
- Dev utilities → backend-methodology only

---

### Content Sites (Blogs, Marketing, Landing Pages)

**Characteristics**:
- Content-focused
- SEO important
- Fast page loads
- CMS integration (often)
- Minimal backend logic

**Complexity**: LOW-MEDIUM (frontend-heavy, simple backend)

**Recommended Core Team**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| frontend-methodology | methodology-specialist | Component architecture, layouts |
| content-specialist | methodology-specialist | Content structure, SEO, accessibility |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| performance-specialist | strategic-planner | Page speed critical for SEO |
| seo-specialist | strategic-planner | Advanced SEO strategy |

**Implementation Skills**:
- nextjs-app-router / astro-patterns / framework-specific
- content-patterns (MDX, CMS integration)
- seo-patterns (meta tags, schema markup)

**Why this composition**:
- Content sites are frontend-heavy with simple needs
- Content specialist for editorial workflow and SEO
- Backend often minimal (static generation or simple CMS)
- Strategic oversight only if performance/SEO critical

---

### Games (Phaser, Unity, Godot)

**Characteristics**:
- Game loop and state management
- Graphics, animation, sound
- Input handling and player interaction
- Game design patterns

**Complexity**: MEDIUM-HIGH (specialized domain, performance critical)

**Recommended Core Team (Phaser)**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| game-core-methodology | methodology-specialist | Game loop, state, architecture |
| game-ui-methodology | methodology-specialist | HUD, menus, UI systems |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| game-audio-specialist | methodology-specialist | Sound design, music integration |
| performance-specialist | strategic-planner | Optimization, frame rate |
| qa-methodology | methodology-specialist | Game testing strategy |

**Implementation Skills**:
- phaser-scene-patterns
- phaser-ui-patterns
- phaser-audio-patterns
- game-state-patterns

**Why this composition**:
- Game development is specialized domain
- Core and UI specialists cover main concerns
- Audio specialist if music/sound important
- Performance specialist for optimization

---

### Data Pipelines / ETL

**Characteristics**:
- Data ingestion and transformation
- Batch or stream processing
- Data quality and validation
- Storage and retrieval

**Complexity**: MEDIUM (backend-heavy, data-focused)

**Recommended Core Team**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| backend-methodology | methodology-specialist | Pipeline architecture, error handling |
| database-methodology | methodology-specialist | Data modeling, storage strategy |
| data-engineering-specialist | methodology-specialist | ETL patterns, transformations |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| performance-specialist | strategic-planner | Large-scale data processing |
| qa-methodology | methodology-specialist | Data quality testing |

**Implementation Skills**:
- etl-patterns (framework-specific)
- data-validation-patterns
- database-migration-patterns

**Why this composition**:
- Backend and database specialists for infrastructure
- Data engineering specialist for domain expertise
- Performance critical for large-scale processing

---

### Mobile Apps (React Native, Flutter)

**Characteristics**:
- Mobile UI patterns
- Platform-specific concerns (iOS, Android)
- Offline support
- Push notifications
- App store deployment

**Complexity**: MEDIUM-HIGH (specialized platform, multiple concerns)

**Recommended Core Team (React Native)**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| mobile-methodology | methodology-specialist | Mobile architecture, navigation, state |
| backend-methodology | methodology-specialist | API design for mobile clients |
| performance-specialist | strategic-planner | Mobile performance critical (battery, network) |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| auth-specialist | strategic-planner | Mobile auth flows (OAuth, biometric) |
| qa-methodology | methodology-specialist | Mobile testing strategy |

**Implementation Skills**:
- react-native-patterns
- mobile-ui-patterns
- offline-sync-patterns
- push-notification-patterns

**Why this composition**:
- Mobile specialist for platform-specific guidance
- Backend specialist for API design
- Performance critical from start (can't optimize later easily)

---

### API-Only / Backend Services

**Characteristics**:
- No frontend
- RESTful or GraphQL API
- Business logic and data processing
- Often microservices

**Complexity**: MEDIUM (backend-focused, architectural decisions)

**Recommended Core Team**:
| Agent | Class | Rationale |
|-------|-------|-----------|
| backend-methodology | methodology-specialist | API design, service architecture |
| database-methodology | methodology-specialist | Schema design, query optimization |

**Supporting Specialists**:
| Agent | Class | When to Add |
|-------|-------|-------------|
| api-design-specialist | strategic-planner | Complex API design (GraphQL, versioning) |
| performance-specialist | strategic-planner | High-traffic APIs |
| security-specialist | strategic-planner | Auth, rate limiting, data protection |
| qa-methodology | methodology-specialist | API testing strategy |

**Implementation Skills**:
- express-api-patterns / framework-specific
- graphql-patterns (if using GraphQL)
- api-testing-patterns
- database-patterns

**Why this composition**:
- Backend and database cover main domains
- API design specialist for complex API concerns
- Strategic specialists for cross-cutting concerns
- No frontend means focused roster

---

## Team Composition Decision Matrix

### When to Include strategic-planner Agents

| Scenario | Include strategic-planner? | Which Ones? |
|----------|---------------------------|-------------|
| **Security-critical** (payments, PII, auth) | ✅ YES | security-specialist, auth-specialist |
| **Performance-critical** (high traffic, real-time) | ✅ YES | performance-specialist |
| **Complex architecture** (microservices, distributed) | ✅ YES | architect, system-design-specialist |
| **Scalability concerns** (growth expected) | ✅ YES | performance-specialist, architect |
| **Multiple domains** (>3 tech concerns) | ✅ YES | architect or relevant specialists |
| **Simple CRUD app** | ❌ NO | methodology-specialists sufficient |
| **CLI tool** | ❌ NO | One methodology-specialist |
| **Prototype/MVP** | ⚠️ MAYBE | Only if security/performance critical |
| **Internal tool** (low traffic, small team) | ⚠️ MAYBE | Usually not needed |

### When to Exclude strategic-planner Agents

**Exclude when**:
- Single-purpose, focused scope
- Low complexity
- Prototype or experiment
- Internal tool with low stakes
- Small team, fast iteration needed

**Include methodology-specialists only**:
- Feature-level guidance sufficient
- Domain expertise needed, not strategic oversight
- Keep roster lean and focused

### Roster Size Guidelines

| Project Complexity | Recommended Roster Size | Composition |
|-------------------|------------------------|-------------|
| **Simple** (CLI, static site, utility) | 1-2 agents | 1 methodology-specialist, maybe 1 skill |
| **Medium** (CRUD app, content site) | 2-3 agents | 2-3 methodology-specialists |
| **Complex** (SaaS, e-commerce) | 4-6 agents | 3 methodology-specialists + 1-3 strategic-planners |
| **Very Complex** (platform, enterprise) | 6-9 agents | 3-4 methodology-specialists + 3-5 strategic-planners |

**Golden rule**: Start small, add as needed. It's easier to add specialists than remove them.

---

## Tech Stack → Agent Mapping

### Frontend Frameworks

| Framework | Recommended Agent | Rationale |
|-----------|------------------|-----------|
| **React** | frontend-methodology | Component architecture, hooks, state |
| **Next.js** | fullstack-methodology | SSR decisions, App Router, API routes |
| **Vue 3** | frontend-methodology | Composition API, Pinia, patterns |
| **Angular** | frontend-methodology | Modules, services, RxJS patterns |
| **Svelte/SvelteKit** | frontend-methodology | Reactivity, stores, patterns |
| **Astro** | frontend-methodology | Islands architecture, content focus |

**Rule**: Meta-frameworks (Next.js, SvelteKit, etc.) → fullstack-methodology. Pure frontend frameworks → frontend-methodology.

### Backend Frameworks

| Framework | Recommended Agent | Rationale |
|-----------|------------------|-----------|
| **Express (Node)** | backend-methodology | Middleware, routing, RESTful patterns |
| **NestJS** | backend-methodology | DI, modules, decorators, architecture |
| **Fastify** | backend-methodology | Performance focus, plugin system |
| **Django** | fullstack-methodology | MTV, ORM, admin, batteries-included |
| **Flask** | backend-methodology | Lightweight, flexible patterns |
| **FastAPI** | backend-methodology | Async Python, type hints, modern API |
| **Gin (Go)** | backend-methodology | Go idioms, middleware, routing |
| **Rails** | fullstack-methodology | Convention over config, ActiveRecord |
| **Laravel** | fullstack-methodology | MVC, Eloquent, Blade templates |

**Rule**: Full-featured frameworks (Django, Rails, Laravel) → fullstack-methodology. API-focused frameworks → backend-methodology.

### Databases

| Database | Recommended Agent | Also Consider |
|----------|------------------|---------------|
| **PostgreSQL** | database-methodology | ORM specialist if using Prisma/TypeORM |
| **MongoDB** | database-methodology | Document modeling patterns |
| **MySQL** | database-methodology | Relational patterns |
| **Redis** | cache-specialist | Or backend-methodology for simple use |
| **DynamoDB** | database-methodology | NoSQL patterns, AWS-specific |
| **Firebase/Firestore** | firebase-specialist | Firebase ecosystem expert |

**Rule**: Relational or NoSQL → database-methodology. Special platforms (Firebase) → platform specialist.

---

## Project Pattern Examples

### Example 1: Stripe-Integrated SaaS

**Stack**: Next.js, TypeScript, PostgreSQL, Stripe

**Complexity**: HIGH (payments, auth, multi-tenant)

**Recommended Roster**:
```
Core Team:
- fullstack-methodology (Next.js architecture)
- database-methodology (Multi-tenant schema)
- auth-specialist (User accounts, sessions)
- security-specialist (Payment security, Stripe integration)

Supporting:
- performance-specialist (Dashboard optimization)
- qa-methodology (User flow testing)

Skills:
- nextjs-app-router
- prisma-patterns
- stripe-integration-patterns
- auth-patterns
```

**Rationale**: Payments and auth require strategic oversight (security + auth specialists). Next.js handles full stack, so fullstack-methodology. Multi-tenancy adds DB complexity.

---

### Example 2: Internal Admin Dashboard

**Stack**: React, Node/Express, MongoDB

**Complexity**: MEDIUM (internal use, no payments, moderate traffic)

**Recommended Roster**:
```
Core Team:
- frontend-methodology (React architecture)
- backend-methodology (Express API)
- database-methodology (MongoDB schema)

Supporting:
- (none initially - add as needed)

Skills:
- react-tailwind
- express-api-patterns
- mongo-schema-patterns
```

**Rationale**: Internal tool, low security stakes, moderate traffic. No strategic-planner needed initially. Three methodology-specialists cover all domains. Can add performance/qa later if needed.

---

### Example 3: CLI Deployment Tool

**Stack**: Go, Cobra CLI

**Complexity**: LOW (single-purpose, CLI)

**Recommended Roster**:
```
Specialist:
- backend-methodology (Go idioms, CLI architecture)

Skills:
- go-cli-patterns
- go-testing-patterns
```

**Rationale**: CLI tool = focused scope. One specialist sufficient. No strategic oversight needed. Keep it simple.

---

### Example 4: Real-Time Multiplayer Game

**Stack**: Phaser, TypeScript, WebSockets, Node backend

**Complexity**: HIGH (real-time, multiplayer, game logic)

**Recommended Roster**:
```
Core Team:
- game-core-methodology (Game loop, state, multiplayer)
- game-ui-methodology (HUD, menus)
- backend-methodology (WebSocket server, game state sync)
- performance-specialist (Real-time performance critical)

Supporting:
- game-audio-specialist (Sound/music)
- qa-methodology (Game testing, multiplayer edge cases)

Skills:
- phaser-scene-patterns
- phaser-multiplayer-patterns
- websocket-patterns
```

**Rationale**: Real-time multiplayer = HIGH complexity. Game specialists for domain, backend for server, performance specialist essential for smooth gameplay. Strategic oversight needed for real-time sync.

---

### Example 5: Content Blog with CMS

**Stack**: Astro, TypeScript, ContentLayer CMS

**Complexity**: LOW-MEDIUM (content-focused, static generation)

**Recommended Roster**:
```
Core Team:
- frontend-methodology (Astro patterns, component islands)
- content-specialist (Content structure, SEO)

Supporting:
- seo-specialist (If SEO critical for business)

Skills:
- astro-patterns
- content-cms-patterns
- seo-patterns
```

**Rationale**: Content site = frontend-heavy. Astro handles static generation well. Content specialist for editorial and SEO. No backend complexity. Strategic specialist only if SEO is business-critical.

---

## Handoff Guidelines

### To manage-sources
**When**: Recommended agent not in sources

```
"I'd recommend [agent-name], but it's not in your sources.

Let me connect you with manage-sources to add the [guild-name]."
```

### To create-agent
**When**: Need custom agent for project-specific specialty

```
"Your project needs a [specialty] specialist, but I don't see one
in any guild.

Want me to help you create a custom agent for [specialty]?"
```

---

## Anti-Patterns

**Don't**:
- ❌ Recommend full roster for simple projects (over-staffing)
- ❌ Recommend strategic-planner for focused/simple projects
- ❌ Skip security-specialist for payment/auth projects (under-staffing)
- ❌ Recommend agents for domains not in tech stack
- ❌ Ignore user's stated needs in favor of "standard" roster

**Do**:
- ✅ Match roster size to project complexity
- ✅ Include strategic-planner only when justified
- ✅ Start small, add as needed (can always expand)
- ✅ Consider project stage (MVP vs production)
- ✅ Listen to user's constraints (timeline, team size)

---

## Maintenance Notes

This reference should be updated when:
- New project patterns emerge
- New agent classes are introduced
- Official guilds add new agents
- User feedback reveals gaps in recommendations

**Version**: 1.0.0 (2026-02-25)

---

**End of Project Patterns Reference**
