# Functional Specification: Fuel Entry

**File:** `/03-product/functional-specs/fuel-entry.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M4)
**Related features:** M4 (Fuel Entry -- Specialized)

---

## 1. Overview

**What this feature does:** Provides a dedicated fuel logging flow with specialized fields: liters, price per liter, total cost, odometer, full/partial fill indicator, and optional station name. Automatically calculates fuel consumption (L/100km) between fill-ups.

**Why it matters:** Fuel is the most frequent car expense. A dedicated flow reduces entry time vs. the generic expense form and enables consumption tracking -- a key differentiator. Bulgarian enthusiasts care about fuel efficiency, especially with rising fuel prices (Pain: F2 track fuel efficiently; Gain: G2 cost per kilometer understanding).

**User type:** All users.

---

## 2. User Flow

### Happy Path -- Log a Full Fill-Up

1. User taps "+" FAB, then taps "Fuel" icon in Quick-Add (or navigates to Fuel tab)
2. System shows Fuel Entry form
3. User enters price per liter (e.g., 2.85 лв/L)
4. User enters liters (e.g., 45.2 L)
5. System auto-calculates total cost (128.82 лв)
6. User enters current odometer reading (e.g., 155,200 km)
7. User selects "Full tank" (toggle -- default ON)
8. User optionally enters station name
9. User taps "Save"
10. System saves fuel entry, calculates consumption if possible, shows success toast: "128.82 лв -- 7.8 L/100km"
11. User returns to previous screen

### Alternative Paths

- **Path A: Enter total cost instead of per-liter** -- User taps "Total" toggle. Enters total cost (128.82 лв) + liters (45.2 L). System auto-calculates price per liter (2.85 лв/L).
- **Path B: Partial fill** -- User toggles "Full tank" OFF. Consumption is NOT calculated (needs two consecutive full fills). System stores entry but marks it as partial.
- **Path C: No odometer** -- User skips odometer field. Fuel entry is saved but consumption cannot be calculated. System shows hint: "Add your odometer reading to track fuel consumption."
- **Path D: Edit existing fuel entry** -- User taps a fuel entry in timeline or fuel log. Taps "Edit." Modifies fields. Saves. Consumption recalculated for affected entries.
- **Path E: View fuel history and consumption trend** -- User navigates to Fuel tab (or section within dashboard). Sees list of fuel entries + consumption chart.

---

## 3. Screen Descriptions

### Screen: Fuel Entry Form

**Purpose:** Log a fuel fill-up with specialized fields for consumption calculation.
**Entry points:** "Fuel" icon in Quick-Add sheet. "Add Fuel" from Fuel log/tab. "Fuel" shortcut (if implemented).

**Layout:**
- "Log Fuel" heading
- Input mode toggle: "Per Liter" (default) | "Total"
- **Per Liter mode:**
  - Price per liter field (numeric, 2 decimal, лв/L)
  - Liters field (numeric, 1 decimal, L)
  - Total cost (auto-calculated, displayed prominently, read-only)
- **Total mode:**
  - Total cost field (numeric, 2 decimal, лв)
  - Liters field (numeric, 1 decimal, L)
  - Price per liter (auto-calculated, displayed, read-only)
- Odometer field (numeric, km -- with hint showing last recorded reading)
- Full tank toggle (switch, default: ON, label: "Full tank")
- Station name field (optional, text, with autocomplete from previous entries)
- Date field (pre-filled: today, tappable to change)
- Vehicle selector (pre-filled: active vehicle)
- Notes field (optional, single line)
- "Save" primary button

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Per Liter / Total toggle | Tap | Switches input mode, recalculates derived field |
| Price per liter | Type | Auto-calculates total (if liters entered) |
| Liters | Type | Auto-calculates total or price per liter |
| Total cost | Type (Total mode) | Auto-calculates price per liter |
| Full tank toggle | Toggle | ON: consumption will be calculated. OFF: partial fill noted. |
| Station name | Type | Autocomplete from previous station entries |
| "Save" | Tap | Validates, saves, shows consumption in toast |

**Loading state:** "Save" shows spinner.
**Empty state:** All input fields empty. Hint below odometer: "Last reading: [X] km on [date]" (or "No odometer recorded yet").
**Error state:**
- No liters entered: inline -- "Enter the number of liters"
- No price/total entered: inline -- "Enter the price per liter" or "Enter the total cost"
- Odometer lower than last reading: warning (not blocking) -- "This is lower than your last reading ([X] km). Continue?"
- Network error: toast with retry

---

### Screen: Fuel Log / History

**Purpose:** View all fuel entries and consumption trend for the active vehicle.
**Entry points:** "Fuel" tab or section in the app. "See all fuel entries" from dashboard.

**Layout:**
- Consumption summary card (top):
  - Current average consumption: "X.X L/100km" (calculated from last 5 full-tank entries)
  - Trend arrow: up (getting worse), down (improving), flat
  - "Last fill: [date] at [station]"
- Consumption trend chart (line chart, last 10-20 entries, L/100km over time)
- Fuel entry list (newest first):
  - Each row: date | liters + лв/L | total cost | consumption (if calculated) | station
  - Partial fills marked with a "Partial" badge
- "Add Fuel" FAB

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Fuel entry row | Tap | Opens fuel entry detail/edit |
| Chart | Tap data point | Shows tooltip: date, L/100km, station |
| "Add Fuel" | Tap | Opens Fuel Entry Form |

**Loading state:** Skeleton card + list.
**Empty state:** Illustration + "No fuel entries yet. Log your first fill-up to start tracking consumption." + "Add Fuel" button.
**Error state:** "Could not load fuel history. Pull down to retry."

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Fuel consumption (L/100km) is calculated only between two consecutive FULL-TANK fill-ups for the same vehicle. | Fill 1: full, 150,000 km. Fill 2: partial, 150,300 km. Fill 3: full, 150,600 km. Consumption calculated for fills 1-to-3 combined. |
| BR-2 | Consumption formula: (liters at fill N) / (odometer at fill N - odometer at fill N-1) * 100 | Fill at 150,000 km. Next full fill: 45.2 L at 150,600 km. Consumption: 45.2 / 600 * 100 = 7.53 L/100km |
| BR-3 | If multiple partial fills exist between two full fills, sum the liters for consumption calculation. | Full at 150,000. Partial: 20L. Partial: 25L. Full: 40L at 150,800. Total liters: 20+25+40 = 85L. Consumption: 85/800*100 = 10.63 L/100km |
| BR-4 | Consumption is not calculated if odometer is missing from either the current or previous full fill. | Fill without odometer -- no consumption displayed. Hint: "Add odometer for consumption tracking." |
| BR-5 | Average consumption is calculated from the last 5 full-tank fill-up intervals. | If user has 3 intervals, average of 3. If user has 10 intervals, average of the most recent 5. |
| BR-6 | Fuel entries are also stored as Expenses (category: "fuel") so they appear in the cost dashboard and timeline. | A 128.82 лв fuel entry appears both in the fuel log and in the expense list/timeline under "Fuel" category |
| BR-7 | Price per liter and total cost auto-calculate from each other. User can edit either one. | Enter 2.85 лв/L and 45.2L = 128.82 лв total. User changes total to 130 лв = 2.88 лв/L recalculated. |
| BR-8 | Station name autocomplete suggests from the user's previous entries only (not a global database). | User has logged "Shell Младост" and "OMV Люлин" before. Typing "Sh" suggests "Shell Младост". |
| BR-9 | Fuel type is inherited from the vehicle profile. If a vehicle is "Diesel", the fuel entry is implicitly diesel. | No fuel-type selector on the fuel form. Vehicle fuel type is shown as context: "Diesel -- BMW 320d" |
| BR-10 | For LPG dual-fuel vehicles: fuel entries should distinguish between petrol and LPG fills. | Show a fuel-type selector ONLY for LPG vehicles: "Petrol" or "LPG". Consumption tracked separately per fuel type. |
| BR-11 | Liters must be > 0 and <= 999.9. Price per liter must be > 0 and <= 99.99. Total must be > 0 and <= 99,999.99. | 0 liters = rejected. 1000 liters = rejected. |

---

## 5. Data Requirements

**Entities involved:**

- `FuelEntry` -- id, vehicle_id, user_id, liters, price_per_liter, total_cost, currency, odometer_km, is_full_tank, station_name, fuel_type (for dual-fuel vehicles), date, notes, created_at, updated_at
- `Expense` -- A corresponding Expense record is created for every FuelEntry (category="fuel", amount=total_cost). The fuel_entry_id links them.
- `OdometerReading` -- Created/updated when odometer is provided.

**Data read:** Previous fuel entries (for consumption calculation, station autocomplete). Last odometer reading (for hint display). Vehicle fuel type.
**Data written:** FuelEntry, Expense (auto-created), OdometerReading.

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| liters | decimal(6,1) | Yes | > 0, <= 999.9 |
| price_per_liter | decimal(5,2) | Yes (or derived) | > 0, <= 99.99 |
| total_cost | decimal(10,2) | Yes (or derived) | > 0, <= 99,999.99 |
| odometer_km | integer | No (recommended) | > 0, <= 9,999,999, >= last reading (soft warning) |
| is_full_tank | boolean | Yes | Default: true |
| station_name | string | No | Max 100 chars |
| fuel_type | enum | Only for LPG vehicles | "petrol", "lpg" |
| date | date | Yes | <= today |
| notes | string | No | Max 500 chars |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| First ever fuel entry (no previous data) | No consumption calculated. Show: "Log your next full fill-up to see your consumption." |
| Only partial fills logged (never a full tank) | No consumption calculated. Show hint: "Mark a fill as 'Full tank' to calculate consumption." |
| Odometer goes backwards (e.g., gauge reset, typo) | Soft warning: "This reading is lower than your last ([X] km). Save anyway?" Allow override. |
| Very high consumption calculated (> 30 L/100km) | Display normally but show a note: "Unusually high -- double-check your odometer and liters." Likely data entry error. |
| Very low consumption (< 2 L/100km) | Display normally but show note: "Unusually low -- double-check your entries." |
| User edits a past fuel entry's odometer or liters | Recalculate consumption for this entry and the next one in sequence. |
| User deletes a fuel entry | Remove entry. Recalculate consumption for surrounding entries if affected. |
| Price per liter has 3+ decimals (some stations price at 2.849) | Allow up to 3 decimal places for price per liter input. Display rounded to 2 decimals. Store full precision. |
| No internet | Toast: "No connection. Entry not saved." Form data preserved. |

---

## 7. Non-Functional Requirements

- **Performance:** Fuel entry save < 1 second. Consumption calculation is instant (client-side from cached data). Fuel log loads < 1.5 seconds.
- **Offline behavior:** No offline saves in MVP. Cached fuel log viewable offline.
- **Accessibility:** Numeric keypads for all number fields. Full tank toggle is labeled for screen readers.
- **Localization:** Price format follows locale (2.85 лв/L for BG). Liters abbreviated as "L". Consumption unit: "L/100km" (standard in Europe).

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management (vehicle with fuel_type).
- **Depended on by:** Cost Dashboard (fuel costs in totals, consumption display), Vehicle Timeline (fuel entries as events), Challenges (fuel efficiency challenge).
- **API endpoints needed:**
  - `POST /api/vehicles/{vehicleId}/fuel-entries` -- create fuel entry (also creates Expense)
  - `GET /api/vehicles/{vehicleId}/fuel-entries` -- list fuel entries (with pagination)
  - `GET /api/vehicles/{vehicleId}/fuel-entries/{id}` -- get fuel entry detail
  - `PUT /api/vehicles/{vehicleId}/fuel-entries/{id}` -- update fuel entry
  - `DELETE /api/vehicles/{vehicleId}/fuel-entries/{id}` -- delete fuel entry (also deletes linked Expense)
  - `GET /api/vehicles/{vehicleId}/fuel-entries/consumption` -- get consumption stats (average, trend)
  - `GET /api/vehicles/{vehicleId}/fuel-entries/stations` -- autocomplete station names

---

## 9. Out of Scope

- Fuel price comparison / cheapest station finder -- not planned
- Fuel price trends (national average tracking) -- not planned
- Multiple fuel types per single entry (e.g., petrol + LPG in one trip) -- not needed
- CO2 emissions calculation -- deferred
- Trip-based fuel tracking (fuel per specific route) -- deferred
- Integration with fuel station APIs / loyalty cards -- not planned

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should consumption be calculated client-side or server-side? | Affects data consistency if entries are edited on multiple devices | Recommend: server-side calculation triggered on save/edit/delete. Return calculated value in API response. |
| OQ-2 | For LPG vehicles, should we track petrol and LPG consumption separately on the fuel log? | Affects chart display and average calculation | Recommend: yes, separate consumption tracking per fuel type. Show toggle on fuel log: "All / Petrol / LPG" |
| OQ-3 | Should we show fuel price trends over time (what the user paid per liter)? | Nice for cost awareness but low priority | Defer to v1.1. Simple to add once data exists. |
| OQ-4 | How do we handle electric vehicles? They don't use liters. | EV charging: kWh, price/kWh, range added | Recommend: for MVP, treat EV fuel entries as: kWh (instead of liters), price/kWh, total cost. Consumption: kWh/100km. Adapt labels but same form structure. |

---

**New data entities discovered:** FuelEntry
**New API endpoints discovered:** 7 fuel entry endpoints (listed in Section 8)
