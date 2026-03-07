# Functional Specification: Cost Dashboard

**File:** `/03-product/functional-specs/cost-dashboard.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M5, S2)
**Related features:** M5 (Cost Dashboard), S2 (Spending Trends)
**Related journeys:** `/03-product/user-journeys/journey-aha-moment.md`, `/03-product/user-journeys/journey-daily-expense-logging.md`, `/03-product/user-journeys/journey-premium-upgrade.md`, `/03-product/user-journeys/journey-first-time-experience.md`

---

## 1. Overview

**What this feature does:** Displays the user's true car cost at a glance — monthly total, category breakdown, annualized projection, and (premium) cost per kilometer, spending trends, and model benchmarks. This is the primary screen of the app, the home screen, and the destination after every expense save.

**Why it matters:** The dashboard IS the product. The entire app exists so users can see this screen with meaningful data. The monthly total is the "aha moment" that creates retention — users are typically shocked when they see their real car costs for the first time: "I had no idea my car cost me THAT much" (Journey 3). The dashboard transforms the app from a logging tool into a cost intelligence platform. It drives premium conversion through contextual, subtle teases for cost/km, trends, and benchmarks (Journey 5 — premium teases are whispers, not shouts).

**User type:** All users. Free tier: monthly total + basic category breakdown. Premium: adds cost/km, trends, forecasts, benchmarks.

---

## 2. User Flow

### Happy Path — Aha Moment (2-4 Weeks After Signup)

1. User opens app (routine check-in or after notification: "You've tracked 15 expenses this month. See your monthly total.")
2. Dashboard loads with accumulated data. Monthly total is prominent: "**847 лв** this month"
3. User processes the number — it's higher than expected
4. App reinforces with annualized projection below: "~10,164 лв per year" (Journey 3 amplification strategy)
5. User taps category breakdown (pie/donut chart)
6. Breakdown expands: Fuel 380 лв (45%), Maintenance 250 лв (30%), Parking 95 лв (11%), etc.
7. User taps a category to see that category's expenses
8. User scrolls down past breakdown
9. Encounters premium section: "Your cost per km: [lock icon] Unlock with Premium" — subtle, below the fold
10. User closes app or explores further

### Alternative Paths

- **Path A: Post-onboarding (1 expense)** — Dashboard shows single amount: "Your [Car Model] has cost you **127.50 лв** so far. Keep tracking to see your monthly total." Single category at 100%. No charts with 1 data point.
- **Path B: Building data (5-9 expenses)** — Growing total, 2-3 categories forming proportions. "You're getting close to your first full monthly view."
- **Path C: After expense save** — User just saved an expense. Dashboard updates instantly. Monthly total animation: number counts up to new total (micro-reward from Journey 2).
- **Path D: Premium user** — Dashboard shows additional sections: cost/km, spending trends (line chart), model benchmarks (if available), forecast.
- **Path E: Month navigation** — User swipes or taps to view previous months. Each month shows its own total and breakdown.
- **Path F: FAB (+) button** — Always visible. Tapping opens quick-add sheet (expense-tracking spec).

---

## 3. Screen Descriptions

### Screen: Dashboard (Main Screen)

**Purpose:** Show the user their true car cost at a glance. This is the hero screen of the entire product.
**Entry points:** App launch (default screen), after expense save, bottom navigation "Dashboard" tab.

**Layout (top to bottom):**

**Header:**
- Vehicle selector (active vehicle name + photo thumbnail, tap to switch)
- Current month label ("March 2026")

**Hero Section — THE MONTHLY TOTAL:**
- **The number** — largest, most prominent element on the entire screen. Large typography. Bold. Centered. No clutter around it. Example: "**847 лв**"
- Vehicle model below: "BMW E46 320i"
- Annualized projection (free feature): "~10,164 лв per year" in smaller text below the monthly total

**Design mandate (from Journey 3):** The monthly total is sacred. It must always be the largest, most prominent element on the dashboard. No competing elements. No visual clutter. This number IS the product.

**Category Breakdown Section:**
- Pie/donut chart showing expense categories with proportions
- Legend below chart: each category with icon, name, amount, percentage
- Tap any category to drill into expenses for that category this month

**Monthly Trend Section (visible after 2+ months):**
- Bar chart: monthly totals for last 6 months
- Current month highlighted
- Swipe to see older months
- If < 2 months: "Track for 2 months to see your spending trends"

**Premium Tease Section (free users only):**
- Subtle, below the fold, separated from free content with soft divider
- "Your cost per kilometer: [lock icon] Unlock with Premium" — tappable
- "See how your [model] compares to other owners → [lock icon]" — tappable
- "Spending trends over time → [lock icon]" — tappable
- Design: small lock icons, soft "Unlock with Premium" links. NOT a banner. NOT a modal. NOT aggressive. (Journey 5 anti-patterns: never full-screen paywall, never "are you sure?")

**Premium Sections (premium users only):**
- **Cost per kilometer:** "**0.42 лв/km**" — prominent number with trend indicator (up/down vs. previous month). Requires odometer data from fuel entries.
- **Spending trends:** Line chart — monthly totals over 6-12 months. Category trend lines toggleable.
- **Model benchmarks:** "Your BMW E46 costs X% more/less than average" (when sufficient community data exists). If no benchmark data: "Benchmarks for your model are coming soon."
- **Spending forecast:** "Based on your patterns, expect ~900 лв next month" (requires 3+ months of data). If < 3 months: "Forecasts available after 3 months of tracking."

**FAB (+) Button:**
- Always visible, bottom-right. Large touch target. Most prominent interactive element.
- The gateway to expense logging. Never hidden by scrolling.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Vehicle selector | Tap | Open vehicle switcher |
| Monthly total | Tap | No action (it's the hero — nothing to drill into from here) |
| Annualized projection | Tap | No action (informational) |
| Category in breakdown | Tap | Navigate to expense list filtered by that category for current month |
| Pie/donut chart segment | Tap | Same as tapping category legend |
| Monthly trend bar | Tap | Switch dashboard view to that month |
| Premium tease link | Tap | Navigate to premium benefits screen (Journey 5) |
| FAB (+) | Tap | Open quick-add sheet (expense-tracking spec) |
| Month label | Tap/swipe | Navigate to previous/next month |

**Loading state:** Show last-known cached state immediately. Refresh from API in background. If first load: skeleton with pulsing placeholder for the big number.
**Empty state — see Progressive Data Richness section below.**
**Error state:** If API fails: show cached data with subtle "Last updated: [time]" indicator. If no cached data: "Could not load your dashboard. Pull to refresh."

---

### Progressive Data Richness (Journey 3 Design)

The dashboard must feel like it's "filling up" as the user logs more expenses. This is NOT a single empty state — it's a progression:

| Data Level | Monthly Total | Category Breakdown | Monthly Trend | Premium Teases | Design Goal |
|------------|--------------|-------------------|---------------|---------------|-------------|
| **0 expenses** | "Add your first expense to start tracking" + illustration + CTA button | Not shown | Not shown | Not shown | Invite action. Make emptiness feel like a beginning. Never show empty charts. |
| **1 expense** | "Your [model] has cost you **X лв** so far. Keep tracking to see your monthly total." | Single category at 100%. Minimal but intentional — not lonely. | Not shown | Not shown | Celebrate the start. Set expectation: "this will grow." |
| **2-4 expenses** | Actual total, becoming more meaningful. | 1-2 categories with proportions forming. | Not shown | Not shown | Encourage more: "Add a few more to see the bigger picture." |
| **5-9 expenses** | Growing total. "You're getting close to your first full monthly view." | 3+ categories. Breakdown starting to be useful. | Not shown | Not yet (too early) | Build anticipation. The breakdown is forming. |
| **10+ expenses (1 month)** | **FULL AHA MOMENT.** Monthly total, bold and prominent. Annualized projection visible. | Complete pie/donut chart. User can see where money goes. | Single bar for current month. | Premium teases now visible: cost/km, benchmarks | **Maximize impact.** This is THE moment. Everything is designed for this. |
| **2+ months** | Monthly total with comparison to last month (arrow + delta). | Full breakdown. | Monthly trend bars visible. Comparison possible. | Full premium tease set. | Reinforce with patterns. The aha evolves into ongoing insight. |
| **6+ months** | Full dashboard with all elements active. | Rich breakdown with history. | 6-month trend chart fully populated. | All premium features rich with data. | The dashboard is now a genuine cost intelligence tool. |

---

### Aha Moment Amplification Strategies (Journey 3)

These strategies are explicitly designed into the dashboard to maximize the emotional impact:

| Strategy | Implementation | Location |
|----------|---------------|----------|
| **Annualized projection** | "~X лв per year" below monthly total | Always visible (free), after 1+ expenses |
| **Monthly summary notification** | Push: "Your [model] cost you 847 лв in February." | End of each month (if user has 5+ expenses that month) |
| **Milestone celebrations** | At 10, 25, 50, 100 expenses: "You've tracked 50 expenses! You know more about your car costs than 95% of owners." | Inline celebration card on dashboard, dismissable |
| **Category surprise** | Highlight unexpected category: "Parking cost you 95 лв this month — more than you might think" | Below breakdown, when a minor category exceeds 10% of total |
| **Year-to-date total** | "Since you started tracking: **4,235 лв** total" | Below monthly total, shown after 2+ months |
| **Month-over-month delta** | "12% more than last month" with up/down arrow | Next to monthly total, after 2+ months |

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Monthly total includes ALL expense categories for the active vehicle in the selected month | Fuel + maintenance + insurance + everything else = one total |
| BR-2 | Annualized projection = monthly total * 12 (simple multiplication, not forecasting) | 847 лв/month → "~10,164 лв per year" |
| BR-3 | Annualized projection is a FREE feature | It amplifies the aha moment and drives retention. Never gate this. (Journey 3 insight) |
| BR-4 | Category breakdown shows all categories with at least 1 expense this month | Categories with 0 expenses are hidden, not shown as 0% |
| BR-5 | Cost per kilometer is PREMIUM only | Calculation: total expenses this period / total km driven. Requires odometer data. Primary paywall trigger (Journey 5: 35% of conversions). |
| BR-6 | Premium teases are visible only after 10+ expenses (or 2+ weeks active) | Don't show premium to new users — let them experience free value first (Journey 5 anti-pattern: no premium during onboarding) |
| BR-7 | Monthly total updates instantly after expense save | Optimistic UI update. Number animation: counts up from old total to new total. Visible change is the micro-reward. (Journey 2 key moment) |
| BR-8 | Dashboard shows data for the active vehicle only | Vehicle switcher in header to change vehicle |
| BR-9 | Monthly trend chart shows last 6 months | Scrollable to see older months. Minimum 2 months needed before showing. |
| BR-10 | Model benchmarks require community data | If < 50 data points for the user's make/model: "Benchmarks for your model are coming as our community grows." Show seeded data for popular models if available. |
| BR-11 | Spending forecast requires 3+ months of data | Simple pattern matching, not ML. "Based on patterns, expect ~X лв next month." Premium only. |
| BR-12 | Month-over-month comparison shows delta (amount and percentage) | "12% more than January" or "180 лв less than January" with color-coded arrow |
| BR-13 | Dashboard caches last-known state for instant display on app open | Show cached data immediately. Refresh from API in background. User sees current data within 1-2 seconds. |
| BR-14 | Milestone celebrations (10, 25, 50, 100 expenses) show once and are dismissable | Don't re-show dismissed milestones. Track milestone_shown in user preferences. |

---

## 5. Data Requirements

**Entities involved:**

- `Expense` — aggregated by vehicle_id, month, category
- `FuelEntry` — for cost/km calculation (odometer data)
- `Vehicle` — for model name, photo in header

**No new entities created by the dashboard — it reads and aggregates existing data.**

**Data read:**
- Monthly expense totals (sum by vehicle + month)
- Category breakdown (sum by vehicle + month + category)
- Monthly trend (totals for last 6-12 months)
- Cost per km (total expenses / odometer delta for the period) — premium
- Fuel consumption trend (from FuelEntry) — premium
- Model benchmark data (aggregated community data) — premium, future

**Calculated fields (server-side or client-side):**

| Calculation | Formula | Tier |
|-------------|---------|------|
| Monthly total | SUM(expenses.amount) WHERE vehicle_id AND month | Free |
| Category breakdown | SUM(expenses.amount) GROUP BY category WHERE vehicle_id AND month | Free |
| Annualized projection | Monthly total * 12 | Free |
| Year-to-date total | SUM(expenses.amount) WHERE vehicle_id AND year = current_year | Free |
| Month-over-month delta | Current month total - previous month total | Free (after 2 months) |
| Cost per km | SUM(expenses.amount in period) / (max_odometer - min_odometer in period) | Premium |
| Consumption trend | From FuelEntry consumption_l_per_100km over time | Premium |
| Spending forecast | Average of last 3 months + known upcoming (reminders) | Premium |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No expenses at all | Empty state with illustration and CTA: "Add your first expense to start tracking." Never show empty charts. |
| Only 1 expense | Show the amount with encouraging message. No pie chart (100% of one category isn't useful as a chart). Show category name instead. |
| No expenses this month (but has history) | "0 лв this month. New month, fresh start!" Show previous month's total for reference. FAB (+) prominent. |
| All expenses in one category | Pie chart shows 100% for that category. Note: "All your expenses this month are [category]. Log other types to see your full breakdown." |
| No odometer data (can't calculate cost/km for premium) | Premium cost/km section: "Add odometer readings to your fuel entries to see your cost per km." |
| Network error on dashboard load | Show cached data with "Last updated: [time]" subtle indicator. Pull-to-refresh available. |
| User views very old month (e.g., 6 months ago) | Load from API. Show that month's total and breakdown. If no data: "No expenses tracked in [month]." |
| Number feels inaccurate (user forgot expenses) | Below total: "Missing any expenses? Add older ones to see a more accurate total." Tappable — opens quick-add with date picker visible. (Journey 3 drop-off mitigation) |
| Number is depressing (very high) | Frame positively. Never judge or suggest spending less. "Knowledge is power. Now you can make informed decisions." (Journey 3 insight) |
| Premium tease tapped | Smooth transition to premium benefits screen. No jarring popup. |

---

## 7. Non-Functional Requirements

- **Performance:** Dashboard loads (from cache) in < 500ms on app open. Fresh data from API in < 2 seconds. Monthly total number animation is smooth (60fps). Charts render in < 1 second.
- **Offline behavior:** Dashboard shows cached data when offline. Fully functional for viewing. FAB (+) leads to quick-add (which may or may not save offline depending on tech stack).
- **Accessibility:** Monthly total announced to screen readers: "Your BMW E46 cost you 847 лева this month." Chart has accessible alternative (text list of categories and amounts). Premium teases have descriptive labels.
- **Localization:** Currency in лв. Numbers use Bulgarian formatting. Month names in Bulgarian. Category names in Bulgarian.
- **Analytics events:** dashboard_viewed, category_breakdown_tapped, category_drilled, month_navigated, premium_tease_impressed, premium_tease_tapped, milestone_shown, milestone_dismissed. Track session duration on dashboard.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (data source), Fuel Entry (for cost/km data)
- **Depended on by:** Share & Export (vehicle card stats come from dashboard data), Challenges (displays challenge rank on dashboard)
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/dashboard` — monthly total, category breakdown, annualized projection, milestones
  - `GET /api/vehicles/{vehicleId}/dashboard/trend` — monthly totals for last N months
  - `GET /api/vehicles/{vehicleId}/dashboard/cost-per-km` — cost/km calculation (premium)
  - `GET /api/vehicles/{vehicleId}/dashboard/benchmark` — model benchmark comparison (premium)
  - `GET /api/vehicles/{vehicleId}/dashboard/forecast` — spending forecast (premium)

