# Operations Runbook

## Health Checks

| Endpoint | Service | Expected |
|----------|---------|----------|
| `GET /health` | Rendezvous | `{"status":"healthy"}` |
| `GET /metrics` | Rendezvous | Prometheus format |
| `GET /api/v1/attest/challenge` | Rendezvous | `{"challenge":"..."}` |

```bash
# Quick health check
curl -s http://localhost:8080/health

# Full health script
./scripts/docker-health-check.sh
```

## Logs / Metrics Locations

| Component | Logs | Metrics |
|-----------|------|---------|
| Rendezvous | stdout (docker logs) | `:8080/metrics` |
| Prometheus | - | `:9090` (dev) |
| Grafana | - | `:3000` (dev) |

```bash
# View rendezvous logs
docker compose logs -f rendezvous

# Prometheus config
infra/prometheus/prometheus.yml
```

## Common Failure Modes

### Rendezvous won't start

**Symptom:** `Failed to run migrations` or `failed to ping database`

**Diagnosis:**
```bash
# Check postgres is up
docker compose exec postgres pg_isready -U lumenlink

# Check DATABASE_URL
echo $DATABASE_URL
```

**Fix:** Ensure postgres is healthy, DATABASE_URL is correct, migrations run.

### Config pack returns 500

**Symptom:** `POST /api/v1/config` returns 500

**Diagnosis:** Check `LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY` is set (or `LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY=true` in dev).

### Rate limit 429

**Symptom:** Clients get 429 Too Many Requests

**Diagnosis:** Per-IP limit is 100 req/min, burst 10. Check for abuse or increase limit in code.

### DB connection pool exhausted

**Symptom:** `failed to query gateways` or connection errors

**Diagnosis:** Check `db.SetMaxOpenConns(25)` and connection leaks. Restart rendezvous to reset pool.
