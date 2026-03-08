# DEC-009: Backend Approach

**File:** `/00-project/decisions/DEC-009-backend-approach.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-08
**Status:** Decided
**Decision:** ASP.NET Core Web API — Modular Monolith with Controllers, deployed on VPS

---

## Context

The backend API is the single source of truth for the entire platform. Both the Flutter mobile app and the Blazor WASM web app consume the same API. It must handle:

- Authentication (email+password, OAuth, session/token management, password reset)
- CRUD for users, vehicles, expenses, fuel entries, timeline entries, photos, reminders, challenges
- Dashboard aggregation (monthly totals, category breakdowns, cost/km, trends)
- Push notifications (APNs + FCM)
- Subscription validation (Apple IAP + Google Play Billing receipt verification)
- Image upload, compression, and storage
- Leaderboards and challenge ranking
- Multi-role architecture (MVP: driver only; Phase 2-3: garage_owner, dealer, fleet_manager)
- GDPR compliance (data export, deletion, consent)
- i18n-aware formatting

**Scale:** 5,000 MAU Year 1 → 20,000 MAU Year 2 → 50,000 MAU Year 3.
**Budget:** Infrastructure < €50-130/month pre-revenue.
**Operator:** Solo developer.

---

## Options Considered

### Framework Decision

#### Option A: ASP.NET Core Web API (C#) — CHOSEN

**Pros:**
- Developer has direct C# backend experience
- Code sharing with Blazor WASM via shared C# class library (DTOs, validation, enums, business rules)
- Keeps total stack at two languages (Dart + C#)
- Entity Framework Core — mature ORM with strongly-typed queries, excellent migrations
- One of the fastest web frameworks in benchmarks (TechEmpower) — trivially handles 50K MAU
- ASP.NET Identity for auth, mature JWT support
- Firebase Admin SDK for .NET (push notifications)
- Rich NuGet ecosystem — libraries exist for every feature this project needs
- Excellent AI-assisted development support

**Cons:**
- No code sharing with Flutter mobile (Dart) — models maintained in both languages
- Slightly higher memory footprint than Node.js (irrelevant at this scale)

#### Option B: Node.js / Express or Fastify (TypeScript)

**Eliminated.** Introduces a third language (TypeScript alongside Dart + C#). No code sharing with Blazor. Developer has no Node.js backend experience. No advantage over ASP.NET Core for this project.

#### Option C: Supabase (BaaS)

**Eliminated.** While Supabase offers fast CRUD and auth out of the box, the project's complex business logic (dashboard aggregation, challenge leaderboards, IAP validation, multi-role access control, benchmark calculations) would require a custom API layer anyway — negating the BaaS advantage. Also breaks C# code sharing with Blazor.

#### Option D: Firebase (BaaS)

**Eliminated.** Firestore's NoSQL data model is fundamentally wrong for this project's highly relational data (users → vehicles → expenses → timeline entries) and aggregation-heavy queries (dashboard totals, category breakdowns, leaderboard rankings).

### Architecture Pattern Decision

#### Option 1: Simple Monolith

**Considered.** Maximum simplicity and fastest to MVP. However, with B2B modules (garage, dealer, fleet) coming in Phase 2-3 and multi-role architecture required from day one, a flat structure would degrade quickly. Acceptable for a pure MVP but doesn't align with the developer's code quality/architecture priority.

#### Option 2: Modular Monolith — CHOSEN

**Pros:**
- Clean module boundaries from day one — enforces separation of concerns
- Still a single deployment unit — no microservices operational overhead
- Natural fit for B2B expansion — each vertical (garage, dealer, fleet) becomes its own module
- Multi-role architecture benefits from module-level data access boundaries
- Easy to relax boundaries early if they feel premature, easy to tighten later
- Aligns directly with developer's top priority: code quality/architecture

**Cons:**
- Slightly more project setup than a simple monolith
- Module boundaries require discipline to maintain

#### Option 3: Microservices

**Eliminated.** Massively over-engineered for a solo developer at 50K MAU. A single ASP.NET Core instance handles millions of requests per day. The operational overhead (multiple deployments, service discovery, inter-service communication, distributed tracing) would consume development time with zero benefit at this scale.

---

## Decision

**ASP.NET Core Web API as a modular monolith, using controllers, deployed on a VPS.**

## Rationale

### Framework: ASP.NET Core

1. **C# consistency.** With Blazor WASM on the web side, ASP.NET Core keeps the backend and web in the same language. Shared class library for DTOs, validation attributes, and enums eliminates an entire category of serialization/translation bugs.

2. **Developer experience.** The developer has direct C# backend experience. No learning curve, immediate productivity.

3. **Two languages, not three.** The full stack is Dart (mobile) + C# (everything else). Adding Node.js or TypeScript would create a third language with models maintained in three places.

4. **Performance headroom.** ASP.NET Core is one of the highest-performance web frameworks. A single VPS instance will handle the project's scale requirements through Year 3 and beyond.

### Architecture: Modular Monolith

1. **Code quality priority.** The developer ranked code quality/architecture as the top priority. A modular monolith enforces clean boundaries from day one.

2. **B2B expansion path.** Phase 2-3 adds garage, dealer, and fleet modules. A modular structure makes this a natural extension — add a new module project, define its interfaces, and integrate. No architectural refactoring needed.

3. **Multi-role architecture.** The flows-index.md requires role-scoped data access, organization entities, and source/created_by tracking. Module boundaries help enforce these access patterns.

4. **Zero operational overhead.** Unlike microservices, a modular monolith deploys as a single unit. One CI/CD pipeline, one VPS, one process to monitor.

### API Style: Controllers

Controllers chosen over Minimal APIs for their structured, conventional approach — better alignment with the code quality/architecture priority and clearer organization in a modular monolith.

### Hosting: VPS

VPS chosen over managed platforms (Azure App Service, Railway, Render) for full control over the environment, predictable pricing, and flexibility to co-locate the database on the same server if needed during early stages.

---

## Module Structure

```
src/
  CarPlatform.API/              -- API host (controllers, middleware, startup config)
  CarPlatform.Shared/           -- Shared library (DTOs, validation, enums) — also referenced by Blazor
  CarPlatform.Core/             -- Domain models, interfaces, shared business logic
  Modules/
    CarPlatform.Auth/           -- Authentication, registration, roles, tokens
    CarPlatform.Vehicles/       -- Vehicle profiles, makes/models
    CarPlatform.Expenses/       -- Expense tracking, fuel entries, categories
    CarPlatform.Dashboard/      -- Aggregation, cost calculations, trends
    CarPlatform.Timeline/       -- Timeline entries, photos
    CarPlatform.Reminders/      -- Maintenance reminders, push notification triggers
    CarPlatform.Challenges/     -- Challenges, leaderboards, badges
    CarPlatform.Subscriptions/  -- Premium subscription validation, feature gating
    CarPlatform.Admin/          -- Admin-specific operations
