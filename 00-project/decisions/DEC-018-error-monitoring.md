# DEC-018: Error Monitoring

**File:** `/00-project/decisions/DEC-018-error-monitoring.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-22
**Status:** Decided
**Decision:** Sentry (free tier) as the unified error monitoring platform across all projects

---

## Context

The platform has three runtime surfaces that can produce errors:

1. **Flutter mobile app** — crashes, unhandled exceptions, Dart errors on user devices
2. **ASP.NET Core Web API** — unhandled exceptions, 500 errors, failed background jobs
3. **Blazor WASM web app** — JavaScript interop errors, unhandled C# exceptions in the browser

As a solo developer, there is no QA team and no support team to relay user bug reports. **If a crash happens and it's not automatically captured, it's invisible.** Error monitoring must be automatic, cross-platform, and zero-effort to maintain.

**Requirements:**
- Automatic crash reporting with stack traces on all three platforms
- Source map / debug symbol support for meaningful stack traces
- Release tracking — know which version introduced a bug
- Alerting — email or webhook notification for new errors
- Free or very low cost at MVP scale

---

## Options Considered

### Option A: Sentry — CHOSEN

**Pros:**
- **Free tier: 5,000 events/month** — sufficient for MVP through early growth
- **Supports all three platforms:** Flutter (`sentry_flutter`), ASP.NET Core (`Sentry.AspNetCore`), Blazor WASM (`Sentry` .NET SDK)
- **Single dashboard** for errors across mobile, API, and web
- **Release tracking** — tag errors by app version, see regressions
- **Performance monitoring** included — transaction traces, slow API endpoints
- **Source context** — shows the exact line of code that failed with surrounding context
- **Breadcrumbs** — automatic capture of events leading up to a crash
- **Alerting** — configurable alerts for new issues, regressions, and error spikes
- **Issue grouping** — automatically deduplicates similar errors
- EU data residency available (Sentry.io EU region)
- Self-hosted option available if needed (Docker)

**Cons:**
- 5,000 events/month may be tight if error rates are high during early development (mitigated by sampling and filtering)
- Paid plans start at $26/month for 50K events (Team plan)
- Another external dependency

### Option B: Firebase Crashlytics

**Pros:**
- Free and unlimited — no event caps
- Excellent Flutter integration (`firebase_crashlytics`) — shares the Firebase project already configured for FCM (DEC-014) and Analytics (DEC-015)
- Real-time crash reporting with stack traces
- Crash-free user metrics
- Version tracking

**Cons:**
- **Mobile only** — no ASP.NET Core backend support, no Blazor WASM support
- Would leave the API (the most critical component) completely unmonitored
- Would require a second tool (Sentry or Application Insights) for backend — defeating the "single dashboard" requirement
- Less sophisticated issue grouping and alerting than Sentry

### Option C: Bugsnag

**Pros:**
- Multi-platform: Flutter, .NET, JavaScript
- Free tier: 7,500 events/month
- Good stability monitoring dashboard

**Cons:**
- Free tier has limited features (no release tracking, limited alerting)
- Smaller community than Sentry — fewer integrations, less documentation
- .NET SDK less mature than Sentry's
- Blazor WASM support is via JavaScript SDK (less native than Sentry's .NET approach)
- Paid plans more expensive than Sentry at equivalent tiers

### Option D: Application Insights (Azure Monitor)

**Pros:**
- Excellent ASP.NET Core integration (first-party Microsoft)
- 5GB free ingestion/month

**Cons:**
- Designed for the Azure ecosystem — awkward fit with self-managed VPS
- Flutter support requires community packages (not official)
- No native Blazor WASM client-side support
- Complex pricing model
- Azure vendor lock-in for a monitoring feature

---

## Decision

**Sentry (free tier) as the unified error monitoring platform for Flutter, ASP.NET Core, and Blazor WASM.**

## Rationale

1. **Single dashboard, all platforms.** Sentry is the only option that covers Flutter, ASP.NET Core, and Blazor WASM in one place. A solo developer cannot afford to check three different monitoring tools. One dashboard, one alert stream.

2. **Backend monitoring is non-negotiable.** The API is the single point of failure for the entire platform. Firebase Crashlytics would leave it unmonitored. Backend errors (database connection failures, failed webhook processing, broken migrations) are the highest-impact issues — they affect all users simultaneously.

3. **5,000 events is sufficient for MVP.** With proper error handling, a well-built app generates very few error events. The 5K limit is for errors and exceptions, not normal requests. Even with 5,000 MAU, this is adequate. Event sampling can be configured if needed.

4. **Release tracking and regressions.** Sentry automatically tracks which release introduced a new error. When deploying frequently as a solo developer, knowing "this error appeared in v1.2.3" is invaluable for quick diagnosis.

5. **Performance monitoring included.** Sentry's performance monitoring (transaction traces, slow endpoint detection) complements the error monitoring at no additional cost on the free tier.

---

## Implementation Design

### SDK Integration

| Project | SDK | Configuration |
|---|---|---|
| Flutter mobile | `sentry_flutter` | Auto-capture unhandled Dart exceptions, native crashes, HTTP breadcrumbs |
| ASP.NET Core API | `Sentry.AspNetCore` | Auto-capture unhandled exceptions, 500 responses, EF Core query errors |
| Blazor WASM | `Sentry` (.NET SDK) | Auto-capture unhandled C# exceptions, JS interop errors |

### Architecture

```
Flutter App                    ASP.NET Core API              Blazor WASM
├── sentry_flutter             ├── Sentry.AspNetCore         ├── Sentry (.NET)
│   ├── Unhandled exceptions   │   ├── Unhandled exceptions  │   ├── Unhandled exceptions
│   ├── Native crashes         │   ├── 500 responses         │   ├── JS interop errors
│   ├── HTTP breadcrumbs       │   ├── EF Core errors        │   └── Navigation errors
│   └── Navigation breadcrumbs │   ├── Background job errors │
│                              │   └── Webhook failures      │
└───────────────┬──────────────┴──────────────┬──────────────┘
                │         Sentry Cloud         │
                ├── Issue grouping & deduplication
                ├── Release tracking (per project)
                ├── Alert rules (email, webhook)
                ├── Performance monitoring
                └── Single dashboard for all projects
