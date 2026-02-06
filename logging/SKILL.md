---
name: logging
description: Structured logging patterns, log levels, trace IDs, and what to log vs what never to log. Use when setting up logging, choosing log levels, implementing request tracing, or reviewing log output quality.
metadata:
  version: "1.0"
---

# Logging

## Decision Tree

```
What to log? → What level?
    ├─ Application started, config loaded → INFO
    ├─ Request received/completed → INFO (or DEBUG in high-traffic)
    ├─ User action (login, purchase, permission change) → INFO
    ├─ Expected failure (validation, 404) → WARN
    ├─ Unexpected failure (unhandled error, 500) → ERROR
    ├─ Variable values during debugging → DEBUG
    └─ Granular tracing (entering/exiting functions) → TRACE (off in production)
```

## Log Levels

| Level | When | Production? |
|-------|------|------------|
| **ERROR** | Unexpected failures, needs attention | Always on |
| **WARN** | Expected failures, degraded state | Always on |
| **INFO** | Business events, lifecycle events | Usually on |
| **DEBUG** | Diagnostic detail, variable values | Off (enable per-request) |
| **TRACE** | Function-level tracing | Off |

## Structured Logging Format

Always log as structured JSON, not free-text strings.

```
BAD:  logger.info(`User ${userId} logged in from ${ip}`)
GOOD: logger.info('User logged in', { userId, ip, method: 'password' })
```

```json
{
  "level": "info",
  "message": "User logged in",
  "timestamp": "2026-02-05T12:00:00Z",
  "userId": "abc-123",
  "ip": "192.168.1.1",
  "method": "password",
  "requestId": "req-456",
  "service": "auth-api"
}
```

## What to Log

| Always Log | NEVER Log |
|-----------|-----------|
| Request ID / trace ID | Passwords or password hashes |
| User ID (not PII) | API keys, tokens, secrets |
| Action performed | Credit card numbers |
| HTTP method, path, status, duration | Session tokens |
| Error messages and codes | Full request bodies with PII |
| Timestamp (ISO 8601, UTC) | Social security / national IDs |

## Request Tracing

Assign a unique `requestId` at the edge (API gateway or first middleware). Pass it through every log entry and downstream call.

```typescript
// Middleware
app.use((req, res, next) => {
  req.id = req.headers['x-request-id'] || crypto.randomUUID();
  res.setHeader('x-request-id', req.id);
  next();
});

// Then in every log call
logger.info('Processing payment', { requestId: req.id, orderId });
```

Return it in error responses so users can reference it in support requests.

## Tools by Stack

| Stack | Library | Key Feature |
|-------|---------|-------------|
| Node.js | `pino` | Fast structured JSON, low overhead |
| Node.js | `winston` | Flexible transports, widely used |
| Python | `structlog` | Structured logging, processors |
| Python | `logging` (stdlib) | Built-in, use with JSON formatter |
| Go | `slog` (stdlib 1.21+) | Structured, built-in |

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| `console.log` / `print` in production | Use structured logger |
| Logging sensitive data | Scrub PII, mask secrets |
| No request ID | Add trace ID middleware |
| String concatenation in log messages | Use structured fields |
| Logging in hot loops | Guard with level check or move outside loop |
| No log rotation / retention policy | Configure max size, retention days |
