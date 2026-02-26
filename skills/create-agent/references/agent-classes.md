# Agent Classes Reference

> **For create-agent skill**: Use this reference to determine the appropriate class for new agents and configure them correctly.

**Version**: 2.0.0 (Two-class system)
**Last updated**: 2026-02-25

---

## Overview

CAMI uses a **two-class system** for agents. Each class represents a distinct role in the development lifecycle.

**Key Change**: workflow-specialist is **retired**. Procedural workflows are now handled by **skills**, not agents.

---

## The Two Classes

### 1. methodology-specialist (Feature Builder)

**Identity**: Domain experts who guide feature development decisions.

**Purpose**: Provide methodology, judgment, and best practices for building features within a specific domain (frontend, backend, database, etc.).

**Core Philosophy**: Methodology over implementation. They guide WHAT and WHY, delegating HOW to skills.

**Typical Domains**:
- Frontend methodology (UI architecture, state management)
- Backend methodology (API design, service architecture)
- Database methodology (schema design, query optimization)
- API methodology (REST/GraphQL design, versioning)
- Testing methodology (test strategy, coverage decisions)

**Model Recommendation**: `sonnet` (balanced judgment + speed)

**Phase Weights**:
| Phase | Weight | Why |
|-------|--------|-----|
| Analyze | 30% | Understand requirements deeply |
| Design | 40% | Core strength - methodology decisions |
| Implement | 20% | Guide implementation, delegate to skills |
| Verify | 10% | Quality standards, review |

**Skill-Aware**: Yes - expects to work with implementation skills.

**Example Decisions**:
- "Should we use Redux or Context API for this app?"
- "How should we structure API routes for this feature?"
- "When should we denormalize this database schema?"
- "What test coverage level is appropriate here?"

**Example Agent Names**:
- `frontend-methodology`
- `backend-methodology`
- `database-methodology`
- `api-methodology`
- `mobile-methodology`

---

### 2. strategic-planner (System Architect)

**Identity**: Strategic thinkers who guide system-level decisions and research.

**Purpose**: Architecture, optimization, security, and research that spans multiple domains or requires deep analysis.

**Core Philosophy**: Strategic thinking before tactical implementation. They plan, then delegate execution.

**Typical Domains**:
- System architecture (multi-service design, scalability)
- Security (threat modeling, compliance)
- Performance optimization (profiling, bottleneck analysis)
- DevOps strategy (deployment pipelines, infrastructure)
- Research (technology evaluation, proof-of-concepts)

**Model Recommendation**: `opus` (complex reasoning, strategic thinking)

**Phase Weights**:
| Phase | Weight | Why |
|-------|--------|-----|
| Analyze | 40% | Deep analysis is their foundation |
| Design | 40% | Strategic design decisions |
| Implement | 10% | Minimal - they plan, others execute |
| Verify | 10% | Validate strategy, not implementation |

**Skill-Aware**: No - they operate at a higher level than skills.

**Example Decisions**:
- "How should we architect this multi-tenant system?"
- "What security measures are needed for HIPAA compliance?"
- "Should we use microservices or a monolith for this project?"
- "How do we optimize this app for 10x traffic growth?"

**Example Agent Names**:
- `system-architect`
- `security-specialist`
- `performance-specialist`
- `devops-architect`
- `data-architect`

---

## Class Comparison

| Aspect | methodology-specialist | strategic-planner |
|--------|------------------------|-------------------|
| **Focus** | Domain expertise | Strategic thinking |
| **Scope** | Single domain (frontend, backend, etc.) | Cross-domain, system-level |
| **Decisions** | Feature development methodology | Architecture, optimization, research |
| **Model** | Sonnet (fast, balanced) | Opus (deep reasoning) |
| **Skill-Aware** | Yes - works with skills | No - higher abstraction level |
| **Typical Phase** | Design + Implement | Analyze + Design |
| **Triggers** | "build", "implement", "develop" | "architect", "optimize", "plan", "research" |

---

## Detection Logic

When creating an agent, use these signals to determine the class:

### Signals for methodology-specialist

**Domain Words**:
- "frontend", "backend", "database", "API", "mobile"
- "UI", "service", "schema", "endpoint"

**Action Words**:
- "build", "implement", "develop", "create"
- "design features", "develop components"

