# DEC-017: CI/CD Pipeline

**File:** `/00-project/decisions/DEC-017-ci-cd.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-22
**Status:** Decided
**Decision:** GitHub Actions as the single CI/CD system for all projects, with Fastlane for mobile signing and store deployment

---

## Context

The platform is a monorepo on GitHub containing four projects that all need CI/CD:

1. **Flutter mobile app** — build Android APK/AAB + iOS IPA, sign, deploy to Google Play and App Store
2. **ASP.NET Core Web API** — build, test, deploy to VPS via SSH
3. **Blazor WASM web app** — build, deploy static files to VPS (served by nginx)
4. **Shared C# class library** — build and test (consumed by API and Blazor)

**Requirements:**
- One CI/CD system for all projects — not separate tools per project
- Automated builds on push/PR for all projects
- Automated deployment to VPS for API and web
- Automated store deployment for mobile (or at least build artifacts ready for manual upload)
- Free or very low cost for a solo developer
- Must handle monorepo (trigger only affected project pipelines on changes)

**Source control:** GitHub (confirmed by developer).

---

## Options Considered

### Option A: GitHub Actions + Fastlane — CHOSEN

**Pros:**
- **Native GitHub integration** — no external CI service to configure or authenticate
- **Free tier: 2,000 minutes/month** on Linux runners (Ubuntu) — sufficient for API + Blazor builds and deployments
- **macOS runners available** — required for iOS builds (10x minute multiplier: 200 effective minutes/month on free tier)
- **Massive marketplace** — pre-built actions for Flutter, .NET, SSH deployment, caching, etc.
- **Monorepo path filtering** — `on.push.paths` triggers only the relevant workflow when specific directories change
- **Fastlane integration** — Fastlane handles iOS/Android code signing, build numbering, and store upload inside a GitHub Actions workflow
- **Single system** — one place for all CI/CD across all four projects
- **Secrets management** — built-in encrypted secrets for API keys, signing certificates, SSH keys

**Cons:**
- macOS runners consume free minutes at 10x rate — iOS builds must be efficient
- Self-hosted runners needed if free minutes are exhausted (can run on the VPS for Linux builds)
- YAML-based configuration can be verbose

### Option B: Azure DevOps

**Pros:**
- Good .NET integration, free 1,800 minutes/month
- Parallel jobs, release pipelines with approvals

**Cons:**
- Separate system from GitHub — requires syncing or mirroring
- Less Flutter ecosystem support (fewer pre-built tasks)
- Overkill for a solo developer monorepo
- Two dashboards to monitor (GitHub for code, Azure DevOps for CI/CD)

### Option C: Bitrise

**Pros:**
- Mobile-specialized CI — excellent Flutter and iOS support
- Visual workflow builder

**Cons:**
- Free tier: 300 build minutes/month — limited
- Mobile-only focus — does not handle .NET API or Blazor deployment
- Would require a second CI system for backend — violates "one system" requirement
- Paid plans start at $89/month

### Option D: Codemagic

**Pros:**
- Flutter-specialized CI — fastest Flutter builds
- Free tier: 500 build minutes/month on macOS
- Automatic code signing and store deployment

**Cons:**
- Flutter/mobile only — no .NET support
- Would require a second CI system for backend
- Violates "one system" requirement

### Option E: Fastlane (standalone)

**Not a CI system** — Fastlane is a build automation tool for mobile. It handles code signing, versioning, screenshots, and store upload. It runs inside a CI system (like GitHub Actions), not as a replacement for one.

**Decision:** Use Fastlane inside GitHub Actions workflows for mobile-specific build automation.

---

## Decision

**GitHub Actions as the single CI/CD system for all projects, with Fastlane for mobile code signing and store deployment automation.**

## Rationale

1. **One system, all projects.** GitHub Actions handles Flutter builds, .NET builds, and VPS deployment from a single platform. No context-switching between CI tools.

2. **Native to the source.** The repo is on GitHub. CI/CD configuration lives alongside the code in `.github/workflows/`. PR checks, branch protection, and deployment status are all integrated.

3. **Free tier is sufficient.** 2,000 Linux minutes/month handles all API and Blazor builds easily. iOS builds on macOS runners use more minutes but can be optimized (build only on release tags, not every PR).

4. **Monorepo support.** Path-based triggers ensure that a change in `/mobile/` only triggers the Flutter workflow, a change in `/api/` only triggers the API workflow, etc. No wasted build minutes.

5. **Fastlane for mobile complexity.** iOS code signing, provisioning profiles, and store upload are genuinely complex. Fastlane encapsulates this complexity into declarative `Fastfile` configuration, running inside a GitHub Actions macOS job.

---

## Workflow Design

### Monorepo Workflow Triggers

| Workflow | Trigger Path | Runner | Actions |
|---|---|---|---|
| `api-ci.yml` | `/api/**`, `/shared/**` | `ubuntu-latest` | Build .NET, run tests, deploy to VPS on `main` push |
| `web-ci.yml` | `/web/**`, `/shared/**` | `ubuntu-latest` | Build Blazor WASM, deploy static files to VPS on `main` push |
| `mobile-ci.yml` | `/mobile/**` | `ubuntu-latest` | Build Flutter (Android), run tests on PR |
| `mobile-release.yml` | Git tag `v*` | `macos-latest` | Build Android + iOS, sign with Fastlane, upload to stores |
| `shared-ci.yml` | `/shared/**` | `ubuntu-latest` | Build and test shared library (also triggered by api/web workflows) |

### Deployment Pipeline (API + Web)

```
Push to main (api/ or web/ changed)
  → GitHub Actions (ubuntu-latest)
    → Restore NuGet packages (cached)
    → Build solution
    → Run unit + integration tests
    → Publish release build
    → SSH into VPS
    → Upload artifacts
    → Restart service (systemctl restart carplatform-api)
    → Verify health check endpoint responds 200
```

### Mobile Release Pipeline

```
Push tag v1.0.0
  → GitHub Actions (macos-latest)
    → Checkout + install Flutter (cached)
    → Run flutter test
    → Fastlane: build Android AAB (signed with upload key)
    → Fastlane: build iOS IPA (signed with distribution certificate)
    → Fastlane: upload AAB to Google Play (internal track)
    → Fastlane: upload IPA to TestFlight
    → Create GitHub Release with build artifacts
```

### Caching Strategy

| Cache | Key | Saves |
|---|---|---|
| NuGet packages | `nuget-${{ hashFiles('**/*.csproj') }}` | ~30-60 seconds per build |
| Flutter SDK | `flutter-${{ env.FLUTTER_VERSION }}` | ~2-3 minutes per build |
| Pub packages | `pub-${{ hashFiles('**/pubspec.lock') }}` | ~30 seconds per build |
| Fastlane dependencies | `fastlane-${{ hashFiles('**/Gemfile.lock') }}` | ~20 seconds per build |

