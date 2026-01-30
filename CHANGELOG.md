# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- E2E verification layer (verify.sh, smoke tests)
- Production runbooks (deployment, rollback, operations, secrets)
- Acceptance matrix and evidence package

### Changed
- README: Quick start, prereqs, env vars, verification commands
- docker-compose.prod.yml: Fixed paths for repo root

## [0.1.0] - 2026-01-27

### Added
- Initial LumenLink monorepo
- Rust core library (crypto, discovery, transports)
- Go Rendezvous API (config pack, attestation, gateways)
- Rust relay (eBPF/XDP)
- Next.js web frontend
- Android and iOS app structure
- Docker Compose (dev and prod)
- Database migrations (PostgreSQL/TimescaleDB)