**Decision Types**:
- "when to use X pattern"
- "how to structure Y"
- "best practices for Z"

**Examples**:
```
"I need an agent for React component architecture" → methodology-specialist
"Create an agent for API design" → methodology-specialist
"Help me with database schema decisions" → methodology-specialist
```

### Signals for strategic-planner

**Domain Words**:
- "architecture", "security", "performance", "scalability"
- "system", "infrastructure", "optimization"

**Action Words**:
- "architect", "plan", "research", "optimize"
- "evaluate", "strategize", "analyze"

**Decision Types**:
- "should we use microservices or monolith"
- "how do we scale to X users"
- "what's the security posture for Y"

**Examples**:
```
"I need an agent for system architecture" → strategic-planner
"Create a security expert" → strategic-planner
"Help me optimize performance" → strategic-planner
```

### When Ambiguous

Ask clarifying questions:

```
User: "I need an architecture agent"

You: "Architecture can mean different things. What kind?

- **Domain architecture** (e.g., React app structure, API organization)
  → methodology-specialist

- **System architecture** (e.g., microservices, scaling strategy)
  → strategic-planner

Which fits better?"
```

---

## Agent Frontmatter Templates

### methodology-specialist Template

```yaml
---
name: domain-methodology
version: "1.0.0"
description: Use this agent PROACTIVELY when [specific methodology decisions in domain]. Triggers on [domain] architecture questions, [technology] best practices, [pattern] decisions. Provides judgment on WHEN and WHY to use specific approaches, delegates implementation (HOW) to skills.
class: methodology-specialist
specialty: domain-methodology
model: sonnet
skill_aware: true
---
```

**Example (Frontend)**:
```yaml
---
name: frontend-methodology
version: "1.0.0"
description: Use this agent PROACTIVELY when making frontend architecture decisions. Triggers on React/Vue/Angular questions, component design patterns, state management choices, routing strategies. Provides judgment on WHEN and WHY to use specific patterns, delegates implementation (HOW) to skills.
class: methodology-specialist
specialty: frontend-methodology
model: sonnet
skill_aware: true
---
```

### strategic-planner Template

```yaml
---
name: domain-architect
version: "1.0.0"
description: Use this agent PROACTIVELY when planning [system-level concern]. Triggers on [architectural decisions], [optimization strategies], [security reviews], [scalability planning]. Provides strategic analysis and system-level design recommendations.
class: strategic-planner
specialty: domain-architecture
model: opus
skill_aware: false
---
```

**Example (System Architecture)**:
```yaml
---
name: system-architect
version: "1.0.0"
description: Use this agent PROACTIVELY when planning system architecture or making technology choices. Triggers on microservices vs monolith decisions, scaling strategies, multi-service design, infrastructure planning. Provides strategic analysis and architectural recommendations.
class: strategic-planner
specialty: system-architecture
model: opus
skill_aware: false
---
```

---

## Phase Weights Explained

Phase weights guide how agents allocate their effort across the development lifecycle.

### Analyze Phase
**What**: Understanding requirements, context, constraints.

- methodology-specialist: 30% (understand domain needs)
- strategic-planner: 40% (deep analysis is foundation)

### Design Phase
**What**: Making decisions, creating plans, defining approaches.

- methodology-specialist: 40% (core strength - methodology decisions)
- strategic-planner: 40% (strategic design is their purpose)

### Implement Phase
**What**: Hands-on building, coding, executing.

- methodology-specialist: 20% (guide implementation, delegate to skills)
- strategic-planner: 10% (minimal - they plan, others execute)

### Verify Phase
**What**: Testing, reviewing, quality assurance.

- methodology-specialist: 10% (quality standards, code review)
- strategic-planner: 10% (validate strategy, not implementation)

**Usage**: Agent architects use these weights to configure agent behavior and priorities.

---

## Model Selection Guide

### When to Use Sonnet

**Best for**: methodology-specialist agents

**Why**:
- Fast enough for frequent consultations
- Strong enough for domain methodology decisions
- Cost-effective for iterative feature development
- Balanced judgment without overthinking

**Use cases**:
- Frontend/backend/database methodology
- Feature-level architectural decisions
- Best practice guidance
- Pattern selection

### When to Use Opus

