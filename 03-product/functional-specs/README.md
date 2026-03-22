# Functional Specifications -- Summary

**Produced by:** @product-architect
**Date:** 2026-03-06
**Version:** 1.0
**Command:** `/write-spec all`

---

## Specs Produced

| # | Spec | File | Epic | Status |
|---|------|------|------|--------|
| 1 | Onboarding & Auth | `onboarding-auth.md` | Epic 1 | Done |
| 2 | Vehicle Management | `vehicle-management.md` | Epic 2 | Done |
| 3 | Expense Tracking | `expense-tracking.md` | Epic 3 | Done |
| 4 | Fuel Entry | `fuel-entry.md` | Epic 3 | Done |
| 5 | Cost Dashboard | `cost-dashboard.md` | Epic 4 | Done |
| 6 | Vehicle Timeline | `vehicle-timeline.md` | Epic 5 | Done |
| 7 | Maintenance Reminders | `maintenance-reminders.md` | Epic 6 | Done |
| 8 | Challenges & Gamification | `challenges-gamification.md` | Epic 7 | Done |
| 9 | Share & Export | `share-export.md` | Epic 5 | Done |

---

## Data Entities Discovered

All entities identified across all 9 functional specs. This informs the database schema design.

| Entity | Discovered In | Description |
|--------|--------------|-------------|
| `User` | onboarding-auth | User account: email, password hash, profile, preferences |
| `AuthToken` | onboarding-auth | JWT/session tokens for authentication |
| `PasswordResetToken` | onboarding-auth | Time-limited tokens for password reset flow |
| `LoginAttempt` | onboarding-auth | Failed login tracking for account lockout |
| `Vehicle` | vehicle-management | Car profile: make, model, year, fuel type, photo |
| `OdometerReading` | vehicle-management | Timestamped odometer updates from multiple sources |
| `Expense` | expense-tracking | General car expense: amount, category, date, notes |
| `ExpensePhoto` | expense-tracking | Photos attached to expenses (1-3 per expense) |
| `FuelEntry` | fuel-entry | Specialized fuel fill-up: liters, price, consumption |
| `Reminder` | maintenance-reminders | Date/mileage-based maintenance reminders |
| `ReminderHistory` | maintenance-reminders | Completion history for recurring reminders |
| `Challenge` | challenges-gamification | Monthly challenge definitions |
| `ChallengeParticipant` | challenges-gamification | User's current standing in a challenge |
| `ChallengeResult` | challenges-gamification | Final standings snapshot at month end |

**Initial discovery: 14 core entities.** The authoritative schema is now in `/03-product/technical/database-schema.md` with 22 tables across 9 modules.

### Entity Relationship Summary

```
User (1) --- (*) Vehicle
User (1) --- (*) AuthToken
User (1) --- (*) LoginAttempt
User (1) --- (*) PasswordResetToken

Vehicle (1) --- (*) Expense
Vehicle (1) --- (*) FuelEntry
Vehicle (1) --- (*) OdometerReading
Vehicle (1) --- (*) Reminder

Expense (1) --- (*) ExpensePhoto
Expense (1) --- (0..1) FuelEntry  (fuel entries create a linked expense)

Reminder (1) --- (*) ReminderHistory

Challenge (1) --- (*) ChallengeParticipant
Challenge (1) --- (*) ChallengeResult
User (1) --- (*) ChallengeParticipant
```

---

## API Endpoints Discovered

All endpoints identified across all 9 specs. This informs the API specification design.

### Auth & User (10 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| POST | `/api/auth/register` | onboarding-auth |
| POST | `/api/auth/login` | onboarding-auth |
| POST | `/api/auth/logout` | onboarding-auth |
| POST | `/api/auth/refresh` | onboarding-auth |
| POST | `/api/auth/password-reset/request` | onboarding-auth |
| POST | `/api/auth/password-reset/confirm` | onboarding-auth |
| POST | `/api/auth/oauth/{provider}` | onboarding-auth |
| POST | `/api/auth/verify-email` | onboarding-auth |
| GET | `/api/users/me` | onboarding-auth |
| PUT | `/api/users/me` | onboarding-auth |

### Vehicles (8 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| POST | `/api/vehicles` | vehicle-management |
| GET | `/api/vehicles` | vehicle-management |
| GET | `/api/vehicles/{id}` | vehicle-management |
| PUT | `/api/vehicles/{id}` | vehicle-management |
| DELETE | `/api/vehicles/{id}` | vehicle-management |
| POST | `/api/vehicles/{id}/photo` | vehicle-management |
| POST | `/api/vehicles/{id}/odometer` | vehicle-management |
| PUT | `/api/vehicles/{id}/activate` | vehicle-management |

