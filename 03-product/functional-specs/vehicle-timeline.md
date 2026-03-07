# Functional Specification: Vehicle Timeline

**File:** `/03-product/functional-specs/vehicle-timeline.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M6, S1)
**Related features:** M6 (Vehicle Timeline), S1 (Photo Attachments)

---

## 1. Overview

**What this feature does:** Displays a chronological feed of all expenses, fuel entries, services, and modifications for a vehicle -- a "car journal." Each entry shows date, category, amount, notes, and photos. The timeline is filterable by category, scrollable, and designed to feel visual and personal.

**Why it matters:** This is the key differentiator vs. all competitors. No existing app offers a rich, visual timeline of a car's life. For enthusiasts, a car is a story -- modifications, repairs, upgrades. The timeline captures that story and becomes a source of pride and a tool for resale proof (Pain: P3 no organized history, P7 no proof when selling; Gain: G5 beautiful car record, G10 prove maintenance history).

**User type:** All users.

---

## 2. User Flow

### Happy Path -- Browse Timeline

1. User taps "Timeline" tab in bottom navigation
2. System shows chronological feed for the active vehicle (newest first)
3. User scrolls through entries: each shows date, category icon, description, amount, and photo thumbnail (if any)
4. User taps an entry to see full details
5. User scrolls further back in time -- infinite scroll loads older entries

### Alternative Paths

- **Path A: Filter by category** -- User taps filter icon, selects one or more categories (e.g., "Modifications" only). Timeline shows only matching entries.
- **Path B: View entry detail** -- User taps an entry. Sees full details: date, category, subcategory, amount, notes, photos (full-size gallery), odometer. Options: Edit, Delete.
- **Path C: Add entry from timeline** -- User taps "+" FAB on timeline. Opens Quick-Add sheet (same as expense tracking).
- **Path D: View photo gallery** -- User taps a photo thumbnail. Opens full-screen photo viewer with swipe navigation between photos for that entry.
- **Path E: Timeline for different vehicle** -- User switches vehicle via selector. Timeline refreshes with the new vehicle's data.

---

## 3. Screen Descriptions

### Screen: Vehicle Timeline (Feed)

**Purpose:** Show the complete chronological history of a vehicle in a visually rich, scrollable feed.
**Entry points:** "Timeline" tab in bottom navigation. "View Timeline" from vehicle profile.

**Layout:**
- Vehicle name in header (via vehicle selector component)
- Filter button (top right) -- category filter
- Timeline feed (vertical scrollable list):
  - Date separators (group entries by date, e.g., "6 March 2026")
  - Entry cards:
    - Left: category icon (colored)
    - Center: description line 1 (subcategory or category name), description line 2 (notes preview, truncated)
    - Right: amount (bold, лв)
    - Below (if photos): horizontal row of photo thumbnails (max 3 visible, "+X more" badge if more)
    - Odometer badge (if recorded): small "152,000 km" tag
  - Entries within a date group sorted by creation time (newest first)
- "+" FAB (bottom right) -- add new entry
- "Total: X лв" floating footer showing total of currently visible/filtered entries

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Entry card | Tap | Opens entry detail screen |
| Photo thumbnail | Tap | Opens full-screen photo viewer |
| "+X more" photos badge | Tap | Opens entry detail (scrolled to photos) |
| Filter button | Tap | Opens category filter bottom sheet |
| "+" FAB | Tap | Opens Quick-Add sheet |
| Scroll to bottom | Gesture | Loads next page of older entries (infinite scroll) |
| Pull down | Gesture | Refreshes timeline |

**Loading state:** Skeleton entry cards (3-4) with shimmer animation.
**Empty state:** Illustration of an empty road + "Your car's story starts here. Log your first expense to begin the timeline." + "Add First Entry" button.
**Error state:** "Could not load timeline. Pull down to retry."

---

### Screen: Timeline Entry Detail

**Purpose:** View full details of a single timeline entry (expense or fuel entry).
**Entry points:** Tap an entry card in the timeline feed.

**Layout:**
- Category icon + category name (header, colored)
- Amount (large, prominent)
- Date (formatted: "6 March 2026" / "6 март 2026")
- Subcategory (if set)
- Odometer reading (if recorded, with "km" label)
- Notes (full text, multi-line)
- Photos section:
  - Photo grid (if 1-3 photos) or carousel (if many)
  - Tap any photo to open full-screen viewer
- Fuel-specific fields (if fuel entry): liters, price/L, consumption
- Action buttons:
  - "Edit" (opens expense or fuel edit form)
  - "Delete" (destructive, with confirmation)
- Metadata footer: "Added [date/time]" / "Last edited [date/time]"

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Photo | Tap | Opens full-screen photo viewer with swipe |
| "Edit" | Tap | Opens appropriate edit form (expense or fuel) |
| "Delete" | Tap | Confirmation: "Delete this entry? This cannot be undone." |

**Loading state:** Skeleton layout.
**Empty state:** N/A (entry always has at minimum amount + category + date).
**Error state:** "Could not load entry. Tap to retry."

---

### Component: Category Filter (Bottom Sheet)

**Purpose:** Filter timeline by one or more expense categories.
**Entry points:** Filter button on Timeline screen.

**Layout:**
- "Filter by Category" heading
- "All" toggle (default: ON -- shows everything)
- Grid of category icons with labels (same 10 categories as expense tracking)
- Each icon is a toggle (selected = highlighted, deselected = dimmed)
- "Apply" button
- "Reset" link

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "All" toggle | Tap | Selects/deselects all categories |
| Category icon | Tap | Toggles that category on/off |
| "Apply" | Tap | Closes sheet, filters timeline |
| "Reset" | Tap | Selects all, clears filter |

---

### Component: Full-Screen Photo Viewer

**Purpose:** View photos at full resolution.
**Entry points:** Tap any photo thumbnail in timeline or entry detail.

**Layout:**
- Black background, full-screen photo
- Swipe left/right to navigate between photos (within the same entry)
- Close button (X) top left
- Photo counter: "1 / 3" (top center)
- Pinch to zoom

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Swipe left/right | Gesture | Next/previous photo |
| Pinch | Gesture | Zoom in/out |
| X button | Tap | Close viewer, return to previous screen |
| Tap background | Tap | Toggle UI overlay visibility |

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Timeline shows ALL entry types: expenses, fuel entries. They are unified into a single chronological feed. | A fuel entry and a maintenance expense on the same day appear together under that date. |
| BR-2 | Entries are sorted newest first (reverse chronological). | March 6 entries appear above March 5 entries. |
| BR-3 | Date grouping uses the expense/fuel date, not the creation date. | Expense dated Feb 28 but entered on March 6 appears under Feb 28 in the timeline. |
| BR-4 | Photo attachments: free tier has a total of 5 photos across all vehicles. Premium: unlimited. | Free user with 5 photos sees "Upgrade to add more photos" when trying to add photo #6 from any entry. |
| BR-5 | Photos attached to an entry are displayed as thumbnails in the timeline card. Max 3 thumbnails visible in the card; if more, show "+X" badge. | Entry with 5 photos: shows 3 thumbnails + "+2" badge. |
| BR-6 | Deleting a timeline entry deletes the underlying expense/fuel entry and all associated photos. | Delete a timeline entry = delete from expenses too. Dashboard totals update. |
| BR-7 | Filtering by category applies to all visible entries. The "Total" footer updates to reflect filtered total. | Filter: "Modifications" only. Timeline shows 5 mod entries. Footer: "Total: 2,340 лв" |
| BR-8 | Timeline pagination: load 20 entries per page. Load next page on scroll to bottom. | User scrolls past 20 entries -- system fetches next 20. |
| BR-9 | The timeline is vehicle-specific. Switching vehicles reloads the timeline. | Switch from BMW to Audi -- timeline shows Audi's entries only. |
| BR-10 | Fuel entries in the timeline show additional info: liters, consumption (if calculated). | Fuel entry card shows: "45.2L @ 2.85 лв/L -- 7.8 L/100km" below the amount. |

---

## 5. Data Requirements

**Entities involved:**

- `Expense` -- all expenses for the vehicle (timeline entries)
- `FuelEntry` -- fuel entries (shown with extra fuel-specific fields)
- `ExpensePhoto` -- photos linked to expenses
- `Vehicle` -- active vehicle context

**Data read:** Expenses + FuelEntries for active vehicle, ordered by date desc. Photos for each entry. Vehicle details.
**Data written:** None directly (timeline is read-only; edits/deletes go through expense/fuel specs).

**API data shape (timeline entry):**

```json
{
  "id": 42,
  "type": "expense",
  "category": "maintenance",
  "subcategory": "Oil change",
  "amount": 85.00,
  "currency": "BGN",
  "date": "2026-03-06",
  "odometer_km": 152000,
  "notes": "Full synthetic 5W-30. Filter replaced too.",
  "photos": [
    { "id": 1, "url": "https://cdn.../photo1.jpg", "thumbnail_url": "https://cdn.../photo1_thumb.jpg" }
  ],
  "fuel_data": null,
  "created_at": "2026-03-06T14:30:00Z"
}
```

For fuel entries, `fuel_data` is populated:
```json
{
  "fuel_data": {
    "liters": 45.2,
    "price_per_liter": 2.85,
    "is_full_tank": true,
    "consumption_l_per_100km": 7.8,
    "station_name": "Shell Mladost"
  }
}
```

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No entries at all (new vehicle) | Empty state illustration + "Your car's story starts here" + "Add First Entry" button |
| Only 1 entry | Show the single entry. No date grouping header needed if only one date. |
| Hundreds of entries (long history) | Infinite scroll pagination. Smooth scrolling. Load indicator at bottom while fetching. |
| Photo fails to load (CDN issue) | Show broken image placeholder with retry icon. Tap to retry loading. |
| User filters to a category with no entries | Empty state: "No [category] entries yet." |
| Entry has no notes and no photos | Card shows: category icon + category/subcategory name + amount. No photo row. No notes preview. |
| Two entries on the same date with same category and amount | Both shown. Not de-duplicated. |
| No internet | Show cached timeline (last loaded page). "Offline" banner. Pull-to-refresh shows "No connection." |
| Very long notes text | Truncate in card view (2 lines max with "..."). Full text visible in detail view. |

---

## 7. Non-Functional Requirements

- **Performance:** Initial timeline load (first 20 entries) < 2 seconds. Next page load < 1.5 seconds. Photo thumbnails lazy-load (appear as user scrolls). Smooth 60fps scrolling even with photos.
- **Image optimization:** Thumbnails served at 200x200px. Full-size served at max 1200px wide. CDN with caching headers.
- **Offline behavior:** Cached entries viewable offline. Photos cached if previously viewed. No offline entry creation (per MVP constraint).
- **Accessibility:** Entry cards have descriptive labels for screen readers: "[Category]: [amount] on [date]. [notes preview]." Photo viewer supports VoiceOver with alt text.
- **Localization:** Date format: "6 март 2026" (Bulgarian) or "6 March 2026" (English). Category names translated. Currency per settings.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management, Expense Tracking (data), Fuel Entry (data).
- **Depended on by:** Share & Export (shareable vehicle card references timeline stats like "mods count").
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/timeline` -- unified timeline feed (expenses + fuel), paginated, filterable by category
  - `GET /api/vehicles/{vehicleId}/timeline/{entryId}` -- single entry detail (with photos, fuel data)

