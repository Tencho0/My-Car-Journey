# Product Requirements Document: Car Ownership Intelligence Platform — MVP

**Author:** Solo Founder
**Date:** 2026-03-06
**Status:** Draft
**Version:** 1.0 — Pre-validation
**Produced by:** @product-architect | Skill: create-prd

## References

- Concept document: `/00-project/concept-document.md`
- Lean Canvas: `/02-strategy/lean-canvas.md`
- Value Proposition: `/02-strategy/value-proposition.md`
- Competitor Analysis: `/01-discovery/research/competitor-analysis.md`
- Market Sizing: `/01-discovery/research/market-sizing.md`
- MVP Feature List: `/03-product/mvp-feature-list.md`
- Positioning Strategy: `/02-strategy/positioning-strategy.md`
- Monetization Plan: `/02-strategy/monetization-plan.md`
- Interview Guide: `/01-discovery/customers/interview-guide.md`

---

### 1. Executive Summary

We are building a cross-platform mobile app that shows car enthusiasts in Bulgaria the true cost of owning their car — per month, per year, and per kilometer — with a visual vehicle timeline, maintenance reminders, and basic gamification. The MVP targets 150,000-250,000 active car enthusiasts aged 20-40 who spend significant money on their vehicles but currently have no consolidated view of their total costs. Unlike Fuelio, Drivvo, and Simply Auto, which are passive expense loggers, our product is a **car ownership intelligence platform** that transforms raw expense data into actionable cost insights, model-based benchmarks, and a car culture experience worth returning to.

The MVP validates one core hypothesis: **car enthusiasts will track their expenses if the app gives them a number they can't get anywhere else — their true cost of ownership.**

---

### 2. Background & Context

#### Context

Car ownership is one of the largest ongoing personal expenses, yet almost nobody knows what their car actually costs them. Expenses are fragmented across fuel, maintenance, modifications, insurance, tax, tires, parking, fines, and car washes. There is no single place to see the full picture. In Bulgaria specifically, a strong car culture exists (3.5 million registered cars, active Facebook communities with 300K+ combined members) but zero dedicated local tools to track ownership costs.

#### Why Now?

1. **Rising costs create urgency.** Fuel prices, parts inflation, and growing used car prices post-COVID are increasing the pain of cost blindness. Bulgarian car owners are spending more and understanding less.
2. **No local competition.** Fuelio, Drivvo, and Simply Auto have weak-to-zero presence in Bulgaria. No app has Bulgarian localization, local community integration, or cultural fit. The market is open.
3. **Community distribution is free.** The target segment (car enthusiasts 20-40) is concentrated in identifiable Facebook groups, forums (Auto.bg), and car meets. Organic distribution is efficient and nearly zero-cost.
4. **Category is unclaimed.** Every existing app is positioned as an "expense tracker." Nobody has claimed "car ownership intelligence." First mover in this category creates lasting positioning advantage.

#### Prior Art

| Product | What It Does | What It Misses |
|---|---|---|
| **Fuelio** | Fuel + basic expense logging (Android only) | No total cost view, no insights, no iOS, dated UI, no community |
| **Drivvo** | Cross-platform expense tracking + income | Cluttered UI, feels like accounting software, no intelligence layer |
| **Simply Auto** | Clean expense tracking + reminders | No benchmarks, no social features, no enthusiasm for car culture |
| **Jerry** | Insurance comparison + ownership tools (US only) | Not in Europe, insurance-focused, not for enthusiasts |
| **Doing nothing** | Zero effort | Complete blindness to real costs — the #1 competitor |

*Full analysis: `/01-discovery/research/competitor-analysis.md`*

#### Evidence

| Evidence Type | Source | Key Finding |
|---|---|---|
| Competitor gap analysis | `/01-discovery/research/competitor-analysis.md` | No competitor provides total cost of ownership, benchmarks, or car culture design |
| Market sizing | `/01-discovery/research/market-sizing.md` | 150K-250K active car enthusiasts in Bulgaria; ~€10K Year 1 ARR (validation, not revenue) |
| App store sentiment | Competitor analysis, Section 7 | Users repeatedly ask for "insights, not just logging" and "comparison with other owners" |
| Community behavior | Lean canvas, Facebook group analysis | 300K+ members actively discuss car costs in Bulgarian Facebook groups |
| Customer interviews | **NOT YET CONDUCTED** | Interview guide ready at `/01-discovery/customers/interview-guide.md` — 15-20 interviews needed |

**Critical gap:** Customer interviews have not been conducted. All pains and gains are hypothesized based on secondary research. This PRD should be updated after interviews validate or refute assumptions.

