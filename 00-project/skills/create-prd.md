---
name: create-prd
description: "Create a Product Requirements Document using an 8-section template covering problem, objectives, segments, value propositions, solution, assumptions, and release planning."
agent: @product-architect
used-by: [@product-architect]
---

# Create a Product Requirements Document

## Purpose

Produce the authoritative specification for a product or feature. The PRD is the single source of truth that aligns all downstream work — functional specs, user stories, technical design, and development.

## When to Use

- Starting a new product or major feature
- Documenting an MVP scope
- Aligning stakeholders on what's being built and why
- Before writing functional specs or user stories

## Template

```markdown
# Product Requirements Document: [Product/Feature Name]

**Author:** [name]
**Date:** [date]
**Status:** Draft | In Review | Approved
**Produced by:** @product-architect
**Output:** `/03-product/product-requirements-document.md`

## References
- Concept document: `/00-project/concept-document.md`
- Lean Canvas: `/02-strategy/lean-canvas.md`
- Value Proposition: `/02-strategy/value-proposition.md`
- Competitor Analysis: `/01-discovery/research/competitor-analysis.md`
- Personas: `/01-discovery/customers/personas.md`

---

### 1. Executive Summary
[2-3 sentences: what we're building, for whom, and the core value delivered]

### 2. Background & Context
- **Context:** What is this initiative about?
- **Why now?** What has changed that makes this the right time?
- **Prior art:** What exists today? What have we or others tried before?
- **Evidence:** What research, interviews, or data supports this?

### 3. Objectives & Success Metrics

**Goals** (what success looks like):
1. [Specific, measurable goal]
2. [...]

**Non-Goals** (explicitly out of scope, and why):
1. [What we're NOT doing]
2. [...]

**Success Metrics:**
| Metric | Current Baseline | Target | How Measured | Timeframe |
|--------|-----------------|--------|--------------|-----------|

### 4. Target Users & Segments
- **Primary segment:** [Who, defined by their problem/job, not demographics]
- **Early adopter profile:** [Specific description of your first user]
- **Segment size:** [How many, reference market-sizing.md]
- **Current workaround:** [How they solve the problem today]
- **Constraints:** [What limits this segment — budget, geography, platform, etc.]

### 5. Value Propositions
- **Customer jobs** we're addressing: [from value-proposition.md]
- **Pains** we're relieving: [from value-proposition.md]
- **Gains** we're creating: [from value-proposition.md]
- **Competitive advantage:** What we do better than alternatives
- **Value curve:** [Where we over-invest vs under-invest compared to competitors]

### 6. Solution

#### 6.1 Key Features (Priority-Tiered)

**P0 — Must Have (MVP cannot launch without):**
| # | Feature | User Story | Acceptance Criteria |
|---|---------|-----------|-------------------|

**P1 — Should Have (high impact, include if time allows):**
| # | Feature | User Story | Acceptance Criteria |
|---|---------|-----------|-------------------|

**P2 — Won't Have Now (future, explicitly deferred):**
| # | Feature | Reason for Deferral |
|---|---------|-------------------|

#### 6.2 User Flows
[Key user journeys — reference user-flows.md when created]

#### 6.3 UX Direction
[High-level UX approach, reference wireframes.md when created]

#### 6.4 Technical Approach
[Tech stack, architecture summary — reference /03-product/technical/architecture.md]
[Solo developer constraints that shape the solution]

#### 6.5 Assumptions
| # | Assumption | Risk Level | How to Validate |
|---|-----------|-----------|----------------|

### 7. Open Questions
| # | Question | Owner | Deadline | Resolution |
|---|----------|-------|----------|------------|

### 8. Timeline & Phasing

**Phase 1 (MVP):**
- Scope: [what's included]
- Estimated effort: [relative, not dates]
- Key milestone: [what "done" looks like]

**Phase 2 (Post-validation):**
- Trigger: [what must be true before starting Phase 2]
- Scope: [what's added]

**Phase 3+ (Future):**
- [High-level roadmap]
```

## Instructions

1. **Check dependencies:** Read lean-canvas.md, value-proposition.md, competitor-analysis.md, and personas.md (if they exist). Extract relevant insights.
2. **Gather missing context:** If key inputs don't exist, ask the user or flag gaps.
3. **Fill the template section by section.** Be specific — vague PRDs create vague products.
4. **Be opinionated about scope.** Challenge every P1 — should it really be P0? A tight PRD beats a comprehensive vague one.
5. **Non-goals are as important as goals.** Every feature you explicitly exclude prevents future scope creep.
6. **Success metrics must have baselines.** "Improve retention" is useless. "Increase 30-day retention from unknown to 25%+" is actionable.
7. **Flag assumptions honestly.** Unvalidated assumptions are the #1 startup killer.
8. **Save output** to `/03-product/product-requirements-document.md`

## Quality Checklist

- [ ] Every feature maps to a validated user pain or gain
- [ ] Non-goals section exists and is specific
- [ ] Success metrics have current baseline AND target
- [ ] Assumptions are flagged with validation methods
- [ ] Open questions have owners and deadlines
- [ ] Phase 1 scope is achievable by a solo developer in the stated timeframe
- [ ] Technical constraints are acknowledged in the solution section
- [ ] All reference documents are linked by file path
- [ ] A new team member could read this and understand what we're building and why

## After This Skill

Suggest to the user:
- "Want me to challenge your P1 priorities? I can argue for cutting scope."
- "Ready to write functional specs? → `/write-spec`"
- "Want to break features into user stories? → `/write-stories`"
- "Should we run a pre-mortem on this PRD?"
