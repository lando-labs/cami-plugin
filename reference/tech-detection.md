# Tech Stack Auto-Discovery Reference

> **For CAMI Skills**: Use this reference to automatically detect project tech stacks without requiring users to maintain configuration files.

**Key Principle**: Discovery over Configuration. Scan project files to understand the tech stack, then offer relevant capabilities.

---

## Detection Sources

Scan these files in order to build a complete tech stack profile:

### Package Managers & Dependency Files

| File | Technologies Detected | Priority |
|------|----------------------|----------|
| `package.json` | Node.js, npm/yarn/pnpm, React, Vue, Angular, Svelte, Next.js, TypeScript, Express, testing libs | **HIGH** |
| `go.mod` | Go version, Go modules | **HIGH** |
| `requirements.txt` | Python packages | **MEDIUM** |
| `pyproject.toml` | Python version, Poetry, Django, Flask, FastAPI | **HIGH** |
| `Cargo.toml` | Rust version, crates | **HIGH** |
| `composer.json` | PHP version, Laravel, Symfony | **HIGH** |
| `Gemfile` | Ruby version, Rails, Sinatra | **HIGH** |
| `pom.xml` / `build.gradle` | Java, Spring, Maven/Gradle | **MEDIUM** |

### Version Lock Files

| File | Information |
|------|-------------|
| `.nvmrc` | Node.js version |
| `.node-version` | Node.js version |
| `.ruby-version` | Ruby version |
| `.python-version` | Python version |
| `.go-version` | Go version |

### Configuration Files

| File Pattern | Technologies Detected |
|--------------|----------------------|
| `tsconfig.json` | TypeScript configuration, compiler options |
| `vite.config.*` | Vite bundler, plugins |
| `next.config.*` | Next.js, app/pages router |
| `nuxt.config.*` | Nuxt.js (Vue) |
| `tailwind.config.*` | Tailwind CSS |
| `postcss.config.*` | PostCSS, CSS tooling |
| `jest.config.*` | Jest testing |
| `vitest.config.*` | Vitest testing |
| `playwright.config.*` | Playwright E2E testing |
| `cypress.config.*` | Cypress E2E testing |
| `.eslintrc.*` | ESLint, code quality |
| `.prettierrc.*` | Prettier, code formatting |

### Infrastructure Files

| File | Information |
|------|-------------|
| `docker-compose.yml` | Services (PostgreSQL, MySQL, MongoDB, Redis, etc.), container orchestration |
| `Dockerfile` | Base images, runtime environment |
| `firebase.json` | Firebase services (hosting, functions, firestore, etc.) |
| `vercel.json` | Vercel deployment |
| `.firebaserc` | Firebase project IDs |

### Framework-Specific Files

| File/Directory | Framework |
|----------------|-----------|
| `angular.json` | Angular |
| `vue.config.js` | Vue.js |
| `svelte.config.js` | SvelteKit |
| `remix.config.js` | Remix |
| `astro.config.*` | Astro |
| `app/` directory | Next.js App Router (if with next.config) |
| `pages/` directory | Next.js Pages Router |
| `src-tauri/` | Tauri (Rust desktop apps) |

---

## Detection Logic

Follow this sequence when scanning a project:

### Step 1: Initial File Discovery

```
1. Check for .git directory (is this a repository?)
2. Check for .claude directory (CAMI already initialized?)
3. Scan for package manager files (package.json, go.mod, etc.)
4. Note: Use Glob tool to find files efficiently
```

### Step 2: Parse Primary Files

**For Node.js projects (package.json exists)**:
```json
{
  "dependencies": {
    "react": "^19.0.0",           → Frontend: React 19
    "next": "^15.0.0",            → Framework: Next.js 15
    "express": "^4.18.0",         → Backend: Express
    "postgres": "^3.4.0",         → Database client: PostgreSQL
    "@tailwindcss/typography": "*" → Styling: Tailwind
  },
  "devDependencies": {
    "typescript": "^5.0.0",       → Language: TypeScript 5
    "vite": "^5.0.0",             → Build tool: Vite
    "vitest": "^1.0.0",           → Testing: Vitest
    "playwright": "^1.40.0"       → E2E testing: Playwright
  },
  "engines": {
    "node": ">=20.0.0"            → Runtime: Node.js 20+
  }
}
```

