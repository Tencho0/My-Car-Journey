# DEC-011: Authentication

**File:** `/00-project/decisions/DEC-011-authentication.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** ASP.NET Identity + JWT (self-managed, no external auth provider)

---

## Context

The platform requires authentication that works across both clients (Flutter mobile app, Blazor WASM web app) through the same ASP.NET Core API. Requirements:

- Email + password registration and login (MVP)
- OAuth providers: Google first, then Apple Sign-In (post-MVP, added iteratively)
- Password reset flow
- Session persistence (users stay logged in on mobile)
- Multi-role from day one: `driver` (MVP), `garage_owner`, `dealer`, `fleet_manager` (Phase 2-3)
- JWT tokens — standard for API-based auth serving multiple clients
- GDPR: account deletion, data export
- Subscription status and premium feature gating tied to user identity
- MFA: not in MVP, potential future addition for B2B users

Auth must be API-centric — both Flutter and Blazor call the same ASP.NET Core API endpoints. Neither client authenticates directly with a third-party service.

---

## Options Considered

### Option A: ASP.NET Identity + JWT — CHOSEN

**Pros:**
- Single source of truth — all user data in PostgreSQL alongside domain data. No split-identity problem.
- Multi-role built-in — `UserManager.AddToRoleAsync()`, `[Authorize(Roles = "driver")]`. First-class role/claims system.
- Full control over JWT token issuance — access token expiration, refresh token rotation, claims structure, token revocation. Subscription status and role embedded in claims for feature gating.
- EF Core integration is first-class — Identity tables created via migrations alongside domain tables. Same database, same transaction scope.
- Zero cost, zero vendor lock-in — no external service dependency, no pricing tiers.
- C# consistency — auth logic is ASP.NET Core code, tested and debugged with existing tools.
- GDPR: one database transaction handles user deletion across auth and domain data.
- OAuth providers supported via `Microsoft.AspNetCore.Authentication.Google` and `.Apple` packages when needed.
- Excellent AI-assisted development support — ASP.NET Identity + JWT is extremely well-documented.

**Cons:**
- More initial setup than managed auth (JWT issuance, refresh token logic, password reset flow). Developer is comfortable with this.
- OAuth provider configuration is more work than Firebase's one-click setup. Mitigated by deferring OAuth to post-MVP.

### Option B: Firebase Auth

**Eliminated.** Creates a split-identity system: Firebase manages auth, PostgreSQL manages domain data. Every API call requires mapping Firebase UID to a local user record. Multi-role support is limited to custom claims (1000 bytes max). Blazor WASM support requires JS interop — awkward, not first-class. GDPR deletion requires coordinating two systems. The convenience of managed auth does not outweigh the architectural complexity of maintaining two identity sources.

### Option C: Auth0

**Eliminated.** Same split-identity problem as Firebase. Cost escalates at scale — $35-240/month at 25K+ MAU just for auth. High vendor lock-in. No advantage over ASP.NET Identity for a project that already uses ASP.NET Core with PostgreSQL.

### Option D: Supabase Auth

**Eliminated.** Designed to work with Supabase's own database stack. Awkward fit with self-hosted PostgreSQL + EF Core. Same split-identity problem.

### Option E: Clerk

**Eliminated.** Poor Flutter support (community-maintained, not official). Minimal Blazor support. Designed for the React ecosystem.

---

## Decision

**ASP.NET Identity + JWT — self-managed within the ASP.NET Core API.**

## Rationale

1. **Single source of truth.** User data lives in PostgreSQL alongside all domain data. No split-identity mapping, no two systems to synchronize. User roles, subscription status, premium feature flags, and domain relationships are all in the same database, queryable in the same transactions.

2. **Multi-role is first-class.** The platform requires `driver`, `garage_owner`, `dealer`, `fleet_manager` roles from day one (even if only `driver` is active in MVP). ASP.NET Identity's role and claims system handles this natively. Firebase and Auth0 would require bolting role management on top of an external identity system.

3. **Full token control.** The developer controls access token expiration, refresh token rotation, claims content (role, subscription tier, vehicle count), and revocation logic. When feature gating decisions depend on JWT claims, owning the token pipeline eliminates external dependencies.

4. **Zero cost, zero lock-in.** No external service to depend on at any scale. No pricing tiers to worry about as MAU grows. No migration headache if a provider changes terms or pricing.

5. **GDPR simplicity.** One `DELETE` cascade in PostgreSQL handles both auth data (Identity tables) and domain data (vehicles, expenses, etc.). No coordinated multi-system deletion.

6. **Developer comfort.** The developer is comfortable implementing JWT token issuance and refresh logic. The setup effort is bounded and well-documented.

---

## Implementation Plan

### MVP (Phase 1)

- **Email + password** registration and login
- **JWT access tokens** (short-lived, ~15-30 minutes)
- **Refresh tokens** (long-lived, stored in database, rotated on use)
- **Password reset** via email link
- **Role assignment:** All new users get `driver` role on registration
- **Claims:** Role, user ID, subscription status embedded in JWT
- **Session persistence:** Flutter stores refresh token securely (flutter_secure_storage). Blazor stores in browser (localStorage or cookie).
- **Account deletion:** GDPR-compliant endpoint to delete user and all associated data

### Post-MVP (iterative)

- **Google Sign-In** — first OAuth provider. Add via `Microsoft.AspNetCore.Authentication.Google`. Link to existing account if email matches.
- **Apple Sign-In** — add if/when social login is introduced on iOS (Apple requires it if any social login is offered).
- **Multi-role activation** — `garage_owner`, `dealer`, `fleet_manager` roles activated when B2B modules are built (Phase 2-3). Role assignment via admin panel or B2B onboarding flow.
- **MFA** — not in MVP. Consider for B2B users in Phase 2-3 where security expectations are higher. ASP.NET Identity has built-in TOTP MFA support when needed.

### What We're NOT Building

- No Facebook OAuth (low priority, can add later if demand exists)
- No phone/SMS authentication (complexity, cost of SMS providers)
- No magic link authentication (email+password is sufficient)
- No anonymous/guest accounts (users must register to track data)

---

## Architecture Notes

```
Flutter App  ─── POST /api/auth/login ──────→  ASP.NET Core API
Blazor WASM  ─── POST /api/auth/login ──────→  (same endpoints)

