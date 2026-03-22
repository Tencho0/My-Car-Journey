# Functional Specification: Expense Tracking

**File:** `/03-product/functional-specs/expense-tracking.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M3, S3, S6, S7)
**Related features:** M3 (Expense Tracking), S3 (Quick-Add Widget), S6 (Category Icons), S7 (Currency Formatting)
**Related journeys:** `/03-product/user-journeys/journey-daily-expense-logging.md`, `/03-product/user-journeys/journey-first-time-experience.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Allows users to log any car-related expense quickly using a minimal quick-add interface. Supports 10 expense categories with visual icons, smart defaults, optional detail fields, and instant dashboard feedback. Also supports editing, deleting, and reviewing past expenses.

**Why it matters:** This is the most frequent interaction in the entire product. If logging feels like work, retention dies within 2 weeks regardless of how good the dashboard is (Journey 2 critical truth). The expense tracking habit is the engine that powers the aha moment (Journey 3), feeds the timeline (Journey 4), and ultimately drives premium conversion (Journey 5). The core interaction must complete in under 30 seconds total (app open to app close), with the actual input taking under 10 seconds and 3 taps + typing an amount (Journey 2 target).

**User type:** All users. Unlimited expense entries in both free and premium tiers.

---

## 2. User Flow

### Happy Path — Quick-Add (Standard, Most Common)

Target: Under 30 seconds total. Core input: 3 taps + amount entry.

1. User opens app — dashboard loads instantly (<1 second), FAB (+) button prominent in bottom-right
2. User taps + (FAB) button
3. Quick-add sheet slides up from bottom. Pre-filled: today's date, active vehicle. Category icon grid visible. Numeric keypad ready.
4. User taps category icon (e.g., wrench for Maintenance)
5. Category highlights. Numeric keypad auto-opens (or becomes active).
6. User types amount (e.g., "127.50") — amount formats in лв as typed
7. Running total hint visible: "Month total: 847 лв + 127.50 = 974.50 лв" (Journey 2 insight)
8. User taps "Save"
9. Expense saved. Success micro-animation (checkmark + subtle haptic). Sheet dismisses.
10. Dashboard updates instantly with new monthly total. User SEES the number change. (Journey 2 key moment)
11. User closes app — total time: 10-15 seconds

### Alternative Paths

- **Path B: Quick-add widget (fastest)** — User taps home screen widget. Widget opens quick-add directly. Pre-filled: today, default vehicle. Category + amount + save. Target: under 10 seconds total.
- **Path C: With optional details** — After selecting category and entering amount, user taps "More details" (expandable section). Shows: subcategory, notes, odometer, date change. Saves with all details.
- **Path D: Fuel category selected** — User taps Fuel icon. System offers: "Quick fuel entry" (amount only) or "Detailed fuel entry" (redirects to fuel-entry spec with liters, price/liter, odometer, consumption calculation).
- **Path E: Edit existing expense** — User taps an expense in the timeline or expense list. Edit screen opens with all fields pre-filled. User modifies and saves. No confirmation dialog for edits — friction-free (Journey 2 insight).
- **Path F: Delete expense** — From edit screen, user taps "Delete." Simple confirmation: "Delete this expense?" Yes/No. Deleted. Dashboard and timeline update.
- **Path G: Batch catch-up** — User hasn't logged in 3+ days. Dashboard shows: "Welcome back! You might have expenses to catch up on." Quick-add supports changing the date for backfilling.
- **Path H: From push notification** — User receives "Did you fuel up today?" notification. Taps it. App opens to quick-add with Fuel category pre-selected.

### Flow Diagram

```
Entry Points:
[Dashboard FAB +] --> [Quick-Add Sheet]
[Home Widget]     --> [Quick-Add Sheet]
[Notification]    --> [Quick-Add Sheet (category pre-selected)]
[Timeline entry]  --> [Edit Expense]

Quick-Add Sheet:
[Category Icon Grid] --> Tap icon --> [Numeric Keypad + Amount]
    |                                       |
    v (optional)                            v
[More Details]                         [Save button]
[subcategory, notes,                        |
 odometer, date]                            v
    |                               [Success animation]
    v                                       |
[Save button]                               v
    |                               [Dashboard updates
    v                                instantly with
[Success + Dashboard]                new monthly total]
```

---

## 3. Screen Descriptions

### Screen: Quick-Add Sheet

**Purpose:** The fastest way to log an expense. The single most important interaction in the entire app.
**Entry points:** FAB (+) button on dashboard, home screen widget, push notification tap.

