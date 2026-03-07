# Functional Specification: Cost Dashboard

**File:** `/03-product/functional-specs/cost-dashboard.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M5, S2, S7)
**Related features:** M5 (Cost Dashboard), S2 (Spending Trends), S7 (Currency Formatting)

---

## 1. Overview

**What this feature does:** Displays the user's total car cost for the current month as a prominent number, with a category breakdown chart, monthly trend bars, and (for premium users) cost per kilometer, year-over-year comparisons, and spending forecasts. This is the "hero screen" of the app -- the "aha moment."

**Why it matters:** The entire product exists for this screen. The moment a user sees "Your car costs you 847 лв this month" is the moment they understand the app's value. This screen converts free users into retained users, and retained users into premium subscribers (Pain: P1 don't know true cost, P2 surprised by costs; Gain: G1 see exact monthly cost, G2 cost per kilometer).

**User type:** All users. Free and premium tiers show different levels of detail.

---

## 2. User Flow

### Happy Path -- Monthly Review

1. User opens app (dashboard is the default home screen)
2. System shows current month's total cost (big number, top of screen)
3. User sees category breakdown (donut chart) below the total
4. User taps a category slice to see expenses in that category
5. User scrolls down to see monthly trend (bar chart, last 6 months)
6. User taps a past month's bar to see that month's details
7. (Premium) User sees cost/km metric, YoY comparison, and forecast section

### Alternative Paths

- **Path A: First-time user (just completed onboarding)** -- Dashboard shows the first expense they just logged. Small total. Motivational message: "Keep tracking to see your real monthly cost."
- **Path B: User with no expenses this month** -- Dashboard shows "0 лв" for current month with a CTA: "Log your first expense for [month]."
- **Path C: User switches vehicle** -- Dashboard refreshes with the selected vehicle's data.
- **Path D: Premium upsell interaction** -- Free user sees locked "Cost per km" section with blurred value and "Unlock with Premium" CTA. Tapping opens the premium paywall.
- **Path E: User taps "See All Expenses"** -- Navigates to the full expense list for the current month.

---

## 3. Screen Descriptions

### Screen: Cost Dashboard (Main)

**Purpose:** Show the user their car's true cost at a glance -- the app's core value delivery.
**Entry points:** App launch (default screen). Bottom nav "Dashboard" tab. After onboarding completion.

**Layout (top to bottom):**

**Section 1: Vehicle & Month Header**
- Vehicle selector (header component -- see Vehicle Management spec)
- Month selector: "< March 2026 >" (left/right arrows to navigate months)

**Section 2: Total Cost (The Hero Number)**
- Current month total: large, bold, prominent (e.g., "847.50 лв")
- Subtitle: "Total car costs in March 2026"
- Change indicator vs. previous month: "+12%" or "-8%" with arrow (green = lower, red = higher)

**Section 3: Cost per Kilometer [Premium]**
- "Cost per km: 0.42 лв/km" (calculated from total expenses / km driven this month)
- Free tier: show blurred "0.XX лв/km" with lock icon and "Unlock with Premium" label
- Tap (free): opens premium paywall
- Tap (premium): shows cost/km trend detail

**Section 4: Category Breakdown**
- Donut/pie chart showing expense distribution by category
- Legend below chart: category icon + name + amount + percentage
- Only categories with expenses are shown
- Tap a slice or legend item: navigates to filtered expense list for that category

**Section 5: Monthly Trend**
- Bar chart: last 6 months (or fewer if user hasn't tracked that long)
- Each bar shows the month's total cost
- Current month's bar is highlighted/colored differently
- Tap a bar: shows that month's total in a tooltip, tap again to drill into that month's expenses
- Free tier: 6 months shown
- [Premium] Extended to 12 months + trend line

**Section 6: Spending Trends [Premium -- S2]**
- Year-over-year comparison: "March 2026 vs March 2025: +15%"
- Category trend: "Maintenance costs are rising -- up 23% over 3 months"
- Free tier: this section shows a preview card: "See how your spending changes over time -- Go Premium"
- (Requires 2+ months of data to display)

**Section 7: Quick Actions**
- "Add Expense" button (opens Quick-Add)
- "Add Fuel" button (opens Fuel Entry)
- "See All Expenses" link (opens expense list for current month)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Month selector arrows | Tap | Navigate to previous/next month |
| Total cost number | Tap | Navigates to expense list for that month |
| Cost/km (free) | Tap | Opens premium paywall |
| Cost/km (premium) | Tap | Shows cost/km detail with trend |
| Donut chart slice | Tap | Navigates to expense list filtered by that category |
| Legend item | Tap | Same as tapping the chart slice |
| Bar chart bar | Tap | Shows tooltip with month total; second tap drills into month |
| Spending trends preview (free) | Tap | Opens premium paywall |
| "Add Expense" | Tap | Opens Quick-Add sheet |
| "Add Fuel" | Tap | Opens Fuel Entry form |
| "See All Expenses" | Tap | Opens expense list |
| Pull down | Gesture | Refresh dashboard data |

**Loading state:** Skeleton layout: gray placeholder for hero number, shimmer animation for chart areas, skeleton bars for trend chart. Full skeleton loads < 0.5s, data populates < 2s.
**Empty state (no expenses ever):** Hero number shows "0 лв". Charts replaced with illustration + "Start tracking your car costs to see the real picture." + "Add Your First Expense" prominent button.
**Empty state (no expenses this month):** Hero number shows "0 лв" for this month. Charts show from previous months if available. Message: "No expenses logged in [month] yet."
**Error state:** "Could not load your dashboard. Pull down to refresh." Retry on pull-to-refresh.

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Monthly total includes ALL expense categories for the active vehicle, including fuel entries. | 5 fuel entries (640 лв) + 2 maintenance (350 лв) + 1 insurance (200 лв) = 1,190 лв total |
| BR-2 | Month boundaries follow calendar months (1st to last day). | March total = all expenses dated March 1-31. |
| BR-3 | Cost per kilometer = total expenses for the month / total kilometers driven that month. | March expenses: 847 лв. Odometer March 1: 150,000. Odometer March 31: 152,000. Cost/km: 847 / 2,000 = 0.42 лв/km |
| BR-4 | Cost/km is a Premium feature. Free users see a blurred/locked version. | Free user sees: "0.XX лв/km [lock icon]" |
| BR-5 | Cost/km requires at least 2 odometer readings in the selected month (or spanning the month boundaries). If missing, show "Add odometer readings to see cost/km" instead of a number. | No odometer data -- premium users see a prompt, not a wrong number |
| BR-6 | Change indicator compares current month total to previous month total. | March: 847 лв. February: 756 лв. Change: +12% (red arrow up, costs increased) |
| BR-7 | Change indicator color: green (costs decreased or same), red (costs increased). | -8% = green arrow down. +12% = red arrow up. 0% = gray, no arrow. |
| BR-8 | Donut chart only shows categories that have expenses. Empty categories are omitted. | If only Fuel and Maintenance have expenses, the chart has 2 slices. |
| BR-9 | Monthly trend bar chart shows the last 6 months (free) or 12 months (premium). If user has fewer months, show only available months. | User registered in January: March dashboard shows Jan, Feb, Mar bars only. |
| BR-10 | Spending trends (S2) require at least 2 months of data. Not shown for new users. | User with 1 month of data: trends section hidden. After month 2: trends appear. |
| BR-11 | Year-over-year comparison requires same month data from previous year. If not available, show "Track for a full year to see YoY comparison." | No March 2025 data: message instead of comparison. |
| BR-12 | Dashboard data refreshes on: app open, pull-to-refresh, vehicle switch, month switch, and after saving an expense. | User saves an expense via Quick-Add, returns to dashboard -- total is updated. |
| BR-13 | Currency formatting follows user settings. Default: amount followed by лв (e.g., "847.50 лв"). EUR format: "847.50 EUR". | Consistent across all dashboard elements. |
| BR-14 | Dashboard always shows data for the ACTIVE vehicle only (no aggregate view in MVP). | User with 2 vehicles sees data for whichever is currently selected. |

---

## 5. Data Requirements

**Entities involved:**

- `Expense` -- aggregated for totals, category breakdown
- `FuelEntry` -- included in totals (via linked Expense), consumption data
- `OdometerReading` -- for cost/km calculation
- `Vehicle` -- active vehicle context

**Data read:**
- Monthly expense summary: total, by-category totals, expense count
- Monthly odometer readings: for cost/km
- Historical monthly totals: for trend chart (6-12 months)
- Fuel consumption average: for fuel section (if displayed)

**Data written:** None -- dashboard is read-only.

**API data shape (expected response from summary endpoint):**

```json
{
  "month": "2026-03",
  "vehicle_id": 1,
  "total": 847.50,
  "currency": "BGN",
  "previous_month_total": 756.00,
  "change_percent": 12.1,
  "cost_per_km": 0.42,
  "km_driven": 2000,
  "categories": [
    { "category": "fuel", "total": 480.00, "percent": 56.6, "count": 5 },
    { "category": "maintenance", "total": 250.00, "percent": 29.5, "count": 2 },
    { "category": "insurance", "total": 117.50, "percent": 13.9, "count": 1 }
  ],
  "monthly_trend": [
    { "month": "2025-10", "total": 620.00 },
    { "month": "2025-11", "total": 890.00 },
    { "month": "2025-12", "total": 1250.00 },
    { "month": "2026-01", "total": 710.00 },
    { "month": "2026-02", "total": 756.00 },
    { "month": "2026-03", "total": 847.50 }
  ]
}
```

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| Brand new user (0 expenses) | Hero: "0 лв". No charts. CTA: "Add your first expense to see your dashboard come alive." |
| User has expenses only in 1 month | Show that month's data. Trend chart shows 1 bar. No change indicator (no previous month). |
| All expenses in one category | Donut chart shows a full circle in one color. Legend shows only that category. |
| Very large total (e.g., 15,000 лв from a major repair) | Display full number. No truncation. Consider "15 000 лв" format with space as thousands separator (Bulgarian standard). |
| Very small total (e.g., 2.50 лв) | Display as "2.50 лв". No rounding issues. |
| Month with 0 expenses but previous months have data | Hero: "0 лв". Change: "-100%" (green). Trend bar for this month is zero-height. Charts empty for this month, historical data visible in trend. |
| Cost/km calculation with 0 km driven (no odometer updates) | Don't show cost/km. Show: "Update your odometer to see cost per km." |
| Division by zero in cost/km (0 km) | Prevented by not displaying when km = 0. |
| User rapidly switches months | Debounce API calls. Show loading state between fetches. Cancel previous request. |
| No internet on app open | Show last cached dashboard data with "Offline -- showing last updated data" banner. Pull-to-refresh retries. |

---

## 7. Non-Functional Requirements

- **Performance:** Dashboard load < 2 seconds on first open. Month switch < 1 second. Charts render smoothly (60fps animations).
- **Caching:** Cache the last viewed month's data locally. Pre-fetch previous month for fast switching.
- **Offline behavior:** Show cached dashboard data with offline banner. No stale data indicator needed beyond the banner.
- **Accessibility:** Hero number readable by screen readers with context: "Total car cost in March 2026: 847 лева and 50 stotinki." Charts have text alternatives in legend.
- **Localization:** Month names in Bulgarian (Март, Април, etc.). Currency per settings. Thousands separator: space (Bulgarian standard: "1 847.50 лв"). Decimal separator: period or comma per locale.
- **Charts:** Must support: donut/pie chart (category breakdown), bar chart (monthly trend), line chart (consumption trend if shown). Smooth animations on load and data change. Touch-interactive (tap for details).

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (data source), Fuel Entry (data source).
- **Depended on by:** Premium paywall (cost/km is a conversion trigger), Challenges (references dashboard metrics), Share & Export (shareable stats).
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/dashboard?month=2026-03` -- full dashboard data for a month
  - `GET /api/vehicles/{vehicleId}/dashboard/trend?months=12` -- monthly trend data
  - `GET /api/vehicles/{vehicleId}/dashboard/cost-per-km?month=2026-03` -- cost/km detail (premium)
  - `GET /api/vehicles/{vehicleId}/dashboard/spending-trends` -- YoY and category trends (premium)

