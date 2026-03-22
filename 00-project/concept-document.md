# Concept Document — Car Ownership Platform
## `/00-project/concept-document.md`
### Last updated: March 2026 | Version: 1.1

---

## 1. Vision

To become the **Operating System for Car Ownership** — a single platform that connects drivers, garages, dealerships, and fleet operators around the true cost and story of every vehicle.

**Long-term ambition:** A comprehensive ecosystem where every car has a living digital profile, every expense is tracked, every service record is connected, and real-world ownership data powers insights for the entire automotive industry.

---

## 2. The Problem

Car ownership is one of the largest ongoing expenses in most people's lives, yet almost nobody knows what their car actually costs them. Expenses are fragmented across fuel stops, mechanic visits, insurance renewals, tax payments, tire changes, modifications, parking, and fines. There is no single place to see the full picture.

**For individual drivers:**
- No consolidated view of total ownership cost (monthly, yearly, per kilometer)
- No way to predict upcoming costs or compare spending against other owners
- No organized record of a vehicle's maintenance and modification history
- Existing apps (Fuelio, Drivvo, Simply Auto) are basic expense loggers with no insights, no community, and no connection to the broader automotive ecosystem

**For automotive businesses:**
- Dealerships lack tools to calculate true profitability per vehicle (factoring in all hidden costs)
- Fleet operators manage vehicles with spreadsheets or expensive enterprise software
- Auto repair garages have no digital connection to their customers' vehicle records
- No shared data layer exists between drivers and service providers

---

## 3. The Solution

A digital platform built in phases, starting as a mobile app for individual drivers and expanding into a connected ecosystem for automotive businesses.

**Core idea:** Track every cost, build a living history of your vehicle, understand what your car truly costs, and connect with a community of car owners — all in one beautifully designed app made for people who love cars.

---

## 4. Target Users

### Primary (MVP Launch)
**Car enthusiasts in Bulgaria, aged 20-40**

These are people who own one or more vehicles and actively care about their cars. They modify, maintain, and discuss cars regularly. They're active in online car communities (Facebook groups, forums, Instagram). They spend significant money on their cars and want to understand and showcase that investment.

**Early adopter profile:** A 25-35 year old who owns a used European car (BMW, Audi, VW, Mercedes), regularly visits car forums and Facebook groups, does some maintenance himself, spends €200-500/month on his car, and currently has no clear picture of total costs.

### Secondary (Phase 2-3 Expansion)
- Cost-conscious everyday drivers who want to budget better
- Gig economy drivers (Bolt, Glovo, taxi) who need cost tracking for tax purposes
- Auto repair garages wanting a digital connection to customers
- Car dealerships (used car market is large in Bulgaria)
- Small fleet operators (construction, logistics, delivery, rental)

---

## 5. Product Modules (Phased)

### Module 1: Driver App (MVP — Phase 1)
The consumer-facing mobile application. Core experience for individual users.

**Key capabilities:**
- Expense tracking across all categories (fuel, maintenance, mods, insurance, tax, tires, parking, fines, car wash)
- Cost dashboard showing total cost per month, per year, and per kilometer
- Vehicle timeline — a chronological, visual history of everything done to the car, with photos and notes
- Maintenance reminders based on mileage and time intervals
- Multi-vehicle support
- Basic gamification — monthly challenges (cost/km, fuel efficiency), leaderboards, achievements
- Model benchmarks — compare your costs to other owners of the same car (requires critical mass of users)
- Shareable vehicle card for social media

**Premium features (paid tier):**
- Advanced analytics (predictive costs, keep-vs-sell insights)
- Detailed exportable reports (PDF service history, tax reports)
- Unlimited photo storage on vehicle timeline
- Document storage (insurance, registration, inspection dates)

Note: The app contains no advertising in any tier. Revenue comes exclusively from premium subscriptions.

### Module 2: Garage Integration (Phase 2)
Connects auto repair shops with vehicle owners on the platform.

**Key capabilities:**
- Garage customer and vehicle database (VIN, service history)
- Work order creation (problem, work performed, parts used, cost)
- Digital service book — when a garage performs maintenance, the record appears automatically in the car owner's app
- Customer communication tools

