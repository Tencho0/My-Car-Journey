---
command: /write-stories
description: "Break a feature or epic into implementable user stories with acceptance criteria, effort estimates, and technical notes"
agent: @product-architect
skills: [user-stories]
output: /03-product/user-stories/epic-N-[name].md
---

# /write-stories — User Stories by Epic

Break features into small, buildable user stories that a solo developer can pick up and implement without ambiguity.

## Invocation

```
/write-stories onboarding
/write-stories epic-3-expense-tracking
/write-stories vehicle-timeline
/write-stories all                     (generates all epics sequentially)
```

## Workflow

### Step 1: Load Context

Read these files:
1. `/03-product/product-requirements-document.md` — feature scope and priorities
2. `/03-product/functional-specs/[relevant-spec].md` — detailed feature behavior
3. `/03-product/mvp-feature-list.md` — what's in/out of MVP
4. `/03-product/technical/database-schema.md` — data entities (if exists)
5. `/03-product/technical/api-specification.md` — API endpoints (if exists)

### Step 2: Identify the Epic

Map the user's request to an epic:

| Epic | Feature Area | Functional Spec |
|---|---|---|
| Epic 1 | Onboarding & Authentication | `onboarding-auth.md` |
| Epic 2 | Vehicle Management | `vehicle-management.md` |
| Epic 3 | Expense Tracking | `expense-tracking.md` |
| Epic 4 | Cost Dashboard | `cost-dashboard.md` |
| Epic 5 | Vehicle Timeline | `vehicle-timeline.md` |
| Epic 6 | Maintenance Reminders | `maintenance-reminders.md` |
| Epic 7 | Challenges & Gamification | `challenges-gamification.md` |

If the functional spec doesn't exist yet, inform the user:
"The functional spec for [feature] doesn't exist yet. I can write stories from the PRD, but they'll be higher-level. Want me to write the spec first? → `/write-spec [feature]`"

### Step 3: Generate Stories

Load skill: `/00-project/skills/user-stories.md`

For each story, produce:
- User story statement (As a... I want... So that...)
- Priority (P0/P1/P2)
- Effort estimate (S/M/L with day ranges)
- Acceptance criteria (3+ testable statements)
- Edge cases
- Technical notes (specific to .NET MAUI / ASP.NET Core)
- Dependencies on other stories
- Out of scope

**Rules while writing:**
- One story = one unit of user value. If it has "and", split it.
- Every story must be completable in 1-5 days by a solo developer
- P0 stories should be buildable independently in Sprint order
- Include empty states — every screen is empty on first use
- Acceptance criteria must be testable without interpretation

### Step 4: Organize and Validate

After generating all stories for the epic:
1. Verify dependency chain — can P0 stories be built in order without circular deps?
2. Count total effort — does the epic fit within its planned sprint?
3. Check coverage — are all PRD features for this epic covered by at least one story?
4. List any gaps or conflicts found

### Step 5: Save

Save to `/03-product/user-stories/epic-N-[name].md`
Update `/00-project/project-status.md`

## After Completion

Suggested next commands:
- `/write-stories [next-epic]` — continue with the next epic
- `/write-spec [feature]` — if a functional spec is needed first
- "Want me to plan these into sprints?"
- "Ready to start building? Begin with the P0 stories."

## Batch Mode

If invoked with `/write-stories all`:
1. Generate stories for all 7 epics sequentially
2. After each epic, briefly summarize story count and total effort
3. At the end, produce a summary table:

| Epic | Stories | P0 | P1 | P2 | Est. Effort |
|------|---------|----|----|-----|-------------|

4. Flag any cross-epic dependencies