---

### 3. Objectives & Success Metrics

#### Goals

1. **Validate that car enthusiasts will track their expenses in an app** — prove the behavior change is possible
2. **Deliver the "aha moment"** — users see their true monthly car cost and cost per kilometer for the first time
3. **Achieve sustainable retention** — users return weekly to log expenses, not just in week 1
4. **Prove willingness to pay** — a meaningful percentage of free users upgrade to premium
5. **Build the foundation for scale** — architecture supports Romania expansion and future B2B modules

#### Non-Goals (Explicitly Out of Scope)

These are things we are **deliberately not doing** in the MVP. They are not oversights.

| # | Non-Goal | Why Excluded |
|---|---|---|
| NG1 | **Offline sync** | Adds significant complexity (conflict resolution, local DB, sync logic). Most Bulgarian users have mobile data. Validate need through interviews. |
| NG2 | **Receipt OCR** | ML/API dependency, accuracy issues, adds scope. Quick-add UX is the MVP answer to data entry friction. |
| NG3 | **Bank/card integration** | Requires banking APIs, PSD2 compliance. Not feasible for a solo developer MVP. |
| NG4 | **Garage/dealer/fleet modules** | B2B features. Different users, different needs. Build after 5,000+ active B2C users. |
| NG5 | **Social profiles and following** | Adds moderation burden and social complexity. Gamification (challenges/leaderboards) provides social engagement without the overhead. |
| NG6 | **Advanced ML predictions** | Requires data scale we won't have at launch. Use simple pattern matching for forecasts (premium). |
| NG7 | **Income tracking** | Targets gig economy drivers (different segment). Adds UI complexity. Evaluate for Phase 2. |
| NG8 | **Marketplace / parts shopping** | Requires supplier partnerships and e-commerce infrastructure. Phase 3+. |
| NG9 | **Being an accounting tool** | We are a car ownership intelligence app, not a bookkeeping tool. We celebrate cars, not crunch numbers. |
| NG10 | **Targeting everyone** | We serve car enthusiasts first. Diluting for "all car owners" weakens the product and messaging. Broaden after validating the niche. |

#### Success Metrics

**North Star Metric:** Weekly active users who log at least 1 expense.

| Metric | Current Baseline | Target | How Measured | Timeframe |
|---|---|---|---|---|
| Downloads | 0 | 1,000+ | App Store / Google Play analytics | Month 3 post-launch |
| Downloads | 0 | 12,000+ | App Store / Google Play analytics | Month 12 post-launch |
| Activation rate (add vehicle + log 1st expense in session 1) | 0% | 60%+ | In-app analytics | Month 3 post-launch |
| 30-day retention (return and log expense within 30 days) | Unknown | 25%+ | Cohort analysis | Month 3 post-launch |
| 30-day retention | Unknown | 30%+ | Cohort analysis | Month 6 post-launch |
| Expenses logged per active user per week | 0 | 2+ | In-app analytics | Month 3 post-launch |
| Premium conversion rate (of MAU) | 0% | 5%+ | Subscription analytics | Month 6 post-launch |
| Premium conversion rate (of MAU) | 0% | 7.5% | Subscription analytics | Month 12 post-launch |
| Monthly recurring revenue | €0 | €270+ | Payment provider | Month 6 post-launch |
| Monthly recurring revenue | €0 | €810+ | Payment provider | Month 12 post-launch |
| Share/referral rate | 0% | 5%+ of users share vehicle card | In-app analytics | Month 6 post-launch |

**Kill criteria:** If, 3 months post-launch, 30-day retention is below 15% AND premium conversion is below 3%, reconsider the core hypothesis before investing further.

*Revenue projections: `/02-strategy/monetization-plan.md` Section 5*
*Market sizing: `/01-discovery/research/market-sizing.md` Section 5*

---

### 4. Target Users & Segments

#### Primary Segment: Car Enthusiasts in Bulgaria, aged 20-40

**Defined by behavior, not demographics:**
People who actively care about their cars. They maintain, modify, and discuss their vehicles regularly. They're active in at least one online car community. They spend significant money on their cars but don't know the total.

| Attribute | Detail |
|---|---|
| Segment size | ~150,000-250,000 in Bulgaria (estimated from Facebook group membership) |
| Vehicles owned | 1-3, typically used European (BMW, Audi, VW, Mercedes) |
| Monthly car spending | €200-500 (fuel, maintenance, mods, insurance, tax, tires, parking) |
| Digital behavior | Smartphone-native, active on Facebook/Instagram, comfortable with apps |
| Current tracking method | Nothing (most common), mental math, occasional spreadsheet |
| Community activity | Facebook car groups, Auto.bg forum, car meets, Instagram car accounts |

