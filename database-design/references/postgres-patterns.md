# PostgreSQL Patterns

## Column Types

| Use Case | Type | Not |
|----------|------|-----|
| Primary key | `BIGSERIAL` or `UUID` | `SERIAL` (32-bit limit) |
| Money | `NUMERIC(12,2)` | `FLOAT`, `MONEY` |
| Timestamps | `TIMESTAMPTZ` | `TIMESTAMP` (no timezone) |
| Short text | `VARCHAR(255)` | `CHAR(n)` |
| Long text | `TEXT` | `VARCHAR` without limit |
| JSON data | `JSONB` | `JSON` (no indexing) |
| Enum | `TEXT` + CHECK constraint | `ENUM` type (hard to modify) |

## Index Examples

```sql
-- Composite (leftmost prefix rule)
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at DESC);

-- Partial (only index what you query)
CREATE INDEX idx_orders_active ON orders(status) WHERE status = 'active';

-- Covering (avoids table lookup)
CREATE INDEX idx_users_email_name ON users(email) INCLUDE (name);
```

## Diagnostic Queries

```sql
-- Table sizes
SELECT relname, pg_size_pretty(pg_total_relation_size(oid))
FROM pg_class WHERE relkind = 'r' ORDER BY pg_total_relation_size(oid) DESC LIMIT 10;

-- Unused indexes
SELECT indexrelname, idx_scan FROM pg_stat_user_indexes
WHERE idx_scan = 0 AND indexrelname NOT LIKE '%pkey%';

-- Lock monitoring
SELECT pid, relation::regclass, mode, granted FROM pg_locks WHERE NOT granted;
```

## JSONB

```sql
CREATE INDEX idx_data_type ON items USING GIN ((data->>'type'));
SELECT * FROM items WHERE data @> '{"type": "widget"}';
```