### Module 3: Dealer Module (Phase 3)
Helps car dealerships manage inventory and track true profitability.

**Key capabilities:**
- Vehicle inventory management
- True profitability calculator (purchase price + transport + repairs + detailing + advertising + fees vs. sale price)
- Analytics: average days to sell, ROI per vehicle, overall dealership profitability
- Vehicle history import from the driver app ecosystem

### Module 4: Fleet Module (Phase 3)
For companies operating multiple vehicles.

**Key capabilities:**
- Fleet dashboard (all vehicles, status, costs)
- Driver assignment and tracking
- Fuel consumption and maintenance expense tracking per vehicle
- Alerts for upcoming service, tire changes, expiring documents
- Cost reports per vehicle, per kilometer, per project/activity

### Module 5: Data Intelligence (Phase 4)
Leverages aggregated, anonymized user data to generate automotive insights.

**Key capabilities:**
- Real-world fuel consumption, maintenance costs, and reliability rankings by make/model
- True ownership cost benchmarks
- Paid industry reports for automotive media, leasing companies, insurance providers
- Analytics API access for partners
- Public dashboards for brand authority and traffic

**Data compliance:** All data processing must comply with GDPR — user consent, anonymization, aggregated reporting without exposing personal information.

---

## 6. Revenue Model

| Stream | Phase | Model |
|---|---|---|
| Premium subscriptions (B2C) | Phase 1 | €1.49-2.99/month or €14.99-24.99/year |
| In-app advertising | Phase 1 | Non-intrusive ads for free-tier users |
| Affiliate partnerships | Phase 1 | Commission on insurance, tires, parts referrals |
| Garage subscriptions (B2B) | Phase 2 | €30-50/month per garage |
| Dealer subscriptions (B2B) | Phase 3 | €50-100/month per dealership |
| Fleet subscriptions (B2B) | Phase 3 | €3-5/vehicle/month |
| Sponsored challenges | Phase 2+ | Brands pay to sponsor competitions |
| Data licensing & reports | Phase 4 | Anonymized ownership cost data sales |

**Strategic note:** B2C revenue alone (Bulgaria launch) will be modest (~€450K ARR by Year 3 under conservative projections). B2B modules are where revenue scales significantly. However, the B2C user base must come first — it creates the data, the ecosystem, and the leverage to attract business customers.

---

## 7. Market Context

### Market Opportunity
- ~250 million registered passenger cars in EU-27
- ~3.5 million registered cars in Bulgaria
- Average annual car ownership cost in Europe: €6,000-9,000
- Existing apps (Fuelio, Drivvo, Simply Auto) have millions of downloads, proving demand
- No competitor combines cost tracking + insights + community + business ecosystem

### Competitive Landscape
The market is fragmented. No single player dominates.

| Competitor | What They Do | What They Miss |
|---|---|---|
| Fuelio | Fuel & expense logging (Android) | No insights, no community, no iOS, dated UI |
| Drivvo | Expense tracking + income (gig drivers) | Cluttered, no social features, basic analytics |
| Simply Auto | Clean expense tracking + reminders | No community, no business features |
| Jerry | Insurance comparison + ownership tools | US-only, insurance-sales focused |
| Fleetio | Fleet management (B2B) | Enterprise-only, no consumer product |

**Primary competitive gap:** Nobody shows the TRUE total cost of ownership with real insights, benchmarks, and community. Every competitor is a logger, not an intelligence platform.

### Launch Market: Bulgaria
**Why Bulgaria first:**
- Home market — deep cultural understanding and access to car communities
- Strong car culture with active enthusiast communities
- Zero competition from existing apps (weak presence of Fuelio/Drivvo)
- Low operating costs — cheap to build, test, and iterate
- Ideal proving ground before expanding to larger European markets

**Expansion path:** Bulgaria → Romania → Regional (Greece, Serbia) → Western Europe (Germany, Netherlands)

---

## 8. Technology

