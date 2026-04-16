# IO Contracts — Typed Inputs and Outputs

Minimise business logic that manipulates raw structures like `dict`, `list`, `tuple`, `bytes`, or untyped JSON payloads. Treat those as **transport and persistence formats** — not your application's internal language.

> **Note on DS vs SFL:** DS standards previously recommended using basic types (str, int, float) for simplicity and reusability. The resolved position (2026-04-16) is SFL supersedes: use typed domain objects inside the system, translate at the edges.

## Core Rule: "Translate at the Edges, Speak Domain Inside"

Convert raw inputs **once** at a system boundary into a well-defined type. Keep the interior of the system working with **domain objects**.

| Zone | Raw structures OK? |
|---|---|
| HTTP handlers / request parsing | Yes |
| Queue consumers / producers | Yes |
| File I/O (CSV / JSON / YAML) | Yes |
| DB rows / ORM models | Yes |
| Third-party SDK responses | Yes |
| Domain services and business rules | No |
| Core workflows / use cases | No |
| Cross-module interfaces | No |

## Recommended Patterns

### 1. Domain Models + Value Objects
Create explicit types representing your concepts and invariants (`Money`, `EmailAddress`, `CustomerId`, `Invoice`). Keep validation and normalisation *inside* these types using `__post_init__` or validators.

### 2. DTOs / Schemas at Boundaries
Use a schema layer (dataclasses, Pydantic) to validate/parse raw payloads. The key: **raw dict in → validated object out**. Parse once; never re-parse downstream.

### 3. Adapters (Anti-Corruption Layer)
For third-party APIs, create an adapter that maps vendor responses into your internal types. Never let vendor-shaped dicts leak into domain logic.

### 4. Repository Pattern for Persistence
Translate DB rows/ORM entities into domain models *inside* the repository. Business logic never cares about storage representation.

### 5. Typed Collection Interfaces
Pass `Sequence[Order]` rather than `list[dict]`. Pass `Mapping[CustomerId, Customer]` rather than `dict[str, dict]`. Contracts become explicit; tooling helps.

## Anti-Patterns to Avoid

- Business rules implemented as `payload["x"]["y"][0]` chains
- Functions that accept/return "bag of fields" dicts ("just add one more key…")
- Cross-module APIs that expose raw JSON/dicts as their contract
- Domain logic that "knows" API key names, DB column names, or message schemas
- Mutating shared dicts across steps

## Why This Matters

Hidden schemas rot silently. Key names and magic indices become implicit APIs that are unsafe to refactor. Validation scatters across call sites. Failures surface as `KeyError` / `TypeError` far from the boundary, with poor diagnostics.

## Designing Good IO First

Even before you know *how* code will work, you can define *what* it needs:

1. Start at the highest level and break down until each piece has a single task
2. Define types for inputs and outputs at each junction
3. Keep positional arguments (required, stable) separate from keyword arguments (optional, with defaults)
4. Avoid changing positional arguments — this causes breaking changes

## Source
- `../raw/SFL Software Design Principles/Design with Clear Contracts…md`
- `../raw/DS Code Development Manual/Writing Code/Design Philosophy…md`

## Related
- [[principles/single-responsibility]]
- [[principles/object-oriented-design]]
- [[principles/design-philosophy]]
- [[code-style/docstrings-and-types]]
