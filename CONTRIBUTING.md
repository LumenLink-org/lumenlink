# Contributing to LumenLink

Thank you for your interest in contributing to LumenLink! This guide will help you get started.

## 📋 Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [Development Workflow](#development-workflow)
4. [Code Standards](#code-standards)
5. [Testing](#testing)
6. [Security](#security)
7. [Documentation](#documentation)
8. [Translation](#translation)

---

## Code of Conduct

We follow the [Contributor Covenant Code of Conduct](https://www.contributor-covenant.org/). Please read it before contributing.

**Key Points:**
- Be respectful and inclusive
- Focus on what's best for the community
- Your safety comes first (use pseudonyms if needed)

---

## Getting Started

### Prerequisites

- **Rust**: 1.70+ (`rustup install stable`)
- **Go**: 1.21+ 
- **Node.js**: 20+
- **Docker**: Latest
- **PostgreSQL**: 15+ (or use Docker)
- **Git**: Latest

### First Time Setup

```bash
# Fork and clone
git clone https://github.com/YOUR_USERNAME/lumenlink.git
cd lumenlink
git remote add upstream https://github.com/LumenLink-org/lumenlink.git

# Run setup script
./scripts/setup-dev.sh

# Verify setup
./scripts/test-all.sh
```

See [GETTING_STARTED.md](./GETTING_STARTED.md) for detailed setup instructions.

---

## Development Workflow

### 1. Find a Task

- Look for issues labeled `good-first-issue`
- Check [TASK_BREAKDOWN.md](./v1/TASK_BREAKDOWN.md) for available tasks
- Comment on the issue to claim it

### 2. Create Feature Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

### 3. Make Changes

- Write code following style guides
- Add tests for new functionality
- Update documentation
- Consider security implications

### 4. Run Checks

```bash
# Rust
cd core && cargo fmt && cargo clippy && cargo test

# Go
cd server/rendezvous && go fmt ./... && go test ./...

# TypeScript
cd web && npm run lint && npm test
```

### 5. Commit Changes

Use [Conventional Commits](https://www.conventionalcommits.org/):

```bash
git commit -m "feat(core): add GPS discovery channel"
git commit -m "fix(android): crash in PERSIA mode"
git commit -m "docs: add Farsi installation guide"
```

**Commit Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `test`: Tests
- `refactor`: Code refactoring
- `perf`: Performance improvement
- `chore`: Maintenance

### 6. Push & Create PR

```bash
git push origin feature/your-feature-name
# Then create PR on GitHub
```

**PR Requirements:**
- [ ] All tests pass
- [ ] Code follows style guide
- [ ] Documentation updated
- [ ] Security checklist reviewed
- [ ] CensorLab validation passes (for transport changes)

---

## Code Standards

### Rust

- Use `rustfmt` (automatic formatting)
- Follow Rust API guidelines
- Use `clippy` for linting
- Document public APIs with `///`

```rust
/// Establishes a MASQUE connection to the server.
///
/// # Arguments
/// * `server_addr` - Server address
/// * `server_name` - SNI server name
///
/// # Returns
/// A MASQUE connection or error
pub async fn connect(server_addr: SocketAddr, server_name: String) -> Result<MasqueConnection> {
    // Implementation
}
```

### Go

- Use `gofmt` (automatic formatting)
- Follow Go style guide
- Use `golint` for linting
- Document exported functions

### TypeScript

- Use Prettier (automatic formatting)
- Follow ESLint rules
- Use TypeScript strict mode
- Document with JSDoc

---

## Testing

### Unit Tests

- **Rust**: `#[test]` functions, aim for >80% coverage
- **Go**: `*_test.go` files, aim for >80% coverage
- **TypeScript**: Jest tests, aim for >80% coverage

### Integration Tests

- Test complete flows (client → server → exit)
- Use Docker for isolated testing
- Mock external services

### CensorLab Validation

All transport changes must pass CensorLab:
```bash
python censorlab/validate.py --profile zoom --threshold 0.55
```

---

## Security

**Before submitting PR, review [SECURITY.md](./SECURITY.md)**

Key points:
- No hardcoded secrets
- All network traffic encrypted
- Memory zeroization for sensitive data
- Input validation and sanitization
- Security review required for crypto/network changes

---

## Documentation

- Document all public APIs
- Add examples for complex functions
- Update user guides if behavior changes
- Translate to priority languages (Farsi, Arabic, etc.)

---

## Translation

Help translate LumenLink to your language:

1. Find translation files: `web/public/locales/`
2. Copy English as template: `cp -r en your-lang-code`
3. Translate all `.json` files
4. Test: `npm run dev` and visit `http://localhost:3000/your-lang-code`

---

## Getting Help

- **GitHub Discussions**: General questions
- **Matrix**: `#lumenlink:matrix.org` (real-time chat)
- **GitHub Issues**: Bug reports, feature requests
- **Security Email**: security@lumenlink.org (encrypted)

**Office Hours:**
- Tuesdays 18:00 UTC: Video call for questions
- Sundays 14:00 UTC: Community sync meeting

---

## Recognition

Contributors are recognized in:
- README.md (after 3+ contributions)
- Annual report (anonymized if requested)
- Conference talks (if interested)

**Badges:**
- 🌱 First Contribution
- 🌟 10 Contributions
- ⭐ 50 Contributions
- 🏆 100 Contributions

---

## Special Notes for High-Risk Regions

### Safety First

- **Never** use your real name or location
- Use Tor/VPN when contributing
- Consider using a throwaway GitHub account
- Your safety is more important than any contribution

### Anonymous Contributions

1. Create GitHub account via Tor
2. Use ProtonMail for email
3. TSC will review and commit on your behalf
4. You'll be credited as "Anonymous Contributor"

---

## Next Steps

1. ✅ Read this guide
2. ✅ Set up development environment
3. ✅ Find a "good first issue"
4. ✅ Ask questions if stuck
5. ✅ Submit your first PR!

**Welcome to the LumenLink community! 🌟**
