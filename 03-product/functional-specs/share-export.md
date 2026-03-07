# Functional Specification: Share & Export

**File:** `/03-product/functional-specs/share-export.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (S5, C2)
**Related features:** S5 (Shareable Vehicle Card), C2 (PDF Service History Export -- deferred but spec'd for reference)

---

## 1. Overview

**What this feature does:** Generates a visually appealing vehicle card image with the car's photo and key stats, shareable to Instagram, WhatsApp, Facebook, and other platforms via the native share sheet. This is an organic growth driver -- every share is a mini-ad for the app.

**Why it matters:** Car enthusiasts love sharing their cars. A beautiful, data-rich vehicle card gives them something worth sharing -- combining their car photo with stats that show commitment and knowledge. Subtle app branding on the card drives discovery (Gain: G6 proud sharing; Social job: S1 show car to others).

**User type:** All users (generate card). Card includes premium stats only for premium users.

---

## 2. User Flow

### Happy Path -- Generate and Share Vehicle Card

1. User navigates to vehicle profile or taps "Share" from dashboard/timeline
2. System generates a vehicle card preview
3. Card shows: vehicle photo, make/model/year, key stats
4. User taps "Share"
5. System opens native OS share sheet (Instagram, WhatsApp, Facebook, Save to Gallery, etc.)
6. User selects a platform and shares
7. App tracks the share event for analytics

### Alternative Paths

- **Path A: Customize card before sharing** -- User can toggle which stats to show/hide on the card. Tap "Customize" to toggle: total cost (on/off), cost/km (premium only, on/off), mods count, months tracked, fuel efficiency.
- **Path B: Save to gallery without sharing** -- User taps "Save Image" instead of sharing to a platform. Card saved to device photo gallery.
- **Path C: No vehicle photo** -- Card uses a placeholder silhouette or gradient background with make/model text. Still shareable but less impactful. Prompt: "Add a photo to make your card stand out!"
- **Path D: Free user card** -- Shows: vehicle photo, make/model/year, months tracked, total entries. Does NOT show cost/km (premium). Shows app branding.
- **Path E: Premium user card** -- Shows all stats including cost/km. Premium badge/accent on card.

---

## 3. Screen Descriptions

### Screen: Vehicle Card Generator / Preview

**Purpose:** Preview and customize the shareable vehicle card before sharing.
**Entry points:** "Share" button on vehicle profile. "Share Card" from dashboard menu. "Share" from vehicle timeline header.

**Layout:**
- Card preview (center of screen, rendered at share resolution):
  - Vehicle photo (top half or background, with gradient overlay for text readability)
  - Make + Model + Year (prominent, white text over photo)
  - Nickname (if set, below make/model)
  - Stats row (bottom section of card, dark background):
    - Total cost: "12,450 лв" (label: "Total Invested")
    - Entries count: "47 entries" (label: "Tracked")
    - Months tracked: "8 months" (label: "Since")
    - [Premium] Cost/km: "0.42 лв/km" (label: "Cost/km")
    - [Premium] Avg fuel: "7.8 L/100km" (label: "Fuel Efficiency")
  - App branding: small logo + app name + tagline at bottom edge of card
- **Below card preview:**
  - "Customize" button (opens stat toggles)
  - "Share" primary button (opens native share sheet)
  - "Save Image" secondary button (saves to gallery)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Customize" | Tap | Opens bottom sheet with toggle switches for each stat |
| Stat toggle | Switch | Shows/hides that stat on the card preview (real-time update) |
| "Share" | Tap | Opens native OS share sheet with card image |
| "Save Image" | Tap | Saves card image to device gallery |
| Card preview | Pinch/zoom | No zoom -- fixed preview |

**Loading state:** Card preview shows skeleton/shimmer while stats are being calculated and image is being rendered.
**Empty state:** If vehicle has no expenses at all: card shows photo + make/model + "Just started tracking!" instead of stats. Still shareable.
**Error state:**
- Photo fails to load: use gradient background with make/model text
- Stats calculation fails: show card with photo + make/model only
- Share sheet fails: toast "Could not share. Try saving the image and sharing from gallery."

---

### Component: Card Customization (Bottom Sheet)

**Purpose:** Let user choose which stats appear on the card.
**Entry points:** "Customize" button on card preview.

**Layout:**
- "Customize Your Card" heading
- Toggle switches:
  - "Total Cost" (default: ON)
  - "Entries Tracked" (default: ON)
  - "Months Tracked" (default: ON)
  - "Cost per km" (premium only -- locked for free with "Premium" label) (default: ON if premium)
  - "Fuel Efficiency" (premium only) (default: ON if premium)
- "Done" button

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | All users can generate and share vehicle cards. No premium gate on sharing itself. | Free users share cards -- cards are organic marketing for the app. |
| BR-2 | Free user cards show: vehicle photo, make/model/year, total cost, entries count, months tracked. No cost/km or fuel efficiency (premium stats). | Free card: "BMW 320d 2004 -- 12,450 лв invested -- 47 entries -- 8 months" |
| BR-3 | Premium user cards additionally show: cost/km, fuel efficiency, and a subtle premium badge/accent. | Premium card adds: "0.42 лв/km -- 7.8 L/100km" + premium styling |
| BR-4 | App branding is always present on the card and cannot be removed. | Bottom of card: "[AppLogo] [App Name] -- Track your car's true cost" |
| BR-5 | Card image is generated at 1080x1920px (Instagram story optimal) and 1080x1080px (square, for feed posts). User can choose format. | Default: story format. Toggle: "Square" option. |
| BR-6 | The "Total Cost" stat shows lifetime total for that vehicle (all months combined), not just the current month. | Vehicle with 8 months of data: "12,450 лв" = sum of all expenses ever. |
| BR-7 | Card image is rendered client-side (no server generation needed). Uses the locally cached vehicle photo and computed stats. | Works even with brief connectivity loss if photo is cached. |
| BR-8 | Share events are tracked: share_initiated, share_completed (if platform reports), share_platform (Instagram, WhatsApp, etc.) | Analytics: "42% of shares go to Instagram, 35% to WhatsApp" |
| BR-9 | If vehicle has no photo, card uses a styled gradient background with the make/model in large text. Card is still functional and shareable. | Gradient card: "BMW 320d 2004" in large text over dark gradient + stats. |
| BR-10 | Users can toggle stats off for privacy (e.g., hide total cost but show everything else). | User shares a card with photo + make/model + months tracked, but total cost hidden. |

---

## 5. Data Requirements

**Entities involved:**

- `Vehicle` -- photo, make, model, year, nickname
- `Expense` -- aggregated for total cost and entry count
- `FuelEntry` -- aggregated for average fuel efficiency
- `OdometerReading` -- for cost/km calculation

**Data read:** Vehicle details, total expenses (lifetime), expense count, cost/km (if premium), fuel efficiency average (if premium), months since first expense.
**Data written:** Analytics events (share_initiated, share_completed, share_platform).

**No new database entities needed.** Card is generated on-the-fly from existing data.

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| Vehicle with 0 expenses | Card shows photo + make/model + "Just started tracking!" No stat numbers. |
| Vehicle with photo but no stats yet (just added) | Card shows photo + make/model + "Just started tracking!" |
| Very large total cost (e.g., 150,000 лв) | Format with thousands separator: "150 000 лв". Ensure text fits card layout. |
| Very long vehicle name (e.g., "Mercedes-Benz GLE 400d 4MATIC 2023") | Truncate or reduce font size to fit. Max 2 lines for make/model on card. |
| Share sheet not available (very old OS) | Fall back to "Save Image" only. Hide share button. |
| User takes screenshot instead of using share | Fine -- branding is on the card regardless. |
| No internet (stats cached) | Card can be generated from cached data. Share to platforms may fail if no internet -- let the OS share sheet handle that error. |
| Premium user downgrades -- previously shared cards with premium stats | Already-shared images are static -- no change. New cards won't show premium stats. |

---

## 7. Non-Functional Requirements

- **Performance:** Card generation < 1 second (client-side rendering). Share sheet opens immediately after tap.
- **Image quality:** Card rendered at 2x resolution for crisp display on high-DPI screens. File size < 500KB (JPEG 90% quality).
- **Offline behavior:** Card can be generated offline if vehicle data and photo are cached. Sharing to external platforms requires internet (handled by OS).
- **Accessibility:** Card preview has alt text for screen readers: "[Make] [Model] [Year] vehicle card with stats: total cost [X], entries [Y]."
- **Localization:** Stats labels in Bulgarian/English. Currency per settings. App branding tagline translated.

---

## 8. Dependencies

- **Depends on:** Vehicle Management (photo, make/model), Expense Tracking (total cost, entry count), Fuel Entry (efficiency), Cost Dashboard (cost/km calculation logic).
- **Depended on by:** Nothing directly. This is a leaf feature.
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/share-stats` -- aggregated stats for card (total cost, entries, months, cost/km, fuel efficiency). Single endpoint, returns everything needed for card generation.

