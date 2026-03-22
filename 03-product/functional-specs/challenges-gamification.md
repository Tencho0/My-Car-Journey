# Functional Specification: Challenges & Gamification

**File:** `/03-product/functional-specs/challenges-gamification.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (S4)
**Related features:** S4 (Basic Challenges)
**Related journeys:** `/03-product/user-journeys/journey-challenge-participation.md`, `/03-product/user-journeys/journey-daily-expense-logging.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

> **⚠️ Deferred to v2:** This feature is planned for v2. Not included in MVP launch. The specification is retained for future implementation.

## 1. Overview

**What this feature does:** Provides monthly challenges where users compete anonymously on metrics like cost per km, fuel efficiency, and expense logging consistency. Includes leaderboards, rank tracking, and badges for top performers. Free users can view leaderboards; premium users can actively compete.

**Why it matters:** Car enthusiasts naturally compare — "my car vs. your car." Challenges channel this competitive instinct into structured competition that also reinforces the core logging behavior (Gain G9: have fun with car ownership). Every expense logged affects challenge metrics, giving users an additional reason to log consistently. Challenges serve as a premium conversion trigger (Journey 5: 10% of conversions) and a retention mechanism through monthly cycles that bring users back.

**User type:** All users can view. Premium users can compete. P1 (Should Have) — include if time allows.

---

## 2. User Flow

### Happy Path — Discover and Join a Challenge

1. User discovers Challenges tab (1-4 weeks after signup, via bottom navigation or nudge notification)
2. Challenge Hub loads: 2-3 active monthly challenges listed with descriptions
3. User taps a challenge (e.g., "Lowest Cost per km")
4. Sees full leaderboard: anonymous usernames, rank, metric value. Their position highlighted.
5. (Premium) Taps "Join Challenge" — joined. Rank calculated from existing data: "You're currently #47. Keep tracking to climb!"
6. (Free) Sees "Go Premium to compete" below leaderboard — can still see rankings and their theoretical rank
7. Throughout the month: logs expenses as usual. After each save, rank may update with subtle notification: "Cost/km Challenge: moved up to #43!"
8. Month ends. Push notification: "Monthly challenge results are in! Your cost/km: 0.38 лв. Rank: #12."
9. Opens results: final leaderboard, badge if top 10. "Next month's challenges start tomorrow."
10. New month: re-joins challenges. Cycle continues.

### Alternative Paths

- **Path A: Free user views leaderboard** — Can see all rankings. Sees their theoretical position. "Go Premium to compete and earn badges." FOMO builds over time. (Journey 6: creates desire, not resentment)
- **Path B: Insufficient data** — User joined but hasn't logged enough (e.g., no fuel entries with odometer for fuel efficiency). "Log more fuel entries with odometer readings to participate in this challenge."
- **Path C: Mid-month check-in** — User visits Challenge Detail: sees updated rank, distance to next rank, days remaining in month.
- **Path D: Didn't participate** — Month ended without joining. "You didn't participate this month. Join a challenge to compete next month!"
- **Path E: Dashboard rank indicator** — Small badge on dashboard: "Cost/km Challenge: #47" — always visible for joined challenges.

---

## 3. Screen Descriptions

### Screen: Challenge Hub

**Purpose:** Overview of all available and past challenges.
**Entry points:** Bottom navigation "Challenges" tab, dashboard rank indicator tap.

**Layout:**
- Header: "Challenges" + current month
- Active challenges section (2-3 cards):
  - Each card: challenge name, icon, short description, participant count, your current rank (if joined)
  - Status: "Active — 18 days remaining"
- Past results section (collapsible): last month's results summary
- If not enough users yet: "Challenges launch when 50+ users are active." (Journey 6: seed with enough users)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Challenge card | Tap | Open Challenge Detail |
| Past results | Tap | Expand to see last month's rankings |

**Loading state:** Skeleton cards.

**Empty state (challenges not yet live):**
"Challenges are coming soon! Start tracking your expenses to be ready when they launch."

**Empty state (challenges live but user is new):**
"Join a challenge to see how you compare to other car owners." Challenge cards visible and tappable.

**Error state:** "Could not load challenges. Pull to refresh."

---

### Screen: Challenge Detail

**Purpose:** View challenge description, full leaderboard, and your position.
**Entry points:** Tap challenge card from Hub, dashboard rank badge.

**Layout:**
- Challenge header: name, icon, description (1-2 sentences)
- Your status section:
  - If joined: "Your rank: #47" + your metric value + trend arrow (up/down since joining)
  - If not joined (premium): "Join Challenge" primary button
  - If not joined (free): your theoretical rank shown + "Go Premium to compete" button
