# Release Process

## How to Cut a Release

### 1. Tag and push

```bash
# Bump version (semver: vMAJOR.MINOR.PATCH)
git tag v1.2.3
git push origin v1.2.3
```

### 2. CI / Release workflow

Pushing a tag matching `v*.*.*` triggers [.github/workflows/release.yml](../.github/workflows/release.yml):

- Builds rendezvous binary (`backend/server/rendezvous`)
- Builds Rust core library
- Uploads artifacts to GitHub Release
- Creates GitHub Release (draft: false)

### 3. Version bumps

Update version in:

| File | Location |
|------|----------|
| core | `core/Cargo.toml` |
| web | `web/package.json` |
| Android | `mobile/android/build.gradle.kts` |
| iOS | `mobile/ios/` Info.plist |

### 4. CHANGELOG

Add entry to `CHANGELOG.md` before tagging:

```markdown
## [1.2.3] - 2026-01-27
### Added
- Feature X
### Fixed
- Bug Y
```

## CI Participation

| Workflow | Triggers | What it does |
|----------|----------|--------------|
| ci.yml | push/PR to main, develop | Rust tests, Go tests, migrations, Docker build, integration, TypeScript, security scan |
| release.yml | push tag v*.*.* | Builds artifacts, creates GitHub Release |
| security.yml | schedule, push | Security scans |

CI validates: build, test, migrations up/down, integration (Rendezvous + Relay), Docker build.
