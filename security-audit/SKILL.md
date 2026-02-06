---
name: security-audit
description: Security audit with OWASP top 10 checklist, dependency scanning, secrets detection, input validation, and injection prevention. Use when auditing code security, reviewing auth implementations, handling user input, or hardening applications.
metadata:
  version: "1.0"
allowed-tools: Bash Grep Read
---

# Security Audit

## Decision Tree

```
Security concern → What type?
    ├─ Reviewing code changes → OWASP checklist below
    ├─ Handling user input → Where does it go?
    │   ├─ Database query → Parameterized queries (see references/injection-patterns.md)
    │   ├─ HTML output → Framework escaping (see references/injection-patterns.md)
    │   ├─ Shell command → Array arguments (see references/injection-patterns.md)
    │   ├─ File path → Resolve + verify within allowed dir
    │   └─ URL redirect → Allowlist or relative paths only
    ├─ Auditing dependencies → Run scanning commands below
    └─ Full security audit → All phases below
```

## Phases

```
Phase 1: Scan → Phase 2: Analyze → Phase 3: Report
```

## OWASP Top 10 Checklist

| # | Vulnerability | Check |
|---|--------------|-------|
| A01 | Broken Access Control | Auth on every endpoint, RBAC server-side, no IDOR |
| A02 | Cryptographic Failures | TLS everywhere, no MD5/SHA1 for passwords, secrets in env not code |
| A03 | Injection | Parameterized queries, no string concat for SQL/shell/HTML |
| A04 | Insecure Design | Rate limiting, account lockout, input size limits |
| A05 | Security Misconfiguration | No default creds, errors don't leak internals, CORS restricted |
| A06 | Vulnerable Components | Deps updated, no known CVEs, lockfile committed |
| A07 | Auth Failures | Strong passwords, MFA available, session timeout |
| A08 | Data Integrity | Signed updates, CI/CD secured, no untrusted deserialization |
| A09 | Logging Failures | Auth events logged, no sensitive data in logs |
| A10 | SSRF | URL validation, allowlists for external calls |

## Scanning Commands

```bash
# Dependency vulnerabilities
npm audit                          # Node
pip-audit                          # Python (pip install pip-audit)

# Secret detection
gitleaks detect --source .

# Static analysis
semgrep --config auto .            # Multi-language
bandit -r .                        # Python
```

## Input Validation Checklist

| Input | Validate |
|-------|----------|
| Strings | Max length, allowed characters, trim whitespace |
| Numbers | Min/max range, integer vs float, NaN check |
| Email | Format + domain check (not just regex) |
| URLs | Protocol allowlist (http/https only), no internal IPs |
| File uploads | Extension allowlist, MIME check, size limit |
| JSON body | Schema validation (zod, joi, pydantic) |
| IDs | Format check (UUID format, positive integer) |

## Response Headers

```
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

## Output Format

```
[CRITICAL|HIGH|MEDIUM|LOW] Category - Finding
  Location: file:line
  Impact: What an attacker could do
  Fix: Specific remediation
```

## For injection prevention patterns see:
- [Injection patterns reference](references/injection-patterns.md)
