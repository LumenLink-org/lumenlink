# Evidence Package — Production Readiness

Quick proof of readiness: copy/paste commands and coverage summary.

## Verification Commands (copy/paste)

```bash
# Full verification (lint, unit, build, integration, e2e)
./scripts/verify.sh

# Quick check (no Docker)
./scripts/check.sh

# Integration only
bash "test scenarios/scripts/integration-relay-rendezvous.sh"

# Smoke only
bash scripts/smoke/smoke-all.sh

# DB-down resilience
bash scripts/smoke/smoke-db-down.sh
```

## What Tests Exist and What They Cover

| Test | Command | Coverage |
|------|---------|----------|
| Lint | `verify.sh` step 1 | Rust fmt/clippy, Go gofmt/vet, Web lint |
| Unit | `verify.sh` step 2 | Rust core, Go rendezvous, Web |
| Build | `verify.sh` step 3 | Core, relay, rendezvous, web |
| Integration | `integration-relay-rendezvous.sh` | Rendezvous + Relay, config, attestation, gateways, failure mode |
| Smoke | `smoke-all.sh` | Bootstrap, happy path, invalid payload, observability, rate limit |
| DB-down | `smoke-db-down.sh` | Graceful failure when DB unavailable |

## Security Checks and Where They Run

| Check | Where | When |
|-------|-------|------|
| cargo audit | CI (security job) | Push/PR |
| cargo deny | CI (security job) | Push/PR |
| Nancy sleuth (Go) | CI (security job) | Push/PR |
| TruffleHog (secrets) | CI (security job) | Push/PR |
| Hardcoded secrets grep | CI (security job) | Push/PR |

See [.github/workflows/security.yml](../.github/workflows/security.yml).

## Red Flag Fixes Applied (Production Gate)

| Issue | Fix |
|-------|-----|
| Hardcoded secrets in backend/.env | Replaced with safe placeholders; file gitignored |
| Gin debug mode in production | gin.SetMode(gin.ReleaseMode) when GO_ENV=production |
| Missing config signing key in prod | Fail fast if LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY empty in prod |
| Ephemeral signing in prod | Fail fast if LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY=true in prod |

## Remaining Known Limitations

| Limitation | Mitigation |
|------------|------------|
| Redis required but not used | REDIS_URL must be set; consider making optional |
| Relay build may fail (eBPF) | verify.sh skips relay build on failure |
| Mobile device testing | Manual; requires physical device |
| Load testing | `load-test-rendezvous.sh` run manually |
| Rate limit test may not trigger 429 | Burst 10; smoke logs warning if not triggered |
