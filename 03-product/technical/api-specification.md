# API Specification — Car Ownership Intelligence Platform

**File:** `/03-product/technical/api-specification.md`
**Produced by:** @product-architect
**Date:** 2026-03-22
**Version:** 1.0
**Status:** Draft

---

## References

### Input Documents

| Document | Path | Used For |
|---|---|---|
| Technical Architecture | `/03-product/technical/architecture.md` | Module structure, API design principles, auth flow, rate limiting |
| Database Schema | `/03-product/technical/database-schema.md` | Table definitions, enum values, relationships |
| Onboarding & Auth Spec | `/03-product/functional-specs/onboarding-auth.md` | Auth endpoints, user profile, onboarding state |
| Vehicle Management Spec | `/03-product/functional-specs/vehicle-management.md` | Vehicle CRUD, make/model lookup, photo upload |
| Expense Tracking Spec | `/03-product/functional-specs/expense-tracking.md` | Expense CRUD, quick-add, category list |
| Fuel Entry Spec | `/03-product/functional-specs/fuel-entry.md` | Fuel CRUD, consumption calculation |
| Cost Dashboard Spec | `/03-product/functional-specs/cost-dashboard.md` | Aggregation endpoints, premium gating |
| Vehicle Timeline Spec | `/03-product/functional-specs/vehicle-timeline.md` | Timeline entries, photo management, search |
| Maintenance Reminders Spec | `/03-product/functional-specs/maintenance-reminders.md` | Reminder CRUD, complete/snooze, templates |
| Challenges & Gamification Spec | `/03-product/functional-specs/challenges-gamification.md` | Challenge list, leaderboard, join, badges |
| Share & Export Spec | `/03-product/functional-specs/share-export.md` | Vehicle card generation |
| User Journeys | `/03-product/user-journeys/` | Client-side data needs and flow validation |
| DEC-009 Backend Approach | `/00-project/decisions/DEC-009-backend-approach.md` | ASP.NET Core Web API, modular monolith, controllers |
| DEC-011 Authentication | `/00-project/decisions/DEC-011-authentication.md` | ASP.NET Identity + JWT |
| DEC-013 Image Storage | `/00-project/decisions/DEC-013-image-storage.md` | Local VPS disk (MVP), presigned URL flow |
| DEC-014 Push Notifications | `/00-project/decisions/DEC-014-push-notifications.md` | FCM device registration |
| DEC-016 Subscriptions | `/00-project/decisions/DEC-016-subscriptions-iap.md` | RevenueCat webhooks |
| PRD | `/03-product/product-requirements-document.md` | MVP scope, feature priorities |

---

## 1. API Design Principles