**Best for**: strategic-planner agents

**Why**:
- Deep reasoning for complex strategic decisions
- Handles multi-variable trade-off analysis
- Better at research and technology evaluation
- Worth the cost for high-impact decisions

**Use cases**:
- System architecture planning
- Security threat modeling
- Performance optimization strategy
- Technology research and evaluation

### When to Use Haiku

**Note**: Not recommended for CAMI agents. Haiku is better suited for lightweight tasks that don't require methodology or strategic judgment.

**If needed**: Consider if a **skill** would be more appropriate instead.

---

## Skill Pairing Recommendations

### methodology-specialist Agents

**Should pair with**: Implementation skills for their domain.

**Example Pairings**:
| Agent | Complementary Skills |
|-------|---------------------|
| frontend-methodology | react-patterns, vue-patterns, typescript-patterns |
| backend-methodology | express-api-patterns, nestjs-patterns, go-web-patterns |
| database-methodology | postgres-schema-design, mongo-schema-design, prisma-patterns |
| api-methodology | rest-api-scaffold, graphql-schema-gen |

**Why**: Agents provide judgment (WHEN/WHY), skills provide implementation (HOW).

### strategic-planner Agents

**Typically don't pair with skills**: They operate at a higher abstraction level.

**Exception**: May reference skills when planning implementation strategy.

---

## Retired Class: workflow-specialist

**Status**: Retired in v2.0.0

**Why retired**: Workflow-specialist described exactly what **skills** are designed to do - execute procedural workflows and checklists.

**Migration**:
- Old workflow-specialist agents → Convert to skills
- New workflow requests → Direct to skill-architect

**What to say when users ask**:
```
"Workflows and procedures are now handled by skills, not agents.

Skills are perfect for repeatable processes like:
- Pre-commit checklists
- Deployment procedures
- Code generation workflows
- Testing automation

Want me to help you create a skill instead?"
```

---

## Common Mistakes to Avoid

### Mistake 1: Using methodology-specialist for System-Level Work

**Wrong**:
```yaml
name: performance-methodology
class: methodology-specialist
# Performance optimization is system-level → strategic-planner
```

**Right**:
```yaml
name: performance-specialist
class: strategic-planner
```

### Mistake 2: Using strategic-planner for Domain-Specific Work

**Wrong**:
```yaml
name: react-architect
class: strategic-planner
# React architecture is domain-specific → methodology-specialist
```

**Right**:
```yaml
name: frontend-methodology
class: methodology-specialist
```

### Mistake 3: Creating Agents for Workflows

**Wrong**:
```yaml
name: deployment-workflow
class: methodology-specialist
# Workflows are procedures → skill, not agent
```

**Right**:
```
Create a deployment-workflow SKILL instead
```

### Mistake 4: Wrong Model for Class

**Wrong**:
```yaml
name: system-architect
class: strategic-planner
model: sonnet  # Too lightweight for strategic work
```

**Right**:
```yaml
name: system-architect
class: strategic-planner
model: opus  # Deep reasoning for strategy
```

---

## Decision Flowchart

```
User needs help with [something]
          ↓
    Is it procedural?
    (checklist, workflow, automation)
          ↓
        YES → Create a SKILL
          ↓
        NO → Create an AGENT
          ↓
    What kind of agent?
          ↓
    Domain-specific decisions?
    (frontend, backend, database, etc.)
          ↓
        YES → methodology-specialist (sonnet)
          ↓
        NO → Is it system-level?
              (architecture, security, performance)
          ↓
        YES → strategic-planner (opus)
          ↓
        NO → Ask for clarification
```

---

## Version History

### v2.0.0 (2026-02-25)
- **BREAKING**: Retired workflow-specialist class
- Two-class system: methodology-specialist + strategic-planner
- Skill-awareness for methodology-specialist
- Clear model recommendations per class
- Phase weight definitions

### v1.0.0 (Initial)
- Three-class system (deprecated)

---

## Related References

- `references/agent-vs-skill.md` - When to create agent vs skill
- `reference/voice/scout-persona.md` - CAMI voice for presenting recommendations
- Agent-architect agent - Uses this reference to create agents
- Skill-architect agent - Creates skills (redirect target for workflows)

---

**End of Agent Classes Reference**