API validates credentials via ASP.NET Identity
API issues JWT access token + refresh token
API returns tokens to client

Subsequent requests:
Client  ─── Authorization: Bearer {access_token} ──→  API
API validates JWT, extracts claims (role, sub status)
API enforces [Authorize(Roles = "driver")] on endpoints

Token refresh:
Client  ─── POST /api/auth/refresh ──→  API
API validates refresh token, rotates it, issues new access token
```

- Both clients use the same `/api/auth/*` endpoints — no client-specific auth paths
- Identity tables (AspNetUsers, AspNetRoles, AspNetUserRoles, etc.) live in the same PostgreSQL database as domain tables
- The `CarPlatform.Auth` module (from DEC-009 modular monolith) owns all auth controllers, services, and Identity configuration

---

## Cost Implications

| Item | Cost |
|---|---|
| ASP.NET Identity | Free (part of ASP.NET Core) |
| JWT libraries | Free (Microsoft.AspNetCore.Authentication.JwtBearer) |
| OAuth packages | Free (Microsoft.AspNetCore.Authentication.Google, .Apple) |
| Email service for password reset | Free tier: SendGrid (100/day), Mailgun (1000/month), or SMTP on VPS |
| **Total** | **€0** |

---

## Review Trigger

Revisit this decision if:
- OAuth provider setup becomes a blocker (unlikely — well-documented)
- B2B customers require SSO/SAML integration (Phase 3+ — may need Auth0 or similar for enterprise SSO specifically)
- MFA implementation effort is higher than expected when B2B requires it
- A security audit identifies weaknesses in self-managed JWT implementation

---

## References

- PRD Section 6.4 — Authentication requirement: `/03-product/product-requirements-document.md`
- PRD Feature M1 — User authentication: `/03-product/product-requirements-document.md`
- Functional Spec — Onboarding & Auth: `/03-product/functional-specs/onboarding-auth.md`
- Flows Index — Multi-Role Architecture Note: `/03-product/user-journeys/flows-index.md`
- DEC-009 — Backend Approach (ASP.NET Core, modular monolith): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-010 — Database (PostgreSQL): `/00-project/decisions/DEC-010-database.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |
