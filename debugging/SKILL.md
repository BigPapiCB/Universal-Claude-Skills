---
name: debugging
description: Systematic diagnostic strategy with decision tree for reproducing, isolating, and tracing bugs. Use when investigating bugs, crashes, or unexpected behavior. Complements systematic-debugging workflow with concrete diagnostic patterns.
metadata:
  version: "1.0"
allowed-tools: Bash Read
---

# Debugging

## Decision Tree

```
Bug reported → Can you reproduce it?
    ├─ Yes → Is it consistent?
    │   ├─ Yes → Add breakpoint/logging at suspected location → Trace
    │   └─ No (intermittent) → Race condition or timing issue → Add timestamps to logs
    └─ No → Check environment differences
        ├─ Works locally, fails in CI → Compare env vars, paths, timezone
        ├─ Works for others, fails for me → Check local config, versions, OS
        └─ Cannot reproduce anywhere → Get reproduction steps from reporter
```

## Common Root Causes

| Symptom | Likely Cause | Check |
|---------|-------------|-------|
| Works locally, fails in CI | Env vars, file paths, timezone | Compare environments |
| Intermittent failure | Race condition, flaky test, timeout | Add logging, increase timeout |
| Null/undefined error | Missing null check, async ordering | Trace data flow backwards |
| Memory growing | Unclosed resources, event listener leaks | Heap snapshot before/after |
| Slow response | N+1 queries, missing index, large payload | Profile, check query count |
| CORS error | Missing headers, wrong origin, preflight | Check server response headers |
| 403/401 | Token expired, wrong scope, missing header | Inspect request headers |
| "It worked yesterday" | Dependency update, config change, data change | Check git log, dep diff, recent deploys |
| Passes alone, fails in suite | Shared state between tests, order dependency | Run in isolation, check setup/teardown |

## Strategy

1. **Reproduce** - Get a reliable reproduction case first
2. **Isolate** - Binary search: remove half the variables, see if it still fails
3. **Trace** - Follow data flow from input to failure point
4. **Verify** - Fix should explain the symptom, not just suppress it

## Anti-Patterns

- **Shotgun debugging** - Changing random things hoping it works. Understand first, fix second.
- **Fix the symptom** - Adding a null check without understanding why it's null. Find the root cause.
- **"It works now"** - Can't explain why it broke or why the fix works. Keep investigating.
- **Printf and pray** - Adding one log, running, adding another. Plan your instrumentation.
