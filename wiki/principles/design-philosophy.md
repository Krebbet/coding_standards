# Design Philosophy

General principles for how to approach writing code — before choosing a pattern or paradigm.

## Keep It Simple (KIS)

Complexity is a cost paid by every future maintainer. The simplest design that correctly solves the problem is usually the best. Avoid adding abstraction, flexibility, or cleverness that is not required today.

- Prefer readable code over dense code
- Cyclomatic complexity should be as low as possible
- Favour simple, clear logic over clever one-liners

## Cohesion and Coupling

**High cohesion:** related code lives together. Functions and classes within a module serve a common, clearly defined purpose.

**Low coupling:** modules interact in a minimal, well-defined way. Changes to one module should not ripple unexpectedly into others.

> "Each unit should only talk to its friends and not to strangers."

Together: high cohesion + low coupling = flexible, maintainable, testable architecture.

## Stable Interfaces

Changing a function's name, its positional arguments, or its return type is a **breaking change**. Every caller must be updated. Plan IO before writing implementation.

Rules for stability:
- Positional arguments are the stable, required inputs — avoid changing them
- Keyword arguments are optional defaults — safe to add; avoid removing
- When adding behaviour, prefer adding a new function over modifying an existing one
- If you must change a signature, consider whether a keyword argument can absorb the change without breaking callers

## Functions vs Classes

OOP is the default design paradigm (see [[principles/object-oriented-design]]). However, within that:

- **Prefer functions** for stateless, one-shot operations with no need for shared state or polymorphism
- **Use a class** when you need to encapsulate state, define a reusable interface, or support multiple implementations (polymorphism)
- **Avoid classes without clear need** — they add complexity and are easier to get wrong

This is not a contradiction: a well-designed OOP system is built from many small, focused functions *and* classes, each in the right place.

## Unix Philosophy

- Write programs that do one thing well
- Build small, composable units
- Favour simplicity and clarity over cleverness
- Design for interoperability

## Wrapping Third-Party Tools

When your code wraps a library function heavily, use `**kwargs` to pass through keyword arguments rather than re-declaring every parameter. Document what `**kwargs` are forwarded to.

## Dictionaries Are for Mappings

`dict` is for key-value *lookup*, not general-purpose data storage. Use typed domain objects for structured data (see [[principles/io-contracts]]).

## Source
- `../raw/DS Code Development Manual/Writing Code/Design Philosophy…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`

## Related
- [[principles/single-responsibility]]
- [[principles/object-oriented-design]]
- [[principles/io-contracts]]
