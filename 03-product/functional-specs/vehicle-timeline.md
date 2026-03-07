# Functional Specification: Vehicle Timeline

**File:** `/03-product/functional-specs/vehicle-timeline.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M6, S1)
**Related features:** M6 (Vehicle Timeline), S1 (Photo Attachments)
**Related journeys:** `/03-product/user-journeys/journey-vehicle-timeline.md`, `/03-product/user-journeys/journey-first-time-experience.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Displays a chronological feed of everything done to a vehicle — expenses, services, modifications, and manual timeline entries — with photos, notes, and costs. Filterable by category. Functions as a visual "car journal" that documents the vehicle's complete story.

**Why it matters:** The timeline is the key differentiator vs. all competitors. No existing app does this well. It addresses Pain P3 (no organized history) and creates Gain G5 (beautiful car record) and G10 (prove history for resale). The emotional connection users feel scrolling through their car's documented journey — seeing modifications with before/after photos, services with notes, the full story of their ownership — creates a different kind of retention than cost data alone. The timeline transforms the app from accounting software into a car culture experience (Journey 4 insight: "car journal, not ledger").

**User type:** All users. Free tier: 5 photos total across all vehicles. Premium: unlimited photos.

---

## 2. User Flow

### Happy Path — Add Timeline Entry with Photos

1. User completes car work (installed new wheels, oil change, detailing)
2. Opens app, navigates to Timeline tab
3. Taps + button on timeline
4. Entry creation screen opens. Selects category (Maintenance, Modification, Other, etc.)
5. Enters description: "Installed BBS RS wheels 17-inch"
6. Enters amount: 1,200 лв
7. Taps "Add Photos" — takes or selects 1-3 photos (before/after shots)
8. Adds notes: "Purchased from BBS Bulgaria. Paid 80 лв for mounting."
9. Taps "Save"
10. Entry appears at top of timeline with photos displayed prominently
11. User scrolls through timeline — sees their car's complete journey

### Alternative Paths

- **Path A: Auto-created from expense** — Every expense logged via quick-add automatically creates a timeline entry. These entries show date, category icon, amount, and notes (if any). No photos unless added separately.
- **Path B: Timeline-only entry (no cost)** — User adds a timeline entry without an amount. Example: "Road trip to Sozopol — 450 km." Useful for documenting non-expense events. Timeline entries CAN include cost but don't have to (Journey 4: timeline vs. expense log differentiation).
- **Path C: View/edit existing entry** — User taps an entry. Full detail view: photos (full-size), all details, notes. Editable.
- **Path D: Photo limit reached** — Free user tries to add 6th photo. System shows: "You've used all 5 free photos. Upgrade to Premium for unlimited photos." This is a premium conversion trigger (Journey 5: 20% of conversions).
- **Path E: Share vehicle card** — User taps "Share" button from timeline header. Generates vehicle card (see share-export spec).
- **Path F: Filter timeline** — User applies category filter to see only Maintenance, or only Modifications, etc.

---

## 3. Screen Descriptions

### Screen: Vehicle Timeline (Feed)

**Purpose:** Show the complete chronological story of a vehicle.
**Entry points:** Bottom navigation "Timeline" tab.

**Layout:**
- Header: vehicle name/model, "Share" button (generates vehicle card), filter icon
- Scrollable feed, newest first
- Each entry card:
  - Date (prominent)
  - Category icon + category name
  - Description/title (if any, or auto-generated from expense category)
  - Amount in лв (if expense-linked)
  - Photo(s): displayed prominently — at least half the card's visual space for entries with photos (Journey 4 design: photos are first-class citizens)
  - Notes preview (1-2 lines, tap to expand)
- Monthly dividers between months: "March 2026" headers
- + button (add timeline entry) — bottom-right or in header
- Filter bar: All, Fuel, Maintenance, Modifications, Insurance, Tax, Tires, Parking, Fines, Car Wash, Other

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Entry card | Tap | Open full entry detail view |
| Photo in card | Tap | Open photo full-screen viewer |
| + button | Tap | Open new timeline entry creation |
| "Share" button | Tap | Generate and preview vehicle card for sharing |
| Filter category | Tap | Filter timeline to show only that category |
| Scroll | Swipe | Infinite scroll through history |

**Loading state:** Skeleton cards while loading. Photos show blur placeholders during image load.

**Empty state (0 entries):**
"Your car's story starts here. Log your first expense or add a timeline entry." Illustration of an empty road stretching ahead — inspiring, not empty. CTA button: "Add your first entry" (Journey 4 design: empty state feels like a beginning).

**Empty state (1-4 entries):**
Entries shown. Timeline feels sparse but intentional. "Keep documenting your car's journey."

**Empty state (5-9 entries, no photos):**
Timeline growing but text-only. Subtle prompt: "Add photos to bring your timeline to life." (Journey 4: nudge toward photo-rich entries)

**Empty state (10+ entries):**
Rich timeline with variety. Filter options become useful. The timeline is genuinely valuable for reviewing history.

