# API Reference

Base URL: `http://localhost:8080/api`

All authenticated endpoints require the header: `Authorization: Bearer <token>`

---

## Auth (`/api/auth`)

| Method | Path | Auth | Description |
|---|---|---|---|
| POST | `/auth/register` | Public | Register a new user |
| POST | `/auth/login` | Public | Login, returns JWT access token |
| GET | `/auth/whoami` | Authenticated | Get current user info |

---

## Cars (`/api/cars`)

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/cars/getAll` | SYSADMIN, ADMIN | List all cars |
| GET | `/cars/getAllVisible` | Authenticated | List visible cars |
| POST | `/cars/create` | Authenticated | Create a car listing |
| DELETE | `/cars/delete/{carId}` | Authenticated | Delete own car |
| GET | `/cars/getCarById/{carId}` | Authenticated | Get car details |
| GET | `/cars/getCarByUserId/{userId}` | Authenticated | Get user's cars |
| GET | `/cars/getAllCarsByModel/{modelId}` | Authenticated | Filter by model |
| GET | `/cars/getAllCarsByBrand/{brandId}` | Authenticated | Filter by brand |
| GET | `/cars/getAllCarsByYear/{year}` | Authenticated | Filter by year |
| PATCH | `/cars/updateDailyPrice` | Authenticated | Update daily price |
| PATCH | `/cars/updateDescription` | Authenticated | Update description |
| PATCH | `/cars/setCarVisibility/{carId}` | Authenticated | Toggle visibility |

---

## Users (`/api/user`)

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/user/getAll` | SYSADMIN, ADMIN | List all users |
| GET | `/user/getAllActive` | Authenticated | List active users |
| GET | `/user/getAllNotActive` | SYSADMIN, ADMIN | List inactive users |
| GET | `/user/getById` | Authenticated | Get user by ID (query param) |
| GET | `/user/getByUsernameOrEmail/{identifier}` | Authenticated | Get by username/email |
| GET | `/user/getUserRoles` | SYSADMIN, ADMIN | Get user roles |
| PATCH | `/user/deactivate/{id}` | Authenticated | Deactivate user |
| PATCH | `/user/activate/{id}` | Authenticated | Activate user |
| PATCH | `/user/updateFirstName` | Authenticated | Update first name |
| PATCH | `/user/updateLastName` | Authenticated | Update last name |
| PATCH | `/user/updateUsername` | Authenticated | Update username |
| PATCH | `/user/updateEmail` | Authenticated | Update email |
| PATCH | `/user/updatePhoneNumber` | Authenticated | Update phone number |
| PATCH | `/user/grantAdminRole/{id}` | SYSADMIN only | Grant admin role |
| PATCH | `/user/revokeAdminRole/{id}` | SYSADMIN only | Revoke admin role |

---

## Reservations (`/api/reservation`)

| Method | Path | Roles | Description |
|---|---|---|---|
| POST | `/reservation/create` | Authenticated | Create reservation |
| GET | `/reservation/getAll` | Authenticated | Get car's reservations |
| GET | `/reservation/getAllByUser` | Authenticated | Get user's reservations |
| PATCH | `/reservation/cancel/{id}` | Authenticated | Cancel reservation |
| GET | `/reservation/getAllCarReservedDates` | Authenticated | Get reserved dates |

---

## Reviews (`/api/reviews`)

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/reviews/getAll` | SYSADMIN, ADMIN | List all reviews |
| POST | `/reviews/create/{carId}` | Authenticated | Create review |
| GET | `/reviews/getReviewById/{reviewId}` | Authenticated | Get review by ID |
| GET | `/reviews/getAllReviewByUserId/{userName}` | Authenticated | Reviews by user |
| GET | `/reviews/getAllReviewByCarId/{carId}` | Authenticated | Reviews by car |
| PATCH | `/reviews/updateComment` | Authenticated | Update comment |
| PATCH | `/reviews/updateRating` | Authenticated | Update rating |
| DELETE | `/reviews/delete` | Authenticated | Delete review |

---

## Driver License (`/api/driver-license`)

| Method | Path | Roles | Description |
|---|---|---|---|
| POST | `/driver-license/analyze` | Authenticated | Analyze license from image URL (OCR) |
| POST | `/driver-license/reanalyze` | Authenticated | Re-analyze existing license |
| POST | `/driver-license/create` | Authenticated | Manual create (skip OCR) |
| GET | `/driver-license/me` | Authenticated | Get my license |
| PUT | `/driver-license/update` | Authenticated | Update license |
| DELETE | `/driver-license/delete` | Authenticated | Delete license |
| GET | `/driver-license/status` | Authenticated | Get verification status |

---

## Payments (`/api/payments`)

### Payment Methods

| Method | Path | Roles | Description |
|---|---|---|---|
| POST | `/payments/createPaymentMethod` | Authenticated | Create payment method |
| GET | `/payments/getUserPaymentMethods` | Authenticated | List my methods |
| GET | `/payments/getById/{paymentMethodId}` | Authenticated | Get method by ID |
| PATCH | `/payments/setDefault/{paymentMethodId}` | Authenticated | Set default method |
| PATCH | `/payments/deactivate/{paymentMethodId}` | Authenticated | Deactivate method |
| PATCH | `/payments/activate/{paymentMethodId}` | Authenticated | Activate method |
| DELETE | `/payments/delete/{paymentMethodId}` | Authenticated | Delete method |

### Payment Transactions

| Method | Path | Roles | Description |
|---|---|---|---|
| POST | `/payments/processPayment` | Authenticated | Process payment |
| GET | `/payments/getById/transaction/{transactionId}` | Authenticated | Get by ID |
| GET | `/payments/getTransactionByCode/{code}` | Authenticated | Get by code |
| GET | `/payments/getUserTransactions` | Authenticated | My transactions |
| GET | `/payments/getUserTransactionsByStatus` | Authenticated | My transactions by status |
| GET | `/payments/getReservationTransactions` | Authenticated | Reservation's transactions |

### Admin Payment Simulation

| Method | Path | Roles | Description |
|---|---|---|---|
| PATCH | `/payments/simulateSuccess/{transactionId}` | SYSADMIN, ADMIN | Simulate success |
| PATCH | `/payments/simulateFailure/{transactionId}` | SYSADMIN, ADMIN | Simulate failure |
| PATCH | `/payments/refund/{transactionId}` | SYSADMIN, ADMIN | Refund payment |

### Admin Data

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/payments/getAllTransactions` | SYSADMIN, ADMIN | All transactions |
| GET | `/payments/getAllTransactionsByStatus` | SYSADMIN, ADMIN | All by status |
| GET | `/payments/admin/user/{userId}/transactions` | SYSADMIN, ADMIN | User's transactions |
| GET | `/payments/admin/user/{userId}/transactions/status` | SYSADMIN, ADMIN | User's transactions by status |
| GET | `/payments/admin/user/{userId}/payment-methods` | SYSADMIN, ADMIN | User's payment methods |

---

## Recommendations (`/api/recommendations`)

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/recommendations/popular` | Authenticated | Popular cars (optional `limit` param) |
| GET | `/recommendations/personalized` | Authenticated | Personalized (params: `limit`, `minHistory`) |

---

## Vehicle Availability (`/api/vehicles`)

| Method | Path | Roles | Description |
|---|---|---|---|
| GET | `/vehicles/{vehicleId}/availability` | Authenticated | Check availability for date range |

---

## Python Integration (`/api/python`)

| Method | Path | Roles | Description |
|---|---|---|---|
| POST | `/python/test` | Authenticated | Test Python subprocess integration |
