# Functional Specification: Maintenance Reminders

**File:** `/03-product/functional-specs/maintenance-reminders.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.0 — Updated with user journey insights
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M7)
**Related features:** M7 (Maintenance Reminders)
**Related journeys:** `/03-product/user-journeys/journey-maintenance-reminder.md`, `/03-product/user-journeys/journey-first-time-experience.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Allows users to create maintenance reminders triggered by date, mileage, or both. Sends push notifications when maintenance is due. When completed, offers seamless flow to log the expense. Reminder resets for the next cycle automatically.

**Why it matters:** Car owners forget maintenance deadlines — oil changes, insurance renewals, tire rotations (Pain P5). Smart reminders build trust and stickiness: the app reliably manages their maintenance schedule, creating a "set it and forget it" relationship. Completing a reminder naturally flows into expense logging, closing the loop between planning and tracking (Journey 7 key moment). The 3-reminder free limit is a premium conversion trigger (Journey 5: 10% of conversions).

**User type:** All users. Free tier: 3 active reminders. Premium: unlimited.

---

## 2. User Flow

### Happy Path — Reminder Triggers, User Acts

1. User previously set up a reminder: "Oil change every 10,000 km"
2. User logs fuel with odometer 154,500 km. System detects: oil change due at 155,000 km (500 km away).
3. Push notification: "BMW E46: Oil change due in 500 km. Your last oil change was 9,500 km ago." (Journey 7: notification must be informative, not generic)
4. User taps notification
5. App opens to Reminder Detail screen: what's due, when, last completed date, current status
6. Additional context: "Average cost for oil change: 120-180 лв" (from user's historical data if available)
7. User takes external action (calls mechanic, plans DIY)
8. Days later: user opens app, navigates to reminder, taps "Mark as Complete"
9. Completion dialog: "When was this done?" (date picker, default today). "Log the expense?" (Yes/No)
10. User taps "Yes — Log expense"
11. Quick-add opens pre-filled: category = Maintenance, subcategory = Oil Change, date = completion date, vehicle = reminder vehicle. User only enters amount.
12. Expense saved. Timeline entry created. Dashboard updated.
13. Reminder resets: next oil change scheduled at 165,000 km (current + 10,000)
14. Peace of mind: "the app will remind me again"

### Alternative Paths

- **Path A: Date-based reminder** — Insurance renewal March 15. Notifications at 30 days, 7 days, and 1 day before.
- **Path B: Combined reminder (date + mileage)** — "Oil change every 10,000 km OR 12 months, whichever comes first." First trigger wins.
- **Path C: User snoozes** — Taps "Snooze" on reminder detail. Options: 1 week or 500 km. After 3 snoozes: "This has been snoozed 3 times. Do you still need this reminder? Reschedule or remove." (Journey 7: prevent snooze loop)
- **Path D: User ignores notification** — Follow-up reminder in 3 days. If dismissed (swiped away): no follow-up for 7 days.
- **Path E: Mark complete without logging expense** — User taps "No — just complete." Reminder marked complete, next cycle scheduled. Missed logging opportunity but not forced. (Journey 7: respect user choice)
- **Path F: Overdue reminder** — Reminder past due date or mileage. Status changes to "Overdue" with red/orange indicator. Escalating: in-app badge on Reminders tab.
- **Path G: Reminder limit reached (free tier)** — User has 3 reminders and tries to add 4th. Shows: "You've used all 3 free reminders. Upgrade to Premium for unlimited reminders." Existing reminders still work. (Journey 5)
- **Path H: Setup from templates** — First-time visitor to Reminders tab sees suggestion cards: "Oil change every 10,000 km?" "Insurance renewal date?" "Annual tax?" One-tap setup. (Journey 7: reduce setup friction)

---

## 3. Screen Descriptions

### Screen: Reminders List

**Purpose:** Overview of all maintenance reminders for the active vehicle. Peace-of-mind screen.
**Entry points:** Bottom navigation "Reminders" tab, or from vehicle profile.

**Layout:**
- Header: "Reminders" + vehicle name
- Reminders list, sorted by urgency:
  - Overdue items (red/orange badge) at top
  - Due soon (within 30 days or 1,000 km) next (yellow badge)
  - All good (green checkmark) at bottom
- Each reminder card:
  - Name (e.g., "Oil Change")
  - Status badge: Overdue / Due Soon / All Good
  - Next due: "In 500 km" or "March 15, 2026" or "In 3 weeks"
  - Last completed: "Dec 15, 2025 at 145,000 km"
- + button to add new reminder
- Free tier counter: "2/3 reminders used" (subtle, bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Reminder card | Tap | Open Reminder Detail |
| + button | Tap | Open New Reminder form (or premium prompt if at limit) |
| Status badge | Visual only | Color-coded status indicator |

**Loading state:** Skeleton cards.

**Empty state (no reminders):**
"Never forget an oil change or insurance renewal. Set up your first reminder." + Suggestion cards for common reminders (Journey 7 templates):
- "Oil change every 10,000 km" — one tap to create
- "Insurance renewal" — asks for date
- "Annual technical inspection" — asks for date
- "Tire swap (seasonal)" — asks for date

**All-clear state (reminders set, none due):**
"All caught up! Your next reminder: Oil change in 3,500 km." Green checkmark icon. Calm, peaceful state. (Journey 7: reminders list as peace-of-mind screen)

**Error state:** "Could not load reminders. Pull to refresh."

---

### Screen: Reminder Detail

**Purpose:** View full details of a reminder. Act on it (complete, snooze, edit, delete).
**Entry points:** Tap reminder card, or from push notification tap.

**Layout:**
- Reminder name and vehicle
- Status: Due Soon / Overdue / Scheduled / Completed (with appropriate color)
- Trigger type icon: calendar (date), speedometer (mileage), or both
- **Due details:**
  - Date-based: "Due: March 15, 2026 (in 8 days)"
  - Mileage-based: "Due: at 155,000 km (in ~500 km)"
  - Combined: shows both, whichever is sooner highlighted
- **Last completed:** Date and odometer when last done. "Never" if first time.
- **Historical cost:** "Average cost for this service: 150 лв" (from user's own expense history for this subcategory, if 2+ entries exist)
- **Action buttons:**
  - "Mark as Complete" (primary)
  - "Snooze" (secondary)
  - "Edit" and "Delete" (bottom, subtle)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Mark as Complete" | Tap | Opens completion dialog |
| "Snooze" | Tap | Options: "1 week" or "500 km". Snooze count tracked. |
| "Edit" | Tap | Open edit form |
| "Delete" | Tap | Confirmation: "Delete this reminder?" |

**Loading state:** Minimal — most data is cached.
**Error state:** "Could not load reminder. Pull to refresh."

---

### Screen: Completion Dialog

**Purpose:** Mark a reminder as complete and optionally log the expense.
**Entry points:** "Mark as Complete" from Reminder Detail.

**Layout:**
- "When was this done?" — date picker, default today
- "Update odometer?" — optional, number field (shows current known odometer as hint)
- "Log the expense?" — prominent Yes/No buttons
  - Yes → transitions to pre-filled quick-add
  - No → completes reminder, schedules next cycle

If "Yes — Log expense":
- Quick-add opens pre-filled: category = Maintenance (or appropriate), subcategory = reminder name, date = completion date, vehicle = reminder vehicle
- User only needs to enter amount (Journey 7: pre-filled expense on completion is seamless)

---

### Screen: New/Edit Reminder

**Purpose:** Create or edit a maintenance reminder.
**Entry points:** + button from reminders list, "Edit" from reminder detail, template suggestion tap.

**Layout:**
- **Template suggestions** (only on new reminder, collapsed after first reminder created):
  - Common maintenance items with pre-filled intervals. One-tap setup. (Journey 7 templates)
- **Name field** — text (e.g., "Oil Change", "Insurance Renewal", "Tire Rotation")
- **Trigger type toggle:** Date / Mileage / Both
- **Date trigger fields** (if Date or Both selected):
  - Next due date (date picker)
  - Repeat interval: None, Monthly, Quarterly, Semi-annually, Annually, Custom (days)
- **Mileage trigger fields** (if Mileage or Both selected):
  - Next due at (km) — or "Every X km" with interval
  - Current odometer display: "Current: 154,500 km" (from vehicle record)
- **Notification timing:**
  - Date: notify at 30 days, 7 days, 1 day before (customizable)
  - Mileage: notify at 1,000 km, 500 km before threshold
- **Notes field** (optional) — free text for additional context
- "Save" primary button

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Template card | Tap | Pre-fills name, trigger type, and interval. User confirms/adjusts. |
| Trigger type toggle | Tap | Shows/hides relevant fields |
| "Save" | Tap | Creates/updates reminder |

**Loading state:** "Save" shows spinner.
**Error state:**
- No name entered: inline — "Give your reminder a name"
- No trigger configured: inline — "Set a date or mileage trigger"
- Network error: toast

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free tier: 3 active reminders. Premium: unlimited. | 4th reminder attempt shows premium prompt. Existing 3 still work. |
| BR-2 | Reminder types: date-based, mileage-based, or combined (whichever triggers first) | Oil change: every 10,000 km OR 12 months |
| BR-3 | Date-based notifications fire at 30 days, 7 days, and 1 day before due date | Insurance renewal March 15 → notifications Feb 13, March 8, March 14 |
| BR-4 | Mileage-based notifications fire at 1,000 km and 500 km before threshold | Due at 155,000 km → notifications at 154,000 and 154,500 |
| BR-5 | Mileage-based reminders depend on odometer updates (primarily from fuel entries) | If user hasn't logged fuel with odometer recently: prompt "Update your odometer to check reminders" |
| BR-6 | After completion, reminder automatically resets for next cycle | Oil change completed at 155,200 km → next due at 165,200 km (155,200 + 10,000) |
| BR-7 | Snooze options: 1 week or 500 km | Snooze postpones the notification, not the due date. Reminder still shows "due" status. |
| BR-8 | After 3 snoozes, offer to reschedule or remove | "This has been snoozed 3 times. Reschedule or remove?" |
| BR-9 | Maximum 1 reminder notification per day | If multiple reminders are due, combine into one notification: "2 maintenance items need attention" |
| BR-10 | Respect notification quiet hours | No notifications between 22:00-08:00 (from user settings) |
| BR-11 | Overdue reminders show in-app badge on Reminders tab | Badge count = number of overdue items |
| BR-12 | Completing a reminder and logging the expense creates a timeline entry | Full chain: reminder complete → expense logged → timeline entry created |
| BR-13 | Reminder notification content must be informative | "BMW E46: Oil change due in 500 km" — NOT "You have a reminder" (Journey 7 design) |
| BR-14 | Push notifications require device permission. If denied: in-app badges only. | Prompt for notifications contextually when user creates first reminder. |
| BR-15 | Reminder source field for future multi-source support | Values: "user_created" (MVP), future: "system_suggested", "garage_created", "fleet_policy" |
| BR-16 | Odometer update from reminder completion updates the vehicle's current_odometer_km | Keeps vehicle odometer current for other reminders |

---

## 5. Data Requirements

**Entities involved:**

- `Reminder` — id, vehicle_id, user_id, name, trigger_type (enum: date, mileage, combined), next_due_date (nullable), mileage_interval_km (nullable), next_due_mileage_km (nullable), notification_days_before (array, default: [30, 7, 1]), notification_km_before (array, default: [1000, 500]), last_completed_date (nullable), last_completed_odometer_km (nullable), snooze_count (default: 0), status (enum: active, snoozed, overdue, completed), source (enum: user_created, system_suggested, garage_created, fleet_policy — default: user_created), notes (nullable), created_at, updated_at

**Data read:** Reminders for vehicle (list, detail), vehicle's current odometer (for mileage comparison)
**Data written:** Reminder (create, update, delete, complete, snooze)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| name | string | Yes | Max 100 chars |
| trigger_type | enum | Yes | Values: "date", "mileage", "combined" |
| next_due_date | date | Yes (if date/combined) | Must be in the future |
| mileage_interval_km | integer | Yes (if mileage/combined) | > 0, <= 999,999 |
| next_due_mileage_km | integer | Yes (if mileage/combined) | > current odometer |
| notes | string | No | Max 500 chars |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| Mileage-based reminder but no recent odometer data | Reminder stays in "unknown" status. Prompt: "Update your odometer (log fuel with odometer) to check if maintenance is due." |
| User never updates odometer | Mileage-based reminders can't fire. Show in-app nudge: "Your mileage reminders need an odometer update." Weekly nudge, max once per week. |
| Push notifications disabled on device | In-app badge on Reminders tab shows overdue count. Periodic in-app prompt: "Enable notifications to get reminded on time." |
| Reminder for a deleted vehicle | Cascade delete: when vehicle is deleted, all its reminders are also deleted. |
| User completes reminder but doesn't log expense | Fine — reminder resets, next cycle scheduled. Expense logging is encouraged, not forced. |
| Combined trigger: date passes but mileage not yet reached | Reminder fires on date (whichever comes first). User can snooze if mileage hasn't been reached. |
| User edits a completed reminder's interval | Next due recalculates from last completion date/mileage. |
| Reminder due date is today | Status: "Due today." Notification fires at the usual morning time. |
| Multiple reminders due on the same day | Single grouped notification: "2 items need attention for your BMW E46." |
| User has exactly 3 reminders (free limit) and deletes one | Count drops to 2/3. Can add one more. |

---

## 7. Non-Functional Requirements

- **Performance:** Reminders list loads in < 1 second. Notification scheduling is server-side and reliable.
- **Push notifications:** Server-side scheduling via APNs (iOS) + FCM (Android). Notifications must be delivered reliably — maintenance reminders are high-trust features. Tech stack TBD for notification service.
- **Offline behavior:** Reminders list is cached for offline viewing. Creating/editing reminders requires internet.
- **Accessibility:** Status badges have descriptive text (not color alone): "Overdue", "Due soon", "All clear". Reminder detail is fully accessible.
- **Localization:** Reminder names can be in any language (user input). System-generated text (status, notifications) in Bulgarian by default. Date format: dd.MM.yyyy.
- **Analytics events:** reminder_created, reminder_completed, reminder_completed_with_expense, reminder_completed_without_expense, reminder_snoozed, reminder_deleted, reminder_notification_sent, reminder_notification_tapped, reminder_limit_hit.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management (reminders belong to a vehicle), Expense Tracking (completion can log expense), Fuel Entry (odometer updates from fuel entries trigger mileage checks)
- **Depended on by:** Vehicle Timeline (completed reminders create timeline entries)
- **API endpoints needed:**
  - `GET /api/vehicles/{vehicleId}/reminders` — list reminders
  - `POST /api/vehicles/{vehicleId}/reminders` — create reminder
  - `GET /api/vehicles/{vehicleId}/reminders/{id}` — get reminder details
  - `PUT /api/vehicles/{vehicleId}/reminders/{id}` — update reminder
  - `DELETE /api/vehicles/{vehicleId}/reminders/{id}` — delete reminder
  - `POST /api/vehicles/{vehicleId}/reminders/{id}/complete` — mark as complete (with date, odometer)
  - `POST /api/vehicles/{vehicleId}/reminders/{id}/snooze` — snooze reminder
  - `GET /api/vehicles/{vehicleId}/reminders/templates` — get suggested templates

---

## 9. Out of Scope

- Predictive reminders based on driving patterns — Phase 3-4
- Garage-created reminders — Phase 2
- Fleet-wide reminder policies — Phase 3
- Integration with vehicle OBD for automatic odometer updates — future
- Calendar export (sync reminders to phone calendar) — v1.1
- Cost estimation for upcoming reminders (beyond historical average) — future
- Automatic reminder creation based on vehicle make/model service schedules — v1.1

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should the app suggest reminders based on the vehicle's make/model? | Reduces setup friction significantly | Recommend: yes for v1.1. MVP uses generic templates. Future: model-specific schedules. |
| OQ-2 | How do we handle mileage estimation for users who rarely update odometer? | Many users may not log fuel with odometer regularly | Recommend: weekly in-app nudge to update odometer. "Estimate your current mileage" option. Don't block reminders — just note that accuracy depends on odometer updates. |
| OQ-3 | Should completed reminders be archived or hidden? | Clutters the list if all historical completions shown | Recommend: show only active/upcoming reminders in the main list. "History" section at bottom shows last 3 completions per reminder. |

---

**Data entities discovered:** Reminder (with source field, snooze_count, notification arrays)
**API endpoints discovered:** 8 reminder endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: informative notification content (Journey 7), completion-to-expense seamless flow with pre-filled fields, template suggestions for first-time setup, snooze loop prevention (3-snooze limit), overdue escalation (gentle not aggressive), reminders list as peace-of-mind screen, grouped notifications (max 1/day), reminder source field for future multi-source, premium 3-reminder limit as conversion trigger (Journey 5). |
