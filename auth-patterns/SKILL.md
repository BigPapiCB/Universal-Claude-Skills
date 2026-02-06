---
name: auth-patterns
description: Authentication and authorization patterns including JWT vs sessions, OAuth flows, token refresh, RBAC, and password handling. Use when implementing login, signup, token management, role-based access, or reviewing auth security.
metadata:
  version: "1.0"
---

# Auth Patterns

## Decision Tree

```
Need authentication → What type?
    ├─ Server-rendered app (SSR) → Session-based auth (cookies)
    ├─ SPA / mobile app → JWT (access + refresh tokens)
    ├─ Third-party login (Google, GitHub) → OAuth 2.0 / OIDC
    ├─ Machine-to-machine → API keys or client credentials
    └─ Internal microservice → mTLS or service tokens
```

## JWT vs Sessions

| Factor | JWT | Sessions |
|--------|-----|----------|
| Storage | Client-side (memory or cookie) | Server-side (DB/Redis) |
| Scalability | Stateless, no server lookup | Requires shared session store |
| Revocation | Hard (need blocklist) | Easy (delete from store) |
| Size | Larger (payload in token) | Small (just session ID) |
| Best for | APIs, SPAs, mobile | Server-rendered apps, simple auth |

## Token Architecture

```
Login → Issue access token (short-lived: 15min) + refresh token (long-lived: 7-30 days)

API request → Send access token in Authorization header
    ├─ Valid → Process request
    ├─ Expired → Client uses refresh token to get new access token
    └─ Refresh expired → User must re-authenticate
```

**Access token:** Short-lived (15 min), contains user ID + roles, sent in `Authorization: Bearer <token>` header.

**Refresh token:** Long-lived (7-30 days), stored securely (httpOnly cookie or secure storage), used ONLY to get new access tokens, rotated on use.

## Password Handling

```
NEVER: MD5, SHA1, SHA256 for passwords (fast hash = easy to brute force)
NEVER: Store plaintext passwords
ALWAYS: bcrypt (cost factor 12+) or argon2id
ALWAYS: Salt automatically (bcrypt does this)
```

```typescript
import bcrypt from 'bcrypt';
const hash = await bcrypt.hash(password, 12);
const valid = await bcrypt.compare(password, hash);
```

```python
from passlib.hash import bcrypt
hash = bcrypt.hash(password, rounds=12)
valid = bcrypt.verify(password, hash)
```

## RBAC (Role-Based Access Control)

```
User → has Roles → Roles have Permissions → Check permission on action

// Middleware pattern
function requirePermission(permission: string) {
  return (req, res, next) => {
    if (!req.user.permissions.includes(permission)) {
      return res.status(403).json({ error: { code: 'FORBIDDEN' } });
    }
    next();
  };
}

app.delete('/users/:id', requirePermission('users:delete'), deleteUser);
```

**Rules:**
- Check permissions server-side ALWAYS (client checks are UX only)
- Use specific permissions (`users:delete`) not broad roles (`admin`) in code
- Roles map to permission sets in config, not hardcoded
- No IDOR — verify the user owns the resource, not just that they're authenticated

## Security Checklist

- [ ] Passwords hashed with bcrypt (cost 12+) or argon2id
- [ ] Access tokens short-lived (15 min max)
- [ ] Refresh tokens rotated on use
- [ ] Tokens in httpOnly cookies (not localStorage)
- [ ] CSRF protection on cookie-based auth
- [ ] Rate limiting on login/signup endpoints
- [ ] Account lockout after failed attempts
- [ ] No sensitive data in JWT payload (no passwords, secrets)
- [ ] HTTPS everywhere (no auth over HTTP)
- [ ] Logout invalidates refresh token server-side

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| JWT in localStorage | httpOnly cookie (XSS-proof) |
| No token expiration | Access: 15min, Refresh: 7-30 days |
| Same secret for all tokens | Separate secrets for access/refresh |
| Permissions checked client-side only | Always enforce server-side |
| Rolling your own crypto | Use bcrypt/argon2id, established JWT libs |
| No rate limiting on auth endpoints | Rate limit + account lockout |
| Refresh tokens that never rotate | Rotate on every use, detect reuse |
| Storing user roles in JWT without server check | JWT roles for UX, server check for auth |

## For OAuth flow details see:
- [OAuth flows reference](references/oauth-flows.md)
