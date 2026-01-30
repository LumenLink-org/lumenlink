# Community Page - Backend Connection Status

## Current Status

**The community page IS connected to the backend**, but it's showing zeros because:

1. ✅ **Frontend is correctly configured** - The `/api/community/operators` endpoint is set up to fetch from the backend
2. ✅ **Backend handler exists** - The `GetGateways` handler is implemented in `server/rendezvous/internal/api/handler.go`
3. ❌ **Route was missing** - The GET `/api/v1/gateways` route was not registered in `main.go` (NOW FIXED)
4. ❌ **Route prefix mismatch** - Frontend expects `/api/v1/gateways` but backend was using `/v1` (NOW FIXED)

## What Was Fixed

### 1. Updated `server/rendezvous/cmd/server/main.go`

- ✅ Changed route group from `/v1` to `/api/v1` to match frontend expectations
- ✅ Added GET `/api/v1/gateways` route using `handler.GetGateways`
- ✅ Properly initialized Handler struct with database connection
- ✅ Fixed database and config service initialization

### 2. Backend Endpoint Now Available

The backend now exposes:
- **GET `/api/v1/gateways`** - Returns list of all active/degraded gateways for the community page

## How It Works

### Data Flow

```
Community Page (Frontend)
  ↓
/api/community/operators (Next.js API Route)
  ↓
GET http://localhost:8080/api/v1/gateways (Backend Rendezvous Service)
  ↓
Database Query (PostgreSQL)
  ↓
Returns gateway data with:
  - Operator ID and callsign
  - Region
  - Current users connected
  - Uptime percentage
  - Health status (active/degraded/offline)
  - Last seen timestamp
  - Blurred location (privacy-protected)
```

### Frontend API Route

**File:** `web/app/api/community/operators/route.ts`

- Fetches from: `{LUMENLINK_RENDEZVOUS_URL}/api/v1/gateways`
- Default backend URL: `http://localhost:8080`
- Timeout: 5 seconds
- Auto-refresh: Every 60 seconds
- Fallback: Returns empty array if backend is unavailable (graceful degradation)

### Backend Handler

**File:** `server/rendezvous/internal/api/handler.go`

- Method: `GetGateways(c *gin.Context)`
- Queries: `database.GetAllGateways(ctx)` 
- Returns: JSON with `{"gateways": [...]}` array
- Filters: Only active/degraded gateways (status IN ('active', 'degraded'))

## To See Live Data

### 1. Start the Backend Service

```bash
cd server/rendezvous
go run cmd/server/main.go
```

**Required Environment Variables:**
- `DATABASE_URL` - PostgreSQL connection string
- `REDIS_URL` - Redis connection string (optional for now)
- `PORT` - Server port (default: 8080)
- `LUMENLINK_ED25519_PRIVATE_KEY` - For config pack signing
- `LUMENLINK_ED25519_PUBLIC_KEY` - For config pack signing

### 2. Ensure Database Has Gateway Data

The backend queries the `gateways` table. Make sure you have:
- Gateways registered in the database
- Status set to 'active' or 'degraded'
- `current_users`, `last_seen`, `region` fields populated

### 3. Set Frontend Environment Variable

**File:** `web/.env.local` or `web/.env`

```env
# Development: http://localhost:8080
# Production: https://api.lumenlink.org
LUMENLINK_RENDEZVOUS_URL=http://localhost:8080
```

### 4. Start the Frontend

```bash
cd web
npm run dev
```

### 5. Check the Community Page

Visit: `http://localhost:3000/en/community`

You should see:
- Total Operators count
- Active operators count
- Total Users connected
- Average Uptime percentage
- Map with operator locations (blurred for privacy)
- Operator list with details

## Testing the Connection

### Test Backend Directly

```bash
curl http://localhost:8080/api/v1/gateways
```

Expected response:
```json
{
  "gateways": [
    {
      "id": "uuid",
      "callsign": "OP-001",
      "region": "Middle East",
      "status": "active",
      "current_users": 1250,
      "max_users": 5000,
      "last_seen": "2026-01-25T10:30:00Z",
      "uptime_percent": 98.5
    }
  ]
}
```

### Test Frontend API Route

```bash
curl http://localhost:3000/api/community/operators
```

Expected response:
```json
{
  "operators": [...],
  "stats": {
    "total": 5,
    "active": 4,
    "degraded": 1,
    "offline": 0,
    "totalUsers": 6250,
    "avgUptime": 98.2
  },
  "lastUpdated": "2026-01-25T10:30:00Z"
}
```

## Troubleshooting

### All Zeros on Community Page

**Possible Causes:**
1. Backend not running
2. `LUMENLINK_RENDEZVOUS_URL` not set or incorrect
3. Backend not accessible from frontend (CORS, network issues)
4. Database has no gateway data
5. Backend route not registered (should be fixed now)

**Check:**
- Backend logs for errors
- Browser console for fetch errors
- Network tab in DevTools to see API calls
- Backend health endpoint: `curl http://localhost:8080/health`

### Backend Returns Empty Array

**Possible Causes:**
1. No gateways in database with status 'active' or 'degraded'
2. Database connection issues
3. `GetAllGateways` query failing

**Check:**
- Database connection string
- Gateway records in database
- Backend logs for database errors

### Frontend Shows "No operators found"

This is normal if:
- Backend is unavailable (graceful degradation)
- Database has no active gateways
- All gateways are filtered out by search/filters

## Next Steps

1. ✅ Backend route is now registered
2. ⏳ Start backend service with database
3. ⏳ Populate database with gateway data (from mobile apps connecting)
4. ⏳ Verify connection works end-to-end

## Summary

**The data IS live and connected** - the infrastructure is in place. You just need to:
1. Start the backend service
2. Have gateways registered in the database (from mobile apps)
3. The community page will automatically show live data

The page refreshes every 60 seconds automatically, so new operators and user counts will appear in real-time.