---

## 9. Out of Scope

- "All vehicles" aggregate dashboard — defer to v1.1
- Custom date ranges (e.g., "last 90 days") — MVP shows by calendar month
- Budget setting and tracking ("I want to spend max 800 лв/month") — future feature
- PDF export of dashboard — see share-export spec
- Interactive chart drilling (pinch-to-zoom on charts) — simple tap to drill for MVP
- Real-time model benchmarks — requires community data. Seed with public data for popular models.
- Weekly or daily views — monthly is the primary view for MVP

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the annualized projection adjust based on available data (e.g., use average if < 12 months of data)? | Avoids misleading projections from 1-month data | Recommend: always multiply current month by 12. Simple and clear. Add note: "Based on this month's spending." |
| OQ-2 | Should cost/km be calculated per month or cumulative (lifetime)? | Monthly is more actionable. Lifetime is more stable. | Recommend: show both. Monthly as primary, lifetime as secondary context. |
| OQ-3 | When should premium teases first appear? | Too early = aggressive. Too late = missed conversion window. | Recommend: after 10+ expenses OR 2+ weeks active, whichever comes first. (Journey 5 prerequisite) |
| OQ-4 | Should category breakdown use a pie/donut chart or a horizontal bar chart? | Pie charts can be hard to read with many small categories | Recommend: donut chart for visual impact (dashboard hero), with text legend below showing exact amounts. Consider horizontal bars as alternative if usability testing shows issues. |

---

**Data entities discovered:** None new (dashboard reads existing entities). Dashboard-specific API endpoints: 5.
**API endpoints discovered:** 5 dashboard endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: progressive data richness stages explicitly defined (Journey 3), aha moment amplification strategies (annualized projection, milestones, category surprises, monthly summaries), premium teases as "whispers not shouts" (Journey 5 anti-patterns), monthly total as sacred design element, number animation on update (Journey 2 micro-reward), delayed premium tease visibility (10+ expenses), month-over-month comparison design, empty states that invite rather than show blank charts. |