**Detection logic for package.json**:
```
1. Read dependencies and devDependencies
2. Check for framework markers:
   - "react" → React
   - "vue" → Vue
   - "angular" → Angular
   - "@angular/core" → Angular
   - "svelte" → Svelte
3. Check for meta-frameworks:
   - "next" → Next.js
   - "@remix-run/react" → Remix
   - "nuxt" → Nuxt
   - "astro" → Astro
4. Check for backend frameworks:
   - "express" → Express
   - "fastify" → Fastify
   - "koa" → Koa
   - "nest" → NestJS
5. Check for database clients:
   - "pg" or "postgres" → PostgreSQL
   - "mysql" or "mysql2" → MySQL
   - "mongodb" → MongoDB
   - "redis" → Redis
6. Check for styling:
   - "tailwindcss" → Tailwind CSS
   - "styled-components" → Styled Components
   - "@emotion/react" → Emotion
7. Check for testing:
   - "jest" → Jest
   - "vitest" → Vitest
   - "mocha" → Mocha
   - "playwright" → Playwright
   - "cypress" → Cypress
8. Check for build tools:
   - "vite" → Vite
   - "webpack" → Webpack
   - "esbuild" → esbuild
   - "turbopack" → Turbopack
```

**For Go projects (go.mod exists)**:
```
module github.com/user/project

go 1.22                           → Go version 1.22

require (
    github.com/gin-gonic/gin v1.9.0      → Web framework: Gin
    github.com/lib/pq v1.10.0            → Database: PostgreSQL
    github.com/redis/go-redis/v9 v9.0.0  → Database: Redis
)
```

**For Python projects (pyproject.toml or requirements.txt)**:
```toml
[tool.poetry]
python = "^3.11"                  → Python 3.11+

[tool.poetry.dependencies]
django = "^5.0"                   → Framework: Django 5
psycopg2-binary = "^2.9"          → Database: PostgreSQL
redis = "^5.0"                    → Database: Redis
```

**For Rust projects (Cargo.toml)**:
```toml
[package]
edition = "2021"                  → Rust edition

[dependencies]
actix-web = "4.0"                 → Web framework: Actix
tokio = { version = "1", features = ["full"] } → Async runtime
sqlx = { version = "0.7", features = ["postgres"] } → Database: PostgreSQL
```

### Step 3: Cross-Reference Configuration Files

Check for confirming evidence:

```
- If "react" in package.json → Look for tsconfig.json (TypeScript?)
- If "tailwindcss" detected → Look for tailwind.config.* (config details)
- If "next" detected → Look for next.config.* (App Router vs Pages Router)
- If "vite" detected → Look for vite.config.* (plugins, features)
```

### Step 4: Scan Infrastructure

**docker-compose.yml detection**:
```yaml
services:
  postgres:
    image: postgres:15              → Database: PostgreSQL 15
  redis:
    image: redis:7-alpine           → Cache: Redis 7
  mongodb:
    image: mongo:7                  → Database: MongoDB 7
```

**Firebase detection**:
```json
{
  "hosting": { ... },               → Firebase Hosting
  "functions": { ... },             → Firebase Functions
  "firestore": { ... }              → Firestore Database
}
```

### Step 5: Detect Testing Setup

| File | Testing Stack |
|------|---------------|
| `jest.config.*` | Jest unit testing |
| `vitest.config.*` | Vitest unit testing |
| `playwright.config.*` | Playwright E2E |
| `cypress.config.*` | Cypress E2E |
| `__tests__/` directory | Test files present |
| `*.test.*` or `*.spec.*` files | Test coverage |

---

## Tech Stack Categories

Group detected technologies into these categories:

### Frontend
- **Frameworks**: React, Vue, Angular, Svelte, Solid
- **Meta-frameworks**: Next.js, Nuxt, Remix, Astro, SvelteKit
- **State Management**: Redux, Zustand, Jotai, Pinia (Vue), NgRx (Angular)
- **UI Libraries**: Material UI, Chakra UI, Ant Design, shadcn/ui

