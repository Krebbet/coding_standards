# Single Responsibility Principle (SRP)

A module, class, or function should have one primary reason to change. SRP is not "do one tiny thing" — it is about **cohesive ownership of a concept** (e.g., pricing rules, persistence, formatting, transport). Each unit owns one slice of the system's concerns and nothing else.

## Why It Matters

- **Change isolation** — when a requirement shifts, you update one place without unintended side effects
- **Testability** — small, focused units need fewer fixtures and mocks
- **Reuse** — focused components compose cleanly, especially with dependency injection
- **Readability** — a reader understands intent faster when concerns are not mixed

## How to Spot SRP Violations

- A class/file mixes: business logic + persistence + HTTP + formatting + logging + error handling
- Method names like `process_*`, `handle_*`, `do_everything()` that branch on flags or type
- Frequent changes to a unit for unrelated reasons (e.g., changing an email template breaks invoice logic)
- Unit tests that require mocking `requests`, the filesystem, a DB, and a clock all at once

## Practical Guidance

Separate concerns into layers:

| Layer | What It Does |
|---|---|
| Domain / business rules | Pure logic; minimal external dependencies |
| Application orchestration | Coordinates steps; calls dependencies |
| Infrastructure / adapters | DB, HTTP clients, file I/O, queues |
| Presentation / formatting | CLI output, HTML/JSON rendering, email templates |

- Prefer **dependency injection** — pass collaborators in rather than importing globals
- Aim for **pure core, impure edges**: side effects (I/O, network, DB) live at system boundaries; core logic stays pure

## Relationship to Other Principles

SRP is the foundation that makes [[principles/io-contracts]], [[principles/object-oriented-design]], and testability (see [[testing/writing-good-tests]]) tractable. If a class violates SRP it will be hard to test and hard to give a clear contract to.

## Source
- `../raw/SFL Software Design Principles/Single Responsibility Principle (SRP)…md`
- `../raw/DS Code Development Manual/Writing Code/Design Philosophy…md` (Cohesion and Coupling section)

## Related
- [[principles/object-oriented-design]]
- [[principles/io-contracts]]
- [[principles/design-philosophy]]
- [[testing/writing-good-tests]]
