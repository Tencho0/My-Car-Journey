# Functional Specification: Share & Export

**File:** `/03-product/functional-specs/share-export.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (S5, C2)
**Related features:** S5 (Shareable Vehicle Card), C2 (PDF Service History Export — deferred to v1.1)
**Related journeys:** `/03-product/user-journeys/journey-vehicle-timeline.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Generates a visual vehicle card — a designed image showing the car's photo, model, and key stats — that users can share to social media (Instagram, WhatsApp, Facebook) with one tap. Also defines the future PDF service history export (deferred to v1.1). The vehicle card is the organic growth engine for the product.

**Why it matters:** Car enthusiasts share car content on social media and in groups. A well-designed vehicle card shared to a BMW Club Bulgaria Facebook group generates curiosity: "What app is that?" (Gain G6: proud sharing). The card must be visually striking — automotive magazine quality, not a boring stat dump (Journey 4 insight). The vehicle card includes subtle app branding (logo + download link), turning every share into a potential acquisition event. This is the primary organic growth channel with near-zero cost.

**User type:** All users (vehicle card generation). Premium users (PDF export, deferred to v1.1).

---

## 2. User Flow

### Happy Path — Generate and Share Vehicle Card

1. User navigates to vehicle profile or timeline and taps "Share"
2. System generates a vehicle card preview: car photo, model name, key stats, subtle app branding
3. User previews the card — can see how it will look when shared
4. User taps "Share"
5. OS share sheet opens: Instagram, WhatsApp, Facebook, Save to Gallery, etc.
6. User selects destination and shares
7. Friends/group members see the card. Some are curious about the app.

### Alternative Paths

- **Path A: Share from vehicle profile** — Tap "Share Vehicle Card" on vehicle profile screen. Same flow.
- **Path B: Share from timeline** — Tap "Share" button in timeline header. Card includes timeline highlights.
- **Path C: Share from dashboard** — Tap share icon on dashboard. Card includes monthly cost stats.
- **Path D: No car photo** — Card uses vehicle type silhouette placeholder. Less engaging but still functional.
- **Path E: Save to gallery** — User taps "Save Image" instead of sharing. Card saved locally. Can share later.
- **Path F: Customize card (future)** — Choose which stats to show, pick a card template/style. Not in MVP — auto-generated only.

---

## 3. Screen Descriptions

### Screen: Vehicle Card Preview

**Purpose:** Preview the generated vehicle card before sharing.
**Entry points:** "Share" button from vehicle profile, timeline header, or dashboard.

**Layout:**
- Full-screen preview of the generated card image
- Card contents (auto-generated):
  - **Vehicle photo** (full-width hero, or silhouette if no photo)
  - **Vehicle name/model:** "BMW E46 320i" + year
  - **Nickname** if set (e.g., "The Beast")
  - **Key stats (2-4 items):**
    - Total tracked (amount or "X months tracked")
    - Expenses logged (count)
    - Top modification or recent highlight (if timeline has entries with photos)
    - Cost per km (only if premium and data available)
  - **App branding:** Small app logo + "Track your car's journey — [App Name]" + download link or QR code (subtle, bottom corner)
- Below preview:
  - "Share" primary button (opens OS share sheet)
  - "Save Image" secondary button
  - "Back" to return without sharing

**Card design requirements (from Journey 4):**
- Must be designed to social-media-sharing standard — think automotive magazine layout
- Not an app screenshot, not a boring stat table
- Clean typography, professional feel
- Car photo is the visual anchor — if no photo, the card is significantly less engaging (nudge: "Add a photo to make your card stand out")
- Stats should feel impressive, not clinical
- App branding is subtle — small logo and tagline, not a large watermark

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Share" | Tap | Opens OS share sheet (Instagram, WhatsApp, Facebook, Messages, etc.) |
| "Save Image" | Tap | Saves card image to device gallery. Toast: "Card saved to your gallery." |
| "Back" | Tap | Return to previous screen |

**Loading state:** Card generation shows brief progress indicator ("Creating your card..."). Target: < 2 seconds.
**Empty state:** If vehicle has no data at all: "Log some expenses first to create a meaningful card." Don't generate empty cards.
**Error state:** "Could not generate card. Try again." (fallback: generate with available data)

---

### Screen: Share Destination (OS Native)

This is the native OS share sheet — not a custom screen. The app generates an image file and passes it to the system share handler. The user chooses their destination.

