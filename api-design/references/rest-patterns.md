# REST Patterns Reference

## Filtering & Sorting

```
GET /resources?status=active&created_after=2024-01-01
GET /resources?search=keyword
GET /resources?sort=-created_at,name    # - prefix = descending
GET /resources?fields=id,name,email     # Sparse fieldsets
```

## Bulk Operations

```
POST /resources/bulk
Body: { "items": [...] }
Response: { "results": [{ "id": 1, "status": "created" }, { "id": 2, "status": "error", "error": "..." }] }
```

## Rate Limiting Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1620000000
Retry-After: 60
```

## Idempotency

- GET, PUT, DELETE are idempotent by nature
- POST: Use `Idempotency-Key` header for safe retries

## Versioning

- URL prefix: `/v1/resources` — recommended for public APIs
- Header: `Accept: application/vnd.api+json; version=1` — for internal APIs
