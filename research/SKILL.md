---
name: research
description: Structured research methodology with scoping, multi-source collection, triangulation, source credibility scoring, and anti-hallucination guards. Use when investigating technologies, comparing tools, evaluating libraries, exploring unfamiliar codebases, or producing research reports.
metadata:
  version: "1.0"
allowed-tools: WebSearch WebFetch Read Grep Glob
---

# Research

## Decision Tree

```
Research request → What depth?
    ├─ Lookup (simple fact, quick answer) → 3-5 sources, skip to Collect + Deliver
    ├─ Synthesis (compare options, summarize topic) → 8-12 sources, all 5 phases
    ├─ Analysis (architecture decision, tech evaluation) → 12-15 sources, all 5 phases + recommendation
    └─ Investigation (novel/complex, no obvious answer) → 15+ sources, all 5 phases + multiple rounds
```

## Phases

```
Phase 1: Scope → Phase 2: Collect → Phase 3: Validate → Phase 4: Synthesize → Phase 5: Deliver
```

## Phase 1: Scope

Before searching, define the boundaries:

- **Core question** — What specifically are we trying to answer?
- **Depth level** — Lookup / Synthesis / Analysis / Investigation
- **Search strategy** — Keywords, year constraints, domain restrictions
- **Success criteria** — What would a good answer look like?

## Phase 2: Collect

Execute multi-angle searches. Don't just search once — approach the topic from multiple directions:

| Search Angle | Example Query Modifier |
|-------------|----------------------|
| Factual | "what is [topic]" |
| Comparative | "[topic] vs [alternative]" |
| Technical | "[topic] architecture implementation" |
| Practical | "[topic] production experience lessons learned" |
| Recent | "[topic] 2026" or "[topic] latest" |
| Critical | "[topic] problems limitations drawbacks" |

**Source diversity required:**
- Official documentation (most authoritative)
- Official blogs and announcements
- Independent technical analysis
- Community discussions (GitHub issues, forums)
- Academic papers (for foundational claims)

**Parallel searches:** Execute 3-5 searches simultaneously when tools support it.

## Phase 3: Validate

Every claim needs triangulation:

```
Claim from Source A → Find Source B that independently confirms or contradicts
    ├─ Confirmed by 2+ sources → High confidence
    ├─ Contradicted → Note conflict, investigate why
    └─ Only one source → Flag as unverified, note in output
```

**Source chain:** Official docs → Independent analysis → Community verification

**URL validation:** Check that every cited link is accessible before including it.

## Phase 4: Synthesize

Score and weight sources using:

| Factor | Weight | High Score | Low Score |
|--------|--------|-----------|-----------|
| Authority | 30% | Official docs, core maintainer | Random blog, no credentials |
| Recency | 25% | Current year, last 12 months | 3+ years old |
| Specificity | 25% | Directly answers the question | Tangentially related |
| Independence | 20% | No vendor affiliation | Vendor marketing material |

**Conflict resolution:**
1. Prefer official documentation
2. Check publication dates (newer often supersedes)
3. Note the disagreement explicitly
4. Present both perspectives if unresolved

## Phase 5: Deliver

**Output structure (scale to depth):**

| Depth | Output Format |
|-------|--------------|
| Lookup | 1-2 paragraphs + sources |
| Synthesis | Sections with comparison table + sources |
| Analysis | Executive summary + detailed sections + recommendation + sources |
| Investigation | Full report with findings, methodology, limitations, sources |

**Every deliverable must include:**
- Inline citations `[Source Name](url)` for all claims
- Sources section at the end with all referenced URLs
- Confidence indicators for uncertain claims
- Date of research (findings may become outdated)

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| Single-source claims | Triangulate with 2+ independent sources |
| Hallucinated citations | Never fabricate URLs — only cite pages you actually fetched |
| Vendor benchmarks as neutral | Explicitly note source bias |
| Overly broad queries | Focus each query on a single specific aspect |
| Missing recency | Add current year to search queries |
| Shallow exploration | Read 10+ sources, not just the first 1-2 results |
| No conflict disclosure | Always note when sources disagree |
| Broken links in report | Validate every URL before including |
| Assuming first result is best | Check multiple results, compare quality |
| Skipping the scope phase | Always define what "done" looks like before searching |

## For detailed source evaluation criteria see:
- [Source evaluation reference](references/source-evaluation.md)
