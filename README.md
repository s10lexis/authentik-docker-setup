# Authentik Docker Deployment (Production Ready)

This setup provides a **production-ready Docker Compose deployment** for [Authentik](https://goauthentik.io/), including:

- PostgreSQL for persistent storage
- Redis for caching and background tasks
- Authentik core and worker services
- NGINX as a reverse proxy
- SMTP email support for password resets and user verification

---
## 📁 Project Structure

authentik/
├── .env                  # All environment variables and secrets
├── docker-compose.yml   # Compose file to launch all services
├── nginx.conf           # NGINX reverse proxy configuration
├── admin-access.md      # Instructions to create admin user
├── README.md            # This documentation

## 🗂 Files Included

- `README.md` – This documentation
- `admin-access.md` – Instructions to bootstrap an admin user
- `docker-compose.yml` – The main Compose file for all services
- `.env` – Configuration file with all environment variables
- `nginx.conf` – Templated NGINX reverse proxy configuration
- `certs` – Folder for optional TLS certificates (if enabling HTTPS)
- `media` – Persistent storage for uploaded files
- `custom-templates` – Optional Authentik UI templates

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://git.edusuc.net/WEBFORX/infrastructure-tools.git
cd infrastructure-tools
git checkout -b authentik-setup
cd authentik
```

---

### 2. Configure `.env`

Create or edit the `.env` file with the following:

```dotenv
# Domain
DOMAIN_NAME=domain_name

# Database
PG_USER=authentik
PG_PASS=[VAULT_INJECTED_SECRET]
PG_DB=authentik
PG_PORT=5432

# Authentik
AUTHENTIK_SECRET_KEY=[VAULT_INJECTED_SECRET]
AUTHENTIK_TAG=2025.4.1

# Ports
COMPOSE_PORT_HTTP=9000
COMPOSE_PORT_HTTPS=9443
NGINX_PORT_HTTP=80
NGINX_PORT_HTTPS=443

# SMTP
SMTP_HOST=[VAULT_INJECTED_SECRET]
SMTP_PORT=[VAULT_INJECTED_SECRET]
SMTP_USER=[VAULT_INJECTED_SECRET]
SMTP_PASS=[VAULT_INJECTED_SECRET]
SMTP_TLS=true
SMTP_FROM="Forgejo <connect-dev@linkafrica.org>"

# Healthcheck Timing
HEALTHCHECK_START_PERIOD=20s
HEALTHCHECK_INTERVAL=30s
HEALTHCHECK_RETRIES=5
HEALTHCHECK_TIMEOUT=3s
POSTGRES_HEALTHCHECK_TIMEOUT=5s

# Healthcheck Command
POSTGRES_HEALTHCHECK_CMD=["CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"]
```

> ⚠️ Replace all `[VAULT_INJECTED_SECRET]` placeholders with actual secure values before deploying.

---

### 3. Start the Services

```bash
docker-compose up -d
```

This will:

- Launch PostgreSQL, Redis, Authentik, and NGINX
- Configure health checks and volumes
- Apply restart policies (`unless-stopped`)

---

### 4. Access the Authentik UI

Once the stack is running:

- Visit: `http://<DOMAIN_NAME>`  
- Or use: `http://localhost:9000` (if not behind DNS)

Refer to `admin-access.md` to create your first admin user if not already initialized.

---

## 📦 Service Overview

| Service     | Description                       | Ports     |
|-------------|-----------------------------------|-----------|
| PostgreSQL  | Authentik’s database              | Internal  |
| Redis       | Background jobs and caching       | Internal  |
| Authentik   | Identity management service       | 9000/9443 |
| NGINX       | Reverse proxy for public access   | 80/443    |

---

## 🔧 SMTP Configuration

Auth emails (reset, confirm, invite) will use the SMTP credentials provided in `.env`.

Ensure your SMTP provider (e.g., Mailgun) allows relaying from the configured `SMTP_FROM` address.

---

## ♻️ Health Check Behavior

Services include basic health checks:

- PostgreSQL uses `pg_isready`
- Redis uses `redis-cli ping`
- Timings controlled through `.env` variables

---

## ✅ Summary

- **Everything is configured using `.env`**
- **Reverse proxy (NGINX) is included**
- **You can enable TLS by editing `nginx.conf` and mounting certificates**
- **All secrets must be manually set (no Vault or injection is enabled by default)**

---

Maintained by **Webforx Technology**  
Version: 10.0