### Estimated Monthly Usage

| Workflow | Frequency | Minutes/Run | Runner | Effective Minutes |
|---|---|---|---|---|
| API CI (PR) | ~30/month | ~3 min | Linux (1x) | ~90 |
| API Deploy (main) | ~15/month | ~5 min | Linux (1x) | ~75 |
| Web CI + Deploy | ~10/month | ~4 min | Linux (1x) | ~40 |
| Mobile CI (PR) | ~20/month | ~8 min | Linux (1x) | ~160 |
| Mobile Release | ~4/month | ~20 min | macOS (10x) | ~800 |
| **Total** | | | | **~1,165 / 2,000** |

Comfortably within the free tier.

---

## Cost Implications

| Item | Cost |
|---|---|
| GitHub Actions (free tier, 2,000 min/month) | Free |
| Fastlane | Free (open source) |
| Self-hosted runner on VPS (if needed) | Free (runs on existing VPS) |
| GitHub Actions (if exceeding free tier) | $0.008/min Linux, $0.08/min macOS |
| **Total MVP** | **€0** |

---

## Review Trigger

Revisit this decision if:
- Free tier minutes are consistently exhausted — consider self-hosted Linux runner on VPS
- iOS build frequency increases significantly — consider Codemagic for mobile-only builds alongside GitHub Actions for backend
- Team grows and parallel build queues become important
- Build times become a bottleneck for development velocity

---

## References

- DEC-007 — Mobile Framework (Flutter): `/00-project/decisions/DEC-007-mobile-framework.md`
- DEC-008 — Web Framework (Blazor WASM): `/00-project/decisions/DEC-008-web-framework.md`
- DEC-009 — Backend Approach (ASP.NET Core): `/00-project/decisions/DEC-009-backend-approach.md`
- DEC-012 — Hosting & Deployment (VPS): `/00-project/decisions/DEC-012-hosting-deployment.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-22 | Initial decision |