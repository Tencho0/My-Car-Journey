# DEC-008: Web Framework

**File:** `/00-project/decisions/DEC-008-web-framework.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-08
**Status:** Decided
**Decision:** Blazor WebAssembly with MudBlazor component library

---

## Context

The platform requires a web application that serves multiple purposes across phases:

- **MVP (Phase 1):** Admin dashboard for the founder — user management, analytics, content moderation, challenge management. Not public-facing.
- **Phase 2-3:** B2B interfaces for garage owners, dealers, and fleet managers. Complex forms, data grids, role-based views, work order management.
- **Phase 2+ (optional):** Secondary web interface for drivers who prefer web over mobile. Not the primary driver experience.

Marketing/landing pages are a separate static site (HTML/Tailwind, Framer, or Webflow) — NOT part of the web application. This eliminates SEO concerns entirely.

The developer has experience with Blazor (both Server and WASM), React, and Angular. Backend is ASP.NET Core (C#). Mobile is Flutter (Dart).

---

## Options Considered

### Option A: Blazor WebAssembly (C#) — CHOSEN

**Pros:**
- Developer has strong Blazor experience and enjoys working with it
- C# code sharing with ASP.NET Core backend — shared DTOs, validation, enums, business rules
- Keeps the stack at two languages (Dart + C#) instead of three
- MudBlazor provides enterprise-grade admin components out of the box (data grids, charts, forms, dialogs)
- Natural fit for admin/B2B interfaces — Blazor's sweet spot
- Potential future path to desktop via .NET MAUI Blazor Hybrid or Electron (same C# codebase)
- WASM runs entirely in the browser — no server-side WebSocket dependency

**Cons:**
- Larger initial download than JS frameworks (~2-5 MB WASM payload)
- Smaller community than React/Angular ecosystems
- Less AI training data for Blazor-specific Razor syntax
- Consumer-facing web experience (if ever prioritized) would be less polished than Next.js

### Option B: Next.js (React/TypeScript)

**Pros:**
- Massive ecosystem, best AI-assisted development support
- Excellent for consumer-facing web with SSR/SSG
- Developer has React experience

**Cons:**
- Introduces a third language (TypeScript) to the stack alongside Dart and C#
- No code sharing with C# backend — models duplicated in yet another language
- Requires more assembly for admin UI compared to MudBlazor's out-of-box components
- Consumer-facing web is not the priority (drivers use mobile, B2B uses admin interfaces)

### Option C: Angular (TypeScript)

**Pros:**
- Developer has Angular experience
- Industry standard for complex enterprise web apps
- Strong component libraries (PrimeNG, Angular Material)

**Cons:**
- Introduces a third language (TypeScript)
- More boilerplate and ceremony than Blazor for the same admin UI
- No code sharing with C# backend
- Heavier framework for what starts as a simple admin dashboard

### Option D: Vue.js / Nuxt

**Eliminated early** — developer has no Vue experience, no differentiating advantage over known frameworks.

---

## Decision

**Blazor WebAssembly with MudBlazor as the primary component library.**

## Rationale

1. **Admin/B2B is the primary use case.** The web app is an admin dashboard (MVP) evolving into B2B interfaces (Phase 2-3). This is Blazor's sweet spot — data tables, forms, CRUD, role-based views. MudBlazor provides these out of the box with minimal code.

2. **Two languages, not three.** With Flutter on mobile (Dart) and ASP.NET Core on backend (C#), choosing Blazor keeps the total at two languages. A JS framework would add TypeScript as a third, with models maintained in three separate languages.

3. **Code sharing with backend.** Shared C# class library for DTOs, validation attributes, enums, and business rules between the API and web dashboard. This is a significant productivity multiplier for a solo developer and reduces the surface area for bugs.

4. **Developer experience and preference.** The developer has strong Blazor experience (both Server and WASM), likes the framework, and reports no major pain points.

5. **Desktop future path.** If the B2B desktop app becomes a reality, Blazor can target desktop via .NET MAUI Blazor Hybrid — reusing the same C# component library. This keeps the option open without additional technology investment.

**Specific choice — WASM over Server or Auto:**
- WASM runs entirely in the browser — no persistent server connection needed
- Better for admin users who may have varying connection quality
- No server-side scaling concerns for WebSocket connections
- Initial download size (~2-5 MB) is acceptable for admin/B2B users (not casual visitors)

**Marketing site separation:** The landing page and marketing content will be a separate static site, built with HTML/Tailwind, Framer, Webflow, or similar. This is not a technology decision that needs to be made now. Separating it from the Blazor app eliminates Blazor's SEO weakness entirely.

---

## Architecture Notes

- **Blazor WASM** hosted model — the API serves both the Flutter app and the Blazor WASM client
- **MudBlazor** for UI components (data grids, charts, forms, navigation, theming)
- **Shared C# class library** between API and Blazor for models, validation, enums
- The optional driver web experience (Phase 2+) is acceptable in Blazor WASM since it's a secondary interface — drivers primarily use the Flutter mobile app
- Desktop app (future, unconfirmed) could reuse Blazor components via MAUI Blazor Hybrid

---

## Cost Implications

| Item | Cost |
|---|---|
| Blazor / .NET | Free (open source) |
| MudBlazor | Free (MIT license) |
| Hosting (WASM — static files) | Minimal — can be served from CDN or same host as API |
| Marketing site (separate) | Free-to-low (static hosting, Cloudflare Pages, Vercel, Netlify) |

---

## Review Trigger

Revisit this decision if:
- Consumer-facing web becomes equally important as mobile for drivers (currently: not planned)
- B2B users report unacceptable performance with Blazor WASM
- Blazor WASM download size becomes a blocker for B2B adoption
- A critical B2B feature is blocked by MudBlazor/Blazor limitations

---

## References

- PRD Section 6.4: `/03-product/product-requirements-document.md`
- User Journeys — Future B2B Journeys (8-12): `/03-product/user-journeys/flows-index.md`
- Monetization Plan — B2B Pricing: `/02-strategy/monetization-plan.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-08 | Initial decision |
