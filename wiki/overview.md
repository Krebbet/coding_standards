# Coding Standards Overview

This wiki captures the unified coding standards for Python data science and cloud application development. It synthesises the **DS Code Development Manual** and the **SFL Software Design Principles** into a single, coherent reference.

## Core Philosophy

Good code is code that can be understood, tested, changed, and reused by the next engineer — including future you. All standards in this wiki serve that goal.

The four pillars:
1. **Clear contracts** — types and interfaces make expectations explicit at every boundary
2. **Single responsibility** — every unit has one reason to change
3. **Observable behaviour** — structured logging makes systems understandable in production
4. **Appropriate rigour** — standards scale with project maturity and deployment risk

## Standards Sections

### Design Principles
- [[principles/single-responsibility]] — SRP: one reason to change
- [[principles/object-oriented-design]] — OOP-first: encapsulation, composition, polymorphism
- [[principles/io-contracts]] — Typed inputs/outputs, domain objects at boundaries
- [[principles/design-philosophy]] — Keep it simple, cohesion & coupling, stable interfaces
- [[principles/logging]] — Structured, contextual, production-grade logging
- [[principles/configuration-management]] — Typed config with pydantic-settings

### Code Style
- [[code-style/python-standards]] — PEP8, Black, naming conventions
- [[code-style/docstrings-and-types]] — Google-style docstrings, type hints
- [[code-style/code-structure]] — Imports, file layout, comments

### Testing
- [[testing/testing-philosophy]] — Why test, when to test, TDD principles
- [[testing/testing-by-maturity]] — PoC / MVP / GA expectations
- [[testing/writing-good-tests]] — Unit test properties, Arrange-Act-Assert, pytest

### Workflow
- [[workflow/project-maturity-stages]] — PoC, MVP, GA definitions
- [[workflow/version-control]] — Branching, commit standards
- [[workflow/pull-requests]] — Feature-oriented PRs, documentation requirements
- [[workflow/code-review]] — Philosophy, checklist, interaction guidelines

### Conflicts & Resolutions
- [[conflicts/ds-vs-sfl-resolved]] — Where the two standards disagreed and what was decided

## Source Hierarchy

When DS and SFL standards conflict, the following resolution rules apply (agreed 2026-04-16):

| Topic | Superseding Standard |
|---|---|
| IO / data types | SFL — use typed domain objects |
| Default paradigm | SFL — OOP-first |
| Testing during development | Always (both agree) |
| Formal unit test thresholds | DS maturity tiers |
| Logging format | SFL — structured JSON |
| Configuration management | SFL — pydantic-settings |

## Source: Raw Documents
- `../raw/DS Code Development Manual/`
- `../raw/SFL Software Design Principles/`
