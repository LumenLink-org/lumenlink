# 🔆 LumenLink - Light That Cannot Be Extinguished

**LumenLink** (also known as **Viosan**) is an open-source, censorship-resistant internet-access platform that guarantees uninterrupted access to the open Internet. Built for citizens, journalists, and institutions facing internet shutdowns and censorship.

> **لومن‌لینک - نوری که خاموش نمی‌شود**  
> *"The light cannot be extinguished."*

---

## 🎯 Mission

LumenLink combines multi-transport agility, unblockable discovery channels, high-performance relays, and community mesh networking to provide resilient internet access when traditional connectivity fails.

**Key Features:**
- **11 Unblockable Discovery Channels**: GPS, FM Radio, Digital TV, Power Lines, GSM, Blockchain, Satellite, and more
- **Multi-Transport Support**: MASQUE (HTTP/3), XTLS-Reality, Parasite Protocol, SSH, L2TP/IPSec, Domain-Fronted HTTPS
- **40Gbps+ Performance**: eBPF/XDP kernel-level packet processing
- **PERSIA Mode**: Starlink sharing for community connectivity during total blackouts
- **Post-Quantum Cryptography**: X25519 + Kyber768 hybrid encryption
- **Zero Logs**: Privacy-first architecture with no user data collection

---

## 🚀 Quick Start

### For Developers

```bash
# Clone the repository
git clone https://github.com/LumenLink-org/lumenlink.git
cd lumenlink

# Set up development environment
./scripts/setup-dev.sh

# Run tests
./scripts/test-all.sh
```

### For Users

- **Android**: Download APK from [Releases](https://github.com/LumenLink-org/lumenlink/releases)
- **iOS**: Available via TestFlight (coming soon)
- **Desktop**: Download binaries from [Releases](https://github.com/LumenLink-org/lumenlink/releases)

---

## 📦 Source Repositories

These are the repos that will likely be of primary interest:

| Repo | Description |
|------|-------------|
| [LumenLink-org/lumenlink](https://github.com/LumenLink-org/lumenlink) | Main monorepo with core library, server, and web frontend |
| [LumenLink-org/lumenlink-backend](https://github.com/LumenLink-org/lumenlink-backend) | Backend server (Go API, Prometheus, Docker) |
| [LumenLink-org/lumenlink-android](https://github.com/LumenLink-org/lumenlink-android) | Android application |
| [LumenLink-org/lumenlink-ios](https://github.com/LumenLink-org/lumenlink-ios) | iOS VPN application |

---

## 📁 Project Structure

```
lumenlink/
├── core/                    # Rust: Shared library (THE BRAIN)
├── server/                  # Backend services
│   ├── relay/               # Rust: eBPF/XDP relay
│   └── rendezvous/          # Go: Control plane
├── mobile/                  # Mobile applications
│   ├── android/             # Kotlin + Rust JNI
│   └── ios/                 # Swift + Rust XCFramework
├── web/                     # Next.js frontend
├── censorlab/               # Python: Adversarial ML testing
├── infra/                   # Infrastructure as Code
└── docs/                    # Documentation
```

---

## 🛠️ Technology Stack

- **Rust**: Core library, transports, discovery, relays (performance & safety)
- **Go**: Control plane, APIs, orchestration (rapid development)
- **TypeScript/Next.js**: Frontend website (type safety & DX)
- **Kotlin**: Android app (modern, safe)
- **Swift**: iOS app (native performance)
- **Python**: SDR/PLC integration, CensorLab (hardware & ML)

---

## 📚 Documentation

- **[Developer Roadmap](./v1/DEVELOPER_ROADMAP.md)**: Complete 26-week development plan
- **[Task Breakdown](./v1/TASK_BREAKDOWN.md)**: Detailed tasks with code examples
- **[Contributor Guide](./v1/CONTRIBUTOR_GUIDE.md)**: How to contribute
- **[API Documentation](./v1/API_DOCUMENTATION.md)**: Complete API reference
- **[Security Checklist](./v1/SECURITY_CHECKLIST.md)**: Pre-submission security review
- **[Merged Specification](./v1/Project%20code%20deteils/MERGED_ALL_BY_CURSOR.md)**: Complete technical spec

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

**Priority Contributors:**
- Rust developers (core, transports, discovery)
- Go developers (control plane, APIs)
- Android/iOS developers
- SDR/RF engineers (GPS, FM, TV channels)
- Security researchers
- Translators (especially Farsi, Arabic, Kurdish)

**Good First Issues:**
Look for issues labeled `good-first-issue` to get started.

---

## 🔒 Security

- **Reporting Vulnerabilities**: security@lumenlink.org (PGP encrypted)
- **Security Policy**: See [SECURITY.md](./SECURITY.md)
- **Audits**: Regular third-party security audits
- **Responsible Disclosure**: 90-day disclosure timeline

---

## 📜 License

Apache 2.0 License - See [LICENSE](./LICENSE)

**Additional Human Rights Clause:**
This software may not be used by entities that engage in systematic human rights violations or operate censorship infrastructure in violation of international law.

---

## 🏛️ Governance

**Stewardship**: [UNITECH FOR GOOD HUB](https://unitechforgood.org) (Non-profit foundation)

**Technical Steering Committee (TSC):**
- 10 members (5 core devs, 3 security, 2 regional coordinators)
- Open RFC process for major changes
- Community-driven decision making

---

## 🌍 Impact

- **Active Operators**: Growing daily
- **Users Connected**: Thousands in crisis zones
- **Countries Supported**: Iran, Myanmar, and more
- **Zero Operator Arrests**: Our top priority

---

## 📞 Contact

- **General**: info@lumenlink.org
- **Security**: security@lumenlink.org
- **Press**: press@lumenlink.org
- **Community**: [GitHub Discussions](https://github.com/LumenLink-org/lumenlink/discussions)
- **Matrix**: `#lumenlink:matrix.org`

---

## 🙏 Acknowledgments

Built by the global community for people fighting for freedom and truth.

**Special Thanks:**
- All contributors and operators
- Human rights organizations
- Open source community
- Everyone who believes in an open internet

---

## 📊 Status

| Component | Status | Version |
|-----------|--------|---------|
| Rust Core | 🟡 In Development | 0.1.0 |
| Android | 🟡 In Development | 0.1.0 |
| iOS | 🟡 In Development | 0.1.0 |
| Server | 🟡 In Development | 0.1.0 |
| Frontend | 🟡 In Development | 0.1.0 |

---

<div align="center">

**LumenLink - light that cannot be extinguished.**

**لومن‌لینک - نوری که خاموش نمی‌شود**

Made with ❤️ by the global community

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Rust](https://img.shields.io/badge/Rust-1.70+-orange.svg)](https://www.rust-lang.org/)
[![Go](https://img.shields.io/badge/Go-1.21+-blue.svg)](https://go.dev/)
[![CI](https://github.com/LumenLink-org/lumenlink/workflows/CI/badge.svg)](https://github.com/LumenLink-org/lumenlink/actions)
[![Security Scan](https://github.com/LumenLink-org/lumenlink/workflows/Security%20Scan/badge.svg)](https://github.com/LumenLink-org/lumenlink/actions)

</div>
