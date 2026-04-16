# Object-Oriented Design

OOP is the **default design paradigm** for this codebase. It helps build software that is easier to change, test, and extend as requirements evolve. Model behaviour around cohesive objects with clear responsibilities, and you reduce coupling and create safe seams for refactoring.

> **Note on DS vs SFL:** The DS manual promoted a functional-first style. The resolved position (2026-04-16) is SFL supersedes: OOP is the default. Functional techniques (pure functions, immutability, higher-order functions) remain valuable *within* bounded units.

## Core Principles

### Encapsulation — Hide Volatility
Keep state private; expose behaviour through stable methods. This prevents "action at a distance" where many callers depend on internal representation.

- Prefer "tell, don't ask": call behaviour rather than pulling data out to decide outside
- Limit public surface area; make invariants impossible to violate from outside

### Abstraction — Design to Intent
Expose *what* clients need to do, not *how* it is done. Name abstractions after responsibilities (`PaymentMethod`, `Notifier`), not implementations (`PaymentMethodImpl`). Keep contracts small — large abstractions become god interfaces.

### Polymorphism — Replace Conditionals with Dispatch
When adding a new variant requires editing many `if/else` chains, that is a signal to use polymorphism. If there are only 1–2 stable variants and they will not grow, keep it simple.

## Abstract Base Classes (ABCs)

Use ABCs when you need both a strong contract *and* shared default behaviour:
- Enforce a common lifecycle (initialise → validate → execute → cleanup)
- Share meaningful implementation across all implementers
- Protect invariants (base class guards/normalises inputs)

Watch-out: ABCs create tight coupling if overused. If shared behaviour is minimal, prefer a `Protocol` + composition instead.

## Inheritance: Use Sparingly

Inheritance is for true "is-a" relationships. The key test: can any subclass be substituted wherever the base type is expected, without surprising callers?

**Use inheritance when:**
- The relationship is truly "is-a" and stable over time
- Subclasses extend behaviour, not rewrite it

**Avoid inheritance when:**
- You are inheriting to reuse code (use composition instead)
- Subclasses override many methods or violate base expectations
- The base class keeps accumulating special cases

Never inherit from concrete classes — only from abstract base classes / interfaces. This avoids tight coupling and hidden behaviour.

## Composition — Default Choice

Build objects out of smaller components (delegation). Composition is almost always preferable to inheritance because you can swap parts without restructuring a type hierarchy.

Choose composition when you want "has-a" relationships, multiple orthogonal variations, or to avoid deep inheritance trees.

## Functional Techniques Inside OOP

Pure functions, immutability, and higher-order functions are valuable *within* OOP units:
- A class's methods can be pure (no side effects, predictable output)
- Prefer returning new objects over mutating shared state
- Use list comprehensions, `map`, `filter` for concise data transformation

See [[principles/design-philosophy]] for guidance on when to reach for functions vs classes.

## Design Patterns

The [Refactoring Guru catalogue](https://refactoring.guru/design-patterns/catalog) is the canonical reference for common patterns (Strategy, Repository, Factory, Observer, etc.).

## Source
- `../raw/SFL Software Design Principles/Object Oriented Design…md`
- `../raw/DS Code Development Manual/Writing Code/Design Philosophy…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[principles/single-responsibility]]
- [[principles/io-contracts]]
- [[principles/design-philosophy]]
- [[testing/writing-good-tests]]