### Backend
- **Node.js**: Express, Fastify, Koa, NestJS, Hono
- **Go**: Gin, Echo, Fiber, Chi
- **Python**: Django, Flask, FastAPI, Sanic
- **Ruby**: Rails, Sinatra, Hanami
- **Rust**: Actix, Rocket, Axum, Warp
- **PHP**: Laravel, Symfony, Slim
- **Java**: Spring Boot, Micronaut, Quarkus

### Database
- **SQL**: PostgreSQL, MySQL, MariaDB, SQLite
- **NoSQL**: MongoDB, Redis, DynamoDB, CouchDB
- **ORMs**: Prisma, TypeORM, Sequelize, Drizzle, SQLx, Diesel

### Build Tools
- **Bundlers**: Vite, Webpack, esbuild, Rollup, Parcel, Turbopack
- **Task Runners**: npm scripts, Gulp, Grunt
- **Monorepo**: Turborepo, Nx, Lerna, Rush

### Styling
- **Frameworks**: Tailwind CSS, Bootstrap, Bulma
- **CSS-in-JS**: Styled Components, Emotion, Stitches
- **Preprocessors**: Sass, Less, PostCSS
- **Design Systems**: Custom, shadcn/ui, Chakra, Material UI

### Testing
- **Unit**: Jest, Vitest, Mocha, Jasmine, pytest, Go testing
- **E2E**: Playwright, Cypress, Selenium, Puppeteer
- **Visual**: Chromatic, Percy, BackstopJS

### DevOps & Deployment
- **Platforms**: Vercel, Netlify, Firebase Hosting, AWS, Fly.io
- **Containers**: Docker, Docker Compose, Kubernetes
- **CI/CD**: GitHub Actions, CircleCI, GitLab CI, Jenkins

---

## Agent/Skill Recommendations

Map detected stacks to recommended CAMI capabilities:

### Frontend Stacks

| Detected Stack | Recommended Agents | Recommended Skills | Rationale |
|---------------|-------------------|-------------------|-----------|
| React + TypeScript | frontend-methodology | react-patterns, typescript-patterns | Modern React architecture decisions |
| React + Tailwind | frontend-methodology, ui-specialist | react-tailwind | Component composition patterns |
| Next.js App Router | fullstack-methodology | nextjs-app-router | Server components, routing |
| Vue 3 + Composition API | frontend-methodology | vue-composition | Vue 3 best practices |
| Angular | frontend-methodology | angular-patterns | Angular modules, services |

### Backend Stacks

| Detected Stack | Recommended Agents | Recommended Skills | Rationale |
|---------------|-------------------|-------------------|-----------|
| Node + Express | backend-methodology | express-api-patterns | RESTful API design |
| Node + NestJS | backend-methodology | nestjs-patterns | DI, modules, decorators |
| Go + Gin | backend-methodology | go-web-patterns | Go idioms, concurrency |
| Python + Django | backend-methodology | django-patterns | MTV architecture |
| Python + FastAPI | backend-methodology | fastapi-patterns | Async Python, type hints |
| Rust + Actix | backend-methodology | rust-web-patterns | Async Rust, ownership |

### Full Stack Combinations

| Detected Stack | Recommended Agents | Recommended Skills | Rationale |
|---------------|-------------------|-------------------|-----------|
| MERN (Mongo+Express+React+Node) | fullstack-methodology | mern-patterns | End-to-end JavaScript |
| Next.js + Prisma + PostgreSQL | fullstack-methodology | nextjs-prisma | Modern full-stack |
| Django + React | fullstack-methodology | django-react-separation | API-first architecture |

### Database-Specific

| Detected Database | Recommended Agents | Recommended Skills |
|------------------|-------------------|-------------------|
| PostgreSQL | database-methodology | postgres-schema-design |
| MongoDB | database-methodology | mongo-schema-design |
| Prisma ORM | data-methodology | prisma-patterns |

### Testing-Specific

| Detected Testing | Recommended Agents | Recommended Skills |
|------------------|-------------------|-------------------|
| Playwright | qa-methodology | playwright-e2e-patterns |
| Jest + React Testing Library | qa-methodology | react-testing-patterns |
| Vitest | qa-methodology | vitest-patterns |

### DevOps-Specific

