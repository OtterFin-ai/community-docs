# Synology NAS

This guide covers two installation methods depending on your DSM version.

!!! note "DSM version differences"
    In DSM 7.1 the Docker app is called **Docker**. In DSM 7.2 and later it was renamed to **Container Manager** and gained a Project feature for running Compose stacks through the UI. Method 1 requires DSM 7.2+. Method 2 works on both.

    DSM 7.1 ships Docker 20.10, which uses the legacy `docker-compose` binary (hyphenated). DSM 7.2+ ships Compose V2, which uses `docker compose` (with a space). The SSH examples below show both where they differ.

## Prerequisites

- Synology NAS running DSM 7.1 or later
- **Docker** (DSM 7.1) or **Container Manager** (DSM 7.2+) installed from Package Center

---

## Setup (both methods)

### 1. Create the data folder

**File Station:** create `/volume1/docker/otterfin/postgres`

Or via SSH:

```bash
mkdir -p /volume1/docker/otterfin/postgres
```

### 2. Create the environment file

Create `/volume1/docker/otterfin/.env` with your settings. This file is shared by both containers — you only need to configure credentials in one place.

Generate a secret key first:

```bash
openssl rand -base64 32
```

Then create the file:

```env
# Database credentials
POSTGRES_HOST=db
POSTGRES_PORT=5432
POSTGRES_USER=otterfin
POSTGRES_PASSWORD=a-strong-password-here
POSTGRES_DB=otterfin

# App
NEXTAUTH_SECRET=your-generated-secret
NEXTAUTH_URL=http://your-nas-ip:4010
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

---

## Method 1: Container Manager Project UI (DSM 7.2+ only)

1. Open **Container Manager** → **Project** → **Create**
2. Set **Project Name** to `otterfin`
3. Set **Path** to `/volume1/docker/otterfin` (Container Manager picks up your `.env` automatically)
4. Select **Create docker-compose.yml** and paste the following:

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
      - /volume1/docker/otterfin/postgres:/var/lib/postgresql
    restart: unless-stopped

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
      - db
    restart: unless-stopped
```

5. Click **Next**, then **Done**. Container Manager pulls the images and starts both containers. Allow 30–60 seconds on first boot while migrations run, then open `http://your-nas-ip:4010`.

---

## Method 2: SSH (DSM 7.1 and 7.2+)

Use this method if you are on DSM 7.1, or if you prefer managing files directly over SSH.

Create `/volume1/docker/otterfin/docker-compose.yml`:

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
      - /volume1/docker/otterfin/postgres:/var/lib/postgresql
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

Then start the stack:

```bash
cd /volume1/docker/otterfin

# DSM 7.2+ (Compose V2)
docker compose up -d

# DSM 7.1 (legacy binary)
docker-compose up -d
```

---

## Accessing OtterFin via a domain name

Port 4010 works fine for local LAN access (`http://nas-ip:4010`). If you want to reach OtterFin from outside your home network, or via a hostname, choose one of the options below.

### Option A: Tailscale (recommended for private access)

Tailscale gives every device on your Tailnet a stable hostname without exposing anything to the public internet.

1. Install the **Tailscale** package from Synology Package Center and sign in
2. Note your NAS's Tailscale hostname — something like `nas-name.tail1234.ts.net`
3. From any device on your Tailnet, open `http://nas-name.tail1234.ts.net:4010`
4. Update `NEXTAUTH_URL` in your `.env` to `http://nas-name.tail1234.ts.net:4010` and restart the stack

No reverse proxy or certificate setup required. Only devices connected to your Tailnet can reach OtterFin.

### Option B: Synology reverse proxy with DDNS (public HTTPS)

Use this if you want OtterFin reachable from the public internet via a `synology.me` address or your own domain.

**Enable DDNS** (if not already set up):

1. **Control Panel** → **External Access** → **DDNS** → **Add**
2. Choose **Synology** as the provider and register a hostname (e.g. `yourname.synology.me`)
3. Ensure port 443 is forwarded to your NAS in your router settings

**Configure the reverse proxy:**

1. **Control Panel** → **Login Portal** → **Advanced** → **Reverse Proxy** → **Create**
2. Source: Protocol `HTTPS`, Hostname your domain, Port `443`, enable **HSTS**
3. Destination: Protocol `HTTP`, Hostname `localhost`, Port `4010`
4. Save

**Update your env:**

```env
NEXTAUTH_URL=https://yourname.synology.me
```

Restart the stack:

```bash
cd /volume1/docker/otterfin
docker compose up -d      # DSM 7.2+
docker-compose up -d      # DSM 7.1
```

---

## Upgrading

SSH into your NAS, then pull the latest image and restart the stack:

```bash
cd /volume1/docker/otterfin

# DSM 7.2+
sudo docker compose pull && sudo docker compose up -d

# DSM 7.1
sudo docker-compose pull && sudo docker-compose up -d
```

Migrations run automatically on startup — allow 30–60 seconds after an upgrade.

!!! tip
    If you are already in the `docker` group you can omit `sudo`. Check with `groups` — if `docker` appears in the output, `sudo` is not required.

---

## Troubleshooting

**App fails to start / database errors** — the database container may still be initializing. Wait 30 seconds and check logs:

```bash
docker logs otterfin-app
docker logs otterfin-postgres
```

**Port 4010 already in use** — change `4010:3000` to another port in your Compose file and update `NEXTAUTH_URL` to match.

**Forgotten admin password** — connect directly to the database and reset it (using the credentials from your `.env`):

```bash
docker exec -it otterfin-postgres psql -U otterfin -d otterfin
```