- **Leaderboard:**
  - Top 10 shown with special visual treatment (gold/silver/bronze for top 3)
  - Your position highlighted (even if outside top 10)
  - Columns: Rank, Username (anonymous), Metric value
  - If joined: "Distance to next rank: 0.02 лв/km" (motivational)
- Challenge info footer:
  - "X participants this month"
  - "Ends in Y days"
  - "How it's calculated" expandable explanation

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Join Challenge" | Tap | Join. Rank calculated. Confirmation: "You're in! Rank: #47" |
| "Go Premium to compete" | Tap | Navigate to premium benefits screen |
| "How it's calculated" | Tap | Expand explanation of the metric |

**Loading state:** Leaderboard skeleton while loading.
**Empty state (joined, insufficient data):**
"Log more expenses and fuel entries this month to improve your rank." Specific guidance based on challenge type.
**Error state:** "Could not load leaderboard. Pull to refresh."

---

### Screen: Monthly Results

**Purpose:** Show end-of-month challenge results.
**Entry points:** Push notification tap at month end, Challenge Hub > Past results.

**Layout:**
- Challenge name and month
- Your final rank and metric
- Improvement indicator: "You improved from #47 to #12 this month!"
- Badge earned (if top 10): visual badge with animation
- "Your stats this month" summary: expenses logged, km driven, average fuel efficiency
- "New challenges start tomorrow — rejoin?" CTA

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free users can VIEW leaderboards and see their theoretical rank. Premium users can JOIN and actively compete. | Free: "You'd be #34. Go Premium to compete." Premium: "Join Challenge" button available. |
| BR-2 | Challenges are anonymous. Display name or auto-generated username, never real name. | Leaderboard shows "Driver_2847" or user-chosen display name |
| BR-3 | Challenge cycle: 1st to last day of each month. Results published on 1st of next month. | March challenge: March 1-31. Results: April 1. |
| BR-4 | Start with 2-3 challenges. Add more based on engagement data. | Initial set: Lowest Cost/km, Best Fuel Efficiency, Most Consistent Tracker |
| BR-5 | Challenges require minimum 50 active participants to launch | Don't launch challenges if user base is too small — empty leaderboards are demotivating |
| BR-6 | "Most Consistent Tracker" is the most democratic challenge — anyone can win | Metric: number of expenses logged in the month. Not skill or car dependent. |
| BR-7 | Cost/km calculation: total expenses / km driven (from odometer delta) in the month | Requires at least 2 odometer readings in the month. Without odometer data: can't participate in this challenge. |
| BR-8 | Fuel efficiency: average L/100km from fuel entries in the month | Requires at least 2 full-tank fuel entries with odometer. |
| BR-9 | After logging an expense that affects challenge rank, show micro-notification | "Cost/km Challenge: moved up to #43!" Brief, dismissable. Not for every entry — only when rank actually changes. (Journey 6 key moment) |
| BR-10 | Top 10 earns a badge. Badges visible on vehicle profile. | Badge: "Top 10 — Cost/km — March 2026" |
| BR-11 | Challenge rank indicator on dashboard (small badge) for joined challenges | "Cost/km: #47" — tappable, navigates to challenge detail |
| BR-12 | Results are archived. Users can see their historical challenge performance. | "Your history: #12 in March, #8 in February" |
| BR-13 | Challenge model supports sponsor and scope fields for future expansion | sponsor: null (MVP), future: brand_id. scope: "global" (MVP), future: "vehicle_class", "team", "fleet" |

---

## 5. Data Requirements

**Entities involved:**

- `Challenge` — id, name, description, metric_type (enum: cost_per_km, fuel_efficiency, expense_count), month, year, status (enum: upcoming, active, completed), participant_count, min_participants (default: 50), sponsor_id (nullable — future), scope (enum: global, vehicle_class, team, fleet — default: global), created_at
- `ChallengeParticipant` — id, challenge_id, user_id, vehicle_id, current_rank, current_metric_value, badge_earned (nullable), joined_at, updated_at
- `ChallengeBadge` — id, user_id, challenge_id, badge_type (e.g., "top_10"), month, year, earned_at