| Detected Platform | Recommended Agents | Recommended Skills |
|------------------|-------------------|-------------------|
| Docker Compose | devops-methodology | docker-compose-patterns |
| Firebase | firebase-specialist | firebase-deployment |
| Vercel | deployment-specialist | vercel-deployment |

---

## User Confirmation Flow

After detection, present findings to the user in this format:

### Presentation Template

```
I scanned your project and detected the following tech stack:

**Frontend**
- React 19.0.0 (package.json)
- TypeScript 5.0.0 (package.json)
- Tailwind CSS 3.4.0 (tailwind.config.js)
- Vite 5.0.0 (vite.config.ts)

**Backend**
- Node.js 20.0.0 (.nvmrc)
- Express 4.18.0 (package.json)

**Database**
- PostgreSQL 15 (docker-compose.yml)
- Redis 7 (docker-compose.yml)

**Testing**
- Vitest 1.0.0 (package.json)
- Playwright 1.40.0 (package.json)

**Build & Deploy**
- Vite bundler
- Docker Compose for local development

---

Based on this stack, I'd recommend adding these capabilities:

**Agents** (methodology experts):
- frontend-methodology - React architecture decisions
- backend-methodology - API design patterns
- database-methodology - Schema design guidance

**Skills** (implementation patterns):
- react-tailwind - Component composition patterns
- express-api-patterns - RESTful API structure
- postgres-schema-design - Database modeling

Does this look right? I can adjust if I missed something or if you don't need certain capabilities.
```

### Confirmation Questions

```
1. "Does this tech stack look accurate?"
2. "Did I miss anything? (e.g., GraphQL, background jobs, etc.)"
3. "Which capabilities would you like me to deploy?"
4. "Want me to save this profile for future reference?"
```

### Handling Corrections

```
User: "Actually, we use Fastify, not Express"

CAMI: "Got it! Updating to Fastify. That changes my recommendation -
I'd suggest the fastify-patterns skill instead of express-api-patterns.
Fastify's plugin system and decorators are quite different from Express."
```

---

## Project Profile Output

When user confirms, optionally save this profile:

### Profile Location

```
<project>/.claude/tech-profile.md
```

### Profile Format

```markdown
# Project Tech Profile

> Auto-detected: 2026-02-25
> Last updated: 2026-02-25

## Tech Stack

### Frontend
- **Framework**: React 19.0.0 (package.json)
- **Language**: TypeScript 5.0.0 (tsconfig.json, package.json)
- **Styling**: Tailwind CSS 3.4.0 (tailwind.config.js, package.json)
- **Build Tool**: Vite 5.0.0 (vite.config.ts)

### Backend
- **Runtime**: Node.js 20.0.0 (.nvmrc)
- **Framework**: Express 4.18.0 (package.json)
- **API Style**: RESTful

### Database
- **Primary**: PostgreSQL 15 (docker-compose.yml)
- **Cache**: Redis 7 (docker-compose.yml)
- **ORM**: None detected

### Testing
- **Unit Testing**: Vitest 1.0.0 (package.json)
- **E2E Testing**: Playwright 1.40.0 (playwright.config.ts)

### Build & Deploy
- **Bundler**: Vite 5.0.0
- **Container**: Docker Compose (docker-compose.yml)
- **Hosting**: Not detected (check for vercel.json, firebase.json)

## Deployed Capabilities

### Agents
- frontend-methodology (v1.0.0) - Deployed: 2026-02-25
- backend-methodology (v1.0.0) - Deployed: 2026-02-25

### Skills
- react-tailwind (v1.0.0) - Deployed: 2026-02-25
- express-api-patterns (v1.0.0) - Deployed: 2026-02-25

## Notes

- Uses App Router pattern (detected: app/ directory)
- TypeScript strict mode enabled (tsconfig.json)
- Tailwind configured with custom theme (tailwind.config.js)

## Future Considerations

Based on your stack, you might want to consider:
- prisma-patterns (if you add an ORM)
- graphql-patterns (if you add GraphQL)
- ci-cd-patterns (when ready to automate deployments)
```

### Profile Usage

Skills can read this profile to:
1. Skip re-detection on subsequent runs
2. Track capability deployment history
3. Suggest complementary capabilities
4. Detect tech stack drift over time

