# Docstrings and Type Hints

All functions and methods must have full documentation. Type hints are required for function signatures.

**Constraint level:** Hard — applies to all project types (PoC, MVP, GA).

## Format: Google Style

Follow [Google's Python Style Guide](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings) for docstrings. Use triple double-quotes.

```python
def calculate_discount(price: float, tier: str, coupon_rate: float = 0.0) -> float:
    """Calculate the discounted price for an order.

    Args:
        price (float): The original item price in USD.
        tier (str): Customer tier, one of "standard", "silver", "gold".
        coupon_rate (float): Optional coupon discount as a decimal (default 0.0).

    Returns:
        float: The final price after all discounts are applied.

    Raises:
        ValueError: If price is negative or tier is not a recognised value.
    """
```

## Required Sections

- **Args** — each argument with its type and description
- **Returns** — return type and what the value represents
- **Raises** — any exceptions explicitly raised (omit if none)

## Type Hints

Use PEP 484 type annotations on all function signatures. Type hints are machine-checkable documentation:

```python
from typing import Optional, Sequence

def process_items(items: Sequence[str], max_count: Optional[int] = None) -> list[str]:
    ...
```

For optional arguments that default to `None`, use `Optional[T]` (Python < 3.10) or `T | None` (Python ≥ 3.10):

```python
# Python 3.10+
def foo(name: str, opts: dict | None = None) -> str:
    opts = opts or {}
    ...
```

## Inline Comments

Comments explain **why**, not **what**. If you need to explain *what* code does, it should probably be made simpler or extracted into a well-named function.

```python
# Good — explains why
# Data CSV contains two header rows of metadata; skip to avoid parse errors.
df = pd.read_csv("data.csv", skiprows=2)

# Bad — explains what (the code already says this)
# Read the CSV file
df = pd.read_csv("data.csv")
```

## Script Preamble

Scripts (not notebooks) should begin with a module-level docstring describing the script's purpose, expected inputs, and outputs:

```python
"""
Train the anomaly detection model.

Usage:
    python train.py --config config.yaml

Inputs:
    - data/processed/features.parquet
Outputs:
    - models/anomaly_detector.pkl
"""
```

## Source
- `../raw/DS Code Development Manual/Writing Code/Code Standards/Code Standards Overview/Docstring Format, Type Hinting and Default Arguments…md`
- `../raw/DS Code Development Manual/Code Review/Code Review Philosophy…md` (Comments section)

## Related
- [[code-style/python-standards]]
- [[principles/io-contracts]]