**Error state:** "Could not load timeline. Pull to refresh." Show cached entries if available.

---

### Screen: Timeline Entry Detail

**Purpose:** View full details of a timeline entry.
**Entry points:** Tap any entry in the timeline feed.

**Layout:**
- Full-width photos (swipeable if multiple) at top
- Date and category badge
- Description/title
- Amount (if any), with лв formatting
- Odometer (if recorded)
- Notes (full text, not truncated)
- "Edit" button
- "Delete" link (bottom, subtle)
- Timestamps: created/edited dates

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Photo | Tap | Full-screen photo viewer with swipe navigation |
| "Edit" | Tap | Open edit form with all fields pre-filled |
| "Delete" | Tap | Confirmation: "Delete this entry?" Yes/No. If expense-linked: "This will also delete the linked expense." |

**Loading state:** Photos load progressively (blur → full resolution).
**Error state:** "Could not load entry details. Pull to refresh."

---

### Screen: New/Edit Timeline Entry

**Purpose:** Create a new manual timeline entry or edit an existing one.
**Entry points:** + button from timeline, "Edit" from entry detail.

**Layout:**
- Category picker (same icon grid as expense tracking, plus "Event" category for non-expense entries)
- Description field (text, max 200 chars) — "What did you do?"
- Amount field (optional — numeric, лв)
- Date field (default: today)
- Photos section: "Add Photos" button (camera/gallery). Shows thumbnails of selected photos (1-3). Tap thumbnail to remove.
- Notes field (multi-line text, max 1000 chars) — "Tell the story"
- Odometer field (optional, km)
- "Save" primary button

**Photo limit enforcement:**
- Free tier: 5 photos total across all vehicles and entries. Counter shown: "Photos: 3/5 used"
- When at limit: "Add Photos" button shows lock icon. Tap shows premium prompt.
- Premium: unlimited. No counter shown.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Category icon | Tap | Select category |
| "Add Photos" | Tap | If under limit: camera/gallery picker. If at limit: premium prompt. |
| Photo thumbnail | Tap X | Remove photo from entry |
| "Save" | Tap | Validate and save entry |

**Loading state:** "Save" shows spinner. Photo upload shows progress indicator per photo.
**Error state:**
- Photo upload fails: "Photo couldn't be uploaded. Entry saved without photos. You can add them later."
- Network error: toast — form data preserved for retry

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Every expense logged via quick-add automatically creates a timeline entry | Expense for "127.50 лв — Maintenance" appears in timeline with date, icon, amount |
| BR-2 | Timeline entries can exist WITHOUT an expense link (documentation-only entries) | "Road trip to Plovdiv — 500 km" with photos, no cost |
| BR-3 | Timeline entries WITH expense link: deleting the timeline entry also deletes the expense (and vice versa) | Cascading delete, with confirmation warning |
| BR-4 | Photos are limited to 5 total for free tier. Premium: unlimited. | Counter: "3/5 photos used." Each photo counts once regardless of which entry. |
| BR-5 | Maximum 3 photos per entry | Prevents excessive uploads per entry. Covers before/during/after. |
| BR-6 | Photos are compressed on upload: max 1920x1080, max 500KB JPEG | Client-side compression before upload |
| BR-7 | Timeline is sorted newest-first (reverse chronological) | Most recent entry at top. Monthly dividers for orientation. |
| BR-8 | Filter by category shows only entries of that category | "All" shows everything. Single category filter, not multi-select. |
| BR-9 | Timeline entries support a created_by field for future multi-source entries | Values: "owner" (MVP), future: "garage", "dealer", "system". MVP only uses "owner". |
| BR-10 | Entries with photos display photos prominently — more visual space than text-only entries | Photos at least half the card height. Text-only entries are compact but clean. (Journey 4: photos are first-class citizens) |
| BR-11 | Modification tracking is the timeline differentiator | Modifications with before/after photos create content enthusiasts care about and want to share. Encourage modification documentation. |
| BR-12 | Free-text search available at 20+ entries | Search by description, notes, category. Answers: "When did I last change the oil?" |

---

## 5. Data Requirements

**Entities involved:**

- `TimelineEntry` — id, vehicle_id, user_id, expense_id (nullable — links to expense if cost-related), category, description (nullable), amount (nullable), date, odometer_km (nullable), notes (nullable), created_by (enum: owner, garage, dealer, system — default: owner), created_at, updated_at
- `TimelinePhoto` — id, timeline_entry_id, photo_url, sort_order, created_at

