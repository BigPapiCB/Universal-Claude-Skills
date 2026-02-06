# Universal Baseline Skills

Stack-agnostic core skills for any project. Follows the [SKILL.md standard](https://agentskills.io/specification).

## Skills (17)

| Skill | Purpose |
|-------|---------|
| code-review | Review checklist, severity levels, output format |
| debugging | Diagnostic decision tree, root cause patterns |
| security-audit | OWASP checklist, scanning, injection prevention |
| performance-audit | Profiling, bottleneck patterns, phased workflow |
| test-writer | What to test, assertions, mocking, anti-patterns |
| api-design | REST/GraphQL conventions, error format |
| database-design | Schema patterns, indexing, migrations |
| docker-setup | Multi-stage builds, Compose, security |
| ci-cd | GitHub Actions templates (Node, Python) |
| project-setup | Env validation, gitignore hygiene, versioning |
| error-handling | Error types, propagation, user vs internal |
| logging | Structured logging, levels, trace IDs |
| mcp-builder | MCP server development (TypeScript, Python) |
| research | Structured research methodology, source evaluation, triangulation |
| auth-patterns | JWT vs sessions, OAuth flows, RBAC, token handling |
| refactoring | Code smells, safe refactoring steps, when to refactor |
| accessibility | WCAG checklist, ARIA, keyboard nav, color contrast |

## Installation

Clone the repo directly into your Claude skills directory for a permanent, global installation that works across all projects:

```bash
git clone https://github.com/BigPapiCB/Universal-Claude-Skills.git ~/.claude/skills
```

### Updating

```bash
cd ~/.claude/skills && git pull
```

## Adding Project-Specific Skills

Add skills to your project at `<project>/.claude/skills/`. Project-level skills take precedence over global skills, so you can override any baseline skill with your own version.

For example, to override the global `code-review` skill with project-specific rules:

```bash
mkdir -p .claude/skills/code-review
```

Then create `.claude/skills/code-review/SKILL.md` with your custom review criteria. Claude will use your project version instead of the global one.
