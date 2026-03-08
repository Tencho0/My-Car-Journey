# DEC-007: Mobile Framework

**File:** `/00-project/decisions/DEC-007-mobile-framework.md`
**Produced by:** @product-architect + @developer
**Date:** 2026-03-08
**Status:** Decided
**Decision:** Flutter (Dart) with Riverpod for state management

---

## Context

The car ownership platform requires a cross-platform mobile app (Android + iOS) as the primary consumer interface. The app must support:

- Charts/visualizations (pie, bar, line) rendering smoothly
- Push notifications (APNs + FCM)
- In-app purchases (Apple IAP + Google Play Billing)
- Camera/gallery access for photo attachments
- Home screen widget (quick-add expense)
- i18n for Bulgarian/English/Romanian
- High-quality animations for gamification features (XP, achievements, level-up effects)
- "Car culture, not accounting" design — the app must feel premium and enthusiast-oriented

The developer has prior experience with .NET MAUI (built a digital university app) and strong C# skills. Backend will be ASP.NET Core (C#).

---

## Options Considered

### Option A: .NET MAUI (C#)

**Pros:**
- Developer has direct MAUI experience
- Full C# stack — shared models, validation, DTOs with ASP.NET Core backend
- Single language across all non-mobile projects
- Strong MVVM pattern support

**Cons:**
- Smaller community and ecosystem than Flutter/React Native
- Fewer UI libraries and third-party packages
- Animation capabilities limited compared to Flutter — gamification features (XP, achievements, animated rewards) would be significantly harder
- UI rendering quality lower than Flutter — less pixel-perfect, can feel less polished
- 3D rendering and advanced visual features are very difficult
- Occasional platform-specific bugs across releases
- Hot reload less consistent than Flutter

### Option B: Flutter (Dart) — CHOSEN

**Pros:**
- Best-in-class cross-platform rendering — pixel-perfect consistency across Android/iOS
- Superior animation system — Rive, Lottie, and built-in animation framework make gamification features easy
- Largest cross-platform UI ecosystem — rich libraries for charts (fl_chart), components, and design
- Hot reload is best-in-class — fastest iteration cycle
- Opens door for future 3D rendered car models/avatars
- Excellent AI-assisted development support (well-represented in training data)
- Official Google packages for in-app purchases, push notifications, widgets
- Very high solo developer productivity once learned

**Cons:**
- Developer needs 2-3 weeks to learn Dart (mitigated by strong programming background)
- No code sharing with C# backend — models maintained in both Dart and C#
- Dart is a separate language from the rest of the stack (C# for backend/web)

### Option C: React Native (TypeScript)

**Pros:**
- Developer has React web experience (transferable)
- Massive ecosystem and community
- Expo framework simplifies builds and OTA updates
- Excellent AI-assisted development support

**Cons:**
- No code sharing with C# backend
- Bridge performance for complex animations (though improving with New Architecture)
- JavaScript/TypeScript dependency churn
- Widget implementation less straightforward than Flutter
- Animation system less capable than Flutter for gamification needs

### Option D: Kotlin Multiplatform

**Eliminated early** — no Kotlin experience, smallest ecosystem, least mature for full cross-platform apps, no synergy with C# backend.

---

## Decision

**Flutter (Dart) with Riverpod for state management.**

## Rationale

The developer overrode the initial MAUI recommendation for well-justified reasons:

1. **Gamification requirements:** Planned features (XP, achievements, animated rewards, level-up effects) require a strong animation system. Flutter's animation framework and libraries like Rive/Lottie make this significantly easier than MAUI.

2. **Design quality ceiling:** The "car culture, not accounting" design principle demands a premium-feeling, visually polished app. Flutter's pixel-perfect rendering and large UI ecosystem provide a higher quality ceiling.

3. **Future optionality:** Potential future 3D rendered car model/avatar feature. Flutter keeps this door open; MAUI would make it very difficult.

4. **Long-term investment:** While MAUI would be faster for a pure MVP, Flutter is the better fit for the full product roadmap. Investing 2-3 weeks in Dart now avoids a painful MAUI-to-Flutter migration later.

**Accepted trade-offs:**
- 2-3 weeks Dart learning curve
- Models maintained in both Dart (mobile) and C# (backend/web)
- Two languages in the stack instead of a unified C# monoglot

**State management:** Riverpod chosen for its compile-time safety, testability, and clean architecture patterns — aligning with the developer's priority of code quality/architecture.

---

## Cost Implications

| Item | Cost |
|---|---|
| Flutter SDK | Free (open source) |
| IDE (VS Code / Android Studio) | Free |
| Apple Developer Account | €99/year (required regardless of framework) |
| Google Play Developer Account | $25 one-time (required regardless of framework) |
| Dart/Flutter packages | Free (open source) |

No cost difference between framework options. Decision is skill/quality/productivity driven.

---

## Review Trigger

Revisit this decision if:
- Flutter introduces breaking changes that significantly impact the project
- Dart learning curve proves longer than estimated (>4 weeks)
- A critical mobile feature is blocked by Flutter limitations
- .NET MAUI ecosystem dramatically improves (e.g., MAUI 10+ with strong animation/3D support)

---

## References

- PRD Section 6.4: `/03-product/product-requirements-document.md`
- MVP Feature List Section 9: `/03-product/mvp-feature-list.md`
- Functional Spec — Challenges/Gamification: `/03-product/functional-specs/challenges-gamification.md`

## Document History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-03-08 | Initial decision |
