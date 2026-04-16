# Writing Good Tests

Practical guidance for writing unit and integration tests in Python using pytest.

## Tools

- **pytest** — primary test framework (preferred over `unittest` for new projects)
- **coverage** — measure which lines are exercised
- **mock / unittest.mock** — replace dependencies with controlled fakes
- **bandit** — security checks (GA projects)
- **tox** — multi-environment testing (GA projects)

## Unit Test Structure: Arrange-Act-Assert

Every unit test has three clear phases:

```python
def test_price_order_gold_tier_applies_15_percent_discount():
    # Arrange
    order = Order(items=[LineItem(unit_price=Money(Decimal("100")), quantity=1)])
    user = User(tier=Tier.GOLD)

    # Act
    result = price_order(order, user)

    # Assert
    assert result.discount_rate == Decimal("0.15")
    assert result.total == Money(Decimal("85.00").quantize(Decimal("0.01")))
```

## File Structure

Mirror `src/` in `test/`:

```
src/
  model/
    training.py
  data/
    pipeline.py
test/
  model/
    test_training.py
  data/
    test_pipeline.py
    fixtures/
      sample_input.csv
```

## What Makes a Test Good

- **Easy to write** — if it is hard, the unit under test needs to be smaller (see [[principles/single-responsibility]])
- **Readable** — another developer can understand it immediately; intent is clear
- **Reliable** — only fails when the code it tests is broken; no flakiness from timing or external state
- **Independent** — does not share state with other tests; does not fail because of a change in an unrelated function
- **Fast** — milliseconds, not seconds

## Design for Testability

Functions that depend on the current time, random numbers, or external I/O cannot be deterministically tested without dependency injection or parameter injection:

```python
# Hard to test — depends on datetime.now() internally
def get_time_of_day():
    hour = datetime.now().hour
    ...

# Testable — inject the time
def get_time_of_day(time: datetime) -> str:
    if time.hour < 6:
        return "Night"
    elif time.hour < 12:
        return "Morning"
    ...
```

Pass dependencies in; do not reach for them from inside.

## Testing Edge Cases

A test suite that only tests the happy path is incomplete. Always test:
- Boundary values (0, -1, max allowed value)
- Empty inputs
- Null/None inputs
- Invalid types (if the function validates inputs)
- Error/exception paths

Use category partitioning to systematically identify edge cases.

## Regression Tests for Bugs

When a bug is found manually:
1. Fix the bug
2. Write a test that would have caught it
3. Verify the test fails on the pre-fix code and passes on the fixed code

## Example: Full Unit Test Suite

```python
import unittest
from datetime import datetime

from mypackage.time_utils import get_time_of_day

class TestGetTimeOfDay(unittest.TestCase):
    def setUp(self):
        self.night   = datetime(2024, 1, 1, 3, 0)
        self.morning = datetime(2024, 1, 1, 9, 0)
        self.afternoon = datetime(2024, 1, 1, 14, 0)
        self.evening = datetime(2024, 1, 1, 20, 0)

    def test_night(self):
        self.assertEqual(get_time_of_day(self.night), "Night")

    def test_morning(self):
        self.assertEqual(get_time_of_day(self.morning), "Morning")

    def test_afternoon(self):
        self.assertEqual(get_time_of_day(self.afternoon), "Afternoon")

    def test_evening(self):
        self.assertEqual(get_time_of_day(self.evening), "Evening")

    def test_rejects_non_datetime(self):
        with self.assertRaises(AssertionError):
            get_time_of_day("9am")
        with self.assertRaises(AssertionError):
            get_time_of_day(9)
```

## Integration Tests

Integration tests verify that units work together correctly. They:
- Have no knowledge of internal implementation details
- Cover the flow through the pipeline end-to-end
- May use real or test-double infrastructure (real DB in a test container, or a fast in-memory DB)

At minimum, integration tests should cover all code that interfaces with users or other modules.

## Source
- `../raw/DS Code Development Manual/Code Testing/Unit Testing & Integration Testing/Unit and Integration Testing Guidelines…md`
- `../raw/DS Code Development Manual/Writing Code/DRAFT Updated Code Practices…md`
- `../raw/DS Code Development Manual/Code Testing/Unit Testing & Integration Testing/PyTest Crash Course/`

## Related
- [[testing/testing-philosophy]]
- [[testing/testing-by-maturity]]
- [[principles/single-responsibility]]