**Update triggers**:
- package.json changes (dependencies added/removed)
- New config files added
- User requests re-scan
- Manual edits to profile

---

## Implementation Notes for Skills

### Reading Files Efficiently

```
1. Use Glob to find files (faster than recursive ls/find)
2. Use Read to parse file contents
3. Parse JSON with built-in JSON tools
4. Parse YAML with standard parsers
5. Cache results within a single skill invocation
```

### Error Handling

```
- File not found? → Skip gracefully, note in detection report
- Malformed JSON? → Report error, continue with other files
- No package manager detected? → Ask user about tech stack
- Multiple conflicting signals? → Present ambiguity, ask for clarification
```

### Performance Considerations

```
- Don't read every file in project (use targeted Glob patterns)
- Parse only necessary files (priority order)
- Stop early if clear signal found (e.g., package.json is definitive for Node)
- Limit depth of directory scans (respect .gitignore)
```

### Edge Cases

| Scenario | Handling |
|----------|----------|
| Monorepo with multiple packages | Detect root vs package-level stacks |
| Legacy project (no package.json) | Fall back to file extension detection |
| Polyglot project (Go + Node) | Detect both, ask which is primary |
| No clear framework | Present as "vanilla" + language |
| Conflicting versions | Use lockfile as source of truth |

---

## Example Detection Workflows

### Scenario 1: Modern Next.js App

**Files present**:
- package.json (next, react, tailwindcss)
- next.config.ts
- app/ directory
- tailwind.config.js
- .nvmrc

**Detection output**:
```
Tech Stack: Next.js App Router
- Next.js 15.0.0 (App Router)
- React 19
- TypeScript 5
- Tailwind CSS 3
- Node.js 20

Recommended: fullstack-methodology, nextjs-app-router, react-tailwind
```

### Scenario 2: Go API + PostgreSQL

**Files present**:
- go.mod (gin, lib/pq)
- docker-compose.yml (postgres)

**Detection output**:
```
Tech Stack: Go REST API
- Go 1.22
- Gin web framework
- PostgreSQL 15
- Docker Compose

Recommended: backend-methodology, go-web-patterns, postgres-schema-design
```

### Scenario 3: Django Full Stack

**Files present**:
- pyproject.toml (django, psycopg2)
- requirements.txt
- docker-compose.yml (postgres, redis)

**Detection output**:
```
Tech Stack: Django Full Stack
- Python 3.11
- Django 5.0
- PostgreSQL (via psycopg2)
- Redis
- Docker Compose

Recommended: fullstack-methodology, django-patterns, postgres-schema-design
```

---

## Voice Integration

When presenting detection results, use CAMI's scout voice:

**Scout perspective**:
- "I scanned your project" not "Detected files"
- "Your stack looks like..." not "Analysis shows"
- "Based on what I found, I'd recommend..." not "Suggested capabilities"
- "Want me to add these to your roster?" not "Deploy these agents?"

**Confidence levels**:
- High confidence: "I detected React 19" (package.json)
- Medium confidence: "Looks like you're using PostgreSQL" (docker-compose)
- Low confidence: "I think you might be using X" (indirect signals)

**Handling unknowns**:
- "I couldn't determine your database - are you using one?"
- "No testing framework detected - want recommendations?"
- "I see TypeScript, but no framework - is this a library project?"

---

## Maintenance & Updates

### When to Re-Scan

1. User adds new dependencies (package.json changed)
2. User requests capabilities for tech not in profile
3. User explicitly asks: "re-scan my stack"
4. New config files detected (e.g., docker-compose.yml added)
5. Major version upgrades detected

### Keeping Detection Current

This reference should be updated when:
- New frameworks gain popularity
- New package managers emerge
- Detection patterns change
- User feedback reveals missed technologies

### Version History

- v1.0.0 (2026-02-25): Initial auto-discovery reference
  - Covers: Node, Go, Python, Rust, Ruby, PHP
  - Frameworks: React, Vue, Angular, Next.js, Django, Rails, etc.
  - Databases: PostgreSQL, MySQL, MongoDB, Redis
  - Testing: Jest, Vitest, Playwright, Cypress

---

**End of Tech Detection Reference**
