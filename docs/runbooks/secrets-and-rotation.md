# Secrets and Rotation Runbook

## What Secrets Exist

| Secret | Used By | Format |
|--------|---------|--------|
| DATABASE_URL | Rendezvous, migrations | `postgres://user:pass@host:5432/db?sslmode=disable` |
| REDIS_URL | Rendezvous | `redis://[:password@]host:6379` |
| LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY | Config pack signing | Base64 Ed25519 (64 bytes) |
| JWT_SECRET | JWT signing | Arbitrary string |
| ATTESTATION_SECRET | Attestation workflows | Arbitrary string |
| DB_PASSWORD | PostgreSQL | Plain text |
| REDIS_PASSWORD | Redis (prod) | Plain text |
| GRAFANA_PASSWORD | Grafana | Plain text |
| PLAY_INTEGRITY_API_KEY | Android attestation | Google API key |
| APPLE_TEAM_ID, APPLE_KEY_ID | iOS attestation | Apple credentials |

## How to Rotate

### Config signing key

1. Generate new Ed25519 key pair.
2. Set `LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY` and `LUMENLINK_CONFIG_SIGNING_PUBLIC_KEY` in .env.
3. Restart rendezvous.
4. **Impact:** Old config packs (signed with previous key) will fail verification on clients. Clients must fetch new config.

### Database password

1. Change password in PostgreSQL.
2. Update `DATABASE_URL` and `DB_PASSWORD` in .env.
3. Restart rendezvous and any service using DB.

### Redis password

1. Update Redis `requirepass`.
2. Update `REDIS_URL` with new password.
3. Restart rendezvous.

### JWT_SECRET / ATTESTATION_SECRET

1. Generate new secret.
2. Update .env.
3. Restart rendezvous.
4. **Impact:** Existing JWT tokens invalidated; users must re-auth.

## What Breaks If Not Rotated

| Secret | Risk if leaked | Rotation urgency |
|--------|----------------|------------------|
| LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY | Attacker can forge config packs | High |
| DATABASE_URL | Full DB access | Critical |
| JWT_SECRET | Token forgery | High |
| REDIS_PASSWORD | Cache manipulation | Medium |