#### Early Adopter Profile

A 25-35 year old male who owns a used BMW E46/E90, is active in BMW Club Bulgaria on Facebook, does some maintenance himself but uses a trusted mechanic for bigger jobs, spends ~350 лв/month on his car, and has no idea that's 4,200 лв/year. He'd be surprised — and engaged — if an app showed him that number.

#### Current Workarounds

| Method | Used By | Why It Fails |
|---|---|---|
| Do nothing | ~70% of target segment | Complete blindness to costs |
| Mental math / memory | ~20% | Wildly inaccurate, forgets 50%+ of expenses |
| Spreadsheet / notes app | ~5% | Tedious, abandoned after 2 weeks |
| Fuelio / Drivvo | ~5% | Basic loggers, no insights, no car culture |
| Ask Facebook groups | Common for specific questions | Unreliable, anecdotal, answers disappear |

#### Constraints

- **Geography:** Bulgaria only at MVP launch. Architecture must support Romanian expansion by Month 9-12.
- **Language:** Bulgarian primary, English secondary. i18n must be built in from day one.
- **Purchasing power:** Bulgarian users are price-sensitive. €2.99/month is at the high end of what they typically pay for app subscriptions. The free tier must be genuinely useful.
- **Connectivity:** Most urban users have reliable mobile data. Rural/garage connectivity may be spotty — validate whether offline is needed through interviews.

*Segment sizing: `/01-discovery/research/market-sizing.md` Section 4*
*Personas: Not yet created — pending interview data. See `/01-discovery/customers/interview-guide.md`*

---

### 5. Value Propositions

#### Customer Jobs We're Addressing

| Priority | Job | Type |
|---|---|---|
| Critical | Budget for and understand car-related expenses (F3) | Functional |
| Critical | Feel in control of a major expense (E1) | Emotional |
| High | Maintain and repair their car efficiently (F1) | Functional |
| High | Pay for fuel efficiently and track consumption (F2) | Functional |
| High | Stay on top of maintenance schedules (F7) | Functional |
| High | Feel smart about car ownership decisions (E2) | Emotional |
| Medium | Keep records for when they sell the car (F5) | Functional |
| Medium | Show their car to others, compare with peers (S1, S2) | Social |

#### Pains We're Relieving

| Pain | Severity | Our Answer |
|---|---|---|
| **P1: Don't know true monthly/yearly car cost** | High | Cost dashboard — one number, instantly |
| **P2: Surprised by unexpected costs** | High | Category trends + spending forecasts |
| **P3: No organized vehicle history** | High | Vehicle timeline with photos and notes |
| **P8: Tedious manual data entry** | High | Quick-add UX (<10 seconds per entry) |
| **P5: Forget maintenance deadlines** | Medium | Smart reminders (date + mileage) |
| **P4: Can't compare costs to other owners** | Medium | Model benchmarks (seeded, then real data) |
| **P7: No proof of history when selling** | Medium | PDF service history export |

#### Gains We're Creating

| Gain | Importance | Feature |
|---|---|---|
| **G1: See exact monthly car cost** | Critical | Cost dashboard |
| **G2: Understand cost per kilometer** | High | Cost/km calculation (premium) |
| **G8: Save time tracking** | High | Quick-add, smart defaults |
| **G4: Never forget maintenance** | High | Push notification reminders |
| **G5: Beautiful, complete car record** | High | Vehicle timeline |
| **G3: Know how you compare** | High | Model benchmarks |
| **G9: Have fun with car ownership** | Medium | Challenges and leaderboards |

#### Competitive Advantage

**We are the only app that:**
1. Calculates your complete cost of ownership — every category, per month, per kilometer
2. Feels like it was built by car enthusiasts for car enthusiasts (not accounting software)
3. Tells you how your costs compare to other owners of the same car model

**Value curve — where we deliberately over-invest vs. under-invest:**

| Axis | Us | Competitors |
|---|---|---|
| Cost intelligence (total cost, cost/km, trends) | Over-invest | Basic or none |
| Car culture design (timeline, photos, mods) | Over-invest | None |
| Community (challenges, benchmarks, share cards) | Over-invest | None |
| Offline capability | Under-invest (MVP) | Fuelio: strong |
| Income tracking | Under-invest (MVP) | Drivvo: has it |
| Enterprise/fleet features | Under-invest (MVP) | Fleetio: strong |

*Full positioning: `/02-strategy/positioning-strategy.md`*
*Full value prop canvas: `/02-strategy/value-proposition.md`*

---

### 6. Solution

