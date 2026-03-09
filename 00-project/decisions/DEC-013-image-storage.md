# DEC-013: Image Storage

**File:** `/00-project/decisions/DEC-013-image-storage.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-09
**Status:** Decided
**Decision:** Local VPS disk (MVP) → Cloudflare R2 (future migration), behind an `IPhotoStorageService` abstraction

---

## Context

The platform requires image storage for vehicle timeline photos. Users attach photos to timeline entries (maintenance events, modifications, milestones) to build a visual history of their vehicle.

**Requirements from functional specs:**
- Vehicle timeline photos (`/03-product/functional-specs/vehicle-timeline.md`)
- Max 1MB per photo (compressed on upload)
- Estimated ~100GB storage at 5K users
- Accessible from both Flutter mobile app and Blazor WASM web app
- Photos uploaded via ASP.NET Core API (not direct client-to-storage)
- GDPR: must support per-user deletion of all associated photos
- PostgreSQL stores metadata and file references only — not binary image data

**Architecture constraints:**
- ASP.NET Core backend handles upload/download endpoints (DEC-009)
- PostgreSQL stores image metadata (DEC-010)
- Co-located API + DB on same VPS (DEC-012) — local disk is available

---

## Options Considered

### Option A: Local VPS Disk → Object Storage Later — CHOSEN

**Pros:**
- Zero additional cost — photos stored on VPS filesystem
- Simplest implementation — standard file I/O behind API endpoints
- No external SDK, no credentials, no network latency for storage operations
- No external service dependency for MVP
- Easy migration path: move files to object storage and update the storage service implementation

**Cons:**
- VPS disk is finite — must monitor and expand or migrate before capacity limits
- No CDN — every photo request hits the VPS directly (acceptable at 5K MAU)
- No built-in redundancy beyond VPS backup strategy
- Must eventually migrate to object storage as photo volume grows

### Option B: Cloudflare R2

**Pros:**
- S3-compatible API — works with AWS SDK for .NET
- Zero egress fees — ideal for serving photos to mobile + web clients
- 10GB free storage + 10M reads/month free tier
- Integrated CDN via Cloudflare's network
- EU data residency option (GDPR)
- Pricing: ~$0.015/GB/month (~€1.50/month for 100GB)

**Cons:**
- Requires Cloudflare account setup and configuration
- External dependency from day one (unnecessary for MVP scale)
- Slightly more complex deployment and credential management

### Option C: AWS S3

**Pros:**
- Industry standard, most documented object storage
- Excellent .NET SDK (AWSSDK.S3)
- eu-central-1 (Frankfurt) for GDPR

**Cons:**
- Egress fees: $0.09/GB — adds up when serving photos to users
- More complex IAM setup than needed
- Overkill and more expensive than R2 for this use case

### Option D: Azure Blob Storage

**Pros:**
- Natural .NET integration (Azure.Storage.Blobs SDK)
- Hot/cool/archive tiers

**Cons:**
- Egress fees similar to S3
- Azure pricing complexity
- No advantage over R2 unless already on Azure

### Option E: Supabase Storage

**Eliminated.** Designed for the Supabase ecosystem. Awkward fit with self-hosted PostgreSQL. Vendor-specific API, not S3-compatible.

---

## Decision

**Local VPS disk for MVP, with Cloudflare R2 as the planned future migration target.**

All image storage access is abstracted behind an `IPhotoStorageService` interface so that swapping from local disk to R2 (or any S3-compatible provider) requires only a new implementation class — no changes to API contracts, controllers, or business logic.

## Rationale

1. **MVP simplicity.** Local disk storage has zero setup, zero cost, zero external dependencies. For a solo developer launching an MVP, this eliminates an entire category of infrastructure configuration.

2. **Sufficient for early scale.** At 5K MAU with typical photo upload rates, local disk on a modern VPS (50-200GB+ available) is more than adequate. CDN is unnecessary when the user base is concentrated in Bulgaria.

3. **Clean migration path.** The `IPhotoStorageService` abstraction means the rest of the system (controllers, services, domain logic) never knows where photos are physically stored. Migration to R2 is a configuration change + data migration, not a code refactoring.

4. **Cloudflare R2 as future target.** When photo volume approaches disk limits or CDN becomes important for performance, R2 offers the best cost profile: S3-compatible API (easy integration), zero egress fees (critical for serving photos), and EU data residency for GDPR.

---

## Implementation Design

### Abstraction Layer

```csharp
public interface IPhotoStorageService
{
    Task<string> UploadAsync(Stream imageStream, string userId, string timelineEntryId, string fileExtension);
    Task<Stream> DownloadAsync(string photoReference);
    Task DeleteAsync(string photoReference);
    Task DeleteAllForUserAsync(string userId);  // GDPR per-user deletion
}
```

### Planned Implementations

| Implementation | When | Storage Location |
|---|---|---|
| `LocalDiskPhotoStorageService` | MVP | VPS filesystem: `/data/photos/{userId}/{timelineEntryId}/{uniqueId}.{ext}` |
| `R2PhotoStorageService` | Post-MVP / when needed | Cloudflare R2 bucket (S3-compatible API) |
| (Future S3-compatible) | If needed | Any S3-compatible provider |

### Design Requirements

- **Unique file naming:** Each photo gets a unique identifier (GUID or similar) to prevent collisions
- **Per-user directory structure:** Photos organized by userId for efficient GDPR deletion
- **Max upload size validation:** Enforced at the API level (1MB limit) before reaching storage service
- **Image compression:** Applied during the upload flow (before storage) to ensure consistent file sizes
- **Metadata in PostgreSQL:** File reference (path or key), userId, timelineEntryId, upload timestamp, file size, MIME type stored in the `TimelinePhoto` table
- **Serving photos:** API endpoint returns the photo stream — the client never accesses storage directly. This keeps the storage implementation fully encapsulated.

### Migration Strategy (Local → R2)

1. Implement `R2PhotoStorageService` with the same interface
2. Migrate existing files from VPS disk to R2 bucket (script or background job)
3. Update PostgreSQL references if the photo reference format changes
4. Switch DI registration from `LocalDiskPhotoStorageService` to `R2PhotoStorageService`
5. API endpoints and clients remain unchanged

---

## Cost Implications

| Item | Cost |
|---|---|
| **MVP: Local disk** | €0 (included in VPS storage) |
| **Future: Cloudflare R2 (100GB)** | ~€1.50/month storage + €0 egress |
| **Future: Cloudflare R2 (500GB)** | ~€7.50/month storage + €0 egress |
| AWS S3 equivalent (100GB + egress) | ~€10-20/month (rejected — more expensive) |

---

## Review Trigger

Revisit this decision if:
- VPS disk usage exceeds 70% capacity — trigger migration to R2
- Photo serving latency becomes noticeable for users outside Bulgaria — consider CDN / R2
- A feature requires image transformations (resize, thumbnails) at scale — consider Cloudflare Images or Imgproxy
- Photo upload volume significantly exceeds projections

---

## References

- Functional Spec — Vehicle Timeline: `/03-product/functional-specs/vehicle-timeline.md`
- DEC-009 — Backend Approach (ASP.NET Core, VPS): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-010 — Database (PostgreSQL): `/00-project/decisions/DEC-010-database.md`
- DEC-012 — Hosting & Deployment (self-managed VPS): `/00-project/decisions/DEC-012-hosting-deployment.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-09 | Initial decision |