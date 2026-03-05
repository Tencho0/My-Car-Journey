---
command: /write-spec
description: "Write a detailed functional specification for a feature area"
agent: @product-architect
skills: [functional-spec]
output: /03-product/functional-specs/[feature-name].md
---

# /write-spec — Functional Specification

Define exactly what a feature does — every screen, interaction, business rule, and edge case — so a developer can build it without questions.

## Invocation

```
/write-spec expense-tracking
/write-spec fuel-entry
/write-spec cost-dashboard
/write-spec vehicle-timeline
/write-spec all                     (generates all specs sequentially)
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/03-product/product-requirements-document.md` — feature scope
2. `/03-product/mvp-feature-list.md` — what's in MVP
3. `/02-strategy/value-proposition.md` — which pains this feature addresses
4. `/01-discovery/customers/personas.md` — who uses this feature (if exists)
5. `/03-product/technical/database-schema.md` — data model (if exists)

### Step 2: Identify the Feature Area

Map the user's request to a spec:

| Feature Area | Spec File | Related Epic |
|---|---|---|
| Onboarding & Auth | `onboarding-auth.md` | Epic 1 |
| Vehicle Management | `vehicle-management.md` | Epic 2 |
| Expense Tracking | `expense-tracking.md` | Epic 3 |
| Fuel Entry | `fuel-entry.md` | Epic 3 |
| Cost Dashboard | `cost-dashboard.md` | Epic 4 |
| Vehicle Timeline | `vehicle-timeline.md` | Epic 5 |
| Maintenance Reminders | `maintenance-reminders.md` | Epic 6 |
| Challenges & Gamification | `challenges-gamification.md` | Epic 7 |
| Share & Export | `share-export.md` | Epic 5 |

### Step 3: Ask Clarifying Questions (if needed)

Only ask if the PRD doesn't provide enough detail:
1. "Any specific screen layouts you have in mind?"
2. "Are there business rules I should know that aren't documented?"
3. "Any specific .NET MAUI components you want to use for this?"

Most of the time, the PRD + concept document will have enough. Don't over-ask.

### Step 4: Generate the Spec

Load skill: `/00-project/skills/functional-spec.md`

Apply the 10-section template:
1. Overview (what and why)
2. User Flow (happy path + alternative paths)
3. Screen Descriptions (layout, interactions, states)
4. Business Rules (numbered, with examples)
5. Data Requirements (entities, validation rules)
6. Edge Cases & Error Handling
7. Non-Functional Requirements
8. Dependencies
9. Out of Scope
10. Open Questions

**Key rules while writing:**
- Think from the user's perspective, not the code's perspective
- Every screen MUST have: normal state, empty state, loading state, error state
- Business rules must be unambiguous — include concrete examples
- Validation rules must specify: field, type, required/optional, constraints, error message
- Bulgarian language context matters — mention currency (лв), date formats, language
- Reference database entities by name (anticipate the schema even if not written yet)
- Flag anything that needs design decision from the founder

### Step 5: Cross-Reference

After writing the spec:
1. Verify all PRD features for this area are covered
2. Identify any data entities that need to be added to the database schema
3. Identify API endpoints that will be needed
4. Flag dependencies on other feature specs

### Step 6: Save

Save to `/03-product/functional-specs/[feature-name].md`
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/write-spec [next-feature]` — continue with next feature area
- `/write-stories [epic-name]` — break this spec into user stories
- `/design-db` — design or update the database schema
- `/design-api` — design or update the API specification

## Batch Mode

If invoked with `/write-spec all`:
1. Generate specs in this order (dependencies flow naturally):
   - onboarding-auth → vehicle-management → expense-tracking → fuel-entry → cost-dashboard → vehicle-timeline → maintenance-reminders → challenges-gamification → share-export
2. After each spec, briefly note any new data entities or API endpoints discovered
3. At the end, produce a summary of all data entities and API endpoints needed

## Notes

- Fuel entry is a separate spec from expense tracking because it has specialized fields (liters, price/liter, odometer, consumption calculation)
- Share & export covers the shareable vehicle card and PDF export — cross-cutting concern
- Specs are living documents — update them when interviews or testing reveal new requirements
