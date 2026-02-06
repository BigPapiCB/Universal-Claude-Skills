---
name: database-design
description: Schema design, indexing strategy, migration patterns, and anti-patterns for relational databases. Use when designing schemas, writing migrations, optimizing queries, or reviewing database changes.
metadata:
  version: "1.0"
---

# Database Design

## Decision Tree

```
Data storage needed → What type?
    ├─ Structured, relational → PostgreSQL (server) or SQLite (embedded/mobile)
    ├─ Document-oriented, flexible schema → MongoDB or PostgreSQL JSONB
    ├─ Key-value / caching → Redis
    ├─ Full-text search → PostgreSQL tsvector or Elasticsearch
    └─ Time-series → TimescaleDB (PostgreSQL extension)
```

## Schema Design Checklist

- [ ] Every table has a primary key (prefer UUID or BIGSERIAL)
- [ ] Foreign keys have ON DELETE behavior defined (CASCADE, SET NULL, RESTRICT)
- [ ] Timestamps: `created_at` and `updated_at` on every table
- [ ] Columns NOT NULL by default, nullable only when intentional
- [ ] Text fields have reasonable length constraints
- [ ] Sensitive data columns identified for encryption

## Indexing Strategy

| Always Index | Consider Indexing | Don't Index |
|-------------|------------------|-------------|
| Foreign keys | Columns in WHERE clauses | Boolean columns (low cardinality) |
| Unique constraints | Columns in ORDER BY | Small tables (<1000 rows) |
| Columns in JOINs | Partial indexes for common filters | Frequently updated columns |

## Migration Rules

1. **Always reversible** - Every `up` has a `down`
2. **No data loss** - Add columns before removing old ones
3. **No locks on large tables** - Use `CREATE INDEX CONCURRENTLY`
4. **One concern per migration** - Don't mix schema + data changes
5. **Test with production-size data** - Migrations that work on 100 rows may lock on 10M

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| EAV (Entity-Attribute-Value) | Can't index, can't validate | Proper columns or JSONB |
| God table (100+ columns) | Slow queries, null hell | Normalize into related tables |
| No foreign keys | Orphaned data, integrity bugs | Always define relationships |
| SELECT * | Wastes bandwidth, breaks on schema change | Explicit column list |
| Comma-separated values | Can't query, can't join | Junction table |
| Money as FLOAT | Rounding errors | DECIMAL or integer cents |

## For detailed patterns see:
- [PostgreSQL patterns](references/postgres-patterns.md)
- [Schema design patterns](references/schema-patterns.md)
