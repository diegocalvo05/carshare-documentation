# Deployment

## Docker (Backend)

### Prerequisites

- Docker & Docker Compose
- `.env` file with database and JWT configuration

### Build & Run

```bash
cd backend/CarShareSV_webservice
docker compose up --build
```

This starts two services:

| Service | Image | Port | Purpose |
|---|---|---|---|
| `db` | `postgres:16-alpine` | — (internal) | PostgreSQL database |
| `backend` | Built from `Dockerfile` | `8080:8080` | Spring Boot + Python OCR |

### Docker Compose Details

```yaml
services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
    volumes:
      - carshare-db-data:/var/lib/postgresql/data

  backend:
    build: .
    depends_on:
      db:
        condition: service_healthy
    env_file: .env
    ports:
      - "8080:8080"
    volumes:
      - carshare-ocr-models:/home/carshare/.paddleocr
```

### Volumes

| Volume | Mount Point | Purpose |
|---|---|---|
| `carshare-db-data` | `/var/lib/postgresql/data` | Database persistence |
| `carshare-ocr-models` | `/home/carshare/.paddleocr` | PaddleOCR model cache |

### Dockerfile Layers

The Docker image uses a multi-stage build:

1. **Builder** (`maven:3.9.6-eclipse-temurin-21`): compiles the JAR with layer-optimized caching
2. **Runtime** (`eclipse-temurin:21-jre-noble`): JRE + Python 3.12 + PaddleOCR

Key runtime details:
- Python 3.12.3 with PaddleOCR (pre-baked model at build time)
- Non-root user `carshare` (uid 1001)
- Healthcheck at `/actuator/health` (interval 30s, start-period 60s)
- Memory limit: 4g (backend), 512m (db)

### Updating PaddleOCR Models

```bash
docker compose down
docker volume rm carshare-ocr-models
docker compose up --build
```

This drops the cached model volume and recreates it from the freshly baked image.

## Vercel (Frontend)

The frontend is deployed on Vercel as a SPA.

### Configuration (`vercel.json`)

```json
{
  "rewrites": [{"source": "/(.*)", "destination": "/index.html"}]
}
```

All routes fall back to `index.html` for client-side routing.

### Environment Variables (Vercel)

| Variable | Description |
|---|---|
| `VITE_API_URL` | Production API base URL |
| `VITE_CLOUDINARY_UPLOAD_PRESET` | Cloudinary upload preset |
| `VITE_CLOUDINARY_API_KEY` | Cloudinary API key |
| `VITE_CLOUDINARY_FOLDER_NAME` | Cloudinary folder for car images |

## GitHub Pages (Documentation)

This documentation site is built with MkDocs and published to GitHub Pages.

### Build Locally

```bash
pip install mkdocs mkdocs-material
mkdocs build
```

### Deploy

```bash
mkdocs gh-deploy
```

This pushes the built site to the `gh-pages` branch, which GitHub Pages serves automatically.

## Environment Variables Reference

### Backend

| Variable | Default | Required | Description |
|---|---|---|---|
| `PORT` | `8080` | No | Server port |
| `DB_HOST` | `db` | No | PostgreSQL host |
| `DB_PORT` | `5432` | No | PostgreSQL port |
| `DB_NAME` | — | **Yes** | Database name |
| `DB_USER` | — | **Yes** | Database user |
| `DB_PASSWORD` | — | **Yes** | Database password |
| `JWT_SECRET` | — | **Yes** | HMAC key (openssl rand -hex 32) |
| `JWT_EXPIRATION` | `86400000` | No | Token lifetime (ms) |
| `PYTHON_EXECUTABLE` | `python` | No | Python binary |
| `PYTHON_EXECUTION_TIMEOUT` | `120` | No | OCR timeout (s) |

### Frontend

| Variable | Required | Description |
|---|---|---|
| `VITE_API_URL` | **Yes** | Backend API base URL |
| `VITE_CLOUDINARY_UPLOAD_PRESET` | **Yes** | Cloudinary upload preset |
| `VITE_CLOUDINARY_API_KEY` | **Yes** | Cloudinary API key |
| `VITE_CLOUDINARY_FOLDER_NAME` | No | Cloudinary folder |
