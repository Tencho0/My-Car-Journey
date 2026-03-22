# Functional Specification: Vehicle Profile Management

**File:** `/03-product/functional-specs/vehicle-management.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M2, M8)
**Related features:** M2 (Vehicle Profile Management), M8 (Multi-Vehicle Support)
**Related journeys:** `/03-product/user-journeys/journey-first-time-experience.md`, `/03-product/user-journeys/journey-vehicle-timeline.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Allows users to add, view, edit, and delete vehicle profiles. Each vehicle stores identifying information (make, model, year), optional details (fuel type, license plate, odometer, photo), and serves as the container for all expenses, timeline entries, and reminders. Supports multi-vehicle switching.

**Why it matters:** The vehicle is the core data object. Every expense, timeline entry, and reminder belongs to a vehicle. Without a vehicle profile, nothing else in the app works. For enthusiasts who own 2-3 cars, multi-vehicle support is essential (Pain: F6 manage multiple vehicles; Gain: G5 beautiful car record). During onboarding, vehicle setup should feel satisfying, not bureaucratic — the user is personalizing the app, not filling out a form (Journey 1 insight).

**User type:** All users. Free tier: up to 2 vehicles. Premium: unlimited.

---

## 2. User Flow

### Happy Path — Add a New Vehicle (Post-Onboarding)

1. User taps "Add Vehicle" (from vehicle switcher, settings, or empty-state CTA)
2. System checks vehicle count against tier limit (free: 2, premium: unlimited)
3. If under limit: shows Add Vehicle screen
4. User selects make (popular Bulgarian makes at top: BMW, VW, Audi, Mercedes, Opel, Toyota)
5. User selects model (filtered by make, type-ahead search)
6. User selects year
7. User optionally adds: fuel type, photo, license plate, current odometer, nickname
8. User taps "Save"
9. System creates vehicle, navigates to vehicle profile or dashboard
10. Vehicle is now available in the vehicle switcher

### Alternative Paths

- **Path A: Onboarding vehicle creation** — Handled by onboarding-auth spec. Minimum fields: make, model, year only. All other fields deferred to vehicle profile editing.
- **Path B: Edit existing vehicle** — User navigates to vehicle profile, taps "Edit." All fields editable. Save updates the vehicle.
- **Path C: Delete vehicle** — User navigates to vehicle profile, taps "Delete." Confirmation dialog warns about cascading data loss. User confirms. Vehicle and all associated data deleted permanently.
- **Path D: Vehicle limit reached (free tier)** — User has 2 vehicles and taps "Add Vehicle." System shows: "You've reached the free limit of 2 vehicles. Upgrade to Premium for unlimited vehicles." Tappable link to premium benefits screen. This is a premium conversion trigger (Journey 5: 10% of expected conversions).
- **Path E: Switch vehicles** — User taps vehicle selector in header/nav. Bottom sheet shows all vehicles with photo thumbnail, make/model, year, nickname. Tap to switch. Dashboard, timeline, and reminders update instantly.
- **Path F: Add photo later** — User navigates to vehicle profile. Photo area shows car silhouette with "Add Photo" overlay. A car with a photo feels personal; a car without one feels like a database entry (Journey 1 insight).

---

## 3. Screen Descriptions

### Screen: Vehicle Profile

**Purpose:** Display vehicle details and serve as the hub for vehicle-specific actions.
**Entry points:** Vehicle switcher, Settings > My Vehicles, tapping vehicle name on dashboard.

**Layout:**
- Hero section: vehicle photo (full-width, or silhouette placeholder if no photo)
- Vehicle name: nickname if set, otherwise "[Make] [Model]"
- Year and fuel type badge below name
- Details section: license plate, current odometer
- Quick stats section: total expenses tracked, total amount spent (лв), months tracked, photos count
- Action buttons: "Edit," "Share Vehicle Card" (links to share-export spec), "Delete" (bottom, subtle/danger style)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Photo area | Tap | Change/add photo (camera or gallery) |
| "Edit" | Tap | Navigate to Edit Vehicle screen |
| "Share Vehicle Card" | Tap | Generate shareable vehicle card (see share-export spec) |
| "Delete" | Tap | Show confirmation dialog |
| Quick stats | Tap on any stat | Navigate to relevant screen (expenses, timeline) |

**Loading state:** Skeleton placeholder while fetching vehicle data. Photo area shows blur placeholder during image load.
**Empty state:** New vehicle with no expenses: stats show "0 expenses tracked, 0 лв spent, just started." CTA: "Log your first expense for this vehicle." Illustration of an empty road stretching ahead (Journey 4 empty state design).
**Error state:** Network error: show cached data with "offline" banner.

---

### Screen: Add/Edit Vehicle

**Purpose:** Create a new vehicle or edit an existing one.
**Entry points:** "Add Vehicle" CTAs, "Edit" from vehicle profile.

