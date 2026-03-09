# DEC-015: Analytics & Telemetry

**File:** `/00-project/decisions/DEC-015-analytics-telemetry.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** Firebase Analytics (MVP, mobile-focused) behind an `IAnalyticsService` abstraction

---

## Context

The platform requires product analytics to track:

- **Feature usage** — which screens, how often, time spent
- **Paywall/premium conversion funnel** — who sees upgrade prompt → who converts
- **Journey metrics** — first expense logged, "aha moment" (cost dashboard viewed), retention triggers
- **Engagement patterns** — session frequency, feature adoption rates

**Requirements:**
- GDPR compliant — user consent required, no sensitive personal data in events
- Budget-conscious — free or very low cost at 5K-50K MAU
- Analytics is instrumentation, not core business logic — it must not couple to the application

**Architecture constraints:**
- Flutter mobile app (DEC-007) — primary analytics surface for MVP
- Blazor WASM web app (DEC-008) — admin dashboard, analytics tracking not a priority for MVP
- ASP.NET Core backend (DEC-009) — potential server-side event source (future)
- Firebase project already exists for FCM (DEC-014) — Firebase Analytics can share the same project

---

## Options Considered

### Option A: Firebase Analytics — CHOSEN

**Pros:**
- Free and unlimited for mobile (no event caps)
- Deep Flutter integration — `firebase_analytics` is official and mature
- Automatic screen tracking, user properties, event logging
- Shares the same Firebase project already created for FCM (DEC-014) — zero additional setup
- Simple SDK — low learning curve for MVP instrumentation
- Google's infrastructure — reliable, scalable

**Cons:**
- Limited custom funnel and cohort analysis compared to Mixpanel/PostHog
- Data stored by Google (US) — GDPR reliance on Google's DPA
- Raw data export requires BigQuery (paid) for advanced analysis
- Blazor WASM support requires JS interop (not a concern for MVP — admin dashboard doesn't need product analytics)
- Less suited for deep product analytics (funnels, retention cohorts) than purpose-built tools

### Option B: Mixpanel

**Pros:**
- Best-in-class product analytics — funnels, retention, cohorts
- Free tier: 20M events/month (very generous)
- Official Flutter SDK
- JavaScript SDK for Blazor WASM

**Cons:**
- Cloud only — no self-hosted option
- US-hosted by default (EU residency on paid plans)
- Another external service to configure and manage from day one
- Stronger than needed for MVP — the advanced funnel/cohort features become valuable at scale, not at launch

### Option C: PostHog (Self-Hosted or Cloud)

**Pros:**
- Open source — can self-host on VPS
- Cloud free tier: 1M events/month
- Product analytics + session replay + feature flags in one tool

**Cons:**
- Self-hosted requires Docker + ~2GB RAM (significant VPS resource cost alongside API + PostgreSQL)
- Flutter SDK is community-maintained, not official
- Blazor integration requires JS interop
- More operational overhead than needed for MVP

### Option D: Amplitude

**Pros:**
- Strong product analytics, free tier: 10M events/month

**Cons:**
- No official Flutter SDK (community packages only)
- Cloud only, US-hosted by default
- No advantage over Mixpanel for this use case

### Option E: Custom Server-Side Analytics

**Pros:**
- Full control, data in own PostgreSQL

**Cons:**
- Significant development time to build event ingestion, storage, aggregation, and dashboards
- Reinventing the wheel for a solved problem
- Solo developer time is better spent on product features

---

## Decision

**Firebase Analytics for MVP, primarily for mobile analytics, behind an `IAnalyticsService` abstraction.**

Firebase Analytics shares the same Firebase project already provisioned for FCM (DEC-014). No additional Firebase services are introduced — just the analytics component alongside messaging.

## Rationale

1. **Zero additional setup.** The Firebase project already exists for push notifications (DEC-014). Adding Firebase Analytics to the Flutter app is a single package addition with shared configuration. No new accounts, no new credentials.

2. **Free and unlimited.** No event caps, no pricing tiers. Sufficient for MVP through 50K MAU and beyond.

3. **Flutter-native.** The `firebase_analytics` package is official, well-maintained, and deeply integrated with Flutter's navigation system. Automatic screen tracking and standard event logging work out of the box.

4. **MVP-appropriate.** For launch, the team needs basic metrics: are users logging expenses? Are they viewing the cost dashboard? Are they hitting the upgrade prompt? Firebase Analytics handles these questions without the complexity of a full product analytics suite.

5. **Abstraction preserves flexibility.** The `IAnalyticsService` interface means the application code never calls Firebase directly. If/when deeper product analytics are needed (funnels, cohorts, retention curves), switching to Mixpanel, PostHog, or a composite approach requires only a new implementation — no refactoring of screens, services, or business logic.

---

## Implementation Design

### Abstraction Layer

```dart
abstract class IAnalyticsService {
  Future<void> trackEvent(String eventName, [Map<String, Object>? parameters]);
  Future<void> trackScreen(String screenName);
  Future<void> identifyUser(String userId);
  Future<void> setUserProperties(Map<String, String> properties);
  Future<void> resetIdentity();
}
```

### Planned Implementations

| Implementation | When | Provider |
|---|---|---|
| `FirebaseAnalyticsService` | MVP | Firebase Analytics (`firebase_analytics` package) |
| `MixpanelAnalyticsService` | If/when deeper product analytics needed | Mixpanel Flutter SDK |
| `PostHogAnalyticsService` | If/when self-hosted analytics preferred | PostHog Flutter SDK |
| `NoOpAnalyticsService` | Testing, development, or when user declines consent | No-op (silently discards events) |

### Design Principles

- **No Firebase SDK calls spread through the app.** All analytics calls go through `IAnalyticsService`. Screens and services never import Firebase packages directly.
- **Common operations only.** The abstraction covers: track event, track screen, identify user, set user properties, reset identity. No provider-specific features leak into the interface.
- **Consent-gated.** Analytics initialization and event sending respect user consent. If consent is not granted, the `NoOpAnalyticsService` is used (or events are suppressed). Consent state is checked before any data is sent.
- **No sensitive personal data.** Events contain behavioral data (screen viewed, expense logged, challenge joined) — never personal identifiers, financial amounts, vehicle details, or other PII.
- **Analytics is instrumentation.** It observes the application; it does not influence business logic. No feature behavior depends on analytics state. Analytics failures are silently ignored — they never crash the app or block user actions.

### MVP Tracking Plan (Initial Events)

| Event | Screen/Trigger | Purpose |
|---|---|---|
| `onboarding_completed` | Onboarding flow | Activation funnel |
| `vehicle_added` | Add Vehicle | Activation (first vehicle = key milestone) |
| `expense_logged` | Add Expense | Core engagement metric |
| `fuel_entry_logged` | Add Fuel Entry | Core engagement metric |
| `dashboard_viewed` | Cost Dashboard | "Aha moment" — first time viewing cost insights |
| `timeline_viewed` | Vehicle Timeline | Feature adoption |
| `reminder_created` | Add Reminder | Feature adoption |
| `challenge_joined` | Challenge Detail | Gamification engagement |
| `upgrade_prompt_viewed` | Paywall screen | Conversion funnel — top |
| `upgrade_completed` | After successful purchase | Conversion funnel — bottom |
| `photo_uploaded` | Timeline Entry | Feature adoption |

### Firebase Project Scope (Updated from DEC-014)

| Firebase Service | Used? | Decision |
|---|---|---|
| Cloud Messaging (FCM) | Yes | DEC-014 |
| Analytics | Yes | DEC-015 (this document) |
| Authentication | No | Using ASP.NET Identity (DEC-011) |
| Firestore / Realtime Database | No | Using PostgreSQL (DEC-010) |
| Cloud Storage | No | Using local disk / R2 (DEC-013) |
| Crashlytics | No | Separate consideration (future) |

### Blazor WASM Analytics (Post-MVP)

For MVP, the Blazor WASM admin dashboard does not require product analytics tracking. The admin is the developer — no conversion funnel or feature adoption metrics are needed for the admin interface.

If analytics are later needed for Blazor WASM (e.g., B2B user behavior tracking in Phase 2-3), options include:
- Firebase Analytics via JS interop (same Firebase project)
- Mixpanel JavaScript SDK via JS interop
- Server-side event tracking from the ASP.NET Core API

This decision can be made when B2B modules are built. It does not affect the mobile analytics architecture.

---

## Cost Implications

| Item | Cost |
|---|---|
| Firebase Analytics | Free (unlimited) |
| firebase_analytics Flutter package | Free (open source) |
| Firebase project | Free (shared with FCM) |
| **Future: Mixpanel (if migrated)** | Free tier: 20M events/month |
| **Future: PostHog Cloud (if migrated)** | Free tier: 1M events/month |
| **Total (MVP)** | **€0** |

---

## Review Trigger

Revisit this decision if:
- The team needs deeper funnel analysis, cohort tracking, or retention curves that Firebase Analytics cannot provide — consider Mixpanel or PostHog
- GDPR audit requires analytics data stored in EU (Firebase Analytics data is processed in the US) — consider PostHog self-hosted or Mixpanel with EU residency
- B2B modules require cross-platform analytics (mobile + web) with unified user journeys — consider Mixpanel or PostHog as a single cross-platform solution
- Firebase Analytics event limitations become restrictive (500 distinct event types, 25 parameters per event)
- The developer needs raw event data access without BigQuery — consider PostHog or custom analytics

---

## References

- PRD — Success Metrics: `/03-product/product-requirements-document.md`
- Functional Spec — Onboarding: `/03-product/functional-specs/onboarding-auth.md`
- Functional Spec — Cost Dashboard: `/03-product/functional-specs/cost-dashboard.md`
- User Journey — Aha Moment: `/03-product/user-journeys/journey-aha-moment.md`
- User Journey — Premium Upgrade: `/03-product/user-journeys/journey-premium-upgrade.md`
- DEC-007 — Mobile Framework (Flutter): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-014 — Push Notifications (FCM, shared Firebase project): `/00-project/decisions/DEC-014-push-notifications.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |