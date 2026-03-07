# Functional Specification: Vehicle Management

**File:** `/03-product/functional-specs/vehicle-management.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M2, M8)
**Related features:** M2 (Vehicle Profile Management), M8 (Multi-Vehicle Support)

---

## 1. Overview

**What this feature does:** Allows users to add, view, edit, and delete vehicles. Each vehicle serves as the container for all expenses, fuel entries, timeline events, and reminders. Multi-vehicle support lets users switch between cars and manage each one independently.

**Why it matters:** The vehicle is the core data entity. Without it, nothing else works -- no expenses, no dashboard, no timeline. Multi-vehicle support addresses the reality that Bulgarian enthusiasts often own 2-3 cars (Pain: F6 manage multiple vehicles; Gain: G5 beautiful car record).

**User type:** All users.

---

## 2. User Flow

### Happy Path -- Add a New Vehicle

1. User taps "+" or "Add Vehicle" from the vehicle selector or garage screen
2. System checks free-tier limit (2 vehicles for free users)
3. If within limit: system shows Add Vehicle form
4. User fills in make, model, year, fuel type (required), plus optional fields
5. User optionally uploads a photo
6. User taps "Save"
7. System creates vehicle, sets it as active, navigates to its empty dashboard
8. Vehicle appears in the vehicle selector

### Alternative Paths

- **Path A: Free-tier limit reached** -- User has 2 vehicles and tries to add a 3rd. System shows premium upsell: "Upgrade to Premium to add unlimited vehicles." Options: "Go Premium" or "Cancel."
- **Path B: Edit existing vehicle** -- User navigates to vehicle profile, taps "Edit." Modifies fields. Taps "Save." Changes reflected immediately.
- **Path C: Delete vehicle** -- User navigates to vehicle profile, taps "Delete." System shows destructive confirmation: "Delete [Make Model]? All expenses, timeline entries, and reminders for this vehicle will be permanently deleted. This cannot be undone." User confirms. Vehicle and all associated data are deleted.
- **Path D: Switch active vehicle** -- User taps vehicle selector in the header/nav. Sees list of their vehicles. Taps a different vehicle. Dashboard, timeline, and reminders switch to selected vehicle.
- **Path E: Upload/change photo** -- User taps the vehicle photo area. Chooses camera or gallery. Photo is compressed and uploaded. Previous photo is replaced.

---

## 3. Screen Descriptions

### Screen: Garage / Vehicle List

**Purpose:** Show all user's vehicles and allow switching between them.
**Entry points:** Tap vehicle selector in header/navigation bar. Tap "My Cars" in bottom nav or side menu.

**Layout:**
- "My Cars" heading
- List of vehicle cards, each showing:
  - Vehicle photo (or placeholder silhouette if no photo)
  - Make + Model + Year
  - License plate (if entered)
  - Current odometer (if entered)
  - Badge: "Active" on the currently selected vehicle
- "Add Vehicle" button (bottom of list, or prominent CTA if no vehicles)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Vehicle card | Tap | Sets vehicle as active, navigates to its dashboard |
| Vehicle card | Long press or swipe | Shows "Edit" and "Delete" options |
| "Add Vehicle" button | Tap | Opens Add Vehicle form (or premium upsell if at limit) |

**Loading state:** Skeleton cards while fetching vehicle list.
**Empty state:** Illustration + "No cars yet. Add your first car to start tracking!" + "Add Vehicle" prominent button. (This state only appears if user skipped onboarding.)
**Error state:** "Could not load your vehicles. Pull down to retry." with retry action on pull-to-refresh.

---

### Screen: Vehicle Profile / Detail

**Purpose:** View and manage a single vehicle's details.
**Entry points:** Tap vehicle name/info area on dashboard. Tap "Edit" from vehicle list.

**Layout:**
- Vehicle photo (large, top area -- tappable to change)
- Make + Model + Year (heading)
- Detail fields (read-only view):
  - Fuel type
  - License plate
  - Current odometer + "Update" button
  - Date added
- Quick stats row:
  - Total expenses to date
  - Number of entries
  - Months tracked
- Action buttons:
  - "Edit Vehicle" (primary)
  - "Delete Vehicle" (destructive, bottom of screen)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Photo | Tap | Camera/gallery picker to update photo |
| "Update" (odometer) | Tap | Opens odometer update mini-form (number input + date) |
| "Edit Vehicle" | Tap | Opens edit form with pre-filled fields |
| "Delete Vehicle" | Tap | Shows destructive confirmation dialog |

**Loading state:** Skeleton layout while fetching vehicle details.
**Empty state:** N/A (vehicle always has at least make/model/year)
**Error state:** "Could not load vehicle details. Tap to retry."

---

### Screen: Add/Edit Vehicle Form

**Purpose:** Create a new vehicle or edit an existing one.
**Entry points:** "Add Vehicle" button, "Edit Vehicle" button, onboarding step 1.

**Layout:**
- Photo area (tap to add/change -- camera or gallery)
- Make field (searchable dropdown)
- Model field (searchable dropdown, filtered by make)
- Year field (number picker)
- Fuel type field (dropdown: Petrol, Diesel, LPG, Hybrid, Electric)
- License plate field (optional, text input)
- Current odometer field (optional, number + km unit)
- Nickname field (optional -- e.g., "My E46", shown in vehicle selector)
- "Save" primary button
- "Cancel" secondary button (or back navigation)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Make dropdown | Tap/search | Shows searchable make list |
| Model dropdown | Tap/search | Shows models for selected make |
| "Save" | Tap | Validates, saves, navigates back |
| "Cancel" / back | Tap | Discards changes, navigates back (confirm if unsaved changes) |

**Loading state:** "Save" button shows spinner.
**Empty state:** (Add mode) All fields empty with placeholders. (Edit mode) Pre-filled with current values.
**Error state:**
- Required fields missing: inline validation -- "Make, model, year, and fuel type are required"
- Network error: toast -- "Could not save. Check your connection."

---

### Component: Vehicle Selector (Header)

**Purpose:** Quick switch between vehicles without leaving the current screen.
**Entry points:** Always visible in the app header/navigation when user has 1+ vehicles.

**Layout:**
- Current vehicle name (e.g., "BMW E46 2004") with dropdown arrow
- Tap opens a dropdown/bottom sheet listing all vehicles
- Each vehicle shows: photo thumbnail + make model year
- "Manage Cars" link at bottom of dropdown

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Selector | Tap | Opens vehicle dropdown |
| Vehicle in dropdown | Tap | Switches active vehicle, refreshes current screen data |
| "Manage Cars" | Tap | Navigates to Garage / Vehicle List |

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free-tier users can have a maximum of 2 vehicles. Premium users: unlimited. | Free user with 2 vehicles taps "Add Vehicle" -- sees premium upsell screen |
| BR-2 | Make, model, year, and fuel type are required. All other fields are optional. | User can save a vehicle with just "BMW / 320d / 2004 / Diesel" |
| BR-3 | Deleting a vehicle permanently deletes ALL associated data: expenses, fuel entries, timeline entries, reminders, photos. | Delete "BMW E46" removes its 47 expenses, 12 fuel entries, 3 reminders, and all photos |
| BR-4 | Vehicle deletion requires explicit confirmation with the vehicle name visible in the confirmation dialog. | "Delete BMW 320d 2004? All data will be permanently lost." |
| BR-5 | If the active vehicle is deleted, the system auto-selects the next remaining vehicle. If no vehicles remain, show empty state. | Delete active "BMW E46" when "Audi A4" also exists -- Audi becomes active |
| BR-6 | Odometer readings must be monotonically increasing. A new reading cannot be lower than the previous one. | Previous reading: 152,000 km. User enters 148,000 -- error: "Odometer reading must be higher than the last recorded value (152,000 km)." |
| BR-7 | Odometer updates are timestamped. They contribute to cost/km calculations and mileage-based reminders. | User updates odometer to 160,000 km on March 6 -- system uses this for all calculations from this date forward |
| BR-8 | Vehicle photo is compressed on upload to max 1200px wide, JPEG quality 80%. | 5MB photo from camera is compressed to ~200-400KB before upload |
| BR-9 | Make/model list is pre-populated with the top 50+ makes and their models common in the Bulgarian market. Users can also type custom values. | "Lada" might not be in the dropdown but user can type it manually |
| BR-10 | One vehicle must always be "active" (selected) when the user has vehicles. The app always shows data for the active vehicle. | App opens -- shows dashboard for the last active vehicle |
| BR-11 | Nickname is optional but, if set, is displayed in the vehicle selector instead of make/model. | Nickname "The Beast" shows as "The Beast" in selector instead of "BMW 320d 2004" |

---

## 5. Data Requirements

**Entities involved:**

- `Vehicle` -- id, user_id, make, model, year, fuel_type, license_plate, nickname, photo_url, is_active, created_at, updated_at
- `OdometerReading` -- id, vehicle_id, reading_km, recorded_at, source (manual, expense, fuel_entry)

**Data read:** Vehicle list for current user. Active vehicle for all dashboard/timeline queries.
**Data written:** Vehicle (CRUD). OdometerReading (updates).

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| make | string | Yes | Max 50 chars |
| model | string | Yes | Max 50 chars |
| year | integer | Yes | Range: 1970 to current year |
| fuel_type | enum | Yes | Values: "petrol", "diesel", "lpg", "hybrid", "electric" |
| license_plate | string | No | Max 20 chars, alphanumeric + hyphens |
| nickname | string | No | Max 30 chars |
| odometer_km | integer | No | Min: 0, Max: 9,999,999. Must be >= last recorded reading. |
| photo | image | No | Max 10MB before compression. Formats: JPEG, PNG, HEIC |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User deletes their only vehicle | Dashboard shows empty state with CTA: "Add a car to start tracking." |
| User tries to add a 3rd vehicle on free tier | Show premium upsell. Do not show the form. |
| User downgrades from premium with 5 vehicles | Existing vehicles are NOT deleted. User keeps access to all vehicles but cannot add new ones until under the limit. |
| Photo upload fails mid-upload | Toast: "Photo upload failed. Vehicle saved without photo." Vehicle is still created. User can add photo later. |
| Two devices add a vehicle simultaneously | Both are created. Server assigns unique IDs. Both appear in the vehicle list. |
| User enters a very old year (e.g., 1971 classic car) | Allowed. Year range starts at 1970 to support classic car enthusiasts. |
| Make/model not in dropdown list | User can type a custom value. System stores whatever the user enters. |
| Network failure during vehicle delete | Toast: "Could not delete vehicle. Check your connection." Vehicle remains. |
| User enters odometer lower than previous | Inline error: "Odometer must be at least [X] km (your last recorded reading)." |

---

## 7. Non-Functional Requirements

- **Performance:** Vehicle list loads in < 1 second. Vehicle switch (data refresh) < 2 seconds.
- **Offline behavior:** Cached vehicle list is available offline. Add/edit/delete requires internet (no offline queue in MVP).
- **Accessibility:** Vehicle photos have alt text: "[Make] [Model] [Year]". All interactive elements meet 44x44pt minimum.
- **Localization:** Make/model names are not translated (brand names are universal). UI labels in Bulgarian/English.
- **Photo storage:** Compressed photos stored in cloud storage (CDN). Photo URLs served via CDN for fast loading.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth (user must be authenticated).
- **Depended on by:** Expense Tracking, Fuel Entry, Cost Dashboard, Vehicle Timeline, Maintenance Reminders, Challenges, Share & Export. Essentially every other feature.
- **API endpoints needed:**
  - `POST /api/vehicles` -- create vehicle
  - `GET /api/vehicles` -- list user's vehicles
  - `GET /api/vehicles/{id}` -- get vehicle details
  - `PUT /api/vehicles/{id}` -- update vehicle
  - `DELETE /api/vehicles/{id}` -- delete vehicle (cascade)
  - `POST /api/vehicles/{id}/photo` -- upload/replace photo
  - `POST /api/vehicles/{id}/odometer` -- record odometer reading
  - `PUT /api/vehicles/{id}/activate` -- set as active vehicle

---

## 9. Out of Scope

- VIN decoder / auto-fill from VIN -- deferred to v1.1+
- Vehicle value estimation (current market value) -- deferred
- Insurance/registration document storage on vehicle profile -- separate feature (C3)
- Vehicle comparison (side by side) -- not planned
- Vehicle sharing between users (family account) -- not planned for MVP
- Detailed vehicle specs (horsepower, weight, etc.) -- not needed for cost tracking

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we pre-populate make/model from a static list or use a third-party vehicle database API? | Affects data quality and maintenance burden | Recommend: static list for MVP. Top 50 makes with models for Bulgarian market. Allow custom entry. |
| OQ-2 | Should the "aggregate all vehicles" view be in MVP? | PRD marks it as "optional" for M8 | Recommend: defer to v1.1. Simplifies dashboard logic. |
| OQ-3 | Should vehicle deletion be soft-delete (recoverable for 30 days) or hard-delete? | Affects data storage and user trust | Recommend: soft-delete with 30-day recovery window. Show "Recently Deleted" section. |
| OQ-4 | How should we handle fuel_type "LPG" -- is it always dual-fuel (petrol + LPG)? | Affects fuel entry form and consumption calculation | Research: in Bulgaria, LPG cars run on both. May need "primary fuel" and "secondary fuel" fields. |

---

**New data entities discovered:** Vehicle, OdometerReading
**New API endpoints discovered:** 8 vehicle endpoints (listed in Section 8)
