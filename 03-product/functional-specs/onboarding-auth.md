# Functional Specification: Onboarding & Authentication

**File:** `/03-product/functional-specs/onboarding-auth.md`
**Produced by:** @product-architect
**Date:** 2026-03-07
**Version:** 2.1 — Removed Facebook OAuth; Google + Apple confirmed for MVP
**Source PRD:** `/03-product/product-requirements-document.md` — Section 6.1 (M1, M10)
**Related features:** M1 (User Authentication), M10 (Onboarding Flow)
**Related journeys:** `/03-product/user-journeys/journey-first-time-experience.md`, `/03-product/user-journeys/journey-premium-upgrade.md`

---

## 1. Overview

**What this feature does:** Enables users to create an account, log in, reset their password, and be guided through a first-launch onboarding flow that gets them to the "aha moment" (seeing a cost on the dashboard) in under 3 minutes.

**Why it matters:** Authentication is the foundation — no account means no data sync, no premium, no cross-device access. The onboarding flow is the single most important funnel for activation. A user who doesn't complete this journey in their first session has a <5% chance of ever returning (Journey 1 insight). The goal: account created in under 10 seconds, entire onboarding under 3 minutes, 60%+ completion rate.

**User type:** All users (new and returning). MVP role: driver. Auth system must include a role/type field from day one (driver, garage_owner, dealer, fleet_manager) to support future role-based onboarding flows, even though MVP only serves the driver role.

---

## 2. User Flow

### Happy Path — New User (First Launch)

Target: Under 3 minutes total. Optimal: under 90 seconds with OAuth.

1. User opens the app for the first time
2. System shows Welcome screen with a single, clear value proposition: "Finally know what your car costs." — no feature lists, no multi-slide carousel
3. User taps "Get Started"
4. System shows Auth screen — OAuth buttons (Google, Apple) prominent and primary; email registration secondary
5a. User taps OAuth (Google/Apple) — single tap, auto-fills name and email (~10 seconds)
5b. User taps "Sign up with email" — minimal form: email, password, name (~30 seconds)
6. System creates account, logs user in. Brief success animation (0.5s). Immediately transitions to "Add Your Car"
7. System shows "Add Your Car" screen with make/model/year pickers. Popular Bulgarian makes at top: BMW, VW, Audi, Mercedes, Opel, Toyota (these account for 60%+ of the target segment)
8. User selects make from scrollable list, then model (filtered by make), then year
9. Vehicle profile preview appears with car silhouette matching the type
10. User optionally taps "Add Photo" (camera/gallery picker) — encouraged but skippable via "Skip" or "Continue"
11. User taps "Save" or "Continue"
12. System creates vehicle, transitions to guided expense entry with message: "Great! Now log your first expense so we can start calculating."
13. System shows pre-filled expense form: today's date, their car selected, category picker prominent, numeric keypad ready
14. User taps category icon, types amount
15. User taps "Save"
16. Expense saved with satisfying micro-animation (checkmark + brief haptic)
17. Screen transitions to dashboard: "Your [Car Model] has cost you **X лв** so far. Keep tracking to see your monthly total."
18. Onboarding complete — user is in the main app

**Critical design moment (Step 17):** The dashboard reveal is the single most important screen transition in the entire product. The design must make this feel impactful — big, clear typography for the amount, the car name/model above it, a sense of "this is the beginning of understanding your car." Not a data table. Not a spreadsheet. A clear, emotional number.

### Alternative Paths

- **Path A: OAuth registration** — User taps Google/Apple on step 4. OAuth provider handles authentication. On success, user is logged in and proceeds to step 7. Email is pre-filled from OAuth profile. No password needed. Target: 70%+ of registrations via OAuth.
- **Path B: User skips vehicle setup** — "Skip" link available on step 7. If skipped, user lands on an empty dashboard with prominent CTA: "Add your first car to get started." Illustration of a car, not blank space. Skipping is tracked in analytics.
- **Path C: User skips first expense** — "Skip" link on step 13. User lands on dashboard with vehicle but no expenses. Shows: "Your car's story starts here. Log your first expense." with CTA button.
- **Path D: User already has account** — Taps "Log In" on Welcome screen. Enters email + password (or OAuth). Lands on dashboard (or empty state if no vehicles).
- **Path E: Password reset** — From Login screen, taps "Forgot password?" Enters email. System sends reset link. User clicks link, enters new password. Redirected to login.
- **Path F: Email verification** — After registration, user receives a verification email. App is fully functional without verification. No verification required to start using the app (Journey 1 insight: no friction during onboarding). If unverified after 7 days, show a more prominent reminder.

