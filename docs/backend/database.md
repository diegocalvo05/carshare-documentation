# Database Schema

**Engine**: PostgreSQL 16  
**DDL Strategy**: `hibernate.ddl-auto: update` (schema auto-managed)  
**Default Schema**: `public`

## Entity Relationship Diagram

```
┌──────────┐     ┌──────────────┐     ┌──────────┐
│   User   │1───*│  Reservation │*───1│   Car    │
└────┬─────┘     └──────────────┘     └────┬─────┘
     │                                     │
     │1                                    │1
     │                                     │
┌────┴──────┐                    ┌────────┴────────┐
│   Role    │*                    │     Brand       │
│(join table│                     └────────┬────────┘
│ userxrole)│                              │1
└───────────┘                     ┌────────┴────────┐
                                  │     Model       │
┌─────────────────┐               └─────────────────┘
│ DriverLicense   │
│ (1:1 with User) │               ┌───────────────┐
└─────────────────┘               │     Year       │
                                  └───────────────┘
┌─────────────────┐
│ PaymentMethod   │               ┌───────────────┐
│ (*:1 with User) │               │ Transmission  │
└────────┬────────┘               └───────────────┘
         │
         │*              ┌─────────────────┐
┌────────┴────────┐     │     Image        │
│PaymentTransaction│*───1│ (*:1 with Car)   │
└─────────────────┘     └─────────────────┘

┌──────────┐
│  Review  │*───1 Car
│ *───1 User
└──────────┘
```

## Tables

### `user`
| Column | Type | Constraints |
|---|---|---|
| `user_id` | UUID | PK, auto-generated |
| `first_name` | VARCHAR(255) | |
| `last_name` | VARCHAR(255) | |
| `username` | VARCHAR(255) | UNIQUE |
| `password` | VARCHAR(255) | BCrypt hashed |
| `email` | VARCHAR(255) | UNIQUE |
| `phone_number` | VARCHAR(255) | |
| `birthdate` | DATE | |
| `active` | BOOLEAN | |

### `role`
| Column | Type | Constraints |
|---|---|---|
| `role_id` | INTEGER | PK, IDENTITY |
| `role_name` | VARCHAR(255) | UNIQUE — `ROLE_USER`, `ROLE_ADMIN`, `ROLE_SYSADMIN` |

### `userxrole` (join table)
| Column | Type | References |
|---|---|---|
| `user_id` | UUID | `user(user_id)` |
| `role_id` | INTEGER | `role(role_id)` |

### `car`
| Column | Type | Constraints |
|---|---|---|
| `car_id` | UUID | PK, auto-generated |
| `created_at` | TIMESTAMP | `@CreationTimestamp` |
| `doors` | INTEGER | |
| `capacity` | INTEGER | |
| `daily_price` | FLOAT | |
| `plate_number` | VARCHAR(255) | |
| `description` | TEXT | |
| `visible` | BOOLEAN | |
| `location` | VARCHAR(255) | |
| `latitude` | FLOAT | |
| `longitude` | FLOAT | |
| `user_id` | UUID | FK → `user(user_id)` |
| `year_id` | INTEGER | FK → `year(year_id)` |
| `model_id` | INTEGER | FK → `model(model_id)` |
| `brand_id` | INTEGER | FK → `brand(brand_id)` |
| `transmission_id` | INTEGER | FK → `transmission(transmission_id)` |

### `brand`
| Column | Type | Constraints |
|---|---|---|
| `brand_id` | INTEGER | PK, IDENTITY |
| `brand` | VARCHAR(255) | UNIQUE |

### `model`
| Column | Type | Constraints |
|---|---|---|
| `model_id` | INTEGER | PK, IDENTITY |
| `model` | VARCHAR(255) | UNIQUE |
| `brand_id` | INTEGER | FK → `brand(brand_id)` |

### `year`
| Column | Type | Constraints |
|---|---|---|
| `year_id` | INTEGER | PK, IDENTITY |
| `year` | INTEGER | UNIQUE |

### `transmission`
| Column | Type | Constraints |
|---|---|---|
| `transmission_id` | INTEGER | PK, IDENTITY |
| `transmission` | VARCHAR(255) | UNIQUE |