#### 6.1 Key Features (Priority-Tiered)

**P0 — Must Have (MVP cannot launch without):**

| # | Feature | User Story | Acceptance Criteria |
|---|---|---|---|
| M1 | **User authentication** | As a user, I want to create an account and log in securely so my data is protected and synced. | Registration via email+password. Login, logout, password reset. Session persistence. Consider OAuth (Google/Apple/Facebook) for reduced friction. |
| M2 | **Vehicle profile management** | As a car owner, I want to add my car's details so the app knows what I drive. | Add/edit/delete vehicles. Fields: make, model, year, fuel type, license plate (optional), current odometer, photo. Free: 2 vehicles. Premium: unlimited. |
| M3 | **Expense tracking** | As a car owner, I want to log any car expense quickly so I can see where my money goes. | Log expense with: amount, date, category, subcategory, odometer (optional), notes. Categories: fuel, maintenance, modifications, insurance, tax, tires, parking, fines, car wash, other. Entry must complete in <10 seconds via quick-add flow with smart defaults. Unlimited entries in free tier. |
| M4 | **Fuel entry (specialized)** | As a driver, I want a dedicated fuel logging flow so I can track consumption accurately. | Dedicated fuel form: liters, price/liter, total cost (auto-calculate), odometer reading, full/partial fill, station name (optional). Auto-calculate L/100km between fill-ups. Shows consumption trend. |
| M5 | **Cost dashboard** | As a car owner, I want to see what my car costs me at a glance so I finally know the real number. | **This is the "aha moment" screen.** Shows: monthly total cost (big, prominent number), category breakdown (pie/donut chart), monthly trend (bar chart, last 6 months). Tap to drill into any month or category. Free tier: monthly total + basic breakdown. Premium: adds cost/km, year-over-year trends, spending forecasts, model benchmarks. The dashboard must feel impactful — design for clarity, not density. |
| M6 | **Vehicle timeline** | As a car enthusiast, I want a chronological record of everything I've done to my car so I can see its full story. | Chronological feed: each entry shows date, category icon, amount, notes, photos (if any). Filterable by category. Newest first. Scrollable. Must feel like a "car journal" — visual, photo-rich. Free: 5 photos total. Premium: unlimited. |
| M7 | **Maintenance reminders** | As a car owner, I want to be reminded before maintenance is due so I never miss an oil change or inspection. | Create reminders based on: date (e.g., insurance renewal March 15), mileage interval (e.g., oil change every 10,000 km), or both. Push notification when due/overdue. Free: 3 active reminders. Premium: unlimited. Mileage-based reminders require periodic odometer updates. |
| M8 | **Multi-vehicle support** | As an enthusiast who owns multiple cars, I want to switch between vehicles and see each one's data separately. | Vehicle selector in header/nav. Each vehicle has its own timeline, dashboard, and reminders. Aggregate "all vehicles" view optional. Free: 2 vehicles. Premium: unlimited. |
| M9 | **User profile & settings** | As a user, I want to configure the app to my preferences. | Edit name, email. Settings: currency (лв primary, € secondary), distance unit (km), language (Bulgarian/English), notification preferences (on/off, quiet hours). |
| M10 | **Onboarding flow** | As a new user, I want to be guided to get value quickly so I understand what the app does. | First-launch flow: Welcome → Add your car (make/model/year) → Log your first expense → See your dashboard. Target: 60%+ of new users complete this flow. Must reach the "aha moment" (seeing a cost on the dashboard) within session 1. Skip option available but discouraged. |

**P1 — Should Have (high impact, include if time allows):**

| # | Feature | User Story | Acceptance Criteria |
|---|---|---|---|
| S1 | **Photo attachments** | As an enthusiast, I want to attach photos to expenses so I can document work done on my car. | Attach 1-3 photos per expense/timeline entry. Camera capture or gallery selection. Compress on upload. Free: 5 photos total across all vehicles. Premium: unlimited. |
| S2 | **Spending trends** | As a user, I want to see how my spending changes over time so I can spot patterns. | Year-over-year comparison, category trend lines, monthly spending graph (6-12 months). Premium feature. Requires 2+ months of data. Show "unlock after 2 months of tracking" for new users. |
| S3 | **Quick-add widget** | As a frequent user, I want to log an expense from my home screen in 2 taps. | Home screen widget or app shortcut. Pre-filled: today's date, default vehicle. User enters: amount + category. Optional: note. Saves in <5 seconds. Platform-specific implementation required. |
| S4 | **Basic challenges** | As a competitive car enthusiast, I want to compete with others on cost efficiency. | Monthly challenges: lowest cost/km, best fuel efficiency, most expenses logged. Anonymous leaderboard. Free: view leaderboards. Premium: compete and earn badges. Start with 2-3 challenges. Reset monthly. |
| S5 | **Shareable vehicle card** | As a proud car owner, I want to share my car's stats on social media. | Generate a visual card image: car photo, name/model, key stats (total cost, cost/km if premium, mods count, months tracked). One-tap share to Instagram/WhatsApp/Facebook. Includes subtle app branding (logo + download link). Design must be share-worthy. |
| S6 | **Expense category icons** | As a user, I want visual categories so expense types are instantly recognizable. | Custom icon per category. Pre-defined 10 categories with icons. Custom "other" subcategories with generic icon. Used in timeline, dashboard breakdown, and expense entry. |
| S7 | **Currency formatting** | As a Bulgarian user, I want to see costs in лева so numbers feel natural. | Proper лв formatting (e.g., 847.50 лв). Optional € display. Settings control which currency is primary. Auto-conversion display available. |