**Supported platforms:**
- Instagram (Stories or Feed)
- Facebook (post or story)
- WhatsApp (direct message or status)
- Telegram
- Save to gallery
- Copy image
- Other apps (any app that accepts images)

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Vehicle card is a FREE feature. No premium gate. | Sharing drives organic growth — never gate the growth engine. |
| BR-2 | Card requires at least 1 expense logged for the vehicle | Don't generate empty cards with 0 stats. Prompt: "Log expenses to create your vehicle card." |
| BR-3 | Card includes app branding (logo + tagline + link) | Small, subtle. Not a watermark. "Track your car's journey — [App Name]" |
| BR-4 | Card photo uses the vehicle's profile photo | If no photo: silhouette placeholder. Nudge: "Add a photo to make your card stand out." |
| BR-5 | Stats shown on card are contextual — show what's most impressive | If user has modifications with photos → highlight those. If user has months of data → show total tracked. If premium with cost/km → show that. |
| BR-6 | Card image format: PNG or JPEG, optimized for social media | Recommended dimensions: 1080x1350 (Instagram portrait) or 1080x1080 (square). High quality. |
| BR-7 | Card generation happens client-side or server-side — decision pending, evaluate during development | If client-side: faster, works offline. If server-side: consistent quality, easier to update templates. |
| BR-8 | One card design for MVP. Multiple templates in future. | Keep it simple — one well-designed template beats three mediocre ones. |
| BR-9 | Cost per km stat only appears on card for premium users | Free users see: total tracked, expenses count, months tracked. Premium adds: cost/km. |
| BR-10 | Share event is tracked in analytics | Track: share_card_generated, share_card_shared (with destination if detectable), share_card_saved. Critical for measuring organic growth channel. |

---

## 5. Data Requirements

**Entities involved:**

No new entities. The vehicle card is generated on-the-fly from existing data:
- `Vehicle` — photo, make, model, year, nickname
- `Expense` — aggregated totals (count, sum)
- `TimelineEntry` — recent highlights (entries with photos)
- `FuelEntry` — for cost/km (premium)

**Data read:**
- Vehicle profile (photo, name, model)
- Expense summary: total amount, expense count, months tracked
- Timeline highlights: most recent photo entries (for card visual elements)
- Cost per km (premium only)

**Data written:** None — card is generated, not stored. Analytics events only.

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No vehicle photo | Card uses silhouette placeholder. Still functional but less engaging. Nudge before share: "Add a car photo for a better card." |
| Only 1 expense | Card shows: "Just started tracking — X лв tracked so far." Minimal stats but still shareable. |
| Very large total (e.g., 50,000 лв) | Display normally. Impressive numbers make better share content. |
| No internet (client-side generation) | If card generation is client-side: works offline. If server-side: "No connection. Try again when online." |
| Image share fails | Toast: "Could not share. Try saving the image and sharing manually." |
| User hasn't set a display name | Card uses "[Make] [Model] [Year]" as the vehicle name. Fine. |
| Platform-specific image requirements | Generate card at 1080x1350 for Instagram compatibility. All other platforms accept this ratio. |

---

## 7. Non-Functional Requirements

- **Performance:** Card generation in < 2 seconds. Share sheet opens immediately after generation.
- **Image quality:** High resolution (1080x1350 or 1080x1080). Text is crisp and readable at social media display sizes. Photos are not pixelated.
- **Offline behavior:** Depends on implementation (client-side vs. server-side). Client-side preferred for offline support.
- **Accessibility:** "Share" and "Save" buttons meet minimum touch targets. Card preview has text description for screen readers.
- **Localization:** Stats text on card in the user's language (Bulgarian by default). App branding tagline in Bulgarian.
- **Analytics events:** share_card_generated, share_card_preview_viewed, share_card_shared, share_card_saved, share_card_destination (if detectable).

---

## 8. Dependencies

- **Depends on:** Vehicle Management (vehicle data), Expense Tracking (stats), Vehicle Timeline (photo highlights), Cost Dashboard (cost/km for premium)
- **Depended on by:** Nothing directly — this is a growth/engagement feature.
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/share-card/data` — get all data needed to generate the card (vehicle, stats, highlights)
  - `POST /api/vehicles/{vehicleId}/share-card/generate` — generate card image (if server-side generation)

---

## 9. Out of Scope

- PDF service history export — deferred to v1.1 (PRD C2). High-value premium feature for car sellers. Will be specified separately.
- CSV data export — future feature
- Multiple card templates/styles — MVP has one template. Multiple styles in v1.1+.
- Interactive/animated cards — static image only
- Direct posting to social media (API integration) — use OS share sheet
- QR code on card linking to vehicle profile (public profiles don't exist in MVP)
- Card customization (choose stats, pick colors) — future feature
- Shared vehicle profiles (public URLs) — not in MVP

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should card generation be client-side or server-side? | Client-side: faster, offline. Server-side: consistent quality, updatable templates. | Recommend: client-side for MVP speed. Server-side if the tech stack makes it easy. |
| OQ-2 | Should the card include a QR code or download link? | Helps app discovery but adds visual clutter | Recommend: small text link (e.g., "app.name.com") bottom corner. QR code in v1.1 if needed. |
| OQ-3 | How do we track app installs from shared cards? | Critical for measuring organic growth channel | Recommend: UTM-tagged link in card. Track "source=vehicle_card" in analytics. |
| OQ-4 | Should we A/B test card designs? | Different designs may perform differently on different platforms | Recommend: single design for MVP. A/B test post-launch when share volume is sufficient. |

---

**Data entities discovered:** None new. Uses existing Vehicle, Expense, TimelineEntry, FuelEntry data.
**API endpoints discovered:** 2 share-card endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: automotive magazine quality design requirement (Journey 4), vehicle card as free feature (never gate the growth engine), contextual stats based on user's data, car photo as visual anchor with nudge if missing, subtle app branding design guidelines, platform-specific image dimensions, analytics for tracking organic growth attribution. PDF export explicitly deferred to v1.1 as separate spec. |