**Data read:** Active challenges, leaderboard (ranked participants), user's rank and metric, historical results
**Data written:** ChallengeParticipant (join, rank updates), ChallengeBadge (earned at month end)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| challenge_id | reference | Yes | Must reference an active challenge |
| vehicle_id | reference | Yes | Must be user's vehicle |
| current_metric_value | decimal | Calculated | Based on metric_type and user's data for the month |
| current_rank | integer | Calculated | Position in leaderboard |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User joins mid-month | Rank calculated from existing data for the month. Fair — uses all available data. |
| User has no fuel entries (can't do fuel efficiency challenge) | "This challenge requires fuel entries with odometer. Log your next fill-up to participate." |
| User has no odometer data (can't do cost/km challenge) | "Add odometer readings to participate in this challenge." |
| Only 5 participants | Challenge is marked "Coming soon — need 50 participants." Show count: "5/50 users ready." |
| Tie in rank | Same rank for tied users. Next rank skips (e.g., two #3, next is #5). |
| User switches vehicle mid-month | Challenge metric recalculates for the new vehicle. Consider: should challenge track one vehicle per month? |
| User downgrades from premium mid-challenge | Participation paused. Rank preserved but marked inactive. If re-subscribes: resumes. |
| Month ends with no data from user | "You didn't have enough data to rank this month. Try next month!" |
| Very few participants make leaderboard uninteresting | Badge threshold scales: if < 20 participants, top 3 get badges. If < 10, top 1. |

---

## 7. Non-Functional Requirements

- **Performance:** Leaderboard loads in < 2 seconds. Rank updates are near-real-time (recalculated hourly or on expense save). Badge earning is batch-processed at month end.
- **Privacy:** Leaderboard is anonymous. No real names, no vehicle details shared. Username only.
- **Offline behavior:** Challenge Hub and leaderboard require internet. Cached state shown if available.
- **Accessibility:** Leaderboard is a structured list with rank, username, and metric announced by screen readers. Badge earned has visual and text indication.
- **Localization:** Challenge names and descriptions in Bulgarian. Metric values in Bulgarian formatting (лв/km, L/100km).
- **Analytics events:** challenge_hub_viewed, challenge_detail_viewed, challenge_joined, challenge_rank_changed, challenge_badge_earned, challenge_premium_prompt_shown, challenge_premium_prompt_tapped.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (for expense_count and cost/km), Fuel Entry (for fuel efficiency and odometer data)
- **Depended on by:** Cost Dashboard (shows challenge rank badge)
- **API endpoints needed:**
  - `GET /api/challenges` — list active and recent challenges
  - `GET /api/challenges/{id}` — challenge details with leaderboard
  - `POST /api/challenges/{id}/join` — join a challenge (premium only)
  - `GET /api/challenges/{id}/rank` — get user's current rank and metric
  - `GET /api/users/me/challenges` — user's active and past challenge participation
  - `GET /api/users/me/badges` — user's earned badges

---

## 9. Out of Scope

- Sponsored challenges (brand partnerships) — Phase 2+
- Vehicle-class sub-leaderboards (sedan vs. SUV) — future, when user base is large enough
- Team challenges (car clubs competing) — Phase 2
- Fleet challenges (internal company competition) — Phase 3
- Achievement badges (non-challenge milestones like "100 expenses") — deferred to v1.1 (C6)
- Custom challenges created by users — not planned
- Real-time leaderboard (websockets) — hourly recalculation is sufficient for MVP
- Social features (chat, comments on challenge results) — not in MVP

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should challenges launch from day 1 or wait for critical mass? | Empty leaderboards are demotivating | Recommend: launch when 50+ active users. Show "coming soon" before that. |
| OQ-2 | Should cost/km challenge track one vehicle or allow switching mid-month? | Switching could game the system | Recommend: one vehicle per challenge per month. Chosen at join time. |
| OQ-3 | Should the "Most Consistent Tracker" challenge be open to free users as a teaser? | Would let free users experience competition, driving premium for the other challenges | Recommend: consider for post-launch if conversion is below target. MVP: all challenges require premium to compete. |
| OQ-4 | How should badges be visually designed to feel valuable? | Low-quality badges feel meaningless | Recommend: invest in badge design. Automotive-themed. Shows on vehicle profile and (future) shareable vehicle card. |

---

**Data entities discovered:** Challenge, ChallengeParticipant, ChallengeBadge
**API endpoints discovered:** 6 challenge endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: free users see leaderboard + theoretical rank (Journey 6 creates desire not resentment), micro-notifications on rank change (Journey 6 key moment), monthly cycle with clear start/end, 50-user minimum before launch, dashboard rank indicator, "Most Consistent Tracker" as democratic challenge, sponsor and scope fields for future expansion, badge scaling for small participant counts. |
