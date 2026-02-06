---
name: test-writer
description: Test design patterns, what to test, assertion strategies, mocking strategy, and common anti-patterns. Use when writing unit tests, integration tests, or end-to-end tests. Complements TDD workflow with concrete test design knowledge.
metadata:
  version: "1.0"
---

# Test Writer

## Decision Tree

```
Need to write a test → What kind?
    ├─ Testing a pure function → Unit test, no mocking needed
    ├─ Testing a class with dependencies → Unit test, mock dependencies
    ├─ Testing API endpoint → Integration test, real server + test DB
    ├─ Testing user workflow → E2E test (Playwright / platform integration test)
    └─ Testing UI appearance → Snapshot test
```

## What to Test

| Always Test | Skip |
|------------|------|
| Business logic / core algorithms | Framework internals |
| Edge cases (empty, null, boundary) | Simple getters/setters |
| Error paths and error messages | Third-party library behavior |
| State transitions | Private methods (test via public API) |
| Integration points (API, DB) | UI layout pixel-perfection |
| Security-sensitive paths (auth, validation) | Generated code |

## Naming Convention

```
test_[unit]_[scenario]_[expected result]

# Examples:
test_login_with_valid_credentials_returns_token
test_login_with_expired_password_returns_403
test_cart_add_item_when_full_raises_limit_error
```

## Assertion Best Practices

- **One logical assertion per test** - Test one behavior, not one `assert` statement
- **Assert on behavior, not implementation** - Check return values, not internal state
- **Use specific assertions** - `assertEqual(x, 5)` not `assertTrue(x == 5)`
- **Include failure messages** - `assertEqual(result, 5, "discount should be 5% for VIP")`
- **Test exact error types** - `assertRaises(ValueError)` not `assertRaises(Exception)`

## Mocking Strategy

| Mock | Don't Mock |
|------|-----------|
| External APIs / HTTP calls | The unit under test |
| Database (in unit tests) | Simple value objects |
| File system / network | Pure functions |
| Time / randomness | Internal collaborators (usually) |
| Third-party services | Everything (over-mocking = brittle tests) |

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Testing implementation details | Breaks on refactor | Test behavior/output |
| Flaky tests ignored | Erode trust in test suite | Fix or delete immediately |
| Copy-paste test setup | Hard to maintain | Use fixtures/factories |
| No assertion (test just "runs") | Proves nothing | Always assert expected outcome |
| Testing too much in one test | Unclear what failed | One behavior per test |
| Mocking everything | Tests prove nothing | Only mock boundaries |

## Test Structure (Arrange-Act-Assert)

```
# Arrange - Set up inputs and expected outputs
user = create_user(role="admin")

# Act - Call the function under test
result = permissions.can_delete(user, resource)

# Assert - Verify the result
assert result is True
```

## Coverage Targets

- **New code**: 80%+ line coverage
- **Critical paths** (auth, payments, data): 95%+
- **Don't chase 100%**: Diminishing returns past 85% overall
