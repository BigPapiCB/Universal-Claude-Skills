---
name: performance-audit
description: Performance profiling with phased workflow, bottleneck patterns, and diagnostic queries. Use when diagnosing slow responses, high memory usage, or optimizing application performance.
metadata:
  version: "1.0"
allowed-tools: Bash Read
---

# Performance Audit

## Decision Tree

```
Performance issue → Where is it slow?
    ├─ API response time → Check DB queries, N+1, missing indexes
    ├─ Page load → Check bundle size, images, network waterfall
    ├─ Memory growing → Check for leaks (event listeners, closures, unclosed resources)
    ├─ Build/CI slow → Check caching, parallelization, unnecessary steps
    └─ Unknown → Profile first, then follow the data
```

## Phases

```
Phase 1: Measure (baseline) → Phase 2: Identify (profile) → Phase 3: Fix → Phase 4: Verify (compare to baseline)
```

## Key Metrics

| Metric | Target | Tool |
|--------|--------|------|
| API response time (p95) | <200ms | Load test, APM |
| Time to First Byte | <100ms | curl -w, Lighthouse |
| First Contentful Paint | <1.5s | Lighthouse, WebPageTest |
| Memory usage | Stable over time | `top`, `htop`, profiler |
| DB query time | <50ms per query | Query logs, EXPLAIN |
| Bundle size (JS) | <200KB gzipped | `npx bundlesize`, webpack-bundle-analyzer |

## Profiling Commands

```bash
# Node.js
node --prof app.js                 # V8 profiler
clinic doctor -- node app.js       # Auto-diagnose (npm i -g clinic)

# Python
python -m cProfile -s cumtime app.py
py-spy record -o profile.svg -- python app.py

# Database
EXPLAIN ANALYZE SELECT ...;         # PostgreSQL
```

## Common Bottlenecks

| Pattern | Symptom | Fix |
|---------|---------|-----|
| N+1 queries | Slow list pages, many DB calls | Eager loading, JOIN, batch fetch |
| Missing index | Slow queries on large tables | Add index on WHERE/JOIN columns |
| Unbounded query | Memory spike, timeout | Add LIMIT, pagination |
| Synchronous I/O | Blocked event loop | async/await, worker threads |
| Large payload | Slow API, high bandwidth | Pagination, field selection, compression |
| No caching | Repeated expensive operations | Redis, in-memory cache, HTTP cache headers |
| Unoptimized images | Slow page load | WebP, lazy loading, srcset |
| Memory leak | Growing memory, eventual crash | Heap snapshots, check event listeners/closures |

## Database Query Analysis

```sql
-- PostgreSQL: Find slow queries
SELECT query, mean_exec_time, calls
FROM pg_stat_statements ORDER BY mean_exec_time DESC LIMIT 10;

-- Find missing indexes
SELECT relname, seq_scan, idx_scan
FROM pg_stat_user_tables WHERE seq_scan > idx_scan ORDER BY seq_scan DESC;
```

## Output Format

```
[IMPACT: HIGH|MEDIUM|LOW] Category - Finding
  Measured: Current value
  Target: Expected value
  Fix: Specific optimization
```