**P2 — Won't Have Now (explicitly deferred):**

| # | Feature | Reason for Deferral |
|---|---|---|
| C1 | **Model benchmarks** | Core differentiator but requires critical mass of user data. Seed with public data for popular models. Becomes real at 500-1,000+ active users per model. Target for v1.1-v1.2. |
| C2 | **PDF service history export** | High perceived value, good premium conversion trigger. Deferred due to effort. Target for v1.1. |
| C3 | **Document storage** | Convenience feature. Low effort but not core to the "aha moment." Target for v1.1. |
| C4 | **Spending forecast** | Requires 3+ months of user data to be meaningful. Simple pattern matching, not ML. Target for v1.2. |
| C5 | **Keep vs. sell insights** | Premium conversion trigger. Requires sufficient historical data. Target for v1.2. |
| C6 | **Achievement badges** | Low effort, adds delight. Not critical. Target for v1.1. |
| C7 | **Dark mode** | Expected by modern users. Can defer briefly. Target for v1.1. |
| W1-W12 | **Offline sync, OCR, bank integration, B2B modules, social profiles, ML predictions, income tracking, marketplace, VIN decoder, data intelligence** | See full rationale in `/03-product/mvp-feature-list.md` Section 5. |

*Full feature details, effort estimates, and pain/gain mapping: `/03-product/mvp-feature-list.md`*

---

#### 6.2 User Flows

**Critical user journeys (to be detailed in `/03-product/user-flows.md`):**

**Flow 1: First-Time User (Onboarding → Aha Moment)**
```
Download → Launch → Create Account → Add Vehicle (make/model/year/photo)
→ Log First Expense (guided) → See Dashboard with first cost entry
→ "Your car has cost you X лв so far. Keep tracking to see your monthly total."
```
**Success = user completes this in <3 minutes and sees their dashboard.**

**Flow 2: Returning User (Quick Expense Logging)**
```
Open app (or widget) → Quick-add: Amount + Category → Save
→ Dashboard updates instantly → Back to life in <10 seconds
```
**Success = <10 seconds from intent to saved expense.**

**Flow 3: Dashboard Discovery (Monthly Review)**
```
Open app → View dashboard → See monthly total → Tap category breakdown
→ See where money goes → (Premium tease: "Unlock cost per km →")
→ Review timeline → See car's full history
```
**Success = user has an emotional reaction to their cost number.**

**Flow 4: Maintenance Reminder**
```
Push notification: "Oil change due in 500 km" → Tap → See reminder detail
→ After service: Log expense → Update odometer → Reminder resets
```
**Success = user acts on reminder and logs the resulting expense.**

**Flow 5: Premium Conversion**
```
User sees dashboard monthly total (free) → Locked section: "Your cost per km: [locked]"
→ "See how you compare to other [model] owners → Go Premium"
→ Paywall screen: €2.99/month or €24.99/year → Subscribe
→ Dashboard unlocks: cost/km, trends, benchmarks
```
**Success = natural value escalation, not aggressive gate.**

---

#### 6.3 UX Direction

**Design principles:**

1. **Car culture, not accounting.** The app must feel like it was built by someone who loves cars. Vehicle timeline should feel like a car journal. Dashboard should feel like a cockpit, not a spreadsheet. Use automotive-inspired design language.

2. **Clarity over density.** The dashboard shows ONE big number (monthly total). Details are one tap away. Never overwhelm. The "aha moment" is a clean, impactful number — not a cluttered data table.

3. **Speed over completeness.** Quick-add is the default. Full-detail entry is optional. Every screen prioritizes the most common action. Logging an expense must be faster than opening a notes app.