```

Each module owns its:
- Controllers (API endpoints)
- Services (business logic)
- Data access (EF Core repository/queries for its domain)
- Module-specific models

Cross-module communication goes through defined interfaces in `CarPlatform.Core`, not direct database access across module boundaries.

---

## Cost Implications

| Item | Cost |
|---|---|
| ASP.NET Core / .NET SDK | Free (open source) |
| Entity Framework Core | Free (open source) |
| VPS (entry level — 2 vCPU, 4GB RAM) | ~€6-20/month depending on provider |
| VPS (growth — 4 vCPU, 8GB RAM) | ~€20-40/month |
| SSL certificate | Free (Let's Encrypt) |
| CI/CD | Free (GitHub Actions free tier) |

Well within the €50-130/month budget, even accounting for database and other services on the same VPS.

---

## Review Trigger

Revisit this decision if:
- A specific module grows so large that it needs independent scaling (unlikely before 500K+ MAU)
- VPS management overhead becomes a bottleneck — consider managed platform migration
- A critical feature requires a technology not available in the .NET ecosystem
- Team grows beyond solo developer and module ownership becomes important (positive trigger — may reinforce the modular structure)

---

## References

- PRD Section 6.4 — Technical Requirements: `/03-product/product-requirements-document.md`
- Flows Index — Multi-Role Architecture Note: `/03-product/user-journeys/flows-index.md`
- Project Status — Entity List: `/00-project/project-status.md`
- DEC-007 — Mobile Framework (Flutter/Dart): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-008 — Web Framework (Blazor WASM): `/00-project/decisions/DEC-008-web-framework.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-08 | Initial decision |
