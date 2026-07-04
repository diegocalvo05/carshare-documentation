# Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Frontend (Vite + React)                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐ │
│  │  Pages   │  │Components│  │  Hooks   │  │Services │ │
│  └────┬─────┘  └──────────┘  └────┬─────┘  └────┬────┘ │
│       │                           │              │       │
│  ┌────┴───────────────────────────┴──────────────┴────┐ │
│  │              UserContext (JWT token + user)         │ │
│  └─────────────────────────┬──────────────────────────┘ │
│                            │ HTTP (Axios / fetch)        │
└────────────────────────────┼────────────────────────────┘
                             │
                    ┌────────┴────────┐
                    │   JWT Bearer    │
                    │  Authorization  │
                    └────────┬────────┘
                             │
┌────────────────────────────┼──────────────────────────────┐
│                    Backend (Spring Boot 3.5)              │
│  ┌─────────────────────────┴──────────────────────────┐  │
│  │              Security Filter Chain                   │  │
│  │  JwtAuthFilter → UsernamePasswordAuthenticationFilter│  │
│  └─────────────────────────┬──────────────────────────┘  │
│                            │                              │
│  ┌─────────────────────────┴──────────────────────────┐  │
│  │              REST Controllers (x10)                  │  │
│  │  /api/auth  /api/cars  /api/reservation             │  │
│  │  /api/user  /api/reviews  /api/driver-license       │  │
│  │  /api/payments  /api/recommendations                │  │
│  │  /api/vehicles  /api/python                         │  │
│  └─────────────────────────┬──────────────────────────┘  │
│                            │                              │
│  ┌─────────────────────────┴──────────────────────────┐  │
│  │              Service Layer (interfaces + impls)      │  │
│  │  AuthService  CarService  ReservationService        │  │
│  │  UserService  ReviewService  PaymentService         │  │
│  │  RecommendationService  DriverLicenseService        │  │
│  │  PythonRunnerService                                │  │
│  └─────────────────────────┬──────────────────────────┘  │
│                            │                              │
│  ┌─────────────────────────┴──────────────────────────┐  │
│  │           Repository Layer (Spring Data JPA)        │  │
│  └─────────────────────────┬──────────────────────────┘  │
│                            │                              │
│  ┌─────────────────────────┴──────────────────────────┐  │
│  │              PostgreSQL 16                          │  │
│  │  12 tables: user, car, reservation, review, ...     │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                            │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         Python Subprocess (OCR Pipeline)             │  │
│  │  read_license.py (PaddleOCR) → parser.py            │  │
│  │           ↓                                          │  │
│  │  DriverLicenseScoringService + ValidationService    │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                            │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         Schedulers (cron: 0 0 * * *)                 │  │
│  │  ReservationScheduler — expire ACTIVE reservations   │  │
│  │  DriverLicenseScheduler — expire licenses            │  │
│  └─────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

## Design Decisions

### Stateless Authentication
JWT tokens are used instead of sessions. The backend does not maintain session state, making horizontal scaling straightforward.

### Three-Tier Role Model
- **ROLE_USER** — standard user: browse, reserve, manage own cars
- **ROLE_ADMIN** — administrator: view all data, simulate payments, moderate content
- **ROLE_SYSADMIN** — super admin: grant/revoke admin roles

### OCR Pipeline
Driver license verification uses Python subprocesses:
1. Image URL → `read_license.py` (PaddleOCR) → raw text + confidence
2. Raw OCR → `parser.py` → structured fields (name, license#, category, expiration)
3. Structured result → ScoringService + ValidationService → `VerificationStatus`

The Docker image pre-bakes the PaddleOCR model at build time, so no download happens at container start.

### Payment Simulation
Payments follow a simulated lifecycle: `PENDING → PROCESSING → COMPLETED / FAILED / REFUNDED`. Admins can trigger `simulateSuccess` or `simulateFailure` for testing.

### Recommendation Engine
Two strategies:
- **Popular**: weighted score = reservation_count × 2 + avg_rating
- **Personalized**: 90-day window preference boost by brand, model, transmission, location. Falls back to popular for cold-start users.