| Principle | Implementation |
|---|---|
| **RESTful, resource-oriented** | URLs represent resources (nouns), HTTP methods represent actions (verbs). No verbs in URLs except for action-oriented sub-resources (`/complete`, `/snooze`, `/join`). |
| **Consistent base path** | All endpoints under `/api/v1/{module}/{resource}`. Module groupings match the backend modular monolith structure. |
| **JSON everywhere** | All request and response bodies use `application/json`. Property names use `camelCase` (C# DTOs serialize with `System.Text.Json` camelCase policy). |
| **URL-based versioning** | `/api/v1/` prefix from day one. Breaking changes increment version (`/api/v2/`). Additive changes (new optional fields, new endpoints) stay on current version. |
| **ISO 8601 UTC timestamps** | All `DateTime` fields serialized as ISO 8601 strings in UTC (e.g., `"2026-03-22T14:30:00Z"`). Clients convert to local time for display. |
| **Money as integer cents** | All monetary values transmitted as integers representing cents (e.g., 127.50 лв = `12750`). Currency code sent alongside. Frontend formats for display. |
| **Bearer token auth** | JWT access tokens sent via `Authorization: Bearer {token}` header. |
| **Consistent error shape** | All errors return RFC 7807 `ProblemDetails` format. |
| **No unbounded queries** | Every list endpoint supports pagination. No endpoint ever returns all rows. |
| **Soft-delete awareness** | Deleted resources return 404. Deleted resources are excluded from all list results. |

---

## 2. Authentication & Authorization

### 2.1 Auth Flow Overview

The platform uses ASP.NET Identity for user management with JWT bearer tokens for API authentication (DEC-011).

```
Registration/Login → API returns accessToken + refreshToken
    ↓
All subsequent requests → Authorization: Bearer {accessToken}
    ↓
Token expires (30 min) → POST /auth/refresh with refreshToken → new pair
    ↓
Refresh token expires (30 days) or revoked → user must re-authenticate
```

### 2.2 Token Format

**Access Token (JWT):**

| Claim | Type | Description |
|---|---|---|
| `sub` | UUID | User ID |
| `email` | string | User email |
| `role` | string | User role (`driver`, `admin`) |
| `subscriptionTier` | string | `free` or `premium` |
| `subscriptionExpiry` | ISO 8601 / null | Premium expiration date, null for free |
| `iat` | Unix timestamp | Issued at |
| `exp` | Unix timestamp | Expires at (30 minutes after issue) |

**Refresh Token:** Opaque string stored hashed server-side. 30-day expiry. Rotated on each use (old token revoked, new pair issued).

### 2.3 Where Tokens Are Sent

- **Access token:** `Authorization: Bearer {accessToken}` header on every authenticated request.
- **Refresh token:** In the request body of `POST /api/v1/auth/refresh` only. Never sent as a header or query param.

### 2.4 Endpoint Auth Levels

| Level | Description | How Enforced |
|---|---|---|
| **Public** | No authentication required | No `[Authorize]` attribute |
| **Authenticated** | Valid JWT required (any role) | `[Authorize]` |
| **Premium** | Valid JWT + `subscriptionTier = premium` | `[Authorize]` + `SubscriptionRequiredAttribute` (custom) |
| **Admin** | Valid JWT + `role = admin` | `[Authorize(Roles = "admin")]` |

### 2.5 Role-Based Access

MVP roles: `driver` (default for all registrations), `admin` (manually assigned). Future: `garage_owner`, `dealer`, `fleet_manager`.

**Data isolation rule:** Every authenticated endpoint implicitly filters by the current user's ID. A driver can never access another user's data. Admin endpoints bypass this filter for management purposes.

### 2.6 Refresh Token Flow

1. Client detects 401 response (or proactively checks `exp` claim before request).
2. Client calls `POST /api/v1/auth/refresh` with the refresh token.
3. Server validates: token exists, not expired, not revoked.
4. Server rotates: old refresh token marked revoked, new access + refresh token pair issued.
5. If refresh token is invalid/expired → 401 → client must re-authenticate (login screen).

### 2.7 Multi-Device Support

- A user can be logged in on multiple devices simultaneously.
- Each device has its own refresh token.
- Revoking a refresh token only affects that device's session.
- `POST /api/v1/auth/logout` revokes the refresh token for the current device only.

---

## 3. Common Patterns

### 3A. Standard Response Envelope

**Success (single resource):**

```json
{
  "data": {
    "id": "uuid",
    "make": "BMW",
    "model": "E46 320i",
    "year": 2004
  }
}
```

**Success (list with pagination):**

```json
{
  "data": [
    { "id": "uuid", "make": "BMW" },
    { "id": "uuid", "make": "VW" }
  ],
  "meta": {
    "nextCursor": "eyJpZCI6Ijk...",
    "hasMore": true,
    "totalCount": 47
  }
}
```

**Success (no content):** HTTP 204 with empty body.

**Error (RFC 7807 ProblemDetails):**

```json
{
  "type": "https://carplatform.bg/errors/validation",
  "title": "Validation Error",
  "status": 400,
  "detail": "One or more validation errors occurred.",
  "errors": {
    "amount": ["Must be greater than 0."],
    "category": ["Invalid category value."]
  }
}
```

### Standard Error Codes

| HTTP Status | `type` suffix | When |
|---|---|---|
| 400 | `/errors/validation` | Request body fails validation |
| 400 | `/errors/bad-request` | Malformed request (missing body, wrong content type) |
| 401 | `/errors/unauthorized` | Missing/invalid/expired JWT |
| 403 | `/errors/forbidden` | Valid JWT but insufficient role or subscription |
| 403 | `/errors/premium-required` | Feature requires premium subscription |
| 404 | `/errors/not-found` | Resource does not exist or is soft-deleted |
| 409 | `/errors/conflict` | Duplicate resource (e.g., email already registered) |
| 422 | `/errors/business-rule` | Business rule violation (e.g., vehicle limit exceeded) |
| 429 | `/errors/rate-limited` | Rate limit exceeded |
| 500 | `/errors/internal` | Unexpected server error |

### 3B. Pagination

**Strategy:** Cursor-based pagination for all list endpoints.

**Why cursor-based (not offset):** Offset pagination breaks when data is inserted/deleted between page fetches (items shift, duplicates or gaps appear). Cursor pagination is stable — each page starts exactly where the previous ended. This matters for timeline/expense feeds where new items are added frequently.

**Standard query parameters:**

| Param | Type | Default | Description |
|---|---|---|---|
| `cursor` | string (opaque) | null | Cursor from previous `meta.nextCursor`. Omit for first page. |
| `limit` | integer | 20 | Items per page. Min: 1, Max: 100. |

**Standard pagination response (inside `meta`):**

| Field | Type | Description |
|---|---|---|
| `nextCursor` | string / null | Opaque cursor for next page. Null if no more items. |
| `hasMore` | boolean | Whether more items exist after this page. |
| `totalCount` | integer / null | Total items matching the query. Null if too expensive to compute. |

**Implementation:** Cursor encodes the `(date, id)` or `(created_at, id)` of the last item in the page (base64-encoded). Server decodes and uses as `WHERE (date, id) < (@lastDate, @lastId)` for keyset pagination.

### 3C. Filtering & Sorting

**Standard filter query parameters:**

| Param | Type | Description |
|---|---|---|
| `dateFrom` | ISO 8601 date | Filter items on or after this date |
| `dateTo` | ISO 8601 date | Filter items on or before this date |
| `category` | string | Filter by expense/timeline category (enum value) |
| `search` | string | Free-text search (where supported) |

**Sorting:** Each list endpoint has a default sort order (typically newest first). Sort is not user-configurable in MVP — each endpoint's documentation specifies its fixed sort order. This reduces complexity; custom sorting can be added post-MVP if needed.

### 3D. Bulk Operations

**MVP approach:** No dedicated bulk endpoints. The mobile app handles batch catch-up by making sequential individual requests. This keeps the API simple for a solo developer.

**Future pattern (if needed):**

```json
POST /api/v1/vehicles/{vehicleId}/expenses/batch
{
  "items": [
    { "category": "fuel", "amountCents": 12150, ... },
    { "category": "parking", "amountCents": 500, ... }
  ]
}

Response:
{
  "data": {
    "succeeded": [ { "index": 0, "id": "uuid" }, { "index": 1, "id": "uuid" } ],
    "failed": []
  }
}
```

---

## 4. Endpoint Definitions — By Module

### 4.1 Auth Module — `CarPlatform.Auth`

Controller: `AuthController` — route prefix: `/api/v1/auth`
Controller: `UsersController` — route prefix: `/api/v1/users`
Controller: `DevicesController` — route prefix: `/api/v1/devices`

---

#### AUTH-01: Register (Email + Password)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/register` |
| **Summary** | Create a new user account with email and password |
| **Auth** | Public |
| **Source** | onboarding-auth.md §2 Happy Path step 5b, §8 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `email` | string | Yes | Valid email (RFC 5322), max 254 chars, case-insensitive unique |
| `password` | string | Yes | Min 8 chars, at least 1 number, max 128 chars |
| `displayName` | string | No | Max 50 chars |
| `locale` | string | No | `"bg"` (default) or `"en"` |
| `currencyPreference` | string | No | `"BGN"` (default) or `"EUR"` |

**Response 201 Created:**

```json
{
  "data": {
    "userId": "uuid",
    "email": "user@example.com",
    "displayName": "Иван",
    "accessToken": "eyJ...",
    "refreshToken": "dGhpcyBpcyBhIH...",
    "expiresIn": 1800
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Invalid email format, weak password |
| 409 | conflict | Email already registered (message: "This email is already registered.") |
| 429 | rate-limited | More than 5 registrations per hour from this IP |

**Notes:**
- Creates `user_profiles` row simultaneously (one-to-one).
- Sends verification email asynchronously (non-blocking).
- Sets `onboarding_completed = false`.
- Role defaults to `driver`.

---

#### AUTH-02: Login (Email + Password)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/login` |
| **Summary** | Authenticate with email and password, receive tokens |
| **Auth** | Public |
| **Source** | onboarding-auth.md §3 Login Screen, §4 BR-4 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `email` | string | Yes | Valid email |
| `password` | string | Yes | Non-empty |

**Response 200 OK:**

```json
{
  "data": {
    "userId": "uuid",
    "email": "user@example.com",
    "displayName": "Иван",
    "role": "driver",
    "subscriptionTier": "free",
    "onboardingCompleted": true,
    "accessToken": "eyJ...",
    "refreshToken": "dGhpcyBpcyBhIH...",
    "expiresIn": 1800
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 401 | unauthorized | Wrong email or password (generic message: "Incorrect email or password.") |
| 403 | forbidden | Account locked (BR-4: 5 failed attempts → 15 min lockout). Body includes `lockedUntil`. |
| 429 | rate-limited | More than 10 login attempts per 15 min from this IP |

**Notes:**
- Records `login_attempts` row (success or failure).
- On success: updates `last_activity_at`.
- Returns `onboardingCompleted` so the client knows whether to show onboarding or dashboard.

---

#### AUTH-03: OAuth Authentication

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/oauth/{provider}` |
| **Summary** | Authenticate or register via OAuth provider |
| **Auth** | Public |
| **Path Params** | `provider`: `google` or `apple` |
| **Source** | onboarding-auth.md §2 Path A, §4 BR-3 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `idToken` | string | Yes | Provider-issued ID token |
| `accessToken` | string | No | Provider access token (if needed for profile fetch) |

**Response 200 OK (existing user) / 201 Created (new user):**

Same shape as AUTH-02 response, plus `isNewUser: true/false`.

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Invalid or expired ID token |
| 400 | bad-request | Unsupported provider |
| 400 | bad-request | Token does not include email permission |

**Notes:**
- If email matches an existing account: links OAuth provider, returns tokens (BR-3: account merge).
- If new email: creates account, sets `authProvider` to the provider value.
- `displayName` auto-filled from OAuth profile if available.

---

#### AUTH-04: Refresh Token

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/refresh` |
| **Summary** | Exchange a valid refresh token for a new access + refresh token pair |
| **Auth** | Public (but requires valid refresh token in body) |
| **Source** | onboarding-auth.md §4 BR-6, architecture.md §5.1 |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `refreshToken` | string | Yes |

**Response 200 OK:**

```json
{
  "data": {
    "accessToken": "eyJ...",
    "refreshToken": "bmV3IHJlZnJlc2g...",
    "expiresIn": 1800
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 401 | unauthorized | Refresh token invalid, expired, or already revoked |

**Notes:**
- Implements token rotation: old refresh token revoked immediately, new pair issued.
- `replaced_by_token_id` chain enables detecting refresh token reuse (potential theft).

---

#### AUTH-05: Logout

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/logout` |
| **Summary** | Revoke the current device's refresh token |
| **Auth** | Authenticated |
| **Source** | onboarding-auth.md §8 |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `refreshToken` | string | Yes |

**Response:** 204 No Content

**Notes:**
- Revokes only the specified refresh token (current device).
- Other devices remain logged in.

---

#### AUTH-06: Request Password Reset

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/password-reset/request` |
| **Summary** | Send a password reset email |
| **Auth** | Public |
| **Source** | onboarding-auth.md §3 Password Reset Screen, §4 BR-5 |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `email` | string | Yes |

**Response:** 204 No Content (always — even if email doesn't exist, for security)

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 429 | rate-limited | More than 3 reset requests per hour for this email |

**Notes:**
- Reset token expires after 1 hour (BR-5).
- Same response for existing and non-existing emails (security: don't reveal which emails exist).

---

#### AUTH-07: Confirm Password Reset

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/password-reset/confirm` |
| **Summary** | Set a new password using a reset token |
| **Auth** | Public |
| **Source** | onboarding-auth.md §3 Password Reset |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `token` | string | Yes | From email link |
| `newPassword` | string | Yes | Min 8 chars, at least 1 number, max 128 chars |

**Response:** 204 No Content

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Weak password |
| 400 | bad-request | Token expired (>1 hour) or already used |
| 404 | not-found | Token does not exist |

---

#### AUTH-08: Verify Email

| | |
|---|---|
| **Method + Path** | `POST /api/v1/auth/verify-email` |
| **Summary** | Confirm email address using verification token |
| **Auth** | Public |
| **Source** | onboarding-auth.md §2 Path F |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `token` | string | Yes |

**Response:** 204 No Content

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | bad-request | Token expired or invalid |

**Notes:**
- Sets `emailConfirmed = true` on the user.
- App is fully functional without verification (BR-11). This is optional.

---

#### USER-01: Get Current User Profile

| | |
|---|---|
| **Method + Path** | `GET /api/v1/users/me` |
| **Summary** | Get the authenticated user's profile and settings |
| **Auth** | Authenticated |
| **Source** | onboarding-auth.md §8, cost-dashboard.md (subscription tier) |

**Response 200 OK:**

```json
{
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "displayName": "Иван",
    "avatarUrl": "https://...",
    "emailVerified": true,
    "role": "driver",
    "authProvider": "google",
    "locale": "bg",
    "currencyPreference": "BGN",
    "distanceUnit": "km",
    "onboardingCompleted": true,
    "onboardingSkippedAtStep": null,
    "subscriptionTier": "free",
    "subscriptionExpiry": null,
    "settings": {
      "notificationQuietStart": "22:00",
      "notificationQuietEnd": "08:00"
    },
    "stats": {
      "vehicleCount": 2,
      "totalExpenses": 47,
      "memberSince": "2026-01-15T00:00:00Z"
    },
    "createdAt": "2026-01-15T10:30:00Z"
  }
}
```

---

#### USER-02: Update User Profile

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/users/me` |
| **Summary** | Update the authenticated user's profile and settings |
| **Auth** | Authenticated |
| **Source** | onboarding-auth.md §5 Data Requirements, vehicle-management.md (profile settings) |

**Request Body (all fields optional — partial update):**

| Field | Type | Validation |
|---|---|---|
| `displayName` | string | Max 50 chars |
| `locale` | string | `"bg"` or `"en"` |
| `currencyPreference` | string | `"BGN"` or `"EUR"` |
| `distanceUnit` | string | `"km"` or `"mi"` |
| `notificationQuietStart` | string (HH:mm) | Valid time format |
| `notificationQuietEnd` | string (HH:mm) | Valid time format |

**Response 200 OK:** Updated user profile (same shape as USER-01).

---

#### USER-03: Get My Challenges

| | |
|---|---|
| **Method + Path** | `GET /api/v1/users/me/challenges` |
| **Summary** | Get the user's active and past challenge participations |
| **Auth** | Authenticated |
| **Source** | challenges-gamification.md §8 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `status` | string | null | Filter by challenge status: `active`, `completed` |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 20 | Page size |

**Response 200 OK:**

```json
{
  "data": [
    {
      "challengeId": "uuid",
      "challengeName": "Lowest Cost per km",
      "month": 3,
      "year": 2026,
      "status": "active",
      "vehicleId": "uuid",
      "vehicleName": "BMW E46 320i",
      "currentRank": 47,
      "currentMetricValue": 0.38,
      "metricType": "cost_per_km",
      "joinedAt": "2026-03-05T12:00:00Z"
    }
  ],
  "meta": { "nextCursor": null, "hasMore": false, "totalCount": 3 }
}
```

---

#### USER-04: Get My Badges

| | |
|---|---|
| **Method + Path** | `GET /api/v1/users/me/badges` |
| **Summary** | Get the user's earned challenge badges |
| **Auth** | Authenticated |
| **Source** | challenges-gamification.md §8, §4 BR-10 |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "challengeId": "uuid",
      "challengeName": "Lowest Cost per km",
      "badgeType": "top_10",
      "finalRank": 8,
      "finalMetricValue": 0.32,
      "month": 2,
      "year": 2026,
      "earnedAt": "2026-03-01T00:00:00Z"
    }
  ]
}
```

---

#### USER-05: Export My Data (GDPR)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/users/me/data-export` |
| **Summary** | Request a full data export (GDPR right to portability) |
| **Auth** | Authenticated |
| **Source** | PRD §6.4 GDPR compliance, architecture.md §7.1 |

**Response 202 Accepted:**

```json
{
  "data": {
    "exportId": "uuid",
    "status": "processing",
    "estimatedReadyAt": "2026-03-22T15:00:00Z"
  }
}
```

**Notes:**
- Export is generated asynchronously. User receives push notification when ready.
- Export includes all user data in JSON format.
- Download link available via `GET /api/v1/users/me/data-export/{exportId}` for 48 hours.

---

#### USER-06: Delete My Account (GDPR)

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/users/me` |
| **Summary** | Permanently delete the user's account and all data |
| **Auth** | Authenticated |
| **Source** | PRD §6.4 GDPR compliance |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `confirmPassword` | string | Yes (email auth) | Must match current password |

**Response:** 204 No Content

**Notes:**
- Hard-deletes all PII. Anonymizes analytics events.
- Revokes all tokens. Active subscription cancelled via RevenueCat API.
- Irreversible — confirmation required via password.

---

#### DEVICE-01: Register Device (FCM Token)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/devices` |
| **Summary** | Register or update an FCM token for push notifications |
| **Auth** | Authenticated |
| **Source** | DEC-014, maintenance-reminders.md §4 BR-14 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `fcmToken` | string | Yes | Max 500 chars |
| `platform` | string | Yes | `"android"` or `"ios"` |
| `deviceName` | string | No | Max 100 chars |

**Response 200 OK (upsert — token updated) / 201 Created (new device):**

```json
{
  "data": {
    "id": "uuid",
    "platform": "android",
    "deviceName": "Pixel 8",
    "isActive": true,
    "createdAt": "2026-03-22T10:00:00Z"
  }
}
```

**Notes:**
- Upserts by `fcmToken` — if token already exists for this user, updates it. If it exists for a different user, transfers it (device changed owner).

---

#### DEVICE-02: Deregister Device

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/devices/{id}` |
| **Summary** | Deactivate a device's push notification registration |
| **Auth** | Authenticated (own devices only) |
| **Source** | DEC-014 |

**Response:** 204 No Content

---

### 4.2 Vehicles Module — `CarPlatform.Vehicles`

Controller: `VehiclesController` — route prefix: `/api/v1/vehicles`
Controller: `VehicleLookupController` — route prefix: `/api/v1/vehicles`

---

#### VEH-01: Create Vehicle

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles` |
| **Summary** | Add a new vehicle to the user's garage |
| **Auth** | Authenticated |
| **Source** | vehicle-management.md §2 Happy Path, §4 BR-1/BR-2 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `make` | string | Yes | Max 50 chars |
| `model` | string | Yes | Max 50 chars |
| `year` | integer | Yes | 1970–current year |
| `fuelType` | string | No | Enum: `petrol`, `diesel`, `lpg`, `hybrid`, `electric`, `other` |
| `licensePlate` | string | No | Max 15 chars |
| `currentOdometerKm` | integer | No | 0–9,999,999 |
| `nickname` | string | No | Max 30 chars |
| `photoUrl` | string | No | URL from photo upload flow |

**Response 201 Created:**

```json
{
  "data": {
    "id": "uuid",
    "make": "BMW",
    "model": "E46 320i",
    "year": 2004,
    "fuelType": "petrol",
    "licensePlate": null,
    "currentOdometerKm": 154000,
    "photoUrl": null,
    "nickname": "My E46",
    "isActive": true,
    "createdAt": "2026-03-22T10:00:00Z"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Missing required fields, invalid year range |
| 422 | business-rule | Free tier vehicle limit reached (2 vehicles). Body includes `maxVehicles`, `currentCount`, `upgradeRequired: true`. |

**Notes:**
- First vehicle is automatically set as active (`isActive = true`).
- Updates `onboardingSkippedAtStep` if during onboarding.
- Free tier: max 2 vehicles (BR-1). Premium: unlimited.

---

#### VEH-02: List User's Vehicles

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles` |
| **Summary** | Get all vehicles for the current user |
| **Auth** | Authenticated |
| **Source** | vehicle-management.md §3 Vehicle Switcher |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "make": "BMW",
      "model": "E46 320i",
      "year": 2004,
      "fuelType": "petrol",
      "photoUrl": "https://...",
      "nickname": "My E46",
      "isActive": true,
      "stats": {
        "totalExpenses": 47,
        "totalAmountCents": 487500,
        "monthsTracked": 3,
        "photoCount": 4
      }
    }
  ],
  "meta": { "totalCount": 2 }
}
```

**Notes:**
- No pagination needed — max vehicles per user is small (free: 2, premium: practical limit ~20).
- Includes lightweight stats for vehicle switcher display.

---

#### VEH-03: Get Vehicle Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}` |
| **Summary** | Get full details for a specific vehicle |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-management.md §3 Vehicle Profile |

