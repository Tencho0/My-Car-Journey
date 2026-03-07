# Functional Specification: Maintenance Reminders

**File:** `/03-product/functional-specs/maintenance-reminders.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M7)
**Related features:** M7 (Maintenance Reminders)

---

## 1. Overview

**What this feature does:** Allows users to create reminders for recurring or one-time maintenance and administrative tasks, triggered by date, mileage, or both. Sends push notifications when a reminder is due or approaching. After the task is completed, the user can log the associated expense and the reminder resets (for recurring items) or marks as done.

**Why it matters:** Forgetting maintenance deadlines leads to missed oil changes, expired insurance, failed inspections, and costly consequences. This is a table-stakes feature -- competitors offer it, and users expect it. Our differentiation is tying reminders to the expense flow: complete a reminder > log the expense > see it on the dashboard (Pain: P5 forget maintenance; Gain: G4 never forget a deadline).

**User type:** All users.

---

## 2. User Flow

### Happy Path -- Create a Date-Based Reminder

1. User navigates to "Reminders" tab or section
2. User taps "Add Reminder"
3. System shows reminder creation form
4. User selects type: "Insurance Renewal"
5. User sets trigger: date = March 15, 2027
6. User sets advance notice: "30 days before"
7. User optionally adds notes: "BULSTRAD, policy #12345"
8. User taps "Save"
9. System creates reminder. On February 13, 2027, push notification: "Insurance renewal due in 30 days"
10. On March 15: second notification: "Insurance renewal is due today"
11. User taps notification > opens reminder detail > taps "Mark as Done & Log Expense"
12. Opens expense form pre-filled: category=Insurance, date=today, notes from reminder

### Happy Path -- Create a Mileage-Based Reminder

1. User taps "Add Reminder"
2. User selects type: "Oil Change"
3. User sets trigger: every 10,000 km
4. Current odometer: 150,000 km. Next due: 160,000 km.
5. User sets advance notice: "500 km before"
6. User taps "Save"
7. When odometer reaches 159,500 km (via odometer update or fuel entry): push notification: "Oil change due in 500 km"
8. At 160,000 km: "Oil change is due now"

### Alternative Paths

- **Path A: Combined trigger (date AND mileage)** -- User sets both date and mileage: "Oil change: every 10,000 km OR every 12 months, whichever comes first." Reminder fires on whichever trigger is reached first.
- **Path B: Edit reminder** -- User taps existing reminder, taps "Edit", modifies fields, saves.
- **Path C: Delete reminder** -- User taps existing reminder, taps "Delete", confirms.
- **Path D: Snooze reminder** -- Notification arrives, user taps "Snooze". Options: remind in 1 day, 3 days, 1 week.
- **Path E: Mark as done without logging expense** -- User completes the task but doesn't want to log an expense (e.g., free warranty service). Taps "Mark as Done". Recurring reminder resets.
- **Path F: Mileage reminder but no odometer updates** -- System cannot trigger mileage-based reminders. Shows persistent hint: "Update your odometer to activate mileage reminders."

---

## 3. Screen Descriptions

### Screen: Reminders List

**Purpose:** View and manage all reminders for the active vehicle.
**Entry points:** "Reminders" tab in bottom nav or app menu. Push notification deep link.

**Layout:**
- Vehicle name in header (via vehicle selector)
- Segmented control: "Upcoming" (default) | "Completed"
- **Upcoming tab:**
  - Reminders sorted by urgency: overdue first (red), then due soon (orange/yellow), then future (default)
  - Each reminder card:
    - Icon (based on type: wrench for maintenance, shield for insurance, document for tax, etc.)
    - Title (reminder type)
    - Due info: "Due March 15, 2027" or "Due in 1,200 km" or "Due March 15 OR 1,200 km"
    - Status badge: "Overdue" (red), "Due Soon" (orange), "Upcoming" (gray)
    - Notes preview (if any)
  - "Add Reminder" button (bottom of list or FAB)
- **Completed tab:**
  - Past reminders that were marked as done
  - Each card: type, completed date, linked expense (if any)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Reminder card | Tap | Opens Reminder Detail |
| Reminder card | Swipe left | Reveals "Edit" and "Delete" actions |
| "Add Reminder" | Tap | Opens Reminder Form |
| Segmented control | Tap | Switches between Upcoming and Completed |

**Loading state:** Skeleton cards.
**Empty state:** Illustration + "No reminders set. Add one to never miss an oil change or insurance renewal." + "Add Reminder" button.
**Error state:** "Could not load reminders. Pull down to retry."

---

### Screen: Reminder Form (Add/Edit)

**Purpose:** Create or edit a maintenance reminder.
**Entry points:** "Add Reminder" button. "Edit" from reminder detail.

**Layout:**
- Reminder type selector (dropdown or selection list):
  - Oil Change, Tire Rotation/Swap, Brake Service, Timing Belt, Filters, Battery, Coolant Flush, Spark Plugs, Insurance Renewal, Road Tax, Annual Inspection (ГТП), Vignette, Other (custom title)
- Custom title field (shown when "Other" is selected)
- **Trigger section:**
  - "Remind me by:" toggle group:
    - Date (calendar date picker)
    - Mileage (number input: "At [X] km" or "Every [X] km")
    - Both (date AND mileage -- whichever comes first)
  - For mileage: show current odometer and calculated "due at" km
  - For recurring mileage: "Repeat every [X] km" toggle
  - For date: "Repeat yearly" toggle
- **Advance notice:** dropdown: "1 day before", "3 days", "1 week", "2 weeks", "1 month", "500 km before", "1,000 km before"
- Notes field (optional, multi-line, max 300 chars)
- "Save" primary button
- "Cancel" / back

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Type selector | Select | Sets reminder type, auto-fills icon |
| "Other" type | Select | Shows custom title field |
| Trigger toggles | Tap | Switches between date/mileage/both |
| "Repeat yearly" / "Repeat every X km" | Toggle | Makes reminder recurring |
| "Save" | Tap | Validates, saves, navigates to reminders list |

**Loading state:** "Save" shows spinner.
**Empty state:** (Add mode) All fields at defaults. (Edit mode) Pre-filled with current values.
**Error state:**
- No type selected: inline -- "Choose a reminder type"
- No trigger set: inline -- "Set a date or mileage trigger"
- Mileage trigger lower than current odometer: inline -- "Mileage must be higher than current reading ([X] km)"
- Network error: toast

---

### Screen: Reminder Detail

**Purpose:** View a single reminder's details and take action (mark done, log expense, snooze).
**Entry points:** Tap a reminder card. Push notification deep link.

**Layout:**
- Type icon + title (header)
- Status: "Overdue since [date]" (red) or "Due in [X days / X km]" (orange/gray) or "Completed on [date]" (green)
- Trigger info: "Due: March 15, 2027" and/or "Due at: 160,000 km (currently 158,800 km)"
- Advance notice setting
- Recurring info: "Repeats yearly" or "Repeats every 10,000 km"
- Notes
- **Action buttons:**
  - "Mark as Done & Log Expense" (primary) -- opens pre-filled expense form
  - "Mark as Done" (secondary) -- completes without logging expense
  - "Snooze" -- options: 1 day, 3 days, 1 week
  - "Edit" -- opens edit form
  - "Delete" (destructive)
- If recurring and previously completed: "History" section showing past completions with dates and linked expenses

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Mark as Done & Log Expense" | Tap | Opens expense form pre-filled from reminder |
| "Mark as Done" | Tap | Marks complete. If recurring, resets to next occurrence. |
| "Snooze" | Tap | Shows snooze options, reschedules notification |
| "Edit" | Tap | Opens edit form |
| "Delete" | Tap | Confirmation, then deletes |

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Free tier: max 3 active (upcoming) reminders. Premium: unlimited. | Free user with 3 reminders taps "Add Reminder" -- premium upsell: "Upgrade for unlimited reminders." |
| BR-2 | Completed reminders don't count toward the free tier limit. | Free user completes 1 of 3 reminders -- now has 2 active, can add 1 more. |
| BR-3 | Mileage-based reminders trigger when a new odometer reading (from any source: manual update, expense, fuel entry) reaches or exceeds the trigger threshold minus advance notice. | Trigger: 160,000 km. Advance: 500 km. Odometer updated to 159,600 km -- reminder fires. |
| BR-4 | If no odometer updates occur, mileage-based reminders cannot fire. System shows a persistent hint on the reminders list: "Update your odometer to activate mileage reminders." | User has a mileage reminder but hasn't updated odometer in 3 months -- reminder stays "pending, waiting for odometer update." |
| BR-5 | Date-based reminders fire at the configured advance notice time. A second notification fires on the due date itself. | Advance: 30 days. Due: March 15. First notification: Feb 13. Second: March 15. |
| BR-6 | Combined triggers (date AND mileage) fire on whichever condition is met first. | "Oil change: 160,000 km or March 2027." If 160,000 km is reached in January, reminder fires then (before March). |
| BR-7 | Recurring date reminders reset by adding the interval to the original due date (not the completion date). | Insurance due March 15, 2027 (yearly). Completed March 20. Next due: March 15, 2028 (not March 20, 2028). |
| BR-8 | Recurring mileage reminders reset by adding the interval to the trigger mileage. | Oil change due at 160,000 km (every 10,000 km). Completed at 160,500 km. Next due: 170,000 km (not 170,500 km). |
| BR-9 | "Mark as Done & Log Expense" pre-fills the expense form: category = mapped from reminder type, date = today, notes = reminder notes. User can modify before saving. | Insurance reminder completed: opens expense with category=Insurance, notes="BULSTRAD, policy #12345". |
| BR-10 | Overdue reminders appear at the top of the list with red status and are surfaced in the dashboard as a badge: "1 overdue reminder." | Dashboard header shows a small red badge icon next to the reminders shortcut. |
| BR-11 | Push notifications require device permission. If denied, reminders still appear in-app but no push. | Show in-app prompt explaining why push is needed: "Allow notifications to never miss a maintenance deadline." |
| BR-12 | Snoozing reschedules the notification only. The due date/mileage doesn't change. | Reminder due March 15. Snoozed 3 days. New notification on March 18. Status still shows "Overdue since March 15." |
| BR-13 | Reminder type-to-expense category mapping: Oil Change/Brake Service/Timing Belt/Filters/Battery/Coolant/Spark Plugs -> Maintenance. Insurance -> Insurance. Road Tax -> Tax. Inspection (ГТП) -> Tax. Vignette -> Tax. Tire Rotation/Swap -> Tires. Other -> Other. | Auto-mapping reduces friction when logging the expense from a completed reminder. |

---

## 5. Data Requirements

**Entities involved:**

- `Reminder` -- id, vehicle_id, user_id, type, custom_title, trigger_type (date/mileage/both), due_date, due_mileage_km, repeat_interval_months, repeat_interval_km, advance_notice_days, advance_notice_km, notes, status (active/completed/snoozed), snoozed_until, completed_at, linked_expense_id, created_at, updated_at
- `ReminderHistory` -- id, reminder_id, completed_at, linked_expense_id (for recurring reminders that have been completed multiple times)

**Data read:** Reminders for active vehicle. Current odometer reading (for mileage triggers). Expense link (for completed reminders).
**Data written:** Reminder (CRUD). ReminderHistory (on recurring completion).

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| type | enum | Yes | One of the predefined types or "other" |
| custom_title | string | Only if type="other" | Max 100 chars |
| trigger_type | enum | Yes | "date", "mileage", "both" |
| due_date | date | If trigger includes date | Must be in the future (for new reminders) |
| due_mileage_km | integer | If trigger includes mileage | Must be > current odometer |
| repeat_interval_months | integer | No | 1-120 (if recurring date) |
| repeat_interval_km | integer | No | 1,000-100,000 (if recurring mileage) |
| advance_notice_days | integer | No | 0-90 (default: 7) |
| advance_notice_km | integer | No | 0-5,000 (default: 500) |
| notes | string | No | Max 300 chars |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| User creates a mileage reminder but has never set an odometer reading | Show warning: "Set your current odometer reading first so we know when to remind you." Link to vehicle profile odometer update. |
| Push notification permission denied | Show in-app reminders only. Persistent banner on reminders screen: "Enable notifications in settings for push alerts." |
| User has overdue reminders across multiple vehicles | Dashboard badge shows total overdue count across all vehicles. Reminder list shows only active vehicle's reminders. |
| Recurring reminder completed early (e.g., oil change at 155,000 km instead of 160,000 km) | Next reminder resets to 170,000 km (original interval from trigger point, not completion point). Per BR-8. |
| User deletes a vehicle with reminders | All reminders for that vehicle are cascade-deleted. |
| Two reminders for the same task (user creates duplicate) | Allowed. No dedup logic. User manages their own reminders. |
| Snooze past the next occurrence of a recurring reminder | Edge case unlikely but possible. System should not stack notifications. Snoozed reminder fires once at snooze time. |
| Free user downgrades from premium with 10 active reminders | Existing reminders are NOT deleted. User keeps them but cannot add new ones until under the limit (3). |
| No internet | Cached reminders viewable. Status changes (mark done) require internet. |

---

## 7. Non-Functional Requirements

- **Performance:** Reminders list loads < 1 second. Creating a reminder < 1 second.
- **Push notifications:** Must work reliably on both iOS (APNs) and Android (FCM). Schedule notifications server-side for date-based. Mileage-based triggers require server-side processing when odometer updates arrive.
- **Offline behavior:** Cached reminders viewable offline. Actions (create, complete, delete) require internet.
- **Accessibility:** Overdue status is communicated via text and color (not color alone). Screen reader announces: "[Type] reminder, overdue since [date]" or "[Type] reminder, due in [X] days."
- **Localization:** Reminder type names translated. Date format per locale. "ГТП" (Bulgarian annual inspection) is a locale-specific type.

---

## 8. Dependencies

- **Depends on:** Onboarding & Auth, Vehicle Management (vehicle + odometer data), Expense Tracking (for "log expense" flow after completion).
- **Depended on by:** Dashboard (overdue badge), Push notification system.
- **API endpoints needed:**
  - `POST /api/vehicles/{vehicleId}/reminders` -- create reminder
  - `GET /api/vehicles/{vehicleId}/reminders` -- list reminders (filter: active/completed)
  - `GET /api/vehicles/{vehicleId}/reminders/{id}` -- get reminder detail
  - `PUT /api/vehicles/{vehicleId}/reminders/{id}` -- update reminder
  - `DELETE /api/vehicles/{vehicleId}/reminders/{id}` -- delete reminder
  - `POST /api/vehicles/{vehicleId}/reminders/{id}/complete` -- mark as done (with optional expense_id link)
  - `POST /api/vehicles/{vehicleId}/reminders/{id}/snooze` -- snooze (body: snooze_days)
  - `GET /api/users/me/reminders/overdue-count` -- overdue count across all vehicles (for dashboard badge)

---

## 9. Out of Scope

- Automatic maintenance schedule detection from vehicle make/model/year (e.g., "BMW E46 recommends oil change every 15,000 km") -- deferred to v1.1+
- Integration with mechanic/garage booking -- not planned for MVP
- Reminders shared between users (e.g., family sharing a car) -- not planned
- Reminder templates or suggested reminders for new vehicles -- deferred to v1.1
- Location-based reminders (e.g., "remind me when near a mechanic") -- not planned
- Estimated cost on reminder (e.g., "expected cost: ~200 лв") -- deferred

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we suggest default reminders when a user adds a new vehicle? (e.g., "Add oil change reminder? Insurance reminder?") | Improves activation of reminder feature | Recommend: yes for v1.0 if effort is small. Show a one-time prompt after vehicle creation: "Want to set up common reminders?" with checkboxes for oil change, insurance, inspection. |
| OQ-2 | How frequently should we check mileage triggers? On every odometer update only, or also periodic prompts to update odometer? | Mileage reminders are useless without odometer updates | Recommend: check on every odometer update (from any source). Also: prompt user monthly if no odometer update in 30+ days: "Update your odometer to keep mileage reminders accurate." |
| OQ-3 | Should notifications include action buttons (e.g., "Log Expense" directly from notification)? | Reduces friction for marking reminders complete | Platform dependent. Recommend: include "View" and "Snooze" actions in notification. "Log Expense" requires opening the app. |
| OQ-4 | Is 3 free reminders the right limit? | Too low might frustrate; too high reduces premium conversion | Validate with interviews. A car typically needs: oil change, insurance, road tax, inspection = 4 basic reminders. Limit of 3 forces premium for full coverage. Consider 4 or 5 if feedback says 3 is too restrictive. |

---

**New data entities discovered:** Reminder, ReminderHistory
**New API endpoints discovered:** 8 reminder endpoints (listed in Section 8)
