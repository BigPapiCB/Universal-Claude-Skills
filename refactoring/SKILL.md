---
name: refactoring
description: When to refactor vs leave alone, code smell detection, safe refactoring steps, and common transformation patterns. Use when asked to refactor code, clean up technical debt, or when reviewing code that shows structural problems.
metadata:
  version: "1.0"
---

# Refactoring

## Decision Tree

```
Code looks messy → Should you refactor?
    ├─ Is it blocking the current task? → Yes → Refactor the minimum needed
    ├─ Is it actively causing bugs? → Yes → Refactor with tests first
    ├─ Will you touch this code again soon? → Yes → Refactor now (Boy Scout Rule)
    ├─ Is it working and rarely changed? → Leave it alone
    ├─ Does the user explicitly ask for refactoring? → Yes → Refactor per their scope
    └─ Is it just "not how I'd write it"? → Leave it alone
```

## Code Smells → Refactoring

| Smell | How to Detect | Refactoring |
|-------|--------------|-------------|
| **Long function** (>30 lines) | Scrolling to read it | Extract Method — pull out logical blocks |
| **Duplicate code** | Same 3+ lines in 2+ places | Extract shared function or module |
| **Deep nesting** (>3 levels) | Arrow-shaped code | Early returns, guard clauses, extract helper |
| **God class** (does everything) | 500+ lines, 10+ methods | Split by responsibility into focused classes |
| **Primitive obsession** | Passing 5+ params, string IDs everywhere | Introduce value objects or typed IDs |
| **Feature envy** | Method uses another class's data more than its own | Move method to the class it envies |
| **Shotgun surgery** | One change requires editing 10 files | Group related logic into one module |
| **Dead code** | Unused imports, unreachable branches | Delete it (git has history if you need it back) |
| **Magic numbers/strings** | `if (status === 3)` | Extract named constant: `STATUS_ACTIVE = 3` |
| **Boolean blindness** | `doThing(true, false, true)` | Use options object or named parameters |

## Safe Refactoring Process

```
1. Ensure tests exist (write them first if they don't)
2. Make ONE small change
3. Run tests
4. Commit
5. Repeat
```

**Never refactor and change behavior in the same commit.** Refactoring = same behavior, better structure. New behavior = new commit.

## Common Refactoring Patterns

### Extract Early Returns (flatten nesting)

```
// Before
function process(user) {
  if (user) {
    if (user.active) {
      if (user.hasPermission) {
        return doWork(user);
      }
    }
  }
  return null;
}

// After
function process(user) {
  if (!user) return null;
  if (!user.active) return null;
  if (!user.hasPermission) return null;
  return doWork(user);
}
```

### Replace Conditional with Polymorphism

```
// Before
function getArea(shape) {
  switch (shape.type) {
    case 'circle': return Math.PI * shape.radius ** 2;
    case 'rectangle': return shape.width * shape.height;
  }
}

// After — each shape knows how to calculate its own area
class Circle { getArea() { return Math.PI * this.radius ** 2; } }
class Rectangle { getArea() { return this.width * this.height; } }
```

### Replace Magic Values with Constants

```
// Before
if (retries > 3) { setTimeout(fn, 5000); }

// After
const MAX_RETRIES = 3;
const RETRY_DELAY_MS = 5000;
if (retries > MAX_RETRIES) { setTimeout(fn, RETRY_DELAY_MS); }
```

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| Refactoring without tests | Write tests first, then refactor |
| Big-bang rewrite | Incremental, one smell at a time |
| Refactoring code you won't touch again | Leave working code alone |
| Mixing refactoring with feature work | Separate commits: refactor, then feature |
| Premature abstraction | Wait for 3 instances before extracting (Rule of Three) |
| Renaming everything for style | Only rename if current name is misleading |
| Refactoring in a time crunch | Add a TODO, come back when there's time |
| "Just a quick cleanup" that touches 50 files | Scope it down — small, reviewable changes |
