# Testing Philosophy

Testing is not a bureaucratic requirement — it is the mechanism that allows you to change code with confidence. If code is hard to test, that is a design signal, not an excuse to skip tests.

## Core Principle

**Always test your code during development.** This is non-negotiable regardless of project maturity. Formal unit test coverage requirements scale with maturity (see [[testing/testing-by-maturity]]), but the act of testing as you build is universal.

If writing a test feels hard, the unit probably needs to be broken up further.

## Why Testing Matters

1. **Confidence to change** — tests let you refactor without anxiety about breaking existing behaviour
2. **Forces modular design** — testable code is inherently smaller and more focused
3. **Bug prevention** — catches issues before they accumulate or reach clients
4. **Documentation** — a good test suite describes how code is supposed to behave
5. **Reusability** — code + tests migrates more safely to new projects

## Types of Tests

### Unit Tests
Verify a single unit in isolation. A unit test has:
- Lots of knowledge about one unit of code
- Zero knowledge about other parts of the codebase
- No real external dependencies (no DB, no network, no filesystem)

A change in function X must never cause a unit test for function Y to fail.

### Integration Tests
Verify that different units work together. An integration test has:
- No knowledge of internal implementation details
- Coverage of the flow between modules/services

These are valuable even in PoC projects to catch pipeline breaks early and reduce handoff cost.

### End-to-End (E2E) Tests
Run through the same motions a client/user will perform. Required before delivery at all maturity stages.

## Arrange-Act-Assert Pattern

Structure every unit test as three phases:

```python
def test_calculate_discount_gold_tier():
    # Arrange
    price = 100.0
    tier = "gold"

    # Act
    result = calculate_discount(price, tier)

    # Assert
    assert result == 85.0
```

## Properties of a Good Unit Test

| Property | What It Means |
|---|---|
| Easy to write | If it's hard, the unit under test needs to be smaller |
| Readable | Another developer can understand it at a glance |
| Reliable | Passes when code is correct; fails when code is broken — no flakiness |
| Independent | Never fails because of a change in an unrelated function |
| Fast | Runs in milliseconds; can be run on every save |

## Testability as a Design Signal

A function that is difficult to test usually:
- Has multiple responsibilities (violates [[principles/single-responsibility]])
- Depends on hidden global state or mutable defaults
- Mixes I/O with business logic

The fix is a design fix, not a test workaround.

## On Finding Bugs

When a bug is found manually (not caught by tests):
1. Report to the team
2. Fix the bug
3. Write a regression test that would have caught it
4. Discuss whether existing test criteria need updating

## Time Budget

A developer should spend approximately **25–30% of development time on tests**. Writing too few tests and writing too many are both problems. Use judgment, guided by project maturity and the team lead.

## Source
- `../raw/DS Code Development Manual/Code Testing/Unit Testing & Integration Testing/Unit and Integration Testing Guidelines…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`
- `../raw/DS Code Development Manual/Writing Code/Design Philosophy…md`

## Related
- [[testing/testing-by-maturity]]
- [[testing/writing-good-tests]]
- [[principles/single-responsibility]]
