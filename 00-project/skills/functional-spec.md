---
name: functional-spec
description: "Write a detailed functional specification for a feature area covering user flows, screen behavior, business rules, data requirements, and edge cases."
agent: @product-architect
used-by: [@product-architect, @developer]
---

# Write a Functional Specification

## Purpose

Define exactly WHAT a feature does from the user's perspective — every screen, every interaction, every rule, every edge case. This is the bridge between the PRD (strategic "what and why") and user stories (implementable chunks). A developer should be able to build the feature from this spec without asking questions.

## When to Use

- After the PRD defines the feature at a high level
- Before writing user stories (specs inform stories, not the other way around)
- When a feature is complex enough that user stories alone would miss important details
- One spec per feature area (not per screen or per user story)

## Template

```markdown
# Functional Specification: [Feature Name]

**File:** `/03-product/functional-specs/[feature-name].md`
**Produced by:** @product-architect
**Date:** [date]
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` — Section [X]
**Related user stories:** `/03-product/user-stories/epic-N-[name].md`

---

## 1. Overview

**What this feature does:** [1-2 sentences]
**Why it matters:** [Link to user pain/gain from value-proposition.md]
**User type:** [Which persona uses this]

## 2. User Flow

### Happy Path
1. User [action] →
2. System [response] →
3. User sees [result] →
4. ...

### Alternative Paths
- **Path A:** [If user does X instead of Y]
- **Path B:** [If user comes from a different entry point]

### Flow Diagram (optional)
[Text-based or mermaid diagram if the flow is complex]

## 3. Screen Descriptions

### Screen: [Screen Name]

**Purpose:** [What the user accomplishes here]
**Entry points:** [How the user gets to this screen]
**Layout:**
- [Key element 1 — what it shows, where it is]
- [Key element 2]
- [Key element 3]

**Interactions:**
| Element | Action | Result |
|---------|--------|--------|

**Loading state:** [What shows while data loads]
**Empty state:** [What shows when there's no data yet — first-time use]
**Error state:** [What shows when something fails]

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | [Rule description] | [Concrete example of the rule in action] |
| BR-2 | ... | ... |

## 5. Data Requirements

**Entities involved:** [Reference database-schema.md entities]
**Data read:** [What data this feature reads and from where]
**Data written:** [What data this feature creates or modifies]
**Validation rules:**
| Field | Type | Required | Validation |
|-------|------|----------|------------|

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No internet connection | [What happens] |
| Invalid input | [What happens] |
| Data doesn't exist yet (first use) | [What happens] |
| Maximum values exceeded | [What happens] |
| Concurrent modification | [What happens] |

## 7. Non-Functional Requirements

- **Performance:** [Expected load time, response time]
- **Offline behavior:** [What works offline, if anything]
- **Accessibility:** [Any specific requirements]
- **Localization:** [Language considerations — Bulgarian first, then English]

## 8. Dependencies

- **Depends on:** [Other features that must exist first]
- **Depended on by:** [Features that need this to exist]
- **API endpoints needed:** [Reference api-specification.md]

## 9. Out of Scope

- [What this spec explicitly does NOT cover]
- [Features that look related but are deferred]

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
```

## Instructions

1. **Read the PRD section for this feature first.** The spec expands on the PRD — it doesn't contradict it.
2. **Think from the user's perspective.** Describe what the user sees and does, not how the code works internally.
3. **Be exhaustive about edge cases.** The #1 cause of bugs is unspecified behavior. If it can happen, document what should happen.
4. **Empty states are critical for a new product.** Every screen will be empty on first use. Design those states to guide the user.
5. **Business rules must be unambiguous.** If a rule has exceptions, document them.
6. **One spec per feature area.** Feature areas for this project: onboarding-auth, vehicle-management, expense-tracking, fuel-entry, cost-dashboard, vehicle-timeline, maintenance-reminders, challenges-gamification, share-export.
7. **Reference other documents by file path.** Maintain traceability.

## Quality Checklist

- [ ] Happy path is fully documented step by step
- [ ] Every screen has empty state, loading state, and error state defined
- [ ] All business rules are listed with examples
- [ ] All validation rules are specified (field, type, required, constraints)
- [ ] Edge cases cover: no data, no internet, invalid input, boundary values
- [ ] Data requirements reference the database schema entities
- [ ] Dependencies on other features are flagged
- [ ] Out of scope is explicit
- [ ] A developer could build this feature using only this spec + user stories

## After This Skill

Suggest to the user:
- "Ready to break this into user stories? → use user-stories skill"
- "Want me to write the next feature spec? → `/write-spec [feature-name]`"
- "Should I design the database schema for this feature? → `/design-db`"
