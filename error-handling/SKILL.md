---
name: error-handling
description: Error propagation patterns, custom error types, user-facing vs internal errors, and HTTP error mapping. Use when designing error handling strategy, creating custom error classes, or deciding how to propagate and present errors.
metadata:
  version: "1.0"
---

# Error Handling

## Decision Tree

```
Something went wrong → What do you do?
    ├─ Can you recover? (retry, fallback, default value)
    │   └─ Yes → Handle silently or log at DEBUG level
    ├─ Is it a programming bug? (null ref, type error, assertion)
    │   └─ Yes → Crash / throw — don't catch bugs, fix them
    ├─ Is it an expected failure? (validation, not found, conflict)
    │   └─ Yes → Return typed error, let caller decide
    └─ Is it an infrastructure failure? (DB down, network timeout)
        └─ Yes → Log at ERROR, return 503, alert
```

## Error Type Hierarchy

Define a small set of error types. Don't create one per function — create one per *category*.

```typescript
// Base error with code for programmatic handling
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 500,
    public isOperational: boolean = true
  ) {
    super(message);
  }
}

// Specific categories
class ValidationError extends AppError {
  constructor(message: string, public fields?: Record<string, string>) {
    super(message, 'VALIDATION_ERROR', 422);
  }
}

class NotFoundError extends AppError {
  constructor(resource: string, id: string) {
    super(`${resource} ${id} not found`, 'NOT_FOUND', 404);
  }
}

class AuthError extends AppError {
  constructor(message = 'Unauthorized') {
    super(message, 'AUTH_ERROR', 401);
  }
}

class ConflictError extends AppError {
  constructor(message: string) {
    super(message, 'CONFLICT', 409);
  }
}
```

```python
class AppError(Exception):
    def __init__(self, message: str, code: str, status_code: int = 500):
        self.message = message
        self.code = code
        self.status_code = status_code

class NotFoundError(AppError):
    def __init__(self, resource: str, id: str):
        super().__init__(f"{resource} {id} not found", "NOT_FOUND", 404)

class ValidationError(AppError):
    def __init__(self, message: str, fields: dict | None = None):
        super().__init__(message, "VALIDATION_ERROR", 422)
        self.fields = fields
```

## Propagation Rules

| Layer | Responsibility |
|-------|---------------|
| **Repository/DB** | Throw domain errors (NotFoundError, ConflictError). Never expose DB driver errors. |
| **Service/Business** | Catch and translate. Add context. Re-throw as domain errors. |
| **Controller/Handler** | Catch domain errors, map to HTTP responses. Log unexpected errors. |
| **Global handler** | Catch everything else. Log. Return 500 with generic message. |

## User-Facing vs Internal Errors

```
NEVER expose to users:
  - Stack traces
  - Database error messages ("relation users does not exist")
  - Internal file paths
  - Dependency versions

ALWAYS expose to users:
  - What went wrong (in plain language)
  - What they can do about it
  - A request ID for support
```

## HTTP Error Mapping

```typescript
// Global error handler (Express example)
app.use((err, req, res, next) => {
  if (err instanceof AppError && err.isOperational) {
    return res.status(err.statusCode).json({
      error: { code: err.code, message: err.message }
    });
  }
  // Unexpected error — log and return generic
  logger.error('Unexpected error', { err, requestId: req.id });
  res.status(500).json({
    error: { code: 'INTERNAL_ERROR', message: 'Something went wrong', requestId: req.id }
  });
});
```

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| `catch (e) {}` — swallowing errors | At minimum log, usually re-throw |
| `catch (e) { throw new Error("error") }` — losing context | Wrap: `throw new AppError(message, { cause: e })` |
| Returning null to indicate failure | Return a typed error or use Result pattern |
| Logging + throwing (double reporting) | Do one or the other at each layer |
| Generic `catch (Exception)` at every level | Catch specific types, let unexpected ones bubble |
| String error codes (`"ERR_001"`) | Use human-readable codes (`"NOT_FOUND"`) |