**Approach:** Cross-platform mobile app built with .NET MAUI (C#), single codebase for Android and iOS.

**Stack:**
- Frontend: .NET MAUI with MVVM pattern
- Backend: ASP.NET Core Web API
- Database: PostgreSQL or SQL Server
- Authentication: ASP.NET Identity or Firebase Auth
- Image storage: Azure Blob Storage or Cloudflare R2
- Charts: Microcharts for MAUI or SkiaSharp

**Developer:** Solo founder/developer for MVP phase.

---

## 9. Development Phases

| Phase | Focus | Timeline (estimated) |
|---|---|---|
| Phase 1 | Driver app MVP — expense tracking, cost dashboard, vehicle timeline, reminders, basic challenges | 10-14 weeks |
| Phase 2 | Garage integration — digital service book, garage-driver connection | After 5,000+ active users |
| Phase 3 | Dealer module + fleet module | After 10,000+ active users |
| Phase 4 | Data intelligence platform | After significant data accumulation |

**MVP scope (Phase 1):**
- User registration and authentication
- Vehicle profile management (multi-vehicle)
- Categorized expense tracking with quick-add
- Specialized fuel entry (liters, price/liter, odometer, consumption calculation)
- Cost dashboard (monthly total, cost/km, category breakdown, trends)
- Vehicle timeline with photos and notes
- Maintenance reminders (mileage-based and date-based)
- Basic challenges and leaderboards (cost/km, fuel efficiency)
- Shareable vehicle summary card
- Freemium model with premium subscription

**Explicitly excluded from MVP:**
Offline sync, receipt OCR, bank integration, garage/dealer/fleet modules, social profiles, advanced predictions, data intelligence.

---

## 10. Key Assumptions & Risks

| Assumption | Risk Level | Validation Method |
|---|---|---|
| Car enthusiasts want to track costs, not just talk about cars | 🔴 High | Customer interviews |
| People will log expenses regularly (not just the first week) | 🔴 High | MVP retention data (30/60/90 day) |
| Manual data entry won't kill retention | 🔴 High | MVP usage data, solve with quick-add UX |
| Vehicle timeline is a strong engagement hook for enthusiasts | 🟡 Medium | Interviews + MVP feature usage |
| Gamification drives repeat usage | 🟡 Medium | A/B testing in MVP |
| 5%+ of free users will convert to premium | 🟡 Medium | Paywall testing post-launch |
| Bulgarian market is large enough to validate the concept | 🟢 Low | 3.5M cars, strong car culture |
| Users will share cost data and compete publicly | 🟡 Medium | Interviews + challenge participation rates |

---

## 11. Success Criteria for MVP

The MVP is successful if, within 3 months of launch:

1. **1,000+ downloads** — proves discoverability and interest
2. **25%+ 30-day retention** — proves ongoing value (users come back)
3. **Active users log 2+ expenses per week** — proves the habit forms
4. **5%+ premium conversion** — proves willingness to pay
5. **Positive qualitative feedback** — users say "I finally know what my car costs me"

If these criteria are NOT met, pivot or kill the idea before investing in Phase 2.

---

## 12. Strategic Positioning

**What we are:** The app that turns your car from a mystery expense into a story you understand, track, and share.

**What we are NOT:** An accounting tool, a marketplace, a social network, or an insurance broker.

**Positioning statement:**
For car enthusiasts in Bulgaria who spend significant money on their vehicles but have no visibility into what their car truly costs, our platform is a mobile app that tracks every expense, calculates the real cost of ownership, and lets you document and share your car's journey — unlike Fuelio or Drivvo, which are basic loggers with no insights, no community, and no connection to the automotive world.

---

## 13. Open Questions

These remain to be answered through research, interviews, and MVP testing:

1. What is the right premium price point for the Bulgarian market?
2. How critical is offline functionality for the MVP?
3. Should we target the EV segment specifically as an early niche?
4. What is the minimum number of users needed before model benchmarks become useful?
5. Can we establish garage partnerships before building the garage module (manual integration)?
6. What legal entity structure is optimal for a Bulgarian startup planning EU expansion?

---

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | March 2026 | Original concept from founder |
| 1.1 | March 2026 | Enriched with competitor analysis, market sizing, lean canvas, value proposition, MVP definition, and strategic decisions (Bulgaria launch, B2C first, car enthusiast focus, .NET MAUI tech stack) |