4. **Progressive disclosure.** Free tier: simple and useful. Premium: reveals depth. Don't show locked features aggressively — tease them contextually ("Unlock cost per km" when they're already looking at their costs).

5. **Bulgarian first.** All UI text, categories, currency formatting, and date formats default to Bulgarian. English is a secondary language option, not the primary.

**Key screens (to be wireframed in `/03-product/wireframes.md`):**
- Dashboard (the hero screen — monthly total, breakdown, trends)
- Vehicle timeline (scrollable feed with photos)
- Quick-add expense (minimal form)
- Fuel entry (specialized form)
- Vehicle profile
- Reminders list
- Challenges / leaderboard
- Settings
- Onboarding flow (3-4 screens)
- Premium paywall

---

#### 6.4 Technical Approach

**Tech stack status: PENDING DECISION**

The technology stack has not been finalized. A dedicated technical architecture decision document will be produced at `/03-product/technical/architecture.md` evaluating options against the following requirements. This PRD intentionally does not prescribe specific technologies.

**Technical requirements:**

| Requirement | Description | Priority |
|---|---|---|
| **Cross-platform mobile** | Single codebase producing both iOS and Android apps | P0 — Must |
| **Backend API** | RESTful (or equivalent) API for authentication, data sync, user management, subscription validation, and benchmark calculations | P0 — Must |
| **Relational database** | Structured storage for users, vehicles, expenses, reminders, challenges. Must support efficient queries for dashboard aggregation and timeline retrieval. | P0 — Must |
| **Authentication service** | Email+password registration, OAuth (Google/Apple/Facebook), session management, password reset | P0 — Must |
| **Image storage** | Cloud storage for vehicle photos and expense attachments. Must handle compression on upload and efficient retrieval. Consider cost at scale. | P1 — Should |
| **Push notifications** | APNs (iOS) + FCM (Android) for maintenance reminders, challenge updates, and engagement nudges | P0 — Must |
| **In-app purchases** | Apple IAP + Google Play Billing for premium subscription management. 30% platform commission factored into revenue model. | P0 — Must |
| **Analytics/telemetry** | Track activation, retention, feature usage, paywall trigger performance. Needed for success metric measurement. | P1 — Should |
| **Localization framework** | i18n from day one. Bulgarian primary, English secondary. Architecture must support adding Romanian without refactoring. | P0 — Must |
| **GDPR compliance** | User data export and deletion capabilities. Consent management. Anonymized data for any aggregation features. | P0 — Must |
| **Charting/visualization** | Cost dashboard requires: pie/donut chart, bar chart, line chart (trends). Must render smoothly on mobile. | P0 — Must |

**Solo developer constraints that shape the solution:**

- **Prefer simplicity.** Choose proven, well-documented technologies with strong community support. Avoid bleeding-edge or complex architectures.
- **Minimize infrastructure.** Use managed services where possible (managed database, managed auth, CDN for images). Don't self-host what can be managed.
- **Single deploy pipeline.** One build process for both platforms. Avoid maintaining separate codebases.
- **Operational overhead.** The founder is the sole operator. Monitoring, error reporting, and automated alerts are required from day one.
- **Cost sensitivity.** Infrastructure costs must stay under €50-130/month pre-revenue. Use free tiers where possible.

**Technical decisions pending:**

| Decision | Options to Evaluate | Decides |
|---|---|---|
| Mobile framework | Cross-platform options (evaluate trade-offs: native feel, community support, dev speed, solo dev feasibility) | `/03-product/technical/architecture.md` |
| Backend framework | Evaluate options based on: API speed, auth libraries, ORM support, hosting costs, developer familiarity | `/03-product/technical/architecture.md` |
| Database | Evaluate relational options based on: hosting cost, managed availability, tooling, ORM compatibility | `/03-product/technical/architecture.md` |
| Auth provider | Build vs. managed service (trade-off: control vs. development speed) | `/03-product/technical/architecture.md` |
| Hosting/cloud | Evaluate based on: cost at low scale, managed services, European data residency (GDPR) | `/03-product/technical/architecture.md` |
| Image storage | Evaluate based on: cost per GB, CDN, upload/compress pipeline | `/03-product/technical/architecture.md` |

---

#### 6.5 Assumptions