**Response 200 OK:** Full vehicle object with stats (same as VEH-02 item but with additional fields like `licensePlate`, `currentOdometerKm`, `createdAt`, `updatedAt`).

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 404 | not-found | Vehicle doesn't exist or belongs to another user |

---

#### VEH-04: Update Vehicle

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}` |
| **Summary** | Update vehicle details |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-management.md §2 Path B, §3 Add/Edit Vehicle |

**Request Body:** Same fields as VEH-01 (all optional — partial update).

**Response 200 OK:** Updated vehicle object.

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Invalid field values |
| 404 | not-found | Vehicle not found |

**Notes:**
- Odometer decrease triggers a warning in the response: `"warnings": ["Odometer is lower than previous reading (154,500 km)."]` (BR-7). Does NOT reject — allows override.

---

#### VEH-05: Delete Vehicle

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}` |
| **Summary** | Permanently delete a vehicle and all associated data |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-management.md §2 Path C, §4 BR-4/BR-5 |

**Request Body (for vehicles with 10+ entries):**

| Field | Type | Required | Validation |
|---|---|---|---|
| `confirmVehicleName` | string | Conditional | Required if vehicle has 10+ timeline entries. Must match vehicle display name. |

**Response:** 204 No Content

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | `confirmVehicleName` required but missing or doesn't match |
| 404 | not-found | Vehicle not found |

**Notes:**
- Cascading soft-delete: vehicle, all expenses, fuel entries, timeline entries, timeline photos, reminders, reminder completions, challenge participations.
- If active vehicle deleted: next vehicle (by creation date) becomes active. If no vehicles remain: no active vehicle.

---

#### VEH-06: Set Active Vehicle

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}/active` |
| **Summary** | Set a vehicle as the active/selected vehicle |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-management.md §3 Vehicle Switcher, §4 BR-11 |

**Response:** 204 No Content

**Notes:**
- Sets `isActive = true` on the specified vehicle, `isActive = false` on all others for this user.

---

#### VEH-07: Get Upload URL for Vehicle Photo

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/photo/upload-url` |
| **Summary** | Get a presigned URL to upload a vehicle photo |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | DEC-013 image storage, vehicle-management.md §4 BR-8 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `contentType` | string | Yes | `"image/jpeg"` or `"image/png"` |
| `fileSizeBytes` | integer | Yes | Max 500,000 (500 KB after client-side compression) |

**Response 200 OK:**

```json
{
  "data": {
    "uploadUrl": "https://api.carplatform.bg/uploads/...",
    "photoUrl": "https://api.carplatform.bg/photos/...",
    "expiresIn": 300
  }
}
```

**Notes:**
- Client uploads directly to `uploadUrl` via PUT.
- After successful upload, client includes `photoUrl` in the vehicle update request (VEH-04).
- MVP: upload goes to local VPS disk behind nginx. Future: Cloudflare R2.
- Presigned URL expires in 5 minutes.

---

#### VEH-08: List Vehicle Makes

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/makes` |
| **Summary** | Get the list of vehicle makes for the picker |
| **Auth** | Authenticated |
| **Source** | vehicle-management.md §3 Add/Edit Vehicle, database-schema.md §3.2 |

**Response 200 OK:**

```json
{
  "data": [
    { "id": "uuid", "name": "BMW", "isPopularBg": true },
    { "id": "uuid", "name": "Volkswagen", "isPopularBg": true },
    { "id": "uuid", "name": "Audi", "isPopularBg": true }
  ]
}
```

**Notes:**
- Sorted by `displayOrder` (popular Bulgarian makes first, then alphabetical).
- Static data — can be cached aggressively (24-hour cache header).

---

#### VEH-09: List Vehicle Models for a Make

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/makes/{makeId}/models` |
| **Summary** | Get models for a specific vehicle make |
| **Auth** | Authenticated |
| **Source** | vehicle-management.md §3 Add/Edit Vehicle |

**Query Params:**

| Param | Type | Description |
|---|---|---|
| `search` | string | Type-ahead filter on model name |

**Response 200 OK:**

```json
{
  "data": [
    { "id": "uuid", "name": "E36" },
    { "id": "uuid", "name": "E46" },
    { "id": "uuid", "name": "E90" }
  ]
}
```

**Notes:**
- Sorted by `displayOrder`.
- Static data — cache aggressively.

---

### 4.3 Expenses Module — `CarPlatform.Expenses`

Controller: `ExpensesController` — route prefix: `/api/v1/vehicles/{vehicleId}/expenses`
Controller: `ExpenseCategoriesController` — route prefix: `/api/v1/expense-categories`

---

