# DS vs SFL: Conflict Analysis and Resolutions

This page documents where the DS Code Development Manual and SFL Software Design Principles disagreed, and the ruling made on 2026-04-16 that now governs all wiki pages.

---

## Conflict 1 — IO / Data Types

**Status: Resolved — SFL supersedes**

| Standard | Position |
|---|---|
| DS | Use basic types (str, int, float, bool) for IO. Avoid passing complex data types such as custom classes. Document custom types thoroughly if used. |
| SFL | "Design with Clear Contracts" — convert raw inputs to typed domain objects at system boundaries. Passing raw dicts/basic types as internal APIs is an anti-pattern. |

**Resolution:** SFL supersedes. Use typed domain objects (dataclasses, Pydantic models) inside the system. Translate raw inputs at boundaries (HTTP, file I/O, DB). See [[principles/io-contracts]].

**Rationale:** The DS approach prioritised initial simplicity and reusability in data science contexts. SFL's position is that hidden schemas rot silently, and typed domain objects are the mechanism that makes refactoring and testing reliable at scale.

---

## Conflict 2 — Default Programming Paradigm

**Status: Resolved — SFL supersedes**

| Standard | Position |
|---|---|
| DS | Functional-first: use functions until you have a clear need for a class. Dedicated section promoting Functional Programming as the default paradigm. |
| SFL | OOP-first: OOP design patterns, ABCs, Protocols, composition by default. Functional techniques used within bounded units. |

**Resolution:** SFL supersedes. OOP is the default design approach. Functional techniques (pure functions, immutability, higher-order functions) remain valuable *within* OOP units, and are encouraged where they make code cleaner. See [[principles/object-oriented-design]].

**Rationale:** OOP provides clearer seams for testing, dependency injection, and change isolation at the system level. Functional techniques complement OOP within components.

---

## Conflict 3 — Testing Expectations

**Status: Clarified — not a true conflict**

| Standard | Position |
|---|---|
| DS | Tiered by project maturity: no unit tests required for PoC, essential units for MVP, 100% coverage for GA. |
| SFL (DRAFT) | "TEST YOUR CODE. If it's hard to test, that's a design smell." — stated as an absolute. |

**Resolution — Clarified 2026-04-16:**
- **Always test during development** — this is universal, not maturity-dependent. Informally verify your code works as you build it.
- **Formal unit test coverage** follows DS maturity tiers (PoC = not required but encouraged, MVP = essential units, GA = 100%).
- The two statements address different things: SFL is about the development habit; DS is about formal test coverage requirements.

See [[testing/testing-philosophy]] and [[testing/testing-by-maturity]].

---

## Conflict 4 — Logging Format

**Status: Resolved — SFL supersedes**

| Standard | Position |
|---|---|
| DS | Simple `logging.basicConfig(level=logging.INFO)`. No format prescribed. |
| SFL | Structured, contextual JSON logs with stable fields (`event`, `component`, `request_id`, `duration_ms`, etc.). Treat logs as a product interface for operators. |

**Resolution:** SFL supersedes. Use structured, contextual logging. Include stable fields on all meaningful log events. Both standards agree that logging configuration belongs in entrypoints only. See [[principles/logging]].

---

## Gaps (DS Has No Standard; SFL Does)

### Gap 1 — Configuration Management
DS has no guidance on configuration management. SFL prescribes a `pydantic-settings` pattern with typed, validated, environment-specific settings.

**Adopted:** SFL's configuration management standard. See [[principles/configuration-management]].

### Gap 2 — Security in Logging
DS logging guidance does not mention PII or secrets. SFL explicitly prohibits logging tokens, passwords, card numbers, and other sensitive data.

**Adopted:** SFL's security rules for logging. See [[principles/logging]].

---

## Areas of Strong Alignment

These principles were consistent across both standards:
- PEP 8 / Black formatting
- Google-style docstrings with type hints
- Composition over inheritance
- Low coupling / high cohesion
- Feature-oriented PRs
- Commit message standards
- Mutable default argument avoidance
- No wildcard imports
- Log not print (though DS is less prescriptive on format)
