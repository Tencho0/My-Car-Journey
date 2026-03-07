# Functional Specification: Onboarding & Authentication

**File:** `/03-product/functional-specs/onboarding-auth.md`
**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Source PRD:** `/03-product/product-requirements-document.md` -- Section 6.1 (M1, M10)
**Related features:** M1 (User Authentication), M10 (Onboarding Flow)

---

## 1. Overview

**What this feature does:** Enables users to create an account, log in, reset their password, and be guided through a first-launch onboarding flow that gets them to the "aha moment" (seeing a cost on the dashboard) as fast as possible.

**Why it matters:** Authentication is the foundation -- no account means no data sync, no premium, no cross-device access. The onboarding flow is the single most important funnel for activation. If users don't add a vehicle and log their first expense in session 1, retention drops sharply (Pain: P8 tedious entry; Gain: G1 see exact monthly cost).

**User type:** All users (new and returning).

---

## 2. User Flow

### Happy Path -- New User (First Launch)

1. User opens the app for the first time
2. System shows Welcome screen (value proposition, 2-3 slides max)
3. User taps "Get Started"
4. System shows Registration screen
5. User enters email + password (or taps OAuth: Google / Apple / Facebook)
6. System creates account, logs user in, sends verification email
7. System shows "Add Your Car" screen (onboarding step 1)
8. User enters make, model, year, fuel type (minimum required fields)
9. User optionally adds a photo, license plate, current odometer
10. User taps "Save"
11. System creates vehicle, shows "Log Your First Expense" screen (onboarding step 2)
12. User enters amount + category (pre-filled: today's date, default vehicle)
13. User taps "Save"
14. System shows the Cost Dashboard with the first expense visible
15. System shows a celebratory micro-animation: "You're tracking! Keep logging to see your real monthly cost."
16. Onboarding complete -- user is in the main app

### Alternative Paths

- **Path A: OAuth registration** -- User taps Google/Apple/Facebook on step 5. OAuth provider handles authentication. On success, user is logged in and proceeds to step 7. Email is pre-filled from OAuth profile. No password needed.
- **Path B: User skips onboarding** -- "Skip" link available on step 7 and step 11. If skipped, user lands on an empty dashboard with a prominent CTA: "Add your first car to get started." Skipping is tracked in analytics.
- **Path C: User already has account** -- Taps "Log In" on Welcome screen. Enters email + password (or OAuth). Lands on dashboard (or empty state if no vehicles).
- **Path D: Password reset** -- From Login screen, taps "Forgot password?" Enters email. System sends reset link. User clicks link, enters new password. Redirected to login.
- **Path E: Email verification** -- After registration, user receives a verification email. App shows a subtle banner: "Verify your email to secure your account." App is fully functional without verification. If unverified after 7 days, show a more prominent reminder.

### Flow Diagram

```
[App Launch]
    |
    v
[Welcome Screen] --"Log In"--> [Login Screen] --> [Dashboard]
    |                              |
    "Get Started"              "Forgot Password?" --> [Reset Flow]
    |
    v
[Registration Screen]
    |  (email+pw or OAuth)
    v
[Add Your Car] --"Skip"--> [Empty Dashboard + CTA]
    |
    v
[Log First Expense] --"Skip"--> [Dashboard with vehicle, no expenses]
    |
    v
[Dashboard with first expense -- AHA MOMENT]
```

---

## 3. Screen Descriptions

### Screen: Welcome (First Launch Only)

**Purpose:** Communicate the app's value in 5 seconds and drive user to register.
**Entry points:** First app launch only (never shown again after registration).

**Layout:**
- Full-screen illustration or car imagery (brand-aligned)
- 2-3 swipeable slides:
  - Slide 1: "Know what your car really costs you" (P1 pain)
  - Slide 2: "Track every expense in seconds" (P8 pain relief)
  - Slide 3: "See your car's complete story" (P3 pain relief)
- Page dots indicating current slide
- "Get Started" primary button (bottom)
- "Already have an account? Log In" secondary link (bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Slides | Swipe left/right | Navigate between value prop slides |
| Page dots | Tap | Jump to specific slide |
| "Get Started" | Tap | Navigate to Registration screen |
| "Log In" | Tap | Navigate to Login screen |

**Loading state:** N/A (static content)
**Empty state:** N/A
**Error state:** N/A

---

### Screen: Registration

**Purpose:** Create a new account.
**Entry points:** "Get Started" from Welcome screen.

**Layout:**
- App logo (top)
- "Create Your Account" heading
- Email field
- Password field (with show/hide toggle)
- "Create Account" primary button
- Divider: "or continue with"
- OAuth buttons: Google, Apple (iOS only), Facebook (row of icons)
- "Already have an account? Log In" link (bottom)
- Terms of service + privacy policy links (small text, bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Email field | Type | Validates format on blur |
| Password field | Type | Shows strength indicator (weak/medium/strong) |
| Show/hide toggle | Tap | Toggles password visibility |
| "Create Account" | Tap | Validates inputs, creates account, proceeds to onboarding |
| OAuth button | Tap | Opens OAuth provider flow |
| "Log In" | Tap | Navigate to Login screen |
| ToS / Privacy link | Tap | Opens in-app browser |

**Loading state:** "Create Account" button shows spinner during API call. OAuth buttons disabled during flow.
**Empty state:** N/A (form with empty fields)
**Error state:**
- Invalid email format: inline error below field -- "Enter a valid email address"
- Weak password: inline error -- "Password must be at least 8 characters with 1 number"
- Email already registered: inline error -- "This email is already registered. Log in instead?"
- Network error: toast notification -- "Connection failed. Check your internet and try again."
- OAuth failure: toast -- "Could not connect to [provider]. Try another method."

---

### Screen: Login

**Purpose:** Authenticate an existing user.
**Entry points:** "Log In" from Welcome screen, "Log In" from Registration screen, deep link from password reset email.

**Layout:**
- App logo (top)
- "Welcome Back" heading
- Email field (pre-filled if coming from failed registration)
- Password field (with show/hide toggle)
- "Forgot password?" link (right-aligned below password)
- "Log In" primary button
- Divider: "or continue with"
- OAuth buttons: Google, Apple (iOS only), Facebook
- "Don't have an account? Sign Up" link (bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| "Log In" | Tap | Validates, authenticates, navigates to Dashboard |
| "Forgot password?" | Tap | Navigate to Password Reset screen |
| OAuth button | Tap | Opens OAuth flow, navigates to Dashboard on success |
| "Sign Up" | Tap | Navigate to Registration screen |

**Loading state:** "Log In" button shows spinner.
**Empty state:** N/A
**Error state:**
- Wrong credentials: inline error -- "Incorrect email or password. Try again."
- Account locked (5+ failed attempts): "Account temporarily locked. Try again in 15 minutes or reset your password."
- Network error: toast -- "Connection failed. Check your internet and try again."

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

### Screen: Onboarding -- Add Your Car

**Purpose:** Guide new user to add their first vehicle immediately after registration.
**Entry points:** Automatically shown after successful registration.

**Layout:**
- Progress indicator (step 1 of 2)
- "Add Your Car" heading
- "Let's start with the basics" subtitle
- Make field (dropdown/search -- pre-populated with common Bulgarian market makes: BMW, Audi, VW, Mercedes, Opel, Toyota, Ford, Peugeot, Renault)
- Model field (dropdown/search -- filtered by selected make)
- Year field (number picker, range: 1970-current year)
- Fuel type field (dropdown: Petrol, Diesel, LPG, Hybrid, Electric)
- Photo area (optional -- tap to take photo or select from gallery)
- License plate field (optional -- text)
- Current odometer field (optional -- number + km)
- "Save & Continue" primary button
- "Skip for now" link (bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Make dropdown | Tap/type | Shows searchable list of makes |
| Model dropdown | Tap/type | Shows models filtered by selected make |
| Photo area | Tap | Opens camera/gallery picker |
| "Save & Continue" | Tap | Creates vehicle, proceeds to step 2 |
| "Skip for now" | Tap | Skips to empty dashboard with CTA |

**Loading state:** "Save & Continue" shows spinner while creating vehicle.
**Empty state:** All fields empty with placeholder text.
**Error state:**
- Make/model/year not filled: inline validation -- "Make, model, year, and fuel type are required"
- Network error: toast -- "Could not save your vehicle. Check your connection."

---

### Screen: Onboarding -- Log First Expense

**Purpose:** Guide user to log their first expense immediately after adding a vehicle.
**Entry points:** Automatically shown after saving first vehicle in onboarding.

**Layout:**
- Progress indicator (step 2 of 2)
- "Log Your First Expense" heading
- "How much did you last spend on your [make model]?" subtitle
- Amount field (numeric keypad, лв currency)
- Category selector (visual grid of icons: Fuel, Maintenance, Mods, Insurance, Tax, Tires, Parking, Fines, Car Wash, Other)
- Date field (pre-filled: today, tap to change)
- Notes field (optional, single line)
- "Save & See Dashboard" primary button
- "Skip for now" link (bottom)

**Interactions:**

| Element | Action | Result |
|---------|--------|--------|
| Amount field | Tap | Opens numeric keypad |
| Category icon | Tap | Selects category (highlighted state) |
| Date field | Tap | Opens date picker |
| "Save & See Dashboard" | Tap | Saves expense, navigates to dashboard |
| "Skip for now" | Tap | Navigates to dashboard (vehicle added, no expenses) |

**Loading state:** Button shows spinner while saving.
**Empty state:** Amount empty, no category selected.
**Error state:**
- No amount entered: inline -- "Enter an amount"
- No category selected: inline -- "Choose a category"
- Network error: toast

---

## 4. Business Rules

| # | Rule | Example |
|---|------|---------|
| BR-1 | Email addresses must be unique across all accounts (case-insensitive) | "User@Email.com" and "user@email.com" are the same account |
| BR-2 | Passwords must be at least 8 characters with at least 1 number | "password" = rejected; "password1" = accepted |
| BR-3 | OAuth accounts are linked by email. If a user registers with email+password and later tries OAuth with the same email, accounts are merged. | User registers as john@gmail.com, later taps "Sign in with Google" using the same email -- same account, both login methods now work |
| BR-4 | Account lockout after 5 consecutive failed login attempts. Lockout duration: 15 minutes. | After 5 wrong passwords, show lockout message with countdown |
| BR-5 | Password reset tokens expire after 1 hour | User clicks reset link after 2 hours -- "This link has expired. Request a new one." |
| BR-6 | Sessions persist for 30 days. After 30 days of inactivity, user must re-authenticate. | User opens app after 45 days -- redirected to Login screen |
| BR-7 | Welcome screen is shown only on first launch (before any account exists on device) | Returning user who reinstalls: sees Welcome screen (no local state). Existing user who logs out: sees Login screen, not Welcome. |
| BR-8 | Onboarding "Skip" is allowed but tracked. Users who skip have lower activation -- show contextual CTAs later. | Skipped "Add Your Car" -- dashboard shows "Add your first car" CTA prominently |
| BR-9 | Onboarding is shown only once per account. If user logs out and back in, they go straight to the dashboard. | Completed onboarding, logged out, logged in again -- lands on dashboard |
| BR-10 | Free tier allows up to 2 vehicles. Onboarding creates the first one -- no paywall friction. | First vehicle is always free, no premium check needed |
| BR-11 | Email verification is encouraged but not required for app functionality. | Unverified users can use all features. After 7 days, show persistent banner. |
| BR-12 | User must accept Terms of Service and Privacy Policy to create an account. | Registration requires implicit consent (creating account = accepting ToS). Link must be visible. |

---

## 5. Data Requirements

**Entities involved:**

- `User` -- id, email, password_hash, display_name, avatar_url, email_verified, created_at, updated_at, last_login_at, auth_provider, locale, currency_preference, distance_unit
- `AuthToken` -- id, user_id, token, refresh_token, expires_at, created_at
- `PasswordResetToken` -- id, user_id, token, expires_at, used
- `LoginAttempt` -- id, user_id, ip_address, success, created_at (for lockout tracking)

**Data read:** User table (login, session validation)
**Data written:** User (registration), AuthToken (login/refresh), PasswordResetToken (reset flow), LoginAttempt (each attempt)

**Validation rules:**

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| email | string | Yes | Valid email format (RFC 5322), max 254 chars, unique (case-insensitive) |
| password | string | Yes (unless OAuth) | Min 8 chars, at least 1 number, max 128 chars |
| display_name | string | No | Max 50 chars, no special characters except spaces and hyphens |
| locale | enum | Yes (default: bg) | Values: "bg", "en" |
| currency_preference | enum | Yes (default: BGN) | Values: "BGN", "EUR" |
| distance_unit | enum | Yes (default: km) | Values: "km" |

---

## 6. Edge Cases & Error Handling

| Scenario | Expected Behavior |
|----------|-------------------|
| No internet during registration | Show error toast: "No internet connection. Please check and try again." Form data preserved. |
| No internet during OAuth | Show error toast. Fall back to email+password option. |
| User closes app mid-onboarding (after registration, before adding vehicle) | On next launch, user is logged in. Show "Add Your Car" CTA on empty dashboard. Do not replay onboarding slides. |
| OAuth provider returns insufficient permissions (no email) | Show error: "We need your email to create an account. Please grant email permission." |
| User tries to register with a disposable email | Allow it. No domain blacklisting for MVP. |
| Two devices logged in simultaneously | Both sessions are valid. Data syncs via API. Last-write-wins for profile changes. |
| Password reset requested for non-existent email | Show same success message as valid email (security). No email sent. |
| User taps reset link on a different device | Reset works -- token is server-side. After reset, redirect to login on that device. |
| Extremely long email or name input | Enforce max length. Truncate display if needed. |
| App killed during onboarding vehicle save | Retry on next launch. If vehicle was partially created server-side, detect and resume. |

---

## 7. Non-Functional Requirements

- **Performance:** Registration/login API response < 2 seconds. OAuth flow < 5 seconds total (including provider).
- **Security:** Passwords hashed with bcrypt (or equivalent). HTTPS only. Tokens stored securely (iOS Keychain / Android Keystore). No passwords stored in plaintext or logs.
- **Offline behavior:** Login requires internet. If user has a valid cached session, app opens to last cached state with a "No connection" banner. No offline registration.
- **Accessibility:** All form fields have labels. Error messages are associated with fields (screen reader compatible). Minimum touch target: 44x44pt.
- **Localization:** All UI text in Bulgarian by default. English as secondary. Date format: dd.MM.yyyy (Bulgarian standard). Currency: лв (BGN) default.

---

## 8. Dependencies

- **Depends on:** Nothing -- this is the foundation feature. Must be built first.
- **Depended on by:** Every other feature (all features require an authenticated user).
- **API endpoints needed:**
  - `POST /api/auth/register` -- create account
  - `POST /api/auth/login` -- authenticate
  - `POST /api/auth/logout` -- end session
  - `POST /api/auth/refresh` -- refresh token
  - `POST /api/auth/password-reset/request` -- send reset email
  - `POST /api/auth/password-reset/confirm` -- set new password
  - `POST /api/auth/oauth/{provider}` -- OAuth flow
  - `POST /api/auth/verify-email` -- verify email token
  - `GET /api/users/me` -- get current user profile
  - `PUT /api/users/me` -- update profile

---

## 9. Out of Scope

- Two-factor authentication (2FA) -- deferred to post-MVP
- Social login via Twitter/X or LinkedIn -- not relevant for target segment
- Account deletion flow (GDPR) -- will be specified in a separate compliance spec
- Biometric login (fingerprint/face) -- deferred to v1.1
- "Remember me" toggle -- sessions auto-persist for 30 days by default
- Admin panel for user management -- not in MVP

---

## 10. Open Questions

| # | Question | Impact | Resolution |
|---|----------|--------|------------|
| OQ-1 | Should we require email verification before allowing premium subscription? | Prevents fraud on premium accounts | Decide during payment integration |
| OQ-2 | Which OAuth providers are most popular among Bulgarian car enthusiasts? Google and Facebook likely top. Is Apple Sign-In worth the effort for MVP? | Apple requires Apple Sign-In if any OAuth is offered on iOS | Research App Store requirements -- may be mandatory |
| OQ-3 | Should make/model lists be hardcoded or fetched from an API? | Affects onboarding offline capability and data freshness | Recommend: ship with a static list of top 50 makes + models for Bulgarian market. Add API endpoint for updates later. |
| OQ-4 | What analytics events should fire during onboarding? | Critical for measuring activation rate (target: 60%+) | Define event schema: onboarding_started, vehicle_added, expense_added, onboarding_completed, onboarding_skipped |

---

**New data entities discovered:** User, AuthToken, PasswordResetToken, LoginAttempt
**New API endpoints discovered:** 10 auth/user endpoints (listed in Section 8)
