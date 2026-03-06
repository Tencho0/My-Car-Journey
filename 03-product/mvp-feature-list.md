# MVP Feature List — Car Ownership Platform
## `/03-product/mvp-feature-list.md`
### Produced by: @product-architect | Date: March 2026
### Version: 1.0 — Pre-validation

## References
- Concept Document: `/00-project/concept-document.md`
- Lean Canvas: `/02-strategy/lean-canvas.md`
- Value Proposition: `/02-strategy/value-proposition.md`
- Competitor Analysis: `/01-discovery/research/competitor-analysis.md`
- Positioning Strategy: `/02-strategy/positioning-strategy.md`
- Monetization Plan: `/02-strategy/monetization-plan.md`

---

## 1. Prioritization Method

**Framework:** MoSCoW (Must / Should / Could / Won't)

**Prioritization criteria:**
- Does it address a validated pain point? (See value proposition P1-P10)
- Is it required for the core "aha moment" (seeing your real car cost)?
- Is it feasible for a solo developer in the MVP timeline?
- Does it drive retention (weekly return visits)?
- Does it support the premium conversion funnel?

**Effort estimates:** S (Small) = 1-3 days, M (Medium) = 3-7 days, L (Large) = 1-3 weeks

**Tech stack:** TBD — see technical architecture decision. Effort estimates are framework-agnostic and assume cross-platform mobile app + backend API + relational database.

---

## 2. Must Have (P0) — Ship Breaks Without These

These are non-negotiable for MVP launch. Without any one of them, the product fails to deliver its core promise.

| # | Feature | Description | Pain/Gain Addressed | Effort | Notes |
|---|---|---|---|---|---|
| M1 | **User authentication** | Registration (email + password), login, password reset, session management | Foundation | M | Consider OAuth (Google/Apple/Facebook) for reduced friction. Tech stack TBD. |
| M2 | **Vehicle profile management** | Add/edit/delete vehicles. Fields: make, model, year, fuel type, license plate, odometer, photo. Multi-vehicle support. | F6, G5 | M | Free tier: up to 2 vehicles. Premium: unlimited. |
| M3 | **Expense tracking** | Log expenses across categories: fuel, maintenance, modifications, insurance, tax, tires, parking, fines, car wash, other. Fields: amount, date, category, subcategory, odometer (optional), notes. | P1, P2, G1, G8 | L | This is the core interaction. Must be fast (<10 seconds per entry). Quick-add mode with smart defaults. |
| M4 | **Fuel entry (specialized)** | Dedicated fuel logging: liters, price per liter, total cost, odometer reading, full/partial fill, station (optional). Auto-calculate consumption (L/100km). | F2, G2 | M | Fuel is the most frequent expense. Dedicated flow reduces friction. |
| M5 | **Cost dashboard** | Monthly total cost, cost per kilometer (premium), category breakdown pie chart, monthly trend bar chart. Current month prominent, tap to see past months. | P1, P2, G1, G2 | L | The "aha moment" screen. Design for impact: big number, clear breakdown, visible trends. Free tier: monthly total + basic breakdown. Premium: cost/km + trends + forecasts. |
| M6 | **Vehicle timeline** | Chronological feed of all expenses, services, and modifications for a vehicle. Each entry shows: date, category, amount, notes, photos (if any). Filterable by category. | P3, G5, G10 | L | Key differentiator vs. competitors. Must feel like a "car journal" — visual, scrollable, photo-rich. Free tier: up to 5 photos total. Premium: unlimited. |
| M7 | **Maintenance reminders** | Create reminders based on date (e.g., insurance renewal March 15) or mileage (e.g., oil change every 10,000 km) or both. Push notifications when due. | P5, G4 | M | Free tier: up to 3 active reminders. Premium: unlimited. Require periodic odometer updates to trigger mileage-based reminders. |
| M8 | **Multi-vehicle support** | Switch between vehicles. Each vehicle has its own timeline, dashboard, and reminders. Aggregate view optional. | F6 | S | Free: 2 vehicles. Premium: unlimited. Enthusiasts often own 2-3 cars. |
| M9 | **User profile & settings** | Edit profile (name, email). App settings: currency (лв/€), distance unit (km), language (Bulgarian/English), notification preferences. | Foundation | S | Bulgarian language primary. English for broader reach. |
| M10 | **Onboarding flow** | First-launch experience: add your car → log your first expense → see your dashboard. Guide user to the "aha moment" as fast as possible. | Activation | M | Critical for activation rate. Target: 60%+ of new users complete onboarding (add vehicle + log 1 expense). |

**Total Must Have effort estimate: ~8-12 weeks** (solo developer, depending on tech stack)

---

## 3. Should Have (P1) — Important, Include If Feasible

These significantly enhance the experience and support retention/conversion but are not blockers for a bare-minimum launch.

| # | Feature | Description | Pain/Gain Addressed | Effort | Notes |
|---|---|---|---|---|---|
| S1 | **Photo attachments on expenses** | Attach 1-3 photos to any expense or timeline entry (receipt, before/after, work done). | G5, P3 | M | Storage: Tech stack TBD. Free: 5 photos total. Premium: unlimited. Compress on upload. |
| S2 | **Spending trends** | Year-over-year comparison, category trends over time, monthly spending graph (6-12 months). Premium feature. | P2, G7 | M | Requires 2+ months of data to be useful. Show "unlock after 2 months" for new users. |
| S3 | **Quick-add widget** | Home screen widget or shortcut to log an expense in 2-3 taps (amount + category, auto-date, default vehicle). | P8, G8 | M | Critical for retention. Every tap you remove increases the chance they'll keep logging. Platform-specific implementation. |
| S4 | **Basic challenges** | Monthly challenges: lowest cost/km, best fuel efficiency, most expenses logged. Leaderboard showing anonymous rankings. | G9 | M | Free: view leaderboards. Premium: compete and earn badges. Start with 2-3 simple challenges. |
| S5 | **Shareable vehicle card** | Generate a visual card image: car photo, name, key stats (total cost, cost/km, mods count). One-tap share to Instagram/WhatsApp/Facebook. | G6, S1 | M | Organic growth driver. Design must be share-worthy (not a boring stat dump). Include subtle app branding. |
| S6 | **Expense categories with icons** | Visual category system with custom icons. Pre-defined categories (fuel, maintenance, mods, insurance, tax, tires, parking, fines, car wash) + custom "other" with user-defined subcategories. | G8 | S | Better than plain text lists. Makes the app feel polished. |
| S7 | **Dashboard currency formatting** | Proper лв and € formatting. Auto-conversion display (show both currencies). | Foundation | S | Bulgarian users think in лв. Keep it local. |

**Total Should Have effort estimate: ~4-6 weeks**

---

## 4. Could Have (P2) — Nice to Have, Defer If Time-Constrained

These add value but can be added in a v1.1 or v1.2 release without compromising the core experience.

| # | Feature | Description | Pain/Gain Addressed | Effort | Notes |
|---|---|---|---|---|---|
| C1 | **Model benchmarks** | "Your BMW E46 costs X% more/less per km than average." Compare user's costs to aggregated data from other users of same make/model. | P4, G3 | L | Core differentiator BUT requires user data. Seed with public average cost data for popular models at launch. Becomes real at 500-1,000+ active users per model. |
| C2 | **PDF service history export** | Generate a professional PDF: vehicle details, full chronological service/expense history, photos. Useful for selling car. Premium feature. | G10, P7 | M | High perceived value. Good premium conversion trigger. |
| C3 | **Document storage** | Upload photos of insurance policy, registration, inspection certificate, warranty. Premium feature. | F8 | S | "Everything in one place." Convenience feature. |
| C4 | **Spending forecast** | Based on historical patterns, predict next month's likely expenses. Show upcoming known costs (insurance renewal, scheduled service). Premium feature. | G11 | M | Requires 3+ months of data. Use simple pattern matching, not ML. |
| C5 | **Keep vs. sell insights** | Show total invested in the car. Cost trajectory (rising/flat/falling). "At this rate, your next 12 months will cost X." Premium feature. | G7 | M | Premium conversion trigger for users considering selling. |
| C6 | **Achievement badges** | Badges for milestones: first expense logged, 30-day streak, 100 expenses, full year tracked, etc. | G9 | S | Low effort, adds delight. Not critical for MVP but supports retention. |
| C7 | **Dark mode** | System-default or manual dark/light theme toggle. | UX | S | Expected by modern app users. Can defer to v1.1 but not much longer. |

**Total Could Have effort estimate: ~4-7 weeks**

---

## 5. Won't Have (Not in MVP) — Explicitly Excluded

These are intentionally out of scope for MVP. They may be built later but are excluded now to maintain focus and timeline.

| # | Feature | Why Excluded | When It Might Come |
|---|---|---|---|
| W1 | **Offline sync** | Adds significant complexity (conflict resolution, local DB, sync logic). Most users have mobile data. Not worth the engineering cost for MVP. | Phase 2 (if user feedback demands it) |
| W2 | **Receipt OCR** | ML/API dependency, accuracy issues, adds scope. Manual entry with quick-add is sufficient for MVP. | Phase 2 (if manual entry proves too painful) |
| W3 | **Bank/card integration** | Requires banking APIs, PSD2 compliance, and significant backend work. Not feasible for solo dev MVP. | Phase 3+ |
| W4 | **Garage module** | B2B feature. Requires separate UI, business onboarding, and service-book sync. Build after 5,000+ active B2C users. | Phase 2 |
| W5 | **Dealer module** | B2B feature. Inventory management, profitability tracking. Build after garage module is validated. | Phase 3 |
| W6 | **Fleet module** | B2B feature. Multi-vehicle dashboard for businesses. Different user needs entirely. | Phase 3 |
| W7 | **Social profiles** | User profiles visible to others, following, commenting. Adds moderation burden and social complexity. | Phase 2+ (if community engagement warrants it) |
| W8 | **Advanced predictions (ML)** | Machine learning for maintenance predictions, depreciation curves, anomaly detection. Requires data scale we won't have at launch. | Phase 3-4 |
| W9 | **Income tracking** | For gig drivers (Uber/Bolt/Glovo). Different user segment. Adds UI complexity. | Phase 2 (if gig drivers emerge as a segment) |
| W10 | **Marketplace / parts shopping** | In-app parts or service marketplace. Requires supplier partnerships and e-commerce infrastructure. | Phase 3+ |
| W11 | **VIN decoder / auto-fill** | Auto-populate vehicle details from VIN. Requires third-party API subscription. Nice but not essential. | v1.1 or v1.2 |
| W12 | **Data intelligence / reports** | Anonymized aggregated reports for industry buyers. Requires massive data volume. | Phase 4 |

---

## 6. Feature-to-Pain Mapping

Traceability from features back to validated pains (from `/02-strategy/value-proposition.md`):

| Pain | Severity | Features That Address It |
|---|---|---|
| P1: Don't know true cost | High | M3 (expense tracking), M5 (cost dashboard) |
| P2: Surprised by costs | High | M5 (dashboard trends), S2 (spending trends), C4 (forecast) |
| P3: No organized history | High | M6 (vehicle timeline), S1 (photos), C2 (PDF export) |
| P4: Can't compare costs | Medium | C1 (model benchmarks) |
| P5: Forget maintenance | Medium | M7 (maintenance reminders) |
| P6: Boring apps | Medium | S6 (icons), S5 (share card), C6 (badges), overall design |
| P7: No proof when selling | Medium | M6 (timeline), C2 (PDF export) |
| P8: Tedious entry | High | S3 (quick-add widget), M3 (optimized entry flow) |
| P9: Family pressure | Medium | S5 (shareable card), M5 (dashboard sharing) |
| P10: Info overload | Low | C1 (data-backed benchmarks) |

---

## 7. Feature-to-Revenue Mapping

How features support the freemium conversion funnel:

| Feature | Free | Premium | Conversion Role |
|---|---|---|---|
| Expense tracking | Unlimited | Unlimited | Builds the habit (free) — no gating on core action |
| Cost dashboard (monthly total) | Yes | Yes | Creates awareness (free) |
| Cost per kilometer | No | Yes | **Primary paywall trigger** — the "aha moment" extension |
| Category breakdown | Basic | Detailed | Teases depth |
| Spending trends | No | Yes | Locked section visible on dashboard |
| Model benchmarks | No | Yes | "See how you compare" CTA |
| Vehicle timeline | 5 photos | Unlimited | Photo limit hits organically |
| Maintenance reminders | 3 reminders | Unlimited | Limit hits when they add 4th |
| Multi-vehicle | 2 vehicles | Unlimited | Limit hits for multi-car owners |
| Challenges | View only | Compete | "Go Premium to compete" |
| PDF export | No | Yes | Triggered when selling car |
| Document storage | No | Yes | "Store your insurance here" |
| Spending forecast | No | Yes | "What will your car cost next month?" |
| Keep vs. sell insights | No | Yes | Triggered at cost milestones |

---

## 8. MVP Scope Summary

| Priority | Feature Count | Estimated Effort | Required for Launch |
|---|---|---|---|
| Must Have (P0) | 10 features | 8-12 weeks | Yes |
| Should Have (P1) | 7 features | 4-6 weeks | Highly recommended |
| Could Have (P2) | 7 features | 4-7 weeks | No — defer to v1.1/v1.2 |
| Won't Have | 12 features | — | Explicitly excluded |

**Recommended MVP scope:** All Must Have + all Should Have = ~12-18 weeks for a solo developer.

**Minimum viable launch (if time-constrained):** Must Have only = ~8-12 weeks. Ship without photos, trends, challenges, quick-add widget, and share card. Add them in rapid follow-up releases.

**Non-negotiable for any launch:** M1 (auth), M2 (vehicle profiles), M3 (expense tracking), M4 (fuel entry), M5 (cost dashboard), M6 (vehicle timeline), M7 (reminders), M10 (onboarding).

---

## 9. Technical Considerations

*Note: Tech stack has not been finalized. These are framework-agnostic considerations.*

| Area | Consideration | Decision Needed |
|---|---|---|
| **Cross-platform** | iOS + Android from single codebase | Tech stack TBD — see technical architecture decision |
| **Backend API** | RESTful API for data sync, auth, benchmarks | Tech stack TBD |
| **Database** | Relational DB for structured expense/vehicle data | Tech stack TBD |
| **Image storage** | Cloud storage for vehicle and expense photos | Tech stack TBD — consider cost at scale |
| **Push notifications** | Required for maintenance reminders | Platform-specific (APNs + FCM) regardless of stack |
| **In-app purchases** | Apple IAP + Google Play Billing for premium subscription | Required — 30% commission factored into revenue model |
| **Analytics** | Track activation, retention, feature usage, paywall triggers | Choose analytics provider during tech architecture phase |
| **Localization** | Bulgarian primary, English secondary. Architecture must support i18n from day one. | Build with localization framework from start |
| **Data privacy** | GDPR compliance required. User data export/delete capabilities. | Design into data model from start |

---

## 10. Assumptions

| # | Assumption | Risk | Impact If Wrong |
|---|---|---|---|
| 1 | Solo developer can ship Must Have scope in 8-12 weeks | Medium | MVP delayed — reduce scope to absolute minimum (auth + vehicles + expenses + dashboard) |
| 2 | Quick-add UX can reduce expense logging to <10 seconds | Medium | Retention suffers — iterate on UX aggressively |
| 3 | 5 photos / 2 vehicles / 3 reminders are the right free-tier limits | Medium | Too generous = no conversion. Too tight = user resentment. Test and adjust post-launch. |
| 4 | Model benchmarks can be seeded with public data | Low | If public data is unreliable, delay benchmarks to when we have real user data |
| 5 | Users will tolerate no offline mode in MVP | Medium | If connectivity is an issue in Bulgaria (garages, rural areas), reconsider priority |

---

## Hands Off To

- **@developer** — This document defines what to build. Start with Must Have features. Use the effort estimates for sprint planning. Wait for technical architecture decision before starting implementation.
- **@strategist** — Feature-to-revenue mapping validates the monetization plan. Free/premium split is aligned with `/02-strategy/monetization-plan.md`.
- **@qa** — Use feature list to create test scenarios for each Must Have and Should Have feature.
- **@brand-architect** — Feature list defines the screens and interactions that need visual design direction.

---

## Next Steps

1. Finalize technical architecture and tech stack decision
2. Write Product Requirements Document (PRD) with detailed specifications → `/03-product/product-requirements-document.md`
3. Create user stories per epic (auth, vehicles, expenses, dashboard, timeline, reminders, gamification) → `/03-product/user-stories/`
4. Write functional specs for each feature area → `/03-product/functional-specs/`
5. Design database schema → `/03-product/technical/database-schema.md`
6. Create API specification → `/03-product/technical/api-specification.md`
