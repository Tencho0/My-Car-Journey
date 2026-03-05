---
command: /write-prd
description: "Create a comprehensive Product Requirements Document from a feature idea, problem statement, or existing concept document"
agent: @product-architect
skills: [create-prd]
output: /03-product/product-requirements-document.md
---

# /write-prd — Product Requirements Document

Create a structured PRD that serves as the single source of truth for what's being built, why, and how success is measured.

## Invocation

```
/write-prd
/write-prd Car expense tracking MVP for Bulgarian car enthusiasts
/write-prd [paste a feature brief or problem statement]
```

## Workflow

### Step 1: Load Context

Read these files (skip any that don't exist yet):
1. `/00-project/concept-document.md` — overall product vision
2. `/02-strategy/lean-canvas.md` — business model
3. `/02-strategy/value-proposition.md` — customer pains and gains
4. `/01-discovery/research/competitor-analysis.md` — competitive landscape
5. `/01-discovery/customers/personas.md` — target user profiles
6. `/03-product/mvp-feature-list.md` — feature scope

Extract key insights from each to inform the PRD.

### Step 2: Gather Missing Context

If key context is missing, ask the user — most important first:

1. **Scope:** Are we writing the PRD for the full MVP or a specific feature?
2. **User problem:** Is the core problem clearly defined, or do we need to sharpen it?
3. **Success criteria:** Does the user have specific targets in mind?
4. **Constraints:** Timeline, budget, technical limitations beyond what's documented?
5. **Stakeholders:** Who needs to approve or review this? (Solo founder = just you)

If the user has uploaded a document, extract answers from it and only ask about gaps.

### Step 3: Generate the PRD

Load skill: `/00-project/skills/create-prd.md`

Apply the 8-section template:
1. Executive Summary
2. Background & Context (include "Why now?")
3. Objectives & Success Metrics (Current → Target table)
4. Target Users & Segments
5. Value Propositions
6. Solution (P0/P1/P2 tiered features with acceptance criteria)
7. Open Questions (with owners and deadlines)
8. Timeline & Phasing

**Key rules while writing:**
- Be opinionated about scope — challenge anything that feels like P0 but might be P1
- Non-goals section must exist and be specific
- Every feature must trace to a validated pain or gain
- Success metrics must have baseline AND target numbers
- Assumptions must be flagged with validation methods
- Technical approach must acknowledge solo developer / .NET MAUI constraints

### Step 4: Review and Iterate

After generating, offer these options:

- "Want me to **tighten scope**? I can challenge which P1s should really be P2s."
- "Should I **run a pre-mortem**? I'll identify what could go wrong with this plan."
- "Ready to **write functional specs**? → `/write-spec [feature-name]`"
- "Want to **break features into user stories**? → `/write-stories [epic-name]`"
- "Should I **design the database** for these features? → `/design-db`"

### Step 5: Save

Save the PRD to `/03-product/product-requirements-document.md`
Update `/00-project/project-status.md` to mark PRD as complete.

## After Completion

Suggested next commands:
- `/write-spec expense-tracking` — detail the most important feature
- `/write-spec cost-dashboard` — detail the core value feature
- `/write-stories epic-1-onboarding` — start breaking into buildable stories
- `/define-positioning` — if positioning isn't done yet

## Notes

- The PRD is a living document — it will be updated as interviews and testing reveal new insights
- Version the document (1.0, 1.1, etc.) when making significant changes
- Keep it under 10 pages — if it's longer, you're probably including too much detail that belongs in functional specs