| # | Assumption | Risk Level | How to Validate |
|---|---|---|---|
| A1 | Car enthusiasts want to know their true cost (not just talk about cars) | High | Customer interviews: Q3, Q7, Q13 in interview guide |
| A2 | Users will log expenses regularly, not just in week 1 | High | MVP 30/60/90-day retention data |
| A3 | Manual data entry won't kill retention (quick-add solves the friction problem) | High | MVP usage data + UX iteration on quick-add flow |
| A4 | The cost dashboard "aha moment" (seeing your real monthly number) creates retention | High | Interview reactions (Q13) + post-launch feature engagement |
| A5 | €2.99/month is acceptable in the Bulgarian market | Medium | Interview pricing probe (Q12) + post-launch conversion data |
| A6 | Vehicle timeline appeals emotionally to enthusiasts (not just utility) | Medium | Interviews (Q10) + MVP feature usage |
| A7 | Gamification (challenges, leaderboards) drives return visits | Medium | A/B testing post-launch |
| A8 | Facebook car groups are an effective, low-cost distribution channel | Medium | Track installs from group posts in first 3 months |
| A9 | 7.5% premium conversion is achievable with our intelligence value gap | Medium | Post-launch conversion data. Industry average is 5%. |
| A10 | Solo developer can ship P0 scope in 8-12 weeks | Medium | Track actual velocity after tech stack decision. Reduce scope if behind. |
| A11 | Users will tolerate no offline mode in MVP | Medium | Interview feedback + post-launch support requests |
| A12 | Model benchmarks can be seeded with public automotive cost data | Low | Research publicly available average cost data for popular BG car models |
| A13 | Bulgarian market is large enough to validate product-market fit | Low | 3.5M cars, 150K-250K enthusiasts. Partially validated by market sizing. |

**Existential assumptions (A1-A4):** If any of these are false, the product concept may be fundamentally flawed. Customer interviews must probe these aggressively before committing to full development.

---

### 7. Open Questions

| # | Question | Owner | Deadline | Resolution |
|---|---|---|---|---|
| Q1 | **What tech stack should we use?** Cross-platform mobile framework, backend, database, hosting. | @product-architect | Before development starts | Produce `/03-product/technical/architecture.md` with recommendation |
| Q2 | **Do customer interviews validate the core hypothesis?** Will enthusiasts actually track costs? | @customer-analyst | Within 3 weeks | Conduct 15-20 interviews per `/01-discovery/customers/interview-guide.md` |
| Q3 | **Is €2.99/month the right price for Bulgaria?** | @strategist | After interviews | Interview pricing probes + post-launch A/B test |
| Q4 | **How critical is offline functionality for MVP?** Do target users need to log expenses at garages or in rural areas with poor connectivity? | @customer-analyst | After interviews | Interview Q4 context + direct question |
| Q5 | **What product name do we use?** | @brand-architect | Before development starts | Run `/name-product` command |
| Q6 | **What is the minimum user count before model benchmarks become useful?** | @product-architect | Before v1.1 planning | Estimate: 500-1,000 active users per model. Validate with data analysis. |
| Q7 | **Should we target EV owners as a specific sub-niche?** | @strategist | After interviews | Probe EV ownership in interviews. Decide based on segment size. |
| Q8 | **What legal entity structure for a Bulgarian startup planning EU expansion?** | @ops-legal | Before launch | Produce legal setup recommendation |
| Q9 | **Can we seed benchmark data from public sources?** What public datasets exist for Bulgarian car ownership costs? | @researcher | Before v1.1 planning | Research public automotive cost databases |
| Q10 | **Which Facebook groups will allow promotional posts?** Community guidelines vary. | @growth-lead | Before launch | Research group rules, establish presence as community member first |

---

### 8. Timeline & Phasing

#### Phase 1: MVP (v1.0)

**Scope:** All P0 features (M1-M10) + as many P1 features (S1-S7) as time allows.

**Minimum viable launch (if time-constrained):** M1 (auth), M2 (vehicle profiles), M3 (expense tracking), M4 (fuel entry), M5 (cost dashboard), M6 (vehicle timeline), M7 (reminders), M9 (settings), M10 (onboarding). Ship without photos, trends, challenges, quick-add widget, and share card. Add them in rapid follow-up releases.

**Estimated effort:**
- P0 only: ~8-12 weeks (solo developer, tech stack dependent)
- P0 + P1: ~12-18 weeks
- Recommended: Ship P0 + S3 (quick-add) + S6 (icons) + S7 (currency) at minimum. These directly impact retention.

**Key milestone:** A working app that a car enthusiast can download, add their car, log 5 expenses, and see their monthly total on the dashboard. That's the MVP. Everything else enhances it.

**Pre-development blockers:**
1. Tech stack decision → `/03-product/technical/architecture.md`
2. Customer interview results (ideally — but can start development in parallel)
3. Product naming → `/04-brand/naming-exploration.md`

#### Phase 1.1 (v1.1 — Fast Follow)

