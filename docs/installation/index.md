# Installation

OtterFin ships as a pre-built Docker image. You need Docker, a `.env` file, and a `docker-compose.yml`. No source code or build tools required on the target machine.

!!! note "Synology NAS"
    For step-by-step instructions using the Synology Container Manager UI, see the [Synology NAS](synology.md) guide.

## Prerequisites

- Docker with the Compose plugin — verify with `docker compose version`
- Any machine running Linux, macOS, or Windows with Docker Desktop

## 1. Get the Compose file

Download `docker-compose.yml` from the OtterFin repository:

```bash
curl -O https://raw.githubusercontent.com/otterfin-ai/otterfin/main/docker-compose.yml
```

Or create the file manually:

```yaml
services:
  db:
    image: postgres:18.3
    container_name: otterfin-postgres
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-otterfin}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?Set POSTGRES_PASSWORD in your .env file}
      POSTGRES_DB: ${POSTGRES_DB:-otterfin}
    volumes:
      - ./postgres:/var/lib/postgresql
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER:-otterfin} -d ${POSTGRES_DB:-otterfin}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  app:
    image: otterfin/community:latest
    container_name: otterfin-app
    ports:
      - "4010:3000"
    env_file:
      - .env
    environment:
      AUTH_TRUST_HOST: "true"
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
```

## 2. Create your .env file

Generate a secret key first:

```bash
openssl rand -base64 32
```

Then create `.env` in the same directory as your Compose file:

```env
# Database — set these before first run
POSTGRES_HOST=otterfin-postgres   # container name — leave as-is
POSTGRES_PORT=5432
POSTGRES_USER=otterfin
POSTGRES_PASSWORD=a-strong-password-here
POSTGRES_DB=otterfin

# App
NEXTAUTH_SECRET=your-generated-secret
NEXTAUTH_URL=http://your-server-ip:4010
AUTH_TRUST_HOST=true

# Optional — email notifications
SMTP_HOST=
SMTP_PORT=
SMTP_USER=
SMTP_PASS=

# Optional — exchange rate sync for multi-currency
EXCHANGE_RATE_API_KEY=

# Optional — household owner sees all accounts without explicit sharing
HOUSEHOLD_OWNER_IMPLICIT_ACCOUNT_ACCESS=false

# Optional — social login
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
```

!!! tip
    `DATABASE_URL` is constructed automatically from the `POSTGRES_*` variables — do not set it separately.

## 3. Start OtterFin

```bash
docker compose up -d
```

This pulls the images, starts PostgreSQL, runs database migrations, and starts the app. Allow 30–60 seconds on first boot while migrations complete.

Open `http://your-server-ip:4010` to complete first-run setup.

## First-run onboarding

On first boot, OtterFin starts in onboarding mode:

- Registration is open only while the database has no users
- The first user provides a name, household name, email, and password
- That user becomes the household owner and instance admin
- After the first user registers, `/auth/register` is automatically disabled for new visitors

## Ports

| Service    | Host port | Container port |
|------------|-----------|----------------|
| App        | 4010      | 3000           |
| PostgreSQL | 5433      | 5432           |

PostgreSQL is only exposed for direct host access. The app communicates with it over the internal Docker network.

## Updating

```bash
docker compose pull && docker compose up -d
```

Migrations run automatically on startup — new migrations are applied, already-applied ones are skipped.

## Stopping

```bash
docker compose down        # stop, preserve data
docker compose down -v     # stop and delete all data (destructive)
```
