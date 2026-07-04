# Setup

## Prerequisites

- **Java 21** (JDK)
- **Node.js 22+**
- **Docker + Docker Compose** (for the backend stack)
- **Python 3.12** (for OCR — bundled in Docker)

## Environment Variables

### Backend `.env`

Create `backend/CarShareSV_webservice/.env`:

```ini
DB_NAME=carsharedb
DB_USER=carshare
DB_PASSWORD=your_secure_password
DB_HOST=db
DB_PORT=5432
JWT_SECRET=generate_with_openssl_rand_hex_32
JWT_EXPIRATION=86400000
PORT=8080
```

Required variables:

| Variable | Default | Description |
|---|---|---|
| `DB_NAME` | — | PostgreSQL database name |
| `DB_USER` | — | Database user |
| `DB_PASSWORD` | — | Database password |
| `DB_HOST` | `db` | Database hostname |
| `DB_PORT` | `5432` | Database port |
| `JWT_SECRET` | — | HMAC-SHA key (generate: `openssl rand -hex 32`) |
| `JWT_EXPIRATION` | `86400000` | Token lifetime in ms (24h) |
| `PORT` | `8080` | Server port |
| `PYTHON_EXECUTABLE` | `python` | Python binary path |
| `PYTHON_EXECUTION_TIMEOUT` | `120` | OCR timeout in seconds |

### Frontend `.env`

Create `frontend/CarShareSV-Frontend/.env`:

```ini
VITE_API_URL=http://localhost:8080/api
VITE_CLOUDINARY_UPLOAD_PRESET=your_preset
VITE_CLOUDINARY_API_KEY=your_key
VITE_CLOUDINARY_FOLDER_NAME=carshare-cars
```

## Running Locally

### Backend (Docker)

```bash
cd backend/CarShareSV_webservice
cp .env.example .env
# Edit .env with your values
docker compose up --build
```

The backend starts on `http://localhost:8080`. Healthcheck: `http://localhost:8080/actuator/health`.

### Backend (without Docker)

```bash
cd backend/CarShareSV_webservice
# Ensure PostgreSQL is running and .env is configured
./mvnw spring-boot:run
```

### Frontend

```bash
cd frontend/CarShareSV-Frontend
npm install
npm run dev
```

The frontend starts on `http://localhost:5173`.

## Scripts

### Backend

| Command | Description |
|---|---|
| `./mvnw clean package -DskipTests` | Build JAR |
| `./mvnw spring-boot:run` | Run dev server |
| `./mvnw test` | Run tests |

### Frontend

| Command | Description |
|---|---|
| `npm run dev` | Development server (Vite) |
| `npm run build` | Production build |
| `npm run preview` | Preview production build |
| `npm run lint` | ESLint check |
