---

## How Agents, Skills, and Commands Work Together

```
USER REQUEST
     │
     ▼
ORCHESTRATOR ──→ selects AGENT (who does the work)
     │                │
     │                ▼
     │           AGENT loads SKILL(s) (how to do the work)
     │                │
     │                ▼
     │           Produces OUTPUT (deliverable in correct folder)
     │
     └──→ or triggers a COMMAND (predefined workflow chaining multiple skills)
```

**Agents** = WHO — role, mission, rules, responsibilities
**Skills** = HOW — templates, frameworks, step-by-step methods for specific tasks
**Commands** = WORKFLOWS — user-triggered sequences that chain agent + skills into end-to-end processes

---

## Skill Format

Every skill file follows this structure:

```markdown
---
name: skill-name
description: "One-line description of what this skill does"
agent: @agent-name (primary agent that uses this skill)
used-by: [@agent1, @agent2] (if multiple agents use it)
---

# Skill Name

## Purpose
What this skill produces and when to use it.

## Template / Framework
The actual structure, template, or framework to follow.

## Instructions
Step-by-step process for applying this skill.

## Output Format
What the final deliverable should look like.

## Quality Checklist
How to verify the output is good.
```

---

## Command Format

Every command file follows this structure:

```markdown
---
command: /command-name
description: "One-line description"
agent: @agent-name
skills: [skill-1, skill-2, skill-3]
output: /path/to/output-file.md
---

# /command-name — Description

## When to Use
Situations that trigger this command.

## Invocation Examples
Example ways to call this command.

## Workflow
Step 1: ...
Step 2: ...
Step 3: ...

## After Completion
Suggested next commands or actions.
```

---

## Skill-Agent Mapping

| Agent | Skills |
|---|---|
| @researcher | competitor-analysis, market-sizing, app-review-mining, pricing-benchmark, trend-analysis |
| @customer-analyst | interview-script, interview-analysis, persona-creation, jtbd-mapping, empathy-mapping, validation-scoring |
| @strategist | lean-canvas, value-proposition, positioning, monetization-strategy, pricing-strategy, gtm-strategy, kpi-framework, revenue-modeling |
| @product-architect | create-prd, user-stories, functional-spec, user-flows, prioritization, sprint-planning, db-schema, api-design |
| @brand-architect | product-naming, brand-identity, messaging-framework, landing-page-copy, app-store-listing, content-calendar |
| @developer | (uses specs directly — no separate skills needed) |
| @growth-lead | launch-planning, beta-testing, growth-experiments, community-strategy, channel-strategy |
| @ops-legal | company-registration, gdpr-checklist, terms-of-service, privacy-policy, budget-planning, pitch-deck |

---

## Command-to-Deliverable Mapping

| Command | Agent | Skills Used | Output File |
|---|---|---|---|
| `/research-competitors` | @researcher | competitor-analysis | `/01-discovery/research/competitor-analysis.md` |
| `/size-market` | @researcher | market-sizing | `/01-discovery/research/market-sizing.md` |
| `/mine-reviews` | @researcher | app-review-mining | `/01-discovery/research/app-review-analysis.md` |
| `/prep-interviews` | @customer-analyst | interview-script | `/01-discovery/customers/interview-guide.md` |
| `/analyze-interviews` | @customer-analyst | interview-analysis, validation-scoring | `/01-discovery/customers/interview-notes-summary.md` |
| `/create-personas` | @customer-analyst | persona-creation, jtbd-mapping | `/01-discovery/customers/personas.md` |
| `/build-canvas` | @strategist | lean-canvas | `/02-strategy/lean-canvas.md` |
| `/define-positioning` | @strategist | positioning, value-proposition | `/02-strategy/positioning-strategy.md` |
| `/plan-monetization` | @strategist | monetization-strategy, pricing-strategy | `/02-strategy/monetization-plan.md` |
| `/plan-gtm` | @strategist | gtm-strategy, channel-strategy | `/02-strategy/go-to-market-plan.md` |
| `/write-prd` | @product-architect | create-prd, prioritization | `/03-product/product-requirements-document.md` |
| `/write-stories` | @product-architect | user-stories | `/03-product/user-stories/*.md` |
| `/write-spec` | @product-architect | functional-spec | `/03-product/functional-specs/*.md` |
| `/design-db` | @product-architect | db-schema | `/03-product/technical/database-schema.md` |
| `/design-api` | @product-architect | api-design | `/03-product/technical/api-specification.md` |
| `/name-product` | @brand-architect | product-naming | `/04-brand/naming-exploration.md` |
| `/build-brand` | @brand-architect | brand-identity, messaging-framework | `/04-brand/brand-identity.md` |
| `/plan-launch` | @growth-lead | launch-planning, community-strategy | `/06-growth/launch-plan.md` |
| `/plan-growth` | @growth-lead | growth-experiments, channel-strategy | `/06-growth/growth-experiments.md` |
| `/setup-legal` | @ops-legal | company-registration, gdpr-checklist | `/07-legal-ops/company-registration-guide.md` |
| `/write-privacy` | @ops-legal | privacy-policy, gdpr-checklist | `/07-legal-ops/privacy-policy.md` |
| `/build-pitch` | @ops-legal | pitch-deck, revenue-modeling | `/07-legal-ops/pitch-deck.md` |

---

## Discovery Workflow (Chained Commands)

```
/research-competitors
       │
       ▼
/size-market
       │
       ▼
/mine-reviews
       │
       ▼
/prep-interviews ──→ [YOU: conduct interviews] ──→ /analyze-interviews
       │                                                    │
       ▼                                                    ▼
/create-personas ◄──────────────────────────────────────────┘
```

## Strategy Workflow

```
/build-canvas ──→ /define-positioning ──→ /plan-monetization ──→ /plan-gtm
```

## Product Workflow

```
/write-prd ──→ /write-spec (per feature) ──→ /write-stories (per epic) ──→ /design-db ──→ /design-api
```

## Brand & Launch Workflow

```
/name-product ──→ /build-brand ──→ /plan-launch ──→ /plan-growth
```