### `image`
| Column | Type | Constraints |
|---|---|---|
| `image_id` | UUID | PK, auto-generated |
| `url` | VARCHAR(255) | |
| `car_id` | UUID | FK → `car(car_id)` |

### `reservation`
| Column | Type | Constraints |
|---|---|---|
| `reservation_id` | UUID | PK, auto-generated |
| `start_date` | TIMESTAMP | |
| `end_date` | TIMESTAMP | |
| `address` | VARCHAR(255) | |
| `total` | FLOAT | |
| `status` | VARCHAR(255) | ACTIVE / FINISHED / CANCELLED |
| `user_id` | UUID | FK → `user(user_id)` |
| `car_id` | UUID | FK → `car(car_id)` |

### `review`
| Column | Type | Constraints |
|---|---|---|
| `review_id` | UUID | PK, auto-generated |
| `comment` | TEXT | |
| `rating` | INTEGER | |
| `car_id` | UUID | FK → `car(car_id)` |
| `user_id` | UUID | FK → `user(user_id)` |

### `driver_license`
| Column | Type | Constraints |
|---|---|---|
| `id` | UUID | PK, auto-generated |
| `image_url` | VARCHAR(255) | |
| `driver_name` | VARCHAR(255) | |
| `license_number` | VARCHAR(255) | |
| `category` | VARCHAR(255) | A, B, C, D, E, M |
| `expiration_date` | DATE | |
| `raw_text` | TEXT | Full OCR output |
| `ocr_confidence` | FLOAT | |
| `validation_score` | INTEGER | |
| `valid` | BOOLEAN | |
| `validation_errors` | TEXT | JSON array |
| `validation_warnings` | TEXT | JSON array |
| `validation_messages` | TEXT | JSON array |
| `verification_status` | VARCHAR(255) | Enum |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |
| `user_id` | UUID | FK → `user(user_id)`, UNIQUE |

### `payment_methods`
| Column | Type | Constraints |
|---|---|---|
| `id` | BIGINT | PK, IDENTITY |
| `payment_type` | VARCHAR(255) | CREDIT_CARD, DEBIT_CARD, PAYPAL, BANK_TRANSFER, DIGITAL_WALLET |
| `card_number` | VARCHAR(255) | Encrypted |
| `card_holder_name` | VARCHAR(255) | |
| `expiry_date` | VARCHAR(255) | |
| `cvv` | VARCHAR(255) | Encrypted |
| `is_default` | BOOLEAN | |
| `is_active` | BOOLEAN | |
| `created_at` | TIMESTAMP | |
| `updated_at` | TIMESTAMP | |
| `user_id` | UUID | FK → `user(user_id)` |

### `payment_transactions`
| Column | Type | Constraints |
|---|---|---|
| `transaction_id` | UUID | PK, auto-generated |
| `transaction_code` | VARCHAR(255) | UNIQUE, pattern: `TXN-{timestamp}` |
| `amount` | DECIMAL(10,2) | |
| `currency` | VARCHAR(3) | Default: `USD` |
| `status` | VARCHAR(255) | PENDING, PROCESSING, COMPLETED, FAILED, CANCELLED, REFUNDED |
| `description` | VARCHAR(255) | |
| `reference_id` | VARCHAR(255) | |
| `created_at` | TIMESTAMP | |
| `processed_at` | TIMESTAMP | |
| `user_id` | UUID | FK → `user(user_id)` |
| `payment_method_id` | BIGINT | FK → `payment_methods(id)` |
| `reservation_id` | UUID | FK → `reservation(reservation_id)` |

## Enums

| Enum | Values |
|---|---|
| `VerificationStatus` | `NOT_UPLOADED`, `PENDING`, `OCR_VALIDATED`, `VERIFIED`, `PENDING_REVIEW`, `REJECTED`, `EXPIRED` |
| `PaymentStatus` | `PENDING`, `PROCESSING`, `COMPLETED`, `FAILED`, `CANCELLED`, `REFUNDED` |
| `PaymentType` | `CREDIT_CARD`, `DEBIT_CARD`, `PAYPAL`, `BANK_TRANSFER`, `DIGITAL_WALLET` |
