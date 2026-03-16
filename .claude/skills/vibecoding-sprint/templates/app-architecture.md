# Application Architecture

> **Last Updated**: YYYY-MM-DD
> **Updated After Sprint**: `vX.Y.Z-focus-slug`
> **Next Review**: After current sprint completes

## Application Overview

**Name**: [Application Name]
**Purpose**: [1-2 sentence description]
**Primary Users**: [Who uses this app]

## Feature Areas

Each feature area is a logical grouping for sprint planning. Issues should map to one of these areas.

### 1. [Feature Area Name]

| Field | Value |
|-------|-------|
| **Description** | [What this area does] |
| **Primary Files** | `src/[path]/*` |
| **Test Files** | `tests/[path]/*` |
| **Dependencies** | [Other areas this depends on] |
| **Dependents** | [Areas that depend on this] |

**Key Components**:
- `ComponentName` - [Purpose]
- `ComponentName` - [Purpose]

**API Endpoints** (if applicable):
- `GET /api/[endpoint]` - [Purpose]
- `POST /api/[endpoint]` - [Purpose]

---

### 2. [Feature Area Name]

| Field | Value |
|-------|-------|
| **Description** | [What this area does] |
| **Primary Files** | `src/[path]/*` |
| **Test Files** | `tests/[path]/*` |
| **Dependencies** | [Other areas this depends on] |
| **Dependents** | [Areas that depend on this] |

**Key Components**:
- `ComponentName` - [Purpose]

---

### 3. [Feature Area Name]

[Repeat pattern...]

---

## Shared/Core Areas

These are cross-cutting concerns that affect multiple feature areas.

### Authentication & Authorization

| Field | Value |
|-------|-------|
| **Primary Files** | `src/auth/*`, `src/middleware/auth.ts` |
| **Affects** | All protected routes and components |
| **External Services** | [Auth provider if any] |

### Database/Data Layer

| Field | Value |
|-------|-------|
| **Primary Files** | `src/db/*`, `src/models/*` |
| **Type** | [PostgreSQL, MongoDB, etc.] |
| **ORM/Client** | [Prisma, Mongoose, etc.] |

### API Layer

| Field | Value |
|-------|-------|
| **Primary Files** | `src/api/*`, `src/routes/*` |
| **Style** | [REST, GraphQL, tRPC] |
| **Documentation** | [Link or path to API docs] |

### UI/Design System

| Field | Value |
|-------|-------|
| **Primary Files** | `src/components/ui/*` |
| **Framework** | [Tailwind, MUI, etc.] |
| **Design Tokens** | [Path to theme/tokens] |

---

## Discipline Areas

For global issues that span features, group by these disciplines.

| Discipline | Scope | Example Issues |
|------------|-------|----------------|
| **Security** | Auth, input validation, CSRF, XSS | Rate limiting, secret rotation |
| **Performance** | Load times, bundle size, caching | Lazy loading, query optimization |
| **Accessibility** | ARIA, keyboard nav, screen readers | Color contrast, focus management |
| **SEO** | Meta tags, structured data, sitemap | OG tags, canonical URLs |
| **Testing** | Unit, integration, e2e coverage | Test gaps, flaky tests |
| **DevOps** | CI/CD, deployment, monitoring | Pipeline fixes, alerting |

---

## Page/Route Map

For UI applications, map routes to feature areas.

| Route | Feature Area | Components | Notes |
|-------|--------------|------------|-------|
| `/` | [Area] | `HomePage` | [Notes] |
| `/login` | Auth | `LoginForm` | [Notes] |
| `/dashboard` | [Area] | `Dashboard`, `Widgets` | [Notes] |
| `/settings` | [Area] | `SettingsForm` | [Notes] |
| `/api/*` | API Layer | N/A | Backend routes |

---

## External Integrations

| Service | Purpose | Files | Credentials Location |
|---------|---------|-------|---------------------|
| [Service] | [Purpose] | `src/integrations/[name]` | `.env` / secrets manager |
| [Service] | [Purpose] | `src/integrations/[name]` | `.env` / secrets manager |

---

## Architecture Decisions

Key decisions that affect how the app is structured.

| Decision | Choice | Rationale | Date |
|----------|--------|-----------|------|
| State Management | [Redux/Zustand/Context] | [Why] | YYYY-MM-DD |
| API Style | [REST/GraphQL/tRPC] | [Why] | YYYY-MM-DD |
| Database | [PostgreSQL/MongoDB] | [Why] | YYYY-MM-DD |
| Auth Strategy | [JWT/Session/OAuth] | [Why] | YYYY-MM-DD |

---

## Update Log

| Date | Sprint | Changes |
|------|--------|---------|
| YYYY-MM-DD | `vX.Y.Z-focus` | Initial architecture doc |
| YYYY-MM-DD | `vX.Y.Z-focus` | Added [new feature area] |
| YYYY-MM-DD | `vX.Y.Z-focus` | Updated [area] dependencies |

---

*Architecture document maintained by vibecoding-sprint skill*
