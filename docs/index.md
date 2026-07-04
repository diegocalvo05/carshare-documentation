# CarShareSV

CarShareSV is a **car-sharing platform** built as a monorepo with a Spring Boot backend and a React frontend. It connects car owners with renters, providing vehicle browsing, reservations, payment processing, and driver license verification via OCR.

## Tech Stack

| Layer | Technology |
|---|---|
| **Backend** | Java 21, Spring Boot 3.5, PostgreSQL 16, JPA/Hibernate |
| **Frontend** | React 19, Vite 6, React Router 7, Tailwind CSS 4 |
| **Auth** | JWT (jjwt 0.12.6), BCrypt, Spring Security |
| **OCR** | Python 3.12 + PaddleOCR for driver license processing |
| **Maps** | Leaflet + React-Leaflet (OpenStreetMap + Nominatim) |
| **Payments** | Simulated payment gateway with multi-step flow |
| **Container** | Docker multi-stage (JDK 21 + Python 3.12), docker-compose |

## Repository Structure

```
carshare-all/
├── backend/
│   └── CarShareSV_webservice/     # Spring Boot app
│       ├── src/main/java/org/carshare/carsharesv_webservice/
│       │   ├── configuration/     # Security, ModelMapper, app config
│       │   ├── controller/        # 10 REST controllers
│       │   ├── domain/
│       │   │   ├── dto/           # Request/Response DTOs
│       │   │   └── entity/        # JPA entities (12 tables)
│       │   ├── exception/         # Custom exceptions + global handler
│       │   ├── repository/        # Spring Data JPA repositories
│       │   ├── scheduler/         # Cron jobs (daily midnight)
│       │   ├── security/          # JWT filter chain + provider
│       │   ├── service/           # Business logic layer
│       │   └── util/              # Constants, helpers
│       ├── python/                # OCR scripts (PaddleOCR)
│       ├── Dockerfile
│       └── docker-compose.yml
│
└── frontend/
    └── CarShareSV-Frontend/       # React + Vite app
        ├── src/
        │   ├── components/        # Reusable UI components
        │   ├── pages/             # Page components (13 pages)
        │   ├── services/          # API service layer
        │   ├── hooks/             # Custom React hooks
        │   ├── context/           # UserContext (auth state)
        │   └── assets/            # Images, static assets
        ├── vercel.json
        └── vite.config.js
```

## Features

- **Vehicle Marketplace** — Browse, filter, and reserve cars
- **User Authentication** — Register, login, JWT-based auth with 3 roles (USER, ADMIN, SYSADMIN)
- **Driver License OCR** — Upload license images, automatic text extraction and validation via PaddleOCR
- **Recommendations** — Popular and personalized car recommendations
- **Payment Processing** — Multi-step checkout with payment method management
- **Admin Dashboard** — User management, car oversight, payment simulation
- **Role-Based Access** — Granular permissions per endpoint
