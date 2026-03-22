# Epic Index — Feature-to-Epic Mapping

**File:** `/03-product/epic-index.md`
**Produced by:** @product-architect
**Date:** 2026-03-23
**Status:** Placeholder — to be generated when user stories are written

---

## Planned Structure

- Each Epic represents a major feature area
- Each Epic contains User Stories (small, implementable pieces)
- Sprints are time-boxed periods (1-2 weeks) that pull stories from across multiple Epics
- Sprints are NOT nested inside Epics — they cut across them based on priority and dependencies

## Example

Sprint 1 might include stories from both the Onboarding Epic AND Vehicle Management Epic,
because both are needed for a functional app. Features are built as vertical slices, not
one complete epic at a time.

## Epics (preliminary — to be finalized with user stories)

| Epic | Feature Area | Related Specs |
|---|---|---|
| Epic 1 | Onboarding & Auth | `onboarding-auth.md` |
| Epic 2 | Vehicle Management | `vehicle-management.md` |
| Epic 3 | Expense Tracking & Fuel | `expense-tracking.md`, `fuel-entry.md` |
| Epic 4 | Cost Dashboard | `cost-dashboard.md` |
| Epic 5 | Vehicle Timeline & Share | `vehicle-timeline.md`, `share-export.md` |
| Epic 6 | Maintenance Reminders | `maintenance-reminders.md` |
| Epic 7 | Challenges & Gamification (v2) | `challenges-gamification.md` |

*This file will be expanded with full user story breakdowns per epic when `/write-stories` is executed.*
