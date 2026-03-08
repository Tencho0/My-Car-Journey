# DEC-010: Database

**File:** `/00-project/decisions/DEC-010-database.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** PostgreSQL — self-hosted on VPS, accessed via EF Core with Npgsql

---

## Context

The platform requires a relational database as the single data store shared by all applications (Flutter mobile app, Blazor WASM web app, ASP.NET Core API). The data model is highly relational:

- 12+ entities: User, AuthToken, PasswordResetToken, LoginAttempt, Vehicle, Expense, FuelEntry, TimelineEntry, TimelinePhoto, Reminder, Challenge, ChallengeParticipant, ChallengeBadge
- Future B2B entities: Organization, WorkOrder, FleetVehicle, etc.
- Aggregation-heavy queries: monthly totals, category breakdowns, cost/km, year-over-year trends, leaderboard rankings
- Multi-role data isolation: driver sees own vehicles, garage owner sees customer vehicles, fleet manager sees fleet vehicles
- GDPR: per-user data export and deletion
- Scale: millions of expense rows by Year 3 (50K MAU, 2+ expenses/week/user)

The ORM is Entity Framework Core (decided in DEC-009). Hosting is VPS (decided in DEC-009).

---

## Options Considered

### Option A: PostgreSQL — CHOSEN

**Pros:**
- Developer has direct PostgreSQL experience
- Fully open source — free on VPS with no licensing limits
- Best-in-class aggregation: window functions, CTEs, partial indexes — ideal for dashboard queries
- Excellent EF Core support via Npgsql provider (mature, well-maintained, supports PostgreSQL-specific features)
- JSONB support for flexible metadata fields without schema changes
- Built-in full-text search (useful for timeline/notes search)
- Rich extension ecosystem (pg_trgm, pg_stat_statements, PostGIS)
- Native Linux VPS fit — no licensing friction
- Large, active open-source community
- Straightforward backup/restore (pg_dump, WAL archiving)

**Cons:**
- Self-hosted means the developer handles backups, updates, and monitoring
- No built-in GUI management (mitigated by pgAdmin, DBeaver, or DataGrip)

### Option B: SQL Server

**Eliminated.** While the developer has MSSQL experience and EF Core support is excellent, SQL Server on a Linux VPS in production requires either Express edition (10GB database limit, 1GB RAM cap) or a paid license (~€900+). PostgreSQL provides equivalent or superior functionality at zero licensing cost. The 10GB Express limit would become a ceiling as expense data grows.

### Option C: MySQL

**Eliminated.** Weaker aggregation capabilities than PostgreSQL (window functions added later, less sophisticated optimizer). The EF Core provider (Pomelo) is less polished than Npgsql. No JSONB equivalent. No advantage over PostgreSQL for this project.

### Option D: SQLite

**Eliminated.** Not suitable as the primary server database for a multi-client API. Single-writer model creates write contention under concurrent load from mobile + web clients. Potential future use as local cache in the Flutter app (offline), but not as the server database.

---

## Decision

**PostgreSQL — self-hosted on the same VPS as the backend API.**

## Rationale

1. **Free with no limits.** PostgreSQL is fully open source. On a VPS, there are no licensing costs, no database size caps, and no feature restrictions. This is the most cost-effective choice.

2. **Best aggregation engine for the core product.** The cost dashboard is the "aha moment" — monthly totals, category breakdowns, cost/km trends, leaderboard rankings. PostgreSQL's window functions, CTEs, and query optimizer handle these queries better than any alternative at this price point.

3. **Developer experience.** The developer has direct PostgreSQL experience. No learning curve.

4. **EF Core + Npgsql maturity.** The Npgsql provider is well-maintained and supports PostgreSQL-specific features (JSONB, arrays, full-text search) through EF Core. Migrations, strongly-typed queries, and LINQ translation all work reliably.

5. **Future migration path.** If operational overhead or scaling needs increase, the database can be migrated to a managed PostgreSQL service (Neon, Supabase, DigitalOcean Managed, AWS RDS) without application changes — same PostgreSQL engine, same Npgsql provider, same EF Core code.

---

## Implementation Intent

- **ORM:** Entity Framework Core with Npgsql provider
- **Schema approach:** Primarily relational and normalized. Core domain data (users, vehicles, expenses, reminders, challenges) in proper relational tables with foreign keys and constraints.
- **Indexing:** Add proper indexes early for common query patterns — dashboard aggregation, timeline retrieval, leaderboard ranking, user-scoped lookups.
- **JSONB:** Used sparingly for flexible metadata fields (vehicle custom attributes, expense extra fields) where schema flexibility is genuinely needed. Not for core domain data.
- **Backups:** Configure regular automated backups (pg_dump on cron or WAL archiving). Verify restore procedure before launch.
- **Monitoring:** Basic query performance monitoring (pg_stat_statements). Alert on connection count, disk usage, and slow queries.

---

## Cost Implications

| Item | Cost |
|---|---|
| PostgreSQL on VPS | Free (open source, runs on existing VPS) |
| Disk storage for database | Included in VPS cost |
| Backup storage | Minimal — compressed pg_dump to VPS disk or cheap object storage (~€1-3/month) |
| pgAdmin / DBeaver (management) | Free |
| **Future managed migration (if needed)** | |
| Neon | Free tier (0.5GB), Pro ~€19/month |
| DigitalOcean Managed | ~€15/month (1GB RAM) |
| Supabase | Free tier (500MB), Pro $25/month |

MVP cost: **€0** (self-hosted on VPS already provisioned in DEC-009).

---

## Review Trigger

Revisit this decision if:
- Self-hosted management overhead becomes a significant time burden
- Database size exceeds VPS disk capacity
- Scaling requires read replicas (unlikely before 500K+ MAU)
- A critical feature requires a capability not available in PostgreSQL (extremely unlikely)
- Team grows and database reliability SLA becomes more important than cost savings

---

## References

- PRD Section 6.4 — Technical Requirements: `/03-product/product-requirements-document.md`
- Project Status — Entity List: `/00-project/project-status.md`
- Flows Index — Multi-Role Architecture Note: `/03-product/user-journeys/flows-index.md`
- DEC-009 — Backend Approach (ASP.NET Core, VPS): `/00-project/decisions/DEC-009-backend-approach.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |
