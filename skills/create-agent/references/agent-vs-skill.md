# Agent vs Skill Decision Guide

> **For create-agent skill**: Use this guide to detect when users need a skill instead of an agent, and redirect them appropriately.

**Last updated**: 2026-02-25

---

## Core Distinction

| Capability | Purpose | Provides | Example |
|------------|---------|----------|---------|
| **Agent** | Methodology & Judgment | WHEN and WHY | "Should I use Redux or Context API?" |
| **Skill** | Implementation & Patterns | HOW and WHAT | "Generate a React component with state" |

**Key Insight**: If the request is about executing a procedure or generating code, it's a skill. If it's about making decisions or providing guidance, it's an agent.

---

## Detection Framework

### Skill Signals (Redirect to skill-architect)

When you hear these words or phrases, the user needs a **SKILL**:

#### Process & Workflow Words
- "checklist"
- "procedure"
- "steps"
- "workflow"
- "every time I..."
- "whenever I..."
- "automate"
- "run these commands"

#### Generation & Implementation Words
- "generate code"
- "create files"
- "scaffold"
- "template"
- "boilerplate"
- "starter"

#### Syntax & Pattern Words
- "syntax for..."
- "pattern for..."
- "how to write..."
- "structure for..."
- "format for..."

#### Repetition Indicators
- "I keep doing..."
- "I always need to..."
- "every project needs..."
- "repetitive task"

---

### Agent Signals (Create an agent)

When you hear these words or phrases, the user needs an **AGENT**:

#### Expertise & Judgment Words
- "expert"
- "specialist"
- "architect"
- "advisor"
- "consultant"
- "methodologist"

#### Decision-Making Words
- "decisions about..."
- "when to..."
- "whether to..."
- "should I..."
- "which approach..."
- "trade-offs"

#### Guidance & Best Practices
- "best practices"
- "guidance on..."
- "help me think through..."
- "review my approach"
- "design decisions"

#### Methodology Indicators
- "architecture"
- "strategy"
- "principles"
- "philosophy"
- "methodology"

---

## Decision Matrix

| User Request | Agent or Skill? | Why | Create |
|--------------|-----------------|-----|--------|
| "Help me decide between Redux and Context API" | Agent | Requires judgment and trade-off analysis | frontend-methodology agent |
| "Generate React components with Redux" | Skill | Code generation pattern | react-redux-component skill |
| "Design my database schema" | Agent | Schema design requires domain judgment | database-methodology agent |
| "Generate SQL migration files" | Skill | Code generation from template | sql-migration-gen skill |
| "Review my API architecture" | Agent | Requires architectural judgment | api-methodology agent |
| "Scaffold RESTful API endpoints" | Skill | Repetitive code generation | rest-api-scaffold skill |
| "When should I use microservices?" | Agent | Strategic decision-making | system-architect agent |
| "Create a Docker Compose setup" | Skill | Template generation | docker-compose-gen skill |
| "Run tests before every commit" | Skill | Automated workflow | pre-commit-testing skill |
| "Help me plan my testing strategy" | Agent | Strategic test planning | qa-methodology agent |

---

## Examples with Redirects

### Example 1: Workflow Request → Skill

```
User: "I need an agent that runs tests and linting before every commit"

Detection:
- "runs tests" → execution, not judgment
- "before every commit" → automated workflow
- Repeatable procedure → SKILL

Response:
"That's a workflow - perfect for a skill, not an agent. Skills handle
automated procedures and repeatable tasks.

What you want is a pre-commit-testing skill that:
- Runs your test suite
- Runs linting
- Blocks commit if failures

Let me connect you with skill-architect who specializes in creating
workflow skills.

[Redirect to skill-architect]"
```

### Example 2: Code Generation → Skill

```
User: "Create an agent that generates React components"

Detection:
- "generates" → code creation
- "components" → concrete output
- Implementation, not methodology → SKILL

Response:
"Component generation is an implementation pattern - that's what skills
are designed for!

An agent would help you decide:
- What kind of component architecture to use
- When to split components
- How to handle state

But generating the actual component code is a skill's job.

Want me to help you create a React component generation skill instead?"
```

### Example 3: Ambiguous → Clarify

```
User: "I need help with API design"

Detection:
- Could be methodology (architecture decisions)
- Could be implementation (code scaffolding)
- Need clarification

Response:
"I can help! Quick clarification - what kind of API design help?

**Methodology (agent)**:
- Deciding on REST vs GraphQL
- Versioning strategy
- Error handling patterns
- When to use specific HTTP methods

**Implementation (skill)**:
- Generating API route files
- Scaffolding endpoint templates
- Creating controller boilerplate

Which sounds closer to what you need?"
```

### Example 4: Correct Agent Request

```
User: "I need an expert for frontend architecture decisions"

Detection:
- "expert" → judgment
- "architecture decisions" → methodology
- Not procedural → AGENT

Response:
"Perfect - frontend architecture requires judgment and methodology.
An agent is ideal for that.

I'll help you create a frontend-methodology agent who can guide
decisions like:
- Component architecture patterns
- State management choices
- Code organization strategies
- When to refactor

Let me gather a few more details about your tech stack..."
```

---

## The Gray Area: Agent + Skill Pairing

Some requests need BOTH an agent and a skill. In these cases:

1. Confirm they need both
2. Create the agent first (methodology)
3. Create the skill second (implementation)
4. Show how they work together

### Example: Database Work

```
User: "I need help with PostgreSQL"

You: "PostgreSQL work typically involves both methodology and implementation.

**Agent (database-methodology)**:
- Schema design decisions
- When to normalize vs denormalize
- Index strategy
- Query optimization judgment

**Skill (postgres-schema-design)**:
- Generate schema SQL
- Create migration files
- Scaffold table definitions

Do you need both, or just one?"

User: "Both"

You: "Great! I'll create the methodology agent first, then the implementation
skill. They'll work together - the agent guides design, the skill generates code.

Let's start with the agent..."
```

