# Functional Specification: Expense Tracking

**File:** `/03-product/functional-specs/expense-tracking.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M3, S1, S3, S6, S7)
**Related features:** M3 (Expense Tracking), S1 (Photo Attachments), S3 (Quick-Add Widget), S6 (Category Icons), S7 (Currency Formatting)

---

## 1. Overview

**What this feature does:** Allows users to log any car-related expense (except fuel, which has a dedicated flow) quickly and accurately. Supports 10 pre-defined categories with icons, optional photos, notes, and odometer readings. Includes a quick-add mode for <10-second entry and a full-detail mode for comprehensive logging.

**Why it matters:** This is the core interaction of the entire product. Every other feature (dashboard, timeline, trends, challenges) depends on expense data existing. If logging is too slow or tedious, users stop (Pain: P1 don't know true cost, P8 tedious entry; Gain: G1 see exact monthly cost, G8 save time).

**User type:** All users.

---

## 2. User Flow

### Happy Path -- Quick-Add Expense

1. User taps "+" FAB (floating action button) from any screen
2. System shows Quick-Add sheet (bottom sheet overlay)
3. Amount field is auto-focused, numeric keypad open
4. User types amount (e.g., "85.50")
5. User taps a category icon (e.g., Maintenance)
6. Date is pre-filled (today), vehicle is pre-filled (active vehicle)
7. User taps "Save"
8. System saves expense, shows brief success toast: "85.50 лв saved"
9. Sheet closes, user is back where they started
10. Total time: <10 seconds

### Happy Path -- Full-Detail Expense

1. User taps "+" FAB, then taps "Full Details" link in Quick-Add sheet (or navigates to Expenses tab > "Add Expense")
2. System shows Full Expense Form
3. User fills in: amount, category, subcategory, date, odometer, notes
4. User optionally attaches 1-3 photos
5. User taps "Save"
6. System saves expense, navigates to expense list or timeline

### Alternative Paths

- **Path A: Edit existing expense** -- User taps an expense in the timeline or expense list. Views detail. Taps "Edit." Modifies fields. Taps "Save."
- **Path B: Delete expense** -- User taps an expense, taps "Delete." Confirmation: "Delete this expense? This cannot be undone." User confirms.
- **Path C: Add expense from widget** (S3) -- Home screen widget opens Quick-Add directly. Pre-filled: today's date, active vehicle. User enters amount + category. Saves.
- **Path D: Change vehicle during entry** -- In Full Details mode, user taps vehicle field and selects a different vehicle from dropdown.
- **Path E: Add expense for past date** -- User taps date field, picks a past date from date picker.

---

## 3. Screen Descriptions

### Screen: Quick-Add Sheet (Bottom Sheet)

**Purpose:** Log an expense in under 10 seconds with minimum friction.
**Entry points:** "+" FAB from any main screen. Home screen widget (S3).

**Layout:**
- Bottom sheet overlay (60% screen height)
- Amount field (large, prominent, auto-focused, numeric keypad)
- Currency indicator: "лв" (or "EUR" based on settings)
- Category icon grid (2 rows of 5 icons):
  - Row 1: Fuel (redirects to fuel form), Maintenance, Mods, Insurance, Tax
  - Row 2: Tires, Parking, Fines, Car Wash, Other
- Selected category highlighted with label below icons
- Pre-filled info row: date (today) + vehicle name (active) -- both tappable to change
- "Save" primary button (full width)
- "Full Details" link (below Save)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Amount field | Type | Enters number with decimal support |
| Category icon | Tap | Selects category, highlights icon, shows label |
| Fuel icon | Tap | Closes Quick-Add, opens dedicated Fuel Entry form |
| Date | Tap | Opens date picker |
| Vehicle name | Tap | Opens vehicle selector dropdown |
| "Save" | Tap | Validates and saves expense |
| "Full Details" | Tap | Expands to Full Expense Form |
| Swipe down / tap outside | Gesture | Closes sheet (confirm if data entered) |

**Loading state:** "Save" button shows spinner.
**Empty state:** Amount empty, no category selected.
**Error state:**
- No amount: shake amount field, inline message "Enter an amount"
- No category: flash category grid, inline message "Choose a category"
- Network error: toast "Could not save. Tap to retry." (data preserved)

---

### Screen: Full Expense Form

**Purpose:** Log an expense with all available detail.
**Entry points:** "Full Details" from Quick-Add. "Add Expense" from expense list. "Edit" from expense detail.

**Layout:**
- Amount field (numeric, with currency)
- Category selector (dropdown or icon grid)
- Subcategory field (dropdown, filtered by category -- see subcategory list below)
- Date field (date picker, default: today)
- Vehicle selector (dropdown, default: active vehicle)
- Odometer field (optional, numeric, km)
- Notes field (optional, multi-line text, max 500 chars)
- Photo attachments area (optional):
  - "Add Photo" button (camera or gallery)
  - Thumbnail previews of attached photos (1-3)
  - Tap thumbnail to view full-size or remove
- "Save" primary button
- "Cancel" secondary button / back navigation

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Category | Select | Updates subcategory options |
| Subcategory | Select | Sets specific expense type |
| Date | Tap | Opens date picker (past dates allowed, future dates blocked) |
| "Add Photo" | Tap | Opens camera/gallery picker |
| Photo thumbnail | Tap | Preview full-size with "Remove" option |
| "Save" | Tap | Validates, saves, navigates back |
| Back/Cancel | Tap | Confirms discard if unsaved changes |

**Loading state:** "Save" shows spinner. Photo upload shows progress indicator per photo.
**Empty state:** (Add mode) All fields empty with placeholders except date and vehicle.
**Error state:**
- Required fields missing: inline validation
- Photo upload failed: toast "Photo failed to upload. Expense saved without photo."
- Network error: toast with retry

---

### Screen: Expense Detail (View)

**Purpose:** View a single expense with all its details.
**Entry points:** Tap an expense in the timeline or expense list.

**Layout:**
- Category icon + category name (header)
- Amount (large, prominent)
- Date
- Vehicle name
- Subcategory (if set)
- Odometer (if recorded)
- Notes (if any)
- Photos (if any -- tappable thumbnails, open full-screen gallery)
- "Edit" button
- "Delete" button (bottom, destructive style)
- Created/modified timestamps (small, bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Edit" | Tap | Opens Full Expense Form with pre-filled data |
| "Delete" | Tap | Confirmation dialog, then deletes |
| Photo thumbnail | Tap | Opens full-screen photo viewer |

**Loading state:** Skeleton layout.
**Empty state:** N/A (expense always has amount + category + date).
**Error state:** "Could not load expense. Tap to retry."

---

### Screen: Expense List

**Purpose:** Browse and manage all expenses for the active vehicle.
**Entry points:** "Expenses" tab in bottom nav, or "See All" from dashboard.

**Layout:**
- Month selector (horizontal scroll: current month highlighted, swipe to see past months)
- Monthly total for selected month (prominent)
- List of expenses for selected month, grouped by date, sorted newest first:
  - Each row: category icon | description (subcategory or notes preview) | amount
- Filter button (filter by category)
- Sort button (by date, by amount)
- "Add Expense" FAB

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Month selector | Swipe/tap | Switches to that month's expenses |
| Expense row | Tap | Opens Expense Detail |
| Expense row | Swipe left | Reveals "Edit" and "Delete" actions |
| Filter button | Tap | Opens category filter (multi-select) |
| Sort button | Tap | Toggles sort: date (default), amount high-low, amount low-high |

**Loading state:** Skeleton list while fetching.
**Empty state:** Illustration + "No expenses this month. Tap + to log your first one." If overall no expenses: "Start tracking your car costs. Every лв counts."
**Error state:** "Could not load expenses. Pull down to retry."

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Every expense must have: amount, category, date, and vehicle_id. All other fields are optional. | Minimum valid expense: 50.00 лв, Maintenance, 2026-03-06, vehicle_id=1 |
| BR-2 | Amount must be positive and non-zero. Maximum: 99,999.99 | 0.00 = rejected. -50 = rejected. 100,000 = rejected. |
| BR-3 | Date cannot be in the future. | User selects tomorrow's date -- error: "Date cannot be in the future" |
| BR-4 | Date cannot be before the vehicle's creation date (or a reasonable minimum: 1970-01-01). | Vehicle added 2026-01-15. Expense dated 2025-12-01 is allowed (backdating for historical entry). Expense dated 1960-01-01 is rejected. |
| BR-5 | Expense tracking is unlimited in the free tier. No cap on number of expenses. | Free users can log 10,000 expenses -- no restriction |
| BR-6 | Photo attachments: max 3 per expense. Free tier: 5 photos total across all vehicles. Premium: unlimited. | Free user with 4 photos already attached to various expenses: can add 1 more. On 6th attempt: premium upsell. |
| BR-7 | Photos are compressed on upload: max 1200px wide, JPEG 80% quality. | 4MB photo becomes ~200-400KB |
| BR-8 | Deleting an expense removes it and its photos permanently. | Delete an expense with 2 photos: expense row and photo files are both deleted |
| BR-9 | If an expense includes an odometer reading, it creates an OdometerReading record and must follow the monotonically increasing rule. | Expense on March 1 at 150,000 km. New expense on March 5 at 148,000 km -- warning: "This is lower than your reading on March 1 (150,000 km). Is this correct?" Allow override with confirmation. |
| BR-10 | Tapping the Fuel category icon in Quick-Add redirects to the dedicated Fuel Entry form (separate spec). | User taps Fuel icon in Quick-Add -- Quick-Add closes, Fuel Entry form opens |
| BR-11 | Currency display follows user settings. Default: лв (BGN). All amounts are stored in the user's primary currency. No multi-currency conversion in MVP. | User set to лв: all amounts display as "847.50 лв". User set to EUR: all amounts display as "433.12 EUR". |
| BR-12 | Subcategories are optional. If no subcategory is selected, the expense is logged under the parent category only. | Expense: 200 лв, Maintenance, no subcategory -- valid |

---

### Category & Subcategory List

| Category | Icon | Subcategories |
|----------|------|---------------|
| Fuel | Gas pump | (Redirects to Fuel Entry form) |
| Maintenance | Wrench | Oil change, Brakes, Filters, Belts, Battery, Cooling system, Electrical, Bodywork, Other maintenance |
| Modifications | Gear/tuning | Performance, Exterior, Interior, Audio, Lighting, Suspension, Other mods |
| Insurance | Shield | MTPL (Гражданска), Casco, Roadside assistance, Other insurance |
| Tax | Document | Annual road tax, Registration fee, Other tax |
| Tires | Tire | Purchase, Seasonal swap, Alignment, Balancing, Repair, Other tire |
| Parking | Parking sign | Monthly parking, Daily parking, Garage rent, Other parking |
| Fines | Warning triangle | Speeding, Parking fine, Other fine |
| Car Wash | Water drops | Exterior wash, Interior cleaning, Full detail, Other wash |
| Other | Ellipsis | (No subcategories -- free text in notes) |

---

## 5. Data Requirements

**Entities involved:**

- `Expense` -- id, vehicle_id, user_id, amount, currency, category, subcategory, date, odometer_km, notes, created_at, updated_at
- `ExpensePhoto` -- id, expense_id, photo_url, sort_order, created_at
- `OdometerReading` -- (shared with Vehicle Management) id, vehicle_id, reading_km, recorded_at, source

**Data read:** Expenses for active vehicle (list, detail, dashboard aggregation). Vehicle data (for vehicle selector). User settings (currency, locale).
**Data written:** Expense (CRUD). ExpensePhoto (create/delete). OdometerReading (when odometer is logged with expense).

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| amount | decimal(10,2) | Yes | > 0, <= 99,999.99 |
| currency | enum | Yes (auto from settings) | "BGN", "EUR" |
| category | enum | Yes | One of the 10 defined categories |
| subcategory | string | No | Must match parent category's subcategory list, or null |
| date | date | Yes | <= today, >= 1970-01-01 |
| vehicle_id | FK | Yes | Must belong to current user |
| odometer_km | integer | No | > 0, <= 9,999,999, >= last recorded reading (soft warning) |
| notes | string | No | Max 500 chars |
| photos | image[] | No | Max 3 per expense. Max 10MB each before compression. JPEG/PNG/HEIC. |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No internet when saving expense | Show error toast: "No connection. Expense not saved." Preserve form data. User can retry. (No offline queue in MVP.) |
| User enters amount with wrong decimal separator | Accept both "." and "," as decimal separators. Normalize to "." for storage. |
| Photo upload fails but expense saves | Save expense without photo. Toast: "Expense saved. Photo upload failed -- you can add it later by editing." |
| User deletes a vehicle that has expenses | All expenses for that vehicle are cascade-deleted (per vehicle management spec). |
| User switches vehicle mid-entry (in Full Details) | Expense is saved under the newly selected vehicle. |
| Extremely large number of expenses (1000+) | Paginate expense list (50 per page). Month view limits scope naturally. |
| Duplicate expense (same amount, category, date) | Allow it. Users may have legitimate duplicate expenses (e.g., two parking fees in one day). |
| User edits an expense's date to a different month | Expense moves to the new month's grouping. Dashboard totals update accordingly. |
| Free user at photo limit (5 total) tries to add more | Show premium upsell: "You've used all 5 free photos. Go Premium for unlimited photos." |
| Amount entered with more than 2 decimal places | Round to 2 decimal places (standard rounding). |

---

## 7. Non-Functional Requirements

- **Performance:** Quick-Add save < 1 second (API response). Expense list for a month loads < 1.5 seconds. Photo upload happens asynchronously -- expense saves immediately, photos upload in background.
- **Offline behavior:** No offline expense creation in MVP. Cached expense list is viewable offline. Show "No connection" banner.
- **Accessibility:** Numeric keypad for amount. Category icons have text labels for screen readers. Swipe-to-delete has accessible alternative (long-press menu).
- **Localization:** Category names translated (Bulgarian/English). Currency symbol position follows locale (лв after number: "847.50 лв"; EUR before: "EUR 433.12" or "433.12 EUR"). Date format: dd.MM.yyyy.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth (user), Vehicle Management (vehicle must exist).
- **Depended on by:** Cost Dashboard (aggregates expenses), Vehicle Timeline (displays expenses chronologically), Challenges (expense count, cost/km), Share & Export (stats derived from expenses).
- **API endpoints needed:**
  - `POST /api/vehicles/{vehicleId}/expenses` -- create expense
  - `GET /api/vehicles/{vehicleId}/expenses` -- list expenses (with filters: month, category, sort, pagination)
  - `GET /api/vehicles/{vehicleId}/expenses/{id}` -- get expense detail
  - `PUT /api/vehicles/{vehicleId}/expenses/{id}` -- update expense
  - `DELETE /api/vehicles/{vehicleId}/expenses/{id}` -- delete expense
  - `POST /api/vehicles/{vehicleId}/expenses/{id}/photos` -- upload photo(s)
  - `DELETE /api/vehicles/{vehicleId}/expenses/{id}/photos/{photoId}` -- delete photo
  - `GET /api/vehicles/{vehicleId}/expenses/summary` -- monthly totals, category breakdown (used by dashboard)

---

## 9. Out of Scope

- Recurring expenses (auto-repeat monthly) -- deferred to v1.1
- Receipt OCR (scan receipt to auto-fill) -- explicitly excluded from MVP (W2)
- Bank/card integration (auto-import) -- explicitly excluded (W3)
- Offline expense queue with sync -- explicitly excluded (W1)
- Multi-currency support (logging in mixed currencies) -- deferred
- Bulk import (CSV/spreadsheet) -- deferred
- Expense splitting (shared expenses between users) -- not planned

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we support custom categories beyond the 10 pre-defined ones? | Users may have niche expenses (e.g., "Vignette", "Toll fees") | Recommend: use "Other" category with free-text notes for MVP. Add custom categories in v1.1 if requested. Consider adding "Tolls/Vignette" as a standard category if interviews show demand. |
| OQ-2 | Should the Quick-Add widget (S3) be in MVP or deferred? | Directly impacts retention (P8 tedious entry) | PRD lists as P1. Recommend: include if feasible. Platform-specific effort may be significant. |
| OQ-3 | How should we handle Bulgarian-specific expense categories? (e.g., "Годишен технически преглед" / annual vehicle inspection) | Cultural relevance for Bulgarian users | Add as a subcategory under Tax or create a dedicated "Inspection" subcategory under Maintenance. Research most common categorization. |
| OQ-4 | Should amount field support calculator-like input? (e.g., "50+35" = 85) | Convenience for users adding up multiple small expenses | Defer to v1.1. Keep Quick-Add simple for MVP. |

---

**New data entities discovered:** Expense, ExpensePhoto
**New API endpoints discovered:** 8 expense endpoints (listed in Section 8)
