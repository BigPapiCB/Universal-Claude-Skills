# Schema Design Patterns

## One-to-Many

```sql
CREATE TABLE users (id BIGSERIAL PRIMARY KEY, name TEXT NOT NULL);

CREATE TABLE posts (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
CREATE INDEX idx_posts_user ON posts(user_id);
```

## Many-to-Many (Junction Table)

```sql
CREATE TABLE tags (id BIGSERIAL PRIMARY KEY, name TEXT UNIQUE NOT NULL);

CREATE TABLE post_tags (
  post_id BIGINT REFERENCES posts(id) ON DELETE CASCADE,
  tag_id BIGINT REFERENCES tags(id) ON DELETE CASCADE,
  PRIMARY KEY (post_id, tag_id)
);
```

## Soft Delete

```sql
ALTER TABLE posts ADD COLUMN deleted_at TIMESTAMPTZ;
CREATE INDEX idx_posts_active ON posts(id) WHERE deleted_at IS NULL;
-- Always filter: WHERE deleted_at IS NULL
```

## Audit Trail

```sql
CREATE TABLE audit_log (
  id BIGSERIAL PRIMARY KEY,
  table_name TEXT NOT NULL,
  record_id BIGINT NOT NULL,
  action TEXT NOT NULL CHECK (action IN ('INSERT', 'UPDATE', 'DELETE')),
  old_data JSONB,
  new_data JSONB,
  changed_by BIGINT REFERENCES users(id),
  changed_at TIMESTAMPTZ DEFAULT NOW()
);
```

## SQLite (Embedded/Mobile)

- `INTEGER PRIMARY KEY` = auto-increment alias for rowid
- `CREATE TABLE t (...) STRICT;` -- enforces types
- No native UUID: store as TEXT or BLOB
- No TIMESTAMPTZ: store as ISO 8601 TEXT or Unix integer
- `PRAGMA journal_mode=WAL;` -- concurrent reads
