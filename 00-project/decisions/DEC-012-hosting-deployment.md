# DEC-012: Hosting & Deployment

**File:** `/00-project/decisions/DEC-012-hosting-deployment.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** Self-managed VPS — provider selected and managed by the developer

---

## Context

The platform requires hosting for:

- **ASP.NET Core Web API** — the single backend serving both Flutter mobile and Blazor WASM web clients
- **PostgreSQL database** — co-located on the same VPS (decided in DEC-010)
- **Blazor WASM static files** — served from the same host or a CDN
- **SSL termination** — Let's Encrypt (free)
- **CI/CD** — GitHub Actions (free tier)

DEC-009 already decided on VPS hosting over managed platforms (Azure App Service, Railway, Render) for full control, predictable pricing, and the ability to co-locate the database.

**Scale:** 5,000 MAU Year 1 → 20,000 MAU Year 2 → 50,000 MAU Year 3.
**Budget:** Infrastructure < €50-130/month pre-revenue.
**Region:** Europe (Bulgarian users, GDPR compliance).

---

## Options Considered

### Option A: Hetzner Cloud (Germany/Finland)

**Pros:**
- Best price/performance in Europe — CX22 (2 vCPU, 4GB RAM) at ~€4.35/month
- EU datacenters (Falkenstein, Nuremberg, Helsinki) — low latency to Bulgaria, GDPR native
- Excellent reputation in the European developer community
- Volumes (block storage) available for expansion

**Cons:**
- No managed database addon
- Email-based support only on basic plans

### Option B: DigitalOcean (EU region)

**Pros:**
- Developer-friendly, good documentation
- Amsterdam datacenter (EU)
- Managed PostgreSQL available (~€15/month) if needed later

**Cons:**
- ~5x more expensive than Hetzner for equivalent specs (~€22/month for 2 vCPU / 4GB)
- Only Amsterdam in EU (further from Bulgaria)

### Option C: Azure (EU region)

**Pros:**
- Natural .NET ecosystem fit
- $200 free credits for new accounts
- Managed PostgreSQL Flexible Server available

**Cons:**
- Significantly more expensive — B2s VM at ~€35-40/month
- Complex pricing model
- Overkill for solo developer at this scale

### Option D: Contabo (Germany)

**Pros:**
- Very cheap — VPS S (4 vCPU, 8GB) at ~€6.99/month

**Cons:**
- Inconsistent performance reputation
- Slower support, no cloud API/automation

---

## Decision

**Self-managed VPS — provider selection and management handled by the developer.**

The specific VPS provider is not a project-level architecture decision. The developer will select, provision, and manage the VPS based on personal preference, pricing, and availability. The architecture only requires:

1. A Linux VPS with sufficient resources (minimum 2 vCPU, 4GB RAM to start)
2. EU datacenter for low latency to Bulgaria and GDPR compliance
3. Ability to run ASP.NET Core (.NET runtime) and PostgreSQL
4. SSH access for deployment and administration
5. Vertical scaling path (upgrade to 4+ vCPU, 8+ GB RAM when needed)

## Rationale

1. **VPS already decided.** DEC-009 decided on VPS hosting. The specific provider is an operational choice, not an architectural one.

2. **Co-located API + DB.** Both the ASP.NET Core API and PostgreSQL run on the same VPS during MVP and early growth. This minimizes latency between API and database, simplifies deployment, and keeps costs at a single line item.

3. **Developer-managed.** The developer has indicated comfort with Linux server administration and prefers to handle VPS selection independently. This is consistent with the self-managed philosophy across the stack (self-hosted DB in DEC-010, self-managed auth in DEC-011).

4. **Budget-friendly.** European VPS providers offer 2 vCPU / 4GB RAM starting at €4-7/month, well within the €50-130/month total infrastructure budget.

---

## Deployment Architecture

```
VPS (Linux, EU datacenter)
├── ASP.NET Core API          (Kestrel behind nginx reverse proxy)
├── PostgreSQL                (co-located, same server)
├── Blazor WASM static files  (served via nginx or from API host)
├── nginx                     (reverse proxy, SSL termination via Let's Encrypt)
└── Certbot                   (Let's Encrypt auto-renewal)

CI/CD: GitHub Actions → build → deploy to VPS via SSH/SCP
Mobile: Flutter apps deployed to App Store / Google Play (separate pipeline)
```

---

## Cost Implications

| Item | Cost |
|---|---|
| VPS (2 vCPU, 4GB RAM, EU) | ~€4-20/month depending on provider |
| VPS (growth: 4 vCPU, 8GB RAM) | ~€8-40/month |
| SSL certificate | Free (Let's Encrypt) |
| CI/CD | Free (GitHub Actions free tier) |
| Domain name | ~€10-15/year |

---

## Review Trigger

Revisit this decision if:
- VPS management overhead becomes a significant time burden for the solo developer
- Traffic requires horizontal scaling beyond a single VPS (unlikely before 200K+ MAU)
- A managed platform offers a compelling cost/convenience advantage
- Team grows and deployment automation becomes more important

---

## References

- DEC-009 — Backend Approach (ASP.NET Core, VPS): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-010 — Database (PostgreSQL, self-hosted on VPS): `/00-project/decisions/DEC-010-database.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |