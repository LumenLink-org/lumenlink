# Docker Development Environment

Complete guide for setting up and using the LumenLink Docker development environment.

---

## Quick Start

```bash
# Setup and start all services
./scripts/docker-setup.sh

# Or manually:
docker-compose up -d
```

---

## Services

### PostgreSQL with TimescaleDB
- **Port:** 5432
- **Database:** `lumenlink_dev`
- **User:** `lumenlink`
- **Password:** Set in `.env` file
- **Image:** `timescale/timescaledb:latest-pg15`

**Connect:**
```bash
docker-compose exec postgres psql -U lumenlink -d lumenlink_dev
```

### Redis
- **Port:** 6379
- **Image:** `redis:7-alpine`
- **Persistence:** AOF enabled

**Connect:**
```bash
docker-compose exec redis redis-cli
```

### Rendezvous Service (Go)
- **Port:** 8080
- **Hot Reload:** Enabled with Air
- **Health Check:** `http://localhost:8080/health` (dev) / [https://api.lumenlink.org/health](https://api.lumenlink.org/health) (production)

**View Logs:**
```bash
docker-compose logs -f rendezvous
```

### Prometheus
- **Port:** 9090
- **URL:** http://localhost:9090 (dev only; internal in production)
- **Config:** `infra/prometheus/prometheus.yml`

### Grafana
- **Port:** 3000
- **URL:** http://localhost:3000 (dev) / [https://lumenlink.org](https://lumenlink.org) (production)
- **Username:** `admin`
- **Password:** Set in `.env` (default: `admin`)

---

## Common Commands

### Start Services
```bash
# Start all services
docker-compose up -d

# Start specific service
docker-compose up -d postgres
```

### Stop Services
```bash
# Stop all services
docker-compose down

# Stop and remove volumes (⚠️ deletes data)
docker-compose down -v
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f rendezvous
docker-compose logs -f postgres
```

### Restart Services
```bash
# Restart all
docker-compose restart

# Restart specific service
docker-compose restart rendezvous
```

### Check Status
```bash
# List running services
docker-compose ps

# Check health
./scripts/docker-health-check.sh
```

### Execute Commands
```bash
# Run command in service
docker-compose exec rendezvous sh
docker-compose exec postgres psql -U lumenlink -d lumenlink_dev

# Run one-off command
docker-compose run --rm rendezvous go test ./...
```

---

## Database Migrations

Migrations run automatically when the rendezvous service starts. To run manually:

```bash
# Inside rendezvous container
docker-compose exec rendezvous sh
cd /app
go run cmd/migrate/main.go -command up

# Or from host (if Go is installed)
cd server/rendezvous
export DATABASE_URL="postgres://lumenlink:dev_password_change_me@localhost:5432/lumenlink_dev?sslmode=disable"
go run cmd/migrate/main.go -command up
```

---

## Environment Variables

Create a `.env` file in the project root:

```bash
# Copy example
cp env.example .env

# Edit with your settings
nano .env
```

**Key Variables:**
- `DB_NAME` - PostgreSQL database name
- `DB_USER` - PostgreSQL username
- `DB_PASSWORD` - PostgreSQL password
- `DATABASE_URL` - Full PostgreSQL connection string
- `REDIS_URL` - Redis connection string
- `GRAFANA_PASSWORD` - Grafana admin password

---

## Volumes & Data Persistence

Data is persisted in Docker volumes:

- `postgres_data` - PostgreSQL data
- `redis_data` - Redis data
- `prometheus_data` - Prometheus metrics
- `grafana_data` - Grafana dashboards and config

**Backup PostgreSQL:**
```bash
docker-compose exec postgres pg_dump -U lumenlink lumenlink_dev > backup.sql
```

**Restore PostgreSQL:**
```bash
docker-compose exec -T postgres psql -U lumenlink lumenlink_dev < backup.sql
```

---

## Troubleshooting

### Services Won't Start

1. **Check Docker is running:**
   ```bash
   docker info
   ```

2. **Check port conflicts:**
   ```bash
   # Windows
   netstat -ano | findstr :5432
   
   # Linux/Mac
   lsof -i :5432
   ```

3. **View service logs:**
   ```bash
   docker-compose logs postgres
   docker-compose logs redis
   ```

### Database Connection Issues

1. **Verify PostgreSQL is healthy:**
   ```bash
   docker-compose exec postgres pg_isready -U lumenlink
   ```

2. **Check connection string:**
   ```bash
   echo $DATABASE_URL
   ```

3. **Test connection:**
   ```bash
   docker-compose exec postgres psql -U lumenlink -d lumenlink_dev -c "SELECT 1;"
   ```

### Migration Issues

1. **Check migration version:**
   ```bash
   cd server/rendezvous
   go run cmd/migrate/main.go -command version
   ```

2. **Reset database (⚠️ deletes all data):**
   ```bash
   docker-compose down -v
   docker-compose up -d postgres
   # Wait for postgres to be ready
   cd server/rendezvous
   go run cmd/migrate/main.go -command up
   ```

### Hot Reload Not Working

1. **Check Air is installed:**
   ```bash
   docker-compose exec rendezvous air -v
   ```

2. **Check .air.toml exists:**
   ```bash
   ls server/rendezvous/.air.toml
   ```

3. **Restart service:**
   ```bash
   docker-compose restart rendezvous
   ```

---

## Development Workflow

### 1. Start Environment
```bash
./scripts/docker-setup.sh
```

### 2. Make Code Changes
Edit files in `server/rendezvous/` - Air will automatically rebuild and restart.

### 3. View Logs
```bash
docker-compose logs -f rendezvous
```

### 4. Run Tests
```bash
# Inside container
docker-compose exec rendezvous go test ./...

# Or from host
cd server/rendezvous
go test ./...
```

### 5. Stop When Done
```bash
docker-compose down
```

---

## Production Considerations

⚠️ **This Docker setup is for DEVELOPMENT ONLY**

For production:
- Use separate `docker-compose.prod.yml`
- Set strong passwords
- Enable SSL/TLS
- Use secrets management
- Configure resource limits
- Set up monitoring and alerts
- Use production-grade images
- Enable backup automation

---

## Additional Resources

- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [TimescaleDB Documentation](https://docs.timescale.com/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)

---

**Need Help?** Check logs first: `docker-compose logs -f`
