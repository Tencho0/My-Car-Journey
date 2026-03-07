# Functional Specification: Challenges & Gamification

**File:** `/03-product/functional-specs/challenges-gamification.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (S4)
**Related features:** S4 (Basic Challenges)

---

## 1. Overview

**What this feature does:** Provides monthly challenges that let users compete (anonymously) on metrics like lowest cost per kilometer, best fuel efficiency, and most expenses logged. Includes leaderboards that update in real-time and reset monthly. Free users can view leaderboards; premium users can compete and appear on them.

**Why it matters:** Gamification creates return visits beyond pure utility. A user who might forget to log expenses on a Tuesday will check on Saturday to see their leaderboard position. This drives retention without being the core value -- it's a supplemental engagement layer (Gain: G9 have fun with car ownership).

**User type:** All users (view). Premium users (compete).

---

## 2. User Flow

### Happy Path -- View and Compete in a Challenge

1. User navigates to "Challenges" tab or section
2. System shows active monthly challenges (2-3 challenges)
3. User taps a challenge (e.g., "Lowest Cost/km -- March 2026")
4. System shows leaderboard: rank, anonymous username, metric value
5. (Premium user) User's position is highlighted: "You: #12 -- 0.38 лв/km"
6. (Free user) Leaderboard is visible but user's rank is locked: "Go Premium to compete"
7. User is motivated to log expenses and update odometer to improve rank
8. At month end, leaderboard freezes. Winners announced. New challenges start.

### Alternative Paths

- **Path A: Free user views leaderboard** -- Can see top 10 positions. Cannot see own rank. CTA: "Go Premium to see your rank and compete."
- **Path B: Premium user with insufficient data** -- User hasn't logged enough data (e.g., no odometer readings for cost/km challenge). Message: "Log your odometer and expenses to join this challenge."
- **Path C: Challenge just started (beginning of month)** -- Few participants. Leaderboard shows available positions with a motivational message: "Be among the first to compete this month!"
- **Path D: Month ends** -- Challenges freeze on the last day. Results shown for 3 days. Then new month's challenges start.

---

## 3. Screen Descriptions

### Screen: Challenges Hub

**Purpose:** Browse active and past challenges.
**Entry points:** "Challenges" tab in bottom nav or app menu.

**Layout:**
- "Challenges" heading
- **Active Challenges section:**
  - 2-3 challenge cards for the current month:
    - Challenge icon + title (e.g., "Fuel Efficiency Champion")
    - Description: "Achieve the lowest L/100km this month"
    - Your rank (premium) or "Compete" CTA (free)
    - Time remaining: "18 days left"
    - Participant count: "142 competing"
  - Each card is tappable
- **Past Challenges section:**
  - Previous month's results (collapsed by default)
  - Tap to expand: top 3 winners + your result (premium)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Challenge card | Tap | Opens challenge leaderboard |
| "Compete" CTA (free user) | Tap | Opens premium paywall |
| Past challenges | Tap | Expands to show results |

**Loading state:** Skeleton challenge cards.
**Empty state:** "Challenges coming soon! Start logging expenses to be ready." (Only shown if challenges feature hasn't launched yet.)
**Error state:** "Could not load challenges. Pull down to retry."

---

### Screen: Challenge Leaderboard

**Purpose:** View rankings for a specific challenge.
**Entry points:** Tap a challenge card.

**Layout:**
- Challenge header:
  - Title + icon
  - Description + rules
  - Time remaining
- **Top 3 podium:** visual podium display showing rank 1-3 with their metric values
- **Full leaderboard list:**
  - Each row: rank number | anonymous display name | metric value
  - Premium user's own row is highlighted (sticky or scrolls with list)
  - Free users see top 10 only, then a blurred area: "Go Premium to see full rankings and your position"
- **Your Stats section (premium):**
  - Your current metric value
  - Your rank: "#12 of 142"
  - Improvement needed: "Improve by 0.02 лв/km to reach #11"

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Leaderboard row | Tap | No action (anonymous, no profile to view) |
| "Go Premium" | Tap | Opens premium paywall |
| Pull down | Gesture | Refreshes leaderboard |

**Loading state:** Skeleton leaderboard rows.
**Empty state:** "No one has competed yet this month. Be the first!" (premium) or "Go Premium to be the first competitor!" (free)
**Error state:** "Could not load leaderboard. Pull down to retry."

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free users can VIEW leaderboards (top 10). Premium users can COMPETE (appear on leaderboard, see own rank). | Free user sees: ranks 1-10, then blurred. Premium user sees: full list + own highlighted position. |
| BR-2 | Challenges reset on the 1st of each month at 00:00 UTC. Previous month's results are preserved for viewing. | March challenge ends March 31 23:59 UTC. April challenge starts April 1 00:00 UTC. |
| BR-3 | MVP launches with 3 challenge types: | See challenge definitions below. |
| BR-4 | **Challenge 1: Lowest Cost/km** -- Metric: total expenses / total km driven in the month. Lower is better. Requires: at least 2 odometer readings and at least 3 expenses in the month. | User with 847 лв expenses and 2,000 km driven = 0.42 лв/km. |
| BR-5 | **Challenge 2: Best Fuel Efficiency** -- Metric: average L/100km from full-tank fill-ups in the month. Lower is better. Requires: at least 2 full-tank fuel entries with odometer in the month. | User averages 7.2 L/100km across 3 fill-ups. |
| BR-6 | **Challenge 3: Most Consistent Tracker** -- Metric: number of expenses logged in the month. Higher is better. Requires: at least 1 expense. | User logged 23 expenses this month = rank based on count. |
| BR-7 | Participants are anonymous. Display names are auto-generated (e.g., "Driver_7F3A" or "Racer #42"). No real names or usernames shown on leaderboards. | Leaderboard shows: #1 Driver_A3B2 -- 0.31 лв/km. No way to identify real user. |
| BR-8 | Leaderboards are global (all users, all vehicle types). No filtering by car model in MVP. | A user with a Prius and a user with a BMW X5 compete on the same board for cost/km. |
| BR-9 | Users who don't meet minimum data requirements for a challenge are not ranked. They see: "Log more data to qualify for this challenge." | Cost/km challenge: user with only 1 odometer reading can't compete. |
| BR-10 | Leaderboard updates are near-real-time (within 1 hour of an expense/fuel entry being logged). Not instant. | User logs fuel entry. Their rank updates within 1 hour on the leaderboard. |
| BR-11 | Past month results show the final leaderboard for 3 months, then are archived and no longer viewable in the app. | January results visible until April 1. After that, gone from the app. |
| BR-12 | A user can compete with multiple vehicles but their leaderboard entry uses data from their BEST vehicle (not aggregated). | User has 2 cars. Car A: 0.35 лв/km. Car B: 0.55 лв/km. Leaderboard shows: 0.35 лв/km. |

---

## 5. Data Requirements

**Entities involved:**

- `Challenge` -- id, type (cost_km/fuel_efficiency/consistency), month, year, status (active/completed), created_at
- `ChallengeParticipant` -- id, challenge_id, user_id, vehicle_id, metric_value, rank, qualified (boolean), updated_at
- `ChallengeResult` -- id, challenge_id, user_id, final_rank, final_metric_value, month, year (snapshot of final standings)

**Data read:** Expense totals, odometer readings, fuel entry consumption data (computed from existing entities). Challenge leaderboard entries.
**Data written:** ChallengeParticipant (updated on expense/fuel entry save). ChallengeResult (on month end).

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| challenge type | enum | Yes | "cost_km", "fuel_efficiency", "consistency" |
| metric_value | decimal | Computed | >= 0 |
| rank | integer | Computed | >= 1 |
| qualified | boolean | Computed | Based on minimum data rules per challenge type |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| Only 1 participant in a challenge | Show leaderboard with 1 entry. "#1 -- you're the champion! Invite friends to compete." |
| Premium user downgrades mid-month | User is removed from leaderboards. Their data remains for recalculation if they re-subscribe. |
| User with manipulated data (unrealistically low cost/km) | No automated fraud detection in MVP. Monitor manually. Consider adding outlier detection in v1.1. |
| Month with 0 challenges (feature not yet enabled) | Challenges hub shows: "Challenges launching soon! Start tracking to be ready." |
| User deletes expenses after ranking high | Metric recalculates on next update cycle. Rank changes accordingly. |
| Tie in rankings | Users with identical metric values share the same rank. Display order among tied users is arbitrary. |
| Very large number of participants (10,000+) | Paginate leaderboard. Show top 100 + user's position. |
| No internet | Show cached leaderboard with "Last updated [time]" note. |

---

## 7. Non-Functional Requirements

- **Performance:** Leaderboard loads < 2 seconds. Rank recalculation happens asynchronously (not blocking expense save).
- **Scalability:** Leaderboard ranking is a server-side batch job (every ~1 hour), not real-time per transaction.
- **Offline behavior:** Cached leaderboard viewable. No competition actions offline.
- **Accessibility:** Ranks communicated as text for screen readers: "Rank 12 of 142. Your metric: 0.38 лева per kilometer."
- **Localization:** Challenge titles and descriptions in Bulgarian/English. Metric formatting per locale.
- **Privacy:** Leaderboards are fully anonymous. No way to identify another user from their leaderboard entry.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth (user), Vehicle Management (vehicle), Expense Tracking (expense data for cost/km and consistency), Fuel Entry (consumption data for fuel efficiency), Cost Dashboard (cost/km calculation logic).
- **Depended on by:** Share & Export (sharing challenge rank on vehicle card -- future).
- **API endpoints needed:**
  - `GET /api/challenges` -- list active and recent past challenges
  - `GET /api/challenges/{id}/leaderboard` -- get leaderboard (paginated, includes user's rank if premium)
  - `GET /api/challenges/{id}/my-stats` -- current user's stats for a challenge (premium)
  - `GET /api/challenges/history` -- past challenge results

---

## 9. Out of Scope

- Achievement badges (C6) -- deferred to v1.1
- Vehicle-model-specific leaderboards (e.g., "Best among BMW E46 owners") -- deferred, requires critical mass
- Weekly or daily challenges -- MVP is monthly only
- Team/group challenges -- not planned
- Prizes or rewards beyond recognition -- not planned
- Challenge customization (user-created challenges) -- not planned
- Social features (follow, comment, react to leaderboard) -- explicitly excluded (NG5)

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should challenges be visible to free users at all, or only teased? | Full visibility drives FOMO and premium conversion. Hidden means free users don't know what they're missing. | Recommend: fully visible leaderboards (top 10) for free users. Strong conversion trigger. |
| OQ-2 | Is "Most Consistent Tracker" a good challenge? It rewards quantity, not quality. | Could incentivize fake/tiny expenses just to rank higher | Recommend: keep it for MVP as a retention driver. Consider minimum expense amount (e.g., > 1 лв) to qualify. |
| OQ-3 | How do we handle different vehicle types fairly? A Prius will always beat an SUV on fuel efficiency. | Unfairness could discourage participation | For MVP: accept it. In v1.1, introduce vehicle-class leaderboards (by fuel type, engine size, or model). |
| OQ-4 | Should we show the user's all-time best rank as a "personal best" motivator? | Low effort, high engagement | Recommend: include. Show "Your best: #3 in January 2026" on the challenge card. |

---

**New data entities discovered:** Challenge, ChallengeParticipant, ChallengeResult
**New API endpoints discovered:** 4 challenge endpoints (listed in Section 8)