**Layout:**
- Photo area (top): tap to take photo or select from gallery. Shows current photo or car silhouette placeholder with encouraging text: "Show off your car" (Journey 1 insight: photo encouraged but not required).
- Nickname field (optional — text, max 30 chars, e.g., "My E46," "The Daily")
- Make field (scrollable picker/search) — **popular Bulgarian makes at top: BMW, VW, Audi, Mercedes, Opel, Toyota** (Journey 1: these account for 60%+ of target segment)
- Model field (filtered by selected make, with type-ahead search)
- Year field (number picker, range: 1970-current year)
- Fuel type field (dropdown: Petrol, Diesel, LPG, Hybrid, Electric, Other)
- License plate field (optional — text, Bulgarian format hint)
- Current odometer field (optional — number in km)
- "Save" primary button

**For Edit mode:** All fields pre-filled with existing data.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Photo area | Tap | Opens camera/gallery picker. Compress on upload. |
| Make picker | Tap/scroll | Scrollable list: popular BG makes first, then alphabetical. "Other" at bottom with manual text entry. |
| Model picker | Tap/scroll | Filtered by selected make. Type-ahead search. "Other" for unlisted models. |
| Year picker | Scroll | Number wheel: 1970-current year |
| "Save" | Tap | Validates required fields, saves vehicle |
| Back/Cancel | Tap | Discard changes (confirm if unsaved changes exist) |

**Loading state:** "Save" button shows spinner.
**Empty state:** All fields empty with placeholder text.
**Error state:**
- Make/model/year not filled: inline — "Make, model, and year are required"
- Network error: toast — "Could not save. Check your connection."
- Photo upload failed: toast — "Photo could not be uploaded. Vehicle saved without photo. You can add it later."

---

### Screen: Vehicle Switcher

**Purpose:** Switch between vehicles in a multi-vehicle account.
**Entry points:** Tap on vehicle name/icon in the main navigation header.

**Layout:**
- Bottom sheet overlay
- List of vehicles: each shows photo thumbnail (or silhouette), nickname or make/model, year
- Active vehicle highlighted with checkmark or accent color
- "Add Vehicle" button at bottom of list
- Tap outside or swipe down to dismiss

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Vehicle row | Tap | Switch to that vehicle. Dashboard, timeline, reminders update instantly. Sheet dismisses. |
| "Add Vehicle" | Tap | Navigate to Add Vehicle (or premium prompt if at limit) |
| Outside area / swipe | Tap/swipe | Dismiss switcher |

**Loading state:** Vehicle list loads from cache first, updates from API in background.
**Empty state:** N/A (user always has at least 1 vehicle after onboarding)
**Error state:** If vehicles can't load: show cached list with "offline" indicator.

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free tier: maximum 2 vehicles. Premium: unlimited. | User with 2 vehicles on free tier taps "Add Vehicle" — sees premium upgrade prompt |
| BR-2 | Required fields for vehicle creation: make, model, year | All other fields are optional |
| BR-3 | During onboarding: only make, model, year are shown. Other fields available in Edit. | Journey 1 mandates minimum input for speed |
| BR-4 | Vehicle deletion is permanent and cascading — deletes all expenses, timeline entries, reminders, and photos | Confirmation dialog must explicitly warn about data loss |
| BR-5 | Vehicle deletion confirmation: for vehicles with 10+ entries, require typing the vehicle name. For newer vehicles, simple "Delete" / "Cancel" buttons. | Prevents accidental deletion of rich vehicle histories |
| BR-6 | If the active vehicle is deleted, switch to the next available vehicle. If no vehicles remain, show empty state. | Delete only vehicle — land on empty dashboard with "Add your first car" illustration |
| BR-7 | Odometer readings must be non-decreasing. Warn if new reading is lower than last recorded. | Warning: "This is lower than your last reading (154,500 km). Are you sure?" Allow override for odometer reset situations. |
| BR-8 | Vehicle photo is compressed on upload | Max resolution: 1920x1080. Max file size after compression: 500KB. Format: JPEG. |
| BR-9 | Make and model list is static for MVP, covering top 50+ makes in the Bulgarian market | "Other" option for unlisted makes/models with manual text entry |
| BR-10 | Vehicle nickname is optional. Default display: "[Make] [Model]" | Nickname appears in vehicle switcher and dashboard header when set |
| BR-11 | Multi-vehicle switching is instant — show cached data first, refresh in background | No full page reload. Smooth transition. |
| BR-12 | Each vehicle's data is completely isolated | Vehicle A's expenses never appear in Vehicle B's views |
| BR-13 | Vehicle photo encouragement: show "Add Photo" prompt on profile if no photo set | A car with a photo feels personal. Nudge but don't force. (Journey 1 insight) |

---

## 5. Data Requirements

**Entities involved:**