---

## 9. Out of Scope

- Aggregate "all vehicles" dashboard -- deferred (single vehicle view only in MVP)
- Spending forecasts / predictions (C4) -- deferred to v1.2
- Model benchmarks on dashboard (C1) -- deferred to v1.1
- Keep vs. sell insights (C5) -- deferred to v1.2
- Custom date ranges (e.g., "last 90 days") -- MVP is strictly monthly
- Budget setting / budget vs. actual tracking -- not planned
- Dashboard widgets on device home screen -- deferred
- Export dashboard as image -- covered in Share & Export spec

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the dashboard show a "yearly total" in addition to monthly? | Users may want annual perspective without premium | Recommend: show "Year to date: X лв" below the monthly total for free users. Adds high impact with minimal effort. |
| OQ-2 | What chart library should we use? | Must support donut, bar, and line charts with touch interaction and smooth animation on mobile. | Tech stack dependent. Evaluate during architecture phase. |
| OQ-3 | Should cost/km on the free tier be completely hidden or teased with a blurred number? | Blurred number is a stronger conversion trigger ("I can almost see it") vs. hidden ("I don't know what I'm missing") | Recommend: blurred number with lock icon. More effective for premium conversion. |
| OQ-4 | How should we handle months where the user tracked inconsistently (e.g., logged 2 expenses but clearly spent more)? | Misleading totals could damage trust | No action for MVP. Trust the user's data. In v1.1, consider: "Looks like you might be missing some expenses this month." |
| OQ-5 | Should the donut chart show amounts or percentages as default labels? | Both are useful but showing both clutters | Recommend: show percentages on chart, amounts in legend below. Tap for details. |

---

**New data entities discovered:** None (dashboard is read-only, consumes existing entities)
**New API endpoints discovered:** 4 dashboard endpoints (listed in Section 8)