#### EXP-01: Create Expense

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/expenses` |
| **Summary** | Log a new expense for a vehicle |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | expense-tracking.md §2 Happy Path, §4 BR-1/BR-2/BR-13 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `amountCents` | integer | Yes | 1–99,999,999 (0.01–999,999.99 in display currency) |
| `currency` | string | No | `"BGN"` (default) or `"EUR"` |
| `category` | string | Yes | Enum: `fuel`, `maintenance`, `modifications`, `insurance`, `tax`, `tires`, `parking`, `fines`, `car_wash`, `other` |
| `subcategory` | string | No | Max 50 chars |
| `date` | ISO 8601 date | No | Default: today. Not in future. Not > 5 years in past. |
| `odometerKm` | integer | No | 0–9,999,999 |
| `notes` | string | No | Max 500 chars |

**Response 201 Created:**

```json
{
  "data": {
    "id": "uuid",
    "vehicleId": "uuid",
    "amountCents": 12750,
    "currency": "BGN",
    "category": "maintenance",
    "subcategory": "Oil change",
    "date": "2026-03-22",
    "odometerKm": 155200,
    "notes": "Full synthetic at mechanic",
    "source": "manual",
    "createdAt": "2026-03-22T14:30:00Z",
    "monthlyTotalCents": 84700,
    "timelineEntryId": "uuid"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Amount <= 0, invalid category, future date |
| 404 | not-found | Vehicle not found |

**Notes:**
- Automatically creates a `timeline_entries` row linked via `expense_id` (BR-13).
- Returns `monthlyTotalCents` — the updated monthly total for the vehicle in the expense's month. This enables the client to show the "running total" update animation (Journey 2 key moment).
- Returns `timelineEntryId` so the client can navigate to the timeline entry if needed.
- Updates `asp_net_users.last_expense_at` (denormalized field for admin analytics).
- If `odometerKm` provided: updates `vehicles.current_odometer_km` if it's higher than the current value.

---

#### EXP-02: List Expenses

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/expenses` |
| **Summary** | Get paginated expenses for a vehicle, filterable by month and category |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | expense-tracking.md §3 Expense List |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `dateFrom` | date | null | Filter expenses on or after this date |
| `dateTo` | date | null | Filter expenses on or before this date |
| `category` | string | null | Filter by category enum value |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 20 | Page size (max 100) |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "amountCents": 12750,
      "currency": "BGN",
      "category": "maintenance",
      "subcategory": "Oil change",
      "date": "2026-03-22",
      "odometerKm": 155200,
      "notes": "Full synthetic",
      "source": "manual",
      "hasFuelEntry": false,
      "createdAt": "2026-03-22T14:30:00Z"
    }
  ],
  "meta": {
    "nextCursor": "eyJ...",
    "hasMore": true,
    "totalCount": 47,
    "periodTotalCents": 84700,
    "periodExpenseCount": 23
  }
}
```

**Notes:**
- Default sort: newest date first (`date DESC, created_at DESC`).
- `meta.periodTotalCents` and `meta.periodExpenseCount` are the aggregates for the current filter (e.g., "March 2026: 847 лв, 23 expenses").
- `hasFuelEntry` indicates whether a linked fuel entry exists (so client knows to show fuel details).

---

#### EXP-03: Get Expense Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/expenses/{expenseId}` |
| **Summary** | Get full details of a single expense |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | expense-tracking.md §3 Expense Detail/Edit |

**Response 200 OK:** Full expense object plus `timelineEntryId` and `fuelEntryId` (if linked).

---

#### EXP-04: Update Expense

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}/expenses/{expenseId}` |
| **Summary** | Update an existing expense |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | expense-tracking.md §2 Path E, §4 BR-11 |

**Request Body:** Same fields as EXP-01 (all optional — partial update).

**Response 200 OK:** Updated expense object.

**Notes:**
- Also updates the linked timeline entry's `amount_cents`, `date`, `category`, and `notes`.
- No confirmation dialog on API side — that's a client-side UX choice (BR-11).

---

#### EXP-05: Delete Expense

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}/expenses/{expenseId}` |
| **Summary** | Delete an expense and its linked timeline entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | expense-tracking.md §2 Path F, vehicle-timeline.md §4 BR-3 |

**Response:** 204 No Content

**Notes:**
- Cascading soft-delete: expense → linked timeline entry → linked timeline photos.
- If expense has a linked fuel entry → fuel entry is also soft-deleted.
- Updates `asp_net_users.last_expense_at` if this was the most recent expense.

---

#### EXP-06: List Expense Categories

| | |
|---|---|
| **Method + Path** | `GET /api/v1/expense-categories` |
| **Summary** | Get all expense categories with localized names and icon identifiers |
| **Auth** | Authenticated |
| **Source** | expense-tracking.md §4 BR-7, database-schema.md §3.3 expense_categories |

**Response 200 OK:**

```json
{
  "data": [
    {
      "slug": "fuel",
      "nameBg": "Гориво",
      "nameEn": "Fuel",
      "iconName": "fuel_pump",
      "displayOrder": 1
    },
    {
      "slug": "maintenance",
      "nameBg": "Поддръжка",
      "nameEn": "Maintenance",
      "iconName": "wrench",
      "displayOrder": 2
    }
  ]
}
```

**Notes:**
- Static data — cache aggressively (24-hour cache header).
- 10 fixed categories for MVP.

---

### 4.4 Fuel Module — `CarPlatform.Expenses`

Controller: `FuelEntriesController` — route prefix: `/api/v1/vehicles/{vehicleId}/fuel-entries`

---

#### FUEL-01: Create Fuel Entry

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/fuel-entries` |
| **Summary** | Log a detailed fuel entry (creates both FuelEntry and linked Expense) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §2 Happy Path, §4 BR-5 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `litersMl` | integer | Conditional | Milliliters (42.5L = 42500). Required if `totalCostCents` not provided. > 0. |
| `pricePerLiterThousandths` | integer | No | Thousandths (2.85 лв = 2850). Auto-calculated if liters + total provided. |
| `totalCostCents` | integer | Conditional | Cents. Required if `litersMl` not provided. > 0. |
| `currency` | string | No | `"BGN"` (default) or `"EUR"` |
| `odometerKm` | integer | No (strongly encouraged) | 0–9,999,999 |
| `fillType` | string | No | `"full"` (default) or `"partial"` |
| `stationName` | string | No | Max 50 chars |
| `date` | date | No | Default: today |
| `notes` | string | No | Max 500 chars |

**Response 201 Created:**

```json
{
  "data": {
    "id": "uuid",
    "expenseId": "uuid",
    "litersMl": 42500,
    "pricePerLiterThousandths": 2850,
    "totalCostCents": 12113,
    "odometerKm": 155200,
    "fillType": "full",
    "stationName": "Shell Надежда",
    "consumptionLPer100km": 8.17,
    "previousConsumption": 8.70,
    "consumptionTrend": "improved",
    "date": "2026-03-22",
    "monthlyTotalCents": 84700,
    "createdAt": "2026-03-22T14:30:00Z"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Neither `litersMl` nor `totalCostCents` provided, negative values |
| 404 | not-found | Vehicle not found |

**Notes:**
- Server performs auto-calculation: any 2 of 3 (liters, price/liter, total) → the third is calculated (BR-1).
- Creates a linked `expenses` row with `category = 'fuel'` (BR-5).
- Creates a linked `timeline_entries` row.
- Calculates `consumptionLPer100km` if `fillType = 'full'` and previous odometer exists (BR-2/BR-3).
- Updates `vehicles.current_odometer_km` if `odometerKm` > current value (BR-11).
- Returns `previousConsumption` and `consumptionTrend` (`improved`, `worsened`, `unchanged`, `first_entry`) for instant client feedback (Journey 2).

---

#### FUEL-02: List Fuel Entries

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/fuel-entries` |
| **Summary** | Get paginated fuel entries for a vehicle |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §8 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `dateFrom` | date | null | Start date filter |
| `dateTo` | date | null | End date filter |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 20 | Page size |

**Response 200 OK:** Paginated list of fuel entry objects (same shape as FUEL-01 response items).

---

#### FUEL-03: Get Fuel Entry Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/fuel-entries/{fuelEntryId}` |
| **Summary** | Get full details of a single fuel entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §8 |

**Response 200 OK:** Full fuel entry object.

---

#### FUEL-04: Update Fuel Entry

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}/fuel-entries/{fuelEntryId}` |
| **Summary** | Update a fuel entry (and its linked expense) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §6 "User edits a past fuel entry's odometer" |

**Request Body:** Same fields as FUEL-01 (all optional — partial update).

**Response 200 OK:** Updated fuel entry with recalculated consumption.

**Notes:**
- Recalculates consumption for this entry and the NEXT entry in the sequence (adjacent cascade only).
- Updates linked expense's `amount_cents` and `date` if those changed.

---

#### FUEL-05: Delete Fuel Entry

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}/fuel-entries/{fuelEntryId}` |
| **Summary** | Delete a fuel entry, its linked expense, and timeline entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §8 |

**Response:** 204 No Content

**Notes:**
- Cascading soft-delete: fuel entry → linked expense → linked timeline entry → timeline photos.
- Recalculates consumption for the next fuel entry (if the deleted entry was used in the calculation).

---

#### FUEL-06: Get Consumption Trend

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/fuel-entries/consumption-trend` |
| **Summary** | Get fuel consumption over time for trend display |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | fuel-entry.md §8, cost-dashboard.md (consumption trend — premium) |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `months` | int | 6 | Number of months of data to return |

**Response 200 OK:**

```json
{
  "data": {
    "entries": [
      { "date": "2026-03-22", "consumptionLPer100km": 8.17 },
      { "date": "2026-03-08", "consumptionLPer100km": 8.70 }
    ],
    "averageConsumption": 8.44,
    "bestConsumption": 7.90,
    "worstConsumption": 9.20
  }
}
```

**Notes:**
- Only includes entries where consumption was calculated (full fills with odometer data).
- Basic consumption data is free. Detailed trend charts are premium (cost-dashboard.md BR-5 notes cost/km is premium; basic L/100km feedback is free per fuel-entry.md OQ-1).

---

### 4.5 Dashboard Module — `CarPlatform.Dashboard`

Controller: `DashboardController` — route prefix: `/api/v1/vehicles/{vehicleId}/dashboard`

---

#### DASH-01: Get Monthly Summary

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/dashboard/monthly` |
| **Summary** | Get monthly cost total and category breakdown |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | cost-dashboard.md §2/§3/§4 BR-1/BR-2/BR-4 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `month` | integer | Current month | 1–12 |
| `year` | integer | Current year | Year |

**Response 200 OK:**

```json
{
  "data": {
    "vehicleId": "uuid",
    "month": 3,
    "year": 2026,
    "totalCents": 84700,
    "currency": "BGN",
    "expenseCount": 23,
    "annualizedCents": 1016400,
    "previousMonthTotalCents": 75200,
    "monthOverMonthDeltaCents": 9500,
    "monthOverMonthDeltaPercent": 12.6,
    "yearToDateTotalCents": 235400,
    "categories": [
      {
        "category": "fuel",
        "totalCents": 38000,
        "percentage": 44.9,
        "expenseCount": 8
      },
      {
        "category": "maintenance",
        "totalCents": 25000,
        "percentage": 29.5,
        "expenseCount": 3
      }
    ]
  }
}
```

**Notes:**
- `annualizedCents` = `totalCents * 12` (BR-2/BR-3 — free feature, simple multiplication).
- `previousMonthTotalCents` and delta fields only populated when previous month has data.
- `yearToDateTotalCents` = sum of all expenses for this vehicle in the current year.
- Categories sorted by `totalCents` descending. Only categories with expenses are included (BR-4).

---

#### DASH-02: Get Monthly Trend

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/dashboard/trend` |
| **Summary** | Get monthly totals for the last N months (bar chart data) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | cost-dashboard.md §3 Monthly Trend Section, §4 BR-9 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `months` | int | 6 | Number of months (max 12) |

**Response 200 OK:**

```json
{
  "data": {
    "months": [
      { "month": 3, "year": 2026, "totalCents": 84700, "expenseCount": 23 },
      { "month": 2, "year": 2026, "totalCents": 75200, "expenseCount": 19 },
      { "month": 1, "year": 2026, "totalCents": 75500, "expenseCount": 21 }
    ]
  }
}
```

---

#### DASH-03: Get Cost Per Kilometer ⭐ PREMIUM

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/dashboard/cost-per-km` |
| **Summary** | Calculate cost per kilometer for the vehicle |
| **Auth** | Authenticated + **Premium** |
| **Source** | cost-dashboard.md §4 BR-5, Journey 5 (35% of conversions) |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `period` | string | `"month"` | `"month"`, `"quarter"`, `"year"`, `"all"` |

**Response 200 OK:**

```json
{
  "data": {
    "costPerKmCents": 42,
    "totalCostCents": 84700,
    "totalKm": 2017,
    "period": "month",
    "previousPeriodCostPerKmCents": 38,
    "trend": "worsened"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 403 | premium-required | User is on free tier |
| 422 | business-rule | Insufficient odometer data to calculate |

---

#### DASH-04: Get Spending Forecast ⭐ PREMIUM

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/dashboard/forecast` |
| **Summary** | Predict next month's spending based on historical patterns |
| **Auth** | Authenticated + **Premium** |
| **Source** | cost-dashboard.md §3 Premium Sections, §4 BR-11 |

**Response 200 OK:**

```json
{
  "data": {
    "forecastCents": 90000,
    "confidence": "medium",
    "basedOnMonths": 3,
    "breakdown": [
      { "category": "fuel", "forecastCents": 40000 },
      { "category": "maintenance", "forecastCents": 25000 }
    ]
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 403 | premium-required | Free tier user |
| 422 | business-rule | Less than 3 months of data |

---

#### DASH-05: Get Milestone Status

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/dashboard/milestones` |
| **Summary** | Check for unshown expense milestones (10, 25, 50, 100) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | cost-dashboard.md §3 Aha Moment Amplification, §4 BR-14 |

**Response 200 OK:**

```json
{
  "data": {
    "totalExpenses": 52,
    "unshownMilestones": [50],
    "nextMilestone": 100
  }
}
```

**Notes:**
- Client calls this on dashboard load. If `unshownMilestones` is non-empty, shows celebration.
- After showing, client calls `PUT /api/v1/users/me` with the milestone flag to mark as shown.

---

### 4.6 Timeline Module — `CarPlatform.Timeline`

Controller: `TimelineController` — route prefix: `/api/v1/vehicles/{vehicleId}/timeline`

---

#### TL-01: List Timeline Entries

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/timeline` |
| **Summary** | Get paginated timeline entries (newest first), filterable by category |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §3 Timeline Feed, §4 BR-7/BR-8 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `category` | string | null | Filter by category (expense categories + `"event"`) |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 20 | Page size (max 100) |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "vehicleId": "uuid",
      "expenseId": "uuid",
      "category": "modifications",
      "description": "Installed BBS RS wheels 17-inch",
      "amountCents": 120000,
      "currency": "BGN",
      "date": "2026-03-20",
      "odometerKm": 155000,
      "notes": "Purchased from BBS Bulgaria.",
      "createdBy": "owner",
      "photos": [
        { "id": "uuid", "photoUrl": "https://...", "sortOrder": 0 },
        { "id": "uuid", "photoUrl": "https://...", "sortOrder": 1 }
      ],
      "createdAt": "2026-03-20T16:00:00Z"
    }
  ],
  "meta": { "nextCursor": "eyJ...", "hasMore": true, "totalCount": 142 }
}
```

**Notes:**
- Photos included inline (max 3 per entry — small payload).
- Default sort: `date DESC, created_at DESC`.
- Supports infinite scroll on mobile.

---

#### TL-02: Create Timeline Entry

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/timeline` |
| **Summary** | Create a standalone timeline entry (not linked to an expense) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §2 Path B, §3 New/Edit Timeline Entry |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `category` | string | Yes | Expense categories + `"event"` |
| `description` | string | No | Max 200 chars |
| `amountCents` | integer | No | >= 0 if provided |
| `currency` | string | No | `"BGN"` (default) or `"EUR"` |
| `date` | date | No | Default: today. Not in future. Not > 5 years in past. |
| `odometerKm` | integer | No | 0–9,999,999 |
| `notes` | string | No | Max 1000 chars |

**Response 201 Created:** Timeline entry object.

**Notes:**
- If `amountCents` is provided and `category` is not `"event"`, also creates a linked `expenses` row automatically. The timeline entry becomes expense-linked.
- If `category = "event"`, no expense is created regardless of amount.

---

#### TL-03: Get Timeline Entry Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/timeline/{entryId}` |
| **Summary** | Get full details of a timeline entry including photos |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §3 Timeline Entry Detail |

**Response 200 OK:** Full timeline entry with photos array.

---

#### TL-04: Update Timeline Entry

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}/timeline/{entryId}` |
| **Summary** | Update a timeline entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §3 New/Edit Timeline Entry |

**Request Body:** Same fields as TL-02 (all optional — partial update).

**Response 200 OK:** Updated entry.

**Notes:**
- If expense-linked: also updates the linked expense's `amount_cents`, `category`, `date`, `notes`.

---

#### TL-05: Delete Timeline Entry

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}/timeline/{entryId}` |
| **Summary** | Delete a timeline entry (cascades to linked expense if any) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §4 BR-3 |

**Response:** 204 No Content

**Notes:**
- If `expense_id` is set: also deletes the linked expense (and linked fuel entry, if any).
- Deletes all associated `timeline_photos`.

---

#### TL-06: Get Upload URL for Timeline Photo

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/timeline/{entryId}/photos/upload-url` |
| **Summary** | Get a presigned URL to upload a photo for a timeline entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §4 BR-4/BR-5/BR-6, DEC-013 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `contentType` | string | Yes | `"image/jpeg"` or `"image/png"` |
| `fileSizeBytes` | integer | Yes | Max 500,000 (500 KB) |

**Response 200 OK:**

```json
{
  "data": {
    "uploadUrl": "https://...",
    "photoUrl": "https://...",
    "expiresIn": 300,
    "photoId": "uuid"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 422 | business-rule | Entry already has 3 photos (BR-5: max 3 per entry) |
| 422 | business-rule | Free tier: 5 total photos reached. Body includes `maxPhotos`, `currentCount`, `upgradeRequired: true`. |

**Notes:**
- After successful upload to `uploadUrl`, client calls `POST .../photos/confirm` to finalize.

---

#### TL-07: Confirm Photo Upload

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/timeline/{entryId}/photos/{photoId}/confirm` |
| **Summary** | Confirm that a photo was successfully uploaded and add it to the entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | DEC-013 |

**Response 200 OK:** Photo object with `id`, `photoUrl`, `sortOrder`.

---

#### TL-08: Delete Photo

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}/timeline/{entryId}/photos/{photoId}` |
| **Summary** | Delete a photo from a timeline entry |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §6 "Free user has exactly 5 photos and deletes one" |

**Response:** 204 No Content

**Notes:**
- Decrements the user's total photo count. Free users regain a slot.

---

#### TL-09: Search Timeline

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/timeline/search` |
| **Summary** | Free-text search through timeline entries |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | vehicle-timeline.md §4 BR-12 |

**Query Params:**

| Param | Type | Required | Description |
|---|---|---|---|
| `q` | string | Yes | Search query (min 2 chars) |
| `cursor` | string | No | Pagination cursor |
| `limit` | int | No | Page size (default 20) |

**Response 200 OK:** Paginated list of matching timeline entries (same shape as TL-01).

**Notes:**
- Searches `description` and `notes` fields.
- Uses PostgreSQL `pg_trgm` for fuzzy matching.
- Available after 20+ entries (BR-12).

---

### 4.7 Reminders Module — `CarPlatform.Reminders`

Controller: `RemindersController` — route prefix: `/api/v1/vehicles/{vehicleId}/reminders`

---

#### REM-01: List Reminders

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/reminders` |
| **Summary** | Get all reminders for a vehicle, sorted by urgency |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §3 Reminders List |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "vehicleId": "uuid",
      "name": "Oil Change",
      "triggerType": "mileage",
      "nextDueDate": null,
      "nextDueMileageKm": 165000,
      "repeatIntervalDays": null,
      "mileageIntervalKm": 10000,
      "status": "active",
      "urgency": "due_soon",
      "distanceRemainingKm": 500,
      "lastCompletedDate": "2025-12-15",
      "lastCompletedOdometerKm": 145000,
      "snoozeCount": 0,
      "notes": null,
      "createdAt": "2025-12-15T10:00:00Z"
    }
  ],
  "meta": {
    "totalCount": 3,
    "overdueCount": 0,
    "dueSoonCount": 1,
    "freeLimit": 3,
    "currentCount": 3
  }
}
```

**Notes:**
- Sorted by urgency: overdue → due soon → all good.
- `urgency` is calculated server-side: `"overdue"` (past due date or mileage), `"due_soon"` (within 30 days or 1,000 km), `"all_good"`.
- `distanceRemainingKm` calculated from `vehicles.current_odometer_km` vs. `next_due_mileage_km`.
- `meta.freeLimit` and `meta.currentCount` for free tier display.

---

#### REM-02: Create Reminder

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/reminders` |
| **Summary** | Create a new maintenance reminder |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §3 New/Edit Reminder, §4 BR-1 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `name` | string | Yes | Max 100 chars |
| `triggerType` | string | Yes | `"date"`, `"mileage"`, `"combined"` |
| `nextDueDate` | date | Conditional | Required if `date` or `combined`. Must be in the future. |
| `repeatIntervalDays` | integer | No | > 0. Null = one-time. |
| `mileageIntervalKm` | integer | Conditional | Required if `mileage` or `combined`. > 0. |
| `nextDueMileageKm` | integer | Conditional | Required if `mileage` or `combined`. > current odometer. |
| `notificationDaysBefore` | string | No | Comma-separated days. Default: `"30,7,1"` |
| `notificationKmBefore` | string | No | Comma-separated km. Default: `"1000,500"` |
| `notes` | string | No | Max 500 chars |