---

## 9. Out of Scope

- PDF service history export (C2) -- deferred to v1.1. Will be a separate spec when prioritized. High-level: generate a professional PDF with vehicle details + chronological expense/service list + photos. Premium feature.
- CSV data export -- deferred
- Sharing the dashboard or timeline (not just the card) -- deferred
- Animated or video cards -- not planned
- Card templates or themes -- defer to v1.1
- Deep link from shared card back to the app (smart link for installs) -- implement in v1.1 for attribution tracking
- QR code on card linking to app download -- consider for v1.1

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the card include a QR code or download link for the app? | Drives installs from shares | Recommend: include a small "Download: [short URL]" text at the bottom with the branding. QR code in v1.1. |
| OQ-2 | Should we offer multiple card designs/templates? | Design variety increases share-worthiness | Defer to v1.1. Ship with one well-designed template for MVP. |
| OQ-3 | Should card generation happen server-side (for consistent rendering) or client-side (for offline/speed)? | Client-side is faster, server-side ensures consistency across devices | Recommend: client-side for MVP. If rendering inconsistencies arise, add server-side option later. |
| OQ-4 | What specific card dimensions should we support? 1080x1920 (story), 1080x1080 (square), both? | Instagram Stories vs. Feed have different optimal sizes | Recommend: default to story (1080x1920). Add square toggle. Most car content on Instagram is stories. |
| OQ-5 | Should the "Total Cost" stat be opt-out by default for privacy? Some users may not want to share how much they spend. | Privacy concern vs. impressive stat for sharing | Recommend: all stats ON by default. User can toggle any stat off. Most users will share proudly. |

---

**New data entities discovered:** None
**New API endpoints discovered:** 1 share-stats endpoint (listed in Section 8)