---

## 9. Out of Scope

- Adding entries with custom types (e.g., "milestone" or "achievement" entries) -- not in MVP
- Photo albums or gallery view (grouped by entry vs. all photos in a grid) -- deferred
- Comments or notes from other users on timeline entries -- not planned (no social features in MVP)
- Timeline sharing (sharing the full timeline externally) -- deferred (Share & Export covers the vehicle card)
- Search within timeline (keyword search) -- deferred to v1.1
- Timeline for "all vehicles" combined -- deferred

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the timeline support "milestone" entries (e.g., "Bought the car", "Reached 100,000 km") without an expense? | Adds emotional value to the timeline | Recommend: defer to v1.1. For MVP, every timeline entry is an expense or fuel entry. |
| OQ-2 | Should fuel entries show differently in the timeline (distinct card design) vs. regular expenses? | Visual differentiation helps users scan | Recommend: yes, fuel entries have a slightly different card layout showing liters + consumption inline. Same card structure, different info row. |
| OQ-3 | Should the timeline support "before/after" photo pairs for modifications? | High emotional value for car enthusiasts showing mods | Defer to v1.1. For MVP, photos are a flat list per entry. |
| OQ-4 | Should photos be viewable in a "car gallery" mode (all photos across all entries)? | Useful for showcasing the car visually | Defer to v1.1. MVP: photos are always in entry context. |

---

**New data entities discovered:** None (consumes Expense, FuelEntry, ExpensePhoto)
**New API endpoints discovered:** 2 timeline endpoints (listed in Section 8)