**Response 201 Created:** Reminder object.

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 400 | validation | Missing required trigger fields, invalid values |
| 422 | business-rule | Free tier: 3 active reminders reached. Body includes `maxReminders`, `currentCount`, `upgradeRequired: true`. |

---

#### REM-03: Get Reminder Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/reminders/{reminderId}` |
| **Summary** | Get full reminder details including completion history |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §3 Reminder Detail |

**Response 200 OK:** Reminder object plus:

```json
{
  "data": {
    "...reminder fields...",
    "completionHistory": [
      {
        "id": "uuid",
        "completedDate": "2025-12-15",
        "odometerKm": 145000,
        "expenseId": "uuid",
        "expenseAmountCents": 15000
      }
    ],
    "averageCostCents": 14500,
    "completionCount": 3
  }
}
```

**Notes:**
- `completionHistory` shows last 3 completions (maintenance-reminders.md OQ-3).
- `averageCostCents` calculated from linked expenses in completion history.

---

#### REM-04: Update Reminder

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/vehicles/{vehicleId}/reminders/{reminderId}` |
| **Summary** | Update reminder settings |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §3 New/Edit Reminder |

**Request Body:** Same as REM-02 (all optional — partial update).

**Response 200 OK:** Updated reminder.

---

#### REM-05: Delete Reminder

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/vehicles/{vehicleId}/reminders/{reminderId}` |
| **Summary** | Delete a reminder |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §6 |

**Response:** 204 No Content

---

#### REM-06: Complete Reminder

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/reminders/{reminderId}/complete` |
| **Summary** | Mark a reminder as complete, optionally log expense |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §2 Happy Path steps 8-14, §3 Completion Dialog |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `completedDate` | date | No | Default: today |
| `odometerKm` | integer | No | Updates vehicle odometer |
| `logExpense` | boolean | No | Default: false. If true, `expenseAmountCents` required. |
| `expenseAmountCents` | integer | Conditional | Required if `logExpense = true`. > 0. |
| `expenseNotes` | string | No | Max 500 chars |

**Response 200 OK:**

```json
{
  "data": {
    "reminderId": "uuid",
    "completionId": "uuid",
    "nextDueDate": "2027-03-22",
    "nextDueMileageKm": 165200,
    "expenseId": "uuid",
    "timelineEntryId": "uuid"
  }
}
```

**Notes:**
- Creates `reminder_completions` row.
- If `logExpense = true`: creates expense with `category = 'maintenance'`, `subcategory = reminder name`, `source = 'reminder_completion'`. Also creates timeline entry.
- Auto-schedules next cycle: adds `repeatIntervalDays` to date, adds `mileageIntervalKm` to current odometer (BR-6).
- Resets `snoozeCount` to 0.
- Updates `vehicles.current_odometer_km` if `odometerKm` provided (BR-16).

---

#### REM-07: Snooze Reminder

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/reminders/{reminderId}/snooze` |
| **Summary** | Snooze a reminder (postpone notification) |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | maintenance-reminders.md §2 Path C, §4 BR-7/BR-8 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `snoozeType` | string | Yes | `"1_week"` or `"500_km"` |

**Response 200 OK:**

```json
{
  "data": {
    "reminderId": "uuid",
    "snoozeCount": 2,
    "maxSnoozes": 3,
    "snoozedUntilDate": "2026-03-29",
    "snoozedUntilKm": null
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 422 | business-rule | Already snoozed 3 times (BR-8). Body includes prompt to reschedule or remove. |

---

#### REM-08: Get Reminder Templates

| | |
|---|---|
| **Method + Path** | `GET /api/v1/reminders/templates` |
| **Summary** | Get suggested reminder templates for quick setup |
| **Auth** | Authenticated |
| **Source** | maintenance-reminders.md §2 Path H, §3 Reminders List empty state |

**Response 200 OK:**

```json
{
  "data": [
    {
      "name": "Oil Change",
      "triggerType": "mileage",
      "mileageIntervalKm": 10000,
      "repeatIntervalDays": null,
      "iconName": "oil",
      "descriptionBg": "Смяна на масло",
      "descriptionEn": "Oil change"
    },
    {
      "name": "Insurance Renewal",
      "triggerType": "date",
      "mileageIntervalKm": null,
      "repeatIntervalDays": 365,
      "iconName": "shield",
      "descriptionBg": "Подновяване на застраховка",
      "descriptionEn": "Insurance renewal"
    }
  ]
}
```

**Notes:**
- Static data — cache aggressively.
- Templates are suggestions, not entities. Client pre-fills the create form.

---

### 4.8 Challenges Module — `CarPlatform.Challenges`

> **Note:** Challenges are deferred to v2. These endpoints are not implemented in MVP.

Controller: `ChallengesController` — route prefix: `/api/v1/challenges`

---

#### CHAL-01: List Challenges

| | |
|---|---|
| **Method + Path** | `GET /api/v1/challenges` |
| **Summary** | Get active and recently completed challenges |
| **Auth** | Authenticated |
| **Source** | challenges-gamification.md §3 Challenge Hub, §4 BR-3 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `status` | string | null | `"active"`, `"completed"`, `"upcoming"` |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Lowest Cost per km",
      "description": "Track your expenses and aim for the lowest cost per kilometer this month.",
      "metricType": "cost_per_km",
      "month": 3,
      "year": 2026,
      "status": "active",
      "participantCount": 147,
      "minParticipants": 50,
      "daysRemaining": 9,
      "myParticipation": {
        "isJoined": true,
        "currentRank": 47,
        "currentMetricValue": 0.38,
        "vehicleId": "uuid"
      }
    }
  ]
}
```

**Notes:**
- `myParticipation` is null if user hasn't joined, shows theoretical rank if free user.
- Challenges with < `minParticipants` show as "Coming soon" (BR-5).

---

#### CHAL-02: Get Challenge Details + Leaderboard

| | |
|---|---|
| **Method + Path** | `GET /api/v1/challenges/{challengeId}` |
| **Summary** | Get challenge details and paginated leaderboard |
| **Auth** | Authenticated |
| **Source** | challenges-gamification.md §3 Challenge Detail |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `leaderboardLimit` | int | 20 | Number of leaderboard entries |
| `leaderboardCursor` | string | null | Pagination cursor for leaderboard |

**Response 200 OK:**

```json
{
  "data": {
    "id": "uuid",
    "name": "Lowest Cost per km",
    "description": "...",
    "metricType": "cost_per_km",
    "month": 3,
    "year": 2026,
    "status": "active",
    "participantCount": 147,
    "daysRemaining": 9,
    "howItWorks": "Total expenses divided by total km driven this month. Lower is better.",
    "myParticipation": {
      "isJoined": true,
      "currentRank": 47,
      "currentMetricValue": 0.38,
      "distanceToNextRank": 0.02,
      "vehicleId": "uuid"
    },
    "leaderboard": [
      { "rank": 1, "displayName": "Driver_1234", "metricValue": 0.22, "isCurrentUser": false },
      { "rank": 2, "displayName": "Driver_5678", "metricValue": 0.25, "isCurrentUser": false }
    ],
    "leaderboardMeta": { "nextCursor": "eyJ...", "hasMore": true }
  }
}
```

**Notes:**
- Free users see the full leaderboard but their rank is marked as "theoretical" (not joined).
- Leaderboard shows anonymous display names (BR-2).

---

#### CHAL-03: Join Challenge ⭐ PREMIUM

| | |
|---|---|
| **Method + Path** | `POST /api/v1/challenges/{challengeId}/join` |
| **Summary** | Join a challenge with a specific vehicle |
| **Auth** | Authenticated + **Premium** |
| **Source** | challenges-gamification.md §2 Happy Path step 5, §4 BR-1 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `vehicleId` | string (UUID) | Yes | Must be user's own vehicle |

**Response 201 Created:**

