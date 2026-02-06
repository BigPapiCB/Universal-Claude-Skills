---
name: project-setup
description: Non-obvious project setup patterns for environment config, gitignore hygiene, and release versioning. Use when initializing projects, auditing tracked files, setting up env validation, or planning releases. Focuses on common mistakes Claude makes without guidance.
metadata:
  version: "1.0"
---

# Project Setup

## Environment Config — The Non-Obvious Parts

### Validate at startup, fail fast

Don't just read env vars — validate them on boot so missing config fails immediately, not at 3am when that code path runs.

```typescript
const required = (key: string): string => {
  const val = process.env[key];
  if (!val) throw new Error(`Missing required env var: ${key}`);
  return val;
};

export const config = {
  port: parseInt(process.env.PORT || '3000'),
  databaseUrl: required('DATABASE_URL'),
  jwtSecret: required('JWT_SECRET'),
} as const;
```

```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str
    jwt_secret: str
    port: int = 8000

    class Config:
        env_file = ".env"

settings = Settings()  # Validates on import, raises if missing
```

### Env anti-patterns

| Mistake | Why it's bad | Fix |
|---------|-------------|-----|
| Real secrets in .env.example | Gets committed, leaked | Fake/placeholder values only |
| No validation at startup | Fails at runtime, not boot | Validate eagerly |
| Same secret across environments | One leak compromises all | Unique per env |
| Secrets in Docker build args | Cached in image layers | Runtime env or Docker secrets |
| .env not in .gitignore | Secrets committed | Add immediately, rotate if exposed |

## Gitignore — The Mistakes

Claude generates fine .gitignore files. These are the mistakes to watch for:

| Mistake | Fix |
|---------|-----|
| Committing .env then adding to .gitignore | `git rm --cached .env` + **rotate all secrets** |
| Ignoring lockfiles (package-lock.json) | **Commit** lockfiles — reproducible builds |
| Ignoring .vscode entirely | Only ignore `settings.json`, commit `extensions.json` |
| Not running an audit | `git ls-files \| grep -E '\.(env\|pem\|key)$'` |

## Versioning — Decision Tree

Claude knows semver. This is for the edge cases:

```
What changed? → What bump?
    ├─ Removed/renamed public API → MAJOR
    ├─ Changed existing behavior (even if "fixed") → MAJOR
    ├─ Added new feature (backwards compatible) → MINOR
    ├─ Added optional parameter → MINOR
    ├─ Bug fix (same API contract) → PATCH
    ├─ Performance improvement (same API) → PATCH
    ├─ Dependency update (no API change) → PATCH
    └─ Pre-release (0.x.y) → No stability guarantee
```

### Release process

```bash
# 1. Update changelog
# 2. Bump version in package.json / pyproject.toml / etc.
git add -A && git commit -m "release: v2.1.0"
git tag -a v2.1.0 -m "Release v2.1.0"
git push && git push --tags
```

### Automation tools

| Tool | Ecosystem | What It Does |
|------|-----------|-------------|
| `semantic-release` | Node | Auto version + changelog from commits |
| `python-semantic-release` | Python | Same for Python |
| `release-please` | Any (GitHub Action) | Auto PRs with version bumps + changelog |
