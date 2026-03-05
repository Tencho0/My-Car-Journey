---
name: user-stories
description: "Write user stories with acceptance criteria grouped by epic, following INVEST criteria and the 3 C's pattern."
agent: @product-architect
used-by: [@product-architect, @developer]
---

# Write User Stories

## Purpose

Break features from the PRD and functional specs into implementable user stories that a developer can pick up and build without ambiguity. Each story represents a single unit of user value.

## When to Use

- After the PRD is written and approved
- After functional specs define the detailed behavior
- When planning sprints
- When a feature needs to be broken into buildable chunks

## Framework

### The 3 C's
- **Card:** The story itself (short, fits on a card)
- **Conversation:** Context and discussion captured in notes and acceptance criteria
- **Confirmation:** Testable acceptance criteria that prove the story is done

### INVEST Criteria (every story must be)
- **I**ndependent — can be built without depending on other stories
- **N**egotiable — details can be discussed, it's not a rigid contract
- **V**aluable — delivers value to the user (not just technical work)
- **E**stimable — developer can estimate the effort
- **S**mall — completable in one sprint (for solo dev: 1-5 days)
- **T**estable — clear criteria to verify it works

## Template

```markdown
# User Stories: [Epic Name]

**File:** `/03-product/user-stories/epic-N-[name].md`
**Produced by:** @product-architect
**Date:** [date]
**Source PRD:** `/03-product/product-requirements-document.md`
**Source Spec:** `/03-product/functional-specs/[name].md`

---

## Epic Overview
[1-2 sentences: what this epic achieves and why it matters]

**User value:** [What the user can do after this epic is complete]
**Estimated total effort:** [S/M/L]

---

## Stories

### US-[epic].[number]: [Short Title]

> As a [specific user type],
> I want to [action/capability],
> so that [benefit/outcome].

**Priority:** P0 | P1 | P2
**Effort:** S (1-2 days) | M (3-4 days) | L (5+ days)
**Dependencies:** [other stories or none]

**Acceptance Criteria:**
- [ ] [Given/When/Then or declarative statement]
- [ ] [...]
- [ ] [...]

**Edge Cases:**
- [What happens when X goes wrong]
- [Empty state behavior]

**Technical Notes:**
- [Implementation hints relevant to .NET MAUI / ASP.NET Core]
- [Database entities involved]
- [API endpoints needed]

**Out of Scope:**
- [What this story explicitly does NOT include]

---
```

## Instructions

1. **Read the PRD and relevant functional spec first.** Stories must trace back to documented requirements.
2. **One story = one unit of user value.** If a story has "and" in it, split it.
3. **Write acceptance criteria as testable statements.** A developer should be able to read the criteria and know exactly when the story is done.
4. **Include edge cases.** What happens with empty data? No internet? Invalid input? First-time use?
5. **Add technical notes** specific to .NET MAUI and ASP.NET Core where helpful — but keep them as hints, not prescriptions.
6. **Flag dependencies** between stories. If US-3.3 requires US-3.1 to be complete, say so.
7. **Keep stories small.** For a solo developer, each story should be completable in 1-5 days. If it's bigger, split it.
8. **Group by epic.** One file per epic: `epic-1-onboarding.md`, `epic-2-vehicle-management.md`, etc.

## Effort Sizing Guide

| Size | Solo Dev Time | Complexity |
|---|---|---|
| S | 1-2 days | Single screen, simple logic, one API endpoint |
| M | 3-4 days | Multiple screens or complex logic, 2-3 API endpoints |
| L | 5+ days | Complex feature, multiple screens + logic + API, consider splitting |

## Quality Checklist

- [ ] Every story traces to a PRD feature or functional spec requirement
- [ ] Every story has at least 3 acceptance criteria
- [ ] Stories follow INVEST criteria
- [ ] Edge cases are documented (empty states, errors, boundaries)
- [ ] No story takes more than 5 days for a solo developer
- [ ] Dependencies between stories are flagged
- [ ] Technical notes reference correct .NET MAUI / ASP.NET Core patterns
- [ ] Stories are prioritized (P0/P1/P2)
- [ ] Out of scope is explicit for stories where confusion is likely

## After This Skill

Suggest to the user:
- "Want me to plan these into sprints? → use sprint-planning skill"
- "Should I write test scenarios for these stories?"
- "Ready to start building? Begin with the P0 stories in Sprint 1."
