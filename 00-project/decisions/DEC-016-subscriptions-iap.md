# DEC-016: In-App Purchases & Subscription Management

**File:** `/00-project/decisions/DEC-016-subscriptions-iap.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-22
**Status:** Decided
**Decision:** RevenueCat (free tier) for unified subscription management across Apple and Google

---

## Context

The platform offers a freemium model with premium subscriptions:

- **Monthly:** €2.99/month
- **Annual:** €24.99/year (~30% discount)
- Apple and Google take 30% commission (15% after Year 1 via Small Business Program eligibility)
- Premium unlocks: cost/km, spending trends, unlimited photos, unlimited vehicles, unlimited reminders, challenge participation, PDF export
- Both Flutter mobile app and (future) web purchase paths must share a single subscription state
- Subscription status is embedded in JWT claims (DEC-011) for feature gating

**Architecture constraints:**
- Flutter mobile app (DEC-007) — primary purchase surface
- ASP.NET Core backend (DEC-009) — must validate receipts and gate features
- ASP.NET Identity + JWT (DEC-011) — subscription status in JWT claims
- Web-based subscription purchase may come later to avoid 30% store fees

---

## Options Considered

### Option A: RevenueCat — CHOSEN

**Pros:**
- Free up to $2,500 MRR (~830 monthly subscribers at €2.99) — covers first 1-2 years easily
- Official Flutter SDK (`purchases_flutter`) — mature, well-documented
- Handles both Apple StoreKit and Google Play Billing in a single API
- Server-side receipt validation — no need to implement Apple/Google verification logic
- Webhooks to ASP.NET Core backend when subscription status changes
- Dashboard: MRR, churn, trial conversion, subscriber count — investor-ready metrics out of the box
- Entitlements system maps directly to premium feature gating
- Supports web purchases via API (Stripe integration) for future store-fee avoidance
- Handles edge cases: grace periods, billing retries, subscription transfers, family sharing
- REST API for backend queries — can check subscription status server-side

**Cons:**
- External dependency for a critical revenue path
- Above $2,500 MRR: charges 1% of revenue (or $8/month minimum on Starter plan)
- Another third-party account to manage
- Slight latency on webhook delivery (typically <1 minute)

### Option B: Direct StoreKit + Google Play Billing

**Pros:**
- No middleware, full control
- Zero cost at any scale
- No external dependency

**Cons:**
- Two completely different APIs: StoreKit 2 (Swift/iOS) and Google Play Billing Library (Kotlin/Android)
- Flutter packages (`in_app_purchase`) provide a wrapper, but receipt validation must be implemented server-side for both Apple and Google — significant backend work
- No unified subscription dashboard — build your own analytics
- Must handle: receipt validation, subscription state machine, grace periods, billing retries, refunds, subscription transfers — all manually
- Estimated 3-5 weeks of additional development for a solo developer
- Every edge case is a potential revenue leak

### Option C: Adapty

**Pros:**
- Free up to $10K MTR (monthly tracked revenue) — more generous free tier
- Flutter SDK available
- A/B testing for paywalls built-in
- Server-side validation and webhooks

**Cons:**
- Smaller community and less documentation than RevenueCat
- Flutter SDK less mature (fewer GitHub stars, less community usage)
- Less battle-tested at scale
- Fewer integrations with analytics/attribution tools

### Option D: Qonversion

**Pros:**
- Free tier available
- Cross-platform support

**Cons:**
- Smallest ecosystem of all options
- Least Flutter community adoption
- Documentation quality lower than RevenueCat/Adapty
- Higher risk of SDK abandonment given smaller company

---

## Decision

**RevenueCat (free tier) for unified subscription management.**

## Rationale

1. **Solo developer time is the scarcest resource.** Direct StoreKit + Google Billing implementation would consume 3-5 weeks of development time and produce a fragile, hard-to-debug subscription system. RevenueCat eliminates this entire category of work.

2. **Free tier covers the growth runway.** At €2.99/month, the $2,500 MRR threshold equals ~830 monthly subscribers. With a 5% premium conversion target and 5,000 MAU Year 1, that's ~250 subscribers — well under the free tier ceiling. RevenueCat will likely be free for 1-2 years.

3. **Investor-ready metrics from day one.** RevenueCat's dashboard provides MRR, churn rate, trial conversion, ARPU, and subscriber cohorts — exactly the metrics VCs want to see. No need to build a custom revenue analytics dashboard.

4. **Web purchase path.** RevenueCat supports Stripe integration for web-based purchases, enabling future web subscriptions that bypass the 30% store commission. The entitlements system unifies store and web purchases under a single user identity.

5. **Edge case handling.** Subscription billing has dozens of edge cases (grace periods, billing retries, subscription pauses, refunds, family sharing, cross-platform restore). RevenueCat handles all of these. A custom implementation would have revenue-impacting bugs for months.

---

## Implementation Design

### Architecture

```
Flutter App
├── purchases_flutter SDK
│   ├── Presents paywall / subscription options
│   ├── Handles Apple StoreKit / Google Play Billing purchase flow
│   └── Reports purchase to RevenueCat servers
│
RevenueCat Cloud
├── Validates receipts with Apple / Google
├── Manages subscription state (active, expired, grace period, etc.)
├── Sends webhook to ASP.NET Core API on status change
└── Provides REST API for server-side subscription queries
│
ASP.NET Core API
├── POST /api/webhooks/revenuecat  (receives subscription events)
│   ├── Updates user's subscription status in PostgreSQL
│   └── Issues new JWT with updated subscription claim
├── Middleware: checks JWT subscription claim for premium endpoints
└── GET /api/subscription/status  (client queries current status)
```

### Entitlements Mapping

| RevenueCat Entitlement | Feature Gated |
|---|---|
| `premium` | Cost/km, spending trends, unlimited photos, unlimited vehicles, unlimited reminders, challenge participation, PDF export, model benchmarks |

### Webhook Events to Handle

| Event | Action |
|---|---|
| `INITIAL_PURCHASE` | Set user subscription status to `active`, update JWT claims |
| `RENEWAL` | Extend subscription, update JWT claims |
| `CANCELLATION` | Mark subscription as `cancelled` (still active until period end) |
| `EXPIRATION` | Set subscription status to `expired`, revoke premium JWT claim |
| `BILLING_ISSUE` | Mark as `grace_period`, retain premium access temporarily |
| `PRODUCT_CHANGE` | Update plan (monthly ↔ annual) |

### JWT Claims Update

When subscription status changes:
1. Backend updates `SubscriptionStatus` and `SubscriptionExpiry` in PostgreSQL
2. Next token refresh includes updated `subscription_tier` claim
3. Flutter app refreshes token and updates local feature gating

---

## Cost Implications

| Item | Cost |
|---|---|
| RevenueCat (up to $2,500 MRR) | Free |
| RevenueCat (above $2,500 MRR) | 1% of revenue or $8/month minimum |
| purchases_flutter SDK | Free (open source) |
| Apple commission | 30% (15% after Year 1 via Small Business Program) |
| Google commission | 30% (15% after Year 1 via reduced service fee) |
| **Total MVP** | **€0** (RevenueCat free tier) |

---

## Review Trigger

Revisit this decision if:
- RevenueCat changes pricing or eliminates the free tier
- Subscription edge cases require deeper customization than RevenueCat allows
- MRR exceeds $2,500 and the 1% fee becomes significant relative to alternatives
- Web-based subscriptions become the primary purchase path (may simplify to Stripe-only)
- A critical subscription feature is blocked by RevenueCat limitations

---

## References

- Monetization Plan: `/02-strategy/monetization-plan.md`
- PRD — Premium Features: `/03-product/product-requirements-document.md`
- MVP Feature List — Feature-to-Revenue Mapping: `/03-product/mvp-feature-list.md`
- DEC-007 — Mobile Framework (Flutter): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-009 — Backend Approach (ASP.NET Core): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-011 — Authentication (JWT claims): `/00-project/decisions/DEC-011-authentication.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-22 | Initial decision |