**Layout:**
- Bottom sheet that slides up, covering ~60% of screen
- Top row: vehicle selector (shows active vehicle — tap to switch), date (shows "Today" — tap to change)
- Category icon grid: 2 rows of 5 icons with small labels below each:
  - Row 1: Fuel (pump icon), Maintenance (wrench), Mods (car with star), Insurance (shield), Tax (document)
  - Row 2: Tires (tire icon), Parking (P sign), Fines (warning triangle), Car Wash (water droplet), Other (+)
- After 5+ entries: "Quick picks" row above the grid showing last 3 used categories (Journey 2 smart default)
- Amount field: large, prominent, лв suffix. Numeric keypad auto-opens when category is selected.
- Running total hint (subtle text below amount): "Month total: 847 лв + [amount] = [new total] лв"
- "More details" expandable link (collapsed by default)
- "Save" primary button (large, bottom of sheet)

**Expanded "More details" section (only when tapped):**
- Subcategory dropdown (filtered by selected category)
- Notes field (single line, free text)
- Odometer field (number, km)
- Date picker (if user needs to change from today)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Vehicle selector | Tap | Opens vehicle switcher bottom sheet |
| Date display ("Today") | Tap | Opens date picker |
| Category icon | Tap | Selects category (icon highlights with accent color + label bolds). Numeric keypad becomes active. |
| Quick-pick category | Tap | Selects that category (same as tapping from grid) |
| Amount field | Type | Number formats with лв suffix. Running total updates. |
| "More details" | Tap | Expands subcategory, notes, odometer, date fields |
| "Save" | Tap | Validates (amount > 0, category selected), saves expense, success animation, dismisses sheet, dashboard updates |
| Swipe down / tap outside | Gesture | Dismisses sheet (confirm discard if amount entered) |
| Fuel category | Tap | Shows option: "Quick add" (stays in sheet) or "Detailed fuel entry" (navigates to fuel-entry screen) |

**Loading state:** "Save" button shows brief spinner (<500ms target). Sheet remains visible until save completes.
**Empty state:** No category selected, amount field empty and ready. Keypad not yet active until category tapped.
**Error state:**
- No amount entered, taps Save: inline below amount — "Enter an amount"
- No category selected, taps Save: brief shake animation on category grid — "Choose a category"
- Network error on save: toast — "Could not save. Will retry when connected." (queue for retry if possible, else "Check your connection")
- Amount = 0: inline — "Amount must be greater than zero"

---

### Screen: Expense Detail / Edit

**Purpose:** View or edit an existing expense.
**Entry points:** Tap any expense in timeline, tap expense in category drill-down on dashboard.

**Layout:**
- All fields from quick-add, pre-filled with existing data
- Category icon grid with current category highlighted
- Amount field with current amount
- All "More details" fields visible (not collapsed)
- Photo thumbnails if photos attached (tap to view full-size)
- "Save Changes" primary button
- "Delete" link (bottom, danger style)
- Timestamps: "Added [date] at [time]" / "Last edited [date]" (subtle, bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Any field | Edit | Marks as changed (Save button activates) |
| "Save Changes" | Tap | Saves edits. Returns to previous screen. Dashboard/timeline update. |
| "Delete" | Tap | Confirmation: "Delete this expense?" Yes/No |
| Back without saving | Gesture | If changes exist: "Discard changes?" Yes/No |

**Loading state:** "Save" shows spinner.
**Empty state:** N/A (always has data — this is an edit screen)
**Error state:**
- Network error: toast — "Could not save changes. Try again."
- Concurrent edit (rare): last-write-wins for MVP

---

### Screen: Expense List (Optional, Within Dashboard)

**Purpose:** View all expenses for the active vehicle, filterable by month and category.
**Entry points:** "See all expenses" from dashboard, category drill-down.

**Layout:**
- Filter bar: month selector (current month default, swipe to change), category filter (all or specific)
- List of expenses: date, category icon, amount (лв), note preview (if any)
- Sorted by date (newest first)
- Total for filter at top: "March 2026: 847 лв (23 expenses)"
- Tap any expense to edit

**Loading state:** Skeleton list while loading.
**Empty state (no expenses for this filter):** "No expenses for [Month] in [Category]." CTA: "Log an expense"
**Error state:** "Could not load expenses. Pull to refresh."

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Expense logging is unlimited in both free and premium tiers | Never gate the core interaction — this builds the habit that drives conversion (Journey 2, Journey 5 anti-pattern) |
| BR-2 | Minimum viable expense: category + amount. Everything else is optional. | 3 taps + amount entry = saved expense. No required notes, no required odometer. |
| BR-3 | Default date = today. 95%+ of entries are same-day. | Date picker available but not shown by default. |
| BR-4 | Default vehicle = active (last used) vehicle | Vehicle selector visible but not required to interact with |
| BR-5 | Amounts are stored in the user's primary currency (лв default) | All amounts in лв. EUR conversion display available but лв is stored. |
| BR-6 | Amount must be > 0 and <= 999,999.99 лв | Covers any realistic car expense including vehicle purchase |
| BR-7 | Expense categories are fixed (10 categories). Users cannot add custom categories but can add subcategories under "Other." | Categories: Fuel, Maintenance, Modifications, Insurance, Tax, Tires, Parking, Fines, Car Wash, Other |
| BR-8 | After 5+ logged expenses, show "quick picks" — the user's last 3 used categories above the grid | Reduces selection time for repeat categories |
| BR-9 | When Fuel category is selected, offer choice: quick add (amount only) or detailed fuel entry | Detailed fuel entry navigates to fuel-entry spec |
| BR-10 | After saving, dashboard monthly total must update visibly and instantly | The number animation (counting up to new total) is the micro-reward that reinforces the habit loop (Journey 2 key moment) |
| BR-11 | Editing an expense is friction-free: no confirmation dialog for edits. Just save. | Tap expense → edit → save. No "Are you sure?" for edits. |
| BR-12 | Deleting an expense requires simple confirmation: "Delete this expense?" | Not aggressive, but prevents accidental taps |
| BR-13 | Expense entries automatically create timeline entries | Each expense appears in the vehicle timeline with date, category icon, amount |
| BR-14 | Date can be backdated (for catching up on missed entries) | Allow any date within the last 5 years. No future dates. |
| BR-15 | Running total hint during entry shows the impact of this expense on the monthly total | "Month total: 847 лв + 127.50 = 974.50 лв" — reinforces the value of tracking |

---

## 5. Data Requirements

**Entities involved:**

- `Expense` — id, vehicle_id, user_id, amount, currency, category, subcategory (nullable), date, odometer_km (nullable), notes (nullable), source (enum: manual, reminder_completion, garage_sync — default: manual), created_at, updated_at

**Data read:** Expenses list (filtered by vehicle, month, category), monthly totals (for running total hint)
**Data written:** Expense (create, update, delete)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| amount | decimal | Yes | > 0, <= 999,999.99, 2 decimal places |
| currency | enum | Yes (default: BGN) | Values: "BGN", "EUR" |
| category | enum | Yes | Values: "fuel", "maintenance", "modifications", "insurance", "tax", "tires", "parking", "fines", "car_wash", "other" |
| subcategory | string | No | Max 50 chars. Free text or from suggested list per category. |
| date | date | Yes (default: today) | Cannot be in the future. Cannot be more than 5 years in the past. |
| odometer_km | integer | No | Min: 0, Max: 9,999,999. Must be >= vehicle's last recorded odometer (warn if not). |
| notes | string | No | Max 500 chars |
| source | enum | Yes (default: manual) | Values: "manual", "reminder_completion", "garage_sync" |

**Suggested subcategories per category (for the subcategory dropdown):**

| Category | Suggested Subcategories |
|----------|------------------------|
| Fuel | Regular, Premium, Diesel, LPG |
| Maintenance | Oil change, Brakes, Filters, Fluids, Belts, Diagnostics, Other |
| Modifications | Wheels, Exhaust, Suspension, Body, Interior, Electronics, Other |
| Insurance | Liability, Full coverage (Kasko), Renewal |
| Tax | Annual tax, Registration fee |
| Tires | Summer tires, Winter tires, All-season, Mounting/balancing |
| Parking | Monthly pass, Hourly, Garage rent |
| Fines | Speeding, Parking violation, Other |
| Car Wash | External wash, Interior cleaning, Full detailing |
| Other | User-defined |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No internet when saving | If tech stack supports offline queue: save locally, sync when connected, show "Saved offline — will sync." If not: toast "No connection. Try again." Preserve form data. |
| User enters amount with wrong decimal separator | Accept both comma and period as decimal separators (Bulgarian convention varies). Store as period. |
| User tries to save without category | Brief shake animation on category grid. Inline message: "Choose a category." |
| User enters very large amount (e.g., 50,000 лв) | Allow it — could be a major repair or vehicle purchase. No warning. |
| User enters very small amount (e.g., 0.01 лв) | Allow it — could be a rounding or parking meter amount. |
| User closes app during save | If save was in progress: retry on next launch. If not yet saved: data lost (no draft saving in MVP). |
| User logs 50+ expenses in one month | All stored and displayed. Performance must not degrade. Pagination in expense list if needed. |
| Expense for a date in a previous month | Correctly attributed to that month in dashboard totals. Monthly breakdown recalculates. |
| Returning after 7+ days of inactivity | Dashboard shows gentle re-engagement: "Welcome back! You might have expenses to catch up on." Quick-add supports date changes for backfilling. (Journey 2 empty state) |
| New month starts with 0 expenses | Dashboard shows "0 лв this month" with cheerful prompt: "New month, fresh start. Log your first expense." (Journey 2 empty state) |
| Quick-add dismissed with amount entered | Confirm discard: "You have an unsaved expense. Discard?" Yes/No |

---

## 7. Non-Functional Requirements

- **Performance:** Quick-add sheet opens in < 300ms. Expense save completes in < 1 second (API response). Dashboard updates instantly after save (optimistic UI update, don't wait for server confirmation). App cold start to quick-add save: under 15 seconds median (Journey 2 target).
- **Offline behavior:** MVP requires internet connection for all operations. Offline support is not planned for MVP (see MVP feature list W1).
- **Accessibility:** Category icons have accessible labels. Amount field announces currency. Save confirmation is both visual (animation) and haptic. Minimum touch targets: 44x44pt for all interactive elements.
- **Localization:** Category names in Bulgarian by default (Гориво, Поддръжка, Модификации, Застраховка, Данък, Гуми, Паркиране, Глоби, Автомивка, Друго). Amount formatting: Bulgarian convention (period or comma as decimal separator accepted). Currency display: "127.50 лв".
- **Analytics events:** expense_add_started, expense_category_selected, expense_saved, expense_edited, expense_deleted, quick_add_opened, quick_add_dismissed, more_details_expanded, fuel_detailed_entry_chosen. Track time from quick_add_opened to expense_saved.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth (user must exist), Vehicle Management (expense belongs to a vehicle)
- **Depended on by:** Cost Dashboard (aggregates expenses), Vehicle Timeline (displays expenses), Fuel Entry (specialized expense type), Maintenance Reminders (completion logs expense), Challenges (metrics calculated from expenses)
- **API endpoints needed:**
  - `POST /api/vehicles/{vehicleId}/expenses` — create expense
  - `GET /api/vehicles/{vehicleId}/expenses` — list expenses (with filters: month, category, date range)
  - `GET /api/vehicles/{vehicleId}/expenses/{id}` — get expense details
  - `PUT /api/vehicles/{vehicleId}/expenses/{id}` — update expense
  - `DELETE /api/vehicles/{vehicleId}/expenses/{id}` — delete expense
  - `GET /api/vehicles/{vehicleId}/expenses/summary` — monthly totals, category breakdown (for running total hint)

---

## 9. Out of Scope

- Receipt OCR / photo-to-amount extraction — deferred (PRD NG2)
- Bank/card integration for automatic expense import — deferred (PRD NG3)
- Income tracking — deferred (PRD NG7)
- Recurring expense templates (auto-create monthly entries) — future feature
- Expense splitting (shared costs between vehicles) — not needed
- Expense approval workflows (fleet) — Phase 3
- Offline expense queue — depends on tech stack decision, not guaranteed in MVP
- Custom category creation — fixed 10 categories for MVP (subcategories under "Other" serve as escape valve)

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the home screen widget be a P0 (must-have) or P1 (should-have) for MVP? | Widget is the fastest entry point (under 10 seconds). Critical for retention. | PRD lists as P1 (S3). Recommend elevating to P0 if tech stack supports it easily. |
| OQ-2 | Should we implement offline expense saving? | Major UX improvement for users at gas stations or garages with poor connectivity | Depends on tech stack. If feasible: yes. If complex: defer. |
| OQ-3 | Should amounts be stored in a single currency or support multi-currency? | Affects users who travel or buy parts from abroad | Recommend: store in primary currency (лв). Allow EUR display conversion. Multi-currency storage is v1.1+. |
| OQ-4 | Should we show the running total hint only after 3+ expenses (to avoid showing "0 + X = X")? | Running total of "0 + 50 = 50" isn't impactful | Recommend: show running total only when month has at least 1 prior expense. Otherwise just show "This will be your first expense this month." |

---

**Data entities discovered:** Expense (with source field for future multi-source support)
**API endpoints discovered:** 6 expense endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: under-30-second total time target enforced, 3-tap minimum interaction, FAB button as most prominent element, running total hint during entry, success micro-animation as habit reward, smart defaults (quick picks after 5+ entries), category icon grid design, edit without confirmation dialog, dashboard instant update requirement, re-engagement for inactive users, source field for future garage/fleet integration. |