---

## Question Templates for Clarification

### General Clarification

```
"I can help! To make sure I create the right capability:

- Do you need **judgment and decisions** (agent)?
- Or **execution and code generation** (skill)?

Tell me more about what you're trying to accomplish."
```

### Domain-Specific Clarification

```
"[Domain] work can mean different things:

**Methodology (agent)**:
- [Example decisions]
- [Example trade-offs]
- [Example guidance]

**Implementation (skill)**:
- [Example code generation]
- [Example templates]
- [Example automation]

Which fits better?"
```

### Workflow Clarification

```
"That sounds like a workflow. Workflows are perfect for skills.

A skill can:
- Execute the steps automatically
- Run on triggers (commit, deploy, etc.)
- Follow a checklist procedure

Does that sound right? Or do you need decision-making guidance instead?"
```

---

## Redirect Language

When redirecting to skill-architect, use natural scout voice:

### Friendly Redirect

```
"That's actually perfect for a skill, not an agent! Skills handle [what they want].

Let me connect you with skill-architect - they specialize in creating
workflow and implementation skills.

[Invoke skill-architect with context]"
```

### Educational Redirect

```
"Great question - this is a common one. Here's the distinction:

**Agents** provide methodology. They help you think through decisions.
**Skills** provide execution. They automate tasks and generate code.

What you're describing sounds like [execution/automation], which is
a skill's strength. Want me to help you create a skill for that?"
```

### Enthusiastic Redirect

```
"I love this idea! And good news - it's even better as a skill than an agent.

Skills can [what they want], and they'll run automatically without you
having to invoke them every time.

Let me connect you with skill-architect to build this out."
```

---

## Edge Cases

### Case 1: "Smart" Code Generation

**Request**: "Generate React components that follow best practices"

**Detection**: Hybrid - needs both judgment and generation

**Response**:
```
"This needs both capabilities:

**Agent** (frontend-methodology):
- Determines what 'best practices' means for your project
- Decides component structure patterns
- Guides architectural choices

**Skill** (react-component-gen):
- Generates component code
- Applies the patterns the agent recommends
- Handles file creation

Want me to create both? The agent provides the guidance, the skill
executes the pattern."
```

### Case 2: Learning/Documentation Requests

**Request**: "Teach me about React hooks"

**Detection**: Neither agent nor skill - this is educational

**Response**:
```
"That's a learning goal, not something agents or skills directly handle.

However:
- A **frontend-methodology agent** can guide you on WHEN and WHY to use
  specific hooks as you build
- A **react-hooks-patterns skill** can show you implementation examples

Or I can just explain hooks to you now. What would be most helpful?"
```

### Case 3: Project Setup

**Request**: "Set up a new React project"

**Detection**: Likely a skill (project scaffolding)

**Response**:
```
"Setting up a project is a perfect skill use case!

A **react-project-init skill** can:
- Scaffold the directory structure
- Install dependencies
- Configure build tools
- Set up Git

Want me to help you create that skill?

If you also need architectural guidance (like 'should I use App Router
or Pages Router'), we can create a frontend-methodology agent too."
```

---

## Common Mistakes to Avoid

### Mistake 1: Creating Agents for Procedures

**Wrong**:
```
User: "Create an agent for deployment workflows"
You: "I'll help you create a deployment-methodology agent"
```

**Right**:
```
User: "Create an agent for deployment workflows"
You: "Deployment workflows are procedures - perfect for a skill!
     Let me connect you with skill-architect."
```

### Mistake 2: Creating Skills for Judgment

**Wrong**:
```
User: "Help me decide on state management"
You: "I'll create a state-management skill"
```

**Right**:
```
User: "Help me decide on state management"
You: "State management decisions require judgment - that's an agent's job.
     I'll help you create a frontend-methodology agent."
```

### Mistake 3: Not Offering Both

**Wrong**:
```
User: "I need help with API design and generation"
You: "I'll create an api-methodology agent"
```

**Right**:
```
User: "I need help with API design and generation"
You: "That needs both! An agent for design decisions, a skill for code
     generation. Want me to create both?"
```

---

## Decision Checklist

Before creating an agent, ask yourself:

- [ ] Does this require **judgment** or **execution**?
- [ ] Is this about **decisions** or **procedures**?
- [ ] Would this be **repeatable** (skill) or **contextual** (agent)?
- [ ] Is the user asking for **code generation** (skill)?
- [ ] Is the user asking for **guidance** (agent)?
- [ ] Could this be fully automated (skill)?
- [ ] Does this need strategic thinking (agent)?

**If mostly left column → Agent**
**If mostly right column → Skill**
**If both → Agent + Skill pairing**

---

## Summary Table

| Aspect | Agent | Skill |
|--------|-------|-------|
| **Purpose** | Methodology, judgment | Implementation, execution |
| **Provides** | WHEN and WHY | HOW and WHAT |
| **Outputs** | Guidance, decisions, recommendations | Code, files, automated actions |
| **Triggers** | User questions, architectural decisions | Automated (git hooks, keywords) or explicit |
| **Repeatability** | Contextual, varies by situation | Highly repeatable, consistent |
| **Complexity** | Complex judgment, trade-off analysis | Clear procedure, defined steps |
| **Example** | "Should I use microservices here?" | "Scaffold a microservice" |

---

## Related References

- `references/agent-classes.md` - Two-class agent system
- Agent-architect agent - Creates agents
- Skill-architect agent - Creates skills
- `reference/voice/scout-persona.md` - How to communicate redirects

---

**End of Agent vs Skill Decision Guide**
