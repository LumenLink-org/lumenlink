# How to Run & Install LumenLink

This guide covers prerequisites, environment configuration, Docker setup, build steps, and how to run the full stack (backend services, web, and mobile clients).

## 1) Prerequisites

Install these first:
- **Docker Desktop** (with docker-compose)
- **Rust** (latest stable)
- **Go** (latest stable)
- **Node.js** (20+)
- **Python 3.10+** (optional, for CensorLab)

Optional platform tools:
- **Android Studio** + SDK/NDK (for Android app)
- **Xcode** (for iOS app)

## 2) Environment Setup

Copy the example env file and set required variables:

```bash
cp env.example .env
```

### Environment Variables (from `env.example`)

**Database**
- `DB_NAME` – Database name.
- `DB_USER` – Database user.
- `DB_PASSWORD` – Database password.
- `DATABASE_URL` – Full PostgreSQL URL.

**Redis**
- `REDIS_URL` – Redis connection URL.
- `REDIS_PASSWORD` – Optional Redis password.

**Rendezvous Service**
- `RENDEZVOUS_PORT` – API port.
- `RENDEZVOUS_HOST` – API host binding.

**Relay Service**
- `RELAY_INTERFACE` – Network interface for XDP attach (e.g. `eth0`).
- `RELAY_PORT` – Relay listener port.

**Security**
- `JWT_SECRET` – Secret for JWT signing.
- `ATTESTATION_SECRET` – Secret for attestation workflows.

**Attestation (Production)**
- `PLAY_INTEGRITY_API_KEY` – Google Play Integrity API key.
- `APPLE_TEAM_ID` – Apple team ID for DCAppAttest.
- `APPLE_KEY_ID` – Apple key ID for DCAppAttest.
- `LUMENLINK_ALLOW_ATTESTATION_BYPASS` – **false in production**.

**Config Pack Signing**
- `LUMENLINK_CONFIG_SIGNING_PRIVATE_KEY` – Base64 Ed25519 private key (64 bytes).
- `LUMENLINK_CONFIG_SIGNING_PUBLIC_KEY` – Base64 Ed25519 public key (32 bytes, optional).
- `LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY` – **false in production**.

**Monitoring**
- `PROMETHEUS_PORT` – Prometheus port.
- `GRAFANA_PORT` – Grafana port.
- `GRAFANA_PASSWORD` – Grafana admin password.

**Development**
- `RUST_LOG` – Rust logging level.
- `GO_ENV` – Go env (e.g. `development`).
- `NODE_ENV` – Node env (e.g. `development`).

## 3) First-Run / Fresh Bootstrap

For a **new environment** (empty database), run these steps in order:

### 3a) Start dependencies (DB + Redis)

```bash
docker compose up -d postgres redis
```

Wait for services to be ready (or use `./scripts/docker-setup.sh` which does this):

```bash
# Wait for PostgreSQL
until docker compose exec -T postgres pg_isready -U lumenlink 2>/dev/null; do sleep 1; done

# Wait for Redis
until docker compose exec -T redis redis-cli ping 2>/dev/null; do sleep 1; done
```

### 3b) Apply migrations

```bash
export DATABASE_URL="postgres://lumenlink:dev_password_change_me@localhost:5432/lumenlink_dev?sslmode=disable"
cd backend/server/rendezvous
go run cmd/migrate/main.go -command up
cd ../..
```

Or with custom `DATABASE_URL` from `.env`:

```bash
source .env  # or export DATABASE_URL=...
cd backend/server/rendezvous && go run cmd/migrate/main.go -command up
```

### 3c) Run the app

```bash
export DATABASE_URL="postgres://lumenlink:dev_password_change_me@localhost:5432/lumenlink_dev?sslmode=disable"
export REDIS_URL="redis://localhost:6379"
export LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY=true   # dev only
cd backend/server/rendezvous
go run cmd/server/main.go
```

**Seed data:** No seed data is required. The app runs with an empty `gateways` table; config packs are generated on demand. To add gateways, use the gateway operator registration flow or insert test rows manually.

---

## 4) Docker (Databases + Infra)

Start core services (includes migrations):

```bash
./scripts/docker-setup.sh
```

Check health:

```bash
./scripts/docker-health-check.sh
```

This brings up:
- PostgreSQL (TimescaleDB)
- Redis
- Prometheus
- Grafana

## 5) Backend Services (Rendezvous + Relay)

### Rendezvous (Go)

From repo root:

```bash
cd backend/server/rendezvous
go test ./...
go run cmd/server/main.go
```

### Relay (Rust + eBPF)

From repo root:

```bash
cd server/relay
cargo build
```

To build the XDP program (Linux):

```bash
./scripts/build-ebpf.sh
```

To build XDP program in WSL (Windows):

```powershell
.\scripts\build-ebpf.ps1
```

> The relay requires Linux kernel headers and CAP_BPF/CAP_NET_ADMIN. Run on Linux for real XDP attach.

## 6) Core Library (Rust)

From repo root:

```bash
cd core
cargo build
cargo test
```

## 7) Web (Next.js)

From repo root:

```bash
cd web
npm install
npm run dev
```

Production build:

```bash
npm run build
npm start
```

## 7) Android App

Use **Android Studio**:
- Open `mobile/android`.
- Sync Gradle.
- Ensure NDK is installed (for JNI).
- Build and run on device/emulator.

The Android app loads the Rust core via JNI. Ensure the Rust library is built and packaged by the Android build system.

## 9) iOS App

Use **Xcode**:
- Open `mobile/ios`.
- Configure signing & provisioning.
- Build and run on device/simulator.

The iOS app expects a Rust XCFramework; ensure it is built and linked in the project.

## 10) Running All Tests

From repo root:

```bash
# Full verification (lint, unit, build, integration, e2e)
./scripts/verify.sh
# or: make verify

# Quick check (lint, build, unit - no Docker)
./scripts/check.sh

# All unit tests
./scripts/test-all.sh
```

## 11) Common Troubleshooting

- **Docker not running**: Start Docker Desktop.
- **Missing env vars**: Set required values in `.env`.
- **Relay XDP errors**: Must run on Linux with kernel headers.
- **Mobile build issues**: Ensure SDK/NDK (Android) or Xcode (iOS).

## 12) Production Notes

- Do **not** use bypass flags in production:
  - `LUMENLINK_ALLOW_ATTESTATION_BYPASS=false`
  - `LUMENLINK_ALLOW_EPHEMERAL_SIGNING_KEY=false`
- Provide valid Play Integrity and DCAppAttest credentials.
- Provide a real Ed25519 signing key for config packs.