### Flow Diagram

```
[App Launch]
    |
    v
[Welcome Screen -- single screen, no carousel]
    |                              |
    "Get Started"              "Log In"
    |                              |
    v                              v
[Auth Screen]                 [Login Screen] --> [Dashboard]
    |  (OAuth primary,             |
    |   email secondary)      "Forgot Password?" --> [Reset Flow]
    |
    v
[Add Your Car -- make/model/year only]
    |                              |
    "Continue"                 "Skip" --> [Empty Dashboard + CTA]
    |
    v
[Log First Expense -- guided quick-add]
    |                              |
    "Save"                     "Skip" --> [Dashboard, vehicle, no expenses]
    |
    v
[Dashboard Reveal -- THE AHA MOMENT]
    "Your BMW E46 has cost you 127.50 лв so far"
```

---

## 3. Screen Descriptions

### Screen: Welcome (First Launch Only)

**Purpose:** Communicate the app's value in 5 seconds. Drive user to register immediately.
**Entry points:** First app launch only (never shown again after registration).

**Layout:**
- Full-screen car illustration (brand-aligned, aspirational)
- Single value proposition headline: "Finally know what your car costs." (Journey 1 insight: one sentence max, lead with the emotional hook)
- No feature lists. No multi-slide carousel. No progress dots. One screen, one message.
- "Get Started" primary button (large, prominent, bottom)
- "Already have an account? Log In" secondary link (bottom, smaller)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Get Started" | Tap | Navigate to Auth screen |
| "Log In" | Tap | Navigate to Login screen |

**Loading state:** N/A (static content)
**Empty state:** N/A
**Error state:** N/A

**Design note (from Journey 1):** The welcome screen must be a single screen, not a carousel. Multi-slide onboarding adds friction and delays the user. The value proposition must be clear in one glance. Every second here costs users.

---

### Screen: Auth (Registration)

**Purpose:** Create a new account with minimum friction.
**Entry points:** "Get Started" from Welcome screen.

**Layout:**
- App logo (top, small)
- "Create Your Account" heading
- OAuth buttons row (Google, Apple on iOS) — **prominent, primary position, above the fold**
- Divider: "or"
- "Sign up with email" secondary link (not a full form by default — tapping expands the email form)
- When email form expands: email field, password field (with show/hide toggle), name field
- "Create Account" button
- "Already have an account? Log In" link (bottom)
- Terms of service + privacy policy links (small text, bottom)

**Key design decision (from Journey 1):** OAuth must be the primary, most visible option. Email registration is secondary. The goal: account created in under 10 seconds. If this takes longer than 30 seconds, expect 30-40% drop-off.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| OAuth button (Google) | Tap | Opens Google OAuth flow — single tap, auto-fills name and email |
| OAuth button (Apple) | Tap | Opens Apple Sign-In flow (iOS only) |
| "Sign up with email" | Tap | Expands email/password/name form fields |
| Email field | Type | Validates format on blur |
| Password field | Type | Shows strength indicator (weak/medium/strong) |
| Show/hide toggle | Tap | Toggles password visibility |
| "Create Account" | Tap | Validates inputs, creates account, proceeds to onboarding |
| "Log In" | Tap | Navigate to Login screen |
| ToS / Privacy link | Tap | Opens in-app browser |

**Loading state:** OAuth buttons show spinner during flow. "Create Account" button shows spinner during API call.
**Empty state:** N/A (form with empty fields)
**Error state:**
- Invalid email format: inline error below field — "Enter a valid email address"
- Weak password: inline error — "Password must be at least 8 characters with 1 number"
- Email already registered: inline error — "This email is already registered. Log in instead?" with tappable link
- Network error: toast notification — "Connection failed. Check your internet and try again."
- OAuth failure: toast — "Could not connect to [provider]. Try another method."
- OAuth returns insufficient permissions (no email): "We need your email to create an account. Please grant email permission."

---

### Screen: Login

**Purpose:** Authenticate an existing user.
**Entry points:** "Log In" from Welcome screen, "Log In" from Auth screen, deep link from password reset email.

