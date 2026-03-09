# DEC-014: Push Notifications

**File:** `/00-project/decisions/DEC-014-push-notifications.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** Firebase Cloud Messaging (FCM) as the single unified gateway for mobile push notifications

---

## Context

The platform requires push notifications for:

- **Maintenance reminders** — "Your oil change is due in 3 days" (`/03-product/functional-specs/maintenance-reminders.md`)
- **Challenge updates** — challenge start/end, milestone badges, leaderboard changes (`/03-product/functional-specs/challenges-gamification.md`)
- **Engagement nudges** — potential future use for retention (e.g., "You haven't logged expenses in 2 weeks")

**Characteristics:**
- Scheduled/event-driven, not real-time chat-scale
- Server-initiated — the ASP.NET Core backend triggers all notifications
- Mobile only for MVP (Flutter on Android + iOS)
- Web push (Blazor WASM) is explicitly out of scope for MVP

**Architecture constraints:**
- Flutter mobile app (DEC-007) — requires FCM (Android) and APNs (iOS) support
- ASP.NET Core backend (DEC-009) — server-side sender
- PostgreSQL (DEC-010) — stores device tokens and reminder schedules
- Self-managed VPS (DEC-012) — no managed notification infrastructure

---

## Options Considered

### Option A: FCM as Unified Gateway — CHOSEN

FCM can deliver to both Android (natively) and iOS (proxying through APNs automatically). This means one server-side API handles both platforms.

**Pros:**
- **Free and unlimited** — FCM has no pricing tiers or event caps
- **One integration, both platforms** — FCM delivers to Android natively and proxies to APNs for iOS
- One server-side SDK (`FirebaseAdmin` for .NET), one client-side package (`firebase_messaging` for Flutter)
- No third-party middleware between backend and devices
- Web push support available when needed (FCM supports web push)
- Consistent with self-managed philosophy — you own the notification pipeline

**Cons:**
- Requires a Firebase project (free) — but only FCM is used, no other Firebase services
- Device token registration and lifecycle management is the developer's responsibility
- No built-in analytics dashboard for notification delivery/open rates (can build basic tracking)
- Testing requires physical devices or emulators with Google Play Services

### Option B: OneSignal

**Pros:**
- Free tier: 10K subscribers, unlimited push
- Unified API for FCM + APNs
- Built-in delivery analytics, segmentation
- Web push support built-in

**Cons:**
- Third-party dependency — another vendor to manage
- Free tier has branding limitations
- Notification logic splits between backend (scheduling) and OneSignal (delivery)
- Adds latency: API → OneSignal → FCM/APNs → device
- Paid plans at $9+/month if exceeding free tier

### Option C: Azure Notification Hubs

**Pros:**
- Unified API for FCM + APNs + WNS
- Good .NET SDK integration

**Cons:**
- Requires Azure account (not aligned with self-managed VPS approach)
- Free tier limited to 500 active devices — very restrictive
- Overkill for scheduled reminders at this scale
- Azure vendor lock-in for a single feature

### Option D: Direct FCM + APNs (separate integrations)

**Pros:**
- Maximum control over both platforms independently

**Cons:**
- Two server-side integrations instead of one
- APNs requires separate certificate management, HTTP/2 client
- No advantage — FCM already proxies to APNs, eliminating the need for direct APNs integration

---

## Decision

**Firebase Cloud Messaging (FCM) as the single unified gateway for both Android and iOS push notifications.**

Firebase is used **only for FCM** — no Firebase Auth, no Firestore, no other Firebase services. The Firebase project exists solely as the push notification delivery channel.

## Rationale

1. **Free and unlimited.** FCM has no pricing tiers — it's free at any scale. No cost consideration at 5K or 50K MAU.

2. **One integration, both platforms.** FCM delivers to Android natively and proxies to APNs for iOS automatically. One server-side SDK (`FirebaseAdmin`), one client-side package (`firebase_messaging`). Half the integration work compared to managing FCM + APNs separately.

3. **No third-party middleware.** The notification path is: backend → FCM → device. No OneSignal or Azure in between. Fewer moving parts, fewer potential failure points.

4. **Aligned with self-managed philosophy.** Consistent with self-hosted DB (DEC-010), self-managed auth (DEC-011), and self-managed VPS (DEC-012). The developer owns the scheduling logic, token management, and notification content — FCM is only the delivery pipe.

5. **Web push ready.** When Blazor WASM needs push notifications post-MVP, FCM supports web push using the same Firebase project. No new service needed.

---

## Implementation Design

### Architecture

```
ASP.NET Core API (backend)
├── Background Job (IHostedService)
│   ├── Checks reminder schedules in PostgreSQL
│   ├── Evaluates challenge events
│   └── Calls INotificationService when notification is due
├── INotificationService (abstraction)
│   └── FcmNotificationService (implementation)
│       └── Uses FirebaseAdmin SDK → FCM API → device
└── Device Token Management
    ├── POST /api/devices/register   (Flutter sends FCM token)
    ├── DELETE /api/devices/{token}   (token removal)
    └── Tokens stored in PostgreSQL (per user, per device)

