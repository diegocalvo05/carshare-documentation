# Backend Overview

## Stack

| Component | Technology |
|---|---|
| Runtime | Java 21 (Eclipse Temurin) |
| Framework | Spring Boot 3.5 |
| Database | PostgreSQL 16 |
| ORM | Spring Data JPA / Hibernate |
| Auth | Spring Security + JWT (jjwt 0.12.6) |
| Mapping | ModelMapper 3.0 |
| Validation | Jakarta Bean Validation |
| Config | spring-dotenv 4.0 |
| Build | Maven (wrapper 3.9.9) |

## Project Structure

```
src/main/java/org/carshare/carsharesv_webservice/
├── CarShareSvWebserviceApplication.java   # Entry point (@SpringBootApplication, @EnableScheduling)
├── configuration/
│   ├── SecurityConfiguration.java         # Spring Security, CORS, JWT filter
│   ├── MapperConfig.java                  # ModelMapper bean
│   ├── DriverLicenseScoringProperties.java
│   ├── DriverLicenseValidationProperties.java
│   └── PythonRunnerProperties.java
├── controller/                            # 10 REST controllers
├── domain/
│   ├── dto/request/                       # 11 request DTOs
│   ├── dto/response/                      # 18 response DTOs
│   └── entity/                            # 12 JPA entities + 3 enums
├── exception/                             # 30+ custom exceptions + GlobalErrorHandler
├── repository/                            # 14 Spring Data JPA repositories
├── scheduler/                             # 2 cron tasks (daily midnight)
├── security/                              # JWT filter, provider, UserDetailsService
├── service/                               # 12 interfaces + 11 implementations
└── util/                                  # Constants, helpers
```

## Modules

### Auth
Handles registration (BCrypt hashing), login (`AuthenticationManager`), and JWT generation. Exposes `/api/auth/*` endpoints publicly.

### Cars
Full CRUD for car listings. Supports filtering by brand, model, year, transmission, and visibility toggling.

### Users
User profile management, activation/deactivation, and role assignment. Admin and SysAdmin have elevated access.

### Reservations
Creates reservations with overlap detection and driver license validation gate. Supports cancellation and date-range availability queries.

### Reviews
CRUD for car reviews with rating and comment. Filters by user or car.

### Payments
Payment method management (with card masking/encryption) and transaction processing through a simulated state machine.

### Recommendations
Popular ranking (reservation count + rating) and personalized recommendations (90-day preference window).

### Driver License
Full OCR pipeline: image upload → PaddleOCR → field parsing → scoring → validation → verification status.

### Python Runner
Executes Python scripts as subprocesses with timeout and path traversal protection.

## API Response Envelope

All endpoints return:

```json
{
  "message": "Success",
  "data": { ... },
  "status": "OK"
}
```

Errors use the same envelope with error codes:

```json
{
  "message": "License upload is required before creating a reservation",
  "data": null,
  "status": "LICENSE_REQUIRED"
}
```
