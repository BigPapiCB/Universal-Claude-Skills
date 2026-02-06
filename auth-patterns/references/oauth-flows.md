# OAuth 2.0 / OIDC Flows Reference

## Authorization Code Flow (recommended for web apps)

```
1. User clicks "Login with Google"
2. Redirect to provider: GET /authorize?client_id=X&redirect_uri=Y&scope=openid+email&response_type=code
3. User authenticates with provider
4. Provider redirects back: GET /callback?code=AUTH_CODE
5. Server exchanges code for tokens: POST /token (code + client_secret)
6. Server receives access_token + id_token
7. Server creates session or issues own JWT
```

**Key:** The auth code exchange happens server-side (client_secret never exposed to browser).

## Authorization Code + PKCE (recommended for SPAs/mobile)

```
1. Client generates code_verifier (random string) and code_challenge (SHA256 hash)
2. Redirect to provider with code_challenge
3. User authenticates
4. Provider redirects with auth code
5. Client exchanges code + code_verifier for tokens
6. Provider verifies code_challenge matches code_verifier
```

**Key:** No client_secret needed. PKCE prevents interception attacks.

## Client Credentials (machine-to-machine)

```
POST /token
  grant_type=client_credentials
  client_id=X
  client_secret=Y
  scope=api.read
```

**Key:** No user involved. Service authenticates directly.

## Provider Configuration

```typescript
// Google
const googleConfig = {
  authorizationUrl: 'https://accounts.google.com/o/oauth2/v2/auth',
  tokenUrl: 'https://oauth2.googleapis.com/token',
  scopes: ['openid', 'email', 'profile'],
};

// GitHub
const githubConfig = {
  authorizationUrl: 'https://github.com/login/oauth/authorize',
  tokenUrl: 'https://github.com/login/oauth/access_token',
  scopes: ['user:email'],
};
```

## OIDC (OpenID Connect)

OIDC adds an `id_token` (JWT) to OAuth 2.0 that contains:
- `sub` — unique user ID
- `email` — user's email
- `name` — display name
- `iss` — issuer (must validate)
- `aud` — audience (must match your client_id)
- `exp` — expiration (must check)

**Always validate:** issuer, audience, expiration, and signature.

## Anti-Patterns

- **Implicit flow** — Deprecated. Use Authorization Code + PKCE instead.
- **Storing provider tokens long-term** — Exchange for your own session/JWT.
- **Not validating `state` parameter** — CSRF vulnerability. Always check.
- **Trusting `id_token` without signature verification** — Verify with provider's public keys.
