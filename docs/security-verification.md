# LumenLink Security Verification

This document describes security hardening applied to the LumenLink stack and how to verify it.

## What Was Changed

### 1. Secrets & Credentials
- **No hardcoded secrets in code.** All credentials come from environment variables.
- **`.env` is in `.gitignore`** (root and backend). Never commit `.env` files.
- **Production defaults:** `docker-compose.prod.yml` uses `${VAR:-change_me}` placeholders. Set real values in `.env` before production deploy.
- **Attestation bypass guard:** Server fails fast if `LUMENLINK_ALLOW_ATTESTATION_BYPASS=true` when `GO_ENV=production`.

### 2. Public Exposure & Bindings
- **Production (`docker-compose.prod.yml`):**
  - Postgres: no host port (internal only)
  - Redis: no host port (internal only)
  - Rendezvous: `127.0.0.1:8080` (Nginx reverse proxy only)
  - Web: `127.0.0.1:3000` (Nginx reverse proxy only)
  - Grafana: `127.0.0.1:3001` (internal/admin only)
- **Development (`docker-compose.yml`):** Ports exposed for local dev. Do not use in production.

### 3. CORS, Headers, Rate Limiting
- **CORS:** Configurable via `CORS_ALLOWED_ORIGINS`. Production default: `https://lumenlink.org,https://www.lumenlink.org`. Dev default: localhost.
- **Security headers:** X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, Referrer-Policy on all responses.
- **Rate limiting:** API routes (`/api/v1/*`) limited to 100 req/min per client IP (burst 10). Health and metrics excluded.

### 4. Redis Hardening
- **`backend/redis.conf`:** `protected-mode yes`, `appendonly yes`, optional `requirepass` via env.
- **Production:** Use `REDIS_PASSWORD` in `.env`. Compose passes it to Redis via `--requirepass`.

## How to Verify Locally

```bash
# 1. Backend build and test
cd backend/server/rendezvous
go mod tidy
go build ./...
go test ./...

# 2. Start dev stack (ports exposed for local use)
docker-compose up -d

# 3. Health check
curl http://localhost:8080/health

# 4. CORS (from browser or curl with Origin header)
curl -H "Origin: http://localhost:3000" -I http://localhost:8080/api/v1/gateways

# 5. Rate limit (hammer the endpoint)
for i in $(seq 1 150); do curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/api/v1/gateways; done
# Expect 429 after ~100 requests
```

## Production Safety Checks

| Check | Command / Action |
|-------|------------------|
| **Postgres not public** | `ss -tulpn \| grep 5432` → should show nothing or 127.0.0.1 only |
| **Redis not public** | `ss -tulpn \| grep 6379` → should show nothing or 127.0.0.1 only |
| **Rendezvous behind Nginx** | `curl https://api.lumenlink.org/health` → 200 OK |
| **CORS strict** | Browser from non-lumenlink.org origin → CORS error |
| **Secrets not in logs** | `docker logs lumenlink-rendezvous` → no passwords/tokens |
| **UFW / firewall** | Ports 22, 80, 443 only from internet; 5432, 6379, 8080 blocked |

## Remaining Known Risks

1. **Metrics endpoint (`/metrics`):** Exposed on Rendezvous. In production, Nginx should not proxy `/metrics` publicly, or protect with auth.
2. **Grafana:** Bound to 127.0.0.1 in prod. Access via SSH tunnel or Nginx with auth.
3. **Ephemeral signing key:** `LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY` must be `false` in production. Provide real Ed25519 keys.

## Mitigation

- Add Nginx config to block `/metrics` from public or require auth.
- Document Grafana access via `ssh -L 3001:127.0.0.1:3001 user@server`.
- Add startup check for `LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY` in production (similar to attestation bypass guard).
