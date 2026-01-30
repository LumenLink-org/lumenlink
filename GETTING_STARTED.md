# Getting Started with LumenLink Development

**Welcome!** This guide will help you get started with LumenLink development.

---

## ✅ What's Been Completed

### Task 1.1: Monorepo Structure ✅

All foundational files and directory structure have been created:

**✅ Created:**
- Complete monorepo structure with all directories
- Rust core library skeleton with all modules
- Go rendezvous service structure
- Docker Compose configuration
- CI/CD pipeline foundation
- Documentation (README, CONTRIBUTING, LICENSE, SECURITY, etc.)
- Development scripts

**📁 Project Structure:**
```
lumenlink/
├── core/              ✅ Rust shared library (structure complete)
├── server/            ✅ Backend services (structure complete)
├── mobile/            📁 Ready for Android/iOS setup
├── web/               📁 Ready for Next.js setup
├── censorlab/         📁 Ready for Python ML testing
├── infra/             📁 Ready for Terraform/K8s
├── docs/              ✅ Architecture docs created
└── scripts/           ✅ Setup and test scripts
```

---

## 🚀 Next Steps

### 1. Install Prerequisites

**Required:**
- **Rust**: Install from https://rustup.rs/
  ```bash
  # Windows (PowerShell)
  Invoke-WebRequest https://win.rustup.rs/x86_64 -OutFile rustup-init.exe
  .\rustup-init.exe
  ```

- **Go**: Install from https://go.dev/dl/
  - Download Go 1.21+ installer for Windows
  - Add Go to PATH

- **Node.js**: Install from https://nodejs.org/
  - Download Node.js 20+ LTS
  - Includes npm

- **Docker Desktop**: Install from https://www.docker.com/products/docker-desktop/
  - Required for PostgreSQL and Redis

**Optional (for mobile development):**
- Android Studio (for Android)
- Xcode (for iOS, macOS only)

### 2. Set Up Development Environment

```bash
# Clone repository (if not already done)
git clone https://github.com/LumenLink-org/lumenlink.git
cd lumenlink

# Copy environment file
cp env.example .env
# Edit .env with your settings

# Start Docker services
docker-compose up -d

# Verify services are running
docker-compose ps
```

### 3. Build and Test

```bash
# Build Rust core
cd core
cargo build
cargo test

# Build Go server
cd ../server/rendezvous
go build ./cmd/server
go test ./...

# Run all tests
cd ../..
./scripts/test-all.sh
```

### 4. Start Development

**LumenLink is now in Beta.** All apps (Android, iOS, Desktop) are ready for community testing.

**For contributors:**
1. Check [GitHub Issues](https://github.com/LumenLink-org/lumenlink/issues) for good first issues
2. Review [CONTRIBUTING.md](./CONTRIBUTING.md) for contribution guidelines
3. See [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) for system design
4. Visit [Roadmap](https://lumenlink.org/en/roadmap) for development status

---

## 📚 Documentation

**Essential Reading:**
1. **[README.md](./README.md)** - Project overview
2. **[CONTRIBUTING.md](./CONTRIBUTING.md)** - How to contribute
3. **[docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)** - System design
4. **[HOW_TO_RUN.md](./HOW_TO_RUN.md)** - Setup, env vars, run commands
5. **[docs/release.md](./docs/release.md)** - Release process

**Reference:**
- **[docs/runbooks/](./docs/runbooks/)** - Deployment, rollback, operations
- **[docs/evidence.md](./docs/evidence.md)** - Verification commands
- **[SECURITY.md](./SECURITY.md)** - Security policy

---

## 🛠️ Development Workflow

### Daily Workflow

```bash
# 1. Pull latest changes
git pull upstream main

# 2. Create feature branch
git checkout -b feature/your-feature

# 3. Make changes
# ... edit files ...

# 4. Run tests
./scripts/test-all.sh

# 5. Commit
git commit -m "feat(core): your feature description"

# 6. Push and create PR
git push origin feature/your-feature
```

### Testing

```bash
# Run all tests
./scripts/test-all.sh

# Rust only
cd core && cargo test

# Go only
cd server/rendezvous && go test ./...

# TypeScript only (when implemented)
cd web && npm test
```

---

## 🐛 Troubleshooting

### Rust Not Found
```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# Or on Windows, download rustup-init.exe from rustup.rs
```

### Go Not Found
- Download from https://go.dev/dl/
- Add to PATH: `C:\Program Files\Go\bin`

### Docker Issues
- Make sure Docker Desktop is running
- Check: `docker ps`

### Port Conflicts
- Change ports in `docker-compose.yml` if 5432, 6379, or 8080 are in use

---

## 💡 Tips

1. **Start Small**: Pick a "good first issue" to get familiar
2. **Ask Questions**: Use GitHub Discussions or Matrix
3. **Read Code**: Explore existing modules to understand patterns
4. **Test Often**: Run tests after every change
5. **Security First**: Review security checklist before PR

---

## 🎯 Current Status

**✅ Beta Release — All Complete:**
- Monorepo structure
- Rust core library
- Go Rendezvous API
- Android app (Beta)
- iOS app (Beta)
- Desktop app (Beta)
- Web frontend with How-To Guide
- Docker configuration
- CI/CD pipeline
- Full documentation

**For users:** Download apps from [lumenlink.org/download](https://lumenlink.org/en/download). See [How-To Guide](https://lumenlink.org/en/how-to) for step-by-step instructions.

---

## 📞 Getting Help

- **GitHub Discussions**: General questions
- **Matrix**: `#lumenlink:matrix.org`
- **GitHub Issues**: Bug reports
- **Security**: security@lumenlink.org

---

**Ready to code? See [CONTRIBUTING.md](./CONTRIBUTING.md) and [GitHub Issues](https://github.com/LumenLink-org/lumenlink/issues).**