**Trigger:** MVP launched, initial user feedback collected, no critical blockers.
**Scope:** P2 features that enhance retention and conversion:
- C1: Model benchmarks (seeded with public data)
- C2: PDF service history export
- C3: Document storage
- C6: Achievement badges
- C7: Dark mode
- Any P1 features not included in v1.0

**Estimated effort:** 4-6 weeks.

#### Phase 1.2 (v1.2 — Intelligence Update)

**Trigger:** 2-3 months of user data accumulated. Enough data for meaningful trends and forecasts.
**Scope:**
- C4: Spending forecasts (simple pattern matching)
- C5: Keep vs. sell insights
- Model benchmark improvements (real user data supplementing seeded data)
- UX refinements based on user feedback

**Estimated effort:** 3-5 weeks.

#### Phase 2 (Post-Validation)

**Trigger:** All 5 success criteria met (1,000+ downloads, 25%+ retention, 2+ expenses/week, 5%+ conversion, positive feedback). Active user base of 5,000+.

**Scope:**
- Romania expansion (localization + market entry)
- Garage integration module (digital service book, B2B subscriptions)
- Affiliate partnerships (insurance, tires, parts referrals)
- Potentially: offline sync, income tracking (based on user feedback)

#### Phase 3+ (Future)

- Dealer module (inventory, profitability)
- Fleet module (multi-vehicle business dashboard)
- Advanced ML predictions (maintenance forecasting, depreciation curves)
- Data intelligence platform (anonymized, aggregated reports)
- Additional market expansion (Greece, Serbia, Western Europe)

---

## Appendix: Feature-to-Pain Traceability

Every P0 and P1 feature maps to a validated pain or gain from `/02-strategy/value-proposition.md`:

| Feature | Primary Pain/Gain | Why P0/P1 |
|---|---|---|
| M1: Auth | Foundation | Can't have an app without accounts |
| M2: Vehicle profiles | F6 (manage vehicles), G5 (car record) | Core data model |
| M3: Expense tracking | P1 (don't know cost), G1 (see exact cost) | The core interaction |
| M4: Fuel entry | F2 (track fuel), G2 (cost/km) | Most frequent expense type |
| M5: Cost dashboard | P1, P2, G1, G2 | **The "aha moment" — the entire product exists for this screen** |
| M6: Vehicle timeline | P3 (no history), G5 (car record) | Key differentiator vs. all competitors |
| M7: Maintenance reminders | P5 (forget maintenance), G4 (never forget) | Table stakes — competitors do this |
| M8: Multi-vehicle | F6 (manage multiple cars) | Enthusiasts own 2-3 cars |
| M9: Settings | Foundation | Localization, currency, preferences |
| M10: Onboarding | Activation | Gets user to "aha moment" in session 1 |
| S1: Photos | P3, G5 | Makes timeline visual and emotional |
| S2: Trends | P2, G7 | Adds depth to dashboard (premium) |
| S3: Quick-add | P8, G8 | **Directly addresses the #1 retention killer** |
| S4: Challenges | G9 | Engagement + retention hook |
| S5: Share card | G6 | Organic growth driver |
| S6: Category icons | G8 | Polish — makes categories scannable |
| S7: Currency | Foundation | Bulgarian users think in лева |

---

## Hands Off To

- **@developer** — This PRD defines what to build. Wait for technical architecture decision before starting. Start with P0 features. Use `/03-product/mvp-feature-list.md` for effort estimates.
- **@brand-architect** — Screen list and UX direction inform visual design and brand expression. Run `/name-product` to establish product name.
- **@qa** — Use feature acceptance criteria to create test scenarios. Run `/write-tests` for structured test generation.
- **@customer-analyst** — Validate assumptions A1-A6 through interviews. PRD should be updated post-interviews.
- **@strategist** — Kill criteria and success metrics align with monetization plan. Review if interview results change assumptions.

---

## Post-Generation Options

- **Tighten scope?** Several P1 features could be pushed to P2 if timeline is tight. S2 (trends) and S4 (challenges) are the most deferrable.
- **Run a pre-mortem?** Identify what could go wrong: retention cliff at week 2, quick-add UX too slow, pricing too high for Bulgaria, Facebook groups ban promotional posts.
- **Write functional specs?** `/write-spec expense-tracking` — detail the most critical feature. `/write-spec cost-dashboard` — detail the "aha moment" feature.
- **Break into user stories?** `/write-stories epic-1-onboarding` — start with the activation flow.
- **Design the database?** Produce `/03-product/technical/database-schema.md` — requires tech stack decision first.

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-06 | Initial PRD. Pre-validation — customer interviews not yet conducted. Tech stack pending. |