- `Vehicle` — id, user_id, make, model, year, fuel_type (nullable), license_plate (nullable), current_odometer_km (nullable), photo_url (nullable), nickname (nullable), created_at, updated_at, is_active (boolean)

**Data read:** Vehicle list (switcher, profile), single vehicle details
**Data written:** Vehicle (create, update, delete)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| make | string | Yes | Max 50 chars. From static list or manual "Other" entry |
| model | string | Yes | Max 50 chars. Filtered by make or manual "Other" entry |
| year | integer | Yes | Range: 1970 to current year |
| fuel_type | enum | No | Values: "petrol", "diesel", "lpg", "hybrid", "electric", "other" |
| license_plate | string | No | Max 15 chars |
| current_odometer_km | integer | No | Min: 0, Max: 9,999,999 |
| photo_url | string | No | URL to compressed image in cloud storage |
| nickname | string | No | Max 30 chars |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User's car make/model not in the list | "Other" option with manual text entry for both make and model |
| Odometer entered lower than previous reading | Warning dialog. Allow override (odometer reset, engine rebuild, different dashboard). |
| Photo upload fails | Save vehicle without photo. Toast: "Photo couldn't be uploaded. You can add it later." |
| User deletes their only vehicle | Dashboard shows empty state with illustration and "Add your first car" CTA |
| User tries to add 3rd vehicle on free tier | Premium upgrade prompt: "Track all your cars with Premium." Links to premium benefits. |
| Very old car (year < 1990) | Allow it. Enthusiasts own classic cars. Year picker goes to 1970. |
| Duplicate vehicles (same make/model/year) | Allow. Users may own two of the same model. Nickname helps differentiate. |
| Vehicle with hundreds of expenses deleted | Requires typing vehicle name to confirm. Deletion is permanent. |
| No internet when adding vehicle | Toast error. Form data preserved for retry. |
| User uploads very large photo (10MB+) | Client-side compression before upload. If still too large after compression: "Photo is too large. Try a different image." |

---

## 7. Non-Functional Requirements

- **Performance:** Vehicle list loads in < 1 second. Vehicle switching is instant (cached data first). Photo compression happens client-side before upload.
- **Offline behavior:** Vehicle profiles cached locally for read access. Write operations require internet for MVP. Resolved — see DEC-007 through DEC-018.
- **Accessibility:** All form fields labeled. Photo area has text alternative for screen readers. Minimum touch targets: 44x44pt.
- **Localization:** Make/model names are universal (not translated). UI text in Bulgarian by default. License plate hint shows Bulgarian format.
- **Image handling:** Photos compressed client-side. Cloud storage with CDN for retrieval. Thumbnail versions generated for list views. Local VPS disk (MVP) with client-side compression. See DEC-013.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth (user must exist)
- **Depended on by:** Expense Tracking, Fuel Entry, Cost Dashboard, Vehicle Timeline, Maintenance Reminders, Challenges & Gamification, Share & Export
- **API endpoints needed:**
  - `GET /api/vehicles` — list user's vehicles
  - `POST /api/vehicles` — create vehicle
  - `GET /api/vehicles/{id}` — get vehicle details
  - `PUT /api/vehicles/{id}` — update vehicle
  - `DELETE /api/vehicles/{id}` — delete vehicle (cascading)
  - `PUT /api/vehicles/{id}/photo` — upload/update vehicle photo
  - `PUT /api/vehicles/{id}/activate` — set as active vehicle

---

## 9. Out of Scope

- VIN decoder / auto-fill — deferred to v1.1-v1.2
- Vehicle make/model database API (using static list for MVP)
- Vehicle valuation or market price estimation
- Document attachment to vehicle profile (deferred)
- Cross-vehicle comparison dashboard
- "Archive" vehicle (soft delete) — MVP only supports permanent deletion
- Bulk vehicle import (fleet managers, Phase 3)
- Aggregate "all vehicles" dashboard view — defer to v1.1

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we show an "aggregate all vehicles" view on the dashboard? | Useful for multi-car owners, adds complexity | Recommend: defer to v1.1. MVP shows one vehicle at a time with switcher. |
| OQ-2 | How large should the static make/model list be? | App size and onboarding experience | Recommend: top 50 makes with top 20 models each. Cover 95%+ of Bulgarian market. |
| OQ-3 | Should fuel type be asked during onboarding or deferred? | Journey 1 recommends minimum fields, but fuel type helps fuel entry | Recommend: defer to first fuel entry for this vehicle. |

---

**Data entities discovered:** Vehicle
**API endpoints discovered:** 7 vehicle endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: minimum onboarding fields (make/model/year only), photo as encouraged-not-required, popular BG makes first, vehicle deletion confirmation tiered by data volume, premium vehicle limit as conversion trigger from Journey 5, empty states with illustrations from Journey 4. |
