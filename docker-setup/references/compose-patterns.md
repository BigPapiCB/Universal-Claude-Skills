# Docker Compose Patterns

## App + Database

```yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    env_file: .env
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASS}
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  pgdata:
```

## With Redis

```yaml
  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
```

## Development Overrides

```yaml
# docker-compose.override.yml (auto-loaded, NOT committed)
services:
  app:
    build:
      target: builder
    volumes:
      - .:/app
      - /app/node_modules
    command: npm run dev
```

## Production Tips

- `restart: unless-stopped` (not `always`)
- Set memory limits: `deploy.resources.limits.memory: 512M`
- Named volumes for persistence (not bind mounts)
- Pin image tags to exact versions
