# Source Evaluation Reference

## Source Type Hierarchy

| Tier | Source Type | Trust Level | Use For |
|------|-----------|------------|---------|
| 1 | Official documentation | Highest | API details, configuration, behavior |
| 2 | Official blog / changelog | High | Announcements, roadmap, design rationale |
| 3 | Core maintainer (GitHub, talks) | High | Intent, undocumented behavior, future plans |
| 4 | Independent tech analysis | Medium-high | Comparison, benchmarks, real-world experience |
| 5 | Community discussion | Medium | Edge cases, workarounds, common problems |
| 6 | Tutorial / course content | Medium | Learning patterns, but may be outdated |
| 7 | AI-generated content | Low | Cross-reference only, never primary source |
| 8 | Vendor marketing | Low | Claims need independent verification |

## Red Flags

| Signal | Action |
|--------|--------|
| No author attribution | Reduce trust, cross-reference |
| No date published | Assume outdated, verify claims |
| Affiliate links / sponsored | Note bias, seek independent confirmation |
| Benchmarks without methodology | Don't cite performance claims |
| "Best [tool] for [year]" listicles | Skip entirely — SEO content, not analysis |
| Circular citations (A cites B cites A) | Find original source |

## Evaluating GitHub Repositories

| Signal | Indicates |
|--------|----------|
| Stars > 1000 + recent commits | Active, community-trusted |
| Stars > 1000 + no commits in 6mo | Popular but potentially abandoned |
| Few stars + recent commits | Early stage, evaluate on merit |
| Many open issues, few closed | Maintenance concern |
| Clear README + examples + tests | Quality project |
| No license | Cannot use in production |

## Evaluating npm/PyPI Packages

| Check | Command |
|-------|---------|
| Weekly downloads | npm info [pkg] or PyPI stats |
| Last publish date | npm info [pkg] time.modified |
| Open vulnerabilities | npm audit / pip-audit |
| Dependency count | Fewer = better (less supply chain risk) |
| TypeScript types available | @types/[pkg] or built-in |

## When Sources Conflict

```
Source A says X, Source B says Y → Resolution:
    ├─ A is official docs, B is blog → Trust A
    ├─ A is newer than B → Lean toward A, note change
    ├─ A and B are equal authority → Present both, note disagreement
    ├─ A has data/benchmarks, B has opinions → Trust A
    └─ Cannot resolve → Explicitly state uncertainty
```
