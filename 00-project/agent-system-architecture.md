# Agent System Architecture

**File:** `/00-project/agent-system-architecture.md`
**Produced by:** @orchestrator
**Date:** 2026-03-23
**Version:** 1.0

---

## Overview

This project uses a 10-agent AI system to produce deliverables across discovery, strategy, product definition, brand, development, growth, and operations. Each agent has a defined role, inputs, outputs, and folder destination. Agents do not duplicate responsibilities.

The system has three layers:
- **Agents** — WHO does the work (role definitions)
- **Skills** — HOW agents do specific tasks (templates, frameworks, methods)
- **Commands** — WHAT triggers the work (user-invoked workflows chaining agent + skill)

---

## Agent Roster

| Agent | Role | Output Folder | Definition |
|---|---|---|---|
| `@orchestrator` | Project coordinator, task router, progress tracker | `/00-project/` | `/00-project/agents/orchestrator.md` |
| `@researcher` | Market intelligence and competitive analysis | `/01-discovery/research/` | `/00-project/agents/researcher.md` |
| `@customer-analyst` | Customer research and user understanding | `/01-discovery/customers/` | `/00-project/agents/customer-analyst.md` |
| `@strategist` | Business model, positioning, monetization, GTM | `/02-strategy/` | `/00-project/agents/strategist.md` |
| `@product-architect` | Product definition, specs, and technical design | `/03-product/` | `/00-project/agents/product-architect.md` |
| `@brand-architect` | Brand identity, naming, marketing content | `/04-brand/` | `/00-project/agents/brand-architect.md` |
| `@developer` | Software engineer building the product from specs | `/05-development/` | `/00-project/agents/developer.md` |
| `@growth-lead` | User acquisition, launch execution, growth strategy | `/06-growth/` | `/00-project/agents/growth-lead.md` |
| `@ops-legal` | Business operations, legal compliance, financial planning | `/07-legal-ops/` | `/00-project/agents/ops-legal.md` |
| `@qa` | Quality assurance, test design, verification | `/03-product/test-scenarios/` | `/00-project/agents/qa.md` |

See each agent's definition file for full mission, rules, input requirements, output formats, and handoff instructions.

---

## Skills Roster

Skills define templates, frameworks, and step-by-step methods that agents use to produce deliverables.

| Skill | Purpose | Used By | Definition |
|---|---|---|---|
| create-prd | Create Product Requirements Document | @product-architect | `/00-project/skills/create-prd.md` |
| user-stories | Break features into implementable user stories | @product-architect, @developer | `/00-project/skills/user-stories.md` |
| functional-spec | Define what a feature does from user's perspective | @product-architect, @developer | `/00-project/skills/functional-spec.md` |
| positioning | Define competitive positioning and differentiation | @strategist, @brand-architect | `/00-project/skills/positioning.md` |
| product-naming | Generate and evaluate product name candidates | @brand-architect | `/00-project/skills/product-naming.md` |
| interview-script | Design customer interview guide (Mom Test) | @customer-analyst | `/00-project/skills/interview-script.md` |
| gtm-strategy | Create go-to-market strategy | @strategist, @growth-lead | `/00-project/skills/gtm-strategy.md` |
| test-scenarios | Generate test scenarios and unit test specs | @qa, @developer | `/00-project/skills/test-scenarios.md` |

---

## Commands Roster

Commands are user-triggered workflows that chain an agent with one or more skills to produce a deliverable.

### Implemented Commands (8)

| Command | Agent | Skill | Output | Definition |
|---|---|---|---|---|
| `/write-prd` | @product-architect | create-prd | `/03-product/product-requirements-document.md` | `/00-project/commands/write-prd.md` |
| `/write-stories` | @product-architect | user-stories | `/03-product/user-stories/*.md` | `/00-project/commands/write-stories.md` |
| `/write-spec` | @product-architect | functional-spec | `/03-product/functional-specs/*.md` | `/00-project/commands/write-spec.md` |
| `/define-positioning` | @strategist | positioning | `/02-strategy/positioning-strategy.md` | `/00-project/commands/define-positioning.md` |
| `/name-product` | @brand-architect | product-naming | `/04-brand/naming-exploration.md` | `/00-project/commands/name-product.md` |
| `/prep-interviews` | @customer-analyst | interview-script | `/01-discovery/customers/interview-guide.md` | `/00-project/commands/prep-interviews.md` |
| `/plan-gtm` | @strategist | gtm-strategy | `/02-strategy/go-to-market-plan.md` | `/00-project/commands/plan-gtm.md` |
| `/write-tests` | @qa | test-scenarios | `/03-product/test-scenarios/*.md` | `/00-project/commands/write-tests.md` |

### Planned Commands (not yet implemented)

- `/design-architecture`, `/design-schema`, `/design-api` — @product-architect
- `/plan-sprints` — @product-architect
- `/write-code`, `/review-code` — @developer
- `/run-tests`, `/deploy` — @developer
- `/analyze-metrics` — @growth-lead
- `/create-pitch` — @ops-legal
- `/audit-gdpr` — @ops-legal
- `/plan-launch` — @growth-lead
- `/research-competitors` — @researcher

---

## How It Works

```
User invokes a command (e.g., /write-spec vehicle-management)
    ↓
System reads the command file (/00-project/commands/write-spec.md)
    ↓
Command identifies the agent (@product-architect) and skill (functional-spec)
    ↓
System reads the agent definition (/00-project/agents/product-architect.md)
System reads the skill template (/00-project/skills/functional-spec.md)
    ↓
Agent gathers required inputs (PRD, feature list, existing specs)
    ↓
Agent produces deliverable using the skill's template and quality checklist
    ↓
Output saved to the specified folder (/03-product/functional-specs/)
    ↓
Project status updated (/00-project/project-status.md)
```

---

## Workflow Chains

Deliverables flow between agents in a defined order:

1. **Discovery:** @researcher → @customer-analyst → feeds into strategy
2. **Strategy:** @strategist produces lean canvas, positioning, monetization, GTM
3. **Product:** @product-architect consumes strategy outputs → PRD → feature list → functional specs → user journeys → technical architecture → database schema → API spec → user stories
4. **Brand & Launch:** @brand-architect (naming, identity) + @growth-lead (launch plan) consume positioning and product definition
5. **Development:** @developer consumes user stories, specs, architecture → builds the product
6. **Quality:** @qa consumes functional specs → test scenarios → verifies developer output

---

## References

- Full skills architecture documentation: `/00-project/skills/README.md`
- Project status and progress: `/00-project/project-status.md`
- Agent definitions: `/00-project/agents/*.md`
- Skill definitions: `/00-project/skills/*.md`
- Command definitions: `/00-project/commands/*.md`
