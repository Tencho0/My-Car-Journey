# CLAUDE.md — Car Ownership Platform

## Project Overview

We are building a startup product: a car ownership cost tracking platform. The MVP is a mobile app for Bulgarian car enthusiasts (aged 20-40) that tracks expenses, calculates true ownership costs, maintains a vehicle timeline, and includes basic gamification.

Tech stack: .NET MAUI (C#), ASP.NET Core Web API, PostgreSQL, Entity Framework Core.

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

**Before activating any agent:** Read its definition file from `/00-project/agents/` to load its full mission, rules, input requirements, output formats, and handoff instructions.

### Agent Rules
- Agents do NOT duplicate responsibilities
- Every deliverable states which agent produced it and the date
- If an agent needs input from another agent's work, check if that deliverable exists first
- Use evidence-based reasoning; state assumptions explicitly
- Maintain traceability between documents (reference related docs by path)
- All outputs are structured markdown unless otherwise specified

## Key Decisions Made

- **Target market:** Bulgaria (launch), then Romania, then Western Europe
- **First user:** Car enthusiasts aged 20-40 who actively maintain/modify their cars
- **Launch approach:** B2C only, B2B modules come after 5,000-10,000 active users
- **Tech stack:** .NET MAUI + ASP.NET Core + PostgreSQL
- **Monetization:** Freemium (free app + premium at ~€1.49-2.99/month)
- **MVP scope:** Expense tracking, cost dashboard, vehicle timeline, maintenance reminders, basic challenges
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
- `/02-strategy/lean-canvas.md` ✅
- `/02-strategy/value-proposition.md` ✅
- `/03-product/mvp-feature-list.md` ✅

## Current Phase

Phase 1: Discovery & Validation — IN PROGRESS
- Customer interviews: NOT YET CONDUCTED (guide is ready)
- Competitor research: DONE
- Market sizing: DONE

Phase 2: Strategy & Planning — PARTIALLY STARTED
- Lean Canvas: DONE
- Value Proposition: DONE
- Positioning, monetization, GTM: NOT STARTED

Next priority tasks:
1. Product Requirements Document (PRD)
2. Technical architecture & database schema
3. Brand naming & identity
4. Functional specs for each feature area
5. User stories per epic
