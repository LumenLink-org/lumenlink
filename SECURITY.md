# Security Policy

## 🛡️ Reporting a Vulnerability

**DO NOT** report security vulnerabilities through public GitHub issues.

### For Critical Vulnerabilities

1. **Encrypt your report** using our PGP key:
   ```
   -----BEGIN PGP PUBLIC KEY BLOCK-----
   [PGP key will be added before launch]
   -----END PGP PUBLIC KEY BLOCK-----
   ```

2. **Email to:** `security@lumenlink.org`

3. **Subject:** `[SECURITY] Vulnerability Report`

4. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if any)
   - Your contact information (optional)

### Response Time

- **Critical**: 24 hours
- **High**: 48 hours
- **Medium/Low**: 7 days

We will acknowledge receipt within 24 hours and provide a timeline for fixes.

## 🚨 Emergency Contact

If the vulnerability is actively being exploited and endangering users:

**Signal/WhatsApp:** `+1-XXX-XXX-XXXX` (emergency only)  
**Telegram:** `@lumenlink_emergency` (use "Delete after reading")

## 🔒 Supported Versions

We provide security updates for:

| Version | Supported          |
| ------- | ------------------ |
| 1.x.x   | ✅ Active support |
| 0.x.x   | ⚠️ Critical fixes only |

## 📋 Security Considerations

### For Users in High-Risk Regions

- The app includes **auto-wipe** features
- Use **decoy mode** when crossing checkpoints
- **Burner identities** rotate automatically
- **Never** discuss LumenLink on monitored channels

### For Developers

- **Never** commit secrets or API keys
- **Always** use `cargo audit` before committing
- **All** cryptography must be reviewed by 3+ people
- **Assume** all code will be inspected by adversaries

### For Operators

- Servers must be **outside oppressive jurisdictions**
- **Zero logs** policy is mandatory
- **Regular key rotation** (automated)
- **DDoS protection** must be enabled

## 🔍 Security Features

### 1. Cryptography

- **Hybrid Post-Quantum:** X25519 + Kyber768
- **Signatures:** Dilithium3
- **Key Derivation:** Argon2id
- **Randomness:** System CSPRNG + Fortuna

### 2. App Security

- **Remote Attestation:** Play Integrity / DCAppAttest
- **Auto-Wipe:** 3 failed biometric attempts
- **Memory Protection:** mlock, zeroization
- **Secure Enclave:** Where available

### 3. Network Security

- **Perfect Forward Secrecy:** All sessions
- **Traffic Obfuscation:** Mimics legitimate apps
- **DNS Security:** DNSSEC, DoH/DoT
- **Certificate Pinning:** Hardcoded + dynamic

### 4. Operational Security

- **No Central Database:** Minimal user data
- **Ephemeral Infrastructure:** Servers can be burned
- **Geographic Distribution:** Multiple jurisdictions
- **Legal Protection:** Non-profit, open source

## 🧪 Security Testing

### Automated Testing

```bash
# Run security checks
./scripts/security-check.sh

# Includes:
# - cargo audit
# - cargo deny
# - snyk test
# - trufflehog (secrets scanning)
# - hadolint (Docker security)
```

### Manual Testing

- **Penetration testing** every 6 months
- **Third-party audits** before major releases
- **Red team exercises** with humanitarian orgs

### Bug Bounty Program

We offer bounties for critical vulnerabilities:

| Severity | Bounty |
|----------|--------|
| Critical | $5,000 + Humanitarian recognition |
| High     | $2,000 |
| Medium   | $500   |
| Low      | $100   |

**Note:** Bounties paid in cryptocurrency or donated to charity of your choice.

## 📚 Security Documentation

- [Threat Model](docs/security/threat-model.md)
- [Cryptographic Protocols](docs/security/crypto.md)
- [Secure Deployment Guide](docs/security/deployment.md)
- [Incident Response Plan](docs/security/incident-response.md)
- [docs/security-verification.md](./docs/security-verification.md)

## 👥 Security Team

**Lead:** `@security-lead` (anonymous)  
**Cryptography:** `@crypto-team` (3 members)  
**Infrastructure:** `@infra-security` (2 members)  
**Mobile:** `@mobile-security` (2 members)

## 🕵️ Responsible Disclosure

We follow a 90-day disclosure timeline:

1. **Day 0:** Report received
2. **Day 7:** Acknowledge + triage
3. **Day 30:** Fix developed and tested
4. **Day 60:** Deployed to beta testers
5. **Day 90:** Public disclosure + CVE

Extensions granted for coordination with other projects.

## 🇮🇷 Special Considerations for Iran

Given the current situation:

- **No Iranian IPs** on our infrastructure
- **Tor bridges** available for access
- **Snowflake integration** for extreme censorship
- **Emergency access** via satellite providers

## 🏛️ Legal Protections

- **501(c)(3)** non-profit status (pending)
- **Swiss foundation** for legal jurisdiction
- **All code** is open source (Apache 2.0)
- **No warrants can compel** non-existent logs

## ❤️ Final Note

We build for people in danger. Every security consideration is a life consideration.

*"Your vulnerability report might save someone's life. Thank you."*
