# Rollback Runbook

## How to Roll Back Safely

### Docker Compose

```bash
# 1. Stop current deployment
docker compose -f docker-compose.prod.yml down

# 2. Checkout previous version
git checkout v1.2.3  # or previous tag/commit

# 3. Rebuild and start
docker compose -f docker-compose.prod.yml up -d --build
```

### Database Migrations

Migrations are **forward-only** by default. To roll back a migration:

```bash
cd backend/server/rendezvous
export DATABASE_URL="postgres://..."
go run cmd/migrate/main.go -command down
```

**Warning:** Rollback removes schema changes. Data in rolled-back tables may be lost. Test rollback on staging first.

### What Data/State Is Affected

| Component | State | Rollback Impact |
|-----------|-------|-----------------|
| Rendezvous API | Stateless | Restart with previous image |
| PostgreSQL | Persistent (volumes) | Migrations down may drop tables |
| Redis | Persistent (volumes) | Cache only; safe to restart |
| Web | Stateless | Restart with previous image |

**Best practice:** Before deploying, tag the current DB migration version. If rollback needed, run `migrate -command down` only if the new migration was applied.