```

### Sentry Project Organization

| Sentry Project | Source | Environment Tags |
|---|---|---|
| `carplatform-mobile` | Flutter app | `production`, `staging` |
| `carplatform-api` | ASP.NET Core API | `production`, `staging` |
| `carplatform-web` | Blazor WASM admin | `production` |

### Alert Configuration

| Alert | Condition | Action |
|---|---|---|
| New issue | First occurrence of a new error | Email notification |
| Regression | Previously resolved error reappears | Email notification |
| Error spike | >10 events of same error in 1 hour | Email notification |
| API 500 spike | >5 unhandled API errors in 15 minutes | Email notification |

### Sensitive Data Filtering

- **Strip PII:** Configure Sentry to strip email addresses, names, and financial data from error reports
- **No request bodies:** Don't send expense amounts, vehicle details, or user profile data in error context
- **Sanitize headers:** Strip `Authorization` headers from HTTP breadcrumbs
- **GDPR:** Use Sentry EU region (eu.sentry.io) for data residency compliance

---

## Firebase Crashlytics — Supplementary Use

Firebase Crashlytics remains available in the existing Firebase project (DEC-014, DEC-015) and can be enabled alongside Sentry for mobile-specific crash metrics (crash-free user percentage, crash trends). This is optional and additive — Sentry is the primary error monitoring tool.

If enabled, Crashlytics provides:
- Crash-free user percentage (a metric investors and app store reviewers value)
- Native crash symbolication (NDK crashes on Android)
- Firebase console integration alongside Analytics and FCM

**Recommendation:** Enable Crashlytics alongside Sentry on mobile only. It's free, adds <1 minute of setup, and provides the crash-free user metric.

---

## Cost Implications

| Item | Cost |
|---|---|
| Sentry (free tier, 5K events/month) | Free |
| Sentry (Team plan, 50K events/month) | $26/month |
| Sentry (Business plan, 100K events/month) | $80/month |
| `sentry_flutter` SDK | Free (open source) |
| `Sentry.AspNetCore` SDK | Free (open source) |
| Firebase Crashlytics (optional, mobile-only) | Free |
| **Total MVP** | **€0** |

---

## Review Trigger

Revisit this decision if:
- 5,000 events/month is consistently exceeded — upgrade to Team plan ($26/month) or implement stricter sampling
- Sentry's free tier removes features that are currently included
- Performance monitoring needs exceed what Sentry provides — consider Datadog or Grafana
- Self-hosted Sentry is needed for compliance (Docker deployment on VPS is an option)
- Firebase Crashlytics provides all the monitoring needed and Sentry adds no value (unlikely given backend requirement)

---

## References

- DEC-007 — Mobile Framework (Flutter): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-008 — Web Framework (Blazor WASM): `/00-project/decisions/DEC-008-web-framework.md`
- DEC-009 — Backend Approach (ASP.NET Core): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-014 — Push Notifications (Firebase project): `/00-project/decisions/DEC-014-push-notifications.md`
- DEC-015 — Analytics (Firebase Analytics): `/00-project/decisions/DEC-015-analytics-telemetry.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-22 | Initial decision |