**Data read:** Timeline entries for vehicle (paginated, filtered), photo URLs
**Data written:** TimelineEntry (create, update, delete), TimelinePhoto (create, delete)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| category | enum | Yes | Same categories as expense + "event" for non-expense entries |
| description | string | No | Max 200 chars |
| amount | decimal | No | > 0, <= 999,999.99 if provided |
| date | date | Yes (default: today) | Not in future. Not > 5 years in past. |
| notes | string | No | Max 1000 chars |
| odometer_km | integer | No | Min: 0, Max: 9,999,999 |
| photos | array | No | Max 3 per entry. Max 5 total for free tier (unlimited premium). |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User deletes expense that has a timeline entry | Timeline entry also deleted. Confirmation warns about both. |
| User deletes timeline entry that has linked expense | Expense also deleted. Confirmation warns about both. |
| Photo upload partially fails (2 of 3 photos upload) | Save entry with successful photos. Toast: "1 photo couldn't be uploaded. You can add it later via edit." |
| Free user has exactly 5 photos and deletes one | Photo count drops to 4/5. Can add one more. |
| User tries to add 4+ photos to one entry | "Maximum 3 photos per entry." Allow selecting which 3. |
| Very long timeline (500+ entries) | Pagination: load 20 entries at a time. Infinite scroll loads more. Performance must not degrade. |
| Filter returns no results | "No [category] entries found for this vehicle." |
| Search returns no results | "No entries matching '[query]'." |
| Timeline entry with amount of 0 | Allow — could be a warranty repair at no cost. |
| No internet when viewing timeline | Show cached entries. Photos that haven't been cached show placeholder. |

---

## 7. Non-Functional Requirements

- **Performance:** Timeline feed loads first 20 entries in < 1 second. Infinite scroll is seamless. Photo thumbnails load progressively. Full-size photos load on tap.
- **Offline behavior:** Cached timeline entries viewable offline. Cached photo thumbnails displayed. New entries require internet for MVP.
- **Accessibility:** Each entry card has descriptive label for screen readers: "[Date] — [Category] — [Description] — [Amount]". Photos have alt text: "Photo [N] for [description]".
- **Localization:** Category names in Bulgarian. Date format: dd.MM.yyyy. Currency: лв. Notes and descriptions in user's language.
- **Image handling:** Client-side compression before upload. Thumbnails (~200px wide) for feed view. Full-resolution for detail view. CDN for fast retrieval. Tech stack TBD for storage provider.
- **Analytics events:** timeline_viewed, timeline_entry_created, timeline_entry_with_photos, timeline_entry_without_photos, timeline_photo_added, timeline_photo_limit_hit, timeline_filtered, timeline_searched, timeline_entry_deleted.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (expenses auto-create timeline entries)
- **Depended on by:** Share & Export (vehicle card pulls timeline highlights), Maintenance Reminders (completed reminders create timeline entries)
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/timeline` — list timeline entries (paginated, filterable by category)
  - `POST /api/vehicles/{vehicleId}/timeline` — create timeline entry
  - `GET /api/vehicles/{vehicleId}/timeline/{id}` — get entry details with photos
  - `PUT /api/vehicles/{vehicleId}/timeline/{id}` — update entry
  - `DELETE /api/vehicles/{vehicleId}/timeline/{id}` — delete entry (cascade to linked expense if any)
  - `POST /api/vehicles/{vehicleId}/timeline/{id}/photos` — upload photo(s)
  - `DELETE /api/vehicles/{vehicleId}/timeline/{id}/photos/{photoId}` — delete photo
  - `GET /api/vehicles/{vehicleId}/timeline/search` — search entries by text

---

## 9. Out of Scope

- PDF export of full timeline/service history — see share-export spec (deferred to v1.1)
- Video attachments — photos only for MVP
- Comments on timeline entries (social feature) — not in MVP
- Timeline entries from garages or dealers — MVP only supports owner-created entries (created_by field is future-ready)
- Automatic entries from connected services (OBD, insurance API) — not in MVP
- Timeline analytics (e.g., "most common maintenance type") — future feature
- Reordering entries (always chronological by date)

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should expense-created timeline entries be editable from the timeline, or only from the expense edit? | Bidirectional editing adds complexity | Recommend: editing from either location updates both. Single source of truth in the expense record. Timeline is a view. |
| OQ-2 | Should the 5-photo limit count vehicle profile photos? | Unclear whether the vehicle hero photo counts toward the limit | Recommend: vehicle profile photo is separate from the timeline photo limit. 5 photos = timeline photos only. |
| OQ-3 | Should we support before/after photo pairing explicitly? | Useful for modifications (before/after wheels, paint, etc.) | Recommend: not explicitly in MVP. Users can add 2-3 photos and describe in notes. Explicit before/after feature in v1.1. |
| OQ-4 | At what entry count should search become available? | Too few entries makes search unnecessary | Recommend: show search icon always but implement text search when user has 20+ entries. |

---

**Data entities discovered:** TimelineEntry (with created_by field), TimelinePhoto
**API endpoints discovered:** 8 timeline endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: "car journal not ledger" design principle (Journey 4), photos as first-class citizens with prominent display, progressive empty states (road illustration), photo limit as premium trigger (Journey 5: 20% of conversions), modification tracking as differentiator, created_by field for future multi-source entries, search at 20+ entries, timeline-only entries without cost, entries with photos get more visual space. |
