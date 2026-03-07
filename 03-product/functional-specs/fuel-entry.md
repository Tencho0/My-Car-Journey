# Functional Specification: Fuel Entry

**File:** `/03-product/functional-specs/fuel-entry.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M4)
**Related features:** M4 (Fuel Entry — Specialized)
**Related journeys:** `/03-product/user-journeys/journey-daily-expense-logging.md`, `/03-product/user-journeys/journey-aha-moment.md`, `/03-product/user-journeys/journey-challenge-participation.md`

---

## 1. Overview

**What this feature does:** Provides a dedicated fuel logging form that captures liters, price per liter, total cost, odometer reading, and fill type (full/partial). Auto-calculates fuel consumption (L/100km) between fill-ups and shows consumption trends. Fuel is the most frequent expense type for car owners.

**Why it matters:** Fuel is logged 2-4 times per month — the most frequent single expense category. A specialized form reduces friction for this common action and enables consumption calculations that generic expense tracking can't provide. Fuel efficiency is also the basis for the "Best Fuel Efficiency" challenge (Journey 6) and contributes to the cost-per-km calculation that drives premium conversion (Journey 5). The fuel entry should provide immediate consumption feedback — "8.2 L/100km" with a trend arrow — creating a micro-aha moment every time they fill up (Journey 2 insight).

**User type:** All users who drive fuel/LPG vehicles. Unlimited entries in both free and premium tiers.

---

## 2. User Flow

### Happy Path — Detailed Fuel Entry

1. User opens app, taps + (FAB)
2. Quick-add sheet appears. User taps Fuel category icon.
3. System offers: "Quick add (amount only)" or "Detailed fuel entry" — user taps "Detailed"
4. Fuel entry screen opens with fields pre-filled: today's date, active vehicle
5. User enters liters (e.g., 42.5)
6. User enters price per liter (e.g., 2.85 лв) — total cost auto-calculates (121.13 лв)
7. User enters current odometer (e.g., 155,200 km)
8. If previous odometer exists: consumption auto-calculates: "8.2 L/100km" with trend arrow (up/down vs. last fill-up)
9. User selects fill type: Full tank / Partial fill
10. Optionally enters station name
11. User taps "Save"
12. Expense saved. Dashboard updates. Consumption trend updates.
13. Subtle feedback: "Your consumption: 8.2 L/100km (improved from 8.7)" (Journey 2 insight: immediate consumption feedback)

### Alternative Paths

- **Path A: Quick fuel add** — User taps Fuel category, selects "Quick add." Enters total amount only (like a regular expense). No liters, no consumption calculation. Fastest path.
- **Path B: Enter total cost + liters (calculate price/liter)** — User enters total cost (121.13 лв) and liters (42.5). Price per liter auto-calculates (2.85 лв/L). Either direction of auto-calculation works.
- **Path C: Partial fill** — User selects "Partial fill." Consumption is NOT calculated for this fill-up (partial fills break the calculation). Next full-fill calculation spans the partial.
- **Path D: No previous odometer** — First fuel entry or no prior odometer reading. No consumption calculated. Message: "Add your odometer reading to start tracking consumption."
- **Path E: From challenge context** — User participating in "Best Fuel Efficiency" challenge navigates to fuel entry. After save, rank update shown: "Fuel Efficiency Challenge: you moved to #34!" (Journey 6 connection)

---

## 3. Screen Descriptions

### Screen: Fuel Entry Form

**Purpose:** Specialized fuel logging with consumption calculation.
**Entry points:** "Detailed fuel entry" from quick-add when Fuel category is selected.

**Layout:**
- Header: "Log Fuel" with vehicle name/model
- Date field (pre-filled: today, tap to change)
- **Liters field** — numeric with 1 decimal (e.g., 42.5). Large, prominent.
- **Price per liter field** — numeric with 2 decimals (e.g., 2.85 лв/L)
- **Total cost field** — auto-calculated from liters x price/liter. Editable (triggers reverse calculation). Shows in лв.
- Auto-calculation indicator: when user enters 2 of 3 fields, the third auto-fills with a subtle "calculated" badge
- **Odometer field** — numeric (km). Shows last recorded reading as hint: "Last: 154,500 km"
- **Consumption result** (if calculable): "**8.2 L/100km**" with trend arrow (green down = improved, red up = worsened, gray dash = first entry or unchanged). Shows comparison: "vs. last fill: 8.7 L/100km"
- **Fill type toggle:** Full tank (default) / Partial fill
- **Station name field** — optional, free text (max 50 chars)
- "Save" primary button
- "Cancel" / back navigation

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Liters field | Type | If price/liter filled: total auto-calculates |
| Price/liter field | Type | If liters filled: total auto-calculates |
| Total cost field | Type | If liters filled: price/liter auto-calculates. If price/liter filled: liters auto-calculates. |
| Odometer field | Type | If previous reading exists: consumption calculates and displays immediately |
| Fill type toggle | Tap | Switches between Full/Partial. If Partial: consumption result hides with note "Consumption calculated on next full fill" |
| "Save" | Tap | Validates, saves fuel entry + expense, returns to dashboard |

**Loading state:** "Save" shows spinner.
**Empty state:** All fields empty except date (today) and vehicle (active).
**Error state:**
- No liters or total cost entered: inline — "Enter liters or total cost"
- Odometer lower than last reading: warning — "This is lower than your last reading (154,500 km). Are you sure?"
- Negative values: inline — "Value must be greater than zero"
- Network error: toast — "Could not save. Check your connection."

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Auto-calculation: any 2 of 3 fields (liters, price/liter, total cost) auto-calculates the third | Enter liters (42.5) + price/liter (2.85) → total auto-fills (121.13 лв). Or: enter total (120) + liters (42) → price/liter auto-fills (2.86 лв/L). |
| BR-2 | Consumption calculation: (liters / (current_odometer - previous_odometer)) * 100 = L/100km | Liters: 42.5, Current: 155,200, Previous: 154,680 → (42.5 / 520) * 100 = 8.17 L/100km |
| BR-3 | Consumption only calculated on full-tank fill-ups | Partial fills are excluded. If fill A is full, fill B is partial, fill C is full: consumption for C uses total liters from B+C and odometer delta from A to C. |
| BR-4 | If no previous odometer reading exists, consumption cannot be calculated | Show: "Add your odometer reading to start tracking consumption" |
| BR-5 | Fuel entry creates both a FuelEntry record and an Expense record | The expense record has category = "fuel" and amount = total cost. Both are linked. |
| BR-6 | Consumption trend arrow: green down arrow if improved (lower L/100km), red up arrow if worsened, gray dash if unchanged or first entry | Threshold for change: ±0.1 L/100km |
| BR-7 | Price per liter is stored with 3 decimal places for accuracy | Display with 2 decimals (2.85 лв), store with 3 (2.850). Bulgarian fuel prices sometimes have 3 decimals. |
| BR-8 | Station name is optional and free-text | No station database for MVP. Could be structured in future. |
| BR-9 | Fill type defaults to "Full tank" | Most users fill up completely. Partial is the exception. |
| BR-10 | Quick fuel add (amount only) creates an Expense with category = "fuel" but NO FuelEntry record | No consumption data. Faster but less insightful. |
| BR-11 | Odometer from fuel entry updates the vehicle's current_odometer_km | Keeps vehicle odometer current — critical for mileage-based maintenance reminders |
| BR-12 | Consumption feedback is immediate after entering odometer | No need to save first — calculate and display in real-time as the user types |

---

## 5. Data Requirements

**Entities involved:**

- `FuelEntry` — id, expense_id, vehicle_id, liters, price_per_liter, total_cost, odometer_km, fill_type (enum: full, partial), station_name (nullable), consumption_l_per_100km (nullable — calculated), created_at, updated_at
- `Expense` — linked via expense_id (category = "fuel", amount = total_cost)

**Data read:** Previous fuel entry for same vehicle (for consumption calculation), vehicle's last odometer reading
**Data written:** FuelEntry (create, update, delete), Expense (create, update, delete), Vehicle.current_odometer_km (update)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| liters | decimal | Yes (or total_cost) | > 0, <= 999.9, 1 decimal place |
| price_per_liter | decimal | No (auto-calculated) | > 0, <= 99.999, 3 decimal places |
| total_cost | decimal | Yes (or liters) | > 0, <= 999,999.99, 2 decimal places |
| odometer_km | integer | No (strongly encouraged) | Min: 0, Max: 9,999,999. Should be >= last recorded. |
| fill_type | enum | Yes (default: full) | Values: "full", "partial" |
| station_name | string | No | Max 50 chars |
| date | date | Yes (default: today) | Not in future. Not > 5 years in past. |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User enters total cost and liters but not price/liter | Auto-calculate price/liter. Show with "calculated" badge. |
| User enters only total cost (no liters) | Valid — treated as a simple fuel expense. No consumption calculation. |
| All 3 fields entered manually and conflict | Last-edited field takes priority. The other auto-recalculates. Example: user enters liters (42), price (2.85 = 119.70), then changes total to 120. Price recalculates to 2.857. |
| Odometer reading lower than previous | Warning dialog. Allow override. |
| Very high consumption (e.g., 25 L/100km) | Allow it — could be a heavy vehicle, spirited driving, or city traffic. No upper limit warning. |
| Very low consumption (e.g., 2 L/100km) | Allow it — could be a hybrid or error. No lower limit warning. |
| Partial fill after partial fill after partial fill | No consumption calculated until a full fill. Liters accumulate across partial fills for the eventual full-fill calculation. |
| First ever fuel entry (no history) | No consumption calculated. Message: "Great start! Track your next fill-up to see your fuel consumption." |
| User edits a past fuel entry's odometer | Recalculate consumption for this entry and the NEXT entry (cascade). Only recalculate adjacent entries, not the entire history. |
| Network error during save | Toast error. Form data preserved. Retry available. |

---

## 7. Non-Functional Requirements

- **Performance:** Auto-calculation is instant (client-side math). Save completes in < 1 second. Consumption display appears within 100ms of odometer entry.
- **Offline behavior:** Same as expense tracking. Requires internet for save in MVP. Calculations are all client-side and work offline.
- **Accessibility:** All fields labeled. Auto-calculated fields announce "calculated" to screen readers. Consumption result has descriptive text: "8.2 liters per 100 kilometers, improved from 8.7."
- **Localization:** Liters (not gallons — Bulgaria uses metric). Price in лв per liter. Consumption in L/100km (European standard, not MPG). Decimal separator: accept both period and comma.
- **Analytics events:** fuel_entry_started, fuel_entry_saved, fuel_quick_add_used, fuel_detailed_used, consumption_calculated, odometer_entered.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (fuel entry creates an expense)
- **Depended on by:** Cost Dashboard (fuel is a category in breakdown), Vehicle Timeline (fuel entries appear), Challenges (fuel efficiency challenge uses L/100km data), Maintenance Reminders (odometer updates from fuel entries trigger mileage-based reminders)
- **API endpoints needed:**
  - `POST /api/vehicles/{vehicleId}/fuel-entries` — create fuel entry (also creates linked expense)
  - `GET /api/vehicles/{vehicleId}/fuel-entries` — list fuel entries (for consumption history/trend)
  - `GET /api/vehicles/{vehicleId}/fuel-entries/{id}` — get fuel entry details
  - `PUT /api/vehicles/{vehicleId}/fuel-entries/{id}` — update fuel entry
  - `DELETE /api/vehicles/{vehicleId}/fuel-entries/{id}` — delete fuel entry (also deletes linked expense)
  - `GET /api/vehicles/{vehicleId}/fuel-entries/consumption-trend` — consumption over time (for trend display)

---

## 9. Out of Scope

- Fuel price database or station finder — not in MVP
- LPG dual-fuel tracking (separate LPG + petrol in one fill) — v1.1+
- Fuel cost optimization recommendations — future intelligence feature
- Electric vehicle charging tracking (kWh, charging station) — deferred. MVP focuses on combustion/LPG vehicles.
- Fuel receipts OCR — deferred (PRD NG2)
- Station reviews or ratings — not planned
- Fuel price comparison between stations — not planned

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should consumption data be free or premium? | L/100km is valuable data. But gating it might reduce fuel entry adoption. | Recommend: consumption is FREE. This encourages detailed fuel logging. Premium gets consumption trends over time and efficiency comparison with other owners. |
| OQ-2 | How do we handle LPG vehicles that use both LPG and petrol? | Some Bulgarian cars switch between fuels | Recommend: allow selecting fuel type per entry (Petrol/Diesel/LPG) within the fuel form. Separate consumption calculations per fuel type. Defer to v1.1 if complex. |
| OQ-3 | Should we build a station name autocomplete from user data over time? | Reduces typing, improves data consistency | Recommend: free text for MVP. Build autocomplete from aggregated user entries in v1.1+. |

---

**Data entities discovered:** FuelEntry (linked to Expense)
**API endpoints discovered:** 6 fuel entry endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: immediate consumption feedback with trend arrow (Journey 2 micro-aha), quick-add vs. detailed fuel entry choice, running total hint, odometer updates vehicle and triggers reminders, consumption as free feature to encourage detailed logging, challenge connection for fuel efficiency, partial fill accumulation logic. |