```json
{
  "data": {
    "challengeId": "uuid",
    "vehicleId": "uuid",
    "currentRank": 47,
    "currentMetricValue": 0.38,
    "joinedAt": "2026-03-22T14:00:00Z"
  }
}
```

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 403 | premium-required | Free tier user |
| 409 | conflict | Already joined this challenge |
| 422 | business-rule | Challenge not active, or insufficient data for the metric |

---

#### CHAL-04: Get My Challenge Rank

| | |
|---|---|
| **Method + Path** | `GET /api/v1/challenges/{challengeId}/rank` |
| **Summary** | Get user's current rank and metric for a specific challenge |
| **Auth** | Authenticated |
| **Source** | challenges-gamification.md §8 |

**Response 200 OK:**

```json
{
  "data": {
    "currentRank": 47,
    "currentMetricValue": 0.38,
    "previousRank": 52,
    "rankChange": 5,
    "distanceToNextRank": 0.02,
    "isJoined": true
  }
}
```

---

### 4.9 Subscriptions Module — `CarPlatform.Subscriptions`

Controller: `SubscriptionController` — route prefix: `/api/v1/subscription`
Controller: `WebhooksController` — route prefix: `/api/v1/webhooks`

---

#### SUB-01: Get Subscription Status

| | |
|---|---|
| **Method + Path** | `GET /api/v1/subscription/status` |
| **Summary** | Get current subscription status and history |
| **Auth** | Authenticated |
| **Source** | DEC-016, cost-dashboard.md (premium gating), Journey 5 |

**Response 200 OK:**

```json
{
  "data": {
    "tier": "premium",
    "plan": "monthly",
    "status": "active",
    "startedAt": "2026-02-15T00:00:00Z",
    "expiresAt": "2026-04-15T00:00:00Z",
    "autoRenew": true,
    "features": {
      "unlimitedVehicles": true,
      "unlimitedReminders": true,
      "unlimitedPhotos": true,
      "costPerKm": true,
      "spendingTrends": true,
      "spendingForecast": true,
      "modelBenchmarks": true,
      "challengeParticipation": true
    }
  }
}
```

**Notes:**
- `features` object tells the client exactly what's unlocked — client doesn't need to hardcode which features are premium.

---

#### SUB-02: Get Available Plans

| | |
|---|---|
| **Method + Path** | `GET /api/v1/subscription/plans` |
| **Summary** | Get available subscription plans for the paywall display |
| **Auth** | Authenticated |
| **Source** | Journey 5, monetization-plan.md |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "premium_monthly",
      "name": "Monthly",
      "priceCents": 299,
      "currency": "EUR",
      "interval": "month",
      "features": ["Unlimited vehicles", "Unlimited reminders", "Cost per km", "..."]
    },
    {
      "id": "premium_annual",
      "name": "Annual",
      "priceCents": 2499,
      "currency": "EUR",
      "interval": "year",
      "savingsPercent": 30,
      "features": ["Same as monthly"]
    }
  ]
}
```

**Notes:**
- Prices are from RevenueCat configuration. API returns them for the paywall UI.
- Actual purchase happens through RevenueCat SDK on the client side.

---

#### SUB-03: RevenueCat Webhook

| | |
|---|---|
| **Method + Path** | `POST /api/v1/webhooks/revenuecat` |
| **Summary** | Handle subscription events from RevenueCat |
| **Auth** | Webhook signature verification (shared secret) |
| **Source** | DEC-016, architecture.md §4.3 |

**Request Body:** RevenueCat webhook payload (see RevenueCat docs).

**Response:** 200 OK (always — even on errors, to prevent retry storms)

**Notes:**
- Deduplication by `revenuecat_event_id` (UNIQUE constraint).
- Creates `subscription_history` row for each event.
- Events handled: `INITIAL_PURCHASE`, `RENEWAL`, `CANCELLATION`, `EXPIRATION`, `PRODUCT_CHANGE`, `BILLING_ISSUE`.
- On status change: next token refresh for the user will include updated `subscriptionTier` claim.

---

### 4.10 Share & Export — `CarPlatform.Vehicles`

(Endpoints live in the Vehicles module since they're vehicle-scoped.)

---

#### SHARE-01: Get Vehicle Card Data

| | |
|---|---|
| **Method + Path** | `GET /api/v1/vehicles/{vehicleId}/share-card` |
| **Summary** | Get all data needed to generate a shareable vehicle card |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | share-export.md §5 Data Requirements |

**Response 200 OK:**

```json
{
  "data": {
    "vehicle": {
      "make": "BMW",
      "model": "E46 320i",
      "year": 2004,
      "nickname": "My E46",
      "photoUrl": "https://..."
    },
    "stats": {
      "totalAmountCents": 487500,
      "expenseCount": 47,
      "monthsTracked": 3,
      "topModification": "BBS RS wheels 17-inch",
      "topModificationPhotoUrl": "https://...",
      "costPerKmCents": 42
    },
    "isPremium": true,
    "brandTagline": "Track your car's journey",
    "appDownloadUrl": "https://carplatform.bg/download"
  }
}
```

**Notes:**
- `costPerKmCents` only included for premium users (BR-9).
- `topModification` pulled from the most recent timeline entry with photos in the "modifications" category.
- Vehicle card is a FREE feature — never gated (BR-1).

---

#### SHARE-02: Generate Vehicle Card Image (Server-Side)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/vehicles/{vehicleId}/share-card/generate` |
| **Summary** | Generate a shareable vehicle card image on the server |
| **Auth** | Authenticated (own vehicles only) |
| **Source** | share-export.md §4 BR-7 |

**Response 200 OK:** Binary image response (`image/png`), 1080x1350 pixels.

**Error Cases:**