Flutter App (mobile)
├── firebase_messaging package
│   ├── Requests notification permission
│   ├── Receives FCM device token
│   └── Sends token to API on registration/refresh
└── Handles foreground/background notification display
```

### Key Design Principles

- **Device tokens stored per user, per device** — a user with multiple devices receives notifications on all of them
- **Token invalidation/cleanup** — stale tokens removed when FCM reports them as invalid
- **Notification sending abstracted behind `INotificationService`** — delivery mechanism is decoupled from scheduling/business logic
- **Reminder scheduling logic separated from delivery logic** — the background job decides *what* to send and *when*; the notification service handles *how*
- **Future web push** can be added as an additional implementation or extension without redesigning the module

### Background Job Approach

**Simple `IHostedService` with a timer** for MVP:
- Runs on a configurable interval (e.g., every 5-15 minutes)
- Queries PostgreSQL for reminders due within the next interval window
- Sends notifications via `INotificationService`
- Marks reminders as notified to prevent duplicates

This avoids the overhead of Hangfire or Quartz.NET for what is a simple periodic check. If scheduling requirements become more complex (e.g., exact-time delivery, retry queues, many concurrent job types), Hangfire can be introduced later without changing the notification or reminder logic.

### Firebase Project Scope

| Firebase Service | Used? |
|---|---|
| Cloud Messaging (FCM) | Yes — push notification delivery |
| Authentication | No — using ASP.NET Identity (DEC-011) |
| Firestore / Realtime Database | No — using PostgreSQL (DEC-010) |
| Cloud Storage | No — using local disk / R2 (DEC-013) |
| Analytics | No — separate decision (DEC-015) |
| Crashlytics | No — separate consideration |

---

## Cost Implications

| Item | Cost |
|---|---|
| Firebase Cloud Messaging | Free (unlimited) |
| Firebase project | Free |
| FirebaseAdmin .NET SDK | Free (open source) |
| firebase_messaging Flutter package | Free (open source) |
| APNs (via FCM proxy) | Free |
| Apple Developer Account (for APNs certificates) | €99/year (already required per DEC-007) |
| **Total** | **€0** |

---

## Review Trigger

Revisit this decision if:
- FCM introduces pricing or usage limits (extremely unlikely given Google's business model)
- Notification delivery reliability becomes a concern — may need delivery tracking or a secondary channel
- Complex scheduling requirements emerge (exact-time delivery, retry logic, priority queues) — consider Hangfire
- Web push implementation for Blazor WASM requires a different approach than FCM web push
- A need arises for rich notification analytics (delivery rates, open rates, engagement) — consider OneSignal or custom tracking

---

## References

- Functional Spec — Maintenance Reminders: `/03-product/functional-specs/maintenance-reminders.md`
- Functional Spec — Challenges & Gamification: `/03-product/functional-specs/challenges-gamification.md`
- DEC-007 — Mobile Framework (Flutter): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-009 — Backend Approach (ASP.NET Core): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-010 — Database (PostgreSQL): `/00-project/decisions/DEC-010-database.md`
- DEC-011 — Authentication (ASP.NET Identity): `/00-project/decisions/DEC-011-authentication.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |