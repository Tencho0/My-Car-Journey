# CLAUDE.md — Car Ownership Platform

## Project Overview

We are building a startup product: a car ownership cost tracking platform. The MVP is a mobile app for Bulgarian car enthusiasts (aged 20-40) that tracks expenses, calculates true ownership costs, maintains a vehicle timeline, and includes basic gamification.

Tech stack: Flutter (Dart) + Riverpod (mobile), Blazor WebAssembly + MudBlazor (admin web), ASP.NET Core Web API modular monolith (backend), PostgreSQL + EF Core (database), ASP.NET Identity + JWT (auth).

Solo developer project. MVP target: 10-14 weeks part-time.

## Agent System

This project uses a specialized AI agent system. Each agent has a defined role, inputs, outputs, and folder destinations. When asked to produce a deliverable, adopt the appropriate agent role and follow its rules.

### Agents

Each agent has a full definition file in `/00-project/agents/`. Read the relevant agent file before producing any deliverable to ensure you follow its specific rules, formats, and handoff procedures.

| Agent | Definition File | Primary Output Folder |
|---|---|---|
| `@orchestrator` | `/00-project/agents/orchestrator.md` | `/00-project/` |
| `@researcher` | `/00-project/agents/researcher.md` | `/01-discovery/research/` |
| `@customer-analyst` | `/00-project/agents/customer-analyst.md` | `/01-discovery/customers/` |
| `@strategist` | `/00-project/agents/strategist.md` | `/02-strategy/` |
| `@product-architect` | `/00-project/agents/product-architect.md` | `/03-product/` |
| `@brand-architect` | `/00-project/agents/brand-architect.md` | `/04-brand/` |
| `@developer` | `/00-project/agents/developer.md` | `/05-development/` |
| `@growth-lead` | `/00-project/agents/growth-lead.md` | `/06-growth/` |
| `@ops-legal` | `/00-project/agents/ops-legal.md` | `/07-legal-ops/` |
| `@qa` | `/00-project/agents/qa.md` | `/03-product/test-scenarios/` + `/05-development/src/Tests/` |

**Before activating any agent:** Read its definition file from `/00-project/agents/` to load its full mission, rules, input requirements, output formats, and handoff instructions.

### Agent Rules
- Agents do NOT duplicate responsibilities
- Every deliverable states which agent produced it and the date
- If an agent needs input from another agent's work, check if that deliverable exists first
- Use evidence-based reasoning; state assumptions explicitly
- Maintain traceability between documents (reference related docs by path)
- All outputs are structured markdown unless otherwise specified

## Skills & Commands

**Skills** define HOW agents do specific tasks — templates, frameworks, step-by-step methods. Stored in `/00-project/skills/`.

**Commands** are user-triggered workflows that chain agent + skills into end-to-end processes. Stored in `/00-project/commands/`.

When a user invokes a command (e.g., `/write-prd`):
1. Read the command file from `/00-project/commands/`
2. Identify the agent and skills referenced
3. Read the agent definition from `/00-project/agents/`
4. Read the skill file(s) from `/00-project/skills/`
5. Follow the workflow steps in the command
6. Produce output to the specified file path

When producing any deliverable, check if a matching skill exists in `/00-project/skills/` and follow its template and quality checklist.

### Available Skills

| Skill | File | Used By |
|---|---|---|
| create-prd | `/00-project/skills/create-prd.md` | @product-architect |
| user-stories | `/00-project/skills/user-stories.md` | @product-architect, @developer |
| functional-spec | `/00-project/skills/functional-spec.md` | @product-architect, @developer |
| positioning | `/00-project/skills/positioning.md` | @strategist, @brand-architect |
| product-naming | `/00-project/skills/product-naming.md` | @brand-architect |
| interview-script | `/00-project/skills/interview-script.md` | @customer-analyst |
| gtm-strategy | `/00-project/skills/gtm-strategy.md` | @strategist, @growth-lead |
| test-scenarios | `/00-project/skills/test-scenarios.md` | @qa, @developer |

### Available Commands

| Command | Agent | Skills | Output |
|---|---|---|---|
| `/write-prd` | @product-architect | create-prd | `/03-product/product-requirements-document.md` |
| `/write-stories` | @product-architect | user-stories | `/03-product/user-stories/*.md` |
| `/write-spec` | @product-architect | functional-spec | `/03-product/functional-specs/*.md` |
| `/define-positioning` | @strategist | positioning | `/02-strategy/positioning-strategy.md` |
| `/name-product` | @brand-architect | product-naming | `/04-brand/naming-exploration.md` |
| `/prep-interviews` | @customer-analyst | interview-script | `/01-discovery/customers/interview-guide.md` |
| `/plan-gtm` | @strategist | gtm-strategy | `/02-strategy/go-to-market-plan.md` |
| `/write-tests` | @qa | test-scenarios | `/03-product/test-scenarios/*.md` |