### Expenses (8 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| POST | `/api/vehicles/{vehicleId}/expenses` | expense-tracking |
| GET | `/api/vehicles/{vehicleId}/expenses` | expense-tracking |
| GET | `/api/vehicles/{vehicleId}/expenses/{id}` | expense-tracking |
| PUT | `/api/vehicles/{vehicleId}/expenses/{id}` | expense-tracking |
| DELETE | `/api/vehicles/{vehicleId}/expenses/{id}` | expense-tracking |
| POST | `/api/vehicles/{vehicleId}/expenses/{id}/photos` | expense-tracking |
| DELETE | `/api/vehicles/{vehicleId}/expenses/{id}/photos/{photoId}` | expense-tracking |
| GET | `/api/vehicles/{vehicleId}/expenses/summary` | expense-tracking |

### Fuel Entries (7 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| POST | `/api/vehicles/{vehicleId}/fuel-entries` | fuel-entry |
| GET | `/api/vehicles/{vehicleId}/fuel-entries` | fuel-entry |
| GET | `/api/vehicles/{vehicleId}/fuel-entries/{id}` | fuel-entry |
| PUT | `/api/vehicles/{vehicleId}/fuel-entries/{id}` | fuel-entry |
| DELETE | `/api/vehicles/{vehicleId}/fuel-entries/{id}` | fuel-entry |
| GET | `/api/vehicles/{vehicleId}/fuel-entries/consumption` | fuel-entry |
| GET | `/api/vehicles/{vehicleId}/fuel-entries/stations` | fuel-entry |

### Dashboard (4 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| GET | `/api/vehicles/{vehicleId}/dashboard` | cost-dashboard |
| GET | `/api/vehicles/{vehicleId}/dashboard/trend` | cost-dashboard |
| GET | `/api/vehicles/{vehicleId}/dashboard/cost-per-km` | cost-dashboard |
| GET | `/api/vehicles/{vehicleId}/dashboard/spending-trends` | cost-dashboard |

### Timeline (2 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| GET | `/api/vehicles/{vehicleId}/timeline` | vehicle-timeline |
| GET | `/api/vehicles/{vehicleId}/timeline/{entryId}` | vehicle-timeline |

### Reminders (8 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| POST | `/api/vehicles/{vehicleId}/reminders` | maintenance-reminders |
| GET | `/api/vehicles/{vehicleId}/reminders` | maintenance-reminders |
| GET | `/api/vehicles/{vehicleId}/reminders/{id}` | maintenance-reminders |
| PUT | `/api/vehicles/{vehicleId}/reminders/{id}` | maintenance-reminders |
| DELETE | `/api/vehicles/{vehicleId}/reminders/{id}` | maintenance-reminders |
| POST | `/api/vehicles/{vehicleId}/reminders/{id}/complete` | maintenance-reminders |
| POST | `/api/vehicles/{vehicleId}/reminders/{id}/snooze` | maintenance-reminders |
| GET | `/api/users/me/reminders/overdue-count` | maintenance-reminders |

### Challenges (4 endpoints)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| GET | `/api/challenges` | challenges-gamification |
| GET | `/api/challenges/{id}/leaderboard` | challenges-gamification |
| GET | `/api/challenges/{id}/my-stats` | challenges-gamification |
| GET | `/api/challenges/history` | challenges-gamification |

### Share (1 endpoint)

| Method | Endpoint | Source Spec |
|--------|----------|-------------|
| GET | `/api/vehicles/{vehicleId}/share-stats` | share-export |

**Initial discovery: 52 API endpoints.** The authoritative endpoint list is now in `/03-product/technical/api-specification.md` with 86 endpoints across 12 controller groups.

---

## Cross-Cutting Open Questions

Collected from all specs. These need resolution before or during development.

| # | Question | From Spec | Impact |
|---|----------|-----------|--------|
| 1 | Static make/model list vs. third-party vehicle database API? | onboarding-auth, vehicle-management | Data quality |
| 2 | Apple Sign-In mandatory if any OAuth on iOS? | onboarding-auth | App Store compliance |
| 3 | Custom expense categories beyond the 10 pre-defined? | expense-tracking | User flexibility |
| 4 | How to handle LPG dual-fuel vehicles? | vehicle-management, fuel-entry | Data model |
| 5 | Electric vehicle fuel entry adaptation (kWh vs liters)? | fuel-entry | Data model |
| 6 | Chart library selection (donut, bar, line with touch)? | cost-dashboard | Tech dependency |
| 7 | Blurred vs hidden premium stats (conversion optimization)? | cost-dashboard | UX/conversion |
| 8 | Is 3 free reminders the right limit? | maintenance-reminders | Conversion vs UX |
| 9 | Vehicle-class-specific leaderboards for fairness? | challenges-gamification | Complexity |
| 10 | Server-side vs client-side card rendering? | share-export | Architecture |

---

## Next Steps

1. **Write user stories** per epic — `/write-stories` for each feature area
2. ~~**Design database schema**~~ ✅ Done — `/03-product/technical/database-schema.md` (22 tables, 9 modules)
3. ~~**Design API specification**~~ ✅ Done — `/03-product/technical/api-specification.md` (86 endpoints, 12 controller groups)
4. **Name the product** — `/name-product` command (inputs ready)
5. **Write test scenarios** — `/write-tests` for each spec
