# Frontend Overview

## Stack

| Component | Technology |
|---|---|
| Framework | React 19 |
| Build Tool | Vite 6 + SWC |
| Routing | React Router 7 |
| Styling | Tailwind CSS 4 |
| HTTP Client | Axios 1.10 + fetch() |
| Maps | Leaflet 1.9 + React-Leaflet 5 |
| Icons | React Icons 5 |
| Date Picker | React Date Range 2 + date-fns 4 |
| File Upload | React Dropzone 14 |

## Project Structure

```
src/
├── main.jsx                          # Entry point (React.StrictMode)
├── App.jsx                           # Root: BrowserRouter + Routes + UserContext
├── index.css                         # Tailwind v4 imports + theme
├── utils.js                          # Date/time utilities (Spanish locale)
│
├── context/
│   └── UserContext.jsx                # Auth context: token, user, isAdmin
│
├── hooks/
│   ├── useCars.jsx                    # Fetch visible/hidden/user cars
│   ├── useDriverLicense.jsx           # License CRUD + reanalysis
│   ├── useManageCars.jsx              # Create, delete, toggle car visibility
│   ├── usePayment.jsx                 # Payment methods + processing
│   ├── useRecommendations.jsx         # Popular + personalized
│   ├── useReservation.jsx             # Create/cancel, check availability
│   ├── useReview.jsx                  # Create reviews, fetch car reviews
│   ├── useSignIn.jsx                  # Registration + auto-login
│   ├── useUploadImage.jsx             # Cloudinary image upload
│   └── useUser.jsx                    # Login, logout, admin operations
│
├── services/
│   ├── auth.service.js                # Auth API (register, login, whoami)
│   ├── car.service.js                 # Cars API (CRUD, visibility)
│   ├── cloudinary.service.js          # Image upload to Cloudinary
│   ├── driverLicense.service.js       # License analyze/reanalyze
│   ├── payment.service.js             # Payment methods + processing
│   ├── recommendation.service.js      # Popular + personalized
│   ├── reservation.service.js         # Reservations CRUD
│   └── review.service.js              # Reviews CRUD
│
├── components/
│   ├── alerts/alert.jsx               # Reusable AlertPopup modal
│   ├── button/Button.jsx              # Reusable Button (Link or <button>)
│   ├── carReviews/reviewCard.jsx      # Review display (stars + comment)
│   ├── cards/
│   │   ├── CarCard/                   # Car card for explore grid
│   │   ├── CurrentDetailCard/         # Active rent details
│   │   ├── MyCarsCard/                # Car management card
│   │   ├── MyRentsCard/               # Rent car info
│   │   ├── PastDetailCard/            # Past rent + review link
│   │   ├── RecommendationCard/        # Recommendation item
│   │   └── UserCard/                  # Admin user card
│   ├── driverLicense/
│   │   ├── DriverLicenseUploader.jsx
│   │   ├── DriverLicenseStatusCard.jsx
│   │   └── DriverLicenseVerificationModal.jsx
│   ├── filters/
│   │   ├── explorefilters/            # Combined explore filters
│   │   └── generalfilters/            # Brand, Model, Year, Transmission
│   ├── header/Header.jsx              # Role-aware navigation
│   ├── imageslider/imageslider.jsx    # Image carousel + fullscreen
│   ├── map/
│   │   ├── MapaPicker.jsx             # Draggable marker + Nominatim search
│   │   └── MapaShow.jsx               # Read-only map display
│   ├── recommendations/
│   │   └── RecommendationsSection.jsx # Recommendation carousel
│   └── rentsSwitcher/RentsSwitcher.jsx # Tab switcher (Actuals/Pasts)
│
└── pages/
    ├── landingPage/LandingPage.jsx     # Hero page
    ├── signIn/SignIn.jsx               # Registration form
    ├── logIn/LogIn.jsx                 # Login form
    ├── explorePage/Explore.jsx         # Car browsing + filters
    ├── carDetails/CarDetails.jsx       # Car detail + reservation
    ├── checkout/CheckoutPage.jsx       # Multi-step checkout
    ├── myProfile/MyProfile.jsx         # User profile + license
    ├── myCars/MyCars.jsx              # User's posted cars
    ├── postPage/PostPage.jsx          # Car creation form
    ├── actualRents/ActualRents.jsx    # Active reservations
    ├── pastRents/PastRents.jsx        # Past reservations
    └── adminView/
        ├── myProfileAdmin/myProfileAdmin.jsx
        ├── UsersList/userlist.jsx     # User management
        ├── carDetails/carDetailsAdmin.jsx
        └── hiddenCars/HiddenCars.jsx  # Hidden cars admin
```

## Pages & Routes

| Route | Component | Description |
|---|---|---|
| `/` | `LandingPage` | Hero with background image and CTA |
| `/signin` | `SignIn` | User registration |
| `/login` | `LogIn` | User login |
| `/explore` | `Explore` | Browse cars with filters + recommendations |
| `/car/:carId` | `CarDetails` | Car detail, date picker, reservation |
| `/checkout` | `CheckoutPage` | Multi-step payment checkout |
| `/myprofile` | `MyProfile` | Profile + driver license management |
| `/mycars` | `MyCars` | Manage own car listings |
| `/post/new` | `PostPage` | Create new car listing |
| `/rents/actuals` | `ActualRents` | Active reservations |
| `/rents/pasts` | `PastRents` | Past reservations |
| `/admin/myprofile` | `AdminProfile` | Admin profile |
| `/admin/users` | `UsersList` | User management (admin) |
| `/admin/hiddenCars` | `HiddenCars` | Hidden cars (admin) |

## Authentication Flow

1. User submits credentials via `LogIn.jsx`
2. `auth.service.js` → `POST /api/auth/login` → receives `{ accessToken }`
3. Token stored in `sessionStorage` via `UserContext`
4. `GET /api/auth/whoami` fetches user object with roles
5. `Header` renders role-specific navigation (admin links for ADMIN/SYSADMIN)

**Note**: Routes are not guarded at the router level. Protection relies on UI-level hiding of admin links and backend authorization enforcement.

## API Client Pattern

Services use a mix of `fetch()` and `axios`:

- **`fetch()`** — used for mutations in auth, car, review, and cloudinary services
- **`axios`** — used for GET requests and reservation/payment/driver-license services

The base URL is configured via `VITE_API_URL` environment variable.
Authentication headers are added per-request using the token from `UserContext`.

## Styling

- **Tailwind CSS 4** with Vite plugin
- **Theme colors**: `primary` (#d1285e) and `secondary` (#28115F)
- **Font**: Poppins (Google Fonts)
- Spanish-first UI throughout all labels and messages