See `/00-project/skills/README.md` for the full architecture documentation.

## Key Decisions Made

- **Target market:** Bulgaria (launch), then Romania, then Western Europe
- **First user:** Car enthusiasts aged 20-40 who actively maintain/modify their cars
- **Launch approach:** B2C only, B2B modules come after 5,000-10,000 active users
- **Tech stack:** Flutter (Dart) + Blazor WASM + ASP.NET Core + PostgreSQL + ASP.NET Identity + JWT
- **Infrastructure:** Self-managed VPS (EU), FCM, Firebase Analytics, RevenueCat, Sentry, GitHub Actions
- **Monetization:** Freemium (free app + premium at €2.99/month or €24.99/year)
- **Auth:** Google Sign-In and Apple Sign-In in MVP. No Facebook OAuth.
- **MVP scope:** Expense tracking, cost dashboard, vehicle timeline, maintenance reminders
- **Deferred to v2:** Challenges/gamification, model benchmarks
- **Excluded from MVP:** Offline sync, OCR, bank integration, garage/dealer/fleet modules, social profiles, advanced predictions

## Folder Structure

```
/startup-project/
├── /00-project/           — Meta: status, decisions, agents, workflows
├── /01-discovery/         — Research, customer analysis, raw data
├── /02-strategy/          — Lean canvas, positioning, monetization, GTM
├── /03-product/           — PRD, features, user stories, functional specs, technical specs
├── /04-brand/             — Brand identity, naming, content, landing page
├── /05-development/       — Source code, docs, changelog
├── /06-growth/            — Launch, acquisition, experiments, metrics
└── /07-legal-ops/         — Legal, compliance, budget, pitch deck
```

See `/00-project/project-status.md` for current progress and next tasks.

## Document Standards

Every markdown deliverable should include:
- File path in the header (e.g., `/02-strategy/lean-canvas.md`)
- Agent that produced it (e.g., `Produced by: @strategist`)
- Date produced
- Version number
- References to input documents used
- Assumptions section if any assumptions were made
- "Next steps" or "Hands off to" section where relevant

## How to Work

1. When asked to create a deliverable, first check if its dependencies exist by reading the relevant input files
2. Adopt the correct agent role
3. Produce the output in the correct folder
4. Update `/00-project/project-status.md` to reflect the new deliverable
5. If a task spans multiple agents, break it into subtasks and handle sequentially

## Completed Deliverables

- `/00-project/concept-document.md` ✅
- `/00-project/agent-system-architecture.md` ✅
- `/01-discovery/research/competitor-analysis.md` ✅
- `/01-discovery/research/market-sizing.md` ✅
- `/01-discovery/customers/interview-guide.md` ✅
- `/01-discovery/customers/google-form-spec.md` ✅
- `/02-strategy/lean-canvas.md` ✅
- `/02-strategy/value-proposition.md` ✅
- `/02-strategy/positioning-strategy.md` ✅
- `/02-strategy/monetization-plan.md` ✅
- `/03-product/mvp-feature-list.md` ✅
- `/03-product/product-requirements-document.md` ✅
- `/03-product/functional-specs/*.md` (9 specs, all v2.0) ✅
- `/03-product/user-journeys/` (7 journeys + index) ✅
- `/00-project/decisions/DEC-007 through DEC-018` (12 tech decisions) ✅
- `/03-product/technical/architecture.md` ✅
- `/03-product/technical/database-schema.md` ✅
- `/03-product/technical/api-specification.md` ✅

## Current Phase

Phase 1: Discovery & Validation — IN PROGRESS (75%)
- Customer interviews: NOT YET CONDUCTED (guide is ready)
- Competitor research: DONE
- Market sizing: DONE

Phase 2: Strategy & Planning — IN PROGRESS (80%)
- Lean Canvas: DONE
- Value Proposition: DONE
- Positioning: DONE
- Monetization: DONE
- GTM: NOT STARTED

Phase 3: Product Definition — IN PROGRESS (95%)
- PRD: DONE
- Functional specs: DONE (9 specs v2.0)
- User journeys: DONE (7 journeys)
- Tech stack decisions: DONE (DEC-007 through DEC-018)
- Technical architecture: DONE
- Database schema: DONE (22 tables, 9 modules)
- API specification: DONE (86 endpoints, 12 controller groups)
- User stories: NOT STARTED

Next priority tasks:
1. User stories per epic
2. Sprint plan / development roadmap
3. Brand naming & identity
4. Go-to-market plan
5. Conduct customer interviews
