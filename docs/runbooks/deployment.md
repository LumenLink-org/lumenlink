# Deployment Runbook

## How to Deploy

### Docker Compose (recommended)

From repo root:

```bash
# 1. Copy and configure env
cp env.example .env
# Edit .env with production values (see Required Env Vars below)

# 2. Build and start
docker compose -f docker-compose.prod.yml up -d --build

# 3. Migrations run automatically when rendezvous starts (see main.go).
# For manual migration (e.g. before first deploy):
# cd backend/server/rendezvous && export DATABASE_URL="..." && go run cmd/migrate/main.go -command up
```

### Host / bare metal

```bash
# 1. Build rendezvous
cd backend/server/rendezvous
go build -o rendezvous ./cmd/server

# 2. Run migrations
export DATABASE_URL="..."
go run cmd/migrate/main.go -command up

# 3. Start rendezvous
./rendezvous
```

### Config Profiles

| Profile | GO_ENV | LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY | LUMENLINK_ALLOW_ATTESTATION_BYPASS |
|---------|--------|--------------------------------------|------------------------------------|
| dev | development | true | true (dev only) |
| staging | staging | false | false |
| prod | production | false | false |

## Required Env Vars / Secrets

| Var | Required | Notes |
|-----|-----------|-------|
| DATABASE_URL | Yes | PostgreSQL connection string |
| REDIS_URL | Yes | Redis connection string |
| LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY | Prod | Base64 Ed25519 (64 bytes) |
| LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY | Prod | Must be false |
| LUMENLINK_ALLOW_ATTESTATION_BYPASS | Prod | Must be false |
| JWT_SECRET | Yes | JWT signing |
| ATTESTATION_SECRET | Yes | Attestation workflows |
| DB_PASSWORD | Yes | PostgreSQL password |
| REDIS_PASSWORD | Prod | Redis password (prod) |
| GRAFANA_PASSWORD | Optional | Grafana admin |

Put secrets in `.env` (never commit). Use a secrets manager in production.