| Status | Code | When |
|---|---|---|
| 422 | business-rule | No expenses logged (BR-2: don't generate empty cards) |

**Notes:**
- Alternative: client-side generation (decision TBD per OQ-1).
- If server-side: image rendered by server using a template engine.
- Response includes `Content-Type: image/png` and `Content-Disposition: inline`.

---

### 4.11 Admin Module — `CarPlatform.Admin`

Controller: `AdminUsersController` — route prefix: `/api/v1/admin/users`
Controller: `AdminAnalyticsController` — route prefix: `/api/v1/admin/analytics`
Controller: `AdminSubscriptionsController` — route prefix: `/api/v1/admin/subscriptions`
Controller: `AdminChallengesController` — route prefix: `/api/v1/admin/challenges`

All admin endpoints require `[Authorize(Roles = "admin")]`.

---

#### ADMIN-01: List Users

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/users` |
| **Summary** | Get paginated list of all users |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `search` | string | null | Search by email or display name |
| `status` | string | null | `"active"`, `"disabled"`, `"churned"` |
| `subscriptionTier` | string | null | `"free"`, `"premium"` |
| `sortBy` | string | `"createdAt"` | `"createdAt"`, `"lastActivityAt"`, `"lastExpenseAt"` |
| `sortOrder` | string | `"desc"` | `"asc"`, `"desc"` |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 50 | Page size (max 100) |

**Response 200 OK:**

```json
{
  "data": [
    {
      "id": "uuid",
      "email": "user@example.com",
      "displayName": "Иван",
      "role": "driver",
      "subscriptionTier": "premium",
      "vehicleCount": 2,
      "expenseCount": 47,
      "lastActivityAt": "2026-03-22T14:30:00Z",
      "lastExpenseAt": "2026-03-22T14:30:00Z",
      "createdAt": "2026-01-15T10:30:00Z",
      "isDisabled": false
    }
  ],
  "meta": { "nextCursor": "eyJ...", "hasMore": true, "totalCount": 1247 }
}
```

---

#### ADMIN-02: Get User Details

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/users/{userId}` |
| **Summary** | Get detailed info for a specific user |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 |

**Response 200 OK:** User profile + vehicles summary + subscription history + activity summary. Does NOT include individual expense amounts (privacy).

---

#### ADMIN-03: Disable/Enable User

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/admin/users/{userId}/status` |
| **Summary** | Disable or enable a user account |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `isDisabled` | boolean | Yes |
| `reason` | string | No |

**Response:** 204 No Content

---

#### ADMIN-04: Trigger User Data Export (GDPR)

| | |
|---|---|
| **Method + Path** | `POST /api/v1/admin/users/{userId}/data-export` |
| **Summary** | Trigger a GDPR data export for a user |
| **Auth** | Admin |
| **Source** | architecture.md §7.1, PRD GDPR |

**Response 202 Accepted:** Export processing started.

---

#### ADMIN-05: Delete User Account (GDPR)

| | |
|---|---|
| **Method + Path** | `DELETE /api/v1/admin/users/{userId}` |
| **Summary** | Permanently delete a user's account (GDPR right to erasure) |
| **Auth** | Admin |
| **Source** | architecture.md §7.1, PRD GDPR |

**Response:** 204 No Content

---

#### ADMIN-06: Analytics Dashboard

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/analytics/dashboard` |
| **Summary** | Get core metrics for the admin analytics dashboard |
| **Auth** | Admin |
| **Source** | architecture.md §7.2 |

**Response 200 OK:**

```json
{
  "data": {
    "dau": 234,
    "wau": 567,
    "mau": 1247,
    "newUsersToday": 12,
    "newUsersThisWeek": 78,
    "newUsersThisMonth": 312,
    "totalUsers": 3456,
    "premiumSubscribers": 89,
    "premiumConversionRate": 7.1,
    "avgExpensesPerActiveUserPerWeek": 3.2,
    "usersByState": {
      "dailyActive": 234,
      "weeklyActive": 567,
      "monthlyActive": 1247,
      "atRisk": 156,
      "churned": 423
    },
    "cachedAt": "2026-03-22T14:00:00Z",
    "cacheExpiresAt": "2026-03-22T14:15:00Z"
  }
}
```

**Notes:**
- Cached for 5-15 minutes to avoid expensive aggregation queries on every load.

---

#### ADMIN-07: Retention Cohorts

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/analytics/retention` |
| **Summary** | Get retention cohort data |
| **Auth** | Admin |
| **Source** | architecture.md §7.2 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `months` | int | 6 | Number of cohort months to include |

**Response 200 OK:** Retention cohort matrix (signup month × retention period).

---

#### ADMIN-08: List Subscribers

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/subscriptions` |
| **Summary** | Get paginated list of all premium subscribers |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 |

**Query Params:**

| Param | Type | Default | Description |
|---|---|---|---|
| `status` | string | null | `"active"`, `"cancelled"`, `"expired"`, `"grace_period"` |
| `plan` | string | null | `"monthly"`, `"annual"` |
| `cursor` | string | null | Pagination cursor |
| `limit` | int | 50 | Page size |

**Response 200 OK:** Paginated subscriber list.

---

#### ADMIN-09: Revenue Summary

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/subscriptions/revenue` |
| **Summary** | Get revenue metrics for admin dashboard |
| **Auth** | Admin |
| **Source** | architecture.md §7.2 Revenue Metrics |

**Response 200 OK:**

```json
{
  "data": {
    "mrr": 26611,
    "currency": "EUR",
    "totalSubscribers": 89,
    "monthlySubscribers": 54,
    "annualSubscribers": 35,
    "churnRatePercent": 4.2,
    "arpu": 2134
  }
}
```

---

#### ADMIN-10: Manual Subscription Override

| | |
|---|---|
| **Method + Path** | `POST /api/v1/admin/subscriptions/{userId}/override` |
| **Summary** | Manually grant or revoke premium for a user |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `action` | string | Yes | `"grant"` or `"revoke"` |
| `reason` | string | Yes | Max 200 chars (e.g., "Beta tester", "Support case #123") |
| `expiresAt` | date | No | When grant expires (null = indefinite) |

**Response:** 204 No Content

**Notes:**
- Creates `subscription_history` row with `event_type = 'manual_grant'` or `'manual_revoke'`.

---

#### ADMIN-11: List Challenges (Admin)

| | |
|---|---|
| **Method + Path** | `GET /api/v1/admin/challenges` |
| **Summary** | List all challenges with management details |
| **Auth** | Admin |
| **Source** | architecture.md §7.1 Post-Launch: Challenge management |

**Response 200 OK:** All challenges with participant counts, statuses, and management actions available.

---

#### ADMIN-12: Create Challenge

| | |
|---|---|
| **Method + Path** | `POST /api/v1/admin/challenges` |
| **Summary** | Create a new monthly challenge |
| **Auth** | Admin |
| **Source** | challenges-gamification.md §5 |

**Request Body:**

| Field | Type | Required | Validation |
|---|---|---|---|
| `name` | string | Yes | Max 100 chars |
| `description` | string | Yes | Max 500 chars |
| `metricType` | string | Yes | `"cost_per_km"`, `"fuel_efficiency"`, `"expense_count"` |
| `month` | int | Yes | 1–12 |
| `year` | int | Yes | >= current year |
| `minParticipants` | int | No | Default: 50 |

**Response 201 Created:** Challenge object.

---

#### ADMIN-13: Update Challenge Status

| | |
|---|---|
| **Method + Path** | `PUT /api/v1/admin/challenges/{challengeId}` |
| **Summary** | Update challenge details or status |
| **Auth** | Admin |
| **Source** | challenges-gamification.md |

**Request Body:**

| Field | Type | Required |
|---|---|---|
| `name` | string | No |
| `description` | string | No |
| `status` | string | No | `"upcoming"`, `"active"`, `"completed"` |
| `minParticipants` | int | No |

**Response 200 OK:** Updated challenge.

---

### 4.12 Health — `CarPlatform.API`

Controller: `HealthController` — route prefix: `/api/v1`

---

#### HEALTH-01: Health Check

| | |
|---|---|
| **Method + Path** | `GET /api/v1/health` |
| **Summary** | API health check |
| **Auth** | Public |

**Response 200 OK:**

```json
{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2026-03-22T14:30:00Z"
}
```

---

## 5. DTO Reference

### 5.1 Auth DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `RegisterRequest` | Auth | `email`, `password`, `displayName?`, `locale?`, `currencyPreference?` | Yes |
| `LoginRequest` | Auth | `email`, `password` | Yes |
| `OAuthRequest` | Auth | `idToken`, `accessToken?` | Yes |
| `RefreshRequest` | Auth | `refreshToken` | Yes |
| `LogoutRequest` | Auth | `refreshToken` | Yes |
| `PasswordResetRequest` | Auth | `email` | Yes |
| `PasswordResetConfirmRequest` | Auth | `token`, `newPassword` | Yes |
| `AuthResponse` | Auth | `userId`, `email`, `displayName`, `role`, `subscriptionTier`, `onboardingCompleted`, `accessToken`, `refreshToken`, `expiresIn`, `isNewUser?` | Yes |
| `TokenResponse` | Auth | `accessToken`, `refreshToken`, `expiresIn` | Yes |
| `UserProfileResponse` | Auth | Full user profile with settings and stats | Yes |
| `UpdateProfileRequest` | Auth | `displayName?`, `locale?`, `currencyPreference?`, `distanceUnit?`, `notificationQuietStart?`, `notificationQuietEnd?` | Yes |
| `DeviceRegistrationRequest` | Auth | `fcmToken`, `platform`, `deviceName?` | No (mobile only) |
| `DeviceResponse` | Auth | `id`, `platform`, `deviceName`, `isActive`, `createdAt` | No |

### 5.2 Vehicles DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `CreateVehicleRequest` | Vehicles | `make`, `model`, `year`, `fuelType?`, `licensePlate?`, `currentOdometerKm?`, `nickname?`, `photoUrl?` | Yes |
| `UpdateVehicleRequest` | Vehicles | Same as create, all optional | Yes |
| `VehicleResponse` | Vehicles | Full vehicle fields + `stats` object | Yes |
| `VehicleListItemResponse` | Vehicles | Compact vehicle with stats for switcher | Yes |
| `VehicleMakeResponse` | Vehicles | `id`, `name`, `isPopularBg` | Yes |
| `VehicleModelResponse` | Vehicles | `id`, `name` | Yes |
| `PhotoUploadUrlRequest` | Vehicles | `contentType`, `fileSizeBytes` | No (mobile only) |
| `PhotoUploadUrlResponse` | Vehicles | `uploadUrl`, `photoUrl`, `expiresIn`, `photoId?` | No |
| `DeleteVehicleRequest` | Vehicles | `confirmVehicleName?` | Yes |

### 5.3 Expenses DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `CreateExpenseRequest` | Expenses | `amountCents`, `currency?`, `category`, `subcategory?`, `date?`, `odometerKm?`, `notes?` | Yes |
| `UpdateExpenseRequest` | Expenses | Same as create, all optional | Yes |
| `ExpenseResponse` | Expenses | Full expense + `monthlyTotalCents`, `timelineEntryId`, `hasFuelEntry` | Yes |
| `ExpenseListItemResponse` | Expenses | Compact expense for lists | Yes |
| `ExpenseCategoryResponse` | Expenses | `slug`, `nameBg`, `nameEn`, `iconName`, `displayOrder` | Yes |

### 5.4 Fuel DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `CreateFuelEntryRequest` | Expenses | `litersMl?`, `pricePerLiterThousandths?`, `totalCostCents?`, `currency?`, `odometerKm?`, `fillType?`, `stationName?`, `date?`, `notes?` | Yes |
| `UpdateFuelEntryRequest` | Expenses | Same, all optional | Yes |
| `FuelEntryResponse` | Expenses | Full fuel entry + `consumptionLPer100km?`, `previousConsumption?`, `consumptionTrend`, `monthlyTotalCents`, `expenseId` | Yes |
| `ConsumptionTrendResponse` | Expenses | `entries[]`, `averageConsumption`, `bestConsumption`, `worstConsumption` | Yes |

### 5.5 Dashboard DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `MonthlySummaryResponse` | Dashboard | `totalCents`, `expenseCount`, `annualizedCents`, `previousMonthTotalCents`, deltas, `yearToDateTotalCents`, `categories[]` | Yes |
| `MonthlyTrendResponse` | Dashboard | `months[]` with `month`, `year`, `totalCents`, `expenseCount` | Yes |
| `CostPerKmResponse` | Dashboard | `costPerKmCents`, `totalCostCents`, `totalKm`, `period`, trend | Yes |
| `ForecastResponse` | Dashboard | `forecastCents`, `confidence`, `basedOnMonths`, `breakdown[]` | Yes |
| `MilestoneResponse` | Dashboard | `totalExpenses`, `unshownMilestones[]`, `nextMilestone` | Yes |

### 5.6 Timeline DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `CreateTimelineEntryRequest` | Timeline | `category`, `description?`, `amountCents?`, `currency?`, `date?`, `odometerKm?`, `notes?` | Yes |
| `UpdateTimelineEntryRequest` | Timeline | Same, all optional | Yes |
| `TimelineEntryResponse` | Timeline | Full entry + `photos[]` | Yes |
| `TimelinePhotoResponse` | Timeline | `id`, `photoUrl`, `sortOrder` | Yes |

### 5.7 Reminders DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `CreateReminderRequest` | Reminders | `name`, `triggerType`, `nextDueDate?`, `repeatIntervalDays?`, `mileageIntervalKm?`, `nextDueMileageKm?`, `notificationDaysBefore?`, `notificationKmBefore?`, `notes?` | Yes |
| `UpdateReminderRequest` | Reminders | Same, all optional | Yes |
| `ReminderResponse` | Reminders | Full reminder + `urgency`, `distanceRemainingKm`, `completionHistory[]`, `averageCostCents` | Yes |
| `CompleteReminderRequest` | Reminders | `completedDate?`, `odometerKm?`, `logExpense`, `expenseAmountCents?`, `expenseNotes?` | Yes |
| `SnoozeReminderRequest` | Reminders | `snoozeType` | Yes |
| `ReminderTemplateResponse` | Reminders | `name`, `triggerType`, `mileageIntervalKm?`, `repeatIntervalDays?`, `iconName`, `descriptionBg`, `descriptionEn` | Yes |

### 5.8 Challenges DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `ChallengeResponse` | Challenges | Full challenge + `myParticipation?`, `daysRemaining` | Yes |
| `ChallengeDetailResponse` | Challenges | Challenge + `leaderboard[]`, `howItWorks` | Yes |
| `JoinChallengeRequest` | Challenges | `vehicleId` | Yes |
| `ChallengeParticipationResponse` | Challenges | `challengeId`, `currentRank`, `currentMetricValue`, `vehicleId`, `joinedAt` | Yes |
| `LeaderboardEntryResponse` | Challenges | `rank`, `displayName`, `metricValue`, `isCurrentUser` | Yes |
| `BadgeResponse` | Challenges | `id`, `challengeId`, `challengeName`, `badgeType`, `finalRank`, `finalMetricValue`, `month`, `year`, `earnedAt` | Yes |

### 5.9 Subscription DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `SubscriptionStatusResponse` | Subscriptions | `tier`, `plan`, `status`, `startedAt`, `expiresAt`, `autoRenew`, `features{}` | Yes |
| `SubscriptionPlanResponse` | Subscriptions | `id`, `name`, `priceCents`, `currency`, `interval`, `savingsPercent?`, `features[]` | Yes |

### 5.10 Admin DTOs

| DTO Name | Module | Fields | Shared |
|---|---|---|---|
| `AdminUserListItemResponse` | Admin | `id`, `email`, `displayName`, `role`, `subscriptionTier`, `vehicleCount`, `expenseCount`, `lastActivityAt`, `createdAt`, `isDisabled` | No (admin only) |
| `AdminUserDetailResponse` | Admin | Full user + vehicles summary + subscription history | No |
| `AdminAnalyticsDashboardResponse` | Admin | DAU, WAU, MAU, new users, premium stats, user states | No |
| `AdminRetentionCohortResponse` | Admin | Cohort matrix | No |
| `AdminSubscriberListResponse` | Admin | Subscriber details with plan info | No |
| `AdminRevenueResponse` | Admin | MRR, subscriber counts, churn rate, ARPU | No |
| `SubscriptionOverrideRequest` | Admin | `action`, `reason`, `expiresAt?` | No |
| `AdminCreateChallengeRequest` | Admin | `name`, `description`, `metricType`, `month`, `year`, `minParticipants?` | No |

---

## 6. Push Notification Triggers

Derived from functional specs — every event that should trigger a push notification to the user's registered devices via FCM (DEC-014).

| # | Trigger Event | Notification Content | When Sent | Source |
|---|---|---|---|---|
| PN-1 | **Reminder due (date-based)** | "[Vehicle]: [Reminder name] is due in [X] days." | At 30, 7, and 1 days before due date (BR-3) | maintenance-reminders.md |
| PN-2 | **Reminder due (mileage-based)** | "[Vehicle]: [Reminder name] due in ~[X] km." | When vehicle odometer passes notification thresholds (BR-4) | maintenance-reminders.md |
| PN-3 | **Reminder overdue** | "[Vehicle]: [Reminder name] is overdue!" | Day after due date/mileage (if not completed) | maintenance-reminders.md |
| PN-4 | **Monthly summary** | "Your [Vehicle] cost you [X] лв in [Month]. See the breakdown." | 1st of each month (if user had 5+ expenses) | cost-dashboard.md §3 |
| PN-5 | **Challenge results** | "Monthly challenge results are in! Your cost/km: [X]. Rank: #[Y]." | 1st of each month (for participants) | challenges-gamification.md §2 step 8 |
| PN-6 | **Challenge rank change** | "Cost/km Challenge: you moved to #[X]!" | When rank improves after an expense save (not every time — only on actual change) | challenges-gamification.md §4 BR-9 |
| PN-7 | **Retention nudge** | "Did you fuel up today? Quick-add it in 10 seconds." | Day after first onboarding completion; then at day 3, 7 if no activity | onboarding-auth.md §3 Post-reveal flow |
| PN-8 | **Milestone reached** | "You've tracked [N] expenses! You know more about your car costs than 95% of owners." | When expense count hits 10, 25, 50, 100 | cost-dashboard.md §3 |

**Delivery Rules:**
- Respect quiet hours (user_profiles: `notification_quiet_start` / `notification_quiet_end`) — default 22:00–08:00 (BR-10).
- Maximum 1 reminder notification per day per user (BR-9).
- Multiple due reminders on same day → grouped: "2 maintenance items need attention for your [Vehicle]."
- If notification permission denied → in-app badge only (BR-14).

**Payload Shape (FCM):**

```json
{
  "notification": {
    "title": "BMW E46: Oil change due in 500 km",
    "body": "Your last oil change was 9,500 km ago."
  },
  "data": {
    "type": "reminder_due",
    "reminderId": "uuid",
    "vehicleId": "uuid",
    "deepLink": "/vehicles/{vehicleId}/reminders/{reminderId}"
  }
}
```

---

## 7. Rate Limiting

| Endpoint Group | Limit | Window | Scope |
|---|---|---|---|
| `POST /auth/register` | 5 requests | 1 hour | Per IP |
| `POST /auth/login` | 10 requests | 15 minutes | Per IP |
| `POST /auth/password-reset/request` | 3 requests | 1 hour | Per email |
| `POST /auth/oauth/{provider}` | 10 requests | 15 minutes | Per IP |
| All authenticated endpoints | 100 requests | 1 minute | Per user |
| Photo upload (`POST .../upload-url`) | 10 uploads | 1 hour | Per user |
| Share card generation | 5 requests | 1 hour | Per user |
| Admin endpoints | 200 requests | 1 minute | Per user |

**Implementation:** ASP.NET Core `RateLimiter` middleware with `SlidingWindowRateLimiter`.

**Response when rate limited:**

```
HTTP 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1711101800
```

---

## 8. Versioning & Deprecation

### 8.1 Versioning Strategy

- **URL-based:** `/api/v1/expenses`, `/api/v2/expenses`
- **Breaking changes:** removed fields, changed field types, changed semantics → new version.
- **Non-breaking changes:** new optional fields, new endpoints, additional enum values → stay on current version.

### 8.2 Deprecation Protocol

1. Add `Deprecation` response header: `Deprecation: true`
2. Add `Sunset` header with end-of-life date: `Sunset: Sat, 01 Jun 2026 00:00:00 GMT`
3. Include `Link` header pointing to replacement: `Link: </api/v2/expenses>; rel="successor-version"`
4. Minimum deprecation period: **3 months** (mobile apps can't be force-updated).

### 8.3 Minimum Supported Version

- API supports one major version back (e.g., when v2 launches, v1 stays active).
- After deprecation period: v1 returns `410 Gone` with message pointing to the new version.
- Mobile app update prompt: if app detects `Deprecation: true`, show non-blocking update banner.

---

## 9. Mobile-Specific Considerations

### 9.1 Idempotency Keys

For create endpoints where duplicate submissions could be problematic (network retries):

| Endpoint | Idempotency |
|---|---|
| `POST .../expenses` | Optional `X-Idempotency-Key` header (UUID). Server deduplicates within 24-hour window. |
| `POST .../fuel-entries` | Optional `X-Idempotency-Key` header. |
| `POST .../timeline` | Optional `X-Idempotency-Key` header. |
| `POST .../reminders` | Optional `X-Idempotency-Key` header. |

**Implementation:** Server stores `(idempotency_key, response)` in a cache (in-memory or Redis). If the same key is received within 24 hours, returns the cached response without re-processing.

### 9.2 Bandwidth Optimization

- **Partial responses:** Not implemented in MVP. All responses return the full DTO. If payload size becomes an issue, add `?fields=id,make,model` support post-MVP.
- **Compression:** API serves with `gzip` or `br` content encoding (nginx handles compression).
- **Photo thumbnails:** Timeline list includes photo URLs. Client requests different resolutions as needed. MVP: single resolution (compressed to 1920x1080 max). Future: URL-based image resizing.

### 9.3 Image Upload Flow

**Never upload raw files directly to the API.** Always use the presigned URL pattern:

```
1. Client → POST /api/v1/.../upload-url (content type, file size)
2. Server validates, creates presigned URL, returns it
3. Client → PUT {uploadUrl} (raw file bytes)
4. Client → POST /api/v1/.../confirm OR includes photoUrl in create/update request
```

**Benefits:**
- API server never handles large binary payloads.
- Upload goes directly to storage (local disk via nginx in MVP, R2 in future).
- Server only validates metadata and stores the URL reference.

### 9.4 Minimum Payload Principle

- List endpoints return compact DTOs. Detail endpoints return full DTOs.
- Expensive fields (stats, aggregations) are only included where explicitly needed.
- `EXP-01` returns `monthlyTotalCents` directly — avoids a separate `DASH-01` call after every expense save.
- `FUEL-01` returns consumption data directly — avoids a separate consumption trend call.

---

## 10. Coverage Matrix

### 10.1 Functional Spec → Endpoints

| Functional Spec | Endpoints |
|---|---|
| **onboarding-auth.md** | AUTH-01 through AUTH-08, USER-01, USER-02, USER-05, USER-06, DEVICE-01, DEVICE-02 |
| **vehicle-management.md** | VEH-01 through VEH-09 |
| **expense-tracking.md** | EXP-01 through EXP-06 |
| **fuel-entry.md** | FUEL-01 through FUEL-06 |
| **cost-dashboard.md** | DASH-01 through DASH-05 |
| **vehicle-timeline.md** | TL-01 through TL-09 |
| **maintenance-reminders.md** | REM-01 through REM-08 |
| **challenges-gamification.md** | CHAL-01 through CHAL-04, USER-03, USER-04 |
| **share-export.md** | SHARE-01, SHARE-02 |

### 10.2 Database Table → Endpoints

| Table | Read By | Written By |
|---|---|---|
| `asp_net_users` | USER-01, ADMIN-01, ADMIN-02 | AUTH-01, AUTH-02, AUTH-03, USER-02, USER-06, EXP-01 (last_expense_at), ADMIN-03, middleware (last_activity_at) |
| `user_profiles` | USER-01 | AUTH-01 (create), USER-02 (update), DASH-05 (milestones) |
| `refresh_tokens` | AUTH-04 | AUTH-01, AUTH-02, AUTH-03, AUTH-04 (rotate), AUTH-05 (revoke) |
| `password_reset_tokens` | AUTH-07 | AUTH-06, AUTH-07 (mark used) |
| `login_attempts` | AUTH-02 (lockout check) | AUTH-02 |
| `user_devices` | Push notification background job | DEVICE-01, DEVICE-02 |
| `vehicles` | VEH-02, VEH-03, all vehicle-scoped endpoints | VEH-01, VEH-04, VEH-05, VEH-06, FUEL-01 (odometer), REM-06 (odometer) |
| `vehicle_makes` | VEH-08 | Seed data only |
| `vehicle_models` | VEH-09 | Seed data only |
| `expenses` | EXP-02, EXP-03, DASH-01, DASH-02, DASH-03 | EXP-01, EXP-04, EXP-05, FUEL-01, REM-06, TL-02 (if amount provided) |
| `expense_categories` | EXP-06 | Seed data only |
| `fuel_entries` | FUEL-02, FUEL-03, FUEL-06, DASH-03 | FUEL-01, FUEL-04, FUEL-05 |
| `timeline_entries` | TL-01, TL-03, TL-09, SHARE-01 | EXP-01 (auto-create), TL-02, TL-04, TL-05, FUEL-01 (auto-create) |
| `timeline_photos` | TL-01, TL-03 | TL-06, TL-07, TL-08 |
| `reminders` | REM-01, REM-03, background job (notification check) | REM-02, REM-04, REM-05, REM-06, REM-07 |
| `reminder_completions` | REM-03 | REM-06 |
| `subscription_history` | SUB-01, ADMIN-08, ADMIN-09 | SUB-03 (webhook), ADMIN-10 |
| `challenges` | CHAL-01, CHAL-02, ADMIN-11 | ADMIN-12, ADMIN-13, background job (status transitions) |
| `challenge_participants` | CHAL-02, CHAL-04, USER-03 | CHAL-03, background job (rank recalculation) |
| `challenge_badges` | USER-04 | Background job (month-end badge award) |
| `analytics_events` | ADMIN-06, ADMIN-07 | Various background jobs |

### 10.3 User Journey → Endpoints

| Journey | Endpoints Used |
|---|---|
| **Journey 1: First-Time Experience** | AUTH-01/AUTH-03, VEH-01, VEH-08, VEH-09, EXP-01, DASH-01 |
| **Journey 2: Daily Expense Logging** | EXP-01, EXP-06, DASH-01 |
| **Journey 3: Aha Moment** | DASH-01, DASH-02, DASH-05 |
| **Journey 4: Vehicle Timeline** | TL-01, TL-02, TL-06, TL-07, SHARE-01 |
| **Journey 5: Premium Upgrade** | DASH-03, DASH-04, SUB-01, SUB-02, CHAL-03 |
| **Journey 6: Challenge Participation** | CHAL-01, CHAL-02, CHAL-03, CHAL-04, USER-03, USER-04 |
| **Journey 7: Maintenance Reminder** | REM-01, REM-02, REM-03, REM-06, REM-07, REM-08, EXP-01 |

### 10.4 Gaps & Uncovered Items

| Gap | Description | Resolution |
|---|---|---|
| **Model benchmarks** | cost-dashboard.md mentions "how your [model] compares to other owners" as a premium feature. No dedicated endpoint. | Deferred to post-MVP per PRD C1. When ready: `GET /api/v1/vehicles/{vehicleId}/dashboard/benchmarks`. Requires critical mass of user data. |
| **PDF service history export** | share-export.md mentions this as deferred to v1.1. | Not in MVP API. Future: `POST /api/v1/vehicles/{vehicleId}/export/pdf`. |
| **Document storage** | PRD C3 mentions this as deferred. | Not in MVP API. |
| **Quick-add widget data** | expense-tracking.md Path B mentions home screen widget. | Widget uses the same EXP-01 endpoint. No special API needed — widget calls existing endpoints. |
| **Analytics event tracking** | All specs mention analytics events. | Client-side events go to Firebase Analytics directly (DEC-015). Server-side events recorded by background jobs into `analytics_events` table. No dedicated client-facing analytics endpoint needed. |
| **Email verification resend** | onboarding-auth.md mentions 7-day reminder if unverified. | Add `POST /api/v1/auth/verify-email/resend` if needed. Low priority since verification is not required for functionality. |
| **User data export download** | USER-05 triggers export but no download endpoint documented. | `GET /api/v1/users/me/data-export/{exportId}` — returns download URL. Add when implementing GDPR flows. |

---

## Assumptions

| # | Assumption | If Wrong |
|---|---|---|
| 1 | Cursor-based pagination is sufficient for all list views (no need for offset) | Admin tables may need offset for MudBlazor DataTable. Consider offset pagination for admin endpoints only. |
| 2 | Single active vehicle per user is sufficient for the switcher model | If users need "all vehicles" aggregate views, add aggregate dashboard endpoints. |
| 3 | Image upload via presigned URL works on local VPS disk behind nginx | If not feasible with local storage, fall back to direct upload to API (multipart form) as temporary MVP solution. |
| 4 | RevenueCat webhook is the sole subscription state mutator (except manual admin override) | If webhook delivery is unreliable, add client-side subscription status push (`POST /api/v1/subscription/sync`). |
| 5 | 86 endpoints are implementable by a solo developer within the MVP timeline | Prioritize by module: Auth → Vehicles → Expenses/Fuel → Dashboard → Timeline → Reminders → Challenges → Admin. Ship modules incrementally. |

---

## Hands Off To

- **@developer** — Implement endpoints following this specification. Start with Auth and Vehicles modules. Use `CarPlatform.Shared` for all DTOs.
- **@qa** — Use this spec to write API test scenarios. Every endpoint's error cases are testable.
- **@product-architect** — Maintain this document as user stories are written. Flag any acceptance criteria that require new endpoints.

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-22 | Initial API specification — 86 endpoints across 12 controller groups derived from 9 functional specs, 7 user journeys, 22 database tables, and PRD |
