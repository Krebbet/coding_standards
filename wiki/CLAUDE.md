# Coding Standards Wiki — Schema & Conventions

This document defines how the LLM maintains this wiki. Read it at the start of every session.

## Purpose

This is a persistent, compounding knowledge base of coding standards and best practices for Python data science and cloud application development. It synthesises two internal standards sets (DS Code Development Manual and SFL Software Design Principles), external research, and resolved conflicts into a single authoritative reference.

## Directory Structure

```
wiki/
├── CLAUDE.md               ← this file (schema)
├── index.md                ← content catalog (LLM updates on every ingest)
├── log.md                  ← append-only session log
├── overview.md             ← high-level synthesis of all principles
├── principles/             ← core design & engineering principles
├── code-style/             ← language-level style and formatting rules
├── testing/                ← testing philosophy, patterns, expectations
├── workflow/               ← git, PRs, code review, project maturity
├── conflicts/              ← resolved and open conflicts between sources
└── research/               ← pages synthesised from external sources (added later)
```

## Raw Sources

Raw source documents live in `../raw/` and are **never modified**. Two original standards sets:
- `../raw/DS Code Development Manual/` — DS-specific Python standards (SFL Scientific)
- `../raw/SFL Software Design Principles/` — Software engineering design principles

## Page Format

Every wiki page should have:
1. A `# Title` at the top
2. A brief one-paragraph summary at the start (for index.md)
3. `## Source` section at the bottom noting which raw documents informed the page
4. `## Related` section linking to related pages using `[[wiki-link]]` syntax

## Cross-References

Use Obsidian `[[page-name]]` links. The page name is the filename without `.md`. Always link the first mention of a concept to its page.

## Resolved Conflicts

When two sources conflict and the user has made a ruling, the resolution goes in `conflicts/ds-vs-sfl-resolved.md`. All wiki pages reflect the resolved position, not the raw source position.

## Ingesting New Sources

When adding a new source to `../raw/`:
1. Read the source
2. Discuss key takeaways
3. Write/update concept pages it touches
4. Add a log entry to `log.md`
5. Update `index.md`

## Linting Checks

Periodically check for:
- Orphan pages (no inbound links)
- Contradictions between pages
- Missing cross-references
- Important concepts mentioned but lacking their own page
