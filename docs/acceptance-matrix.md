# LumenLink Acceptance Matrix

Maps requirements and critical flows to automated checks. Run full verification with `./scripts/verify.sh`.

## Primary Flows

| Scenario / Requirement | Automated? | How to Run | Expected Result | Notes |
|------------------------|------------|------------|-----------------|-------|
| Config pack retrieval (primary flow) | Y | `./scripts/verify.sh` or `test scenarios/scripts/integration-relay-rendezvous.sh` | POST /api/v1/config returns config_pack | Happy path |
| Attestation challenge | Y | Same | GET /api/v1/attest/challenge returns challenge | iOS App Attest flow |
| Attestation verification | Y | Same | POST /api/v1/attest with token | Unit tests + integration |
| Gateway list (community page) | Y | Same | GET /api/v1/gateways returns gateways array | May be empty |
| Gateway status update | Y | `go test ./...` in backend/server/rendezvous | RecordGatewayStatus stores metrics | Unit tests |
| Discovery log | Y | Same | RecordDiscoveryLog inserts row | Unit tests |
| Health check | Y | Integration + smoke | GET /health returns healthy | All scripts |
| Metrics (Prometheus) | Y | Integration | GET /metrics returns lumenlink_* | Observability |

## Bootstrap & Data Layer

| Scenario / Requirement | Automated? | How to Run | Expected Result | Notes |
|------------------------|------------|------------|-----------------|-------|
| Fresh start: deps up, migrate, app start | Y | `scripts/smoke/smoke-all.sh` | Services healthy | Smoke A |
| Migrations run on empty DB | Y | Integration script | Migrations complete | Uses docker down -v |
| Migrations incremental (existing DB) | N | Manual | `go run cmd/migrate/main.go -command up` | Run twice, no errors |
| Seed data | N | N/A | No seed required | App works with empty gateways |

## Failure Modes

| Scenario / Requirement | Automated? | How to Run | Expected Result | Notes |
|------------------------|------------|------------|-----------------|-------|
| Invalid payload (empty required fields) | Y | Integration + smoke | 400 Bad Request | Smoke C |
| Invalid JSON body | Y | Smoke D | 400, no stack/panic leak | Observability |
| Rate limit exceeded | Y | Smoke E | 429 Too Many Requests | Burst 10, 15 rapid requests |
| DB down at startup | Y | `scripts/smoke/smoke-db-down.sh` | Server exits with clear error, no panic | Standalone script |
| Redis down at startup | N | Manual | Server exits (REDIS_URL required) | Not yet optional |

## Security & Auth

| Scenario / Requirement | Automated? | How to Run | Expected Result | Notes |
|------------------------|------------|------------|-----------------|-------|
| Attestation bypass disabled in production | Y | `go test ./...` | checkProductionAttestationGuard | GO_ENV=production |
| Config signing key required (no ephemeral) | N | Manual | Server fails without key | LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY=false |
| Error responses sanitized (no internals) | Y | Smoke D | No panic/stack/paths in response | Observability |

## Observability

| Scenario / Requirement | Automated? | How to Run | Expected Result | Notes |
|------------------------|------------|------------|-----------------|-------|
| Logs don't leak secrets | N | Manual review | No passwords/tokens in logs | Audit |
| Metrics endpoint exposed | Y | Integration | /metrics returns Prometheus format | |
| Health endpoint | Y | All scripts | /health returns healthy | |

## Deployment Gates (from DEPLOYMENT_GATES.md)

| Gate | Automated? | Coverage |
|------|------------|----------|
| A - Security and Cryptography | Partial | Attestation guard tested; config signing manual |
| B - Discovery and Transport | Partial | Discovery log API tested; transport fallback manual |
| C - Mobile Clients | N | Requires device testing |
| D - Backend Services | Y | Load test script exists; migrations reversible |
| E - Observability | Partial | Metrics + health automated; alerts manual |
| F - Release Compliance | N | Manual |

## Test Scripts Reference

| Script | Coverage |
|--------|----------|
| `./scripts/verify.sh` | Full: lint → unit → build → integration → smoke |
| `./scripts/check.sh` | Lint, build, unit tests (no Docker) |
| `test scenarios/scripts/integration-relay-rendezvous.sh` | Rendezvous + Relay, config, attestation, gateways, failure mode |
| `test scenarios/scripts/e2e-mobile-backend.sh` | Backend-only E2E (config, discovery log, attestation) |
| `scripts/smoke/smoke-all.sh` | Bootstrap, happy path, failure modes, observability, rate limit |
| `scripts/smoke/smoke-db-down.sh` | DB-down graceful failure (standalone) |