**Layout:**
- App logo (top)
- "Welcome Back" heading
- Email field (pre-filled if coming from failed registration)
- Password field (with show/hide toggle)
- "Forgot password?" link (right-aligned below password)
- "Log In" primary button
- Divider: "or continue with"
- OAuth buttons: Google, Apple (iOS only)
- "Don't have an account? Sign Up" link (bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Log In" | Tap | Validates, authenticates, navigates to Dashboard |
| "Forgot password?" | Tap | Navigate to Password Reset screen |
| OAuth button | Tap | Opens OAuth flow, navigates to Dashboard on success |
| "Sign Up" | Tap | Navigate to Auth screen |

**Loading state:** "Log In" button shows spinner.
**Empty state:** N/A
**Error state:**
- Wrong credentials: inline error — "Incorrect email or password. Try again."
- Account locked (5+ failed attempts): "Account temporarily locked. Try again in 15 minutes or reset your password."
- Network error: toast — "Connection failed. Check your internet and try again."

---

### Screen: Password Reset

**Purpose:** Allow users to reset a forgotten password.
**Entry points:** "Forgot password?" from Login screen.

**Layout:**
- Back arrow (top left)
- "Reset Password" heading
- "Enter your email and we'll send you a reset link" subtitle
- Email field
- "Send Reset Link" primary button

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Send Reset Link" | Tap | Sends email, shows confirmation message |
| Back arrow | Tap | Return to Login screen |

**Loading state:** Button shows spinner during API call.
**Empty state:** N/A
**Error state:**
- Email not found: Show the same success message as valid email (security: don't reveal which emails exist)
- Network error: toast

**Post-action:** Shows confirmation: "Check your email. We sent a reset link to [email]." With "Back to Login" button.

---

### Screen: Onboarding — Add Your Car

**Purpose:** Guide new user to add their first vehicle immediately after registration. This should feel satisfying, not bureaucratic (Journey 1 insight).
**Entry points:** Automatically shown after successful registration (brief 0.5s success animation between).

**Layout:**
- "Add Your Car" heading
- "Let's see what it costs you" subtitle (reinforces the value proposition)
- Make field (scrollable picker/search) — **Popular Bulgarian makes at top: BMW, VW, Audi, Mercedes, Opel, Toyota** (Journey 1: these account for 60%+ of the target segment)
- Model field (filtered by selected make, with type-ahead search)
- Year field (number picker, range: 1970-current year)
- Photo area (optional) — "Add Photo" button with camera icon, encouraging text: "Show off your car"
- "Continue" primary button
- "Skip for now" link (bottom, small)

**Minimum required fields: make, model, year. That's it.**

**NOT shown during onboarding (deferred to vehicle profile editing):**
- Fuel type (infer from model or ask later)
- License plate (optional, always)
- Current odometer (ask later when they log fuel)

**Key design decision (from Journey 1):** Minimum viable fields reduce drop-off. Every additional field during onboarding is friction. Fuel type, license plate, and odometer are available in vehicle profile editing but NOT required during first setup.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Make picker | Tap/scroll | Shows scrollable list with popular BG makes at top, then alphabetical. Type-ahead search available. |
| Model picker | Tap/scroll | Shows models filtered by selected make. Type-ahead search. |
| Year picker | Scroll | Number wheel, default to common years (2005-2020 range visible) |
| Photo area | Tap | Opens camera/gallery picker. Photo is optional but encouraged. |
| "Continue" | Tap | Validates make+model+year filled, creates vehicle, proceeds to Log First Expense |
| "Skip for now" | Tap | Skips to empty dashboard with CTA |

**Loading state:** "Continue" shows spinner while creating vehicle.
**Empty state:** Pickers show placeholder text. Car silhouette placeholder in photo area.
**Error state:**
- Make/model/year not filled: inline validation — "Select your car's make, model, and year to continue"
- Network error: toast — "Could not save your vehicle. Check your connection."

---

### Screen: Onboarding — Log First Expense

**Purpose:** Guide user to log their first expense immediately after adding a vehicle.
**Entry points:** Automatically shown after saving first vehicle in onboarding.

**Layout:**
- "Log Your First Expense" heading
- "What did you last spend on your [Make Model]?" subtitle (Journey 1 insight: suggest a common expense)
- Suggestion prompts below subtitle: "Fuel? A car wash? Parking?" (tappable — pre-select the category)
- Category selector (visual grid of icons with small labels: Fuel, Maintenance, Mods, Insurance, Tax, Tires, Parking, Fines, Car Wash, Other)
- Amount field (numeric keypad opens automatically when category is selected — Journey 2 insight)
- Date field (pre-filled: today — 95% of entries are same-day)
- "Save & See Your Dashboard" primary button
- "Skip for now" link (bottom, small)

**Key design decision (from Journey 1):** Suggest a common expense to reduce the "I don't know what to enter" hesitation. Pre-fill everything possible: date = today, vehicle = the one just created, currency = лв. The user only needs to select a category and type an amount.

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Suggestion prompt ("Fuel?") | Tap | Pre-selects the Fuel category icon |
| Category icon | Tap | Selects category (highlighted state), numeric keypad appears |
| Amount field | Type | Amount formats in лв as typed (e.g., "127.50 лв") |
| Date field | Tap | Opens date picker (default: today) |
| "Save & See Your Dashboard" | Tap | Saves expense, transitions to dashboard reveal |
| "Skip for now" | Tap | Navigates to dashboard (vehicle added, no expenses) |

**Loading state:** Button shows spinner while saving.
**Empty state:** No category selected, amount empty. Suggestion prompts visible.
**Error state:**
- No amount entered: inline — "Enter an amount"
- No category selected: inline — "Choose a category"
- Network error: toast

---

### Screen: Dashboard Reveal (Post-Onboarding)

**Purpose:** The "aha moment" — show the user their first cost on the dashboard.
**Entry points:** Saving first expense during onboarding.

This is NOT a separate screen — it's the regular Dashboard in its "1 expense" state, but the transition TO it during onboarding is critical.

**Transition design (from Journey 1):**
- After expense saves, brief success animation (checkmark + haptic)
- Screen transitions to dashboard
- The amount appears prominently with a gentle animation (number counting up to the total)
- Message: "Your [Car Model] has cost you **X лв** so far. Keep tracking to see your monthly total."
- Single category shown in breakdown (100% of one category)
- Below: encouraging prompts: "Add a few more expenses to see the bigger picture" and "Log more to unlock your monthly total"

**What NOT to show during the reveal:**
- Empty charts with zero data
- Premium features or pricing
- Challenges or gamification
- Notification permission prompts (delay until AFTER the aha moment — Journey 1 insight)

**Post-reveal flow:**
- After ~3 seconds on dashboard, schedule a push notification for next day: "Did you fuel up today? Quick-add it in 10 seconds."
- If user explores further (great!), let them naturally discover timeline, reminders, etc.
- If user closes app (fine!), the next-day notification brings them back.

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Email addresses must be unique across all accounts (case-insensitive) | "User@Email.com" and "user@email.com" are the same account |
| BR-2 | Passwords must be at least 8 characters with at least 1 number | "password" = rejected; "password1" = accepted |
| BR-3 | OAuth accounts are linked by email. If a user registers with email+password and later tries OAuth with the same email, accounts are merged. | User registers as john@gmail.com, later taps "Sign in with Google" using the same email — same account, both login methods now work |
| BR-4 | Account lockout after 5 consecutive failed login attempts. Lockout duration: 15 minutes. | After 5 wrong passwords, show lockout message with countdown |
| BR-5 | Password reset tokens expire after 1 hour | User clicks reset link after 2 hours — "This link has expired. Request a new one." |
| BR-6 | Sessions persist for 30 days. After 30 days of inactivity, user must re-authenticate. | User opens app after 45 days — redirected to Login screen |
| BR-7 | Welcome screen is shown only on first launch (before any account exists on device) | Returning user who reinstalls: sees Welcome screen (no local state). Existing user who logs out: sees Login screen, not Welcome. |
| BR-8 | Onboarding "Skip" is allowed but tracked. Users who skip have lower activation — show contextual CTAs later. | Skipped "Add Your Car" — dashboard shows "Add your first car" CTA with illustration |
| BR-9 | Onboarding is shown only once per account. If user logs out and back in, they go straight to the dashboard. | Completed onboarding, logged out, logged in again — lands on dashboard |
| BR-10 | Free tier allows up to 2 vehicles. Onboarding creates the first one — no paywall friction. | First vehicle is always free, no premium check needed |
| BR-11 | Email verification is encouraged but not required for app functionality. | Unverified users can use all features. After 7 days, show persistent banner. |
| BR-12 | User must accept Terms of Service and Privacy Policy to create an account. | Registration requires implicit consent (creating account = accepting ToS). Link must be visible. |
| BR-13 | Notification permission prompt is delayed until AFTER the dashboard reveal. | Never ask for notifications during onboarding registration or vehicle setup (Journey 1 insight). Frame as: "Get reminders when maintenance is due." |
| BR-14 | Onboarding minimum required fields for vehicle: make, model, year only. | Fuel type, license plate, odometer, and photo are all optional during onboarding — available in vehicle profile editing later. |
| BR-15 | User account includes a role field defaulting to "driver" for MVP. | role = "driver". Future values: "garage_owner", "dealer", "fleet_manager". Onboarding flow routing will be role-based when additional roles are supported. |
| BR-16 | OAuth should be the primary registration method. Email registration is a fallback. | OAuth buttons are visually prominent and positioned above email form. Target: 70%+ of registrations via OAuth (Journey 1 insight). |

---

## 5. Data Requirements

**Entities involved:**

- `User` — id, email, password_hash, display_name, avatar_url, email_verified, role (enum: driver, garage_owner, dealer, fleet_manager — default: driver), created_at, updated_at, last_login_at, auth_provider (email, google, apple), locale, currency_preference, distance_unit, onboarding_completed (boolean), onboarding_skipped_at_step (nullable string)
- `AuthToken` — id, user_id, token, refresh_token, expires_at, created_at
- `PasswordResetToken` — id, user_id, token, expires_at, used
- `LoginAttempt` — id, user_id, ip_address, success, created_at (for lockout tracking)

**Data read:** User table (login, session validation)
**Data written:** User (registration, onboarding status), AuthToken (login/refresh), PasswordResetToken (reset flow), LoginAttempt (each attempt)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| email | string | Yes | Valid email format (RFC 5322), max 254 chars, unique (case-insensitive) |
| password | string | Yes (unless OAuth) | Min 8 chars, at least 1 number, max 128 chars |
| display_name | string | No | Max 50 chars, no special characters except spaces and hyphens |
| role | enum | Yes (default: driver) | Values: "driver", "garage_owner", "dealer", "fleet_manager" |
| locale | enum | Yes (default: bg) | Values: "bg", "en" |
| currency_preference | enum | Yes (default: BGN) | Values: "BGN", "EUR" |
| distance_unit | enum | Yes (default: km) | Values: "km" |
| onboarding_completed | boolean | Yes (default: false) | Set to true when user completes or skips onboarding |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No internet during registration | Show error toast: "No internet connection. Please check and try again." Form data preserved. |
| No internet during OAuth | Show error toast. Fall back to email+password option. |
| User closes app mid-onboarding (after registration, before adding vehicle) | On next launch, user is logged in. Show "Add Your Car" CTA on empty dashboard with illustration. Do not replay welcome screen. |
| User closes app mid-onboarding (after vehicle, before expense) | On next launch, show dashboard with vehicle but no expenses. Show expense CTA. |
| OAuth provider returns insufficient permissions (no email) | Show error: "We need your email to create an account. Please grant email permission." |
| User tries to register with a disposable email | Allow it. No domain blacklisting for MVP. |
| Two devices logged in simultaneously | Both sessions are valid. Data syncs via API. Last-write-wins for profile changes. |
| Password reset requested for non-existent email | Show same success message as valid email (security). No email sent. |
| User taps reset link on a different device | Reset works — token is server-side. After reset, redirect to login on that device. |
| Extremely long email or name input | Enforce max length. Truncate display if needed. |
| App killed during onboarding vehicle save | Retry on next launch. If vehicle was partially created server-side, detect and resume. |
| User's car make/model not in the list | "Other" option at the bottom of make list. Manual text entry for make and model. |
| Dashboard shows tiny amount after first expense | Message matters: "This is just the start. Most owners are shocked when they see the monthly total." Encourage adding 2-3 more. (Journey 1 insight) |
| User denies notification permission when prompted post-dashboard | Accept gracefully. No re-prompt immediately. Show in-app reminder badge on Reminders tab instead. Prompt again contextually when they set up their first reminder. |

---

## 7. Non-Functional Requirements

- **Performance:** Registration/login API response < 2 seconds. OAuth flow < 5 seconds total (including provider). Entire onboarding flow (registration to dashboard) must be completable in under 3 minutes (Journey 1 target). Optimal with OAuth: under 90 seconds.
- **Security:** Passwords hashed using ASP.NET Identity (bcrypt). See DEC-011. HTTPS only. Tokens stored securely in platform keychain/keystore. No passwords stored in plaintext or logs.
- **Offline behavior:** Login requires internet. If user has a valid cached session, app opens to last cached state with a "No connection" banner. No offline registration.
- **Accessibility:** All form fields have labels. Error messages are associated with fields (screen reader compatible). Minimum touch target: 44x44pt. OAuth buttons have descriptive labels for screen readers.
- **Localization:** All UI text in Bulgarian by default (detect from device locale). English as secondary. Date format: dd.MM.yyyy (Bulgarian standard). Currency: лв (BGN) default. Popular Bulgarian car makes prioritized in vehicle picker.
- **Analytics events:** onboarding_started, auth_method_selected (email/google/apple), account_created, vehicle_added, vehicle_skipped, expense_added, expense_skipped, onboarding_completed, dashboard_first_view. Track timestamps for each step to measure funnel performance.

---

## 8. Dependencies

- **Depends on:** Nothing — this is the foundation feature. Must be built first.
- **Depended on by:** Every other feature (all features require an authenticated user).
- **API endpoints needed:**
  - `POST /api/auth/register` — create account (email+password)
  - `POST /api/auth/login` — authenticate
  - `POST /api/auth/logout` — end session
  - `POST /api/auth/refresh` — refresh token
  - `POST /api/auth/password-reset/request` — send reset email
  - `POST /api/auth/password-reset/confirm` — set new password
  - `POST /api/auth/oauth/{provider}` — OAuth flow (provider: google, apple)
  - `POST /api/auth/verify-email` — verify email token
  - `GET /api/users/me` — get current user profile
  - `PUT /api/users/me` — update profile

---

## 9. Out of Scope

- Two-factor authentication (2FA) — deferred to post-MVP
- Social login via Twitter/X or LinkedIn — not relevant for target segment
- Account deletion flow (GDPR) — will be specified in a separate compliance spec
- Biometric login (fingerprint/face) — deferred to v1.1
- "Remember me" toggle — sessions auto-persist for 30 days by default
- Admin panel for user management — not in MVP
- Garage/dealer/fleet onboarding flows — MVP only supports driver role
- Historical expense import during onboarding — start fresh (Journey 1 insight: asking for historical data is overwhelming)
- Multi-slide welcome carousel — single screen only (Journey 1 insight)

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we require email verification before allowing premium subscription? | Prevents fraud on premium accounts | Decide during payment integration |
| OQ-2 | Which OAuth providers to support? Google Sign-In and Apple Sign-In are included in MVP (Apple requires it if any OAuth is offered on iOS). Facebook is not included. | Apple requires Apple Sign-In if any OAuth is offered on iOS | Resolved — Google and Apple in MVP. No Facebook. |
| OQ-3 | Should make/model lists be hardcoded or fetched from an API? | Affects onboarding offline capability and data freshness | Recommend: ship with a static list of top 50 makes + models for Bulgarian market. Add API endpoint for updates later. |
| OQ-4 | What analytics events should fire during onboarding? | Critical for measuring activation rate (target: 60%+) | Proposed events listed in Section 7 NFRs |
| OQ-5 | Should the welcome screen have a single screen or a brief carousel? | Journey 1 strongly recommends single screen for speed. But carousel could communicate more. | Recommend single screen per Journey 1 analysis. Test post-launch if activation is below 60%. |

---

**Data entities discovered:** User (with role field), AuthToken, PasswordResetToken, LoginAttempt
**API endpoints discovered:** 10 auth/user endpoints (listed in Section 8)

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial spec |
| 2.0 | 2026-03-07 | Regenerated with user journey insights. Key changes: single welcome screen (not carousel), OAuth as primary auth, minimum vehicle fields (make/model/year only), delayed notification permission, dashboard reveal design, role field for future multi-role support, analytics events, empty state and drop-off mitigation details from Journey 1. |
| 2.1 | 2026-03-23 | Removed Facebook OAuth references. Google and Apple Sign-In confirmed as MVP OAuth providers per DEC-011 v1.1. |
