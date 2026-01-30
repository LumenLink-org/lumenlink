# LumenLink Architecture
**System Design and Architecture Documentation**

---

## System Overview

LumenLink is a multi-layered censorship-resistant networking platform designed for maximum availability and stealth.

### Core Principles

1. **Multi-Transport Agility**: Multiple transport protocols attempt connections in parallel
2. **Unblockable Discovery**: Gateway information embedded in essential infrastructure signals
3. **High Performance**: eBPF/XDP kernel-level processing for 40Gbps+ throughput
4. **Community Resilience**: PERSIA Mode enables Starlink sharing during total blackouts
5. **Zero Logs**: Privacy-first architecture with no user data collection

---

## Architecture Layers

### 1. Client Layer

**Components:**
- **Android App** (Kotlin + Rust JNI)
- **iOS App** (Swift + Rust XCFramework)
- **Desktop Client** (Rust)

**Responsibilities:**
- User interface
- VPN/tunnel management
- Discovery channel scanning
- Transport selection and connection
- Security features (panic button, auto-wipe)

### 2. Core Library (Rust)

**Location:** `core/`

**Modules:**
- `transports/` - Transport protocols
- `discovery/` - Multi-channel discovery
- `crypto/` - Hybrid post-quantum cryptography
- `security/` - Burner identities, compromise detection
- `shaping/` - Traffic profiling and mimicry
- `mixnet/` - Sphinx/Nym integration

**Shared Between:**
- Mobile applications (via JNI/XCFramework)
- Server components
- Desktop clients

### 3. Transport Layer

**Protocols (in priority order):**

1. **XTLS-Reality** (Primary)
   - Mimics Apple/Microsoft TLS fingerprints
   - SNI spoofing
   - Fast connection (<100ms)

2. **MASQUE** (Secondary)
   - HTTP/3 UDP tunneling
   - Mimics Apple iCloud Private Relay
   - RFC 9298 compliant

3. **Parasite Protocol** (Fallback)
   - HTTP header smuggling
   - Hides data in ETag/If-None-Match headers
   - Works through CDNs

4. **SSH/Obfuscated-SSH** (Fallback)
   - Standard SSH tunneling
   - Obfuscated handshake
   - Compatibility with existing infrastructure

5. **Ghost Mesh** (Last Resort)
   - Wi-Fi Aware (Android)
   - UWB (iOS)
   - Local-only connectivity

### 4. Discovery System

**11 Unblockable Channels:**

1. **GPS/GNSS** - L1 C/A code manipulation
2. **FM RDS** - Radio Data System encoding
3. **Digital TV** - MPEG-TS steganography
4. **Power Line** - PLC smart meter encoding
5. **GSM Cell Broadcast** - Emergency channels
6. **LTE SIB** - System Information Blocks
7. **IoT/MQTT** - Public MQTT brokers
8. **Blockchain** - Ethereum/Bitcoin/Monero
9. **Satellite API** - Starlink/Iridium
10. **National Intranet** - Image/text steganography
11. **Social Media** - Post steganography

**Unified Scanner:**
- Parallel scanning across all channels
- FEC decoding (Reed-Solomon)
- Gateway deduplication
- Signature verification

### 5. Server Infrastructure

**Components:**

**Relay Servers (Rust + eBPF/XDP)**
- Location: `server/relay/`
- High-performance packet forwarding
- 40Gbps+ throughput
- DDoS protection
- Token verification in kernel

**Rendezvous Service (Go)**
- Location: `server/rendezvous/`
- Config pack distribution
- Remote attestation verification
- Geo-load balancing
- Incremental rollouts

**Discovery Broadcasters (Python)**
- Location: `server/discovery/`
- SDR integration (HackRF)
- PLC modem integration
- DTV signal generation

### 6. Database Layer

**PostgreSQL 15+ with TimescaleDB**

**Tables:**
- `gateways` - Gateway information
- `config_packs` - Signed configuration packs
- `attestations` - Device attestation records
- `operator_metrics` - Time-series metrics (TimescaleDB)
- `discovery_logs` - Discovery channel analytics

**Caching:**
- Redis for hot data
- Gateway info caching
- Config pack caching
- Rate limiting

---

## Data Flow

### Connection Establishment

```
1. Client starts
   ↓
2. Load signed config pack (from Rendezvous or Discovery)
   ↓
3. Verify attestation (Play Integrity / DCAppAttest)
   ↓
4. Start multi-channel discovery (parallel scan)
   ↓
5. Transport selector races all transports
   ↓
6. First successful transport wins
   ↓
7. Establish tunnel connection
   ↓
8. Route traffic through tunnel
```

### PERSIA Mode (Starlink Sharing)

```
1. Starlink operator enables PERSIA Mode
   ↓
2. Wi-Fi Aware publishes "Bridge Available"
   ↓
3. Neighbors discover bridge via Wi-Fi Aware
   ↓
4. Secure handshake (zero-knowledge proof)
   ↓
5. Neighbor requests → Bridge → Starlink → Internet
   ↓
6. Response → Starlink → Bridge → Neighbor
```

---

## Security Architecture

### Cryptography

**Hybrid Post-Quantum:**
- Key Exchange: X25519 + Kyber768
- Signatures: Dilithium3
- Key Derivation: Argon2id
- Randomness: System CSPRNG + Fortuna

### Identity Management

**Burner Identities:**
- WiFi MAC rotation (24h)
- BLE MAC rotation (24h)
- Encryption key rotation (24h)
- Automatic zeroization

### Compromise Detection

**Behavioral Analysis:**
- Network signature detection
- Timing pattern analysis
- App integrity checks
- Anomaly scoring

**Safety Protocol:**
- Key zeroization
- Storage wipe
- Decoy mode activation
- Emergency beacon

---

## Performance Targets

| Metric | Target | Status |
|--------|--------|--------|
| Relay Throughput | >40 Gbps | 🟡 In Development |
| Connection Time | <3s (p50) | 🟡 In Development |
| Discovery Success | >95% | 🟡 In Development |
| Battery Drain | <5%/hr | 🟡 In Development |
| CensorLab AUC | <0.55 | 🟡 In Development |

---

## Deployment Architecture

### Development
- Docker Compose
- Local PostgreSQL + Redis
- Hot reload for all services

### Production
- Kubernetes orchestration
- Bare metal relays (Hetzner/OVH)
- Geographic distribution
- Auto-scaling

---

## Monitoring & Observability

**Metrics:**
- Prometheus for metrics collection
- Grafana for visualization
- Custom dashboards for:
  - Relay performance
  - Gateway health
  - Discovery success rates
  - Security events

**Logging:**
- Structured logging (JSON)
- No sensitive data in logs
- Centralized log aggregation

---

## Future Enhancements

- [ ] Additional discovery channels
- [ ] Enhanced mixnet integration
- [ ] Machine learning for traffic optimization
- [ ] Advanced DDoS protection
- [ ] Global mesh network

---

**For detailed implementation, see:**
- [TASK_BREAKDOWN.md](../v1/TASK_BREAKDOWN.md)
- [DEVELOPER_ROADMAP.md](../v1/DEVELOPER_ROADMAP.md)
