---
name: api-design
description: REST and GraphQL API design conventions including URL patterns, status codes, pagination, error format, and versioning. Use when designing new APIs, reviewing API contracts, or adding endpoints to any backend.
metadata:
  version: "1.0"
---

# API Design

## Decision Tree

```
New API needed → What type?
    ├─ CRUD operations on resources → REST
    ├─ Complex queries, multiple resources in one call → GraphQL
    ├─ Real-time updates → WebSocket or SSE
    ├─ Internal microservice → gRPC or REST
    └─ Simple webhook/callback → REST POST
```

## REST URL Conventions

```
GET    /resources              # List (with pagination)
GET    /resources/:id          # Get one
POST   /resources              # Create
PUT    /resources/:id          # Full replace
PATCH  /resources/:id          # Partial update
DELETE /resources/:id          # Delete

# Nested resources (one level max)
GET    /users/:id/posts        # User's posts

# Actions (when CRUD doesn't fit)
POST   /resources/:id/actions/publish
```

**Rules:**
- Plural nouns, lowercase, hyphens for multi-word
- No verbs in URLs (the HTTP method IS the verb)
- Max one level of nesting

## Status Codes

| Code | When |
|------|------|
| 200 | Success with body |
| 201 | Created (POST) |
| 204 | Success, no body (DELETE) |
| 400 | Invalid request data |
| 401 | Not authenticated |
| 403 | Authenticated but not authorized |
| 404 | Not found |
| 409 | Conflict (duplicate, state conflict) |
| 422 | Validation error |
| 429 | Rate limited |
| 500 | Server error |

## Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable explanation",
    "details": [
      { "field": "email", "message": "must be a valid email address" }
    ]
  }
}
```

## Pagination

```
# Offset-based (simple)
GET /resources?page=2&per_page=20

# Cursor-based (preferred for large datasets)
GET /resources?cursor=abc123&limit=20
```

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| Verbs in URLs (`/getUsers`) | HTTP methods (`GET /users`) |
| Singular nouns (`/user/1`) | Plural (`/users/1`) |
| Deep nesting (`/a/1/b/2/c/3`) | Max one level, use query params |
| 200 with error body | Use proper status codes |
| No pagination on lists | Always paginate, default limit |
| Exposing auto-increment IDs | UUIDs for public APIs |

## For detailed patterns see:
- [REST patterns reference](references/rest-patterns.md)
- [GraphQL patterns reference](references/graphql-patterns.md)
